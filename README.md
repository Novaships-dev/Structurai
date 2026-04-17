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

### 14 Agents IA Autonomes

| Agent | Ce qu'il fait |
|-------|---------------|
| **Devis** | Vocal → décomposition postes → prix marché → TVA multi-taux → PDF conforme → envoi + signature |
| **Relance** | Devis sans réponse J+3, factures impayées J+15/30/45, ton adaptatif par client |
| **Compta** | Photo ticket → OCR → catégorisation → attribution chantier → export comptable |
| **Planning** | Timer chantier, marge temps réel, détection dépassements, enchaînements |
| **Prospection** | CRM architectes/apporteurs, rappels automatiques, suivi réseau |
| **Fiscalité** | Suivi fiscal annuel par statut (micro/EURL/SAS), rappels URSSAF/TVA, alertes seuils, scan courrier admin, préparation dossier comptable |
| **Déplacements** | GPS intégré, frais km automatiques, indemnités BTP par zone, paniers repas, suivi carburant/parking/amendes |
| **RH** | Pointage heures employés, calcul heures sup (convention BTP), indemnités par employé/chantier/jour, congés CIBTP, export éléments de paie |
| **Email Pro** | Connexion boîte mail, filtrage IA, catégorisation, résumé quotidien, alertes urgentes, fiche prospect auto |
| **Vision IA** | Analyse toute photo/document : chantier → catégorisation, ticket → OCR, plan → compréhension, détection oublis dans les devis, **détection devis concurrent (F121)** |
| **Site Web** | Génération site vitrine IA en 5 min, MAJ auto mensuelle (photos/avis), validation artisan, SEO local |
| **Réputation & Marketing** | SMS avis Google + réponse IA + publication réseaux sociaux avant/après + SEO local |
| **Coach Business** (14ème agent) | Analyse stratégique mensuelle : taux horaire vs benchmark département, taux conversion devis, carnet de commandes, marge moyenne, positioning, recommandations pricing/prospection |
| **Mesure IA** (module) | Photos pièce → dimensions estimées (Vision IA ±20%), 4 niveaux (Vision/AR/LiDAR/Photogrammétrie), pré-devis à distance |

### Architecture Cerveau IA — 4 Couches

