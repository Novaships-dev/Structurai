---
name: DEPLOY
description: Guide déploiement complet de STRUCTORAI — Railway (backend-api + agents-workers + MemPalace + Redis + cron-jobs), Vercel (PWA iOS + site marketing), EAS Build (APK/AAB Google Play Store), GitHub Actions CI/CD (backend-ci.yml + mobile-ci.yml + deploy.yml), Dockerfiles (backend + agents), domaines (structorai.app + structorai.io + api.structorai.app), DNS Porkbun, SSL Let's Encrypt, environnements staging vs production, rollback procedures, blue-green deployment, smoke tests post-deploy, monitoring.
type: technical-deployment-runbook
scope: infrastructure, ci-cd, mobile-build, domains, rollback
priority: critical
date: 2026-04-18
version: 1.0
targets: [Railway EU, Vercel EU, EAS Build, Google Play Console]
staging_url: https://api-staging.structorai.app
production_url: https://api.structorai.app
mobile_app_url: https://app.structorai.app
marketing_url: https://structorai.app
---

# docs/DEPLOY.md — Guide de déploiement STRUCTORAI

> **Ce fichier est le runbook de déploiement.**
> Tout déploiement production DOIT suivre ces étapes.
> Le rollback est toujours possible en <5 min (voir §12).
> Reference architecture : `docs/ARCH.md` §9 Infrastructure.

---

## 0. SOMMAIRE

