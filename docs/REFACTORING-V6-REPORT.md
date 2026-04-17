---
name: REFACTORING-V6-REPORT
description: Rapport final de l'audit V6 — intégration des 22 améliorations stratégiques dans STRUCTORAI. Liste des 16 fichiers modifiés, 24 nouvelles features, nouveaux chiffres appliqués. Livrable de l'audit du 17/04/2026.
type: reference
date: 2026-04-17
author: Claude Code
---

# REFACTORING-V6-REPORT — Audit 17/04/2026

> Livrable final de l'intégration des 22 améliorations stratégiques dans le repo STRUCTORAI.
> Aucun code applicatif modifié. 16 fichiers .md harmonisés. 24 nouvelles features ajoutées.

---

## 1. 3 DÉCISIONS STRATÉGIQUES PRISES

### A. 14ème agent — Agent Coach Business (F119)
- Ajouté dans les 14 agents V1 (+ Supervisor + 4 modules fonctionnels = **19 composants**)
- Modèle : `claude-opus-4-7` — Budget : $0.10/appel
- Fichier : `agents/agent_coach.py` + prompt `prompts/coach_prompt.py`
- Inclus dans tous les plans — Starter 1 analyse/mois, Pro/Business illimité
- Analyses mensuelles automatiques : taux horaire vs benchmark département, conversion devis, carnet de commandes, marge moyenne, positioning
- Budget allocation Supervisor redistribué sur 14 agents

### B. Pricing enrichi — 7 options (F130)
| Plan | Prix | Trial |
|------|------|-------|
| Starter | €0 | — |
| Pro | €29/mois | 30 jours |
| **Pro annuel** | **€229/an (≈ €19/mois, -35%)** | 30 jours |
| Business | €79/mois | 30 jours |
| **Business annuel** | **€629/an (≈ €52/mois, -35%)** | 30 jours |
| **Lifetime** | **€990 une fois (100 licences, promo launch)** | — |
| **Fédération (`CAPEB20` / `FFB20`)** | **-20% à vie sur Pro/Business** | 30 jours |

**Add-on Agent Téléphone IA (V2)** : +15€/mois sur tous plans sauf Starter.

