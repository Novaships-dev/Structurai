---
name: MEMORY
description: Architecture mémoire STRUCTORAI — décision tranchée hybride Mem0 + MemPalace avec mapping BTP (wings/rooms/drawers), règles de routage quoi va où, niveaux de mémoire, enrichissement automatique. Complémentaire de docs/MEMORY-STRATEGY.md (fallback + procédures ops).
type: architecture
scope: all-agents, memory-layer, knowledge-base, rag-engine
priority: immuable
date: 2026-04-17
version: 1.0
decision: HYBRIDE Mem0 + MemPalace (validé audit V7, Fabrice 17/04/2026)
---

# docs/MEMORY.md — Architecture mémoire STRUCTORAI

> **Décision tranchée** : architecture **hybride Mem0 + MemPalace**.
> **Ce fichier décrit le QUOI** (architecture, mapping BTP, règles de routage).
> **Le COMMENT opérationnel** (fallback, migration, monitoring) est dans `docs/MEMORY-STRATEGY.md`.

---

## 0. SOMMAIRE

1. [La décision — pourquoi hybride](#1-la-décision--pourquoi-hybride)
2. [Les 4 sources de vérité du cerveau IA](#2-les-4-sources-de-vérité-du-cerveau-ia)
3. [Mem0 — rôle et usage](#3-mem0--rôle-et-usage)
4. [MemPalace — rôle et usage](#4-mempalace--rôle-et-usage)
5. [Mapping BTP — wings / rooms / drawers](#5-mapping-btp--wings--rooms--drawers)
6. [Règles de routage — quelle donnée va où](#6-règles-de-routage--quelle-donnée-va-où)
7. [Patterns de recall chaîné](#7-patterns-de-recall-chaîné)
8. [Couches de mémoire (user / session / agent)](#8-couches-de-mémoire-user--session--agent)
9. [Auto-enrichissement](#9-auto-enrichissement)
10. [Souveraineté des données & RGPD](#10-souveraineté-des-données--rgpd)
11. [Score de confiance et règle zéro invention](#11-score-de-confiance-et-règle-zéro-invention)
12. [Intégration avec Supabase et RAG](#12-intégration-avec-supabase-et-rag)
13. [Maintenance et évolution](#13-maintenance-et-évolution)

---

## 1. LA DÉCISION — POURQUOI HYBRIDE

### 1.1 — Les 3 options évaluées

| Option | Avantages | Inconvénients | Décision |
|---|---|---|---|
| **Mem0 seul** | Mature (91K⭐), stable production, SDK propre (pip install mem0ai), cloud EU configurable | Verbatim pauvre (résumés auto), knowledge graph limité, données transitent chez Mem0 cloud (ou self-hosted plus complexe) | ❌ Rejeté seul |
| **MemPalace seul** | 100% local (ChromaDB + SQLite), benchmark R@5 96,6% vs ~65-75% Mem0, knowledge graph temporel natif, 29 MCP tools, RGPD parfait | Jeune (23K⭐, projet récent), pas de SDK python officiel stable, risque d'instabilité en production | ❌ Rejeté seul |
| **Hybride Mem0 + MemPalace** | Stabilité Mem0 pour le critique + richesse MemPalace pour le verbatim + souveraineté 100% EU + fallback possible | Double intégration, complexité maintenance | ✅ **ADOPTÉ** |

### 1.2 — Pourquoi cette combinaison gagne

**Mem0 = le socle stable** : profil artisan, patterns structurés légers (prix, clients, fournisseurs), résumés. Mature, l'artisan ne verra jamais de bug.

**MemPalace = la richesse** : conversations vocales verbatim, knowledge graph temporel (paiements clients dans le temps, évolutions prix matériaux, expirations RGE), devis archivés complets. 100% local sur Railway EU.

**Fallback garanti** : si MemPalace présente >5% d'instabilité en production, bascule auto vers **Mem0 seul** en 2h sans downtime (voir `docs/MEMORY-STRATEGY.md`).

**Argument marketing majeur** : "Souveraineté des données 100% EU" — différenciateur massif vs concurrents qui hébergent aux US.

---

## 2. LES 4 SOURCES DE VÉRITÉ DU CERVEAU IA

Le cerveau IA STRUCTORAI hiérarchise ses sources dans cet ordre STRICT :

| Priorité | Source | Contenu | Localisation |
|---|---|---|---|
| **1** | **Mem0 / MemPalace** (mémoire artisan) | Prix perso, clients, fournisseurs, conversations, style | Railway EU |
| **2** | **Base de connaissances BTP** (RAG) | `data/legal/*.json`, `data/prix/*.json`, 11 référentiels métier, `docs/METIER.md` | Supabase EU + vector store |
| **3** | **Claude API** (raisonnement général) | Raisonnement et interprétation, **toujours flaggué "estimation"** | Anthropic US (DPA signé) |
| **4** | **Recherche web** (fallback) | Info non trouvée dans 1-3 → web → stockée dans base pour apprentissage | Fallback dernier recours |

**Règle absolue** : le cerveau cherche dans cet ordre. Si la priorité 1 a l'info, il n'escalade pas aux suivantes. Si il escalade, il le flag dans sa réponse ("je suis allé chercher sur le web parce que je n'avais pas l'info chez toi").

---

## 3. MEM0 — RÔLE ET USAGE

### 3.1 — Type de données stockées

**Ce que Mem0 stocke (patterns structurés légers) :**

| Catégorie | Exemples |
|---|---|
| **Profil artisan** | Statut juridique (EURL/SAS/Micro), métier principal, zone géographique, taille entreprise, certifications RGE/Qualibat, régime TVA |
| **Prix perso** | "Pose carrelage 60×60 : 45€/m²", "Receveur Wedi Fundo : 380€ HT posé", "Taux horaire plombier : 52€/h" |
| **Patterns clients** | "Martin paie toujours 10 jours en retard", "Dupont négocie toujours 10%", "Mme Leroy préfère les virements" |
| **Fournisseurs habituels** | "Chez Point P tu as -8% carrelage", "Cedeo livre en 24h", "BigMat moins cher sur l'isolation" |
| **Style & préférences** | "L'artisan signe ses devis 'Cordialement, Jean'", "Inclut toujours évacuation gravats", "Niveau de détail : moyen" |
| **Apprentissages** | "L'artisan a corrigé le prix de la dépose baignoire de 280€ à 320€ → retenir 320€" |

### 3.2 — Intégration technique

**SDK** : `mem0ai` (pip install mem0ai)

**Hébergement** : Mem0 cloud **région EU** (configurable via variable d'environnement `MEM0_REGION=eu-west-1`).

**Code d'exemple** :

```python
# Stocker un prix artisan
await mem0.add(
    user_id=artisan_id,
    data="Prix pose carrelage 60×60 : 45€/m²",
    metadata={
        "type": "prix",
        "metier": "carrelage",
        "zone": "paris_intra",
        "confidence": "high"
    }
)

# Récupérer avant de proposer un prix
memories = await mem0.search(
    user_id=artisan_id,
    query="prix carrelage",
    limit=5
)
```

### 3.3 — Namespace par organisation

**Règle critique multi-tenant** : chaque requête Mem0 est filtrée par `organization_id` (pas seulement `user_id`), pour isoler les données entre artisans d'une même entreprise et entre entreprises.

Voir `SKILL-MEM0-INTEGRATION.md` (à créer) pour le pattern complet.

---

## 4. MEMPALACE — RÔLE ET USAGE

### 4.1 — Type de données stockées

**Ce que MemPalace stocke (verbatim riche + knowledge graph) :**

| Catégorie | Exemples |
|---|---|
| **Conversations vocales verbatim** | Transcriptions WhatsApp complètes, échanges avec le client en vocal, briefs chantier |
| **Devis archivés complets** | PDF + XML Factur-X + contexte conversationnel ayant mené au devis |
| **Factures archivées** | Archives 10 ans (obligation légale) avec contexte |
| **Knowledge graph temporel** | Évolution des paiements d'un client dans le temps, évolution des prix matériaux, expirations RGE/Qualibat, renouvellement assurance décennale |
| **Photos chantiers** | Métadonnées enrichies : "Photo avant du chantier Dupont, prise le 15/03/2026 à 9h12 GPS Marseille 13008, associée au devis DEV-2026-023" |
| **Courriers admin scannés** | OCR + classification + action prise + suite donnée |
| **Historique visites chantier** | "3 visites Dupont : 10/03 diagnostic, 15/03 début travaux, 22/03 réception" |

### 4.2 — Intégration technique

**Stockage** : 100% local sur Railway EU.
- **ChromaDB** : vector store embeddings pour recherche sémantique
- **SQLite embarqué** : structure knowledge graph + métadonnées

**Aucune dépendance cloud externe** — toute la mémoire verbatim reste sur le serveur STRUCTORAI.

**SDK** : pattern wrapper custom (pas de SDK officiel stable, on wrappera les 29 MCP tools).

**Backup** : snapshots SQLite + ChromaDB quotidiens vers Supabase Storage EU.

### 4.3 — Les 29 MCP tools natifs

MemPalace expose 29 outils MCP (Model Context Protocol) pour l'interaction avec la mémoire. Les plus utilisés :

| Tool | Usage STRUCTORAI |
|---|---|
| `recall_verbatim` | Retrouver une conversation exacte |
| `search_knowledge_graph` | Requête temporelle ("comment Martin payait-il il y a 6 mois ?") |
| `trace_relations` | Relations client-chantier-devis-facture |
| `temporal_query` | "Quand le prix du cuivre a-t-il changé pour la dernière fois ?" |
| `store_verbatim` | Enregistrer une conversation vocale complète |
| `link_entities` | Lier un devis à son client et au chantier associé |

Voir `SKILL-MEMPALACE-INTEGRATION.md` (priorité 3, si décision MemPalace confirmée) pour liste exhaustive.

---

## 5. MAPPING BTP — WINGS / ROOMS / DRAWERS

MemPalace structure la mémoire en **wings** (ailes) → **rooms** (pièces) → **drawers** (tiroirs). Voici le mapping spécifique BTP de STRUCTORAI.

### 5.1 — Les 6 wings principales

| Wing | Contenu |
|---|---|
| **Wing 1 — Business** | Devis, factures, chantiers, acomptes, paiements, retenues de garantie |
| **Wing 2 — Clients & Contacts** | Fiches clients, contacts pro (archis, apporteurs), historique interactions, leads |
| **Wing 3 — Opérationnel** | Planning chantiers, pointage heures, tâches employés, matériaux, déplacements |
| **Wing 4 — Fiscal & Admin** | Courriers URSSAF/impôts, TVA, cotisations, calendrier fiscal, déclarations |
| **Wing 5 — Marketing & Réputation** | Avis Google, publications réseaux sociaux, prospection, site web, SEO local |
| **Wing 6 — Connaissance métier** | Prix personnels, patterns chantiers, apprentissages, corrections |

### 5.2 — Détail rooms et drawers par wing

#### Wing 1 — Business

```
Wing 1 — Business
├── Room: Devis
│   ├── Drawer: Devis en cours (non signés)
│   ├── Drawer: Devis signés (archives)
│   ├── Drawer: Devis refusés (pour analyse)
│   └── Drawer: Templates réutilisables
├── Room: Factures
│   ├── Drawer: Factures à émettre
│   ├── Drawer: Factures envoyées
│   ├── Drawer: Factures payées (archive 10 ans)
│   ├── Drawer: Avoirs et rectificatives
│   └── Drawer: Factures de situation (avancement BTP)
├── Room: Chantiers
│   ├── Drawer: À commencer
│   ├── Drawer: En cours
│   └── Drawer: Terminés
└── Room: Trésorerie
    ├── Drawer: Encaissements
    ├── Drawer: Impayés (J+15 / J+30 / J+45)
    └── Drawer: Retenues de garantie 5%
```

#### Wing 2 — Clients & Contacts

```
Wing 2 — Clients & Contacts
├── Room: Clients particuliers (B2C)
│   ├── Drawer: Actifs (chantier en cours ou terminé <1 an)
│   ├── Drawer: Historique (>1 an)
│   └── Drawer: Leads non convertis
├── Room: Clients pros (B2B)
│   ├── Drawer: Architectes
│   ├── Drawer: Apporteurs d'affaires
│   ├── Drawer: Agences immobilières
│   └── Drawer: Entreprises générales (sous-traitance)
└── Room: Fournisseurs
    ├── Drawer: Point P / Cedeo / BigMat (grossistes BTP)
    ├── Drawer: Leroy Merlin / Brico Dépôt (GSB)
    └── Drawer: Spécialisés (sanitaire, électricité, carrelage)
```

#### Wing 3 — Opérationnel

```
Wing 3 — Opérationnel
├── Room: Planning
│   ├── Drawer: Cette semaine
│   ├── Drawer: Ce mois
│   └── Drawer: Trimestre suivant
├── Room: Heures & Employés
│   ├── Drawer: Pointage par employé
│   ├── Drawer: Heures sup (25% / 50%)
│   └── Drawer: Congés CIBTP
├── Room: Matériaux & Achats
│   ├── Drawer: Commandes en cours
│   ├── Drawer: Livraisons chantier
│   └── Drawer: Stock matériel
└── Room: Déplacements
    ├── Drawer: Frais km
    ├── Drawer: Paniers repas
    └── Drawer: Indemnités zones BTP 1A→5
```

#### Wing 4 — Fiscal & Admin

```
Wing 4 — Fiscal & Admin
├── Room: URSSAF & Cotisations
├── Room: TVA
│   ├── Drawer: TVA collectée
│   ├── Drawer: TVA déductible
│   └── Drawer: Déclarations trimestrielles/mensuelles
├── Room: Courriers administratifs scannés
├── Room: Attestations & certifications
│   ├── Drawer: Décennale (date validité, n° police)
│   ├── Drawer: RGE (date validité, vérification mensuelle auto)
│   └── Drawer: Qualibat / Qualifelec / Qualit'EnR
└── Room: Calendrier fiscal par statut
    ├── Drawer: Micro-entreprise
    ├── Drawer: EURL
    ├── Drawer: SAS / SASU
    └── Drawer: EI
```

#### Wing 5 — Marketing & Réputation

```
Wing 5 — Marketing & Réputation
├── Room: Avis Google
│   ├── Drawer: Avis reçus (5★/4★/3★/2★/1★)
│   ├── Drawer: Réponses données (avec mots-clés SEO)
│   └── Drawer: Demandes SMS envoyées
├── Room: Réseaux sociaux
│   ├── Drawer: Facebook
│   ├── Drawer: Instagram
│   └── Drawer: Google Business Profile
├── Room: Prospection pro
│   └── Drawer: Relances archis/apporteurs
└── Room: Site web vitrine IA
    ├── Drawer: Pages (accueil, prestations, galerie, avis)
    └── Drawer: Historique mises à jour mensuelles
```

#### Wing 6 — Connaissance métier

```
Wing 6 — Connaissance métier (la plus importante — c'est le moat)
├── Room: Prix personnels
│   ├── Drawer: Plomberie
│   ├── Drawer: Électricité
│   ├── Drawer: Carrelage
│   ├── Drawer: Peinture
│   └── Drawer: (1 par corps de métier, 11 total)
├── Room: Patterns chantiers
│   ├── Drawer: "SDB complète" — ce que j'inclus toujours
│   ├── Drawer: "Ravalement façade" — ce que j'inclus toujours
│   └── Drawer: Templates type par chantier
├── Room: Corrections & apprentissages
│   └── Drawer: "Prix dépose baignoire corrigé de 280€ à 320€"
└── Room: Style & ton
    ├── Drawer: Formules devis habituelles
    ├── Drawer: Ton relances clients (ferme vs doux)
    └── Drawer: Signature devis
```

---

## 6. RÈGLES DE ROUTAGE — QUELLE DONNÉE VA OÙ

**Règle générale** : **Mem0** pour les patterns légers, **MemPalace** pour le verbatim riche + knowledge graph temporel.

### 6.1 — Tableau de routage détaillé

| Type de donnée | Mem0 | MemPalace | Supabase DB |
|---|---|---|---|
| Profil artisan (statut, métier, RGE) | ✅ | ❌ | ✅ (source officielle) |
| Prix personnels | ✅ (patterns) | ❌ | ❌ |
| Patterns clients (paye en retard, négocie) | ✅ | ❌ | ❌ |
| Fournisseurs habituels | ✅ (résumé) | ❌ | ✅ (fiches) |
| Style rédactionnel devis | ✅ | ❌ | ❌ |
| Apprentissages / corrections | ✅ | ❌ | ❌ |
| **Conversation vocale WhatsApp complète** | ❌ | ✅ (verbatim) | ❌ |
| **Transcription voice call** | ❌ | ✅ (verbatim) | ❌ |
| Devis PDF + XML Factur-X | ❌ | ✅ (archive) | ✅ (métadonnées) |
| Facture PDF + XML | ❌ | ✅ (archive 10 ans) | ✅ (métadonnées) |
| Photo chantier | ❌ | ✅ (métadonnées enrichies) | ✅ (storage URL) |
| Ticket de caisse OCR | ❌ | ✅ (verbatim OCR) | ✅ (catégorisation) |
| Fiches clients/chantiers (structuré) | ❌ | ❌ | ✅ (source) |
| Heures de travail pointées | ❌ | ❌ | ✅ |
| **Knowledge graph paiements client dans le temps** | ❌ | ✅ | ❌ |
| **Évolution prix matériaux dans le temps** | ❌ | ✅ | ❌ |
| **Expirations RGE/Qualibat/décennale** | ❌ | ✅ | ✅ (alerte) |

### 6.2 — Pourquoi cette répartition

**Mem0 gagne quand** :
- La donnée est structurée et légère (prix, pattern, préférence)
- Le cerveau doit retrouver rapidement par query sémantique courte
- L'info change peu ou évolue lentement
- Stabilité prioritaire (Mem0 mature en prod)

**MemPalace gagne quand** :
- On veut le verbatim exact (preuve, protection juridique)
- On a besoin du contexte conversationnel complet
- La dimension temporelle est critique (évolutions, historique)
- La donnée est volumineuse (photos, PDFs, transcriptions)
- La souveraineté stricte est requise (jamais chez un tiers cloud)

**Supabase DB gagne quand** :
- La donnée est structurée et nécessite des requêtes SQL (tables relationnelles)
- Le multi-tenant RLS est critique
- La donnée est source de vérité métier (fiche client, devis ID, statut chantier)

---

## 7. PATTERNS DE RECALL CHAÎNÉ

### 7.1 — Exemple : génération d'un devis

Quand l'Agent Devis doit générer un devis "SDB complète pour M. Dupont", il exécute :

```
1. Supabase DB → fiche client Dupont (adresse, contact, historique chantiers)
2. MemPalace → recherche conversations verbatim avec Dupont (préférences exprimées, style)
3. Mem0 → "prix pose carrelage artisan", "style devis habituel", "taux horaire"
4. Knowledge base RAG → référentiel métier `data/prix/carrelage.json` pour référence marché
5. docs/METIER.md → TVA applicable (5.5/10/20% selon conditions) + 47 mentions
6. Claude API → assemblage final avec raisonnement sur les postes

→ Devis généré en 2 minutes avec TOUT le contexte disponible
```

### 7.2 — Exemple : relance facture impayée

Quand l'Agent Relance doit envoyer une relance à Martin (J+15 impayée) :

```
1. Supabase DB → facture impayée FAC-2026-042 (montant, date échéance)
2. MemPalace → toutes les conversations avec Martin (pour adapter le ton)
3. Mem0 → pattern "Martin paie toujours 10j en retard" → ton plus doux
4. Mem0 → style rédactionnel relance de l'artisan
5. docs/METIER.md → taux intérêt légal 2,62% + plancher 7,86% + indemnité 40€

→ Relance adaptée envoyée, ton ajusté, mentions légales incluses
```

### 7.3 — Exemple : contrôle fiscal (F129)

Quand l'artisan déclenche un export contrôle fiscal :

```
1. Supabase DB → toutes les factures/devis de la période (métadonnées)
2. MemPalace → archives PDF/XML complets (verbatim)
3. MemPalace → tickets OCR originaux (preuves)
4. MemPalace → attestations scannées (décennale, RGE, etc.)
5. docs/METIER.md → vérification conformité mentions + TVA

→ ZIP signé horodaté avec TOUT exporté
```

---

## 8. COUCHES DE MÉMOIRE (USER / SESSION / AGENT)

Mem0 supporte 3 niveaux de mémoire. STRUCTORAI les utilise ainsi :

### 8.1 — Mémoire USER (artisan)

**Durée de vie** : persistante à vie (jusqu'à suppression compte RGPD).
**Contenu** : profil, prix, patterns, style. Tout ce qui est "connaissance durable de l'artisan".
**Namespace** : `user_id={artisan_id}`

### 8.2 — Mémoire SESSION (conversation en cours)

**Durée de vie** : 24h maximum (puis promue en mémoire USER si pertinente).
**Contenu** : contexte de la conversation courante, état intermédiaire, choix non encore validés.
**Namespace** : `session_id={uuid}`
**Usage** : éviter de redemander "c'était quel client déjà ?" au milieu d'une conversation longue.

### 8.3 — Mémoire AGENT (par agent spécialisé)

**Durée de vie** : persistante mais propre à chaque agent.
**Contenu** : patterns d'usage d'un agent pour cet artisan. Ex : l'Agent Relance apprend le ton préféré de l'artisan, l'Agent Devis apprend ses prix, l'Agent Fiscalité apprend les questions fréquentes.
**Namespace** : `user_id={artisan_id}, agent_id={agent_name}`

### 8.4 — Mémoire ORGANIZATION (partagée)

**Durée de vie** : persistante.
**Contenu** : données partagées entre plusieurs utilisateurs d'une même entreprise (Business plan 2-10 salariés).
**Namespace** : `organization_id={org_id}` (CRITIQUE pour RLS multi-tenant)
**Règle** : chaque requête Mem0 et MemPalace DOIT filtrer par `organization_id` pour isoler entre entreprises.

---

## 9. AUTO-ENRICHISSEMENT

### 9.1 — La boucle d'enrichissement (6 mécanismes)

1. **Recherche web → Base métier.** Chaque recherche web faite par le cerveau (prix, règle, fournisseur) est validée, structurée et enregistrée en base métier. Le cerveau ne cherche JAMAIS la même info 2 fois.

2. **Réponse artisan → Mem0 + Base métier.** Quand le cerveau demande "quel prix pour un receveur Wedi ?" et l'artisan répond "380€", l'info va dans :
   - Mem0 de l'artisan (prix perso)
   - Base métier comme data point anonymisé (enrichit le prix moyen marché)

3. **Devis validés → Templates enrichis.** Chaque devis validé enrichit les templates métier. Si 50 plombiers incluent tous "évacuation gravats" → le template est mis à jour automatiquement.

4. **Corrections artisan → Apprentissage.** Si l'artisan corrige un prix ou un poste :
   - Mem0 stocke la correction (ne refait plus l'erreur)
   - Si N artisans font la même correction → base métier mise à jour

5. **Veille réglementaire automatique.** Agent de background qui scanne Légifrance / FFB / CAPEB pour détecter évolutions TVA, obligations, Factur-X. Met à jour `docs/METIER.md` et notifie.

6. **Veille prix matériaux.** Agent de background surveille catalogues fournisseurs (Point P, Cedeo, BigMat). Alerte hausse cuivre, carrelage, etc.

### 9.2 — Pipeline technique

```
Recherche web (web_search_service.py)
    ↓
Validation IA (Claude Sonnet) — "est-ce que cette info est fiable ?"
    ↓
Si 🟢 haute confiance → base métier (Supabase `knowledge_base`)
Si 🟡 confiance moyenne → base métier avec flag "à confirmer"
Si 🔴 basse confiance → NON stocké, demande à l'artisan

        ↓
        
Artisan confirme/corrige
    ↓
Mem0 (prix perso) + MemPalace (verbatim conversation) + base métier (agrégé)
```

Service dédié : `backend/app/memory/enrichment.py`

---

## 10. SOUVERAINETÉ DES DONNÉES & RGPD

### 10.1 — Hébergement de chaque composant

| Composant | Hébergeur | Région |
|---|---|---|
| Supabase DB (PostgreSQL) | Supabase | **EU (Frankfurt)** |
| Railway backend (FastAPI) | Railway | **EU** |
| Vercel PWA | Vercel | **EU** |
| Mem0 cloud | Mem0 | **EU (configurable)** |
| **MemPalace** | **Self-hosted Railway** | **100% EU, 0 dépendance externe** |
| ChromaDB (dans MemPalace) | Embedded dans Railway backend | **EU** |
| SQLite (dans MemPalace) | Embedded dans Railway backend | **EU** |
| Supabase Storage (photos, PDFs) | Supabase | **EU** |

### 10.2 — Sous-traitants hors EU (avec DPA)

Uniquement pour les **appels API ponctuels**, pas de stockage persistant de données artisan :

| Sous-traitant | Usage | DPA |
|---|---|---|
| Anthropic (Claude API) | Raisonnement, pas de stockage | ✅ DPA signé |
| OpenAI (Whisper STT) | Transcription ponctuelle, pas de stockage | ✅ DPA signé |
| ElevenLabs (TTS) | Synthèse vocale ponctuelle | ✅ DPA signé |
| Stripe (paiements) | Paiements uniquement | ✅ DPA signé |

### 10.3 — Argument marketing "Souveraineté"

Slogan : *"Tes données restent en France. Pas chez Google, pas chez AWS US."*

Voir `COMMUNICATION.md` Force 3 et `SECURITE.md` section 15.

---

## 11. SCORE DE CONFIANCE ET RÈGLE ZÉRO INVENTION

### 11.1 — Score de confiance sur chaque info mémorisée

Chaque entrée Mem0 et MemPalace a un score de confiance :

| Score | Critère | Exemple |
|---|---|---|
| 🟢 **Haute confiance** (>90%) | Info vérifiée réglementairement OU confirmée par 10+ artisans | TVA 10% travaux rénovation (source Légifrance) |
| 🟡 **Confiance moyenne** (60-90%) | Info de source web fiable OU confirmée 3-9 artisans | "Prix pose carrelage 60×60 zone Paris : 48-55€/m²" |
| 🔴 **Basse confiance** (<60%) | Info unique OU estimation Claude non confirmée | "Prix estimé receveur italien : ~450€" |

### 11.2 — Comportement du cerveau selon le score

- **🟢** : le cerveau affirme, pas besoin de flag
- **🟡** : le cerveau flag "estimation, à confirmer"
- **🔴** : le cerveau **DIT** "je ne suis pas sûr à 100%, tu confirmes ?" — il ne dit jamais de connerie en silence

### 11.3 — Règle zéro invention (voir aussi `docs/METIER.md` §10)

> **Le cerveau IA préfère dire "je ne sais pas" plutôt que d'inventer.**

Un artisan qui envoie un devis avec un prix faux perd sa crédibilité. Un artisan qui envoie un devis avec TVA fausse risque un redressement. La règle est absolue.

---

## 12. INTÉGRATION AVEC SUPABASE ET RAG

### 12.1 — Les 3 couches distinctes

```
┌────────────────────────────────────────────┐
│ COUCHE 2 MÉMOIRE (Mem0 + MemPalace)       │  → patterns artisan + verbatim
└────────────────────────────────────────────┘
          ↑↓  (enrichissement bidirectionnel)
┌────────────────────────────────────────────┐
│ COUCHE 1 CONNAISSANCE (RAG + Knowledge)    │  → base métier BTP anonymisée
│ - data/legal/*.json                         │
│ - data/prix/*.json (11 métiers)             │
│ - docs/METIER.md                            │
│ - knowledge/rag_engine.py                   │
└────────────────────────────────────────────┘
          ↑↓  
┌────────────────────────────────────────────┐
│ SUPABASE DB (données structurées)          │  → fiches, factures, chantiers
│ - 37 migrations SQL                         │
│ - RLS multi-tenant organization_id          │
└────────────────────────────────────────────┘
```

### 12.2 — Quand utiliser quoi — exemples concrets

| Question artisan | Sources interrogées |
|---|---|
| "Quel est le nom du client du chantier Dupont ?" | Supabase DB uniquement |
| "Quel prix j'applique habituellement pour du carrelage 60×60 ?" | Mem0 (prix perso) → fallback base métier |
| "Rappelle-moi ce que Martin m'a dit sur sa cuisine lors de notre dernière conversation" | MemPalace verbatim |
| "Combien de fois Dupont a-t-il payé en retard cette année ?" | MemPalace knowledge graph temporel |
| "Quelle TVA sur une SDB complète en résidence principale ?" | docs/METIER.md (base métier) |
| "Donne-moi le prix moyen marché pour pose faïence 30×60 en région PACA" | Base métier (data/prix) + coefficient régional |

### 12.3 — Migration 017 `agent_memory`

La table `agent_memory` dans Supabase sert à :
- Stocker des **pointeurs** vers Mem0 (pour traçabilité et audit)
- Stocker des **résumés compacts** de MemPalace (pour les recherches SQL rapides)
- **PAS de duplication** des données (Mem0 et MemPalace sont les sources)

---

## 13. MAINTENANCE ET ÉVOLUTION

### 13.1 — Monitoring de la mémoire

Métriques à tracker (voir `docs/MEMORY-STRATEGY.md` pour le détail ops) :

| Métrique | Seuil alerte |
|---|---|
| Taux d'erreur MemPalace | >5% sur 1h → **déclenche fallback Mem0-seul** |
| Latence Mem0 search p95 | >500ms → investigation |
| Latence MemPalace recall p95 | >1s → investigation |
| Taille DB MemPalace par artisan | >500MB → optimisation |
| Taux de recall précis (@5) | <85% → réentraînement embeddings |

### 13.2 — Plan B : MemPalace instable

Voir **`docs/MEMORY-STRATEGY.md`** pour la procédure complète.

Résumé :
1. Désactivation des writes MemPalace (on continue à lire)
2. Redirection writes vers Mem0 avec tags enrichis
3. Migration lot MemPalace → Mem0 en arrière-plan
4. Temps de bascule : **2h sans downtime**

### 13.3 — Évolution future

**V2 (sept-oct 2026)** :
- Évaluer si MemPalace stable après 3-6 mois → possible bascule MemPalace-first
- Ajout Mem0 "Agent Memory" pour chaque nouvel agent V2

**V3 (2027+)** :
- Possibilité de migrer vers un système propriétaire si les outils évoluent
- Intégration MCP tools spécifiques BTP si disponibles

---

## 14. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/METIER.md` | Constitution BTP — TVA, mentions, zones (source vérité base métier) |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` | Chiffres du projet |
| `docs/MEMORY-STRATEGY.md` (à créer) | Fallback, migration, monitoring ops mémoire |
| `CLAUDE.md` | Règles d'or pattern d'invocation Mem0 |
| `PRODUCT_CONTEXT.md` | Couches du cerveau IA (description produit) |
| `BUILD_PLAN.md` | Arborescence `backend/app/memory/` + `knowledge/` |
| `SECURITE.md` §15 | Souveraineté des données |
| `SKILL-MEM0-INTEGRATION.md` (priorité 3) | Pattern code Mem0 |
| `SKILL-MEMPALACE-INTEGRATION.md` (priorité 3) | Pattern code MemPalace + 29 MCP tools |
| `SKILL-HYBRID-MEMORY.md` (priorité 3) | Recall chaîné, quand Mem0 vs MemPalace |

---

> **Ce fichier décrit l'architecture mémoire tranchée et documentée.**
> **La procédure opérationnelle (fallback, migration, monitoring)** est dans `docs/MEMORY-STRATEGY.md`.
> **Les règles métier sur lesquelles la mémoire s'appuie** sont dans `docs/METIER.md`.
