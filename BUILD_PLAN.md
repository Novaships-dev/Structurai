# STRUCTORAI — PLAN DE BUILD COMPLET
> Ce fichier est le blueprint exact pour Claude Code.
> Chaque fichier listé ici doit être créé, avec son rôle précis.
> Fabrice ne code pas. Claude Code code tout. Fabrice crée les comptes infra et configure les clés.
> Date : 14/04/2026

---

## REPOS & SKILLS À DONNER À CLAUDE CODE

### Fichiers à placer DANS le repo STRUCTORAI avant de commencer

```
structorai/
├── CLAUDE.md                          ← Instructions Claude Code (adapté d'AgentShield)
├── .claude/
│   └── skills/
│       └── saas-debug-pipeline/       ← COPIE EXACTE du debug pipeline template
│           ├── SKILL.md
│           ├── scripts/
│           │   ├── generate_db_audit.py
│           │   └── parse_railway_logs.py
│           └── references/
│               ├── db-audit-template.sql
│               └── setup-guide.md
├── docs/                              ← Docs de référence (source de vérité)
│   └── PRODUCT-CONTEXT.md             ← Le PRODUCT-CONTEXT complet (842 lignes)
└── skills/                            ← Skills Claude Code spécifiques STRUCTORAI
```

### MCP Servers à connecter dans Claude Code

```bash
# 1. Supabase (DB + Auth + RLS)
claude mcp add supabase --url "https://mcp.supabase.com/mcp?project_ref=<PROJECT_REF>&read_only=false"

# 2. Vercel (PWA iOS hosting + Logs)
claude mcp add vercel --url "https://mcp.vercel.com"

# 3. Railway (Backend + Agents workers + Logs)
claude mcp add railway-mcp-server -- npx -y @railway/mcp-server

# 4. Anthropic API (pour tester les agents IA)
# → Pas de MCP, juste la clé ANTHROPIC_API_KEY dans les env vars
```

### Plugins & Skills externes à installer dans Claude Code AVANT le build

```bash
# 1. Superpowers (120K ⭐) — méthodologie de dev agentique complète
# Brainstorming → spec → plan → subagent-driven-development → TDD
# Installe le workflow complet : l'agent planifie, découpe en tâches, lance des subagents, review
/plugin install superpowers@claude-plugins-official

# 2. UI/UX Pro Max Skill (57K ⭐) — design intelligence multi-plateforme
# Patterns de design system, composants, spacing, typography, couleurs
# CRITIQUE pour que l'app soit belle et intuitive pour des artisans (pas des devs)
# Copier dans .claude/skills/ :
git clone https://github.com/nextlevelbuilder/ui-ux-pro-max-skill.git /tmp/ui-ux
cp -r /tmp/ui-ux/.claude/skills/* .claude/skills/

# 3. Everything Claude Code (128K ⭐) — skills, hooks, agents, commands optimisés
# Récupérer les skills pertinents pour notre stack :
git clone https://github.com/affaan-m/everything-claude-code.git /tmp/ecc
# Copier les skills utiles :
cp -r /tmp/ecc/skills/fastapi-skill .claude/skills/        # si existe
cp -r /tmp/ecc/skills/react-native-skill .claude/skills/    # si existe
cp -r /tmp/ecc/skills/supabase-skill .claude/skills/        # si existe
# Copier les hooks utiles :
cp -r /tmp/ecc/hooks/ .claude/hooks/
# Copier les MCP configs utiles :
cp /tmp/ecc/mcp-configs/supabase.json .claude/mcp-configs/
cp /tmp/ecc/mcp-configs/railway.json .claude/mcp-configs/
# Adapter le CLAUDE.md avec les best practices du repo

# 4. AndroidUtilCode (33.7K ⭐) — référence patterns utils mobile
# PAS intégré directement (Java/Kotlin, on est en React Native)
# Mais lister comme RÉFÉRENCE dans docs/CONVENTIONS.md pour les patterns :
# - Network utils, permissions, file handling, image compression, device info
```

### Repos open-source de RÉFÉRENCE pour Claude Code

| Repo | Stars | Ce qu'on prend | Comment |
|------|-------|---------------|---------|
| **everything-claude-code** | 128K | Skills, hooks, commands, MCP configs | Copier dans .claude/ les skills pertinents |
| **superpowers** | 120K | Plugin Claude Code officiel | `/plugin install` — workflow brainstorming→spec→plan→subagent→TDD |
| **ui-ux-pro-max-skill** | 57K | Skill UI/UX design intelligence | Copier dans .claude/skills/ — l'app doit être belle pour des artisans |
| **AndroidUtilCode** | 33.7K | Patterns utils mobile (référence) | Pas copié — référence pour les utils React Native |
| **Ouroboros** | 456 | Supervisor, consciousness, budget, BIBLE.md | Pattern architecturel — adapté en agents/supervisor.py |
| **OpenClaw** | 247K | Skills/plugins, canaux WhatsApp | Pattern modulaire — adapté en tool_registry.py |
| **Mem0** | 91K+ | Mémoire persistante multi-level | Intégré directement via npm/pip — memory/mem0_client.py |
| **claw-code** | 121K | Tools manifest, query engine | Pattern architecturel — adapté en tool_registry.py |

---

## ARBORESCENCE COMPLÈTE — TOUS LES FICHIERS

