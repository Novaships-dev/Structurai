# STRUCTORAI — COÛTS RÉELS ET PIÈGES À ÉVITER

> Chaque centime compté. Zéro surprise.
> Date : 14/04/2026

---

## PHASE 1 — BUILD (0 clients, 0 revenus)

### Coûts FIXES mensuels pendant le build

| Service | Plan | Coût/mois | Note |
|---------|------|-----------|------|
| Railway (backend) | Hobby → Pro | **0€ mois 1** → 5€/mois ensuite | Free trial inclut $5 de crédits. Pro = $5/mois + usage. |
| Supabase | Free | **0€** | 500 MB DB, 1 GB stockage, 50K auth users. Largement suffisant pour le build. |
| Vercel (PWA) | Hobby | **0€** | 100 GB bandwidth. Gratuit pour les projets perso. |
| Google Play (Android) | One-time | **25$ une fois** | Compte développeur, paiement unique à vie. |
| Domaine structorai.app | Annuel | **~15€/an ≈ 1.25€/mois** | Via Cloudflare Registrar ou Google Domains. |
| **TOTAL BUILD** | | **~1.25€/mois** (+ 25$ one-time) | |

### Coûts VARIABLES pendant le build (dev + tests)

| Service | Usage pendant le build | Coût estimé/mois | Piège potentiel |
|---------|----------------------|-----------------|-----------------|
| Claude API (tests agents) | ~50 appels Sonnet/jour × 30 jours = 1500 appels, ~2K tokens input + ~1K output par appel | **~15-25$** | ⚠️ Si tu laisses un agent en boucle = facture explosive |
| Whisper/OpenAI STT (tests voix) | ~20 tests/jour × 30s = 10 min/jour | **~2$** | $0.006/min. Très cheap. |
| ElevenLabs TTS (tests voix) | Free tier = 10K chars/mois ≈ 10 min audio | **0$** | Le free tier suffit pour tester. |
| Brevo (email) | Free tier = 300 emails/jour | **0$** | Gratuit pour le dev. |
| Twilio (SMS) | ~50 SMS de test | **~2$** | $0.04/SMS France. |
| Mem0 | Free tier / self-hosted | **0$** | Self-host sur Railway si possible. |
| Sentry | Free tier (5K events/mois) | **0$** | |
| **TOTAL VARIABLE BUILD** | | **~20-30$/mois** | |

### TOTAL PHASE BUILD

```
Mois 1 : ~25$ (API tests) + 25$ (Google Play) + 15€ (domaine annuel) = ~65€
Mois 2 : ~30$ (API tests) + 5$ (Railway) = ~35€
```

**Budget build total : ~100€ pour 2 mois de dev.**

---

## PHASE 2 — LAUNCH (1-50 clients)

### Coûts FIXES mensuels

| Service | Plan | Coût/mois | Seuil pour upgrade |
|---------|------|-----------|-------------------|
| Railway | Pro | **5$** + usage (~5-15$ pour <50 users) | >100 users → scale |
| Supabase | Free → Pro | **0$** → **25$/mois si >500MB DB** | Upgrade quand DB > 500MB ou besoin backup quotidien |
| Vercel | Hobby | **0$** | >100 GB bandwidth/mois → Pro $20 |
| Brevo | Free → Starter | **0$** → **9€/mois si >300 emails/jour** | Upgrade si relances auto + emails transactionnels |
| ElevenLabs | Starter | **5$/mois** (30K chars = ~30 min TTS) | C'est serré pour 50 users. Voir optimisation ci-dessous. |
| Sentry | Free | **0$** | |
| Yousign API (signature devis) | Starter | **75€/mois** (500 signatures/an) | Couvre ~42 signatures/mois. |
| **TOTAL FIXE** | | **~90-125€/mois** selon upgrades | |

### Coûts VARIABLES par artisan payant (Pro à 29€/mois)

C'est ici que tu dois être chirurgical. Chaque artisan qui utilise l'app coûte de l'argent en API.

