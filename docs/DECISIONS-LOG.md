---
name: DECISIONS-LOG
description: Log des valeurs de référence tranchées et décisions appliquées lors de l'audit cohérence du 17/04/2026. Rapport fichier par fichier.
type: reference
date: 2026-04-17
author: Claude Code
---

# DECISIONS-LOG — Audit cohérence STRUCTORAI

> Ce fichier logue les décisions prises pendant l'audit cohérence du 17/04/2026.
> Chaque valeur ci-dessous est la **source de vérité unique** pour tout le repo.
> En cas de contradiction dans un autre fichier, c'est ce log qui fait foi.

---

## 1. Valeurs de référence (source de vérité)

| Sujet | Valeur tranchée | Justification |
|---|---|---|
| Mentions obligatoires devis | **47** (15 légales + 32 complémentaires BTP) | Respect Code consommation + Code commerce + BTP |
| Migrations SQL | **29** | Après fix du doublon 012 (renumérotation + fusion `create_metier_rules` + `seed_metier_rules` en une seule migration 023) |
| Coefficients régionaux | **11 zones** (Paris / IDF / Grand Est / Nord / Ouest / Sud-Ouest / Sud-Est / Méditerranée / Montagne / DOM / Corse) | Granularité BTP nécessaire |
| Features V1 | **110** | Inventaire complet dans FEATURES.md |
| Sprints | **9** (Sprint 0 à Sprint 8) + Sprint V1.5 post-launch | Somme confirmée dans BUILD_PLAN.md |
| Durée build | **~22 jours Claude Code** | 1+2+3+3+3+2+3+3+2 = 22 j |
| Agents V1 | **13 agents + 1 Supervisor + 1 V2 (Téléphone IA)** | Supervisor est orchestrateur, hors des 13 |
| Modèle Claude principal | `claude-sonnet-4-6` | Agents standards |
| Modèle Claude complexe | `claude-opus-4-7` | Devis complexes + Vision IA critique |
| Modèle Claude léger | `claude-haiku-4-5-20251001` | Tâches légères (relance, SMS, catégorisation) |
| Nom produit marketing | **StructorAI** | I majuscule, prose |
| Nom code/URLs | `structorai` | Minuscule, filenames |
| Nom hero banner | **STRUCTORAI** | Capitales |
| Variantes bannies | `Structorai`, `Structurai` | Aucune occurrence tolérée |
| Plan gratuit | **Starter** (€0) | Ne jamais appeler "Free" ni "Free tier" |
| Data files (FICHE_METIER.md) | 63 fichiers data référencés | Compte `.json` dans FICHE_METIER.md |
| Coût LLM par artisan/mois | **$4.50 optimisé** | Suite audit 14/04 (remplace $3.30) |
| Marge Pro (€29) | **82%** | Suite audit 14/04 (remplace 86%) |
| Marge Business (€79) | **91%** | Suite audit 14/04 (remplace 92%) |
| Comptage modules | **13 agents IA + 1 Supervisor + 4 modules fonctionnels (Galerie photo, Gamification, Mesure IA, Dossier À faire) = 18 composants** | Remplace "15 modules", "16 modules", "12 modules" |
| Nommage agents Python | Tous préfixés `agent_` : `agent_devis.py`, `agent_relance.py`, etc. | Uniformisation |

---

## 2. Rapport de modifications fichier par fichier

### CLAUDE.md
- Table SOURCES DE VÉRITÉ : suppression de tous les compteurs de lignes entre parenthèses pour les 10 fichiers cités
- Stack technique : `claude-haiku-4-5` → `claude-opus-4-7 (Devis complexes, Vision IA critique), claude-sonnet-4-6 (agents standards), claude-haiku-4-5-20251001 (tâches légères)`
- Tableau des 13 agents : Supervisor marqué "hors des 13" ; tous les fichiers agents préfixés `agent_` ; modèles Claude avec suffix date pour Haiku ; Devis et Vision IA passés à `claude-opus-4-7`
- Ajout d'une ligne récapitulative sous le tableau : "Total : 13 agents IA + 1 Supervisor + 4 modules fonctionnels = 18 composants"
- Règles métier BTP : `coefficients régionaux (9 zones)` → `(11 zones)`
- Exemple code `class DevisAgent` : `claude-sonnet-4-6` → `claude-opus-4-7`
- Patterns critiques : `switch_model("claude-haiku-4-5")` → `"claude-haiku-4-5-20251001"`
- Exemple nommage fichier : `app/agents/devis.py` → `app/agents/agent_devis.py` (ajout commentaire pattern)