```
structorai/
│
├── CLAUDE.md                              # Instructions Claude Code — la loi du repo
├── README.md                              # README public GitHub
├── LICENSE                                # MIT
├── .gitignore
├── .env.example                           # Template variables d'environnement
│
├── .claude/
│   └── skills/
│       └── saas-debug-pipeline/           # Debug pipeline (copié du template)
│           ├── SKILL.md
│           ├── scripts/
│           │   ├── generate_db_audit.py
│           │   └── parse_railway_logs.py
│           └── references/
│               ├── db-audit-template.sql
│               └── setup-guide.md
│
├── docs/                                  # 20+ docs de référence
│   ├── PRODUCT-CONTEXT.md                 # Source de vérité produit (842 lignes)
│   ├── ARCH.md                            # Architecture technique détaillée
│   ├── API.md                             # Tous les endpoints REST
│   ├── CONVENTIONS.md                     # Naming, patterns, règles de code
│   ├── AGENTS.md                          # Architecture des 13 agents IA + Supervisor
│   ├── METIER.md                          # Constitution BTP — règles immuables (TVA, mentions, Factur-X)
│   ├── MEMORY.md                          # Architecture mémoire Mem0 — niveaux, recall, enrichissement
│   ├── VOICE.md                           # Pipeline vocal — STT, TTS, latence, langues
│   ├── WHATSAPP.md                        # Intégration WhatsApp Business API — webhooks, templates
│   ├── DEPLOY.md                          # Guide déploiement Railway + Vercel + Google Play
│   ├── ENV.md                             # Toutes les variables d'environnement
│   ├── MIGRATIONS.md                      # Guide migrations Supabase
│   ├── SECURITY.md                        # Auth, RLS, API keys, JWT
│   ├── ERRORS.md                          # Codes d'erreur standardisés
│   ├── PRICING-ENGINE.md                  # Plans Free/Pro/Business, limites, billing
│   ├── I18N.md                            # Multi-langue — architecture, 6 langues, traductions
│   ├── GAMIFICATION.md                    # Niveaux, XP, quêtes, badges, progression cerveau
│   ├── KNOWLEDGE.md                       # Système de connaissance — 4 sources, auto-enrichissement
│   ├── PROMPTS.md                         # Guide d'écriture des system prompts par agent, itération, patterns
│   ├── OFFLINE.md                         # Stratégie mode hors-ligne — stockage local, sync, conflits
│   ├── TESTS.md                           # Stratégie de test
│   └── CHANGELOG.md                       # Log des changements
│
├── skills/                                # 25+ skills Claude Code
│   ├── SKILL-FASTAPI.md                   # Patterns endpoints (adapté d'AgentShield)
│   ├── SKILL-EXPO.md                      # React Native Expo — composants, navigation, build
│   ├── SKILL-SUPABASE.md                  # Clients, queries, RLS, migrations (adapté d'AgentShield)
│   ├── SKILL-AUTH.md                      # Supabase Auth SSR + JWT + API keys
│   ├── SKILL-STRIPE.md                    # Checkout, webhooks, plans (adapté d'AgentShield)
│   ├── SKILL-MULTI-TENANT.md             # RLS par organization_id (adapté d'AgentShield)
│   ├── SKILL-AGENTS.md                    # Comment coder un agent IA (pattern Ouroboros)
│   ├── SKILL-SUPERVISOR.md                # Supervisor — queue, workers, consciousness, budget
│   ├── SKILL-MEM0.md                      # Intégration Mem0 — store, recall, enrichissement
│   ├── SKILL-VOICE.md                     # Pipeline vocal — Whisper/Deepgram + ElevenLabs/OpenAI TTS
│   ├── SKILL-WHATSAPP.md                  # WhatsApp Business API — webhook, envoi, templates
│   ├── SKILL-OCR.md                       # Claude Vision pour scan tickets + fallback Tesseract
│   ├── SKILL-PHOTOS.md                    # Galerie photo chantier — upload, analyse Claude Vision, avant/après, export PDF/social
│   ├── SKILL-FISCALITE.md                 # Agent Fiscalité — statuts juridiques, calendrier fiscal, URSSAF, TVA, suivi annuel
│   ├── SKILL-DEPLACEMENTS.md              # Agent Déplacements — GPS, barème km, zones BTP, indemnités, paniers repas
│   ├── SKILL-RH.md                        # Agent RH — conventions BTP (6 IDCC), grilles salaires, CIBTP, heures sup, export paie
│   ├── SKILL-PDF.md                       # Génération PDF devis/factures — ReportLab + Factur-X
│   ├── SKILL-DEVIS.md                     # Agent Devis — vocal→postes→prix→TVA→PDF→envoi
│   ├── SKILL-RELANCE.md                   # Agent Relance — séquences, ton adaptatif, escalade
│   ├── SKILL-COMPTA.md                    # Agent Compta — OCR tickets, catégorisation, export
│   ├── SKILL-PLANNING.md                  # Agent Planning — timer, capacité, enchaînements
│   ├── SKILL-REPUTATION.md                # Agent Réputation & Marketing — avis Google, réponse IA, SEO, réseaux sociaux
│   ├── SKILL-PROSPECTION.md               # Agent Prospection — CRM pro, relances architectes
│   ├── SKILL-EMAIL.md                     # Module email pro — IMAP, filtrage, catégorisation
│   ├── SKILL-I18N.md                      # Multi-langue — i18next, 6 langues, traductions
│   ├── SKILL-GAMIFICATION.md              # Niveaux, XP, quêtes, badges
│   ├── SKILL-CIRCUIT-BREAKER.md           # Pattern circuit breaker — fallback models, empty response handling
│   ├── SKILL-CONTEXT-COMPACTION.md        # Résumé contexte long via LLM léger (pattern Ouroboros)
│   ├── SKILL-TOOL-REGISTRY.md             # Auto-discovery des tools, manifest pattern (pattern claw-code)
│   ├── SKILL-PROMPTS.md                   # Comment écrire et itérer les system prompts des agents
│   ├── SKILL-HEALTH-INVARIANTS.md         # Checks santé injectés dans le contexte LLM
│   ├── SKILL-OFFLINE-SYNC.md              # Mode hors-ligne — SQLite local + queue sync
│   ├── SKILL-DEPLOY.md                    # Railway + Vercel + EAS Build + Google Play
│   ├── SKILL-TESTING.md                   # Patterns de test (adapté d'AgentShield)
│   └── SKILL-WEBSOCKET.md                 # WebSocket pour chat temps réel
│
├── backend/                               # FastAPI — Python 3.12+
│   ├── Dockerfile                         # Image Docker backend API
│   ├── Dockerfile.agents                  # Image Docker agents workers
│   ├── requirements.txt
│   ├── pyproject.toml
│   └── app/
│       ├── __init__.py
│       ├── main.py                        # App FastAPI — middlewares, routes, error handlers
│       ├── config.py                      # Settings pydantic — toutes les env vars
│       │
│       ├── api/                           # Endpoints REST
│       │   ├── __init__.py
│       │   └── v1/
│       │       ├── __init__.py
│       │       ├── router.py              # Registre tous les routers
│       │       ├── chat.py                # POST /v1/chat — message texte au cerveau
│       │       ├── chat_audio.py          # POST /v1/chat/audio — upload vocal → Whisper → cerveau → TTS
│       │       ├── devis.py               # CRUD devis — list, get, create, update, send, sign
│       │       ├── factures.py            # CRUD factures — create from devis, send, status
│       │       ├── chantiers.py           # CRUD chantiers — pipeline, timer start/stop, stats
│       │       ├── clients.py             # CRUD clients — fiche, historique, tags
│       │       ├── tickets.py             # POST /v1/tickets/scan — photo → OCR → catégorisation
│       │       ├── contacts_pro.py        # CRUD architectes, apporteurs d'affaires
│       │       ├── photos.py              # CRUD photos chantier — upload, list, analyze, avant-après, export
│       │       ├── emails.py              # GET /v1/emails/summary — résumé emails pro
│       │       ├── avis.py                # CRUD avis Google — list, respond
│       │       ├── stats.py               # GET /v1/stats/dashboard — métriques globales
│       │       ├── billing.py             # Stripe checkout, portal, webhooks
│       │       ├── onboarding.py          # GET/POST quêtes, XP, niveau, badges
│       │       ├── settings_user.py       # CRUD settings user — langue, voix, préférences
│       │       ├── whatsapp_webhook.py    # POST /v1/whatsapp/webhook — réception messages WhatsApp
│       │       └── health.py              # GET /health — status services
│       │
│       ├── middleware/                     # Middlewares FastAPI
│       │   ├── __init__.py
│       │   ├── auth.py                    # JWT + API key verification (pattern AgentShield)
│       │   ├── rate_limit.py              # Rate limiting par plan
│       │   └── plan_limits.py             # Vérification plan Free/Pro/Business
│       │
│       ├── services/                      # Logique métier (jamais dans les endpoints)
│       │   ├── __init__.py
│       │   ├── chat_service.py            # Orchestration chat → Supervisor → Agent → réponse
│       │   ├── voice_service.py           # Pipeline Whisper/Deepgram STT + ElevenLabs TTS
│       │   ├── devis_service.py           # Génération devis — vocal→postes→prix→TVA→PDF
│       │   ├── facture_service.py         # Génération factures + Factur-X
│       │   ├── ocr_service.py             # Claude Vision OCR tickets de caisse
│       │   ├── photo_service.py           # Galerie photo chantier — upload, analyse IA (Claude Vision), avant/après, export, compression
│       │   ├── pdf_service.py             # Génération PDF (ReportLab) — devis, factures
│       │   ├── email_service.py           # IMAP polling, filtrage, catégorisation
│       │   ├── whatsapp_service.py        # Meta Cloud API — envoi/réception messages
│       │   ├── stripe_service.py          # Billing — checkout, portal, webhooks (pattern AgentShield)
│       │   ├── brevo_service.py           # Email transactionnel (relances, notifications)
│       │   ├── sms_service.py             # Twilio SMS (relances, avis Google)
│       │   ├── google_reviews_service.py  # Google Business API — avis, réponses
│       │   ├── signature_service.py       # Yousign API — signature électronique devis
│       │   ├── gamification_service.py    # XP, niveaux, quêtes, badges
│       │   ├── knowledge_service.py       # RAG vectoriel — recherche base métier BTP
│       │   ├── web_search_service.py      # Recherche web fallback + auto-enrichissement
│       │   └── i18n_service.py            # Détection langue, traduction, localization
│       │
│       ├── agents/                        # Les 13 agents IA + Supervisor (pattern Ouroboros)
│       │   ├── __init__.py
│       │   ├── supervisor.py              # Orchestrateur — queue, workers, budget, consciousness
│       │   ├── consciousness.py           # Background consciousness — pense entre les tâches
│       │   ├── state.py                   # État global artisan — budget LLM, métriques
│       │   ├── queue.py                   # File de tâches prioritaires
│       │   ├── workers.py                 # Cycle de vie des agents
│       │   ├── budget.py                  # Tracking coût LLM par agent, circuit breaker
│       │   ├── circuit_breaker.py         # 3 réponses vides → pause + fallback model chain (pattern Ouroboros)
│       │   ├── context_compaction.py      # Résumé contexte ancien via LLM léger quand trop long (pattern Ouroboros)
│       │   ├── health_invariants.py       # Health checks injectés dans le contexte LLM (budget drift, stale data)
│       │   ├── tool_registry.py           # Auto-discovery des tools par agent (pattern Ouroboros tools/)
│       │   ├── metier.py                  # METIER.md loader — constitution BTP
│       │   ├── base_agent.py              # Classe de base agent (system prompt, tools, memory)
│       │   ├── agent_devis.py             # Agent Devis
│       │   ├── agent_relance.py           # Agent Relance
│       │   ├── agent_compta.py            # Agent Compta
│       │   ├── agent_planning.py          # Agent Planning
│       │   ├── agent_reputation.py        # Agent Réputation & Marketing
│       │   ├── agent_prospection.py       # Agent Prospection
│       │   ├── agent_fiscalite.py         # Agent Fiscalité — suivi fiscal annuel, rappels URSSAF/TVA, alertes seuils, scan courrier admin
│       │   ├── agent_deplacements.py      # Agent Déplacements — GPS, frais km, paniers, indemnités BTP zones, export frais
│       │   ├── agent_rh.py               # Agent RH — pointage heures, heures sup, indemnités employés, congés CIBTP, export paie
│       │   ├── email_pro.py                     # Agent Email Pro (IMAP, filtrage, résumé)
│       │   ├── vision.py                        # Agent Vision IA (analyse photos/documents)
│       │   ├── site_web.py                      # Agent Site Web (génération + MAJ site vitrine)
│       │   └── telephone.py                     # Agent Téléphone IA (V2 — décroche, prise d'info)
│       │
│       ├── prompts/                       # System prompts séparés du code (pattern Ouroboros prompts/)
│       │   ├── __init__.py
│       │   ├── supervisor_prompt.py       # System prompt du Supervisor
│       │   ├── consciousness_prompt.py    # Prompt de la background consciousness ("Wake up. Think.")
│       │   ├── devis_prompt.py            # System prompt Agent Devis (comprend le BTP, TVA, mentions)
│       │   ├── relance_prompt.py          # System prompt Agent Relance (ton adaptatif, escalade)
│       │   ├── compta_prompt.py           # System prompt Agent Compta (OCR, catégorisation)
│       │   ├── planning_prompt.py         # System prompt Agent Planning (timer, capacité)
│       │   ├── reputation_prompt.py       # System prompt Agent Réputation & Marketing (avis, SEO, réseaux sociaux)
│       │   ├── prospection_prompt.py      # System prompt Agent Prospection (CRM, relance archi)
│       │   ├── fiscalite_prompt.py        # System prompt Agent Fiscalité (statuts micro/EURL/SAS, URSSAF, TVA, calendrier fiscal)
│       │   ├── deplacements_prompt.py     # System prompt Agent Déplacements (barème km, zones BTP, paniers, GPS)
│       │   ├── rh_prompt.py              # System prompt Agent RH (conventions collectives BTP, grilles, CIBTP, heures sup)
│       │   └── metier_context.py          # Injecteur du contexte METIER.md + health invariants dans chaque prompt
│       │
│       ├── memory/                        # Couche mémoire (Mem0)
│       │   ├── __init__.py
│       │   ├── mem0_client.py             # Client Mem0 — init, store, recall, search
│       │   ├── artisan_memory.py          # Mémoire artisan — prix, clients, fournisseurs
│       │   └── enrichment.py              # Auto-enrichissement — web→base, corrections→apprentissage
│       │
│       ├── knowledge/                     # Base de connaissances BTP
│       │   ├── __init__.py
│       │   ├── rag_engine.py              # Moteur RAG — embeddings, recherche sémantique
│       │   ├── btp_rules.py               # Règles BTP — TVA, mentions, Factur-X, décennale
│       │   ├── price_database.py          # Base prix matériaux/MO par région
│       │   └── templates_devis.py         # Templates devis par corps de métier
│       │
│       ├── utils/                         # Utilitaires
│       │   ├── __init__.py
│       │   ├── supabase.py                # Client Supabase singleton (pattern AgentShield)
│       │   ├── redis.py                   # Client Redis (pattern AgentShield)
│       │   ├── errors.py                  # Erreurs custom StructoraiError (pattern AgentShield)
│       │   ├── claude_client.py           # Client Claude API — appels LLM avec retry
│       │   └── confidence.py              # Score de confiance par information (🟢🟡🔴)
│       │
│       └── models/                        # Modèles Pydantic
│           ├── __init__.py
│           ├── user.py                    # Organization, User, Settings
│           ├── devis.py                   # Devis, DevisLine, DevisStatus
│           ├── facture.py                 # Facture, FactureStatus
│           ├── chantier.py                # Chantier, ChantierStatus, TimeEntry
│           ├── client.py                  # Client, ClientTag
│           ├── ticket.py                  # Ticket, TicketCategory
│           ├── contact_pro.py             # Architecte, ApporteurAffaires
│           ├── agent_task.py              # Task, TaskResult, TaskStatus
│           └── gamification.py            # Level, Quest, Badge, XPEvent
│
├── mobile/                                # React Native (Expo) — 1 codebase → Android + PWA
│   ├── app.json                           # Config Expo
│   ├── eas.json                           # Config EAS Build (Android + PWA)
│   ├── package.json
│   ├── tsconfig.json
│   ├── tailwind.config.js                 # NativeWind (Tailwind pour React Native)
│   ├── babel.config.js
│   ├── metro.config.js
│   │
│   ├── app/                               # Expo Router (file-based routing)
│   │   ├── _layout.tsx                    # Root layout — providers, i18n, auth
│   │   ├── index.tsx                      # Landing/splash → redirect login ou dashboard
│   │   ├── (auth)/
│   │   │   ├── _layout.tsx
│   │   │   ├── login.tsx                  # Login email/password + Google OAuth
│   │   │   └── signup.tsx                 # Inscription + choix langue
│   │   ├── (dashboard)/
│   │   │   ├── _layout.tsx                # Dashboard layout — tabs + FAB micro flottant
│   │   │   ├── index.tsx                  # Home — résumé quotidien, actions prioritaires
│   │   │   ├── chantiers.tsx              # Pipeline Kanban — prospects → devis → en cours → terminé
│   │   │   ├── chantiers/[id].tsx         # Détail chantier — timer, dépenses, marge, historique, PHOTOS
│   │   │   ├── chantiers/[id]/photos.tsx  # Galerie photos du chantier — avant/pendant/après, analyse IA
│   │   │   ├── devis.tsx                  # Liste devis — statuts, filtres
│   │   │   ├── devis/[id].tsx             # Détail devis — preview PDF, envoyer, relancer
│   │   │   ├── devis/new.tsx              # Nouveau devis — vocal ou formulaire
│   │   │   ├── factures.tsx               # Liste factures — statuts, impayés
│   │   │   ├── compta.tsx                 # Tickets scannés, catégories, export comptable
│   │   │   ├── compta/scan.tsx            # Écran caméra — scan ticket
│   │   │   ├── clients.tsx                # Liste clients — CRM
│   │   │   ├── clients/[id].tsx           # Fiche client — historique, chantiers, paiements
│   │   │   ├── contacts-pro.tsx           # Architectes, apporteurs d'affaires
│   │   │   ├── avis.tsx                   # Avis Google — liste, score, réponses
│   │   │   ├── stats.tsx                  # Dashboard stats — CA, marge, temps, taux conversion
│   │   │   ├── chat.tsx                   # Chat plein écran avec le cerveau IA
│   │   │   ├── rh.tsx                     # Gestion employés — planning, heures, export paie
│   │   │   ├── fiscal.tsx                 # Tableau de bord fiscal annuel
│   │   │   ├── todo.tsx                   # File d'attente "À faire" — toutes les validations
│   │   │   └── settings.tsx               # Profil, langue, voix H/F, plan, entreprise
│   │   └── onboarding/
│   │       ├── _layout.tsx
│   │       └── index.tsx                  # Quêtes d'onboarding — gamification, progression
│   │
│   ├── components/
│   │   ├── ui/                            # Composants UI réutilisables
│   │   │   ├── Button.tsx
│   │   │   ├── Card.tsx
│   │   │   ├── Input.tsx
│   │   │   ├── Badge.tsx
│   │   │   ├── Modal.tsx
│   │   │   ├── Tabs.tsx
│   │   │   ├── KanbanBoard.tsx            # Pipeline chantiers drag-and-drop
│   │   │   └── ProgressBar.tsx            # Barre XP, progression cerveau
│   │   ├── chat/
│   │   │   ├── ChatBubble.tsx             # Message user/IA dans le chat
│   │   │   ├── ChatInput.tsx              # Input texte + bouton micro
│   │   │   ├── VoiceFAB.tsx              # Bouton micro flottant (floating action button)
│   │   │   └── VoiceWaveform.tsx          # Visualisation onde pendant l'enregistrement
│   │   ├── devis/
│   │   │   ├── DevisCard.tsx              # Carte devis dans la liste
│   │   │   ├── DevisPreview.tsx           # Preview PDF du devis
│   │   │   └── DevisVocalForm.tsx         # Formulaire vocal → décomposition postes
│   │   ├── chantier/
│   │   │   ├── ChantierCard.tsx           # Carte chantier dans le pipeline
│   │   │   ├── TimerWidget.tsx            # Timer start/stop chantier
│   │   │   └── MargeIndicator.tsx         # Indicateur marge temps réel
│   │   ├── compta/
│   │   │   ├── TicketCard.tsx             # Carte ticket scanné
│   │   │   └── ScanCamera.tsx             # Composant caméra OCR
│   │   ├── photos/
│   │   │   ├── PhotoCamera.tsx            # Prise de photo liée au chantier (géolocalisée, horodatée)
│   │   │   ├── PhotoGrid.tsx              # Grille photos du chantier (avant/pendant/après)
│   │   │   ├── BeforeAfterCompare.tsx     # Comparatif avant/après split screen
│   │   │   └── PhotoExport.tsx            # Export avant/après pour réseaux sociaux / devis PDF
│   │   ├── rh/
│   │   │   ├── EmployeeCard.tsx           # Carte employé — nom, classification, chantier affecté
│   │   │   ├── TeamPlanning.tsx           # Vue planning équipe — qui est où, quel jour
│   │   │   └── PayrollExport.tsx          # Export éléments de paie mensuel
│   │   ├── fiscal/
│   │   │   └── FiscalDashboard.tsx        # Tableau de bord fiscal annuel — CA, TVA, cotisations, résultat
│   │   ├── deplacements/
│   │   │   └── DeplacementTracker.tsx     # Suivi GPS + frais km + indemnités
│   │   ├── todo/
│   │   │   └── TodoQueue.tsx             # Dossier "À faire" — toutes actions en attente de validation
│   │   ├── gamification/
│   │   │   ├── LevelBadge.tsx             # Badge niveau (Apprenti→Légende)
│   │   │   ├── QuestCard.tsx              # Carte quête d'onboarding
│   │   │   ├── XPBar.tsx                  # Barre de progression XP
│   │   │   └── BrainProgress.tsx          # "Mon assistant me connaît à X%"
│   │   └── providers/
│   │       ├── AuthProvider.tsx            # Context auth Supabase
│   │       ├── I18nProvider.tsx            # Context i18n (i18next)
│   │       └── ChatProvider.tsx            # Context chat WebSocket
│   │
│   ├── hooks/
│   │   ├── useAuth.ts                     # Hook auth Supabase
│   │   ├── useChat.ts                     # Hook chat — envoi message, réception réponse
│   │   ├── useVoice.ts                    # Hook vocal — recording, STT, TTS playback
│   │   ├── useApi.ts                      # Hook API — fetch avec auth token
│   │   └── useI18n.ts                     # Hook i18n — langue courante, t()
│   │
│   ├── lib/
│   │   ├── supabase.ts                    # Client Supabase (Expo)
│   │   ├── api.ts                         # Client API backend
│   │   ├── storage.ts                     # AsyncStorage — cache local + offline queue
│   │   └── voice.ts                       # Expo Audio — record + playback TTS
│   │
│   ├── i18n/
│   │   ├── index.ts                       # Config i18next
│   │   ├── fr.json                        # Traductions français
│   │   ├── en.json                        # Traductions anglais
│   │   ├── tr.json                        # Traductions turc
│   │   ├── es.json                        # Traductions espagnol
│   │   ├── pt.json                        # Traductions portugais
│   │   └── ar.json                        # Traductions arabe
│   │
│   ├── types/
│   │   ├── index.ts                       # Types TypeScript partagés
│   │   ├── devis.ts
│   │   ├── chantier.ts
│   │   ├── client.ts
│   │   └── agent.ts
│   │
│   └── assets/
│       ├── icon.png                       # Icône app
│       ├── splash.png                     # Splash screen
│       └── adaptive-icon.png              # Icône Android adaptive
│
├── supabase/                              # Migrations SQL
│   └── migrations/
│       ├── 001_create_organizations.sql
│       ├── 002_create_users.sql
│       ├── 003_create_clients.sql
│       ├── 004_create_chantiers.sql
│       ├── 005_create_devis.sql
│       ├── 006_create_devis_lines.sql
│       ├── 007_create_factures.sql
│       ├── 008_create_tickets.sql
│       ├── 009_create_depenses.sql
│       ├── 010_create_time_entries.sql
│       ├── 011_create_photos.sql          # Photos chantier — url, chantier_id, type (avant/pendant/après), analyse IA, géoloc, metadata
│       ├── 012_create_contacts_pro.sql
│       ├── 012_create_avis_google.sql
│       ├── 013_create_emails.sql
│       ├── 014_create_relances.sql
│       ├── 015_create_agent_tasks.sql
│       ├── 016_create_agent_memory.sql
│       ├── 017_create_agent_budget.sql
│       ├── 018_create_knowledge_base.sql
│       ├── 019_create_gamification.sql
│       ├── 020_create_api_keys.sql
│       ├── 021_create_subscriptions.sql
│       ├── 022_create_metier_rules.sql
│       ├── 023_create_helper_functions.sql
│       ├── 024_create_rls_policies.sql
│       ├── 025_seed_metier_rules.sql      # TVA, mentions obligatoires, templates devis
│       ├── 026_create_deplacements.sql     # Déplacements — chantier_id, employe_id, km, zone BTP, indemnités, panier
│       ├── 027_create_employes.sql         # Employés — nom, classification BTP, coefficient, compétences, disponibilité
│       ├── 028_create_fiscal_calendar.sql  # Échéances fiscales par statut — URSSAF, TVA, CFE, IS, bilan
│       └── 029_create_deplacements_frais.sql # Frais déplacements détaillés — carburant, parking, péages, amendes par chantier
│
├── data/                                  # Données seed BTP
│   ├── tva_rules.json                     # Règles TVA BTP (5.5%, 10%, 20%)
│   ├── mentions_obligatoires.json         # 47 mentions obligatoires devis
│   ├── templates_devis/                   # Templates par corps de métier
│   │   ├── plomberie_sdb.json
│   │   ├── plomberie_cuisine.json
│   │   ├── electricite_tableau.json
│   │   ├── electricite_renovation.json
│   │   ├── peinture_appartement.json
│   │   ├── carrelage_sdb.json
│   │   ├── maconnerie_mur.json
│   │   └── menuiserie_fenetre.json
│   ├── fiscalite/
│   │   ├── micro_entreprise.json          # Seuils, taux cotisations, franchise TVA, ACRE 2026
│   │   ├── eurl.json                      # Charges déductibles, cotisations TNS, IS/IR, bilan
│   │   ├── sas_sasu.json                 # Assimilé salarié, cotisations, dividendes, flat tax
│   │   └── calendrier_fiscal.json         # Toutes les échéances par statut
│   ├── rh/
│   │   ├── conventions_btp.json           # 6 IDCC, grilles salariales, coefficients
│   │   ├── indemnites_btp_2026.json       # Barèmes trajet, transport, panier par zone et par région
│   │   └── cotisations_specifiques.json   # CIBTP 20.70%, OPPBTP 0.11%, PRO BTP, DFS 7%
│   ├── deplacements/
│   │   └── bareme_kilometrique_2026.json  # Barème fiscal km par puissance CV
│   └── prix_marche/                       # Prix moyens par région
│       ├── ile_de_france.json
│       ├── paca.json
│       ├── auvergne_rhone_alpes.json
│       └── ...                            # Autres régions
│
└── .github/
    └── workflows/
        ├── backend-ci.yml                 # CI backend — lint, tests, type check
        ├── mobile-ci.yml                  # CI mobile — lint, tests, type check
        └── deploy.yml                     # CD — deploy Railway + Vercel + EAS Build
```

