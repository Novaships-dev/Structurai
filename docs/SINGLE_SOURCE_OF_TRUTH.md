---
name: SINGLE_SOURCE_OF_TRUTH
description: Table centrale de TOUS les chiffres, valeurs, versions, seuils et décisions du projet STRUCTORAI. Source de vérité unique — en cas de contradiction dans un autre fichier du repo, c'est ce fichier qui fait foi.
type: reference
scope: all-files, all-agents, all-services
priority: immuable
date: 2026-04-17
version: 1.0
maintainer: Fabrice (Nova)
---

# docs/SINGLE_SOURCE_OF_TRUTH.md — Source de vérité STRUCTORAI

> **Tout autre fichier du repo pointe vers celui-ci.**
> Plus jamais de chiffres dispersés ni de valeurs contradictoires.
> Toute modification DOIT être loguée dans `docs/CHANGELOG.md` + `docs/DECISIONS-LOG.md`.

---

## 0. SOMMAIRE

1. [Identité projet](#1-identité-projet)
2. [Composants produit](#2-composants-produit)
3. [Les 14 agents IA](#3-les-14-agents-ia)
4. [Budget LLM Supervisor (allocation par agent)](#4-budget-llm-supervisor-allocation-par-agent)
5. [Features — total, découpage V1/V2/V3](#5-features--total-découpage-v1v2v3)
6. [Migrations SQL](#6-migrations-sql)
7. [Plans tarifaires (7 options)](#7-plans-tarifaires-7-options)
8. [Marges et coûts par artisan](#8-marges-et-coûts-par-artisan)
9. [Modèles Claude utilisés](#9-modèles-claude-utilisés)
10. [Trial, conversion, onboarding](#10-trial-conversion-onboarding)
11. [Métriques cibles](#11-métriques-cibles)
12. [Sprints et durée build](#12-sprints-et-durée-build)
13. [Données métier BTP (chiffres)](#13-données-métier-btp-chiffres)
14. [Multi-pays et langues](#14-multi-pays-et-langues)
15. [Budget one-time et récurrents](#15-budget-one-time-et-récurrents)
16. [Rétrocompatibilité et kill criteria](#16-rétrocompatibilité-et-kill-criteria)
17. [Historique des audits](#17-historique-des-audits)

---

## 1. IDENTITÉ PROJET

| Clé | Valeur |
|---|---|
| Nom du produit | **STRUCTORAI** (uppercase en titre), **Structorai** (prose), **structorai** (slugs/code) |
| Slogan #1 | "100% du pipeline artisan" |
| Slogan #2 | "14 agents. 1 cerveau. Tout ton business." |
| Tool # dans le challenge Nova | **Tool #2** (après AgentShield) |
| Domaines | `structorai.app` + `structorai.io` |
| Sous-domaine client (V2) | `client.structorai.app/[chantier-token]` |
| Sous-domaine transparence | `structorai.app/transparency` |
| Date démarrage build | **14/04/2026** |
| Date launch prévue | **juin 2026** |
| Cible MRR septembre 2026 | **€10 000 cumulé** (objectif Nova challenge) |
| Email contact | `contact@structorai.app` |
| Email DPO (RGPD) | `dpo@structorai.app` |
| Email confidentialité | `privacy@structorai.app` |
| Nom produit à l'international | STRUCTORAI (inchangé) |

---

## 2. COMPOSANTS PRODUIT

| Composant | Nombre | Détail |
|---|---|---|
| **Agents IA V1** | **14** | Devis, Relance, Compta, Planning, Réputation & Marketing, Prospection, Email Pro, Fiscalité & Trésorerie, Déplacements, RH, Vision IA, Site Web, Coach Business, + Supervisor (hors-compte) |
| **Supervisor** | **1** | Orchestrateur — hors des 14 agents |
| **Agents V2** | **1** | Agent Téléphone IA (+ extension télé-dépannage F126) |
| **Modules fonctionnels** | **4** | Galerie Photo, Gamification, Mesure IA, Dossier "À faire" |
| **TOTAL COMPOSANTS** | **19** | 14 agents + Supervisor + 4 modules fonctionnels |
| **Corps de métier BTP couverts** | **11** | Plomberie, Électricité, Carrelage, Peinture, Maçonnerie, Menuiserie-Serrurerie, Plaquiste, Façade, Couverture, Chauffage/Climatisation, Isolation |
| **Référentiels techniques (lignes totales)** | **8 819** | 11 fichiers `.md` avec 1 080+ postes chiffrés et 132 erreurs terrain documentées |
| **Zones régionales coefficients** | **11** | Voir §13 + `docs/METIER.md` §9 |
| **Langues supportées** | **6** | Français (prio), Anglais, Turc, Portugais, Arabe, Espagnol |
| **Canaux d'entrée** | **3** | App mobile, PWA web, WhatsApp |
| **Mentions obligatoires devis** | **47** | 15 légales + 32 complémentaires BTP (voir `docs/METIER.md` §2) |
| **Nouvelles mentions facture 2026** | **4** | SIREN client B2B, adresse livraison, catégorie opération, option TVA débits |

---

## 3. LES 14 AGENTS IA

| # | Agent | Fichier | Modèle Claude | Budget/appel | Scope |
|---|---|---|---|---|---|
| — | **Supervisor** | `agents/supervisor.py` | `claude-sonnet-4-6` | $0.10 | Orchestrateur — routage, queue, budget, briefing, consciousness |
| 1 | Devis | `agents/agent_devis.py` | `claude-opus-4-7` | $0.15 | Vocal/texte/photo → postes → prix → TVA → PDF 47 mentions → Yousign → acompte |
| 2 | Relance | `agents/agent_relance.py` | `claude-haiku-4-5-20251001` | $0.02 | Devis J+3, factures J+15/30/45, ton adaptatif (Mem0), mise en demeure |
| 3 | Compta | `agents/agent_compta.py` | `claude-sonnet-4-6` | $0.08 | OCR tickets, catégorisation, marge temps réel, export |
| 4 | Planning | `agents/agent_planning.py` | `claude-haiku-4-5-20251001` | $0.03 | Timer chantier, dépassements, capacité, rappels matériaux |
| 5 | Réputation & Marketing | `agents/agent_reputation.py` | `claude-haiku-4-5-20251001` | $0.02 | Avis Google (SMS + IA), réseaux sociaux (avant/après), SEO |
| 6 | Prospection | `agents/agent_prospection.py` | `claude-haiku-4-5-20251001` | $0.02 | CRM archi/apporteurs, leads, rappels réseau |
| 7 | Email Pro | `agents/agent_email_pro.py` | `claude-haiku-4-5-20251001` | $0.03 | IMAP, filtrage pro/perso, résumé quotidien, fiche prospect auto |
| 8 | Fiscalité & Trésorerie | `agents/agent_fiscalite.py` | `claude-sonnet-4-6` | $0.05 | Statuts Micro/EURL/SAS/EI, URSSAF, TVA, trésorerie prévisionnelle |
| 9 | Déplacements | `agents/agent_deplacements.py` | `claude-haiku-4-5-20251001` | $0.02 | Frais km, paniers, indemnités BTP par zone (Grand Paris, petit dépl.) |
| 10 | RH | `agents/agent_rh.py` | `claude-sonnet-4-6` | $0.05 | Pointage, heures sup conv. BTP, CIBTP, intérim, export paie |
| 11 | Vision IA | `agents/agent_vision.py` | `claude-opus-4-7` | $0.08 | Premier filtre images — chantier/ticket/courrier, détecteur devis concurrent (F121) |
| 12 | Site Web | `agents/agent_site_web.py` | `claude-sonnet-4-6` | $0.10 | Génération + MAJ site vitrine IA, SEO local, avis Google intégrés |
| 13 | Coach Business (F119, 14ème agent audit V6) | `agents/agent_coach.py` | `claude-opus-4-7` | $0.10 | Conseil stratégique mensuel — taux horaire, conversion, marge, positioning. **Positionnement "éclaireur"** (jamais décideur, disclaimers UI obligatoires, voir `docs/COACH-DISCLAIMER.md`) |
| 14 (V2) | Téléphone IA | `agents/agent_telephone.py` | `claude-sonnet-4-6` | $0.10 | Décroche, prise d'info, + télé-dépannage F126 via arbres décision `data/diagnostics/` |

---

## 4. BUDGET LLM SUPERVISOR (ALLOCATION PAR AGENT)

Redistribution % du budget mensuel LLM de chaque artisan (audit V6 — redistribution suite ajout Coach Business) :

| Agent | Budget alloué |
|---|---|
| **Devis** | **20%** (était 22% avant audit V6) |
| Compta | 9% (était 10%) |
| Vision IA | 9% (était 10%) |
| Fiscalité & Trésorerie | 8% |
| RH | 8% |
| Site Web | 7% (était 8%) |
| Relance | 7% |
| Planning | 6% |
| Réputation & Marketing | 6% |
| **Coach Business** (nouveau V6) | **6%** |
| Prospection | 5% (était 6%) |
| Email Pro | 4% |
| Déplacements | 3% |
| **V2 Téléphone IA** | **2%** (réservé post-launch) |
| **TOTAL** | **100%** |

Budget quotidien par artisan :
- **Pro / Pro annuel** : cap quotidien **$2.00/jour**
- **Business / Business annuel** : cap quotidien **$5.00/jour**
- **Starter** : cap quotidien **$0.50/jour**
- Au-delà : réponse "limite quotidienne atteinte"

---

## 5. FEATURES — TOTAL, DÉCOUPAGE V1/V2/V3

| Métrique | Valeur |
|---|---|
| **Total features** | **136** (F001 à F136) |
| Features V1 (launch juin 2026) | **133** (136 moins 3 reports V2/V3) |
| Features V2 (sept-oct 2026) | **4** (F122 Pennylane/Cegid + F126 Télé-dépannage + F127 Portail client + F113 V2 API grossistes) |
| Features V3 (2027+) | **2** (F109 Photogrammétrie + F113 V3 API grossistes exclusives post 2K MRR) |

### Features structurantes (audit V6 — F112 à F135) — 24 features

| # | Feature | Statut | Sprint |
|---|---|---|---|
| F112 | Import universel concurrents (Obat, Tolteck, Batigest, EBP, Excel, Facture.net) | V1 | 2-3 |
| F113 | Partenariats grossistes (V1 scan carte / V2 API temps réel / V3 exclusif) | V1-V2-V3 | 4 / V2 / V3 |
| F114 | Code parrain structurel | V1 | 7 |
| F115 | Sandbox publique sans inscription | V1 | 8 |
| F116 | Rapport annuel de l'artisan | V1 | 7 |
| F117 | Score "cerveau me connaît à X%" + warning switching cost | V1 | 7 |
| F118 | Switching cost moat (import entrant, pas d'export symétrique) | V1 | Doc |
| F119 | Agent Coach Business (14ème agent) | V1 | 6-7 |
| F120 | Formation contextuelle par capsules vidéo 60s | V1 | 7 |
| F121 | Détection et analyse devis concurrent (Vision IA) | V1 | 3 |
| F122 | Synchro compta native (Pennylane/Cegid/Sage/QuickBooks) | **V2** | post-launch |
| F123 | Assignation tâches employés avec suivi photo | V1 | 7 |
| F124 | App mode Compagnon (employés) | V1 | 7 |
| F125 | Onboarding inversé 90 secondes | V1 | 8 |
| F126 | Télé-dépannage (extension Agent Téléphone) | **V2** | post-launch |
| F127 | Portail client final (mini-PWA `client.structorai.app`) | **V2** | post-launch |
| F128 | Validation auto RGE/Qualibat temps réel | V1 | 3 |
| F129 | Pack contrôle fiscal (ZIP signé horodaté) | V1 | 6 |
| F130 | Tarification enrichie 7 options | V1 | 1 |
| F131 | Conformité AI Act (page /transparency + badge Décision IA + audit log) | V1 | 6 |
| F132 | Résilience et redondance (fallback LLM, read replica, monitoring) | V1 | 8 |
| F133 | Acquisition artisans étrangers en France (6 langues + ciblage culturel) | V1 | 8 |
| F134 | Verticalisation (éditions métier — architecture V1, lancement post 2K MRR) | V1 archi / V2+ | Sprint 1 archi |
| F135 | Architecture multi-pays (country_code sur toutes tables) | V1 | 1 |

### Feature F136 (audit V7)

| # | Feature | Statut | Sprint |
|---|---|---|---|
| F136 | Support hybride IA + humain (3 niveaux : IA N1 70% / async <2h / urgence <1h SMS) | V1 | 6 |

---

## 6. MIGRATIONS SQL

**Total : 37 migrations** (29 historiques + 8 ajoutées audit V6)

### Migrations 001-029 (historiques)

001 organizations · 002 users · 003 clients · 004 chantiers · 005 devis · 006 devis_lines · 007 factures · 008 tickets · 009 depenses · 010 time_entries · 011 photos · 012 contacts_pro · 013 avis_google · 014 emails · 015 relances · 016 agent_tasks · 017 agent_memory · 018 agent_budget · 019 knowledge_base · 020 gamification · 021 api_keys · 022 subscriptions · 023 metier_rules · 024 helper_functions · 025 rls_policies · 026 deplacements · 027 employes · 028 fiscal_calendar · 029 deplacements_frais

### Migrations 030-037 (audit V6 et V7)

| # | Fichier | Contenu | Feature |
|---|---|---|---|
| 030 | `create_referrals.sql` | Programme parrainage | F114 |
| 031 | `create_capsules.sql` | Formation contextuelle vidéo 60s | F120 |
| 032 | `create_tasks.sql` | Assignation tâches employés | F123 |
| 033 | `update_subscriptions_plans.sql` | 7 plans tarifaires + add-on Téléphone | F130 |
| 034 | `create_diagnostics.sql` | Arbres décision télé-dépannage (V2) | F126 |
| 035 | `create_client_tokens.sql` | Portail client final (V2) | F127 |
| 036 | `create_ai_audit_log.sql` | Logs AI Act (décisions IA horodatées) | F131 |
| 037 | `add_country_edition.sql` | country_code + edition sur organizations | F134 + F135 |

---

## 7. PLANS TARIFAIRES (7 OPTIONS)

| Plan | Prix | Trial | Cible | Limites |
|---|---|---|---|---|
| **Starter** | €0/mois | — | Auto-entrepreneurs découverte | 5 devis/mois · Coach 1×/mois · pas de relance auto · cap $0.50/jour |
| **Pro** | €29/mois | 30 jours | Artisan seul | Devis illimités · 14 agents actifs · voix · cap $2/jour |
| **Pro annuel** | €229/an (≈ €19/mois, -35%) | 30 jours | Artisan engagement 12 mois | Idem Pro, paiement annuel |
| **Business** | €79/mois | 30 jours | TPE 2-10 salariés | Tout Pro + multi-user + API + priorité · cap $5/jour |
| **Business annuel** | €629/an (≈ €52/mois, -35%) | 30 jours | TPE engagement 12 mois | Idem Business, paiement annuel |
| **Lifetime** | €990 une fois | — | Promo launch | Accès Pro à vie · **limité à 100 licences** · non renouvelable |
| **Fédération** | Pro -20% / Business -20% à vie | 30 jours | Membres CAPEB / FFB | Codes promo `CAPEB20` / `FFB20` · justificatif membre requis |

**Add-on Agent Téléphone IA (V2)** : **+15€/mois** sur Pro/Pro annuel/Business/Business annuel (pas en Starter).

**Règles de facturation :**
- Tous les prix en **EUR HT**
- Stripe Products dédiés par plan + add-on (Migration 033)
- **Grâce period 7 jours** après échec de paiement (pas de coupure sèche — différenciation vs Tolteck)
- Après 7 jours d'impayé : mode lecture seule (pas de création, consultation OK)
- Après 30 jours d'impayé : suspension complète + export RGPD disponible 30 jours

---

## 8. MARGES ET COÛTS PAR ARTISAN

### Coût LLM total par artisan par mois

| Phase | Coût | Détail |
|---|---|---|
| Sans optimisation | ~$13/mois | Pas de caching, pas de Haiku |
| Optimisé V5 (14/04) | **$4.50/mois** | Routage 70% Haiku + prompt caching + consciousness réduite |
| Optimisé V6 (17/04) | **$4.80/mois** | + Coach Business ($0.20) + capsules ($0.05) + benchmarks ($0.05) |
| Optimisé V7 (≥50 clients) | **~$7/mois équivalent** | + 2€ support externalisé à partir de 50 clients |

### Marges par plan

| Plan | Prix | Coût LLM | Coût infra + capsules | **Marge brute** | **% marge** |
|---|---|---|---|---|---|
| **Pro €29** (<50 clients) | 29€ | 4.40€ | 1.25€ | **23.35€** | **80%** |
| **Pro €29** (≥50 clients, audit V7) | 29€ | 4.40€ | 1.25€ + 2€ support | **21.35€** | **78%** |
| **Pro annuel €229/an** | 19€ équivalent | 4.40€ | 1.25€ | **13.35€** | **70%** |
| **Business €79** (<50 clients) | 79€ | 6.20€ | 2.50€ | **70.30€** | **89%** |
| **Business €79** (≥50 clients) | 79€ | 6.20€ | 2.50€ + 2€ support | **68.30€** | **87%** |
| **Business annuel €629/an** | 52€ équivalent | 6.20€ | 2.50€ | **43.30€** | **83%** |
| **Lifetime €990** | amorti 24 mois | 115€ cumulés (24 mois) | + infra | **~875€** | **~88% amorti** |
| **Fédération -20% Pro** | 23.20€ | 4.40€ | 1.25€ | **~17.55€** | **76%** |
| **Fédération -20% Business** | 63.20€ | 6.20€ | 2.50€ | **~54.50€** | **86%** |
| **Starter €0** | 0€ | ~0.80€ (usage limité) | 0.50€ | **-1.30€** | Perte (acquisition) |

**ARPU cible** : **€40/mois** (mix Pro/Business + engagements annuels + Lifetime amorti)

---

## 9. MODÈLES CLAUDE UTILISÉS

| Modèle | Version exacte | Coût input / output (par M tokens) | Usage STRUCTORAI |
|---|---|---|---|
| **Claude Opus 4.7** | `claude-opus-4-7` | ~$15 / ~$75 | Agents critiques : Devis complexe, Vision IA, Coach Business |
| **Claude Sonnet 4.6** | `claude-sonnet-4-6` | ~$3 / ~$15 | 5 agents standards : Supervisor, Compta, Fiscalité, RH, Site Web, + V2 Téléphone |
| **Claude Haiku 4.5** | `claude-haiku-4-5-20251001` | ~$1 / ~$5 | 6 agents légers : Relance, Planning, Réputation, Prospection, Email Pro, Déplacements |

**Règle répartition** : objectif 70% Haiku / 30% Sonnet / 5% Opus (après routage Supervisor).

### Autres APIs

| Service | Usage | Coût |
|---|---|---|
| OpenAI Whisper | STT vocal FR + multilingue | $0.006/min |
| ElevenLabs TTS Multilingual v2 | Réponses vocales cerveau IA | $0.12/1K chars |
| Claude Vision (via Claude API) | OCR tickets, analyse photos | Inclus dans Opus/Sonnet |

---

## 10. TRIAL, CONVERSION, ONBOARDING

| Clé | Valeur |
|---|---|
| **Trial Pro/Business** | **30 jours** (audit V6 — était 14 jours) |
| **Grâce period échec paiement** | **7 jours** |
| **Mode lecture seule** | Après 7 jours d'impayé |
| **Suspension complète** | Après 30 jours d'impayé |
| **Export RGPD post-résiliation** | 30 jours disponibles |
| **Type onboarding** | **Inversé (F125)** : landing → chat public → premier devis → signup pour ENVOYER |
| **Cible conversion free → paid** | **>25%** (audit V6, était >12%) |
| **Temps premier devis** | **<90 secondes** (cible onboarding inversé) |
| **Temps moyen création devis** | **<3 min** (cible perçue) |
| **Signups mois 1 cible** | 100+ |
| **Clients payants mois 1 cible** | 15-25 |
| **MRR fin mois 1 cible** | €400-800 |

---

## 11. MÉTRIQUES CIBLES

### Métriques produit

| Métrique | Cible V1 |
|---|---|
| Devis générés par user/semaine | 3-5 |
| Temps moyen création devis | <3 min |
| Premier devis (onboarding inversé) | <90 secondes |
| Taux d'envoi devis signé | >60% |
| Taux de réponse relance auto | +15% vs relance manuelle |
| Avis Google collectés par artisan | +30 en 3 mois |
| Résolution IA support (vs humain) | **>70%** (F136) |
| Temps moyen escalade → réponse humaine | **<2h** en journée |
| Churn mensuel | **<3%** (renforcé par warning F117) |
| NPS | >50 |

### Métriques business

| Métrique | Cible |
|---|---|
| ARPU | **€40/mois** |
| CAC (coût acquisition client) | <€50 |
| LTV (Lifetime Value) | >€800 (churn 3% = LTV ~33 mois) |
| Ratio LTV/CAC | >15× |
| MRR cible septembre 2026 | **€10 000 cumulé Nova challenge** |

### Kill criteria / Go criteria (12 semaines post-launch)

| Situation | Décision |
|---|---|
| < €200 MRR | Kill — documenter, open-source, Tool #3 |
| €200-500 MRR | Itérer — améliorer onboarding et rétention |
| > €500 MRR | Double down |
| > €2 000 MRR | All-in (déclenche négociation API grossistes V3) |

---

## 12. SPRINTS ET DURÉE BUILD

| Sprint | Nom | Durée |
|---|---|---|
| 0 | Setup infra (Fabrice) | 1 jour |
| 1 | Fondations — FastAPI skeleton, auth, 37 migrations SQL, RLS, Stripe 7 plans F130, Expo init, i18n, architecture multi-pays F135 | ~2 jours |
| 2 | Agents Devis + Relance + Imports concurrents (F112) | ~3 jours |
| 3 | Agents Compta + Vision IA + Détecteur concurrent F121 + Validation RGE F128 | ~3 jours |
| 4 | Agents Planning + Réputation + Prospection + scan carte fidélité grossistes F113-V1 | ~3 jours |
| 5 | Agents Email Pro + Fiscalité + PA facturation électronique | ~3 jours |
| 6 | Agents RH + Déplacements + Coach Business F119 + AI Act F131 + Pack contrôle fiscal F129 + Support hybride F136 | ~3 jours |
| 7 | Site Web + Gamification + Parrainage F114 + Rapport annuel F116 + Capsules F120 + Tâches employés F123-F124 | ~3 jours |
| 8 | Security audit + Sandbox F115 + Onboarding inversé F125 + Résilience F132 + Artisans étrangers F133 | ~3 jours |
| V1.5 | Mesure AR Android (post-launch) | — |
| V2 | Sept-oct 2026 : F122 + F126 + F127 + Agent Téléphone base | — |
| V3 | 2027+ : F109 + F113 V3 | — |

| Métrique | Valeur |
|---|---|
| **Durée build V1 totale** | **~25 jours Claude Code** (audit V6 — était 22 jours avant) |
| **Nombre de sprints V1** | **9** (Sprint 0 à 8) |
| **Total fichiers à créer (V1)** | **~320** |

---

## 13. DONNÉES MÉTIER BTP (CHIFFRES)

### TVA BTP (voir `docs/METIER.md` §1)

| Taux | Usage |
|---|---|
| **5,5%** | Rénovation énergétique, logement > 2 ans, artisan RGE obligatoire |
| **10%** | Travaux de rénovation, logement > 2 ans |
| **20%** | Construction neuve, locaux pro, fournitures client |

### 11 zones coefficients régionaux (voir `docs/METIER.md` §9)

| Zone | Coefficient |
|---|---|
| Paris intra-muros | 1,35 |
| Île-de-France (hors Paris) | 1,20 |
| Sud-Est (Lyon, Alpes-Rhône) | 1,10 |
| Méditerranée / PACA | 1,15 |
| DOM | 1,25 |
| Corse | 1,15 |
| Montagne (>800m) | 1,10 |
| Ouest | 1,00 (base) |
| Grand Est | 0,95 |
| Nord / Hauts-de-France | 0,95 |
| Sud-Ouest | 0,95 |

### Sanctions (voir `docs/METIER.md` §8)

| Infraction | Sanction |
|---|---|
| Devis absent avant travaux | 3 000€ (PP) / 15 000€ (société) |
| Mention décennale absente | 75 000€ + 6 mois emprisonnement |
| Facture non conforme | 75 000€ (PP) / 375 000€ (société) |
| E-invoicing non conforme | 50€/facture, plafonné 15 000€/an |
| E-reporting manquant | 500€/transmission, plafonné 15 000€/an |

### Taux intérêt légal S1 2026 (Arrêté 15/12/2025)

| Créancier | Taux |
|---|---|
| Particuliers | **6,67%** |
| Professionnels | **2,62%** |
| **Plancher pénalités B2B** (3× prof.) | **7,86%** |
| **Indemnité forfaitaire recouvrement** | **40€/facture** |

### Retenue de garantie

- **Taux** : 5% du montant TTC
- **Durée** : 1 an après réception des travaux
- **Base légale** : Loi n°71-584 du 16/07/1971

### Calendrier e-invoicing / e-reporting

| Date | Obligation |
|---|---|
| **1er septembre 2026** | Réception obligatoire factures électroniques (toutes entreprises) |
| **1er septembre 2026** | Émission obligatoire (grandes entreprises + ETI) |
| **1er septembre 2027** | Émission obligatoire (PME, TPE, micro-entreprises) |
| **1er septembre 2027** | E-reporting B2C pour TPE/PME |

### Factur-X

- Format : **PDF/A-3 + XML EN 16931**
- Profil STRUCTORAI : **EXTENDED avec extension CIUS Construction BTP**
- PA recommandée V1 : **FactPulse** (29€/mois, sandbox gratuit, intégration 2h)
- PA alternative : B2Brouter (gratuit jusqu'au 31/08/2026)

---

## 14. MULTI-PAYS ET LANGUES

### 6 langues supportées

| Langue | Code i18n | Priorité |
|---|---|---|
| Français | `fr` | 🔴 Primaire |
| Anglais | `en` | 🟡 International |
| Turc | `tr` | 🟢 Communauté FR |
| Portugais | `pt` | 🟢 Communauté FR |
| Arabe | `ar` | 🟢 Communauté FR |
| Espagnol | `es` | 🟢 Communauté FR |

### Plan expansion Europe (DISTRIBUTION.md)

| Pays | Effort | Spécificités |
|---|---|---|
| Belgique | 2 mois | Proximité FR, adaptations TVA BE |
| Suisse | 2 mois | FR + DE, franc suisse |
| Portugal | 3 mois | Langue déjà supportée (pt) |
| Espagne | 4 mois | FacturaE, langue déjà supportée (es) |
| Italie | 5 mois | SDI (facturation électronique) |
| Allemagne | 5 mois | XRechnung, traduction DE |
| Royaume-Uni | 5 mois | VAT, traduction complète |

**Architecture V1** : `country_code` + `edition` sur toutes les tables (Migration 037, F135).

---

## 15. BUDGET ONE-TIME ET RÉCURRENTS

### One-time (avant launch)

| Poste | Montant | Phase |
|---|---|---|
| Compte Google Play Android | **25$ one-time** | Phase build |
| Domaine structorai.app | **~15€/an** | Phase build |
| Revue juridique AI Act (cabinet Bensoussan/Caprioli) | **1 500€ one-time** | Avant launch (audit V7) |
| Coûts build API (tests Claude + Whisper + ElevenLabs) | **~60€** sur 2 mois | Phase build |
| **TOTAL BUILD** | **~1 600€** | 2 mois dev |

### Récurrents mensuels (phase launch, 1-50 clients)

| Poste | Coût |
|---|---|
| Railway Pro + usage | ~10-20$/mois |
| Supabase Pro (≥50 clients) | 25$/mois |
| Supabase read replica (≥50 clients, F132) | +25$/mois |
| Vercel | 0$ (Hobby OK <100GB) |
| Brevo (emails) | 0-9€ |
| ElevenLabs Starter | 5$/mois |
| Yousign Starter | 75€/mois (500 signatures/an) |
| Plateforme Agréée FactPulse | 29-100€/mois (ou gratuit B2Brouter jusqu'au 31/08/2026) |
| Crisp widget (support F136) | 0€ → 25€/mois dès 50 clients |
| Sentry Free | 0$ |
| UptimeRobot + Better Uptime | 0€ à 15€/mois |
| **TOTAL FIXE** | **~90-150€/mois** |

### Support externalisé (Freelance Malt/Upwork, à partir 50 clients)

| Stade | Coût |
|---|---|
| 0-50 clients | Fabrice seul, ~1h/jour |
| 50-200 clients | Freelance FR, **500-800€/mois** |
| 200-500 clients | Équipe 2 personnes, ~3K€/mois |
| 500+ | Équipe support dédiée |

---

## 16. RÉTROCOMPATIBILITÉ ET KILL CRITERIA

### Règles de cohérence

- **Aucun chiffre dans ce document ne peut être contredit** sans mise à jour + log dans `docs/DECISIONS-LOG.md` + `docs/CHANGELOG.md`
- **Les autres fichiers pointent vers celui-ci**, jamais l'inverse
- Toute feature ou migration ajoutée → **renuméroter les compteurs** ici d'abord
- En cas de conflit d'information entre deux fichiers → **ce fichier gagne**

### Kill criteria strict (post-launch 12 semaines)

- **< €200 MRR** → Kill projet, documenter apprentissages, passer à Tool #3 Nova
- **€200-500 MRR** → Itérer onboarding + rétention, ne pas ajouter de features
- **> €500 MRR** → Double down (features V2)
- **> €2 000 MRR** → All-in (déclenche négociation API grossistes V3 Point P/Cedeo)

---

## 17. HISTORIQUE DES AUDITS

| Date | Audit | Résumé |
|---|---|---|
| 14/04/2026 | **V5** — Cohérence | Valeurs de référence fixées : 47 mentions, 29 migrations, 11 zones, 110 features, 9 sprints, 22 jours, 13 agents |
| 17/04/2026 | **V6** — 22 améliorations stratégiques | 14ème agent Coach, 7 plans tarifaires, V1/V2/V3, +24 features (F112-F135), 37 migrations, $4.50→$4.80, marges 82%/91%→80%/89%, 22→25 jours |
| 17/04/2026 | **V7** — 5 renforcements (faiblesses→forces) | +F136 Support hybride, 1 500€ revue juridique AI Act, Coach "éclaireur", MemPalace souveraineté EU, marges ≥50 clients 78%/87% |
| — | **V8** (à venir) | Création docs priorité 1 (ce fichier, METIER, FEATURES_COMPLETE, MEMORY, COACH-DISCLAIMER, MEMORY-STRATEGY, AI-ACT-COMPLIANCE, SUPPORT-STRATEGY) |

### Règles de nommage des audits

- **V1-V4** : itérations internes avant formalisation
- **V5** : premier audit cohérence chiffré
- **V6** : intégration stratégique (22 améliorations)
- **V7** : transformation faiblesses → forces (5 renforcements)
- **V8** : création des 8 docs priorité 1 (en cours)

### Log des décisions par audit

Voir `docs/DECISIONS-LOG.md` pour détail chronologique.

---

## 18. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `CLAUDE.md` | Mission + 14 agents + règles métier (résumé) + pricing |
| `PRODUCT_CONTEXT.md` | Vision produit + couches cerveau IA + Moat + ICP |
| `BUILD_PLAN.md` | Plan d'exécution 320 fichiers 9 sprints |
| `FEATURES.md` | Liste F001-F136 détaillée |
| `README.md` | Entrée publique du repo |
| `COUT_REEL.md` | Calcul marges détaillé |
| `DISTRIBUTION.md` | Stratégie distribution + expansion Europe |
| `COMMUNICATION.md` | Arguments marketing + 5 forces |
| `STRATEGIE-COMPETITIVE.md` | Exploit faiblesses concurrents |
| `ANALYSE_CONCURRENCE.md` | 24 concurrents scannés |
| `UX_PARCOURS.md` | Parcours écran par écran |
| `SECURITE.md` | RGPD, AI Act, résilience, AI Act, souveraineté, RGE, contrôle fiscal |
| `FICHE_METIER.md` | Liste exhaustive fichiers data/ à préparer |
| `docs/METIER.md` | **Constitution BTP immuable** |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` | **Ce fichier** — table centrale des chiffres |
| `docs/FEATURES_COMPLETE.md` | Liste exhaustive F001-F136 (à créer) |
| `docs/MEMORY.md` | Décision mémoire Mem0 vs MemPalace vs hybride (à créer) |
| `docs/MEMORY-STRATEGY.md` | Architecture hybride + fallback (à créer) |
| `docs/COACH-DISCLAIMER.md` | Cadre juridique Coach Business (à créer) |
| `docs/AI-ACT-COMPLIANCE.md` | Conformité AI Act (à créer) |
| `docs/SUPPORT-STRATEGY.md` | Support hybride 3 niveaux (à créer) |
| `docs/CHANGELOG.md` | Log modifications fichiers repo |
| `docs/DECISIONS-LOG.md` | Log décisions par audit |

---

> **Ce fichier est vérifié et mis à jour à chaque audit.**
> **Prochaine mise à jour prévue** : après création des 6 autres docs priorité 1 (MEMORY, FEATURES_COMPLETE, COACH-DISCLAIMER, MEMORY-STRATEGY, AI-ACT-COMPLIANCE, SUPPORT-STRATEGY).
