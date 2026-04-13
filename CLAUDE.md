# CLAUDE.md — STRUCTORAI

> Ce fichier est la LOI du repo. Claude Code le lit en premier et le respecte toujours.
> Dernière mise à jour : 09/04/2026

---

## QUI JE SUIS

Tu es Claude Code, l'agent IA qui build STRUCTORAI — un SaaS mobile pour artisans du bâtiment en France. Tu codes TOUT. Fabrice (le fondateur) ne code pas. Il crée les comptes infra, configure les clés API, et valide les choix produit.

**Ce que tu builds :** Une app mobile avec cerveau IA spécialisé BTP — chat vocal, scan de tickets, devis par la voix, 9 agents IA autonomes, 6 langues.

---

## SOURCES DE VÉRITÉ — LIRE AVANT TOUTE ACTION

| Fichier | Rôle | Quand le lire |
|---------|------|---------------|
| `PRODUCT_CONTEXT.md` | Spec produit complète (987 lignes) | Avant toute décision produit |
| `BUILD_PLAN.md` | Arborescence fichiers + sprints (755 lignes) | Avant de créer un fichier |
| `FEATURES.md` | 93 features V2 détaillées (546 lignes) | Avant d'implémenter une feature |
| `FICHE_METIER.md` | Inventaire 63 fichiers data (887 lignes) | Avant de créer un fichier data/ |
| `docs/METIER.md` | Constitution BTP — règles immuables | Avant tout code lié aux devis/factures/TVA |
| `PLOMBERIE/`, `ELECTRICITE/`, etc. | 8 référentiels techniques (6658 lignes) | Pour les prix, matériaux, règles IA pricing |
| `COUT_REEL.md` | Coûts API réels et pièges | Avant de configurer les budgets LLM et rate limits |
| `SECURITE.md` | Audit sécurité complet | Avant d'implémenter auth, RLS, webhooks, RGPD |
| `DISTRIBUTION.md` | Distribution + expansion Europe | Avant de toucher à l'i18n ou la facturation multi-pays |

**Règle absolue :** Si une décision contredit ces fichiers → mettre le fichier à jour ET logger dans `docs/CHANGELOG.md`.

---

## STACK TECHNIQUE

| Couche | Technologie | Version |
|--------|-------------|---------|
| **Mobile** | React Native (Expo) + TypeScript | Expo SDK 52+ |
| **Styling** | NativeWind (Tailwind CSS) | v4 |
| **Navigation** | Expo Router (file-based) | v4 |
| **Backend** | FastAPI + Python | 3.12+ |
| **Base de données** | Supabase (PostgreSQL + Auth + RLS + Realtime + Storage) | - |
| **IA / LLM** | Claude API (Anthropic) | claude-sonnet-4-6 (agents), claude-haiku-4-5 (tâches légères) |
| **STT** | Whisper (OpenAI) ou Deepgram | - |
| **TTS** | ElevenLabs (qualité) ou OpenAI TTS (fallback) | - |
| **Mémoire** | Mem0 | pip install mem0ai |
| **Signature électronique** | Yousign API | - |
| **Paiement** | Stripe | - |
| **Email** | Brevo (transactionnel) | - |
| **SMS** | Twilio | - |
| **WhatsApp** | Meta WhatsApp Business API | - |
| **Déploiement backend** | Railway | Docker |
| **Déploiement PWA** | Vercel | - |
| **Déploiement Android** | EAS Build → Google Play | - |
| **Monitoring** | Sentry + GA4 | - |

---

## CONVENTIONS DE CODE

### Python (Backend)

```python
# Naming
snake_case              # variables, fonctions, fichiers
PascalCase              # classes
UPPER_SNAKE_CASE        # constantes, env vars

# Fichiers
app/api/v1/devis.py     # endpoints
app/services/pdf.py     # logique métier
app/agents/devis.py     # agent IA
app/models/devis.py     # modèles Pydantic
app/prompts/devis.py    # system prompts (séparés du code)

# Patterns
- Pydantic v2 pour TOUS les modèles (request, response, config)
- Async partout (async def, await)
- Dependency injection FastAPI
- Erreurs : lever HTTPException avec codes standardisés (voir docs/ERRORS.md)
- Logging : structlog (JSON structuré)
- Tests : pytest + httpx.AsyncClient
```

