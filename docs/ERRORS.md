---
name: ERRORS
description: Catalogue exhaustif des codes d'erreur StructorAIError. Classe Python complète (pattern AgentShield) avec code, http_status, message_fr, message_en, details, retry_able. ~180 codes d'erreur groupés par catégorie (auth, validation, business logic, quota, infrastructure, voice, WhatsApp, Stripe, AI/LLM, Supabase, memory, signature, OCR). Messages utilisateur en français + anglais. Retry strategies. Logging Sentry scrubbing. Monitoring alertes. Mapping HTTP codes. Comportements mobile face aux erreurs.
type: technical-error-catalog
scope: backend-errors, frontend-error-handling, monitoring, sentry
priority: critical
date: 2026-04-18
version: 1.0
total_error_codes: ~180
languages: fr (primaire), en (fallback), tr, pt, ar, es (via i18n)
logging_system: Sentry + JSON structured logs
scrubbing_enabled: true
---

# docs/ERRORS.md — Catalogue des erreurs STRUCTORAI

> **Ce fichier est la source de vérité des codes d'erreur.**
> Toute nouvelle erreur DOIT être ajoutée ici AVANT implémentation.
> Reference API : `docs/API.md` §5.
> Reference architecture : `docs/ARCH.md` §12 Résilience.

---

## 0. SOMMAIRE