```
┌─────────────────────────────────────────────┐
│  COUCHE 4 — Supervisor (Orchestrateur)      │
│  Queue prioritaire · Workers · Budget LLM   │
│  Background consciousness · Circuit breaker │
├─────────────────────────────────────────────┤
│  COUCHE 3 — 14 Agents Autonomes              │
│  Devis · Relance · Compta · Planning        │
│  Réputation & Marketing · Prospection       │
│  Email Pro · Fiscalité & Trésorerie         │
│  Déplacements · RH · Vision IA · Site Web   │
│  Coach Business (14ème agent audit V6)       │
├─────────────────────────────────────────────┤
│  COUCHE 2 — Mémoire Persistante (Mem0)      │
│  Prix artisan · Clients · Fournisseurs      │
│  Tempo chantiers · Style devis · Réseau     │
├─────────────────────────────────────────────┤
│  COUCHE 1 — LLM Spécialisé BTP             │
│  System prompt métier · RAG base BTP        │
│  TVA · Mentions légales · Prix marché       │
│  11 référentiels techniques (8800+ lignes)  │
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
| **Paiement** | Stripe (7 options : Starter €0 / Pro €29 / Pro annuel €229 / Business €79 / Business annuel €629 / Lifetime €990 / Fédération -20% avec CAPEB20/FFB20) |
| **Communication** | WhatsApp Business API · Brevo (email) · Twilio (SMS) |
| **Déploiement** | Railway (backend) · Vercel (PWA) · Google Play (Android) |
| **Monitoring** | Sentry · Google Analytics 4 |
| **i18n** | 6 langues : FR, EN, TR, ES, PT, AR |

---

## Structure du Repo

```
structorai/
├── PRODUCT_CONTEXT.md          # Source de vérité produit
├── BUILD_PLAN.md               # Blueprint build — ~320 fichiers, 9 sprints V1 (S0-S8) + V1.5 + V2 + V3
├── FEATURES.md                 # 135 features détaillées (V1 / V2 / V3)
├── FICHE_METIER.md             # Inventaire des fichiers data métier
├── CLAUDE.md                   # Instructions Claude Code — la loi du repo
├── README.md                   # Ce fichier
├── UX_PARCOURS.md            # Navigation, onglets, 7 parcours UX
├── ANALYSE_CONCURRENCE.md    # Analyse concurrentielle complète — fiches concurrents
├── STRATEGIE-COMPETITIVE.md  # Plan d'action offensif face aux concurrents
├── COUT_REEL.md              # Coûts réels API/infra et pièges à éviter
├── SECURITE.md               # Audit sécurité complet — RGPD, RLS, vecteurs d'attaque
├── DISTRIBUTION.md           # Stratégie distribution France + expansion 10 pays Europe
├── COMMUNICATION.md          # Stratégie de communication complète
│
├── PLOMBERIE/                  # Référentiel technique plomberie
├── ELECTRICITE/                # Référentiel technique électricité
├── CARRELAGE/                  # Référentiel technique carrelage
├── MACONNERIE/                 # Référentiel technique maçonnerie
├── PEINTURE/                   # Référentiel technique peinture
├── MENUISERIE_SERRURERIE/      # Référentiel technique menuiserie & serrurerie
├── FACADE/                     # Référentiel technique façadier
├── PLAQUISTE/                  # Référentiel technique plâtrier-plaquiste
├── COUVERTURE/                 # Référentiel technique couverture
├── CHAUFFAGE_CLIM/             # Référentiel technique chauffage & climatisation
└── ISOLATION/                  # Référentiel technique isolation
```

### Référentiels Techniques — 11 Corps de Métier

La base de connaissances BTP du cerveau IA. Chaque référentiel contient : matériaux avec prix fournisseurs réels, toutes les mises en oeuvre chiffrées (fourchettes min-max), outillage, DTU/normes, erreurs terrain courantes, coefficients régionaux (11 zones de Paris au DOM-TOM), coordination inter-corps de métier, règles IA pricing (9 règles impératives par métier), assurances et garanties, temps de travail réaliste, consommables détaillés, et devis types chiffrés ligne par ligne.

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
| Couverture | 703 | 16 | 40+ | 12 | 3 devis |
| Chauffage & Climatisation | 693 | 18 | 50+ | 12 | 3 devis |
| Isolation | 765 | 17 | 40+ | 12 | 3 devis |
| **Total** | **8819** | **236** | **1080+** | **132** | **29+** |

> **Total documentation** : ~16 000+ lignes (référentiels techniques + PRODUCT_CONTEXT + BUILD_PLAN + FEATURES + FICHE_METIER + CLAUDE.md + UX_PARCOURS + ANALYSE_CONCURRENCE + COUT_REEL + SECURITE + DISTRIBUTION).

---

## Plan de Build

**~320 fichiers · ~25 jours · 9 sprints V1 (S0-S8) + V1.5 + V2 + V3**

| Sprint | Contenu | Durée |
|--------|---------|-------|
| **0** | Setup infra (Fabrice) — domaines, Supabase, Railway, Stripe, comptes API | 1 jour |
| **1** | Fondations — FastAPI skeleton, auth, **37 migrations SQL**, RLS, Stripe **7 plans (F130)**, Expo init, i18n, **architecture multi-pays F135** | ~2 jours |
| **2** | Chat + Voix + Cerveau IA — Supervisor, Mem0, pipeline vocal, WebSocket, WhatsApp + **F112 Import concurrents** | ~3 jours |
| **3** | Agent Devis — vocal→postes→prix→TVA→PDF Factur-X, knowledge base BTP, signature + **F121 Détecteur devis concurrent** + **F128 Validation RGE** | ~3 jours |
| **4** | Pipeline chantiers + Compta + **F113 Scan carte fidélité grossistes** | ~3 jours |
| **5** | Relances + Factures — Agent Relance, Brevo, Twilio, cron jobs, PA Factur-X | ~2 jours |
| **6** | Réputation + Prospection + Email + Fiscalité + Déplacements + **F119 Agent Coach Business (14ème)** + **F129 Pack contrôle fiscal** + **F131 AI Act** | ~3 jours |
| **7** | Gamification + RH + **F114 Parrainage** + **F116 Rapport annuel** + **F120 Capsules** + **F123 Tâches employés** + **F124 App Compagnon** | ~3 jours |
| **8** | Build + Deploy + Launch + **F115 Sandbox** + **F125 Onboarding inversé** + **F132 Résilience** + **F133 Artisans étrangers** | ~2 jours |

Build entièrement réalisé par **Claude Code**. Fabrice crée les comptes infra et configure les clés.

### Patterns Open-Source Intégrés

| Pattern | Source | Usage |
|---------|--------|-------|
| Supervisor + workers + queue | Ouroboros (456★) | Orchestration des 14 agents |
| Background consciousness | Ouroboros | Proactivité — le cerveau pense entre les tâches |
| Circuit breaker + fallback | Ouroboros v6.0 | 3 réponses vides → pause + fallback model |
| Context compaction | Ouroboros v6.1 | Résumé contexte long via LLM léger |
| Tool auto-discovery | Ouroboros + claw-code (121K) | Agents découvrent leurs outils dynamiquement |
| Mémoire persistante multi-level | Mem0 (91K+) | Prix, clients, habitudes par artisan |
| Skills/plugins | OpenClaw (247K) + superpowers (120K) | Modules métier comme plugins |

---

## Pricing — 7 options (audit V6)

| Plan | Prix | Trial | Cible |
|------|------|-------|-------|
| **Starter** | Gratuit | — | Découverte — 5 devis/mois, chat limité, Coach 1×/mois |
| **Pro** | 29€/mois | 30 j | Artisan solo — devis illimités, 14 agents, voix |
| **Pro annuel** | 229€/an (≈ 19€/mois, -35%) | 30 j | Artisan engagement 12 mois |
| **Business** | 79€/mois | 30 j | Artisan + employés — multi-user, API, priorité, app Compagnon |
| **Business annuel** | 629€/an (≈ 52€/mois, -35%) | 30 j | TPE engagement 12 mois |
| **Lifetime** | 990€ une fois | — | Promo launch, limité à 100 licences |
| **Fédération** | Pro/Business -20% à vie | 30 j | Code `CAPEB20` / `FFB20`, membres fédérations |

**Add-on Agent Téléphone IA (V2)** : +15€/mois sur tous les plans sauf Starter.

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