1. [Vue d'ensemble des environnements](#1-vue-densemble-des-environnements)
2. [Domaines et DNS](#2-domaines-et-dns)
3. [Services Railway (backend + agents + MemPalace + Redis + cron)](#3-services-railway-backend--agents--mempalace--redis--cron)
4. [Vercel (PWA iOS + site marketing)](#4-vercel-pwa-ios--site-marketing)
5. [EAS Build (Android Google Play)](#5-eas-build-android-google-play)
6. [Dockerfiles](#6-dockerfiles)
7. [GitHub Actions CI/CD](#7-github-actions-cicd)
8. [Variables d'environnement](#8-variables-denvironnement)
9. [Migrations Supabase](#9-migrations-supabase)
10. [Pre-deploy checklist](#10-pre-deploy-checklist)
11. [Deploy production (step by step)](#11-deploy-production-step-by-step)
12. [Rollback procedures](#12-rollback-procedures)
13. [Smoke tests post-deploy](#13-smoke-tests-post-deploy)
14. [Blue-green deployment (futur)](#14-blue-green-deployment-futur)
15. [Monitoring et alertes](#15-monitoring-et-alertes)
16. [Deploy mobile Android détaillé](#16-deploy-mobile-android-détaillé)
17. [Deploy PWA iOS détaillé](#17-deploy-pwa-ios-détaillé)
18. [Staging workflow](#18-staging-workflow)
19. [Gestion des secrets](#19-gestion-des-secrets)
20. [Plan de continuité (DR)](#20-plan-de-continuité-dr)
21. [Coûts d'infrastructure](#21-coûts-dinfrastructure)
22. [Troubleshooting deploy](#22-troubleshooting-deploy)
23. [Références croisées](#23-références-croisées)

---

## 1. VUE D'ENSEMBLE DES ENVIRONNEMENTS

### 1.1 — Les 3 environnements

| Env | Domaine API | Domaine App | Domaine Web | Usage |
|---|---|---|---|---|
| **Local (dev)** | `http://localhost:8000` | `http://localhost:19006` | `http://localhost:3000` | Fabrice développement |
| **Staging** | `https://api-staging.structorai.app` | `https://app-staging.structorai.app` | `https://staging.structorai.app` | Tests pré-prod, beta testeurs |
| **Production** | `https://api.structorai.app` | `https://app.structorai.app` | `https://structorai.app` | Clients finaux |

### 1.2 — Projets Railway distincts

- `structorai-staging` — staging
- `structorai-production` — production

Chaque projet contient ses 5 services (backend-api, agents-workers, mempalace, redis, cron-jobs).

### 1.3 — Projets Supabase distincts

- `structorai-staging` — staging (région Frankfurt EU)
- `structorai-production` — production (région Frankfurt EU)

**Jamais de prod data en staging** (respect RGPD).

### 1.4 — Projets Vercel

- `structorai-app-staging` — PWA staging
- `structorai-app` — PWA production
- `structorai-web-staging` — site marketing staging
- `structorai-web` — site marketing production

### 1.5 — Stripe

- Mode **test** en staging (produits test, cartes 4242...)
- Mode **live** en production

---

## 2. DOMAINES ET DNS

### 2.1 — Domaines à posséder

Via **Porkbun** (fournisseur FR low-cost, bon support DNS) :
- `structorai.app` (principal, marketing + app)
- `structorai.io` (backup + redirect)

**Coût** : ~15€/an par domaine = 30€/an total.

### 2.2 — Records DNS production

| Type | Host | Target | TTL | Service |
|---|---|---|---|---|
| A | `structorai.app` | `76.76.21.21` | 300 | Vercel (site marketing) |
| CNAME | `app.structorai.app` | `cname.vercel-dns.com` | 300 | Vercel (PWA iOS) |
| CNAME | `api.structorai.app` | `structorai-production.up.railway.app` | 300 | Railway (backend API) |
| CNAME | `client.structorai.app` | `cname.vercel-dns.com` | 300 | Vercel (portail client V2 F127) |
| MX | `structorai.app` | `10 in1-smtp.messagingengine.com` | 3600 | Fastmail (email pro) |
| MX | `structorai.app` | `20 in2-smtp.messagingengine.com` | 3600 | Fastmail |
| TXT | `structorai.app` | `v=spf1 include:sendinblue.com ~all` | 3600 | SPF Brevo |
| TXT | `mail._domainkey.structorai.app` | `v=DKIM1; ...` | 3600 | DKIM Brevo |
| TXT | `_dmarc.structorai.app` | `v=DMARC1; p=quarantine; ...` | 3600 | DMARC |

### 2.3 — Records DNS staging

| Type | Host | Target |
|---|---|---|
| CNAME | `staging.structorai.app` | `cname.vercel-dns.com` |
| CNAME | `app-staging.structorai.app` | `cname.vercel-dns.com` |
| CNAME | `api-staging.structorai.app` | `structorai-staging.up.railway.app` |

### 2.4 — SSL / TLS

- **Vercel** : SSL auto (Let's Encrypt, renouvelé automatiquement)
- **Railway** : SSL auto sur `*.up.railway.app`, config custom domain activée
- **TLS 1.3** minimum partout
- HSTS activé (1 an)

### 2.5 — Redirect structorai.io → structorai.app

Porkbun → Redirect permanent (301) : `structorai.io/*` → `https://structorai.app/$1`

---

## 3. SERVICES RAILWAY (BACKEND + AGENTS + MEMPALACE + REDIS + CRON)

### 3.1 — Les 5 services Railway

```
Project: structorai-production
│
├── backend-api              # FastAPI Gateway (service principal)
│   ├── Source: GitHub repo, branch "main", dossier "backend/"
│   ├── Dockerfile: backend/Dockerfile
│   ├── Start command: (défini dans Dockerfile)
│   ├── Port: 8000
│   ├── Public domain: api.structorai.app
│   └── Ressources: 1 CPU, 1 GB RAM (scale)
│
├── agents-workers           # Workers agents IA (Supervisor + 14 agents)
│   ├── Source: GitHub repo, branch "main", dossier "backend/"
│   ├── Dockerfile: backend/Dockerfile.agents
│   ├── Start command: python -m app.workers.main
│   ├── Ressources: 1 CPU, 2 GB RAM (scale agents)
│   └── Pas de domaine public (service interne)
│
├── mempalace                # MemPalace (ChromaDB + SQLite self-hosted)
│   ├── Source: Docker image "mempalace/mempalace:latest"
│   ├── Volume persistent: /data (ChromaDB + SQLite)
│   ├── Ressources: 1 CPU, 2 GB RAM
│   └── Internal URL: mempalace.railway.internal:8001
│
├── redis                    # Queue Redis (Supervisor queue + cache)
│   ├── Source: Railway Redis template
│   ├── Port: 6379
│   ├── Persistence: AOF activé
│   └── Internal URL: redis.railway.internal:6379
│
└── cron-jobs                # Background Consciousness + jobs schedulés
    ├── Source: GitHub repo, branch "main", dossier "backend/"
    ├── Dockerfile: backend/Dockerfile (même que backend-api)
    ├── Start command: python -m app.cron.main
    └── Ressources: 0.5 CPU, 1 GB RAM
```

### 3.2 — Réseau interne Railway

Les services communiquent via `*.railway.internal` (pas d'exposition publique) :
- `backend-api` → `redis.railway.internal:6379`
- `backend-api` → `mempalace.railway.internal:8001`
- `agents-workers` → `redis.railway.internal:6379`
- `agents-workers` → `mempalace.railway.internal:8001`
- `cron-jobs` → `redis.railway.internal:6379`

### 3.3 — Region

**EU-West (Amsterdam)** — respect souveraineté données (voir `docs/MEMORY.md`).

### 3.4 — Scaling strategy

**<100 clients** (phase launch) :
- backend-api : 1 CPU / 1 GB / 1 instance
- agents-workers : 1 CPU / 2 GB / 1 instance
- mempalace : 1 CPU / 2 GB / 1 instance
- redis : 256 MB / 1 instance
- cron-jobs : 0.5 CPU / 1 GB / 1 instance

**100-500 clients** :
- backend-api : 2 CPU / 2 GB / 2 instances (load balancer Railway)
- agents-workers : 2 CPU / 4 GB / 2 instances
- mempalace : 2 CPU / 4 GB / 1 instance
- redis : 512 MB / 1 instance

**500-1K clients** :
- backend-api : 4 CPU / 4 GB / 3 instances
- agents-workers : 4 CPU / 8 GB / 3 instances
- Séparation par agent si >5K clients (voir `docs/ARCH.md` §17)

### 3.5 — Health checks

**backend-api** :
- Endpoint : `GET /health`
- Interval : 30s
- Timeout : 5s
- Failure threshold : 3 consecutive → restart instance

**agents-workers** :
- Endpoint interne : `GET :8081/health-worker`
- Interval : 60s

---

## 4. VERCEL (PWA IOS + SITE MARKETING)

### 4.1 — Projets Vercel

**Projet 1 : `structorai-app`** (PWA iOS)
- Source : GitHub repo, dossier `mobile/`
- Build : `npx expo export:web`
- Output : `mobile/web-build/`
- Domain : `app.structorai.app`

**Projet 2 : `structorai-web`** (site marketing + landing)
- Source : GitHub repo, dossier `marketing/`
- Build : `next build`
- Framework : Next.js
- Domain : `structorai.app`

### 4.2 — Region

**CDG1 (Paris)** puis réplication globale via Vercel Edge Network. Bonus pour SEO FR.

### 4.3 — Preview branches

Chaque PR génère un déploiement preview automatique :
- URL : `https://structorai-app-git-[branch-name].vercel.app`
- Utile pour tester visuellement avant merge

### 4.4 — Config `vercel.json`

**PWA app** :
```json
{
  "buildCommand": "cd mobile && npx expo export:web",
  "outputDirectory": "mobile/web-build",
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {"key": "X-Frame-Options", "value": "DENY"},
        {"key": "Strict-Transport-Security", "value": "max-age=31536000; includeSubDomains"},
        {"key": "Referrer-Policy", "value": "strict-origin-when-cross-origin"},
        {"key": "Permissions-Policy", "value": "camera=(self), microphone=(self)"}
      ]
    }
  ],
  "rewrites": [
    {"source": "/(.*)", "destination": "/index.html"}
  ]
}
```

### 4.5 — Service Worker PWA

Expo Web génère automatiquement un service worker pour :
- Cache assets statiques
- Offline fallback (page "hors ligne")
- Push notifications web (limitées iOS)

---

## 5. EAS BUILD (ANDROID GOOGLE PLAY)

### 5.1 — Setup EAS

1. `npm install -g eas-cli`
2. `cd mobile && eas login`
3. `eas build:configure` → génère `eas.json`

### 5.2 — Config `eas.json`

```json
{
  "cli": {
    "version": ">= 5.0.0"
  },
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal",
      "android": {
        "buildType": "apk"
      }
    },
    "preview": {
      "distribution": "internal",
      "android": {
        "buildType": "apk"
      },
      "env": {
        "EXPO_PUBLIC_API_URL": "https://api-staging.structorai.app"
      }
    },
    "production": {
      "android": {
        "buildType": "app-bundle"
      },
      "autoIncrement": "versionCode",
      "env": {
        "EXPO_PUBLIC_API_URL": "https://api.structorai.app"
      }
    }
  },
  "submit": {
    "production": {
      "android": {
        "serviceAccountKeyPath": "./secrets/play-service-account.json",
        "track": "production",
        "releaseStatus": "completed"
      }
    }
  }
}
```

### 5.3 — Config `app.json` (Expo)

```json
{
  "expo": {
    "name": "STRUCTORAI",
    "slug": "structorai",
    "version": "1.0.0",
    "orientation": "portrait",
    "icon": "./assets/icon.png",
    "splash": {
      "image": "./assets/splash.png",
      "resizeMode": "cover",
      "backgroundColor": "#0A1628"
    },
    "ios": {
      "bundleIdentifier": "com.structorai.app",
      "supportsTablet": false
    },
    "android": {
      "package": "com.structorai.app",
      "versionCode": 1,
      "permissions": [
        "RECORD_AUDIO",
        "CAMERA",
        "ACCESS_FINE_LOCATION",
        "ACCESS_COARSE_LOCATION",
        "READ_MEDIA_IMAGES",
        "INTERNET"
      ],
      "adaptiveIcon": {
        "foregroundImage": "./assets/adaptive-icon.png",
        "backgroundColor": "#0A1628"
      }
    },
    "plugins": [
      "expo-camera",
      "expo-audio",
      "expo-location",
      "expo-notifications",
      "expo-secure-store",
      "expo-sqlite"
    ],
    "extra": {
      "eas": {
        "projectId": "xxx-xxx-xxx"
      }
    }
  }
}
```

### 5.4 — Build Android production

```bash
# Build AAB pour Google Play
cd mobile
eas build --platform android --profile production

# Submit automatique après build
eas submit --platform android --profile production
```

### 5.5 — Google Play Console

**Compte développeur** : 25$ one-time (voir `COUT_REEL.md`).

**Tracks de distribution** :
- **Internal testing** : beta testeurs (<100 users, instant)
- **Closed testing** : alpha + beta groupes (jours d'attente variables)
- **Open testing** : testing public
- **Production** : publication (1-7 jours review)

**STRUCTORAI strategy** :
- Sprint 8 : upload en **internal testing** (5 beta artisans)
- Pre-launch : passage en **closed testing** (50-100 testeurs)
- Launch : **production** (review 1-7j)

### 5.6 — Signing

**Google Play App Signing** activé → Google gère les clés.

Upload key (générée par EAS) stockée dans `secrets/` et dans GitHub Secrets (pour CI auto).

### 5.7 — Updates OTA (over-the-air)

**EAS Update** permet push de JS bundles sans passer par le store :
- Changements JS/assets déployés en secondes
- Changements natifs nécessitent nouveau build

```bash
eas update --branch production --message "Fix typo dashboard"
```

---

## 6. DOCKERFILES

### 6.1 — `backend/Dockerfile` (API Gateway)

```dockerfile
FROM python:3.12-slim AS builder

WORKDIR /app

# Installation des dépendances système
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# Installation des dépendances Python
COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

# Stage final
FROM python:3.12-slim

WORKDIR /app

# Copie des dépendances depuis builder
COPY --from=builder /root/.local /root/.local
ENV PATH=/root/.local/bin:$PATH

# Copie du code
COPY . .

# Healthcheck
HEALTHCHECK --interval=30s --timeout=5s --start-period=60s --retries=3 \
    CMD curl -f http://localhost:8000/health || exit 1

# Port
EXPOSE 8000

# Démarrage
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000", "--workers", "2"]
```

### 6.2 — `backend/Dockerfile.agents` (Workers agents)

```dockerfile
FROM python:3.12-slim AS builder

WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential libpq-dev && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

FROM python:3.12-slim
WORKDIR /app
COPY --from=builder /root/.local /root/.local
ENV PATH=/root/.local/bin:$PATH
COPY . .

# Workers n'exposent pas de port public, juste un health endpoint interne
EXPOSE 8081

HEALTHCHECK --interval=60s --timeout=5s --start-period=60s --retries=3 \
    CMD curl -f http://localhost:8081/health-worker || exit 1

CMD ["python", "-m", "app.workers.main"]
```

### 6.3 — `backend/.dockerignore`

```
__pycache__
*.pyc
*.pyo
.env
.env.*
.venv
tests/
.pytest_cache/
.mypy_cache/
.git/
.github/
docs/
*.md
```

### 6.4 — Image size optimizations

- Multi-stage build (stage builder + stage runtime)
- `python:3.12-slim` au lieu de full
- `.dockerignore` strict
- Taille cible : <300 MB

---

## 7. GITHUB ACTIONS CI/CD

### 7.1 — Trois workflows

**`.github/workflows/backend-ci.yml`** — Tests sur chaque PR backend

```yaml
name: Backend CI
on:
  pull_request:
    paths:
      - 'backend/**'
      - '.github/workflows/backend-ci.yml'

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: test
        ports:
          - 5432:5432
      redis:
        image: redis:7
        ports:
          - 6379:6379
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - name: Install deps
        working-directory: backend
        run: pip install -r requirements.txt
      - name: Lint ruff
        working-directory: backend
        run: ruff check .
      - name: Type check mypy
        working-directory: backend
        run: mypy app/
      - name: Tests pytest
        working-directory: backend
        env:
          DATABASE_URL: postgresql://postgres:test@localhost:5432/test
          REDIS_URL: redis://localhost:6379
        run: pytest --cov=app --cov-report=term-missing --cov-fail-under=70
```

**`.github/workflows/mobile-ci.yml`** — Tests sur chaque PR mobile

```yaml
name: Mobile CI
on:
  pull_request:
    paths:
      - 'mobile/**'
      - '.github/workflows/mobile-ci.yml'

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - name: Install deps
        working-directory: mobile
        run: npm ci
      - name: Lint eslint
        working-directory: mobile
        run: npm run lint
      - name: Type check tsc
        working-directory: mobile
        run: npm run typecheck
      - name: Tests jest
        working-directory: mobile
        run: npm run test -- --coverage --coverageThreshold='{"global":{"lines":60}}'
```

**`.github/workflows/deploy.yml`** — Deploy automatique sur merge `main`

```yaml
name: Deploy Production
on:
  push:
    branches: [main]

jobs:
  # 1. Tests finaux avant deploy
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run all tests
        run: # ... (reprise backend-ci + mobile-ci)
  
  # 2. Deploy backend Railway
  deploy-backend:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy Railway backend-api
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}
        run: |
          npm install -g @railway/cli
          railway up --service backend-api --environment production
      - name: Deploy Railway agents-workers
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}
        run: railway up --service agents-workers --environment production
      - name: Deploy Railway cron-jobs
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}
        run: railway up --service cron-jobs --environment production
  
  # 3. Apply Supabase migrations
  migrations:
    needs: deploy-backend
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Apply migrations
        env:
          SUPABASE_ACCESS_TOKEN: ${{ secrets.SUPABASE_ACCESS_TOKEN }}
          SUPABASE_PROJECT_REF: ${{ secrets.SUPABASE_PROJECT_REF_PROD }}
        run: |
          npm install -g supabase
          supabase link --project-ref $SUPABASE_PROJECT_REF
          supabase db push
  
  # 4. Deploy PWA Vercel (auto via Vercel GitHub integration, pas de step explicit ici)
  # Vercel écoute les push sur main et déploie automatiquement
  
  # 5. EAS Update (OTA si pas de changement natif)
  mobile-update:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - name: Install EAS CLI
        run: npm install -g eas-cli
      - name: EAS Update
        working-directory: mobile
        env:
          EXPO_TOKEN: ${{ secrets.EXPO_TOKEN }}
        run: eas update --branch production --message "${{ github.event.head_commit.message }}" --non-interactive
  
  # 6. Smoke tests post-deploy
  smoke-tests:
    needs: [deploy-backend, migrations]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Wait for services ready
        run: sleep 60
      - name: Run smoke tests
        env:
          API_URL: https://api.structorai.app
        run: |
          # Test /health
          curl -f $API_URL/health || exit 1
          # Test signup → login
          bash scripts/smoke_test_prod.sh
  
  # 7. Notification Slack + rollback si smoke fail
  notify:
    needs: [smoke-tests]
    if: always()
    runs-on: ubuntu-latest
    steps:
      - name: Notify Slack
        uses: slackapi/slack-github-action@v1
        with:
          channel-id: 'deploys'
          slack-message: |
            Deploy status: ${{ job.status }}
            Commit: ${{ github.event.head_commit.message }}
        env:
          SLACK_BOT_TOKEN: ${{ secrets.SLACK_BOT_TOKEN }}
```

### 7.2 — Secrets GitHub Actions

À configurer dans Settings → Secrets → Actions :
- `RAILWAY_TOKEN`
- `SUPABASE_ACCESS_TOKEN`
- `SUPABASE_PROJECT_REF_PROD`
- `SUPABASE_PROJECT_REF_STAGING`
- `EXPO_TOKEN`
- `VERCEL_TOKEN`
- `SLACK_BOT_TOKEN`
- `SENTRY_AUTH_TOKEN`

### 7.3 — Branch strategy

- `main` — production (auto-deploy)
- `staging` — staging (auto-deploy vers staging)
- `feature/*` — branches de dev (preview Vercel uniquement)

**Règle** : pas de merge direct dans `main`. PR `feature/xxx` → `staging` → tests beta → PR `staging` → `main`.

---

## 8. VARIABLES D'ENVIRONNEMENT

Voir `docs/ENV.md` (à créer) pour la liste complète.

### 8.1 — Gestion par environnement

**Local** : `.env` (gitignored) dans `backend/` et `mobile/`.

**Staging** : Railway Environment Variables (dashboard) pour chaque service.

**Production** : Railway Environment Variables pour chaque service + secrets GitHub Actions pour CI.

### 8.2 — Variables critiques production

Voir extrait `BUILD_PLAN.md` lignes 561-604. Grand résumé :

```bash
# App
APP_ENV=production
APP_URL=https://api.structorai.app
FRONTEND_URL=https://app.structorai.app

# Supabase
SUPABASE_URL=https://xxx.supabase.co
SUPABASE_ANON_KEY=eyJ...
SUPABASE_SERVICE_ROLE_KEY=eyJ...
SUPABASE_JWT_SECRET=xxx

# Redis (interne Railway)
REDIS_URL=redis://default:xxx@redis.railway.internal:6379

# MemPalace (interne Railway)
MEMPALACE_URL=http://mempalace.railway.internal:8001

# Claude / Anthropic
ANTHROPIC_API_KEY=sk-ant-xxx
ANTHROPIC_MODEL_OPUS=claude-opus-4-7
ANTHROPIC_MODEL_SONNET=claude-sonnet-4-6
ANTHROPIC_MODEL_HAIKU=claude-haiku-4-5-20251001

# Mem0
MEM0_API_KEY=xxx

# Voice
OPENAI_API_KEY=xxx
DEEPGRAM_API_KEY=xxx
ELEVENLABS_API_KEY=xxx
ELEVENLABS_VOICE_MALE_PRO=pNInz6obpgDQGcFmaJgB
ELEVENLABS_VOICE_FEMALE_PRO=EXAVITQu4vr4xnSDxMaL

# WhatsApp
WHATSAPP_PHONE_NUMBER_ID=xxx
WHATSAPP_ACCESS_TOKEN=xxx
WHATSAPP_VERIFY_TOKEN=xxx
WHATSAPP_APP_SECRET=xxx

# Stripe
STRIPE_SECRET_KEY=sk_live_xxx
STRIPE_WEBHOOK_SECRET=whsec_xxx

# SMS / Twilio
TWILIO_ACCOUNT_SID=xxx
TWILIO_AUTH_TOKEN=xxx
TWILIO_PHONE_NUMBER=+33xxx

# Email / Brevo
BREVO_API_KEY=xxx

# Signature / Yousign
YOUSIGN_API_KEY=xxx

# Crisp (support F136)
CRISP_WEBSITE_ID=xxx

# Monitoring
SENTRY_DSN=https://xxx@sentry.io/xxx
GA4_MEASUREMENT_ID=G-xxx

# Factur-X
FACTURX_PA_PROVIDER=factpulse       # ou b2brouter
FACTURX_PA_API_KEY=xxx
```

### 8.3 — Règle de rotation

Secrets critiques rotés **tous les 90 jours** :
- Stripe secret key
- Anthropic API key
- Supabase service role key
- Meta WhatsApp access token

Script de rotation automatisé dans `scripts/rotate_secrets.sh`.

---

## 9. MIGRATIONS SUPABASE

### 9.1 — Outil

Supabase CLI (`supabase` npm package).

### 9.2 — Workflow

```bash
# Dev local
cd backend
supabase start                                    # démarre Supabase local (Docker)
supabase migration new add_users_table           # crée fichier migration
# ... édition du fichier SQL
supabase db reset                                 # applique migrations en local
pytest                                            # tests

# Commit + PR
git add supabase/migrations/
git commit -m "Add users table migration"
# ... PR, review

# Apply en staging
supabase link --project-ref $STAGING_PROJECT_REF
supabase db push                                  # applique en staging

# Apply en production (via CI/CD GitHub Actions, cf §7.1 deploy.yml)
supabase link --project-ref $PROD_PROJECT_REF
supabase db push
```

### 9.3 — Règles migration

1. **Jamais modifier une migration déjà appliquée en prod** → créer nouvelle migration
2. **Toujours tester rollback** localement avant push
3. **Migrations idempotentes** quand possible (`IF NOT EXISTS`, `IF EXISTS`)
4. **Transactions explicites** sur migrations complexes
5. **Pas de DROP COLUMN** sans preuve qu'aucun code prod ne l'utilise (risque downtime)

### 9.4 — Rollback migration

Chaque migration a son pendant `_down.sql` optionnel pour rollback manuel :

```sql
-- supabase/migrations/20260418120000_add_users_whatsapp.sql
ALTER TABLE users ADD COLUMN whatsapp_phone TEXT;

-- supabase/migrations/20260418120000_add_users_whatsapp_down.sql
ALTER TABLE users DROP COLUMN whatsapp_phone;
```

### 9.5 — Liste des 37 migrations

Voir `docs/MIGRATIONS.md` (à créer) pour détails.

---

## 10. PRE-DEPLOY CHECKLIST

Avant tout deploy production :

### 10.1 — Code

- [ ] CI GitHub Actions vert (backend-ci + mobile-ci)
- [ ] Code review approuvée (au moins 1 reviewer si équipe >1)
- [ ] Tests coverage >70% backend, >60% mobile
- [ ] Aucune `TODO` / `FIXME` critique
- [ ] Pas de `console.log`, `print()` oubliés
- [ ] Pas de secrets hardcodés (scan `detect-secrets`)

### 10.2 — Migrations

- [ ] Migrations testées en local avec `supabase db reset`
- [ ] Rollback testé
- [ ] Staging déployé et validé
- [ ] Aucune migration destructive sans backup préalable

### 10.3 — Env vars

- [ ] Toutes les env vars sont configurées sur Railway production
- [ ] Secrets GitHub Actions à jour
- [ ] `.env.example` à jour dans le repo

### 10.4 — Communication

- [ ] Changelog mis à jour (`docs/CHANGELOG.md`)
- [ ] Décision loguée (`docs/DECISIONS-LOG.md`) si changement structurant
- [ ] Slack notif prévue (deploy en cours)

### 10.5 — Backup

- [ ] Backup Supabase production fait dans les 24h
- [ ] MemPalace backup fait (voir `docs/MEMORY-STRATEGY.md` §7)

### 10.6 — Monitoring

- [ ] Sentry prêt à recevoir les nouvelles erreurs
- [ ] UptimeRobot / Better Uptime configurés
- [ ] Alertes Slack actives

---

## 11. DEPLOY PRODUCTION (STEP BY STEP)

### 11.1 — Étapes séquentielles

```
ÉTAPE 1 — Préparation
  ├─ Vérifier pre-deploy checklist (§10)
  ├─ Annoncer deploy sur Slack #deploys
  └─ Noter l'heure de début
       │
       ▼
ÉTAPE 2 — Merge PR feature → staging
  ├─ PR staging validée, merge
  ├─ GitHub Actions deploy staging
  └─ Smoke tests staging OK
       │
       ▼
ÉTAPE 3 — Validation staging (30 min minimum)
  ├─ Tests manuels critiques
  ├─ Vérification Sentry staging (0 nouvelle erreur)
  └─ Si OK → passer à étape 4
       │
       ▼
ÉTAPE 4 — Merge staging → main
  ├─ Création PR staging → main
  ├─ Review finale
  ├─ Merge
  └─ GitHub Actions deploy.yml déclenché
       │
       ▼
ÉTAPE 5 — Deploy automatique GitHub Actions
  ├─ Tests finaux
  ├─ Deploy Railway backend-api
  ├─ Deploy Railway agents-workers
  ├─ Deploy Railway cron-jobs
  ├─ Apply migrations Supabase
  ├─ Deploy Vercel (auto)
  └─ EAS Update OTA mobile
       │
       ▼
ÉTAPE 6 — Smoke tests post-deploy
  ├─ Bash script scripts/smoke_test_prod.sh
  ├─ Vérification Sentry prod (10 min)
  └─ Vérification UptimeRobot
       │
       ▼
ÉTAPE 7 — Validation finale
  ├─ Test manuel flow critique (signup → devis vocal)
  ├─ Vérification métriques (erreurs <1%, latence P95 OK)
  └─ Si KO → rollback (§12)
       │
       ▼
ÉTAPE 8 — Clôture
  ├─ Slack notif "Deploy OK"
  ├─ Update `docs/CHANGELOG.md`
  └─ Monitoring 30 min
```

### 11.2 — Durée totale cible

- Étape 1-3 : **30-60 min** (staging + validation)
- Étape 4-5 : **10-15 min** (deploy CI)
- Étape 6-7 : **15-30 min** (validation)
- **TOTAL : ~1-2h**

### 11.3 — Fenêtres de deploy

- **Recommandé** : mardi-mercredi, 10h-14h (bas trafic)
- **Éviter** : vendredi après-midi, weekend, nuit
- **Interdit** : veille de fériés, périodes de forte activité (rentrée, etc.)

---

## 12. ROLLBACK PROCEDURES

### 12.1 — Déclencheurs de rollback

- Erreur 5xx >10% pendant 5 min
- Sentry : >50 erreurs nouvelles en 10 min
- Smoke test post-deploy échoué
- Incident critique détecté (data corruption, auth broken)

### 12.2 — Rollback Railway (backend)

**Rapide** (<2 min) :

```bash
# Via Railway CLI
railway rollback --service backend-api --environment production

# Ou via dashboard Railway
# Deployments → sélectionner précédent → "Redeploy"
```

Railway conserve automatiquement les **20 derniers deployments**. Rollback instantané vers n'importe lequel.

### 12.3 — Rollback Vercel (PWA + site)

```bash
# Via Vercel CLI
vercel rollback https://app.structorai.app

# Ou via dashboard Vercel
# Deployments → sélectionner précédent → "Promote to Production"
```

### 12.4 — Rollback mobile (EAS Update)

```bash
# Revert OTA update
cd mobile
eas update --branch production --message "Rollback from xxx" --republish

# Re-publier ancien bundle
eas channel:edit production --branch production-previous
```

### 12.5 — Rollback migrations Supabase

**Si possible** (pas de data loss) :

```bash
cd backend
supabase db reset --db-url $SUPABASE_DB_URL --target-migration <previous_timestamp>
```

**Si data loss risqué** → procédure manuelle :
1. Restore backup Supabase (<24h)
2. Exécuter migration `_down.sql` correspondante
3. Re-apply migrations suivantes

### 12.6 — Communication pendant rollback

- Slack #deploys : "🚨 Rollback en cours : [raison]"
- Status page : incident actif
- Si >5 min downtime : email artisans via Brevo (template préparé)

### 12.7 — Post-mortem obligatoire

Après chaque rollback → post-mortem dans `docs/post-mortems/AAAA-MM-DD-incident.md` :
- Qu'est-ce qui s'est passé
- Pourquoi c'est arrivé (root cause)
- Impact (durée, artisans affectés)
- Actions correctives

---

## 13. SMOKE TESTS POST-DEPLOY

### 13.1 — Script `scripts/smoke_test_prod.sh`

```bash
#!/bin/bash
set -e

API_URL="${API_URL:-https://api.structorai.app}"

echo "🧪 Smoke tests STRUCTORAI — $API_URL"

# 1. Health endpoint
echo "1. GET /health"
curl -f "$API_URL/health" | jq -e '.status == "healthy"' > /dev/null

# 2. Health services internals
echo "2. Services up"
curl -f "$API_URL/health" | jq -e '.services.database == "up"' > /dev/null
curl -f "$API_URL/health" | jq -e '.services.redis == "up"' > /dev/null
curl -f "$API_URL/health" | jq -e '.services.mempalace == "up"' > /dev/null

# 3. Public sandbox chat (F115)
echo "3. Public sandbox chat"
RESPONSE=$(curl -f -X POST "$API_URL/v1/public/chat" \
  -H "Content-Type: application/json" \
  -d '{"message": "Bonjour"}')
echo "$RESPONSE" | jq -e '.data.response.content' > /dev/null

# 4. Stripe webhook endpoint accessible
echo "4. Stripe webhook"
curl -X POST "$API_URL/v1/webhooks/stripe" \
  -H "Stripe-Signature: test" \
  -d '{}' -w "%{http_code}" -o /dev/null -s | grep -q "400\|403" # 400/403 ok, pas 500

# 5. WhatsApp webhook accessible
echo "5. WhatsApp webhook verification"
curl -f "$API_URL/v1/whatsapp/webhook?hub.mode=subscribe&hub.verify_token=INVALID&hub.challenge=test" \
  -w "%{http_code}" -o /dev/null -s | grep -q "403"

echo "✅ Smoke tests passés"
```

### 13.2 — Smoke tests automatisés dans `deploy.yml`

Voir §7.1 — step `smoke-tests` qui exécute le script ci-dessus avec alerte Slack si échec.

### 13.3 — Monitoring continu post-deploy

30 min après le deploy, surveiller :
- Sentry : aucune erreur nouvelle
- UptimeRobot : uptime 100%
- Mixpanel events : flow normal
- Railway metrics : CPU/RAM stables

---

## 14. BLUE-GREEN DEPLOYMENT (FUTUR)

### 14.1 — Quand implémenter

**V2 post-launch** (sept-oct 2026) : quand >500 clients et downtime inacceptable.

### 14.2 — Principe

Deux environnements production identiques (Blue et Green) :
- Seul un reçoit le traffic (via load balancer)
- Deploy sur l'autre
- Bascule traffic une fois validé
- Rollback = re-bascule

### 14.3 — Implémentation Railway

Railway ne supporte pas blue-green natif. Alternative :
- 2 projets Railway (prod-blue / prod-green)
- Load balancer Cloudflare devant
- Bascule DNS quand deploy validé

### 14.4 — Coût estimé

**Doublement** des coûts infra backend pendant le deploy (~2h).

Acceptable à >500 clients (MRR > 15K€/mois).

---

## 15. MONITORING ET ALERTES

### 15.1 — Stack monitoring

- **Sentry** : erreurs backend + mobile + performance
- **UptimeRobot** : uptime HTTP (gratuit)
- **Better Uptime** : advanced uptime + on-call rotation (post-launch)
- **Mixpanel** (ou PostHog EU) : events produit
- **Railway native metrics** : CPU/RAM/network par service
- **Supabase Dashboard** : queries performance + slow logs

### 15.2 — Alertes Slack + SMS

Voir `docs/SUPPORT-STRATEGY.md` pour détails.

**Niveau critique (SMS Fabrice)** :
- API down >2 min
- Database down
- Erreur 5xx >20%

**Niveau warning (Slack)** :
- Latence P95 API >3s
- Disk usage >80%
- Free tier Claude API épuisé

### 15.3 — Dashboards

**Fabrice ops dashboard** (`/admin/ops`) :
- Statut services
- Erreurs en temps réel
- Latences
- Volume requêtes

**Business dashboard** (`/admin/business`) :
- MRR
- Active users
- Churn
- NPS

Voir `docs/API.md` §30 endpoints admin.

---

## 16. DEPLOY MOBILE ANDROID DÉTAILLÉ

### 16.1 — Releases model

STRUCTORAI utilise **semantic versioning** :
- `1.0.0` launch juin 2026
- `1.0.1`, `1.0.2` : bugfixes
- `1.1.0`, `1.2.0` : nouvelles features
- `2.0.0` : V2 (sept 2026, add-on Téléphone)

### 16.2 — Process release

```bash
# 1. Bump version dans app.json
# "version": "1.0.1" + "android.versionCode": 2

# 2. Commit + tag
git commit -m "Release 1.0.1"
git tag v1.0.1
git push --tags

# 3. Build production
cd mobile
eas build --platform android --profile production

# 4. Check build OK (dashboard EAS)

# 5. Submit Google Play
eas submit --platform android --profile production
```

### 16.3 — Google Play rollout progressif

Rollout en % :
- Jour 1 : 10%
- Jour 3 : 50% (si pas d'erreur détectée)
- Jour 7 : 100%

**Rollback** : via Google Play Console → "Halt rollout" → investiguer.

### 16.4 — App Bundle vs APK

**App Bundle (AAB)** préféré :
- Google délivre APK optimisé par device
- Taille download réduite (~30%)
- Obligatoire Google Play depuis 2021

**APK** utilisé pour :
- Distribution hors store (beta testeurs)
- Build "preview" EAS

---

## 17. DEPLOY PWA IOS DÉTAILLÉ

### 17.1 — Pas de store iOS

STRUCTORAI **bypass l'Apple Store** (commission 30% + validation longue).

### 17.2 — Install via Safari

Flow utilisateur :
1. Visite `https://app.structorai.app` sur Safari
2. Appuie sur "Partager" → "Ajouter à l'écran d'accueil"
3. Icône STRUCTORAI apparaît sur le home iPhone
4. Launch en plein écran (PWA manifest)

### 17.3 — Manifest PWA

**Fichier** : `mobile/public/manifest.json`

```json
{
  "name": "STRUCTORAI",
  "short_name": "STRUCTORAI",
  "description": "L'assistant IA BTP",
  "start_url": "/",
  "display": "standalone",
  "background_color": "#0A1628",
  "theme_color": "#0A1628",
  "icons": [
    {"src": "/icon-192.png", "sizes": "192x192", "type": "image/png"},
    {"src": "/icon-512.png", "sizes": "512x512", "type": "image/png"}
  ]
}
```

### 17.4 — Onboarding "ajouter à l'écran d'accueil"

Sur iPhone détecté → banner auto avec tuto vidéo :
- "Installe STRUCTORAI comme une app native"
- Tutoriel 3 étapes animé

### 17.5 — Limitations PWA iOS

- Pas de push notifications natives
- Stockage limité à 50 MB
- Pas de biométrie FaceID
- Pas de background sync fiable
- Fallback email/SMS pour notifications critiques

---

## 18. STAGING WORKFLOW

### 18.1 — Pourquoi staging

- Tester les déploiements avant prod
- Beta testeurs artisans
- QA manuelle
- Validation migrations

### 18.2 — Différences staging vs production

| Aspect | Staging | Production |
|---|---|---|
| URL API | api-staging.structorai.app | api.structorai.app |
| Supabase | Projet staging (Frankfurt) | Projet prod (Frankfurt) |
| Stripe | Mode TEST | Mode LIVE |
| WhatsApp | Sandbox Meta | Numéro live |
| Anthropic | Clé staging (budget limité) | Clé prod |
| Notifications | Envoyées vers Slack (pas utilisateurs) | Envoyées aux utilisateurs |
| Données | Fake / anonymisées | Réelles |

### 18.3 — Reset staging régulier

Toutes les 2 semaines :
- Purge DB staging
- Recrée data synthétique (script `scripts/seed_staging.py`)
- Invite beta-testeurs à re-tester

---

## 19. GESTION DES SECRETS

### 19.1 — Règles d'or

1. **Jamais de secret dans le code** (vérification `detect-secrets` dans CI)
2. **Jamais de secret dans logs** (scrubbing Sentry)
3. **Jamais de secret dans `.env` commit** (gitignored strict)
4. **Rotation tous les 90 jours** (script `scripts/rotate_secrets.sh`)
5. **Chiffrement at rest** sur Railway (native)

### 19.2 — Stockage

- **Local** : `.env` (gitignored)
- **CI/CD** : GitHub Actions Secrets (encrypted)
- **Runtime** : Railway Environment Variables (encrypted at rest)
- **Dev partagé équipe** : 1Password vault "STRUCTORAI"

### 19.3 — Audit

Audit mensuel :
- Lister les secrets actifs
- Identifier ceux non utilisés → révoquer
- Vérifier rotation

---

## 20. PLAN DE CONTINUITÉ (DR)

### 20.1 — RTO / RPO cibles

- **RTO** (temps de recovery) : **2h** maximum
- **RPO** (perte données max) : **24h** maximum

### 20.2 — Scénarios DR

| Scénario | Solution | RTO |
|---|---|---|
| Service Railway backend-api down | Rollback ou restart | <5 min |
| Service Railway agents-workers down | Rollback ou restart | <10 min |
| MemPalace down | Fallback Mem0 seul (voir `docs/MEMORY-STRATEGY.md`) | 2h |
| Supabase down complet | Alerte Supabase + attente (rare) | Selon SLA |
| Région Railway EU down | Migration vers Railway US temporaire | 4h |
| Domain hijack | Restore Porkbun + DNS | 24h |
| Data corruption | Restore backup Supabase <24h | 2h |
| Compte Fabrice compromis | Révocation + rotation secrets | Immédiat |

### 20.3 — Backups

**Supabase** :
- Backup quotidien automatique Supabase (7 jours gratuits)
- Backup manuel hebdomadaire téléchargé (retenu 30 jours)
- Point-in-time recovery via Supabase Pro (+$25/mois)

**MemPalace** :
- Voir `docs/MEMORY-STRATEGY.md` §10 (daily encrypted backup)

**Code** :
- GitHub = source de vérité (redondant cloud)
- Fork privé backup sur GitLab

### 20.4 — Tests DR

**Trimestriel** : simulation d'incident (rollback, restore backup) en staging.

---

## 21. COÛTS D'INFRASTRUCTURE

### 21.1 — Coûts mensuels par palier

**<100 clients (launch)** :

| Service | Coût |
|---|---|
| Railway (5 services) | ~20$/mois |
| Supabase Pro | 25$/mois (dès 50 clients) |
| Vercel Hobby | 0$ |
| Sentry Free | 0$ |
| UptimeRobot | 0€ |
| Google Play | 25$ one-time |
| Domaines Porkbun | ~30€/an |
| **TOTAL** | **~50-70$/mois** |

**100-500 clients** :

| Service | Coût |
|---|---|
| Railway (scale) | ~80$/mois |
| Supabase Pro + read replica | 50$/mois |
| Vercel Pro | 20$/mois |
| Sentry Team | 26$/mois |
| Better Uptime | 15€/mois |
| **TOTAL** | **~200€/mois** |

**500-1K clients** :

| Service | Coût |
|---|---|
| Railway (scale) | ~200$/mois |
| Supabase | 75$/mois |
| Vercel | 20$/mois |
| Sentry | 80$/mois |
| **TOTAL** | **~400€/mois** |

Voir `COUT_REEL.md` pour analyse complète.

---

## 22. TROUBLESHOOTING DEPLOY

### 22.1 — Problèmes fréquents

| Symptôme | Cause | Solution |
|---|---|---|
| Deploy Railway échoue | Build Docker fail | Check Dockerfile, requirements.txt |
| Migrations Supabase fail | Syntax SQL ou conflit version | Tester en local, `supabase db reset` |
| Vercel build timeout | Dépendances trop lourdes | Cache node_modules, optim bundle |
| EAS build fail Android | Config eas.json invalide | `eas build --clear-cache` |
| Smoke test fail post-deploy | Env var manquante | Check Railway env vars |
| 502 Bad Gateway après deploy | Service pas encore ready | Attendre 60s, vérifier logs Railway |
| Rollback ne revient pas | Migration appliquée non réversible | Restore backup Supabase |

### 22.2 — Logs à consulter

- **Railway** : dashboard → service → "Logs" tab
- **Vercel** : dashboard → deployment → "Functions" → "Logs"
- **Sentry** : erreurs détaillées
- **Supabase** : Database → "Logs" tab
- **GitHub Actions** : Actions tab → workflow → run

### 22.3 — Contact support

- Railway : Discord community + email
- Vercel : support tickets + Discord
- Supabase : Discord (actif) + support Pro
- EAS : Discord Expo + docs.expo.dev

---

## 23. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/ARCH.md` §9 | Infrastructure (Railway + Vercel + EAS) |
| `docs/API.md` | Endpoints + health checks |
| `docs/ENV.md` (à créer) | Variables d'environnement exhaustives |
| `docs/MIGRATIONS.md` (à créer) | 37 migrations Supabase |
| `docs/ERRORS.md` (à créer) | Codes erreur (smoke tests) |
| `docs/TESTS.md` (à créer) | Stratégie tests |
| `docs/MEMORY-STRATEGY.md` | Backup MemPalace + fallback |
| `docs/AI-ACT-COMPLIANCE.md` | Rétention audit logs 12 mois |
| `docs/SECURITE.md` | Secrets management |
| `docs/SUPPORT-STRATEGY.md` | Communication incidents |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` | Budget infra |
| `COUT_REEL.md` | Coûts détaillés |
| `BUILD_PLAN.md` | Sprint 0 setup infra |
| `SECURITE.md` | Sécurité |

### 23.1 — Références externes

- **Railway docs** : https://docs.railway.app/
- **Vercel docs** : https://vercel.com/docs
- **Expo EAS** : https://docs.expo.dev/eas/
- **Google Play Console** : https://play.google.com/console
- **Supabase CLI** : https://supabase.com/docs/reference/cli
- **GitHub Actions** : https://docs.github.com/en/actions

---

> **Ce fichier est le runbook de déploiement STRUCTORAI.**
> **Règle d'or** : jamais de deploy vendredi après-midi ni en fin de journée.
> **RTO cible** : 2h maximum en cas d'incident.
> **Rollback** : toujours possible en <5 min via Railway / Vercel.
> **Pre-deploy checklist obligatoire** : aucun deploy sans validation de §10.