---

## CE QUE FABRICE DOIT CRÉER (comptes + clés)

### Comptes à créer

| Service | Action | Résultat |
|---------|--------|---------|
| Supabase | Créer nouveau projet "structorai" | URL + anon key + service role key + JWT secret |
| Railway | Créer nouveau projet "structorai" | 2 services : api + agents |
| Vercel | Créer nouveau projet "structorai-pwa" | Domaine PWA iOS |
| Stripe | Créer compte (ou réutiliser Nova) + 3 Products/Prices | Secret key + webhook secret + price IDs |
| Google Play | Créer compte développeur ($25) | Accès Google Play Console |
| Meta Business | Créer app WhatsApp Business | Phone number ID + token + verify token |
| Porkbun | Acheter structorai.app + structorai.io | Domaines + DNS configurés |
| ElevenLabs | Créer compte | API key |
| Deepgram ou OpenAI | Créer compte (pour Whisper) | API key |
| Anthropic | Utiliser compte existant OU créer séparé | API key Claude |
| Brevo | Créer compte (ou réutiliser Nova) | API key |
| Twilio | Créer compte | Account SID + Auth token + phone number |
| Sentry | Créer nouveau projet "structorai" | DSN |
| Google Analytics | Créer propriété GA4 | Measurement ID |
| Yousign | Créer compte | API key |

