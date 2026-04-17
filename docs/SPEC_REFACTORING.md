---
name: SPEC_REFACTORING
description: Plan d'exécution du refactoring documentaire Audit V5 (17/04/2026). À valider par Fabrice AVANT Phase 3. Fichier TEMPORAIRE (supprimé en fin de refactoring, archivé dans docs/REFACTORING_REPORT.md).
type: spec
date: 2026-04-17
author: Claude Opus 4.7 (1M context)
status: DRAFT — en attente validation Fabrice
---

# SPEC — Refactoring cohérence STRUCTORAI (Audit V5)

## 1. Compréhension du produit (ce que je build)

**STRUCTORAI** = SaaS mobile pour les 500K artisans BTP français (83% non-digitalisés). App Android natif (Expo/RN) + PWA iOS + WhatsApp Business, cerveau IA spécialisé BTP à 4 couches :
- **Couche 1** — LLM Claude + RAG sur 11 référentiels techniques (8 819 lignes, 1 080+ postes chiffrés, 132 erreurs terrain, 29+ devis types).
- **Couche 2** — Mémoire persistante par artisan : **hybride Mem0 (raisonnée) + MemPalace (verbatim + knowledge graph temporel)**.
- **Couche 3** — 13 agents autonomes V1 (Devis, Relance, Compta, Planning, Réputation & Marketing, Prospection, Email Pro, Fiscalité & Trésorerie, Déplacements, RH, Vision IA, Site Web) + 1 agent V2 (Téléphone IA).
- **Couche 4** — Supervisor (Ouroboros pattern) : queue prioritaire, workers, budget LLM, circuit breaker, background consciousness.

**Timing commercial** : Factur-X obligatoire réception sept 2026 / émission sept 2027 → fenêtre massive.
**Plans** : Starter (gratuit, 5 devis/mois) / Pro (29€) / Business (79€). Marges cibles : 82% Pro, 91% Business, coût LLM $4.50/artisan/mois optimisé.
**Stack** : FastAPI + Python 3.12 + Supabase (PostgreSQL + RLS) + Claude API (opus-4-7 / sonnet-4-6 / haiku-4-5-20251001) + Whisper STT + ElevenLabs TTS + Mem0 + MemPalace + Stripe + Yousign + Brevo + Twilio + WhatsApp Cloud API + **Redis (ARQ queue + rate limit + cache RAG)** + Railway + Vercel + EAS.
**Build** : 9 sprints (S0 setup → S8 launch) + Sprint V1.5 post-launch (AR Android) = **~22 jours Claude Code**. Launch prévu juin 2026.

Le cerveau IA **ne viole JAMAIS** la constitution BTP (`docs/METIER.md` à créer) : TVA BTP (5.5 / 10 / 20), mentions obligatoires devis/facture, Factur-X PDF/A-3 + XML EN 16931, mention déchets (arrêté 29/12/2020), retenue de garantie 5% (loi 16/07/1971), attestation TVA mention texte 2025. Fourchettes min-max + coefficient régional (11 zones) + défère à Mem0/MemPalace si prix artisan connu + alerte si <70% ou >150% référentiel.

## 2. Les 29 incohérences + décisions

> NB : le fichier s'appelle `STRUCTORAI-AUDIT-FIXES-V2.md` (pas `-FIXES.md`) et liste **29** items (12 H + 10 M + 7 L), pas 27. Je traite les 29.