| Service | Usage/artisan/mois | Coût/artisan/mois | Calcul |
|---------|-------------------|-------------------|--------|
| **Claude API (Sonnet 4.6)** | ~60 appels × 3K input × 1.5K output tokens | **~$1.60** | (60×3K/1M×$3) + (60×1.5K/1M×$15) = $0.54 + $1.35 |
| **Claude API (Haiku 4.5)** — agents légers | ~100 appels × 1.5K input × 0.5K output | **~$0.40** | (100×1.5K/1M×$1) + (100×0.5K/1M×$5) = $0.15 + $0.25 |
| **Claude Vision (OCR tickets)** — Sonnet | ~15 scans × 1K tokens image + 500 output | **~$0.27** | Images = tokens. ~$0.018/scan. |
| **Whisper STT (vocal)** | ~30 vocaux × 20s = 10 min | **~$0.06** | $0.006/min |
| **ElevenLabs TTS** | ~30 réponses × 200 chars = 6K chars | **~$0.72** | $0.12/1K chars (Multilingual v2) |
| **Brevo (emails)** | ~10 emails (relances, devis, factures) | **~$0.02** | Quasi gratuit |
| **Twilio SMS** | ~5 SMS (relances, avis Google) | **~$0.20** | $0.04/SMS |
| **Supabase Storage** | ~50 MB (tickets, photos, PDFs) | **~$0.01** | $0.021/GB |
| **Claude Vision (Agent Vision IA)** | ~10-15 analyses photo chantier/plan × Sonnet | **~$0.35** | Au-delà de l'OCR tickets : photos chantier, plans, détection oublis |
| **Agent Site Web (génération)** | Génération initiale + MAJ mensuelle × Sonnet | **~$0.50** | + coût hébergement ~$2-5/mois par site (Vercel) |
| **Agent Email Pro (IMAP)** | Polling IMAP + ~5 résumés/jour × Haiku | **~$0.15** | Connexion IMAP + filtrage + résumé quotidien |
| **Agent Réputation & Marketing (social)** | ~4 posts réseaux sociaux/mois × Haiku | **~$0.10** | Publication Facebook/Instagram/Google Business |
| **TOTAL PAR ARTISAN** | | **~$4.50/mois** | |

### Marge par artisan

| Plan | Prix | Coût API/artisan | Coût infra partagé | **Marge brute** | **% marge** |
|------|------|-----------------|-------------------|----------------|-------------|
| **Pro 29€** | 29€ | ~4.50$ ≈ 4.10€ | ~1€ (quote-part infra) | **~23.90€** | **~82%** |
| **Business 79€** | 79€ | ~6.50$ ≈ 5.90€ (usage + élevé) | ~1.50€ | **~71.60€** | **~91%** |
| **Starter 0€** | 0€ | ~0.80€ (usage limité) | ~0.50€ | **-1.30€** | Perte |

**Le Starter coûte de l'argent.** C'est normal — c'est de l'acquisition. Mais il faut le plafonner.

---

## LES 12 PIÈGES QUI EXPLOSENT LA FACTURE

### PIÈGE 1 — Agent en boucle infinie (CRITIQUE)
**Risque :** Un agent qui s'appelle lui-même ou relance sans fin = des centaines d'appels API en quelques minutes.
**Coût potentiel :** 50-500$ en une heure.
**Protection :**
```python
# OBLIGATOIRE dans chaque agent
MAX_ITERATIONS_PER_TASK = 5
MAX_TOKENS_PER_SESSION = 50_000
BUDGET_MAX_PER_CALL = 0.15  # $0.15 max
BUDGET_MAX_PER_ARTISAN_PER_DAY = 2.00  # $2 max/jour
```

### PIÈGE 2 — Context window trop large
**Risque :** Envoyer tout l'historique de conversation à chaque appel → 50K+ tokens d'input.
**Coût :** Un appel Sonnet avec 50K input = $0.15 d'input seul. × 60 appels/jour = $9/jour/artisan.
**Protection :**
- Context compaction : résumer les anciens messages (pattern Ouroboros)
- Max 10K tokens d'historique par appel
- Prompt caching : le system prompt (identique à chaque appel) est caché → 90% de réduction

### PIÈGE 3 — Prompt caching non activé
**Risque :** Le system prompt (~3K tokens) est facturé full price à chaque appel.
**Coût gaspillé :** 3K tokens × $3/MTok × 60 appels = $0.54/jour. Avec cache = $0.054/jour.
**Protection :** Activer `cache_control` sur TOUS les system prompts. Gain : 90%.

### PIÈGE 4 — Haiku sous-utilisé
**Risque :** Utiliser Sonnet ($3/$15) pour des tâches que Haiku ($1/$5) gère très bien.
**Économie potentielle :** 60-70% sur les agents simples (Relance, Réputation, Prospection, Planning).
**Protection :** Router 70% du trafic vers Haiku, 30% vers Sonnet (devis, compta, fiscalité).

