---
name: WHATSAPP
description: Intégration complète Meta Cloud API WhatsApp Business pour STRUCTORAI. Couvre le canal 3 (quand l'artisan n'est PAS sur l'app). Setup Meta Business, vérification numéro, webhook endpoint, authentification OTP numéro artisan, réception vocal/photo/texte, envoi messages session et templates pré-approuvés, coûts par conversation 0.0025-0.12€, handling inbound client (Agent Téléphone V2 + F87 auto-création fiche prospect + F60 photo), gestion session 24h Meta, templates à faire approuver, conformité numéro Green Tick Business Verification, rate limits Meta.
type: technical-integration
scope: backend-whatsapp-service, webhook-handler, meta-cloud-api
priority: critical
date: 2026-04-18
version: 1.0
provider: Meta Cloud API (WhatsApp Business Platform)
api_version: v20.0 (current)
cost_business_initiated: 0.0466 € / conversation Marketing
cost_user_initiated: 0.0068 € / conversation User Service
cost_authentication: 0.0160 € / conversation Auth (OTP)
---

# docs/WHATSAPP.md — Intégration WhatsApp Business

> **Ce fichier est la source de vérité pour l'intégration WhatsApp.**
> WhatsApp est le **canal 3** de STRUCTORAI (voir `docs/ARCH.md` §3.3) : pour l'artisan quand il n'est PAS sur l'app + pour les clients finaux qui contactent l'artisan.
> Reference : `docs/API.md` §28.2 (webhook) + `docs/AGENTS.md` §16 (Agent Téléphone V2).
> Différenciation : **aucun concurrent BTP** n'a de WhatsApp bidirectionnel (seul Renalto fait de la dictée WhatsApp, pas de pipeline complet).

---

## 0. SOMMAIRE

1. [Vue d'ensemble — les 3 cas d'usage WhatsApp](#1-vue-densemble--les-3-cas-dusage-whatsapp)
2. [Architecture d'intégration](#2-architecture-dintégration)
3. [Setup Meta Business](#3-setup-meta-business)
4. [Numéro WhatsApp Business STRUCTORAI](#4-numéro-whatsapp-business-structorai)
5. [Green Tick Business Verification](#5-green-tick-business-verification)
6. [Webhook endpoint](#6-webhook-endpoint)
7. [Authentification de l'artisan (OTP lors de l'onboarding)](#7-authentification-de-lartisan-otp-lors-de-lonboarding)
8. [Réception messages artisan (inbound artisan)](#8-réception-messages-artisan-inbound-artisan)
9. [Réception messages clients (inbound client vers artisan)](#9-réception-messages-clients-inbound-client-vers-artisan)
10. [Envoi messages session (24h window)](#10-envoi-messages-session-24h-window)
11. [Envoi messages templates (hors session 24h)](#11-envoi-messages-templates-hors-session-24h)
12. [Templates pré-approuvés à créer](#12-templates-pré-approuvés-à-créer)
13. [Types de contenus supportés](#13-types-de-contenus-supportés)
14. [Coûts par conversation](#14-coûts-par-conversation)
15. [Rate limits Meta](#15-rate-limits-meta)
16. [Flow inbound client → fiche prospect automatique (F87)](#16-flow-inbound-client--fiche-prospect-automatique-f87)
17. [Flow photo WhatsApp → chantier (F60)](#17-flow-photo-whatsapp--chantier-f60)
18. [Mapping WhatsApp ↔ Organisation ↔ User](#18-mapping-whatsapp--organisation--user)
19. [Backend — `whatsapp_service.py`](#19-backend--whatsapp_servicepy)
20. [Conformité RGPD + AI Act](#20-conformité-rgpd--ai-act)
21. [Gestion des erreurs Meta](#21-gestion-des-erreurs-meta)
22. [Plan de migration vers Agent Téléphone V2 (F126)](#22-plan-de-migration-vers-agent-téléphone-v2-f126)
23. [Tests et validation](#23-tests-et-validation)
24. [Troubleshooting](#24-troubleshooting)
25. [Références croisées](#25-références-croisées)

---

## 1. VUE D'ENSEMBLE — LES 3 CAS D'USAGE WHATSAPP

### 1.1 — Cas 1 : Artisan → Cerveau (canal alternatif à l'app)

L'artisan a **son propre numéro personnel** qu'il utilise pour WhatsApp. Il envoie vocal/photo/texte au **numéro WhatsApp Business STRUCTORAI** (identifié comme "cerveau IA" dans ses contacts).

**Exemples** :
- "Fais-moi un devis pour Dupont, SDB complète 8m²" (vocal)
- Envoie photo d'un ticket de caisse → Compta OCR
- Envoie photo d'un chantier → Galerie
- "Combien me doit Martin ?" (texte)

**Pourquoi c'est critique** : l'artisan ouvre WhatsApp 15×/jour. L'app STRUCTORAI, 2-3×/jour. **WhatsApp = 80% des interactions rapides** depuis le chantier.

### 1.2 — Cas 2 : Client de l'artisan → Cerveau (inbound prospect)

Un prospect appelle l'artisan. L'artisan ne répond pas (sur chantier). Le prospect envoie un message sur le numéro de l'artisan (qui est en réalité le numéro WhatsApp Business STRUCTORAI que l'artisan utilise comme numéro pro).

**Exemples** :
- "Bonjour, je cherche un plombier pour une fuite urgente"
- Envoi photo d'un dégât → pré-devis IA
- "Vous pouvez venir cette semaine ?"

**Le cerveau prend le relais** :
- Auto-annonce : *"Bonjour, vous êtes bien chez [Nom artisan]. Je suis son assistant IA..."* (transparence AI Act)
- Collecte infos structurées
- Crée fiche prospect auto (F87)
- Notifie l'artisan

### 1.3 — Cas 3 : Cerveau → Client de l'artisan (outbound)

L'artisan valide une action → le cerveau envoie au client via WhatsApp :

**Exemples** :
- Envoi du devis (PDF en pièce jointe)
- Relance J+3 sur devis non signé
- Relance facture impayée
- Demande d'avis Google post-chantier

**Coût maîtrisé** : chaque envoi outbound hors session = template Meta (~$0.05-0.12). Usage principal des **templates pré-approuvés** (voir §12).

### 1.4 — Répartition estimée du volume

| Cas | % du volume messages | Coût moyen |
|---|---|---|
| Cas 1 (artisan → cerveau) | 70% | 0€ (user-initiated) |
| Cas 2 (client → cerveau) | 20% | 0€ (user-initiated) |
| Cas 3 (cerveau → client) | 10% | ~0.05€ (template) |

**Total coût WhatsApp** estimé par artisan : **~1-3€/mois** (couvert par la marge Pro/Business).

---

## 2. ARCHITECTURE D'INTÉGRATION

### 2.1 — Diagramme

```
┌───────────────────────────────────────────────────────────┐
│                  WhatsApp Business Platform                │
│                     (Meta Cloud API)                       │
└──────────────────┬──────────────────┬─────────────────────┘
                   │                  │
        Inbound    │                  │   Outbound
       (webhook)   │                  │ (API calls)
                   ▼                  │
┌──────────────────────────────┐     │
│  POST /v1/whatsapp/webhook   │     │
│  (FastAPI endpoint)          │     │
└──────────────┬───────────────┘     │
               │                      │
               ▼                      │
┌──────────────────────────────┐     │
│   whatsapp_service.py        │◄────┘
│   ├── verify_webhook()       │
│   ├── parse_incoming()       │
│   ├── identify_sender()      │
│   ├── route_to_supervisor()  │
│   └── send_message()         │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│      Supervisor              │
│  (pattern Ouroboros)         │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│    Agents appropriés         │
│  (Devis, Vision, Téléphone   │
│   V2, etc.)                  │
└──────────────────────────────┘
```

### 2.2 — Stack Meta

- **API** : Meta Cloud API v20.0 (REST JSON)
- **Endpoints** :
  - `https://graph.facebook.com/v20.0/{PHONE_NUMBER_ID}/messages` (send)
  - `https://graph.facebook.com/v20.0/{PHONE_NUMBER_ID}/media` (upload media)
  - `https://graph.facebook.com/v20.0/{MEDIA_ID}` (download media)
  - `https://graph.facebook.com/v20.0/{PHONE_NUMBER_ID}/message_templates` (templates)
- **Auth** : Bearer token (permanent access token généré dans Meta Business)
- **Webhook** : Meta → STRUCTORAI via POST JSON (signature SHA-256 vérifiée)

### 2.3 — Variables d'environnement

Voir `docs/ENV.md` (à créer). Extraits :

```bash
WHATSAPP_PHONE_NUMBER_ID=123456789012345
WHATSAPP_BUSINESS_ACCOUNT_ID=987654321098765
WHATSAPP_ACCESS_TOKEN=EAAxxxxxx...        # Permanent (rotatif tous les 60j)
WHATSAPP_VERIFY_TOKEN=structorai_wh_verify_xxx  # Token custom généré par STRUCTORAI
WHATSAPP_APP_SECRET=xxx                   # Pour vérifier signature webhooks
WHATSAPP_WEBHOOK_URL=https://api.structorai.app/v1/whatsapp/webhook
```

---

## 3. SETUP META BUSINESS

### 3.1 — Prérequis

1. **Compte Facebook Business Manager** — https://business.facebook.com/
2. **Numéro de téléphone dédié** — PAS un numéro personnel (obligatoire Meta)
3. **Site web vérifié** (structorai.app)
4. **Compte de paiement valide** (CB Stripe acceptée par Meta)
5. **Justificatifs entreprise** (extrait Kbis / SIRET pour vérification)

### 3.2 — Étapes de setup

1. Créer une **app Meta Business** de type "Business"
2. Ajouter le produit "WhatsApp" à l'app
3. Ajouter un numéro de téléphone (voir §4)
4. Générer un **Permanent Access Token** (System User admin)
5. Configurer le webhook (voir §6)
6. Créer les templates nécessaires (voir §12)
7. Soumettre pour **Business Verification** (Green Tick — voir §5)

### 3.3 — Permissions app nécessaires

- `whatsapp_business_messaging`
- `whatsapp_business_management`
- `business_management`

### 3.4 — Tests en sandbox

Meta fournit un numéro sandbox gratuit pour tester :
- Numéro sandbox : `+1 555 XXX XXXX`
- Messages **gratuits** mais uniquement vers 5 numéros de test whitelistés
- Pas de templates nécessaires en sandbox
- Utile pour le dev sans coûts

---

## 4. NUMÉRO WHATSAPP BUSINESS STRUCTORAI

### 4.1 — Stratégie numéro

**STRUCTORAI ne fournit PAS un numéro unique pour tout** — modèle impossible (1 client / 1 numéro → WhatsApp considère cela comme du spam).

**2 options à disposition de l'artisan** :

#### Option A — Numéro partagé STRUCTORAI (Starter / Pro)

- 1 seul numéro WhatsApp Business STRUCTORAI (ex: +33 9 XX XX XX XX)
- Tous les artisans reçoivent/envoient via ce numéro
- Routage interne par mapping `phone_number ↔ organization`
- **Limitation** : l'artisan n'a PAS son numéro pro sur WhatsApp → il doit orienter ses clients vers ce numéro partagé
- **Avantage** : zéro setup pour l'artisan, fonctionne en 30s

#### Option B — Numéro dédié artisan (Business)

- L'artisan connecte **son propre numéro WhatsApp pro** à STRUCTORAI via Meta Business
- Process d'onboarding guidé (~15 min)
- **Avantage** : l'artisan garde son numéro commercial, image pro
- **Limitation** : nécessite création Meta Business account par l'artisan

**Plan défaut** :
- Starter : Option A (numéro partagé)
- Pro : Option A ou B au choix
- Business : Option B recommandée (image pro)

### 4.2 — Numéro partagé — routage

Quand un message arrive sur le numéro partagé, STRUCTORAI doit identifier **à quel artisan il est destiné**.

**Mécanismes** :

1. **Mapping explicite** — l'artisan donne son numéro perso WhatsApp à l'onboarding → stocké `users.whatsapp_phone` → tous les messages entrants depuis ce numéro vont chez cet artisan

2. **Mot-clé identifiant** — le client doit écrire "@Dupont" ou "@artisan-martin" en début de message → résolution via mapping `organizations.whatsapp_handle`

3. **Onboarding client** — quand un client écrit pour la première fois sans mot-clé → cerveau demande : *"Bonjour, quel artisan souhaitez-vous contacter ?"*

### 4.3 — Numéro dédié artisan (Option B)

Flow setup :
1. Artisan clique "Connecter mon WhatsApp Business" dans settings
2. Redirect vers Meta Business Embedded Signup Flow (WhatsApp Business Platform)
3. Artisan autorise STRUCTORAI à gérer son numéro
4. Meta retourne `phone_number_id` + `waba_id`
5. STRUCTORAI stocke dans `organizations.whatsapp_phone_number_id`
6. Webhook Meta redirigé vers STRUCTORAI

**Limites Meta** :
- 1 numéro peut être connecté à **1 seul WABA** (WhatsApp Business Account)
- Migration numéro possible depuis WhatsApp Business app classique (perte de l'ancienne app)

---

## 5. GREEN TICK BUSINESS VERIFICATION

### 5.1 — C'est quoi

Le **Green Tick** est la coche verte de vérification Meta à côté du nom d'une entreprise WhatsApp. Indique que l'entreprise est vérifiée et "officielle".

**Pas obligatoire** pour fonctionner. Obligatoire pour afficher le nom commercial.

### 5.2 — Critères d'obtention

- Entreprise vérifiée (Business Verification complete)
- Marque **notable** (presse, site web, réseaux sociaux actifs)
- Volume de messages significatif
- Pas de violations des politiques WhatsApp Business

**Réalité** : c'est difficile à obtenir pour une startup. Pas critique au launch.

### 5.3 — Stratégie STRUCTORAI

- Launch juin 2026 : **SANS Green Tick** (pas bloquant)
- Dossier Green Tick soumis automatiquement quand :
  - >1 000 artisans actifs
  - Presse tech FR (Les Echos, Frenchweb, Maddyness)
  - Site web + réseaux sociaux actifs

---

## 6. WEBHOOK ENDPOINT

### 6.1 — Endpoint

**URL** : `POST /v1/whatsapp/webhook` (production) + `GET` pour vérification initiale Meta.

**Fichier** : `backend/app/api/v1/whatsapp_webhook.py`

### 6.2 — Vérification initiale (GET)

Meta appelle d'abord en GET pour valider que STRUCTORAI "possède" bien l'URL :

```python
@router.get("/webhook")
async def verify_webhook(
    hub_mode: str = Query(alias="hub.mode"),
    hub_verify_token: str = Query(alias="hub.verify_token"),
    hub_challenge: str = Query(alias="hub.challenge"),
):
    if hub_mode == "subscribe" and hub_verify_token == settings.WHATSAPP_VERIFY_TOKEN:
        return PlainTextResponse(hub_challenge)
    raise HTTPException(403, "Invalid verify token")
```

### 6.3 — Réception messages (POST)

```python
@router.post("/webhook")
async def receive_webhook(
    request: Request,
    x_hub_signature_256: str = Header(None),
):
    # 1. Vérifier signature HMAC-SHA256
    body = await request.body()
    if not verify_signature(body, x_hub_signature_256, settings.WHATSAPP_APP_SECRET):
        raise HTTPException(403, "Invalid signature")
    
    # 2. Parser payload
    payload = await request.json()
    
    # 3. Enqueue pour traitement async (ne pas bloquer Meta > 10s)
    await whatsapp_queue.put(payload)
    
    # 4. Retourner 200 OK immédiatement
    return {"status": "received"}
```

### 6.4 — Vérification signature HMAC

```python
import hmac
import hashlib

def verify_signature(body: bytes, signature_header: str, app_secret: str) -> bool:
    if not signature_header or not signature_header.startswith("sha256="):
        return False
    
    expected = hmac.new(
        app_secret.encode(),
        body,
        hashlib.sha256
    ).hexdigest()
    
    actual = signature_header.replace("sha256=", "")
    return hmac.compare_digest(expected, actual)
```

### 6.5 — Payload webhook Meta

Structure type (message texte entrant) :

```json
{
  "object": "whatsapp_business_account",
  "entry": [{
    "id": "WABA_ID",
    "changes": [{
      "value": {
        "messaging_product": "whatsapp",
        "metadata": {
          "display_phone_number": "33912345678",
          "phone_number_id": "PHONE_NUMBER_ID"
        },
        "contacts": [{
          "profile": {"name": "Jean Dupont"},
          "wa_id": "33612345678"
        }],
        "messages": [{
          "from": "33612345678",
          "id": "wamid.HBgL...",
          "timestamp": "1713369600",
          "type": "text",
          "text": {"body": "Bonjour, je cherche un plombier"}
        }]
      },
      "field": "messages"
    }]
  }]
}
```

### 6.6 — Retry Meta

Meta retry en cas de non-réponse 200 sous 10s :
- Retry 1 : 5s après
- Retry 2 : 30s après
- Retry 3 : 2 min après
- Retry 4 : 15 min après
- Retry 5 : 1h après

Puis abandon (message perdu côté Meta). → **STRUCTORAI doit TOUJOURS répondre <10s**, même en cas d'erreur interne (enqueue async + return 200).

---

## 7. AUTHENTIFICATION DE L'ARTISAN (OTP LORS DE L'ONBOARDING)

### 7.1 — Pourquoi OTP

Quand un message arrive depuis le numéro WhatsApp personnel de l'artisan, STRUCTORAI doit savoir **avec certitude** qu'il s'agit bien de l'artisan (pas d'un tiers).

### 7.2 — Flow onboarding

1. Lors de l'inscription sur l'app (`POST /v1/auth/signup`), l'artisan renseigne son numéro WhatsApp (ex: +33 6 12 34 56 78)
2. STRUCTORAI génère un code OTP à 6 chiffres (ex: `482913`)
3. STRUCTORAI envoie un **message template "auth_otp"** (approuvé par Meta) au numéro de l'artisan
4. Artisan reçoit le message sur WhatsApp : *"Ton code STRUCTORAI : 482913"*
5. Artisan entre le code dans l'app
6. Vérification → lien établi

### 7.3 — Template "auth_otp" à créer

**Catégorie Meta** : `AUTHENTICATION`

**Texte** :
```
{{1}} est ton code STRUCTORAI. Il expire dans 10 minutes. 
Ne le partage avec personne.
```

**Paramètre** : `{{1}}` = code à 6 chiffres

**Coût** : **0.0160 €** par conversation (tarif Auth, moins cher que Utility).

### 7.4 — Validation OTP

- TTL : 10 minutes
- Max 3 tentatives avant blocage 15 min
- Anti-bruteforce : rate limit par IP (5 OTP max / jour)

### 7.5 — Table `users`

Ajouter colonnes :
- `whatsapp_phone` (string, indexed, UNIQUE par organization) — numéro WhatsApp E.164
- `whatsapp_verified_at` (timestamp) — null tant que non vérifié
- `whatsapp_opt_in_marketing` (bool, default false) — pour templates Marketing

---

## 8. RÉCEPTION MESSAGES ARTISAN (INBOUND ARTISAN)

### 8.1 — Identification sender

Quand un message arrive :
1. Extraire `from` (numéro E.164 sans `+`)
2. Lookup `users.whatsapp_phone = from AND whatsapp_verified_at IS NOT NULL`
3. Si trouvé → c'est un artisan authentifié
4. Si pas trouvé → c'est un client ou un prospect (voir §9)

### 8.2 — Types de messages artisan

| Type | Traitement |
|---|---|
| `text` | Envoi au Supervisor comme un chat texte normal |
| `audio` (vocal) | Download audio → Whisper STT (voir `docs/VOICE.md`) → Supervisor |
| `image` | Download → Vision IA classifie (ticket/photo chantier/plan/devis concurrent) |
| `document` (PDF, DOCX) | Download → traiter selon contexte (devis concurrent, facture, etc.) |
| `video` | Support limité V1 — stockage Supabase, pas d'analyse auto |
| `location` | Si près d'un chantier en cours → auto-imputation (déplacements) |
| `contacts` | Prospection — ajout carnet |
| `sticker` | Ignoré |

### 8.3 — Pipeline message texte artisan

```
Meta webhook → STRUCTORAI enqueue → parse → identify artisan →
  Supervisor.handle_chat(text, context={"source": "whatsapp"}) →
  Agent approprié → réponse →
  whatsapp_service.send_message(to=artisan_phone, text=response)
```

### 8.4 — Pipeline vocal artisan

```
Meta webhook → enqueue → parse type=audio →
  Download audio (Media API Meta) →
  Whisper STT (voir VOICE.md) → transcript →
  Supervisor.handle_chat(transcript) →
  Réponse →
  TTS ElevenLabs → audio mp3 →
  Upload media vers Meta → envoi message audio artisan
```

### 8.5 — Pipeline photo artisan

```
Meta webhook → enqueue → parse type=image →
  Download image →
  Vision IA classify(image) →
    Si ticket → Compta OCR → confirmation
    Si photo chantier → Galerie + analyse
    Si plan → Devis (quantités auto)
    Si devis concurrent → F121 parse → analyse comparative
  Réponse adaptée
```

---

## 9. RÉCEPTION MESSAGES CLIENTS (INBOUND CLIENT VERS ARTISAN)

### 9.1 — Identification d'un prospect/client

Si le `from` **n'est pas** dans `users.whatsapp_phone` → c'est un prospect ou client.

**Étape 1** — lookup `clients.phone = from` (dans toutes les organizations)
- Si trouvé dans 1 org → client connu, router vers cette org
- Si trouvé dans plusieurs orgs → ambigu, demander à l'expéditeur
- Si pas trouvé → prospect potentiel

### 9.2 — Comportement "prospect inconnu"

Le cerveau répond :
1. **Transparence AI Act** : *"Bonjour, je suis l'assistant IA de [artisan]. [Prénom] est actuellement indisponible."*
2. Demande contextualisation : *"Quel est votre besoin ?"*
3. Si besoin identifié → collecte structurée (nom, lieu, urgence, description)
4. Création fiche prospect auto (F87, voir §16)
5. Notification artisan

### 9.3 — Comportement "client connu"

Le cerveau :
1. Identifie le client
2. Salue par nom
3. Répond ou collecte la demande
4. Si message urgent → notification immédiate artisan
5. Si message info simple → peut répondre + notifier artisan

### 9.4 — Exemples concrets

**Prospect qui veut un devis** :

```
CLIENT (à 14h32) : "Bonjour, c'est Mme Leroy, j'ai une fuite 
sous mon évier qui coule depuis ce matin, vous pouvez venir ?"

CERVEAU :
"Bonjour Mme Leroy,

Je suis l'assistant IA de Jean (plombier). Il est sur un chantier 
actuellement.

Pour une fuite urgente, c'est sérieux. Quelques questions pour 
qu'il vienne préparé :

1. L'eau coule en continu ou juste quand vous ouvrez le robinet ?
2. Vous pouvez m'envoyer une photo ?
3. Quelle est votre adresse ?

Je transmets à Jean immédiatement, il revient vers vous dans 
l'heure."

[Fabrice reçoit notif push : "🚨 Urgence fuite Mme Leroy, 
photo reçue, tu peux la rappeler ?"]
```

### 9.5 — Session 24h WhatsApp

Chaque conversation client ouvre une **session 24h** (voir §10). Pendant cette session, envoi libre de messages (coût user-initiated ~0.007€). Après 24h sans nouvel échange → session fermée → besoin d'un **template** pour re-contacter.

---

## 10. ENVOI MESSAGES SESSION (24H WINDOW)

### 10.1 — Règle Meta des 24h

Après que l'utilisateur a envoyé un message, **STRUCTORAI peut répondre librement pendant 24h** (n'importe quel type de message, pas de template requis).

Après 24h de silence → impossible d'envoyer du contenu libre → **template requis** (voir §11).

### 10.2 — Endpoint envoi

```python
POST https://graph.facebook.com/v20.0/{PHONE_NUMBER_ID}/messages

Headers:
  Authorization: Bearer {WHATSAPP_ACCESS_TOKEN}
  Content-Type: application/json

Body:
{
  "messaging_product": "whatsapp",
  "recipient_type": "individual",
  "to": "33612345678",
  "type": "text",
  "text": {
    "preview_url": true,
    "body": "Votre message ici..."
  }
}
```

### 10.3 — Types de messages envoyables

- `text` (max 4096 caractères)
- `image` (jpg, png, max 5 MB)
- `audio` (aac, mp3, max 16 MB)
- `video` (mp4, max 16 MB)
- `document` (pdf, docx, xlsx, max 100 MB)
- `sticker` (webp, max 100 KB)
- `location`
- `contacts`
- `interactive` (boutons, liste)

### 10.4 — Interactive messages (boutons)

Utiles pour confirmer des actions :

```json
{
  "type": "interactive",
  "interactive": {
    "type": "button",
    "body": {
      "text": "Ton devis est prêt. Tu veux l'envoyer au client ?"
    },
    "action": {
      "buttons": [
        {"type": "reply", "reply": {"id": "send_yes", "title": "Oui envoyer"}},
        {"type": "reply", "reply": {"id": "modify", "title": "Modifier"}},
        {"type": "reply", "reply": {"id": "cancel", "title": "Annuler"}}
      ]
    }
  }
}
```

Quand l'artisan clique → Meta envoie un webhook avec `button_reply.id` → action déclenchée.

### 10.5 — Envoi media — flow en 2 étapes

Pour envoyer une image/audio/video :

1. **Upload** vers Meta Media API → obtient `media_id`
2. **Envoi message** avec `media_id`

```python
# Étape 1 : upload
POST /v20.0/{PHONE_NUMBER_ID}/media
Content-Type: multipart/form-data
  file: (binary)
  type: "image/jpeg"
  messaging_product: "whatsapp"

# Réponse : {"id": "MEDIA_ID"}

# Étape 2 : envoi
POST /v20.0/{PHONE_NUMBER_ID}/messages
{
  "to": "33612345678",
  "type": "image",
  "image": {"id": "MEDIA_ID", "caption": "Ton devis en PDF"}
}
```

### 10.6 — Envoi PDF devis

```json
{
  "to": "33612345678",
  "type": "document",
  "document": {
    "id": "MEDIA_ID",
    "filename": "Devis-DEV-2026-042.pdf",
    "caption": "Voici le devis pour la rénovation SDB. À valider."
  }
}
```

---

## 11. ENVOI MESSAGES TEMPLATES (HORS SESSION 24H)

### 11.1 — Règle

Si > 24h sans échange avec le destinataire → **obligation d'utiliser un template pré-approuvé** par Meta.

**Sinon** → erreur `recipient_hasnt_opted_in` ou `outside_24h_window`.

### 11.2 — Catégories de templates

| Catégorie | Usage | Tarif/conversation FR |
|---|---|---|
| `AUTHENTICATION` | OTP, 2FA | **0.0160 €** |
| `UTILITY` | Relances, confirmations, alertes transactionnelles | **0.0275 €** |
| `MARKETING` | Promo, actualités, news | **0.0466 €** |

STRUCTORAI utilise principalement **UTILITY** (relances, alertes) et **AUTHENTICATION** (OTP).

### 11.3 — Structure d'un template

```
Header (optionnel) : image, vidéo, doc, texte
Body (obligatoire) : texte avec variables {{1}}, {{2}}
Footer (optionnel) : texte court
Buttons (optionnel) : Quick replies ou CTA
```

### 11.4 — Envoi d'un template

```json
POST /v20.0/{PHONE_NUMBER_ID}/messages
{
  "to": "33612345678",
  "type": "template",
  "template": {
    "name": "relance_facture_j15",
    "language": {"code": "fr"},
    "components": [
      {
        "type": "body",
        "parameters": [
          {"type": "text", "text": "M. Martin"},
          {"type": "text", "text": "FACT-2026-087"},
          {"type": "text", "text": "2 450€"}
        ]
      }
    ]
  }
}
```

### 11.5 — Process de création template

1. Dans Meta Business Manager → WhatsApp Manager → Templates
2. Créer template → Submit for review
3. Attente 24-48h approbation Meta
4. Si approuvé → utilisable immédiatement
5. Si rejeté → voir raison, corriger, resubmit

### 11.6 — Règles d'approbation Meta

**Ce qui est REJETÉ** :
- Marketing déguisé en Utility
- Placeholder sans contexte (`{{1}}` ambigu)
- Promesse commerciale forte ("Gagnez X€")
- Urgence artificielle ("Dernière chance !")
- URL de tracking pur

**Ce qui est ACCEPTÉ** :
- Informations transactionnelles claires
- Identification claire de l'expéditeur
- Variables avec contexte clair
- Phrasing neutre et factuel

---

## 12. TEMPLATES PRÉ-APPROUVÉS À CRÉER

### 12.1 — Liste exhaustive des templates V1

**14 templates** à créer et faire approuver avant le launch juin 2026.

#### T1 — `auth_otp` (AUTHENTICATION)

```
{{1}} est ton code STRUCTORAI. Il expire dans 10 minutes. 
Ne le partage avec personne.
```

#### T2 — `relance_devis_j3` (UTILITY)

```
Bonjour {{1}}, j'espère que vous allez bien. J'ai bien vu que 
vous aviez reçu le devis {{2}} il y a 3 jours. Avez-vous pu 
le regarder ? Je reste à votre disposition.

Cordialement,
{{3}}
```

Variables : `{{1}}` nom client, `{{2}}` numéro devis, `{{3}}` nom artisan.

#### T3 — `relance_facture_j15` (UTILITY)

```
Bonjour {{1}},

Pour information, la facture {{2}} d'un montant de {{3}} arrive 
à échéance. Merci de procéder au règlement.

Cordialement,
{{4}}
```

#### T4 — `relance_facture_j30` (UTILITY)

```
Bonjour {{1}},

Votre facture {{2}} ({{3}}) est impayée depuis 30 jours. 
Merci de régulariser rapidement.

Cordialement,
{{4}}
```

#### T5 — `relance_facture_j45` (UTILITY)

```
Bonjour {{1}},

Votre facture {{2}} ({{3}}) reste impayée malgré nos relances. 
Sans règlement sous 15 jours, nous serons contraints d'initier 
la procédure de recouvrement.

Cordialement,
{{4}}
```

#### T6 — `confirmation_devis_signe` (UTILITY)

```
Bonjour {{1}},

Nous avons bien reçu votre devis signé {{2}}. Les travaux 
sont planifiés à partir du {{3}}.

Cordialement,
{{4}}
```

#### T7 — `demande_avis_google` (UTILITY)

```
Bonjour {{1}},

Merci d'avoir fait appel à nos services pour {{2}}. Si notre 
travail vous a satisfait, un avis Google serait très apprécié : 
{{3}}

Cordialement,
{{4}}
```

#### T8 — `rendez_vous_confirmation` (UTILITY)

```
Bonjour {{1}},

Votre rendez-vous avec {{2}} est confirmé pour le {{3}} à {{4}}.

Adresse : {{5}}

À bientôt.
```

#### T9 — `rendez_vous_rappel_j1` (UTILITY)

```
Bonjour {{1}},

Petit rappel : rendez-vous demain {{2}} à {{3}} avec {{4}} 
pour {{5}}.

À demain.
```

#### T10 — `chantier_commence` (UTILITY)

```
Bonjour {{1}},

{{2}} démarre les travaux aujourd'hui comme prévu. Le chantier 
est estimé à {{3}} jours.

N'hésitez pas pour toute question.
```

#### T11 — `chantier_termine` (UTILITY)

```
Bonjour {{1}},

{{2}} vient de terminer les travaux. La facture finale 
{{3}} ({{4}}) vous sera envoyée dans la journée.

Merci de votre confiance.
```

#### T12 — `acompte_recu` (UTILITY)

```
Bonjour {{1}},

Nous avons bien reçu votre acompte de {{2}} pour le devis {{3}}. 
Les travaux peuvent démarrer comme convenu le {{4}}.

Cordialement,
{{5}}
```

#### T13 — `alerte_echeance_fiscale` (UTILITY — pour artisan)

```
📅 Rappel STRUCTORAI

Échéance {{1}} le {{2}}. Montant estimé : {{3}}.

Plus de détails dans l'app.
```

#### T14 — `briefing_matin` (UTILITY — pour artisan, opt-in)

```
☀️ Bonjour {{1}}

Ta journée :
{{2}}

Bonne journée !
```

### 12.2 — Gestion multi-langue

Chaque template est créé en **6 versions linguistiques** (FR/EN/TR/PT/AR/ES). Meta gère le routage selon `language.code`.

**Total templates à approuver** : 14 × 6 langues = **84 templates**.

**Stratégie pragmatique launch** :
- V1 : créer les 14 en FR uniquement
- Post-launch : ajouter EN, puis TR/PT/AR/ES selon demandes

### 12.3 — Versioning des templates

Quand on modifie un template approuvé :
- Le template passe en status "Edited"
- Nécessite re-approval Meta
- L'ancien template continue de fonctionner pendant la revue

---

## 13. TYPES DE CONTENUS SUPPORTÉS

### 13.1 — Tableau synthétique inbound

| Type | Format | Limite | Traitement STRUCTORAI |
|---|---|---|---|
| `text` | UTF-8 | 4096 chars | Chat classique |
| `audio` (PTT vocal) | ogg, aac, mp4 | 16 MB | Whisper STT |
| `image` | jpeg, png | 5 MB | Vision IA classify |
| `document` | pdf, docx, xlsx, pptx, txt | 100 MB | Parse selon type |
| `video` | mp4, 3gp | 16 MB | Stockage, analyse limitée V1 |
| `location` | coords | — | Si proche chantier → auto-impute |
| `contacts` | vcard | — | Ajout prospection |
| `sticker` | webp | 100 KB | Ignoré |
| `reaction` | emoji | — | Ignoré |

### 13.2 — Tableau synthétique outbound

| Type | Format | Limite | Usage STRUCTORAI |
|---|---|---|---|
| `text` | UTF-8 | 4096 chars | Réponses, FAQ |
| `audio` | aac, mp3 | 16 MB | Réponse vocale (TTS ElevenLabs) |
| `image` | jpeg, png | 5 MB | Photos chantier, avant/après |
| `document` | pdf | 100 MB | Devis, factures, rapports Coach |
| `video` | mp4 | 16 MB | Capsules formation (F120) |
| `interactive` | boutons/liste | 3 boutons max | Validations rapides |
| `template` | — | — | Hors session 24h |

### 13.3 — Stockage media Meta

Les medias reçus sont stockés **temporairement 30 jours** par Meta :
- Après 30 jours, URL expire → besoin de télécharger et stocker en Supabase dans les 30j

**Stratégie STRUCTORAI** : téléchargement immédiat à la réception + stockage Supabase Storage avec ID Meta en référence.

---

## 14. COÛTS PAR CONVERSATION

### 14.1 — Tarifs Meta Cloud API France (avril 2026)

Source : https://developers.facebook.com/docs/whatsapp/pricing

| Type conversation | Initiateur | Prix France |
|---|---|---|
| **User Service** | Utilisateur (client) | **0.0068 €** |
| **Authentication** | Business | **0.0160 €** |
| **Utility** | Business | **0.0275 €** |
| **Marketing** | Business | **0.0466 €** |

**Note** : une "conversation" dure **24h** après le 1er message business-initiated, peu importe le nombre de messages échangés pendant cette fenêtre.

### 14.2 — Free tier Meta

- **1000 conversations/mois gratuites** en User Service (client-initiated)
- Les conversations Authentication, Utility, Marketing ne sont PAS gratuites (payantes dès la 1ère)

### 14.3 — Projection par artisan/mois

Scénario type artisan Pro (~30 interactions WhatsApp/mois) :

| Type | Volume mensuel | Coût |
|---|---|---|
| User Service (client écrit) | 20 | 0€ (dans free tier) |
| Utility (relances, confirmations) | 8 | 0.22€ |
| Authentication (OTP signup, rare) | 0 (1 fois au signup) | 0€ |
| Marketing | 0 (non utilisé V1) | 0€ |
| **Total / artisan** | 28 | **~0.22€/mois** |

### 14.4 — Projection à 500 clients

- 500 × 0.22€ = **~110€/mois**
- Négligeable vs revenus (~20 000€ MRR)

### 14.5 — Optimisations coûts

1. **Rester dans la session 24h** quand possible — utiliser la fenêtre active après message client
2. **Grouper les relances** — une seule conv par session plutôt que plusieurs
3. **Templates Utility** plutôt que Marketing — 41% moins cher
4. **Pas de Marketing push** en V1 (coût élevé pour ROI incertain)

---

## 15. RATE LIMITS META

### 15.1 — Limites par numéro

Meta impose des **tiers** selon le volume d'envois business-initiated :

| Tier | Destinataires uniques/24h |
|---|---|
| Tier 1 | **1 000** |
| Tier 2 | **10 000** |
| Tier 3 | **100 000** |
| Tier 4 | **illimité** |

**Nouveau numéro** → démarre en Tier 1 → upgrade automatique selon qualité et volume.

### 15.2 — Messaging Quality Rating

Meta calcule une qualité (Green / Yellow / Red) :
- **Green** : >95% messages délivrés et lus, peu de blocages
- **Yellow** : 90-95%, quelques blocages
- **Red** : <90%, beaucoup de blocages → risque de ban

**Si Red** → upgrade de tier bloqué, risque de suspension numéro.

**STRUCTORAI strategy** :
- Pas de spam
- Templates bien calibrés
- Opt-in clair pour marketing
- Gestion opt-out (voir §20.4)

### 15.3 — Rate limits API

- **200 requests/sec** par numéro (large)
- Au-delà → HTTP 429 → retry exponentiel côté STRUCTORAI

### 15.4 — Broadcast limits

Envoyer à >1000 destinataires d'un coup = flag possible → faire par **batchs de 250** avec délai 10s entre chaque batch.

---

## 16. FLOW INBOUND CLIENT → FICHE PROSPECT AUTOMATIQUE (F87)

### 16.1 — Feature F87

Auto-création fiche prospect depuis WhatsApp (voir `FEATURES.md` F87).

### 16.2 — Pipeline complet

```
1. Client inconnu envoie message WhatsApp
   ↓
2. Webhook → STRUCTORAI identifie "sender inconnu"
   ↓
3. Lookup organizations → quel artisan destinataire ?
   - Via numéro partagé → mapping demande
   - Via numéro dédié → artisan identifié
   ↓
4. Supervisor → Agent Téléphone V2 (en V1 : Supervisor direct)
   ↓
5. Script transparence AI Act :
   "Bonjour, je suis l'assistant IA de [artisan]..."
   ↓
6. Collecte structurée (5 champs obligatoires) :
   - Nom / Prénom
   - Type de besoin
   - Adresse (ville min)
   - Urgence (oui/non)
   - Description
   ↓
7. Stockage fiche prospect :
   INSERT INTO clients (
     organization_id, nom, prenom, telephone, source='whatsapp',
     premier_contact_message, urgence, description, status='prospect'
   )
   ↓
8. Notification artisan push + SMS si urgence :
   "Nouveau prospect WhatsApp — Mme Leroy, fuite urgente, 13008"
   ↓
9. Fiche apparaît dans Dossier "À faire"
   ↓
10. Artisan rappelle quand dispo → convertit en devis
```

### 16.3 — Qualification du prospect

Le cerveau score le prospect :
- **Chaud** : urgence + adresse proche + budget implicite → notif immédiate
- **Tiède** : besoin clair mais pas urgent → dans liste à rappeler
- **Froid** : question générale → réponse IA, pas d'escalade

### 16.4 — Anti-spam

Filtres appliqués :
- Mots-clés promo/spam connus → rejet silencieux
- Numéros blacklistés (Meta + custom STRUCTORAI) → rejet
- >3 messages consécutifs sans réponse user → ignore

---

## 17. FLOW PHOTO WHATSAPP → CHANTIER (F60)

### 17.1 — Feature F60

L'artisan envoie une photo par WhatsApp → le cerveau la classe et l'associe au chantier en cours.

### 17.2 — Pipeline

```
1. Artisan envoie photo (possiblement + vocal descriptif)
   ↓
2. Webhook → STRUCTORAI identifie artisan
   ↓
3. Si vocal joint → Whisper STT → description
   Sinon → cerveau demande : "C'est pour quel chantier ?"
   ↓
4. Agent Vision IA analyse :
   - Classification : ticket / plan / photo chantier / devis / autre
   - Si photo chantier :
     - Detect type (avant / pendant / après)
     - Détection éléments (baignoire, carrelage, peinture, etc.)
     - Anomalies
   ↓
5. Auto-imputation chantier :
   - Si un chantier est marqué "en cours" → impute direct
   - Si plusieurs chantiers actifs → demande confirmation
   - Si vocal mentionne un nom client → cherche correspondance
   ↓
6. Stockage Supabase Storage + row photos_chantier
   ↓
7. Réponse artisan :
   "J'ai classé cette photo (avant travaux) dans le chantier 
   Martin. Tu veux que je génère un avant/après quand tu as 
   la photo après ?"
```

### 17.3 — Cas particuliers

- **Photo sans contexte** → demande confirmation
- **Ticket de caisse détecté** → redirige vers flow Compta OCR
- **Devis concurrent détecté** → redirige vers F121 (Vision IA analyse)

---

## 18. MAPPING WHATSAPP ↔ ORGANISATION ↔ USER

### 18.1 — Schéma relationnel

```
organizations
  ├── id (UUID)
  ├── whatsapp_integration_mode ENUM ('shared', 'dedicated')
  ├── whatsapp_phone_number_id (nullable, dédié uniquement)
  ├── whatsapp_waba_id (nullable)
  └── whatsapp_handle (nullable, pour numéro partagé : "@martin-plombier")

users
  ├── id (UUID)
  ├── organization_id (FK)
  ├── whatsapp_phone (E.164, unique par org)
  ├── whatsapp_verified_at (timestamp)
  └── whatsapp_opt_in_marketing (bool)

clients
  ├── id (UUID)
  ├── organization_id (FK)
  ├── phone (E.164)
  ├── whatsapp_opt_in (bool)
  └── first_whatsapp_contact_at
```

### 18.2 — Migration SQL

Migration dédiée (exemple `023_add_whatsapp_fields.sql`) :

```sql
ALTER TABLE organizations
  ADD COLUMN whatsapp_integration_mode TEXT 
    DEFAULT 'shared' CHECK (whatsapp_integration_mode IN ('shared', 'dedicated')),
  ADD COLUMN whatsapp_phone_number_id TEXT,
  ADD COLUMN whatsapp_waba_id TEXT,
  ADD COLUMN whatsapp_handle TEXT UNIQUE;

ALTER TABLE users
  ADD COLUMN whatsapp_phone TEXT,
  ADD COLUMN whatsapp_verified_at TIMESTAMP WITH TIME ZONE,
  ADD COLUMN whatsapp_opt_in_marketing BOOLEAN DEFAULT FALSE;

CREATE UNIQUE INDEX idx_users_whatsapp_phone_org 
  ON users(organization_id, whatsapp_phone) 
  WHERE whatsapp_phone IS NOT NULL;

ALTER TABLE clients
  ADD COLUMN whatsapp_opt_in BOOLEAN DEFAULT FALSE,
  ADD COLUMN first_whatsapp_contact_at TIMESTAMP WITH TIME ZONE;
```

### 18.3 — Requêtes clés

**Trouver l'artisan depuis un numéro entrant** :
```sql
SELECT u.*, o.*
FROM users u
JOIN organizations o ON u.organization_id = o.id
WHERE u.whatsapp_phone = $1 AND u.whatsapp_verified_at IS NOT NULL;
```

**Trouver le destinataire d'un client inconnu (numéro partagé)** :
```sql
-- Extraction du handle @artisan-xxx dans le 1er message
SELECT o.*
FROM organizations o
WHERE o.whatsapp_handle = $1;
```

---

## 19. BACKEND — `whatsapp_service.py`

### 19.1 — Structure

**Fichier** : `backend/app/services/whatsapp_service.py`

```python
class WhatsAppService:
    """Encapsule Meta Cloud API pour STRUCTORAI."""
    
    def __init__(self, settings: Settings):
        self.phone_number_id = settings.WHATSAPP_PHONE_NUMBER_ID
        self.access_token = settings.WHATSAPP_ACCESS_TOKEN
        self.app_secret = settings.WHATSAPP_APP_SECRET
    
    # Incoming
    def verify_signature(self, body: bytes, signature: str) -> bool: ...
    async def parse_webhook(self, payload: dict) -> WhatsAppIncomingMessage: ...
    async def identify_sender(self, phone: str) -> SenderContext: ...
    
    # Outgoing text/media
    async def send_text(self, to: str, text: str, preview_url: bool = True) -> str: ...
    async def send_image(self, to: str, image_url_or_id: str, caption: str = "") -> str: ...
    async def send_audio(self, to: str, audio_url_or_id: str) -> str: ...
    async def send_document(self, to: str, doc_url_or_id: str, filename: str, caption: str = "") -> str: ...
    async def send_interactive_buttons(self, to: str, body_text: str, buttons: list[dict]) -> str: ...
    
    # Templates
    async def send_template(self, to: str, template_name: str, language: str, variables: list[str]) -> str: ...
    
    # Media
    async def upload_media(self, file: bytes, mime_type: str) -> str: ...      # retourne media_id
    async def download_media(self, media_id: str) -> bytes: ...
    
    # OTP
    async def send_otp(self, to: str, code: str, language: str = "fr") -> str:
        return await self.send_template(
            to=to,
            template_name="auth_otp",
            language=language,
            variables=[code]
        )
    
    # Session
    async def is_session_open(self, phone: str) -> bool:
        """Vérifie si < 24h depuis dernier message client."""
        ...
```

### 19.2 — Gestion async

Tous les appels Meta API sont **async** (`httpx` avec pool de connexions).

Retry exponentiel sur 5xx Meta :
- Retry 1 : 1s
- Retry 2 : 5s
- Retry 3 : 30s
- Puis dead letter queue + alerte Sentry

### 19.3 — Logging

Chaque message envoyé/reçu loggé dans table `whatsapp_messages` :
- `id` (wamid Meta)
- `organization_id`
- `direction` (inbound/outbound)
- `type` (text/audio/image/doc/template)
- `content_summary`
- `cost_eur` (estimé)
- `sent_at`

Utile pour billing, analytics, audit.

---

## 20. CONFORMITÉ RGPD + AI ACT

### 20.1 — Consentement opt-in clients

Pour envoyer des messages à un client, **consentement explicite** requis (Meta policy + RGPD).

Consentement recueilli :
- Case à cocher lors de la création fiche client dans l'app
- Confirmation verbale vocal sur WhatsApp : *"Puis-je vous envoyer mon devis par WhatsApp ?"*
- Opt-in automatique quand client écrit en premier

### 20.2 — Opt-out

Obligatoire : un mot STOP en français ou anglais → déclenche opt-out.

**Mots-clés détectés** :
- "STOP", "ARRÊT", "UNSUBSCRIBE"
- "Ne plus me contacter", "Ne m'envoyez plus"

Traitement :
- `UPDATE clients SET whatsapp_opt_in = FALSE`
- Confirmation : *"Vous ne recevrez plus de messages de [artisan] sur WhatsApp. Vos données sont conservées selon notre politique RGPD."*

### 20.3 — Transparence AI Act

Voir `docs/AI-ACT-COMPLIANCE.md` §6.3.

Script obligatoire au **premier contact** avec un client sur WhatsApp :

> *"Bonjour, je suis l'assistant IA de [artisan]. [Prénom] est actuellement indisponible. Je peux prendre votre demande ou votre message."*

Jamais de prétention d'humanité. Le client doit toujours savoir qu'il parle à une IA.

### 20.4 — RGPD — droits clients

- **Droit accès** : client peut demander ses données WhatsApp → export JSON
- **Droit effacement** : DELETE fiches + messages après 30 jours
- **Droit opposition** : opt-out immédiat
- **DPA Meta** : signé avec Meta Business (sous-traitant)

### 20.5 — Conservation

- Messages WhatsApp : **12 mois** maximum (configurable)
- Audio/vidéo : 30 jours puis purge
- Logs webhook : 90 jours

### 20.6 — Audit log AI Act

Chaque interaction IA via WhatsApp loggée dans `ai_decisions` (voir `docs/AI-ACT-COMPLIANCE.md` §9).

---

## 21. GESTION DES ERREURS META

### 21.1 — Codes d'erreur principaux

| Code Meta | Signification | Action STRUCTORAI |
|---|---|---|
| `131000` | Paramètre invalide | Bug côté nous, fix code |
| `131005` | Access denied | Token expiré → renouveler |
| `131031` | Invalid phone number | Validation phone côté app |
| `131047` | Re-engagement message required | Utiliser template (>24h) |
| `131051` | Unsupported message type | Dégrader vers type supporté |
| `133000` | Incomplete deletion | Retry purge |
| `133004` | Template pause | Template en cours de review |
| `133005` | Template rejected | Créer nouveau template |
| `368` | Temporary blocked (quality) | Pause 24h, audit qualité |

### 21.2 — Errors custom STRUCTORAI

Mappés sur `StructorAIError` (voir `docs/API.md` §5) :
- `WHATSAPP_SIGNATURE_INVALID` (403)
- `WHATSAPP_SENDER_UNKNOWN` (404)
- `WHATSAPP_OUTSIDE_SESSION_WINDOW` (422 — besoin de template)
- `WHATSAPP_TEMPLATE_NOT_APPROVED` (422)
- `WHATSAPP_MEDIA_TOO_LARGE` (413)
- `WHATSAPP_RATE_LIMITED` (429)
- `WHATSAPP_API_DOWN` (502)

### 21.3 — Circuit breaker Meta

Si >5% erreurs 5xx sur 5 min → circuit breaker ouvert → pause 2 min → retry.

Alerte Sentry + notification Fabrice si circuit breaker actif >10 min.

### 21.4 — Fallback SMS

Si Meta API indisponible >10 min → bascule des messages critiques (OTP, relances urgentes) vers **Twilio SMS**.

Fallback documenté dans `docs/MEMORY-STRATEGY.md` (même pattern que pour les services critiques).

---

## 22. PLAN DE MIGRATION VERS AGENT TÉLÉPHONE V2 (F126)

### 22.1 — V1 (juin 2026) — WhatsApp uniquement

- Supervisor direct sur messages inbound clients
- Script transparence AI Act
- Collecte prospect F87
- Templates 14 approuvés

### 22.2 — V2 (sept-oct 2026) — Ajout Agent Téléphone

- Agent Téléphone IA dédié (add-on +15€/mois)
- Intégration appels voix (Twilio Voice API ou Vonage)
- **F126 télé-dépannage** : 550 arbres de décision (50 pannes × 11 métiers)
- WhatsApp devient un des canaux de l'Agent Téléphone (plus Supervisor direct)

### 22.3 — Impact architecture

Voir `docs/AGENTS.md` §16. Refactor mineur : le handler webhook WhatsApp route désormais vers `agent_telephone.py` au lieu du Supervisor directement (uniquement pour inbound clients).

---

## 23. TESTS ET VALIDATION

### 23.1 — Tests unitaires

**Fichier** : `backend/tests/test_whatsapp_service.py`

- Test verify_signature (valid, invalid, missing)
- Test parse_webhook (text, audio, image, doc, button_reply)
- Test identify_sender (artisan known, client known, unknown)
- Test send_text (mock HTTP)
- Test send_template avec variables
- Test rate limit handling
- Test retry exponentiel

### 23.2 — Tests d'intégration

**Avec sandbox Meta** :
- End-to-end : numéro test → webhook → Supervisor → réponse envoyée
- Vérifier délai <3s entre message reçu et réponse envoyée
- Tester tous les types de messages

### 23.3 — Tests en production (canaries)

Au launch : 5 artisans beta-testeurs avec numéro WhatsApp réel, 1 semaine de tests avant public launch.

### 23.4 — Monitoring

Dashboard dédié :
- Taux de délivrance (>95%)
- Messaging Quality Rating Meta (Green)
- Latence webhook → réponse (P95 <3s)
- Templates rejetés (alerte)
- Coût quotidien (évite explosion)

---

## 24. TROUBLESHOOTING

### 24.1 — Problèmes fréquents

| Symptôme | Cause probable | Solution |
|---|---|---|
| "Le webhook ne reçoit rien" | URL pas whitelistée Meta / Verify token mismatch | Vérifier Meta Business > Webhook configuration |
| "Signature invalide sur webhook" | App secret incorrect | Régénérer + update env |
| "Message non envoyé au client" | Session 24h expirée | Utiliser template approprié |
| "Template rejeté par Meta" | Trop promotionnel | Reformuler en UTILITY |
| "Numéro bloqué" | Quality Rating Red | Pause envois, audit contenu |
| "Artisan n'est pas reconnu" | OTP pas complété | Re-déclencher flow OTP |
| "Photo WhatsApp pas classée" | Vision IA confidence low | Demander confirmation |

### 24.2 — Logs à consulter

- Sentry → erreurs WhatsApp
- Railway logs → whatsapp_service.py
- `whatsapp_messages` table → historique complet
- Meta Business Manager → logs envois + quality rating

### 24.3 — Contacts support Meta

- Meta Business Support : https://business.facebook.com/business/help
- WhatsApp Business API docs : https://developers.facebook.com/docs/whatsapp/cloud-api
- Community forum : https://developers.facebook.com/community/

---

## 25. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/ARCH.md` §3.3 | WhatsApp comme canal 3 |
| `docs/API.md` §28.2 | Webhook `/v1/whatsapp/webhook` |
| `docs/AGENTS.md` §16 | Agent Téléphone V2 (F126) |
| `docs/VOICE.md` | Pipeline vocal (audio WhatsApp → Whisper) |
| `docs/AI-ACT-COMPLIANCE.md` §6.3 | Transparence IA |
| `docs/MEMORY-STRATEGY.md` | Fallback SMS si Meta down |
| `docs/ENV.md` (à créer) | Variables `WHATSAPP_*` |
| `docs/ERRORS.md` (à créer) | Codes `WHATSAPP_*` |
| `docs/MIGRATIONS.md` (à créer) | Migration `023_add_whatsapp_fields.sql` |
| `docs/I18N.md` (à créer) | Templates 6 langues |
| `docs/TESTS.md` (à créer) | Tests WhatsApp |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` | Coûts Meta Cloud API |
| `COUT_REEL.md` | Coûts WhatsApp ~0.22€/artisan |
| `BUILD_PLAN.md` | whatsapp_service.py + webhook |
| `PRODUCT_CONTEXT.md` | Canal WhatsApp vision produit |
| `FEATURES.md` F60, F87, F126 | Features WhatsApp |

### 25.1 — Références externes

- **Meta WhatsApp Business Platform** : https://developers.facebook.com/docs/whatsapp
- **Cloud API Reference** : https://developers.facebook.com/docs/whatsapp/cloud-api
- **Pricing** : https://developers.facebook.com/docs/whatsapp/pricing
- **Message Templates** : https://developers.facebook.com/docs/whatsapp/message-templates/guidelines
- **Webhooks** : https://developers.facebook.com/docs/whatsapp/cloud-api/webhooks
- **Business Verification** : https://www.facebook.com/business/help/2058515294227817
- **Quality Rating** : https://developers.facebook.com/docs/whatsapp/cloud-api/overview/quality-rating-and-status
- **Rate Limits** : https://developers.facebook.com/docs/whatsapp/cloud-api/overview/messaging-limits

---

> **Ce fichier est la spec complète de l'intégration WhatsApp.**
> **WhatsApp est un canal critique** (80% des interactions artisan depuis chantier).
> **Règle d'or** : respecter les 24h de session pour éviter les coûts templates inutiles.
> **Transparence AI Act non négociable** : tout client qui écrit DOIT savoir qu'il parle à une IA dès le 1er message.
> **Différenciation concurrence** : aucun concurrent BTP n'a de WhatsApp bidirectionnel complet. C'est notre moat.
