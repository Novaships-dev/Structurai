# STRUCTORAI — FICHIERS À CRÉER AVANT LE BUILD

> À créer par **Fabrice + Claude** (pas Claude Code).
> Claude Code ne touche à rien tant que ces fichiers ne sont pas prêts.
> Ordre de création recommandé : priorité 1 → 3.

---

## PRIORITÉ 1 — Constitution et source de vérité (OBLIGATOIRE avant le build)

### 1. `docs/METIER.md` — Constitution BTP immuable
Contenu à produire :
- TVA BTP : 3 taux (5.5% / 10% / 20%) avec conditions, attestation client (mention texte post-01/01/2025 — CERFA supprimés)
- Les **47 mentions obligatoires devis** numérotées 1 à 47 (15 légales + 32 complémentaires) avec source légale + sanction
- Mentions obligatoires facture (+ 4 nouvelles mentions facture électronique 2026)
- Mention déchets obligatoire (arrêté 29/12/2020)
- Retenue de garantie 5% (loi 16/07/1971)
- Factur-X : PDF/A-3 + XML EN 16931, profils, extension CIUS Construction
- Dates e-invoicing : sept 2026 (réception) / sept 2027 (émission TPE/PME)
- Sanctions exhaustives
- **11 zones coefficients régionaux** avec coefficients chiffrés
- URLs sources légales (legifrance, impots.gouv)

### 2. `docs/SINGLE_SOURCE_OF_TRUTH.md` — Table centrale des chiffres
Tableau avec TOUS les chiffres-clés du projet (agents, features, migrations, zones, coûts, marges, modèles Claude). Tous les autres fichiers pointent vers celui-ci, plus jamais de chiffres dispersés.

### 3. `docs/FEATURES_COMPLETE.md` — Liste F001 à F110 exhaustive
Aujourd'hui FEATURES.md ne liste que ~56 features. Créer la liste complète des 110.

### 4. `docs/MEMORY.md` — Architecture mémoire
Décision à trancher : Mem0 seul, MemPalace seul, ou hybride. Mapping BTP (wings/rooms/drawers si MemPalace). Choix documenté.

### 5. `docs/COACH-DISCLAIMER.md` — Cadre juridique Agent Coach (audit V7)
- Texte exact du disclaimer UI à afficher avant chaque analyse
- Liste exhaustive des exclusions (pas de conseil fiscal agressif, pas de restructuration, etc.)
- Template de phrases "au conditionnel" pour le system prompt
- Référence articles de loi (activités réglementées des conseillers en gestion)

### 6. `docs/MEMORY-STRATEGY.md` — Architecture mémoire hybride + fallback (audit V7)
- Architecture détaillée Mem0 + MemPalace avec mapping BTP (wings/rooms/drawers)
- Règles de routage : quelle donnée va où
- Procédure de fallback Mem0-seul si MemPalace instable >5%
- Script de migration MemPalace → Mem0 (documentation technique)
- Argument souveraineté EU

### 7. `docs/AI-ACT-COMPLIANCE.md` — Conformité AI Act Européen (audit V7)
- Analyse article par article de l'AI Act
- Classification STRUCTORAI avec justification
- Mesures de transparence implémentées
- Liste des modèles avec versions et dates
- Template attestation avocat
- Page `/transparency` contenu type
- Procédure audit logs

### 8. `docs/SUPPORT-STRATEGY.md` — Support 24/7 hybride (audit V7)
- Architecture 3 niveaux (IA / asynchrone / urgence)
- Détection mots-clés escalade
- Procédure escalade SMS Fabrice
- KPIs à tracker (résolution IA, temps escalade, NPS)
- Seuil d'embauche support externe (50, 200, 500 clients)
- Scripts de réponse humaine pour cas fréquents

---

## PRIORITÉ 2 — Docs techniques (nécessaires pour un build propre)

