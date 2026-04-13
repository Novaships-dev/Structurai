# PRODUCT-CONTEXT — STRUCTORAI (Tool #2 Nova)
> Source de vérité du deuxième SaaS Nova.
> Statut : BUILD — launch prévu Juin 2026
> Priorité : Tool #2 dans le portfolio Nova (remplace EngageRadar)
> Dernière mise à jour : 14/04/2026
> Pivot stratégique : EngageRadar abandonné (marché saturé, ICP = codeurs qui buildent eux-mêmes). Nouveau Tool #2 = STRUCTORAI, SaaS pour artisans du bâtiment.

---

## DÉCISION DE PIVOT — 02/04/2026

**Pourquoi EngageRadar est abandonné :**
1. Le marché Reddit monitoring est saturé (15+ outils identiques)
2. L'ICP (indie hackers, devs) build ses propres outils avec n8n + Claude en 20 min
3. L'algo X 2026 a tué les reply guys (API replies bloquée le 25/02/2026)
4. Le backlash anti-AI slop rend tout outil "AI reply" toxique
5. Aucune différenciation réelle vs SubredditSignals, Redreach, Prems, etc.

**Pourquoi les artisans du bâtiment :**
1. 500 000 entreprises BTP en France, majorité TPE/artisans indépendants
2. 83% n'ont PAS commencé leur transition numérique
3. Ils NE PEUVENT PAS builder leur propre solution (zéro compétence tech)
4. Ils ont de l'ARGENT (CA moyen artisan : 80-200K€/an)
5. La douleur est QUOTIDIENNE (6-8h/semaine d'admin)
6. Les outils existants sont faits pour des gens derrière un bureau — pas pour un mec avec du plâtre sur les mains
7. La facturation électronique Factur-X devient OBLIGATOIRE en septembre 2026 — fenêtre massive
8. L'expérience terrain de Fabrice dans le BTP = avantage compétitif inimitable

---

## EN UNE PHRASE

**"L'assistant IA qui gère ton business pendant que tu es sur le chantier."**

Une app mobile avec cerveau IA spécialisé bâtiment — chat vocal intégré, scan de tickets, devis par la voix, relances automatiques, suivi de chantiers — accessible sur Android (Play Store), iPhone (PWA), et WhatsApp.

**Ce que c'est :** Un assistant personnel IA dans ta poche qui comprend le métier du bâtiment et qui travaille 24/7 pour toi. Tu lui parles, il fait le reste.

**Ce que ce n'est PAS :**
- Pas un logiciel de devis de plus (Batappli, Obat, EBP = logiciels de bureau)
- Pas un chatbot générique (ChatGPT ne connaît pas la TVA BTP ni la décennale)
- Pas de l'automatisation n8n déguisée en Python (un vrai cerveau LLM métier)
- Pas un outil qui nécessite de s'asseoir devant un écran
- Pas une app de plus qu'on ouvre jamais (le chat vocal rend l'interaction naturelle)

**La promesse :** "Rien à faire ce soir." L'artisan rentre du chantier, le cerveau a tout géré.

**Multi-langue dès le launch :** Français, Anglais, Turc, Espagnol, Portugais, Arabe. Le cerveau IA répond dans la langue configurée par l'utilisateur. Toute l'interface app (boutons, menus, labels, notifications) est traduite. Les devis et factures sont générés dans la langue du client de l'artisan (configurable par client). Architecture i18n extensible pour ajouter d'autres langues (fichiers JSON).

**Pourquoi ces 6 langues :**
- Français : marché primaire (France)
- Turc : énorme communauté artisans BTP en France + marché Turquie
- Arabe : forte communauté artisans BTP en France + Maghreb + Moyen-Orient
- Espagnol : Espagne + Amérique latine (gros marché BTP)
- Portugais : Portugal + Brésil (gros marché BTP)
- Anglais : international + crédibilité + marché UK/US/Australie

**Distribution :**
- Android → Google Play Store (React Native via Expo, build EAS)
- iPhone → PWA installable (Expo Web, bypass Apple Store complet)
- WhatsApp → canal parallèle pour les actions rapides depuis le chantier

---

## LE PROBLÈME — EXPÉRIENCE TERRAIN

Source : expérience directe de Fabrice dans le bâtiment.

### La journée d'un artisan du bâtiment en France

**6h30-7h** — Route vers le chantier. Déjà des appels de prospects. Notes sur un bout de papier dans le camion.

**7h-12h** — Chantier. Achats chez le grossiste (Point P, Cedeo, BigMat). Tickets de caisse dans la poche. D'autres prospects appellent. Notes à la volée. Le papier se perd.

**12h-13h** — Pause. Vérification rapide des messages. Un client demande un devis par téléphone. Le prix est dit à haute voix mais le client veut un devis écrit. Noté dans les Notes du téléphone ou sur un bout de papier.

**13h-18h/19h** — Suite du chantier. Le temps dépasse le prévu. La marge fond. Pas de visibilité sur la rentabilité réelle.

**19h-19h30** — Retour. Crevé. Les tickets de caisse sont froissés dans la poche. Il faut :
- Faire les devis en attente (1-2h par devis, 9 sur 10 refusés)
- Envoyer les factures
- Relancer les impayés
- Trier les tickets pour le comptable
- Répondre aux prospects
- Relancer les architectes
- Faire la paperasse URSSAF/TVA

**20h+** — L'artisan devrait être avec sa famille. Il est devant Word ou Excel à faire des devis.

### Les 10 douleurs identifiées

| # | Douleur | Impact | Fréquence |
|---|---------|--------|-----------|
| 1 | Appels prospects sur le chantier → notes perdues → devis en retard de 2 jours → client parti ailleurs | Perte de chantiers | Quotidien |
| 2 | Tickets de caisse/factures fournisseurs partout → oublis de frais déductibles → perte d'argent | Perte financière | Quotidien |
| 3 | Temps de chantier sous-évalué → dépassements → marge qui fond sans visibilité | Rentabilité invisible | Chaque chantier |
| 4 | Plusieurs chantiers + prospects + architectes en parallèle → tout dans la tête → oublis | Prospects perdus | Permanent |
| 5 | Devis interminables le soir → 1-2h par devis → 9/10 refusés → temps volé à la famille | Épuisement + perte temps | Hebdomadaire |
| 6 | Factures + relances impayés → Excel/papier → retards paiement → trésorerie morte | Cash flow | Hebdomadaire |
| 7 | Paperasse société (URSSAF, TVA, comptable) → repoussé → stress permanent | Stress + amendes | Mensuel |
| 8 | Prospection impossible → pas le temps → bouche-à-oreille insuffisant | Carnet de commandes vide | Permanent |
| 9 | Visites chantier pour devis → 1-2h par visite × 10 devis → 10-20h/mois pour 1 chantier gagné | Temps + essence perdus | Hebdomadaire |
| 10 | Mails pro non suivis → demandes noyées → administratif raté | Opportunités perdues | Quotidien |

### Ce qu'ils utilisent aujourd'hui

| Outil actuel | % des artisans | Problème |
|-------------|---------------|---------|
| Papier + stylo | ~40% | Se perd, illisible, pas de suivi |
| Notes du téléphone | ~30% | Pas structuré, pas partageable |
| Excel/Word | ~20% | Lent, pas conforme, pas métier |
| Logiciel BTP (Batappli, Obat, EBP) | ~10% | Trop complexe, nécessite un bureau |

**Insight clé :** On ne se bat pas contre un logiciel. On se bat contre un bout de papier et l'app Notes du téléphone. La barre d'entrée est au sol.

---

## LA SOLUTION — 15 MODULES, 1 CERVEAU

### Principe fondamental

L'artisan ne s'assoit JAMAIS devant un ordinateur. Il interagit par 3 canaux qui parlent au MÊME cerveau :

**Canal 1 — App native Android (Google Play Store)**
- App complète React Native (Expo) avec dashboard, pipeline chantiers, compta, stats
- **Chat intégré avec MODE CONVERSATION VOCALE COMPLÈTE** :
  - Bouton micro flottant (FAB) permanent sur TOUTES les pages
  - Push-to-talk : appuie → parle → relâche → le cerveau traite
  - **Mode mains libres** : l'artisan a les mains sales/occupées. Il dit "Hey [Nom]" ou appuie une fois → le cerveau ÉCOUTE en continu → l'artisan parle → le cerveau RÉPOND À VOIX HAUTE avec une voix naturelle fluide et humaine
  - **Choix de voix** : homme ou femme, configurable dans les settings. Voix naturelle (pas robotique), fluide, avec intonation humaine. Powered by ElevenLabs (meilleure qualité) ou OpenAI TTS (fallback moins cher)
  - **Conversation bidirectionnelle** : l'artisan parle → le cerveau comprend → le cerveau PARLE la réponse → l'artisan peut enchaîner sans toucher l'écran. Comme parler à un assistant humain sur le chantier.
  - Aussi utilisable en texte (clavier) pour ceux qui préfèrent
  - Commandes vocales CONTEXTUELLES : si l'artisan est sur la page d'un chantier et dit "ajoute un ticket de 234€ chez Point P", le cerveau sait que c'est pour CE chantier
  - **Le cerveau lit les infos importantes à voix haute** : "Tu as 3 factures impayées pour un total de 4 800€. La plus ancienne c'est Martin, 2 200€ depuis 25 jours. Tu veux que je relance ?"
  - **La voix parle dans la langue de l'artisan** (FR/EN/TR/ES/PT/AR) avec un accent naturel par langue