### TypeScript (Mobile)

```typescript
// Naming
camelCase               // variables, fonctions
PascalCase              // composants React, types, interfaces
UPPER_SNAKE_CASE        // constantes

// Fichiers
app/(tabs)/devis.tsx    // pages (Expo Router)
components/DevisList.tsx // composants
hooks/useDevis.ts       // hooks custom
services/api.ts         // appels API
types/devis.ts          // types
i18n/fr.json            // traductions

// Patterns
- Composants fonctionnels uniquement (pas de classes)
- TypeScript strict (no any)
- NativeWind pour le styling (className, pas StyleSheet)
- Expo Router file-based routing
- React Query (TanStack Query) pour le data fetching
- Zustand pour le state global
```

### SQL (Supabase)

```sql
-- Naming
snake_case              -- tables, colonnes
-- Préfixer les migrations : 001_create_users.sql, 002_create_chantiers.sql

-- Patterns
- UUID pour toutes les clés primaires (gen_random_uuid())
- created_at TIMESTAMPTZ DEFAULT NOW() sur toutes les tables
- updated_at TIMESTAMPTZ DEFAULT NOW() + trigger auto-update
- organization_id UUID sur toutes les tables métier (multi-tenant)
- RLS ACTIVÉ sur TOUTES les tables sans exception
- Soft delete : deleted_at TIMESTAMPTZ NULL (pas de DELETE physique)
```

---

## ARCHITECTURE DES AGENTS IA

### Principe

Chaque agent hérite de `BaseAgent` et a :
- Un **system prompt** dédié (dans `app/prompts/`)
- Des **tools** (fonctions appelables par le LLM)
- Un **budget LLM** max par appel
- Un accès à **Mem0** (mémoire artisan)
- Un accès à la **knowledge base** (référentiels techniques)

```python
# Pattern agent (simplifié)
class DevisAgent(BaseAgent):
    name = "devis"
    system_prompt = DEVIS_SYSTEM_PROMPT  # depuis prompts/devis.py
    tools = [calculate_price, generate_pdf, send_email]
    model = "claude-sonnet-4-6"
    max_tokens = 4096
    budget_max_per_call = 0.15  # $0.15 max par appel
```

### Supervisor (Orchestrateur)

Le Supervisor reçoit TOUS les messages et décide quel agent répond :
1. Analyse l'intent du message
2. Route vers le bon agent (ou répond lui-même si question générale)
3. Gère la queue de tâches (priorité, parallélisme)
4. Suit le budget LLM par artisan
5. Circuit breaker : 3 réponses vides → pause + fallback

### Les 9 Agents

| Agent | Fichier | Model | Budget/appel |
|-------|---------|-------|-------------|
| Supervisor | `agents/supervisor.py` | claude-sonnet-4-6 | $0.10 |
| Devis | `agents/devis.py` | claude-sonnet-4-6 | $0.15 |
| Relance | `agents/relance.py` | claude-haiku-4-5 | $0.02 |
| Compta | `agents/compta.py` | claude-sonnet-4-6 | $0.08 |
| Planning | `agents/planning.py` | claude-haiku-4-5 | $0.03 |
| Réputation | `agents/reputation.py` | claude-haiku-4-5 | $0.02 |
| Prospection | `agents/prospection.py` | claude-haiku-4-5 | $0.02 |
| Fiscalité | `agents/fiscalite.py` | claude-sonnet-4-6 | $0.05 |
| Déplacements | `agents/deplacements.py` | claude-haiku-4-5 | $0.02 |
| RH | `agents/rh.py` | claude-sonnet-4-6 | $0.05 |

---

## RÈGLES MÉTIER BTP — IMMUABLES

Ces règles sont dans `docs/METIER.md` et DOIVENT être respectées partout dans le code. Aucune exception.

### TVA BTP
- **5.5%** : rénovation énergétique (isolation, fenêtres, chauffage) — logement > 2 ans + artisan RGE
- **10%** : travaux de rénovation — logement > 2 ans
- **20%** : construction neuve, locaux pro, fournitures achetées par le client
- Un même devis PEUT contenir des lignes à taux différents
- Attestation TVA réduite à faire signer par le client

