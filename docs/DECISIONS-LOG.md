---
name: DECISIONS-LOG
description: Log des valeurs de référence tranchées et décisions appliquées lors des audits cohérence (V5 14/04/2026) puis intégration 22 améliorations (V6 17/04/2026). Rapport fichier par fichier.
type: reference
date: 2026-04-17
author: Claude Code
---

# DECISIONS-LOG — Audits STRUCTORAI

> Ce fichier logue les décisions prises pendant les audits successifs.
> Chaque valeur ci-dessous est la **source de vérité unique** pour tout le repo.
> En cas de contradiction dans un autre fichier, c'est ce log qui fait foi.

---

## 17/04/2026 — V7 : 5 Renforcements stratégiques (faiblesses → forces)

### Décisions fixées
1. **Agent Coach positionnement "éclaireur"** : conseil au conditionnel, disclaimers obligatoires, redirection expert-comptable systématique
2. **Mémoire hybride Mem0 + MemPalace** avec fallback Mem0-seul documenté
3. **AI Act classification** : "Système IA à usage limité avec supervision humaine" (pas haut risque). Revue juridique 1 500€ avant launch.
4. **Support hybride 3 niveaux** : IA 24/7 / escalade <2h / urgence SMS <1h. Widget Crisp par défaut.
5. **Slogan #1** : "100% du pipeline artisan" (vs 25-40% concurrents).

### Nouvelle feature
- F136 — Support hybride IA + humain (V1, Sprint 6)

### 4 docs planifiés (priorité 1)
- `docs/COACH-DISCLAIMER.md`
- `docs/MEMORY-STRATEGY.md`
- `docs/AI-ACT-COMPLIANCE.md`
- `docs/SUPPORT-STRATEGY.md`

### Budget additionnel
- **1 500€ one-time** revue juridique AI Act
- ~25€/mois widget support (Crisp/Intercom) à partir de 50 clients
- ~500-800€/mois support externalisé à partir de 50-200 clients

### Marges recalculées
- Pro à 50+ clients : **78%** (vs 80% précédent, -2pts pour support externalisé)
- Business à 50+ clients : **87%** (vs 89% précédent)
- Marges <50 clients inchangées : 80% Pro, 89% Business

---

## AUDIT V6 — 17/04/2026 — Intégration des 22 améliorations stratégiques

### 3 décisions prises

**1. 14ème agent — Agent Coach Business (F119)**
- Ajouté dans les 14 agents V1 (+ Supervisor + 4 modules fonctionnels = 19 composants)
- Modèle : `claude-opus-4-7`
- Budget : $0.10/appel
- Fichier : `agents/agent_coach.py` + `prompts/coach_prompt.py`
- Inclus dans TOUS les plans : Starter 1 analyse/mois, Pro/Business illimité
- Budget allocation Supervisor redistribué : Devis 20% (22→20), Compta 9% (10→9), Vision IA 9% (10→9), Fiscalité 8%, RH 8%, Site Web 7% (8→7), Relance 7%, Planning 6%, Réputation 6%, **Coach 6% (nouveau)**, Prospection 5% (6→5), Email Pro 4%, Déplacements 3%, V2 Téléphone 2%

**2. Pricing enrichi — 7 options (F130)**
Garder Starter €0 / Pro €29 / Business €79 mensuel. AJOUTER :
- **Pro annuel** : €229/an (≈ €19/mois, économie 35%)
- **Business annuel** : €629/an (≈ €52/mois, économie 35%)
- **Lifetime** : €990 une fois (limité 100 licences, promo launch)
- **Fédération** : Code promo `CAPEB20` / `FFB20` (-20% à vie, nécessite justificatif membre)
- **Trial** : 30 jours gratuits sur Pro et Business (pas 14)
- **Add-on Agent Téléphone IA (V2)** : +15€/mois sur tous plans sauf Starter