### PIÈGE 5 — ElevenLabs explose avec le vocal
**Risque :** Chaque réponse vocale = 150-300 caractères TTS. 50 artisans × 30 réponses/jour = 450K chars/mois.
**Coût :** 450K × $0.12/1K = $54/mois juste pour le TTS.
**Protection :**
- Utiliser le modèle **Turbo/Flash** ($0.06/1K au lieu de $0.12) → -50%
- Ne pas vocaliser les réponses longues : si >500 chars, résumer en 2 phrases avant TTS
- Fallback **OpenAI TTS** ($0.015/1K chars = 8× moins cher, qualité correcte)
- Mode "texte seul" par défaut, vocal uniquement si l'artisan a parlé

### PIÈGE 6 — Supabase Storage non contrôlé
**Risque :** Photos chantier haute résolution × 50 artisans × 20 photos/mois = 10 GB/mois.
**Coût :** Free = 1 GB. Au-delà = $0.021/GB/mois. 10 GB = $0.21/mois (pas cher, mais ça grossit).
**Protection :**
- Compression photos côté client avant upload (max 1 MB/photo)
- Suppression automatique des photos non liées à un chantier après 90 jours
- Taille max ticket OCR : 500 KB

### PIÈGE 7 — Whisper appelé pour rien
**Risque :** L'artisan appuie sur le micro par erreur → audio vide → Whisper facturé quand même.
**Protection :** Vérifier côté client que l'audio fait > 1s et > 500 bytes avant d'envoyer à Whisper.

### PIÈGE 8 — Background consciousness en roue libre
**Risque :** Le "cerveau qui pense entre les tâches" (pattern Ouroboros) fait un appel LLM toutes les heures × 50 artisans = 1200 appels/jour.
**Coût :** 1200 × $0.02 (Haiku) = $24/jour = **$720/mois**.
**Protection :**
- Consciousness uniquement pour les artisans ACTIFS (connectés dans les 24h)
- Max 3 pensées/jour par artisan (pas 24)
- Haiku uniquement pour la consciousness

### PIÈGE 9 — Relances cron sans plafond
**Risque :** Le cron de relance tourne chaque nuit et envoie des SMS/emails même si personne ne paie.
**Coût Twilio :** 50 artisans × 5 SMS/semaine = 1000 SMS/mois × $0.04 = $40/mois.
**Protection :**
- Relances SMS uniquement pour les artisans Pro/Business
- Relances email pour tous (quasi gratuit via Brevo)
- Max 3 relances par client par devis/facture