- Notifications push natives (fiables, instantanées)
- Caméra native fluide pour scan tickets de caisse
- Mode hors-ligne : stockage local + sync quand le réseau revient
- L'artisan parle au cerveau SANS quitter l'app, SANS toucher l'écran

**Pourquoi le mode vocal complet est CRITIQUE :**
L'artisan est sur le chantier. Il a les mains dans le plâtre, la peinture, le ciment. Il ne peut PAS taper sur un écran. Il ne peut PAS naviguer dans une app. Il peut PARLER. Le cerveau doit être un collègue invisible à qui tu parles et qui te répond — pas un écran à toucher.

**Canal 2 — PWA iOS (bypass Apple Store)**
- Même app en version web progressive (PWA) via Expo Web
- S'installe sur l'écran d'accueil iPhone comme une app native
- Icône, splash screen, plein écran, même expérience visuelle
- **Mode conversation vocale identique** : push-to-talk + réponse vocale du cerveau (voix homme/femme au choix)
- Web Speech API pour la reconnaissance vocale + Audio API pour la lecture TTS
- Zéro review Apple, zéro $99/an, zéro attente
- Notifications via email/SMS (les push PWA sur iOS sont limités)

**Canal 3 — WhatsApp (quand l'artisan n'est PAS sur l'app)**
- Pour les actions rapides depuis le chantier : dicter un devis, envoyer un ticket photo, poser une question
- L'artisan envoie un vocal ou une photo → le cerveau traite
- Pas besoin d'ouvrir l'app — WhatsApp suffit pour 80% des actions rapides
- Historique de conversation PARTAGÉ avec le chat in-app (même thread, même mémoire)

**1 codebase → 3 canaux :** React Native (Expo) compile vers Android natif + Web PWA. Le backend est identique. Le cerveau est le même. L'artisan peut commencer un devis sur WhatsApp depuis le chantier et le retrouver dans l'app le soir.

**Multi-langue :** i18next intégré dès le jour 1. Français (prioritaire) + Anglais (international). Architecture prête pour ajouter des langues (fichiers JSON de traduction). Le cerveau IA répond dans la langue configurée par l'utilisateur.

### Le cerveau IA — 4 couches

**COUCHE 1 — Compréhension métier BTP (LLM spécialisé)**

Le LLM n'est pas un ChatGPT générique. Il est spécialisé bâtiment via un system prompt métier + RAG sur une base de données BTP :

- Comprend le langage artisan : "sdb complète" = salle de bain avec dépose, plomberie, carrelage, faïence, douche ou baignoire, meuble vasque, sèche-serviette, joints silicone, évacuation gravats
- Connaît les taux TVA BTP : 5.5% (rénovation énergétique), 10% (travaux rénovation), 20% (neuf + fournitures)
- Sait appliquer les 47 mentions obligatoires d'un devis BTP 2026
- Connaît les prix moyens du marché par région et par corps de métier (base de données BatiChiffrage ou équivalent)
- Détecte les oublis : "Vous n'avez pas mentionné l'évacuation des gravats — je l'ajoute ?"
- Génère des documents conformes Factur-X (obligatoire septembre 2026)
- Pré-remplit la décennale, RGE, Qualibat, conditions de paiement, pénalités de retard, droit de rétractation

**COUCHE 2 — Mémoire persistante par artisan (Mem0/MemOS)**

Le cerveau se souvient de TOUT pour chaque artisan :

- **Mémoire prix** : tes prix habituels, tes marges, tes coûts fournisseurs réels (pas les prix catalogue)
- **Mémoire clients** : historique complet de chaque client (chantiers passés, délais de paiement, niveau de négociation, personnalité)
- **Mémoire fournisseurs** : où tu achètes, les remises que tu as, les délais de livraison
- **Mémoire chantiers** : temps réel vs estimé sur chaque type de chantier → le cerveau apprend TON rythme réel
- **Mémoire réseau** : architectes, apporteurs d'affaires, historique de collaboration, fréquence de contact
- **Mémoire style** : comment tu formules tes devis, ton niveau de détail, tes options habituelles

Plus l'artisan utilise l'outil, plus il est précis. Au bout de 3 mois, le cerveau fait des devis EXACTEMENT comme l'artisan les ferait — mais en 2 minutes au lieu de 2 heures.

**COUCHE 3 — 13 agents autonomes (pattern Ouroboros)**

Pas de l'automatisation "if/then". De vrais agents LLM avec conscience d'arrière-plan (background consciousness) inspirés d'Ouroboros. Chaque agent :
- A sa propre spécialité et ses propres outils
- Prend des décisions intelligentes, pas des règles bêtes
- Communique avec les autres agents via le Supervisor
- A un budget LLM dédié et tracké
- Est proactif : il pense et agit ENTRE les tâches, pas seulement quand on lui demande

Les 13 agents :

**Agent DEVIS (le killer feature)**
- Le devis est CONVERSATIONNEL : le cerveau dicte poste par poste, l'artisan valide ou modifie chaque prix en vocal
- Chaque modification de prix est mémorisée dans Mem0 pour les prochains devis
- Le cerveau utilise les photos du chantier pour pré-décomposer les postes
- L'artisan dicte par WhatsApp vocal : "Devis pour M. Dupont, sdb complète, 8m²"
- L'agent comprend le métier, structure les postes, cherche les prix dans la mémoire de l'artisan (ou propose les prix marché si nouveau type de chantier)
- Calcule automatiquement : quantités (m², ml, unités), main d'oeuvre (basé sur le rythme réel de l'artisan), TVA multi-taux, total HT/TTC
- Intègre les 47 mentions obligatoires, la décennale, les conditions
- Génère un PDF professionnel envoyé au client par email/SMS avec lien de signature électronique
- Si le client signe → l'agent crée automatiquement la fiche chantier + demande d'acompte
- Proactivité : "Le devis Dupont n'a pas été envoyé depuis 2 jours — je l'envoie maintenant ?"
- Pré-devis instantané : estimation rapide par téléphone AVANT la visite → filtre les prospects sérieux

**Agent RELANCE**
- Surveille tous les devis envoyés et toutes les factures émises
- Devis sans réponse J+3 → message de relance personnalisé (ton adapté au client : ferme pour le mauvais payeur, doux pour le bon client)
- Facture impayée J+15 → relance polie par email
- Facture impayée J+30 → relance ferme par SMS
- Facture impayée J+45 → alerte à l'artisan avec proposition de mise en demeure
- Détecte si le client a ouvert l'email (tracking)
- Adapte le ton et le timing basé sur l'historique du client (mémoire Mem0)
- Proactivité : anticipe les retards basé sur les patterns du client ("M. Martin paie toujours avec 10 jours de retard — je relance plus tôt")

**Agent COMPTA**
- L'artisan photographie un ticket de caisse → OCR → extraction automatique : montant, fournisseur, date, articles
- Catégorisation intelligente : matériaux, outillage, carburant, frais de déplacement, sous-traitance
- Attribution automatique au bon chantier (basé sur le contexte : "tu étais sur le chantier Dupont aujourd'hui, ce ticket Point P de 234€ en carrelage va sur Dupont")
- Alerte anomalie : "Ce ticket Point P de 847€ est inhabituel pour ce chantier, tu confirmes ?"
- Préparation export comptable mensuel : toutes les pièces organisées par mois, par catégorie, avec résumé TVA collectée/déductible
- Envoi automatique au comptable (email ou accès partagé)
- Suivi marge par chantier en temps réel : "Chantier Dupont : 65% du budget matériaux consommé, 40% du chantier fait"
- Proactivité : "Fin du mois dans 5 jours. Il te reste 12 tickets non classés. Je prépare l'export ?"

**Agent PLANNING**
- Vue de tous les chantiers en cours avec dates prévues
- Timer par chantier : l'artisan tape "start Dupont" le matin, "stop" le soir → temps réel tracké
- Comparaison temps prévu vs temps réel : "Chantier Dupont : 120% du temps prévu, marge restante : 340€"
- Détection des dépassements AVANT qu'ils arrivent : "Au rythme actuel, le chantier Dupont finira jeudi au lieu de mardi"
- Gestion des enchaînements : si Dupont déborde → décaler le prochain → prévenir le client suivant automatiquement
- Calcul de la capacité : "Tu as 3 chantiers en avril, ta capacité max est de 4, tu peux accepter 1 devis de plus"
- Proactivité : "Le chantier Martin commence lundi mais tu n'as pas commandé les matériaux. Je te rappelle de passer chez Point P ?"