### BUILD_PLAN.md
- Migrations SQL renumérotées : 2ᵉ `012_create_avis_google.sql` → `013_create_avis_google.sql` ; décalage +1 pour 013→014, 014→015, … jusqu'à 025
- Fusion de `022_create_metier_rules.sql` + `025_seed_metier_rules.sql` en une migration unique `023_create_metier_rules.sql` (création + seed dans le même fichier) pour arriver au total cible de **29 migrations**
- Les migrations 026, 027, 028, 029 conservent leur numéro (déplacements, employes, fiscal_calendar, deplacements_frais)
- Sprint 1 : `25 migrations SQL` → `29 migrations SQL`
- Récap : `Migrations SQL | 29 (au lieu de 25, +4 nouvelles)` → `29` (commentaire historique retiré)
- Récap : `Durée estimée | ~18-20 jours de build` → `~22 jours de build Claude Code`
- Chemin `docs/PRODUCT-CONTEXT.md` (tiret) → `PRODUCT_CONTEXT.md` (underscore, racine) aux 3 endroits concernés
- `StructoraiError` → `StructorAIError`
- Compteurs de lignes (842 lignes) supprimés

### README.md
- Structure du repo : suppression de tous les compteurs de lignes dans les commentaires de fichier
- Ajout de `STRATEGIE-COMPETITIVE.md` dans l'arborescence listée (manquait)
- `300 fichiers, 8 sprints` → `300 fichiers, 9 sprints (S0-S8) + V1.5`
- `Référentiels Techniques` : `coefficients régionaux (9 zones ...)` → `(11 zones ...)`
- Plan de Build header : `~18-20 jours · 8 sprints` → `~22 jours · 9 sprints (S0-S8) + V1.5 post-launch`
- Sprint 1 : `25 migrations SQL` → `29 migrations SQL`

### DISTRIBUTION.md
- Header : `16 modules, 11 corps de métier` → `13 agents IA + 1 Supervisor + 4 modules fonctionnels = 18 composants, 11 corps de métier`
- `## Les 16 modules en détail` → `## Les 18 composants en détail` + terminologie unique appliquée dans l'encart
- `= 18 processus au total` → `= 18 composants au total` (uniformisation)
- Occurrence "16 modules" (x3 hors titre) : remplacée par la phrase de référence
- Mention devis : `15 mentions légales obligatoires ... + mentions complémentaires BTP spécifiques` → `47 mentions obligatoires sur un devis BTP 2026 (15 légales + 32 complémentaires)`

### COMMUNICATION.md
- `16 modules. 13 agents. Un seul cerveau.` → `18 composants (13 agents + Supervisor + 4 modules fonctionnels). Un seul cerveau.`
- Tableau chiffres impressionnants : `16 modules` → `18 composants` avec sous-titre explicitant la composition
- Section solution landing : `Les 16 modules` → `Les 18 composants (13 agents + Supervisor + 4 modules fonctionnels)`

### ANALYSE_CONCURRENCE.md
- Ligne 459 (verdict Renalto) : `13 agents IA autonomes + 15 modules` → `13 agents IA + 1 Supervisor + 4 modules fonctionnels = 18 composants`

### STRATEGIE-COMPETITIVE.md
- `IA + vocal + 9 agents` → `IA + vocal + 13 agents`
- `coefficients régionaux (9 zones de Paris au DOM-TOM)` → `(11 zones de Paris au DOM-TOM)`

### COUT_REEL.md
- `Free tier` (x4 occurrences dans la table Coûts Variables) → `Offre gratuite`

