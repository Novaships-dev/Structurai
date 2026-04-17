# STRUCTORAI — PARCOURS UX & ARCHITECTURE INTERFACE

> Ce fichier est la source de vérité pour l'interface de l'app.
> Claude Code le lit AVANT de créer un composant mobile.
> Règle : si l'artisan doit réfléchir, c'est raté.
> Date : 17/04/2026 (audit V6 : onboarding inversé, écrans import/parrainage/concurrent-analyzer)

---

## CIBLE UTILISATEUR

Un plombier de 45 ans qui a un Samsung Galaxy A15, qui déteste les apps complexes, qui a fui Sage et EBP parce que "c'est trop compliqué", et qui fait ses devis sur Word le soir à 22h. Il veut que ça marche, pas comprendre comment ça marche.

---

## NAVIGATION PRINCIPALE — 5 onglets en bas

```
💬 Chat  |  📄 Documents  |  🏗️ Chantiers  |  📊 Business  |  ✅ À faire
```

- Toujours visibles, jamais masqués
- Icône + texte en dessous (jamais une icône seule)
- L'onglet actif est coloré, les autres sont gris
- Badge rouge (pastille) sur les onglets qui ont des notifications (Documents = 2 impayés, À faire = 3 actions)

**En haut de chaque écran :**
- Logo STRUCTORAI petit à gauche
- Barre de recherche au centre (vocale ou texte) : "Cherche un client, un devis, un chantier..."
- Avatar/initiales de l'artisan à droite → tap = menu profil

---

## ONGLET 1 — 💬 CHAT

**C'est le coeur de l'app. 80% du temps de l'artisan est ici.**

### Éléments de l'écran
- Historique de conversation (bulles comme WhatsApp)
- Les résultats des agents s'affichent inline (carte devis, carte client, photo analysée, aperçu PDF)
- Bouton micro GROS en bas au centre (60px, couleur primaire, toujours visible) — push-to-talk
- Champ texte à gauche du micro (pour ceux qui préfèrent taper)
- Bouton caméra à droite du micro (pour scanner ticket ou photo chantier)
- Indicateur vocal animé quand le cerveau écoute (onde sonore)
- Indicateur "Le cerveau réfléchit..." quand l'IA traite (pas de spinner vide)