1. [Principes fondamentaux](#1-principes-fondamentaux)
2. [Classe `StructorAIError` (pattern AgentShield)](#2-classe-structoraierror-pattern-agentshield)
3. [Format de réponse API](#3-format-de-réponse-api)
4. [Mapping HTTP codes](#4-mapping-http-codes)
5. [Catégorie 1 — Authentification (AUTH_*)](#5-catégorie-1--authentification-auth_)
6. [Catégorie 2 — Validation (VALIDATION_*)](#6-catégorie-2--validation-validation_)
7. [Catégorie 3 — Ressources non trouvées (_NOT_FOUND)](#7-catégorie-3--ressources-non-trouvées-_not_found)
8. [Catégorie 4 — Business logic (409)](#8-catégorie-4--business-logic-409)
9. [Catégorie 5 — Quota et rate limiting](#9-catégorie-5--quota-et-rate-limiting)
10. [Catégorie 6 — Infrastructure (502/503/504)](#10-catégorie-6--infrastructure-502503504)
11. [Catégorie 7 — LLM et agents IA](#11-catégorie-7--llm-et-agents-ia)
12. [Catégorie 8 — Voice (STT + TTS)](#12-catégorie-8--voice-stt--tts)
13. [Catégorie 9 — WhatsApp](#13-catégorie-9--whatsapp)
14. [Catégorie 10 — Stripe (billing)](#14-catégorie-10--stripe-billing)
15. [Catégorie 11 — Mémoire (Mem0 + MemPalace)](#15-catégorie-11--mémoire-mem0--mempalace)
16. [Catégorie 12 — Signature Yousign](#16-catégorie-12--signature-yousign)
17. [Catégorie 13 — OCR + Vision IA](#17-catégorie-13--ocr--vision-ia)
18. [Catégorie 14 — Factur-X / PA](#18-catégorie-14--factur-x--pa)
19. [Catégorie 15 — Fiscalité + Métier BTP](#19-catégorie-15--fiscalité--métier-btp)
20. [Catégorie 16 — AI Act compliance](#20-catégorie-16--ai-act-compliance)
21. [Comportement client face aux erreurs](#21-comportement-client-face-aux-erreurs)
22. [Retry strategies](#22-retry-strategies)
23. [Logging et Sentry](#23-logging-et-sentry)
24. [Monitoring et alertes](#24-monitoring-et-alertes)
25. [i18n messages](#25-i18n-messages)
26. [Anti-patterns](#26-anti-patterns)
27. [Références croisées](#27-références-croisées)

---

## 1. PRINCIPES FONDAMENTAUX

### 1.1 — 10 règles immuables

1. **Tout endpoint qui peut échouer DOIT lever une `StructorAIError`** (jamais une exception Python brute)
2. **Un code = une erreur spécifique** (pas de code générique "quelque chose a foiré")
3. **Message utilisateur en français** par défaut + fallback anglais
4. **Pas de détails techniques** dans les messages utilisateur (pas de stack trace, pas de chemins)
5. **trace_id** dans chaque réponse d'erreur pour debug
6. **Logger toujours**, même si erreur "normale" (comptage, patterns)
7. **Scrubber les secrets** dans les détails avant log
8. **retry_able** documenté explicitement
9. **Retry après exponentiel** quand applicable
10. **Status code HTTP correct** (pas tout en 500)

### 1.2 — Hiérarchie des erreurs

```
Exception (Python)
└── StructorAIError (base classe custom)
    ├── AuthError (401/403)
    ├── ValidationError (400/422)
    ├── NotFoundError (404)
    ├── ConflictError (409)
    ├── QuotaError (402/429)
    ├── InfrastructureError (502/503/504)
    ├── BusinessLogicError (422)
    └── AIError (500/502)
        ├── LLMError
        ├── VoiceError
        ├── VisionError
        └── MemoryError
```

### 1.3 — Emplacement

**Fichier** : `backend/app/utils/errors.py`

**Import partout** :
```python
from app.utils.errors import (
    StructorAIError,
    AuthError, ValidationError, NotFoundError,
    ConflictError, QuotaError, InfrastructureError,
    BusinessLogicError, AIError,
    # Codes spécifiques aussi importables directement
    DEVIS_NOT_FOUND, LLM_PROVIDER_DOWN, ...
)
```

---

## 2. CLASSE `STRUCTORAIERROR` (PATTERN AGENTSHIELD)

### 2.1 — Structure complète

```python
# backend/app/utils/errors.py

from dataclasses import dataclass, field
from typing import Optional, Any
from enum import Enum


class ErrorLogLevel(str, Enum):
    INFO = "info"
    WARNING = "warning"
    ERROR = "error"
    CRITICAL = "critical"


@dataclass
class StructorAIError(Exception):
    """
    Erreur métier STRUCTORAI.
    Pattern AgentShield — toutes les erreurs du backend passent par cette classe.
    """
    # Obligatoires
    code: str                              # Ex: "DEVIS_NOT_FOUND"
    http_status: int                       # Ex: 404
    message_fr: str                        # Message utilisateur français
    
    # Optionnels
    message_en: Optional[str] = None       # Fallback anglais
    details: dict = field(default_factory=dict)   # Contexte technique (scrubé avant log)
    retry_able: bool = False               # Si True → retry possible
    retry_after_seconds: Optional[int] = None
    log_level: ErrorLogLevel = ErrorLogLevel.ERROR
    user_facing: bool = True               # Si True → affiché dans l'app
    sentry_fingerprint: Optional[list[str]] = None   # Custom grouping Sentry
    
    def __post_init__(self):
        # Fallback message_en sur message_fr si absent
        if not self.message_en:
            self.message_en = self.message_fr
    
    def to_response(self, trace_id: str, locale: str = "fr") -> dict:
        """Convertit en réponse HTTP JSON."""
        message = self.message_fr if locale.startswith("fr") else self.message_en
        
        return {
            "error": {
                "code": self.code,
                "message": message,
                "details": self._safe_details(),
                "trace_id": trace_id,
                "retry_able": self.retry_able,
                "retry_after_seconds": self.retry_after_seconds,
            }
        }
    
    def _safe_details(self) -> dict:
        """Scrubbing : retire clés sensibles avant exposition."""
        from app.utils.scrubbing import scrub_dict
        return scrub_dict(self.details)


# Sous-classes par catégorie

class AuthError(StructorAIError):
    """Erreurs d'authentification (401/403)."""
    def __init__(self, code: str, message_fr: str, **kwargs):
        super().__init__(code=code, http_status=401, message_fr=message_fr,
                         log_level=ErrorLogLevel.WARNING, **kwargs)


class ValidationError(StructorAIError):
    """Erreurs de validation (400/422)."""
    def __init__(self, code: str, message_fr: str, field: Optional[str] = None, **kwargs):
        details = kwargs.pop("details", {})
        if field:
            details["field"] = field
        super().__init__(code=code, http_status=422, message_fr=message_fr,
                         details=details, log_level=ErrorLogLevel.INFO, **kwargs)


class NotFoundError(StructorAIError):
    """Ressource non trouvée (404)."""
    def __init__(self, code: str, message_fr: str, resource_id: Optional[str] = None, **kwargs):
        details = kwargs.pop("details", {})
        if resource_id:
            details["resource_id"] = resource_id
        super().__init__(code=code, http_status=404, message_fr=message_fr,
                         details=details, log_level=ErrorLogLevel.INFO, **kwargs)


class ConflictError(StructorAIError):
    """Conflit métier (409)."""
    def __init__(self, code: str, message_fr: str, **kwargs):
        super().__init__(code=code, http_status=409, message_fr=message_fr,
                         log_level=ErrorLogLevel.WARNING, **kwargs)


class QuotaError(StructorAIError):
    """Quota ou rate limit (402/429)."""
    def __init__(self, code: str, message_fr: str, http_status: int = 429, **kwargs):
        super().__init__(code=code, http_status=http_status, message_fr=message_fr,
                         log_level=ErrorLogLevel.WARNING, **kwargs)


class InfrastructureError(StructorAIError):
    """Provider externe down (502/503/504)."""
    def __init__(self, code: str, message_fr: str, http_status: int = 502, **kwargs):
        super().__init__(code=code, http_status=http_status, message_fr=message_fr,
                         retry_able=True, log_level=ErrorLogLevel.ERROR, **kwargs)


class BusinessLogicError(StructorAIError):
    """Règle métier violée (422)."""
    def __init__(self, code: str, message_fr: str, **kwargs):
        super().__init__(code=code, http_status=422, message_fr=message_fr,
                         log_level=ErrorLogLevel.WARNING, **kwargs)


class AIError(StructorAIError):
    """Erreurs IA (500/502)."""
    def __init__(self, code: str, message_fr: str, http_status: int = 502, **kwargs):
        super().__init__(code=code, http_status=http_status, message_fr=message_fr,
                         retry_able=True, log_level=ErrorLogLevel.ERROR, **kwargs)
```

### 2.2 — Handler FastAPI

```python
# backend/app/middleware/error_handler.py

from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse
from app.utils.errors import StructorAIError
from app.utils.logging import log_error

def register_error_handlers(app: FastAPI):
    @app.exception_handler(StructorAIError)
    async def structorai_error_handler(request: Request, exc: StructorAIError):
        trace_id = getattr(request.state, "trace_id", str(uuid.uuid4()))
        locale = request.headers.get("Accept-Language", "fr")[:2]
        
        # Log
        log_error(exc, trace_id=trace_id, request_path=request.url.path)
        
        # Sentry si ERROR ou CRITICAL
        if exc.log_level in (ErrorLogLevel.ERROR, ErrorLogLevel.CRITICAL):
            from sentry_sdk import capture_exception
            with sentry_sdk.push_scope() as scope:
                scope.set_tag("error_code", exc.code)
                scope.set_tag("trace_id", trace_id)
                scope.set_context("error_details", exc._safe_details())
                if exc.sentry_fingerprint:
                    scope.fingerprint = exc.sentry_fingerprint
                capture_exception(exc)
        
        # Response
        headers = {}
        if exc.retry_after_seconds:
            headers["Retry-After"] = str(exc.retry_after_seconds)
        
        return JSONResponse(
            status_code=exc.http_status,
            content=exc.to_response(trace_id=trace_id, locale=locale),
            headers=headers,
        )
    
    @app.exception_handler(Exception)
    async def generic_exception_handler(request: Request, exc: Exception):
        """Fallback pour toute exception non StructorAIError."""
        trace_id = getattr(request.state, "trace_id", str(uuid.uuid4()))
        
        from sentry_sdk import capture_exception
        capture_exception(exc)
        
        # Ne JAMAIS exposer de détails techniques à l'utilisateur
        return JSONResponse(
            status_code=500,
            content={
                "error": {
                    "code": "INTERNAL_SERVER_ERROR",
                    "message": "Une erreur inattendue s'est produite. Réessaie dans un instant.",
                    "trace_id": trace_id,
                    "retry_able": True,
                }
            }
        )
```

---

## 3. FORMAT DE RÉPONSE API

### 3.1 — Structure uniforme

Toutes les erreurs renvoient le même format JSON :

```json
{
  "error": {
    "code": "DEVIS_NOT_FOUND",
    "message": "Le devis demandé n'existe pas ou ne t'appartient pas",
    "details": {
      "resource_id": "dev_abc123"
    },
    "trace_id": "550e8400-e29b-41d4-a716-446655440000",
    "retry_able": false,
    "retry_after_seconds": null
  }
}
```

### 3.2 — Headers HTTP

- **`X-Trace-ID`** : trace_id (pour support debug)
- **`Retry-After`** : secondes à attendre (si applicable)
- **`WWW-Authenticate`** : si 401 + token invalide

### 3.3 — Champs

| Champ | Type | Requis | Description |
|---|---|---|---|
| `code` | string | ✅ | Identifiant unique erreur |
| `message` | string | ✅ | Message utilisateur (français par défaut) |
| `details` | object | ⚪ | Contexte (scrubé de secrets) |
| `trace_id` | UUID | ✅ | Pour support |
| `retry_able` | bool | ✅ | Si le client peut réessayer |
| `retry_after_seconds` | int | ⚪ | Délai avant retry |

---

## 4. MAPPING HTTP CODES

| Code HTTP | Usage | Catégories concernées |
|---|---|---|
| **400** | Bad Request (payload invalide) | Validation syntaxique |
| **401** | Non authentifié | AUTH_TOKEN_MISSING/INVALID/EXPIRED |
| **402** | Paiement requis | PLAN_LIMIT_REACHED, PLAN_EXPIRED |
| **403** | Interdit | FORBIDDEN_RESOURCE, AUTH_API_KEY_REVOKED |
| **404** | Non trouvé | *_NOT_FOUND |
| **409** | Conflit | DEVIS_ALREADY_SIGNED, CONCURRENT_MODIFICATION |
| **413** | Payload trop gros | *_TOO_LARGE |
| **422** | Sémantique invalide | ValidationError, BusinessLogicError |
| **429** | Rate limit | RATE_LIMIT_EXCEEDED, LLM_BUDGET_EXCEEDED |
| **500** | Bug interne | INTERNAL_SERVER_ERROR |
| **502** | Bad Gateway | Provider externe down |
| **503** | Service Unavailable | MAINTENANCE_MODE |
| **504** | Timeout | Provider externe timeout |

---

## 5. CATÉGORIE 1 — AUTHENTIFICATION (AUTH_*)

### 5.1 — Tableau complet

| Code | HTTP | retry_able | Message FR | Description |
|---|---|---|---|---|
| `AUTH_TOKEN_MISSING` | 401 | ❌ | "Tu n'es pas connecté. Reconnecte-toi." | Pas de header Authorization |
| `AUTH_TOKEN_INVALID` | 401 | ❌ | "Session invalide. Reconnecte-toi." | JWT signature invalide |
| `AUTH_TOKEN_EXPIRED` | 401 | ❌ | "Session expirée. Reconnecte-toi." | JWT expiré (refresh possible) |
| `AUTH_SESSION_REVOKED` | 401 | ❌ | "Ta session a été révoquée." | Admin a invalidé sessions |
| `AUTH_USER_DISABLED` | 403 | ❌ | "Ton compte est désactivé. Contacte le support." | User `deleted_at` set |
| `AUTH_ORG_SUSPENDED` | 403 | ❌ | "Ton abonnement est suspendu. Régularise ton paiement." | Grace period dépassée |
| `AUTH_API_KEY_MISSING` | 401 | ❌ | "API key manquante." | Pas de header X-API-Key |
| `AUTH_API_KEY_INVALID` | 401 | ❌ | "API key invalide." | Hash ne match pas |
| `AUTH_API_KEY_REVOKED` | 403 | ❌ | "API key révoquée." | Key flaggée revoked |
| `AUTH_API_KEY_EXPIRED` | 403 | ❌ | "API key expirée." | Past expires_at |
| `AUTH_API_KEY_INSUFFICIENT_SCOPE` | 403 | ❌ | "Cette API key n'a pas les droits nécessaires." | Missing scope |
| `AUTH_OTP_EXPIRED` | 401 | ❌ | "Code OTP expiré. Demande-en un nouveau." | OTP TTL dépassé |
| `AUTH_OTP_INVALID` | 401 | ❌ | "Code OTP incorrect." | Mauvais code |
| `AUTH_OTP_TOO_MANY_ATTEMPTS` | 429 | ❌ | "Trop de tentatives. Réessaie dans 15 minutes." | >3 essais |
| `AUTH_EMAIL_NOT_VERIFIED` | 403 | ❌ | "Vérifie ton email pour continuer." | email_verified_at null |
| `AUTH_WHATSAPP_NOT_VERIFIED` | 403 | ❌ | "Numéro WhatsApp non vérifié." | whatsapp_verified_at null |
| `FORBIDDEN_RESOURCE` | 403 | ❌ | "Tu n'as pas accès à cette ressource." | RLS violation (rare, normalement pré-filtré) |
| `FORBIDDEN_ADMIN_ONLY` | 403 | ❌ | "Accès réservé aux administrateurs." | Endpoint admin |

---

## 6. CATÉGORIE 2 — VALIDATION (VALIDATION_*)

### 6.1 — Tableau complet

| Code | HTTP | Message FR | Description |
|---|---|---|---|
| `VALIDATION_ERROR` | 400 | "Données invalides." | Pydantic validation fail générique |
| `VALIDATION_REQUIRED_FIELD` | 422 | "Le champ {field} est obligatoire." | Champ manquant |
| `VALIDATION_FIELD_TOO_SHORT` | 422 | "Le champ {field} est trop court." | Min length |
| `VALIDATION_FIELD_TOO_LONG` | 422 | "Le champ {field} est trop long." | Max length |
| `VALIDATION_INVALID_EMAIL` | 422 | "Format email invalide." | Email mal formé |
| `VALIDATION_INVALID_PHONE` | 422 | "Numéro de téléphone invalide." | Format E.164 fail |
| `VALIDATION_INVALID_SIRET` | 422 | "SIRET invalide." | 14 chiffres + algo Luhn |
| `VALIDATION_INVALID_TVA_INTRA` | 422 | "Numéro TVA intracommunautaire invalide." | Format VIES fail |
| `VALIDATION_INVALID_IBAN` | 422 | "IBAN invalide." | Checksum fail |
| `VALIDATION_INVALID_URL` | 422 | "URL invalide." | URL mal formée |
| `VALIDATION_INVALID_DATE` | 422 | "Date invalide." | Format ISO 8601 |
| `VALIDATION_INVALID_UUID` | 422 | "Identifiant invalide." | Format UUID v4 |
| `VALIDATION_INVALID_ENUM_VALUE` | 422 | "Valeur non autorisée pour {field}." | Enum check |
| `VALIDATION_INVALID_TVA_RATE` | 422 | "Taux TVA non autorisé (5.5, 10 ou 20 uniquement en FR)." | TVA BTP FR |
| `VALIDATION_INVALID_AMOUNT` | 422 | "Montant invalide." | Négatif ou trop grand |
| `VALIDATION_AMOUNT_TOO_HIGH` | 422 | "Montant supérieur au maximum autorisé." | >1M€ |
| `VALIDATION_AMOUNT_NEGATIVE` | 422 | "Le montant ne peut pas être négatif." | <0 |
| `VALIDATION_INVALID_LOCALE` | 422 | "Langue non supportée." | Pas dans 6 langues |
| `VALIDATION_FILE_TYPE` | 422 | "Type de fichier non supporté." | MIME type invalide |
| `VALIDATION_FILE_TOO_LARGE` | 413 | "Fichier trop volumineux (max {max_size})." | Voir limites §13/14 |
| `VALIDATION_FILE_EMPTY` | 422 | "Fichier vide." | 0 bytes |
| `VALIDATION_MISSING_MANDATORY_MENTION` | 422 | "Mention obligatoire manquante : {mention}." | 47 mentions devis |

---

## 7. CATÉGORIE 3 — RESSOURCES NON TROUVÉES (_NOT_FOUND)

### 7.1 — Tableau complet

| Code | HTTP | Message FR |
|---|---|---|
| `ORGANIZATION_NOT_FOUND` | 404 | "Organisation non trouvée." |
| `USER_NOT_FOUND` | 404 | "Utilisateur non trouvé." |
| `CLIENT_NOT_FOUND` | 404 | "Client non trouvé." |
| `CHANTIER_NOT_FOUND` | 404 | "Chantier non trouvé." |
| `DEVIS_NOT_FOUND` | 404 | "Devis non trouvé ou non accessible." |
| `DEVIS_LINE_NOT_FOUND` | 404 | "Ligne de devis non trouvée." |
| `FACTURE_NOT_FOUND` | 404 | "Facture non trouvée." |
| `TICKET_NOT_FOUND` | 404 | "Ticket non trouvé." |
| `DEPENSE_NOT_FOUND` | 404 | "Dépense non trouvée." |
| `PHOTO_NOT_FOUND` | 404 | "Photo non trouvée." |
| `CONTACT_PRO_NOT_FOUND` | 404 | "Contact pro non trouvé." |
| `AVIS_NOT_FOUND` | 404 | "Avis non trouvé." |
| `RELANCE_NOT_FOUND` | 404 | "Relance non trouvée." |
| `EMPLOYE_NOT_FOUND` | 404 | "Employé non trouvé." |
| `TIME_ENTRY_NOT_FOUND` | 404 | "Entrée de temps non trouvée." |
| `DEPLACEMENT_NOT_FOUND` | 404 | "Déplacement non trouvé." |
| `API_KEY_NOT_FOUND` | 404 | "API key non trouvée." |
| `SUBSCRIPTION_NOT_FOUND` | 404 | "Abonnement non trouvé." |
| `TASK_NOT_FOUND` | 404 | "Tâche non trouvée." |
| `AGENT_TASK_NOT_FOUND` | 404 | "Tâche agent non trouvée." |
| `CAPSULE_NOT_FOUND` | 404 | "Capsule formation non trouvée." |
| `REFERRAL_CODE_NOT_FOUND` | 404 | "Code parrainage invalide." |
| `COACH_ANALYSIS_NOT_FOUND` | 404 | "Analyse Coach non trouvée." |
| `CONVERSATION_NOT_FOUND` | 404 | "Conversation non trouvée." |
| `JOB_NOT_FOUND` | 404 | "Tâche de traitement introuvable." |
| `TEMPLATE_NOT_FOUND` | 404 | "Template non trouvé." |

---

## 8. CATÉGORIE 4 — BUSINESS LOGIC (409)

### 8.1 — Tableau complet

| Code | HTTP | Message FR | Contexte |
|---|---|---|---|
| `DEVIS_ALREADY_SIGNED` | 409 | "Impossible de modifier : devis déjà signé." | Edit devis signé |
| `DEVIS_ALREADY_SENT` | 409 | "Impossible de modifier : devis déjà envoyé." | Edit devis envoyé |
| `DEVIS_EXPIRED` | 409 | "Ce devis a expiré. Crée-en un nouveau." | Past expires_at |
| `DEVIS_NOT_SIGNABLE` | 409 | "Ce devis ne peut pas être signé dans cet état." | Status incorrect |
| `FACTURE_ALREADY_PAID` | 409 | "Impossible de modifier : facture déjà payée." | |
| `FACTURE_ALREADY_SENT` | 409 | "Impossible de modifier : facture déjà envoyée." | |
| `FACTURE_NOT_CANCELLABLE` | 409 | "Cette facture ne peut pas être annulée (déjà transmise PA)." | |
| `CHANTIER_ALREADY_CLOSED` | 409 | "Chantier déjà clôturé." | |
| `CHANTIER_HAS_OPEN_DEVIS` | 409 | "Chantier a des devis ouverts. Clôture-les avant." | |
| `CLIENT_HAS_ACTIVE_CHANTIER` | 409 | "Client a un chantier actif. Impossible à archiver." | |
| `CONCURRENT_MODIFICATION` | 409 | "Modification concurrente détectée. Recharge la page." | Optimistic lock fail |
| `DUPLICATE_NUMERO` | 409 | "Numéro déjà utilisé." | Uniq devis/facture |
| `TIMER_ALREADY_RUNNING` | 409 | "Un timer est déjà démarré pour ce chantier." | |
| `TIMER_NOT_RUNNING` | 409 | "Aucun timer en cours." | Stop sans start |
| `EMPLOYE_ALREADY_EXISTS` | 409 | "Un employé avec cet email existe déjà." | |
| `SUBSCRIPTION_ALREADY_ACTIVE` | 409 | "Tu as déjà un abonnement actif." | |
| `CANCEL_ALREADY_REQUESTED` | 409 | "L'annulation est déjà demandée." | |
| `REFERRAL_CODE_ALREADY_USED` | 409 | "Tu as déjà utilisé ce code parrain." | |
| `REFERRAL_CODE_SELF_USE` | 409 | "Tu ne peux pas utiliser ton propre code." | |
| `API_KEY_ALREADY_EXISTS` | 409 | "Une API key avec ce nom existe déjà." | |
| `PHOTO_ALREADY_PUBLISHED` | 409 | "Cette photo est déjà publiée." | |
| `FEDERATION_CODE_ALREADY_APPLIED` | 409 | "Code fédération déjà appliqué." | CAPEB20/FFB20 |
| `OTP_ALREADY_USED` | 409 | "Code OTP déjà utilisé." | |
| `WHATSAPP_ALREADY_LINKED` | 409 | "Ce numéro WhatsApp est déjà lié à un autre compte." | |
| `OPERATION_NOT_ALLOWED_IN_STATUS` | 409 | "Opération impossible dans l'état actuel." | |

---

## 9. CATÉGORIE 5 — QUOTA ET RATE LIMITING

### 9.1 — Tableau complet

| Code | HTTP | retry_able | Message FR | Contexte |
|---|---|---|---|---|
| `RATE_LIMIT_EXCEEDED` | 429 | ✅ | "Tu as envoyé trop de requêtes. Réessaie dans {retry_after}s." | API rate limit |
| `RATE_LIMIT_IP_EXCEEDED` | 429 | ✅ | "Trop de requêtes depuis ton IP. Réessaie dans {retry_after}s." | Sandbox public F115 |
| `PLAN_LIMIT_REACHED` | 402 | ❌ | "Tu as atteint la limite de ton plan {plan}. Upgrade pour continuer." | Quota fonctionnel |
| `PLAN_LIMIT_DEVIS_MONTHLY` | 402 | ❌ | "Limite de devis/mois atteinte (Starter 5). Upgrade Pro." | Starter 5 devis/mois |
| `PLAN_LIMIT_FACTURES_MONTHLY` | 402 | ❌ | "Limite de factures/mois atteinte. Upgrade Pro." | Starter 5 factures/mois |
| `PLAN_LIMIT_CLIENTS` | 402 | ❌ | "Limite de clients atteinte ({limit}). Upgrade." | Starter 20 clients |
| `PLAN_LIMIT_CHANTIERS_ACTIVE` | 402 | ❌ | "Limite de chantiers actifs atteinte (3). Upgrade Pro." | Starter 3 chantiers |
| `PLAN_LIMIT_EMPLOYES` | 402 | ❌ | "Plan Business requis pour ajouter des salariés." | Employes = Business only |
| `PLAN_LIMIT_COACH_ANALYSIS` | 402 | ❌ | "Limite d'analyses Coach atteinte (Starter 1/mois)." | |
| `PLAN_LIMIT_API_KEYS` | 402 | ❌ | "Les API keys sont réservées au plan Business." | |
| `PLAN_LIMIT_SITE_WEB` | 402 | ❌ | "Le site vitrine IA est réservé aux plans Pro et Business." | |
| `PLAN_EXPIRED` | 402 | ❌ | "Ton abonnement a expiré. Régularise pour continuer." | Past period_end |
| `PLAN_GRACE_PERIOD` | 402 | ❌ | "Tu es en période de grâce (7 jours). Mets à jour ta CB." | Paiement échoué |
| `LLM_BUDGET_EXCEEDED` | 429 | ✅ | "Budget IA quotidien dépassé. Réessaie demain ou upgrade." | Cap quotidien |
| `LLM_BUDGET_EXCEEDED_MONTHLY` | 429 | ❌ | "Budget IA mensuel dépassé. Contacte le support." | Seuil anti-abus |
| `VOICE_QUOTA_EXCEEDED` | 429 | ❌ | "Limite de vocaux quotidienne atteinte ({limit})." | 20/100/300 vocaux |
| `WHATSAPP_QUOTA_EXCEEDED` | 429 | ❌ | "Limite WhatsApp Meta atteinte. Réessaie plus tard." | Meta tier limit |
| `SMS_QUOTA_EXCEEDED` | 429 | ❌ | "Limite SMS atteinte. Contacte le support." | Protection coût Twilio |
| `STORAGE_QUOTA_EXCEEDED` | 402 | ❌ | "Stockage plein ({limit} GB). Supprime des fichiers ou upgrade." | Plan Storage |
| `BROADCAST_LIMIT_EXCEEDED` | 429 | ✅ | "Trop de destinataires d'un coup. Envoie par groupes plus petits." | Batch WhatsApp |

---

## 10. CATÉGORIE 6 — INFRASTRUCTURE (502/503/504)

### 10.1 — Tableau complet

| Code | HTTP | retry_able | Message FR | Contexte |
|---|---|---|---|---|
| `MAINTENANCE_MODE` | 503 | ✅ | "Maintenance en cours. On revient très vite." | Maintenance planifiée |
| `DATABASE_DOWN` | 503 | ✅ | "Base de données momentanément indisponible. Réessaie." | Supabase down |
| `DATABASE_TIMEOUT` | 504 | ✅ | "Requête trop longue. Réessaie." | Query >30s |
| `REDIS_DOWN` | 503 | ✅ | "Queue momentanément indisponible." | Redis Railway |
| `STORAGE_DOWN` | 503 | ✅ | "Stockage momentanément indisponible." | Supabase Storage |
| `STORAGE_UPLOAD_FAILED` | 502 | ✅ | "Upload échoué. Réessaie." | |
| `INTERNAL_SERVER_ERROR` | 500 | ✅ | "Une erreur inattendue s'est produite. Réessaie dans un instant." | Bug inattendu |
| `SERVICE_UNAVAILABLE` | 503 | ✅ | "Service temporairement indisponible." | |
| `TIMEOUT` | 504 | ✅ | "Opération trop longue. Réessaie." | Timeout générique |
| `NETWORK_ERROR` | 502 | ✅ | "Problème réseau. Vérifie ta connexion." | |
| `CIRCUIT_BREAKER_OPEN` | 503 | ✅ | "Service en pause suite à des erreurs. Réessaie dans {retry_after}s." | Circuit breaker actif |

---

## 11. CATÉGORIE 7 — LLM ET AGENTS IA

### 11.1 — Tableau complet

| Code | HTTP | retry_able | Message FR | Contexte |
|---|---|---|---|---|
| `LLM_PROVIDER_DOWN` | 502 | ✅ | "L'IA est momentanément indisponible. Réessaie." | Anthropic API down |
| `LLM_TIMEOUT` | 504 | ✅ | "L'IA met trop de temps. Réessaie." | >30s timeout |
| `LLM_EMPTY_RESPONSE` | 502 | ✅ | "L'IA n'a pas répondu. Réessaie." | Response vide |
| `LLM_INVALID_RESPONSE` | 502 | ✅ | "Réponse IA mal formée. Réessaie." | Parse fail |
| `LLM_CONTEXT_TOO_LONG` | 413 | ❌ | "Conversation trop longue. Démarre une nouvelle." | >200K tokens |
| `LLM_AUTHENTICATION_FAILED` | 502 | ❌ | "Problème d'authentification IA. Contacte le support." | API key révoquée |
| `LLM_QUOTA_PROVIDER_EXCEEDED` | 502 | ✅ | "Quota IA provider dépassé. Le support est prévenu." | Anthropic quota |
| `LLM_MODEL_UNAVAILABLE` | 502 | ✅ | "Modèle IA indisponible, fallback actif." | Modèle deprecated/down |
| `LLM_FALLBACK_EXHAUSTED` | 502 | ❌ | "Tous les modèles IA sont indisponibles. Réessaie plus tard." | Chain fallback épuisée |
| `AGENT_UNKNOWN` | 400 | ❌ | "Agent IA inconnu." | Mauvais nom agent |
| `AGENT_DISABLED` | 503 | ✅ | "Cet agent est temporairement désactivé." | Circuit breaker agent |
| `AGENT_TASK_FAILED` | 500 | ✅ | "L'agent n'a pas pu exécuter la tâche. Réessaie." | Task exception |
| `SUPERVISOR_ROUTING_FAILED` | 500 | ✅ | "Impossible de router ta demande. Réessaie." | Supervisor intent fail |
| `CONFIDENCE_TOO_LOW` | 200* | ❌ | (pas une erreur, juste warning UI) "Je ne suis pas sûr de ma réponse. Demande validation humaine." | <60% confidence |
| `BUDGET_EXCEEDED_AGENT` | 429 | ❌ | "Budget IA de cet agent dépassé. Réessaie plus tard." | Budget par agent |
| `MAX_ROUNDS_REACHED` | 500 | ❌ | "L'IA a atteint le nombre max d'itérations. Réessaie avec une demande plus simple." | MAX_ROUNDS=50 |
| `TOOL_CALL_FAILED` | 500 | ✅ | "L'IA n'a pas pu exécuter une action. Réessaie." | Tool exception |
| `PROMPT_INJECTION_DETECTED` | 400 | ❌ | "Requête refusée pour raisons de sécurité." | Detected injection attempt |

---

## 12. CATÉGORIE 8 — VOICE (STT + TTS)

Voir `docs/VOICE.md`.

### 12.1 — Tableau complet

| Code | HTTP | retry_able | Message FR | Contexte |
|---|---|---|---|---|
| `STT_FAILED` | 502 | ✅ | "Impossible de comprendre ton vocal. Réessaie ou tape." | Whisper + Deepgram échoués |
| `STT_WHISPER_DOWN` | 502 | ✅ | "Service de transcription indisponible. Fallback actif." | Whisper seul down |
| `STT_AUDIO_TOO_SHORT` | 400 | ❌ | "Vocal trop court (minimum 1s)." | <1s |
| `STT_AUDIO_TOO_LONG` | 413 | ❌ | "Vocal trop long (maximum 3 minutes)." | >180s |
| `STT_AUDIO_EMPTY` | 400 | ❌ | "Vocal vide." | <500 bytes |
| `STT_FILE_TOO_LARGE` | 413 | ❌ | "Fichier audio trop volumineux (max 10 Mo)." | >10MB |
| `STT_UNSUPPORTED_FORMAT` | 400 | ❌ | "Format audio non supporté." | Pas m4a/mp3/ogg/webm |
| `STT_LANGUAGE_UNKNOWN` | 422 | ❌ | "Langue non détectée. Précise ta langue dans les settings." | No language detected |
| `STT_NO_SPEECH` | 200* | ❌ | "Je n'ai rien entendu. Essaie à nouveau." | Transcript vide |
| `STT_LOW_CONFIDENCE` | 200* | ❌ | "J'ai peut-être mal compris. Tu peux répéter ?" | Confidence <0.5 |
| `TTS_FAILED` | 502 | ✅ | "Synthèse vocale indisponible. Réponse en texte." | ElevenLabs + OpenAI TTS down |
| `TTS_ELEVENLABS_DOWN` | 502 | ✅ | "Voix principale indisponible. Fallback voix standard." | ElevenLabs seul down |
| `TTS_TEXT_TOO_LONG` | 413 | ❌ | "Texte trop long pour synthèse vocale." | >5K chars |
| `TTS_INVALID_VOICE` | 400 | ❌ | "Voix invalide." | Voice ID inconnu |
| `TTS_QUOTA_EXCEEDED` | 429 | ✅ | "Quota TTS atteint. Bascule en texte." | ElevenLabs monthly quota |

*Code 200 avec warning UI au lieu d'erreur bloquante

---

## 13. CATÉGORIE 9 — WHATSAPP

Voir `docs/WHATSAPP.md` §21.

### 13.1 — Tableau complet

| Code | HTTP | retry_able | Message FR | Contexte |
|---|---|---|---|---|
| `WHATSAPP_SIGNATURE_INVALID` | 403 | ❌ | "Signature webhook invalide." | HMAC fail |
| `WHATSAPP_VERIFY_TOKEN_INVALID` | 403 | ❌ | "Token vérification invalide." | GET webhook |
| `WHATSAPP_SENDER_UNKNOWN` | 404 | ❌ | "Expéditeur inconnu." | Phone not in users/clients |
| `WHATSAPP_API_DOWN` | 502 | ✅ | "WhatsApp momentanément indisponible." | Meta Cloud down |
| `WHATSAPP_API_TIMEOUT` | 504 | ✅ | "Meta API timeout." | |
| `WHATSAPP_OUTSIDE_SESSION_WINDOW` | 422 | ❌ | "Impossible d'envoyer : session 24h fermée. Le client doit répondre d'abord." | Meta 24h rule |
| `WHATSAPP_TEMPLATE_NOT_APPROVED` | 422 | ❌ | "Template WhatsApp pas encore validé par Meta." | |
| `WHATSAPP_TEMPLATE_NOT_FOUND` | 404 | ❌ | "Template WhatsApp inconnu." | |
| `WHATSAPP_INVALID_TEMPLATE_PARAMS` | 400 | ❌ | "Paramètres template invalides." | Variables manquantes |
| `WHATSAPP_MEDIA_TOO_LARGE` | 413 | ❌ | "Fichier trop gros pour WhatsApp ({max})." | |
| `WHATSAPP_MEDIA_UNSUPPORTED_TYPE` | 422 | ❌ | "Type de média non supporté par WhatsApp." | |
| `WHATSAPP_RATE_LIMITED` | 429 | ✅ | "Trop de messages WhatsApp d'un coup." | Meta rate limit |
| `WHATSAPP_QUALITY_RATING_RED` | 503 | ❌ | "Qualité WhatsApp dégradée. Contacte le support." | Meta quality red |
| `WHATSAPP_PHONE_NOT_VALID` | 400 | ❌ | "Numéro WhatsApp invalide." | Pas de compte WA |
| `WHATSAPP_RECIPIENT_BLOCKED` | 403 | ❌ | "Destinataire a bloqué ton numéro." | |
| `WHATSAPP_RECIPIENT_OPT_OUT` | 403 | ❌ | "Destinataire a fait opt-out." | STOP envoyé |
| `WHATSAPP_NUMBER_NOT_CONFIGURED` | 500 | ❌ | "Numéro WhatsApp non configuré." | Config manquante |
| `WHATSAPP_TOKEN_EXPIRED` | 502 | ❌ | "Token WhatsApp expiré. Le support est prévenu." | 60j rotation ratée |

---

## 14. CATÉGORIE 10 — STRIPE (BILLING)

### 14.1 — Tableau complet

| Code | HTTP | retry_able | Message FR | Contexte |
|---|---|---|---|---|
| `STRIPE_API_DOWN` | 502 | ✅ | "Service de paiement momentanément indisponible." | Stripe down |
| `STRIPE_WEBHOOK_SIGNATURE_INVALID` | 403 | ❌ | "Signature webhook Stripe invalide." | |
| `STRIPE_CUSTOMER_NOT_FOUND` | 404 | ❌ | "Compte Stripe introuvable." | |
| `STRIPE_SUBSCRIPTION_NOT_FOUND` | 404 | ❌ | "Abonnement Stripe introuvable." | |
| `STRIPE_PRICE_NOT_FOUND` | 404 | ❌ | "Tarif introuvable." | Price ID invalide |
| `STRIPE_CARD_DECLINED` | 402 | ❌ | "Carte refusée. Essaie une autre CB." | |
| `STRIPE_INSUFFICIENT_FUNDS` | 402 | ❌ | "Fonds insuffisants sur la carte." | |
| `STRIPE_CARD_EXPIRED` | 402 | ❌ | "Carte expirée. Mets à jour ta CB." | |
| `STRIPE_PAYMENT_METHOD_REQUIRED` | 402 | ❌ | "Ajoute un moyen de paiement." | |
| `STRIPE_INVALID_COUPON` | 400 | ❌ | "Code promo invalide ou expiré." | |
| `STRIPE_COUPON_NOT_APPLICABLE` | 400 | ❌ | "Ce code promo n'est pas applicable à ton plan." | |
| `STRIPE_INVOICE_FAILED` | 402 | ❌ | "Prélèvement échoué. Régularise ton paiement." | |
| `STRIPE_REFUND_FAILED` | 500 | ✅ | "Remboursement échoué. Contacte le support." | |
| `STRIPE_CHECKOUT_SESSION_EXPIRED` | 410 | ❌ | "Session de paiement expirée. Refais la demande." | |
| `STRIPE_FEDERATION_CODE_VERIFICATION_FAILED` | 422 | ❌ | "Vérification CAPEB/FFB échouée. Envoie ton justificatif." | |
| `STRIPE_ADDON_INCOMPATIBLE_WITH_PLAN` | 422 | ❌ | "Cet add-on n'est pas compatible avec ton plan." | Téléphone+Starter |

---

## 15. CATÉGORIE 11 — MÉMOIRE (MEM0 + MEMPALACE)

Voir `docs/MEMORY-STRATEGY.md` §11.

### 15.1 — Tableau complet

| Code | HTTP | retry_able | Message FR | Contexte |
|---|---|---|---|---|
| `MEMORY_MEM0_DOWN` | 502 | ✅ | "Mémoire rapide indisponible." | Mem0 cloud down |
| `MEMORY_MEMPALACE_DOWN` | 502 | ✅ | "Mémoire profonde indisponible. Mode dégradé actif." | MemPalace Railway down |
| `MEMORY_SYNC_FAILED` | 500 | ✅ | "Synchronisation mémoire échouée." | |
| `MEMORY_BACKUP_FAILED` | 500 | ✅ | "Backup mémoire échoué. Alerte envoyée au support." | |
| `MEMORY_QUERY_TIMEOUT` | 504 | ✅ | "Recherche mémoire trop longue." | >1s recall |
| `MEMORY_STORE_FAILED` | 500 | ✅ | "Impossible de sauvegarder la mémoire." | |
| `MEMORY_INVALID_LAYER` | 400 | ❌ | "Niveau de mémoire invalide." | user/session/agent/org |
| `MEMORY_MIGRATION_IN_PROGRESS` | 503 | ✅ | "Migration mémoire en cours. Réessaie dans 5 minutes." | MemPalace→Mem0 runbook |
| `MEMORY_DEGRADED_READONLY` | 503 | ❌ | "Mémoire en lecture seule." | State B mode |

---

## 16. CATÉGORIE 12 — SIGNATURE YOUSIGN

### 16.1 — Tableau complet

| Code | HTTP | retry_able | Message FR | Contexte |
|---|---|---|---|---|
| `YOUSIGN_API_DOWN` | 502 | ✅ | "Service de signature indisponible." | |
| `YOUSIGN_DOCUMENT_CREATE_FAILED` | 502 | ✅ | "Création document signature échouée." | |
| `YOUSIGN_INVALID_SIGNER_EMAIL` | 400 | ❌ | "Email du signataire invalide." | |
| `YOUSIGN_SIGNATURE_EXPIRED` | 410 | ❌ | "Lien de signature expiré. Renvoie le devis." | |
| `YOUSIGN_SIGNATURE_DECLINED` | 409 | ❌ | "Le client a refusé de signer." | |
| `YOUSIGN_PDF_INVALID` | 422 | ❌ | "PDF invalide pour signature." | |
| `YOUSIGN_QUOTA_EXCEEDED` | 429 | ❌ | "Quota signatures atteint (500/an). Contacte le support." | |
| `YOUSIGN_WEBHOOK_INVALID` | 403 | ❌ | "Webhook Yousign invalide." | |

---

## 17. CATÉGORIE 13 — OCR + VISION IA

### 17.1 — Tableau complet

| Code | HTTP | retry_able | Message FR | Contexte |
|---|---|---|---|---|
| `OCR_FAILED` | 502 | ✅ | "Lecture du ticket échouée. Réessaie avec une photo plus nette." | Claude Vision fail |
| `OCR_LOW_CONFIDENCE` | 200* | ❌ | (warning) "Je suis pas sûr de la lecture. Vérifie." | <60% confidence |
| `OCR_IMAGE_TOO_DARK` | 400 | ❌ | "Photo trop sombre. Utilise le flash." | |
| `OCR_IMAGE_BLURRY` | 400 | ❌ | "Photo floue. Refais-la bien nette." | |
| `OCR_NO_TEXT_DETECTED` | 200* | ❌ | "Aucun texte détecté. C'est bien un ticket ?" | |
| `OCR_UNSUPPORTED_DOCUMENT` | 400 | ❌ | "Type de document non supporté." | |
| `VISION_ANALYSIS_FAILED` | 502 | ✅ | "Analyse photo échouée. Réessaie." | |
| `VISION_IMAGE_TOO_LARGE` | 413 | ❌ | "Image trop grande (max 15 Mo). Compresse-la." | |
| `VISION_UNSUPPORTED_FORMAT` | 400 | ❌ | "Format image non supporté (jpg/png/heic)." | |
| `VISION_COMPETITOR_QUOTE_PARSE_FAILED` | 200* | ❌ | "Impossible de parser ce devis concurrent complètement." | F121 |

---

## 18. CATÉGORIE 14 — FACTUR-X / PA

Voir `docs/METIER.md` §Factur-X.

### 18.1 — Tableau complet

| Code | HTTP | retry_able | Message FR | Contexte |
|---|---|---|---|---|
| `FACTURX_PA_DOWN` | 502 | ✅ | "Plateforme Agréée indisponible. La facture sera envoyée dès que possible." | FactPulse/B2Brouter down |
| `FACTURX_PDF_GENERATION_FAILED` | 500 | ✅ | "Génération PDF Factur-X échouée." | |
| `FACTURX_XML_VALIDATION_FAILED` | 500 | ❌ | "XML EN 16931 invalide. Le support est prévenu." | Bug côté STRUCTORAI |
| `FACTURX_MISSING_FIELD` | 422 | ❌ | "Champ obligatoire Factur-X manquant : {field}." | |
| `FACTURX_PA_REJECTED` | 422 | ❌ | "Facture rejetée par la Plateforme Agréée : {reason}." | PA refuse |
| `FACTURX_RECIPIENT_NOT_REGISTERED` | 422 | ❌ | "Destinataire non inscrit au répertoire PPF (réception)." | |
| `FACTURX_SIRET_INVALID` | 422 | ❌ | "SIRET destinataire invalide." | |

---

## 19. CATÉGORIE 15 — FISCALITÉ + MÉTIER BTP

### 19.1 — Tableau complet

| Code | HTTP | retry_able | Message FR | Contexte |
|---|---|---|---|---|
| `FISCAL_THRESHOLD_APPROACHING` | 200* | ❌ | (alerte) "Tu approches un seuil fiscal : {seuil}." | Pas erreur |
| `FISCAL_RGE_VALIDATION_FAILED` | 502 | ✅ | "Vérification RGE échouée. Réessaie." | data.gouv.fr down |
| `FISCAL_RGE_INVALID` | 422 | ❌ | "Certification RGE invalide ou expirée. TVA 5,5% impossible." | F128 |
| `FISCAL_RGE_MISSING` | 422 | ❌ | "TVA 5,5% nécessite une certification RGE valide." | |
| `METIER_TVA_RULE_NOT_FOUND` | 500 | ❌ | "Règle TVA introuvable pour ce cas." | Seed data incomplet |
| `METIER_MENTION_NOT_FOUND` | 500 | ❌ | "Mention obligatoire introuvable." | |
| `METIER_REFERENCE_PRICE_NOT_FOUND` | 404 | ❌ | "Prix de référence non trouvé pour {poste}." | data/prix/*.json |
| `METIER_INCONSISTENT_TVA` | 422 | ❌ | "TVA incohérente sur les lignes du devis." | Mix illogique |
| `METIER_UNKNOWN_POSTE` | 400 | ❌ | "Poste métier inconnu : {poste}." | |
| `METIER_REGION_COEFFICIENT_NOT_FOUND` | 404 | ❌ | "Coefficient régional non trouvé pour cette zone." | |

---

## 20. CATÉGORIE 16 — AI ACT COMPLIANCE

Voir `docs/AI-ACT-COMPLIANCE.md`.

### 20.1 — Tableau complet

| Code | HTTP | retry_able | Message FR | Contexte |
|---|---|---|---|---|
| `AI_ACT_HUMAN_VALIDATION_REQUIRED` | 200* | ❌ | (info) "Cette action nécessite ta validation humaine." | Supervision obligatoire |
| `AI_ACT_DISCLAIMER_NOT_ACKNOWLEDGED` | 422 | ❌ | "Accepte le disclaimer Coach avant de continuer." | Coach flow |
| `AI_ACT_AUDIT_LOG_FAILED` | 500 | ✅ | "Log audit IA échoué." | Insert ai_decisions fail |
| `AI_ACT_CONFIDENCE_TOO_LOW` | 200* | ❌ | (info) "Je ne suis pas sûr. Validation humaine recommandée." | <60% |
| `AI_ACT_REDIRECT_TO_PROFESSIONAL` | 200* | ❌ | (info) "Cette question nécessite un professionnel qualifié." | Coach exclusion |
| `AI_ACT_PROHIBITED_PRACTICE` | 403 | ❌ | "Demande refusée (pratique interdite AI Act)." | Art. 5 violation |

---

## 21. COMPORTEMENT CLIENT FACE AUX ERREURS

### 21.1 — Patterns mobile

**Erreur 401 AUTH_TOKEN_EXPIRED** :
- Tenter refresh token automatiquement
- Si refresh fail → redirect login
- Aucune interaction utilisateur nécessaire

**Erreur 402 PLAN_LIMIT_REACHED** :
- Afficher modal avec CTA "Upgrade"
- Lien vers billing portal

**Erreur 403 FORBIDDEN** :
- Alert + log analytics
- Redirect dashboard

**Erreur 404** :
- Toast discret "Ressource introuvable"
- Redirect page parent

**Erreur 409 CONCURRENT_MODIFICATION** :
- Recharger automatiquement les données
- Toast "Contenu mis à jour, vérifie"

**Erreur 422 VALIDATION_** :
- Afficher erreur inline sur le champ concerné

**Erreur 429 RATE_LIMIT** :
- Toast "Réessaie dans X secondes"
- Désactiver bouton pendant delay
- Retry automatique après delay

**Erreur 5xx avec retry_able=true** :
- Retry automatique 3× avec backoff exponentiel
- Si tous retries fail → modal "Problème technique, réessaie plus tard" + bouton support

### 21.2 — Exemple React Native

```typescript
// mobile/lib/api-client.ts
async function apiCall(endpoint: string, options: RequestInit) {
  try {
    const response = await fetch(endpoint, options);
    
    if (!response.ok) {
      const error = await response.json();
      handleApiError(error.error, response.status);
      throw new Error(error.error.code);
    }
    
    return response.json();
  } catch (err) {
    // Network error
    throw err;
  }
}

function handleApiError(error: ApiError, status: number) {
  switch (error.code) {
    case "AUTH_TOKEN_EXPIRED":
      refreshToken();
      break;
    case "PLAN_LIMIT_REACHED":
      showUpgradeModal(error.details);
      break;
    case "RATE_LIMIT_EXCEEDED":
      showToast(`Réessaie dans ${error.retry_after_seconds}s`);
      break;
    case "LLM_PROVIDER_DOWN":
    case "STT_FAILED":
    case "TTS_FAILED":
      if (error.retry_able) {
        scheduleRetry();
      }
      break;
    default:
      showErrorToast(error.message);
  }
}
```

---

## 22. RETRY STRATEGIES

### 22.1 — Exponentiel

Pour erreurs `retry_able=true`, retry avec backoff :

| Tentative | Délai |
|---|---|
| 1 | 1s |
| 2 | 5s |
| 3 | 30s |
| 4 | 2 min |
| 5 | 10 min |

Puis dead letter queue + alerte Sentry.

### 22.2 — Retry quand

✅ **Retry automatique** :
- 502, 503, 504 avec `retry_able=true`
- `LLM_PROVIDER_DOWN`, `WHATSAPP_API_DOWN`, `STRIPE_API_DOWN`
- `DATABASE_TIMEOUT`, `NETWORK_ERROR`
- `STT_WHISPER_DOWN` (fallback Deepgram), `TTS_ELEVENLABS_DOWN` (fallback OpenAI)

❌ **Pas de retry** :
- 4xx (erreurs client, retry inutile)
- `*_NOT_FOUND`
- `VALIDATION_*`
- `AUTH_*_EXPIRED` (refresh à la place)
- `PLAN_LIMIT_REACHED`

### 22.3 — Circuit breaker

Voir `docs/ARCH.md` §12. Après 5 erreurs 5xx consécutives sur un service :
- Désactivation 5 min
- Tous les appels retournent `CIRCUIT_BREAKER_OPEN`
- Tentative de réouverture après 5 min

---

## 23. LOGGING ET SENTRY

### 23.1 — Levels

| Log level | Quand | Envoyé Sentry ? |
|---|---|---|
| `INFO` | Erreurs "normales" (404, validation) | ❌ (juste counter) |
| `WARNING` | Erreurs 409, quota, auth | ⚠️ (aggregated) |
| `ERROR` | 5xx, provider down | ✅ |
| `CRITICAL` | Data corruption, fraude | ✅ + SMS Fabrice |

### 23.2 — Scrubbing Sentry

Voir `docs/ENV.md` §24.3 — dénylist des secrets à scrubber :

```python
# backend/app/utils/scrubbing.py

SCRUB_KEYS = {
    "password", "token", "api_key", "secret", "authorization",
    "anthropic_api_key", "stripe_secret_key", "whatsapp_access_token",
    # ... voir ENV.md
}

def scrub_dict(data: dict) -> dict:
    """Récursivement retire les secrets."""
    scrubbed = {}
    for k, v in data.items():
        if any(key in k.lower() for key in SCRUB_KEYS):
            scrubbed[k] = "[SCRUBBED]"
        elif isinstance(v, dict):
            scrubbed[k] = scrub_dict(v)
        elif isinstance(v, list):
            scrubbed[k] = [scrub_dict(i) if isinstance(i, dict) else i for i in v]
        else:
            scrubbed[k] = v
    return scrubbed
```

### 23.3 — Structured logging

Format JSON :

```json
{
  "timestamp": "2026-04-18T14:32:05.123Z",
  "level": "error",
  "trace_id": "550e8400-...",
  "error_code": "LLM_PROVIDER_DOWN",
  "http_status": 502,
  "endpoint": "/v1/chat",
  "user_id": "usr_...",
  "organization_id": "org_...",
  "details": { "provider": "anthropic", "fallback_attempted": true },
  "message": "Anthropic API returned 5xx"
}
```

### 23.4 — Sentry fingerprinting

Pour éviter doublons :

```python
raise LLMError(
    code="LLM_PROVIDER_DOWN",
    message_fr="L'IA est momentanément indisponible.",
    sentry_fingerprint=["llm-provider-down", "anthropic"],  # Groupe tous ensemble
)
```

---

## 24. MONITORING ET ALERTES

### 24.1 — Seuils critiques (SMS Fabrice)

| Condition | Alerte |
|---|---|
| `INTERNAL_SERVER_ERROR` > 10 en 5 min | SMS + Slack |
| `LLM_FALLBACK_EXHAUSTED` > 3 en 10 min | SMS + Slack |
| `DATABASE_DOWN` > 2 min | SMS + Slack |
| `WHATSAPP_TOKEN_EXPIRED` | SMS immédiat |
| `STRIPE_API_DOWN` > 5 min | SMS + Slack |
| Any `CRITICAL` level | SMS immédiat |

### 24.2 — Seuils warning (Slack)

| Condition | Alerte |
|---|---|
| Erreurs 5xx > 5% sur 5 min | Slack |
| `LLM_TIMEOUT` > 20 en 10 min | Slack |
| `STT_FAILED` > 30 en 10 min | Slack |
| `CIRCUIT_BREAKER_OPEN` | Slack |
| `MEMORY_DEGRADED_READONLY` | Slack |

### 24.3 — Dashboard erreurs

`/admin/errors-dashboard` :
- Top 10 erreurs dernières 24h
- Trend par catégorie
- P95 latence par endpoint
- Error rate par plan (Starter vs Pro vs Business)

---

## 25. I18N MESSAGES

### 25.1 — Structure

Les messages sont définis dans le code Python avec `message_fr` et `message_en` directement.

Pour les autres langues (TR/PT/AR/ES), un mapping à part :

```python
# backend/app/i18n/errors_translations.py

ERROR_TRANSLATIONS = {
    "DEVIS_NOT_FOUND": {
        "fr": "Le devis demandé n'existe pas ou ne t'appartient pas",
        "en": "The requested devis does not exist or is not yours",
        "tr": "İstenen teklif mevcut değil veya size ait değil",
        "pt": "O orçamento solicitado não existe ou não é seu",
        "ar": "الاقتباس المطلوب غير موجود أو ليس لك",
        "es": "El presupuesto solicitado no existe o no te pertenece",
    },
    # ... toutes les erreurs critiques user-facing
}
```

### 25.2 — Fallback

Si langue pas trouvée → anglais, puis français si pas anglais.

### 25.3 — Templating

Les messages peuvent contenir des variables `{variable}` remplacées par `details` :

```python
message_fr = "Limite de devis/mois atteinte ({limit})."
details = {"limit": 5}
# → "Limite de devis/mois atteinte (5)."
```

---

## 26. ANTI-PATTERNS

### 26.1 — À NE JAMAIS FAIRE

1. ❌ `raise Exception("quelque chose a foiré")` — utiliser `StructorAIError`
2. ❌ Exposer stack traces à l'utilisateur
3. ❌ Messages utilisateur en anglais (sauf fallback)
4. ❌ Mettre un secret dans `details`
5. ❌ Retourner 500 pour une erreur 4xx
6. ❌ `retry_able=True` pour une erreur 4xx (retry inutile)
7. ❌ Avaler une erreur silencieusement (sans log)
8. ❌ Créer un code d'erreur sans le documenter ici
9. ❌ Utiliser le même code pour 2 situations différentes
10. ❌ Message technique dans `message_fr` ("NULL constraint violation...")

### 26.2 — À FAIRE

1. ✅ Message clair pour l'artisan ("Ton devis est introuvable")
2. ✅ Trace ID dans chaque erreur
3. ✅ Log au bon niveau (INFO/WARNING/ERROR/CRITICAL)
4. ✅ Scrubbing des secrets
5. ✅ Retry automatique si applicable
6. ✅ Documenter nouveaux codes ici AVANT implémentation
7. ✅ Tester les chemins d'erreur (pas juste le happy path)
8. ✅ Fingerprinting Sentry pour erreurs récurrentes
9. ✅ Monitoring proactif (dashboard admin)
10. ✅ i18n messages critiques user-facing

---

## 27. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/API.md` §5 | Format erreur API |
| `docs/ARCH.md` §12 | Résilience + circuit breakers |
| `docs/AGENTS.md` | Erreurs par agent |
| `docs/VOICE.md` | Erreurs STT/TTS détaillées |
| `docs/WHATSAPP.md` §21 | Erreurs Meta |
| `docs/MEMORY-STRATEGY.md` §11 | Erreurs mémoire |
| `docs/MIGRATIONS.md` | ai_decisions.error_code |
| `docs/SUPPORT-STRATEGY.md` | Escalade sur erreurs critiques |
| `docs/DEPLOY.md` §13 | Smoke tests sur codes erreur |
| `docs/AI-ACT-COMPLIANCE.md` | Audit log + erreurs AI Act |
| `docs/ENV.md` §24 | Scrubbing Sentry |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` | Plans limits + quotas |
| `docs/TESTS.md` (à créer) | Tests paths d'erreur |
| `SECURITE.md` | Scrubbing + RLS + rate limiting |
| `BUILD_PLAN.md` | `backend/app/utils/errors.py` |

### 27.1 — Références externes

- **Pattern AgentShield** : repo Anthropic security helper (référence)
- **Sentry Fingerprinting** : https://docs.sentry.io/platforms/python/usage/sdk-fingerprinting/
- **HTTP Status Codes** : https://developer.mozilla.org/en-US/docs/Web/HTTP/Status
- **FastAPI Exception Handlers** : https://fastapi.tiangolo.com/tutorial/handling-errors/

---

> **Ce fichier est le catalogue exhaustif des erreurs STRUCTORAI.**
> **~180 codes d'erreur** documentés, groupés en **16 catégories**.
> **Règle d'or n°1** : tout nouveau code DOIT être ajouté ici AVANT implémentation.
> **Règle d'or n°2** : un code = une erreur unique (pas de réutilisation pour cas différents).
> **Règle d'or n°3** : messages utilisateur en français clair, jamais de technique.