### Variables d'environnement à configurer

```bash
# === APP ===
APP_ENV=production
APP_DEBUG=false
APP_URL=https://api.structorai.app
FRONTEND_URL=https://app.structorai.app
CORS_ORIGINS=["https://app.structorai.app","https://structorai.app"]

# === SUPABASE ===
SUPABASE_URL=https://xxxxx.supabase.co
SUPABASE_ANON_KEY=eyJ...
SUPABASE_SERVICE_ROLE_KEY=eyJ...
SUPABASE_JWT_SECRET=xxx

# === REDIS ===
REDIS_URL=redis://default:xxx@xxx.railway.internal:6379

# === STRIPE ===
STRIPE_SECRET_KEY=sk_live_xxx
STRIPE_WEBHOOK_SECRET=whsec_xxx
STRIPE_PRICE_PRO=price_xxx
STRIPE_PRICE_BUSINESS=price_xxx

# === ANTHROPIC (Claude API) ===
ANTHROPIC_API_KEY=sk-ant-xxx
ANTHROPIC_MODEL=claude-sonnet-4-6

# === VOICE ===
DEEPGRAM_API_KEY=xxx          # ou OPENAI_API_KEY pour Whisper
ELEVENLABS_API_KEY=xxx
ELEVENLABS_VOICE_MALE=xxx     # Voice ID homme
ELEVENLABS_VOICE_FEMALE=xxx   # Voice ID femme

# === WHATSAPP ===
WHATSAPP_PHONE_NUMBER_ID=xxx
WHATSAPP_ACCESS_TOKEN=xxx
WHATSAPP_VERIFY_TOKEN=xxx

# === SMS ===
TWILIO_ACCOUNT_SID=xxx
TWILIO_AUTH_TOKEN=xxx
TWILIO_PHONE_NUMBER=+33xxx

# === EMAIL ===
BREVO_API_KEY=xxx

# === SIGNATURE ===
YOUSIGN_API_KEY=xxx

# === MONITORING ===
SENTRY_DSN=https://xxx@sentry.io/xxx
GA4_MEASUREMENT_ID=G-xxx

# === MEM0 ===
MEM0_API_KEY=xxx              # ou self-hosted
```