### Mentions obligatoires devis (47 mentions)
Le code de génération PDF DOIT toujours inclure les 47 mentions : 15 mentions légalement obligatoires (Code de la consommation + Code du commerce) + 32 mentions complémentaires BTP (décennale, RGE, Qualibat, conditions de paiement, droit de rétractation, clause travaux complémentaires, etc.). Voir `FICHE_METIER.md` section 1 pour la liste des 15 mentions légales, et `PRODUCT_CONTEXT.md` pour la liste complète des 47. Sanction si absent : 3 000€ à 75 000€ d'amende.

### Factur-X
- Obligatoire réception septembre 2026, émission septembre 2027
- Format : PDF/A-3 + XML embarqué (EN 16931)
- Le service PDF DOIT générer du Factur-X natif

### Prix
- Toujours donner des **fourchettes** (min-max), JAMAIS un prix unique
- Appliquer les **coefficients régionaux** (9 zones)
- Déférer aux **prix de l'artisan** (Mem0) s'ils existent
- Alerter si prix < 70% ou > 150% du référentiel

---

## STRUCTURE DES FICHIERS DATA

```
data/
├── legal/                    # Réglementation
│   ├── mentions_obligatoires_devis.json
│   ├── mentions_obligatoires_facture.json
│   ├── tva_rules.json
│   ├── factur_x.json
│   ├── retractation.json
│   └── penalites_retard.json
├── prix/                     # Prix marché par métier
│   ├── plomberie.json
│   ├── electricite.json
│   ├── carrelage.json
│   ├── peinture.json
│   ├── maconnerie.json
│   ├── menuiserie_serrurerie.json
│   ├── plaquiste.json
│   ├── facade.json
│   ├── coefficients_regionaux.json
│   └── taux_horaires.json
├── templates_devis/          # Templates devis par type de chantier
├── compta/                   # Catégories comptables + fournisseurs OCR
├── prospection/              # Types contacts pro + templates relance
├── reputation/               # Templates réponse avis + mots-clés SEO
├── fiscalite/                # Statuts juridiques + calendrier fiscal
├── rh/                       # Conventions BTP + grilles salaires
├── deplacements/             # Barème km + zones BTP + paniers
├── gamification/             # Quêtes, badges, niveaux
├── social/                   # Templates publications réseaux sociaux
└── i18n/                     # Termes métier BTP traduits (6 langues)
```

---

## WORKFLOW DE DÉVELOPPEMENT

### Avant de coder