### C. Découpage V1 / V2 / V3
| Phase | Période | Contenu |
|-------|---------|---------|
| **V1** | Launch juin 2026 | 135 features (F01-F135) sauf 3 reports — 14 agents + Supervisor + 4 modules |
| **V2** | Sept-oct 2026 post-launch | F122 (Pennylane/Cegid/Sage/QuickBooks, Business only) + F126 (Télé-dépannage) + F127 (Portail client) + Agent Téléphone base + F113 V2 (API grossistes) |
| **V3** | 2027+ | F109 (Photogrammétrie) + F113 V3 (API grossistes exclusives, bloqué jusqu'à 2K MRR) |

---

## 2. 24 NOUVELLES FEATURES AJOUTÉES — F112 À F135

| # | Nom | Sprint | Tag |
|---|-----|--------|-----|
| F112 | Import universel concurrents (Obat, Tolteck, Batigest, EBP, Excel, Facture.net) | Sprint 2-3 | V1 |
| F113 | Partenariats grossistes — scan carte fidélité | Sprint 4 | V1 (scan) / V2 (API) / V3 (exclusif) |
| F114 | Code parrain structurel | Sprint 7 | V1 |
| F115 | Sandbox publique sans inscription | Sprint 8 | V1 |
| F116 | Rapport annuel de l'artisan (1er janvier + 1er juillet) | Sprint 7 | V1 |
| F117 | Score "cerveau me connaît" — warning anti-churn | Sprint 7 | V1 |
| F118 | Switching cost moat documenté (asymétrie import/export) | doc | V1 |
| F119 | **Agent Coach Business (14ème agent)** | Sprint 6-7 | V1 |
| F120 | Formation contextuelle capsules vidéo 60s (50 capsules) | Sprint 7 | V1 |
| F121 | Détection et analyse devis concurrent | Sprint 3 | V1 |
| F122 | Synchro comptable native (Pennylane/Cegid/Sage/QuickBooks) | V2 | V2 |
| F123 | Assignation tâches employés avec suivi photo | Sprint 7 | V1 |
| F124 | App mode Compagnon (employés simplifiés) | Sprint 7 | V1 |
| F125 | Onboarding inversé (90s, cible conversion >25%) | Sprint 8 | V1 |
| F126 | Télé-dépannage (extension Agent Téléphone, 550 arbres de décision) | V2 | V2 |
| F127 | Portail client final (mini-PWA sous-domaine) | V2 | V2 |
| F128 | Validation auto RGE/Qualibat (API data.gouv.fr) | Sprint 3 | V1 |
| F129 | Pack contrôle fiscal (ZIP signé horodaté) | Sprint 6 | V1 |
| F130 | Tarification enrichie — 7 options | Sprint 1 | V1 |
| F131 | Conformité AI Act (badge décision IA, page /transparency) | Sprint 6 | V1 |
| F132 | Résilience et redondance (fallback LLM, read replica, SLA 99.5%) | Sprint 8 | V1 |
| F133 | Acquisition artisans étrangers en France | Sprint 8 | V1 |
| F134 | Verticalisation / éditions métier (architecture V1, lancement V2) | Sprint 1 + V2 | V1 / V2 |
| F135 | Architecture multi-pays (country_code dès V1) | Sprint 1 | V1 |

---

## 3. NOUVEAUX CHIFFRES DE RÉFÉRENCE APPLIQUÉS

| Sujet | Avant (V5) | **Après (V6)** |
|-------|-----------|----------------|
| Agents V1 | 13 | **14** (+Agent Coach Business) |
| Total composants | 18 | **19** (14 agents + Supervisor + 4 modules) |
| Features V1 | 110 + F111 | **135** (+F112 à F135) |
| Migrations SQL | 29 | **37** (+030-037) |
| Coût LLM/artisan/mois | $4.50 | **~$4.80** (+Coach $0.20 +capsules $0.05 +RGE $0) |
| Marge Pro (€29) | 82% | **80%** (-2pts) |
| Marge Business (€79) | 91% | **89%** (-2pts) |
| Trial | 14 jours | **30 jours** |
| Plans tarifaires | 3 | **7** (+annuels, Lifetime, Fédération) |
| Onboarding | signup-first | **inversé** (test avant signup, cible >25% vs >12%) |
| Durée build V1 | ~22 jours | **~25 jours** (+3 jours audit V6) |
| Total fichiers projet | ~300 | **~320** (+20 audit V6) |

---

## 4. 16 FICHIERS MODIFIÉS — DIFF RÉSUMÉ

### 1. CLAUDE.md
- Date : 14/04 → 17/04/2026
- Description produit : 13 → **14 agents IA autonomes**
- Table agents : ajout ligne **Coach Business** (`agent_coach.py`, claude-opus-4-7, $0.10)
- Total : "13 agents + Supervisor + 4 modules = 18 composants" → "**14 + Supervisor + 4 = 19**"
- FEATURES.md : 110 → **135 features V1/V2/V3**
- **Nouvelle section "PRICING — 7 OPTIONS"** : tableau complet Starter / Pro / Pro annuel / Business / Business annuel / Lifetime / Fédération + add-on Téléphone
- Arborescence data enrichie : `benchmarks/`, `capsules/`, `diagnostics/`, `imports/`

### 2. PRODUCT_CONTEXT.md
- Date : 14/04 → 17/04/2026
- Section solution : "16 MODULES" → "**14 AGENTS + SUPERVISOR + 4 MODULES = 19 COMPOSANTS**"
- COUCHE 3 : 13 → **14 agents** + description détaillée Agent Coach Business (20+ lignes)
- Budget allocation Supervisor : redistribution sur 14 agents (Devis 20%, Compta 9%, Vision IA 9%, Fiscalité 8%, RH 8%, Site Web 7%, Relance 7%, Planning 6%, Réputation 6%, **Coach 6% nouveau**, Prospection 5%, Email Pro 4%, Déplacements 3%, V2 Téléphone 2%)
- Diagramme ASCII : ajout bloc Agent Coach Business
- **Section PRICING** : 3 plans → **7 plans** détaillés (Starter, Pro, Pro annuel, Business, Business annuel, Lifetime, Fédération) + add-on Téléphone
- Coûts : $4.50 → **$4.80/mois par artisan** (+Coach +capsules)
- Marges : Pro 82%→**80%**, Business 91%→**89%**
- **Section MOAT enrichie** : +F118 (switching cost asymétrique), +F121 (détecteur devis concurrent unique), +F117 (score anti-churn)
- **Nouvelle section ROADMAP V1/V2/V3** avec tableau explicite

### 3. BUILD_PLAN.md
- Date : 14/04 → 17/04/2026 (audit V6 intégré)
- Arborescence agents : `agent_coach.py` ajouté
- Arborescence prompts : `coach_prompt.py` ajouté
- Arborescence migrations : **030-037 ajoutées** (referrals, capsules, tasks, subscriptions_plans, diagnostics, client_tokens, ai_audit_log, country_edition)
- Sprint 1 : 29 → **37 migrations**, + F130 Pricing, + F135 architecture multi-pays, trial 14→30 jours
- Sprint 2 : + F112 import concurrents (démarrage)
- Sprint 3 : + F112 (fin), + F121 détecteur devis concurrent, + F128 validation RGE
- Sprint 4 : + F113 scan carte fidélité grossistes
- Sprint 6 : + F119 Agent Coach Business, + F129 pack contrôle fiscal, + F131 AI Act
- Sprint 7 : + F114 parrainage, + F116 rapport annuel, + F117 warning, + F120 capsules, + F123 tâches, + F124 app Compagnon
- Sprint 8 : + F115 sandbox, + F125 onboarding inversé, + F132 résilience, + F133 artisans étrangers
- **Nouveau Sprint V2** : F122, F126, F127, F113 V2, Agent Téléphone base
- **Nouveau Sprint V3** : F109 photogrammétrie, F113 V3, F134 éditions métier
- Total fichiers : ~300 → **~320**
- Durée V1 : ~22 → **~25 jours**
- Listés : 5 nouveaux skills + 7 nouveaux docs à créer (plan FICHIERS_A_CREER.md)

### 4. FEATURES.md
- **24 nouvelles features F112-F135 ajoutées** avec détails complets (Quoi/Pourquoi/Comment + Sprint + Tag V1/V2/V3)
- Résumé final : 110 → **135 features**
- Tableau découpage V1/V2/V3 ajouté
- Impact BUILD_PLAN détaillé : 8 nouvelles migrations, nouveaux fichiers data, écrans mobile, skills/docs à créer
- Tableau impact chiffres de référence

### 5. README.md
- Section agents : 13 → **14** (+ Agent Coach Business)
- ASCII COUCHE 3 : mention Coach Business
- Stack Technique : pricing 3 plans → **7 options**
- Structure du repo : BUILD_PLAN 300 fichiers → **~320**, FEATURES 110 → **135**
- Plan de Build : 9 sprints V1 + **V1.5 + V2 + V3**
- Sprint 1 : 29 → **37 migrations**, 7 plans F130, F135
- Sprints 2-8 : features audit V6 taggées par sprint
- Section Pricing : 3 plans → **7 options** (tableau complet)

### 6. COUT_REEL.md
- Date : 14/04 → 17/04/2026
- Table coûts variables : +Agent Coach Business $0.20, +capsules $0.05, +validation RGE $0
- Total par artisan : $4.50 → **$4.80/mois**
- Table marges : ajout lignes Pro annuel, Business annuel, Lifetime, Fédération
- Marges : Pro 82%→**80%**, Business 91%→**89%**
- **Nouvelle section "Coûts fixes additionnels V2"** : read replica (+25$), UptimeRobot+Better Uptime (+15$), fallback LLM, hébergement capsules (+15$), portail client, connectors compta

### 7. DISTRIBUTION.md
- Header : 18 → **19 composants** (14 agents + Supervisor + 4 modules)
- Liste agents : +Agent Coach Business (14ème)
- Section "19 composants en détail" : +ligne 17 Coach Business
- **Nouveau VECTEUR 8 "Acquisition artisans étrangers en France" (F133)** : landings `/tr`, `/ar`, `/pt`, `/es`, calendrier religieux, ciblage communautés
- **Tableau partenariats grossistes 3 niveaux (F113)** : V1 scan / V2 API / V3 exclusif
- **Section partenariats fédérations (F130)** : codes CAPEB20/FFB20
- **Tableau expansion Europe chiffré par pays** : BE 2m / CH 2m / PT 3m / ES 4m / IT 5m / DE 5m / UK 5m + total ~26 mois séquentiel

### 8. COMMUNICATION.md
- Date : 09/04 → 17/04/2026
- "13 agents" → "**14 agents**" (multiples occurrences)
- "18 composants" → "**19 composants**"
- Table chiffres impressionnants : +8 nouveaux chiffres (Coach IA, 90s onboarding, RGE validé, 1 mois offert parrainage, 30 jours trial, 990€ Lifetime, -20% fédérations, etc.)
- **Nouvelle section "ARGUMENTS MARKETING AUDIT V6 — LES 6 NOUVELLES ANGLES"** avec phrases et scripts par canal

### 9. STRATEGIE-COMPETITIVE.md
- Date : 13/04 → 17/04/2026
- Ligne 200 action 4 Import : Sprint 8 → **Sprint 2-3** (remonté en priorité)
- +4 actions concrètes (F119 Coach, F121 détecteur, F128 RGE, F125 onboarding)
- Résumé final : +3 nouveaux angles "personne n'a" (Coach Business, détecteur devis, validation RGE)
- "13 agents" → "**14 agents**" (Coach Business inclus)

### 10. ANALYSE_CONCURRENCE.md
- 18 → **19 composants** (toutes occurrences)
- "13 agents" → "**14 agents**"
- Verdict concurrence : mention Coach Business + détecteur devis concurrent comme moat unique

### 11. UX_PARCOURS.md
- Date : 14/04 → 17/04/2026
- **Parcours 1 : nouveau flow ONBOARDING INVERSÉ (F125)** — landing → chat public → premier devis → signup pour envoyer. Cible conversion >25%
- Parcours 1bis : onboarding classique conservé
- Menu profil enrichi : **Mon parrainage (F114)**, **Importer depuis un concurrent (F112)**, **Analyser un devis concurrent (F121)**, **Pack contrôle fiscal (F129)**, mode Compagnon F124, warning switching cost F118
- Mon abonnement : 7 plans + add-on Téléphone

### 12. SECURITE.md
- **Section 8 AI Act étoffée massivement (F131)** : classification GPAI à risque limité, obligations détaillées (transparence badge Décision IA, documentation technique, traçabilité migration 036, supervision humaine, non-discrimination, droit d'opposition, page publique `/transparency`, signalement incidents, deadline août 2026)
- **Nouvelle section 12 "RÉSILIENCE ET REDONDANCE" (F132)** : fallback LLM multi-provider, read replica Supabase, monitoring externe, SLA 99.5% CGV, plan de continuité
- **Nouvelle section 13 "VALIDATION CERTIFICATIONS RGE / QUALIBAT" (F128)** : service `certif_validation.py`, API data.gouv.fr, re-check mensuel, blocage devis TVA 5.5%
- **Nouvelle section 14 "MODE CONTRÔLE FISCAL" (F129)** : ZIP signé horodaté, composition, signature cryptographique, endpoint + UI

### 13. FICHE_METIER.md
- **Nouvelle section "NOUVEAUX FICHIERS DATA — AUDIT V6"** : 4 catégories ajoutées
  - `data/benchmarks/taux_horaires_par_metier_region.json` (F119, 1056 entrées)
  - `data/capsules/index.json` (F120, 50 capsules)
  - `data/diagnostics/[metier].json` × 11 (F126 V2, 550 arbres de décision)
  - `data/imports/templates/` × 6 (F112, mappings concurrents)
- Résumé total : 63 → **~82 fichiers** (+19 audit V6)

### 14. docs/CHANGELOG.md
- **Entrée "17/04/2026 — Audit V6 : intégration 22 améliorations stratégiques"** en haut
- Détail complet : 3 décisions stratégiques, 24 features, tableau nouveaux chiffres, 8 migrations, 16 fichiers modifiés, skills/docs à créer

### 15. docs/DECISIONS-LOG.md
- **Entrée "AUDIT V6 — 17/04/2026 — Intégration des 22 améliorations stratégiques"** ajoutée en haut
- 3 décisions détaillées
- Tableau nouveaux chiffres V5 → V6
- 16 fichiers modifiés détaillés avec diff résumé
- Règles strictes respectées listées

### 16. docs/REFACTORING-V6-REPORT.md
- **Ce fichier** — livrable final de l'audit.

---

## 5. FICHIERS / SKILLS / DOCS À CRÉER PLUS TARD

Listés dans le plan `FICHIERS_A_CREER.md` (à créer hors de ce refactor) :

### 5 nouveaux skills
- `SKILL-IMPORT-COMPETITORS.md` (F112)
- `SKILL-COMPETITOR-ANALYSIS.md` (F121)
- `SKILL-ACCOUNTING-INTEGRATION.md` (F122 V2)
- `SKILL-AI-ACT-COMPLIANCE.md` (F131)
- `SKILL-RETENTION-PATTERNS.md` (F117/F118)

### 7 nouveaux docs
- `PARTENARIATS.md` (stratégie grossistes F113 + fédérations F130)
- `TRUST-SIGNALS.md` (sponsors, logos, crédibilité)
- `docs/RETENTION.md` (KPIs + triggers anti-churn)
- `docs/AI-ACT-COMPLIANCE.md` (conformité EU F131)
- `docs/RESILIENCE.md` (plan de continuité F132)
- `docs/VERTICAL-EDITIONS.md` (roadmap éditions métier F134)
- `docs/COUTS-V2.md` (coûts V2 détaillés)

---

## 6. VÉRIFICATION — RÈGLES STRICTES RESPECTÉES

| Règle | Statut |
|-------|--------|
| Aucun fichier code applicatif touché (zero .py, .ts, .tsx, .sql) | ✅ 0 fichier code modifié |
| Aucun nouveau doc dans `docs/` | ✅ Sauf `REFACTORING-V6-REPORT.md` (livrable de l'audit, attendu) |
| Aucun skill créé | ✅ Tous reportés à FICHIERS_A_CREER.md |
| Pas de conversion JSON des référentiels métier | ✅ Aucun fichier JSON généré |
| Pas de suppression de fichier existant | ✅ 0 suppression |
| Mise à jour uniquement des 16 fichiers .md listés | ✅ 16 fichiers modifiés + 1 livrable |

---

## 7. VÉRIFICATIONS FINALES (grep)

| Commande | Résultat attendu | Résultat obtenu |
|----------|-----------------|-----------------|
| `grep -rn "14 agents" --include="*.md"` | >5 occurrences | **>40 occurrences** ✅ |
| `grep -rn "13 agents" --include="*.md" --exclude=CHANGELOG --exclude=DECISIONS-LOG --exclude=SPEC_REFACTORING --exclude=STRUCTORAI-AUDIT-FIXES-V2 --exclude=FEATURES` | 0 | **0 hors FEATURES.md** (FEATURES conserve mentions historiques F63/F93 pré-audit) ✅ |
| `grep -n "37 migrations"` | Plusieurs fichiers | **5 fichiers** (BUILD_PLAN, README, PRODUCT_CONTEXT, DECISIONS-LOG, CHANGELOG) ✅ |
| `grep -oE "^### F1[12][0-9]\|^### F13[0-5]" FEATURES.md` | 24 entrées F112-F135 | **24 + F110, F111 = 26 total** ✅ |
| `grep -n "Lifetime\|CAPEB20\|FFB20"` PRODUCT_CONTEXT.md, README.md | Présent | **Présent dans les 2 fichiers** ✅ |
| `grep -n "80%\|89%"` COUT_REEL.md, PRODUCT_CONTEXT.md | Présent | **Présent dans les 2 fichiers** ✅ |
| `grep -rn "Coach Business\|agent_coach"` | >10 occurrences | **44 occurrences** ✅ |

---

## 8. BILAN

### Ce qui a été fait
- ✅ 16 fichiers .md harmonisés (CLAUDE, PRODUCT_CONTEXT, BUILD_PLAN, FEATURES, README, COUT_REEL, DISTRIBUTION, COMMUNICATION, STRATEGIE-COMPETITIVE, ANALYSE_CONCURRENCE, UX_PARCOURS, SECURITE, FICHE_METIER, docs/CHANGELOG, docs/DECISIONS-LOG, docs/REFACTORING-V6-REPORT)
- ✅ 24 nouvelles features F112-F135 documentées avec tag V1/V2/V3
- ✅ 14ème agent (Coach Business) intégré partout — définitions, budget, pricing, marketing
- ✅ Pricing 7 options (Starter / Pro / Pro annuel / Business / Business annuel / Lifetime / Fédération) + trial 30 jours + add-on Agent Téléphone
- ✅ Découpage V1 / V2 / V3 explicité dans PRODUCT_CONTEXT, BUILD_PLAN, FEATURES
- ✅ 8 nouvelles migrations SQL (030-037) listées dans BUILD_PLAN
- ✅ Chiffres de référence mis à jour partout : 14 agents, 19 composants, 135 features, 37 migrations, $4.80/mois, 80%/89% marges
- ✅ Tableau expansion Europe chiffré par pays (BE/CH/PT/ES/IT/DE/UK)
- ✅ Sections SECURITE étoffées : AI Act (F131), Résilience (F132), Validation RGE (F128), Contrôle fiscal (F129)
- ✅ Onboarding inversé (F125) documenté dans UX_PARCOURS
- ✅ Plan des fichiers / skills / docs à créer (`FICHIERS_A_CREER.md` référencé)

### Ce qui reste à faire (hors scope audit V6)
- Créer `FICHIERS_A_CREER.md` avec les 5 skills + 7 docs listés
- Créer les 5 skills (SKILL-IMPORT-COMPETITORS.md, etc.)
- Créer les 7 docs (PARTENARIATS.md, TRUST-SIGNALS.md, RETENTION.md, AI-ACT-COMPLIANCE.md, RESILIENCE.md, VERTICAL-EDITIONS.md, COUTS-V2.md)
- Commit + push du refactor
- Tourner les 50 capsules vidéo (3 mois post-launch)

### Cohérence repo
Les 16 fichiers .md sont désormais cohérents entre eux sur les valeurs :
- **14 agents / 19 composants** (au lieu de 13/18)
- **135 features / 37 migrations** (au lieu de 110/29)
- **$4.80/mois / 80% Pro / 89% Business** (au lieu de $4.50 / 82% / 91%)
- **7 plans tarifaires + 30 jours trial** (au lieu de 3 + 14 jours)
- **V1 / V2 / V3 découpés explicitement** (au lieu de "V1 + mentions V2 floues")

Aucun code applicatif (Python, TypeScript, SQL) n'a été modifié. L'audit V6 est uniquement documentaire. L'implémentation se fera dans les sprints définis dans BUILD_PLAN.md.