---

## ORDRE DE BUILD — SPRINT PAR SPRINT

### Sprint 0 — Setup infra (Fabrice seul, 1 jour)
- [ ] Acheter domaines structorai.app + structorai.io
- [ ] Créer projet Supabase
- [ ] Créer projet Railway (2 services : api + agents)
- [ ] Créer projet Vercel
- [ ] Créer les 3 Products Stripe (Free/Pro/Business)
- [ ] Créer compte Google Play Developer ($25)
- [ ] Créer app Meta Business (WhatsApp)
- [ ] Créer comptes ElevenLabs + Deepgram
- [ ] Créer projet Sentry
- [ ] Configurer DNS (api.structorai.app → Railway, app.structorai.app → Vercel)
- [ ] Copier .env.example → .env avec toutes les clés

### Sprint 1 — Fondations (Claude Code, ~2 jours)
- [ ] Init repo GitHub NovaShips/structorai
- [ ] CLAUDE.md + docs/PRODUCT-CONTEXT.md + tous les skills/
- [ ] Backend : FastAPI skeleton (main.py, config.py, health.py)
- [ ] Backend : Auth middleware (JWT + API keys, pattern AgentShield)
- [ ] Backend : Error handlers (pattern AgentShield)
- [ ] Supabase : 25 migrations SQL
- [ ] Supabase : RLS policies (toutes les tables)
- [ ] Backend : Stripe billing (checkout, portal, webhooks)
- [ ] Mobile : Init Expo project avec navigation
- [ ] Mobile : Auth flow (login, signup, Supabase)
- [ ] Mobile : i18n setup (6 langues, fichiers JSON)
- [ ] Debug pipeline intégré (.claude/skills/)
- [ ] Deploy backend sur Railway + PWA sur Vercel
- [ ] Smoke test : signup → login → health check

