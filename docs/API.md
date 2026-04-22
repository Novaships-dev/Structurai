---
name: API
description: Spécification exhaustive de l'API REST STRUCTORAI. Tous les endpoints /v1/* avec méthode HTTP, auth requise, payload request/response Pydantic, codes d'erreur, rate limits par plan. WebSocket pour chat temps réel. Webhooks Stripe + Meta WhatsApp. Conventions de nommage, versioning, pagination. Endpoints admin réservés Fabrice.
type: technical-api-spec
scope: backend-gateway, mobile-app, integrations
priority: critical
date: 2026-04-18
version: 1.0
base_url_prod: https://api.structorai.app
base_url_staging: https://api-staging.structorai.app
openapi_spec: /v1/openapi.json (auto-généré FastAPI)
swagger_ui: /v1/docs (production désactivée, staging activée)
---

# docs/API.md — Spécification API REST STRUCTORAI

> **Ce fichier est la source de vérité des endpoints.**
> Toute modification d'endpoint DOIT mettre à jour ce fichier.
> La spec OpenAPI est auto-générée par FastAPI à `/v1/openapi.json`.
> Reference architecture : `docs/ARCH.md` §4 Gateway FastAPI.

---

## 0. SOMMAIRE

1. [Conventions générales](#1-conventions-générales)
2. [Authentification](#2-authentification)
3. [Rate limiting par plan](#3-rate-limiting-par-plan)
4. [Quotas et plan limits](#4-quotas-et-plan-limits)
5. [Gestion des erreurs (StructorAIError)](#5-gestion-des-erreurs-structoraierror)
6. [Health et monitoring](#6-health-et-monitoring)
7. [Endpoints Chat (texte + vocal)](#7-endpoints-chat-texte--vocal)
8. [Endpoints Devis](#8-endpoints-devis)
9. [Endpoints Factures](#9-endpoints-factures)
10. [Endpoints Chantiers](#10-endpoints-chantiers)
11. [Endpoints Clients (CRM)](#11-endpoints-clients-crm)
12. [Endpoints Tickets (OCR)](#12-endpoints-tickets-ocr)
13. [Endpoints Contacts Pro](#13-endpoints-contacts-pro)
14. [Endpoints Photos chantier](#14-endpoints-photos-chantier)
15. [Endpoints Emails Pro](#15-endpoints-emails-pro)
16. [Endpoints Avis Google](#16-endpoints-avis-google)
17. [Endpoints Stats (Dashboard)](#17-endpoints-stats-dashboard)
18. [Endpoints Billing (Stripe)](#18-endpoints-billing-stripe)
19. [Endpoints Onboarding + Gamification](#19-endpoints-onboarding--gamification)
20. [Endpoints Settings](#20-endpoints-settings)
21. [Endpoints Support (F136)](#21-endpoints-support-f136)
22. [Endpoints Coach Business (F119)](#22-endpoints-coach-business-f119)
23. [Endpoints Fiscalité](#23-endpoints-fiscalité)
24. [Endpoints Déplacements](#24-endpoints-déplacements)
25. [Endpoints RH](#25-endpoints-rh)
26. [Endpoints Site Web (F74)](#26-endpoints-site-web-f74)
27. [Endpoints Sandbox publique (F115)](#27-endpoints-sandbox-publique-f115)
28. [Webhooks (Stripe, Meta WhatsApp, Yousign)](#28-webhooks-stripe-meta-whatsapp-yousign)
29. [WebSocket (chat temps réel)](#29-websocket-chat-temps-réel)
30. [Endpoints admin (Fabrice)](#30-endpoints-admin-fabrice)
31. [Sync hors-ligne (batch)](#31-sync-hors-ligne-batch)
32. [Pagination, tri, filtrage](#32-pagination-tri-filtrage)
33. [Versioning et deprecation](#33-versioning-et-deprecation)
34. [Références croisées](#34-références-croisées)

---

## 1. CONVENTIONS GÉNÉRALES

### 1.1 — URLs

- **Production** : `https://api.structorai.app`
- **Staging** : `https://api-staging.structorai.app`
- **Base path** : `/v1/` (toutes les routes sont préfixées)

### 1.2 — Méthodes HTTP

| Méthode | Usage | Idempotent |
|---|---|---|
| `GET` | Lecture ressource ou liste | ✅ |
| `POST` | Création + actions non-CRUD (send, sign, scan, etc.) | ❌ |
| `PATCH` | Modification partielle | ⚠️ (logique métier) |
| `PUT` | Remplacement complet (rare) | ✅ |
| `DELETE` | Suppression | ✅ |

**Convention** : on évite `PUT`. On utilise `PATCH` pour modifier, `POST` pour créer ou agir.

### 1.3 — Format

- **Content-Type** : `application/json` par défaut
- **Multipart** : `multipart/form-data` uniquement pour upload fichiers (photos, audio, tickets)
- **Charset** : UTF-8 partout
- **Dates** : ISO 8601 avec timezone (ex: `2026-04-18T14:32:05+02:00`)
- **Montants** : en centimes pour éviter les flottants (ex: 12 500 = 125,00 €)
- **IDs** : UUID v4 (ex: `550e8400-e29b-41d4-a716-446655440000`)

### 1.4 — Headers obligatoires

```
Authorization: Bearer <supabase_jwt_token>
Content-Type: application/json
X-Client-Version: 1.0.0                 # Version app mobile (pour deprecation)
X-Request-ID: <uuid>                    # Optionnel, sinon généré backend
Accept-Language: fr-FR                  # FR / EN / TR / PT / AR / ES
```

### 1.5 — Response enveloppe standard

**Succès** :
```json
{
  "data": { ... },
  "meta": {
    "request_id": "550e8400-...",
    "timestamp": "2026-04-18T14:32:05+02:00"
  }
}
```

**Liste paginée** :
```json
{
  "data": [ ... ],
  "meta": {
    "request_id": "550e8400-...",
    "timestamp": "2026-04-18T14:32:05+02:00",
    "pagination": {
      "page": 1,
      "per_page": 20,
      "total": 145,
      "total_pages": 8
    }
  }
}
```

**Erreur** (voir §5) :
```json
{
  "error": {
    "code": "DEVIS_NOT_FOUND",
    "message": "Le devis demandé n'existe pas ou ne t'appartient pas",
    "details": { "devis_id": "xxx" },
    "trace_id": "550e8400-..."
  }
}
```

### 1.6 — Codes HTTP utilisés

| Code | Usage |
|---|---|
| 200 | Succès GET / PATCH / DELETE |
| 201 | Succès POST (création) |
| 202 | Accepté, traitement async (sync batch, génération devis) |
| 204 | Succès sans contenu retour (rare) |
| 400 | Erreur de validation (payload invalide) |
| 401 | Non authentifié (pas de token ou token invalide) |
| 402 | Paiement requis (plan expiré, grâce period dépassée) |
| 403 | Interdit (accès à une ressource d'une autre org) |
| 404 | Ressource non trouvée |
| 409 | Conflit (ex: devis déjà signé, action impossible) |
| 422 | Semantique invalide (ex: TVA incohérente avec statut) |
| 429 | Rate limit dépassé |
| 500 | Erreur serveur (bug interne) |
| 502 | Bad Gateway (Claude API down, fallback actif) |
| 503 | Service indisponible (maintenance programmée) |
| 504 | Timeout (LLM ou Supabase > 30s) |

---

## 2. AUTHENTIFICATION

### 2.1 — Supabase Auth (JWT)

**Flow** :
1. Mobile app → Supabase Auth SDK → login email/password ou Google OAuth
2. Supabase retourne `access_token` (JWT, 1h TTL) + `refresh_token` (30 jours)
3. Mobile stocke dans `expo-secure-store` (Keychain iOS / Keystore Android)
4. Chaque requête : `Authorization: Bearer <access_token>`
5. Middleware `auth.py` vérifie signature + expiration + extrait `user.id`, `org_id`, `plan`

**Refresh automatique** : le mobile intercepte 401, appelle Supabase `refreshSession()`, retry.

### 2.2 — API Keys (plan Business uniquement)

Pour intégrations tierces (ex: ERP artisan, Pennylane custom, etc.).

**Création** :
```
POST /v1/settings/api-keys
```

Request :
```json
{
  "name": "Integration Pennylane",
  "scopes": ["devis:read", "factures:read", "clients:read"]
}
```

Response (201) :
```json
{
  "data": {
    "id": "ak_...",
    "key": "sk_live_xxxxx",              // Affiché 1 seule fois
    "key_prefix": "sk_live_abc...",      // Visible ensuite
    "name": "Integration Pennylane",
    "scopes": ["devis:read", "factures:read", "clients:read"],
    "created_at": "2026-04-18T...",
    "last_used_at": null,
    "revoked": false
  }
}
```

**Usage** : header `X-API-Key: sk_live_xxxxx` au lieu du JWT.

**Scopes disponibles** :
- `devis:read` / `devis:write`
- `factures:read` / `factures:write`
- `clients:read` / `clients:write`
- `chantiers:read` / `chantiers:write`
- `stats:read`

### 2.3 — Endpoints publics (pas d'auth)

- `GET /health`
- `POST /v1/public/chat` (F115 Sandbox publique, rate-limited par IP)
- Webhooks : `/v1/webhooks/stripe`, `/v1/whatsapp/webhook`, `/v1/webhooks/yousign` (signature HMAC vérifiée)

---

## 3. RATE LIMITING PAR PLAN

### 3.1 — Limites de requêtes

| Plan | Req/min | Req/jour | Burst |
|---|---|---|---|
| Starter | 20 | 500 | 30 |
| Pro | 60 | 5 000 | 100 |
| Pro annuel | 60 | 5 000 | 100 |
| Business | 200 | 20 000 | 300 |
| Business annuel | 200 | 20 000 | 300 |
| Lifetime | 60 (= Pro) | 5 000 | 100 |
| Sandbox public F115 | 10 (par IP) | 30 (par IP) | 15 |

### 3.2 — Headers de réponse

Chaque réponse inclut :
```
X-RateLimit-Limit: 60
X-RateLimit-Remaining: 42
X-RateLimit-Reset: 1745235000
```

### 3.3 — Dépassement (429)

```json
{
  "error": {
    "code": "RATE_LIMIT_EXCEEDED",
    "message": "Tu as envoyé trop de requêtes. Réessaie dans 30 secondes.",
    "details": {
      "limit": 60,
      "retry_after_seconds": 30,
      "plan": "pro"
    }
  }
}
```

### 3.4 — Implementation

Backend : `backend/app/middleware/rate_limit.py` (Redis-based, sliding window).

---

## 4. QUOTAS ET PLAN LIMITS

Ces limites sont vérifiées par `middleware/plan_limits.py` AVANT d'entrer dans les endpoints.

### 4.1 — Limites fonctionnelles par plan

| Limite | Starter | Pro | Business | Lifetime |
|---|---|---|---|---|
| Devis créés / mois | **5** | ∞ | ∞ | ∞ |
| Factures créées / mois | 5 | ∞ | ∞ | ∞ |
| Chantiers actifs | 3 | ∞ | ∞ | ∞ |
| Clients dans CRM | 20 | ∞ | ∞ | ∞ |
| Photos / chantier | 10 | 100 | ∞ | 100 |
| Avis Google auto-répondus / mois | 2 | 50 | ∞ | 50 |
| Relances auto activées | ❌ | ✅ | ✅ | ✅ |
| Agent Coach analyse / mois | **1** | ∞ | ∞ | ∞ |
| Agent Site Web | ❌ | ✅ | ✅ | ✅ |
| Multi-user (salariés) | ❌ | ❌ | **Jusqu'à 10** | ❌ |
| API keys (intégrations) | ❌ | ❌ | ✅ | ❌ |
| Export comptable mensuel | ❌ | ✅ | ✅ | ✅ |

### 4.2 — Budget LLM quotidien par plan

Voir `docs/SINGLE_SOURCE_OF_TRUTH.md` §4 (Budget LLM Supervisor) :

| Plan | Cap quotidien LLM |
|---|---|
| Starter | $0.50/jour |
| Pro / Pro annuel | $2.00/jour |
| Business / Business annuel | $5.00/jour |
| Lifetime | $2.00/jour (= Pro) |

Au-delà du cap → réponse `429 LLM_BUDGET_EXCEEDED` jusqu'au reset 00h Paris.

### 4.3 — Dépassement quota fonctionnel (402)

```json
{
  "error": {
    "code": "PLAN_LIMIT_REACHED",
    "message": "Tu as atteint ta limite de 5 devis/mois sur le plan Starter. Upgrade vers Pro pour en créer sans limite.",
    "details": {
      "limit_type": "devis_per_month",
      "current": 5,
      "limit": 5,
      "plan": "starter",
      "upgrade_url": "https://structorai.app/billing/upgrade"
    }
  }
}
```

---

## 5. GESTION DES ERREURS (STRUCTORAIERROR)

### 5.1 — Classe de base

**Fichier** : `backend/app/utils/errors.py` (pattern AgentShield)

```python
class StructorAIError(Exception):
    code: str                    # Ex: "DEVIS_NOT_FOUND"
    http_status: int             # Ex: 404
    message_fr: str              # Message utilisateur en français
    message_en: str              # English fallback
    details: dict                # Contexte technique
    retry_able: bool             # Si True → retry possible
    log_level: str               # info / warning / error / critical
```

### 5.2 — Codes d'erreur (extraits)

Liste complète dans `docs/ERRORS.md` (à créer). Résumé des plus fréquents :

**Auth (401 / 403)**
- `AUTH_TOKEN_MISSING` — pas de token dans header
- `AUTH_TOKEN_INVALID` — JWT signature invalide
- `AUTH_TOKEN_EXPIRED` — JWT expiré (refresh requis)
- `AUTH_API_KEY_REVOKED` — API key révoquée
- `FORBIDDEN_RESOURCE` — accès à ressource autre org

**Validation (400 / 422)**
- `VALIDATION_ERROR` — Pydantic validation fail
- `INVALID_TVA_RATE` — taux TVA non autorisé (5,5 / 10 / 20)
- `INVALID_SIRET` — SIRET au mauvais format
- `INVALID_IBAN` — IBAN invalide
- `MISSING_MANDATORY_MENTION` — mention obligatoire manquante sur devis/facture

**Business logic (409)**
- `DEVIS_ALREADY_SIGNED` — impossible de modifier un devis signé
- `FACTURE_ALREADY_PAID` — impossible de modifier une facture payée
- `CHANTIER_ALREADY_CLOSED` — chantier fermé
- `CONCURRENT_MODIFICATION` — conflit write simultané

**Ressource (404)**
- `DEVIS_NOT_FOUND`
- `FACTURE_NOT_FOUND`
- `CLIENT_NOT_FOUND`
- `CHANTIER_NOT_FOUND`

**Quota (402 / 429)**
- `PLAN_LIMIT_REACHED` — quota fonctionnel atteint
- `RATE_LIMIT_EXCEEDED` — trop de requêtes
- `LLM_BUDGET_EXCEEDED` — cap quotidien LLM dépassé

**Infrastructure (502 / 503 / 504)**
- `LLM_PROVIDER_DOWN` — Claude/Anthropic indisponible, fallback actif
- `OCR_FAILED` — Vision Claude échec
- `STT_FAILED` — Whisper échec
- `TTS_FAILED` — ElevenLabs échec
- `WHATSAPP_API_DOWN` — Meta Cloud API indisponible
- `STRIPE_API_DOWN` — Stripe indisponible
- `MAINTENANCE_MODE` — maintenance programmée

### 5.3 — Format de réponse

Toujours :
```json
{
  "error": {
    "code": "DEVIS_NOT_FOUND",
    "message": "Le devis demandé n'existe pas ou ne t'appartient pas",
    "details": { "devis_id": "xxx" },
    "trace_id": "550e8400-...",
    "retry_able": false,
    "retry_after_seconds": null
  }
}
```

### 5.4 — Logging

Chaque erreur est loggée avec :
- Niveau (info/warning/error/critical)
- Sentry event si `error` ou `critical`
- trace_id = request_id

---

## 6. HEALTH ET MONITORING

### 6.1 — `GET /health`

**Auth** : aucune (endpoint public)

**Response (200)** :
```json
{
  "status": "healthy",
  "version": "1.2.3",
  "environment": "production",
  "timestamp": "2026-04-18T14:32:05+02:00",
  "services": {
    "database": "up",
    "redis": "up",
    "supabase_storage": "up",
    "claude_api": "up",
    "whisper_api": "up",
    "elevenlabs_api": "up",
    "stripe": "up",
    "mem0": "up",
    "mempalace": "up"
  }
}
```

**Response (503 si dégradé)** :
```json
{
  "status": "degraded",
  "services": {
    "mempalace": "down",
    "mem0": "up",
    ...
  },
  "degraded_mode": "memory_readonly"
}
```

### 6.2 — `GET /v1/status` (auth)

Détail par service pour dashboards utilisateurs.

### 6.3 — `GET /metrics` (Prometheus format)

Auth : seulement avec token admin ou IP allowlistée.

---

## 7. ENDPOINTS CHAT (TEXTE + VOCAL)

### 7.1 — `POST /v1/chat`

Envoyer un message texte au cerveau IA (Supervisor).

**Auth** : JWT requis

**Request** :
```json
{
  "message": "Fais-moi un devis pour la rénovation SDB du Martin",
  "context": {
    "current_screen": "chantiers/detail",
    "chantier_id": "550e8400-...",
    "locale": "fr-FR"
  },
  "conversation_id": "conv_abc123"        // Optionnel, pour continuité
}
```

**Response (200)** :
```json
{
  "data": {
    "message_id": "msg_xyz",
    "conversation_id": "conv_abc123",
    "response": {
      "type": "text",
      "content": "J'ai commencé un devis pour Martin. Dimensions de la SDB ?",
      "confidence": "high",
      "agent": "devis",
      "model": "claude-opus-4-7",
      "actions_proposed": [
        {
          "id": "act_001",
          "type": "create_devis",
          "summary": "Créer devis SDB Martin",
          "requires_validation": true
        }
      ]
    },
    "metadata": {
      "latency_ms": 2450,
      "tokens_input": 1850,
      "tokens_output": 180,
      "cost_usd": 0.014
    }
  }
}
```

**Erreurs possibles** : `LLM_BUDGET_EXCEEDED`, `LLM_PROVIDER_DOWN`, `RATE_LIMIT_EXCEEDED`.

### 7.2 — `POST /v1/chat/audio`

Upload audio → Whisper STT → cerveau → réponse (optionnellement TTS).

**Auth** : JWT requis

**Content-Type** : `multipart/form-data`

**Request fields** :
- `audio` (file) — m4a/mp3/ogg/webm, max 10 Mo, max 3 min
- `context` (JSON) — même format que §7.1
- `conversation_id` (string, optionnel)
- `response_mode` (enum) — `text` / `voice` / `both` (default: `both`)
- `voice` (enum) — `male_pro` / `female_pro` / `male_casual` / `female_casual` (default: préférence user)

**Response (200)** :
```json
{
  "data": {
    "message_id": "msg_xyz",
    "conversation_id": "conv_abc123",
    "transcript": "Fais-moi un devis pour la rénovation SDB du Martin",
    "transcript_confidence": 0.94,
    "detected_language": "fr",
    "response": {
      "type": "voice_and_text",
      "content": "J'ai commencé un devis pour Martin. Dimensions de la SDB ?",
      "audio_url": "https://storage.structorai.app/tts/xyz.mp3",
      "audio_duration_ms": 3200,
      "confidence": "high",
      "agent": "devis",
      "model": "claude-opus-4-7"
    },
    "metadata": {
      "stt_latency_ms": 850,
      "llm_latency_ms": 1200,
      "tts_latency_ms": 400,
      "total_latency_ms": 2450,
      "cost_usd": 0.042
    }
  }
}
```

### 7.3 — `GET /v1/chat/conversations`

Lister les conversations récentes.

**Query params** :
- `page` (int, default 1)
- `per_page` (int, default 20, max 100)
- `since` (ISO date, optionnel)

**Response** : liste paginée de `ConversationSummary`.

### 7.4 — `GET /v1/chat/conversations/{id}`

Récupérer l'historique d'une conversation.

### 7.5 — `DELETE /v1/chat/conversations/{id}`

Supprimer conversation (+ purge Mem0/MemPalace, voir RGPD).

---

## 8. ENDPOINTS DEVIS

### 8.1 — `POST /v1/devis`

Créer un devis manuellement (alternative au vocal).

**Request** :
```json
{
  "client_id": "550e8400-...",
  "chantier_id": "550e8400-...",       // Optionnel
  "intitule": "Rénovation salle de bain complète",
  "adresse_chantier": "12 rue de la République, 75001 Paris",
  "lines": [
    {
      "designation": "Dépose ancienne baignoire + évacuation",
      "quantite": 1,
      "unite": "forfait",
      "prix_unitaire_ht": 25000,        // En centimes (250,00 €)
      "tva_rate": 10.0,
      "poste": "plomberie"
    },
    {
      "designation": "Pose receveur douche italienne 120x90",
      "quantite": 1,
      "unite": "ensemble",
      "prix_unitaire_ht": 85000,
      "tva_rate": 10.0,
      "poste": "plomberie"
    }
  ],
  "remise_globale_pct": 5,              // Optionnel
  "conditions_paiement": "30% à la commande, 40% en cours de chantier, 30% à la fin",
  "validite_jours": 30,
  "notes_client": "Travaux estimés 3 semaines",
  "mentions_supplementaires": []
}
```

**Response (201)** :
```json
{
  "data": {
    "id": "dev_abc123",
    "numero": "DEV-2026-042",
    "status": "draft",
    "total_ht": 110000,
    "total_tva": 11000,
    "total_ttc": 121000,
    "tva_breakdown": [
      {"rate": 10.0, "base_ht": 110000, "montant": 11000}
    ],
    "mentions_appliquees": ["TVA_10_RENOVATION", "RGE_NON_REQUIRED", "..."],
    "confidence_score": "high",
    "created_at": "2026-04-18T...",
    "pdf_url": null                     // null tant que non généré
  }
}
```

### 8.2 — `GET /v1/devis`

Liste paginée.

**Query params** :
- `page`, `per_page`
- `status` : `draft` / `sent` / `signed` / `accepted` / `refused` / `expired`
- `client_id`
- `chantier_id`
- `date_from`, `date_to`
- `search` (full-text sur numéro ou intitulé)
- `sort` : `created_at` / `-created_at` / `total_ttc` / `-total_ttc`

### 8.3 — `GET /v1/devis/{id}`

Détail complet d'un devis.

### 8.4 — `PATCH /v1/devis/{id}`

Modifier un devis en `draft`. **Interdit** si status ≠ draft → `DEVIS_ALREADY_SENT` (409).

### 8.5 — `DELETE /v1/devis/{id}`

Supprimer un devis en `draft` uniquement.

### 8.6 — `POST /v1/devis/{id}/generate-pdf`

Générer le PDF (ReportLab + mentions + Factur-X).

**Response (202)** :
```json
{
  "data": {
    "job_id": "job_xyz",
    "status": "processing",
    "estimated_completion_seconds": 3
  }
}
```

Polling via `GET /v1/jobs/{job_id}` ou WebSocket event `devis.pdf_ready`.

### 8.7 — `POST /v1/devis/{id}/send`

Envoyer au client (email + WhatsApp selon préférence).

**Request** :
```json
{
  "channels": ["email", "whatsapp"],
  "custom_message": "Voilà comme convenu...",   // Optionnel
  "require_signature": true
}
```

Si `require_signature=true` → appel Yousign API, le client signe électroniquement.

### 8.8 — `POST /v1/devis/{id}/send-signature`

Envoyer lien Yousign dédié (si oublié dans le send initial).

### 8.9 — `GET /v1/devis/{id}/status-signature`

Récupérer statut signature Yousign.

### 8.10 — `POST /v1/devis/{id}/duplicate`

Dupliquer un devis existant (base de nouveau devis).

### 8.11 — `POST /v1/devis/{id}/convert-to-facture`

Convertir un devis signé en facture.

---

## 9. ENDPOINTS FACTURES

### 9.1 — `POST /v1/factures`

Créer une facture (ou depuis un devis via `/v1/devis/{id}/convert-to-facture`).

**Request** : similaire à `POST /v1/devis` + :
```json
{
  "devis_id": "dev_abc123",             // Optionnel
  "type_facture": "acompte" | "avancement" | "solde" | "standard",
  "pct_avancement": 30,                 // Requis si type=acompte ou avancement
  "date_echeance": "2026-05-18"
}
```

**Response (201)** :
```json
{
  "data": {
    "id": "fac_xyz",
    "numero": "FACT-2026-128",
    "status": "draft",
    "total_ht": 33000,
    "total_tva": 3300,
    "total_ttc": 36300,
    "facturx_pdf_url": null,            // Généré après validation
    "created_at": "2026-04-18T..."
  }
}
```

### 9.2 — `GET /v1/factures`

Liste paginée. Query params similaires à §8.2 + :
- `status` : `draft` / `sent` / `paid` / `partial` / `overdue` / `cancelled`
- `date_echeance_until`

### 9.3 — `GET /v1/factures/{id}`

Détail.

### 9.4 — `POST /v1/factures/{id}/generate-facturx`

Génère PDF/A-3 + XML EN 16931 profil EXTENDED (voir `docs/METIER.md` §Factur-X).

### 9.5 — `POST /v1/factures/{id}/send`

Envoi au client (même logique devis).

À partir de sept 2026 : obligation de transiter par **Plateforme Agréée** (PA) pour réception obligatoire (voir `docs/METIER.md`). À partir de sept 2027 : obligation émission.

### 9.6 — `POST /v1/factures/{id}/mark-paid`

Marquer comme payée (manuel).

**Request** :
```json
{
  "date_paiement": "2026-04-20",
  "montant_paye": 36300,
  "moyen_paiement": "virement" | "cheque" | "cb" | "especes" | "stripe_link",
  "reference": "VIR20260420-001"
}
```

### 9.7 — `POST /v1/factures/{id}/send-relance`

Envoyer relance manuelle (ou laisser l'Agent Relance le faire auto).

**Request** :
```json
{
  "niveau": "J+15" | "J+30" | "J+45" | "mise_en_demeure",
  "ton": "doux" | "ferme" | "adaptatif",
  "custom_message": null
}
```

### 9.8 — `POST /v1/factures/{id}/generer-attestation-tva`

Générer attestation TVA 10% pour travaux (obligatoire avec la facture en France).

---

## 10. ENDPOINTS CHANTIERS

### 10.1 — `POST /v1/chantiers`

Créer un chantier (ou auto-créé via devis).

**Request** :
```json
{
  "nom": "Rénovation SDB Martin",
  "client_id": "...",
  "adresse": "...",
  "type_chantier": "renovation" | "construction_neuve" | "depannage",
  "date_debut_prevue": "2026-05-01",
  "date_fin_prevue": "2026-05-22",
  "budget_estime_ht": 15000000,
  "postes": ["plomberie", "carrelage", "electricite"]
}
```

### 10.2 — `GET /v1/chantiers`

Liste Kanban (tous les chantiers). Query :
- `status` : `prospect` / `devis_sent` / `accepte` / `en_cours` / `termine` / `facture` / `paye` / `abandonne`
- `client_id`
- `date_from` / `date_to`

### 10.3 — `GET /v1/chantiers/{id}`

Détail : lignes temps, dépenses, marge calculée, photos, historique events.

### 10.4 — `PATCH /v1/chantiers/{id}`

Modifier.

### 10.5 — `POST /v1/chantiers/{id}/timer/start`

Démarrer timer (Agent Planning).

### 10.6 — `POST /v1/chantiers/{id}/timer/stop`

Arrêter timer + créer `time_entry`.

### 10.7 — `GET /v1/chantiers/{id}/time-entries`

Historique du temps passé.

### 10.8 — `POST /v1/chantiers/{id}/close`

Clôturer le chantier + calculer marge finale + proposer facture solde.

### 10.9 — `GET /v1/chantiers/{id}/margin`

Marge live : CA actuel - coûts matériaux - coûts MO - frais déplacements.

---

## 11. ENDPOINTS CLIENTS (CRM)

### 11.1 — `POST /v1/clients`

Créer un client (particulier ou entreprise).

**Request** :
```json
{
  "type": "particulier" | "entreprise",
  "civilite": "M." | "Mme",
  "prenom": "Jean",
  "nom": "Martin",
  "raison_sociale": null,
  "siret": null,
  "tva_intra": null,
  "email": "jean.martin@example.com",
  "telephone": "+33612345678",
  "adresse": {
    "rue": "12 rue de la République",
    "code_postal": "75001",
    "ville": "Paris",
    "pays": "FR"
  },
  "source": "bouche_a_oreille" | "architecte" | "site_web" | "facebook" | "other",
  "apporteur_id": null,
  "tags": ["haut_de_gamme", "payeur_rapide"]
}
```

### 11.2 — `GET /v1/clients`

Liste. Query :
- `search` (nom, email, tel)
- `type`
- `tag`
- `sort` : `-created_at` / `nom` / `-ca_total`

### 11.3 — `GET /v1/clients/{id}`

Détail + historique devis/factures/chantiers + stats paiement.

### 11.4 — `PATCH /v1/clients/{id}`

Modifier.

### 11.5 — `DELETE /v1/clients/{id}`

Archive (soft delete). Ne peut pas supprimer si chantier actif.

### 11.6 — `GET /v1/clients/{id}/timeline`

Timeline events : devis envoyés, factures, appels, visites, etc.

### 11.7 — `POST /v1/clients/import`

Import batch (CSV / Excel).

**Request** : `multipart/form-data` avec `file` (csv/xlsx).

**Response (202)** : `job_id` pour polling.

---

## 12. ENDPOINTS TICKETS (OCR)

### 12.1 — `POST /v1/tickets/scan`

Scanner un ticket de caisse (Claude Vision OCR).

**Content-Type** : `multipart/form-data`

**Fields** :
- `image` (file) — jpg/png/heic, max 10 Mo
- `chantier_id` (UUID, optionnel — sinon auto-détection GPS)
- `force_category` (optionnel) — forcer catégorie

**Response (200)** :
```json
{
  "data": {
    "id": "tck_xyz",
    "fournisseur": "Point P",
    "date": "2026-04-18",
    "montant_ht": 4720,
    "montant_tva": 472,
    "montant_ttc": 5192,
    "tva_rate": 10.0,
    "lignes_detectees": [
      {"designation": "Colle carrelage C2", "quantite": 2, "prix_ttc": 2896},
      {"designation": "Joint époxy blanc", "quantite": 1, "prix_ttc": 2296}
    ],
    "categorie": "materiaux",
    "sous_categorie": "carrelage",
    "chantier_id": "...",
    "confidence": "high",
    "photo_url": "https://storage.structorai.app/tickets/...",
    "requires_validation": false
  }
}
```

Si `confidence < medium` → `requires_validation: true` → apparaît dans Dossier "À faire".

### 12.2 — `GET /v1/tickets`

Liste. Query :
- `chantier_id`, `date_from`, `date_to`, `categorie`, `fournisseur`

### 12.3 — `PATCH /v1/tickets/{id}`

Corriger catégorie / montant.

### 12.4 — `DELETE /v1/tickets/{id}`

Supprimer.

### 12.5 — `POST /v1/tickets/export-compta`

Export comptable mensuel (CSV + JSON + FEC-compatible).

---

## 13. ENDPOINTS CONTACTS PRO

Architectes, apporteurs d'affaires, fournisseurs privilégiés.

### 13.1 — `POST /v1/contacts-pro`

Créer contact.

### 13.2 — `GET /v1/contacts-pro`

Liste. Query `type` : `architecte` / `apporteur` / `fournisseur` / `artisan_partenaire`.

### 13.3 — `PATCH /v1/contacts-pro/{id}`, `DELETE`

### 13.4 — `GET /v1/contacts-pro/{id}/leads`

Leads apportés par ce contact.

### 13.5 — `POST /v1/contacts-pro/{id}/commission`

Tracker commission apporteur (pour une affaire).

---

## 14. ENDPOINTS PHOTOS CHANTIER

### 14.1 — `POST /v1/photos`

Upload photo.

**Content-Type** : `multipart/form-data`

**Fields** :
- `image` (file) — jpg/png/heic, max 15 Mo (compression auto côté serveur)
- `chantier_id` (UUID, requis)
- `type` : `avant` / `pendant` / `apres` / `probleme` / `reference`
- `description` (string, optionnel)
- `analyze_with_ai` (bool, default true) — si oui : Agent Vision analyse

**Response (201)** :
```json
{
  "data": {
    "id": "pho_xyz",
    "url": "https://storage.structorai.app/photos/...",
    "thumbnail_url": "...",
    "type": "avant",
    "chantier_id": "...",
    "gps": {"lat": 48.8566, "lng": 2.3522},
    "taken_at": "2026-04-18T...",
    "ai_analysis": {
      "description": "Salle de bain existante, baignoire à démolir...",
      "detected_elements": ["baignoire", "carrelage_vieux", "vmc_absente"],
      "anomalies": ["pas de VMC visible"],
      "confidence": "high"
    }
  }
}
```

### 14.2 — `GET /v1/photos`

Liste. Query : `chantier_id`, `type`, `date_from`.

### 14.3 — `POST /v1/photos/{id}/analyze`

Re-lancer analyse Claude Vision (si pas fait au upload).

### 14.4 — `POST /v1/photos/montage-before-after`

Créer montage avant/après (pour réseaux sociaux).

**Request** :
```json
{
  "photo_avant_id": "...",
  "photo_apres_id": "...",
  "layout": "horizontal" | "vertical",
  "add_logo": true
}
```

**Response** : URL PNG + URL JPEG + proposition de caption sociale (Agent Réputation).

### 14.5 — `POST /v1/photos/{id}/publish-social`

Publier sur réseaux (Facebook + Instagram + Google Business).

**Request** :
```json
{
  "channels": ["facebook", "instagram", "google_business"],
  "caption": "...",
  "schedule_at": null            // null = publier maintenant
}
```

---

## 15. ENDPOINTS EMAILS PRO

### 15.1 — `GET /v1/emails/summary`

Résumé quotidien Agent Email Pro.

**Query params** :
- `date` (ISO, default today)

**Response** :
```json
{
  "data": {
    "date": "2026-04-18",
    "total_emails": 47,
    "categorized": {
      "prospects_nouveaux": 3,
      "clients_existants": 12,
      "fournisseurs": 8,
      "administratif_urgent": 1,
      "spam": 23
    },
    "actions_proposed": [
      {
        "id": "act_...",
        "type": "respond_prospect",
        "summary": "Nouveau prospect — Mme Dupont, SDB à Versailles",
        "urgency": "medium"
      }
    ],
    "requires_validation_count": 4
  }
}
```

### 15.2 — `POST /v1/emails/{id}/respond`

Répondre à un email via le cerveau.

### 15.3 — `POST /v1/emails/imap-config`

Configurer connexion IMAP (plan Business).

---

## 16. ENDPOINTS AVIS GOOGLE

### 16.1 — `GET /v1/avis`

Liste des avis reçus.

**Query** : `replied` (bool), `rating_min`, `date_from`.

### 16.2 — `POST /v1/avis/{id}/respond`

Répondre à un avis (génération IA + validation humaine obligatoire).

**Request** :
```json
{
  "response": "...",             // Laisser vide pour génération IA
  "tone": "professionnel" | "chaleureux" | "factuel"
}
```

### 16.3 — `POST /v1/avis/request-review`

Demander un avis à un client post-chantier.

**Request** :
```json
{
  "client_id": "...",
  "chantier_id": "...",
  "channel": "sms" | "email" | "whatsapp"
}
```

---

## 17. ENDPOINTS STATS (DASHBOARD)

### 17.1 — `GET /v1/stats/dashboard`

Vue globale dashboard artisan.

**Query** : `period` = `today` / `week` / `month` / `year` / `custom`.

**Response** :
```json
{
  "data": {
    "period": "month",
    "ca_ht": 1850000,               // En centimes (18 500,00 €)
    "ca_ttc": 2035000,
    "marge_brute_pct": 42.5,
    "devis_envoyes": 23,
    "devis_signes": 12,
    "taux_conversion_devis": 52.2,
    "factures_emises": 15,
    "factures_payees": 11,
    "factures_en_retard": 2,
    "chantiers_actifs": 4,
    "chantiers_termines": 3,
    "temps_total_heures": 187,
    "heures_facturables": 152,
    "taux_productif_pct": 81.3,
    "top_clients": [
      {"id": "...", "nom": "Martin", "ca_ttc": 450000}
    ]
  }
}
```

### 17.2 — `GET /v1/stats/chantiers`, `/devis`, `/factures`, `/clients`

Statistiques détaillées par entité.

### 17.3 — `GET /v1/stats/benchmark`

Benchmark vs confrères même département + métier (Agent Coach).

---

## 18. ENDPOINTS BILLING (STRIPE)

### 18.1 — `POST /v1/billing/checkout`

Créer session Stripe Checkout pour upgrade ou souscription.

**Request** :
```json
{
  "plan": "pro" | "pro_annuel" | "business" | "business_annuel" | "lifetime",
  "add_ons": ["telephone_ia"],
  "coupon": "CAPEB20"                // Optionnel
}
```

**Response** :
```json
{
  "data": {
    "checkout_url": "https://checkout.stripe.com/...",
    "session_id": "cs_..."
  }
}
```

### 18.2 — `GET /v1/billing/portal`

Lien vers Stripe Customer Portal (gestion CB, factures, annulation).

### 18.3 — `GET /v1/billing/subscription`

État de l'abonnement.

### 18.4 — `POST /v1/billing/cancel`

Annuler abonnement (à la fin de la période).

### 18.5 — `GET /v1/billing/invoices`

Liste factures STRUCTORAI (reçues par l'artisan).

### 18.6 — `POST /v1/webhooks/stripe`

Webhook Stripe (voir §28).

---

## 19. ENDPOINTS ONBOARDING + GAMIFICATION

### 19.1 — `GET /v1/onboarding/state`

État onboarding (quêtes, niveau, XP, badges).

### 19.2 — `POST /v1/onboarding/quest/{id}/complete`

Marquer une quête terminée (événement généralement auto-déclenché).

### 19.3 — `GET /v1/gamification/leaderboard`

Classement (opt-in uniquement).

### 19.4 — `GET /v1/gamification/badges`

Liste badges + éligibilité.

---

## 20. ENDPOINTS SETTINGS

### 20.1 — `GET /v1/settings/user`

Settings utilisateur courant.

**Response** :
```json
{
  "data": {
    "user_id": "...",
    "email": "...",
    "telephone": "...",
    "locale": "fr-FR",
    "voice_preference": "male_pro",
    "notifications": {
      "push_enabled": true,
      "email_digest": "daily",
      "sms_urgences": true
    },
    "devis_defaults": {
      "validite_jours": 30,
      "cgv_url": "...",
      "mentions_supplementaires": []
    },
    "agent_settings": {
      "relance_auto": true,
      "coach_analyse_mensuelle": true,
      "reputation_auto_post": false
    }
  }
}
```

### 20.2 — `PATCH /v1/settings/user`

### 20.3 — `GET/POST /v1/settings/organization`

Settings organisation (multi-user).

### 20.4 — `GET/POST /v1/settings/devis-template`

Templates de devis (couleurs, logo, mentions).

### 20.5 — `GET/POST/DELETE /v1/settings/api-keys`

Gestion API keys (voir §2.2).

### 20.6 — `POST /v1/settings/export-data`

Export RGPD complet (JSON + ZIP photos/PDFs).

### 20.7 — `POST /v1/settings/delete-account`

Suppression compte (RGPD). Confirmation email obligatoire.

---

## 21. ENDPOINTS SUPPORT (F136)

### 21.1 — `POST /v1/support/escalate`

Escalade manuelle vers support humain.

**Request** :
```json
{
  "conversation_id": "...",
  "reason": "coach_decision_doubt" | "technical_bug" | "billing_question" | "other",
  "urgency": "normal" | "urgent",
  "custom_message": "..."
}
```

**Response (201)** :
```json
{
  "data": {
    "ticket_id": "tck_...",
    "crisp_session_url": "https://app.crisp.chat/...",
    "expected_response_time_minutes": 120
  }
}
```

### 21.2 — `POST /v1/support/request-human-validation`

Bouton AI Act "Demander validation humaine" (voir `docs/AI-ACT-COMPLIANCE.md` §12).

**Request** :
```json
{
  "ai_decision_id": "dec_...",        // Référence ai_decisions
  "user_reason": "Le calcul Coach me semble optimiste"
}
```

### 21.3 — `GET /v1/support/tickets`

Liste de ses propres tickets (utilisateur).

---

## 22. ENDPOINTS COACH BUSINESS (F119)

### 22.1 — `GET /v1/coach/analyses`

Liste des analyses mensuelles.

### 22.2 — `POST /v1/coach/trigger-analysis`

Déclencher analyse manuellement (1/mois en Starter, illimité sinon).

**Response (202)** : `job_id` (traitement async 30s-2min).

### 22.3 — `GET /v1/coach/analyses/{id}`

Détail d'une analyse + disclaimer obligatoire.

---

## 23. ENDPOINTS FISCALITÉ

### 23.1 — `GET /v1/fiscalite/calendrier`

Calendrier fiscal personnalisé (URSSAF, TVA, IS, CVAE).

### 23.2 — `GET /v1/fiscalite/seuils`

Position par rapport aux seuils (micro-BIC, TVA franchise, etc.).

### 23.3 — `POST /v1/fiscalite/scan-courrier`

Scanner courrier admin (URSSAF, DGFiP, etc.) → Claude Vision → action proposée.

### 23.4 — `GET /v1/fiscalite/alertes`

Alertes actives (approche seuil, échéance proche).

---

## 24. ENDPOINTS DÉPLACEMENTS

### 24.1 — `POST /v1/deplacements`

Créer un déplacement (auto via GPS ou manuel).

### 24.2 — `GET /v1/deplacements`

Liste. Query `date_from`, `date_to`, `chantier_id`.

### 24.3 — `POST /v1/deplacements/export-mensuel`

Export frais km + paniers + indemnités BTP pour la compta.

### 24.4 — `GET /v1/deplacements/zones-btp`

Liste des zones BTP (indemnités trajet/panier par zone).

---

## 25. ENDPOINTS RH

### 25.1 — `POST /v1/rh/employes`

Créer employé (plan Business).

### 25.2 — `GET /v1/rh/employes`

Liste employés.

### 25.3 — `POST /v1/rh/pointage`

Pointage heure (start/stop).

### 25.4 — `GET /v1/rh/pointage/{employe_id}`

Historique pointage.

### 25.5 — `POST /v1/rh/conges`

Gestion congés CIBTP.

### 25.6 — `POST /v1/rh/export-paie`

Export éléments de paie mensuel (heures, heures sup, indemnités, absences) pour cabinet comptable.

---

## 26. ENDPOINTS SITE WEB (F74)

### 26.1 — `POST /v1/site/generate`

Générer le site vitrine initial (Agent Site Web).

### 26.2 — `GET /v1/site/config`

Config actuelle (domaine, sections, photos, SEO).

### 26.3 — `PATCH /v1/site/config`

Mettre à jour.

### 26.4 — `POST /v1/site/publish`

Publier les changements (déploiement Vercel).

### 26.5 — `GET /v1/site/stats`

Stats visiteurs, contact forms reçus.

---

## 27. ENDPOINTS SANDBOX PUBLIQUE (F115)

**Pas d'auth JWT**. Rate limit par IP (10 req/min, 30 req/jour, session Redis 15 min).

### 27.1 — `POST /v1/public/chat`

Chat de démonstration (sans compte).

**Request** :
```json
{
  "message": "Fais-moi un devis pour une SDB 8m²",
  "session_id": "anon_xyz"           // Optionnel, généré backend sinon
}
```

**Response** : après 3 messages → CTA "Crée ton compte pour envoyer ce devis" (F125 onboarding inversé).

### 27.2 — `POST /v1/public/signup-from-sandbox`

Signup depuis la sandbox (migre la session anonyme vers le compte créé).

---

## 28. WEBHOOKS (STRIPE, META WHATSAPP, YOUSIGN)

### 28.1 — `POST /v1/webhooks/stripe`

**Signature** : vérifiée via `stripe-signature` header et `STRIPE_WEBHOOK_SECRET`.

**Events handled** :
- `checkout.session.completed` → activer abonnement
- `customer.subscription.updated` → mettre à jour plan
- `customer.subscription.deleted` → dégrader vers Starter
- `invoice.payment_failed` → démarrer grâce period 7 jours
- `invoice.payment_succeeded` → reset grâce period
- `charge.refunded` → traçabilité RGPD

### 28.2 — `POST /v1/whatsapp/webhook`

**Meta Cloud API verification** : GET pour vérification + POST pour messages.

Identification de l'artisan via son numéro WhatsApp lié à son compte (OTP à l'onboarding).

### 28.3 — `POST /v1/webhooks/yousign`

Events signature :
- `signature.signer.done` → devis signé
- `signature.expired` → devis expiré
- `signature.declined` → refus

### 28.4 — `POST /v1/webhooks/google-reviews`

(Si Google Business configure webhook) → nouvel avis reçu.

---

## 29. WEBSOCKET (CHAT TEMPS RÉEL)

### 29.1 — Connexion

```
wss://api.structorai.app/v1/chat/ws?token=<jwt>
```

Si pas de WebSocket disponible (corporate proxy) → fallback HTTP long-polling via `/v1/chat/poll`.

### 29.2 — Events serveur → client

```json
{"event": "connected", "session_id": "..."}
{"event": "typing", "agent": "devis"}
{"event": "message", "data": {...}}
{"event": "voice_stream_chunk", "data": {"audio_b64": "...", "seq": 1}}
{"event": "voice_stream_end"}
{"event": "action_proposed", "data": {...}}
{"event": "error", "data": {"code": "...", "message": "..."}}
{"event": "heartbeat"}                  // Toutes les 30s
```

### 29.3 — Events client → serveur

```json
{"event": "message", "data": {"content": "..."}}
{"event": "cancel"}                     // Annuler génération en cours
{"event": "feedback", "data": {"message_id": "...", "rating": "up" | "down"}}
{"event": "heartbeat_ack"}
```

### 29.4 — Déconnexion

- Client → `{"event": "close"}`
- Serveur → timeout 90s sans heartbeat

---

## 30. ENDPOINTS ADMIN (FABRICE)

Accessibles uniquement si `user.role == "admin"` dans Supabase (seul Fabrice l'a).

### 30.1 — `GET /v1/admin/users`

Liste tous les utilisateurs (avec filtres).

### 30.2 — `GET /v1/admin/organizations`

Liste orgs + plan + MRR.

### 30.3 — `GET /v1/admin/metrics`

Métriques business live (MRR, DAU, churn, NPS).

### 30.4 — `GET /v1/admin/ai-decisions/export`

Export audit log AI Act (voir `docs/AI-ACT-COMPLIANCE.md` §9.4).

**Query** : `start`, `end`, `format` (json / csv).

### 30.5 — `GET /v1/admin/support-dashboard`

Dashboard support (voir `docs/SUPPORT-STRATEGY.md` §9).

### 30.6 — `POST /v1/admin/refund`

Remboursement manuel via Stripe.

### 30.7 — `POST /v1/admin/impersonate`

Impersonate un utilisateur (support — loggé dans audit).

### 30.8 — `GET /v1/admin/llm-costs`

Coûts LLM par artisan, par agent, par jour.

### 30.9 — `POST /v1/admin/feature-flags`

Activer/désactiver features pour tests.

### 30.10 — `GET /v1/admin/incidents`

Historique incidents AI Act (Art. 73 signalements).

---

## 31. SYNC HORS-LIGNE (BATCH)

### 31.1 — `POST /v1/sync/batch`

Batch events offline queue (mobile SQLite → serveur).

**Request** :
```json
{
  "events": [
    {
      "local_id": "local_001",
      "entity": "devis",
      "action": "create",
      "timestamp": "2026-04-18T14:30:00+02:00",
      "payload": {...}
    },
    ...
  ]
}
```

**Response (200)** :
```json
{
  "data": {
    "processed": 45,
    "conflicts": [
      {"local_id": "local_012", "reason": "concurrent_modification", "server_version": {...}}
    ],
    "errors": []
  }
}
```

### 31.2 — `GET /v1/sync/state`

Timestamp dernière sync serveur (pour delta sync).

---

## 32. PAGINATION, TRI, FILTRAGE

### 32.1 — Pagination standard

**Query params** :
- `page` (int, default 1)
- `per_page` (int, default 20, max 100)

**Response meta** :
```json
"pagination": {
  "page": 1,
  "per_page": 20,
  "total": 145,
  "total_pages": 8,
  "has_next": true,
  "has_prev": false
}
```

### 32.2 — Pagination cursor (pour gros volumes)

Pour endpoints à fort volume (timeline, events) :
- `cursor` (opaque string)
- `limit`

Response :
```json
"pagination": {
  "next_cursor": "eyJpZCI6ICIuLi4ifQ==",
  "has_next": true
}
```

### 32.3 — Tri

- `sort=field` → ascendant
- `sort=-field` → descendant
- Multi : `sort=-created_at,nom`

### 32.4 — Filtrage

- `field=value` → égalité
- `field__gte=value`, `field__lte=value` → plage
- `field__in=val1,val2` → inclusion
- `search=text` → full-text où pertinent

---

## 33. VERSIONING ET DEPRECATION

### 33.1 — Stratégie

- Path-based versioning : `/v1/`
- **1 version majeure à la fois** en production
- **12 mois de support** après annonce `v2`

### 33.2 — Deprecation

Quand un endpoint est déprécié :
- Header `Deprecation: true`
- Header `Sunset: 2027-04-18T00:00:00Z` (date de suppression)
- Header `Link: <new-endpoint>; rel="successor-version"`
- Log warning dans Sentry

### 33.3 — Compatibilité mobile

**Rule** : toujours maintenir rétro-compat avec `X-Client-Version` >= dernière - 3 releases.

Si client trop vieux → `426 Upgrade Required` avec URL store.

---

## 34. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/ARCH.md` | Architecture globale (Gateway FastAPI §4) |
| `docs/AGENTS.md` (à créer) | Détail des 14 agents derrière les endpoints |
| `docs/VOICE.md` (à créer) | Pipeline voix derrière `/v1/chat/audio` |
| `docs/WHATSAPP.md` (à créer) | Détail `/v1/whatsapp/webhook` |
| `docs/ENV.md` (à créer) | Variables env (Stripe keys, Yousign, Meta, etc.) |
| `docs/ERRORS.md` (à créer) | Liste exhaustive des codes d'erreur |
| `docs/MIGRATIONS.md` (à créer) | Schéma SQL des tables |
| `docs/OFFLINE.md` (à créer) | Détail sync `/v1/sync/batch` |
| `docs/TESTS.md` (à créer) | Tests e2e sur chaque endpoint |
| `docs/METIER.md` | Règles BTP utilisées (TVA, mentions, Factur-X) |
| `docs/AI-ACT-COMPLIANCE.md` | Endpoints audit (AI Act) |
| `docs/SUPPORT-STRATEGY.md` | Endpoints support (F136) |
| `docs/COACH-DISCLAIMER.md` | Disclaimer endpoints coach |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` | Plans, quotas, modèles Claude |
| `docs/FEATURES_COMPLETE.md` | Mapping features ↔ endpoints |
| `BUILD_PLAN.md` | Structure fichiers backend api/v1/ |
| `SECURITE.md` | Auth, encryption, secrets |

### 34.1 — OpenAPI et docs auto

- Spec OpenAPI 3.1 : `/v1/openapi.json`
- Swagger UI : `/v1/docs` (staging seulement)
- ReDoc : `/v1/redoc` (staging seulement)
- Production : désactivés (sécurité)

---

> **Ce fichier est la spec d'API de référence.**
> **Règle d'or** : si un endpoint n'est pas listé ici, il n'existe pas.
> **Tout ajout d'endpoint DOIT être ajouté ici AVANT implémentation** (contract-first).
> **Les schémas Pydantic détaillés sont dans `backend/app/models/` et exportés vers OpenAPI.**
