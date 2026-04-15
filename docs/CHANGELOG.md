# CHANGELOG — STRUCTORAI

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
