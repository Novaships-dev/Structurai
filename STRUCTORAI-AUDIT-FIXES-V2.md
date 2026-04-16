# STRUCTORAI — AUDIT FIXES (29 incohérences)

> Liste exhaustive des incohérences trouvées dans le repo au 17/04/2026.
> Chaque ligne est actionnable. Destination : prompt Claude Code.
> Une fois ce fichier traité, `docs/SINGLE_SOURCE_OF_TRUTH.md` devient LA référence.

---

## 🔴 SÉVÉRITÉ HAUTE — Blockers pour Claude Code (12)

### H1. Mentions obligatoires devis — nombre flou
- **Problème** : CLAUDE.md dit "47 mentions (15 légales + 32 complémentaires)". FICHE_METIER.md dit "15 mentions + quelques complémentaires" (7 listées seulement, pas 32). PRODUCT_CONTEXT cite "15 + 4 nouvelles facture 2026 + déchets + complémentaires" (flou). README dit "15 mandatory".
- **Impact** : Le code `pdf_service.py` doit savoir EXACTEMENT combien de mentions injecter. Risque légal.
- **Fix** : Dans `docs/METIER.md`, écrire la liste NUMÉROTÉE et EXHAUSTIVE (1 à N). Un seul chiffre total. Tous les autres fichiers remplacent leurs citations par "voir `docs/METIER.md`".

### H2. Migrations SQL dupliquées
- **Problème** : `BUILD_PLAN.md` lignes 456-457 : deux migrations numérotées `012` (`012_create_contacts_pro.sql` ET `012_create_avis_google.sql`).
- **Impact** : Supabase refusera de faire `db push`. Build cassé au Sprint 1.
- **Fix** : Renuméroter en `012_create_contacts_pro.sql` + `013_create_avis_google.sql` et décaler toutes les suivantes (`014`→`015`, etc.). Total final : 30 migrations.

### H3. Nombre total de migrations
- **Problème** : CLAUDE.md dit 25, README dit 25, BUILD_PLAN liste 29, récap BUILD_PLAN dit "29 (au lieu de 25)".
- **Fix** : Après renumérotation H2, chiffre unique = **30 migrations**. Appliquer partout.

### H4. Nombre de coefficients régionaux
- **Problème** : README/CLAUDE.md/11 fichiers référentiels disent "9 zones". BUILD_PLAN.md ligne 511 dit "11 zones (Paris → DOM-TOM)". DISTRIBUTION.md fait référence "9 zones".
- **Fix** : Trancher à **11 zones** (Paris / IDF / Grand Est / Nord / Ouest / Sud-Ouest / Sud-Est / Méditerranée / Montagne / DOM / Corse). Documenter dans `docs/METIER.md`.

### H5. Nombre de fichiers data
- **Problème** : CLAUDE.md et README disent "63 fichiers data". BUILD_PLAN récap dit "~30". FICHE_METIER.md section totale dit "~63 fichiers".
- **Fix** : Compter précisément les fichiers RÉELLEMENT listés dans FICHE_METIER.md → chiffre unique dans `docs/SINGLE_SOURCE_OF_TRUTH.md`.

### H6. Nombre de features
- **Problème** : FEATURES.md récap dit "110 features (F87-F110 = 24 nouvelles)" mais le fichier contient F111. CLAUDE.md dit "110 features". Seulement ~56 features sont listées explicitement dans FEATURES.md — les 54 autres sont où ?
- **Fix** : Trancher **110**. Renuméroter F111→F110 si doublon, ou supprimer si redondant. Créer `docs/FEATURES_COMPLETE.md` avec la liste F001 à F110 exhaustive.

### H7. Nombre de sprints
- **Problème** : README dit "8 sprints". BUILD_PLAN a Sprint 0 à Sprint 8 = **9 sprints** (+ Sprint V1.5).
- **Fix** : Dire "9 sprints (Sprint 0 setup inclus) + Sprint V1.5 post-launch". Appliquer partout.

### H8. Durée totale du build
- **Problème** : README dit "~18-20 jours". Somme des sprints BUILD_PLAN = 1+2+3+3+3+2+3+3+2 = **22 jours**.
- **Fix** : Annoncer "~22 jours de build Claude Code".