**Agent RÉPUTATION & MARKETING**
- Chantier terminé → SMS automatique au client avec lien avis Google (timing intelligent : 2-3 jours après la fin, quand le client est satisfait)
- Détection de nouvel avis Google → réponse IA personnalisée et professionnelle avec mots-clés SEO locaux
- Réponse adaptée : avis 5★ = remerciement chaleureux. Avis 3★ = empathie + proposition de résolution
- Suivi du score Google et de la position dans le map pack local
- Proactivité : "Tu as 12 avis Google (4.6★). L'artisan concurrent en a 45. Si on demande un avis à tes 8 derniers clients satisfaits, tu peux monter à 20."

**Agent PROSPECTION**
- Fiche par architecte, apporteur d'affaires, ancien client
- Historique des chantiers avec chaque contact
- Rappel automatique : "Tu n'as pas contacté l'architecte Lefebvre depuis 45 jours. Message suggéré : [...]"
- Détection d'opportunités : un ancien client a probablement besoin de travaux (achat immo dans le quartier, etc.)
- Gestion des leads entrants : quand un prospect appelle, l'agent crée la fiche, planifie le rappel, suggère le pré-devis
- Proactivité : "Tu n'as que 2 chantiers prévus pour juin. Il faut relancer les architectes. Je prépare 3 messages ?"

**Agent FISCALITÉ & TRÉSORERIE**
- Connaît la fiscalité de chaque statut : Micro-entreprise, EURL, SAS, SASU, EI
- Micro : seuils CA 2026 (203 100€ vente / 83 600€ services), taux cotisations (12.3% vente / 21.2% services BIC), franchise TVA, ACRE
- EURL/SAS : charges déductibles, TVA collectée/déductible, cotisations TNS ou assimilé salarié, bilan annuel
- Tient un tableau de bord fiscal annuel en temps réel : CA cumulé vs seuils, TVA, cotisations payées/à payer, charges déductibles, résultat net estimé, trésorerie prévisionnelle
- Calendrier fiscal automatique adapté au statut avec rappels proactifs
- Scan courrier administratif (URSSAF, impôts) → lecture + explication + action à faire
- Prépare le dossier annuel pour le comptable
- Alerte seuils : "Attention, ton CA approche 83 600€"
- Conseil optimisation : "En EURL tu pourrais déduire tes charges, parles-en à ton comptable"
- Proactivité : "Ta déclaration URSSAF trimestrielle est dans 5 jours. Ton CA du trimestre : 18 400€."

**Agent DÉPLACEMENTS**
- GPS intégré : calcule la distance domicile/siège → chantier automatiquement
- Coût trajet automatique selon barème kilométrique fiscal 2026
- Zones BTP (1A→5) avec indemnités de trajet et transport selon convention collective régionale
- Panier repas automatique si chantier trop loin pour rentrer déjeuner (10.50-14€/jour selon région)
- Compteur aller-retours par chantier par jour
- Intégration devis : propose d'ajouter les frais de déplacement au devis
- Suivi carburant, parking, amendes, péages — classés par chantier
- Pour les artisans avec employés : indemnités calculées par employé/jour/chantier
- Export frais de déplacement dans l'export comptable mensuel
- Proactivité : "Cette semaine tu as fait 340km pour 4 chantiers. Frais km déductibles : 204€."