### PIÈGE 10 — Railway qui scale tout seul
**Risque :** Railway auto-scale les resources si le CPU spike. Un agent qui boucle → CPU 100% → Railway facture plus.
**Coût :** Railway Pro = $5/mois + usage. Usage non plafonné peut monter à $20-50.
**Protection :**
- Fixer des limites de resources dans le `railway.toml` : max 512 MB RAM, 0.5 vCPU
- Alertes Railway si usage > $10/mois
- Le cron des agents tourne sur un service séparé (pas le même que l'API)

### PIÈGE 11 — Anthropic extended thinking activé par défaut
**Risque :** Extended thinking génère des tokens de réflexion facturés comme des output tokens ($15/MTok en Sonnet).
**Coût :** Un agent qui "réfléchit" 5K tokens avant de répondre → $0.075 par appel au lieu de $0.02.
**Protection :**
- Extended thinking OFF par défaut
- L'activer UNIQUEMENT pour : agent Devis (décomposition complexe), agent Fiscalité (calculs)
- Budget thinking : max 2048 tokens

### PIÈGE 12 — Pas de monitoring des coûts
**Risque :** Tu ne sais pas combien tu dépenses avant de recevoir la facture fin de mois.
**Protection :**
- Dashboard coût temps réel dans l'admin STRUCTORAI
- Alerte Slack/email si coût journalier > $5 (phase launch)
- Alerte si un artisan dépasse $3/jour en API
- Log chaque appel API avec le coût estimé

---

## LIMITES PAR PLAN (pour contrôler les coûts)

| Limite | Starter (0€) | Pro (29€) | Business (79€) |
|--------|-------------|-----------|-----------------|
| Messages chat/jour | 10 | 100 | 500 |
| Devis/mois | 5 | Illimité | Illimité |
| Vocaux/jour | 5 | 50 | 200 |
| Scan tickets/mois | 10 | 200 | 1000 |
| Photos chantier/mois | 20 | 200 | 1000 |
| Relance SMS | ❌ | ✅ (max 50/mois) | ✅ (max 200/mois) |
| Relance email | 5/mois | Illimité | Illimité |
| Background consciousness | ❌ | 3 pensées/jour | 10 pensées/jour |
| Stockage | 100 MB | 1 GB | 5 GB |
| Budget LLM max/jour | $0.30 | $2.00 | $5.00 |

---

## SCÉNARIOS DE COÛTS MENSUELS

### Scénario A — Launch (20 artisans payants Pro)
| Poste | Coût/mois |
|-------|-----------|
| Railway (API + Workers) | $10 |
| Supabase (Free) | $0 |
| Vercel (Free) | $0 |
| Claude API (20 artisans × $2) | $40 |
| ElevenLabs Starter | $5 |
| OpenAI Whisper | $2 |
| Brevo (Free) | $0 |
| Twilio SMS | $8 |
| Domaine | $1.25 |
| **TOTAL** | **~$66 ≈ 60€** |
| **Revenus** (20 × 29€) | **580€** |
| **Marge nette** | **520€ (89%)** |

### Scénario B — Traction (100 artisans payants, mix Pro/Business)
| Poste | Coût/mois |
|-------|-----------|
| Railway (scale up) | $25 |
| Supabase Pro | $25 |
| Vercel (Free ou Pro) | $0-20 |
| Claude API (100 × $2.50 moyen) | $250 |
| ElevenLabs Pro | $99 |
| OpenAI Whisper | $10 |
| Brevo Starter | $9 |
| Twilio SMS | $40 |
| Sentry (paid si >5K events) | $0-26 |
| **TOTAL** | **~$480 ≈ 440€** |
| **Revenus** (80×29€ + 20×79€) | **3 900€** |
| **Marge nette** | **3 460€ (89%)** |

### Scénario C — Scale (500 artisans)
| Poste | Coût/mois |
|-------|-----------|
| Infra (Railway + Supabase + Vercel) | $150 |
| Claude API (500 × $2.50) | $1 250 |
| ElevenLabs Scale | $330 |
| Whisper + Twilio + Brevo | $100 |
| **TOTAL** | **~$1 830 ≈ 1 680€** |
| **Revenus** (400×29€ + 100×79€) | **19 500€** |
| **Marge nette** | **17 820€ (91%)** |

---

## OPTIMISATIONS COÛT — PRIORITÉ

| Optimisation | Économie | Difficulté | Quand l'implémenter |
|-------------|----------|-----------|-------------------|
| **Prompt caching** (system prompts) | -90% input tokens récurrents | Facile (1 ligne de config) | Sprint 2 — dès le premier appel API |
| **Routage Haiku/Sonnet** (70/30) | -50% coût LLM moyen | Moyen | Sprint 2 — Supervisor |
| **Context compaction** | -60% input tokens conversation | Moyen | Sprint 2 — après 10 messages |
| **Fallback OpenAI TTS** au lieu d'ElevenLabs | -87% coût TTS | Facile | Sprint 2 — option dans settings |
| **Budget cap par artisan/jour** | Protection contre les boucles | Facile | Sprint 2 — middleware |
| **Compression photos client-side** | -80% stockage | Facile | Sprint 4 — avant upload |
| **Batch API pour crons** (relances, consciousness) | -50% coût | Moyen | Sprint 5 — cron nocturne |

---

## ALERTES À METTRE EN PLACE DÈS LE SPRINT 1

| Alerte | Seuil | Canal |
|--------|-------|-------|
| Coût API journalier total | > $5 (phase launch) | Slack + email Fabrice |
| Coût API par artisan/jour | > $3 | Log + investigation |
| Railway usage | > $15/mois | Email |
| Supabase DB size | > 400 MB (free limit 500) | Email |
| Supabase Storage | > 800 MB | Email |
| Agent en boucle (>5 itérations) | Immédiat | Kill agent + log |
| Erreur rate > 10% | Sur 1h glissante | Sentry + Slack |

---

## RÉSUMÉ — CE QUE TU PAIES VRAIMENT

```
PHASE BUILD (2 mois) :     ~100€ total
PHASE LAUNCH (20 clients) : ~60€/mois   → 520€ de marge
PHASE TRACTION (100 clients): ~440€/mois → 3 460€ de marge  
PHASE SCALE (500 clients) : ~1 680€/mois → 17 820€ de marge

Le poste #1 c'est Claude API (~50-60% des coûts variables).
Le poste #2 c'est ElevenLabs TTS (~15-20%).
Tout le reste est négligeable (<10%).

Marge brute : 82-91% à tous les stades.
Le SaaS est très rentable SI tu contrôles les 12 pièges ci-dessus.
```