### Sprint 2 — Chat + Voix + Cerveau IA (~3 jours)
- [ ] Backend : Supervisor (pattern Ouroboros — queue, workers, state, budget)
- [ ] Backend : base_agent.py (classe de base avec system prompt, tools, memory)
- [ ] Backend : Claude API client (appels LLM avec retry, budget tracking)
- [ ] Backend : Mem0 intégration (store, recall, search)
- [ ] Backend : Pipeline vocal (Whisper STT → cerveau → ElevenLabs TTS)
- [ ] Backend : Chat endpoint (POST /v1/chat + WebSocket)
- [ ] Backend : WhatsApp webhook (réception + envoi messages)
- [ ] Mobile : Chat screen avec input texte + micro FAB
- [ ] Mobile : VoiceFAB composant (push-to-talk + mode mains libres)
- [ ] Mobile : Playback TTS (réponse vocale du cerveau)
- [ ] Mobile : WebSocket connexion temps réel
- [ ] Test : envoyer un message texte → recevoir réponse IA
- [ ] Test : envoyer un vocal → recevoir réponse vocale

### Sprint 3 — Agent Devis (~3 jours)
- [ ] Backend : Agent Devis (vocal → décomposition postes → prix → TVA → PDF)
- [ ] Backend : Knowledge base BTP (RAG — prix marché, TVA, mentions)
- [ ] Backend : PDF service (ReportLab — devis conforme Factur-X)
- [ ] Backend : Signature service (Yousign API)
- [ ] Data : Seed TVA rules + mentions obligatoires + templates devis
- [ ] Data : Seed prix marché (3 régions minimum)
- [ ] Backend : METIER.md loader (constitution BTP)
- [ ] Mobile : Écran nouveau devis (vocal + formulaire)
- [ ] Mobile : Preview PDF devis
- [ ] Mobile : Liste devis avec statuts
- [ ] Test : dicter un devis vocal → PDF généré → envoi email

