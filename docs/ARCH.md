---
name: ARCH
description: Architecture technique globale de STRUCTORAI. Stack complète (frontend React Native Expo, backend FastAPI, Supabase, Claude, Mem0+MemPalace, Railway, Vercel). Diagrammes système (7 couches : clients, gateway, supervisor, 14 agents, mémoire hybride, données, infrastructure). Flux de données bout-en-bout pour les 10 scénarios critiques (devis vocal, relance auto, OCR ticket, etc.). Multi-tenant RLS. Résilience (circuit breakers, fallbacks). Performance (latences cibles). Principes architecturaux et anti-patterns à éviter.
type: technical-architecture
scope: backend, mobile, infrastructure, agents, memory, data
priority: critical
date: 2026-04-17
version: 1.0
stack_summary: React Native Expo + FastAPI + Supabase + Claude (Opus 4.7/Sonnet 4.6/Haiku 4.5) + Whisper + ElevenLabs + Mem0 + MemPalace + Railway + Vercel
target_launch: Juin 2026
---

# docs/ARCH.md — Architecture technique globale STRUCTORAI

> **Ce fichier est la source de vérité architecturale.**
> **Tout changement structurant** (ajout d'un service, changement de DB, changement de provider LLM) **DOIT** mettre à jour ce document.
> **Les 17 docs techniques priorité 2** (API, AGENTS, VOICE, WHATSAPP, DEPLOY, etc.) **s'appuient tous sur ce document comme fondation**.

---

## 0. SOMMAIRE

1. [Principes architecturaux](#1-principes-architecturaux)
2. [Vue d'ensemble — 7 couches](#2-vue-densemble--7-couches)
3. [Couche 1 — Clients (3 canaux)](#3-couche-1--clients-3-canaux)
4. [Couche 2 — Gateway (FastAPI)](#4-couche-2--gateway-fastapi)
5. [Couche 3 — Supervisor (pattern Ouroboros)](#5-couche-3--supervisor-pattern-ouroboros)
6. [Couche 4 — 14 agents autonomes](#6-couche-4--14-agents-autonomes)
7. [Couche 5 — Mémoire hybride](#7-couche-5--mémoire-hybride)
8. [Couche 6 — Données (Supabase PostgreSQL)](#8-couche-6--données-supabase-postgresql)
9. [Couche 7 — Infrastructure](#9-couche-7--infrastructure)
10. [Flux de données — 10 scénarios critiques](#10-flux-de-données--10-scénarios-critiques)
11. [Multi-tenant et sécurité](#11-multi-tenant-et-sécurité)
12. [Résilience et fallbacks](#12-résilience-et-fallbacks)
13. [Performance — latences cibles](#13-performance--latences-cibles)
14. [Observabilité](#14-observabilité)
15. [Anti-patterns à éviter](#15-anti-patterns-à-éviter)
16. [Références croisées](#16-références-croisées)

---

## 1. PRINCIPES ARCHITECTURAUX

### 1.1 — Les 10 principes fondateurs

1. **1 codebase mobile → 3 canaux** : React Native (Expo) compile vers Android natif + PWA iOS + WhatsApp. Le backend est identique. La logique est centralisée.

2. **Le Supervisor est le cerveau unique** : tout passe par lui. Pas d'agent qui parle directement à un autre agent. Le Supervisor orchestre.

3. **Les 14 agents sont stateless** : ils reçoivent leur contexte au moment de l'appel (Mem0 + MemPalace + DB). Ils ne conservent pas d'état.

4. **Mémoire hybride souveraine EU** : Mem0 (cloud EU) pour les patterns légers + MemPalace (self-hosted Railway EU) pour le verbatim. Aucune donnée ne quitte l'Europe.

5. **Multi-tenant par organization_id + RLS Supabase** : chaque artisan = 1 organization, isolation totale des données au niveau PostgreSQL.

6. **Supervision humaine obligatoire** : aucune action externe (envoi devis, facture, post social) sans bouton "Valider" (exigence AI Act + trust artisan).

7. **Circuit breakers partout** : si un LLM pète, fallback sur un autre modèle. Si Mem0 pète, fallback MemPalace. Si Supabase pète, queue locale + retry.

8. **Background consciousness** : le cerveau ne fait pas que répondre. Il pense entre les tâches, détecte les problèmes, propose des actions (pattern Ouroboros).

9. **Score de confiance sur chaque output** : 🟢🟡🔴 affiché à l'artisan. Si 🔴, escalade obligatoire. Règle zéro invention.

10. **Observabilité dès J1** : Sentry + logs structurés + Mixpanel events + audit log AI Act (`ai_decisions`).

### 1.2 — Pourquoi ces choix

| Choix | Alternative rejetée | Pourquoi |
|---|---|---|
| **React Native Expo** | Native iOS + Android séparés | 1 codebase pour 1 dev (Fabrice), PWA iOS bypass Apple Store (commission 30%) |
| **FastAPI** | Node.js Express, Django, Flask | Async natif, Pydantic validation, OpenAPI auto, perf Python 3.12+ |
| **Supabase PostgreSQL** | Firebase, MongoDB, RDS pur | RLS puissant, Auth intégré, Storage S3-compatible, EU Frankfurt |
| **Claude (Anthropic)** | GPT-4, Mistral, Llama | Qualité raisonnement BTP, context long, constitutional AI, Pricing compétitif |
| **Mem0 + MemPalace** | Pinecone seul, Weaviate, OpenAI assistant memory | Hybride = résilience + souveraineté EU, voir `docs/MEMORY.md` |
| **Railway + Vercel** | AWS, GCP, Heroku | Deploy simplissime, EU region, coûts prévisibles <500€/mois à 1K clients |
| **Pattern Ouroboros** | LangChain, CrewAI | Supervisor + Background Consciousness + Circuit Breakers éprouvés, 456 GitHub stars référence |

---

## 2. VUE D'ENSEMBLE — 7 COUCHES

```
┌───────────────────────────────────────────────────────────────────────┐
│                    COUCHE 1 — CLIENTS (3 canaux)                       │
│  ┌─────────────┐  ┌─────────────┐  ┌────────────────────────────┐    │
│  │ Android     │  │ PWA iOS     │  │ WhatsApp                   │    │
│  │ (Google     │  │ (Safari →   │  │ (Meta Cloud API)           │    │
│  │  Play)      │  │  écran home)│  │                            │    │
│  │ React Native│  │ Expo Web    │  │                            │    │
│  └──────┬──────┘  └──────┬──────┘  └──────────┬─────────────────┘    │
└─────────┼────────────────┼────────────────────┼──────────────────────┘
          │ HTTPS/WSS      │ HTTPS/WSS          │ Webhook
          │                │                    │
┌─────────▼────────────────▼────────────────────▼──────────────────────┐
│                    COUCHE 2 — GATEWAY (FastAPI)                       │
│  REST API + WebSocket + Webhooks + Auth Middleware + Rate Limiter    │
└─────────────────────────────┬────────────────────────────────────────┘
                              │
┌─────────────────────────────▼────────────────────────────────────────┐
│            COUCHE 3 — SUPERVISOR (pattern Ouroboros)                  │
│  Queue + Workers + Budget + Circuit Breakers + Background Consc.     │
└─────────────────────────────┬────────────────────────────────────────┘
                              │
┌─────────────────────────────▼────────────────────────────────────────┐
│                 COUCHE 4 — 14 AGENTS AUTONOMES                        │
│  Devis + Relance + Compta + Planning + Réputation + Prospection +    │
│  Email + Fiscalité + Déplacements + RH + Vision + Site Web + Coach + │
│  V2 Téléphone                                                         │
└─────────────────────────────┬────────────────────────────────────────┘
                              │
┌─────────────────────────────▼────────────────────────────────────────┐
│                COUCHE 5 — MÉMOIRE HYBRIDE (EU)                        │
│  ┌────────────────────┐         ┌────────────────────────────┐       │
│  │ Mem0 (cloud EU)    │         │ MemPalace (Railway EU)     │       │
│  │ Patterns légers    │ ◄─────► │ Verbatim + Knowledge Graph │       │
│  └────────────────────┘         └────────────────────────────┘       │
└─────────────────────────────┬────────────────────────────────────────┘
                              │
┌─────────────────────────────▼────────────────────────────────────────┐
│                COUCHE 6 — DONNÉES (Supabase PostgreSQL)               │
│  organizations + users + clients + devis + factures + chantiers +    │
│  tickets + contacts_pro + photos + tasks + ai_decisions + audit      │
│  RLS multi-tenant + Storage S3 compat                                │
└─────────────────────────────┬────────────────────────────────────────┘
                              │
┌─────────────────────────────▼────────────────────────────────────────┐
│                COUCHE 7 — INFRASTRUCTURE                              │
│  Railway (backend+agents) + Vercel (PWA) + EAS (build Android)       │
│  + Sentry + Mixpanel + GitHub Actions                                │
└───────────────────────────────────────────────────────────────────────┘
```

### 2.1 — Flow général (cas chat)

```
Artisan → App → HTTPS POST /v1/chat → Gateway (auth JWT) → 
  Supervisor (enqueue task) → Agent adapté (selon intent) → 
  [Mem0 recall + MemPalace recall + Claude LLM call] → 
  Response → Supervisor validates → Gateway → App → Artisan
                     ↓
              ai_decisions log (audit AI Act)
```

### 2.2 — Flow général (cas proactif)

```
Background Consciousness (tick 1×/h) → Détecte opportunité → 
  Supervisor (enqueue task) → Agent (génère action) → 
  [Mem0 + MemPalace recall] → Proposition → 
  Tasks_to_validate (dossier À faire) → 
  Notif push artisan → Artisan clique "Valider" → Exécution
```

---

## 3. COUCHE 1 — CLIENTS (3 CANAUX)

### 3.1 — Android natif (React Native via Expo)

**Build** : EAS Build (Expo Application Services) → APK/AAB → Google Play Store

**Framework** :
- React Native 0.76+ via Expo SDK 54+
- Expo Router (file-based routing)
- NativeWind (Tailwind pour React Native)
- TypeScript strict

**Features natives clés** :
- Caméra native (scan tickets haute qualité)
- Push Notifications natives (Expo Notifications)
- GPS natif (Agent Déplacements)
- Mode hors-ligne (Expo SQLite local + sync queue)
- Micro FAB flottant permanent (chat vocal partout)
- Biométrie (FaceID/TouchID/Fingerprint) pour déverrouillage rapide

**Voice pipeline** :
- Expo Audio pour enregistrement
- Upload vers `/v1/chat/audio` → Whisper STT → réponse texte + TTS ElevenLabs
- Latence cible : <1.5s entre fin de parole artisan et début réponse vocale

### 3.2 — PWA iOS (Expo Web)

**Stratégie** : on bypass l'Apple Store pour éviter la commission 30% + la validation longue.

**Install** : Safari → "Ajouter à l'écran d'accueil" → icône sur home iPhone (comme une app native)

**Limitations PWA iOS** :
- Pas de push natif → fallback Brevo email + Twilio SMS pour notifications critiques
- Caméra web OK mais qualité < native
- Pas de biométrie (Safari limite)
- Web Speech API pour vocal (correct mais moins optimisé que Whisper)

**Mitigation** :
- UX optimisée pour iOS (design natif-like)
- Notifications email/SMS pour les cas critiques (relance urgente, devis signé)

### 3.3 — WhatsApp (Meta Cloud API)

**Use case** : l'artisan est sur chantier, pas le temps d'ouvrir l'app → WhatsApp est ouvert 15x/jour.

**Flow** :
- Artisan envoie vocal/photo/texte à un numéro WhatsApp Business STRUCTORAI
- Webhook POST → `/v1/whatsapp/webhook` → identification artisan (téléphone) → Supervisor
- Cerveau traite → réponse texte/vocale → renvoyé sur WhatsApp

**Coût** : Meta facture par conversation (0.0025-0.12€/conv selon pays et type). À tracker dans les coûts variables.

**Authentification** : l'artisan lie son numéro WhatsApp à son compte lors de l'onboarding (OTP).

---

## 4. COUCHE 2 — GATEWAY (FASTAPI)

### 4.1 — Stack Gateway

- **FastAPI** 0.115+ (Python 3.12+)
- **Uvicorn** (ASGI server)
- **Pydantic v2** (validation input/output)
- **Hypercorn** en fallback (HTTP/2 et HTTP/3)

### 4.2 — Structure des endpoints

```
/v1/
├── health                            # GET — status services
├── auth/                             # Via Supabase Auth directement
├── chat                              # POST — message texte
├── chat/audio                        # POST — upload vocal
├── devis/                            # CRUD + send + sign
├── factures/                         # CRUD + send + status
├── chantiers/                        # CRUD + timer + stats
├── clients/                          # CRUD + fiche + historique
├── tickets/                          # POST scan → OCR
├── contacts_pro/                     # CRUD archi/apporteurs
├── photos/                           # Upload, analyse, avant/après, export
├── emails/                           # GET résumé emails pro
├── avis/                             # CRUD Google reviews
├── stats/                            # GET dashboard
├── billing/                          # Stripe checkout/portal/webhooks
├── onboarding/                       # Quêtes, XP, niveau, badges
├── settings/                         # CRUD settings user
├── whatsapp/webhook                  # POST Meta → message
├── support/escalate                  # POST escalade humaine (F136)
└── admin/                            # Endpoints admin Fabrice
    ├── support-dashboard
    ├── ai-decisions/export
    └── metrics
```

### 4.3 — Middlewares (ordre d'exécution)

1. **CORS** — autorise les domaines mobile + PWA
2. **Request ID** — UUID par requête pour traçabilité
3. **Auth JWT** — vérifie Supabase Auth token (sauf `/health`, `/webhooks/*`)
4. **Rate limit** — par plan (Free 50/j, Pro 500/j, Business illimité)
5. **Plan limits** — vérifie quota (ex: nb devis/mois plan Starter = 3)
6. **Logging structuré** — timestamp, user_id, endpoint, latency, status
7. **Error handler** — convertit exceptions → StructorAIError → HTTP response

### 4.4 — Auth flow

```
Mobile app → Supabase Auth (login email/pwd ou Google OAuth) → 
  JWT token → stocké SecureStore (Keychain iOS / Keystore Android) → 
  Chaque requête /v1/* → Authorization: Bearer <token> → 
  Middleware auth.py → vérifie JWT + fetch user → 
  Request.state.user = User(id, org_id, plan, ...) → endpoint
```

**Gestion multi-tenant** : `user.organization_id` est injecté dans toutes les requêtes DB. Les RLS Supabase vérifient que `auth.jwt() ->> 'org_id' = row.organization_id`.

### 4.5 — WebSocket (chat temps réel)

Pour le chat, on utilise WebSocket quand possible (meilleure latence) :
- Connexion à `wss://api.structorai.app/v1/chat/ws`
- Auth via token dans query string initial
- Events : `message`, `typing`, `voice_response_stream`, `error`

Fallback : si WebSocket non supporté (corporate firewall), HTTP long polling.

---

## 5. COUCHE 3 — SUPERVISOR (PATTERN OUROBOROS)

### 5.1 — Rôle du Supervisor

Le Supervisor est **l'entrée unique** vers les 14 agents. Aucun agent n'est appelé directement par l'API.

**Responsabilités** :
- Recevoir les tâches de l'API Gateway
- Choisir le(s) bon(s) agent(s) selon l'intent
- Gérer la queue prioritaire
- Tracker le budget LLM par agent
- Déclencher les circuit breakers
- Background consciousness (pensée entre les tâches)
- Events dispatch inter-agents

### 5.2 — Composants (dans `backend/app/agents/`)

| Fichier | Rôle |
|---|---|
| `supervisor.py` | Orchestrateur principal |
| `state.py` | État global artisan, métriques |
| `queue.py` | File de tâches prioritaires (Redis-based) |
| `workers.py` | Cycle de vie des agents |
| `consciousness.py` | Background consciousness (pense 1×/h) |
| `events.py` | Dispatch événements inter-agents |
| `budget.py` | Tracking coût LLM, circuit breaker |
| `circuit_breaker.py` | 3 réponses vides → pause + fallback |
| `context_compaction.py` | Compression contexte via LLM léger |
| `health_invariants.py` | Health checks injectés dans contexte LLM |
| `tool_registry.py` | Auto-discovery des tools par agent |
| `metier.py` | Loader METIER.md (constitution BTP) |

### 5.3 — Budget LLM par agent (allocation %)

Voir `docs/SINGLE_SOURCE_OF_TRUTH.md` §Supervisor Budget Allocation. Résumé :

| Agent | Budget | Modèle principal |
|---|---|---|
| Devis | 20% | Opus 4.7 |
| Compta | 9% | Sonnet 4.6 |
| Vision IA | 9% | Opus 4.7 |
| Fiscalité | 8% | Sonnet 4.6 |
| RH | 8% | Sonnet 4.6 |
| Site Web | 7% | Sonnet 4.6 |
| Relance | 7% | Haiku 4.5 |
| Planning | 6% | Haiku 4.5 |
| Réputation | 6% | Haiku 4.5 |
| Coach | 6% | Opus 4.7 |
| Prospection | 5% | Haiku 4.5 |
| Email Pro | 4% | Haiku 4.5 |
| Déplacements | 3% | Haiku 4.5 |
| Téléphone (V2) | 2% | Sonnet 4.6 |
| **Total** | **100%** | |

### 5.4 — Background Consciousness

**Pattern Ouroboros** : le cerveau pense entre les tâches (pas seulement quand l'artisan parle).

**Déclenchement** :
- Tick toutes les heures (cron-like)
- Événements (devis envoyé, facture payée, etc.)

**Ce qu'elle fait** :
- Détecte les devis non relancés (J+3)
- Détecte les factures en retard
- Prépare les rappels fiscaux
- Enrichit les patterns Mem0
- Propose des actions à l'artisan (Dossier "À faire")

**Coût** : ~$0.07/pensée × 24 pensées/jour = ~$1.68/mois par artisan → budgétisé dans les coûts variables.

### 5.5 — Limites et garde-fous

- **MAX_ROUNDS = 50** par tâche (évite infinite loop)
- **Budget max par agent** (coupe si dépassé)
- **Timeout 30s** par appel LLM (évite attente infinie)
- **Fallback model chain** : Opus → Sonnet → Haiku si erreur

---

## 6. COUCHE 4 — 14 AGENTS AUTONOMES

### 6.1 — Liste des agents V1

Voir `docs/FEATURES_COMPLETE.md` pour détails exhaustifs. Résumé :

| # | Agent | Modèle | Rôle principal |
|---|---|---|---|
| 1 | **Devis** | Opus 4.7 | Génération devis vocal→PDF, TVA, mentions, Factur-X |
| 2 | **Relance** | Haiku 4.5 | Relances devis J+3 et factures J+15/30/45 avec ton adaptatif |
| 3 | **Compta** | Sonnet 4.6 | OCR tickets, catégorisation, marge, export comptable |
| 4 | **Planning** | Haiku 4.5 | Timer chantier, capacité, enchaînements, alertes |
| 5 | **Réputation** | Haiku 4.5 | Avis Google, SEO, publications sociales |
| 6 | **Prospection** | Haiku 4.5 | CRM architectes/apporteurs, leads |
| 7 | **Email Pro** | Haiku 4.5 | Filtrage IMAP, résumé quotidien |
| 8 | **Fiscalité** | Sonnet 4.6 | Calendrier fiscal, alertes seuils, statuts |
| 9 | **Déplacements** | Haiku 4.5 | GPS, frais km, paniers, indemnités BTP |
| 10 | **RH** | Sonnet 4.6 | Pointage, heures sup, CIBTP, export paie |
| 11 | **Vision IA** | Opus 4.7 | Analyse photos chantier + documents (F121) |
| 12 | **Site Web** | Sonnet 4.6 | Génération vitrine IA + MAJ auto |
| 13 | **Coach Business** | Opus 4.7 | Analyse stratégique mensuelle (F119) |
| 14 | **V2 Téléphone** | Sonnet 4.6 | Agent Téléphone IA (V2 add-on) |
| — | **Supervisor** | Sonnet 4.6 | Orchestration + Background Consciousness |

### 6.2 — Classe de base `BaseAgent`

Chaque agent hérite de `BaseAgent` (dans `base_agent.py`) :

```python
class BaseAgent:
    name: str                         # "devis", "relance", etc.
    model: str                        # "claude-opus-4-7", etc.
    system_prompt: str                # Prompt spécialisé (dans prompts/)
    tools: list                       # Tools disponibles
    budget_pct: float                 # % budget global
    
    async def execute(task: Task) -> TaskResult:
        # 1. Recall memory (Mem0 + MemPalace)
        context = await self.recall_context(task)
        
        # 2. Inject METIER.md rules + health invariants
        context += self.metier_context()
        
        # 3. LLM call with retry + circuit breaker
        response = await self.llm_call(task, context)
        
        # 4. Validate confidence score
        if response.confidence < 0.6:
            return self.escalate(task)
        
        # 5. Execute tool calls if any
        if response.tool_calls:
            results = await self.run_tools(response.tool_calls)
        
        # 6. Log to ai_decisions (audit AI Act)
        await self.log_decision(task, response)
        
        # 7. Store learnings to Mem0 + MemPalace
        await self.store_learnings(task, response)
        
        return response
```

### 6.3 — Communication inter-agents

**Règle stricte** : un agent ne peut PAS appeler directement un autre agent. Il passe par le Supervisor via events.

**Exemple** : Agent Devis finit → émet event `devis.sent` → Supervisor → enqueue tâche Agent Relance J+3.

**Pourquoi** : évite les dépendances cycliques, facilite le debug, permet le monitoring centralisé.

### 6.4 — Tool registry

Chaque agent déclare ses tools disponibles dans son fichier :

```python
# Agent Devis
AVAILABLE_TOOLS = [
    "create_devis",
    "fetch_price_from_db",
    "search_knowledge_base",
    "generate_pdf",
    "send_yousign",
    # ...
]
```

Le `tool_registry.py` collecte tous les tools et les expose au LLM via l'API tool use de Claude.

### 6.5 — Prompts système

Stockés dans `backend/app/prompts/` (séparés du code, pattern Ouroboros).

Chaque prompt injecte :
- Le contexte METIER.md (constitution BTP)
- Les health invariants (budget, stale data, etc.)
- La mémoire Mem0 + MemPalace pertinente
- Les règles de l'agent (tone, style, exclusions)

**Taille moyenne prompt système** : 2-4K tokens.

---

## 7. COUCHE 5 — MÉMOIRE HYBRIDE

### 7.1 — Vue d'ensemble

Voir `docs/MEMORY.md` pour détails exhaustifs.

**Principe** : hybride Mem0 (patterns légers, cloud EU) + MemPalace (verbatim riche, self-hosted Railway EU).

**Architecture** :

```
                    ┌─────────────────────────┐
                    │    ARTISAN parle        │
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │  Agent reçoit la tâche  │
                    └────────────┬────────────┘
                                 │
                    ┌────────────┴────────────┐
                    │                         │
                    ▼                         ▼
        ┌─────────────────────┐   ┌─────────────────────┐
        │  Mem0 (cloud EU)    │   │  MemPalace (Railway)│
        │  - Prix perso       │   │  - Verbatim vocaux  │
        │  - Patterns clients │   │  - Knowledge graph  │
        │  - Préférences      │   │  - Temporal facts   │
        │  - Fast recall      │   │  - ChromaDB + SQLite│
        └─────────────────────┘   └─────────────────────┘
                    │                         │
                    └────────────┬────────────┘
                                 │ Fusion
                                 ▼
                    ┌─────────────────────────┐
                    │   Contexte agent        │
                    │   (injected in prompt)  │
                    └─────────────────────────┘
```

### 7.2 — Flux de stockage

Quand l'artisan parle → transcription → agent extrait :
- **Patterns structurés** (prix, clients, préférences) → Mem0
- **Verbatim riche** (conversations, décisions) → MemPalace
- **Facts temporels** (Martin payé le 12/03 à 17h) → MemPalace knowledge graph

### 7.3 — Flux de recall

Quand un agent a besoin de contexte :
1. Query Mem0 (fast, <100ms) → patterns
2. Query MemPalace (plus profond, <300ms) → verbatim
3. Fusion → injection dans prompt

### 7.4 — Souveraineté EU

- **Mem0** : cloud EU (Frankfurt)
- **MemPalace** : 100% self-hosted sur Railway EU (ChromaDB + SQLite)
- **Aucune donnée** ne quitte l'Europe
- Argument massif vs concurrents hébergés US (Obat, Tolteck utilisent souvent AWS US ou pas de précision)

### 7.5 — Fallback dégradé

Voir `docs/MEMORY-STRATEGY.md` pour détails. Résumé :
- Si MemPalace tombe → read-only mode, Mem0 seul
- Si MemPalace down >30 min → migration Mem0 seul (runbook 2h)
- RPO 24h, RTO 2h

---

## 8. COUCHE 6 — DONNÉES (SUPABASE POSTGRESQL)

### 8.1 — Tables principales

| Table | Contenu |
|---|---|
| `organizations` | Multi-tenant (1 org = 1 artisan ou entreprise) |
| `users` | Profil, préférences, abonnement |
| `clients` | Fiche client, contact, historique |
| `devis` | Tous les devis avec statut, PDF, signature |
| `devis_lines` | Lignes de chaque devis |
| `factures` | Toutes factures avec statut paiement |
| `chantiers` | Pipeline : prospect → devis → en cours → terminé |
| `time_entries` | Pointage heures chantier |
| `tickets` | Tickets caisse scannés, catégorisés |
| `contacts_pro` | Architectes, apporteurs d'affaires |
| `photos_chantier` | Galerie photos par chantier |
| `emails_summary` | Résumés emails pro quotidiens |
| `avis_google` | Avis Google reçus + réponses |
| `tasks_to_validate` | Dossier "À faire" (actions en attente validation) |
| `ai_decisions` | Audit log AI Act (voir `docs/AI-ACT-COMPLIANCE.md`) |
| `gamification_events` | XP, quêtes, badges |
| `memory_layer` | Lien avec Mem0 (user/session/agent/org IDs) |

### 8.2 — Multi-tenant avec RLS

**Chaque table** (sauf `ai_decisions` qui a des règles custom) a ce pattern RLS :

```sql
ALTER TABLE devis ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users see own org devis"
    ON devis FOR SELECT
    USING (organization_id = (auth.jwt() ->> 'org_id')::uuid);

CREATE POLICY "Users create own org devis"
    ON devis FOR INSERT
    WITH CHECK (organization_id = (auth.jwt() ->> 'org_id')::uuid);

CREATE POLICY "Users update own org devis"
    ON devis FOR UPDATE
    USING (organization_id = (auth.jwt() ->> 'org_id')::uuid);
```

### 8.3 — Storage (S3 compat Supabase)

Bucket organization avec RLS :
- `organizations/{org_id}/devis/{devis_id}.pdf`
- `organizations/{org_id}/factures/{facture_id}.pdf`
- `organizations/{org_id}/photos/{photo_id}.jpg`
- `organizations/{org_id}/tickets/{ticket_id}.jpg`
- `organizations/{org_id}/signatures/{devis_id}.pdf`

Seul le user de cette org peut lire/écrire dans son bucket.

### 8.4 — Migrations (37 au total)

Voir `docs/MIGRATIONS.md` (à créer en priorité 2). Liste rapide :
- 001-015 : tables de base (Sprint 0-3)
- 016-029 : tables agents + features (Sprint 4-6)
- 030-037 : migrations audit V6/V7 (ai_decisions, tasks_to_validate, etc.)

---

## 9. COUCHE 7 — INFRASTRUCTURE

### 9.1 — Railway (Backend + Agents)

**Services Railway** :
1. `backend-api` — FastAPI Gateway
2. `agents-workers` — Workers des 14 agents
3. `mempalace` — MemPalace (ChromaDB + SQLite)
4. `redis` — Queue Redis (pour Supervisor queue)
5. `cron-jobs` — Background consciousness ticks

**Region** : EU-West (Amsterdam)

**Coût estimé** :
- <100 clients : ~20$/mois
- 100-500 clients : ~50$/mois
- 500-1K clients : ~120$/mois
- +25$/mois pour read replica Supabase à partir de 50 clients

### 9.2 — Vercel (PWA iOS)

- Déploiement du build Expo Web
- CDN global (Cloudflare)
- SSL automatique
- Preview branches (Git PR)

**Coût** : gratuit <100K requêtes/mois, ~20$/mois si dépassement.

### 9.3 — EAS Build (Android APK/AAB)

- Build cloud Expo → Google Play
- Plan EAS Production (29$/mois) ou pay-per-build
- Auto-increment versions

### 9.4 — CI/CD (GitHub Actions)

**Workflows** :
- `backend-test.yml` — tests pytest sur chaque PR
- `backend-deploy.yml` — deploy Railway sur merge main
- `mobile-build.yml` — EAS Build sur merge main
- `mobile-preview.yml` — Vercel preview sur PR
- `migrations.yml` — applique migrations Supabase sur merge

### 9.5 — Monitoring (Sentry)

- Backend : Sentry Python SDK
- Mobile : Sentry React Native SDK
- Alertes : erreurs >5% sur 5 min → Slack + SMS Fabrice

### 9.6 — Analytics

- **GA4** (Google Analytics 4) : pour le marketing + landing
- **Mixpanel** (ou PostHog EU) : events produit (onboarding funnel, feature usage)
- **Privacy** : IP anonymisée, pas de cookies tracking tiers (RGPD)

---

## 10. FLUX DE DONNÉES — 10 SCÉNARIOS CRITIQUES

### 10.1 — Scénario 1 : Création devis vocal

```
1. Artisan → micro FAB mobile → enregistre 45s
2. Upload audio → POST /v1/chat/audio
3. Whisper STT → texte FR
4. Supervisor → intent detection → Agent Devis
5. Agent Devis :
   a. Recall Mem0 (prix artisan, client)
   b. Recall MemPalace (chantiers similaires)
   c. Claude Opus 4.7 → génère 8 postes
   d. Valide prix vs DB → score confiance 🟢🟡🔴
6. Supervisor → task_to_validate (dossier À faire)
7. Notif push artisan : "Devis prêt, valide-le"
8. Artisan ouvre → review → clique "Envoyer"
9. Génération PDF ReportLab → upload Supabase Storage
10. Yousign API → signature lien → Brevo email au client
11. Log ai_decisions (audit AI Act)
12. Event "devis.sent" → Supervisor → enqueue Agent Relance J+3
```

**Latence cible totale** : <15s entre fin parole et devis prêt à valider.

### 10.2 — Scénario 2 : Relance auto J+3

```
1. Cron job (Railway) toutes les 6h → tous les devis avec status="sent" AND sent_at < NOW() - 3 days
2. Pour chaque devis → Supervisor → enqueue Agent Relance
3. Agent Relance :
   a. Recall Mem0 (pattern client : payeur vs mauvais payeur)
   b. Sélection ton (doux / ferme)
   c. Claude Haiku → génère message adapté
4. task_to_validate → Dossier "À faire"
5. Artisan valide → envoi SMS Twilio + Email Brevo
6. Log ai_decisions
```

### 10.3 — Scénario 3 : OCR ticket de caisse

```
1. Artisan scan ticket avec caméra (Expo Camera)
2. Upload photo → POST /v1/tickets/scan
3. Claude Vision (Sonnet 4.6) → extrait : fournisseur, date, montant HT, TVA, catégorie
4. Agent Compta :
   a. Auto-catégorise (matériaux / fourniture / déplacement / etc.)
   b. Impute au chantier en cours (si GPS proche)
   c. Calcule marge impact
5. Insert DB ticket → RLS org_id
6. Notif artisan : "Ticket 47€ ajouté au chantier Martin"
```

**Latence cible** : <5s entre scan et ticket catégorisé.

### 10.4 — Scénario 4 : Question BTP ("Quelle TVA pour rénovation SDB ?")

```
1. Artisan écrit chat → POST /v1/chat
2. Supervisor → intent "question_metier" → pas d'agent spécifique, direct Claude Sonnet
3. Injection contexte METIER.md (section TVA)
4. Claude répond : "10% si logement >2 ans, matériaux+MO compris..."
5. Score confiance 🟢 (source Légifrance)
6. Réponse + bouton "Appliquer au devis en cours"
```

**Latence cible** : <3s.

### 10.5 — Scénario 5 : Agent Coach Business mensuel

```
1. Cron 1×/mois (1er du mois) → pour chaque artisan plan Pro/Business
2. Supervisor → enqueue Agent Coach
3. Agent Coach (Opus 4.7) :
   a. Recall Mem0 (métriques 30 derniers jours)
   b. Recall MemPalace (décisions, pivots)
   c. Benchmarks département (data/benchmarks/)
   d. Analyse : taux conversion, marge, positioning, prospection
4. Disclaimer obligatoire (voir COACH-DISCLAIMER.md)
5. Capsule vidéo 60s + rapport PDF
6. Notif "Ton analyse Coach mensuelle est prête"
```

### 10.6 — Scénario 6 : Vision IA photo chantier

```
1. Artisan prend photo chantier
2. Upload → POST /v1/photos
3. Claude Opus 4.7 Vision :
   a. Identifie : état avancement, anomalies, matériaux visibles
   b. Détecte oublis (pas de joint, pas de VMC, etc.)
4. Stockage avec métadonnées (date, GPS, lié au chantier)
5. Proposition : "Ajouter avant/après à la galerie ?" 
6. Si oui → galerie + option partage social
```

### 10.7 — Scénario 7 : WhatsApp inbound

```
1. Client de l'artisan envoie WhatsApp à l'Agent Téléphone V2
   (ex: "Pouvez-vous me faire un devis SDB ?")
2. Meta Cloud webhook → /v1/whatsapp/webhook
3. Identification : numéro → artisan_id + client_id (si existe)
4. Supervisor → Agent Téléphone V2 (Sonnet) :
   a. Script d'annonce IA (transparence AI Act)
   b. Prise d'infos structurée
   c. Création fiche prospect
5. Notif push artisan : "Nouveau prospect WhatsApp — Mme Dupont, SDB 8m²"
6. Artisan rappelle quand dispo
```

### 10.8 — Scénario 8 : Sync hors-ligne → en ligne

```
1. Artisan sur chantier, pas de réseau
2. Crée devis → Expo SQLite local
3. Queue locale (sync queue)
4. Réseau revient → POST /v1/sync/batch → envoie tous les events en batch
5. Backend valide + écrit DB Supabase
6. Conflits résolus : last-write-wins (timestamp)
7. Ack mobile → purge queue locale
```

### 10.9 — Scénario 9 : Fiscalité alerte

```
1. Cron 1×/jour → Agent Fiscalité (Sonnet)
2. Pour chaque artisan :
   a. Calcule CA cumulé année
   b. Compare avec seuils (micro-BIC, micro-BNC, TVA franchise, etc.)
   c. Si approche seuil → alerte
3. Exemple : CA micro-BIC à 85% du seuil 176 200€
4. Notif : "Attention : tu approches le seuil micro-BIC"
5. Proposition : changement de statut EURL/SAS
6. Disclaimer : "Valide avec ton expert-comptable"
```

### 10.10 — Scénario 10 : Escalade humaine support (F136)

```
1. Artisan écrit plusieurs fois sans résolution IA
2. Supervisor détecte (3+ msgs même sujet + confiance <60%)
3. Propose escalade : "Je passe à Fabrice ?"
4. Artisan accepte → POST /v1/support/escalate
5. Création ticket Crisp avec contexte auto-rempli
6. Notif Fabrice (SMS si urgence, Slack sinon)
7. Fabrice répond Crisp → message retour dans chat STRUCTORAI
8. Artisan flag "résolu" → NPS prompt
```

---

## 11. MULTI-TENANT ET SÉCURITÉ

### 11.1 — Isolation par `organization_id`

Chaque table a une colonne `organization_id UUID NOT NULL REFERENCES organizations(id)`.

**Toutes les requêtes** passent par les RLS policies qui vérifient `auth.jwt() ->> 'org_id'`.

**Impossible d'accéder** aux données d'une autre org via API (même avec un bug code) car PostgreSQL rejette au niveau row.

### 11.2 — JWT et rotation

- Access token : 1h de validité
- Refresh token : 30 jours, rotation à chaque usage
- Révocation possible via Supabase admin API

### 11.3 — API keys pour intégrations

- Artisan plan Business peut créer des API keys pour intégrations tierces
- Hashed via `utils/keys.py` (pattern AgentShield)
- Scopes limitables (read-only, read-write)
- Révocation possible

### 11.4 — Chiffrement

- **En transit** : TLS 1.3 partout (API, DB, storage)
- **Au repos** :
  - Supabase PostgreSQL : AES-256 automatique
  - Supabase Storage : AES-256
  - MemPalace : AES-256 sur backups (voir `docs/MEMORY-STRATEGY.md`)
  - Mobile local SQLite : SQLCipher

### 11.5 — Secrets management

- Variables env sur Railway (chiffrées)
- Secrets Github Actions (chiffrés)
- Aucun secret dans le code source
- Rotation automatique des secrets critiques (Claude API, Stripe) tous les 90 jours

Voir `SECURITE.md` pour détails exhaustifs.

---

## 12. RÉSILIENCE ET FALLBACKS

### 12.1 — Circuit breakers

**Par agent** (pattern Ouroboros) :
- 3 réponses vides consécutives → pause 60s + fallback model chain
- 5 erreurs 5xx consécutives → désactivation agent 5 min + alerte Sentry

**Par service** :
- Claude API down → fallback Sonnet → Haiku → message "Cerveau en maintenance, réessaie dans 2 min"
- Supabase down → queue locale + retry 5 min + alerte Fabrice
- Whisper down → fallback Deepgram
- ElevenLabs down → fallback OpenAI TTS
- Mem0 down → MemPalace seul (voir MEMORY-STRATEGY.md)

### 12.2 — Queue et retry

**Tâches async** dans Redis (Railway) avec retry exponentiel :
- 1ère retry : 1s
- 2ème retry : 5s
- 3ème retry : 30s
- 4ème retry : 2 min
- 5ème retry : 10 min → puis dead letter queue

### 12.3 — Graceful degradation

Si un service critique tombe, l'app dégrade intelligemment :
- Pas de LLM → mode "saisie manuelle" basique (devis à remplir à la main)
- Pas de Supabase → mode offline (local SQLite)
- Pas de Whisper → mode texte seul (pas de vocal)
- Pas de réseau → queue locale + sync au retour

L'artisan n'est JAMAIS bloqué complètement.

---

## 13. PERFORMANCE — LATENCES CIBLES

### 13.1 — Tableau des cibles

| Action | Latence cible | Raisonnement |
|---|---|---|
| Réponse chat texte (question simple) | **<3s** | L'artisan attend la réponse |
| Réponse chat vocal (parole → voix) | **<1.5s** | Conversation naturelle |
| Génération devis vocal complet | **<15s** | L'artisan peut attendre 15s s'il voit le progrès |
| OCR ticket | **<5s** | Un peu lent acceptable |
| Upload photo chantier | **<3s** | Image compressée |
| Open app → dashboard visible | **<2s** | Cold start mobile |
| Migration 036 AI Act (déploiement) | **<30s** | Déploiement |
| Background consciousness tick | **<30s** | Async, pas critique |
| Sync hors-ligne batch (100 events) | **<10s** | Une fois à la reconnexion |

### 13.2 — Optimisations clés

- **Streaming TTS** : ElevenLabs commence à parler avant la fin de la génération LLM
- **Streaming chat** : réponse Claude streamée via WebSocket
- **Cache Redis** : réponses fréquentes (FAQ, métier) cachées 1h
- **Claude prompt caching** : contexte METIER.md caché (économie ~80% tokens)
- **Lazy loading mobile** : images photos chantier chargées au scroll
- **Read replica Supabase** : à partir de 50 clients pour offload reads

### 13.3 — Monitoring latences

Sentry performance monitoring + custom metrics :
- P50, P95, P99 par endpoint
- Alerte si P95 > 2× cible

---

## 14. OBSERVABILITÉ

### 14.1 — Les 4 piliers

1. **Logs structurés** (JSON) dans Railway → export Logtail/Datadog si scale
2. **Métriques** (Prometheus-compatible) exposées sur `/metrics`
3. **Traces** (OpenTelemetry) pour requêtes multi-services
4. **Alertes** (Sentry + custom) vers Slack + SMS Fabrice

### 14.2 — Métriques custom clés

| Métrique | Type | Alerte si |
|---|---|---|
| `llm_calls_total` (par agent, par modèle) | Counter | — |
| `llm_cost_usd` (par agent, par jour) | Gauge | >budget alloué |
| `llm_latency_ms` (par agent) | Histogram | P95 > 5s |
| `ai_decisions_total` (validated/invalidated) | Counter | — |
| `errors_5xx_total` (par endpoint) | Counter | >5% sur 5 min |
| `active_users` (DAU) | Gauge | — |
| `support_escalations_total` (par niveau) | Counter | — |
| `mem0_latency_ms` / `mempalace_latency_ms` | Histogram | P95 > 500ms |

### 14.3 — Dashboards

- **Fabrice ops dashboard** (`/admin/ops`) : santé système en temps réel
- **Fabrice business dashboard** (`/admin/business`) : MRR, churn, NPS
- **Sentry** : erreurs et performance
- **Mixpanel** : usage produit + funnels

---

## 15. ANTI-PATTERNS À ÉVITER

### 15.1 — Anti-patterns architecturaux

| ❌ Ne pas faire | ✅ Faire à la place |
|---|---|
| Agent appelle directement un autre agent | Passer par Supervisor via events |
| Logique métier dans les endpoints FastAPI | Toujours dans `services/` |
| Storage state dans l'agent (classe attribute) | Recall from Mem0/MemPalace/DB au début de chaque task |
| Hardcoder les prompts dans le code | Stocker dans `prompts/` (pattern Ouroboros) |
| Modifier les RLS sans test | Toujours tester en isolation (pytest) |
| Skip le log `ai_decisions` | Obligatoire sur tout appel LLM (AI Act) |
| Synchroniser à chaque message | Batch + debounce |
| Ne pas valider les inputs Pydantic | Strict validation sur chaque endpoint |
| Dépendre d'un seul LLM provider | Fallback chain toujours |
| Stocker des secrets dans le code | Variables env + rotation |
| Ignorer le circuit breaker | Respecter les 3 fails = pause |

### 15.2 — Anti-patterns mobile

- Pas de state global lourd → Zustand minimal
- Pas de re-render inutile → memo + selector pattern
- Pas de requête API à chaque keystroke → debounce 300ms
- Pas de stocker secrets en AsyncStorage → SecureStore (Keychain/Keystore)
- Pas de bloquer UI pendant sync → background queue

### 15.3 — Anti-patterns base de données

- Pas de N+1 queries → joins explicites
- Pas de SELECT * → colonnes spécifiques
- Pas de FK sans index → index systématique
- Pas de transaction sans timeout → max 30s
- Pas de migration sans dry-run → staging d'abord

### 15.4 — Anti-patterns LLM

- Pas de prompt > 100K tokens → compaction via context_compaction.py
- Pas d'appel LLM sans timeout → 30s max
- Pas d'appel LLM sans logging → ai_decisions obligatoire
- Pas de génération longue sans streaming → UX cassée
- Pas d'hallucination silencieuse → score confiance + disclaimer

---

## 16. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/API.md` (à créer) | Endpoints REST détaillés — spec OpenAPI |
| `docs/AGENTS.md` (à créer) | Détail de chaque agent (system prompt, tools, KPIs) |
| `docs/VOICE.md` (à créer) | Pipeline vocal détaillé (Whisper, ElevenLabs, WebRTC) |
| `docs/WHATSAPP.md` (à créer) | Intégration Meta Cloud API |
| `docs/DEPLOY.md` (à créer) | Guide déploiement Railway + Vercel + EAS |
| `docs/ENV.md` (à créer) | Variables d'environnement |
| `docs/MIGRATIONS.md` (à créer) | 37 migrations SQL |
| `docs/ERRORS.md` (à créer) | Codes d'erreur + StructorAIError |
| `docs/PRICING-ENGINE.md` (à créer) | Moteur de prix BTP détaillé |
| `docs/I18N.md` (à créer) | Multi-langue (6 langues) |
| `docs/GAMIFICATION.md` (à créer) | XP, niveaux, quêtes, badges |
| `docs/KNOWLEDGE.md` (à créer) | RAG BTP détaillé |
| `docs/PROMPTS.md` (à créer) | Structure prompts système des 14 agents |
| `docs/OFFLINE.md` (à créer) | Mode hors-ligne + sync |
| `docs/TESTS.md` (à créer) | Stratégie tests (unit + integration + e2e) |
| `docs/CONVENTIONS.md` (à créer) | Conventions code, naming, git |
| `docs/METIER.md` | Règles BTP (TVA, mentions, Factur-X) |
| `docs/MEMORY.md` | Architecture mémoire hybride |
| `docs/MEMORY-STRATEGY.md` | Runbook fallback mémoire |
| `docs/AI-ACT-COMPLIANCE.md` | Conformité AI Act |
| `docs/COACH-DISCLAIMER.md` | Cadre juridique Coach |
| `docs/SUPPORT-STRATEGY.md` | Architecture support |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` | Tous les chiffres |
| `docs/FEATURES_COMPLETE.md` | F001-F136 exhaustif |
| `PRODUCT_CONTEXT.md` §Architecture | Vue produit architecture |
| `BUILD_PLAN.md` | Structure repo complète (~320 fichiers) |
| `SECURITE.md` | Sécurité détaillée |
| `COUT_REEL.md` | Coûts infrastructure |

### 16.1 — Références externes

- **FastAPI** : https://fastapi.tiangolo.com/
- **Expo** : https://docs.expo.dev/
- **Supabase** : https://supabase.com/docs
- **Claude API** : https://docs.anthropic.com/
- **Mem0** : https://docs.mem0.ai/
- **Pattern Ouroboros** : https://github.com/razzant/ouroboros (architecture référence)
- **Railway** : https://docs.railway.app/
- **Vercel** : https://vercel.com/docs
- **EAS Build** : https://docs.expo.dev/build/introduction/

---

## 17. ÉVOLUTIONS V2 ET V3

### 17.1 — V2 (septembre-octobre 2026)

- **Agent Téléphone IA** (V2 add-on +15€/mois) : nouveau 14ème agent opérationnel
- **Synchro compta Pennylane/Cegid/Sage/QuickBooks** (Business only) : Agent Compta étendu avec integrations
- **Télé-dépannage** : extension Agent Téléphone
- **Portail client final** : page dédiée au client de l'artisan (suivi chantier, paiements, photos)
- **API grossistes temps réel** V2

### 17.2 — V3 (2027+)

- **Photogrammétrie multi-photos** : reconstruction 3D chantier via ViroReact/expo-three
- **API grossistes exclusives** : bloqué jusqu'à 2K MRR pour négocier en position de force

### 17.3 — Impact architecture

Aucun changement structurant prévu. L'architecture 7-couches scale jusqu'à 10K+ clients sans refonte majeure.

**Seuls ajustements prévus** :
- Séparation agents-workers en microservices si >5K clients (1 service par agent)
- Kafka/NATS au lieu de Redis pour queue si >10K events/s
- CDN pour Supabase Storage (photos, PDFs) si >500K assets

---

> **Ce fichier est la constitution architecturale de STRUCTORAI.**
> **Tout changement structurant DOIT mettre à jour ce document.**
> **Les 17 docs techniques priorité 2 s'appuient sur cette fondation.**
> **Règle d'or** : si c'est pas dans ARCH.md, c'est pas dans l'architecture. Discussion avant de bricoler.