### Comportements
- L'artisan parle → le texte transcrit s'affiche en temps réel (comme les sous-titres)
- Le cerveau répond en texte ET en vocal (le TTS lit la réponse si l'artisan a parlé)
- Si le cerveau génère un devis → carte cliquable inline avec aperçu (montant, client, postes) + bouton "Voir PDF" + bouton "Envoyer"
- Si l'artisan envoie une photo → l'Agent Vision analyse et affiche le résultat inline ("Ticket Point P 234€ détecté — chantier Dupont ?")
- Si le cerveau a besoin d'une info manquante → il demande dans la conversation, pas un popup

### Ce qu'on peut faire depuis le chat
- Créer un devis (vocal ou texte)
- Scanner un ticket (bouton caméra)
- Prendre une photo chantier (bouton caméra)
- Relancer un client ("Relance Martin")
- Voir ses impayés ("Mes impayés")
- Voir une fiche client ("Fiche de Mme Leroy")
- Créer son site web ("Crée-moi un site")
- Poser une question ("Quel taux de TVA pour une SDB ?")
- Tout le reste — le chat est le raccourci universel

---

## ONGLET 2 — 📄 DOCUMENTS

**L'artisan voit tous ses documents commerciaux.**

### Sous-onglets horizontaux en haut (scrollables)
```
Devis | Factures | Acomptes | Avoirs | Situations | Achats
```

### Chaque sous-onglet affiche une liste
- Tri par date (plus récent en haut) par défaut
- Filtre par : client, chantier, statut, date
- Recherche par numéro ou nom client

### Carte de chaque document
```
📄 Devis #2026-047 — Mme Leroy
   SDB complète 8m² · 2 850€ HT
   Envoyé il y a 3 jours
   🟡 En attente de signature
   [Voir] [Renvoyer] [Dupliquer] [Relancer]
```

### Statuts visuels (couleurs)
| Statut | Couleur | Icône |
|--------|---------|-------|
| Brouillon | Gris | ✏️ |
| Envoyé / En attente | Jaune | 📤 |
| Signé / Accepté | Vert | ✅ |
| Refusé / Expiré | Rouge | ❌ |
| Payé | Vert foncé | 💰 |
| Impayé / En retard | Rouge vif | ⚠️ |

### Tap sur un document → écran détail
- En-tête : numéro, date, client, chantier, statut
- Aperçu PDF intégré (scrollable)
- Boutons d'action en bas : Envoyer, Dupliquer, Transformer (devis → facture, devis → acompte), Relancer, Télécharger PDF, Supprimer
- Historique : "Envoyé le 03/04, ouvert le 04/04, relancé le 07/04"

### Bouton "+" flottant en bas à droite
- Tap → choix : Nouveau devis (vocal), Nouveau devis (manuel), Nouvelle facture, Acompte, Avoir

### Sous-onglet "Achats"
- Bons de commande fournisseurs
- Factures fournisseurs reçues (scannées par l'Agent Vision)
- Filtre par fournisseur, chantier, date
- Total achats du mois visible en haut

---

## ONGLET 3 — 🏗️ CHANTIERS

**Vue visuelle de tous les chantiers, passés et en cours.**

### Vue pipeline (Kanban horizontal) en haut
```
Prospect → Devis envoyé → Signé → En cours → Terminé
```
- Chaque colonne contient des cartes chantier
- Les cartes sont déplaçables (drag & drop) pour changer le statut
- Scroll horizontal entre les colonnes

### Carte chantier
```
┌─────────────────────────┐
│ 📸 [photo principale]   │
│ Mme Leroy — SDB 8m²    │
│ 2 850€ HT              │
│ Marge : 31% 🟢          │
│ ⏱ Jour 3/5             │
│ 📸 4 photos             │
└─────────────────────────┘
```

### Tap sur une carte → page détail chantier

C'est LA page la plus importante de l'app. Tout ce qui concerne un chantier est ici :

**Sections (scroll vertical, chaque section est un bloc séparé) :**

1. **En-tête** : nom client, adresse (tap = ouvre Maps/Waze), dates début/fin prévues, montant devis, statut
2. **Marge en temps réel** : barre de progression (budget consommé vs prévu), montant dépensé, marge en %, alerte si < 20%
3. **Timer** : bouton Start/Stop gros. Temps passé aujourd'hui. Temps total. Comparaison prévu vs réel. Historique des sessions
4. **Documents** : devis + factures + acomptes + situations liés à ce chantier. Tap = ouvre le document
5. **Photos & vidéos** : galerie du chantier, triée par date, catégorisée (avant/pendant/après/problème). Bouton "Ajouter photo/vidéo". Bouton "Montage avant/après"
6. **Achats** : tickets et factures fournisseurs rattachés. Total dépenses matériaux. Tap = détail
7. **Équipe** (si employés) : qui travaille sur ce chantier, heures par personne, intérimaires, sous-traitants
8. **Tâches** (optionnel) : checklist libre — l'artisan ajoute des tâches, les coche
9. **Notes** : notes vocales ou texte, horodatées — l'artisan dicte un mémo sur le chantier

### Bouton "+" flottant → Nouveau chantier (vocal ou manuel)

---

## ONGLET 4 — 📊 BUSINESS

**Tableau de bord — vue d'ensemble de l'activité.**

### Section 1 — Chiffres clés (en haut, GROS, lisibles en plein soleil)
```
CA ce mois         Impayés           Marge moyenne
8 400€             2 300€            31%
+12% vs mois       2 factures        ⚠️ -3pts
dernier             🔴
```
Chaque chiffre est cliquable → détail

### Section 2 — Mes agents (le travail de l'équipe IA)
```
🤖 Devis — 12 devis ce mois, 4 signés (33%)
🤖 Relance — 3 relances envoyées, 1 payée
🤖 Compta — 28 tickets scannés
🤖 Réputation — 6 avis collectés, 4.8★
🤖 Prospection — 2 rappels envoyés
🤖 Fiscalité — URSSAF dans 12j, 2 600€
🤖 Vision — 45 photos analysées
🤖 Site Web — 142 visites ce mois
🤖 Email — 12 emails traités, 2 prospects détectés
```
L'artisan voit que ses agents BOSSENT pour lui. C'est l'argument marketing rendu visible.

### Section 3 — Statistiques (scroll vers le bas)
- CA par mois (graphique barres, simple, 6 derniers mois)
- Taux d'acceptation devis (% signés / envoyés)
- Top 5 clients (CA généré)
- Marge par chantier (meilleur et pire)
- Encaissé vs facturé (retards de paiement)
- Trésorerie prévisionnelle 30 jours

### Section 4 — Mon site web
- Aperçu miniature du site
- Nombre de visites ce mois
- Dernière MAJ + bouton "Voir" / "Modifier"

---

## ONGLET 5 — ✅ À FAIRE

**File de validation. Tout ce que les agents proposent, l'artisan valide ici.**

### Regroupé par priorité
```
🔴 URGENT (2)
  → Facture Martin impayée 25 jours
    [Voir le message] [Envoyer la relance] [Ignorer]
  → Courrier URSSAF reçu par email — mise en demeure
    [Voir le courrier] [Rappel dans 3 jours]

🟡 À VALIDER (4)
  → Réponse avis Google Mme Leroy (5★)
    [Voir la réponse] [Publier] [Modifier]
  → 3 photos à ajouter sur ton site web
    [Voir les photos] [Publier sur le site] [Ignorer]
  → Publication Instagram avant/après SDB Dupont
    [Voir le post] [Publier] [Modifier]
  → Rappeler architecte Lefebvre (45 jours sans contact)
    [Voir le message] [Envoyer] [Reporter]

🟢 FAIT AUJOURD'HUI (3)
  → ✅ Ticket 234€ Point P classé sur chantier Dupont
  → ✅ Devis Leroy envoyé et signé
  → ✅ Avis Google M. Bernard publié
```

### Interaction
- Swipe droite = valider/envoyer
- Swipe gauche = ignorer/reporter
- Tap = voir le détail avant de décider
- Les items "Fait" disparaissent après 24h

---

## MENU PROFIL (icône en haut à droite)

Accessible depuis tous les écrans. Tap sur l'avatar → écran profil avec sections :

### Sections du menu profil
| Section | Ce qu'on y trouve |
|---------|------------------|
| **Mon entreprise** | SIRET, décennale, RGE, Qualibat, logo, statut juridique, régime TVA, zone d'intervention. Gamification : "Profil complet à 85%" |
| **Mes clients** | Répertoire cherchable. Fiche client = coordonnées + historique devis/factures/chantiers + notes + photos |
| **Mes fournisseurs** | Répertoire cherchable. Fiche = coordonnées + historique achats + prix négociés (Mem0) |
| **Ma bibliothèque** | Ouvrages, matériaux, main d'oeuvre avec MES prix. Navigable par catégorie, cherchable. Ajout/modif/suppression |
| **Mon calendrier** | Vue semaine/mois. RDV, chantiers, échéances fiscales, relances. Synchro Google Calendar |
| **Mon site web** | Aperçu complet, pages, modifier, stats visites, URL |
| **Mes employés** | Fiches employés, intérimaires, sous-traitants. Heures, congés, pointage. Mode Compagnon pour l'employé (F124 — code 4 chiffres, photos début/fin) |
| **Mon abonnement** | Plan actuel (Starter / Pro / Pro annuel / Business / Business annuel / Lifetime / Fédération), facturation, upgrade, historique paiements, add-on Agent Téléphone (V2) |
| **Mon parrainage** (F114) | Code parrain personnel, liste des filleuls, mois offerts, crédits Point P accumulés, badge Ambassadeur — écran `settings/parrainage.tsx` |
| **Importer depuis un concurrent** (F112) | Upload export Obat / Tolteck / Batigest / EBP / Excel / Facture.net → mapping automatique → preview → validation → import complet (clients, devis, ouvrages, historique) — écran `settings/import.tsx` |
| **Analyser un devis concurrent** (F121) | Upload PDF/photo d'un devis concurrent → analyse ligne par ligne vs référentiels BTP + Mem0 → argumentaire pour gagner le chantier — écran `devis/concurrent-analyzer.tsx` |
| **Réglages** | Langue (6 langues), notifications (fréquence, canaux), mode sombre, voix du cerveau (homme/femme), agents actifs/inactifs |
| **Aide** | Ouvre le chat — le cerveau EST le support |
| **Exporter mes données** | Export JSON/CSV de toutes les données (RGPD). **Pas d'API export symétrique pour les concurrents (F118 — switching cost)** |
| **Pack contrôle fiscal** (F129) | Génère un ZIP signé horodaté contenant devis + factures + tickets OCR + justifs TVA + registres (en cas de contrôle fiscal) |

---

## PARCOURS CLÉS

### Parcours 1 : Premier lancement — ONBOARDING INVERSÉ (F125, audit V6, ~90 secondes)

Le nouveau flow n'est plus "signup → test". C'est "test → valeur prouvée → signup pour envoyer".

```
1. L'artisan arrive sur structorai.app (landing ou via pub)
2. Landing hero : "Tes soirées de devis sont terminées. Teste 90 secondes. Pas de signup."
3. L'artisan clique "Essaie maintenant" → ouverture de app/(public)/try.tsx (F115 sandbox)
4. Chat public (endpoint /v1/public/chat, rate-limited par IP, session Redis 15 min)
5. Le cerveau parle :
   "Salut ! Je suis l'assistant des artisans BTP. Dis-moi ce que tu veux que je fasse.
    Par exemple : 'Fais-moi un devis pour SDB complète 8m² chez M. Dupont, Marseille'"
6. L'artisan dicte ou tape → le cerveau génère un VRAI devis avec prix marché, TVA,
   mentions obligatoires → affiche le PDF dans le chat
7. "Voilà ton devis. 1 850€ HT, TVA 10% = 2 035€ TTC. Pour l'envoyer à ton client,
    crée un compte — c'est gratuit."
8. L'artisan voit la VALEUR avant de signer → clique "Crée mon compte"
9. Signup minimal (email + mot de passe) → auto-fill SIRET via API INSEE
10. "C'est bon, je peux envoyer le devis à M. Dupont ! Tu veux que je le fasse maintenant ?"

Cible conversion : >25% (vs >12% avec l'onboarding classique signup-first).
Les infos manquantes (décennale, RGE, logo, régime TVA) sont demandées PLUS TARD,
au fil des usages, quand le cerveau en a besoin — PAS pendant l'onboarding.

Écrans concernés :
- app/(public)/try.tsx — sandbox publique (F115)
- Landing hero restructurée (F125)
```

### Parcours 1bis : Onboarding classique signup-first (conservé pour utilisateurs loggés direct sur l'app)

```
1. L'artisan installe l'app → écran de bienvenue simple (logo + "Commencer")
2. Tombe sur le chat. Le cerveau parle :
   "Salut ! Je suis ton assistant IA. Dis-moi juste ton prénom et ton métier."
3. L'artisan dit "Fabrice, plombier"
4. "Super Fabrice ! Tu travailles dans quel coin ?"
5. "Marseille"
6. "Top. Tu veux me donner ton SIRET ? Je remplis tout automatiquement."
7. L'artisan donne le SIRET → auto-fill via API INSEE (nom entreprise, adresse, statut)
8. "C'est bon, je peux déjà faire tes devis ! Tu veux essayer ?"
9. Premier devis possible en 2 minutes
```

### Parcours 2 : Créer un devis (vocal, < 2 min)

```
1. L'artisan ouvre l'app → onglet Chat
2. Appuie sur le micro : "Devis pour Madame Leroy, remplacement chauffe-eau 200 litres, rue de la République à Marseille"
3. Le cerveau :
   - Crée la fiche client (ou retrouve Mme Leroy)
   - Décompose les postes (dépose ancien, fourniture, pose, raccordements, mise en service)
   - Met les prix (Mem0 de l'artisan ou référentiel plomberie)
   - Applique la TVA 10% (rénovation)
   - Génère le PDF avec les 47 mentions
4. Affiche dans le chat : carte devis avec aperçu
   "Devis Mme Leroy — 1 850-2 200€ HT. Tu veux voir le détail ou je l'envoie ?"
5. L'artisan dit "Envoie" → email + SMS + signature électronique
6. Notification quand le client signe → facture d'acompte auto-générée
```

### Parcours 3 : Créer un devis (manuel, via l'onglet Documents)

```
1. Onglet Documents → bouton "+" → "Nouveau devis"
2. Sélectionner un client (recherche ou créer)
3. Ajouter les postes :
   - Recherche dans la bibliothèque (taper ou vocal)
   - Ou ajouter un poste libre (descriptif + prix)
   - Chaque ligne : descriptif, quantité, prix unitaire, TVA
   - Le cerveau calcule les totaux automatiquement
4. Vérifier l'aperçu PDF
5. Envoyer (email/SMS/WhatsApp) ou sauvegarder en brouillon
```

### Parcours 4 : Scanner un ticket (< 5 sec)

```
1. Bouton caméra dans le chat (ou depuis la fiche chantier)
2. Prendre la photo du ticket
3. L'Agent Vision reconnaît un ticket → l'Agent Compta extrait :
   montant, date, fournisseur, articles
4. Popup : "234€ Point P — Chantier Dupont ? ✅ / Modifier"
5. Un tap → classé dans les achats du chantier
```

### Parcours 5 : Routine matin (< 30 sec)

```
1. Notification WhatsApp du cerveau à 7h (briefing matin) :
   "Bonjour Fabrice ! Aujourd'hui :
    - Chantier Martin (jour 4/7, marge 28% ⚠️)
    - 1 relance à envoyer (Dupont, 15 jours)
    - 1 avis Google à valider
    Tu veux que j'envoie la relance ?"
2. L'artisan répond "oui" sur WhatsApp → relance envoyée
3. Ouvre l'app → onglet "À faire" → 2 items restants → swipe, swipe → 10 sec
4. Part sur le chantier
```

### Parcours 6 : Créer son site web (< 5 min)

```
1. Chat → "Crée-moi un site"
   (ou suggestion du cerveau quand le profil est complet à 80%+)
2. Le cerveau génère un aperçu en 30 secondes :
   - Page accueil (accroche IA + photo principale)
   - Page services (basée sur les métiers de l'artisan)
   - Page galerie (avant/après depuis les photos chantier)
   - Page avis (tirés de Google)
   - Page contact (téléphone, email, formulaire)
3. L'artisan voit l'aperçu dans l'app
4. "Change la couleur en bleu" / "Ajoute cette photo" / "Enlève ce texte"
5. "C'est bon" → publié sur artisan-leroy-plombier-marseille.structorai.app
6. Chaque mois, notification :
   "3 nouvelles photos + 2 avis à ajouter à ton site. Tu veux voir ?"
   → Aperçu dans "À faire" → l'artisan valide ou refuse chaque élément
   → Le cerveau ne publie JAMAIS sans validation
```

### Parcours 7 : Terminer un chantier

```
1. L'artisan dit "J'ai fini chez Dupont" (chat vocal) ou passe la carte en "Terminé" (Kanban)
2. Le cerveau enchaîne AUTOMATIQUEMENT :
   - Agent Planning → arrête le timer, calcule le temps total
   - Agent Compta → calcule la marge finale
   - Agent Devis → génère la facture finale (ou de solde si acompte déjà facturé)
   - Agent Réputation → programme le SMS avis Google J+2
   - Agent Vision → prépare le montage avant/après
   - Agent Site Web → note le nouveau contenu pour la prochaine MAJ
3. L'artisan voit : "Chantier Dupont terminé ✅
   Marge finale : 34%. Facture 3 200€ TTC. J'envoie à Mme Dupont ?
   Et dans 2 jours je lui demanderai un avis Google."
4. L'artisan dit "oui" → tout est fait
5. PV de réception généré → envoyé au client pour signature
```

---

## RÈGLES UX — LA LOI DE L'INTERFACE

### Visuel
| Règle | Valeur | Pourquoi |
|-------|--------|----------|
| Taille texte minimum | 16px | Yeux fatigués du chantier, plein soleil |
| Taille boutons minimum | 48×48px | Doigts épais, parfois avec des gants |
| Contraste | WCAG AA minimum | Écran en plein soleil |
| Bouton micro | 60px, couleur primaire, TOUJOURS visible | C'est l'action principale de l'app |
| Icônes | Toujours avec texte dessous | Une icône seule est ambiguë |
| Éléments par écran | Maximum 5 blocs visibles sans scroll | Au-delà le cerveau humain décroche |
| Mode sombre | Oui, commutable dans réglages | Le soir dans le canapé |
| Animations | Minimales, utilitaires | Samsung A15 = mid-range, pas un iPhone 16 |

### Langage
| ❌ Ne JAMAIS dire | ✅ Dire à la place |
|-------------------|-------------------|
| Pipeline | Tes chantiers |
| CRM | Tes clients |
| OCR | Scanner un ticket |
| Agent | Mon assistant / Le cerveau |
| Module | (ne pas en parler dans l'app, seulement dans le marketing) |
| Créances en souffrance | Tes impayés |
| Données | Tes infos |
| Configuration | Réglages |
| Synchroniser | Mettre à jour |
| Webhook | (jamais visible par l'artisan) |

### Comportement
| Règle | Détail |
|-------|--------|
| Chaque action en 1-3 taps max | Devis vocal = micro + parler + "oui" |
| Le cerveau propose, l'artisan valide | Jamais l'inverse |
| Pas de popup sauf erreur critique | Les suggestions passent par le chat ou "À faire" |
| Pas de tutoriel popup | Le cerveau guide par la conversation |
| Pas de formulaire de plus de 5 champs | Si plus → conversation vocale guidée |
| Chargement instantané | Données critiques en cache SQLite local |
| Optimistic updates | L'UI change immédiatement, le sync est en background |
| Notifications max 3/jour | Regroupées, pas de spam |
| Erreur = message humain | "Oups, j'ai pas compris. Tu peux reformuler ?" pas "Error 500" |
| Onboarding progressif | On demande les infos quand on en a besoin, pas tout d'un coup |

---

## RESPONSIVE — MOBILE FIRST

L'app est conçue pour mobile (React Native / Expo). Le web (PWA) est secondaire.

| Élément | Mobile | Tablette | Desktop (PWA) |
|---------|--------|----------|---------------|
| Navigation | 5 onglets en bas | 5 onglets en bas | Sidebar à gauche |
| Chat | Plein écran | Plein écran | Panel gauche (chat) + panel droit (résultat) |
| Devis PDF | Aperçu scrollable | Aperçu large | Aperçu taille réelle |
| Kanban | Scroll horizontal | 4 colonnes visibles | 5 colonnes visibles |
| Tableau de bord | Chiffres empilés | 2 colonnes | 3 colonnes |

---

> **Note pour Claude Code :** Ce fichier définit TOUTE l'interface.
> Chaque composant React Native doit respecter ces règles.
> En cas de doute sur un choix UX, relire ce fichier.
> Le test ultime : "Est-ce qu'un plombier de 45 ans avec un Samsung A15
> comprendrait ça en 2 secondes ?" Si non, simplifier.
