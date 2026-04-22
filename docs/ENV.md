---
name: ENV
description: Catalogue exhaustif des variables d'environnement de STRUCTORAI — backend (FastAPI sur Railway), mobile (React Native Expo), agents-workers, MemPalace, cron-jobs. Groupes par service avec valeurs exemples, où les obtenir, politique rotation, secrets vs config publique, différences staging/production. Fichier `.env.example` complet prêt à copier. Règles de sécurité et anti-patterns.
type: technical-env-reference
scope: backend, mobile, agents-workers, mempalace, cron-jobs, ci-cd
priority: critical
date: 2026-04-18
version: 1.0
total_variables: ~120 (backend) + ~15 (mobile)
secret_count: ~65 secrets à rotater
public_config_count: ~55 public config (non secrets)
---

# docs/ENV.md — Variables d'environnement STRUCTORAI

> **Ce fichier est la source de vérité des variables d'environnement.**
> Reference deploy : `docs/DEPLOY.md` §8 (vue d'ensemble).
> Reference sécurité : `SECURITE.md` (secrets management).
> **Règle d'or** : AUCUN secret jamais dans le code, jamais dans Git.

---

## 0. SOMMAIRE

1. [Principes et conventions](#1-principes-et-conventions)
2. [Types de variables — secret vs public config](#2-types-de-variables--secret-vs-public-config)
3. [Variables Application (backend)](#3-variables-application-backend)
4. [Variables Supabase](#4-variables-supabase)
5. [Variables Redis](#5-variables-redis)
6. [Variables MemPalace](#6-variables-mempalace)
7. [Variables Anthropic / Claude](#7-variables-anthropic--claude)
8. [Variables Mem0](#8-variables-mem0)
9. [Variables Voice (Whisper + Deepgram + ElevenLabs + OpenAI TTS)](#9-variables-voice-whisper--deepgram--elevenlabs--openai-tts)
10. [Variables WhatsApp / Meta Cloud API](#10-variables-whatsapp--meta-cloud-api)
11. [Variables Stripe](#11-variables-stripe)
12. [Variables Twilio (SMS + Voice V2)](#12-variables-twilio-sms--voice-v2)
13. [Variables Brevo (Email)](#13-variables-brevo-email)
14. [Variables Yousign (Signature)](#14-variables-yousign-signature)
15. [Variables Factur-X (Plateforme Agréée)](#15-variables-factur-x-plateforme-agréée)
16. [Variables Crisp (Support F136)](#16-variables-crisp-support-f136)
17. [Variables Monitoring (Sentry + GA4 + Mixpanel + UptimeRobot)](#17-variables-monitoring-sentry--ga4--mixpanel--uptimerobot)
18. [Variables Google (Reviews + Play Services)](#18-variables-google-reviews--play-services)
19. [Variables mobile (Expo)](#19-variables-mobile-expo)
20. [Variables spécifiques agents-workers + cron-jobs](#20-variables-spécifiques-agents-workers--cron-jobs)
21. [Fichier `.env.example` complet](#21-fichier-envexample-complet)
22. [Rotation des secrets](#22-rotation-des-secrets)
23. [Différences staging vs production](#23-différences-staging-vs-production)
24. [Anti-patterns et règles sécurité](#24-anti-patterns-et-règles-sécurité)
25. [Troubleshooting env vars](#25-troubleshooting-env-vars)
26. [Références croisées](#26-références-croisées)

---

## 1. PRINCIPES ET CONVENTIONS

### 1.1 — Nommage

- **SNAKE_CASE** en majuscules
- Préfixe par **domaine logique** : `SUPABASE_*`, `STRIPE_*`, `WHATSAPP_*`, etc.
- **EXPO_PUBLIC_*** pour variables exposées côté mobile (accessibles JS bundle)
- Pas de variable sans préfixe (évite conflits)

### 1.2 — Chargement

**Backend FastAPI** : `pydantic-settings` charge automatiquement depuis :
1. Variables d'environnement système (priorité haute)
2. Fichier `.env` (dev local uniquement, gitignored)
3. Valeurs par défaut dans `backend/app/config.py`

**Mobile Expo** : variables préfixées `EXPO_PUBLIC_*` inclues dans le bundle JS au build.

### 1.3 — Fichier `config.py` (backend)

**Structure** : `backend/app/config.py`

```python
from pydantic_settings import BaseSettings, SettingsConfigDict

class Settings(BaseSettings):
    model_config = SettingsConfigDict(
        env_file=".env",
        env_file_encoding="utf-8",
        case_sensitive=True,
        extra="forbid",      # Rejette variables non déclarées (sécurité)
    )
    
    # App
    APP_ENV: str = "development"
    APP_DEBUG: bool = True
    APP_URL: str
    FRONTEND_URL: str
    # ... toutes les vars listées ci-dessous
```

### 1.4 — Validation obligatoire au démarrage

Le backend **REFUSE** de démarrer si une variable critique manque ou est invalide :
- Fail fast (crash explicite)
- Message d'erreur clair avec nom de la variable
- Pas de fallback silencieux sur valeur par défaut pour secrets

---

## 2. TYPES DE VARIABLES — SECRET VS PUBLIC CONFIG

### 2.1 — Catégorisation

**🔒 SECRET** (ne JAMAIS logger, ne JAMAIS exposer client, rotation 90j) :
- API keys providers (Anthropic, Stripe, Meta, etc.)
- Tokens d'accès (WhatsApp, Yousign, etc.)
- Service role keys (Supabase)
- JWT secrets
- Webhook secrets

**🟢 PUBLIC CONFIG** (safe à logger, peut être exposé) :
- URLs (API, frontend, webhooks)
- Noms de modèles (Claude models)
- IDs publics (Stripe price IDs, Voice IDs ElevenLabs)
- Environment name (production/staging)
- Feature flags

### 2.2 — Notation dans ce document

Chaque variable a ces métadonnées :
- **🔒/🟢** : secret ou public
- **Requis** : oui/non (✅ = obligatoire, ⚪ = optionnel)
- **Où l'obtenir** : source
- **Rotation** : fréquence recommandée
- **Exemple** : valeur type (jamais la vraie)

---

## 3. VARIABLES APPLICATION (BACKEND)

### 3.1 — Configuration générale

| Variable | Type | Requis | Rotation | Exemple |
|---|---|---|---|---|
| `APP_ENV` | 🟢 | ✅ | — | `production` / `staging` / `development` |
| `APP_DEBUG` | 🟢 | ✅ | — | `false` (prod) / `true` (dev) |
| `APP_URL` | 🟢 | ✅ | — | `https://api.structorai.app` |
| `FRONTEND_URL` | 🟢 | ✅ | — | `https://app.structorai.app` |
| `MARKETING_URL` | 🟢 | ✅ | — | `https://structorai.app` |
| `CORS_ORIGINS` | 🟢 | ✅ | — | `["https://app.structorai.app","https://structorai.app"]` |
| `LOG_LEVEL` | 🟢 | ⚪ | — | `INFO` (prod) / `DEBUG` (dev) |
| `LOG_FORMAT` | 🟢 | ⚪ | — | `json` (prod) / `pretty` (dev) |
| `TIMEZONE` | 🟢 | ⚪ | — | `Europe/Paris` |
| `DEFAULT_LOCALE` | 🟢 | ⚪ | — | `fr-FR` |

### 3.2 — Sécurité

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `APP_SECRET_KEY` | 🔒 | ✅ | 180j | Secret signature cookies/CSRF (généré `openssl rand -hex 32`) |
| `SESSION_COOKIE_SECURE` | 🟢 | ✅ | — | `true` (prod HTTPS) |
| `SESSION_COOKIE_SAMESITE` | 🟢 | ⚪ | — | `lax` |

---

## 4. VARIABLES SUPABASE

### 4.1 — Base

| Variable | Type | Requis | Rotation | Où l'obtenir |
|---|---|---|---|---|
| `SUPABASE_URL` | 🟢 | ✅ | — | Dashboard → Settings → API → Project URL |
| `SUPABASE_ANON_KEY` | 🟢 | ✅ | 12 mois | Dashboard → Settings → API → anon public key |
| `SUPABASE_SERVICE_ROLE_KEY` | 🔒 | ✅ | 90j | Dashboard → Settings → API → service_role (CRITIQUE) |
| `SUPABASE_JWT_SECRET` | 🔒 | ✅ | 180j | Dashboard → Settings → API → JWT Secret |
| `SUPABASE_DB_URL` | 🔒 | ✅ | 90j | Dashboard → Settings → Database → Connection string |

### 4.2 — Exemple

```bash
SUPABASE_URL=https://abcxyzwqer.supabase.co
SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFz...
SUPABASE_SERVICE_ROLE_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFz...
SUPABASE_JWT_SECRET=super-secret-jwt-signing-key-min-32-chars
SUPABASE_DB_URL=postgresql://postgres:[password]@db.abcxyzwqer.supabase.co:5432/postgres
```

### 4.3 — Notes critiques

- ⚠️ **Service role key = full admin DB access** — jamais côté client, jamais dans frontend
- `SUPABASE_ANON_KEY` est safe à exposer (protégée par RLS)
- Rotation service role = pénible (nécessite redéploiement + mise à jour env) → 90j minimum

---

## 5. VARIABLES REDIS

### 5.1 — Redis Railway

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `REDIS_URL` | 🔒 | ✅ | 180j | URL complète Redis Railway interne |
| `REDIS_POOL_SIZE` | 🟢 | ⚪ | — | Pool connexions (défaut: 10) |
| `REDIS_TIMEOUT_SECONDS` | 🟢 | ⚪ | — | Timeout opérations (défaut: 5) |

### 5.2 — Exemple

```bash
REDIS_URL=redis://default:abcd1234@redis.railway.internal:6379
REDIS_POOL_SIZE=10
REDIS_TIMEOUT_SECONDS=5
```

### 5.3 — Notes

- URL interne Railway `*.railway.internal` = accessible uniquement depuis services du même projet
- En local, utiliser Docker : `redis://localhost:6379`

---

## 6. VARIABLES MEMPALACE

### 6.1 — Service interne Railway

Voir `docs/MEMORY.md` pour détails architecture.

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `MEMPALACE_URL` | 🟢 | ✅ | — | URL interne service MemPalace |
| `MEMPALACE_API_KEY` | 🔒 | ✅ | 180j | Clé interne auth MemPalace → backend |
| `MEMPALACE_BACKUP_ENCRYPTION_KEY` | 🔒 | ✅ | 365j | AES-256 pour chiffrement backups daily |
| `MEMPALACE_BACKUP_BUCKET` | 🟢 | ✅ | — | Bucket Supabase Storage pour backups |

### 6.2 — Exemple

```bash
MEMPALACE_URL=http://mempalace.railway.internal:8001
MEMPALACE_API_KEY=mpk_live_abc123xyz
MEMPALACE_BACKUP_ENCRYPTION_KEY=32_bytes_hex_key_generated_openssl
MEMPALACE_BACKUP_BUCKET=mempalace-backups
```

---

## 7. VARIABLES ANTHROPIC / CLAUDE

### 7.1 — API Claude

| Variable | Type | Requis | Rotation | Où l'obtenir |
|---|---|---|---|---|
| `ANTHROPIC_API_KEY` | 🔒 | ✅ | 90j | console.anthropic.com → Settings → API Keys |
| `ANTHROPIC_MODEL_OPUS` | 🟢 | ✅ | — | Modèle Opus exact |
| `ANTHROPIC_MODEL_SONNET` | 🟢 | ✅ | — | Modèle Sonnet exact |
| `ANTHROPIC_MODEL_HAIKU` | 🟢 | ✅ | — | Modèle Haiku exact |
| `ANTHROPIC_MAX_TOKENS_DEFAULT` | 🟢 | ⚪ | — | Max tokens par appel (défaut: 2000) |
| `ANTHROPIC_TIMEOUT_SECONDS` | 🟢 | ⚪ | — | Timeout appel LLM (défaut: 30) |
| `ANTHROPIC_STREAM_ENABLED` | 🟢 | ⚪ | — | Streaming (défaut: true) |

### 7.2 — Exemple

```bash
ANTHROPIC_API_KEY=sk-ant-api03-xxxxxxxxxxxxxxxxxxxxxxx
ANTHROPIC_MODEL_OPUS=claude-opus-4-7
ANTHROPIC_MODEL_SONNET=claude-sonnet-4-6
ANTHROPIC_MODEL_HAIKU=claude-haiku-4-5-20251001
ANTHROPIC_MAX_TOKENS_DEFAULT=2000
ANTHROPIC_TIMEOUT_SECONDS=30
ANTHROPIC_STREAM_ENABLED=true
```

### 7.3 — Règle modèles

Voir `docs/SINGLE_SOURCE_OF_TRUTH.md` §9. Ces 3 noms de modèles DOIVENT être strictement les mêmes dans tout le code (aucune dérive avec `claude-sonnet-4-6-20260101` ou autre variante).

---

## 8. VARIABLES MEM0

### 8.1 — Mem0 Cloud EU

Voir `docs/MEMORY.md` pour rôle.

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `MEM0_API_KEY` | 🔒 | ✅ | 180j | Clé API Mem0 (console.mem0.ai) |
| `MEM0_REGION` | 🟢 | ✅ | — | `eu` (obligatoire souveraineté) |
| `MEM0_PROJECT_ID` | 🟢 | ✅ | — | ID projet Mem0 STRUCTORAI |
| `MEM0_TIMEOUT_SECONDS` | 🟢 | ⚪ | — | Timeout (défaut: 10) |

### 8.2 — Exemple

```bash
MEM0_API_KEY=m0-xxxxxxxxxxxxxxxxxxxxxxx
MEM0_REGION=eu
MEM0_PROJECT_ID=structorai-prod
MEM0_TIMEOUT_SECONDS=10
```

---

## 9. VARIABLES VOICE (WHISPER + DEEPGRAM + ELEVENLABS + OPENAI TTS)

### 9.1 — Whisper (OpenAI STT)

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `OPENAI_API_KEY` | 🔒 | ✅ | 90j | Utilisée pour Whisper STT + TTS fallback |
| `OPENAI_WHISPER_MODEL` | 🟢 | ⚪ | — | `whisper-1` (défaut) |
| `OPENAI_TTS_MODEL` | 🟢 | ⚪ | — | `tts-1` (fallback) ou `tts-1-hd` |

### 9.2 — Deepgram (STT fallback)

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `DEEPGRAM_API_KEY` | 🔒 | ⚪ | 90j | Fallback Whisper (optionnel mais recommandé) |
| `DEEPGRAM_MODEL` | 🟢 | ⚪ | — | `nova-2` (défaut) |

### 9.3 — ElevenLabs (TTS principal)

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `ELEVENLABS_API_KEY` | 🔒 | ✅ | 90j | Clé API ElevenLabs |
| `ELEVENLABS_MODEL` | 🟢 | ✅ | — | `eleven_multilingual_v2` |
| `ELEVENLABS_VOICE_MALE_PRO` | 🟢 | ✅ | — | Voice ID défaut (Adam) |
| `ELEVENLABS_VOICE_FEMALE_PRO` | 🟢 | ✅ | — | Voice ID défaut (Bella) |
| `ELEVENLABS_VOICE_MALE_CASUAL` | 🟢 | ⚪ | — | Voice ID (Antoni) |
| `ELEVENLABS_VOICE_FEMALE_CASUAL` | 🟢 | ⚪ | — | Voice ID (Rachel) |

### 9.4 — Exemple

```bash
# STT
OPENAI_API_KEY=sk-proj-xxxxxxxxxxxxxxxxxxxxxxx
OPENAI_WHISPER_MODEL=whisper-1
OPENAI_TTS_MODEL=tts-1

DEEPGRAM_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
DEEPGRAM_MODEL=nova-2

# TTS
ELEVENLABS_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
ELEVENLABS_MODEL=eleven_multilingual_v2
ELEVENLABS_VOICE_MALE_PRO=pNInz6obpgDQGcFmaJgB
ELEVENLABS_VOICE_FEMALE_PRO=EXAVITQu4vr4xnSDxMaL
ELEVENLABS_VOICE_MALE_CASUAL=ErXwobaYiN019PkySvjV
ELEVENLABS_VOICE_FEMALE_CASUAL=21m00Tcm4TlvDq8ikWAM
```

Voir `docs/VOICE.md` pour détails.

---

## 10. VARIABLES WHATSAPP / META CLOUD API

### 10.1 — Meta Cloud API

Voir `docs/WHATSAPP.md` pour détails.

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `WHATSAPP_PHONE_NUMBER_ID` | 🟢 | ✅ | — | ID du numéro dans Meta Business |
| `WHATSAPP_BUSINESS_ACCOUNT_ID` | 🟢 | ✅ | — | WABA ID |
| `WHATSAPP_ACCESS_TOKEN` | 🔒 | ✅ | 60j | Permanent Access Token (rotation stricte 60j) |
| `WHATSAPP_VERIFY_TOKEN` | 🔒 | ✅ | 180j | Token custom vérification webhook |
| `WHATSAPP_APP_SECRET` | 🔒 | ✅ | 180j | App Secret pour signature HMAC |
| `WHATSAPP_API_VERSION` | 🟢 | ⚪ | — | `v20.0` (défaut) |
| `WHATSAPP_WEBHOOK_URL` | 🟢 | ✅ | — | URL publique webhook STRUCTORAI |

### 10.2 — Exemple

```bash
WHATSAPP_PHONE_NUMBER_ID=123456789012345
WHATSAPP_BUSINESS_ACCOUNT_ID=987654321098765
WHATSAPP_ACCESS_TOKEN=EAAxxxxxxxxxxxxxxxxxx
WHATSAPP_VERIFY_TOKEN=structorai_wh_verify_a8b7c6d5e4f3
WHATSAPP_APP_SECRET=xxxxxxxxxxxxxxxxxxxxxx
WHATSAPP_API_VERSION=v20.0
WHATSAPP_WEBHOOK_URL=https://api.structorai.app/v1/whatsapp/webhook
```

### 10.3 — Notes critiques

- ⚠️ `WHATSAPP_ACCESS_TOKEN` **expire tous les 60 jours** par défaut → **rotation automatique obligatoire**
- Script cron : `scripts/rotate_whatsapp_token.py` (lancé chaque 55 jours)
- Si expiration ratée → downtime WhatsApp tant que pas renouvelé

---

## 11. VARIABLES STRIPE

### 11.1 — Stripe API

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `STRIPE_SECRET_KEY` | 🔒 | ✅ | 90j | Clé secrète Stripe |
| `STRIPE_PUBLISHABLE_KEY` | 🟢 | ✅ | 90j | Clé publique (côté mobile) |
| `STRIPE_WEBHOOK_SECRET` | 🔒 | ✅ | 180j | Secret signature webhook |
| `STRIPE_API_VERSION` | 🟢 | ⚪ | — | `2024-12-18.acacia` (pin version) |

### 11.2 — Price IDs

Voir `docs/SINGLE_SOURCE_OF_TRUTH.md` §7 (7 plans tarifaires).

| Variable | Type | Requis | Description |
|---|---|---|---|
| `STRIPE_PRICE_PRO_MONTHLY` | 🟢 | ✅ | Price ID Pro €29/mois |
| `STRIPE_PRICE_PRO_ANNUAL` | 🟢 | ✅ | Price ID Pro annuel €229/an |
| `STRIPE_PRICE_BUSINESS_MONTHLY` | 🟢 | ✅ | Price ID Business €79/mois |
| `STRIPE_PRICE_BUSINESS_ANNUAL` | 🟢 | ✅ | Price ID Business annuel €629/an |
| `STRIPE_PRICE_LIFETIME` | 🟢 | ✅ | Price ID Lifetime €990 |
| `STRIPE_PRICE_ADDON_TELEPHONE` | 🟢 | ⚪ | Add-on Agent Téléphone V2 +15€/mois |
| `STRIPE_COUPON_CAPEB20` | 🟢 | ⚪ | Code promo CAPEB -20% |
| `STRIPE_COUPON_FFB20` | 🟢 | ⚪ | Code promo FFB -20% |

### 11.3 — Exemple

```bash
STRIPE_SECRET_KEY=sk_live_51xxxxxxxxxxxxxxxxxx
STRIPE_PUBLISHABLE_KEY=pk_live_51xxxxxxxxxxxxxxxxxx
STRIPE_WEBHOOK_SECRET=whsec_xxxxxxxxxxxxxxxxxxx
STRIPE_API_VERSION=2024-12-18.acacia

STRIPE_PRICE_PRO_MONTHLY=price_xxx
STRIPE_PRICE_PRO_ANNUAL=price_xxx
STRIPE_PRICE_BUSINESS_MONTHLY=price_xxx
STRIPE_PRICE_BUSINESS_ANNUAL=price_xxx
STRIPE_PRICE_LIFETIME=price_xxx
STRIPE_PRICE_ADDON_TELEPHONE=price_xxx
STRIPE_COUPON_CAPEB20=coupon_xxx
STRIPE_COUPON_FFB20=coupon_xxx
```

### 11.4 — Staging = mode test

En staging : utiliser `sk_test_*` + `pk_test_*` + cartes test `4242 4242 4242 4242`.

---

## 12. VARIABLES TWILIO (SMS + VOICE V2)

### 12.1 — Twilio

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `TWILIO_ACCOUNT_SID` | 🟢 | ✅ | — | Account SID (identifiant compte) |
| `TWILIO_AUTH_TOKEN` | 🔒 | ✅ | 90j | Auth token (accès API) |
| `TWILIO_PHONE_NUMBER` | 🟢 | ✅ | — | Numéro d'envoi SMS |
| `TWILIO_MESSAGING_SERVICE_SID` | 🟢 | ⚪ | — | Service ID pour envoi batch |
| `TWILIO_VOICE_PHONE_NUMBER` | 🟢 | ⚪ | — | Numéro pour Agent Téléphone V2 (voice) |

### 12.2 — Exemple

```bash
TWILIO_ACCOUNT_SID=ACxxxxxxxxxxxxxxxxxxxxxxxxx
TWILIO_AUTH_TOKEN=xxxxxxxxxxxxxxxxxxxxxxxxxx
TWILIO_PHONE_NUMBER=+33912345678
TWILIO_MESSAGING_SERVICE_SID=MGxxxxxxxxxxxxxxxxxxxxxxxxx
TWILIO_VOICE_PHONE_NUMBER=+33912345679
```

### 12.3 — Notes

- `TWILIO_VOICE_PHONE_NUMBER` — activé V2 uniquement (Agent Téléphone F126)
- Pour tests staging : utiliser Twilio trial (crédit gratuit 15$ + restrictions numéros vérifiés)

---

## 13. VARIABLES BREVO (EMAIL)

### 13.1 — Brevo (ex-Sendinblue)

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `BREVO_API_KEY` | 🔒 | ✅ | 90j | Clé API Brevo v3 |
| `BREVO_SENDER_EMAIL` | 🟢 | ✅ | — | Email expéditeur (ex: `noreply@structorai.app`) |
| `BREVO_SENDER_NAME` | 🟢 | ✅ | — | Nom expéditeur (`STRUCTORAI`) |
| `BREVO_REPLY_TO_EMAIL` | 🟢 | ⚪ | — | Email de réponse (ex: `support@structorai.app`) |
| `BREVO_LIST_ID_NEWSLETTER` | 🟢 | ⚪ | — | ID liste newsletter artisans |

### 13.2 — Exemple

```bash
BREVO_API_KEY=xkeysib-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
BREVO_SENDER_EMAIL=noreply@structorai.app
BREVO_SENDER_NAME=STRUCTORAI
BREVO_REPLY_TO_EMAIL=support@structorai.app
BREVO_LIST_ID_NEWSLETTER=2
```

### 13.3 — Template IDs Brevo

Pour les emails transactionnels, utiliser Templates Brevo :

```bash
BREVO_TEMPLATE_WELCOME=1
BREVO_TEMPLATE_OTP=2
BREVO_TEMPLATE_DEVIS_ENVOYE=3
BREVO_TEMPLATE_FACTURE_ENVOYEE=4
BREVO_TEMPLATE_RELANCE_DEVIS=5
BREVO_TEMPLATE_RELANCE_FACTURE=6
BREVO_TEMPLATE_CONFIRMATION_SIGNATURE=7
BREVO_TEMPLATE_RESET_PASSWORD=8
```

---

## 14. VARIABLES YOUSIGN (SIGNATURE)

### 14.1 — Yousign API

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `YOUSIGN_API_KEY` | 🔒 | ✅ | 180j | Clé API Yousign |
| `YOUSIGN_API_URL` | 🟢 | ✅ | — | `https://api.yousign.app/v3` (prod) |
| `YOUSIGN_WEBHOOK_SECRET` | 🔒 | ✅ | 180j | Secret webhook Yousign |
| `YOUSIGN_DEFAULT_SIGNATURE_LEVEL` | 🟢 | ⚪ | — | `electronic_signature` (défaut) |

### 14.2 — Exemple

```bash
YOUSIGN_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
YOUSIGN_API_URL=https://api.yousign.app/v3
YOUSIGN_WEBHOOK_SECRET=xxxxxxxxxxxxxxxxxxxxxx
YOUSIGN_DEFAULT_SIGNATURE_LEVEL=electronic_signature
```

### 14.3 — Notes

- Staging : URL `https://api.yousign.app/v3` + clé sandbox (pas de facturation)
- eIDAS conforme FR

---

## 15. VARIABLES FACTUR-X (PLATEFORME AGRÉÉE)

### 15.1 — FactPulse ou B2Brouter

Voir `docs/METIER.md` pour Factur-X (obligation sept 2026 réception, sept 2027 émission).

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `FACTURX_PA_PROVIDER` | 🟢 | ✅ | — | `factpulse` ou `b2brouter` |
| `FACTURX_PA_API_KEY` | 🔒 | ✅ | 90j | Clé API PA |
| `FACTURX_PA_API_URL` | 🟢 | ✅ | — | URL API PA |
| `FACTURX_PA_SIRET` | 🟢 | ✅ | — | SIRET entreprise STRUCTORAI |
| `FACTURX_DEFAULT_PROFILE` | 🟢 | ⚪ | — | `EXTENDED_CIUS_CONSTRUCTION` |

### 15.2 — Exemple

```bash
FACTURX_PA_PROVIDER=factpulse
FACTURX_PA_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxx
FACTURX_PA_API_URL=https://api.factpulse.com/v1
FACTURX_PA_SIRET=12345678901234
FACTURX_DEFAULT_PROFILE=EXTENDED_CIUS_CONSTRUCTION
```

---

## 16. VARIABLES CRISP (SUPPORT F136)

### 16.1 — Crisp Chat Widget

Voir `docs/SUPPORT-STRATEGY.md` §7.

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `CRISP_WEBSITE_ID` | 🟢 | ✅ | — | Website ID public (dans bundle mobile) |
| `CRISP_API_IDENTIFIER` | 🔒 | ⚪ | 180j | Plugin ID pour API |
| `CRISP_API_KEY` | 🔒 | ⚪ | 180j | Plugin Key pour API |
| `CRISP_WEBHOOK_SECRET` | 🔒 | ⚪ | 180j | Secret webhooks Crisp |

### 16.2 — Exemple

```bash
CRISP_WEBSITE_ID=a8b7c6d5-e4f3-2g1h-i0j9-k8l7m6n5o4p3
CRISP_API_IDENTIFIER=xxxxxxxxxxxxxxxxxxxxxx
CRISP_API_KEY=xxxxxxxxxxxxxxxxxxxxxx
CRISP_WEBHOOK_SECRET=xxxxxxxxxxxxxxxxxxxxxx
```

---

## 17. VARIABLES MONITORING

### 17.1 — Sentry

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `SENTRY_DSN` | 🔒 | ✅ | 365j | DSN Sentry (URL secrète) |
| `SENTRY_ENVIRONMENT` | 🟢 | ✅ | — | `production` / `staging` / `development` |
| `SENTRY_RELEASE` | 🟢 | ⚪ | — | Version release (auto-set par CI) |
| `SENTRY_TRACES_SAMPLE_RATE` | 🟢 | ⚪ | — | Performance sampling (`0.1` = 10%) |
| `SENTRY_PROFILES_SAMPLE_RATE` | 🟢 | ⚪ | — | Profiling sampling (`0.1`) |

### 17.2 — GA4

| Variable | Type | Requis | Description |
|---|---|---|---|
| `GA4_MEASUREMENT_ID` | 🟢 | ⚪ | ID GA4 (ex: `G-XXXXXXXXXX`) |

### 17.3 — Mixpanel (ou PostHog EU)

| Variable | Type | Requis | Description |
|---|---|---|---|
| `MIXPANEL_PROJECT_TOKEN` | 🔒 | ⚪ | Token projet Mixpanel |
| `POSTHOG_API_KEY` | 🔒 | ⚪ | Alternative EU (PostHog Cloud EU) |
| `POSTHOG_HOST` | 🟢 | ⚪ | `https://eu.posthog.com` |

### 17.4 — UptimeRobot / Better Uptime

Pas de variables env backend — configuration externe via dashboard.

### 17.5 — Exemple

```bash
SENTRY_DSN=https://xxxxxxxx@o123456.ingest.sentry.io/7890123
SENTRY_ENVIRONMENT=production
SENTRY_TRACES_SAMPLE_RATE=0.1
SENTRY_PROFILES_SAMPLE_RATE=0.1

GA4_MEASUREMENT_ID=G-XXXXXXXXXX

POSTHOG_API_KEY=phc_xxxxxxxxxxxxxxxxxxxxxxxxxxxx
POSTHOG_HOST=https://eu.posthog.com
```

---

## 18. VARIABLES GOOGLE (REVIEWS + PLAY SERVICES)

### 18.1 — Google Business (avis)

| Variable | Type | Requis | Rotation | Description |
|---|---|---|---|---|
| `GOOGLE_OAUTH_CLIENT_ID` | 🟢 | ✅ | — | OAuth client ID Google |
| `GOOGLE_OAUTH_CLIENT_SECRET` | 🔒 | ✅ | 180j | OAuth client secret |
| `GOOGLE_BUSINESS_API_KEY` | 🔒 | ✅ | 90j | Clé API Google My Business |

### 18.2 — Google Play Service Account

Fichier JSON (pas une env var classique) :
- Path : `secrets/play-service-account.json` (gitignored)
- Dans CI : stocké en secret GitHub Actions `PLAY_SERVICE_ACCOUNT_JSON`

### 18.3 — Exemple

```bash
GOOGLE_OAUTH_CLIENT_ID=xxxxxxxxxxxxxxxxxxxxxx.apps.googleusercontent.com
GOOGLE_OAUTH_CLIENT_SECRET=GOCSPX-xxxxxxxxxxxxxxxxxxx
GOOGLE_BUSINESS_API_KEY=AIzaSyXxxxxxxxxxxxxxxxxxxxxxxxxx
```

---

## 19. VARIABLES MOBILE (EXPO)

### 19.1 — Préfixe obligatoire

Toutes les variables côté mobile accessibles dans le JS bundle **DOIVENT** commencer par `EXPO_PUBLIC_`. Expo ignore les autres par sécurité.

### 19.2 — Liste

| Variable | Type | Requis | Description |
|---|---|---|---|
| `EXPO_PUBLIC_API_URL` | 🟢 | ✅ | URL backend API |
| `EXPO_PUBLIC_SUPABASE_URL` | 🟢 | ✅ | URL Supabase |
| `EXPO_PUBLIC_SUPABASE_ANON_KEY` | 🟢 | ✅ | Anon key Supabase |
| `EXPO_PUBLIC_APP_ENV` | 🟢 | ✅ | `production` / `staging` / `development` |
| `EXPO_PUBLIC_STRIPE_PUBLISHABLE_KEY` | 🟢 | ✅ | Publishable key Stripe |
| `EXPO_PUBLIC_CRISP_WEBSITE_ID` | 🟢 | ✅ | Website ID Crisp |
| `EXPO_PUBLIC_SENTRY_DSN` | 🔒* | ✅ | DSN Sentry mobile (exposé, mais safe car limité à ingestion erreurs) |
| `EXPO_PUBLIC_GA4_MEASUREMENT_ID` | 🟢 | ⚪ | GA4 |
| `EXPO_PUBLIC_POSTHOG_API_KEY` | 🔒* | ⚪ | PostHog key (exposé) |
| `EXPO_PUBLIC_POSTHOG_HOST` | 🟢 | ⚪ | PostHog host EU |
| `EXPO_PUBLIC_DEFAULT_LOCALE` | 🟢 | ⚪ | `fr-FR` |
| `EXPO_PUBLIC_WHATSAPP_BUSINESS_PHONE` | 🟢 | ⚪ | Numéro partagé STRUCTORAI (affiché UI) |
| `EXPO_PUBLIC_TRANSPARENCY_URL` | 🟢 | ✅ | URL page `/transparency` (AI Act) |
| `EXPO_PUBLIC_SUPPORT_EMAIL` | 🟢 | ✅ | `support@structorai.app` |

*🔒* = "pseudo-secret" : exposé mais à portée limitée. Les vraies clés secrètes ne vont JAMAIS dans mobile.

### 19.3 — Exemple `.env` mobile

```bash
EXPO_PUBLIC_API_URL=https://api.structorai.app
EXPO_PUBLIC_SUPABASE_URL=https://abcxyzwqer.supabase.co
EXPO_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
EXPO_PUBLIC_APP_ENV=production
EXPO_PUBLIC_STRIPE_PUBLISHABLE_KEY=pk_live_xxx
EXPO_PUBLIC_CRISP_WEBSITE_ID=a8b7c6d5-xxxx
EXPO_PUBLIC_SENTRY_DSN=https://xxx@o123.ingest.sentry.io/456
EXPO_PUBLIC_GA4_MEASUREMENT_ID=G-XXXXXXXXXX
EXPO_PUBLIC_POSTHOG_API_KEY=phc_xxxxxxx
EXPO_PUBLIC_POSTHOG_HOST=https://eu.posthog.com
EXPO_PUBLIC_DEFAULT_LOCALE=fr-FR
EXPO_PUBLIC_WHATSAPP_BUSINESS_PHONE=+33912345678
EXPO_PUBLIC_TRANSPARENCY_URL=https://structorai.app/transparency
EXPO_PUBLIC_SUPPORT_EMAIL=support@structorai.app
```

### 19.4 — Injection au build EAS

`eas.json` → section `build.{profile}.env` → injecte les variables.

**Staging** : pointe vers `api-staging.structorai.app` + projet Supabase staging + Stripe test.
**Production** : pointe vers `api.structorai.app` + projet Supabase prod + Stripe live.

---

## 20. VARIABLES SPÉCIFIQUES AGENTS-WORKERS + CRON-JOBS

### 20.1 — Agents workers

Même variables que backend-api **+** :

| Variable | Type | Requis | Description |
|---|---|---|---|
| `WORKER_CONCURRENCY` | 🟢 | ⚪ | Nb tâches parallèles (défaut: 4) |
| `WORKER_QUEUE_NAMES` | 🟢 | ⚪ | Queues à consommer (défaut: `all`) |
| `WORKER_HEALTH_PORT` | 🟢 | ⚪ | Port health interne (défaut: 8081) |

### 20.2 — Cron jobs

| Variable | Type | Requis | Description |
|---|---|---|---|
| `CRON_CONSCIOUSNESS_INTERVAL_MINUTES` | 🟢 | ⚪ | Fréquence Background Consciousness (défaut: 60) |
| `CRON_RELANCE_CHECK_INTERVAL_MINUTES` | 🟢 | ⚪ | Check devis/factures à relancer (défaut: 360 = 6h) |
| `CRON_COACH_ANALYSIS_DAY_OF_MONTH` | 🟢 | ⚪ | Jour du mois Coach analyse (défaut: 1) |
| `CRON_FISCALITE_CHECK_INTERVAL_HOURS` | 🟢 | ⚪ | Vérif échéances fiscales (défaut: 24) |

### 20.3 — Exemple

```bash
# Agents workers
WORKER_CONCURRENCY=4
WORKER_QUEUE_NAMES=all
WORKER_HEALTH_PORT=8081

# Cron
CRON_CONSCIOUSNESS_INTERVAL_MINUTES=60
CRON_RELANCE_CHECK_INTERVAL_MINUTES=360
CRON_COACH_ANALYSIS_DAY_OF_MONTH=1
CRON_FISCALITE_CHECK_INTERVAL_HOURS=24
```

---

## 21. FICHIER `.ENV.EXAMPLE` COMPLET

Ce fichier doit être dans le repo, **vide de secrets**, pour que Claude Code sache toutes les variables à créer.

**Path** : `backend/.env.example`

```bash
# ==================================================================
# STRUCTORAI — .env.example
# ==================================================================
# Copier ce fichier vers `.env` et remplir les valeurs.
# JAMAIS committer `.env` dans Git.
# Référence complète : docs/ENV.md
# ==================================================================

# === APP ===
APP_ENV=development
APP_DEBUG=true
APP_URL=http://localhost:8000
FRONTEND_URL=http://localhost:19006
MARKETING_URL=http://localhost:3000
CORS_ORIGINS=["http://localhost:19006","http://localhost:3000"]
LOG_LEVEL=DEBUG
LOG_FORMAT=pretty
TIMEZONE=Europe/Paris
DEFAULT_LOCALE=fr-FR
APP_SECRET_KEY=
SESSION_COOKIE_SECURE=false
SESSION_COOKIE_SAMESITE=lax

# === SUPABASE ===
SUPABASE_URL=
SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
SUPABASE_JWT_SECRET=
SUPABASE_DB_URL=

# === REDIS ===
REDIS_URL=redis://localhost:6379
REDIS_POOL_SIZE=10
REDIS_TIMEOUT_SECONDS=5

# === MEMPALACE ===
MEMPALACE_URL=http://localhost:8001
MEMPALACE_API_KEY=
MEMPALACE_BACKUP_ENCRYPTION_KEY=
MEMPALACE_BACKUP_BUCKET=mempalace-backups

# === ANTHROPIC (Claude) ===
ANTHROPIC_API_KEY=
ANTHROPIC_MODEL_OPUS=claude-opus-4-7
ANTHROPIC_MODEL_SONNET=claude-sonnet-4-6
ANTHROPIC_MODEL_HAIKU=claude-haiku-4-5-20251001
ANTHROPIC_MAX_TOKENS_DEFAULT=2000
ANTHROPIC_TIMEOUT_SECONDS=30
ANTHROPIC_STREAM_ENABLED=true

# === MEM0 ===
MEM0_API_KEY=
MEM0_REGION=eu
MEM0_PROJECT_ID=
MEM0_TIMEOUT_SECONDS=10

# === VOICE STT ===
OPENAI_API_KEY=
OPENAI_WHISPER_MODEL=whisper-1
OPENAI_TTS_MODEL=tts-1
DEEPGRAM_API_KEY=
DEEPGRAM_MODEL=nova-2

# === VOICE TTS ===
ELEVENLABS_API_KEY=
ELEVENLABS_MODEL=eleven_multilingual_v2
ELEVENLABS_VOICE_MALE_PRO=pNInz6obpgDQGcFmaJgB
ELEVENLABS_VOICE_FEMALE_PRO=EXAVITQu4vr4xnSDxMaL
ELEVENLABS_VOICE_MALE_CASUAL=ErXwobaYiN019PkySvjV
ELEVENLABS_VOICE_FEMALE_CASUAL=21m00Tcm4TlvDq8ikWAM

# === WHATSAPP ===
WHATSAPP_PHONE_NUMBER_ID=
WHATSAPP_BUSINESS_ACCOUNT_ID=
WHATSAPP_ACCESS_TOKEN=
WHATSAPP_VERIFY_TOKEN=
WHATSAPP_APP_SECRET=
WHATSAPP_API_VERSION=v20.0
WHATSAPP_WEBHOOK_URL=http://localhost:8000/v1/whatsapp/webhook

# === STRIPE ===
STRIPE_SECRET_KEY=sk_test_
STRIPE_PUBLISHABLE_KEY=pk_test_
STRIPE_WEBHOOK_SECRET=whsec_
STRIPE_API_VERSION=2024-12-18.acacia
STRIPE_PRICE_PRO_MONTHLY=
STRIPE_PRICE_PRO_ANNUAL=
STRIPE_PRICE_BUSINESS_MONTHLY=
STRIPE_PRICE_BUSINESS_ANNUAL=
STRIPE_PRICE_LIFETIME=
STRIPE_PRICE_ADDON_TELEPHONE=
STRIPE_COUPON_CAPEB20=
STRIPE_COUPON_FFB20=

# === TWILIO ===
TWILIO_ACCOUNT_SID=
TWILIO_AUTH_TOKEN=
TWILIO_PHONE_NUMBER=
TWILIO_MESSAGING_SERVICE_SID=
TWILIO_VOICE_PHONE_NUMBER=

# === BREVO (EMAIL) ===
BREVO_API_KEY=
BREVO_SENDER_EMAIL=noreply@structorai.app
BREVO_SENDER_NAME=STRUCTORAI
BREVO_REPLY_TO_EMAIL=support@structorai.app
BREVO_LIST_ID_NEWSLETTER=
BREVO_TEMPLATE_WELCOME=
BREVO_TEMPLATE_OTP=
BREVO_TEMPLATE_DEVIS_ENVOYE=
BREVO_TEMPLATE_FACTURE_ENVOYEE=
BREVO_TEMPLATE_RELANCE_DEVIS=
BREVO_TEMPLATE_RELANCE_FACTURE=
BREVO_TEMPLATE_CONFIRMATION_SIGNATURE=
BREVO_TEMPLATE_RESET_PASSWORD=

# === YOUSIGN (SIGNATURE) ===
YOUSIGN_API_KEY=
YOUSIGN_API_URL=https://staging-api.yousign.app/v3
YOUSIGN_WEBHOOK_SECRET=
YOUSIGN_DEFAULT_SIGNATURE_LEVEL=electronic_signature

# === FACTUR-X ===
FACTURX_PA_PROVIDER=factpulse
FACTURX_PA_API_KEY=
FACTURX_PA_API_URL=
FACTURX_PA_SIRET=
FACTURX_DEFAULT_PROFILE=EXTENDED_CIUS_CONSTRUCTION

# === CRISP ===
CRISP_WEBSITE_ID=
CRISP_API_IDENTIFIER=
CRISP_API_KEY=
CRISP_WEBHOOK_SECRET=

# === MONITORING ===
SENTRY_DSN=
SENTRY_ENVIRONMENT=development
SENTRY_TRACES_SAMPLE_RATE=0.1
SENTRY_PROFILES_SAMPLE_RATE=0.1
GA4_MEASUREMENT_ID=
POSTHOG_API_KEY=
POSTHOG_HOST=https://eu.posthog.com

# === GOOGLE ===
GOOGLE_OAUTH_CLIENT_ID=
GOOGLE_OAUTH_CLIENT_SECRET=
GOOGLE_BUSINESS_API_KEY=

# === WORKERS & CRON ===
WORKER_CONCURRENCY=4
WORKER_QUEUE_NAMES=all
WORKER_HEALTH_PORT=8081
CRON_CONSCIOUSNESS_INTERVAL_MINUTES=60
CRON_RELANCE_CHECK_INTERVAL_MINUTES=360
CRON_COACH_ANALYSIS_DAY_OF_MONTH=1
CRON_FISCALITE_CHECK_INTERVAL_HOURS=24
```

---

## 22. ROTATION DES SECRETS

### 22.1 — Calendrier de rotation

Voir `SECURITE.md` pour détails. Résumé :

| Fréquence | Secrets concernés |
|---|---|
| **60j** (stricte) | `WHATSAPP_ACCESS_TOKEN` (expiration Meta) |
| **90j** | `ANTHROPIC_API_KEY`, `SUPABASE_SERVICE_ROLE_KEY`, `STRIPE_SECRET_KEY`, `OPENAI_API_KEY`, `DEEPGRAM_API_KEY`, `ELEVENLABS_API_KEY`, `TWILIO_AUTH_TOKEN`, `BREVO_API_KEY`, `GOOGLE_BUSINESS_API_KEY`, `FACTURX_PA_API_KEY` |
| **180j** | `APP_SECRET_KEY`, `SUPABASE_JWT_SECRET`, `WHATSAPP_VERIFY_TOKEN`, `WHATSAPP_APP_SECRET`, `STRIPE_WEBHOOK_SECRET`, `YOUSIGN_API_KEY`, `YOUSIGN_WEBHOOK_SECRET`, `MEM0_API_KEY`, `MEMPALACE_API_KEY`, `CRISP_*_SECRET`, `GOOGLE_OAUTH_CLIENT_SECRET` |
| **365j** | `SENTRY_DSN`, `MEMPALACE_BACKUP_ENCRYPTION_KEY` |
| **Événementiel** | Compromission, licenciement équipe, etc. → rotation immédiate |

### 22.2 — Procédure rotation

```
1. Générer nouveau secret (dashboard provider)
2. Ajouter en env var Railway (ajout + conservation ancien)
3. Redéployer backend avec ancien secret encore actif
4. Valider fonctionnement avec nouveau secret
5. Supprimer ancien secret du provider
6. Logguer rotation dans docs/DECISIONS-LOG.md
```

### 22.3 — Script automatisation

`scripts/rotate_secrets.sh` — script semi-automatique pour rotation :
- Rappels via cron (email Fabrice 7j avant expiration)
- Automatisation partielle via Railway API
- Audit trail complet

### 22.4 — WhatsApp auto-refresh (spécifique)

Script cron `scripts/rotate_whatsapp_token.py` :
- Lancé tous les 55 jours
- Renouvelle `WHATSAPP_ACCESS_TOKEN` via Meta Graph API
- Update Railway env var automatiquement
- Alerte Slack si échec

---

## 23. DIFFÉRENCES STAGING VS PRODUCTION

### 23.1 — Tableau des différences clés

| Variable | Staging | Production |
|---|---|---|
| `APP_ENV` | `staging` | `production` |
| `APP_DEBUG` | `true` (pour logs riches) | `false` |
| `APP_URL` | `https://api-staging.structorai.app` | `https://api.structorai.app` |
| `FRONTEND_URL` | `https://app-staging.structorai.app` | `https://app.structorai.app` |
| `STRIPE_SECRET_KEY` | `sk_test_*` | `sk_live_*` |
| `STRIPE_WEBHOOK_SECRET` | `whsec_*` (test) | `whsec_*` (live) |
| `SUPABASE_URL` | Projet staging | Projet production |
| `SUPABASE_*_KEY` | Clés staging | Clés production |
| `WHATSAPP_PHONE_NUMBER_ID` | Sandbox Meta | Numéro live |
| `WHATSAPP_ACCESS_TOKEN` | Token sandbox | Token live |
| `ANTHROPIC_API_KEY` | Clé avec budget limité | Clé production |
| `YOUSIGN_API_URL` | `https://staging-api.yousign.app/v3` | `https://api.yousign.app/v3` |
| `SENTRY_ENVIRONMENT` | `staging` | `production` |
| `SENTRY_TRACES_SAMPLE_RATE` | `1.0` (tout) | `0.1` (10%) |
| `LOG_LEVEL` | `DEBUG` | `INFO` |
| `EXPO_PUBLIC_APP_ENV` | `staging` | `production` |

### 23.2 — Séparation stricte

- **Clés staging jamais utilisées en prod** (et vice-versa)
- **Databases distinctes** : pas de leak data staging ↔ prod
- **Stripe séparé** : mode test vs live
- **Numéros WhatsApp distincts**

### 23.3 — Tests avec data prod

**INTERDIT** de copier data prod → staging (RGPD).

Pour tester avec data réaliste :
- `scripts/seed_staging.py` → génère data synthétique
- Artisans beta-testeurs consentants uniquement

---

## 24. ANTI-PATTERNS ET RÈGLES SÉCURITÉ

### 24.1 — ❌ Anti-patterns

**❌ JAMAIS faire ces erreurs** :

1. ❌ Commiter `.env` dans Git
2. ❌ Hardcoder un secret dans le code source
3. ❌ Logguer la valeur d'un secret (même en debug)
4. ❌ Utiliser `SUPABASE_SERVICE_ROLE_KEY` côté client/mobile
5. ❌ Exposer des variables sans préfixe `EXPO_PUBLIC_` côté mobile
6. ❌ Partager un secret par email/Slack/SMS (utiliser 1Password ou secret manager)
7. ❌ Copier data prod vers staging
8. ❌ Utiliser `sk_live_*` en staging
9. ❌ Oublier de révoquer ancien secret après rotation
10. ❌ Mettre une valeur par défaut silencieuse pour un secret

### 24.2 — ✅ Bonnes pratiques

1. ✅ `.env.example` à jour dans le repo (sans valeurs)
2. ✅ Validation Pydantic strict au démarrage (`extra="forbid"`)
3. ✅ Scrubbing Sentry pour éviter leak secrets dans stack traces
4. ✅ `detect-secrets` en pre-commit hook
5. ✅ Rotation régulière documentée
6. ✅ 1Password vault partagé pour secrets dev
7. ✅ GitHub Actions Secrets pour CI/CD
8. ✅ Railway Environment Variables pour runtime (encrypted at rest)
9. ✅ Audit trail des modifications env vars
10. ✅ Rollback testé en cas de secret cassé

### 24.3 — Scrubbing Sentry

Fichier `backend/app/config.py` :

```python
SENTRY_DENYLIST = [
    "ANTHROPIC_API_KEY",
    "OPENAI_API_KEY",
    "DEEPGRAM_API_KEY",
    "ELEVENLABS_API_KEY",
    "STRIPE_SECRET_KEY",
    "STRIPE_WEBHOOK_SECRET",
    "TWILIO_AUTH_TOKEN",
    "BREVO_API_KEY",
    "WHATSAPP_ACCESS_TOKEN",
    "WHATSAPP_APP_SECRET",
    "WHATSAPP_VERIFY_TOKEN",
    "YOUSIGN_API_KEY",
    "YOUSIGN_WEBHOOK_SECRET",
    "SUPABASE_SERVICE_ROLE_KEY",
    "SUPABASE_JWT_SECRET",
    "MEM0_API_KEY",
    "MEMPALACE_API_KEY",
    "MEMPALACE_BACKUP_ENCRYPTION_KEY",
    "APP_SECRET_KEY",
    "REDIS_URL",         # contient password
    "SUPABASE_DB_URL",   # contient password
    "CRISP_API_KEY",
    "FACTURX_PA_API_KEY",
    "GOOGLE_OAUTH_CLIENT_SECRET",
    "GOOGLE_BUSINESS_API_KEY",
]
```

### 24.4 — Pre-commit hook

`.pre-commit-config.yaml` :

```yaml
repos:
  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.5.0
    hooks:
      - id: detect-secrets
        args: ['--baseline', '.secrets.baseline']
```

---

## 25. TROUBLESHOOTING ENV VARS

### 25.1 — Problèmes fréquents

| Symptôme | Cause probable | Solution |
|---|---|---|
| Backend refuse de démarrer | Variable critique manquante | Check `config.py` vs env Railway |
| `WHATSAPP_ACCESS_TOKEN expired` | Rotation 60j manquée | Renouveler via Meta + script |
| `Invalid JWT signature` | Mismatch `SUPABASE_JWT_SECRET` | Vérifier même clé backend + Supabase |
| Mobile ne charge pas config | Variable pas préfixée `EXPO_PUBLIC_` | Renommer + rebuild EAS |
| CI échoue auth Railway | `RAILWAY_TOKEN` expiré | Régénérer + update GitHub Secrets |
| Stripe webhook 400 | `STRIPE_WEBHOOK_SECRET` erroné | Re-copier depuis Stripe dashboard |
| Claude appels échouent | `ANTHROPIC_API_KEY` révoquée | Nouvelle clé + deploy |
| Sentry ne reçoit rien | `SENTRY_DSN` mal configuré | Vérifier env + dashboard Sentry |

### 25.2 — Debug commands

```bash
# Liste env vars Railway
railway variables --service backend-api

# Test chargement config
cd backend
python -c "from app.config import settings; print(settings.APP_ENV)"

# Vérifier .env sans committer
git status .env     # doit retourner untracked ou ignored

# Test Stripe webhook signature
stripe listen --forward-to localhost:8000/v1/webhooks/stripe

# Test OTP WhatsApp
curl -X POST http://localhost:8000/v1/test/otp \
  -H "Content-Type: application/json" \
  -d '{"phone": "+33612345678"}'
```

### 25.3 — Logs d'erreurs typiques

**Backend ne démarre pas** :
```
ValidationError: 1 validation error for Settings
ANTHROPIC_API_KEY
  field required
```
→ Variable manquante. Check Railway env vars.

**Runtime 502 sur appel LLM** :
```
anthropic.AuthenticationError: Invalid API key
```
→ Clé révoquée/expirée. Regénérer.

**Webhook Stripe 400** :
```
stripe.error.SignatureVerificationError
```
→ `STRIPE_WEBHOOK_SECRET` incorrect.

---

## 26. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/ARCH.md` | Architecture (services Railway) |
| `docs/DEPLOY.md` §8 | Déploiement + env vars par env |
| `docs/API.md` | Endpoints qui utilisent ces secrets |
| `docs/AGENTS.md` | Modèles Claude exacts |
| `docs/VOICE.md` | Whisper + ElevenLabs + Deepgram détails |
| `docs/WHATSAPP.md` | Meta Cloud API config |
| `docs/MEMORY.md` | Mem0 + MemPalace |
| `docs/MEMORY-STRATEGY.md` | Backup encryption |
| `docs/SUPPORT-STRATEGY.md` | Crisp |
| `docs/AI-ACT-COMPLIANCE.md` | Rétention secrets audit |
| `docs/METIER.md` | Factur-X |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` | §9 modèles Claude, §7 Stripe prices |
| `SECURITE.md` | Secrets management complet |
| `BUILD_PLAN.md` | Sprint 0 setup comptes |
| `COUT_REEL.md` | Coûts par service (Stripe, ElevenLabs, etc.) |

### 26.1 — Références externes

- **Pydantic Settings** : https://docs.pydantic.dev/latest/concepts/pydantic_settings/
- **Expo EAS env vars** : https://docs.expo.dev/build-reference/variables/
- **Railway env vars** : https://docs.railway.app/guides/variables
- **detect-secrets** : https://github.com/Yelp/detect-secrets

---

> **Ce fichier est la source de vérité des variables d'environnement.**
> **Règle d'or n°1** : AUCUN secret jamais commité dans Git.
> **Règle d'or n°2** : Rotation selon calendrier §22, sans exception.
> **Règle d'or n°3** : `.env.example` à jour à chaque ajout de variable.
> **~120 variables backend** + **~15 variables mobile**. **~65 secrets à rotater**.