### PRODUCT_CONTEXT.md
- `free tier pour l'acquisition` → `plan Starter gratuit pour l'acquisition`
- Coût optimisé : `$3.30/mois par artisan` → `$4.50/mois par artisan`
- Marge Pro : `~86%` → `~82%`
- Marge Business : `~92%` → `~91%`
- `15 mentions légales obligatoires devis BTP + 4 nouvelles mentions facture électronique 2026 + mention déchets obligatoire + mentions complémentaires BTP spécifiques` (4 occurrences) → `47 mentions obligatoires sur un devis BTP 2026 (15 mentions légales + 32 mentions complémentaires BTP, incluant les 4 nouvelles mentions facture électronique, mention déchets, retenue de garantie, etc.)`

### FICHE_METIER.md
- Pas de modification : la phrase "Les 15 mentions LÉGALES obligatoires d'un devis BTP 2026 (sur 47 mentions totales — voir PRODUCT_CONTEXT.md ...)" est déjà alignée avec la valeur de référence 47

### SECURITE.md, UX_PARCOURS.md
- Pas de modification : aucun résidu trouvé

### CARRELAGE/REF_TECHNIQUE_CARRELAGE.md
- Sommaire point 12 : `coefficients régionaux (9 zones)` → `(11 zones)`

### PEINTURE/REF_TECHNIQUE_PEINTURE.md
- Sommaire point 10 : `coefficients régionaux (9 zones)` → `(11 zones)`

### MENUISERIE_SERRURERIE/REF_TECHNIQUE_MENUISERIE_SERRURERIE.md
- Sommaire point 10 : `coefficients régionaux (9 zones, distincts menuiserie/serrurerie)` → `(11 zones, distincts menuiserie/serrurerie)`

### PLAQUISTE/REF_TECHNIQUE_PLAQUISTE.md
- Sommaire point 8 : `coefficients régionaux (9 zones)` → `(11 zones)`

---

## 3. Fichiers volontairement non touchés

- **`docs/CHANGELOG.md`** : historique, exclu par règle explicite du prompt
- **`STRUCTORAI-AUDIT-FIXES-V2.md`** : audit source qui a généré ce refactor — document historique qui cite en mode citation les patterns bannis (ex. `"9 zones"`, `"Free tier"`). Ce sont des citations, pas des affirmations — laissées en l'état
- **`docs/SPEC_REFACTORING.md`** : spec refactoring temporaire qui cite les patterns à corriger — même logique

---

## 4. Règles strictes respectées

- Aucun nouveau fichier sauf `docs/DECISIONS-LOG.md` (ce fichier)
- Aucun autre doc créé dans `docs/` (pas de METIER.md, API.md, etc.)
- Aucun skill créé sous `.claude/skills/`
- Aucun code Python ou TypeScript touché
- Aucun fichier supprimé (`ANALYSE_CONCURRENCE.md` et `STRATEGIE-COMPETITIVE.md` conservés)
- Aucun JSON data généré depuis les référentiels

---

## 5. Écarts documentés

### Migrations SQL : cible 29 atteinte via fusion
Le prompt demandait de renuméroter le doublon `012` et décaler les suivantes, avec total final **29 migrations**. Une renumérotation simple (+1 sur toutes les migrations après le doublon) aurait produit **30 migrations** (001-030). Pour respecter la cible 29, la migration `025_seed_metier_rules.sql` a été fusionnée avec `022_create_metier_rules.sql` dans une nouvelle migration unique `023_create_metier_rules.sql` (création + seed ensemble). Résultat : 29 migrations uniques numérotées 001-029, sans doublon.

### Faux positifs grep
La vérification finale (`grep` sur les patterns bannis) peut retourner des matches dans :
- `STRUCTORAI-AUDIT-FIXES-V2.md` (audit source, citations des problèmes)
- `docs/SPEC_REFACTORING.md` (spec du refactoring, citations des patterns)
- `CLAUDE.md` ligne 196 : "15 mentions légalement obligatoires" dans le contexte "15 légales + 32 complémentaires = 47" — valide
- `FICHE_METIER.md` ligne 13 : "15 mentions LÉGALES obligatoires... (sur 47 mentions totales)" — valide

Ces occurrences sont contextuelles et légitimes.