### Sprint 4 — Pipeline chantiers + Compta + Photos (~3 jours)
- [ ] Backend : CRUD chantiers (pipeline stages)
- [ ] Backend : Timer service (start/stop par chantier)
- [ ] Backend : Agent Compta (OCR tickets Claude Vision)
- [ ] Backend : Catégorisation automatique dépenses
- [ ] Backend : Photo service (upload Supabase Storage, compression, géoloc, horodatage)
- [ ] Backend : Analyse photo IA (Claude Vision → catégorisation avant/pendant/après, détection matériaux, aide au devis)
- [ ] Backend : Export avant/après (split screen image generation)
- [ ] Mobile : Pipeline Kanban (drag-and-drop chantiers)
- [ ] Mobile : Timer widget (start/stop)
- [ ] Mobile : Scan caméra tickets
- [ ] Mobile : Liste tickets avec catégories
- [ ] Mobile : Galerie photos chantier (PhotoCamera + PhotoGrid + BeforeAfterCompare)
- [ ] Mobile : Export photos avant/après pour réseaux sociaux
- [ ] Test : scan ticket → OCR → catégorisé → lié au chantier
- [ ] Test : photo chantier → upload → analyse IA → catégorisée "avant" → plus tard photo "après" → comparatif généré

### Sprint 5 — Relances + Factures (~2 jours)
- [ ] Backend : Facture service (devis accepté → facture auto)
- [ ] Backend : Agent Relance (séquences email/SMS adaptatives)
- [ ] Backend : Brevo integration (emails transactionnels)
- [ ] Backend : Twilio integration (SMS)
- [ ] Backend : Relance cron job (check quotidien)
- [ ] Mobile : Liste factures avec statuts
- [ ] Mobile : Indicateur impayés
- [ ] Test : devis accepté → facture auto → pas payée J+15 → relance email