9. `docs/ARCH.md` — Architecture technique détaillée
10. `docs/API.md` — Endpoints REST exhaustifs + exemples Pydantic
11. `docs/AGENTS.md` — Architecture des 13 agents + Supervisor
12. `docs/VOICE.md` — Pipeline vocal (STT + TTS, latence cible)
13. `docs/WHATSAPP.md` — Meta Cloud API + templates + fallback
14. `docs/DEPLOY.md` — Railway + Vercel + EAS Build
15. `docs/ENV.md` — Variables d'environnement exhaustives
16. `docs/MIGRATIONS.md` — Guide migrations Supabase (ordre + RLS + seeds)
17. `docs/ERRORS.md` — Codes d'erreur standardisés
18. `docs/PRICING-ENGINE.md` — Plans Starter/Pro/Business + Stripe
19. `docs/I18N.md` — i18next 6 langues
20. `docs/GAMIFICATION.md` — XP, niveaux, quêtes, badges
21. `docs/KNOWLEDGE.md` — 4 sources RAG + auto-enrichissement
22. `docs/PROMPTS.md` — Guide écriture system prompts par agent
23. `docs/OFFLINE.md` — SQLite local + sync queue
24. `docs/TESTS.md` — Stratégie de test (TDD agents, TVA, Factur-X, RLS)
25. `docs/CONVENTIONS.md` — Naming Python/TS/SQL + patterns Git

---

## PRIORITÉ 3 — Skills métier `.claude/skills/`

### Skills techniques
1. `SKILL-BTP-PRICING.md` — 9 règles pricing IA (fourchettes, coefficients, alerte <70%/>150%)
2. `SKILL-FACTURX.md` — Génération PDF/A-3 + XML EN 16931
3. `SKILL-MENTIONS-LEGALES.md` — Validation des 47 mentions avant envoi
4. `SKILL-RLS-MULTITENANT.md` — Pattern organization_id + tests policy
5. `SKILL-AGENT-PATTERN.md` — Template BaseAgent complet
6. `SKILL-MEM0-INTEGRATION.md` — Conventions namespacing
7. `SKILL-SUPERVISOR-PATTERN.md` — Ouroboros (queue + workers + consciousness)
8. `SKILL-CIRCUIT-BREAKER.md` — Fallback models + empty response
9. `SKILL-PROMPT-CACHING.md` — Économies 90% sur system prompts
10. `SKILL-TEST-STRATEGY.md` — TDD pour agents IA (mocking Claude API)

### Skills Claude Code (best practices)
11. `SKILL-HOOKS-CLAUDE-CODE.md` — Hooks Stop / Pre-compact / SessionStart
12. `SKILL-COMMAND-AGENT-SKILL.md` — Pattern hiérarchique `/cmd` → agent → skill

### Si décision MemPalace dans `docs/MEMORY.md`
13. `SKILL-MEMPALACE-INTEGRATION.md` — Wings/rooms/drawers BTP + 29 MCP tools
14. `SKILL-HYBRID-MEMORY.md` — Quand Mem0 vs MemPalace, patterns de recall chaîné

---

## PRIORITÉ 4 — Hooks et commands `.claude/`

### Hooks `.claude/hooks/`
- `on-stop-auto-test.json` — Auto-test après chaque réponse
- `on-pre-compact-mempalace.json` — Sauvegarde mémoire avant compaction
- `on-session-start-context.json` — Recharge METIER.md + SINGLE_SOURCE_OF_TRUTH.md
- `README.md`

### Slash commands `.claude/commands/`
- `/devis-new` — Création devis en mode spec-driven
- `/audit-coherence` — Check cohérence sur tout le repo
- `/valider-metier` — Vérifie qu'un fichier respecte METIER.md
- `/generer-json-metier` — Convertit un référentiel .md en JSON

---

## PRIORITÉ 5 — Data JSON structurée (pour le RAG)

Conversion des 11 référentiels `.md` en JSON consommables par le vector store :
- `data/prix/plomberie.json`
- `data/prix/electricite.json`
- `data/prix/carrelage.json`
- `data/prix/peinture.json`
- `data/prix/maconnerie.json`
- `data/prix/menuiserie_serrurerie.json`
- `data/prix/plaquiste.json`
- `data/prix/facade.json`
- `data/prix/couverture.json`
- `data/prix/chauffage_climatisation.json`
- `data/prix/isolation.json`

Schéma JSON type à définir une fois, appliquer aux 11.

---

## TOTAL

- **8 docs priorité 1** (obligatoires — incl. 4 nouveaux audit V7 : COACH-DISCLAIMER, MEMORY-STRATEGY, AI-ACT-COMPLIANCE, SUPPORT-STRATEGY)
- **17 docs priorité 2** (recommandés)
- **10-14 skills priorité 3**
- **8 fichiers priorité 4** (hooks + commands)
- **11 fichiers JSON priorité 5**

**Total : ~54-58 fichiers** à créer par Fabrice + Claude avant de lancer le build Claude Code.
