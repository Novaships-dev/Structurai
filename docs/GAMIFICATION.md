---
name: GAMIFICATION
description: Système complet de gamification de STRUCTORAI. Rend l'onboarding et l'usage quotidien engageants pour des artisans non-tech. Système XP + niveaux (Débutant → Confirmé → Expert → Maître), quêtes onboarding conversationnelles (pas de formulaires), ~50 badges organisés par catégorie (devis, compta, réputation, prospection, fidélité, équipe), streak quotidien 🔥, classement régional opt-in, récompenses réelles (codes promo, mois offerts, accès V2). Intégration avec F114 parrainage (badge Ambassadeur), F117 score "cerveau me connaît" (anti-churn), F120 capsules formation contextuelles. Objectif produit : rendre l'app addictive et faciliter l'adoption par des artisans qui n'ont pas l'habitude d'utiliser des apps.
type: product-feature-gamification
scope: mobile-ui, backend-gamification-service, retention-engagement
priority: important
date: 2026-04-18
version: 1.0
features_covered: F80-F86 (gamification) + F114 (parrainage) + F117 (score connaissance) + F120 (capsules)
total_badges: ~50
total_quests: ~25
levels: 4 (Débutant, Confirmé, Expert, Maître)
---

# docs/GAMIFICATION.md — Système gamification STRUCTORAI

> **Ce fichier est la spec du système de gamification complet.**
> **Différenciation produit** : l'artisan BTP n'a pas l'habitude d'utiliser des apps. La gamification est ce qui fait la différence entre une app utilisée et une app abandonnée.
> Reference : `FEATURES.md` F75-F77 (gamification enrichie Fabrice) + `docs/FEATURES_COMPLETE.md` F80-F86.
> Migration : `docs/MIGRATIONS.md` §5.5 (`020_create_gamification.sql`).

---

## 0. SOMMAIRE