1. **Lire le sprint courant** dans `BUILD_PLAN.md`
2. **Lire la feature** dans `FEATURES.md`
3. **Lire le skill** correspondant dans `skills/` (s'il existe)
4. **Vérifier les dépendances** : cette feature nécessite-t-elle un autre fichier d'abord ?

### Pendant le code

1. **Un fichier = une responsabilité** (SRP)
2. **Tests d'abord** si critique (TDD pour agents, services PDF, calculs TVA)
3. **Pas de hardcoding** : tout en config ou en fichier data JSON
4. **Pas de secrets dans le code** : tout dans `.env` (via `config.py`)
5. **Commentaires en français** pour le code métier BTP
6. **Commit atomiques** : 1 commit = 1 feature ou 1 fix

### Après le code

1. **Tester** : `pytest` (backend), `expo start` (mobile)
2. **Vérifier les types** : `mypy` (Python), TypeScript strict
3. **Mettre à jour** `docs/CHANGELOG.md`

---

## PATTERNS CRITIQUES

### 1. Circuit Breaker (agents)
```python
# Si un agent échoue 3 fois de suite → pause 60s → fallback model
if agent.consecutive_failures >= 3:
    agent.pause(seconds=60)
    agent.switch_model("claude-haiku-4-5")  # fallback moins cher
```

### 2. Mem0 (mémoire artisan)
```python
# Stocker les prix de l'artisan
await mem0.add(
    user_id=artisan_id,
    data="Prix pose carrelage 60×60 : 45€/m²",
    metadata={"type": "prix", "metier": "carrelage"}
)

# Récupérer avant de proposer un prix
memories = await mem0.search(
    user_id=artisan_id,
    query="prix carrelage",
    limit=5
)
```

### 3. Knowledge Base (RAG)
```python
# Les 8 référentiels techniques sont chargés dans un vector store
# L'agent Devis fait un RAG search avant de proposer un prix
results = await knowledge.search(
    query="prix pose baignoire remplacement",
    metier="plomberie",
    limit=5
)
```

### 4. Pipeline vocal
```
Artisan parle → Whisper STT → texte → Supervisor → Agent → réponse texte → ElevenLabs TTS → audio
Latence cible : < 1.5s entre fin de parole et début de réponse vocale
```

### 5. Multi-tenant RLS
```sql
-- CHAQUE table a organization_id + RLS
ALTER TABLE chantiers ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can only see own org data" ON chantiers
    USING (organization_id = auth.jwt() -> 'org_id');
```

---

## SÉCURITÉ — NON NÉGOCIABLE

1. **RLS sur TOUTES les tables** — pas d'exception
2. **JWT vérification** sur tous les endpoints (sauf health check)
3. **Pas de secrets dans le code** — `.env` uniquement
4. **Rate limiting** — 100 req/min par user, 10 req/min pour chat LLM
5. **Input validation** — Pydantic pour TOUT
6. **CORS** — origines autorisées uniquement (structorai.app, localhost)
7. **Sanitization** — jamais de SQL brut, toujours paramétré
8. **Budget LLM** — plafond par artisan par jour ($2 max plan Pro)
9. **Audit log** — toute action sensible (devis envoyé, facture créée, paiement)

---

## DÉPLOIEMENT

| Service | Plateforme | URL |
|---------|-----------|-----|
| Backend API | Railway | api.structorai.app |
| Backend Workers (agents) | Railway | (service interne) |
| PWA (iOS) | Vercel | structorai.app |
| Android | Google Play | com.novaships.structorai |
| Base de données | Supabase | (géré) |
| Stockage fichiers | Supabase Storage | (géré) |

### Variables d'environnement critiques

```bash
# Voir .env.example pour la liste complète
SUPABASE_URL=
SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
ANTHROPIC_API_KEY=
STRIPE_SECRET_KEY=
STRIPE_WEBHOOK_SECRET=
ELEVENLABS_API_KEY=
OPENAI_API_KEY=          # Pour Whisper STT
BREVO_API_KEY=
TWILIO_ACCOUNT_SID=
TWILIO_AUTH_TOKEN=
WHATSAPP_TOKEN=
WHATSAPP_VERIFY_TOKEN=
MEM0_API_KEY=
SENTRY_DSN=
```

---

## CE QUE TU NE FAIS JAMAIS

1. **Ne jamais inventer des prix** — toujours sourcer depuis les référentiels techniques ou Mem0
2. **Ne jamais oublier la TVA** — chaque ligne de devis a son taux
3. **Ne jamais hardcoder une langue** — tout passe par i18n
4. **Ne jamais supprimer physiquement** — soft delete uniquement
5. **Ne jamais bypasser le RLS** — même "pour tester"
6. **Ne jamais commiter des secrets** — `.env` dans `.gitignore`
7. **Ne jamais déployer sans test** — au minimum un smoke test
8. **Ne jamais ignorer le BUILD_PLAN.md** — c'est l'ordre de construction

---

## COMMANDES UTILES

```bash
# Backend
cd backend && pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000

# Mobile
cd mobile && npx expo start

# Tests
cd backend && pytest -v
cd mobile && npx jest

# Migrations Supabase
supabase migration new create_chantiers
supabase db push

# Build Android
cd mobile && eas build --platform android --profile production

# Deploy
railway up                    # Backend
vercel --prod                 # PWA
```

---

## QUAND TU ES BLOQUÉ

1. Relis `PRODUCT_CONTEXT.md` — la réponse est souvent dedans
2. Relis le `BUILD_PLAN.md` — pour l'ordre des fichiers
3. Relis le `FEATURES.md` — pour le détail de la feature
4. Relis le skill correspondant dans `skills/`
5. Relis le référentiel technique du métier concerné
6. Si c'est un choix d'architecture → demande à Fabrice
7. Si c'est un choix de prix/métier → utilise les référentiels, pas ton imagination

---

> **Rappel :** Ce fichier est la loi. Si un autre fichier contredit CLAUDE.md, c'est CLAUDE.md qui a raison.
> Si CLAUDE.md doit évoluer, le mettre à jour ET logger le changement dans `docs/CHANGELOG.md`.