**3. Découpage V1 / V2 / V3**
- **V1 launch juin 2026** : 135 features (F01-F135) SAUF 3 reports ci-dessous
- **V2 sept-oct 2026 post-launch** : F122 (Synchro compta Pennylane/Cegid/Sage/QuickBooks, Business only) + F126 (Télé-dépannage extension Agent Téléphone) + F127 (Portail client final) + Agent Téléphone IA de base + F113 V2 (API grossistes temps réel)
- **V3 2027+** : F109 (Photogrammétrie multi-photos) + F113 V3 (API grossistes exclusives, bloqué jusqu'à 2K MRR pour négocier)

### Nouveaux chiffres de référence (audit V6)

| Sujet | Valeur V5 (14/04) | Valeur V6 (17/04) |
|---|---|---|
| Agents V1 | 13 | **14** (+Agent Coach Business F119) |
| Total composants | 18 | **19** (14 agents + Supervisor + 4 modules) |
| Features V1 | 110 + F111 | **135** (+F112 à F135 = 24 nouvelles) |
| Migrations SQL | 29 | **37** (+030 à 037) |
| Coût LLM/artisan/mois | $4.50 | **~$4.80** (+Coach +capsules) |
| Marge Pro (€29) | 82% | **80%** (-2pts, capsules + coach incluses) |
| Marge Business (€79) | 91% | **89%** (-2pts, services V2 inclus dans coûts) |
| Trial | 14 jours | **30 jours** |
| Plans tarifaires | 3 (Starter/Pro/Business) | **7** (+Pro annuel, Business annuel, Lifetime, Fédération) |
| Onboarding | signup-first | **inversé** (test avant signup) — cible conversion >25% vs >12% |

### 16 fichiers modifiés (audit V6)

1. **CLAUDE.md** : 13→14 agents partout, ligne Coach Business ajoutée, section Pricing 7 options ajoutée, arborescence data enrichie (benchmarks/capsules/diagnostics/imports)
2. **PRODUCT_CONTEXT.md** : 13→14 agents, description Coach Business (10 lignes), budget Supervisor redistribué, section Pricing restructurée (7 options), coûts $4.50→$4.80, marges 82%/91%→80%/89%, section Moat enrichie (F118 switching cost + F121 détecteur + F117 score), section Roadmap V1/V2/V3 ajoutée
3. **BUILD_PLAN.md** : ajout F112-F135 dans sprints 1-8 + V2/V3, migrations 030-037, agent_coach.py + coach_prompt.py, durée V1 22→25 jours, skills/docs à créer listés
4. **FEATURES.md** : F112-F135 ajoutées (24 nouvelles features avec tag V1/V2/V3), résumé total 110→135 features, tableau découpage V1/V2/V3
5. **README.md** : 13→14 agents (incluant Coach Business), pricing 3→7 options, Plan de Build enrichi (37 migrations, audit V6 features)
6. **COUT_REEL.md** : ligne Coach Business ~$0.20, capsules ~$0.05, total $4.50→$4.80, marges 82%/91%→80%/89%, section "Coûts V2" ajoutée (read replica, monitoring, hébergement capsules, connectors compta)
7. **DISTRIBUTION.md** : 18→19 composants, liste 14 agents (+Coach), section "Acquisition artisans étrangers en France" F133 ajoutée, tableau expansion Europe chiffré par pays (BE 2m / CH 2m / PT 3m / ES 4m / IT 5m / DE 5m / UK 5m), section partenariats grossistes 3 niveaux + fédérations
8. **COMMUNICATION.md** : 13→14 agents, 18→19 composants, 8 nouveaux arguments marketing (Coach IA, parrainage, onboarding 90s, RGE auto, 30 jours trial, 990€ Lifetime, -20% fédérations, détecteur concurrent)
9. **STRATEGIE-COMPETITIVE.md** : F112 import Sprint 8→Sprint 2-3, 4 nouvelles actions (F119 Coach, F121 détecteur, F128 RGE, F125 onboarding), 3 nouveaux angles vs concurrents
10. **ANALYSE_CONCURRENCE.md** : 13→14 agents, mention Coach Business + détecteur devis concurrent comme moat unique
11. **UX_PARCOURS.md** : nouveau parcours 1 onboarding inversé F125 (90s), parcours 1bis classique conservé, menu profil enrichi (parrainage, import, concurrent-analyzer, pack contrôle fiscal, warning switching cost)
12. **SECURITE.md** : section 8 AI Act étoffée massivement (F131 — classification GPAI, badge Décision IA, page /transparency, audit log), sections 12 (Résilience F132), 13 (Validation RGE F128), 14 (Mode contrôle fiscal F129) ajoutées
13. **FICHE_METIER.md** : 4 nouvelles catégories data ajoutées — benchmarks (1056 entrées), capsules index, diagnostics V2 (11 fichiers), imports templates (6 fichiers). Total ~63→~82 fichiers
14. **docs/CHANGELOG.md** : entrée "17/04/2026 — Audit V6" avec détail complet
15. **docs/DECISIONS-LOG.md** : ce fichier, entrée audit V6 ajoutée
16. **docs/REFACTORING-V6-REPORT.md** : rapport final (livrable de l'audit)

### Règles strictes respectées
- Aucun fichier code applicatif touché (zero .py, .ts, .tsx, .sql)
- Aucun nouveau doc dans `docs/` sauf `REFACTORING-V6-REPORT.md` (livrable)
- Aucun skill créé (reportés au plan FICHIERS_A_CREER.md)
- Aucun JSON data généré
- Aucun fichier supprimé

---

## AUDIT V5 — 17/04/2026 (valeurs de référence initiales)

### 1. Valeurs de référence (source de vérité — V5)

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