1. [Objectifs et principes](#1-objectifs-et-principes)
2. [Architecture du système](#2-architecture-du-système)
3. [Système XP](#3-système-xp)
4. [Les 4 niveaux d'artisan](#4-les-4-niveaux-dartisan)
5. [Quêtes d'onboarding](#5-quêtes-donboarding)
6. [Quêtes progressives (post-onboarding)](#6-quêtes-progressives-post-onboarding)
7. [Catalogue badges (~50 badges)](#7-catalogue-badges-50-badges)
8. [Streak quotidien 🔥](#8-streak-quotidien-)
9. [Classement régional](#9-classement-régional)
10. [Récompenses tangibles](#10-récompenses-tangibles)
11. [Progression parcours artisan](#11-progression-parcours-artisan)
12. [Notifications et UX](#12-notifications-et-ux)
13. [Intégration F114 Parrainage](#13-intégration-f114-parrainage)
14. [Intégration F117 Score "cerveau me connaît"](#14-intégration-f117-score-cerveau-me-connaît)
15. [Intégration F120 Capsules formation](#15-intégration-f120-capsules-formation)
16. [Schéma base de données](#16-schéma-base-de-données)
17. [Service backend](#17-service-backend)
18. [Endpoints API](#18-endpoints-api)
19. [Anti-patterns gamification](#19-anti-patterns-gamification)
20. [Métriques et analytics](#20-métriques-et-analytics)
21. [Tests et équilibrage](#21-tests-et-équilibrage)
22. [Références croisées](#22-références-croisées)

---

## 1. OBJECTIFS ET PRINCIPES

### 1.1 — Pourquoi gamifier ?

**Contexte artisan BTP** :
- ~70% des artisans ne sont **pas familiers avec les apps** (voir `COMMUNICATION.md`)
- Mains dans le plâtre, crevés après 10h de chantier
- Ancienne génération (moyenne d'âge 45-55 ans)
- Rejet naturel de tout ce qui ressemble à un "logiciel"

**Solution** :
- Donner un sentiment de **progrès visible** (XP, niveaux)
- **Récompenses concrètes** (mois gratuits, accès anticipés)
- **Challenges simples** (quêtes), pas d'écrans complexes
- **Pair avec la voix** : chaque quête est une conversation, pas un formulaire

### 1.2 — 8 règles immuables

1. **Gamification FACILITE, ne bloque JAMAIS** — un artisan sans niveau doit pouvoir faire un devis
2. **Récompenses concrètes** (pas que virtuelles) — mois offerts, accès, fonctionnalités
3. **Conversation > Formulaire** — chaque quête est une discussion vocale
4. **Progrès visible** — barre XP toujours affichée
5. **Streak tolérant** — 1 jour raté ne casse pas tout (grâce de 24h)
6. **Classement OPT-IN** — certains artisans détestent être comparés
7. **Badges par lots** (1 scanné, 50, 100, 500) — progression infinie
8. **Pas de shame** — jamais "tu n'as rien fait cette semaine"

### 1.3 — Ce que la gamification APPORTE

- **Adoption** : onboarding 25% → 60% (quêtes guidées)
- **Retention** : 30 jours 40% → 65% (streak + quêtes)
- **Engagement quotidien** : 2 sessions/jour → 5 sessions/jour
- **Virality** : badge Ambassadeur → parrainage x3

### 1.4 — Ce que la gamification NE FAIT PAS

- Pas de **pay-to-win** (pas d'XP achetable)
- Pas de **classement forcé** (public par défaut)
- Pas de **punition** (pas de XP négatif)
- Pas de **compétition toxique** (aucun "tu es dernier")

---

## 2. ARCHITECTURE DU SYSTÈME

### 2.1 — Vue d'ensemble

```
┌──────────────────────────────────────────────────┐
│           Événements utilisateur                  │
│  (devis créé, facture payée, avis reçu, etc.)    │
└────────────────────┬─────────────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────────────┐
│       Event Bus (Redis pub/sub)                   │
│  Événement : "devis.created" avec payload         │
└────────────────────┬─────────────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────────────┐
│       Gamification Service                        │
│  1. Calcule XP à attribuer                        │
│  2. Enregistre xp_event                           │
│  3. Check progression niveau                      │
│  4. Check complétion quêtes                       │
│  5. Check déblocage badges                        │
│  6. Check streak                                  │
│  7. Envoie notification si milestone              │
└────────────────────┬─────────────────────────────┘
                     │
                     ▼
┌──────────────────────────────────────────────────┐
│       Tables BDD                                  │
│  xp_events, user_levels, user_quests,             │
│  user_badges, streaks                             │
└──────────────────────────────────────────────────┘
```

### 2.2 — Approche event-driven

Chaque action pertinente du système publie un événement :
- `devis.created`
- `devis.sent`
- `devis.signed`
- `facture.paid`
- `avis.received`
- `ticket.scanned`
- `photo.uploaded`
- `employee.added`
- `referral.converted`
- ...

Le **Gamification Service** écoute ces événements et traite en async.

### 2.3 — Pas bloquant

Le traitement gamification est **toujours asynchrone** — jamais dans le chemin critique d'une action.

Si le service gamification est down → l'artisan peut continuer à créer des devis, il rattrapera ses XP plus tard (replay events).

---

## 3. SYSTÈME XP

### 3.1 — Barème XP par action

**Actions premium (setup + onboarding)** :

| Action | XP | Déclenchement |
|---|---|---|
| Signup + email vérifié | **+100** | Once |
| WhatsApp vérifié (OTP) | **+50** | Once |
| Profil complété (SIRET, décennale, logo) | **+100** | Once |
| Premier devis créé | **+200** | Once |
| Premier devis envoyé | **+150** | Once |
| Premier devis signé | **+300** | Once |
| Première facture envoyée | **+200** | Once |
| Première facture payée | **+300** | Once |
| 10 prix perso enregistrés dans Mem0 | **+200** | Once |

**Actions récurrentes (quotidien/fréquent)** :

| Action | XP | Plafond |
|---|---|---|
| Devis créé | **+20** | illimité |
| Devis envoyé | **+15** | illimité |
| Devis signé | **+50** | illimité |
| Facture envoyée | **+15** | illimité |
| Facture payée | **+30** | illimité |
| Ticket OCR scanné | **+5** | max 50 XP/jour |
| Photo chantier uploadée | **+3** | max 30 XP/jour |
| Avis Google reçu | **+40** | illimité |
| Avis Google répondu | **+10** | illimité |
| Relance envoyée validée | **+5** | max 25 XP/jour |
| Contact pro ajouté | **+10** | max 50 XP/jour |
| Employé ajouté (Business) | **+100** | once par employé |
| Chantier créé | **+15** | max 60 XP/jour |
| Chantier terminé | **+50** | illimité |
| Connexion quotidienne | **+10** | 1×/jour |

**Actions sociales** :

| Action | XP | Plafond |
|---|---|---|
| Parrain converti (filleul paie 1er mois) | **+500** | illimité (F114) |
| 1 mois d'utilisation | **+150** | bonus mensuel |
| 1 an d'utilisation | **+2 000** | bonus annuel |
| Beta-test nouvelle feature | **+100** | par feature |

**Actions Coach Business (Pro/Business)** :

| Action | XP | Plafond |
|---|---|---|
| Analyse Coach reçue | **+50** | 1×/mois Starter, 1×/analyse Pro/Business |
| Recommandation appliquée | **+100** | 1×/recommandation |

### 3.2 — Caps journaliers

Pour éviter le farming, certaines actions répétitives sont plafonnées (ex: max 50 XP/jour sur tickets scannés). Au-delà, 0 XP mais action fonctionnelle.

### 3.3 — Multiplicateurs contextuels

Certaines actions ont des **bonus contextuels** :

| Bonus | Condition | Multiplicateur |
|---|---|---|
| Streak 7j | 7 jours consécutifs | +10% XP |
| Streak 30j | 30 jours consécutifs | +20% XP |
| Streak 100j | 100 jours consécutifs | +50% XP |
| Weekend guerrier | Devis samedi/dimanche | +50% XP |
| Nouveau métier | 1er chantier dans un métier nouveau | +100 XP bonus |

### 3.4 — Stockage

Chaque XP est enregistrée dans `xp_events` (audit trail complet) :

```sql
-- Voir docs/MIGRATIONS.md §5.5
CREATE TABLE xp_events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id),
    event_type TEXT NOT NULL,        -- 'devis_created', 'facture_paid', ...
    xp_amount INTEGER NOT NULL,
    metadata JSONB,                   -- {devis_id, multiplier, context}
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

Total XP d'un user = `SUM(xp_amount) FROM xp_events WHERE user_id = ?`.

---

## 4. LES 4 NIVEAUX D'ARTISAN

### 4.1 — Progression

| Niveau | Nom | XP requis | Bénéfices |
|---|---|---|---|
| **1** | 🔨 Débutant | 0 | Accès standard |
| **2** | 🛠️ Confirmé | 1 000 | +5% remise fournisseurs partenaires, templates premium |
| **3** | ⭐ Expert | 5 000 | Accès webinars exclusifs, beta features, badge visible partout |
| **4** | 👑 Maître | 15 000 | Programme ambassadeur, 1 mois offert/an, accès Claude Opus illimité (Pro→Business) |

### 4.2 — Temps typique de progression

Pour un artisan Pro actif :
- **Débutant → Confirmé** : ~1 semaine (onboarding complet + premiers devis)
- **Confirmé → Expert** : ~2 mois (usage régulier)
- **Expert → Maître** : ~6 mois (usage intensif + parrainage)

### 4.3 — Affichage

Le niveau est affiché **partout** :
- Avatar profil (couronne, étoile, marteau)
- Entête du dashboard
- Dans les emails (signature)
- Sur les devis envoyés aux clients (badge "Artisan Expert STRUCTORAI" optionnel)

### 4.4 — Pas de régression

**Règle** : on ne REDESCEND jamais de niveau. Même inactif, un Maître reste Maître.

### 4.5 — Transition UI

Quand l'artisan passe un niveau :
- Notification push spéciale : "🎉 Tu es passé Confirmé !"
- Écran modal animé (confettis, couleur fête)
- Voix cerveau joue : "Félicitations, tu es maintenant Artisan Confirmé !"
- Partage social suggéré (Facebook/Instagram/WhatsApp)

### 4.6 — Barre de progression

Toujours visible en haut du dashboard :

```
🛠️ Confirmé — 2 340 / 5 000 XP
████████░░░░░░░░░░░░  47%
2 660 XP pour passer Expert
```

---

## 5. QUÊTES D'ONBOARDING

Voir `FEATURES.md` F75-F76 enrichi par Fabrice.

### 5.1 — Philosophie

**Citation Fabrice** : "Un guide, une gamification du parcours pour que ce soit le plus fluide possible même pour celui qui n'a pas l'habitude d'utiliser des apps."

**Principes** :
- **Conversation vocale** > formulaire (le cerveau pose les questions, l'artisan parle)
- **Progression linéaire** mais skippable
- **Micro-accomplissements** fréquents
- **Fléché** : "Prochaine quête suggérée"

### 5.2 — Les 10 quêtes d'onboarding (séquentielle)

**Q1 — Bienvenue + présentation** (pas de XP)
- Le cerveau se présente (30s vocal)
- Demande nom préféré, langue
- Pose la question "Tu fais quel corps de métier principal ?"

**Q2 — Dis-moi qui tu es** (+100 XP)
- Conversation vocale guidée
- Collecte : métier, zone, SIRET, décennale, RGE (optionnel)
- **Output** : pré-remplit TOUS les futurs devis avec mentions obligatoires

**Q3 — Ton premier devis vocal** (+150 XP)
- Le cerveau explique "Raconte-moi un devis simple que tu vas faire"
- L'artisan dicte ("Devis pour Mme Dupont, SDB complète 8m²")
- Le cerveau structure, demande confirmation prix
- **Output** : apprend le style de l'artisan

**Q4 — Tes prix habituels** (+200 XP)
- "Donne-moi 10 prix que tu utilises souvent"
- Ex: pose carrelage/m², heure de MO, dépose receveur
- **Output** : Mem0 enrichi, devis instantanés futurs

**Q5 — Scan ton 1er ticket** (+50 XP)
- "Fais-moi scanner un ticket de caisse récent"
- Claude Vision OCR
- Classement automatique + imputation chantier

**Q6 — Connecte WhatsApp** (+50 XP)
- OTP WhatsApp
- Numéro vérifié
- **Output** : F87 auto-création prospects

**Q7 — Personnalise tes relances** (+100 XP)
- Choix ton (amical/pro/ferme)
- Choix canal (email/SMS/WhatsApp)
- Timing (J+3, J+15, etc.)

**Q8 — Connecte ton Google Business** (+100 XP)
- OAuth Google
- Récupération avis existants
- Activation auto-réponse

**Q9 — Ajoute un chantier en cours** (+50 XP)
- Fiche client + chantier
- Photos avant (au moins 1)

**Q10 — Invite un collègue** (+150 XP, bonus +500 XP si conversion)
- Génération code parrain
- Partage WhatsApp/SMS
- Déblocage badge "Ambassadeur" (voir §7.5)

### 5.3 — Total XP onboarding

Somme = **1 050 XP** (base) → permet d'atteindre **Confirmé** (1 000 XP) en 1 session si complété entièrement.

### 5.4 — Skippable

Chaque quête peut être **skippée** par l'artisan ("Plus tard"). Elle reste disponible dans l'onglet "Quêtes".

### 5.5 — Progression

Dashboard affiche :
```
✅ Bienvenue                         +0 XP
✅ Dis-moi qui tu es                 +100 XP
✅ Premier devis vocal               +150 XP
🟡 Tes prix habituels  [En cours]    3/10 prix
🔒 Scan ton 1er ticket
🔒 Connecte WhatsApp
...
```

---

## 6. QUÊTES PROGRESSIVES (POST-ONBOARDING)

Après onboarding, de nouvelles quêtes sont débloquées selon l'usage.

### 6.1 — Quêtes hebdomadaires (reset chaque lundi)

- 📝 "5 devis cette semaine" → +200 XP
- 💰 "3 factures envoyées" → +150 XP
- ⭐ "1 avis Google reçu" → +100 XP
- 📸 "10 photos chantier" → +80 XP
- 🧾 "20 tickets scannés" → +100 XP

### 6.2 — Quêtes mensuelles (reset 1er du mois)

- 💼 "10 devis signés ce mois" → +500 XP
- 📊 "Lecture analyse Coach Business" → +100 XP (Pro/Business)
- 🎯 "Marge > 30% sur un chantier" → +300 XP
- 🔄 "Convertir 3 prospects" → +400 XP

### 6.3 — Défis mensuels communautaires (F117 enrichi)

Défis communs à tous les artisans STRUCTORAI, avec classement opt-in :

**Exemples** :
- "Novembre = mois du carrelage" : plus grand CA en carrelage → +1000 XP + badge limité
- "Challenge zéro impayé" : 30 jours sans impayé → +500 XP
- "Saison froide" : décembre, plus de chantiers isolation → bonus

### 6.4 — Quêtes événementielles (post-launch)

Lors d'événements spéciaux :
- Fêtes (Noël, Pâques)
- Anniversaire STRUCTORAI
- Launch feature importante

---

## 7. CATALOGUE BADGES (~50 BADGES)

Voir `FEATURES.md` F77 enrichi : "Il doit y avoir beaucoup de badges".

### 7.1 — Catégorie DEVIS

| Badge | Critère | Niveau |
|---|---|---|
| 🥇 Premier Devis | 1 devis créé | bronze |
| 📝 Apprenti Devis | 10 devis | bronze |
| ✍️ Serial Devis | 50 devis | argent |
| 📋 Artisan Devis | 100 devis | or |
| 🏆 Maître Devis | 500 devis | platine |
| ⚡ Rapide | Devis < 3min | or |
| 🎯 Pro du 1er coup | 1er devis signé sans modif | argent |
| 💪 10 signés | 10 devis signés | argent |
| 👑 Conversion King | 70%+ taux conversion sur 30 devis | platine |

### 7.2 — Catégorie COMPTA / TICKETS

| Badge | Critère | Niveau |
|---|---|---|
| 🧾 1er Ticket | 1 ticket scanné | bronze |
| 📸 50 Tickets | 50 tickets scannés | bronze |
| 💯 100 Tickets | 100 | argent |
| 🎯 500 Tickets | 500 | or |
| 📊 Expert OCR | 1000 | platine |
| 🧹 Mois Impeccable | 0 ticket non classé fin de mois | or |
| 📅 Expert Compta | 12 mois de suite avec export comptable sans oubli | platine |

### 7.3 — Catégorie RÉPUTATION / AVIS

| Badge | Critère | Niveau |
|---|---|---|
| ⭐ Premier Avis | 1 avis Google reçu | bronze |
| 🌟 10 Avis | 10 avis | argent |
| 🏅 50 Avis | 50 avis | or |
| 💎 100 Avis | 100 avis | platine |
| 🎖️ 4.8+ Stars | Note moyenne > 4.8 sur 30+ avis | platine |
| 💬 Répondeur | 100% avis répondus sur 20 avis | or |

### 7.4 — Catégorie PROSPECTION

| Badge | Critère | Niveau |
|---|---|---|
| 🤝 Premier Archi | 1 contact architecte ajouté | bronze |
| 📞 Networker | 25 contacts pro | argent |
| 🎯 Chasseur | 50 contacts + 10 conversions | or |
| 🔥 Pipeline Plein | 15 chantiers actifs simultanément | platine |

### 7.5 — Catégorie AMBASSADEUR (F114)

| Badge | Critère | Niveau |
|---|---|---|
| 🎁 Parrain | 1 filleul converti | bronze |
| 🤝 Super Parrain | 5 filleuls convertis | argent |
| 👑 Ambassadeur | 10 filleuls convertis | or |
| 🏆 VIP Ambassadeur | 25 filleuls convertis | platine (avantages spéciaux) |

**Avantages Ambassadeur VIP** :
- Mois gratuit par filleul converti
- Accès direct à Fabrice (Slack privé)
- Nom dans page publique "Ambassadeurs"

### 7.6 — Catégorie FIDÉLITÉ

| Badge | Critère | Niveau |
|---|---|---|
| 🌱 1 Mois | 1 mois d'utilisation | bronze |
| 🌿 3 Mois | 3 mois | bronze |
| 🌳 6 Mois | 6 mois | argent |
| 🎂 Anniversaire | 1 an d'utilisation | or |
| 🏛️ Vétéran | 3 ans | platine |

### 7.7 — Catégorie STREAK

| Badge | Critère | Niveau |
|---|---|---|
| 🔥 Streak 7j | 7 jours consécutifs | bronze |
| 🔥🔥 Streak 30j | 30 jours | argent |
| 🔥🔥🔥 Streak 100j | 100 jours | or |
| 🔥👑 Streak 365j | 1 an non-stop | platine |

### 7.8 — Catégorie ÉQUIPE (Business)

| Badge | Critère | Niveau |
|---|---|---|
| 👷 1er Employé | 1 employé ajouté | bronze |
| 👷‍♂️ Team Builder | 3 employés | argent |
| 👥 Team Leader | 5 employés | or |
| 🏢 Entreprise | 10 employés | platine |

### 7.9 — Catégorie FINANCIÈRE

| Badge | Critère | Niveau |
|---|---|---|
| 💰 1 000€ CA | 1 000€ HT facturés | bronze |
| 💵 10 000€ CA | 10 000€ HT facturés | argent |
| 💸 100 000€ CA | 100 000€ HT facturés | or |
| 🏦 1M€ CA | 1M€ HT facturés | platine |
| 🎯 Zéro Impayé | 6 mois sans facture en retard | or |
| 💎 Marge 40% | Marge moyenne > 40% sur 20 chantiers | platine |

### 7.10 — Catégorie COACH (Pro/Business)

| Badge | Critère | Niveau |
|---|---|---|
| 🧠 Premier Coach | 1ère analyse Coach Business reçue | bronze |
| 📈 Trend Up | Taux horaire +10% vs benchmark en 6 mois | or |
| 🚀 Croissance | CA x2 en 12 mois | platine |

### 7.11 — Badges cachés / surprises

Certains badges sont **secrets** et se débloquent par surprise :
- "Nuit blanche" : devis créé entre 2h et 5h du matin
- "Weekend Warrior" : 10 devis en 1 weekend
- "Beta tester" : 1ère utilisation d'une feature avant launch public
- "Bug hunter" : rapport bug validé par l'équipe

**Total** : ~50 badges répartis sur 10 catégories. Fabrice a demandé "beaucoup de badges" → on en crée plus que nécessaire, c'est motivant.

### 7.12 — Affichage

Dashboard → onglet "Badges" avec grid visuel :
- Badges obtenus en couleur
- Badges verrouillés en gris avec indice de progression
- Partage social (1 clic) pour chaque badge obtenu

---

## 8. STREAK QUOTIDIEN 🔥

### 8.1 — Définition

**Streak** = nombre de jours consécutifs où l'artisan a **utilisé l'app au moins une fois**.

**Usage** = au moins une des actions suivantes dans la journée :
- Ouvrir l'app
- Envoyer un message au cerveau
- Créer/modifier un devis, facture, chantier, ticket
- Valider une tâche de "À faire"

### 8.2 — Affichage

Bandeau permanent en haut de l'écran d'accueil :
```
🔥 Streak : 23 jours — bravo !
```

Ou si manqué :
```
🔥 Streak : 0 jours (tu peux redémarrer aujourd'hui)
```

### 8.3 — Règles de grâce

**Règle n°1** : un jour raté = reset à 0 par défaut.

**Règle n°2 — grâce automatique** : si l'artisan a un streak >7j et rate 1 jour → "grâce" automatique (streak conservé, mais pas incrémenté). Max 1 grâce par mois.

**Règle n°3 — weekend/vacances** : option dans settings "Mode vacances" qui gèle le streak (ne l'incrémente pas mais ne le casse pas).

### 8.4 — Bonus XP

Voir §3.3 — multiplicateurs selon streak (7j +10%, 30j +20%, 100j +50%).

### 8.5 — Notifications streak

- **Jour 3** : "Tu es sur une bonne lancée ! 🔥"
- **Jour 7** : "Une semaine ! Tu gagnes +10% XP" (déclenchement réel bonus)
- **Jour 30** : "Un mois complet ! +20% XP" + badge 🔥🔥
- **Jour 100** : "100 jours !" + badge 🔥🔥🔥
- **Jour 365** : "ONE YEAR STREAK" + badge 🔥👑 + 1 mois offert

**Notifications de rappel** :
- À 20h si streak pas activé aujourd'hui : "🔥 Plus que 4h pour maintenir ton streak de 12 jours !"
- PAS insistant (1 seule notif)

---

## 9. CLASSEMENT RÉGIONAL

### 9.1 — Opt-in par défaut désactivé

**Règle** : par défaut, l'artisan **n'est PAS** dans les classements. Il peut activer dans settings.

### 9.2 — Classements disponibles (si opt-in)

**Département** :
- Top 10 artisans du département par XP total
- Top 10 par CA facturé mensuel (anonymisé, tranches)
- Top 10 par nombre d'avis Google

**Corps de métier dans le département** :
- Top 5 plombiers département 13
- Top 5 électriciens département 69

**National (Maîtres uniquement)** :
- Classement global niveau Maître

### 9.3 — Anonymisation

L'artisan peut choisir son affichage public :
- Nom + entreprise (public)
- Prénom + ville (semi)
- Pseudo seulement (anonyme)

### 9.4 — Pas de "dernier"

Les classements affichent **toujours les top**, jamais les derniers. Pas de "tu es 457ème sur 600".

### 9.5 — UI

```
🏆 Top 10 artisans Marseille (opt-in visible)

1. 👑 Jean-Paul B. (Plombier) — 12 450 XP
2. 👑 Marie L. (Carreleur) — 11 890 XP
3. ⭐ Mohamed A. (Électricien) — 9 200 XP
...

📍 Ta position : 14ème (non visible publiquement)
```

### 9.6 — Reset mensuel vs all-time

- **Classement XP all-time** : progression long terme
- **Classement mensuel** : reset chaque 1er — permet nouveaux artisans de se distinguer

---

## 10. RÉCOMPENSES TANGIBLES

Voir `docs/FEATURES_COMPLETE.md` F86.

### 10.1 — Philosophie

La gamification **seule** ne fonctionne pas pour un artisan (≠ gamer). Il faut des **récompenses réelles**.

### 10.2 — Catalogue récompenses

**Débloquées par niveau** :
- **Niveau 2 Confirmé** : templates PDF premium, 5% chez partenaires
- **Niveau 3 Expert** : accès webinars exclusifs (mensuels), badge visible clients
- **Niveau 4 Maître** : 1 mois offert/an, accès Claude Opus illimité si Pro, programme ambassadeur

**Débloquées par quêtes** :
- Compléter 5 quêtes hebdomadaires consécutives → 1 semaine gratuite
- Compléter 3 quêtes mensuelles → badge + code promo partenaire

**Débloquées par badges** :
- Badge "100 devis" → 10% remise 1 mois
- Badge "Ambassadeur Or" → 1 mois gratuit par filleul
- Badge "Zéro Impayé" → bonus 50€ crédit app
- Badge "1 an d'anniversaire" → 1 mois gratuit + cadeau physique (stickers, casquette)

**Débloquées par streak** :
- 30j → 1 semaine gratuite
- 100j → 1 mois gratuit
- 365j → 3 mois gratuits + cadeau

### 10.3 — Codes promo partenaires (post-launch)

Partenariats envisagés :
- Fournisseurs BTP (Point P, Cedeo) : remises sur matériaux
- Outillage (Makita, Dewalt) : codes promo
- Vêtement pro (LMP, Engelbert Strauss) : 10% off
- Assurance pro : tarif préférentiel

Ces partenariats sont à développer **après launch** quand volume artisans suffisant.

### 10.4 — Implémentation technique

Table `rewards_unlocked` pour tracking :

```sql
CREATE TABLE rewards_unlocked (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    reward_type TEXT NOT NULL,        -- 'free_month','promo_code','webinar_access','gift'
    reward_value TEXT,                -- code promo, référence cadeau
    unlocked_at TIMESTAMPTZ DEFAULT NOW(),
    claimed_at TIMESTAMPTZ,           -- quand artisan a réclamé
    expires_at TIMESTAMPTZ,
    metadata JSONB
);
```

---

## 11. PROGRESSION PARCOURS ARTISAN

### 11.1 — Les 4 phases

**Phase 1 — Découverte (Jour 1 → Jour 7)**
- Status : Niveau 1 Débutant
- Focus : onboarding quêtes (1-10)
- Objectif : premier devis signé
- Si réussi → Niveau 2 Confirmé

**Phase 2 — Installation (Jour 7 → Jour 30)**
- Status : Niveau 2 Confirmé
- Focus : habitudes (daily usage, streak)
- Objectif : 10 devis, 5 factures payées, 3 avis
- Défis hebdomadaires

**Phase 3 — Consolidation (Mois 2 → Mois 6)**
- Status : Niveau 3 Expert
- Focus : optimisation (Coach Business, benchmarks)
- Objectif : améliorer marge, taux conversion, portefeuille
- Défis mensuels communautaires

**Phase 4 — Maîtrise (Mois 6+)**
- Status : Niveau 4 Maître
- Focus : ambassadeur, mentorat
- Objectif : parrainage, contribution communauté
- Accès beta features

### 11.2 — Adaptation aux profils

**Artisan high-activity** (5 devis/semaine) : atteint Maître en 4 mois

**Artisan low-activity** (1 devis/semaine) : atteint Maître en ~12 mois

**Règle** : pas de time-pressure — chacun progresse à son rythme.

### 11.3 — Re-engagement

Si artisan inactif >14 jours :
- Notification rappel douce
- Email avec récap ("Tu as 3 devis en attente, 5 tickets non scannés")
- Capsule formation pour reprendre en main

Si inactif >30 jours :
- Email du cerveau "Je m'inquiète, tout va bien ?"
- Offre de 1 mois gratuit en retour

Si inactif >90 jours :
- Compte flagué "at risk"
- Désabonnement auto (plan Starter) ou cancellation Stripe (Pro/Business)

---

## 12. NOTIFICATIONS ET UX

### 12.1 — Types de notifications gamification

| Type | Trigger | Canal |
|---|---|---|
| XP gagné | Toast discret (+20 XP) | In-app seulement |
| Milestone XP (palier 500/1000) | Notification push | Push + in-app |
| Niveau up | Modal animé + voix cerveau | Push + in-app + vocal |
| Badge débloqué | Notification push + écran partage | Push + in-app |
| Quête complétée | Toast + check animé | In-app seulement |
| Streak milestone (7/30/100/365) | Notification push + modal | Push + in-app |
| Streak en danger (20h, pas activé) | Notification push (soft) | Push |
| Récompense débloquée | Notification push + CTA réclamer | Push + email |
| Classement (opt-in) | Notification hebdo (lundi) | Push |

### 12.2 — Anti-spam

**Règles** :
- Max 3 notifications gamification par jour
- Pas entre 20h et 8h (sauf opt-in "Toujours notifier")
- Streak en danger : uniquement à 20h, 1 fois
- Opt-out par type dans settings

### 12.3 — Design UI

**Écran principal** (dashboard) :
```
┌────────────────────────────────────────┐
│  🛠️ Confirmé — 2 340 / 5 000 XP         │
│  ████████░░░░░░░░░░  47%               │
│  🔥 Streak : 23 jours                  │
└────────────────────────────────────────┘

[Actions quotidiennes]

┌────────────────────────────────────────┐
│  📊 Quêtes en cours                     │
│                                         │
│  🟡 "5 devis cette semaine"             │
│     Progression : 3/5 — +200 XP         │
│                                         │
│  🟡 "10 prix perso enregistrés"         │
│     Progression : 7/10 — +200 XP        │
│                                         │
│  [Voir toutes les quêtes]               │
└────────────────────────────────────────┘

┌────────────────────────────────────────┐
│  🏆 Derniers badges                     │
│                                         │
│  [🥇] [📝] [🧾] [⭐] [🔥]              │
│                                         │
│  [Voir tous les badges]                 │
└────────────────────────────────────────┘
```

### 12.4 — Animations

- Niveau up : confettis + voix cerveau
- Badge débloqué : zoom in + glow effect + haptic feedback
- XP gagné : petite animation number flying
- Streak : flamme qui grossit

Lib suggérée : `react-native-reanimated` + `lottie-react-native`.

---

## 13. INTÉGRATION F114 PARRAINAGE

Voir `docs/MIGRATIONS.md` §7.1.

### 13.1 — Flow

1. Artisan accepte quête "Invite un collègue"
2. Génération code parrain unique (`PARRAIN-JEAN-3F2A`)
3. Partage WhatsApp/SMS/Email avec lien
4. Filleul signup avec code → badge tracking
5. Filleul paie 1er mois → conversion confirmée
6. Parrain débloque :
   - +500 XP
   - 1 mois offert sur son abonnement
   - Progression badge Ambassadeur (1/5, 5/5, 10/10)

### 13.2 — Avantages filleul

- -20% sur premier mois Pro ou Business
- Trial étendu 45 jours (au lieu de 30)
- Accès Ambassadeur VIP si parrain VIP

### 13.3 — Limits

- Max 10 filleuls/mois (anti-abus)
- Conversion seulement si filleul reste actif >30 jours (protection refund)
- Pas de self-parrainage

### 13.4 — Badge Ambassadeur (bronze → VIP)

Voir §7.5 — progression continue, avantages croissants.

---

## 14. INTÉGRATION F117 SCORE "CERVEAU ME CONNAÎT"

Voir `FEATURES.md` F117.

### 14.1 — Concept

Score "% le cerveau me connaît" affiché dans profil.

**Calcul** : % de complétion du profil Mem0 selon critères :
- 30% — profil entreprise complet (SIRET, métier, RGE, décennale)
- 20% — 10+ prix perso enregistrés
- 15% — 20+ clients dans le CRM
- 10% — 50+ tickets scannés
- 10% — 20+ photos chantier
- 10% — préférences relances définies
- 5% — intégrations actives (WhatsApp, Google, IMAP)

### 14.2 — Affichage

```
🧠 Le cerveau me connaît : 78% 
████████████████░░░░  
+22% pour un devis 100% autonome
```

### 14.3 — Quest-driven

Chaque lacune devient une quête suggérée :
- "Ajoute ton RGE (+5%)"
- "Enregistre 3 prix perso supplémentaires (+3%)"

### 14.4 — Anti-churn

Si le score est élevé (>80%), l'artisan est moins susceptible de churn (investissement perso).

Alerte équipe si user >80% score et aucune activité 7 jours → campagne retention personnalisée.

---

## 15. INTÉGRATION F120 CAPSULES FORMATION

Voir `docs/MIGRATIONS.md` §7.2.

### 15.1 — Concept

Vidéos 60s contextuelles déclenchées par Supervisor selon événements :
- Première utilisation d'une feature
- Après une erreur récurrente
- Avant une fonctionnalité avancée

### 15.2 — Déclencheurs gamification

- Après quête "1er devis vocal" → capsule "Comment utiliser au mieux le vocal"
- Après niveau up → capsule "Tes nouveaux avantages"
- Après badge "Ambassadeur" → capsule "Comment développer ton parrainage"

### 15.3 — XP pour visionnage

+20 XP par capsule regardée jusqu'au bout (max 5/jour pour éviter farming).

---

## 16. SCHÉMA BASE DE DONNÉES

Voir `docs/MIGRATIONS.md` §5.5 (migration `020_create_gamification.sql`).

### 16.1 — Tables principales

```sql
-- XP events
CREATE TABLE xp_events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id),
    event_type TEXT NOT NULL,
    xp_amount INTEGER NOT NULL,
    metadata JSONB,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- Quêtes (catalogue)
CREATE TABLE quests (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    slug TEXT UNIQUE NOT NULL,
    titre TEXT NOT NULL,
    description TEXT,
    xp_reward INTEGER,
    criteria JSONB,
    category TEXT,                    -- 'onboarding','weekly','monthly','event'
    unlock_level INTEGER DEFAULT 1,
    order_index INTEGER
);

-- Quêtes par user
CREATE TABLE user_quests (
    user_id UUID REFERENCES users(id),
    quest_id UUID REFERENCES quests(id),
    status TEXT DEFAULT 'pending',    -- 'pending','in_progress','completed'
    progress JSONB,
    completed_at TIMESTAMPTZ,
    PRIMARY KEY (user_id, quest_id)
);

-- Badges (catalogue)
CREATE TABLE badges (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    slug TEXT UNIQUE NOT NULL,
    nom TEXT NOT NULL,
    description TEXT,
    image_url TEXT,
    category TEXT,                    -- 'devis','compta','reputation',...
    level TEXT,                       -- 'bronze','argent','or','platine'
    criteria JSONB,
    hidden BOOLEAN DEFAULT FALSE      -- pour badges secrets
);

-- Badges obtenus
CREATE TABLE user_badges (
    user_id UUID REFERENCES users(id),
    badge_id UUID REFERENCES badges(id),
    earned_at TIMESTAMPTZ DEFAULT NOW(),
    PRIMARY KEY (user_id, badge_id)
);

-- Streaks
CREATE TABLE user_streaks (
    user_id UUID PRIMARY KEY REFERENCES users(id),
    current_streak INTEGER DEFAULT 0,
    longest_streak INTEGER DEFAULT 0,
    last_activity_date DATE,
    grace_used_current_month BOOLEAN DEFAULT FALSE,
    vacation_mode_until DATE,
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Niveaux cache (dérivé de xp_events pour performance)
CREATE TABLE user_levels (
    user_id UUID PRIMARY KEY REFERENCES users(id),
    total_xp INTEGER DEFAULT 0,
    current_level INTEGER DEFAULT 1,
    xp_to_next_level INTEGER DEFAULT 1000,
    updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Récompenses débloquées
CREATE TABLE rewards_unlocked (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    reward_type TEXT NOT NULL,
    reward_value TEXT,
    unlocked_at TIMESTAMPTZ DEFAULT NOW(),
    claimed_at TIMESTAMPTZ,
    expires_at TIMESTAMPTZ,
    metadata JSONB
);
```

### 16.2 — Index

- `xp_events(user_id, created_at DESC)`
- `user_quests(user_id, status)`
- `user_badges(user_id)`
- `user_streaks(last_activity_date)` — pour job reset

### 16.3 — Seed data

Voir `docs/MIGRATIONS.md` §10 — seed pour :
- 25 quêtes de base (10 onboarding + 5 weekly + 5 monthly + 5 événementielles)
- ~50 badges catalogue (voir §7)

---

## 17. SERVICE BACKEND

### 17.1 — Structure

**Fichier** : `backend/app/services/gamification_service.py`

```python
class GamificationService:
    def __init__(self, db, event_bus):
        self.db = db
        self.event_bus = event_bus
    
    async def process_event(self, event_type: str, payload: dict):
        """Traite un événement métier et attribue XP/badges/quêtes."""
        user_id = payload["user_id"]
        
        # 1. Attribuer XP
        xp = self._calculate_xp(event_type, payload)
        if xp > 0:
            await self._award_xp(user_id, event_type, xp, payload)
        
        # 2. Check quêtes
        await self._check_quests_progress(user_id, event_type, payload)
        
        # 3. Check badges
        await self._check_badges_unlock(user_id, event_type, payload)
        
        # 4. Update streak
        if event_type == "user.activity":
            await self._update_streak(user_id)
        
        # 5. Check level up
        await self._check_level_up(user_id)
    
    async def _award_xp(self, user_id, event_type, xp, payload):
        # Multiplicateurs
        multiplier = await self._compute_multiplier(user_id)
        final_xp = int(xp * multiplier)
        
        # Cap journalier
        if not await self._check_daily_cap(user_id, event_type, final_xp):
            final_xp = 0
        
        # Insert
        await self.db.execute(
            "INSERT INTO xp_events (user_id, event_type, xp_amount, metadata) VALUES ($1, $2, $3, $4)",
            user_id, event_type, final_xp, payload
        )
        
        # Update cache user_levels
        await self._update_user_level_cache(user_id, final_xp)
    
    async def _check_quests_progress(self, user_id, event_type, payload):
        """Met à jour progression des quêtes en cours."""
        active_quests = await self.db.fetch(
            "SELECT * FROM user_quests uq JOIN quests q ON q.id = uq.quest_id "
            "WHERE uq.user_id = $1 AND uq.status = 'in_progress'",
            user_id
        )
        for quest in active_quests:
            if self._event_matches_criteria(event_type, payload, quest.criteria):
                new_progress = self._update_progress(quest.progress, payload)
                if self._is_quest_complete(new_progress, quest.criteria):
                    await self._complete_quest(user_id, quest)
                else:
                    await self._save_progress(user_id, quest.id, new_progress)
    
    async def _check_badges_unlock(self, user_id, event_type, payload):
        """Check si badges à débloquer."""
        eligible_badges = await self._find_eligible_badges(user_id, event_type)
        for badge in eligible_badges:
            if self._meets_badge_criteria(user_id, badge):
                await self._award_badge(user_id, badge)
    
    async def _update_streak(self, user_id):
        today = date.today()
        streak = await self.db.fetchrow(
            "SELECT * FROM user_streaks WHERE user_id = $1", user_id
        )
        
        if not streak:
            await self._create_streak(user_id, today)
            return
        
        days_since_last = (today - streak.last_activity_date).days
        
        if days_since_last == 0:
            return  # déjà activé aujourd'hui
        
        if days_since_last == 1:
            new_current = streak.current_streak + 1
            await self._save_streak(user_id, new_current, today)
            if new_current in (7, 30, 100, 365):
                await self._trigger_streak_milestone(user_id, new_current)
        elif days_since_last == 2 and streak.current_streak > 7 and not streak.grace_used_current_month:
            # Grâce : conserve streak mais ne l'incrémente pas
            await self._use_grace(user_id, today)
        else:
            # Reset
            await self._reset_streak(user_id, today)
    
    async def _check_level_up(self, user_id):
        user = await self.db.fetchrow(
            "SELECT * FROM user_levels WHERE user_id = $1", user_id
        )
        new_level = self._compute_level(user.total_xp)
        if new_level > user.current_level:
            await self._trigger_level_up(user_id, new_level)
    
    @staticmethod
    def _compute_level(total_xp: int) -> int:
        if total_xp >= 15000: return 4
        if total_xp >= 5000: return 3
        if total_xp >= 1000: return 2
        return 1
```

### 17.2 — Connexion event bus

Event listener Redis :

```python
async def gamification_event_listener():
    async for event in redis.subscribe("events:*"):
        await gamification_service.process_event(event.type, event.payload)
```

### 17.3 — Job nightly reset streaks

Voir `docs/DEPLOY.md` §3.1 — cron-jobs service.

```python
# Chaque jour à minuit Paris
async def reset_broken_streaks():
    """Reset streaks des users inactifs > 2 jours (sans grâce valide)."""
    yesterday = date.today() - timedelta(days=2)
    await db.execute(
        "UPDATE user_streaks SET current_streak = 0 "
        "WHERE last_activity_date < $1 AND grace_used_current_month = FALSE",
        yesterday
    )

# Chaque 1er du mois : reset grace
async def reset_monthly_grace():
    await db.execute("UPDATE user_streaks SET grace_used_current_month = FALSE")
```

---

## 18. ENDPOINTS API

Voir `docs/API.md` pour le format standard.

### 18.1 — Endpoints

| Endpoint | Méthode | Description |
|---|---|---|
| `/v1/gamification/status` | GET | Retourne XP, niveau, streak, badges obtenus, quêtes actives |
| `/v1/gamification/quests` | GET | Liste quêtes disponibles |
| `/v1/gamification/quests/:id/skip` | POST | Skip une quête |
| `/v1/gamification/badges` | GET | Catalogue badges + obtenus |
| `/v1/gamification/leaderboard/:scope` | GET | Classement (dept/metier/national) |
| `/v1/gamification/leaderboard/opt-in` | PATCH | Activer/désactiver classement |
| `/v1/gamification/rewards/claim/:id` | POST | Réclamer récompense débloquée |
| `/v1/gamification/streak/vacation-mode` | PATCH | Activer mode vacances |

### 18.2 — Exemple réponse `/status`

```json
{
  "user_id": "usr_...",
  "xp": {
    "total": 2340,
    "level": 2,
    "level_name": "Confirmé",
    "xp_to_next_level": 2660,
    "next_level_name": "Expert"
  },
  "streak": {
    "current": 23,
    "longest": 45,
    "last_activity_date": "2026-04-18",
    "vacation_mode_until": null
  },
  "badges": {
    "earned_count": 12,
    "total_count": 50,
    "recent": ["premier_devis", "streak_7j", "50_tickets"]
  },
  "quests": {
    "active_count": 3,
    "completed_count": 10
  },
  "rewards": {
    "unclaimed_count": 1,
    "total_value_saved_eur": 29
  }
}
```

---

## 19. ANTI-PATTERNS GAMIFICATION

### 19.1 — À NE JAMAIS FAIRE

1. ❌ **Gamification bloque l'utilisation** — un artisan sans niveau doit pouvoir faire un devis
2. ❌ **Pay-to-win** — pas d'XP achetable en cash
3. ❌ **Shame** — jamais "tu n'as rien fait cette semaine 😔"
4. ❌ **Notifications spam** — max 3/jour
5. ❌ **Compétition toxique** — pas de "dernier"
6. ❌ **Récompenses vides** — badges sans valeur tangible
7. ❌ **Progression punitive** — pas de perte de niveau
8. ❌ **Pression streak** — tolérance grâce obligatoire
9. ❌ **Données inventées** — les métriques gamification doivent être réelles
10. ❌ **Changement silencieux** — pas de modif barème sans annonce

### 19.2 — À FAIRE

1. ✅ Laisser choisir opt-in/opt-out partout
2. ✅ Récompenses tangibles (mois gratuits, produits)
3. ✅ Célébrer les milestones
4. ✅ Transparence sur les règles (page "Comment ça marche")
5. ✅ Adapter aux rythmes individuels
6. ✅ Prévenir avant changement barème
7. ✅ Feedback utilisateurs → ajustements
8. ✅ Tester A/B les éléments impactants
9. ✅ Monitoring anti-farming
10. ✅ Célébrer les succès silencieusement aussi (toast discret)

---

## 20. MÉTRIQUES ET ANALYTICS

### 20.1 — KPIs gamification

**Adoption** :
- % users qui complètent l'onboarding
- % users qui atteignent Confirmé (niveau 2)
- Temps moyen par niveau

**Engagement** :
- Streak moyen
- % users avec streak >7j
- Nombre moyen de badges obtenus par utilisateur

**Retention** :
- Churn à 30 jours avec vs sans quêtes complétées
- Corrélation score F117 "cerveau me connaît" et retention

**Virality** :
- Nombre moyen de parrainages par Maître
- Conversion parrainage (signup → paid)

### 20.2 — Dashboard admin `/admin/gamification`

- Distribution niveaux (combien de Débutants, Confirmés, etc.)
- Top badges obtenus
- Taux complétion par quête (identifie quêtes trop dures / trop faciles)
- Streak moyen national
- Rewards réclamées vs débloquées

### 20.3 — Alertes anomalies

- Farming detected : user avec 500+ XP en 1h sur actions répétitives
- Quête jamais complétée : taux <5% après 1000 users → ajuster
- Badge jamais obtenu : taux <1% après 3 mois → vérifier criteria

---

## 21. TESTS ET ÉQUILIBRAGE

### 21.1 — Tests

**Unit tests** :
- Calcul XP par event type
- Compute level depuis XP
- Streak update (+1, grace, reset)
- Plural rules dates

**Integration tests** :
- End-to-end : devis créé → XP attribuée → quête progressée → badge éventuellement débloqué

**A/B tests** :
- Quête A "10 prix" vs Quête B "Ajoute 5 clients" — laquelle convertit mieux en usage ?
- Notification streak à 20h vs à 19h

### 21.2 — Équilibrage

**Règle** : les paramètres XP/niveaux sont dans une config JSON, **pas hardcodés**. Ajustables sans deploy.

**Fichier** : `backend/data/gamification_config.json`

```json
{
  "version": "2026-04-01",
  "levels": {
    "1": { "name": "Débutant", "xp_min": 0 },
    "2": { "name": "Confirmé", "xp_min": 1000 },
    "3": { "name": "Expert", "xp_min": 5000 },
    "4": { "name": "Maître", "xp_min": 15000 }
  },
  "xp_amounts": {
    "devis_created": 20,
    "devis_signed": 50,
    "facture_paid": 30,
    "ticket_scanned": 5,
    ...
  },
  "daily_caps": {
    "ticket_scanned": 50,
    "photo_uploaded": 30
  },
  "streak_milestones": [7, 30, 100, 365]
}
```

### 21.3 — Ajustements post-launch

Révision trimestrielle :
- Si trop d'artisans niveau Maître en 2 mois → augmenter seuils
- Si peu atteignent Confirmé → baisser seuils ou simplifier quêtes
- Si badges obtenus en moyenne <5 par user → créer plus de badges atteignables

---

## 22. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `FEATURES.md` F75-F77 | Gamification enrichie Fabrice |
| `docs/FEATURES_COMPLETE.md` F80-F86 | Catalogue features gamification |
| `docs/FEATURES_COMPLETE.md` F114 | Parrainage (Ambassadeur) |
| `docs/FEATURES_COMPLETE.md` F117 | Score "cerveau me connaît" |
| `docs/FEATURES_COMPLETE.md` F120 | Capsules formation |
| `docs/MIGRATIONS.md` §5.5 | Schéma BDD (migration 020) |
| `docs/MIGRATIONS.md` §7.1 | referrals F114 |
| `docs/MIGRATIONS.md` §7.2 | capsules F120 |
| `docs/API.md` | Endpoints `/v1/gamification/*` |
| `docs/AGENTS.md` | Supervisor déclenche capsules |
| `docs/MEMORY.md` | F117 basé sur Mem0 |
| `docs/I18N.md` | Badges et quêtes traduits 6 langues |
| `docs/ARCH.md` | Event bus Redis |
| `docs/DEPLOY.md` §3 | cron-jobs service |
| `docs/SUPPORT-STRATEGY.md` | Communication milestones |
| `CLAUDE.md` | Règles conversation (pas formulaire) |
| `COMMUNICATION.md` | Ton "6 langues + gamification" |
| `BUILD_PLAN.md` | Sprint 1 quêtes onboarding, Sprint 7 rest |

### 22.1 — Références externes

- **Octalysis framework (Yu-kai Chou)** : référence de gamification
- **Duolingo streak system** : meilleur au monde
- **Nike Run Club** : badges et milestones
- **Habitica** : quêtes et niveaux

---

> **Ce fichier est la spec complète du système de gamification.**
> **~50 badges, 25+ quêtes, 4 niveaux, streak, récompenses réelles.**
> **Règle d'or n°1** : la gamification FACILITE, ne bloque JAMAIS.
> **Règle d'or n°2** : récompenses concrètes > virtuelles (mois offerts, accès, produits).
> **Règle d'or n°3** : conversation > formulaire (onboarding vocal).
> **Différenciation produit** : un artisan BTP n'a pas l'habitude d'une app — la gamification fait la différence entre app utilisée et abandonnée.
