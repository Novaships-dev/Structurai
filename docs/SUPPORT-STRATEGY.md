---
name: SUPPORT-STRATEGY
description: Architecture support hybride STRUCTORAI à 3 niveaux (IA 24/7 70% / escalade async <2h 20% / urgence SMS Fabrice <1h 10%). Détection mots-clés, procédures escalade, KPIs à tracker, seuils d'embauche support externe (50/200/500 clients), scripts de réponse humaine pour cas fréquents. Complémentaire de F136 et de la Force 5 (COMMUNICATION.md).
type: operational-runbook
scope: support, agents, ui-mobile, ui-web, admin-dashboard
priority: critical
date: 2026-04-17
version: 1.0
feature: F136 Support hybride IA + humain (V1 Sprint 6)
widget_default: Crisp (alternative Intercom)
---

# docs/SUPPORT-STRATEGY.md — Stratégie de support hybride 24/7

> **Ce fichier est le runbook opérationnel du support STRUCTORAI.**
> Le support n'est pas un département séparé — c'est une extension du cerveau IA.
> Objectif : aucun artisan ne se sent seul, à aucune heure.
> Argument différenciant vs concurrents : **Obat ouvre lundi 9h. STRUCTORAI répond dimanche 22h.**

---

## 0. SOMMAIRE