### H9. Modèles Claude nommés
- **Problème** : Partout "claude-sonnet-4-6" et "claude-haiku-4-5" (sans suffix date). Les vrais strings API actuels (avril 2026) sont `claude-opus-4-7` (plus avancé), `claude-opus-4-6`, `claude-sonnet-4-6`, `claude-haiku-4-5-20251001`.
- **Fix** : 
  - `claude-opus-4-7` pour Devis complexes + Vision IA critique (analyse plan, devis > 5K€)
  - `claude-sonnet-4-6` pour agents standards
  - `claude-haiku-4-5-20251001` pour tâches légères (relance, SMS, catégorisation simple)
  - Remplacer toutes les occurrences de `claude-haiku-4-5` (sans suffix) partout.

### H10. Nom du produit — variations
- **Problème** : Dans le repo on trouve `STRUCTORAI` (66 occurrences), `structorai` (39), `StructorAI` (30), `Structorai` (1 — c'est le nom du zip !).
- **Fix** : 
  - Marketing / prose : **StructorAI** (I majuscule)
  - Code / filenames / URLs : `structorai`
  - Hero banners : **STRUCTORAI**
  - "Structorai" → BANNI, à remplacer partout. "Structurai" → BANNI aussi.

### H11. Absence de `docs/METIER.md` (la constitution)
- **Problème** : CLAUDE.md fait référence à `docs/METIER.md` comme "source de vérité ultime" ET "équivalent du BIBLE.md d'Ouroboros". **Le fichier n'existe pas.**
- **Impact** : Claude Code n'aura pas de constitution légale à laquelle se référer pour TVA, mentions, Factur-X, etc.
- **Fix** : Créer `docs/METIER.md` EXHAUSTIF avant tout (voir Phase 3.1 du prompt de refactoring).

### H12. Architecture mémoire — choix non arrêté
- **Problème** : Mem0 est documenté partout comme "LA" solution mémoire. Mais MemPalace (23K⭐, benchmark 96.6% R@5 vs ~65-75% Mem0, 100% local, RGPD-compliant natif, 29 MCP tools natifs, knowledge graph temporel) peut offrir une solution hybride bien supérieure pour le cas d'usage BTP.
- **Impact** : Le choix d'archi mémoire conditionne TOUTE la couche 2 du cerveau IA, et le RGPD (données chez Mem0 cloud vs chez toi).
- **Fix** : Adopter archi **hybride Mem0 + MemPalace** documentée dans `docs/MEMORY.md` (défaut recommandé). Mapping BTP :
  - **MemPalace** : conversations WhatsApp verbatim, devis/factures archivés, knowledge graph temporel (paiements clients, évolutions prix matériaux, expirations RGE)
  - **Mem0** : mémoire raisonnée structurée (résumés, patterns, apprentissages), profil artisan
  - Les 2 co-existent sur la couche 2. Documenter quand utiliser lequel.

---

## 🟠 SÉVÉRITÉ MOYENNE — Confusions de Claude Code (10)

### M1. Lignes des fichiers mentionnées partout
- **Problème** : README/CLAUDE.md/BUILD_PLAN/FICHE_METIER citent des compteurs de lignes qui deviennent obsolètes à chaque modification. Ex : PRODUCT_CONTEXT = 1084 lignes (README) vs 1094 (réel) vs 842 (BUILD_PLAN ligne 27).
- **Fix** : Supprimer TOUS les compteurs de lignes dans les citations inter-fichiers. Les remplacer par "voir `FILE.md`" sans chiffre. Le compteur reste uniquement en haut du fichier lui-même (optionnel).

### M2. Nom fichier `PRODUCT_CONTEXT.md` vs `PRODUCT-CONTEXT.md`
- **Problème** : Le vrai fichier = `PRODUCT_CONTEXT.md` (underscore). BUILD_PLAN et CLAUDE.md citent parfois `docs/PRODUCT-CONTEXT.md` (tiret).
- **Fix** : Un seul chemin : `PRODUCT_CONTEXT.md` à la racine. Pas de doublon dans `docs/`.

### M3. Fichiers doublons `ANALYSE_CONCURRENCE.md` et `STRATEGIE-COMPETITIVE.md`
- **Problème** : 99% identiques (diff minime : 2 lignes + 1 tableau). Gaspillage. Le CHANGELOG 14/04 disait "à différencier ou supprimer sur décision Fabrice".
- **Fix** : **Supprimer `STRATEGIE-COMPETITIVE.md`**. Conserver uniquement ANALYSE_CONCURRENCE.md.

### M4. Modules : 12, 15, 16 ou 18 ?
- **Problème** : 
  - COMMUNICATION.md : "16 modules"
  - DISTRIBUTION.md : "16 modules" + "18 processus au total"
  - STRATEGIE/ANALYSE_CONCURRENCE : "15 modules"
  - PRODUCT_CONTEXT : "16 MODULES, 1 CERVEAU"
- **Fix** : Définition unique : "**13 agents IA** + **Supervisor** + **4 modules fonctionnels non-agents** (Galerie photo, Gamification + Background Consciousness, Mesure IA, Dossier À faire) = **18 composants au total**". Écrire dans PRODUCT_CONTEXT, citer partout.

### M5. Agents : ambiguïté Supervisor dans le tableau CLAUDE.md
- **Problème** : Le tableau agents dans CLAUDE.md liste Supervisor dans les 13 agents. Mais Supervisor n'est PAS un des 13 (c'est l'orchestrateur au-dessus).
- **Fix** : Dans le tableau, séparer clairement `Supervisor | orchestrateur (hors des 13)` puis les 13 agents. Total : 1 Supervisor + 13 agents + Agent Téléphone V2.

### M6. Agents "6 initiaux" qui deviennent 13 — manque Planning/Prospection
- **Problème** : FEATURES.md récap : "13 (6 → 13 : +Fiscalité, +Déplacements, +RH, +Vision IA, +Site Web, +Email Pro, +Réputation & Marketing)" = 6+7 = 13, mais les "6 initiaux" ne sont pas listés.
- **Fix** : Remplacer par "13 agents : 6 V1.0 initiaux (Devis, Relance, Compta, Planning, Prospection, Réputation) + 7 V1.5 ajoutés (Fiscalité, Déplacements, RH, Vision IA, Site Web, Email Pro, Réputation&Marketing split)".

### M7. Fichier agents — conventions de nommage
- **Problème** : Dans CLAUDE.md tableau, certains agents ont prefix `agent_` (agent_email_pro.py, agent_vision.py, agent_site_web.py, agent_telephone.py), d'autres non (devis.py, relance.py, compta.py, etc.).
- **Fix** : Un seul pattern : **tous en `agent_X.py`** : `agent_devis.py`, `agent_relance.py`, `agent_compta.py`, etc. Appliquer dans CLAUDE.md + BUILD_PLAN.md.

### M8. Redis pas documenté dans la stack
- **Problème** : `utils/redis.py` + `REDIS_URL` dans BUILD_PLAN, mais README + PRODUCT_CONTEXT + CLAUDE.md ne mentionnent jamais Redis dans la section stack.
- **Fix** : Ajouter Redis à la stack officielle avec usage précis : **queue tasks agents async (ARQ) + rate limiting + cache RAG**. Expliquer dans `docs/ARCH.md`.

### M9. Budget LLM : $3.30 ou $4.50 ?
- **Problème** : PRODUCT_CONTEXT donne "$3.30/mois optimisé" puis "Marge 86%". CHANGELOG 14/04 audit 1.8 a corrigé en "$4.50/mois" avec "marge 82%". PRODUCT_CONTEXT pas mis à jour.
- **Fix** : Propager **$4.50/mois** et **marge 82% (Pro) / 91% (Business)** partout.

### M10. Plan "Starter/Free/Gratuit" — naming
- **Problème** : README dit "Starter (Gratuit)", PRODUCT_CONTEXT dit "Starter €0" puis "Free", certains docs disent "Free tier".
- **Fix** : **Un seul nom : "Starter"** (gratuit, €0). Éviter "Free" qui signale moins de valeur.

---

## 🟡 SÉVÉRITÉ FAIBLE — Polish (7)

### L1. Anglicismes et orthographe
- "MAJ" à côté de "mise à jour" — choisir "mise à jour"
- "Sdb" vs "SDB" vs "salle de bain" — uniformiser ("salle de bain" en prose, "SDB" en code/UI)
- "multi-langue" vs "multilingue" — uniformiser ("multilingue")

### L2. Emojis d'icônes dans les docs
- Les emojis (🏗️ 🔴 🟡 🟢) OK en Markdown, peuvent rendre moins bien en PDF/Word mais acceptables. Les garder.

### L3. Prix PA (Plateforme Agréée)
- COUT_REEL.md : "0.10-0.50€/facture ou 29-100€/mois"
- PRODUCT_CONTEXT : "FactPulse 29€/mois, B2Brouter gratuit pilote, Iopole (pas de prix)"
- **Fix** : Table comparative unique dans `docs/COUT_REEL.md` avec les 3 options + recommandation (défaut : FactPulse pour l'intégration rapide + B2Brouter pour la phase pilote).

### L4. Mention "claw-code" vs "OpenClaw"
- README ligne 174-176 et 181-182 cite "OpenClaw (247K★)" et "claw-code (121K)" comme deux repos différents.
- **Fix** : Vérifier et harmoniser. Probablement OpenClaw est le vrai nom et "claw-code" est une confusion. Si doublon, supprimer l'un.

### L5. `.claude/skills/` vs `skills/`
- BUILD_PLAN crée `.claude/skills/` (pour Claude Code) ET `skills/` racine (SKILL-FASTAPI, SKILL-EXPO, etc.). Double répertoire.
- **Fix** : Tout concentrer sous `.claude/skills/` (convention officielle Claude Code). Supprimer toute référence à `skills/` racine.

### L6. `docs/` doublons
- BUILD_PLAN.md liste `docs/PRODUCT-CONTEXT.md` (doublon du fichier racine)
- **Fix** : Supprimer l'entrée dans BUILD_PLAN. Le seul `PRODUCT_CONTEXT.md` est à la racine.

### L7. Liste docs dans BUILD_PLAN vs `.claude/` dans CLAUDE.md
- **Problème** : BUILD_PLAN liste `docs/` (20 fichiers) mais beaucoup n'existent pas. CLAUDE.md cite des docs qui n'existent pas (`docs/METIER.md`, `docs/AGENTS.md`, etc.).
- **Fix** : Créer TOUS les docs manquants (voir Phase 3.3 du prompt).

---

## ✅ RÉCAP — 20+ fichiers à créer

### Docs obligatoires (19)
1. **`docs/METIER.md`** — Constitution BTP (PRIORITÉ 1)
2. **`docs/SINGLE_SOURCE_OF_TRUTH.md`** — Table des chiffres-clés (PRIORITÉ 1)
3. **`docs/MEMORY.md`** — Architecture hybride Mem0 + MemPalace
4. **`docs/FEATURES_COMPLETE.md`** — Liste F001 à F110 exhaustive
5. `docs/ARCH.md`
6. `docs/API.md`
7. `docs/AGENTS.md`
8. `docs/VOICE.md`
9. `docs/WHATSAPP.md`
10. `docs/DEPLOY.md`
11. `docs/ENV.md`
12. `docs/MIGRATIONS.md`
13. `docs/ERRORS.md`
14. `docs/PRICING-ENGINE.md`
15. `docs/I18N.md`
16. `docs/GAMIFICATION.md`
17. `docs/KNOWLEDGE.md`
18. `docs/PROMPTS.md`
19. `docs/OFFLINE.md`
20. `docs/TESTS.md`
21. `docs/CONVENTIONS.md`
22. `docs/DECISIONS-LOG.md` (nouveau, pour logger les choix du refactoring)
23. `docs/SPEC_REFACTORING.md` (temporaire, supprimé après refactoring)
24. `docs/REFACTORING_REPORT.md` (rapport final)

### Skills métier `.claude/skills/` (14)
1. `SKILL-BTP-PRICING.md`
2. `SKILL-FACTURX.md`
3. `SKILL-MENTIONS-LEGALES.md`
4. `SKILL-RLS-MULTITENANT.md`
5. `SKILL-AGENT-PATTERN.md`
6. `SKILL-MEM0-INTEGRATION.md`
7. **`SKILL-MEMPALACE-INTEGRATION.md`** (NOUVEAU)
8. **`SKILL-HYBRID-MEMORY.md`** (NOUVEAU)
9. `SKILL-SUPERVISOR-PATTERN.md`
10. `SKILL-CIRCUIT-BREAKER.md`
11. `SKILL-PROMPT-CACHING.md`
12. `SKILL-TEST-STRATEGY.md`
13. **`SKILL-HOOKS-CLAUDE-CODE.md`** (NOUVEAU — inspiré claude-code-best-practice)
14. **`SKILL-COMMAND-AGENT-SKILL.md`** (NOUVEAU — pattern hiérarchique)

### Hooks `.claude/hooks/` (4 manifests)
1. `on-stop-auto-test.json`
2. `on-pre-compact-mempalace.json`
3. `on-session-start-context.json`
4. `README.md`

### Slash commands `.claude/commands/` (5 manifests)
1. `devis-new.md`
2. `audit-coherence.md`
3. `valider-metier.md`
4. `generer-json-metier.md`
5. `README.md`

### Data JSON (11 fichiers)
Un par référentiel métier : `data/prix/[metier].json` — conversion des `.md` en JSON structuré pour le RAG.

### Fichier à supprimer (1)
- `STRATEGIE-COMPETITIVE.md` (doublon ANALYSE_CONCURRENCE.md)
