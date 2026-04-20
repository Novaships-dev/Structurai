---
name: AI-ACT-COMPLIANCE
description: Conformité STRUCTORAI au Règlement européen AI Act (UE 2024/1689). Classification "système à risque limité avec supervision humaine". Analyse article par article des obligations applicables. Contenu page /transparency publique. Template attestation avocat. Procédure audit logs. Sanctions et mise en conformité avant le 2 août 2026.
type: legal-compliance
scope: all-agents, ui-mobile, ui-web, backend-logging, public-pages
priority: critical
date: 2026-04-17
version: 1.0
regulation: Règlement (UE) 2024/1689 du Parlement européen et du Conseil du 13 juin 2024 (AI Act)
deadline_enforcement: 2 août 2026 (Art. 50 transparence + Art. 26 déployeurs)
classification: Limited risk AI with human oversight (pas haut risque)
role_structorai: Deployer ET Provider (SaaS qui déploie son propre modèle composite basé sur GPAI Anthropic/OpenAI/ElevenLabs)
---

# docs/AI-ACT-COMPLIANCE.md — Conformité AI Act Européen

> **Ce fichier est la source de vérité conformité AI Act.**
> **Deadline d'enforcement : 2 août 2026** — STRUCTORAI doit être en conformité AVANT.
> **Sanctions possibles** : jusqu'à **€40M ou 7% CA mondial** (pratiques interdites), **€20M ou 4%** (transparence Art. 50), **€10M ou 2%** (autres obligations).
> **Argument différenciateur** : premier SaaS BTP FR labellisé AI Act compatible dès le jour 1.

---

## 0. SOMMAIRE