| # | Incohérence | Décision appliquée |
|---|---|---|
| H1 | Mentions devis flou (47 / 15 / 15+4+…) | Recensement exhaustif numéroté 1 à N dans `docs/METIER.md`. Partout ailleurs : « voir docs/METIER.md » (pas de chiffre). |
| H2 | Doublon migration `012` (`012_create_contacts_pro.sql` + `012_create_avis_google.sql`) | Renumérotation : `012_contacts_pro`, `013_avis_google`, `014_emails` … toutes les suivantes décalées de +1. |
| H3 | Migrations : 25 / 29 ? | **30 migrations** après H2 (vérifié : 11 avant doublon + 2 (ex-012) + 17 après = 30). |
| H4 | Coefs régionaux : 9 ou 11 ? | **11 zones** : Paris / IDF / Grand Est / Nord / Ouest / Sud-Ouest / Sud-Est / Méditerranée / Montagne / DOM / Corse. |
| H5 | Fichiers data : 63 / ~30 / ~63 ? | Compte réel : **60 fichiers JSON uniques référencés** dans FICHE_METIER.md (grep). → chiffre unique dans `SINGLE_SOURCE_OF_TRUTH.md`. |
| H6 | Features : 110 / F111 / 56 listées ? | **110** définitif. Renumérotation F111 → F110 (F111 = « Intégration PA » qui n'a pas de F110 existant en doublon → simple renommage). Création `docs/FEATURES_COMPLETE.md` avec F001-F110 : 56 déjà détaillées dans FEATURES.md conservées, 54 restantes dérivées depuis PRODUCT_CONTEXT + BUILD_PLAN en listing synthétique (1-3 lignes chacune). |
| H7 | Sprints : 8 / 9 ? | **9 sprints (S0-S8) + Sprint V1.5 post-launch**. |
| H8 | Durée : 18-20 / 22 j ? | **~22 jours Claude Code**. |
| H9 | Modèles Claude flous | `claude-opus-4-7` (Devis complexes + Vision IA critique) / `claude-sonnet-4-6` (agents standards) / `claude-haiku-4-5-20251001` (tâches légères). |
| H10 | Nom produit 4 variantes | Marketing : **StructorAI** / Code+URLs : `structorai` / Hero : **STRUCTORAI**. `Structorai` et `Structurai` bannis. |
| H11 | `docs/METIER.md` n'existe pas | Création PRIORITÉ 1 (Phase 3.1) — la constitution BTP. |
| H12 | Archi mémoire non arrêtée | **Hybride Mem0 + MemPalace** documenté dans `docs/MEMORY.md` (mapping Wings=artisan, Rooms=clients/chantiers/prix/fournisseurs/style_devis/reseau/fiscalite, Drawers=entités, temporal graph pour paiements/prix matériaux/expirations RGE/échéances fiscales). |
| M1 | Compteurs de lignes inter-fichiers | Supprimés partout sauf en header du fichier lui-même. |
| M2 | `PRODUCT_CONTEXT.md` vs `PRODUCT-CONTEXT.md` | Un seul chemin : `PRODUCT_CONTEXT.md` racine. Purge des refs `docs/PRODUCT-CONTEXT.md`. |
| M3 | Doublon STRATEGIE-COMPETITIVE / ANALYSE_CONCURRENCE | **Fabrice a dit : PAS de suppression.** Les 2 fichiers conservés. Comme ils sont quasi-identiques (MD5 identique au 14/04), il faut les **différencier** : ANALYSE_CONCURRENCE = « qui sont-ils » (fiches concurrents) / STRATEGIE-COMPETITIVE = « comment on les bat » (plan d'action offensif). Je propose ce partage de responsabilités — voir question #2 ci-dessous. |
| M4 | Modules : 12/15/16/18 ? | « **13 agents + Supervisor + 4 modules fonctionnels** (Galerie photo, Gamification+Consciousness, Mesure IA, Dossier À faire) = 18 composants ». Appliqué partout. |
| M5 | Supervisor dans les 13 agents ? | Tableau CLAUDE.md : `Supervisor | orchestrateur (hors compte)` isolé, puis les 13. |
| M6 | « 6 initiaux → 13 » non listés | « 6 V1.0 initiaux : Devis, Relance, Compta, Planning, Prospection, Réputation + 7 ajoutés : Fiscalité&Trésorerie, Déplacements, RH, Vision IA, Site Web, Email Pro, split Réputation&Marketing ». |
| M7 | Noms fichiers agents mixtes | **Tous en `agent_X.py`** : `agent_devis.py`, `agent_relance.py`, etc. |
| M8 | Redis absent de la stack | Ajouté à README + CLAUDE.md + PRODUCT_CONTEXT + `docs/ARCH.md` (usage : ARQ queue + rate limit + cache RAG). |
| M9 | $3.30 vs $4.50 / marge 86% vs 82% | **$4.50/artisan/mois optimisé, marge 82% Pro, 91% Business**. |
| M10 | Starter / Free / Gratuit | **Starter** seul (gratuit, €0). « Free tier » banni. |
| L1 | Anglicismes / orthographe | « mise à jour » (pas MAJ), « salle de bain » prose / « SDB » code, « multilingue » (pas multi-langue). |
| L2 | Emojis icônes | Conservés (OK Markdown). |
| L3 | Prix PA (Plateforme Agréée) | Table comparative unique dans `docs/COUT_REEL.md` ou nouveau `docs/PA-EINVOICING.md` : FactPulse (intégration rapide) + B2Brouter (pilote gratuit) + Iopole. |
| L4 | OpenClaw vs claw-code | Vérifier en lecture fine de README ; si doublon, garder un seul (j'attends retour ou trancherai « OpenClaw 247K + claw-code 121K = 2 repos distincts » si le web le confirme — je note dans DECISIONS-LOG). |
| L5 | `.claude/skills/` vs `skills/` | Tout sous `.claude/skills/`. Toute ref à `skills/` racine supprimée. |
| L6 | `docs/PRODUCT-CONTEXT.md` doublon | Entrée supprimée de BUILD_PLAN. |
| L7 | Docs cités mais inexistants | Tous créés (Phase 3.3). |

**Déviation volontaire vs défauts du prompt** (logguée dans `docs/DECISIONS-LOG.md`) :
- Pour H6, je ne peux pas inventer 54 features manquantes ex nihilo ; je les dérive des features déjà décrites textuellement dans PRODUCT_CONTEXT + BUILD_PLAN (chaque feature mentionnée ailleurs reçoit un numéro). Si < 110 au final, je demanderai à Fabrice de combler le gap ou accepter un chiffre réel (ex : 104).
- Pour L4, je vérifierai mais garderai la distinction si web confirme 2 repos distincts.

## 3. Plan d'exécution chronologique

**Phase 3.1 — Fondations (PRIORITÉ, séquentiel)**
1. `docs/METIER.md` — constitution (review par subagent compliance-auditor avant commit).
2. `docs/SINGLE_SOURCE_OF_TRUTH.md` — table des chiffres-clés.
3. `docs/MEMORY.md` — archi hybride Mem0+MemPalace (review par subagent staff-engineer).
4. `docs/DECISIONS-LOG.md` — décisions prises (mémoire institutionnelle).

**Phase 3.2 — Docs en parallèle (5 subagents)**
- Subagent A : `AGENTS.md` + `PROMPTS.md` + `VOICE.md`
- Subagent B : `ARCH.md` + `API.md` + `MIGRATIONS.md` + `ENV.md` + `DEPLOY.md`
- Subagent C : `FEATURES_COMPLETE.md` (F001-F110) + `PRICING-ENGINE.md` + `GAMIFICATION.md`
- Subagent D : `KNOWLEDGE.md` + `I18N.md` + `WHATSAPP.md` + `OFFLINE.md` + `ERRORS.md`
- Subagent E : `TESTS.md` + `CONVENTIONS.md`

**Phase 3.3 — Skills + hooks + commands en parallèle (2 subagents)**
- Subagent F : 14 skills dans `.claude/skills/`
- Subagent G : 4 hooks manifests + 5 slash commands

**Phase 3.4 — Data JSON en parallèle (11 subagents métier, 1 par référentiel)**
Conversion `*/REF_TECHNIQUE_*.md` → `data/prix/<metier>.json` selon schéma donné.

**Phase 3.5 — Corrections des 29 incohérences (mass edits)**
Pour chaque incohérence : grep → str_replace → re-grep pour 0 résidu.
- Supprimer `STRATEGIE-COMPETITIVE.md`
- Renuméroter migration 012 dans BUILD_PLAN
- Remplacer tous modèles Claude, noms produit, compteurs de lignes, « 9 agents », « Free », « MAJ », etc.

**Phase 3.6 — Mise à jour pilier**
- `CLAUDE.md` : nouvelle table SOURCES DE VÉRITÉ (ordre de priorité : METIER > SSoT > MEMORY > PRODUCT_CONTEXT > AGENTS > API > BUILD_PLAN > FEATURES_COMPLETE > CONVENTIONS > autres).
- `docs/CHANGELOG.md` : entrée « 17/04/2026 — Audit V5 refactoring cohérence complet ».
- `README.md` : aligner chiffres, supprimer compteurs de lignes, ajouter Redis.

**Phase 4 — Vérification**
- Grep cohérence : 0 résidu sur les 5 patterns bannis.
- Check links internes : chaque `.md` cité existe.
- `docs/REFACTORING_REPORT.md` : rapport final.

## 4. Ce que je NE fais PAS

- Aucun fichier `.py` ni `.ts` touché (pur doc/data/skills).
- Aucune réécriture de `BUILD_PLAN.md` (lecture seule jusqu'à validation Fabrice — post-refactoring).
- Aucun commit avant avoir exécuté une phase complète (commits atomiques par fix ou par doc).

## 5. Questions pour Fabrice (AVANT Phase 3)

1. **H6 / features manquantes** : ok pour dériver les 54 features non-détaillées en listing synthétique (1-3 lignes depuis PRODUCT_CONTEXT), ou tu préfères que je m'arrête si le total réel < 110 et qu'on ajuste le chiffre dans SSoT ?
2. **M3 / STRATEGIE-COMPETITIVE.md conservé** : pour éviter le doublon à 99%, je propose de les différencier :
   - `ANALYSE_CONCURRENCE.md` → **fiches descriptives** des 24 concurrents (qui, quoi, prix, forces, faiblesses).
   - `STRATEGIE-COMPETITIVE.md` → **plan d'action offensif** (les 10 actions concrètes, messaging, FUD, comparatifs commerciaux, comment capter les clients de chaque concurrent).
   Tu valides ce partage ? Ou tu veux les garder tous les deux avec le contenu quasi-identique actuel ? Ou autre découpage ?
3. **L4 / OpenClaw vs claw-code** : je les garde distincts dans README (2 repos différents) sauf si tu sais que c'est un doublon.
4. **Ordre des commits** : j'en fais un par phase (Phase 3.1 = 1 commit, Phase 3.2 = 1 commit, etc.) ou un par fichier créé ? Je pars sur **un commit par phase cohérente** sauf avis contraire.
5. **Timing** : je compte ~3-4h de travail total (lectures ✅, création docs, skills, JSON, fix 29 incohérences, vérifs). Tu veux que je fasse tout d'un trait, ou je m'arrête à chaque phase pour un checkpoint ?

---

**Status** : SPEC terminé. J'attends ton GO pour Phase 3.
