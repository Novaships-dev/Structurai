---
name: PRICING-ENGINE
description: Moteur de prix BTP de STRUCTORAI. Algorithme complet pricing d'un devis de A à Z. Lookup en 4 étages (Mem0 prix perso artisan → référentiels 11 métiers `data/prix/*.json` → benchmark département → web fallback). Application coefficients régionaux 11 zones (Paris 1.35 → Sud-Ouest 0.95). TVA multi-taux (5.5/10/20). Marge par poste. Anti-invention. Score confiance 🟢🟡🔴. Alertes artisan si prix < 70% ou > 150% du référentiel. Intégration avec Agent Devis + Agent Coach Business (benchmarks). Format JSON data/prix. Apprentissage continu (prix artisan enrichissent Mem0).
type: technical-pricing-algorithm
scope: agent-devis, data-prix, benchmark, mem0, confidence-score
priority: critical
date: 2026-04-18
version: 1.0
total_postes_referenced: 1080+
trades_covered: 11 (plomberie, électricité, isolation, chauffage/clim, couverture, carrelage, maçonnerie, peinture, menuiserie-serrurerie, plaquiste, façadier)
regional_zones: 11 (Paris 1.35 → Sud-Ouest 0.95)
tva_rates: FR 5.5 / 10 / 20
fallback_chain: Mem0 → data/prix/*.json → benchmark département → web fallback
---

# docs/PRICING-ENGINE.md — Moteur de prix BTP STRUCTORAI

> **Ce fichier est la source de vérité du moteur de prix BTP.**
> **Règle d'or absolue** : **zéro prix inventé**. Tout prix doit provenir d'une source tracée.
> Reference : `docs/METIER.md` §9 + §10 (coefficients + règle zéro invention).
> Reference : `docs/AGENTS.md` §2 Agent Devis (utilisateur principal).
> **Différenciation** : ~**1080 postes** BTP référencés dans 11 métiers (8819 lignes). Aucun concurrent n'a cette profondeur.

---

## 0. SOMMAIRE

1. [Principes fondamentaux](#1-principes-fondamentaux)
2. [Algorithme de pricing — vue d'ensemble](#2-algorithme-de-pricing--vue-densemble)
3. [Étage 1 — Mem0 prix perso artisan](#3-étage-1--mem0-prix-perso-artisan)
4. [Étage 2 — Référentiels `data/prix/*.json`](#4-étage-2--référentiels-dataprixjson)
5. [Étage 3 — Benchmark département](#5-étage-3--benchmark-département)
6. [Étage 4 — Web fallback (exceptionnel)](#6-étage-4--web-fallback-exceptionnel)
7. [Coefficients régionaux 11 zones](#7-coefficients-régionaux-11-zones)
8. [TVA multi-taux BTP](#8-tva-multi-taux-btp)
9. [Calcul marge par poste](#9-calcul-marge-par-poste)
10. [Score de confiance 🟢🟡🔴](#10-score-de-confiance-)
11. [Anti-invention — garde-fous](#11-anti-invention--garde-fous)
12. [Alertes artisan (prix incohérents)](#12-alertes-artisan-prix-incohérents)
13. [Apprentissage continu (feedback loop)](#13-apprentissage-continu-feedback-loop)
14. [Format JSON `data/prix/[metier].json`](#14-format-json-dataprixmetierjson)
15. [11 fichiers métiers détaillés](#15-11-fichiers-métiers-détaillés)
16. [Benchmark Agent Coach Business (F119)](#16-benchmark-agent-coach-business-f119)
17. [Détecteur devis concurrent (F121)](#17-détecteur-devis-concurrent-f121)
18. [Service `pricing_service.py`](#18-service-pricing_servicepy)
19. [Algorithme complet de pricing d'un devis](#19-algorithme-complet-de-pricing-dun-devis)
20. [Performance et cache](#20-performance-et-cache)
21. [Tests et validation](#21-tests-et-validation)
22. [Gouvernance des référentiels](#22-gouvernance-des-référentiels)
23. [Adaptation multi-pays (F135)](#23-adaptation-multi-pays-f135)
24. [Troubleshooting](#24-troubleshooting)
25. [Références croisées](#25-références-croisées)

---

## 1. PRINCIPES FONDAMENTAUX

### 1.1 — 8 règles immuables

1. **Zéro prix inventé** — tout prix doit provenir d'une source tracée (Mem0, `data/prix/`, benchmark, web)
2. **Fourchettes min-max** — jamais un prix unique ; toujours min-max pour refléter variabilité réelle
3. **Prix artisan prime** — si l'artisan a déjà facturé ce poste, SES prix priment sur référentiel
4. **Coefficient régional toujours appliqué** (11 zones, voir §7)
5. **TVA exacte** selon règles FR (5.5/10/20) selon type travaux + âge logement + RGE
6. **Traçabilité source** — chaque prix a une source attribuée
7. **Alertes si anomalie** — prix <70% ou >150% du référentiel → warning artisan
8. **Apprentissage continu** — les prix validés par artisan enrichissent SA base Mem0

### 1.2 — Pourquoi c'est critique

Le moteur de prix est **le cœur de l'Agent Devis**. Un artisan fait **2h de devis/jour** en moyenne :
- 1h à chercher les prix (mail fournisseur, appeler collègues, Google)
- 45 min à taper le devis
- 15 min mentions légales

**STRUCTORAI réduit à 2-5 minutes grâce au moteur de prix** :
- Reconnaissance vocale du besoin ("SDB complète 8m²")
- Décomposition en postes (plomberie, carrelage, peinture, etc.)
- Lookup prix automatique par poste
- Application TVA + mentions + coefficient régional
- PDF Factur-X généré

### 1.3 — Intégration dans l'architecture

```
Agent Devis (Opus)
  └── pricing_service.py
        ├── Mem0Client — prix perso artisan
        ├── ReferentielLoader — data/prix/*.json
        ├── BenchmarkService — comparaison département (F119)
        └── WebSearchFallback — dernier recours (rare)
```

Voir `docs/ARCH.md` §4 Services + `docs/AGENTS.md` §2 Agent Devis.

---

## 2. ALGORITHME DE PRICING — VUE D'ENSEMBLE

### 2.1 — Diagramme de décision

```
┌──────────────────────────────────────────────────┐
│   Input: poste métier, quantité, contexte         │
│   Ex: "pose carrelage 40×40 rectifié", 20 m²,     │
│        zone "Marseille", rénovation               │
└──────────────────────┬───────────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────────┐
│   ÉTAGE 1 — Mem0 artisan                          │
│   Cherche: a-t-il déjà facturé ce poste ?         │
│   Si OUI → ✅ prix artisan (confidence 🟢)        │
└──────────────────────┬───────────────────────────┘
                       │ Si pas trouvé
                       ▼
┌──────────────────────────────────────────────────┐
│   ÉTAGE 2 — data/prix/[metier].json               │
│   Cherche: référentiel interne                    │
│   Si trouvé → ✅ prix référentiel (confidence 🟢) │
└──────────────────────┬───────────────────────────┘
                       │ Si pas trouvé
                       ▼
┌──────────────────────────────────────────────────┐
│   ÉTAGE 3 — Benchmark département                 │
│   Compare avec moyens autres artisans département │
│   Si suffisant → ✅ benchmark (confidence 🟡)     │
└──────────────────────┬───────────────────────────┘
                       │ Si pas suffisant
                       ▼
┌──────────────────────────────────────────────────┐
│   ÉTAGE 4 — Web fallback (exception)              │
│   Web search structuré avec validation            │
│   Si trouvé → ⚠️ prix web (confidence 🔴)         │
└──────────────────────┬───────────────────────────┘
                       │ Si rien
                       ▼
┌──────────────────────────────────────────────────┐
│   ⛔ ÉCHEC — Demander à l'artisan                 │
│   "Combien factures-tu généralement pour ça ?"    │
└──────────────────────┬───────────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────────┐
│   APPLIQUER coefficient régional (zone)           │
│   APPLIQUER TVA (selon type travaux)              │
│   APPLIQUER marge artisan (si définie)            │
│   VÉRIFIER cohérence (alertes si anomalies)       │
└──────────────────────┬───────────────────────────┘
                       │
                       ▼
┌──────────────────────────────────────────────────┐
│   Output: { prix_min, prix_max, source, confidence}│
└──────────────────────────────────────────────────┘
```

### 2.2 — Structure retour

```python
@dataclass
class PricingResult:
    poste: str                      # "pose carrelage 40x40 rectifié"
    quantite: Decimal
    unite: str                       # "m2","ml","u","h","forfait"
    prix_unitaire_min: Decimal       # en centimes HT
    prix_unitaire_max: Decimal
    prix_unitaire_median: Decimal
    total_min: Decimal
    total_max: Decimal
    total_median: Decimal
    tva_rate: Decimal                # 5.5 / 10 / 20
    source: PricingSource            # mem0 | referentiel | benchmark | web | manual
    confidence: Confidence           # high | medium | low
    zone_coefficient: Decimal        # 1.00 base, 1.35 Paris, etc.
    zone_name: str
    warnings: list[str]              # Alertes si anomalies
    metadata: dict                    # Détails source
```

---

## 3. ÉTAGE 1 — MEM0 PRIX PERSO ARTISAN

### 3.1 — Principe

Chaque fois que l'artisan valide un devis, les prix de ses postes enrichissent SA mémoire Mem0 :
- Poste (normalisé)
- Prix unitaire HT
- Unité (m², ml, u, h, forfait)
- Date de facturation
- Lieu (zone) si présent

Cette mémoire devient **sa base de prix personnalisée**, prioritaire sur tout référentiel externe.

### 3.2 — Structure en mémoire

Voir `docs/MEMORY.md` + `docs/MEMORY-STRATEGY.md`.

Mem0 stocke des facts textuels :
```
fact: "L'artisan facture la pose de carrelage grès cérame 40x40 à 48€/m² HT 
       (zone Sud-Est, dernier devis octobre 2025)"

fact: "L'artisan applique une remise de 10% pour les chantiers > 3000€ HT 
       avec clients récurrents"

fact: "Taux horaire main-d'œuvre chef plombier : 55€ HT/h"
```

### 3.3 — Lookup

```python
def get_artisan_prices(
    poste: str,
    organization_id: UUID,
    trade: str,
) -> Optional[ArtisanPriceData]:
    """Cherche prix personnalisés de l'artisan via Mem0."""
    
    # Normalisation du poste (ex: "carrelage 40x40" → "carrelage_40x40_gres_cerame")
    normalized_poste = normalize_poste(poste)
    
    # Recherche Mem0 avec query sémantique
    results = mem0_client.search(
        query=f"prix {trade} {poste}",
        user_id=str(organization_id),
        layer="organization",
        limit=5,
    )
    
    # Extraction prix des facts matchés
    prices = extract_prices_from_facts(results)
    
    if len(prices) >= 2:  # Au moins 2 facturations → fiable
        return ArtisanPriceData(
            prix_median=median(prices),
            prix_min=min(prices),
            prix_max=max(prices),
            nb_facturations=len(prices),
            derniere_facturation=most_recent(prices),
            confidence=Confidence.HIGH,
            source=PricingSource.MEM0_ARTISAN,
        )
    return None
```

### 3.4 — Ancienneté des prix

**Règle** : si le dernier prix remonte à > 18 mois → réduire confidence à MEDIUM (inflation, évolution tarifs).

Après 24 mois → ignorer ce prix (utiliser étage 2).

### 3.5 — Privilégier l'artisan

Si prix artisan trouvé → **utilisé systématiquement**, même s'il diffère significativement du référentiel (liberté commerciale).

MAIS : alerte informative si écart > 30% (voir §12).

---

## 4. ÉTAGE 2 — RÉFÉRENTIELS `data/prix/*.json`

### 4.1 — Architecture

11 fichiers JSON par métier, dans `backend/data/prix/FR/` (multi-pays F135 : `data/prix/{country}/`).

```
backend/data/prix/FR/
├── plomberie.json                    # 1021 lignes
├── electricite.json                  # 960 lignes
├── isolation.json                    # 765 lignes
├── chauffage_climatisation.json      # 693 lignes
├── couverture.json                   # 703 lignes
├── carrelage.json                    # 573 lignes
├── maconnerie.json                   # 488 lignes
├── peinture.json                     # 465 lignes
├── menuiserie_serrurerie.json        # 426 lignes
├── plaquiste.json                    # 401 lignes
├── facadier.json                     # 375 lignes
├── coefficients_regionaux.json       # 11 zones
└── taux_horaires.json                # MO par métier
```

**Total** : 11 × ~500 lignes = ~6 370 lignes de data brute + 8819 lignes de knowledge base (docs/metier/*.md).

### 4.2 — Source des prix

Les référentiels ont été constitués à partir de :
1. **Bases publiques** BTP (barèmes fournisseurs, agrégateurs)
2. **Fiches détaillées** par métier (`FICHE_METIER.md` + 11 fichiers `metier/*.md`)
3. **Validation avec artisans beta-testeurs** (itération continue)
4. **DTU norms** officielles françaises (pour cohérence technique)

### 4.3 — Structure d'une entrée

```json
{
  "id": "carrelage_sol_gres_cerame_40x40_rectifie",
  "poste": "Pose carrelage sol grès cérame 40×40 rectifié",
  "categorie": "pose_sol",
  "sous_categorie": "gres_cerame",
  "unite": "m2",
  "prix_min_ht": 3500,
  "prix_max_ht": 5500,
  "prix_median_ht": 4500,
  "source_base": "Paris/IDF 2025",
  "coefficient_zone_base": 1.20,
  "details_prix": {
    "fourniture": { "min": 1500, "max": 3000, "ratio_prix": 0.4 },
    "main_oeuvre": { "min": 2000, "max": 2500, "ratio_prix": 0.5 },
    "finitions_chutes": { "min": 0, "max": 500, "ratio_prix": 0.1 }
  },
  "duree_moyenne_pose": {
    "unite_temps": "heures",
    "par_m2": 1.5,
    "preparation_supp_h": 1.0
  },
  "conditions": {
    "surface_minimum_m2": 3,
    "age_logement_years_min_for_tva_10": 2,
    "rge_required": false
  },
  "tva_possibles": [20, 10],
  "mentions_specifiques": ["DTU 52.2"],
  "pieges_techniques": [
    "Vérifier planéité avec règle 2m : tolérance max 5mm",
    "Joints de dilatation tous les 40 m² ou 8 m linéaires",
    "Temps de séchage colle : 24h minimum avant jointoiement"
  ],
  "variations_notables": {
    "grand_format_60x60_plus": { "facteur_prix": 1.15 },
    "pose_diagonale": { "facteur_prix": 1.20 },
    "grands_carreaux_90x90": { "facteur_prix": 1.35 },
    "sol_chauffant": { "facteur_prix": 1.15, "condition": "compatibilité colle" }
  },
  "fournisseurs_typiques": [
    "Point P", "Cedeo", "BigMat", "Carrelage Groupe"
  ],
  "last_updated": "2026-04-01",
  "source_metadata": {
    "references": ["capeb_2025_barometre", "ffb_2025_bareme_unitaire"],
    "region_coverage": ["FR_metropolitaine"],
    "sample_size": "~250 devis analysés"
  }
}
```

### 4.4 — Chargement

```python
# backend/app/services/pricing_service.py

class ReferentielLoader:
    def __init__(self, country_code: str = "FR"):
        self.country_code = country_code
        self._cache: dict[str, list[dict]] = {}
        self._load_all()
    
    def _load_all(self):
        """Charge tous les fichiers JSON au démarrage."""
        base_path = f"data/prix/{self.country_code}"
        for metier in TRADES:
            filename = f"{base_path}/{metier}.json"
            with open(filename) as f:
                self._cache[metier] = json.load(f)
    
    def lookup(self, metier: str, poste_query: str) -> Optional[PosteReferentiel]:
        """Recherche dans référentiel."""
        if metier not in self._cache:
            return None
        
        # Exact match d'abord
        for entry in self._cache[metier]:
            if entry["id"] == normalize_poste(poste_query):
                return PosteReferentiel(**entry)
        
        # Fuzzy match semantic (embedding)
        candidates = self._semantic_search(self._cache[metier], poste_query, top_k=3)
        if candidates and candidates[0].similarity > 0.75:
            return candidates[0].entry
        
        return None
```

### 4.5 — Semantic search

Pour matcher "SDB complète 8m²" → décomposer en postes :
- "dépose sanitaire existant"
- "pose receveur de douche"
- "pose WC suspendu"
- "carrelage mural 25m²"
- "carrelage sol 8m²"
- "plomberie encastrée"
- "évacuation gravats"

Utilisation embeddings (Claude Sonnet ou OpenAI ada-002) pour matcher description libre → poste référentiel.

---

## 5. ÉTAGE 3 — BENCHMARK DÉPARTEMENT

### 5.1 — Principe

Quand pas de prix artisan ni référentiel → comparer avec la **moyenne des autres artisans STRUCTORAI du département** pour le même poste.

### 5.2 — Agrégation

Les prix (anonymisés) des devis validés enrichissent une base benchmark :

```sql
-- Table agrégée (job nightly)
CREATE MATERIALIZED VIEW benchmark_prices AS
SELECT
    poste_normalized,
    metier,
    department,
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY prix_unitaire_ht) AS p25,
    PERCENTILE_CONT(0.50) WITHIN GROUP (ORDER BY prix_unitaire_ht) AS median,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY prix_unitaire_ht) AS p75,
    COUNT(*) AS sample_size
FROM devis_lines_agg
WHERE sent_at > NOW() - INTERVAL '12 months'
  AND deleted_at IS NULL
GROUP BY poste_normalized, metier, department
HAVING COUNT(*) >= 10;  -- Sample size minimum pour validité
```

### 5.3 — Anonymisation

Les artisans **ne voient pas** les prix individuels des autres. Seules les agrégations statistiques (p25/p50/p75) sont exposées.

**Consent** : l'artisan accepte de contribuer au benchmark en acceptant les CGU (opt-in par défaut, opt-out possible dans settings).

### 5.4 — Usage

```python
def get_benchmark_price(
    poste: str,
    metier: str,
    department: str,  # ex: "13" Marseille
) -> Optional[BenchmarkData]:
    result = query_benchmark_prices(poste, metier, department)
    if not result or result.sample_size < 10:
        # Élargir à la région
        result = query_benchmark_prices(poste, metier, department=None, region=get_region(department))
    if not result or result.sample_size < 20:
        # Élargir national
        result = query_benchmark_prices(poste, metier)
    
    if result and result.sample_size >= 10:
        return BenchmarkData(
            prix_min=result.p25,
            prix_median=result.median,
            prix_max=result.p75,
            sample_size=result.sample_size,
            scope=result.scope,  # "department" / "region" / "national"
            confidence=Confidence.MEDIUM,
            source=PricingSource.BENCHMARK,
        )
    return None
```

### 5.5 — Valeur stratégique

Plus STRUCTORAI a d'artisans, plus le benchmark est précis → **effet réseau**. Aucun concurrent n'a cette capacité.

---

## 6. ÉTAGE 4 — WEB FALLBACK (EXCEPTIONNEL)

### 6.1 — Quand

Utilisé **uniquement** quand :
- Pas de prix artisan
- Pas de référentiel
- Pas assez de benchmark (< 10 échantillons)
- Le poste est vraiment inhabituel (ex: "rénovation puits artésien")

### 6.2 — Fréquence

**< 2% des lookups** en régime de croisière. Si > 5%, c'est un signal que les référentiels sont incomplets.

### 6.3 — Process

1. Recherche structurée via web_search (agrégateurs de prix BTP fiables : Travaux.com, Forum Construire, Batiexpert)
2. Parsing des résultats avec Claude Sonnet
3. Extraction fourchette min-max
4. Stockage dans table `web_prices_cache` pour réutilisation 30j
5. Flag rouge `confidence=low` sur le devis → artisan doit valider

### 6.4 — Exemple

```python
async def web_fallback_price(poste: str, metier: str) -> Optional[WebPriceData]:
    # Cache
    cached = web_prices_cache.get(poste, metier)
    if cached and cached.age_days < 30:
        return cached
    
    # Web search
    results = await web_search(
        query=f"prix moyen {poste} {metier} France 2026 TTC",
        max_results=5,
    )
    
    if not results:
        return None
    
    # Parse avec Claude
    extraction = await claude_client.complete(
        model="claude-haiku-4-5-20251001",  # Haiku (cheap, structuré)
        prompt=PRICE_EXTRACTION_PROMPT,
        context=results,
    )
    
    if not extraction.valid:
        return None
    
    # Cache
    web_prices_cache.store(poste, metier, extraction)
    
    return WebPriceData(
        prix_min=extraction.prix_min,
        prix_max=extraction.prix_max,
        sources=[r.url for r in results],
        confidence=Confidence.LOW,  # Toujours LOW
        source=PricingSource.WEB,
    )
```

### 6.5 — Limite d'usage

Rate limit : **20 appels web fallback/jour par organization** (protection coûts + évite boucles infinies).

---

## 7. COEFFICIENTS RÉGIONAUX 11 ZONES

### 7.1 — Référence canonique

Voir `docs/METIER.md` §9 et `docs/SINGLE_SOURCE_OF_TRUTH.md` §13.

### 7.2 — Tableau complet

| Zone | Coefficient | Départements typiques |
|---|---|---|
| **Paris intra-muros** | **1.35** | 75 |
| **Île-de-France (hors Paris)** | **1.20** | 77, 78, 91, 92, 93, 94, 95 |
| **Méditerranée / PACA** | **1.15** | 06, 13, 83, 84 |
| **DOM** | **1.25** | 971, 972, 974, 976 |
| **Corse** | **1.15** | 2A, 2B |
| **Sud-Est (Lyon, Alpes-Rhône)** | **1.10** | 69, 01, 38, 73, 74 |
| **Montagne (>800m)** | **1.10** | (selon altitude, pas département) |
| **Ouest** | **1.00** (base) | 35, 44, 49, 56, 85, 22, 29 |
| **Grand Est** | **0.95** | 67, 68, 54, 57, 55, 88 |
| **Nord / Hauts-de-France** | **0.95** | 59, 62, 02, 60, 80 |
| **Sud-Ouest** | **0.95** | 31, 33, 40, 64, 65, 66, 81, 82 |

### 7.3 — Fichier `coefficients_regionaux.json`

```json
{
  "version": "2026-04-01",
  "source": "FFB + CAPEB + INSEE 2025",
  "zones": [
    {
      "id": "paris_intra_muros",
      "nom": "Paris intra-muros",
      "coefficient": 1.35,
      "departements": ["75"],
      "conditions_altitude": null,
      "notes": "Contraintes urbaines : stationnement, accès, horaires travaux"
    },
    {
      "id": "ile_de_france_hors_paris",
      "nom": "Île-de-France hors Paris",
      "coefficient": 1.20,
      "departements": ["77", "78", "91", "92", "93", "94", "95"]
    },
    {
      "id": "mediterranee_paca",
      "nom": "PACA",
      "coefficient": 1.15,
      "departements": ["06", "13", "83", "84", "04", "05"]
    },
    {
      "id": "dom",
      "nom": "DOM-TOM",
      "coefficient": 1.25,
      "departements": ["971", "972", "973", "974", "976"],
      "notes": "Coûts logistique import + climat tropical"
    },
    {
      "id": "corse",
      "nom": "Corse",
      "coefficient": 1.15,
      "departements": ["2A", "2B"]
    },
    {
      "id": "sud_est",
      "nom": "Sud-Est (Lyon, Alpes-Rhône)",
      "coefficient": 1.10,
      "departements": ["69", "01", "38", "73", "74", "26", "07"]
    },
    {
      "id": "montagne",
      "nom": "Montagne > 800m",
      "coefficient": 1.10,
      "condition_altitude_min_m": 800,
      "notes": "Coûts logistique + saisonnalité"
    },
    {
      "id": "ouest",
      "nom": "Ouest (base)",
      "coefficient": 1.00,
      "departements": ["35", "44", "49", "56", "85", "22", "29", "50", "53", "72"]
    },
    {
      "id": "grand_est",
      "nom": "Grand Est",
      "coefficient": 0.95,
      "departements": ["67", "68", "54", "57", "55", "88", "51", "52", "08", "10"]
    },
    {
      "id": "nord",
      "nom": "Nord / Hauts-de-France",
      "coefficient": 0.95,
      "departements": ["59", "62", "02", "60", "80"]
    },
    {
      "id": "sud_ouest",
      "nom": "Sud-Ouest",
      "coefficient": 0.95,
      "departements": ["31", "33", "40", "64", "65", "66", "81", "82", "12", "32", "46"]
    }
  ],
  "default_coefficient": 1.00,
  "fallback_rule": "Si département non listé, utiliser coefficient 1.00 (base Ouest)"
}
```

### 7.4 — Application

```python
def apply_zone_coefficient(
    prix_base: Decimal,
    zone: ZoneInfo,
    source_zone_base: str = "base",  # zone de référence du prix source
) -> Decimal:
    """Applique coefficient zone."""
    source_coef = get_zone_coefficient(source_zone_base)  # ex: 1.20 si prix ref = Paris
    target_coef = zone.coefficient                        # ex: 0.95 Sud-Ouest
    
    # Normalisation : si le prix source est déjà en Paris (1.20),
    # et qu'on veut Sud-Ouest (0.95), ratio = 0.95 / 1.20 = 0.79
    ratio = target_coef / source_coef
    return prix_base * ratio
```

### 7.5 — Détection de la zone

La zone se déduit de **l'adresse du chantier** :
1. Parse l'adresse (libre texte ou structurée)
2. Extract code postal
3. Lookup département (2 ou 3 premiers chars du CP)
4. Match département → zone

Si adresse altitude > 800m → apply "montagne" en priorité.

---

## 8. TVA MULTI-TAUX BTP

### 8.1 — Règles FR

Voir `docs/METIER.md` §1 pour détails complets.

| Taux | Conditions | Documents requis |
|---|---|---|
| **20%** | Construction neuve, locaux pro, fournitures client | — |
| **10%** | Rénovation logement > 2 ans | Attestation TVA 10% signée par client |
| **5.5%** | Rénovation énergétique | Attestation + **RGE valide** obligatoire |

### 8.2 — Logique de détermination

```python
def determine_tva_rate(
    poste: PosteReferentiel,
    context: DevisContext,
) -> TVAResult:
    # 1. Construction neuve → toujours 20%
    if context.type_chantier == "construction_neuve":
        return TVAResult(rate=20.0, mention_slug="TVA_20_NEUF")
    
    # 2. Local professionnel → toujours 20% (sauf exceptions hôtellerie)
    if context.type_local == "commercial":
        return TVAResult(rate=20.0, mention_slug="TVA_20_COMMERCIAL")
    
    # 3. Rénovation énergétique + RGE → 5.5%
    if context.type_travaux == "renovation_energetique" and poste.categorie in TVA_55_ELIGIBLE:
        if not context.organization.rge_valid:
            raise BusinessLogicError(
                code="FISCAL_RGE_MISSING",
                message_fr="TVA 5,5% nécessite une certification RGE valide.",
            )
        return TVAResult(
            rate=5.5,
            mention_slug="TVA_55_ENERGETIQUE",
            requires_attestation=True,
            rge_check_required=True,
        )
    
    # 4. Rénovation logement > 2 ans → 10%
    if context.type_travaux == "renovation" and context.age_logement_years >= 2:
        return TVAResult(
            rate=10.0,
            mention_slug="TVA_10_RENOVATION",
            requires_attestation=True,
        )
    
    # 5. Défaut → 20%
    return TVAResult(rate=20.0, mention_slug="TVA_20_DEFAUT")
```

### 8.3 — Fournitures vs main-d'œuvre

**TVA 10% / 5.5%** s'applique aux **travaux** incluant fournitures et MO, à condition que les fournitures soient **fournies par l'artisan** (pas fournitures client).

Si fournitures client → TVA 20% sur fournitures, 10%/5.5% sur MO.

Le moteur de prix sépare les 2 pour appliquer le bon taux :
```json
{
  "fourniture": { "tva_applicable": "selon_context" },
  "main_oeuvre": { "tva_applicable": "10_ou_5_5" }
}
```

---

## 9. CALCUL MARGE PAR POSTE

### 9.1 — Principe

Chaque poste a une **décomposition** (fourniture / MO / finitions) qui permet à l'artisan de visualiser sa marge.

### 9.2 — Structure référentiel

```json
{
  "details_prix": {
    "fourniture": { "min": 1500, "max": 3000, "ratio_prix": 0.4 },
    "main_oeuvre": { "min": 2000, "max": 2500, "ratio_prix": 0.5 },
    "finitions_chutes": { "min": 0, "max": 500, "ratio_prix": 0.1 }
  }
}
```

### 9.3 — Calcul de marge cible

L'artisan configure dans ses settings :
- **Marge cible fourniture** : ex 20% (cout achat + 20% = prix vente fourniture)
- **Taux horaire MO** : ex 55€/h
- **Marge cible globale** : ex 35%

Le moteur applique ces paramètres si Mem0 contient ces infos. Sinon, il utilise les ratios par défaut du référentiel.

### 9.4 — Affichage à l'artisan

```
┌────────────────────────────────────────────────┐
│  Pose carrelage 40×40 rectifié — 20 m²          │
│  Prix unitaire : 45 €/m² HT                     │
│  Total : 900 € HT                                │
│                                                  │
│  Détail :                                        │
│    Fourniture : 360 € HT (coût 300, marge 20%)  │
│    Main-d'œuvre : 450 € HT (10h × 45€/h)        │
│    Finitions/chutes : 90 € HT                   │
│                                                  │
│  📊 Marge estimée : 30% (270 € HT)              │
│  ⚠️ Sous ton objectif (35%) — tu veux revoir ?  │
└────────────────────────────────────────────────┘
```

---

## 10. SCORE DE CONFIANCE 🟢🟡🔴

### 10.1 — Les 3 niveaux

| Niveau | Couleur | Source | Action requise |
|---|---|---|---|
| **🟢 HIGH** | Vert | Mem0 artisan (≥ 2 facturations récentes) OU référentiel complet | Aucune — prix fiable |
| **🟡 MEDIUM** | Orange | Référentiel partiel OU benchmark département (≥ 10 samples) | Artisan doit valider |
| **🔴 LOW** | Rouge | Web fallback OU interpolation estimée | Validation artisan OBLIGATOIRE |

### 10.2 — Affichage

Sur chaque ligne du devis :
- Icône couleur
- Hover/tap → source explicite ("Basé sur tes 3 derniers devis" / "Référentiel national" / "Web — Estimation")

### 10.3 — Règles agrégation devis

**Devis entier** :
- Si toutes lignes 🟢 → devis 🟢 (envoyable direct)
- Si au moins 1 ligne 🟡 → devis 🟡 (validation recommandée)
- Si au moins 1 ligne 🔴 → devis 🔴 (validation obligatoire, bouton d'envoi grisé tant que pas validé)

### 10.4 — Bouton "Pourquoi ce prix ?"

Pour chaque ligne, l'artisan peut cliquer → panneau explicatif :

```
📊 Pose carrelage 40×40 rectifié : 45 €/m²

Source : Tes prix personnalisés
Basé sur 3 devis facturés entre octobre 2025 et mars 2026 :
  - 08/10/25 — chantier Dupont — 48 €/m²
  - 15/12/25 — chantier Martin — 43 €/m²
  - 22/03/26 — chantier Bernard — 44 €/m²
Moyenne : 45 €/m²

Benchmark département (13) : 42-48 €/m² (127 devis)
Tu es dans la médiane — cohérent ✅
```

### 10.5 — Logging pour audit

Chaque prix appliqué loggé dans `ai_decisions` (voir `docs/AI-ACT-COMPLIANCE.md` §9) :

```python
ai_decision = {
    "agent_name": "devis",
    "action_type": "pricing_lookup",
    "input_summary": "pose_carrelage_40x40, 20 m², zone Marseille",
    "output_summary": "45 €/m², source: mem0_artisan, confidence: high",
    "confidence_score": "high",
    "model_used": "claude-opus-4-7",
    "metadata": {
        "source": "mem0_artisan",
        "prix_median": 4500,
        "sample_size": 3,
    }
}
```

---

## 11. ANTI-INVENTION — GARDE-FOUS

### 11.1 — Règle d'or

**Claude NE DOIT JAMAIS inventer un prix.** C'est une règle absolue documentée dans :
- `CLAUDE.md` §"Ce que tu ne fais jamais" règle 1
- `docs/METIER.md` §10 Règle d'or zéro invention
- System prompt Agent Devis (voir `docs/AGENTS.md` §2)

### 11.2 — Mécanismes

**A. Contraintes dans system prompt Agent Devis** :

```
RÈGLE ABSOLUE : Tu n'inventes JAMAIS un prix. 
Tu as 4 sources dans cet ordre :
1. Prix personnalisés de l'artisan (Mem0)
2. Référentiel data/prix/[metier].json
3. Benchmark département (via tool)
4. Web fallback (via tool, dernier recours)

Si aucune source ne donne de prix fiable, DEMANDE à l'artisan :
"Je ne trouve pas de prix fiable pour [poste]. 
Combien le factures-tu généralement ?"

NE DIS JAMAIS "environ X€" ou "généralement Y€" sans source explicite.
```

**B. Tool pricing obligatoire** :

Agent Devis ne peut pas générer un prix directement, il doit appeler le tool `get_price(poste, context)` qui retourne une `PricingResult`. Si le tool retourne `None`, Agent Devis doit demander à l'artisan.

**C. Validation post-génération** :

Avant PDF final, vérification :
```python
for line in devis.lines:
    if not line.pricing_result or not line.pricing_result.source:
        raise BusinessLogicError(
            code="METIER_REFERENCE_PRICE_NOT_FOUND",
            message_fr=f"Prix sans source détecté sur la ligne '{line.designation}'. "
                       f"Devis bloqué — demande confirmation à l'artisan.",
        )
```

### 11.3 — Monitoring

Alerte Sentry + SMS Fabrice si :
- Ratio "unknown source" > 5% des lignes sur 24h
- Ligne envoyée sans source (bug critique)

---

## 12. ALERTES ARTISAN (PRIX INCOHÉRENTS)

### 12.1 — Règles de cohérence

Pour chaque ligne, si écart important avec référence :

| Condition | Alerte |
|---|---|
| Prix artisan < 70% du référentiel | 🟡 "Tu factures 30% sous la moyenne. C'est volontaire ?" |
| Prix artisan > 150% du référentiel | 🟡 "Tu factures 50% au-dessus de la moyenne. Justifie au client ?" |
| Prix artisan < 50% du référentiel | 🔴 "Prix anormalement bas — tu perds probablement de l'argent." |
| Prix artisan > 200% du référentiel | 🔴 "Prix anormalement haut — risque de refus client." |

### 12.2 — Non bloquant

Les alertes sont **informatives**, jamais bloquantes. L'artisan a la liberté commerciale de fixer ses prix.

### 12.3 — Exemples en situation

```
⚠️ Ton prix pour "pose carrelage 40×40" est 42 €/m².
   Moyenne département (13) : 45-50 €/m² (127 devis)
   Tu es 15% sous la médiane. Volontaire ?
   
   [Oui, c'est voulu]  [Ajuster à 47€/m²]
```

```
💡 Ton taux horaire plombier est 42 €/h HT.
   Moyenne département (13) : 52 €/h (Coach Business)
   Tu pourrais passer à 48 €/h progressivement 
   sans perdre en compétitivité.
   
   [Plus tard]  [Ajuster dans settings]
```

### 12.4 — Apprentissage

Si l'artisan valide les alertes → Mem0 apprend "cet artisan est positionné bas de gamme / haut de gamme".

---

## 13. APPRENTISSAGE CONTINU (FEEDBACK LOOP)

### 13.1 — Cycle

```
1. Artisan envoie devis via Agent Devis
2. Prix lookup (4 étages)
3. Artisan valide ou ajuste les prix
4. Devis envoyé au client
5. Si devis signé → les prix validés enrichissent Mem0
6. Les prix deviennent plus précis au fil des devis
```

### 13.2 — Enrichissement Mem0

Après signature d'un devis :

```python
for line in devis.lines:
    if line.pricing_result.source != PricingSource.MEM0_ARTISAN:
        # Cette ligne utilisait un référentiel/benchmark/web
        # → enrichir Mem0 avec le prix réellement appliqué
        mem0_client.add_fact(
            user_id=str(organization_id),
            layer="organization",
            fact=f"L'artisan facture '{line.designation}' à {line.prix_unitaire_ht/100}€/{line.unite} "
                 f"(zone {devis.zone_name}, {devis.type_chantier}, "
                 f"signé le {devis.signed_at.date()})"
        )
```

### 13.3 — Nettoyage

Les prix Mem0 > 24 mois sont marqués "obsolètes" mais pas supprimés (historique).

Tous les 3 mois, Fabrice peut revoir les prix personnels dans settings.

### 13.4 — Contribution benchmark

Chaque devis signé alimente aussi la `benchmark_prices` (anonymisée, agrégée). Plus il y a d'artisans STRUCTORAI, plus les benchmarks sont précis.

---

## 14. FORMAT JSON `DATA/PRIX/[METIER].JSON`

### 14.1 — Racine

```json
{
  "metier": "carrelage",
  "version": "2026-04-01",
  "country_code": "FR",
  "source_documentation": "docs/metier/carrelage.md (573 lignes)",
  "last_updated": "2026-04-01",
  "postes": [
    { ... entrée 1 ... },
    { ... entrée 2 ... }
  ]
}
```

### 14.2 — Structure d'un poste

Voir §4.3 pour la structure détaillée. Champs obligatoires :

| Champ | Type | Requis | Description |
|---|---|---|---|
| `id` | string | ✅ | Identifiant unique snake_case |
| `poste` | string | ✅ | Désignation lisible |
| `categorie` | string | ✅ | Catégorie principale |
| `unite` | enum | ✅ | m2/ml/u/h/m3/forfait |
| `prix_min_ht` | int | ✅ | Centimes HT |
| `prix_max_ht` | int | ✅ | Centimes HT |
| `prix_median_ht` | int | ✅ | Centimes HT |
| `source_base` | string | ✅ | Zone de référence |
| `details_prix` | object | ⚪ | Décomposition |
| `duree_moyenne_pose` | object | ⚪ | Pour planning |
| `conditions` | object | ⚪ | TVA, RGE, surface min |
| `tva_possibles` | array | ✅ | Taux TVA éligibles |
| `mentions_specifiques` | array | ⚪ | DTU, normes |
| `pieges_techniques` | array | ⚪ | Conseils pro |
| `variations_notables` | object | ⚪ | Variantes (format, pose diag) |
| `last_updated` | date | ✅ | Traçabilité |

### 14.3 — Validation JSON Schema

Chaque fichier est validé au démarrage contre un JSON Schema (`backend/data/prix/schema.json`).

Si validation fail → **backend ne démarre pas** (fail fast).

---

## 15. 11 FICHIERS MÉTIERS DÉTAILLÉS

### 15.1 — Plomberie (1021 lignes)

Postes types : dépose sanitaire, pose receveur/baignoire, pose WC (sol/suspendu), robinetterie, évacuations PVC, alimentation cuivre/PER, chauffe-eau, ballon, dégorgement, recherche de fuite.

Particularités :
- Distinction matériaux (cuivre 45€/ml vs PER 18€/ml)
- Types de pose (apparent vs encastré)
- Normes DTU 60.1/60.5

### 15.2 — Électricité (960 lignes)

Postes types : circuits, tableau, prises, éclairage, domotique, mise aux normes, VDI, bornes recharge VE.

Particularités :
- Norme NF C 15-100
- Distinction sections câbles (1.5/2.5/6/10 mm²)
- TVA 5.5% sur certains postes domotique énergétique

### 15.3 — Isolation (765 lignes)

Postes types : ITE (ITI/mur/sol/toit), soufflage, laine minérale/bois, polystyrène, polyuréthane, écran pare-vapeur.

Particularités :
- **TVA 5.5% fréquente** (rénovation énergétique)
- **RGE obligatoire** pour TVA 5.5%
- Primes (MaPrimeRénov', CEE) à prendre en compte

### 15.4 — Chauffage & Climatisation (693 lignes)

Postes types : pompe à chaleur air-air/air-eau, chaudière gaz/fioul/bois, plancher chauffant, radiateurs, climatisation réversible.

Particularités :
- **RGE QualiPAC** pour pompes à chaleur
- TVA 5.5% pour pompes à chaleur haute performance
- Tubage cheminée, ramonage obligatoire

### 15.5 — Couverture (703 lignes)

Postes types : tuiles (terre cuite/béton), ardoise, zinc, bac acier, isolation toiture, charpente, gouttières, velux.

Particularités :
- Cadences lourdes (10-15€/m² vs 80-150€/m² pour ardoise)
- Contraintes zones ABF (bâtiments classés)

### 15.6 — Carrelage (573 lignes)

Postes types : grès cérame (standard/grand format/rectifié), faïence, mosaïque, parquet stratifié/massif, vinyle, moquette.

Particularités :
- Pose diagonale +20%
- Grand format (60x60+) +15-35%
- Sol chauffant compatibilité colle

### 15.7 — Maçonnerie (488 lignes)

Postes types : démolition, ouverture mur, création ouverture, reprise en sous-œuvre, chape, dalle, enduits, cloisons briques/parpaings.

Particularités :
- Sous-œuvre = bureau d'études obligatoire (coût +30-50%)
- Évacuation gravats systématique
- DTU 20

### 15.8 — Peinture (465 lignes)

Postes types : préparation murs (ponçage, rebouchage, enduit), impression, peinture acrylique/glycéro, papier peint, lasure, vernis.

Particularités :
- Nombre de couches (1 à 3)
- Finition (mate/satinée/brillante)
- Très sensible aux écarts régionaux

### 15.9 — Menuiserie & Serrurerie (426 lignes)

Postes types : portes intérieures/entrée/blindées, fenêtres (PVC/alu/bois), volets, placards, escaliers, serrures multipoints.

Particularités :
- TVA 5.5% sur fenêtres double vitrage performantes (RGE)
- Crédit d'impôt (CITE) intégré

### 15.10 — Plaquiste (401 lignes)

Postes types : cloisons placo, faux plafonds, doublages, isolation combinée, joints.

Particularités :
- Distinction BA13 standard / BA13 hydrofuge / BA13 phonique
- Cadences MO rapides (15-25 min/m²)

### 15.11 — Façadier (375 lignes)

Postes types : ravalement (nettoyage, enduit, peinture), ITE, échafaudage, fixation isolant, bardage bois.

Particularités :
- Échafaudage obligatoire (coût significatif)
- Autorisation (DP mairie) souvent requise
- TVA 5.5% si ITE

---

## 16. BENCHMARK AGENT COACH BUSINESS (F119)

Voir `docs/AGENTS.md` §14 + `docs/FEATURES_COMPLETE.md` F119.

### 16.1 — Lien avec pricing

Agent Coach Business utilise **les mêmes données benchmark** que le moteur de prix pour :
- Comparer le taux horaire de l'artisan vs médiane département
- Analyser ratio fourniture/MO
- Suggérer des ajustements pricing
- Identifier postes sous-facturés ou sur-facturés

### 16.2 — Fichier dédié

`data/benchmarks/taux_horaires_par_metier_region.json` :

```json
{
  "version": "2026-04-01",
  "source": "Agrégation devis STRUCTORAI + FFB/CAPEB 2025",
  "taux_horaires": {
    "plomberie": {
      "FR_national": { "p25": 40, "median": 48, "p75": 55 },
      "FR_paris": { "p25": 55, "median": 65, "p75": 75 },
      "FR_PACA": { "p25": 45, "median": 52, "p75": 62 }
    },
    "electricite": {
      "FR_national": { "p25": 42, "median": 50, "p75": 58 }
    }
  }
}
```

### 16.3 — Analyse mensuelle

Chaque 1er du mois, Agent Coach génère pour l'artisan :
- Positionnement (%ile) vs médiane département
- Recommandations progressives (ex: "+5€/h tous les 3 mois")
- Impact estimé sur MRR

### 16.4 — Rate limit

- Starter : 1 analyse/mois
- Pro/Business : illimité

---

## 17. DÉTECTEUR DEVIS CONCURRENT (F121)

Voir `FEATURES.md` F121.

### 17.1 — Usage du moteur

Endpoint `/v1/devis/analyze-competitor` :
1. Artisan upload PDF devis concurrent
2. Agent Vision IA parse lignes + prix
3. **Moteur de pricing** compare chaque ligne :
   - Vs Mem0 artisan
   - Vs référentiel
   - Vs benchmark département
4. Sortie :
   - "Prix concurrent 12% au-dessus du marché"
   - "Ligne manquante : évacuation gravats (~150€)"
   - "Lignes gonflées : main-d'œuvre à 80€/h (marché 55€/h)"

### 17.2 — Argumentaire de vente

Agent Devis génère texte à envoyer au client :

```
"Après analyse comparative, notre devis est compétitif :
- Nos prix sont alignés sur les tarifs moyens du marché
- Le devis concurrent omet l'évacuation des gravats (150€)
- La main-d'œuvre y est 25% au-dessus des standards
Notre devis inclut tout le nécessaire pour un prix juste."
```

---

## 18. SERVICE `PRICING_SERVICE.PY`

### 18.1 — Structure

**Fichier** : `backend/app/services/pricing_service.py`

```python
class PricingService:
    def __init__(
        self,
        mem0_client: Mem0Client,
        referentiel_loader: ReferentielLoader,
        benchmark_service: BenchmarkService,
        web_search_service: WebSearchService,
    ):
        self.mem0 = mem0_client
        self.referentiel = referentiel_loader
        self.benchmark = benchmark_service
        self.web = web_search_service
    
    async def get_price(
        self,
        poste: str,
        metier: str,
        context: PricingContext,
    ) -> PricingResult:
        """Pipeline complet de lookup prix."""
        
        # Étage 1 : Mem0
        price = await self._try_mem0(poste, context)
        if price:
            return self._apply_modifiers(price, context)
        
        # Étage 2 : Référentiel
        price = self._try_referentiel(poste, metier)
        if price:
            return self._apply_modifiers(price, context)
        
        # Étage 3 : Benchmark
        price = await self._try_benchmark(poste, metier, context.department)
        if price:
            return self._apply_modifiers(price, context)
        
        # Étage 4 : Web fallback
        if context.allow_web_fallback:
            price = await self._try_web_fallback(poste, metier)
            if price:
                return self._apply_modifiers(price, context)
        
        # Échec : raise pour déclencher demande artisan
        raise NotFoundError(
            code="METIER_REFERENCE_PRICE_NOT_FOUND",
            message_fr=f"Prix de référence non trouvé pour '{poste}'.",
        )
    
    def _apply_modifiers(
        self,
        base_price: BasePriceData,
        context: PricingContext,
    ) -> PricingResult:
        """Applique coefficient zone + TVA + marge."""
        # Zone coefficient
        adjusted = apply_zone_coefficient(base_price, context.zone, base_price.source_zone)
        # TVA
        tva = determine_tva_rate(base_price.poste, context)
        # Marge (si artisan a configuré sa marge cible)
        margin_alert = check_margin_coherence(adjusted, context.organization)
        # Cohérence
        warnings = check_price_coherence(adjusted, base_price)
        
        return PricingResult(
            prix_unitaire_min=adjusted.prix_min,
            prix_unitaire_max=adjusted.prix_max,
            prix_unitaire_median=adjusted.prix_median,
            tva_rate=tva.rate,
            source=base_price.source,
            confidence=base_price.confidence,
            zone_coefficient=context.zone.coefficient,
            zone_name=context.zone.nom,
            warnings=warnings + margin_alert,
            metadata=base_price.metadata,
        )
```

### 18.2 — Tool exposé à l'Agent Devis

```python
@agent_tool(
    name="get_price",
    description="Récupère le prix d'un poste BTP. Retourne None si introuvable.",
)
async def get_price_tool(
    poste: str,
    metier: str,
    quantite: float,
    unite: str,
    zone: str,
    type_chantier: str,
) -> dict:
    result = await pricing_service.get_price(
        poste=poste,
        metier=metier,
        context=PricingContext(
            organization_id=current_org_id,
            zone=resolve_zone(zone),
            type_chantier=type_chantier,
            ...
        ),
    )
    return asdict(result)
```

---

## 19. ALGORITHME COMPLET DE PRICING D'UN DEVIS

### 19.1 — End-to-end workflow

**Input** : vocal artisan "Devis pour Mme Martin, SDB complète 8m², Marseille, logement > 10 ans"

```
1. STT Whisper → texte

2. Agent Devis (Opus) parse l'intent :
   - Client : Mme Martin (lookup Mem0 OU création fiche)
   - Chantier : SDB complète 8m²
   - Localisation : Marseille (zone PACA, coef 1.15)
   - Contexte : rénovation logement > 10 ans → TVA 10%

3. Agent Devis décompose en postes (via Mem0 + référentiels métier) :
   - Dépose sanitaires existants (1 forfait)
   - Évacuation gravats (1 forfait)
   - Plomberie encastrée (1 forfait)
   - Pose receveur douche + paroi (1 forfait)
   - Pose WC suspendu (1 forfait)
   - Pose meuble vasque + robinetterie (1 forfait)
   - Étanchéité sol + murs douche (8 m² + 12 m²)
   - Carrelage mural (25 m²)
   - Carrelage sol (8 m²)
   - Joints silicone périphériques (1 forfait)
   - Finitions peinture plafond (3 m²)

4. Pour chaque poste, appel `get_price_tool()` :
   - Mem0 artisan → prix perso si existe
   - Référentiel → lookup fichier JSON métier
   - Application coef zone PACA (1.15)
   - Détermination TVA (10% sur ensemble)

5. Agrégation :
   - Total HT : 4 800 €
   - TVA 10% : 480 €
   - Total TTC : 5 280 €

6. Scores confidence :
   - 8 lignes 🟢 (prix personnels artisan)
   - 3 lignes 🟡 (référentiel standard)
   - 0 ligne 🔴
   → devis globalement 🟡

7. Mentions obligatoires appliquées :
   - IDENTITE_ARTISAN
   - DECENNALE
   - TVA_10_RENOVATION (+ attestation client)
   - VALIDITE_DEVIS (30 jours)
   - ACOMPTE (30%)

8. PDF/A-3 Factur-X généré + envoyé à Mme Martin par WhatsApp

9. Tracking :
   - `ai_decisions` loggés (audit AI Act)
   - `llm_costs_daily` mis à jour
   - `tasks_to_validate` si ligne 🔴 avait existé
```

### 19.2 — Durée cible end-to-end

- STT + parse : ~2s
- Décomposition postes : ~3s
- Lookup prix (11 postes × ~300ms) : ~3s
- Agrégation + TVA + mentions : ~1s
- PDF generation : ~2s
- Envoi WhatsApp : ~1s

**Total : ~12s** pour un devis complet généré vocalement. Vs 1-2h en manuel.

---

## 20. PERFORMANCE ET CACHE

### 20.1 — Cache Redis

Référentiels chargés en RAM (dictionnaire Python) au démarrage backend.

Lookup benchmark : cache Redis 1h par `(poste, metier, department)`.

Lookup Mem0 : cache Redis 10 min par `(organization_id, poste)`.

Lookup web fallback : cache `web_prices_cache` 30 jours.

### 20.2 — Metrics cibles

| Opération | Cible P95 |
|---|---|
| Lookup Mem0 | <150ms |
| Lookup référentiel | <50ms |
| Lookup benchmark | <200ms |
| Lookup web fallback | <5s |
| Pricing ligne devis complète | <300ms |
| Pricing devis 10 lignes | <3s |

### 20.3 — Batch processing

Quand l'Agent Devis a plusieurs postes à pricer → batch en parallèle :

```python
results = await asyncio.gather(
    *[pricing_service.get_price(poste, metier, ctx) for poste in postes]
)
```

---

## 21. TESTS ET VALIDATION

### 21.1 — Tests unitaires

`backend/tests/test_pricing_service.py` :
- Test lookup Mem0 (mocké)
- Test lookup référentiel
- Test lookup benchmark
- Test fallback web
- Test coefficient zone (toutes zones)
- Test TVA multi-taux (tous cas)
- Test anti-invention (pas de source → erreur)
- Test alertes cohérence

### 21.2 — Tests données

`backend/tests/test_prix_data_integrity.py` :
- Chaque fichier `data/prix/*.json` valide le schéma
- Cohérence interne (prix_min ≤ prix_median ≤ prix_max)
- Unicité des `id`
- TVA possibles cohérentes avec catégorie

### 21.3 — Tests end-to-end

Scénarios complets avec beta-testeurs :
- Devis SDB complète → comparaison prix vs devis artisan réel
- Devis pose carrelage 40x40 → validation prix Sud-Est
- Devis rénovation énergétique → TVA 5.5% correctement appliquée

### 21.4 — Validation continue

Chaque nouveau fichier métier passe par :
1. Review par un artisan expert du métier
2. Validation par Fabrice (cohérence globale)
3. Tests A/B sur 10 devis réels
4. Itération si écart > 15% systématique

---

## 22. GOUVERNANCE DES RÉFÉRENTIELS

### 22.1 — Workflow update

```
1. Identification d'un besoin (feedback artisan, nouveau poste)
2. PR sur repo avec modifs data/prix/*.json
3. Review par Fabrice + 1 artisan beta-testeur
4. Merge → CI valide schéma JSON
5. Deploy → nouveau pricing actif
```

### 22.2 — Versioning

Champ `version` dans chaque fichier.

Changelog dans `data/prix/CHANGELOG.md`.

### 22.3 — Pas de rétro-édition

Un devis déjà émis conserve **les prix utilisés à sa génération** (snapshot dans `devis_lines`), même si le référentiel change plus tard.

### 22.4 — Fréquence de révision

- **Revue mensuelle** légère : petits ajustements inflation
- **Revue trimestrielle** complète : vérification marchés
- **Revue annuelle** majeure : ajout nouveaux postes, refonte catégories

### 22.5 — Contribution artisans

Post-launch, feature "Suggère une correction" pour que les artisans puissent remonter des prix incohérents (crowd-sourcing).

---

## 23. ADAPTATION MULTI-PAYS (F135)

### 23.1 — Architecture

Chaque pays a son propre dossier `data/prix/{country_code}/` :
- `FR/` (launch juin 2026)
- `BE/` (expansion 2 mois)
- `CH/` (expansion 2 mois)
- `PT/` (expansion 3 mois)
- `ES/` (expansion 4 mois)
- `IT/`, `DE/`, `GB/` (expansion 2027)

### 23.2 — Différences par pays

- **TVA différente** (BE : 6%/21%, ES : 10%/21%, DE : 7%/19%)
- **Coefficients régionaux** (Zurich > Genève > Lucerne en CH)
- **Normes techniques** (DTU FR, NBN BE, SIA CH)
- **Conventions collectives** (IDCC FR, CCT BE)
- **E-invoicing** (FR PPF, ES FacturaE, IT SDI, DE XRechnung)

### 23.3 — Priorisation

Launch : **FR uniquement**. Autres pays = post 2K MRR.

Voir `docs/DISTRIBUTION.md` pour roadmap expansion.

---

## 24. TROUBLESHOOTING

### 24.1 — Problèmes fréquents

| Symptôme | Cause | Solution |
|---|---|---|
| Prix anormal sur devis | Semantic search a mal matché | Vérifier `normalize_poste()` + ajouter synonymes |
| Beaucoup de lignes 🔴 | Référentiels incomplets | Enrichir `data/prix/*.json` |
| Zone pas détectée | Adresse mal parsée | Utiliser code postal explicite |
| TVA 5.5% refusée à tort | RGE pas vérifié | Check `organizations.rge_certifications` |
| Coef zone pas appliqué | Prix en centimes non normalisé | Passer `source_zone` explicite |
| Mem0 ne retourne rien | <2 facturations | Normal, fallback référentiel |
| Web fallback appelé souvent | Référentiels trop partiels | Audit + enrichissement |

### 24.2 — Debug commands

```python
# Tester lookup direct
python -c "
from app.services.pricing_service import pricing_service
import asyncio
print(asyncio.run(pricing_service.get_price(
    poste='pose carrelage 40x40 rectifie',
    metier='carrelage',
    context=...
)))
"

# Stats cache
redis-cli INFO keyspace | grep pricing
```

### 24.3 — Validation data

```bash
# Vérifier intégrité tous fichiers prix
python scripts/validate_pricing_data.py

# Sortie : 
# ✅ plomberie.json: 342 postes, 0 erreur
# ⚠️ carrelage.json: 187 postes, 1 warning (prix_min > prix_median sur id=xxx)
# ❌ electricite.json: 256 postes, 3 erreurs
```

---

## 25. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/METIER.md` §9 + §10 | Coefficients + règle zéro invention |
| `docs/AGENTS.md` §2 Agent Devis | Utilisateur principal du moteur |
| `docs/AGENTS.md` §14 Coach Business | Benchmarks F119 |
| `docs/MEMORY.md` | Mem0 prix perso artisan |
| `docs/MEMORY-STRATEGY.md` | Stockage facts prix |
| `docs/API.md` §7 | Endpoint `/v1/devis/*` |
| `docs/ARCH.md` §4 | pricing_service dans services |
| `docs/MIGRATIONS.md` §5 | knowledge_chunks, tva_rules |
| `docs/ERRORS.md` §19 | `METIER_REFERENCE_PRICE_NOT_FOUND` |
| `docs/AI-ACT-COMPLIANCE.md` §9 | Logging ai_decisions |
| `docs/I18N.md` (à créer) | Adaptation multi-langue |
| `docs/FEATURES_COMPLETE.md` F119/F121 | Coach Business + Détecteur concurrent |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` §13 | Zones canoniques |
| `CLAUDE.md` §Prix | Règles prix |
| `FICHE_METIER.md` | Structure data/prix |
| `BUILD_PLAN.md` | `backend/data/prix/` |
| `COUT_REEL.md` | Budget Mem0 + Web fallback |
| **11 fichiers métier (`docs/metier/*.md`)** | 8819 lignes knowledge base |

### 25.1 — Références externes

- **FFB barèmes** : https://www.ffbatiment.fr
- **CAPEB référentiels** : https://www.capeb.fr
- **Batirama Observatoire prix BTP** : https://www.batirama.com
- **DTU norms** : https://boutique.afnor.org/

---

> **Ce fichier est le cœur du moteur de prix STRUCTORAI.**
> **Règle d'or absolue** : **zéro prix inventé**. Tout prix a une source tracée.
> **Différenciation concurrentielle** : 1080+ postes référencés dans 11 métiers — aucun concurrent n'a cette profondeur.
> **Effet réseau** : plus il y a d'artisans, plus les benchmarks sont précis.
> **Apprentissage continu** : les prix validés enrichissent Mem0 (pricing perso) + benchmark (pricing collectif anonymisé).
