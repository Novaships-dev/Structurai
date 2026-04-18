# CHANGELOG — STRUCTORAI

---

## 17/04/2026 — Audit V7 : 5 renforcements stratégiques intégrés

### Transformations faiblesses → forces
- Sur-scope V1 → Argument "100% du pipeline artisan"
- Risque juridique Coach → Positionnement "éclaireur" avec disclaimers
- MemPalace neuf → Argument "Souveraineté données EU"
- AI Act à respecter → Premier SaaS BTP FR conforme
- Coût support → "IA 24/7 + humain 2h", argument différenciant

### Fichiers modifiés
- COMMUNICATION.md (section 5 forces)
- DISTRIBUTION.md (slogan + arguments)
- PRODUCT_CONTEXT.md (Moat enrichi + Coach + Mémoire + Support)
- SECURITE.md (section 8 AI Act étoffée + section 15 Souveraineté)
- COUT_REEL.md (+ 1 500€ one-time AI Act + support)
- FEATURES.md (+ F136 Support hybride, F119 et F131 enrichis)
- FICHIERS_A_CREER.md (+ 4 nouveaux docs priorité 1)
- CLAUDE.md (règles d'or Coach/Mémoire/AI Act)
- docs/DECISIONS-LOG.md (entrée V7)

### Total features
135 → **136** (F136 ajoutée)

### Marges
Pro 80% (<50 clients) / **78%** (>50 clients avec support externalisé)
Business 89% (<50 clients) / **87%** (>50 clients)

---

## 17/04/2026 — Audit V6 : intégration 22 améliorations stratégiques

> Source : PLAN-22-AMELIORATIONS.md (spec). Source de vérité : `docs/DECISIONS-LOG.md` entrée 17/04/2026.

### Décisions stratégiques (3)
- **14ème agent ajouté** : Agent Coach Business (F119) — conseil stratégique mensuel (benchmark département, conversion devis, marge, positioning). Modèle claude-opus-4-7, budget $0.10/appel. Inclus dans tous les plans (Starter 1×/mois, Pro/Business illimité)
- **Pricing enrichi 7 options** : Starter €0 / Pro €29 / Pro annuel €229 / Business €79 / Business annuel €629 / Lifetime €990 (100 licences) / Fédération -20% avec CAPEB20 ou FFB20. Trial 30 jours (vs 14). Add-on Agent Téléphone +15€/mois
- **Découpage V1/V2/V3** : V1 = launch juin 2026 (135 features moins 3 reports), V2 = sept-oct 2026 (Pennylane/Cegid/Sage F122 + Télé-dépannage F126 + Portail client F127 + Agent Téléphone base), V3 = 2027+ (Photogrammétrie F109 + API grossistes exclusives F113-V3)

### 24 nouvelles features F112 à F135
| Feature | Nom | Sprint / Tag |
|---------|-----|--------------|
| F112 | Import universel concurrents (Obat, Tolteck, Batigest, EBP, Excel, Facture.net) | Sprint 2-3 / V1 |
| F113 | Partenariats grossistes — scan carte fidélité | Sprint 4 / V1 (scan), V2 (API), V3 (exclusif) |
| F114 | Code parrain structurel | Sprint 7 / V1 |
| F115 | Sandbox publique sans inscription | Sprint 8 / V1 |
| F116 | Rapport annuel de l'artisan | Sprint 7 / V1 |
| F117 | Score "cerveau me connaît" — warning anti-churn | Sprint 7 / V1 |
| F118 | Switching cost moat documenté | documentation / V1 |
| F119 | Agent Coach Business (14ème agent) | Sprint 6-7 / V1 |
| F120 | Formation contextuelle capsules vidéo 60s | Sprint 7 / V1 |
| F121 | Détection et analyse devis concurrent | Sprint 3 / V1 |
| F122 | Synchro comptable native (Pennylane/Cegid/Sage/QuickBooks) | V2 post-launch |
| F123 | Assignation tâches employés avec suivi photo | Sprint 7 / V1 |
| F124 | App mode Compagnon (employés simplifiés) | Sprint 7 / V1 |
| F125 | Onboarding inversé | Sprint 8 / V1 |
| F126 | Télé-dépannage (extension Agent Téléphone) | V2 post-launch |
| F127 | Portail client final | V2 post-launch |
| F128 | Validation auto RGE/Qualibat | Sprint 3 / V1 |
| F129 | Pack contrôle fiscal | Sprint 6 / V1 |
| F130 | Tarification enrichie — 7 options | Sprint 1 / V1 |
| F131 | Conformité AI Act | Sprint 6 / V1 |
| F132 | Résilience et redondance | Sprint 8 / V1 |
| F133 | Acquisition artisans étrangers en France | Sprint 8 / V1 |
| F134 | Verticalisation / éditions métier | Sprint 1 architecture, V2 lancement |
| F135 | Architecture multi-pays (country_code) | Sprint 1 / V1 |

### Nouveaux chiffres de référence
| Sujet | Avant | Après |
|---|---|---|
| Agents V1 | 13 | **14** (+Agent Coach Business) |
| Total composants | 18 | **19** (14 agents + 1 Supervisor + 4 modules) |
| Features | 110 (+ F111) | **135** |
| Migrations SQL | 29 | **37** (+030-037) |
| Coût LLM/artisan/mois | $4.50 | **$4.80** (+Coach +capsules) |
| Marge Pro | 82% | **80%** (-2pts, capsules incluses) |
| Marge Business | 91% | **89%** (-2pts, services V2 inclus) |
| Trial | 14 jours | **30 jours** |
| Plans tarifaires | 3 | **7** (+annuels, Lifetime, Fédération) |

### 8 nouvelles migrations SQL (030-037)
- `030_create_referrals.sql` (F114)
- `031_create_capsules.sql` (F120)
- `032_create_tasks.sql` (F123)
- `033_update_subscriptions_plans.sql` (F130)
- `034_create_diagnostics.sql` (F126 V2)
- `035_create_client_tokens.sql` (F127 V2)
- `036_create_ai_audit_log.sql` (F131)
- `037_add_country_edition.sql` (F134/F135)

### Fichiers modifiés (16)
CLAUDE.md, PRODUCT_CONTEXT.md, BUILD_PLAN.md, FEATURES.md, README.md, COUT_REEL.md, DISTRIBUTION.md, COMMUNICATION.md, STRATEGIE-COMPETITIVE.md, ANALYSE_CONCURRENCE.md, UX_PARCOURS.md, SECURITE.md, FICHE_METIER.md, docs/DECISIONS-LOG.md, docs/CHANGELOG.md, docs/REFACTORING-V6-REPORT.md (nouveau — rapport final).

### Fichiers / skills / docs à créer plus tard
Plan `FICHIERS_A_CREER.md` (à créer) :
- 5 nouveaux skills : SKILL-IMPORT-COMPETITORS.md, SKILL-COMPETITOR-ANALYSIS.md, SKILL-ACCOUNTING-INTEGRATION.md, SKILL-AI-ACT-COMPLIANCE.md, SKILL-RETENTION-PATTERNS.md
- 7 nouveaux docs : PARTENARIATS.md, TRUST-SIGNALS.md, docs/RETENTION.md, docs/AI-ACT-COMPLIANCE.md, docs/RESILIENCE.md, docs/VERTICAL-EDITIONS.md, docs/COUTS-V2.md

### Règles strictes respectées
- Aucun fichier code applicatif touché (zero .py, .ts, .tsx, .sql)
- Aucun nouveau doc dans `docs/` (sauf `REFACTORING-V6-REPORT.md` comme livrable de l'audit)
- Aucun skill créé
- Aucun JSON data généré
- Aucun fichier supprimé

---

## 17/04/2026 — Audit V5 cohérence : valeurs de référence appliquées

> Source de vérité : `docs/DECISIONS-LOG.md`

### Valeurs chiffrées harmonisées (partout)
- Mentions obligatoires devis : toutes les formulations variables ("15 mentions + 4 nouvelles + déchets + complémentaires") → formulation unique "47 mentions (15 légales + 32 complémentaires BTP)"
- Coefficients régionaux : `9 zones` → `11 zones` (CLAUDE.md, README.md, STRATEGIE-COMPETITIVE.md, CARRELAGE, PEINTURE, MENUISERIE_SERRURERIE, PLAQUISTE)
- Sprints : `8 sprints` → `9 sprints (S0-S8) + V1.5 post-launch` (README.md)
- Durée build : `~18-20 jours` → `~22 jours Claude Code` (README.md, BUILD_PLAN.md)
- Migrations SQL : `25 migrations` → `29 migrations` (BUILD_PLAN.md Sprint 1 + récap, README.md Sprint 1)
- Budget LLM / artisan : `$3.30/mois` → `$4.50/mois` (PRODUCT_CONTEXT.md)
- Marge Pro : `~86%` → `~82%` (PRODUCT_CONTEXT.md)
- Marge Business : `~92%` → `~91%` (PRODUCT_CONTEXT.md)

### Bug migrations dupliquées (H2/H3)
- BUILD_PLAN.md : deux migrations numérotées `012` → renumérotation :
  - `012_create_contacts_pro.sql` (inchangée)
  - `012_create_avis_google.sql` renommée `013_create_avis_google.sql`
  - Décalage +1 pour 013-024
  - Fusion de l'ancienne `022_create_metier_rules.sql` + ancienne `025_seed_metier_rules.sql` en `023_create_metier_rules.sql` (création + seed)
- Total final : **29 migrations uniques** numérotées 001-029

### Compteurs de lignes inter-fichiers supprimés
- CLAUDE.md table Sources de vérité : suppression "(1084 lignes)", "(769 lignes)", "(648 lignes)", "(438 lignes)", "(903 lignes)", "(8819 lignes)", "(405 lignes)"
- README.md structure du repo : suppression de tous les "(xxx lignes)" sur les fichiers et répertoires cités
- BUILD_PLAN.md : suppression "(842 lignes)" sur les citations de PRODUCT_CONTEXT

### Nommage agents Python harmonisé
- CLAUDE.md tableau agents : tous les fichiers préfixés `agent_` (`agent_devis.py`, `agent_relance.py`, `agent_compta.py`, `agent_planning.py`, `agent_reputation.py`, `agent_prospection.py`, `agent_fiscalite.py`, `agent_deplacements.py`, `agent_rh.py`)
- CLAUDE.md exemple conventions : `app/agents/devis.py` → `app/agents/agent_devis.py`
- BUILD_PLAN.md arborescence agents : déjà conforme

### Modèles Claude précisés avec suffix
- `claude-haiku-4-5` → `claude-haiku-4-5-20251001` (partout dans CLAUDE.md)
- Agents Devis + Vision IA passés à `claude-opus-4-7` (tâches les plus complexes)
- Stack technique CLAUDE.md : les trois modèles listés (opus-4-7, sonnet-4-6, haiku-4-5-20251001)

### Supervisor clarifié
- Tableau CLAUDE.md : Supervisor marqué "(hors des 13)"
- Ligne récapitulative ajoutée : "13 agents IA + 1 Supervisor + 4 modules fonctionnels (Galerie photo, Gamification, Mesure IA, Dossier À faire) = 18 composants au total"

### Comptage modules unifié
- DISTRIBUTION.md : `16 modules` + `18 processus au total` → phrase de référence unique
- `Les 16 modules en détail` (heading) → `Les 18 composants en détail`
- COMMUNICATION.md : `16 modules. 13 agents.` → `18 composants (13 agents + Supervisor + 4 modules fonctionnels)`
- ANALYSE_CONCURRENCE.md : `13 agents IA autonomes + 15 modules` → `13 agents IA + 1 Supervisor + 4 modules fonctionnels = 18 composants`

### Nom produit harmonisé
- BUILD_PLAN.md : `StructoraiError` → `StructorAIError`
- Aucune occurrence `Structorai` ou `Structurai` restante hors documents d'audit

### Plan gratuit
- COUT_REEL.md : `Free tier` (4 occurrences) → `Offre gratuite`
- PRODUCT_CONTEXT.md : `free tier pour l'acquisition` → `plan Starter gratuit pour l'acquisition`

### Chemin PRODUCT_CONTEXT unifié
- BUILD_PLAN.md : toutes les références `docs/PRODUCT-CONTEXT.md` (tiret) → `PRODUCT_CONTEXT.md` (underscore, racine)

### Nouveau fichier
- `docs/DECISIONS-LOG.md` — log des valeurs de référence + rapport fichier par fichier

---

## 15/04/2026 — Audit 360° web : intégration des 20 trouvailles réglementaires et concurrentielles

### FICHE_METIER.md (7 corrections)
- Ajout sanctions facturation électronique 2026 (50€/facture e-invoicing, 500€/transmission e-reporting)
- Ajout 3 nouvelles mentions obligatoires facture (adresse livraison, catégorie opération, TVA débits)
- Correction attestation TVA : CERFA 1300-SD/1301-SD supprimés depuis 01/01/2025 → mention texte sur devis
- Ajout obligation e-reporting (B2C, données paiement, factures de situation BTP)
- ACRE réforme 01/07/2026 détaillée (exonération 50%→25%, délai 60 jours, conditions restreintes, taux BNC 25.6%)
- Ajout mention déchets obligatoire sur devis (depuis 01/07/2021)
- Ajout retenue de garantie 5% (loi 16/07/1971)

### SECURITE.md (4 sections ajoutées)
- Section 8 : AI Act (transparence, documentation, traçabilité, supervision humaine)
- Section 9 : NIS2 (notification 24h, gestion risques cyber, responsabilité dirigeant)
- Section 10 : Permissions app mobile CNIL (caméra, micro, galerie, contacts, localisation, notifications)
- Section 11 : Durées de conservation données (RGPD + Code de commerce)

### ANALYSE_CONCURRENCE.md (3 concurrents + 1 stat)
- Ajout Mediabat AI (#17), Keyzia (#18), KeoBat (#19)
- Ajout stat marché : 46% des artisans digitalisés (Obat/FranceNum 2026)

### COUT_REEL.md
- Ajout coût Plateforme Agréée (PA) : 0.10-0.50€/facture ou 29-100€/mois

### FEATURES.md
- Ajout F111 : Intégration PA facturation électronique conforme

### PRODUCT_CONTEXT.md
- Ajout stratégie intégration PA (FactPulse/B2Brouter/Iopole)

### BUILD_PLAN.md
- Sprint 5 : ajout 5 tâches (PA, Factur-X Extended, mentions facture, mention déchets, attestation TVA)

### COMMUNICATION.md
- Ajout 3 arguments marketing (Factur-X natif, PA invisible, stat 46%)

---

## 15/04/2026 — Audit V4 : 3 derniers micro-correctifs

### 1. COMMUNICATION.md L86 — Liste des 11 corps de métier complétée
- Ajout des 3 métiers manquants dans la liste : couverture, chauffage & clim, isolation

### 2. ANALYSE_CONCURRENCE.md + STRATEGIE-COMPETITIVE.md L389
- "950+ postes" → "1080+ postes" dans le tableau comparatif concurrents

### 3. BUILD_PLAN.md — Structure data/ alignée sur CLAUDE.md
- data/prix_marche/ (par région) → data/prix/ (par métier) avec les 11 fichiers métier + coefficients_regionaux.json + taux_horaires.json
- Aligné sur la structure définie dans CLAUDE.md et FICHE_METIER.md

---

## 15/04/2026 — Audit V3 : correction des 16 incohérences restantes

### BLOC A — Propagation des 3 nouveaux référentiels (couverture, chauffage & clim, isolation)
- README.md : ajout COUVERTURE/ (703 lignes), CHAUFFAGE_CLIM/ (693 lignes), ISOLATION/ (765 lignes) dans la structure repo
- README.md : "8 Corps de Métier" → "11 Corps de Métier", 3 lignes ajoutées au tableau, total 6658→8819, 185→236, 950+→1080+, 96→132, 20+→29+
- README.md : COUCHE 1 ASCII "8 référentiels techniques (6600+)" → "11 référentiels techniques (8800+)"
- CLAUDE.md : sources de vérité "8 référentiels techniques (6658 lignes)" → "11 référentiels techniques (8819 lignes)"
- CLAUDE.md : RAG "Les 8 référentiels" → "Les 11 référentiels"
- CLAUDE.md : data/prix/ tree — ajout couverture.json, chauffage_climatisation.json, isolation.json
- DISTRIBUTION.md : toutes occurrences "8 corps"→"11 corps", "6658"→"8819", "950+"→"1080+", "96 erreurs"→"132 erreurs", "950 interventions"→"1080+ interventions", description détaillée des 11 référentiels

### BLOC B — Propagation "6658" → "8819"
- ANALYSE_CONCURRENCE.md : 2 occurrences "6658"→"8819"
- STRATEGIE-COMPETITIVE.md : 2 occurrences "6658"→"8819"

### BLOC C — COMMUNICATION.md alignement
- COMMUNICATION.md : "6 agents" → "13 agents" (4 occurrences)
- COMMUNICATION.md : "12 modules" → "16 modules", "8 corps de métier" → "11 corps", "950 interventions" → "1080+ interventions", "950 postes" → "1080+ postes", "96 erreurs" → "132 erreurs", "4709 lignes" → "8819 lignes"
- README.md : ajout COMMUNICATION.md (405 lignes) dans la structure repo
- CLAUDE.md : ajout COMMUNICATION.md dans la table sources de vérité

### BLOC D — Compteurs de lignes
- README.md : PRODUCT_CONTEXT 1067→1084, BUILD_PLAN 759→769, FICHE_METIER 887→903, COUT_REEL 288→292, ANALYSE 606→607, Total doc "~13 300+"→"~16 000+"
- CLAUDE.md : PRODUCT_CONTEXT 1013→1084, BUILD_PLAN 759→769, FICHE_METIER 887→903

### BLOC E — Yousign
- COUT_REEL.md : ajout Yousign API Starter 75€/mois (500 signatures/an, ~42/mois) dans les coûts fixes mensuels. Total fixe mis à jour.

---

## 14/04/2026 — Audit complet : correction des 33 incohérences

### 1.1 Résidu "9 agents" dans ANALYSE_CONCURRENCE.md (3 occurrences)
- Ligne 456 : "9 agents + 12 modules" → "13 agents IA autonomes + 15 modules"
- Ligne 569 : "9 agents IA autonomes" → "13 agents IA autonomes"
- Ligne 597 : "9 agents" → "13 agents"

### 1.1bis Résidu "9 agents" dans STRATEGIE-COMPETITIVE.md (3 occurrences)
- Mêmes corrections que ANALYSE_CONCURRENCE.md (fichier doublon, même hash MD5)

### 1.2 Fichier doublon identifié
- STRATEGIE-COMPETITIVE.md = copie exacte de ANALYSE_CONCURRENCE.md (hash MD5 identique)
- Conservé pour l'instant — à différencier ou supprimer sur décision Fabrice

### 1.3 Résidu "6 agents" dans DISTRIBUTION.md (2 occurrences)
- Ligne 144 : "Les 6 agents sont identiques partout" → "Les 13 agents"
- Ligne 286 : "Mem0, 6 agents, pipeline complet" → "13 agents"

### 1.4 Diagramme ASCII PRODUCT_CONTEXT.md
- "9 AGENTS AUTONOMES" → "13 AGENTS AUTONOMES"
- Diagramme restructuré : 4 colonnes × 3 rangées + ligne V2 Téléphone IA
- Ajout des 4 agents manquants : Email Pro, Fiscalité & Tréso., Vision IA, Site Web

### 1.5 Doublon Agent RÉPUTATION & MARKETING dans PRODUCT_CONTEXT.md
- Supprimé le résumé 1 ligne à la ligne 302 (doublon de la description complète ligne 243)

### 1.5bis Descriptions courtes étoffées dans PRODUCT_CONTEXT.md
- Agent Email Pro : 1 ligne → 8 lignes de description détaillée
- Agent Vision IA : 1 ligne → 8 lignes de description détaillée
- Agent Site Web : 1 ligne → 8 lignes de description détaillée

### 1.6 Budget allocation Supervisor dans PRODUCT_CONTEXT.md
- Ancienne répartition (9 agents, 100%) → nouvelle répartition (13 agents, 100%)
- Devis 22%, Compta 10%, Vision IA 10%, Site Web 8%, Fiscalité 8%, RH 8%, Relance 7%, Planning 6%, Réputation 6%, Prospection 6%, Email Pro 4%, Déplacements 3%, Téléphone V2 2%

### 1.7 Diagramme ASCII README.md — COUCHE 3
- Ajout des 4 agents manquants dans la liste COUCHE 3
- Renommage Réputation → Réputation & Marketing, Fiscalité → Fiscalité & Trésorerie

### 1.8 COUT_REEL.md — 4 agents manquants budgétés
- Ajout Agent Vision IA : ~$0.35/mois
- Ajout Agent Site Web : ~$0.50/mois
- Ajout Agent Email Pro : ~$0.15/mois
- Ajout Agent Réputation & Marketing (social) : ~$0.10/mois
- Total par artisan : $3.30 → $4.50/mois
- Marge Pro : 86% → 82% (toujours saine)
- Marge Business : 92% → 91%

### 1.9 Compteur features obsolète
- CLAUDE.md : "93 features V2 détaillées (546 lignes)" → "105 features détaillées (608 lignes)"
- README.md : "93 features V2 (546 lignes)" → "105 features détaillées (608 lignes)"

### 1.10 Compteurs de lignes mis à jour
- CLAUDE.md : PRODUCT_CONTEXT 987→1013, BUILD_PLAN 755→759, FEATURES 546→608
- README.md : PRODUCT_CONTEXT 987→1013, BUILD_PLAN 755→759, FEATURES 546→608, DISTRIBUTION 333→338
- README.md : Total doc "~11 350+" → "~13 100+"

### 1.11 UX_PARCOURS.md référencé
- Ajouté dans CLAUDE.md table "Sources de vérité" (438 lignes)
- Ajouté dans README.md structure du repo
- Ajouté ANALYSE_CONCURRENCE.md dans README.md structure du repo (606 lignes)

### 1.12 Noms de fichiers harmonisés FICHE_METIER.md ↔ CLAUDE.md
- data/prix/menuiserie.json → data/prix/menuiserie_serrurerie.json (aligné sur CLAUDE.md)
- data/prix/taux_horaire_mo.json → data/prix/taux_horaires.json (aligné sur CLAUDE.md)
- Ajout data/prix/plaquiste.json (manquait dans FICHE_METIER.md)
- Ajout data/prix/facade.json (manquait dans FICHE_METIER.md)

### 1.13 Compteur fichiers data
- Le total de ~63 fichiers reste inchangé (les 2 ajouts plaquiste/facade compensent la consolidation menuiserie)
- Note : 3 métiers dans les prix (couverture, chauffage/clim, isolation) n'ont pas de référentiel technique — documenté

### 1.14 Sections numérotées dans FICHE_METIER.md
- "Fichiers data RH" → Section 8. RH / CONVENTIONS COLLECTIVES BTP
- "Fichiers data déplacements" → Section 9. DÉPLACEMENTS
- "Fichiers data réseaux sociaux" → Section 10. RÉSEAUX SOCIAUX
- Section 8. GAMIFICATION → Section 11. GAMIFICATION & ONBOARDING
- Section 9. I18N → Section 12. I18N — TERMES MÉTIER PAR LANGUE

### 1.15 Nomenclature fichiers agents harmonisée
- BUILD_PLAN.md : email_pro.py → agent_email_pro.py
- BUILD_PLAN.md : vision.py → agent_vision.py
- BUILD_PLAN.md : site_web.py → agent_site_web.py
- BUILD_PLAN.md : telephone.py → agent_telephone.py
- CLAUDE.md : mêmes renommages dans la table des agents

### Dates mises à jour
- CLAUDE.md : 09/04/2026 → 14/04/2026
- COUT_REEL.md : 09/04/2026 → 14/04/2026
- FICHE_METIER.md : 09/04/2026 → 14/04/2026
- (PRODUCT_CONTEXT.md, BUILD_PLAN.md, DISTRIBUTION.md déjà à 14/04/2026)