1. [Classification STRUCTORAI — risque limité + supervision humaine](#1-classification-structorai--risque-limité--supervision-humaine)
2. [Timeline AI Act et dates critiques](#2-timeline-ai-act-et-dates-critiques)
3. [Rôle STRUCTORAI — Deployer ET Provider](#3-rôle-structorai--deployer-et-provider)
4. [Analyse article par article des obligations applicables](#4-analyse-article-par-article-des-obligations-applicables)
5. [Exclusions pratiques interdites (Art. 5)](#5-exclusions-pratiques-interdites-art-5)
6. [Mesures de transparence implémentées (Art. 50)](#6-mesures-de-transparence-implémentées-art-50)
7. [Supervision humaine (Art. 14 et 26)](#7-supervision-humaine-art-14-et-26)
8. [Gouvernance des données et qualité (Art. 10)](#8-gouvernance-des-données-et-qualité-art-10)
9. [Audit logs (Migration 036)](#9-audit-logs-migration-036)
10. [Documentation technique des modèles utilisés](#10-documentation-technique-des-modèles-utilisés)
11. [Contenu page publique `/transparency`](#11-contenu-page-publique-transparency)
12. [Sanctions et plan de mise en conformité](#12-sanctions-et-plan-de-mise-en-conformité)
13. [Template attestation avocat](#13-template-attestation-avocat)
14. [Revue et maintenance](#14-revue-et-maintenance)

---

## 1. CLASSIFICATION STRUCTORAI — RISQUE LIMITÉ + SUPERVISION HUMAINE

### 1.1 — Les 4 niveaux de risque AI Act

| Niveau | Exemples | Obligations | STRUCTORAI ? |
|---|---|---|---|
| **Inacceptable** (Art. 5) | Social scoring, manipulation subliminale, biométrie temps réel par autorités, catégorisation sensible | **Interdit totalement** | ❌ NON (aucune pratique interdite) |
| **Haut risque** (Art. 6 + Annexe III) | Recrutement auto, scoring crédit, infrastructure critique, éducation | Évaluation conformité, enregistrement base EU, supervision humaine stricte, audit log 6 mois min, documentation technique exhaustive | ❌ NON (voir justification §1.3) |
| **Risque limité** (Art. 50) | Chatbots, deepfakes, génération contenu synthétique | **Obligations de transparence** : informer les utilisateurs qu'ils interagissent avec une IA, marquer contenu synthétique | ✅ **STRUCTORAI est ici** |
| **Risque minimal/nul** | Spam filters, jeux vidéo avec IA, inventaire | Aucune obligation spécifique | — |

### 1.2 — Classification STRUCTORAI : "Système IA à usage limité avec supervision humaine"

**Classification officielle** : **Limited risk AI system with human oversight**

**Justification complète :**

1. **STRUCTORAI est un outil d'assistance à un professionnel (l'artisan)**, pas un système qui prend des décisions à sa place sur des sujets à impact fondamental (emploi, crédit, santé, éducation)

2. **Supervision humaine systématique** :
   - Toute décision financière (devis, facture, relance, publication) passe par un bouton "Valider" de l'artisan
   - L'artisan garde le contrôle à 100% sur chaque action
   - Le cerveau propose, l'artisan dispose
   - Un bouton "Demander validation humaine" est accessible partout

3. **Dossier "À faire" centralisé** : file d'attente de validation de toutes les actions automatiques avant exécution

4. **Score de confiance affiché** 🟢🟡🔴 sur chaque output pour informer l'artisan du niveau de fiabilité

### 1.3 — Pourquoi STRUCTORAI n'est PAS haut risque (Art. 6 + Annexe III)

L'Annexe III liste 8 domaines "haut risque". STRUCTORAI **ne rentre dans AUCUN** :

| Domaine Annexe III | STRUCTORAI concerné ? |
|---|---|
| **Biométrie** (identification, catégorisation) | ❌ Non |
| **Infrastructure critique** (transport, eau, électricité, gaz) | ❌ Non |
| **Éducation et formation professionnelle** (admission, évaluation) | ❌ Non |
| **Emploi et gestion RH** (recrutement, promotion, licenciement) | ❌ Non — l'Agent RH gère pointage/paye, PAS de décision d'embauche/licenciement |
| **Accès services essentiels privés/publics** (crédit, assurance, aides sociales) | ❌ Non — pas de scoring solvabilité ni de décision crédit |
| **Application de la loi** (police, justice) | ❌ Non |
| **Migration, asile, contrôles frontières** | ❌ Non |
| **Administration justice et processus démocratiques** | ❌ Non |

**Confirmation** : STRUCTORAI n'est PAS un système à haut risque. **Seul l'Art. 50 (transparence) s'applique**, plus les principes généraux.

### 1.4 — Documentation de cette classification

Selon **Art. 6(3)** et **Art. 80** : les providers qui estiment que leur système n'est PAS haut risque (alors qu'il pourrait rentrer dans Annexe III) DOIVENT documenter leur évaluation avant mise sur le marché.

**Documentation STRUCTORAI** (à conserver 10 ans) :
- Ce fichier `docs/AI-ACT-COMPLIANCE.md` (signé Fabrice + date)
- Attestation avocat (voir §13)
- Grille d'évaluation Annexe III par domaine (voir §1.3)
- Preuve de supervision humaine systématique (captures UI, exports audit log)

---

## 2. TIMELINE AI ACT ET DATES CRITIQUES

### 2.1 — Calendrier officiel

| Date | Obligations qui entrent en vigueur |
|---|---|
| **2 août 2024** | Entrée en vigueur du règlement (UE) 2024/1689 |
| **2 février 2025** | Pratiques interdites (Art. 5) + AI literacy (Art. 4) |
| **2 août 2025** | Règles GPAI (General-Purpose AI Models) pour les providers de modèles fondamentaux |
| **2 août 2026** 🔴 | **Transparence (Art. 50) + obligations déployeurs (Art. 26) + sanctions activables** |
| **2 août 2027** | Obligations haut risque dans Annexe III (peut-être décalé à déc. 2027 via "Digital Omnibus") |

### 2.2 — Deadline STRUCTORAI

**🔴 DEADLINE ABSOLUE : 2 août 2026**

Le launch STRUCTORAI est prévu **juin 2026** → nous avons ~2 mois entre launch et deadline AI Act. Toute la conformité DOIT être opérationnelle AVANT le launch juin 2026.

**Checklist à valider avant launch** :

- [x] Classification formelle documentée (ce fichier)
- [ ] Page `/transparency` publique en ligne
- [ ] Badge "Décision IA" implémenté dans l'UI mobile + web
- [ ] Bouton "Demander validation humaine" accessible partout
- [ ] Migration `036_create_ai_audit_log.sql` déployée
- [ ] Logs audit retenus **≥ 6 mois minimum** (bonne pratique : 12 mois)
- [ ] Revue juridique avocat signée (1 500€)
- [ ] CGU + CGV mises à jour avec clauses AI Act
- [ ] Process de signalement incident documenté

### 2.3 — Digital Omnibus (incertitude)

La Commission européenne a proposé fin 2025 un "Digital Omnibus" qui pourrait **décaler** certaines obligations haut risque à décembre 2027. **STRUCTORAI NE PARIE PAS** sur ce décalage : on se conforme pour le 2 août 2026 dans tous les cas.

---

## 3. RÔLE STRUCTORAI — DEPLOYER ET PROVIDER

L'AI Act distingue 2 rôles principaux. STRUCTORAI est **les deux** :

### 3.1 — STRUCTORAI en tant que Deployer (Art. 26)

**Définition** : entité qui utilise un système IA dans un contexte professionnel.

**STRUCTORAI est Deployer** des modèles suivants :
- Claude Opus 4.7, Sonnet 4.6, Haiku 4.5 (Anthropic — GPAI)
- Whisper API (OpenAI — GPAI)
- ElevenLabs Multilingual v2 (GPAI)

**Obligations Deployer applicables** :
- **Art. 26(1)** : utiliser selon les instructions du provider
- **Art. 26(2)** : supervision humaine par personnes compétentes et formées (Fabrice + équipe support)
- **Art. 26(4)** : pertinence des données d'entrée (garbage in = garbage out)
- **Art. 26(5)** : monitoring continu, signalement incidents graves au provider + AI Office
- **Art. 26(6)** : **conservation logs minimum 6 mois**

### 3.2 — STRUCTORAI en tant que Provider (Art. 16)

**Définition** : entité qui met un système IA sur le marché sous son propre nom/marque.

**STRUCTORAI est Provider** du système composite "STRUCTORAI AI Brain" qui :
- Agrège Claude + Whisper + ElevenLabs en un système intégré
- Est mis sur le marché sous la marque STRUCTORAI
- Ajoute ses propres prompts système (les 14 agents), son RAG BTP, sa mémoire hybride

**Obligations Provider applicables pour risque limité** :
- **Art. 50** : transparence — les utilisateurs doivent savoir qu'ils interagissent avec une IA
- **Art. 50(2)** : marquer le contenu synthétique généré comme tel (pas obligatoire pour STRUCTORAI car pas de deepfake/synthetic content génératif publié)

### 3.3 — Implications pratiques

Cette double casquette impose une **double documentation** :
1. Documentation Deployer (instructions d'usage des modèles GPAI, supervision, logs)
2. Documentation Provider (transparence utilisateur, informations sur le système composite)

---

## 4. ANALYSE ARTICLE PAR ARTICLE DES OBLIGATIONS APPLICABLES

### 4.1 — Articles applicables à STRUCTORAI

| Article | Titre | Applicable ? | Action requise |
|---|---|---|---|
| **Art. 3** | Définitions | ✅ Référence | Utiliser les définitions officielles |
| **Art. 4** | AI literacy | ✅ Oui | Former Fabrice + équipe support sur l'IA (capacités, limites, risques) |
| **Art. 5** | Pratiques interdites | ✅ Vérification | Confirmer qu'aucune pratique interdite n'est utilisée (voir §5) |
| **Art. 6** | Classification haut risque | ✅ Référence | Documenter pourquoi STRUCTORAI n'est PAS haut risque |
| **Art. 10** | Gouvernance données | ⚠️ Partiel | Qualité données RAG, data d'entraînement prompts |
| **Art. 13** | Transparence vers déployeurs | ⚠️ Partiel | Si STRUCTORAI est utilisé via API par des tiers, docs techniques |
| **Art. 14** | Supervision humaine | ✅ Oui | Bouton "Valider", dossier "À faire" |
| **Art. 26** | Obligations Deployer | ✅ Oui | Instructions usage, supervision, monitoring, logs 6 mois |
| **Art. 50** | Transparence (risque limité) | ✅ **CRITIQUE** | Informer utilisateur qu'il interagit avec IA, badges, page /transparency |
| **Art. 53** | Docs techniques GPAI | ❌ Non (STRUCTORAI n'est pas un provider GPAI) | — |
| **Art. 55** | Support SMEs / startups | ✅ Bénéfice | STRUCTORAI = TPE/startup → bénéficie des mesures d'accompagnement |
| **Art. 72** | Post-market monitoring | ✅ Oui | Monitoring continu du comportement du système |
| **Art. 73** | Signalement incidents graves | ✅ Oui | Procédure de signalement à l'AI Office sous 72h |
| **Art. 80** | Classement non-haut-risque | ✅ Oui | Documentation obligatoire de l'évaluation (ce fichier) |

### 4.2 — Art. 50 (le plus important pour STRUCTORAI)

> *"Les fournisseurs veillent à ce que les systèmes d'IA destinés à interagir directement avec les personnes physiques soient conçus et développés de manière à ce que les personnes physiques concernées soient informées du fait qu'elles interagissent avec un système d'IA..."*

**Actions STRUCTORAI pour se conformer à Art. 50** :

1. **Badge "IA" visible** sur chaque message envoyé par le cerveau (chat, vocal, WhatsApp)
   - Texte : "🤖 Message généré par l'IA STRUCTORAI"
   - Visible dès le premier message

2. **Page d'accueil transparente** sur la nature IA de l'outil
   - Homepage landing : "STRUCTORAI est un assistant IA pour artisans BTP"

3. **Transparence vocale** : la voix IA annonce explicitement son statut au premier appel
   - Script type : *"Bonjour, vous êtes bien chez [artisan]. Je suis son assistant IA, je peux prendre votre demande."*

4. **CGU mentionnent explicitement** : "Le service est opéré par un système d'intelligence artificielle supervisé"

5. **Page `/transparency`** publique détaillée (voir §11)

### 4.3 — Art. 14 + 26 (supervision humaine)

STRUCTORAI implémente **3 niveaux de supervision humaine** :

**Niveau 1 — Supervision artisan (utilisateur final)**
- Bouton "Valider" sur CHAQUE action automatique (devis, relance, facture, post social)
- Dossier "À faire" centralisé
- Bouton "Demander validation humaine" partout (appelle l'Agent Support Humain F136)

**Niveau 2 — Supervision STRUCTORAI (Fabrice + support)**
- Monitoring des audit logs `ai_decisions`
- Détection d'anomalies (taux erreur, drift, abus)
- Escalade humaine via widget Crisp (F136)
- Formation AI literacy de l'équipe support

**Niveau 3 — Supervision modèles GPAI (Anthropic, OpenAI, ElevenLabs)**
- Les providers de modèles GPAI appliquent leurs propres safeguards
- Anthropic Claude : safety training, constitutional AI
- OpenAI Whisper : guardrails sur transcription
- DPA signés avec chacun

---

## 5. EXCLUSIONS PRATIQUES INTERDITES (ART. 5)

STRUCTORAI N'UTILISE **AUCUNE** des pratiques interdites par l'Art. 5 :

| Pratique interdite (Art. 5) | STRUCTORAI concerné ? |
|---|---|
| Manipulation subliminale ou techniques trompeuses | ❌ Non — transparence totale sur nature IA |
| Exploitation de vulnérabilités (âge, handicap, situation socio-éco) | ❌ Non — pas de ciblage vulnérabilités |
| Catégorisation biométrique sur attributs sensibles (race, opinions, etc.) | ❌ Non — pas de biométrie |
| Social scoring (évaluation/classification sociale) | ❌ Non — pas de scoring personnes |
| Évaluation risque criminel (sans faits objectifs) | ❌ Non — pas de prédiction criminelle |
| Scraping images faciales pour bases de données reconnaissance | ❌ Non |
| Reconnaissance émotions au travail ou école | ❌ Non — pas d'analyse émotions |
| Identification biométrique temps réel par autorités | ❌ Non — pas d'autorité publique |

**Garanties** :
- Le system prompt de CHAQUE agent contient des règles interdisant ces pratiques
- Documentation conservée pour preuve en cas de contrôle

---

## 6. MESURES DE TRANSPARENCE IMPLÉMENTÉES (ART. 50)

### 6.1 — Badge "Décision IA"

**Composant UI** : `components/AIDecisionBadge.tsx`

**Où il s'affiche** :
- Sur chaque message du cerveau dans le chat
- Sur chaque notification push
- Sur chaque WhatsApp envoyé par le cerveau
- Dans les emails auto (relances, avis Google)
- En footer de chaque PDF généré (devis, facture, analyse Coach)

**Texte du badge** (mobile compact) :
```
🤖 IA STRUCTORAI
```

**Texte version longue** (page PDF, email) :
```
Ce message/document a été généré par l'assistant IA STRUCTORAI 
(système IA à risque limité avec supervision humaine). Votre 
artisan valide chaque envoi. Transparence : structorai.app/transparency
```

### 6.2 — Bouton "Demander validation humaine"

**Composant UI** : `components/RequestHumanValidationButton.tsx`

**Comportement** :
- Toujours présent en bas de l'écran chat (petite icône 👤)
- Sur les analyses Coach (plus proéminent, à côté du disclaimer)
- Sur les alertes fiscales critiques
- Cliquer = ouvre ticket support F136 avec contexte de la conversation

### 6.3 — Transparence vocale

**Script obligatoire au premier appel de l'Agent Téléphone V2** (avant toute prise d'info) :

> *"Bonjour, vous êtes bien chez [Nom Entreprise]. [Prénom] est actuellement indisponible. **Je suis son assistant IA**, je peux prendre votre demande ou votre message et il vous rappellera dans l'heure. Vous souhaitez continuer ?"*

**Si le prospect dit non** → bascule messagerie classique
**Si oui** → recueil info comme prévu, avec rappel à la fin : *"Votre demande a été transmise à [Prénom] par son assistant IA."*

### 6.4 — Information explicite onboarding

Au premier signup/connexion STRUCTORAI :

```
┌───────────────────────────────────────────────┐
│ 👋 Bienvenue sur STRUCTORAI                   │
│                                               │
│ STRUCTORAI est ton assistant IA dédié au BTP. │
│ Il propose, tu valides. Tu gardes toujours    │
│ le contrôle.                                  │
│                                               │
│ [En savoir plus]  [Commencer]                │
└───────────────────────────────────────────────┘
```

### 6.5 — Marquage contenu synthétique (Art. 50.2)

STRUCTORAI ne publie pas de deepfakes ni de contenu synthétique destiné à tromper. Toutefois, pour les outputs générés :

- **Posts réseaux sociaux générés par IA** : mention "Publication suggérée par IA, validée par [artisan]" dans les métadonnées (pas visible aux visiteurs mais dans les logs)
- **Site vitrine généré par IA (Agent Site Web F74)** : footer discret "Site créé avec STRUCTORAI — IA assistance"
- **Textes devis** : pas de marquage (les devis sont des documents commerciaux classiques que l'artisan valide et signe)

---

## 7. SUPERVISION HUMAINE (ART. 14 ET 26)

### 7.1 — Supervision systématique par l'artisan (Deployer)

**Principe** : aucune action qui affecte un client externe de l'artisan n'est déclenchée sans validation humaine de l'artisan.

**Actions qui REQUIÈRENT validation artisan (bouton "Valider")** :
- Envoi devis à un client
- Envoi facture à un client
- Envoi relance (SMS, email)
- Mise en demeure
- Publication réseaux sociaux
- Réponse à avis Google
- Demande d'avis post-chantier (SMS)
- Mise à jour du site vitrine IA
- Déclaration URSSAF / TVA (après vérification Agent Fiscalité)

**Actions qui NE REQUIÈRENT PAS validation (automatiques internes)** :
- Stockage tickets OCR en base
- Catégorisation automatique documents
- Calculs de marge internes
- Enrichissement Mem0 (patterns artisan)
- Monitoring santé système

### 7.2 — Bouton "Valider" universel

**Composant UI** : `components/ValidateActionBottomSheet.tsx`

Chaque action qui va vers l'extérieur passe par une bottom sheet :
```
┌────────────────────────────────────────────┐
│ ✋ Action à valider                         │
│                                            │
│ [Résumé de l'action en 1 ligne]           │
│                                            │
│ Exemple : Envoi devis DEV-2026-042        │
│ à Martin Dupont par email                 │
│                                            │
│ [Voir détail]                             │
│                                            │
│ [❌ Annuler]  [✅ Valider et envoyer]     │
└────────────────────────────────────────────┘
```

### 7.3 — Dossier "À faire" centralisé

**Module fonctionnel** (un des 4 modules non-agents) qui liste :
- Toutes les actions proposées par les agents en attente de validation
- Triées par priorité : urgentes / aujourd'hui / cette semaine
- Actions groupables (valider 3 relances en batch)
- Historique des validations / refus

**Migration SQL** : table `tasks_to_validate` dans migration existante 016.

### 7.4 — Formation AI literacy (Art. 4)

Obligation : Fabrice + équipe support DOIVENT être formés à l'IA.

**Programme de formation AI literacy STRUCTORAI** :
- Module 1 : Fondamentaux de l'IA générative (Claude, Whisper, ElevenLabs)
- Module 2 : Limites et hallucinations (comment les détecter)
- Module 3 : Bias et fairness dans les outputs
- Module 4 : Cas d'usage et contre-indications
- Module 5 : Réglementation (AI Act, RGPD)

**Attestation** : chaque membre support signe une attestation AI literacy conservée dans les RH.

---

## 8. GOUVERNANCE DES DONNÉES ET QUALITÉ (ART. 10)

### 8.1 — Sources de données utilisées

STRUCTORAI utilise les données suivantes pour ses outputs :

| Source | Contenu | Qualité / vérification |
|---|---|---|
| `docs/METIER.md` | Règles BTP (TVA, mentions, Factur-X, sanctions) | 🟢 Source officielle (Légifrance, CGI, CCH) |
| `data/legal/*.json` | Mentions obligatoires, taux TVA, pénalités | 🟢 Source officielle |
| `data/prix/*.json` (11 référentiels métier) | Prix marché par corps de métier | 🟡 Source : terrain + CAPEB/FFB, mise à jour annuelle |
| `data/benchmarks/taux_horaires_par_metier_region.json` | Benchmarks Coach (96 dép × 11 métiers = 1 056 entrées) | 🟡 Source : Travaux.com, TarifArtisan.fr, CAPEB, FFB, 2025-2026 |
| Mem0 artisan | Prix perso, patterns clients | 🟢 Source vérifiée (input artisan direct) |
| MemPalace | Verbatim conversations | 🟢 Source directe artisan |
| Anthropic Claude | Raisonnement général | 🟢 Fournisseur GPAI certifié, DPA signé |
| OpenAI Whisper | Transcription vocale | 🟢 Fournisseur GPAI certifié, DPA signé |

### 8.2 — Mesures qualité

- **Score de confiance** obligatoire sur chaque output (🟢🟡🔴) — voir `docs/METIER.md` §10
- **Règle zéro invention** : le cerveau ne dit jamais de connerie en silence
- **Vérification croisée** des prix (Mem0 vs référentiel vs web) avant output
- **Veille réglementaire automatique** : scan Légifrance + FFB + CAPEB pour détecter évolutions

### 8.3 — Biais et fairness

**Analyses régulières pour détecter biais potentiels** :
- Biais géographique : les coefficients régionaux sont-ils équilibrés ?
- Biais genre : le cerveau traite-t-il différemment les clientes femmes ?
- Biais culturel : le cerveau fonctionne-t-il équitablement en TR / AR / PT / ES ?
- Biais économique : les artisans débutants reçoivent-ils les mêmes conseils que les confirmés ?

**Tests obligatoires post-launch** : échantillon de 100 interactions par mois, analyse qualitative des outputs.

---

## 9. AUDIT LOGS (MIGRATION 036)

### 9.1 — Table `ai_decisions`

Migration `036_create_ai_audit_log.sql` crée la table suivante :

```sql
CREATE TABLE ai_decisions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id),
    user_id UUID NOT NULL REFERENCES users(id),
    agent_name TEXT NOT NULL,
    model_used TEXT NOT NULL,
    model_version TEXT NOT NULL,
    input_summary TEXT,
    output_summary TEXT,
    confidence_score TEXT CHECK (confidence_score IN ('high', 'medium', 'low')),
    human_validated BOOLEAN DEFAULT FALSE,
    disclaimer_shown BOOLEAN DEFAULT FALSE,
    user_acknowledged BOOLEAN DEFAULT FALSE,
    redirect_to_professional TEXT,
    action_type TEXT,
    action_result TEXT,
    error_code TEXT,
    latency_ms INTEGER,
    tokens_input INTEGER,
    tokens_output INTEGER,
    cost_usd DECIMAL(10, 6),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    retained_until TIMESTAMP WITH TIME ZONE DEFAULT (NOW() + INTERVAL '12 months')
);

-- RLS policies
ALTER TABLE ai_decisions ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users see own org decisions" ON ai_decisions
    FOR SELECT USING (organization_id = auth.jwt() ->> 'org_id');

CREATE POLICY "System can insert" ON ai_decisions
    FOR INSERT WITH CHECK (TRUE);

-- Index pour recherche rapide
CREATE INDEX idx_ai_decisions_org_created ON ai_decisions(organization_id, created_at DESC);
CREATE INDEX idx_ai_decisions_agent ON ai_decisions(agent_name, created_at DESC);
```

### 9.2 — Rétention 12 mois

**Exigence AI Act** : 6 mois minimum (Art. 26.6)
**Pratique STRUCTORAI** : **12 mois** (plus strict que l'exigence)

**Raisons** :
- Audits réguliers sur historique 12 mois
- Réponse en cas de contrôle CNIL ou AI Office
- Analyse long terme des patterns et dérives

**Automatisation purge** : job cron quotidien supprime les entrées avec `retained_until < NOW()`.

### 9.3 — Ce qui est loggé

À **CHAQUE appel LLM** (n'importe quel agent) :
- Qui : organization_id + user_id + agent_name
- Quoi (modèle) : model_used + version exacte
- Entrée : input_summary (résumé anonymisé si données sensibles)
- Sortie : output_summary (résumé)
- Performance : latency_ms, tokens_input, tokens_output, cost_usd
- Supervision : human_validated, disclaimer_shown, user_acknowledged
- Contexte : action_type (devis/relance/coach/etc.), redirect_to_professional si Coach
- Score : confidence_score (high/medium/low)

### 9.4 — Export pour AI Office ou CNIL

En cas de demande d'un régulateur :
- **Endpoint admin** : `GET /admin/ai-decisions/export?start=...&end=...`
- **Format** : JSON + CSV signé horodaté
- **Délai réponse** : < 48h (SLA interne pour répondre aux autorités)

---

## 10. DOCUMENTATION TECHNIQUE DES MODÈLES UTILISÉS

### 10.1 — Modèles GPAI utilisés (au 17/04/2026)

| Modèle | Provider | Version exacte | Usage | Justification choix |
|---|---|---|---|---|
| **Claude Opus 4.7** | Anthropic | `claude-opus-4-7` | Agent Devis complexe, Agent Vision IA stratégique, Agent Coach Business | Raisonnement complexe, chain-of-thought, contexte BTP sophistiqué |
| **Claude Sonnet 4.6** | Anthropic | `claude-sonnet-4-6` | Supervisor, 5 agents standards (Compta, Fiscalité, RH, Site Web, V2 Téléphone) | Équilibre qualité/coût |
| **Claude Haiku 4.5** | Anthropic | `claude-haiku-4-5-20251001` | 6 agents légers (Relance, Planning, Réputation, Prospection, Email Pro, Déplacements) | Rapidité + coût faible sur tâches simples |
| **Whisper API** | OpenAI | Dernière version API | Transcription vocale 6 langues (FR/EN/TR/PT/AR/ES) | Meilleur STT multilingue, robuste au bruit chantier |
| **ElevenLabs Multilingual v2** | ElevenLabs | Voix configurables | TTS réponses cerveau | Qualité voix naturelle |

### 10.2 — Limitations connues

**Claude (Anthropic)** :
- Knowledge cutoff : données d'entraînement jusqu'à une date précise (varie selon version)
- Peut halluciner sur données postérieures au cutoff
- Qualité variable selon langue (excellent en FR/EN, bon en ES/PT, correct en TR/AR)

**Whisper** :
- Qualité dégradée avec bruit de chantier > 80dB (disqueuse, perceuse)
- Mots techniques BTP parfois mal transcrits (ex: "VMC", "Placo", références produits spécifiques)
- Latence ~1-2s sur vocaux courts, 3-5s sur vocaux >1min

**ElevenLabs** :
- Voix peuvent sembler "trop parfaites" → risque d'inconfort chez certains artisans
- Prononciation chiffres et abbréviations à vérifier cas par cas

### 10.3 — DPA (Data Processing Agreement)

STRUCTORAI a signé des DPA avec :
- **Anthropic** : DPA GDPR en ligne, engagement région EU pour API calls
- **OpenAI** : DPA GDPR + commitment pas d'entraînement sur data API
- **ElevenLabs** : DPA GDPR + commitment pas de stockage voix

**Conservation des DPA** : dossier juridique `/legal/dpa/` + copie chez l'avocat.

---

## 11. CONTENU PAGE PUBLIQUE `/TRANSPARENCY`

### 11.1 — URL et accessibilité

**URL** : `https://structorai.app/transparency`

**Accessibilité** :
- Pas de login requis
- Lien dans le footer du site principal
- Lien dans les CGU
- Lien dans chaque email du cerveau IA
- Sitemap.xml pour indexation SEO

### 11.2 — Structure de la page

```markdown
# Transparence IA STRUCTORAI

Cette page respecte l'article 50 du règlement européen AI Act 
(UE 2024/1689). Dernière mise à jour : [DATE].

## Qu'est-ce que STRUCTORAI ?

STRUCTORAI est un assistant IA pour artisans BTP. Il aide à la 
gestion administrative (devis, factures, relances), au suivi 
de chantiers, à la comptabilité, à la prospection et à la 
réputation. Chaque artisan reste le décideur final — STRUCTORAI 
propose, l'artisan valide.

## Classification AI Act

STRUCTORAI est classé "système IA à risque limité avec supervision 
humaine" au sens de l'AI Act européen. Cela signifie :

- ✅ Aucune pratique interdite (pas de manipulation, pas de social 
  scoring, pas de biométrie)
- ✅ Pas de haut risque (pas de scoring crédit, pas de décision 
  d'embauche automatisée, pas d'infrastructure critique)
- ✅ Transparence obligatoire (cette page)
- ✅ Supervision humaine systématique de chaque action

## Les modèles d'IA utilisés

STRUCTORAI agrège plusieurs modèles d'IA spécialisés (GPAI — 
General Purpose AI) :

**Pour le raisonnement et la compréhension du BTP**
- Claude Opus 4.7 (Anthropic) — raisonnement complexe sur les devis, 
  analyse des photos de chantier, conseil stratégique mensuel
- Claude Sonnet 4.6 (Anthropic) — orchestration des agents, 
  comptabilité, fiscalité, gestion RH, génération de site vitrine
- Claude Haiku 4.5 (Anthropic) — tâches rapides (relances, 
  planning, réputation, prospection, emails, déplacements)

**Pour la compréhension vocale**
- Whisper API (OpenAI) — transcription en 6 langues 
  (FR/EN/TR/PT/AR/ES)

**Pour la réponse vocale**
- ElevenLabs Multilingual v2 — synthèse vocale naturelle

## Les 14 agents IA de STRUCTORAI

1. Agent Devis — génération devis vocal, calcul TVA, mentions
2. Agent Relance — devis J+3, factures J+15/30/45 avec ton adaptatif
3. Agent Comptabilité — OCR tickets, catégorisation, marge
4. Agent Planning — timer chantier, capacité, enchaînements
5. Agent Réputation & Marketing — avis Google, SEO, réseaux sociaux
6. Agent Prospection — CRM archi/apporteurs, leads
7. Agent Email Pro — filtrage IMAP, résumé quotidien
8. Agent Fiscalité & Trésorerie — calendrier fiscal, alertes
9. Agent Déplacements — frais km, paniers, indemnités BTP
10. Agent RH — pointage, heures sup, CIBTP, export paie
11. Agent Vision IA — analyse photos chantier et documents
12. Agent Site Web — génération vitrine IA
13. Agent Coach Business — conseil stratégique mensuel 
    (voir disclaimer spécifique Coach)
14. Agent Supervisor — orchestration et Background Consciousness

## Supervision humaine

Chaque action automatique importante (envoi devis, relance, 
publication réseaux sociaux, réponse avis Google) passe par 
une validation explicite de l'artisan via un bouton "Valider". 
STRUCTORAI ne peut jamais publier ou envoyer quoi que ce soit 
sans cette validation.

Un bouton "Demander validation humaine" est accessible partout 
dans l'application : il permet à l'artisan de déclencher une 
intervention de l'équipe support humaine de STRUCTORAI en 
moins de 2h (24/7 : 1h en urgence).

## Tes droits (RGPD + AI Act)

- **Droit à l'information** : tu sais que tu interagis avec une 
  IA, marqué sur chaque message.
- **Droit d'accès** : export complet de tes données en 1 clic 
  dans Settings.
- **Droit à l'effacement** : suppression définitive par email à 
  privacy@structorai.app (rétention 30 jours puis purge).
- **Droit de rectification** : modification des données inexactes.
- **Droit d'opposition à un traitement automatisé** : activation 
  du "mode validation manuelle" qui désactive la plupart des 
  automatismes.
- **Droit d'explication** : pour chaque décision IA importante 
  tu peux demander "pourquoi cette recommandation ?"
- **Droit de porter plainte** : CNIL (cnil.fr) pour RGPD, AI 
  Office EU pour AI Act.

## Où tes données sont-elles stockées ?

Tes données sont hébergées en Europe :
- Base de données : Supabase région Frankfurt (Allemagne)
- Backend : Railway région EU
- Frontend : Vercel région EU
- Mémoire Mem0 : cloud EU
- Mémoire MemPalace : 100% local sur nos serveurs EU

Seuls les appels API ponctuels aux modèles d'IA (Anthropic, 
OpenAI, ElevenLabs) transitent hors EU, avec Data Processing 
Agreements (DPA) RGPD signés avec chaque provider.

## Signalement d'incident

Si tu observes un comportement anormal de l'IA STRUCTORAI, 
contacte-nous :
- Par email : dpo@structorai.app
- Via l'application : Settings → Signaler un incident IA
- Urgence : 06.XX.XX.XX.XX (ligne dédiée)

## Audit et traçabilité

Chaque décision IA importante (devis généré, relance envoyée, 
analyse Coach, etc.) est loguée dans notre système d'audit 
interne avec les informations suivantes : modèle utilisé, 
date/heure, score de confiance, validation humaine. Ces logs 
sont conservés 12 mois (exigence AI Act : 6 mois minimum).

## Conformité certifiée

STRUCTORAI a été validé par un cabinet d'avocats spécialisé 
en IA avant son lancement : [Nom cabinet]. Attestation de 
conformité disponible sur demande à dpo@structorai.app.

---

**Contact**
- Questions IA / transparence : dpo@structorai.app
- Support : support@structorai.app
- AI Office EU : https://digital-strategy.ec.europa.eu/fr/policies/ai-office
- CNIL (RGPD France) : cnil.fr
```

### 11.3 — Implémentation technique

**Fichier** : `frontend/pages/transparency.tsx` (Next.js)

**Caractéristiques** :
- SSG (static site generation) pour vitesse
- Responsive mobile
- Lien sitemap.xml
- Version imprimable PDF disponible en bas de page

---

## 12. SANCTIONS ET PLAN DE MISE EN CONFORMITÉ

### 12.1 — Sanctions possibles

| Type de violation | Sanction maximale |
|---|---|
| **Pratiques interdites Art. 5** (par ex: social scoring) | **€40 millions ou 7% du CA mondial** (le plus élevé) |
| **Non-conformité transparence Art. 50** | **€20 millions ou 4% du CA mondial** |
| **Autres obligations** (gouvernance données, supervision, etc.) | **€10 millions ou 2% du CA mondial** |
| **Fourniture informations incorrectes aux autorités** | **€7,5 millions ou 1,5%** |

### 12.2 — Spécificités SMEs / startups

**Art. 55** : les PME et startups bénéficient de :
- Sanctions proportionnelles à la taille de l'entreprise (pas de plafonds absurdes)
- Accès prioritaire aux regulatory sandboxes (à partir de 2028)
- Accompagnement AI Office

**STRUCTORAI en tant que startup** :
- Bénéfice des mesures d'accompagnement
- Mais pas d'exemption aux obligations : conformité obligatoire comme tout le monde

### 12.3 — Plan de mise en conformité STRUCTORAI

#### Avant launch (juin 2026)

- [x] Ce document créé et validé par Fabrice
- [ ] Migration 036 `ai_decisions` déployée
- [ ] UI badges + boutons implémentés (Sprint 6)
- [ ] Page `/transparency` publiée (Sprint 6)
- [ ] Formation AI literacy Fabrice + équipe support
- [ ] Rédaction CGU + CGV avec clauses AI Act
- [ ] Revue juridique avocat (1 500€)

#### Post-launch avant 2 août 2026

- [ ] Audit interne conformité (checklist ci-dessus)
- [ ] Documentation technique modèles finalisée
- [ ] Procédure signalement incident testée
- [ ] SLA réponse régulateurs < 48h validé
- [ ] Première analyse biais publiée

#### Trimestriel post-enforcement

- [ ] Revue conformité (ce document relu et mis à jour)
- [ ] Audit logs vérifiés (rétention 12 mois OK)
- [ ] Nouveaux modèles ajoutés documentés immédiatement
- [ ] Incidents graves signalés à l'AI Office < 72h si applicable

---

## 13. TEMPLATE ATTESTATION AVOCAT

À fournir au cabinet d'avocats (Bensoussan, Caprioli, Avosial, etc.) pour la revue juridique pré-launch (1 500€ budget).

### 13.1 — Livrables attendus du cabinet

1. **Audit de classification AI Act** : validation que STRUCTORAI est bien "risque limité" et non "haut risque"
2. **Revue du positionnement Agent Coach Business** : validation NAF 70.22Z non réglementé
3. **Relecture CGU + CGV** : clauses AI Act + RGPD + clauses Agent Coach
4. **Relecture page `/transparency`** : validation contenu Art. 50
5. **Relecture disclaimers UI** : validation textes légaux
6. **Recommandations RC Pro** : cabinet assureur + plafonds
7. **Attestation finale signée** (voir template ci-dessous)

### 13.2 — Template attestation

```
ATTESTATION DE CONFORMITÉ AI ACT

Cabinet : [NOM CABINET]
Avocat signataire : [NOM AVOCAT], Barreau de [VILLE]
Client : STRUCTORAI / [Nom Fabrice entrepreneur individuel ou raison sociale]

Date : [DATE]

Objet : Audit de conformité de l'application STRUCTORAI au 
Règlement européen (UE) 2024/1689 sur l'intelligence artificielle 
(AI Act).

Après examen :
- De la documentation technique de l'application STRUCTORAI 
  (architecture, agents IA, modèles GPAI utilisés)
- Du positionnement légal de l'Agent Coach Business 
  (NAF 70.22Z, exclusions professions réglementées)
- Des mesures de transparence implémentées (page /transparency, 
  badges "IA", bouton "Demander validation humaine")
- Des mesures de supervision humaine (dossier "À faire", 
  validation explicite de chaque action)
- Du système d'audit logs (table ai_decisions, rétention 12 mois)
- Des CGU et CGV

Le cabinet [NOM CABINET] atteste :

1. Que STRUCTORAI est classifiable en "système IA à risque 
   limité avec supervision humaine" au sens de l'AI Act, et 
   n'est concerné NI par l'interdiction de l'article 5 NI par 
   le haut risque de l'article 6 + Annexe III.

2. Que les mesures de transparence de l'article 50 sont 
   correctement implémentées (information utilisateur, marquage 
   contenu, etc.).

3. Que les obligations de supervision humaine (article 14, 26) 
   sont respectées via les mécanismes de validation.

4. Que l'Agent Coach Business est correctement positionné en 
   "conseil d'entreprise NAF 70.22Z" (activité non réglementée) 
   et n'empiète PAS sur les professions réglementées 
   (expert-comptable ordonnance 1945, avocat loi 1971, CIF AMF).

5. Que STRUCTORAI est conforme au RGPD (UE 2016/679) pour ses 
   traitements de données personnelles.

Cette attestation est valable jusqu'au [DATE + 12 MOIS] ou 
jusqu'à modification substantielle du système, de la 
réglementation ou du positionnement des agents.

Fait à [VILLE], le [DATE].

[SIGNATURE]
[Nom avocat]
[Barreau]
[Nom cabinet]
```

### 13.3 — Publication

L'attestation est :
- Conservée dans le dossier juridique STRUCTORAI
- Disponible sur demande à `dpo@structorai.app` (pas affichée publiquement — confidentialité du cabinet)
- Résumée dans la page `/transparency` avec le nom du cabinet

---

## 14. REVUE ET MAINTENANCE

### 14.1 — Revue trimestrielle

Tous les **3 mois**, Fabrice relit ce document + vérifie :

- [ ] Liste des modèles utilisés toujours exacte (si changement de version Claude/Whisper/ElevenLabs)
- [ ] Table `ai_decisions` fonctionne (rétention 12 mois, logs complets)
- [ ] Page `/transparency` en ligne et à jour
- [ ] Badges "IA" et boutons "Demander validation humaine" toujours visibles
- [ ] Aucun incident grave non signalé à l'AI Office
- [ ] Formation AI literacy à jour pour nouveaux membres équipe

### 14.2 — Revue annuelle

Tous les **12 mois** :
- Nouvelle attestation avocat (~500-800€ update)
- Audit complet compliance par auditeur externe si >1K clients
- Publication rapport annuel de transparence sur `/transparency`

### 14.3 — Veille réglementaire

Topics à surveiller en continu :

| Sujet | Sources |
|---|---|
| Évolutions AI Act | digital-strategy.ec.europa.eu, artificialintelligenceact.eu |
| Digital Omnibus (décalage potentiel obligations haut risque) | Commission européenne |
| GPAI Code of Practice | AI Office EU |
| Jurisprudence AI Act (à partir de 2027) | CJUE |
| Positions CNIL sur IA | cnil.fr |
| Positions autorités nationales (ANSSI, ARCEP) | Sites officiels |

**Alerte** : si une obligation nouvelle s'ajoute → mise à jour du fichier + revue express avocat.

### 14.4 — Modifications structurantes du produit

Si STRUCTORAI évolue significativement (nouveau modèle GPAI, nouvelle catégorie d'agent, nouveau marché), la classification AI Act DOIT être ré-évaluée :

**Déclencheurs de réévaluation** :
- Ajout d'un 15ème agent avec fonctions différentes
- Intégration d'un modèle GPAI non-Claude/non-OpenAI
- Entrée sur un marché haut risque (ex: éducation, santé, RH automatisée)
- Changement de pays d'établissement

---

## 15. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/METIER.md` | Base factuelle utilisée par le système IA (règles BTP) |
| `docs/COACH-DISCLAIMER.md` | Cadre juridique Coach Business (non réglementé) |
| `docs/MEMORY.md` + `docs/MEMORY-STRATEGY.md` | Architecture + procédures mémoire (souveraineté EU) |
| `docs/SUPPORT-STRATEGY.md` (à créer) | Support hybride = mécanisme supervision humaine |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` | Chiffres : 14 agents, modèles exacts, budget 1 500€ |
| `SECURITE.md` §8 | Résumé AI Act (ce fichier = détail complet) |
| `CLAUDE.md` §Règles d'or | Supervision humaine obligatoire |
| `FEATURES.md` F131 | Conformité AI Act comme feature V1 |
| `BUILD_PLAN.md` Sprint 6 | Migration 036 + page /transparency |
| `COUT_REEL.md` | Budget 1 500€ revue juridique |

### 15.1 — Textes et références externes

- **Règlement AI Act** : https://eur-lex.europa.eu/eli/reg/2024/1689/oj
- **AI Office EU** : https://digital-strategy.ec.europa.eu/fr/policies/ai-office
- **Explorateur articles AI Act** : https://artificialintelligenceact.eu/
- **High-level summary AI Act** : https://artificialintelligenceact.eu/high-level-summary/
- **Code of Practice GPAI** : https://digital-strategy.ec.europa.eu/
- **CNIL IA** : https://www.cnil.fr/fr/intelligence-artificielle

---

> **Ce fichier est la constitution AI Act de STRUCTORAI.**
> **Deadline d'enforcement : 2 août 2026 — conformité obligatoire AVANT.**
> **Sanctions jusqu'à €40M ou 7% CA en cas de violation grave.**
> **Toute modification du produit qui touche à l'IA DOIT passer par une revue de ce document avant déploiement.**