1. [Philosophie du support STRUCTORAI](#1-philosophie-du-support-structorai)
2. [Architecture 3 niveaux](#2-architecture-3-niveaux)
3. [Niveau 1 — IA 24/7 (70% des cas)](#3-niveau-1--ia-247-70-des-cas)
4. [Niveau 2 — Escalade asynchrone <2h (20% des cas)](#4-niveau-2--escalade-asynchrone-2h-20-des-cas)
5. [Niveau 3 — Urgence SMS <1h (10% des cas)](#5-niveau-3--urgence-sms-1h-10-des-cas)
6. [Détection mots-clés d'escalade](#6-détection-mots-clés-descalade)
7. [Widget support (Crisp par défaut)](#7-widget-support-crisp-par-défaut)
8. [Scripts de réponse pour cas fréquents](#8-scripts-de-réponse-pour-cas-fréquents)
9. [KPIs et dashboard admin](#9-kpis-et-dashboard-admin)
10. [Seuils d'évolution de l'équipe support](#10-seuils-dévolution-de-léquipe-support)
11. [Onboarding du support externalisé](#11-onboarding-du-support-externalisé)
12. [Escalade AI Act — bouton "Demander validation humaine"](#12-escalade-ai-act--bouton-demander-validation-humaine)
13. [Communication de crise et incidents majeurs](#13-communication-de-crise-et-incidents-majeurs)
14. [Base de connaissance support (knowledge base interne)](#14-base-de-connaissance-support-knowledge-base-interne)
15. [Maintenance et évolution](#15-maintenance-et-évolution)

---

## 1. PHILOSOPHIE DU SUPPORT STRUCTORAI

### 1.1 — Le constat initial

Le support client est **le facteur de rétention n°1 chez les artisans BTP** (voir `STRATEGIE-COMPETITIVE.md` Force Obat). L'un des artisans attend 72h pour une réponse, il part. **On joue la rétention sur chaque interaction.**

**Le problème concurrent** :
- Obat : support 4.9/5 unanime, **mais uniquement lundi-vendredi 9h-18h**
- Tolteck : support illimité inclus, mais **pas 24/7**
- Sage Batigest : **téléphone payant**, réponse 24-48h
- Batappli : **peu réactif**

**La réalité artisan** :
- L'artisan finit son chantier à 19h
- Il fait ses devis le soir, **20h-23h**
- Il bloque sur une mention obligatoire → besoin de réponse **maintenant**
- Dimanche matin, il veut préparer sa semaine → besoin de l'app **maintenant**

→ **Personne ne répond** aux artisans en dehors des heures ouvrées. STRUCTORAI change ça.

### 1.2 — Les 3 principes STRUCTORAI

1. **Le cerveau IA EST le support N1** — pas un ajout, pas un fallback
2. **L'humain arrive en 2h max**, en 1h en urgence soir/weekend
3. **L'artisan ne se sent JAMAIS seul** — réponse immédiate même si c'est juste "Je prépare ta réponse, tu l'as dans 1h"

### 1.3 — Message marketing

> *"Un problème à 22h dimanche ? STRUCTORAI répond instantanément et résout 70% des cas. Si c'est un vrai souci, Fabrice te répond sous 2h, même le week-end. Obat ? Ils ouvrent lundi 9h."*

Voir `COMMUNICATION.md` Force 5.

---

## 2. ARCHITECTURE 3 NIVEAUX

### 2.1 — Vue d'ensemble

```
┌─────────────────────────────────────────────────────────────┐
│  ARTISAN pose une question dans le chat / WhatsApp         │
└────────────────────┬────────────────────────────────────────┘
                     │
         ┌───────────▼───────────┐
         │  NIVEAU 1 — IA 24/7  │
         │   (Supervisor)        │
         └───────────┬───────────┘
                     │
            ┌────────┴────────┐
            │ IA résout ?     │
            └────────┬────────┘
                     │
         ┌───────────┼───────────┐
         │           │           │
       Oui         Non         Urgence
     (70%)       (20%)         (10%)
         │           │           │
         ▼           ▼           ▼
  ┌─────────┐ ┌──────────┐ ┌──────────┐
  │ Réponse │ │ NIVEAU 2 │ │ NIVEAU 3 │
  │ instant │ │ Escalade │ │ SMS      │
  │         │ │ <2h      │ │ Fabrice  │
  │         │ │ (Crisp + │ │ <1h      │
  │         │ │ humain)  │ │ 24/7     │
  └─────────┘ └──────────┘ └──────────┘
```

### 2.2 — Répartition cible des volumes

| Niveau | Part des demandes | Délai de réponse | Canal principal |
|---|---|---|---|
| N1 — IA | **70%** | < 10 secondes | Chat in-app / WhatsApp |
| N2 — Async humain | **20%** | < 2h en journée | Widget Crisp + email |
| N3 — Urgence | **10%** | < 1h (24/7) | SMS Fabrice |

---

## 3. NIVEAU 1 — IA 24/7 (70% DES CAS)

### 3.1 — Capacité de l'IA comme support N1

**Le cerveau IA n'est pas un chatbot. C'est un vrai LLM** (Claude + 14 agents) avec accès à :
- Documentation produit intégrée (comment faire X dans l'app)
- Base de connaissances BTP (`docs/METIER.md`, 11 référentiels)
- Mem0 artisan (ce qu'il a déjà fait)
- MemPalace (conversations passées)
- Tools : peut EFFECTUER des actions, pas juste expliquer

### 3.2 — Types de demandes résolues par l'IA

| Catégorie | Exemples | Résolution IA |
|---|---|---|
| **Comment faire X ?** | "Comment je fais une facture d'acompte ?" | Le cerveau explique ET fait |
| **Question métier BTP** | "Quelle TVA pour une SDB complète ?" | Réponse via `docs/METIER.md` |
| **Question réglementaire** | "Je dois mentionner quoi sur mon devis ?" | Les 47 mentions détaillées |
| **Corriger un document** | "Change le prix de la ligne 3 à 380€" | Action directe + mémorisation |
| **Trouver une info** | "Quand j'ai fait mon dernier chantier Martin ?" | Recherche Mem0 + MemPalace |
| **Aide à la décision** | "Est-ce que j'accepte ce prospect ?" | Analyse carnet + suggestion Coach |
| **Problèmes techniques simples** | "Je n'arrive pas à envoyer le devis" | Diagnostic + solution |
| **Clarification fonctionnelle** | "À quoi sert l'Agent Réputation ?" | Explication produit |

### 3.3 — Le cerveau DOIT détecter ses propres limites

**Règle critique** : quand le cerveau n'est PAS sûr à >60%, il propose l'escalade au lieu d'inventer.

**Exemples de détection** :
- "Je ne suis pas sûr de la réponse, je peux te mettre en contact avec Fabrice ?"
- "Cette question est complexe, un humain va te répondre dans les 2h, c'est OK ?"
- "Je vois que tu es bloqué depuis 3 messages, je passe le relais à l'équipe ?"

**Implémentation dans le system prompt** (voir `prompts/supervisor_prompt.py`) :

```python
ESCALATION_RULES = """
Quand proposer l'escalade vers support humain :
1. Confiance < 60% sur la réponse
2. L'artisan a posé 3+ fois la même question sans résolution
3. Mots-clés urgence détectés (voir §6)
4. Sujet hors périmètre (demande de remboursement, litige, etc.)
5. Bug technique qui ne peut être résolu par l'IA seul
6. Demande explicite de l'artisan ("je veux parler à un humain")

Format d'escalade :
"Je vois que je n'arrive pas à te débloquer complètement. 
Je passe le relais à [Fabrice/équipe support] qui va te 
répondre dans [2h en journée / 1h si urgent]. Tu continues 
pendant que je prépare ta demande pour eux ?"
"""
```

### 3.4 — Mesure du taux de résolution IA

**Méthode** : chaque conversation est flaggée par l'artisan à la fin :
- ✅ "Problème résolu" → comptabilisé comme N1 réussi
- ⚠️ "Toujours bloqué" → escalade automatique N2

**Sondage après 7 messages sans résolution** :
> *"Tu es bloqué sur ce sujet depuis un moment. Tu veux que je transfère à un humain ?"*

---

## 4. NIVEAU 2 — ESCALADE ASYNCHRONE <2H (20% DES CAS)

### 4.1 — Déclencheurs d'escalade N2

**Automatiques** :
- Confiance IA < 60% sur 2 tentatives
- 3+ messages sans résolution sur le même sujet
- Sujet identifié hors périmètre IA (voir §4.3)

**Manuels** :
- L'artisan clique "Demander validation humaine"
- L'artisan écrit "humain" / "personne" / "parler à quelqu'un"

### 4.2 — Procédure d'escalade N2

1. **Le cerveau annonce l'escalade** à l'artisan :
   > *"Je transfère ta demande à l'équipe. Tu as la réponse dans les 2h en journée. Je te notifie dès qu'ils répondent."*

2. **Création du ticket Crisp** automatique avec :
   - Résumé de la conversation (auto-généré par Claude Haiku)
   - Contexte artisan (métier, statut, plan, chantier en cours)
   - Problème identifié
   - Tentatives de résolution IA

3. **Notification Fabrice/support** :
   - Email + push Slack
   - Priorité : normale

4. **L'humain répond** dans Crisp, l'artisan reçoit dans le chat STRUCTORAI

5. **Feedback de l'artisan** sur la résolution :
   - ✅ "C'est résolu" → fermeture ticket + NPS
   - ⚠️ "Pas tout à fait" → suite

### 4.3 — Sujets typiques en N2

| Sujet | Pourquoi IA n'est pas suffisante |
|---|---|
| Demande de remboursement | Décision humaine + Stripe refund manuel |
| Litige artisan/client | Jugement + empathie humaine |
| Bug technique non reproduit | Investigation dev + logs Sentry |
| Demande d'une nouvelle feature | Tracker produit (Canny/Trello) |
| Question fiscale complexe | Redirection expert-comptable |
| Facturation / changement de plan | Modification manuelle Stripe |
| Récupération données supprimées | Restore backup (Fabrice) |
| Problème d'intégration (import concurrent, WhatsApp) | Debug technique |

### 4.4 — SLA strict

**Objectif contractuel (CGV Business)** : réponse humaine en **< 2h** pendant les **heures ouvrées** (lundi-vendredi 9h-19h heure Paris).

**Hors heures ouvrées** : réponse **< 4h** ou matin du lendemain ouvré (sauf urgence → N3).

**Mesure** : tracking automatique dans Crisp + dashboard admin.

---

## 5. NIVEAU 3 — URGENCE SMS <1H (10% DES CAS)

### 5.1 — Qu'est-ce qu'une urgence ?

**Critères** :
- Bug bloquant qui empêche l'artisan de travailler (devis ne part pas pour un client qui attend)
- Client mécontent menaçant d'annuler un chantier
- Problème facturation avec risque financier immédiat
- Demande d'aide au milieu d'un rendez-vous client (live)
- Mot-clé explicite : "URGENT", "BLOQUÉ", "AIDE"

**Critères négatifs** (PAS une urgence) :
- Question générale sur le produit
- Bug non bloquant
- Demande d'amélioration feature

### 5.2 — Détection automatique urgence

Le cerveau analyse en temps réel chaque message pour détecter :

**Mots-clés triggers** (voir §6 pour liste complète) :
- "urgent", "urgence", "tout de suite"
- "bloqué", "panne", "ne fonctionne pas"
- "client mécontent", "colère", "annuler"
- "aide", "help", "sos"

**Patterns comportementaux** :
- L'artisan envoie 5+ messages en <2 min (signe de panique)
- L'artisan revient 3× dans la journée sur le même problème non résolu
- L'artisan écrit en majuscules

### 5.3 — Procédure urgence N3

1. **Détection immédiate** (par le Supervisor ou par le mot-clé)

2. **Ack automatique IA** à l'artisan :
   > *"Je détecte que c'est urgent. Fabrice reçoit un SMS à l'instant. Il te répond dans l'heure même si c'est le soir ou le week-end."*

3. **SMS Twilio envoyé à Fabrice** avec :
   - Nom artisan + téléphone
   - Résumé du problème (1-2 phrases)
   - Lien direct vers la conversation Crisp
   - Niveau d'urgence (1-5)

4. **Pendant l'attente**, l'IA continue à aider :
   - Elle propose des workarounds
   - Elle reste disponible

5. **Fabrice répond** en <1h :
   - SMS → appel direct si critique
   - Ou message dans Crisp si non critique mais urgent

6. **Fermeture du ticket** après résolution + NPS

### 5.4 — Horaires couvertures N3

| Plage horaire | Responsable | Délai SLA |
|---|---|---|
| 9h-19h lun-ven | Fabrice | <30 min |
| 19h-23h lun-ven | Fabrice | <1h |
| 9h-23h sam-dim | Fabrice | <1h |
| 23h-9h lun-dim | Fabrice (SMS silencieux, pas d'appel) | Réponse au réveil + alerte "Je vais revenir vers toi dans la matinée" |

### 5.5 — Évolution à 50+ clients

Quand 50+ clients payants :
- Ajouter un **support de garde** externalisé (freelance FR)
- Rotation weekend Fabrice / freelance
- Maintenir SLA <1h même pendant les congés Fabrice

---

## 6. DÉTECTION MOTS-CLÉS D'ESCALADE

### 6.1 — Liste exhaustive des mots-clés

**Fichier** : `backend/app/services/support_triggers.py`

**Niveau 3 — Urgence (SMS immédiat)** :

```python
URGENCY_KEYWORDS_L3 = [
    # Demande explicite
    "urgent", "urgence", "maintenant", "tout de suite",
    "aide !", "aide svp", "sos", "help !",
    
    # Blocage critique
    "bloqué", "bloque", "panne", "crash",
    "ne fonctionne pas", "marche pas", "casse",
    "erreur", "bug critique",
    
    # Panique client
    "client mécontent", "client en colère", "client annule",
    "perdre le chantier", "perds le chantier",
    "client menace", "plainte",
    
    # Financier critique
    "prélèvement erreur", "double facturation",
    "paiement refusé urgent",
    
    # Signes physiques panique (majuscules)
    # Détection : ratio majuscules > 60% + >3 mots
]
```

**Niveau 2 — Escalade normale (ticket Crisp)** :

```python
ESCALATION_KEYWORDS_L2 = [
    # Demande humain explicite
    "humain", "personne", "quelqu'un",
    "parler à un humain", "pas un bot",
    "vrai personne", "fabrice",
    
    # Insatisfaction modérée
    "pas content", "déçu", "pas satisfait",
    "ça marche pas bien", "problème",
    
    # Sujets hors IA
    "remboursement", "rembourser",
    "annuler mon abonnement", "désabonner",
    "changer de plan", "upgrade",
    "reset password", "mot de passe perdu",
    
    # Litiges
    "litige", "tribunal", "avocat",
    "signaler", "plainte",
    
    # Bug technique
    "bug", "crash", "erreur",
    # (sans le contexte urgent du N3)
]
```

### 6.2 — Logique de matching

```python
def detect_escalation_level(message: str, context: dict) -> int:
    """
    Retourne le niveau d'escalade nécessaire :
    0 = aucune (IA gère)
    2 = escalade N2 (ticket Crisp)
    3 = urgence N3 (SMS Fabrice)
    """
    msg_lower = message.lower()
    
    # N3 : urgence détectée
    for keyword in URGENCY_KEYWORDS_L3:
        if keyword in msg_lower:
            return 3
    
    # Détection majuscules (panique)
    if len(message) > 20:
        upper_ratio = sum(1 for c in message if c.isupper()) / len(message)
        if upper_ratio > 0.6:
            return 3
    
    # N2 : escalade normale
    for keyword in ESCALATION_KEYWORDS_L2:
        if keyword in msg_lower:
            return 2
    
    # Détection patterns comportementaux
    if context.get("messages_last_2min", 0) >= 5:
        return 3  # Panique
    
    if context.get("same_topic_attempts", 0) >= 3:
        return 2  # IA n'y arrive pas
    
    if context.get("ai_confidence", 1.0) < 0.6:
        return 2  # IA pas confiante
    
    return 0  # IA gère
```

### 6.3 — Ajustements continus

**Les mots-clés évoluent**. À revoir **tous les 2 mois** :
- Analyse des conversations mal triées (urgence pas détectée)
- Analyse des escalades non nécessaires (fausse urgence)
- Ajout de nouveaux mots-clés émergents

**Stockage** : `data/support/escalation_keywords.json` versionné dans Git.

---

## 7. WIDGET SUPPORT (CRISP PAR DÉFAUT)

### 7.1 — Pourquoi Crisp

| Critère | Crisp | Intercom | Zendesk |
|---|---|---|---|
| Prix <100 sessions/mois | **0€** | ~29$/mois | ~19$/mois |
| Prix 100-1K sessions | **~25€/mois** | ~74$/mois | ~55$/mois |
| Multi-canal (chat + email + WhatsApp) | ✅ | ✅ | ✅ |
| Équipe FR / support FR | ✅ (Nantes) | ❌ | ❌ |
| API simple d'intégration | ✅ | ✅ | ⚠️ |
| Chatbot + widget personnalisable | ✅ | ✅ | ✅ |

**Décision** : **Crisp** par défaut — gratuit au démarrage, français, intégration simple, prix raisonnable à l'échelle.

**Fallback** : Intercom si besoin de features enterprise au-delà de 500 clients.

### 7.2 — Intégration technique

**Stack** :
- Package : `@crisp-im/react-crisp` (mobile) + script widget web
- Config : variable env `CRISP_WEBSITE_ID`
- Identification user : passage `crisp.push(["set", "user:email", [email]])` à la connexion
- Métadonnées : plan, agent IA en cours, dernier devis, dernier chantier

### 7.3 — Configuration automatique

Quand l'artisan ouvre Crisp, il voit automatiquement :
- Pré-rempli : nom, email, téléphone, métier, plan
- Contexte : dernière conversation IA résumée (derniers 5 messages)
- Tags : urgence, catégorie, type de plan

**But** : le support humain ne repart pas de zéro, il voit tout le contexte.

### 7.4 — Routage interne

**Règles de routage Crisp** :

| Tag | Destination | Priorité |
|---|---|---|
| `urgent` | Fabrice (SMS + push) | P1 |
| `business` | Fabrice | P2 (client premium) |
| `technical` | Fabrice (puis dev support) | P2 |
| `billing` | Fabrice (Stripe manual) | P2 |
| `general` | Support externalisé | P3 |
| `feature_request` | Trello produit (pas réponse immédiate) | P4 |

### 7.5 — Template de réponse pré-rempli

Dans Crisp, créer des **templates** pour les cas fréquents (voir §8) pour accélérer les réponses du support.

---

## 8. SCRIPTS DE RÉPONSE POUR CAS FRÉQUENTS

### 8.1 — Pattern de réponse recommandé

**Structure** :
1. **Empathie** (reconnaître le problème)
2. **Solution immédiate** (si possible)
3. **Action** (ce que le support fait / ce que l'artisan doit faire)
4. **Redirection** vers le bon outil / contact
5. **Follow-up** (je te contacte dans X)

### 8.2 — Scripts par catégorie

#### Script 1 — "Mon devis n'est pas bon / client refuse"

```
Salut [prénom],

Je comprends, c'est frustrant quand un client refuse ton devis. 
Quelques questions pour t'aider vite :

1. Le client t'a donné un feedback concret (prix trop haut, 
   postes manquants, délai trop long) ?
2. Tu as un devis concurrent à me montrer ? Je peux faire 
   l'analyse via la Détection devis concurrent.
3. Tu veux que je regarde le devis avec toi pour identifier 
   les points d'amélioration ?

Envoie-moi le PDF ou une capture, je regarde avec toi maintenant.

[Prénom Fabrice]
```

#### Script 2 — "Demande de remboursement"

```
Salut [prénom],

Merci d'avoir essayé STRUCTORAI. Pour le remboursement, 
voici comment ça se passe :

- **Période d'essai 30 jours** : remboursement intégral 
  automatique, pas de justification demandée
- **Après 30 jours** : au cas par cas si tu as un motif 
  (bug bloquant, changement de situation). Contacte-nous 
  et on regarde ensemble.

Pour déclencher le remboursement, réponds-moi :
1. La raison (tu n'es pas obligé d'être précis)
2. Ton email de compte

Je traite la demande sous 24h.

Pas de rancune — si tu reviens, tes données sont conservées 
30 jours.

[Prénom Fabrice]
```

#### Script 3 — "Bug technique reproduit"

```
Salut [prénom],

Désolé pour le bug. Pour que je le résolve vite, j'ai 
besoin de quelques infos :

1. Sur quel appareil tu es (iPhone/Android/ordi) ?
2. Quelle version de l'app (Settings → À propos) ?
3. Quelle action tu essayais de faire ?
4. Tu peux prendre une capture d'écran ou une vidéo de 10s 
   si le bug se reproduit ?

Pendant que je regarde, voici un workaround si ça t'aide :
[SI POSSIBLE]

Je te tiens au courant dès que c'est fixé — normalement 
sous 24h en semaine.

[Prénom Fabrice]
```

#### Script 4 — "Question fiscale complexe"

```
Salut [prénom],

C'est une question fiscale pointue. Je ne peux pas te 
donner un conseil définitif là-dessus — seul un 
expert-comptable peut trancher sans risque.

Ce que je peux te donner :
- Les éléments factuels (règles générales)
- Les chiffres de ton activité pour préparer ta discussion 
  avec ton expert-comptable
- Le calendrier fiscal correspondant

Si tu n'as pas encore d'expert-comptable, on peut t'en 
recommander un dans ta région. Dis-moi.

[Prénom Fabrice]
```

#### Script 5 — "Client très mécontent, menace de partir"

```
Salut [prénom],

OK je comprends, on va désamorcer ça ensemble. Raconte-moi 
ce qui s'est passé :

1. Qu'est-ce qui le met en colère exactement ?
2. Depuis combien de temps c'est tendu ?
3. Qu'est-ce qu'il demande concrètement ?

Pendant que tu me racontes, j'ouvre son dossier (historique 
devis, facture, conversations). On va trouver le meilleur 
angle pour calmer le jeu.

Si c'est vraiment tendu, je peux appeler avec toi (3-way 
call) ou préparer un message que tu envoies. Tu préfères 
quoi ?

[Prénom Fabrice]
```

#### Script 6 — "Comment faire X dans l'app ?"

```
Salut [prénom],

Pour faire [X], 2 options :

**Option 1 — Via le chat IA** (le plus rapide)
Dis simplement au cerveau : "[phrase type]". Il va le faire 
pour toi.

**Option 2 — Manuellement**
1. [Étape 1]
2. [Étape 2]
3. [Étape 3]

Une capsule vidéo de 60s est disponible dans Settings → 
Aide → [Nom capsule] si tu préfères voir.

Ça t'aide ?

[Prénom Fabrice]
```

#### Script 7 — "Je veux annuler mon abonnement"

```
Salut [prénom],

Compris. Avant d'annuler, je me permets une question : 
qu'est-ce qui ne te convient pas ? Ton feedback nous aide 
à améliorer le produit.

(Tu n'es pas obligé de répondre, c'est juste pour comprendre.)

Pour l'annulation :
1. Settings → Abonnement → Annuler
2. L'annulation prend effet à la fin de la période en cours
3. Tes données restent accessibles 30 jours (export possible)
4. Si tu changes d'avis, tu peux réactiver à tout moment

Si c'est un problème de prix, on a aussi le Starter gratuit 
qui te permet de garder l'accès aux fonctions essentielles.

Pas de rancune ! N'hésite pas à revenir si l'outil te manque.

[Prénom Fabrice]
```

### 8.3 — Stockage des templates

**Crisp** : templates en direct dans l'interface (accessibles en 2 clics).

**Versionné Git** : `docs/support/scripts_reponse.md` (source de vérité).

**Mise à jour** : tous les 2 mois en fonction des nouvelles situations rencontrées.

---

## 9. KPIS ET DASHBOARD ADMIN

### 9.1 — Métriques à tracker dès J1

| KPI | Objectif | Fréquence |
|---|---|---|
| **Taux résolution IA (N1)** | **>70%** | Quotidien |
| Temps réponse moyen N1 | <10 secondes | Quotidien |
| Temps réponse moyen N2 | **<2h** en journée | Quotidien |
| Temps réponse moyen N3 | **<1h** (24/7) | Quotidien |
| Taux escalade N2 → N3 | <5% | Hebdo |
| Volume tickets / semaine | Référence + croissance | Hebdo |
| **NPS support** | **>60** | Mensuel |
| CSAT par ticket | >4.5/5 | Par ticket |
| Taux de réouverture de ticket | <10% | Hebdo |
| Temps moyen de résolution totale | <24h | Hebdo |
| % tickets Crisp avec contexte complet | >95% | Hebdo |
| Coût support / artisan / mois | <2€ (<50 clients) | Mensuel |

### 9.2 — Dashboard admin

**Écran** : `/admin/support-dashboard` accessible Fabrice + support

**Visualisations** :
- **Cumul IA vs humain** (graph temps)
- **Temps moyen de réponse par niveau**
- **Top 10 sujets escaladés** (pour identifier où l'IA doit être améliorée)
- **Heatmap heures d'activité** (quand les artisans écrivent vraiment)
- **Artisans avec 3+ tickets ouverts** (alerte churn)
- **Tickets non clôturés >24h** (alerte SLA)

### 9.3 — Rapports hebdomadaires

Email automatique à Fabrice chaque lundi matin :
- Résumé KPIs semaine précédente
- Top 5 problèmes les plus fréquents (IA devrait mieux les gérer)
- Artisans à risque de churn (tickets répétés sans résolution)
- Succès stories (problèmes bien résolus à partager en équipe)

### 9.4 — Amélioration continue

**Cycle 2 mois** :
1. Analyse KPIs
2. Si taux résolution IA < 70% → améliorer system prompts Supervisor
3. Si NPS support < 60 → revoir scripts de réponse
4. Si tickets répétés même sujet → créer FAQ + capsule vidéo
5. Si volume explose → déclencher seuil §10

---

## 10. SEUILS D'ÉVOLUTION DE L'ÉQUIPE SUPPORT

### 10.1 — Paliers de croissance

| Nb clients payants | Équipe support | Coût mensuel | Horaires |
|---|---|---|---|
| **0-50** | Fabrice seul | 0€ (déjà payé par Fabrice) | 9h-23h 7j/7 |
| **50-200** | Fabrice + 1 freelance FR | 500-800€/mois | 9h-23h 7j/7 avec rotation weekend |
| **200-500** | Fabrice + 2 personnes | ~2.5-3K€/mois | 8h-23h 7j/7 |
| **500-1000** | Équipe support dédiée (3 pers) | ~5K€/mois | 24/7 avec rotation |
| **1000+** | Équipe 5+ personnes + manager support | ~10K€+/mois | 24/7 global |

### 10.2 — Profil freelance idéal (50-200 clients)

**Compétences requises** :
- Français natif (écrit impeccable)
- Expérience support SaaS (minimum 1 an)
- À l'aise avec les outils Crisp/Intercom
- Connaissance minimale du BTP (un plus majeur)
- Empathie et patience démontrables

**Où recruter** :
- **Malt** (freelances support FR, 150-250€/jour)
- **Upwork** (pool international, moins cher mais risque qualité)
- **LinkedIn** (recherche "support client SaaS")
- **Communautés Indie Hackers FR**

**Format** :
- 4h/jour minimum (ex: 14h-18h pour couvrir après-midi quand Fabrice est en build)
- Puis expansion selon volumes
- 500-800€/mois pour 4h/jour × 20 jours ouvrés = 80h

### 10.3 — Onboarding freelance (semaine 1)

Voir §11 pour détail complet.

### 10.4 — Décisions de staffing

**Déclencheurs d'embauche** :
- Volume tickets > 200/semaine → embauche freelance obligatoire
- NPS support < 50 → renforcement équipe
- Fabrice surchargé (>20h/semaine sur support) → embauche
- Un week-end où Fabrice est injoignable = risque critique → couverture weekend

---

## 11. ONBOARDING DU SUPPORT EXTERNALISÉ

### 11.1 — Programme semaine 1

**Jour 1 — Découverte produit**
- Tour de l'app (2h avec Fabrice)
- Lecture : `README.md`, `PRODUCT_CONTEXT.md`, `FEATURES_COMPLETE.md`
- Cas d'usage : 10 artisans types (persona complets)

**Jour 2 — Métier BTP**
- Lecture : `docs/METIER.md` (constitution BTP)
- TVA, mentions obligatoires, Factur-X basics
- Vocabulaire BTP (SDB, gros œuvre, second œuvre, etc.)

**Jour 3 — Outils support**
- Setup Crisp
- Accès dashboard admin (en read-only)
- Formation sur les scripts de réponse (§8)
- Formation AI literacy (obligatoire AI Act, voir `docs/AI-ACT-COMPLIANCE.md` §7.4)

**Jour 4 — Shadowing**
- Observation Fabrice sur 5 tickets réels
- Questions réponses libres

**Jour 5 — Premiers tickets**
- Prise en main tickets P3 (général, non critique)
- Review Fabrice de chaque réponse avant envoi
- Debriefing fin de journée

### 11.2 — Semaine 2 — autonomie progressive

- Tickets P3 en autonomie (review a posteriori)
- Tickets P2 en shadowing
- 1 synchro quotidienne Fabrice (15 min)

### 11.3 — Fin semaine 2 — évaluation

Critères de validation :
- [ ] Qualité de réponse (ton, précision, empathie)
- [ ] Respect SLA 2h en journée
- [ ] Taux de réouverture < 10%
- [ ] Connaissance produit (test interne)
- [ ] AI literacy validée

Si OK → autonomie complète P2 + P3.
Si problèmes → semaine 3 supplémentaire avec coaching renforcé.

### 11.4 — Documentation à fournir

Package onboarding dans `/docs/onboarding-support/` :
- `guide_support.md` — ce fichier
- `scripts_reponse.md` — §8
- `metier_crash_course.md` — 20 pages BTP essentials
- `produit_tour.md` — 30 fonctions principales
- `outils_quotidiens.md` — Crisp, dashboard admin, Stripe
- `escalade_urgence.md` — quand appeler Fabrice

---

## 12. ESCALADE AI ACT — BOUTON "DEMANDER VALIDATION HUMAINE"

### 12.1 — Obligation AI Act

Selon `docs/AI-ACT-COMPLIANCE.md` Art. 14 et 26, STRUCTORAI DOIT offrir à l'utilisateur la possibilité de demander une **validation humaine** de toute décision IA importante.

### 12.2 — Où le bouton est affiché

**Partout dans l'app** :
- Petit bouton 👤 en bas à droite du chat (toujours visible)
- Plus proéminent sur :
  - Chaque analyse de l'Agent Coach Business
  - Chaque alerte fiscale critique
  - Chaque décision importante (génération devis > 5 000€, mise en demeure, etc.)

### 12.3 — Qu'est-ce qui se passe quand on clique

1. Ouverture de Crisp avec contexte pré-rempli
2. Tag automatique `ai_oversight_request`
3. Priorité P2 (dans les 2h)
4. Message pré-rempli :

```
[MESSAGE SYSTÈME AUTOMATIQUE]

L'artisan demande une validation humaine sur la décision IA 
suivante :

Type : [Analyse Coach / Devis généré / Relance envoyée / etc.]
Date : [TIMESTAMP]
Modèle utilisé : [claude-opus-4-7 / claude-sonnet-4-6 / etc.]
Score de confiance : [🟢 / 🟡 / 🔴]

Contexte de la décision :
[RÉSUMÉ AUTO]

Raison de la demande par l'artisan :
[CHAMP LIBRE — à remplir par l'artisan]
```

### 12.4 — Comportement du support humain

Le support DOIT :
1. **Valider** ou **invalider** la décision IA
2. **Expliquer** sa validation/invalidation
3. **Logger** dans `ai_decisions` (colonne `human_validated`)
4. Si invalidation → **corriger l'action** (annuler envoi, corriger devis, etc.)

### 12.5 — Fréquence attendue

Cible : **<1% des interactions** devraient nécessiter "Demander validation humaine". Si >5%, c'est que l'IA ne fait pas correctement son travail → review system prompts.

---

## 13. COMMUNICATION DE CRISE ET INCIDENTS MAJEURS

### 13.1 — Types d'incidents support

| Type | Exemples | Protocole |
|---|---|---|
| **Incident produit** | Bug bloquant, outage API Claude, panne Supabase | Voir `docs/MEMORY-STRATEGY.md` §7 |
| **Incident sécurité** | Fuite données, compromission compte artisan | Voir `SECURITE.md` |
| **Incident réputation** | Post négatif viral, bad press | Voir ci-dessous §13.2 |
| **Incident juridique** | Mise en demeure client, contrôle AI Office | Avocat + voir `docs/AI-ACT-COMPLIANCE.md` |

### 13.2 — Protocole "post négatif viral"

Si un artisan (ou un concurrent) publie un post critique qui gagne en traction (Facebook, LinkedIn, forums) :

1. **Détection** : alertes Google + Mention.com (~10€/mois)
2. **Ne JAMAIS répondre à chaud** — respirer 30 min
3. **Analyse** : le fond est-il légitime ? ou c'est un concurrent ?
4. **Réponse publique** : empathie + fact check + proposition de résolution en DM
5. **DM au posteur** : résolution privée (remboursement, correction, etc.)
6. **Post-mortem** : comment éviter que ça se reproduise

**Règle d'or** : ne jamais se justifier à chaud. Toujours proposer la solution en privé d'abord.

### 13.3 — Signalement incident AI Act (Art. 73)

Si incident grave détecté (décision IA à impact significatif, bug IA non résolu > 48h) :
- Signalement à l'AI Office EU **sous 72h**
- Documentation complète dans `ai_decisions`
- Post-mortem interne dans `docs/post-mortems/`

---

## 14. BASE DE CONNAISSANCE SUPPORT (KNOWLEDGE BASE INTERNE)

### 14.1 — Structure

**Dossier** : `docs/support/knowledge-base/`

```
docs/support/knowledge-base/
├── README.md                          # Index général
├── faq-generale.md                    # FAQ toutes catégories confondues
├── metier-btp/
│   ├── tva.md
│   ├── mentions-obligatoires.md
│   ├── factur-x.md
│   └── calendrier-fiscal.md
├── produit/
│   ├── setup-initial.md
│   ├── devis-avance.md
│   ├── integration-concurrents.md
│   ├── gamification.md
│   └── agents-ia.md
├── facturation/
│   ├── plans-et-tarifs.md
│   ├── changement-plan.md
│   ├── remboursement.md
│   └── factures-structorai.md
├── technique/
│   ├── bugs-connus.md                 # Liste des bugs en cours + workarounds
│   ├── performance.md
│   └── integrations.md
└── crise/
    ├── outage-claude-api.md
    ├── outage-supabase.md
    └── incident-securite.md
```

### 14.2 — FAQ la plus fréquente

À pré-construire AVANT launch (pour alimenter l'IA + le support) :

**Top 20 FAQ attendues** :
1. Comment créer mon premier devis ?
2. Comment scanner un ticket de caisse ?
3. Quelle TVA appliquer pour une rénovation SDB ?
4. Comment activer la signature électronique ?
5. Comment envoyer un devis par WhatsApp ?
6. Le cerveau IA se trompe sur un prix, que faire ?
7. Comment ajouter un employé (plan Business) ?
8. Comment importer mes clients depuis Obat ?
9. Comment changer de plan ?
10. Combien coûte le plan Lifetime ?
11. Comment fonctionne le parrainage ?
12. L'app fonctionne-t-elle hors ligne ?
13. Comment exporter ma compta vers mon expert-comptable ?
14. Mes données sont-elles vraiment en France ?
15. Comment utiliser l'Agent Coach ?
16. Comment activer les relances automatiques ?
17. Comment prendre une photo de chantier qui sert au devis ?
18. Comment le cerveau IA connaît mes prix ?
19. Je veux annuler mon abonnement, comment faire ?
20. Comment contacter le support humain ?

**Chaque FAQ** = article 100-300 mots + vidéo 60s (voir F120 capsules).

### 14.3 — Lien avec l'IA

La base de connaissance support alimente également le cerveau IA (RAG) :
- L'IA peut citer directement ces articles
- L'artisan peut demander "comment je fais pour X ?" et l'IA donne la réponse détaillée
- Synchronisation : quand le support écrit une nouvelle réponse, elle alimente la base

---

## 15. MAINTENANCE ET ÉVOLUTION

### 15.1 — Revue hebdomadaire

Chaque lundi (Fabrice + support freelance si applicable) :
- Lecture du rapport automatique de la semaine
- Identification des 3 sujets les plus problématiques
- Actions correctives (améliorer prompt IA, créer FAQ, update script)

### 15.2 — Revue trimestrielle

Tous les 3 mois :
- Audit complet des KPIs (objectifs vs réalité)
- Revue des scripts de réponse
- Revue des mots-clés d'escalade (§6)
- Interview artisans à risque churn (5-10 appels)
- Réévaluation besoin de staffing (§10)

### 15.3 — Revue annuelle

- Benchmark concurrent (comment Obat/Tolteck/etc. gèrent leur support en 2026-2027)
- Review complète de l'architecture 3 niveaux
- Évolution widget (Crisp → Intercom si scale > 500 clients)
- Formation AI literacy mise à jour

### 15.4 — Indicateurs de santé à long terme

Ces indicateurs doivent rester stables ou s'améliorer :
- Taux résolution IA ≥ 70%
- NPS support ≥ 60
- Temps moyen N2 ≤ 2h
- Temps moyen N3 ≤ 1h
- Churn lié à support ≤ 10% du churn total

Si dégradation → post-mortem + actions correctives.

---

## 16. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/METIER.md` | Base de connaissance métier utilisée par IA + support humain |
| `docs/COACH-DISCLAIMER.md` | Cadre Coach — redirections vers pro qualifié |
| `docs/AI-ACT-COMPLIANCE.md` | Supervision humaine obligatoire, bouton "Demander validation" |
| `docs/MEMORY-STRATEGY.md` | Communication en cas d'incident mémoire |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` | KPIs support, budget support externalisé |
| `FEATURES.md` F136 | Feature Support hybride IA + humain |
| `COMMUNICATION.md` Force 5 | Argument marketing "Support IA 24/7 + humain 2h" |
| `COUT_REEL.md` | Budget Crisp (25€/mois 50+ clients), support externalisé (500-800€/mois) |
| `BUILD_PLAN.md` Sprint 6 | Implémentation widget Crisp + détection mots-clés |
| `PRODUCT_CONTEXT.md` §Architecture Support | Résumé 3 niveaux |
| `STRATEGIE-COMPETITIVE.md` Force Obat | Analyse support concurrent |

---

> **Ce fichier est le runbook du support STRUCTORAI.**
> **Objectif ultime** : l'artisan ne doit JAMAIS se sentir seul. Réponse immédiate (IA) dans 70% des cas. Humain <2h sinon. <1h en urgence 24/7.
> **Argument de vente différenciant** : quand Obat ouvre le lundi 9h, STRUCTORAI répond le dimanche soir.
> **Métriques non négociables** : 70% résolution IA, 2h réponse humaine, NPS 60+.