**Agent RH** (pour les artisans avec employés)
- Pointage heures par employé, par chantier, par jour (normales + heures sup 25%/50%)
- Convention collective BTP : connaît les 6 IDCC (1596, 1597, 2609, 2420, 1702, 2614), grilles salariales par coefficient (N1P1→N4P2) et par région
- Calcul automatique des indemnités BTP par employé/jour (trajet, transport, panier — selon zone)
- Suivi congés/absences avec période CIBTP (1er avril → 31 mars), prime de vacances 30%
- Cotisations spécifiques BTP : CIBTP (~20.70%), OPPBTP (0.11%), PRO BTP, abattement DFS (7% en 2026)
- Export éléments de paie mensuel pour le cabinet comptable : heures, heures sup, indemnités, absences
- Alerte conformité : salaire minimum par coefficient, contingent heures sup (180h/an), congés non pris
- Ne génère PAS les fiches de paie (trop complexe légalement → c'est le job du cabinet comptable)
- Proactivité : "Ahmed a fait 42h cette semaine dont 7h sup. Sa paye doit inclure 7h à 125%."

**Agent EMAIL PRO**
- Connexion IMAP/OAuth, filtrage pro/perso, catégorisation auto, résumé quotidien, alertes urgentes, fiche prospect auto

**Agent VISION IA**
- Premier filtre de toute image (photo chantier, ticket, courrier). Catégorise, analyse, détecte les oublis, transmet à l'agent concerné

**Agent SITE WEB**
- Génère un site vitrine professionnel IA avec les données de l'app. MAJ mensuelle auto avec validation artisan

**Agent RÉPUTATION & MARKETING**
- Avis Google (SMS + réponse IA + SEO) + publication réseaux sociaux (avant/après + texte IA)

**COUCHE 4 — Cerveau global / Supervisor (pattern Ouroboros)**

Inspiré directement de l'architecture Ouroboros avec Supervisor + Background Consciousness :

- **Orchestration des 13 agents** : distribue les tâches, gère les priorités, évite les conflits (l'agent Relance ne contacte pas un client que l'agent Devis vient de relancer)
- **Background Consciousness** : entre les tâches, le cerveau PENSE sur l'état global de l'artisan. Pas réactif — proactif.
  - "Ce mois-ci ta marge moyenne est de 23% (vs 31% le mois dernier). Le problème c'est le chantier Martin qui a dépassé de 40%."
  - "Tu as gagné 3 chantiers sur 12 devis ce mois (25%). Le mois dernier c'était 1/10. Ton nouveau format fonctionne."
  - "Attention : 2 factures impayées totalisent 4 800€. Ta trésorerie va être tendue dans 15 jours."
- **Budget LLM** : chaque agent a un budget dédié, tracké en temps réel. Circuit breaker si un agent dérape.
- **Task Decomposition** : les tâches complexes sont décomposées en sous-tâches avec parent/child tracking (ex: "faire un devis" = identifier postes → prix → quantités → TVA → mentions → PDF → envoi)
- **Résumé quotidien** : chaque matin, notification WhatsApp avec les 3-5 actions prioritaires du jour. Chaque soir, résumé de ce qui s'est passé.
- **METIER.md (constitution)** : équivalent du BIBLE.md d'Ouroboros mais pour le métier BTP. Règles immuables que le cerveau ne viole JAMAIS : TVA, mentions obligatoires, Factur-X, décennale, etc.

### Module EMAIL PRO (NOUVEAU — demandé)

- Connexion à la boîte mail pro (IMAP/OAuth Gmail/Outlook)
- Filtrage intelligent : les emails pro sont séparés des perso
- Catégorisation automatique : prospect, client existant, fournisseur, admin/comptable, spam
- Résumé quotidien : "3 emails importants aujourd'hui : 1 demande de devis de M. Leblanc, 1 facture fournisseur Point P, 1 relance URSSAF"
- Suggestions de réponse pour les emails importants
- Détection de demandes de devis dans les emails → création automatique de fiche prospect
- Alertes sur les emails urgents (mise en demeure, relance URSSAF, etc.)
- Tout est lié au pipeline : un email de prospect → fiche prospect → devis → chantier → facture

### Module GALERIE PHOTO CHANTIER (NOUVEAU — feature terrain critique)

**Le réflexe terrain :** Tout artisan prend des photos avant de commencer un chantier (état initial, problèmes existants, protection du client) et après (résultat fini, preuve de travail). Ces photos sont aujourd'hui éparpillées dans la pellicule du téléphone, mélangées avec les photos perso, impossibles à retrouver 6 mois après.

**La galerie photo intégrée :**
- **Vidéos courtes (15-60s)** liées au chantier, horodatées, géolocalisées
- **Montage automatique avant/après** (split screen photos ET vidéos)
- **Organisation par chantier** : 3 albums (À commencer / En cours / Terminé)
- **Connexion directe réseaux sociaux** (Facebook, Instagram, Google Business) pour publication directe avec texte IA
- **Validation obligatoire par l'artisan** avant toute publication
- **Prise de photo liée au chantier** : depuis la fiche chantier, l'artisan prend une photo → elle est automatiquement associée au bon chantier, datée, géolocalisée
- **Catégorisation automatique** : le cerveau IA analyse chaque photo et la classe → "avant travaux", "pendant travaux", "après travaux", "problème constaté", "ticket/facture", "plan/croquis"
- **Analyse IA des photos (Claude Vision)** : le cerveau VOIT et COMPREND les photos
  - Photo "avant" d'une salle de bain → le cerveau identifie : baignoire à déposer, carrelage mural ancien, robinetterie vétuste, espace 3×2m environ
  - Photo d'un tableau électrique → le cerveau détecte : disjoncteurs anciens, absence de différentiel 30mA, câblage non conforme
  - Photo d'une fissure → le cerveau évalue : fissure structurelle vs superficielle, recommande un diagnostic ou un simple traitement
  - **Aide au devis** : "D'après les photos du chantier, je vois une sdb d'environ 6m² avec baignoire, faïence murale sur 3 murs, carrelage sol ancien. Tu veux que je génère le devis sur cette base ?"
  - **Détection d'oublis** : "Sur la photo je vois un sèche-serviette — tu ne l'as pas inclus dans le devis, je l'ajoute ?"
- **Contexte visuel pour le cerveau** : les photos enrichissent la mémoire (Mem0). Plus le cerveau voit de chantiers, plus il comprend le métier de l'artisan. Il apprend à reconnaître les matériaux, les configurations, les problèmes récurrents.
- **Avant/Après automatique** : le cerveau associe les photos "avant" et "après" du même espace pour générer un comparatif visuel
- **4 usages de la galerie :**

| Usage | Comment | Valeur |
|-------|---------|--------|
| **Devis enrichi** | Photos avant intégrées au devis PDF (page annexe) | Le client voit ce qui va être fait — augmente le taux d'acceptation |
| **Facture illustrée** | Photos après intégrées à la facture PDF | Preuve de travail — réduit les litiges |
| **Portfolio / Pub** | Galerie avant/après exportable pour réseaux sociaux, Google Business | Attire de nouveaux clients — les avant/après BTP cartonnent sur Facebook/Instagram |
| **Protection juridique** | Photos horodatées + géolocalisées de l'état initial | Preuve en cas de litige client ("c'était déjà comme ça avant") |

- **Partage simplifié** : l'artisan sélectionne les avant/après → export en une image comparée (split screen) → envoi direct sur WhatsApp, Facebook, Instagram, Google Business
- **Stockage** : Supabase Storage (S3 compatible), compression auto des images, illimité en plan Business
- **WhatsApp** : l'artisan envoie une photo par WhatsApp → le cerveau l'associe au chantier en cours ("Tu es sur le chantier Martin, je classe cette photo dans Martin/avant")

### Module AGENT TÉLÉPHONE IA — Standard Vocal Intelligent (PLANIFIÉ — en réflexion)

> **Status :** Feature planifiée. Le mécanisme exact (renvoi d'appel, numéro dédié, SIM virtuelle, intégration opérateur) est en cours de réflexion. Non inclus dans le Sprint de build initial. Sera développé en V2.

**Le problème terrain :**
L'artisan est sur le chantier avec la disqueuse qui tourne, ou il conduit sur la nationale. Son téléphone sonne. C'est un prospect qui appelle 4 artisans pour comparer. L'artisan ne décroche pas → le prospect appelle le suivant → chantier perdu. **Le premier qui répond a gagné.** C'est la réalité du terrain — les prospects ne laissent pas de message vocal et ne rappellent pas.

**Ce que l'agent téléphone fera :**
- **Décrochage automatique** quand l'artisan ne répond pas (après X sonneries ou mode "sur chantier" activé)
- **Voix IA naturelle** qui se présente : "Bonjour, vous êtes bien chez [Nom Entreprise]. [Prénom] est actuellement sur un chantier. Je suis son assistant, je peux prendre votre demande et il vous rappellera dans l'heure."
- **Prise d'information structurée** : nom, numéro, type de besoin, adresse, urgence, disponibilités
- **Filtrage intelligent des appels** :
  - Appel prospect/client → l'agent IA prend en charge, récupère les infos, crée la fiche prospect dans l'app, planifie le rappel
  - Appel perso identifié (contacts enregistrés : famille, amis) → laisse sonner normalement / renvoi messagerie classique
  - Appel fournisseur → prend le message, catégorise
  - Appel spam/démarchage → filtre et ne dérange pas l'artisan
- **Notification immédiate** : l'artisan reçoit une notification push avec le résumé de l'appel : "Prospect : M. Durand, SDB complète à Marseille 13008, veut un devis, disponible samedi matin. Rappeler au 06.XX.XX.XX.XX"
- **Proactivité** : "Tu as 2 appels prospects non rappelés depuis ce matin. Le plus urgent c'est Mme Leroy qui veut une fuite réparée aujourd'hui."

**Pistes techniques en réflexion :**
- Renvoi d'appel opérateur (SFR, Orange, Free) vers un numéro Twilio/Vapi qui déclenche l'agent IA
- Numéro professionnel dédié (VoIP) avec l'agent IA en première ligne
- Intégration SIM virtuelle (eSIM) ou app de téléphonie
- API Vapi.ai / Bland.ai / Retell.ai pour la conversation téléphonique IA temps réel

### Module DÉPLACEMENTS & FRAIS PROFESSIONNELS (NOUVEAU)

- **GPS intégré** : calcule la distance domicile/siège → chantier automatiquement
- **Coût trajet automatique** selon barème kilométrique fiscal 2026 (barème URSSAF en vigueur)
- **Zones BTP (1A→5)** avec indemnités de trajet et transport selon convention collective régionale
- **Panier repas automatique** si chantier trop loin pour rentrer déjeuner (10.50-14€/jour selon région)
- **Compteur aller-retours** par chantier par jour — l'artisan dit "je suis arrivé" / "je repars" ou détection GPS auto
- **Intégration devis** : le cerveau propose d'ajouter les frais de déplacement au devis ("Le chantier est à 45km, tu veux facturer le déplacement ?")
- **Suivi carburant** : les tickets essence sont classés automatiquement en "carburant" et répartis entre les chantiers
- **Parking, amendes, péages** : tout est classé par chantier et inclus dans l'export comptable
- **Pour les artisans avec employés** : les indemnités de trajet et transport sont calculées par employé, par chantier, par jour. Le cerveau génère les lignes pour le bulletin de paie (indemnités exonérées de charges)
- **Export comptable** : les frais de déplacement sont inclus dans l'export mensuel au comptable avec les indemnités km, paniers, et barème appliqué
- Proactivité : "Cette semaine tu as fait 340km pour 4 chantiers. Frais km déductibles : 204€."

### Module RH — GESTION EMPLOYÉS (NOUVEAU — pour artisans avec salariés)

- **Pointage heures** par employé, par chantier, par jour (heures normales + heures sup 25%/50%)
- **Convention collective BTP** : connaît les 6 IDCC (1596, 1597, 2609, 2420, 1702, 2614), grilles salariales par coefficient (N1P1→N4P2) et par région
- **Calcul automatique des indemnités BTP** par employé/jour : trajet, transport, panier — selon zone du chantier
- **Suivi congés/absences** avec période CIBTP (1er avril → 31 mars), prime de vacances 30%
- **Cotisations spécifiques BTP** : CIBTP (~20.70%), OPPBTP (0.11%), PRO BTP, abattement DFS (7% en 2026)
- **Export éléments de paie mensuel** pour le cabinet comptable : heures, heures sup, indemnités, absences
- **Alerte conformité** : salaire minimum par coefficient, contingent heures sup (180h/an), congés non pris
- **Ne génère PAS les fiches de paie** (trop complexe légalement → c'est le job du cabinet comptable)
- Il alerte sur les anomalies mais ne remplace pas un expert-comptable
- Mention systématique : "Vérifiez avec votre comptable pour validation"
- Proactivité : "Ahmed a fait 42h cette semaine dont 7h sup. Sa paye doit inclure 7h à 125%."

### Dossier "À FAIRE" centralisé (NOUVEAU)

Un onglet dans l'app qui liste TOUTES les actions en attente de validation : réponses avis, relances, messages clients, publications réseaux, alertes planning. Classé par priorité. L'artisan valide (vocal/tap/swipe) ou refuse. Aucune action sans sa validation.

### SYSTÈME DE CONNAISSANCE — D'OÙ VIENT LE SAVOIR DU CERVEAU

Le cerveau ne devine pas et n'invente JAMAIS un prix ou une info. Il a 4 sources de connaissance empilées par priorité :

**Source 1 — Mémoire de l'artisan (Mem0) — PRIORITÉ ABSOLUE**
Les données propres à chaque artisan : ses prix habituels, ses clients, ses chantiers passés, son rythme de travail, ses fournisseurs, ses marges. C'est la source la plus précieuse parce qu'elle est personnalisée. Quand l'artisan dit "devis sdb complète", le cerveau regarde D'ABORD : "la dernière fois qu'il a fait une sdb, il a facturé combien ? Chez quel fournisseur il achète son carrelage ? Combien de temps ça lui a pris ?" Cette mémoire se construit au fil du temps — plus l'artisan utilise l'outil, plus elle est riche.

**Source 2 — Base de connaissances métier BTP (RAG vectoriel) — LE SOCLE**
Base de données interne, stockée dans Supabase avec embeddings vectoriels pour la recherche sémantique :
- **Prix du marché** par région, par corps de métier, par type de prestation
- **Réglementation** : 47 mentions obligatoires devis BTP, taux TVA (5.5%, 10%, 20%) et conditions d'application, décennale/RGE/Qualibat, Factur-X
- **Templates métier** : structures types de devis par corps de métier (plomberie sdb, électricité tableau, peinture appartement) avec postes standards
- **Nomenclature matériaux** : correspondance langage artisan ("un 32/40 cuivre") → références produits réelles
- **Règles de calcul** : m² carrelage avec marge de coupe 10%, ml plinthe selon périmètre, nombre de prises selon surface

**Source 3 — LLM Claude (connaissances générales) — LE RAISONNEMENT**
Claude Sonnet sert de moteur de raisonnement : il comprend le langage naturel de l'artisan, décompose "sdb complète" en postes, rédige en français professionnel, formule les relances, détecte les incohérences. Mais on ne lui fait PAS confiance pour les CHIFFRES — les prix et les règles viennent des Sources 1 et 2.

**Source 4 — Recherche web temps réel — LE FALLBACK**
Quand les 3 premières sources n'ont pas l'info :
- Prix d'un matériau spécifique pas dans la base
- Nouvelle réglementation pas encore intégrée
- Question spécifique hors scope métier

**Le flow de décision :**
```
L'artisan demande quelque chose
    │
    ▼
1. Cherche dans la mémoire de l'artisan (Mem0)
   → Trouvé ? → Utilise (prix perso, habitudes, historique)
    │
    ▼ Pas trouvé
2. Cherche dans la base métier BTP (RAG vectoriel)
   → Trouvé ? → Utilise (prix marché, réglementation, templates)
    │
    ▼ Pas trouvé
3. Demande à Claude de raisonner avec ses connaissances
   → Capable ? → Utilise MAIS flag "estimation, à confirmer"
    │
    ▼ Pas capable
4. Recherche web temps réel
   → Trouvé ? → Utilise + ENREGISTRE dans la base métier
    │
    ▼ Rien trouvé
5. Demande à l'artisan : "Je n'ai pas cette info.
   Quel prix tu appliques pour [X] ?"
   → Stocke la réponse dans Mem0 pour la prochaine fois
```

### AUTO-ENRICHISSEMENT — LE CERVEAU QUI DEVIENT PLUS INTELLIGENT CHAQUE JOUR

**Principe : chaque interaction rend le cerveau plus complet. Rien n'est jamais perdu.**

**Boucle d'enrichissement automatique :**

1. **Recherche web → Base métier.** Chaque fois que le cerveau cherche une info sur le web (prix matériau, règle, fournisseur), le résultat est automatiquement validé, structuré, et ENREGISTRÉ dans la base métier BTP (Source 2). La prochaine fois que n'importe quel artisan pose la même question → réponse instantanée sans recherche web. Le cerveau ne cherche JAMAIS la même info deux fois.

2. **Réponse artisan → Mem0 + Base métier.** Quand le cerveau demande "Quel prix tu appliques pour un receveur Wedi Fundo ?" et l'artisan répond "380€ HT posé", cette info va dans :
   - Mem0 de l'artisan (son prix perso)
   - La base métier comme data point anonymisé (prix moyen marché enrichi)
   Plus il y a d'artisans qui utilisent l'outil, plus la base de prix est précise.

3. **Devis validés → Templates enrichis.** Chaque devis que l'artisan valide et envoie enrichit les templates métier. Si 50 plombiers font des devis "sdb complète" et qu'ils incluent tous "évacuation gravats" mais que le template de base ne l'avait pas → le template est mis à jour automatiquement.

4. **Corrections artisan → Apprentissage.** Si l'artisan corrige un devis généré (change un prix, ajoute un poste, modifie une quantité), le cerveau APPREND de la correction :
   - Stocke la correction dans Mem0 (ne refait plus l'erreur pour cet artisan)
   - Si N artisans font la même correction → met à jour la base métier

5. **Veille réglementaire automatique.** Agent de background qui scanne périodiquement les sources officielles (Légifrance, FFB, CAPEB) pour détecter les changements de TVA, nouvelles obligations, évolutions Factur-X. Met à jour la base métier AVANT que les artisans soient impactés. Notification : "Nouvelle obligation à partir du 1er janvier : [X]. Vos devis sont déjà conformes."

6. **Veille prix matériaux.** Agent de background qui surveille les catalogues fournisseurs en ligne (Point P, Cedeo, BigMat) pour détecter les hausses de prix. Alerte : "Le prix du cuivre a augmenté de 12% ce mois. Vos devis en cours utilisent l'ancien prix. Voulez-vous les mettre à jour ?"

**Score de confiance par information :**
Chaque info dans la base a un score de confiance :
- 🟢 Haute confiance (>90%) : info vérifiée par réglementation officielle OU confirmée par 10+ artisans
- 🟡 Confiance moyenne (60-90%) : info de source web fiable OU confirmée par 3-9 artisans
- 🔴 Basse confiance (<60%) : info d'une seule source OU estimation Claude non confirmée
- Le cerveau affiche le score quand il utilise une info basse confiance : "Prix estimé à 380€/m² (confiance moyenne — basé sur 5 devis similaires). Tu confirmes ?"

**Règle absolue : le cerveau ne dit JAMAIS de connerie en silence.** S'il n'est pas sûr, il le DIT. Un artisan qui envoie un devis avec un prix faux perd sa crédibilité. Le cerveau préfère dire "je ne sais pas, confirme-moi" que d'inventer.

### GAMIFICATION — ONBOARDING ET APPRENTISSAGE UTILISATEUR

**Le problème :** L'IA est aussi bonne que le contexte qu'on lui donne. Un artisan qui dit "devis sdb" aura un résultat moyen. Un artisan qui dit "devis sdb complète, 8m², dépose baignoire existante, receveur extra-plat 120x80, paroi fixe, carrelage grès cérame 60x60 sol, faïence 30x60 murs hauteur 2m20, meuble vasque 80cm, mitigeur thermostatique, sèche-serviette 750W" aura un résultat parfait.

**L'objectif :** Apprendre à l'artisan à donner du BON contexte à l'IA, de manière ludique et progressive, sans que ça ressemble à une formation chiante.

**Le système de gamification :**

**1. Profil Artisan — Niveau et XP**
- Niveau 1 : Apprenti (vient de s'inscrire)
- Niveau 2 : Compagnon (a complété le setup de base)
- Niveau 3 : Maître (utilise toutes les fonctionnalités)
- Niveau 4 : Expert (le cerveau le connaît parfaitement, devis en 30 secondes)
- Niveau 5 : Légende (contribue à enrichir la base pour tous)
- Chaque action donne des XP. La barre de progression est visible dans l'app.

**2. Quêtes d'onboarding (premiers 7 jours)**
Pas un tutoriel ennuyeux. Des MISSIONS concrètes qui apportent de la valeur immédiate :

| Quête | Ce que l'artisan fait | Ce que le cerveau apprend | XP |
|-------|----------------------|--------------------------|-----|
| "Dis-moi qui tu es" | Renseigne son métier, sa zone, son assurance décennale, son numéro SIRET | Peut pré-remplir TOUS les devis avec les mentions obligatoires | 100 |
| "Ton premier devis vocal" | Dicte un devis simple par la voix | Apprend le style de devis de l'artisan | 150 |
| "Tes prix habituels" | Renseigne 10 prix qu'il utilise souvent (ex: pose carrelage/m², heure de MO) | Ne demandera PLUS ces prix, devis instantanés | 200 |
| "Ton carnet d'adresses" | Importe ou dicte 5 clients + 2 architectes | Pipeline pré-rempli, relances possibles | 150 |
| "Ton premier ticket scanné" | Photographie un ticket de caisse | Apprend à reconnaître ses fournisseurs | 100 |
| "Ta première relance auto" | Active la relance auto sur un devis envoyé | L'agent Relance commence à travailler seul | 150 |
| "Ton profil Google" | Connecte son profil Google Business | L'agent Réputation peut surveiller les avis | 100 |

Chaque quête complétée = le cerveau devient plus intelligent pour CET artisan. L'artisan voit le résultat immédiat : "Grâce à tes prix habituels, je peux maintenant faire un devis sdb en 30 secondes au lieu de 3 minutes."

**3. Score de contexte par devis**
À chaque devis vocal, le cerveau affiche un score de complétude :
- ⭐⭐⭐⭐⭐ "Contexte parfait — devis ultra-précis"
- ⭐⭐⭐⭐ "Bon contexte — 2 infos manquantes que j'ai complétées avec tes habitudes"
- ⭐⭐⭐ "Contexte moyen — j'ai dû utiliser les prix marché pour 5 postes"
- ⭐⭐ "Contexte faible — beaucoup d'estimations, vérifie le devis"
- ⭐ "Trop vague — j'ai besoin de plus de détails"

Avec un tip contextuel : "Pour passer à 5 étoiles, précise la surface ET le type de carrelage la prochaine fois. Ça me prend 10 secondes à dire et ça rend le devis 3x plus précis."

**4. Achievements (badges)**
Badges visibles dans le profil, déblocables par l'usage réel :
- 🏗️ "10 devis envoyés" → puis 50, 100, 500
- ⚡ "Devis en moins de 60 secondes" (le cerveau te connaît assez bien)
- 💰 "Première facture payée via l'app"
- 📸 "100 tickets scannés" (le comptable t'adore)
- ⭐ "10 avis Google collectés"
- 🔥 "30 jours consécutifs d'utilisation"
- 🧠 "Le cerveau te connaît à 95%" (assez de données pour être ultra-précis)
- 👑 "Contributeur" (tes données anonymisées ont amélioré la base pour tous)

**5. Progression visible du cerveau**
Un indicateur dans l'app : "Mon assistant me connaît à X%"
- 0-20% : "Je découvre ton métier. Aide-moi en complétant les quêtes."
- 20-50% : "Je commence à connaître tes prix et tes clients."
- 50-80% : "Je fais tes devis presque comme toi. Quelques détails à affiner."
- 80-95% : "Je te connais très bien. Devis en 30 secondes."
- 95-100% : "Je suis ton bras droit. Tu me parles, je fais exactement ce que tu ferais."

Cet indicateur motive l'artisan à donner plus de contexte : plus tu nourris le cerveau, plus le pourcentage monte, plus les devis sont rapides et précis.

**6. Micro-apprentissages vocaux**
Le cerveau apprend à l'artisan PAR LA VOIX pendant l'utilisation, pas dans un tutoriel séparé :
- "Astuce : si tu me dis la marque du receveur, je peux mettre le prix exact au lieu d'une estimation."
- "Tu savais que tu peux me dire 'ajoute une option sèche-serviette' et je crée une ligne optionnelle dans le devis ?"
- "La prochaine fois, essaie de me donner la surface en m² — ça me permet de calculer les quantités automatiquement."
- Ces tips sont espacés (1 par jour max), contextuels (liés à ce que l'artisan vient de faire), et désactivables.

---

## ARCHITECTURE TECHNIQUE

### Stack

```
Runtime agents : Python 3.12+ (pattern Ouroboros supervisor + workers)
Backend API : FastAPI (webhooks + API REST + billing + agents orchestration)
App mobile : React Native (Expo) — 1 codebase → Android natif + Web PWA (iOS)
  - Android : build via EAS Build → Google Play Store (APK/AAB)
  - iOS : Expo Web → PWA optimisée (bypass Apple Store)
  - Chat in-app : composant chat avec bouton micro flottant (FAB) permanent
  - Voix : Expo Speech/Audio → Whisper API (transcription) + TTS (réponses vocales)
  - Caméra : Expo Camera (scan tickets natif Android, web fallback iOS)
  - Hors-ligne : Expo SQLite local + sync queue quand réseau revient
  - i18n : i18next + react-i18next (FR, EN, TR, ES, PT, AR dès le launch)
Database : Supabase PostgreSQL + RLS (multi-tenant)
Auth : Supabase Auth (email/password + Google OAuth + Magic Link SMS)
Mémoire IA : Mem0 (mémoire persistante user/session/agent-level)
LLM : Claude API — claude-sonnet-4-6 (agents + compréhension métier)
Voix → Texte : Whisper API (OpenAI) ou Deepgram (plus rapide, moins cher) — supporte FR/EN/TR/ES/PT/AR
Texte → Voix : ElevenLabs (voix naturelle premium, choix homme/femme, multi-langue) ou OpenAI TTS (fallback)
  - Voix homme : configurable par l'artisan (voix grave professionnelle)
  - Voix femme : configurable par l'artisan (voix claire professionnelle)
  - Latence cible : <1.5s entre fin de parole artisan et début de réponse vocale
  - Streaming TTS : la voix commence à parler AVANT que toute la réponse soit générée
Canaux : WhatsApp Business API (Meta Cloud API) + SMS (Twilio) + Email (Brevo)
OCR : Claude Vision (tickets de caisse) ou Tesseract fallback
PDF : ReportLab (génération devis/factures PDF conformes Factur-X)
Signature électronique : Yousign API (français, conforme eIDAS)
Paiement SaaS : Stripe (abonnements artisans)
Paiement clients : Stripe Connect ou GoCardless (paiement des factures artisan par ses clients)
Deploy : Railway (backend + agents workers) + Vercel (PWA iOS fallback)
CI/CD : GitHub Actions + EAS Build (Expo Application Services)
Analytics : GA4 + Mixpanel (events in-app)
Monitoring : Sentry (backend + app)
Push notifications : Expo Notifications (Android natif) + Brevo email/SMS (iOS PWA fallback)
```

### Repos open-source intégrés

| Repo | Ce qu'on prend | Rôle |
|------|---------------|------|
| **Ouroboros** (razzant/ouroboros) | Supervisor, background consciousness, task decomposition, budget tracking, BIBLE.md → METIER.md | Cerveau orchestrateur + proactivité des agents |
| **OpenClaw** (openclaw/openclaw) | Système de skills/plugins, canaux WhatsApp/Telegram, architecture modulaire | Interface artisan WhatsApp + modules métier comme skills |
| **Mem0** (mem0ai/mem0) | Mémoire persistante multi-level (user, session, agent), context recall intelligent | Mémoire de chaque artisan (prix, clients, habitudes) |
| **Claude Code skills pattern** | Architecture SKILL.md + scripts, orchestration multi-agents | Pattern de structuration des 10 modules métier |

### Architecture système

```
┌─────────────────────────────────────────────────────────────────┐
│                    ARTISAN (3 canaux → 1 cerveau)                │
│                                                                  │
│  ┌─────────────────┐  ┌──────────────┐  ┌───────────────────┐  │
│  │ APP ANDROID      │  │ PWA iOS      │  │ WHATSAPP          │  │
│  │ (Google Play)    │  │ (écran accueil│  │ (quand pas sur   │  │
│  │                  │  │  iPhone)      │  │  l'app)           │  │
│  │ Dashboard        │  │ Dashboard    │  │                   │  │
│  │ Pipeline         │  │ Pipeline     │  │ Vocal → cerveau   │  │
│  │ Stats            │  │ Stats        │  │ Photo → cerveau   │  │
│  │                  │  │              │  │ Texte → cerveau   │  │
│  │ 🎤 CHAT VOCAL   │  │ 🎤 CHAT VOCAL│  │                   │  │
│  │ Bouton micro FAB │  │ Bouton micro │  │                   │  │
│  │ flottant permanent│ │ Web Speech   │  │                   │  │
│  │ Push-to-talk     │  │ API          │  │                   │  │
│  │ Contextuel       │  │              │  │                   │  │
│  │ (sait sur quelle │  │              │  │                   │  │
│  │  page tu es)     │  │              │  │                   │  │
│  │                  │  │              │  │                   │  │
│  │ 📸 Caméra native │  │ 📸 Web camera│  │ 📸 Photo WhatsApp │  │
│  │ 🔔 Push natif    │  │ 📧 Email/SMS │  │ 🔔 Notif WhatsApp │  │
│  │ 📴 Mode hors-ligne│ │              │  │                   │  │
│  └────────┬─────────┘  └──────┬───────┘  └────────┬──────────┘  │
│           │ React Native       │ Expo Web          │ Meta Cloud  │
│           │ (Expo)             │ (PWA)              │ API         │
└───────────┼────────────────────┼───────────────────┼────────────┘
            │                    │                   │
            └────────────────────┼───────────────────┘
                                 │ Toutes les requêtes
                                 │ → même API backend
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                    GATEWAY (FastAPI)                              │
│                                                                  │
│  ├── API REST (app Android + PWA iOS)                           │
│  │   ├── /api/chat — Chat in-app (texte + vocal transcrit)     │
│  │   ├── /api/chat/audio — Upload audio → Whisper → texte      │
│  │   ├── /api/devis — CRUD devis                               │
│  │   ├── /api/chantiers — Pipeline chantiers                   │
│  │   ├── /api/tickets — Upload photo ticket → OCR              │
│  │   ├── /api/clients — CRM clients                            │
│  │   └── /api/stats — Dashboard métriques                      │
│  ├── WebSocket (chat temps réel + notifications live)           │
│  ├── Webhook WhatsApp (messages, vocaux, photos)                │
│  ├── Webhook Email (IMAP polling ou webhook)                    │
│  ├── Webhook Stripe (paiements artisans + clients)              │
│  ├── Auth (Supabase JWT)                                        │
│  └── i18n middleware (détecte langue user → cerveau + app en FR/EN/TR/ES/PT/AR) │
└───────────────────────────┬─────────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────────┐
│              SUPERVISOR (pattern Ouroboros)                       │
│                                                                  │
│  ├── state.py — État global artisan, budget LLM, métriques      │
│  ├── queue.py — File de tâches prioritaires                     │
│  ├── workers.py — Cycle de vie des 13 agents                    │
│  ├── consciousness.py — Background consciousness (pense entre   │
│  │                       les tâches, génère les résumés,        │
│  │                       détecte les problèmes proactivement)   │
│  ├── events.py — Dispatch événements inter-agents               │
│  ├── budget.py — Tracking coût LLM par agent, circuit breaker  │
│  └── metier.py — Constitution BTP (METIER.md, règles immuables)│
│                                                                  │
│  Règles Supervisor :                                            │
│  - MAX_ROUNDS = 50 par tâche (prevent runaway)                  │
│  - Budget par agent : Devis 30%, Relance 10%, Compta 10%,      │
│    Planning 8%, Réputation 8%, Prospection 8%,                  │
│    Fiscalité 8%, Déplacements 8%, RH 10%                       │
│  - Background consciousness : 1 cycle/heure, $0.07/pensée      │
│  - Multi-model review sur les décisions critiques (devis > 5K€)│
└───────────────────────────┬─────────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────────┐
│                    9 AGENTS AUTONOMES                             │
│                                                                  │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐                        │
│  │  AGENT   │ │  AGENT   │ │  AGENT   │                        │
│  │  DEVIS   │ │ RELANCE  │ │  COMPTA  │                        │
│  │          │ │          │ │          │                        │
│  │ Voix→PDF │ │ Auto-    │ │ OCR      │                        │
│  │ Prix     │ │ relance  │ │ tickets  │                        │
│  │ TVA      │ │ Email+SMS│ │ Export   │                        │
│  │ Mentions │ │ Adaptatif│ │ Marge    │                        │
│  │ Signature│ │ Tracking │ │ Anomalie │                        │
│  └──────────┘ └──────────┘ └──────────┘                        │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐                        │
│  │  AGENT   │ │  AGENT   │ │  AGENT   │                        │
│  │ PLANNING │ │RÉPUTATION│ │PROSPECTIO│                        │
│  │          │ │          │ │   N      │                        │
│  │ Timer    │ │ Avis     │ │ CRM      │                        │
│  │ Capacité │ │ Google   │ │ Archi    │                        │
│  │ Alerte   │ │ Réponse  │ │ Relance  │                        │
│  │ Enchaîne │ │ IA       │ │ Leads    │                        │
│  └──────────┘ └──────────┘ └──────────┘                        │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐                        │
│  │  AGENT   │ │  AGENT   │ │  AGENT   │                        │
│  │FISCALITÉ │ │DÉPLACE-  │ │   RH     │                        │
│  │          │ │ MENTS    │ │          │                        │
│  │ Statuts  │ │ GPS      │ │ Pointage │                        │
│  │ Seuils   │ │ Frais km │ │ Heures   │                        │
│  │ URSSAF   │ │ Paniers  │ │ Conv.BTP │                        │
│  │ Calendr. │ │ Zones BTP│ │ CIBTP    │                        │
│  │ Scan doc │ │ Carburant│ │ Export   │                        │
│  └──────────┘ └──────────┘ └──────────┘                        │
│                                                                  │
│  Chaque agent :                                                 │
│  - A son propre system prompt spécialisé                       │
│  - Accède à la mémoire Mem0 (prix, clients, historique)        │
│  - Communique avec les autres via le Supervisor                │
│  - Track son budget LLM                                        │
│  - Est proactif (background tasks entre les demandes)          │
└───────────────────────────┬─────────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────────┐
│                    MÉMOIRE (Mem0 / MemOS)                        │
│                                                                  │
│  User-level : profil artisan, style de devis, rythme de travail│
│  Client-level : historique par client, délais paiement, perso  │
│  Chantier-level : temps, matériaux, marge, incidents           │
│  Agent-level : apprentissages de chaque agent (patterns)       │
│  Réseau-level : architectes, fournisseurs, apporteurs          │
│                                                                  │
│  Auto-recall : chaque agent reçoit automatiquement le contexte │
│  pertinent avant de traiter une tâche (pas de re-fetch complet)│
└───────────────────────────┬─────────────────────────────────────┘
                            │
┌───────────────────────────▼─────────────────────────────────────┐
│                    DATA (Supabase PostgreSQL)                     │
│                                                                  │
│  organizations — Multi-tenant (1 org = 1 artisan/entreprise)   │
│  users — Profil, préférences, abonnement                       │
│  clients — Fiche client, contact, historique                   │
│  devis — Tous les devis avec statut, PDF, signature            │
│  factures — Toutes les factures avec statut paiement           │
│  chantiers — Pipeline : prospect → devis → en cours → terminé │
│  tickets — Tickets de caisse scannés, catégorisés              │
│  depenses — Dépenses par chantier, par catégorie               │
│  temps — Temps tracké par chantier (timer start/stop)          │
│  relances — Historique des relances envoyées                   │
│  avis — Avis Google, réponses générées                        │
│  contacts_pro — Architectes, apporteurs, fournisseurs          │
│  emails — Emails pro indexés et catégorisés                    │
│  agent_memory — Mémoire persistante des agents (via Mem0)      │
│  agent_tasks — File de tâches et résultats                     │
│  agent_budget — Tracking coût LLM par agent par mois           │
│  subscriptions — Plans Stripe                                  │
│  metier_rules — Règles BTP immuables (TVA, mentions, etc.)    │
│                                                                  │
│  + 45+ RLS policies (même pattern AgentShield)                 │
└─────────────────────────────────────────────────────────────────┘
```

### Pipeline type : "L'artisan dicte un devis"

```
1. L'artisan envoie un vocal WhatsApp : "Devis pour M. Dupont,
   salle de bain complète, 8m², remplacement baignoire par
   douche italienne, carrelage sol et murs"

2. Gateway reçoit le message → Whisper transcription → texte

3. Supervisor reçoit la tâche → assigne à l'Agent DEVIS

4. Agent DEVIS :
   a. Demande à Mem0 : "prix habituels de cet artisan pour sdb ?"
   b. Parse le vocal : identifie les postes (dépose baignoire,
      plomberie, bac à douche, paroi, carrelage sol, faïence
      murs, meuble vasque, robinetterie, joints, évacuation)
   c. Pour chaque poste : prix unitaire (mémoire artisan ou prix marché)
   d. Calcul quantités : 8m² sol × prix/m² + 22ml faïence × prix/ml...
   e. TVA : main d'oeuvre 10%, fournitures 20% (rénovation > 2 ans)
   f. Détection oublis : "Pas de mention sèche-serviette. J'ajoute en option ?"
   g. 47 mentions obligatoires auto-remplies
   h. Génération PDF professionnel
   i. Envoi au client par email + SMS avec lien signature

5. Mem0 stocke : nouveau devis, prix utilisés, postes typiques SDB

6. Supervisor met à jour le pipeline : M. Dupont → "Devis envoyé"

7. Agent RELANCE planifie : relance J+3 si pas de réponse

8. Notification WhatsApp à l'artisan : "✅ Devis Dupont envoyé
   (4 850€ TTC). Relance auto dans 3 jours si pas de réponse."

Temps total : 2-3 minutes. Ancien process : 1-2 heures.
```

---

## DIFFÉRENCIATION vs EXISTANT

| | Batappli / Obat / EBP | Excel / Word | Notre SaaS |
|---|---|---|---|
| Interface | Écran PC, clavier, souris | Écran PC | WhatsApp + voix + photo |
| Temps pour un devis | 30-60 min | 1-2h | 2-3 min |
| Compréhension métier | Bibliothèque d'ouvrages (statique) | Aucune | LLM qui comprend le langage artisan |
| Apprentissage | Aucun | Aucun | Apprend tes prix, ton rythme, tes clients |
| Relance impayés | Manuelle ou basique | Manuelle | IA adaptative qui personnalise le ton et le timing |
| Tickets de caisse | Saisie manuelle | Pas géré | Photo → OCR → classement auto |
| Suivi temps chantier | Timer basique | Pas géré | IA qui détecte les dépassements et alerte |
| Prospection | Pas géré | Pas géré | CRM pro + relances auto architectes |
| Avis Google | Pas géré | Pas géré | SMS auto + réponse IA personnalisée |
| Proactivité | Zéro | Zéro | Le cerveau pense et agit seul |
| Factur-X 2026 | En cours d'adaptation | Non conforme | Natif |
| Prix | 19-60€/mois | "Gratuit" | 29-79€/mois |

**Moat (ce qu'on ne peut pas copier) :**
1. La compréhension métier BTP par LLM (chaque prompt est un investissement en knowledge)
2. La mémoire persistante par artisan (plus tu l'utilises, plus il est précis)
3. L'interface WhatsApp/voix (les concurrents sont tous sur écran PC)
4. Les 13 agents proactifs avec background consciousness

---

## L'ICP — QUI ACHÈTE

### Persona primaire — L'artisan seul / micro-entreprise (60%)

**Profil :**
- Plombier, électricien, peintre, carreleur, maçon, menuisier, couvreur
- Travaille seul ou avec 1 employé/apprenti
- CA : 60-150K€/an
- Zéro outil numérique (papier + Notes du téléphone)
- Smartphone Android (80%) ou iPhone

**Pain principal :** "Je passe mes soirées à faire des devis au lieu d'être avec ma famille."
**Willingness to pay :** 29-49€/mois (= 1h de main d'oeuvre facturée)
**Canal d'acquisition :** Facebook (groupes artisans), bouche-à-oreille, grossistes (partenariats Point P/Cedeo), salon du bâtiment, Google "logiciel devis artisan"

### Persona secondaire — La TPE bâtiment 2-10 salariés (30%)

**Profil :**
- Patron artisan avec 2-10 compagnons
- CA : 200-800K€/an
- Utilise peut-être un logiciel basique mais le patron fait encore les devis lui-même le soir
- A besoin de suivre plusieurs chantiers en parallèle

**Pain principal :** "Je n'ai aucune visibilité sur la rentabilité de mes chantiers."
**Willingness to pay :** 49-79€/mois
**Canal d'acquisition :** LinkedIn (patron BTP), fédérations artisanales (CAPEB, FFB), comptables (prescription)

### Persona tertiaire — L'auto-entrepreneur BTP (10%)

**Profil :**
- Se lance dans le bâtiment, 0-2 ans d'activité
- Aucun outil, aucune habitude
- Budget serré mais prêt à investir pour "faire pro"

**Pain principal :** "Je ne sais pas comment faire un devis conforme."
**Willingness to pay :** 0-29€/mois (free tier pour l'acquisition)
**Canal d'acquisition :** Google "comment faire un devis BTP", YouTube tuto artisans, CMA (Chambre des Métiers)

---

## PRICING

| Plan | Prix/mois | Cible | Inclus |
|------|-----------|-------|--------|
| Starter | €0 | Auto-entrepreneurs, test | 5 devis/mois, scan tickets, pipeline basique. Pas de relance auto ni agents. |
| Pro | €29 | Artisan seul | Devis illimités voix/WhatsApp, 13 agents actifs, relances auto, scan tickets illimité, avis Google, export comptable |
| Business | €79 | TPE 2-10 salariés | Tout Pro + multi-utilisateurs, suivi multi-chantiers avancé, rapports rentabilité, accès comptable partagé, support prioritaire |

**ARPU cible :** €40/mois (mix Pro/Business)
**Conversion free → paid :** >12% (la valeur est évidente dès le premier devis vocal)
**Churn cible :** <3%/mois (une fois que la mémoire est entraînée, l'artisan ne part plus)

---

## COÛTS LLM PAR ARTISAN

### Estimation initiale (sans optimisation)

| Agent | Appels/jour | Coût/mois | Notes |
|-------|-------------|-----------|-------|
| Devis (vocal → PDF) | ~2 | ~$4 | Le plus coûteux (long context métier) |
| Relance (messages adaptatifs) | ~3 | ~$1.50 | Messages courts |
| Compta (OCR + catégorisation) | ~5 tickets | ~$2 | Vision API pour OCR |
| Planning (analyse + alertes) | ~1 | ~$0.50 | |
| Réputation (réponse avis) | ~0.5 | ~$0.30 | |
| Prospection (suggestions) | ~0.5 | ~$0.20 | |
| Fiscalité (tableau de bord + alertes) | ~1 | ~$0.50 | |
| Déplacements (GPS + frais) | ~2 | ~$0.80 | |
| RH (pointage + indemnités) | ~1 | ~$0.50 | |
| Background consciousness | ~24 (1/heure) | ~$1.70 | $0.07/pensée |
| Email filtering | ~10 | ~$1 | |
| **TOTAL (sans optimisation)** | | **~$13/mois par artisan** | |

### Coût réel avec optimisations (voir COUT_REEL.md pour le détail)

| Optimisation | Économie |
|-------------|----------|
| Routage 70% Haiku / 30% Sonnet | -50% coût LLM moyen |
| Prompt caching (system prompts) | -90% input tokens récurrents |
| Background consciousness 3×/jour au lieu de 24× | -87% |
| Context compaction (max 10K tokens historique) | -60% input |
| **TOTAL OPTIMISÉ** | **~$3.30/mois par artisan** |

**Marge sur un artisan Pro (€29/mois) :** €29 - $3.30 LLM - $1 infra = ~€25 marge = **~86%**
**Marge sur un artisan Business (€79/mois) :** €79 - $4.50 LLM - $1.50 infra = ~€73 marge = **~92%**

> Détail complet des coûts : voir `COUT_REEL.md`

---

## MÉTRIQUES CIBLES

### Phase 1 — Launch

| Métrique | Cible |
|----------|-------|
| Signups mois 1 | 100+ |
| Clients payants mois 1 | 15-25 |
| MRR fin mois 1 | €400-800 |
| Conversion free → paid | >12% |
| Devis générés par user/semaine | 3-5 |
| Temps moyen création devis | <3 min |

### Kill criteria / Go criteria

| Situation après 12 semaines | Décision |
|------------------------------|----------|
| < 200€ MRR | Kill — documenter, open-source, Tool #3 |
| 200-500€ MRR | Itérer — améliorer onboarding et rétention |
| > 500€ MRR | Double down |
| > 2 000€ MRR | All-in |

---

## CANAUX D'ACQUISITION

| Canal | Stratégie | Priorité |
|-------|-----------|----------|
| Facebook groupes artisans | "Hier j'ai fait un devis en 2 min depuis mon chantier" avec vidéo démo | 🔴 Primaire |
| Google SEO | "logiciel devis artisan", "facture electronique BTP 2026", "logiciel bâtiment gratuit" | 🔴 Primaire |
| YouTube | Tutos "Comment faire un devis conforme BTP 2026" + démo produit | 🔴 Primaire |
| Partenariats grossistes | Point P, Cedeo, BigMat — flyer dans les sacs de matériaux | 🟡 Secondaire |
| Comptables | Prescription : "Recommandez l'outil à vos clients artisans" | 🟡 Secondaire |
| CAPEB / FFB | Fédérations artisanales — partenariat institutionnel | 🟡 Secondaire |
| CMA | Chambre des Métiers — formation créateurs d'entreprise BTP | 🟡 Secondaire |
| Bouche-à-oreille | Chaque artisan satisfait en parle à 5 collègues (le BTP c'est du réseau) | 🟢 Organique |
| Product Hunt / IndieHackers | Pour la communauté Nova + crédibilité | 🟢 Tertiaire |

**Angle de lancement unique :** "J'ai travaillé dans le bâtiment. Je sais que vous passez vos soirées à faire des devis. J'ai buildé l'outil que j'aurais voulu avoir."

---

## TIMING — FACTUR-X SEPTEMBRE 2026

**Pourquoi c'est le moment PARFAIT :**

La facturation électronique au format Factur-X devient obligatoire pour la réception dès le 1er septembre 2026 (grandes entreprises + ETI) et pour l'émission au 1er septembre 2027 (PME/TPE/micro).

83% des artisans n'ont PAS commencé la transition. Ils vont DEVOIR adopter un outil. Le marketing se fait tout seul : "Mettez-vous en conformité 2026 en 5 minutes."

Les logiciels existants (Batappli, EBP) adaptent leurs outils existants. Nous, on arrive avec un outil natif Factur-X qui est AUSSI un assistant IA. La conformité est le cheval de Troie. L'IA est la valeur réelle.

---

## SÉPARATION TECHNIQUE

| Élément | Valeur |
|---------|--------|
| Nom | STRUCTORAI |
| Repo GitHub | github.com/NovaShips/structorai (repo séparé) |
| Stripe | Compte Nova dédié |
| Domaine principal | structorai.app |
| Domaine secondaire | structorai.io (redirect → .app) |
| Email support | support@structorai.app via Brevo |
| Analytics | Google Analytics GA4 + Mixpanel |
| WhatsApp Business | Compte Meta Business dédié |
| Claude API | Clé API séparée d'AgentShield |
| Google Play | Compte développeur Nova ($25 one-time) |

---

## LIEN AVEC L'ÉCOSYSTÈME NOVA

Ce SaaS est le Tool #2 du portfolio Nova. Le pivot depuis EngageRadar est stratégique :

- AgentShield = Tool #1 (dev/tech, monde anglophone)
- Tool #2 = marché français, non-tech, B2B local
- Diversification géographique (FR) et de persona (non-dev)
- Cross-learning : l'architecture agents/LLM est réutilisable pour les futurs SaaS du portfolio
- Nova peut build in public les deux en parallèle : la communauté tech (AgentShield) ET le marché traditionnel (BTP)

---

> **Règle :** Ce fichier est la vérité sur le Tool #2 Nova.
> Si une décision produit contredit ce fichier → mettre ce fichier à jour
> ET logger la décision dans DECISIONS-LOG.md avec le raisonnement.