### Sprint 6 — Réputation + Prospection + Email + Fiscalité + Déplacements (~3 jours)
- [ ] Backend : Agent Réputation & Marketing (SMS avis Google + réponse IA + réseaux sociaux)
- [ ] Backend : Agent Prospection (CRM pro + rappels architectes)
- [ ] Backend : Module email (IMAP polling + catégorisation)
- [ ] Backend : Background consciousness (loop pensée proactive)
- [ ] Backend : Agent Fiscalité (suivi annuel, calendrier fiscal, alertes seuils, scan courrier admin)
- [ ] Backend : Agent Déplacements (GPS, calcul frais km, indemnités BTP, paniers)
- [ ] Data : Seed fiscalité (micro/EURL/SAS/EI) + calendrier fiscal + barèmes km
- [ ] Mobile : Écran avis Google
- [ ] Mobile : Écran contacts pro
- [ ] Mobile : Résumé emails
- [ ] Mobile : Écran fiscal (tableau de bord annuel)
- [ ] Mobile : DeplacementTracker (GPS + frais)
- [ ] Mobile : TodoQueue (dossier "À faire" centralisé)
- [ ] Test : chantier terminé → SMS avis → avis reçu → réponse IA

### Sprint 7 — Gamification + RH + Polish (~3 jours)
- [ ] Backend : Gamification service (XP, niveaux, quêtes, badges)
- [ ] Backend : Score de contexte par devis (⭐ à ⭐⭐⭐⭐⭐)
- [ ] Backend : "Mon assistant me connaît à X%"
- [ ] Backend : Auto-enrichissement (web → base, corrections → apprentissage)
- [ ] Backend : Agent RH (pointage heures, conventions BTP, indemnités employés, export paie)
- [ ] Data : Seed conventions collectives BTP + grilles salariales + indemnités zones
- [ ] Mobile : Onboarding flow (7 quêtes)
- [ ] Mobile : Profil avec badges, niveau, XP
- [ ] Mobile : BrainProgress component
- [ ] Mobile : Micro-apprentissages vocaux
- [ ] Mobile : Écran RH (planning équipe, heures, export paie)
- [ ] Test : pointage employé → heures sup calculées → indemnités calculées → export paie généré

### Sprint 8 — Build + Deploy + Launch (~2 jours)
- [ ] EAS Build Android → Google Play Store (beta)
- [ ] PWA build → Vercel (iOS)
- [ ] WhatsApp Business : templates validés par Meta
- [ ] Smoke tests complets (debug pipeline)
- [ ] Landing page structorai.app
- [ ] 6 langues : vérification traductions complètes
- [ ] Seed data : prix marché toutes régions
- [ ] Security audit (pattern AgentShield)
- [ ] Performance test : latence vocale < 1.5s
- [ ] Launch : Facebook groupes artisans + Product Hunt

---

## TOTAL ESTIMÉ

| Métrique | Valeur |
|----------|--------|
| Fichiers backend Python | ~90 (+3 agents, +3 prompts, +3 services) |
| Fichiers mobile TypeScript | ~90 (+8 composants, +3 écrans) |
| Migrations SQL | 29 (au lieu de 25, +4 nouvelles) |
| Docs .md | 22 |
| Skills .md | 34 (au lieu de 31, +3 nouvelles) |
| Fichiers data JSON | ~30 (au lieu de ~20, +10 nouvelles) |
| Fichiers i18n | 6 |
| **Total fichiers** | **~300** |
| **Durée estimée** | **~18-20 jours de build** |

Pour référence : AgentShield = 355 fichiers en 32h. STRUCTORAI est plus complexe (agents IA, vocal, mobile, 6 langues) mais le pattern est rodé.

### Patterns critiques empruntés aux repos open-source

| Pattern | Source | Fichier STRUCTORAI | Pourquoi |
|---------|--------|-------------------|----------|
| Supervisor + workers + queue | Ouroboros `supervisor/` | `agents/supervisor.py`, `queue.py`, `workers.py` | Orchestration des 13 agents |
| Background consciousness loop | Ouroboros `consciousness.py` | `agents/consciousness.py` | Proactivité — le cerveau pense entre les tâches |
| BIBLE.md constitution | Ouroboros `BIBLE.md` | `docs/METIER.md` | Règles BTP immuables (TVA, mentions, Factur-X) |
| Circuit breaker + fallback chain | Ouroboros v6.0 | `agents/circuit_breaker.py` | 3 réponses vides → pause + fallback model |
| Context compaction | Ouroboros v6.1 `compact_context` | `agents/context_compaction.py` | Résumé contexte ancien via LLM léger |
| Health invariants injection | Ouroboros v6.0 SYSTEM.md | `agents/health_invariants.py` | Le cerveau détecte ses propres problèmes |
| Tool auto-discovery | Ouroboros `tools/` + claw-code `tools.py` | `agents/tool_registry.py` | Les agents découvrent leurs outils dynamiquement |
| System prompts séparés | Ouroboros `prompts/` | `prompts/` dossier dédié | Itérer les prompts sans toucher au code |
| Task decomposition parent/child | Ouroboros `schedule_task → wait → get_result` | `agents/queue.py` | Décomposer "faire un devis" en sous-tâches |
| Budget tracking per-agent | Ouroboros `state.py` + `budget.py` | `agents/budget.py` | Chaque agent a un budget LLM dédié |
| Skills plugin system | OpenClaw `skills/` + claw-code `commands.py` | Architecture `skills/` du repo | Modules métier comme plugins |
| Mémoire persistante multi-level | Mem0 user/session/agent | `memory/mem0_client.py` | Prix, clients, habitudes par artisan |
| Selective tool schemas | Ouroboros v6.1 | `agents/tool_registry.py` | ~29 tools core + tools optionnels à la demande → économise 40% tokens |
