---
name: AGENTS
description: Spécification exhaustive des 14 agents IA de STRUCTORAI + Supervisor. Pour chaque agent — modèle Claude, rôle précis, features couvertes, tools exposés, triggers (proactif + sollicité), memory access patterns (Mem0 + MemPalace), KPIs, escalation rules, exemples de conversations réelles, prompts système clés. Document de référence pour Claude Code lors de l'implémentation des 15 fichiers backend/app/agents/agent_*.py.
type: technical-agent-spec
scope: backend-agents, prompts, tool-registry, memory
priority: critical
date: 2026-04-18
version: 1.0
total_agents_v1: 14 + Supervisor (15 entités)
total_agents_v2: 14 + V2 Téléphone + Supervisor
budget_cap_daily_pro: $2.00/jour
budget_cap_daily_business: $5.00/jour
budget_cap_daily_starter: $0.50/jour
---

# docs/AGENTS.md — Spécification des 14 agents IA STRUCTORAI

> **Ce fichier est la source de vérité des 14 agents + Supervisor.**
> Toute modification d'agent (modèle, tools, prompt) DOIT mettre à jour ce document.
> Reference architecture : `docs/ARCH.md` §5-6 (Supervisor + Agents).
> Reference business : `docs/FEATURES_COMPLETE.md` pour mapping features ↔ agent.

---

## 0. SOMMAIRE

