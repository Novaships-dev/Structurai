# 🏗️ STRUCTORAI

**"L'assistant IA qui gère ton business pendant que tu es sur le chantier."**

App mobile avec cerveau IA spécialisé bâtiment — chat vocal, scan de tickets, devis par la voix, relances automatiques, suivi de chantiers — accessible sur Android, iPhone (PWA) et WhatsApp.

> **Tool #2 Nova** — [@NovaShips](https://x.com/NovaShips) · Launch prévu Juin 2026
> Domaine : [structorai.app](https://structorai.app)

---

## Le Problème

500 000 artisans BTP en France. 83% n'ont pas commencé leur transition numérique. Ils gèrent leur business avec des bouts de papier, l'app Notes du téléphone, et Excel le soir à 21h quand ils devraient être avec leur famille.

**Leur quotidien :**
- Prospects qui appellent sur le chantier → notes perdues → devis en retard → client parti ailleurs
- Tickets de caisse froissés dans la poche → oublis de frais → perte d'argent
- Devis interminables le soir (1-2h par devis, 9/10 refusés)
- Factures impayées sans relance → trésorerie morte
- Zéro visibilité sur la marge réelle par chantier

**Le timing :** La facturation électronique Factur-X devient **obligatoire en septembre 2026**. Fenêtre massive.

---

## La Solution

Un cerveau IA dans la poche de l'artisan. Il parle, le cerveau fait le reste.

### 3 Canaux — 1 Cerveau

| Canal | Comment | Pour qui |
|-------|---------|----------|
| **Android** (Play Store) | App native React Native (Expo) | Usage principal, dashboard complet |
| **iPhone** (PWA) | Web app installable, bypass Apple Store | iOS sans les $99/an |
| **WhatsApp** | Actions rapides vocal/photo | Sur le chantier, mains occupées |

### 9 Agents IA Autonomes

| Agent | Ce qu'il fait |
|-------|---------------|
| **Devis** | Vocal → décomposition postes → prix marché → TVA multi-taux → PDF conforme → envoi + signature |
| **Relance** | Devis sans réponse J+3, factures impayées J+15/30/45, ton adaptatif par client |
| **Compta** | Photo ticket → OCR → catégorisation → attribution chantier → export comptable |
| **Planning** | Timer chantier, marge temps réel, détection dépassements, enchaînements |
| **Réputation** | SMS avis Google post-chantier, réponse IA aux avis, suivi score |
| **Prospection** | CRM architectes/apporteurs, rappels automatiques, suivi réseau |
| **Fiscalité** | Suivi fiscal annuel par statut (micro/EURL/SAS), rappels URSSAF/TVA, alertes seuils, scan courrier admin, préparation dossier comptable |
| **Déplacements** | GPS intégré, frais km automatiques, indemnités BTP par zone, paniers repas, suivi carburant/parking/amendes |
| **RH** | Pointage heures employés, calcul heures sup (convention BTP), indemnités par employé/chantier/jour, congés CIBTP, export éléments de paie |

### Architecture Cerveau IA — 4 Couches

```
┌─────────────────────────────────────────────┐
│  COUCHE 4 — Supervisor (Orchestrateur)      │
│  Queue prioritaire · Workers · Budget LLM   │
│  Background consciousness · Circuit breaker │
├─────────────────────────────────────────────┤
│  COUCHE 3 — 9 Agents Autonomes              │
│  Devis · Relance · Compta · Planning        │
│  Réputation · Prospection · Fiscalité       │
│  Déplacements · RH                          │
├─────────────────────────────────────────────┤
│  COUCHE 2 — Mémoire Persistante (Mem0)      │
│  Prix artisan · Clients · Fournisseurs      │
│  Tempo chantiers · Style devis · Réseau     │
├─────────────────────────────────────────────┤
│  COUCHE 1 — LLM Spécialisé BTP             │
│  System prompt métier · RAG base BTP        │
│  TVA · Mentions légales · Prix marché       │
│  8 référentiels techniques (6600+ lignes)   │
└─────────────────────────────────────────────┘
```

---

## Stack Technique

| Couche | Technologie |
|--------|-------------|
| **Mobile** | React Native (Expo) · TypeScript · NativeWind (Tailwind) |
| **Backend** | FastAPI · Python 3.12+ |
| **Base de données** | Supabase (PostgreSQL + Auth + RLS + Realtime) |
| **IA / LLM** | Claude API (Anthropic) · Whisper/Deepgram (STT) · ElevenLabs (TTS) |
| **Mémoire** | Mem0 (mémoire persistante multi-level par artisan) |
| **Paiement** | Stripe (Free €0 / Pro €29 / Business €79) |
| **Communication** | WhatsApp Business API · Brevo (email) · Twilio (SMS) |
| **Déploiement** | Railway (backend) · Vercel (PWA) · Google Play (Android) |
| **Monitoring** | Sentry · Google Analytics 4 |
| **i18n** | 6 langues : FR, EN, TR, ES, PT, AR |

---

## Structure du Repo

```
structorai/
├── PRODUCT_CONTEXT.md          # Source de vérité produit (987 lignes)
├── BUILD_PLAN.md               # Blueprint build — 300 fichiers, 8 sprints (755 lignes)
├── FEATURES.md                 # Liste exhaustive des 93 features V2 (546 lignes)
├── FICHE_METIER.md             # Inventaire 63 fichiers data métier (887 lignes)
├── CLAUDE.md                   # Instructions Claude Code — la loi du repo
├── README.md                   # Ce fichier
│
├── PLOMBERIE/                  # Référentiel technique plomberie (1021 lignes)
├── ELECTRICITE/                # Référentiel technique électricité (960 lignes)
├── CARRELAGE/                  # Référentiel technique carrelage (930 lignes)
├── MACONNERIE/                 # Référentiel technique maçonnerie (857 lignes)
├── PEINTURE/                   # Référentiel technique peinture (791 lignes)
├── MENUISERIE_SERRURERIE/      # Référentiel technique menuiserie & serrurerie (744 lignes)
├── FACADE/                     # Référentiel technique façadier (699 lignes)
└── PLAQUISTE/                  # Référentiel technique plâtrier-plaquiste (656 lignes)
```

### Référentiels Techniques — 8 Corps de Métier

La base de connaissances BTP du cerveau IA. Chaque référentiel contient : matériaux avec prix fournisseurs réels, toutes les mises en oeuvre chiffrées (fourchettes min-max), outillage, DTU/normes, erreurs terrain courantes, coefficients régionaux (9 zones de Paris au DOM-TOM), coordination inter-corps de métier, règles IA pricing (9 règles impératives par métier), assurances et garanties, temps de travail réaliste, consommables détaillés, et devis types chiffrés ligne par ligne.

| Métier | Lignes | Sections | Postes chiffrés | Erreurs terrain | Devis types |
|--------|--------|----------|----------------|-----------------|-------------|
| Plomberie | 1021 | 12 | 100+ | 13 | ✅ |
| Électricité | 960 | 24 | 120+ | 13 | ✅ |
| Carrelage | 930 | 26 | 80+ | 14 | 2 devis |
| Maçonnerie | 857 | 30 | 150+ | 12 | 3 devis |
| Peinture | 791 | 18 | 100+ | 12 | 3 devis |
| Menuiserie & Serrurerie | 744 | 26 | 200+ | 12 | 3 devis |
| Façadier | 699 | 25 | 60+ | 10 | 3 devis |
| Plaquiste | 656 | 24 | 60+ | 10 | 3 devis |
| **Total** | **6658** | **185** | **870+** | **96** | **20+** |

> **Total documentation** : ~10 000+ lignes (référentiels techniques + PRODUCT_CONTEXT + BUILD_PLAN + FEATURES + FICHE_METIER + CLAUDE.md).

---

## Plan de Build

**~300 fichiers · ~18-20 jours · 8 sprints**

| Sprint | Contenu | Durée |
|--------|---------|-------|
| **0** | Setup infra (Fabrice) — domaines, Supabase, Railway, Stripe, comptes API | 1 jour |
| **1** | Fondations — FastAPI skeleton, auth, 25 migrations SQL, RLS, Stripe, Expo init, i18n | ~2 jours |
| **2** | Chat + Voix + Cerveau IA — Supervisor, Mem0, pipeline vocal, WebSocket, WhatsApp | ~3 jours |
| **3** | Agent Devis — vocal→postes→prix→TVA→PDF Factur-X, knowledge base BTP, signature | ~3 jours |
| **4** | Pipeline chantiers + Compta — Kanban, timer, OCR tickets, catégorisation | ~2 jours |
| **5** | Relances + Factures — Agent Relance, Brevo, Twilio, cron jobs | ~2 jours |
| **6** | Réputation + Prospection + Email + Fiscalité + Déplacements | ~3 jours |
| **7** | Gamification + Polish + RH (employés) | ~2 jours |
| **8** | Build + Deploy + Launch — Play Store, PWA, landing page, tests | ~2 jours |

Build entièrement réalisé par **Claude Code**. Fabrice crée les comptes infra et configure les clés.

### Patterns Open-Source Intégrés

| Pattern | Source | Usage |
|---------|--------|-------|
| Supervisor + workers + queue | Ouroboros (456★) | Orchestration des 9 agents |
| Background consciousness | Ouroboros | Proactivité — le cerveau pense entre les tâches |
| Circuit breaker + fallback | Ouroboros v6.0 | 3 réponses vides → pause + fallback model |
| Context compaction | Ouroboros v6.1 | Résumé contexte long via LLM léger |
| Tool auto-discovery | Ouroboros + claw-code (121K) | Agents découvrent leurs outils dynamiquement |
| Mémoire persistante multi-level | Mem0 (91K+) | Prix, clients, habitudes par artisan |
| Skills/plugins | OpenClaw (247K) + superpowers (120K) | Modules métier comme plugins |

---

## Pricing

| Plan | Prix | Cible |
|------|------|-------|
| **Starter** | Gratuit | Découverte — 5 devis/mois, chat limité |
| **Pro** | 29€/mois | Artisan solo — devis illimités, 9 agents, voix |
| **Business** | 79€/mois | Artisan + employés — multi-user, API, priorité |

ARPU cible : 40€/mois · Kill criteria : <200€ MRR après 12 semaines

---

## Contexte Nova

Nova ([@NovaShips](https://x.com/NovaShips)) = indie hacker anonyme. Ship 1 SaaS/mois d'avril à septembre 2026. Objectif €10K MRR cumulé.

| # | Tool | Status |
|---|------|--------|
| 1 | **AgentShield** — Observabilité IA | LIVE depuis 01/04/2026 |
| 2 | **STRUCTORAI** — Assistant IA artisans BTP | BUILD — launch Juin 2026 |

Stack identique tous SaaS : FastAPI + Next.js/Expo + TypeScript + Tailwind + Supabase + Stripe + Railway + Vercel.

---

## License

MIT