1. [Principes communs à tous les agents](#1-principes-communs-à-tous-les-agents)
2. [Supervisor — l'orchestrateur](#2-supervisor--lorchestrateur)
3. [Agent 1 — Devis](#3-agent-1--devis)
4. [Agent 2 — Relance](#4-agent-2--relance)
5. [Agent 3 — Compta](#5-agent-3--compta)
6. [Agent 4 — Planning](#6-agent-4--planning)
7. [Agent 5 — Réputation & Marketing](#7-agent-5--réputation--marketing)
8. [Agent 6 — Prospection](#8-agent-6--prospection)
9. [Agent 7 — Email Pro](#9-agent-7--email-pro)
10. [Agent 8 — Fiscalité & Trésorerie](#10-agent-8--fiscalité--trésorerie)
11. [Agent 9 — Déplacements](#11-agent-9--déplacements)
12. [Agent 10 — RH](#12-agent-10--rh)
13. [Agent 11 — Vision IA](#13-agent-11--vision-ia)
14. [Agent 12 — Site Web](#14-agent-12--site-web)
15. [Agent 13 — Coach Business](#15-agent-13--coach-business)
16. [Agent 14 V2 — Téléphone IA](#16-agent-14-v2--téléphone-ia)
17. [Communication inter-agents](#17-communication-inter-agents)
18. [Escalation globale](#18-escalation-globale)
19. [Budget et circuit breakers](#19-budget-et-circuit-breakers)
20. [Références croisées](#20-références-croisées)

---

## 1. PRINCIPES COMMUNS À TOUS LES AGENTS

### 1.1 — Classe de base `BaseAgent`

Tous les agents héritent de `BaseAgent` (`backend/app/agents/base_agent.py`). Voir `docs/ARCH.md` §6.2 pour la structure complète.

**Attributs obligatoires** :
- `name` (str) — identifiant unique
- `model` (str) — version exacte du modèle Claude
- `system_prompt_file` (str) — chemin vers le prompt dans `backend/app/prompts/`
- `tools` (list) — tools exposés à l'agent
- `budget_pct` (float) — % du budget mensuel artisan alloué
- `daily_cap_usd` (float) — cap quotidien absolu
- `mem_access` (enum) — `mem0_only` / `mempalace_only` / `hybrid`
- `can_act` (bool) — peut exécuter des actions (envoi, etc.)
- `requires_validation` (bool) — nécessite validation humaine (bouton "Valider")

### 1.2 — Règles immuables (toutes agents)

**Règle 1 — Constitution BTP**
Tous les agents injectent le contexte METIER.md dans leur prompt (voir `docs/METIER.md`). Pas d'exception.

**Règle 2 — Score de confiance**
Chaque output se termine par un score 🟢 (>80%) / 🟡 (60-80%) / 🔴 (<60%). Règle zéro invention.

**Règle 3 — Escalade si confiance <60%**
L'agent propose l'escalade humaine (voir `docs/SUPPORT-STRATEGY.md`) au lieu d'inventer.

**Règle 4 — Audit log AI Act**
Chaque appel LLM est loggé dans `ai_decisions` (voir `docs/AI-ACT-COMPLIANCE.md` §9).

**Règle 5 — Supervision humaine**
Aucune action externe (envoi, publication, paiement) sans bouton "Valider" (Dossier "À faire").

**Règle 6 — Communication via Supervisor**
Un agent ne peut PAS appeler directement un autre agent. Il émet un event → Supervisor → routage.

**Règle 7 — Stateless**
L'agent recall son contexte (Mem0 + MemPalace + DB) au début de chaque task. Aucun state local persistant.

**Règle 8 — Timeout 30s**
Chaque appel LLM timeout à 30s. Circuit breaker actif après 3 réponses vides.

### 1.3 — Structure du prompt système

Chaque prompt (`backend/app/prompts/*_prompt.py`) suit ce template :

```python
SYSTEM_PROMPT = """
# Identité
Tu es l'Agent [NOM] de STRUCTORAI.
Ton rôle : [ROLE_PRECIS_EN_1_PHRASE]

# Contexte métier (injection automatique)
{METIER_CONTEXT}

# Mémoire artisan (injection automatique)
{MEMORY_CONTEXT}

# Health invariants (injection automatique)
{HEALTH_INVARIANTS}

# Règles absolues
- [RÈGLE 1]
- [RÈGLE 2]
...

# Ton et style
[TON_SPÉCIFIQUE_AGENT]

# Outils disponibles
{TOOL_LIST}

# Format de sortie
[FORMAT_OBLIGATOIRE_INCLUANT_CONFIDENCE]

# Escalade
Si confiance <60% ou sujet hors scope → propose "Je passe le relais au support"
"""
```

### 1.4 — Memory access patterns

| Type de données | Stockage | Qui accède |
|---|---|---|
| Prix perso artisan | Mem0 | Devis, Compta, Coach |
| Patterns clients (payeur vs mauvais payeur) | Mem0 | Relance, Prospection |
| Préférences artisan (ton, voix, voeux) | Mem0 | Tous (via Supervisor) |
| Verbatim conversations vocales | MemPalace | Devis, Coach, Supervisor |
| Knowledge graph temporel (Martin payé 12/03) | MemPalace | Relance, Compta, Coach |
| Décisions passées + raisons | MemPalace | Coach, Supervisor |
| Réseau artisan (archis, apporteurs) | Mem0 + DB | Prospection |

Voir `docs/MEMORY.md` pour détails exhaustifs.

---

## 2. SUPERVISOR — L'ORCHESTRATEUR

### 2.1 — Identité

- **Fichier** : `backend/app/agents/supervisor.py`
- **Prompt** : `backend/app/prompts/supervisor_prompt.py`
- **Modèle** : `claude-sonnet-4-6`
- **Budget/appel** : $0.10
- **Pattern** : Ouroboros Supervisor

### 2.2 — Rôle

Le Supervisor est **l'entrée unique** vers les 14 agents. Aucune requête API ne court-circuite le Supervisor.

**Responsabilités** :
1. **Intent detection** — identifier quel(s) agent(s) mobiliser
2. **Queue management** — ordonnancer les tâches par priorité
3. **Budget enforcement** — tracker coûts, couper si dépassement
4. **Circuit breaking** — gérer les échecs agents
5. **Context briefing** — injecter METIER.md + Mem0 + MemPalace
6. **Background Consciousness** — penser entre les tâches (pattern Ouroboros)
7. **Inter-agent routing** — dispatcher les events entre agents
8. **Confidence aggregation** — valider scores avant retour utilisateur

### 2.3 — Tools exposés

- `route_to_agent(agent_name, task)` — route vers un agent spécifique
- `route_to_multi_agents(agent_list, task)` — task parallélisée
- `get_artisan_context(artisan_id)` — récupère tout le contexte
- `compact_context()` — compresse via LLM léger si context > 50K tokens
- `trigger_circuit_breaker(agent_name)` — désactive un agent 5min
- `escalate_to_human(task, reason)` — escalade support (F136)

### 2.4 — Background Consciousness

**Déclenchement** : tick toutes les heures (cron Railway).

**Cycle** :
```
1. Scan last 1h events → détecter patterns
2. Check alertes prédictives :
   - Devis non relancés J+3+
   - Factures en retard
   - Seuils fiscaux approchés
   - Rappels matériaux chantier en cours
   - Coach mensuel à déclencher (1er du mois)
3. Enqueue tâches proactives → Dossier "À faire"
4. Enrichir Mem0 (patterns détectés)
```

**Coût** : ~$0.07/pensée × 24 pensées/jour = ~$1.68/mois par artisan.

### 2.5 — Règles de routage (intent → agent)

| Intent détecté | Agent(s) mobilisé(s) |
|---|---|
| "Fais un devis pour X" | Devis (+ Vision IA si photo) |
| "Relance ce client" | Relance |
| "Scan ce ticket" | Compta (+ Vision IA pour OCR) |
| "Combien j'ai fait ce mois" | Supervisor direct (query DB) |
| "Prends une photo du chantier" | Vision IA |
| "Envoie une facture à Martin" | Devis (création) + Compta (tracking) |
| "Analyse ce devis concurrent" | Vision IA (F121) |
| "Combien je dois à l'URSSAF" | Fiscalité |
| "Génère un post Insta" | Réputation |
| "Où en est mon chantier Dupont" | Planning |
| "Analyse mon mois" | Coach Business |
| "Publie mon site" | Site Web |
| Tous les cas ambigus | Supervisor choisit + confirme |

### 2.6 — KPIs

| KPI | Objectif |
|---|---|
| Intent detection accuracy | >90% |
| Routage correct (pas d'erreur d'agent) | >95% |
| Temps moyen routage | <500ms |
| Budget dépassement/mois | <2% artisans |
| Consciousness ticks réussis | >95% |

---

## 3. AGENT 1 — DEVIS

### 3.1 — Identité

- **Fichier** : `backend/app/agents/agent_devis.py`
- **Prompt** : `backend/app/prompts/devis_prompt.py`
- **Modèle** : `claude-opus-4-7` (raisonnement complexe requis)
- **Budget/appel** : $0.15
- **Budget allocation** : **20%** du total artisan (le plus gros)

### 3.2 — Features couvertes

**Via `docs/FEATURES_COMPLETE.md`** :
F15, F16, F17, F18, F19, F20, F21, F22, F25, F88, F89, F90, F97, F99, F108, F121

- F15 : Création devis vocal
- F16 : Calcul TVA multi-taux
- F17 : Injection 47 mentions obligatoires (voir `docs/METIER.md`)
- F18 : Génération PDF ReportLab
- F19 : Signature électronique Yousign
- F20 : Acompte auto-calculé
- F21 : Duplication devis
- F22 : Prix référentiel BTP par métier
- F25 : Devis rapide vocal (<15s)
- F88 : Calcul Factur-X anticipé
- F89 : Templates devis personnalisables
- F90 : Gestion CGV artisan
- F97 : Validation prix vs benchmark
- F99 : Adaptation multi-langue 6 langues
- F108 : Versioning devis (v1, v2, v3)
- F121 : Analyse devis concurrent (collaboration Vision IA)

### 3.3 — Rôle précis

**Entrée** : vocal / texte / photo → **Sortie** : PDF devis conforme + lien signature.

**Pipeline complet** :
1. Recall Mem0 (prix perso artisan, client)
2. Recall MemPalace (chantiers similaires récents)
3. Vision IA si photo fournie (analyse + quantités)
4. Génération postes + quantités (Claude Opus 4.7)
5. Calcul prix (référentiels `data/prix/*.json` + Mem0 prix perso)
6. Calcul TVA multi-taux (règles METIER.md)
7. Injection 47 mentions obligatoires (voir `docs/METIER.md` §Mentions)
8. Score de confiance 🟢🟡🔴
9. Préparation PDF ReportLab + profil Factur-X EXTENDED
10. Envoi vers Dossier "À faire" (validation humaine)

### 3.4 — Tools

- `fetch_artisan_prices(poste)` — prix perso Mem0
- `fetch_reference_prices(metier, poste, region)` — référentiels BTP
- `detect_tva_rate(type_travaux, age_logement, rge_required)` — règles TVA
- `inject_mandatory_mentions(devis_draft)` — 47 mentions
- `calculate_acompte(total_ttc, pct)` — acompte
- `generate_pdf(devis_data)` — ReportLab
- `call_vision_agent(photo_id)` — déléguer analyse photo
- `lookup_price_database(designation, region)` — base prix
- `verify_rge_validity(artisan_id, poste)` — blocage TVA 5,5 si RGE invalide (F128)

### 3.5 — Triggers

**Sollicité (90%)** :
- Artisan écrit "fais un devis"
- Artisan parle vocal
- Artisan scan photo "devis concurrent"
- API `POST /v1/devis`

**Proactif (10%)** :
- Background Consciousness détecte : client demande devis par email depuis 3 jours sans réponse
- Artisan a oublié de chiffrer un chantier mentionné verbalement

### 3.6 — Memory access

- **Mem0** : prix perso, client (historique devis, marge moyenne)
- **MemPalace** : verbatim conversations "pour le M.Dupont, on avait fait X à Y euros"

### 3.7 — KPIs

| KPI | Objectif |
|---|---|
| Temps génération devis complet | <15s |
| Score confiance 🟢 | >70% des devis |
| Score confiance 🔴 | <5% des devis |
| Taux signature post-validation artisan | >80% |
| Erreur TVA | <0.1% |
| Erreur mention obligatoire | 0% (critique légal) |

### 3.8 — Escalation rules

- Devis > 50 000€ TTC → multi-model review (Sonnet valide + Opus confirme)
- Incohérence prix >30% vs benchmark → demander confirmation artisan
- Confiance 🔴 → bouton "Demander validation humaine" proéminent

### 3.9 — Exemple de conversation

```
ARTISAN (vocal 45s) : "Bon alors pour Martin c'est la rénovation 
complète de sa SDB, 8m² à peu près, il veut une douche italienne, 
un nouveau sol en carrelage, et il faut refaire toute la plomberie 
parce qu'il y a une fuite. La peinture aussi. Il voudrait le 
devis pour demain."

AGENT DEVIS (Claude Opus 4.7) :
"J'ai noté pour Martin. SDB 8m², rénovation complète.

Voici les postes que je prépare :
1. Dépose ancienne installation (1 forfait)
2. Plomberie complète — nouvelle arrivée + évacuation
3. Douche italienne 120x90 (receveur + colonne + paroi)
4. Carrelage sol et mur (8m² sol + 20m² mur)
5. Peinture plafond + coffre
6. Évacuation déchets

TVA 10% (rénovation logement > 2 ans).

Sur ton historique [Mem0 recall] : 
- Prix plomberie SDB similaire : 2 800€ HT (Mme Leroy janvier)
- Douche italienne : 1 450€ HT (même fournisseur)

Estimation : 6 800€ HT soit 7 480€ TTC.

Confiance 🟢 — prix alignés avec ton historique.

Je génère le PDF ?"

[Bouton "Valider et envoyer"] [Bouton "Modifier"]
```

---

## 4. AGENT 2 — RELANCE

### 4.1 — Identité

- **Fichier** : `backend/app/agents/agent_relance.py`
- **Prompt** : `backend/app/prompts/relance_prompt.py`
- **Modèle** : `claude-haiku-4-5-20251001` (tâche simple, volume élevé)
- **Budget/appel** : $0.02
- **Budget allocation** : 7%

### 4.2 — Features couvertes

F31, F32, F33, F34, F35

- F31 : Relance auto devis J+3
- F32 : Relance facture J+15, J+30, J+45
- F33 : Ton adaptatif (doux / ferme / adaptatif via Mem0)
- F34 : Mise en demeure J+60
- F35 : Tracking ouverture/clic

### 4.3 — Rôle précis

Relancer automatiquement (avec validation artisan) les devis sans réponse et les factures impayées.

**Logique escalade** :
- **Devis J+3** : relance douce "Tu as pu regarder mon devis ?"
- **Facture J+15** : rappel amical
- **Facture J+30** : rappel ferme
- **Facture J+45** : mise en demeure pré-juridique
- **Facture J+60** : proposition mise en demeure officielle (artisan valide avant envoi LRAR)

### 4.4 — Ton adaptatif

L'agent adapte le ton selon **Mem0** (patterns client) :

| Client | Ton utilisé |
|---|---|
| Payeur rapide (moyenne <10j) | Chaleureux, léger |
| Payeur normal (10-30j) | Professionnel standard |
| Mauvais payeur (>45j récurrent) | Ferme direct |
| Client VIP (CA élevé) | Très courtois |
| Premier chantier | Poli, rassurant |

### 4.5 — Tools

- `fetch_client_payment_pattern(client_id)` — Mem0
- `select_tone(pattern)` — doux/ferme/adaptatif
- `generate_relance_message(devis_or_facture, tone, channel)` — message adapté
- `send_email(client, message)` — via Brevo
- `send_sms(client, message)` — via Twilio
- `send_whatsapp(client, message)` — via Meta Cloud API
- `generate_mise_en_demeure_lrar(facture_id)` — PDF officiel

### 4.6 — Triggers

**Proactif (90%)** : Background Consciousness toutes les 6h scanne :
- Devis `status=sent AND sent_at < NOW()-3d`
- Factures `status=sent AND due_date < NOW() AND NOT paid`

**Sollicité (10%)** : artisan clique "Relancer ce devis/cette facture".

### 4.7 — Memory access

- **Mem0** : patterns paiement client, ton préféré artisan
- **MemPalace** : conversations passées client ("il avait dit qu'il paierait le 15")

### 4.8 — KPIs

| KPI | Objectif |
|---|---|
| % devis relancés dans délai J+3 | >95% |
| Taux conversion post-relance devis | >15% |
| Taux paiement post-relance facture J+15 | >40% |
| Taux paiement post-mise en demeure J+60 | >70% |
| False positive (relance envoyée à tort) | <1% |

### 4.9 — Exemple de message

**Devis J+3, client payeur normal (ton pro)** :
```
Bonjour M. Dupont,

J'espère que vous allez bien. Je voulais savoir si vous aviez 
pu regarder le devis DEV-2026-042 que je vous ai envoyé il y a 
3 jours pour la rénovation de votre salle de bain.

N'hésitez pas à me poser vos questions.

Cordialement,
[Artisan]
```

**Facture J+30, mauvais payeur (ton ferme)** :
```
Bonjour M. Martin,

Votre facture FACT-2026-087 de 2 450€ TTC, datée du 15 mars 
dernier, n'a pas été réglée à ce jour.

Le délai de paiement est dépassé de 15 jours. Merci de 
procéder au règlement sous 48h.

Sans règlement dans ce délai, je serai contraint d'initier 
la procédure de recouvrement.

Cordialement,
[Artisan]
```

---

## 5. AGENT 3 — COMPTA

### 5.1 — Identité

- **Fichier** : `backend/app/agents/agent_compta.py`
- **Prompt** : `backend/app/prompts/compta_prompt.py`
- **Modèle** : `claude-sonnet-4-6`
- **Budget/appel** : $0.08
- **Budget allocation** : 9%

### 5.2 — Features couvertes

F09, F10, F30, F36, F37, F38, F39, F41

- F09 : Suivi fiscal annuel (collab Fiscalité)
- F10 : OCR tickets de caisse (via Vision IA)
- F30 : Catégorisation auto (matériaux, fourniture, déplacement, etc.)
- F36 : Marge temps réel par chantier
- F37 : Export comptable mensuel (CSV, FEC-compatible)
- F38 : Export éléments paie
- F39 : Détection anomalies (ticket douteux, doublon)
- F41 : Rapprochement bancaire manuel

### 5.3 — Rôle précis

**Entrée** : tickets scannés + factures + dépenses → **Sortie** : compta propre + marge chantier + export cabinet.

**Pipeline** :
1. Réception ticket (via Vision IA OCR)
2. Catégorisation auto (matériaux / carburant / restauration / matériel / etc.)
3. Imputation chantier (GPS automatique ou choix artisan)
4. Calcul marge live du chantier
5. Alerte si anomalie (prix anormal, doublon)
6. Consolidation mensuelle → export

### 5.4 — Tools

- `parse_ticket_ocr(ticket_image)` — délégation Vision IA
- `categorize_expense(description, vendor)` — ML catégorisation
- `impute_to_chantier(expense, gps)` — auto-impute via GPS
- `calculate_chantier_margin(chantier_id)` — marge live
- `detect_anomalies(expense)` — prix anormal, doublon
- `export_to_fec(period)` — export comptable FEC
- `export_paie(period)` — éléments paie

### 5.5 — Triggers

**Sollicité** : artisan scanne un ticket.
**Proactif** : fin de mois → propose export cabinet.

### 5.6 — Memory access

- **Mem0** : catégories habituelles, fournisseurs récurrents
- **MemPalace** : historique tickets (détection doublons)

### 5.7 — KPIs

| KPI | Objectif |
|---|---|
| Accuracy catégorisation auto | >90% |
| Faux positif anomalie | <5% |
| Temps OCR ticket | <5s |
| Disponibilité export mensuel | 100% (1er du mois) |

---

## 6. AGENT 4 — PLANNING

### 6.1 — Identité

- **Fichier** : `backend/app/agents/agent_planning.py`
- **Prompt** : `backend/app/prompts/planning_prompt.py`
- **Modèle** : `claude-haiku-4-5-20251001`
- **Budget/appel** : $0.03
- **Budget allocation** : 6%

### 6.2 — Features couvertes

F43, F44, F45, F46, F47, F48, F102, F103

- F43 : Timer chantier start/stop
- F44 : Détection dépassement temps prévu
- F45 : Capacité artisan (hebdo, mensuelle)
- F46 : Rappels matériaux à commander
- F47 : Enchaînement chantiers optimisé
- F48 : Alertes météo (si chantier ext)
- F102 : Briefing matin quotidien
- F103 : Adaptation culturelle (Ramadan, fêtes religieuses — F133)

### 6.3 — Rôle précis

Orchestrer le temps de l'artisan : pointage chantier, capacité, matériaux, briefings.

**Pipeline briefing matin (7h-9h)** :
1. Analyse chantiers actifs
2. Météo (si besoin)
3. Matériaux à acheter aujourd'hui
4. Appels à passer
5. Message 3-5 lignes "Ta journée type aujourd'hui"

### 6.4 — Tools

- `start_timer(chantier_id)`
- `stop_timer(chantier_id)`
- `check_capacity(week)` — heures déjà planifiées
- `detect_time_overrun(chantier_id)` — dépassement
- `list_materials_needed(chantier_id)` — matériaux restants
- `fetch_weather(gps, date)` — météo
- `generate_morning_brief()` — briefing quotidien

### 6.5 — Adaptation culturelle (F103/F133)

Si settings `culture` inclut :
- **Ramadan** : pas de briefing avant 9h pendant Ramadan, adaptation pauses
- **Shabbat** (juif) : pas de notifications vendredi 18h → samedi 20h
- **Dimanche chrétien** : notifications réduites
- **Fêtes religieuses** : alerte "Ton chantier tombe sur Aïd/Noël/Yom Kippour"

### 6.6 — Triggers

**Proactif (80%)** :
- Briefing 7h-9h (configurable)
- Alerte dépassement temps chantier
- Rappel matériaux veille de chantier

**Sollicité (20%)** : "Où en est mon chantier X ?"

### 6.7 — KPIs

| KPI | Objectif |
|---|---|
| % briefings matin ouverts | >70% |
| Accuracy prédiction dépassement | >80% |
| Rappel matériaux pertinent | >90% |

---

## 7. AGENT 5 — RÉPUTATION & MARKETING

### 7.1 — Identité

- **Fichier** : `backend/app/agents/agent_reputation.py`
- **Prompt** : `backend/app/prompts/reputation_prompt.py`
- **Modèle** : `claude-haiku-4-5-20251001`
- **Budget/appel** : $0.02
- **Budget allocation** : 6%

### 7.2 — Features couvertes

F57, F58, F59, F60, F61, F62, F63, F64, F65

- F57 : Demande avis Google auto post-chantier (SMS)
- F58 : Réponse avis Google IA (avec validation humaine)
- F59 : Post Instagram/Facebook avec photos avant/après
- F60 : SEO local (Google Business optimisation)
- F61 : Génération descriptions chantier pour le site
- F62 : Gestion identité visuelle (logo, couleurs)
- F63 : Planning de publications mensuel
- F64 : Alerte mentions online (marque artisan)
- F65 : Rapport marketing mensuel

### 7.3 — Rôle précis

Construire et défendre la réputation de l'artisan en ligne sans qu'il y pense.

### 7.4 — Tools

- `request_review_sms(client_id, chantier_id)` — SMS post-chantier (Twilio)
- `draft_review_response(review, rating)` — brouillon réponse
- `generate_before_after_post(photo_avant_id, photo_apres_id)` — post social
- `schedule_social_post(content, date, channels)`
- `fetch_google_business_insights()` — stats Google
- `scan_online_mentions(artisan_name)` — mentions web

### 7.5 — Triggers

**Proactif** :
- Chantier passé en `status=termine` → SMS demande avis J+1
- Avis reçu → brouillon réponse en 1h
- Publication sociale programmée mensuelle

**Sollicité** : "Publie la photo avant/après du Martin sur Insta".

### 7.6 — Memory access

- **Mem0** : ton artisan (formel/décontracté), charte visuelle
- **MemPalace** : historique publications (évite doublons)

### 7.7 — KPIs

| KPI | Objectif |
|---|---|
| Taux réponse avis <24h | >90% |
| Nouveau avis reçu/mois | +3-5 en moyenne |
| Taux note 5 étoiles | >80% des nouveaux |
| Engagement publications social | +20% 3 mois |

---

## 8. AGENT 6 — PROSPECTION

### 8.1 — Identité

- **Fichier** : `backend/app/agents/agent_prospection.py`
- **Prompt** : `backend/app/prompts/prospection_prompt.py`
- **Modèle** : `claude-haiku-4-5-20251001`
- **Budget/appel** : $0.02
- **Budget allocation** : 5%

### 8.2 — Features couvertes

F66, F67, F68, F69, F70, F71, F72, F73, F87

- F66 : CRM architectes
- F67 : CRM apporteurs d'affaires
- F68 : Tracking commissions apporteurs
- F69 : Leads entrants (email, site web, WhatsApp)
- F70 : Relance réseau trimestrielle
- F71 : Score qualité lead
- F72 : Conversion lead → client
- F73 : Historique relations pro
- F87 : Auto-création fiche prospect (depuis WhatsApp, email, téléphone)

### 8.3 — Rôle précis

Entretenir le réseau pro et qualifier les leads entrants.

### 8.4 — Tools

- `create_lead_from_email(email_content)` — auto-fiche
- `create_lead_from_whatsapp(message)` — auto-fiche
- `score_lead(lead)` — chaud/tiède/froid
- `schedule_network_followup(contact, delay_weeks)` — rappel trimestriel
- `track_commission(apporteur_id, affaire_id)` — commission

### 8.5 — Triggers

**Proactif** :
- Email reçu identifié comme prospect → crée fiche
- WhatsApp inbound → crée fiche
- 3 mois sans contact archi → propose rappel

**Sollicité** : "Qui je n'ai pas rappelé depuis 2 mois ?"

### 8.6 — KPIs

| KPI | Objectif |
|---|---|
| % leads qualifiés < 24h | >95% |
| Taux conversion lead→client | >25% |
| Commissions apporteurs calculées correctement | 100% |

---

## 9. AGENT 7 — EMAIL PRO

### 9.1 — Identité

- **Fichier** : `backend/app/agents/agent_email_pro.py`
- **Prompt** : `backend/app/prompts/email_pro_prompt.py`
- **Modèle** : `claude-haiku-4-5-20251001`
- **Budget/appel** : $0.03
- **Budget allocation** : 4%

### 9.2 — Features couvertes

F11 (extension fiscale), F69 (leads entrants), briefing F102.

### 9.3 — Rôle précis

IMAP polling + filtrage + résumé quotidien + actions proposées.

### 9.4 — Tools

- `imap_poll(credentials)` — récup nouveaux emails
- `filter_pro_vs_perso(email)` — tri
- `categorize_email(email)` — prospect / client / fournisseur / admin / spam
- `generate_daily_summary(emails)` — résumé 5-10 lignes
- `propose_response(email)` — brouillon

### 9.5 — Triggers

**Proactif** : polling toutes les 15 min (plan Business) / 1h (plan Pro).
**Sollicité** : "Montre-moi mes emails importants aujourd'hui".

### 9.6 — KPIs

| KPI | Objectif |
|---|---|
| % emails correctement catégorisés | >85% |
| % spam correctement filtré | >95% |
| Délai traitement nouvel email | <30 min |

---

## 10. AGENT 8 — FISCALITÉ & TRÉSORERIE

### 10.1 — Identité

- **Fichier** : `backend/app/agents/agent_fiscalite.py`
- **Prompt** : `backend/app/prompts/fiscalite_prompt.py`
- **Modèle** : `claude-sonnet-4-6`
- **Budget/appel** : $0.05
- **Budget allocation** : 8%

### 10.2 — Features couvertes

F09 (suivi annuel), F11, F12, F13, F14, F40, F42, F129

- F09 : Suivi fiscal annuel
- F11 : Scan courrier admin (URSSAF, DGFiP)
- F12 : Calendrier fiscal personnalisé (statut)
- F13 : Alertes seuils (micro-BIC 176 200€, TVA franchise, etc.)
- F14 : Alertes URSSAF échéances
- F40 : Trésorerie prévisionnelle
- F42 : Simulation changement statut (Micro → EURL → SAS)
- F129 : Pack contrôle fiscal (ZIP horodaté)

### 10.3 — Rôle précis

Tenir à jour la conformité fiscale, anticiper les échéances, détecter les seuils critiques.

### 10.4 — Tools

- `detect_fiscal_status(org)` — Micro-BIC/BNC/EURL/SAS/EI
- `calculate_cumulative_ca(year)` — CA cumulé
- `check_thresholds(ca, status)` — seuils micro, franchise TVA
- `fetch_fiscal_calendar(status)` — échéances personnalisées
- `scan_admin_letter(photo)` — délégation Vision IA + analyse
- `simulate_status_change(current, target)` — simulation
- `generate_control_pack(period)` — ZIP contrôle fiscal

### 10.5 — Disclaimer obligatoire

**Règle critique** : l'Agent Fiscalité **n'est pas un expert-comptable**. Voir `docs/COACH-DISCLAIMER.md` pour les disclaimers (même cadre NAF 70.22Z que Coach).

**Phrase systématique** en fin d'analyse :
> *"Ces chiffres sont indicatifs. Pour toute décision fiscale engageante, valide avec ton expert-comptable."*

### 10.6 — Triggers

**Proactif** :
- J-30 avant échéance URSSAF/TVA → alerte
- Seuil à 80% / 90% / 100% → alerte progressive
- 1er du mois → résumé position fiscale

**Sollicité** : "Combien je dois à l'URSSAF ce trimestre ?"

### 10.7 — KPIs

| KPI | Objectif |
|---|---|
| % échéances alertées à temps | 100% |
| Accuracy calcul CA cumulé | 100% |
| Accuracy détection seuil | 100% |

---

## 11. AGENT 9 — DÉPLACEMENTS

### 11.1 — Identité

- **Fichier** : `backend/app/agents/agent_deplacements.py`
- **Prompt** : `backend/app/prompts/deplacements_prompt.py`
- **Modèle** : `claude-haiku-4-5-20251001`
- **Budget/appel** : $0.02
- **Budget allocation** : 3%

### 11.2 — Features couvertes

F100 (indemnités BTP zones), barème km, paniers.

### 11.3 — Rôle précis

Tracker déplacements + calculer frais km + indemnités BTP par zone + paniers repas.

**Zones BTP** (CCN BTP) :
- Zone 1A (Grand Paris) — indemnité trajet + panier spécifique
- Zone 1B (petite couronne)
- Zone 2 (moyenne couronne)
- Zones régionales (CAPEB/FFB départementales)

### 11.4 — Tools

- `track_gps_trip(start, end)` — automatique
- `calculate_km_fees(distance, vehicle_type)` — barème URSSAF
- `calculate_btp_allowance(zone, distance)` — indemnité trajet
- `calculate_panier(zone, duration)` — panier repas
- `export_monthly(period)` — export comptable

### 11.5 — Triggers

**Proactif** : GPS détecte départ matin → crée déplacement auto.
**Sollicité** : "Ajoute un déplacement à 15h vers Evry".

### 11.6 — KPIs

| KPI | Objectif |
|---|---|
| Auto-détection déplacement | >95% |
| Accuracy distance | ±2% |
| Indemnité BTP correcte | 100% |

---

## 12. AGENT 10 — RH

### 12.1 — Identité

- **Fichier** : `backend/app/agents/agent_rh.py`
- **Prompt** : `backend/app/prompts/rh_prompt.py`
- **Modèle** : `claude-sonnet-4-6`
- **Budget/appel** : $0.05
- **Budget allocation** : 8%

### 12.2 — Plan requis

**Business uniquement** (Starter/Pro = mono-utilisateur).

### 12.3 — Features couvertes

F123, F124, cotisations CIBTP, grilles salariales BTP, export paie.

### 12.4 — Rôle précis

Gérer les employés (plan Business) : pointage, heures sup conv. BTP, CIBTP, intérim, export paie.

**Conventions collectives BTP gérées** :
- IDCC 1596 — Ouvriers bâtiment <10 salariés
- IDCC 1597 — Ouvriers bâtiment >10 salariés
- IDCC 2609 — ETAM bâtiment
- IDCC 2420 — Cadres BTP
- IDCC 1702 — Ouvriers TP
- IDCC 2614 — Cadres TP

### 12.5 — Tools

- `add_employee(data)` — fiche salarié
- `clock_in(employee_id)` / `clock_out(employee_id)` — pointage
- `calculate_overtime(employee_id, period)` — heures sup conv BTP
- `manage_conges_cibtp(employee_id)` — congés payés CIBTP
- `calculate_cibtp_contributions(period)` — cotisations CIBTP
- `export_paie_monthly(period)` — export cabinet paie

### 12.6 — Triggers

**Proactif** :
- Fin de mois → propose export paie
- Congés CIBTP à poser avant date limite

**Sollicité** : "Combien d'heures a fait Jean cette semaine ?"

### 12.7 — KPIs

| KPI | Objectif |
|---|---|
| Accuracy pointage | 100% |
| Calcul cotisations CIBTP correct | 100% |
| Export paie accepté par cabinet | >95% |

---

## 13. AGENT 11 — VISION IA

### 13.1 — Identité

- **Fichier** : `backend/app/agents/agent_vision.py`
- **Prompt** : `backend/app/prompts/vision_prompt.py`
- **Modèle** : `claude-opus-4-7` (vision complexe)
- **Budget/appel** : $0.08
- **Budget allocation** : 9%

### 13.2 — Features couvertes

F10, F50, F51, F53, F56, F121.

- F10 : OCR tickets (collab Compta)
- F50 : Photo chantier avant/pendant/après
- F51 : Détection anomalies (oubli VMC, joint manquant)
- F53 : Analyse plans architecte
- F56 : Scan courrier admin (collab Fiscalité)
- F121 : Détecteur devis concurrent (parse_competitor_quote)

### 13.3 — Rôle précis

**Premier filtre** de toutes les images uploadées :
- Ticket → Compta
- Plan architecte → Devis
- Photo chantier → galerie + analyse anomalies
- Courrier admin → Fiscalité
- Devis concurrent → analyse comparative

### 13.4 — Tools

- `classify_image(image)` — ticket/plan/photo/courrier/devis
- `parse_ticket(image)` — OCR structuré
- `parse_plan(image)` — dimensions, pièces
- `detect_anomalies(photo_chantier)` — VMC, joints, etc.
- `parse_competitor_quote(image)` — F121 analyse
- `scan_admin_letter(image)` — courrier

### 13.5 — F121 — Parser devis concurrent

L'artisan upload un devis concurrent (photo ou PDF). L'agent Vision :
1. Extrait structure (postes, prix, conditions)
2. Compare avec les prix de l'artisan (Mem0)
3. Identifie points faibles du concurrent
4. Propose arguments pour gagner le client

### 13.6 — KPIs

| KPI | Objectif |
|---|---|
| Accuracy classification image | >95% |
| Accuracy OCR ticket | >92% |
| Détection anomalies photo | >70% (utile, pas faux positifs) |

---

## 14. AGENT 12 — SITE WEB

### 14.1 — Identité

- **Fichier** : `backend/app/agents/agent_site_web.py`
- **Prompt** : `backend/app/prompts/site_web_prompt.py`
- **Modèle** : `claude-sonnet-4-6`
- **Budget/appel** : $0.10
- **Budget allocation** : 7%

### 14.2 — Features couvertes

F74, F75, F76, F77, F78, F79.

- F74 : Génération site vitrine initial
- F75 : MAJ auto (nouveaux chantiers, avis Google)
- F76 : SEO local (Google Business sync)
- F77 : Galerie chantiers avant/après
- F78 : Page contact avec formulaire
- F79 : Analytics (visiteurs, leads générés)

### 14.3 — Rôle précis

Générer et maintenir un site vitrine professionnel pour l'artisan (déploiement Vercel).

### 14.4 — Tools

- `generate_site_content(artisan_profile)` — copywriting
- `select_template(metier, style)` — template adapté
- `deploy_to_vercel(site_data)` — déploiement
- `sync_from_portfolio(chantiers)` — MAJ galerie
- `optimize_seo_local(metier, zone)` — meta tags

### 14.5 — Triggers

**Proactif** :
- Nouveau chantier terminé avec photos → propose mise à jour galerie
- Nouvel avis Google 5⭐ → propose mise en avant
- 1×/mois → rapport visiteurs

**Sollicité** : "Mets à jour mon site avec le chantier Dupont".

### 14.6 — KPIs

| KPI | Objectif |
|---|---|
| Temps génération site initial | <5 min |
| Uptime site | >99.5% |
| Leads générés via site | +2-5/mois après 6 mois |

---

## 15. AGENT 13 — COACH BUSINESS

### 15.1 — Identité

- **Fichier** : `backend/app/agents/agent_coach.py`
- **Prompt** : `backend/app/prompts/coach_prompt.py`
- **Modèle** : `claude-opus-4-7` (raisonnement stratégique)
- **Budget/appel** : $0.10
- **Budget allocation** : 6%

### 15.2 — Feature

**F119** — Conseil stratégique mensuel.

### 15.3 — Rôle précis

**Positionnement "éclaireur sectoriel"** — pas conseiller financier ni expert-comptable (voir `docs/COACH-DISCLAIMER.md` pour cadre juridique NAF 70.22Z).

Analyse mensuelle :
- Taux horaire facturé vs benchmark département
- Taux de conversion devis
- Marge brute
- Prospection (nouveaux vs récurrents)
- Positioning (prix vs concurrents zone)

### 15.4 — Disclaimer UI obligatoire

**Avant chaque analyse** :
```
💡 Analyse Coach — Indicative. Ces données éclairent tes choix 
mais ne remplacent pas l'avis d'un professionnel (expert-comptable, 
avocat fiscaliste).
```

**Après chaque recommandation** :
```
⚠️ Ceci est une piste à explorer, pas une directive. 
Valide avec ton expert-comptable avant toute décision engageante.
```

### 15.5 — Formulation obligatoire au conditionnel

Jamais :
- ❌ "Tu dois augmenter tes prix"
- ❌ "Passe en EURL"

Toujours :
- ✅ "Tu pourrais envisager d'augmenter tes prix"
- ✅ "Une EURL pourrait être pertinente dans ton cas, à valider avec ton expert-comptable"

### 15.6 — Tools

- `fetch_monthly_metrics(artisan_id, month)` — KPIs
- `fetch_benchmark(metier, departement)` — benchmarks
- `compare_rates(artisan_rate, benchmark)` — écart
- `generate_monthly_analysis(metrics, benchmarks)` — rapport PDF
- `generate_video_capsule(topic)` — capsule 60s F120

### 15.7 — Triggers

**Proactif** : 1er du mois → déclenche analyse (1/mois Starter, ∞ Pro/Business).
**Sollicité** : "Analyse mon mois".

### 15.8 — Exclusions absolues

L'Agent Coach **NE DOIT JAMAIS** :
- Donner un conseil en investissement
- Donner un conseil en optimisation fiscale agressive
- Recommander un changement de statut sans disclaimer
- Faire de projection garantie ("tu gagneras X€")
- Conseiller sur des décisions juridiques (voir avocat)

### 15.9 — KPIs

| KPI | Objectif |
|---|---|
| % analyses ouvertes par artisan | >60% |
| % recommandations suivies | >20% (track impact MRR long terme) |
| NPS Coach | >70 |
| Zéro réclamation légale | 100% |

---

## 16. AGENT 14 V2 — TÉLÉPHONE IA

### 16.1 — Identité

- **Fichier** : `backend/app/agents/agent_telephone.py`
- **Prompt** : `backend/app/prompts/telephone_prompt.py`
- **Modèle** : `claude-sonnet-4-6`
- **Budget/appel** : $0.10
- **Budget allocation** : 2% (réservé post-V1)

### 16.2 — Phase

**V2 — sept-oct 2026 post-launch**. Pas livré en V1 (juin 2026).

### 16.3 — Add-on

**+15€/mois** sur Pro/Pro annuel/Business/Business annuel (pas en Starter).

### 16.4 — Features couvertes

- Décrochage d'appels prospects quand l'artisan ne répond pas
- Prise d'info structurée (nom, téléphone, besoin, adresse, urgence)
- Filtrage intelligent (prospect vs fournisseur vs spam vs perso)
- **F126** (V2) — Télé-dépannage client (arbres décision 550 pannes × 11 métiers)

### 16.5 — Script de transparence AI Act (obligatoire)

Au premier appel (voir `docs/AI-ACT-COMPLIANCE.md` §6.3) :

> *"Bonjour, vous êtes bien chez [Nom Entreprise]. [Prénom] est actuellement indisponible. Je suis son assistant IA, je peux prendre votre demande ou votre message et il vous rappellera dans l'heure. Vous souhaitez continuer ?"*

### 16.6 — Tools V2

- `answer_call(call_audio)` — prise d'appel
- `identify_caller_type(phone_number)` — prospect/fourn/perso/spam
- `take_prospect_info(conversation)` — structuration
- `diagnose_panne(metier, symptoms)` — F126 télé-dépannage
- `create_prospect_card(info)` — fiche auto

### 16.7 — KPIs (V2)

| KPI | Objectif |
|---|---|
| Taux transparency AI annoncée | 100% |
| Accuracy filtrage | >90% |
| Prospects convertis | >20% |
| F126 dépannages résolus sans intervention | >30% |

---

## 17. COMMUNICATION INTER-AGENTS

### 17.1 — Règle stricte : passage par Supervisor

**Interdit** : Agent A appelle directement Agent B.
**Obligatoire** : Agent A émet event → Supervisor route → Agent B.

### 17.2 — Events inter-agents principaux

| Event émis | Émetteur | Récepteur(s) via Supervisor |
|---|---|---|
| `devis.created` | Devis | Planning (capacité), Supervisor (tracking) |
| `devis.sent` | Devis | Relance (schedule J+3) |
| `devis.signed` | Devis | Planning (démarrage), Compta (acompte) |
| `facture.sent` | Devis | Relance (schedule J+15) |
| `facture.paid` | Compta | Réputation (demande avis J+1) |
| `chantier.started` | Planning | Déplacements (tracking GPS) |
| `chantier.closed` | Planning | Compta (marge finale), Réputation (avis) |
| `ticket.scanned` | Compta (via Vision) | Chantier (imputation) |
| `review.received` | Réputation | Site Web (mise en avant) |
| `threshold.approaching` | Fiscalité | Coach (intègre dans analyse mensuelle) |

### 17.3 — Dispatch

Le Supervisor utilise `events.py` avec pattern pub/sub Redis.

```python
# Agent Devis
supervisor.emit("devis.sent", {"devis_id": ..., "client_id": ..., "sent_at": ...})

# Supervisor
def on_devis_sent(event):
    # Schedule Agent Relance dans 3 jours
    queue.enqueue_at(
        datetime.now() + timedelta(days=3),
        agent="relance",
        task={"type": "relance_devis", "devis_id": event["devis_id"]}
    )
```

---

## 18. ESCALATION GLOBALE

### 18.1 — Hiérarchie d'escalation

**Niveau 1 — L'agent gère** : 70% des cas, confiance >60%.

**Niveau 2 — Validation humaine demandée** : l'agent propose, l'artisan valide.

**Niveau 3 — Multi-model review** : pour actions critiques (devis >50K€, mise en demeure, changement statut).

**Niveau 4 — Escalade support humain** : confiance <60% persistante ou demande explicite (F136, voir `docs/SUPPORT-STRATEGY.md`).

### 18.2 — Cas spécifiques de multi-model review

Sur ces cas, 2 modèles valident en parallèle :

| Action | Model 1 (draft) | Model 2 (review) |
|---|---|---|
| Devis > 50 000€ TTC | Opus 4.7 | Sonnet 4.6 (vérif cohérence) |
| Mise en demeure officielle | Haiku 4.5 | Sonnet 4.6 (vérif ton et juridique) |
| Simulation changement statut | Sonnet 4.6 | Opus 4.7 (vérif exhaustivité) |
| Post social viralité potentielle | Haiku 4.5 | Sonnet 4.6 (vérif réputationnel) |
| Réponse avis Google 1⭐ | Haiku 4.5 | Sonnet 4.6 (désamorçage) |

---

## 19. BUDGET ET CIRCUIT BREAKERS

### 19.1 — Caps quotidiens par plan

Voir `docs/SINGLE_SOURCE_OF_TRUTH.md` §4 :

- **Starter** : $0.50/jour
- **Pro / Pro annuel** : $2.00/jour
- **Business / Business annuel** : $5.00/jour

### 19.2 — Circuit breakers par agent

**Déclencheurs** :
- 3 réponses vides consécutives → pause 60s + fallback model chain
- 5 erreurs 5xx consécutives → désactivation agent 5 min + alerte Sentry
- Budget agent dépassé → bascule modèle moins cher

### 19.3 — Fallback model chain

```
claude-opus-4-7 → claude-sonnet-4-6 → claude-haiku-4-5-20251001 
                                     → message "Cerveau en maintenance"
```

### 19.4 — Budget compaction

Si contexte > 50K tokens (historique long) → compaction via Haiku :
- Résume les 30 derniers messages en 1000 tokens
- Garde système prompt + derniers 10 messages en clair
- Injecte résumé dans contexte

---

## 20. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/ARCH.md` | Architecture globale (Supervisor §5, Agents §6) |
| `docs/API.md` | Endpoints REST exposant les agents |
| `docs/MEMORY.md` | Architecture Mem0 + MemPalace utilisée par les agents |
| `docs/MEMORY-STRATEGY.md` | Fallback mémoire |
| `docs/METIER.md` | Constitution BTP injectée dans chaque prompt |
| `docs/COACH-DISCLAIMER.md` | Cadre juridique Coach + Fiscalité |
| `docs/AI-ACT-COMPLIANCE.md` | Audit log obligatoire sur chaque appel agent |
| `docs/SUPPORT-STRATEGY.md` | Escalade humaine (F136) |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` | Modèles Claude, budgets, allocations |
| `docs/FEATURES_COMPLETE.md` | Mapping features ↔ agent |
| `docs/VOICE.md` (à créer) | Pipeline voix utilisé par les agents |
| `docs/PROMPTS.md` (à créer) | Détail des system prompts |
| `docs/TESTS.md` (à créer) | Stratégie tests agents |
| `BUILD_PLAN.md` | Structure fichiers `backend/app/agents/` |

### 20.1 — Références externes

- **Pattern Ouroboros** : https://github.com/razzant/ouroboros
- **Claude API docs** : https://docs.anthropic.com/
- **Mem0 docs** : https://docs.mem0.ai/
- **MemPalace** : https://github.com/MemPalace/MemPalace

---

> **Ce fichier est la constitution des 14 agents + Supervisor de STRUCTORAI.**
> **Règle d'or** : tout nouvel agent (ex: Agent Assurances, Agent SAV, etc.) DOIT être spécifié ici avant implémentation.
> **Numérotation** : les agents sont numérotés de 1 à 14 (V1) + V2 Téléphone = 15ème agent post-launch.
> **Changement de modèle Claude** : DOIT être loggé dans `docs/DECISIONS-LOG.md` + `docs/CHANGELOG.md`.
