# STRUCTORAI — MODIFICATIONS FEATURES V2 (retour Fabrice)

> Ce document liste UNIQUEMENT les features modifiées, enrichies ou ajoutées suite au retour de Fabrice.
> Les features marquées "Ok" sans modification ne sont pas reprises ici.
> À intégrer dans PRODUCT_CONTEXT.md et BUILD_PLAN.md.

---

## FEATURES MODIFIÉES

### F01. Chat texte avec le cerveau IA — CLARIFIÉ
**Question Fabrice :** "L'artisan peut demander quel temps il fait demain ? C'est un LLM ou un algo ?"

**Réponse :** C'est un **vrai LLM (Claude API)**, pas un chatbot à règles ni un algo. L'artisan peut parler de TOUT :
- **Questions métier** : "combien coûte un receveur Wedi ?", "quelle TVA sur une rénovation sdb ?"
- **Questions perso** : "quel temps il fait demain à Marseille ?" → le cerveau utilise la recherche web et répond
- **Conseil juridique/fiscal** : "j'ai reçu une relance URSSAF, qu'est-ce que je fais ?"
- **Questions générales** : "c'est quoi le numéro des urgences plomberie ?"
- **Conversation naturelle** : "je suis crevé aujourd'hui" → le cerveau répond avec empathie

**Limites :**
- Ne remplace PAS un avocat, un comptable ou un médecin. Le cerveau précise toujours "je ne suis pas un professionnel du droit/de la compta, consulte ton expert-comptable pour valider"
- Pour les questions hors métier (météo, actu, culture générale), le cerveau utilise la recherche web comme fallback
- Les prix et règles BTP viennent des sources vérifiées (Mem0 + base métier + RAG), pas de l'imagination du LLM
- Budget LLM : chaque message coûte des tokens. Les conversations hors-métier très longues sont limitées en plan Starter (free)

**Comment :** Claude API (claude-sonnet-4-6) avec system prompt métier BTP + tools (web search, base prix, Mem0, calcul). Le LLM a accès à tout. Pas de bridage artificiel — l'artisan parle à un vrai assistant intelligent.

---

### F02 + F03. Chat vocal + Mode mains libres — CLARIFIÉ
**Même réponse que F01.** Le vocal est juste un canal d'entrée (voix → Whisper STT → texte → LLM → texte → ElevenLabs TTS → voix). Le cerveau derrière est le même LLM complet. Aucune limite sur les sujets — tout ce qu'on peut demander en texte, on peut le demander en vocal.

**Limites techniques du vocal :**
- Latence cible : <1.5s entre fin de parole et début de réponse vocale
- Langues STT : FR, EN, TR, ES, PT, AR (Whisper/Deepgram supportent toutes)
- Bruit chantier : Whisper est robuste au bruit mais si c'est trop fort (disqueuse), le cerveau peut demander de répéter
- Durée max d'un vocal : ~5 minutes (au-delà → découpage automatique)

---

### F06. Lecture vocale des infos — AJOUT RÉGLAGE
**Modification Fabrice :** "Il doit pouvoir choisir ça dans les réglages"

**Ajout :** Dans les settings, un toggle "Réponses vocales automatiques" avec 3 modes :
- **Toujours vocal** : le cerveau parle TOUTES ses réponses à voix haute (mode chantier)
- **Jamais vocal** : le cerveau répond uniquement en texte (mode silencieux/bureau)
- **Intelligent** (par défaut) : le cerveau parle si l'artisan a parlé, texte si l'artisan a tapé

---

### F09. Scan documents — GROS ENRICHISSEMENT
**Modification Fabrice :** "Pas que les tickets. Les factures fournisseur aussi. Tout ce qui est papier. Et le cerveau doit connaître la fiscalité EURL/SAS/Micro pour guider l'artisan."
**Modification Fabrice V2 :** "La F09 doit permettre un suivi total tout au long de l'année"

**F09 enrichie — Scan & Classification TOUS documents + SUIVI FISCAL ANNUEL COMPLET :**
- **Tickets de caisse** (Point P, Cedeo, BigMat, etc.)
- **Factures fournisseur** (matériaux, location matériel, sous-traitance)
- **Devis fournisseur** (comparaison prix)
- **Courriers URSSAF / impôts / organismes**
- **Relevés bancaires**
- **Attestations (décennale, RGE, Qualibat)**
- **Amendes (stationnement, etc.)**
- **Tout document papier** → photo → OCR → classification → stockage

**SUIVI FISCAL ANNUEL COMPLET (12 mois) :**

Le cerveau tient un **tableau de bord fiscal annuel en temps réel**, pas juste des rappels ponctuels :

| Indicateur suivi en continu | Ce que le cerveau fait |
|----------------------------|----------------------|
| **CA cumulé vs seuils** | "Tu es à 52 400€ de CA sur 83 600€ max en micro. Il te reste 31 200€ de marge." |
| **TVA collectée / déductible** | Pour les assujettis : solde TVA à chaque instant. "Ce trimestre : TVA collectée 3 800€, TVA déductible 1 200€, TVA à payer 2 600€." |
| **Cotisations sociales payées / à payer** | Suivi mensuel ou trimestriel selon le rythme URSSAF. "Tu as payé 8 400€ de cotisations en 6 mois. Estimation annuelle : 17 200€." |
| **Charges déductibles (EURL/SAS)** | Suivi cumulé par catégorie : matériaux, carburant, outillage, assurance, loyer, téléphone, etc. |
| **CFE (Cotisation Foncière)** | Rappel de paiement en novembre. Estimation du montant. |
| **Résultat net estimé** | CA - charges - cotisations = résultat. "À ce rythme, ton résultat annuel sera d'environ 38 000€." |
| **Impôt estimé** | Estimation IR ou IS selon le statut. "Prévois environ 5 200€ d'impôt sur cette base." |
| **Trésorerie prévisionnelle** | Entrées (factures encaissées) - sorties (charges, cotisations, impôts) = trésorerie. "Attention, en septembre tu auras la TVA + les cotisations en même temps, prévois 4 800€." |

**Calendrier fiscal automatique :**
- Le cerveau connaît TOUTES les échéances selon le statut (micro/EURL/SAS/EI) :
  - Déclaration URSSAF mensuelle/trimestrielle
  - Déclaration TVA (mensuelle, trimestrielle ou annuelle)
  - Acomptes IS (EURL/SAS à l'IS)
  - CFE (décembre)
  - Déclaration de revenus (mai-juin)
  - Bilan annuel / liasse fiscale (via comptable)
  - Déclaration CIBTP (si employés)
- Rappels proactifs : "Ta déclaration URSSAF du T2 est dans 8 jours. Ton CA du trimestre est de 21 300€."
- **Préparation du dossier comptable** : en fin d'année (ou fin d'exercice), le cerveau génère un dossier complet pour le comptable avec tous les justificatifs classés, le récapitulatif CA/charges/TVA, et les points d'attention

**NOUVELLE FEATURE MAJEURE — Agent Fiscalité / Gestion d'entreprise :**

Le cerveau connaît la fiscalité de CHAQUE type d'entreprise BTP en France :

| Statut | Ce que le cerveau sait |
|--------|----------------------|
| **Micro-entreprise** | Seuils CA 2026 (203 100€ vente / 83 600€ services), taux cotisations (12.3% vente / 21.2% services BIC), franchise TVA, ACRE 50%→25% (juillet 2026), versement libératoire, CFP, TFCC, pas de déduction de charges réelles |
| **EURL (IS ou IR)** | Charges déductibles, TVA collectée/déductible, cotisations TNS (~45% du bénéfice), bilan annuel, acomptes IS, liasse fiscale |
| **SASU** | Président assimilé salarié, cotisations ~65% du salaire brut, dividendes flat tax 30%, bilan + liasse fiscale, CFE |
| **SAS** | Comme SASU mais multi-associés, AG, PV assemblée |
| **EI (Entreprise Individuelle)** | Régime réel simplifié ou normal, charges déductibles, cotisations TNS |

**Ce que le cerveau fait avec cette connaissance :**
- **Rappels échéances** : "Ta déclaration URSSAF trimestrielle est dans 5 jours. Ton CA du trimestre est de 18 400€, les cotisations seront d'environ 3 900€."
- **Alertes seuils** : "Attention, ton CA approche les 83 600€. Si tu dépasses 2 ans de suite, tu sors du régime micro."
- **Aide aux déclarations** : "Pour ta déclaration TVA, tu as collecté 4 200€ de TVA et tu peux déduire 1 800€ → TVA à payer : 2 400€"
- **Conseil optimisation** : "Tu es en micro à 75K€ de CA. En EURL tu pourrais déduire tes charges (12K€ de matériaux, 3K€ de carburant) et payer moins d'impôts. Parles-en à ton comptable."
- **Scan courrier administratif** : photo courrier URSSAF → le cerveau lit, comprend, et dit quoi faire + quand + les risques si pas fait
- **Guide de la paperasse** : "Tu viens de créer ton EURL. Voici les 12 obligations à respecter la première année : [liste]. On les fait ensemble une par une ?"

**Profil entreprise enrichi (rejoint la gamification F75-F76) :**
- À l'onboarding, l'artisan dit son statut (micro, EURL, SAS, etc.) + son régime TVA + sa date de création
- Le cerveau adapte TOUT en conséquence : les devis (mentions, TVA ou pas), la compta (charges déductibles ou pas), les rappels (URSSAF mensuel ou trimestriel)

---

### F15. Devis par la voix — ENRICHI (devis conversationnel)
**Modification Fabrice :** "L'agent doit pouvoir dicter le devis à l'artisan au fur et à mesure. L'artisan modifie les prix comme il veut. Tout sous mémoire."

**F15 enrichie — Devis conversationnel vocal :**
Le devis n'est plus un "tu dictes, je génère". C'est une CONVERSATION :

1. L'artisan dit "Devis pour M. Dupont, sdb complète 8m²"
2. Le cerveau répond vocalement : "Ok, je te propose : dépose baignoire existante 250€, évacuation gravats 180€, plomberie alimentation/évacuation 850€..."
3. L'artisan interrompt : "Non la dépose c'est 300€ chez moi, et ajoute aussi la dépose du carrelage existant"
4. Le cerveau : "Noté. Dépose baignoire 300€, dépose carrelage sol et murs 400€. Je continue avec le receveur ?"
5. L'artisan : "Oui vas-y"
6. Le cerveau dicte poste par poste → l'artisan valide ou modifie chaque prix
7. **Chaque modification de prix est mémorisée dans Mem0** → la prochaine fois, le cerveau proposera directement 300€ pour la dépose baignoire

**Mémoire des corrections :** prix, formulations, postes ajoutés/enlevés, options habituelles. Après 5-10 devis, le cerveau fait des devis quasi parfaits du premier coup.

---

### F16. Décomposition automatique en postes — ENRICHI (photos + itératif)
**Modification Fabrice :** "Le cerveau doit s'aider des photos. L'artisan peut ajouter/modifier/enlever des choses. Le cerveau fait en arrière-plan."

**F16 enrichie :**
- Le cerveau utilise les photos du chantier (F51-F53) pour pré-décomposer les postes AVANT que l'artisan ne dicte
- "J'ai analysé les 4 photos du chantier Dupont. Je vois une sdb de ~6m² avec baignoire fonte, carrelage mural ancien, robinetterie à changer, et un radiateur sèche-serviette. Tu veux que je parte sur cette base ?"
- L'artisan peut dire à tout moment : "ajoute un poste", "enlève le sèche-serviette", "mets le carrelage en option", "change le prix du receveur"
- Le cerveau exécute IMMÉDIATEMENT en arrière-plan et met à jour le devis
- S'améliore avec le temps : plus l'artisan fait de devis, plus la décomposition initiale est précise

---

### F18. TVA multi-taux — ENRICHI (par statut entreprise)
**Modification Fabrice :** "La TVA doit être gérée selon le type d'entreprise."

**F18 enrichie :**
- **Micro-entreprise en franchise TVA** : le cerveau NE met PAS de TVA sur le devis. Mention "TVA non applicable, article 293B du CGI"
- **Micro-entreprise au-dessus du seuil** : le cerveau ALERTE → "Tu as dépassé le seuil de franchise TVA. Tu dois maintenant facturer la TVA. Je passe tes devis en TTC ?"
- **EURL/SAS/EI au réel** : TVA multi-taux BTP classique (5.5%/10%/20%) avec calcul automatique
- Le statut est configuré à l'onboarding (profil entreprise) et le cerveau adapte TOUT automatiquement

---

### F20. Détection d'oublis — GROS ENRICHISSEMENT (module déplacements)
**Modification Fabrice :** "Les déplacements comptent. GPS intégré. Coût trajet, aller-retours, repas/panier, parking, amendes."

**NOUVELLE FEATURE — Module Déplacements & Frais professionnels :**

- **GPS intégré** : l'artisan entre l'adresse du chantier → le cerveau calcule automatiquement la distance depuis le siège/domicile
- **Coût trajet automatique** : barème kilométrique fiscal 2026 × distance × nb aller-retours
- **Zones BTP automatiques** : le cerveau sait dans quelle zone URSSAF se trouve le chantier (Zone 1A 0-5km → Zone 5 40-50km) et calcule les indemnités de trajet/transport
- **Panier repas** : si le chantier est trop loin pour rentrer déjeuner → ajout automatique de l'indemnité panier (10.50-14€/jour selon région)
- **Compteur aller-retours** : l'artisan dit "start trajet Dupont" le matin → GPS track → "stop" le soir → nb de km réels tracké
- **Intégration devis** : le cerveau propose d'ajouter les frais de déplacement dans le devis ("Le chantier Dupont est à 35km. J'ajoute 40€ HT de frais de déplacement au devis ?")
- **Suivi parking / amendes** : l'artisan scan une amende de stationnement → le cerveau la classe, rappelle de la payer, et note la dépense sur le bon chantier
- **Suivi carburant** : les tickets essence sont classés automatiquement en "carburant" et répartis entre les chantiers
- **Export comptable** : les frais de déplacement sont inclus dans l'export mensuel au comptable avec les indemnités km, paniers, et barème appliqué

**Pour les artisans avec employés (rejoint F40) :**
- Les indemnités de trajet et transport sont calculées par employé, par chantier, par jour
- Le cerveau génère les lignes pour le bulletin de paie (indemnités exonérées de charges)

---

### F23. Pré-devis instantané — ENRICHI (prix indicatifs)
**Modification Fabrice :** "Les prix du pré-devis doivent être indicatifs. Configurable dans les réglages."

**F23 enrichie :**
- Le pré-devis porte la mention : "**Estimation indicative — Le prix définitif sera établi après visite sur site et peut varier selon les conditions réelles du chantier.**"
- Dans les réglages : toggle "Mention prix indicatif sur les pré-devis" (activé par défaut)
- L'artisan peut personnaliser le texte de la mention dans les réglages
- Le pré-devis a un statut distinct "PRÉ-DEVIS" dans le pipeline (différent de "DEVIS")

---

### F25. Relance devis — ENRICHI (multicanal)
**Modification Fabrice :** "Par mail OU téléphone SMS aussi"

**F25 enrichie — Relance multicanal :**
- **Email** (Brevo) — message professionnel personnalisé
- **SMS** (Twilio) — message court + lien vers le devis
- **WhatsApp** — message avec preview du devis
- Le canal est choisi automatiquement selon les préférences du client (si email → email, si pas d'email → SMS, etc.)
- L'artisan peut forcer un canal dans les réglages par client

---

### F30. OCR tickets — ENRICHI (tout document compta)
**Modification Fabrice :** "Les factures et devis fournisseur aussi. La compta doit être gérée de A à Z."

**F30 enrichie — OCR tous documents comptables :**
- Tickets de caisse fournisseur
- Factures fournisseur (PDF ou papier)
- Devis fournisseur (pour comparaison de prix)
- Notes de frais (restaurant, carburant, péage)
- Relevés bancaires
- Tout document scanné → OCR → classification → attribution chantier → export comptable

---

### F32. Attribution au chantier — ENRICHI (correction vocale)
**Modification Fabrice :** "L'artisan peut dire 'non ça va pour un autre client ou un autre chantier'"

**F32 enrichie :**
- Le cerveau propose : "Ce ticket Point P de 234€ va sur le chantier Dupont ?"
- L'artisan peut répondre : "Non, c'est pour le chantier Martin" → le cerveau reclasse immédiatement
- Ou : "Non, c'est perso" → classé en dépenses personnelles (hors compta pro)
- Ou : "Oui" → confirmé et classé
- Le cerveau apprend des corrections → moins d'erreurs d'attribution au fil du temps

---

### F36. Pipeline chantiers (Kanban) — ENRICHI (design fluide)
**Modification Fabrice :** "La visibilité doit être le plus fluide visuellement possible"

**F36 enrichie :**
- UI design avec le skill UI/UX Pro Max : colonnes claires, cartes lisibles, couleurs distinctes par statut
- Drag-and-drop natif (React Native Reanimated)
- Vue résumé sur chaque carte : nom client, montant, marge en cours, jours restants
- Filtres rapides : par statut, par mois, par corps de métier
- Mode liste pour les petits écrans

---

### F38. Détection de dépassements — ENRICHI (validation simplifiée)
**Modification Fabrice :** "Vocal ou un simple valider ou refuser"

**F38 enrichie :**
- Le cerveau alerte : "Le chantier Dupont va dépasser de 2 jours. Je préviens le client Martin que son chantier sera décalé au mercredi ?"
- **3 modes de validation ultra-rapides :**
  - **Vocal** : "Ok" ou "Non, pas de retard" → le cerveau comprend
  - **Tap** : boutons ✅ Valider / ❌ Refuser sur la notification
  - **Swipe** : swipe droite = valider, swipe gauche = refuser (sur la notification push)
- Si refusé, l'artisan peut préciser vocalement : "J'ai pris de l'avance en réglant un problème, pas de retard" → le cerveau met à jour la projection
- **Aucune action automatique sans validation** — l'alerte reste en attente dans le dossier "À faire" (F89) sans limite de temps
- Notification push + rappel vocal si pas de réponse après quelques heures
- L'artisan répond **quand il peut** : à la pause, en fin de journée, ou immédiatement s'il entend la notification
- Le geste de validation est simple (un tap ou un "ok" vocal), mais l'artisan a tout le temps qu'il lui faut pour répondre

---

### F40. Calcul de capacité — GROS ENRICHISSEMENT (multi-employés + MODULE RH)
**Modification Fabrice :** "Prendre en compte le nombre de personnes. L'artisan peut avoir des employés sur différents chantiers."
**Modification Fabrice V2 :** "Il faudrait gérer tout ce qui touche fiche de paie etc par rapport aux heures travaillées. Un RH dans l'appli pour aider les artisans car ça aussi c'est du travail en plus."

**F40 enrichie — Gestion des effectifs + MODULE RH COMPLET :**

**A. Gestion des effectifs (planning)**
- **Profil entreprise** : l'artisan renseigne ses employés (nom, compétences/corps de métier, classification BTP N1P1→N4P2, disponibilité)
- **Affectation par chantier** : "Demain sur Dupont j'ai Ahmed et Karim avec moi. Sur Martin c'est Youssef seul."
- **Calcul capacité par personne** : le cerveau sait que 3 personnes sur un chantier ≠ 3x plus rapide (rendement d'équipe)
- **Multi-corps de métier** : si l'artisan fait plomberie + carrelage, ses employés peuvent avoir des spécialités différentes → affectation optimale
- **Vue planning équipe** : qui est où, quel jour, sur quel chantier

**B. MODULE RH — Suivi des heures et préparation paie**

Le cerveau devient un assistant RH qui prépare TOUT pour le comptable ou la plateforme de paie :

| Fonctionnalité RH | Ce que le cerveau fait |
|-------------------|----------------------|
| **Pointage heures par employé** | Chaque employé est pointé sur chaque chantier (start/stop ou saisie manuelle). Heures normales, heures supplémentaires (25%/50% selon convention BTP), heures de nuit. |
| **Calcul heures sup** | Contingent annuel 180h (ou 145h si annualisation). Majoration 25% les 8 premières heures, 50% au-delà. Le cerveau alerte si on approche du contingent. |
| **Indemnités BTP par employé/jour** | Indemnité de trajet (selon zone 1A→5), indemnité de transport (frais km), panier repas — calculés automatiquement par chantier, par jour, par employé. |
| **Congés et absences** | Suivi des congés payés (gérés par CIBTP), RTT, arrêts maladie, absences. Période de référence BTP : 1er avril → 31 mars. |
| **Classification et salaire** | Grille salariale BTP par convention (IDCC 1596 ≤10 salariés / IDCC 1597 >10) × coefficient (150→270) × région. Le cerveau connaît les minima et alerte si le salaire est en dessous. |
| **Export éléments de paie** | Chaque mois, le cerveau génère un récapitulatif par employé : heures normales, heures sup, indemnités trajet/transport/panier, absences, congés. Prêt à envoyer au cabinet comptable ou à la plateforme de paie. |
| **Cotisations spécifiques BTP** | Le cerveau connaît : cotisation CIBTP (~20.70% masse salariale), OPPBTP (0.11%), PRO BTP (prévoyance obligatoire), CCCA-BTP (formation), abattement DFS (7% en 2026). |
| **Alertes RH** | "Ahmed a fait 42h cette semaine, dont 7h sup. Sa paye doit inclure 7h à 125%." / "Youssef a posé 0 jour de congé en 6 mois, rappelle-lui." / "Le salaire minimum de Karim (coefficient 185, Île-de-France) est de 1 950€ brut, tu le payes 1 900€ → non conforme." |

**Ce que le cerveau NE fait PAS (limites légales) :**
- Il ne génère PAS les fiches de paie (complexité légale, DSN, cotisations précises → c'est le job du cabinet comptable ou de la plateforme de paie type PayFit/Silae)
- Il PRÉPARE les éléments de paie et les envoie au bon destinataire
- Il alerte sur les anomalies mais ne remplace pas un expert-comptable
- Mention systématique : "Vérifiez avec votre comptable pour validation"

**NOUVEL AGENT identifié — Agent RH (F93) :**

| Agent | Rôle |
|-------|------|
| **Agent RH** (nouveau) | Pointage heures, calcul heures sup, indemnités BTP par employé/chantier/jour, suivi congés/absences, export éléments de paie mensuel, alertes conformité convention collective, rappels obligations employeur |

**Total agents mis à jour : 13 agents** (Devis, Relance, Compta, Planning, Réputation & Marketing, Prospection, Email Pro, Fiscalité & Trésorerie, Déplacements, **RH**, Vision IA, Site Web) + Supervisor

---

### F41. SMS avis Google — ENRICHI (validation avant envoi)
**Modification Fabrice :** "À valider par l'artisan avant envoi"

**F41 enrichie :** Le cerveau prépare le SMS → le met dans la file "À faire" → l'artisan valide ou modifie → envoi. Jamais d'envoi automatique sans validation.

---

### F42. Réponse IA avis Google — ENRICHI (validation + file "À faire")
**Modification Fabrice :** "À valider par l'artisan avant envoi. Dans un onglet 'À faire'."

**F42 enrichie :**
- Le cerveau génère la réponse → la place dans un dossier/onglet **"À faire"** dans l'app
- L'artisan voit la liste des actions en attente : réponses avis, relances à valider, messages à approuver
- Il valide, modifie ou rejette chaque action
- Le dossier "À faire" est le hub central de toutes les actions qui nécessitent la validation de l'artisan

---

### F47. Connexion email — ENRICHI (secrétaire IA)
**Modification Fabrice :** "L'IA peut avoir un visuel sur la boîte email et prévenir le client et l'alerter et lui faire des rappels comme une vraie secrétaire"

**F47 enrichie :** Le cerveau surveille la boîte email en continu (pas juste un résumé quotidien). Il alerte en temps réel sur les emails urgents et fait des rappels proactifs :
- "Tu as reçu un email de l'URSSAF il y a 2h — c'est une mise en demeure de paiement, il faut régler avant le 15. Tu veux que je te rappelle dans 3 jours ?"
- "Le devis fournisseur que tu attendais de Point P vient d'arriver par email. 3 400€ HT. Tu veux que je le compare avec le devis Cedeo de la semaine dernière ?"

---

### F49. Résumé quotidien emails — ENRICHI (compte-rendu global)
**Modification Fabrice :** "Pareil pour appels et SMS. Compte-rendu journalier en fin de journée à écouter."

**F49 enrichie — Compte-rendu journalier global (fin de journée) :**
- Le cerveau génère un résumé vocal de la journée :
  - Emails importants reçus
  - Appels (quand l'agent téléphone sera actif — V2)
  - SMS reçus
  - Devis signés / relances envoyées / factures payées
  - Tickets scannés
  - Temps tracké par chantier
- L'artisan écoute son compte-rendu en mode mains libres en rentrant du chantier

---

### F51. Photos chantier — ENRICHI (vidéos aussi)
**Modification Fabrice :** "Il doit pouvoir y avoir les vidéos aussi"

**F51 enrichie :**
- **Photos ET vidéos** liées au chantier
- Vidéos courtes (15-60s) pour montrer l'avancement, un problème, ou un résultat
- Les vidéos sont aussi horodatées et géolocalisées
- Stockage Supabase Storage (compression auto)
- Les vidéos ne sont PAS analysées par Claude Vision (trop coûteux en tokens) — mais elles sont classées dans la galerie et utilisables pour les montages avant/après

---

### F56. Avant/Après automatique — ENRICHI (montage + dossiers par chantier)
**Modification Fabrice :** "L'IA peut créer un montage. Stock dans le dossier chantier avec fini/en cours/à commencer."

**F56 enrichie :**
- **Montage automatique** : l'IA crée une image/vidéo split-screen avant/après (plusieurs photos possibles)
- **Organisation par chantier** : chaque chantier a un album photo avec 3 sous-dossiers visuels :
  - 📁 **À commencer** (photos avant travaux)
  - 📁 **En cours** (photos pendant)
  - 📁 **Terminé** (photos après + montages avant/après)
- L'artisan voit l'avancement visuel de chaque chantier d'un coup d'oeil

---

### F57. Export réseaux sociaux — ENRICHI (connexion directe + texte)
**Modification Fabrice :** "L'app connectée directement aux réseaux sociaux. Publier avec un texte. Validé avant publication."

**F57 enrichie :**
- Connexion OAuth aux réseaux sociaux de l'artisan : Facebook, Instagram, Google Business
- Le cerveau génère un texte de publication adapté au réseau ("Transformation complète d'une salle de bain à Marseille 13008 ! Avant/Après en images. #plomberie #renovation #marseille")
- L'artisan voit le preview complet (image/vidéo + texte) → valide → publication directe
- **Jamais de publication sans validation**

---

### F58. Photos dans devis/factures PDF — ENRICHI (demander avant)
**Modification Fabrice :** "Demander à l'artisan avant"

**F58 enrichie :** Le cerveau propose : "Tu veux que j'ajoute les photos avant en annexe du devis Dupont ?" → l'artisan valide ou non. Toggle dans les réglages pour automatiser (toujours/jamais/demander).

---

### F60. Photo via WhatsApp — ENRICHI (description vocale)
**Modification Fabrice :** "L'artisan doit pouvoir dire à quoi correspond la photo. L'IA peut lui demander."

**F60 enrichie :**
- L'artisan envoie une photo + un vocal : "C'est l'état du mur porteur avant ouverture, chantier Martin"
- Si l'artisan envoie une photo SANS description → le cerveau demande : "C'est pour quel chantier ? C'est une photo avant ou après travaux ?"
- L'IA analyse la photo ET écoute la description vocale pour un classement plus précis

---

### F63. 13 agents autonomes — ENRICHI (nouveaux agents identifiés)
**Modification Fabrice :** "Il y a sûrement d'autres agents à ajouter"

**Agents supplémentaires identifiés :**

| Agent | Rôle | Pourquoi |
|-------|------|----------|
| **Agent FISCALITÉ** (nouveau) | Gestion statut entreprise, rappels URSSAF/TVA/CFE, aide déclarations, alertes seuils, scan courriers administratifs, conseil optimisation | L'artisan est perdu avec la paperasse. C'est la feature F09 enrichie qui justifie un agent dédié. |
| **Agent DÉPLACEMENTS** (nouveau) | GPS, calcul frais km, paniers repas, indemnités zones BTP, suivi carburant/parking/amendes, export frais | La feature F20 enrichie est assez complexe pour justifier un agent dédié. |

**Total : 13 agents autonomes** (Devis, Relance, Compta, Planning, Réputation & Marketing, Prospection, Email Pro, Fiscalité & Trésorerie, Déplacements, RH, Vision IA, Site Web) + Supervisor

Les budgets LLM devront être redistribués pour inclure les 2 nouveaux agents.

---

### F66. Budget LLM — ENRICHI (ne pas perdre d'argent)
**Modification Fabrice :** "Je ne dois pas perdre d'argent"

**F66 enrichie :**
- Circuit breaker strict : 3 réponses vides → pause agent → fallback modèle moins cher
- **Budget max par artisan/mois** : cap dur en $ LLM par artisan. Si atteint → dégrade vers Haiku (moins cher) au lieu de Sonnet
- **Plan Starter (gratuit)** : budget LLM très limité (5 devis/mois, pas de background consciousness, pas d'agent Fiscalité/Déplacements)
- **Monitoring en temps réel** : dashboard coût LLM par agent, par artisan, par jour
- **Alerte si un artisan coûte plus que sa souscription** (ex: artisan Pro à 29€ qui consomme 25$ de LLM → alerte)
- **Calcul de marge par artisan** pour vérifier que chaque client est rentable

---

### F68. METIER.md — ENRICHI (ajout de métiers)
**Modification Fabrice :** "Je dois pouvoir ajouter un métier en plus comme nouvelle feature"

**F68 enrichie :**
- Architecture extensible : chaque métier = 1 fichier référentiel technique (comme les 8 existants)
- Pour ajouter un métier (ex: couverture, chauffage/clim, isolation) : créer le fichier REF_TECHNIQUE_{METIER}.md → l'ajouter à la base RAG → le cerveau l'utilise automatiquement
- Les templates de devis par métier sont aussi modulaires (data/templates_devis/)
- **Admin feature** : interface pour uploader un nouveau référentiel technique sans toucher au code

---

### F74. Veille prix matériaux — ENRICHI (partenariats fournisseurs)
**Modification Fabrice :** "Peut-être des partenariats fournisseurs avec connexion directe à leur catalogue"

**F74 enrichie :**
- **V1** : veille prix par web scraping des catalogues en ligne (Point P, Cedeo, BigMat)
- **V2 (partenariats)** : API directe avec les fournisseurs → prix catalogue temps réel, toujours à jour
- Les prix partenaires sont flaggés dans les devis : "Prix Point P catalogue au 06/04/2026"
- L'artisan peut comparer les prix de plusieurs fournisseurs sur le même matériau
- **Modèle économique potentiel** : commission sur les ventes générées via les recommandations du cerveau (à explorer)

---

### F75-F76. Gamification — ENRICHI (guide ultra-fluide)
**Modification Fabrice :** "Un guide, une gamification du parcours pour que ce soit le plus fluide possible même pour celui qui n'a pas l'habitude d'utiliser des apps"

**F75-F76 enrichies :**
- **Onboarding 100% vocal** : l'artisan n'a rien à lire. Le cerveau lui parle et le guide étape par étape.
- "Bienvenue ! Je suis ton assistant. Pour commencer, dis-moi ton prénom et ton métier."
- Le cerveau annonce le % de progression : "Super, on est à 30%. Encore 4 infos et je pourrai faire ton premier devis."
- Si le cerveau détecte qu'il manque des infos pour une action → il le dit : "Pour faire ce devis, j'aurais aussi besoin de ton numéro SIRET et de ton assurance décennale. On les ajoute maintenant ?"
- **Zéro écran compliqué** : chaque quête est une conversation vocale, pas un formulaire à remplir

---

### F77. Badges — ENRICHI (beaucoup de badges)
**Modification Fabrice :** "Il doit y avoir beaucoup de badges"

**F77 enrichie :** Badges organisés par catégorie :

**Devis :** 1er devis, 10, 50, 100, 500 devis envoyés. Devis en <60s. Devis en <30s. 1er devis signé. Taux conversion >30%.
**Compta :** 1er ticket scanné, 50, 100, 500 tickets. Export comptable mensuel sans oubli. 0 ticket non classé fin de mois.
**Chantier :** 1er chantier terminé, 10, 50, 100. Chantier terminé dans les temps. Marge >40%.
**Réputation :** 1er avis Google, 10, 25, 50 avis. Note >4.5★. Réponse à tous les avis.
**Prospection :** 1er architecte contacté. Réseau de 10+ contacts pro.
**Fidélité :** 7 jours consécutifs, 30, 90, 365. "Le cerveau me connaît à 50%", 80%, 95%.
**Contribution :** Données anonymisées ont amélioré la base pour tous.

---

### F80. Agent Téléphone IA — ENRICHI (compte-rendu + priorité)
**Modification Fabrice :** "L'assistant vocal pour filtrer et trier les appels et faire un compte-rendu de qui a appelé et la priorité et l'urgence"

**F80 enrichie :**
- Chaque appel filtré → compte-rendu structuré avec :
  - Nom de l'appelant
  - Type : prospect / client existant / fournisseur / perso / spam
  - Résumé de la demande
  - **Priorité** : 🔴 Urgent (fuite, dégât des eaux) / 🟡 Normal (demande de devis) / 🟢 Pas pressé (renseignement)
  - Numéro de rappel
- Les comptes-rendus sont classés par priorité dans l'app
- Notification push immédiate pour les 🔴 urgents
- Inclus dans le compte-rendu journalier (F49)

---

## NOUVELLES FEATURES AJOUTÉES

### F87. Agent FISCALITÉ (nouveau)
- **Quoi :** Agent IA dédié à la gestion administrative et fiscale de l'entreprise de l'artisan
- **Pourquoi :** La paperasse administrative est la 2ème source de stress après les devis. L'artisan ne connaît pas ses obligations.
- **Comment :** Agent dédié avec knowledge base fiscale (micro/EURL/SAS/EI) + rappels calendrier + scan documents admin + alertes seuils

### F88. Agent DÉPLACEMENTS (nouveau)
- **Quoi :** Agent IA dédié au suivi des déplacements, frais kilométriques, paniers repas, indemnités BTP, parking, amendes
- **Pourquoi :** Les frais de déplacement sont un poste majeur que les artisans ne suivent jamais → perte d'argent + problème avec le comptable
- **Comment :** GPS intégré + barèmes URSSAF/kilométriques 2026 + calcul automatique par chantier/jour/employé + intégration devis + export comptable

### F89. Dossier "À faire" centralisé (nouveau)
- **Quoi :** Un onglet/dossier dans l'app qui liste TOUTES les actions en attente de validation de l'artisan
- **Pourquoi :** L'artisan doit avoir un seul endroit pour voir ce qu'il doit valider : réponses avis, relances, messages, publications réseaux sociaux
- **Comment :** File d'attente globale alimentée par tous les agents → classée par priorité → l'artisan valide ou rejette

### F90. Vidéos chantier (nouveau)
- **Quoi :** Prise de vidéo courte (15-60s) liée au chantier, horodatée, géolocalisée
- **Pourquoi :** Les vidéos avant/après sont encore plus impactantes que les photos pour le marketing
- **Comment :** Expo Camera video → upload Supabase Storage → classement dans l'album chantier → montage possible

### F91. Publication directe réseaux sociaux (nouveau)
- **Quoi :** Connexion Facebook/Instagram/Google Business → le cerveau génère texte + images → l'artisan valide → publication directe
- **Pourquoi :** Les artisans ne publient jamais sur les réseaux faute de temps. Le cerveau fait 90% du travail.
- **Comment :** OAuth Facebook/Instagram Graph API + Google Business API → post avec images + texte généré par le cerveau

### F92. Profil entreprise complet (nouveau)
- **Quoi :** À l'onboarding, l'artisan crée son profil complet : statut juridique, régime TVA, SIRET, décennale, RGE, employés, corps de métier, zone d'intervention, fournisseurs habituels
- **Pourquoi :** Le cerveau a besoin de TOUTES ces infos pour être précis. Sans profil complet → devis incomplets, TVA fausse, mentions manquantes.
- **Comment :** Conversation vocale guidée (gamification F75-F76) → stockage Mem0 + DB → injection dans tous les agents

### F93. Agent RH (nouveau)
- **Quoi :** Agent IA dédié à la gestion RH des employés : pointage heures, calcul heures sup (convention BTP), indemnités trajet/transport/panier par employé/chantier/jour, suivi congés (CIBTP), absences, classification/coefficient, export éléments de paie mensuel
- **Pourquoi :** L'artisan avec des employés perd des heures chaque mois à préparer les éléments de paie. Les conventions collectives BTP sont complexes (6 conventions, cotisations CIBTP 20.70%, OPPBTP, PRO BTP, abattement DFS 7%). Le cerveau mâche le travail.
- **Comment :** Agent dédié avec knowledge base conventions collectives BTP (IDCC 1596/1597/2609/2420) + grilles salariales régionales + barèmes indemnités URSSAF + intégration avec Agent Déplacements (indemnités) et Agent Planning (heures par chantier) + export CSV/PDF vers le cabinet comptable

### F94. Agent Vision IA (nouveau)
- **Quoi :** Agent IA dédié qui est le premier filtre de TOUTE image reçue dans l'app (photo chantier, ticket de caisse, facture fournisseur, plan, courrier administratif, tableau électrique, photo SDB)
- **Pourquoi :** Centraliser l'intelligence visuelle. Quand l'artisan envoie une photo, le cerveau doit comprendre ce que c'est AVANT de router vers le bon agent (Compta pour un ticket, Devis pour une photo chantier, Fiscalité pour un courrier URSSAF)
- **Comment :** Agent dédié utilisant Claude Vision (Sonnet 4.6). Reçoit toute image → identifie le type → catégorise → extrait les données → transmet à l'agent concerné. Détection d'oublis sur les photos chantier ("je vois un sèche-serviette pas dans le devis"). Catégorisation auto des photos galerie (avant/pendant/après/problème)

### F95. Agent Site Web IA (nouveau)
- **Quoi :** Agent IA qui génère un site vitrine professionnel complet pour l'artisan en 5 minutes, avec mise à jour automatique mensuelle
- **Pourquoi :** 80% des artisans n'ont pas de site web. Les agences facturent 1 500-5 000€. Avec l'IA, le site se crée avec les données déjà dans l'app (profil entreprise, photos chantier, avis Google, corps de métier)
- **Comment :** Agent dédié. Création : profil F92 → génération pages (accueil, services, galerie, avis, contact) → photos depuis galerie chantier → avis depuis Agent Réputation → SEO local auto → publication sur sous-domaine structorai.app (Pro) ou domaine perso (Business). MAJ mensuelle : le cerveau détecte les nouvelles photos/avis → propose une mise à jour → l'artisan valide dans le dossier "À faire" (F89) AVANT publication. Le cerveau ne publie JAMAIS sans validation.
- **Pricing :** Starter = pas de site. Pro (29€) = site inclus sur sous-domaine. Business (79€) = site + domaine perso + SEO avancé

### F96. Agent Email Pro (nouveau)
- **Quoi :** Agent IA qui se connecte à la boîte email de l'artisan (IMAP/OAuth) et agit comme une secrétaire IA
- **Pourquoi :** L'artisan reçoit des dizaines d'emails par jour et rate les demandes de devis, les relances URSSAF, les factures fournisseurs. L'agent filtre, catégorise, alerte et crée des fiches prospects automatiquement
- **Comment :** Connexion IMAP ou OAuth (Gmail, Outlook). Filtrage pro/perso. Catégorisation auto (prospect/client/fournisseur/admin/spam). Résumé quotidien vocal. Alertes urgentes en temps réel (mise en demeure, URSSAF). Détection demande de devis dans les emails → fiche prospect auto → notification "Nouveau prospect Leblanc, demande devis SDB"

### F97. Gestion des fournisseurs (nouveau)
- **Quoi :** Répertoire fournisseurs avec fiche détaillée (coordonnées, historique achats, prix négociés), accessible via menu profil
- **Pourquoi :** L'artisan travaille avec 5-10 fournisseurs réguliers. Leurs prix sont stockés en Mem0 et utilisés par l'Agent Devis
- **Comment :** Fiches fournisseurs en DB (Supabase). Historique achats rattaché (tickets scannés). Prix négociés par matériau stockés en Mem0. Comparaison prix entre fournisseurs sur un même matériau

### F98. Gestion des achats et bons de commande (nouveau)
- **Quoi :** Bons de commande fournisseurs, réception, rattachement au chantier, suivi dépenses
- **Pourquoi :** L'artisan commande chez Point P → bon de commande → réception → facture fournisseur → rattaché au chantier → calcul marge réel. Sans ça, la marge par chantier est imprécise
- **Comment :** Création bon de commande (vocal ou manuel), lié à un chantier et un fournisseur. Réception partielle ou totale. Facture fournisseur rattachée. Total achats par chantier visible dans la fiche chantier

### F99. Situations de travaux / facturation à l'avancement (nouveau)
- **Quoi :** Facturer un chantier par tranches de pourcentage d'avancement (30% → 60% → 100%)
- **Pourquoi :** Indispensable pour les chantiers longs (rénovation complète, gros oeuvre). Obat, Sage et EBP l'ont tous. Un artisan qui fait une cuisine à 15 000€ ne peut pas attendre la fin pour facturer
- **Comment :** Depuis un devis signé, l'artisan crée une facture de situation en définissant le % d'avancement par poste ou global. Le cerveau calcule le reste à facturer. Numérotation conforme

### F100. Attestation TVA réduite auto-générée (nouveau)
- **Quoi :** Génération automatique de l'attestation simplifiée de TVA (formulaire CERFA 1301-SD) que le client doit signer pour bénéficier de la TVA 10% ou 5.5%
- **Pourquoi :** Obligatoire pour appliquer la TVA réduite en rénovation. Sans ce document signé, l'artisan risque un redressement fiscal. Obat le fait automatiquement
- **Comment :** Pré-remplie avec les infos du client et du chantier. Envoyée avec le devis pour signature. Stockée dans la fiche chantier

### F101. PV de réception de chantier (nouveau)
- **Quoi :** Document de réception des travaux, signé par le client à la fin du chantier
- **Pourquoi :** Déclenche les garanties (parfait achèvement 1 an, décennale 10 ans). Protège l'artisan en cas de litige. Preuve que le client a accepté les travaux
- **Comment :** Généré automatiquement quand l'artisan passe un chantier en "Terminé". Pré-rempli. Envoyé au client pour signature. Horodaté

### F102. Duplication de devis en 1 tap (nouveau)
- **Quoi :** Dupliquer un devis existant pour un nouveau client en 1 tap
- **Pourquoi :** L'artisan fait souvent le même type de chantier. Au lieu de recréer un devis de zéro, il duplique et ajuste
- **Comment :** Bouton "Dupliquer" sur chaque devis. Copie tous les postes, prix, TVA. L'artisan change le client et ajuste les quantités

### F103. Calendrier avec vue semaine/mois + synchro Google (nouveau)
- **Quoi :** Calendrier visuel avec tous les RDV, chantiers, échéances fiscales, relances programmées
- **Pourquoi :** L'artisan a besoin de voir sa semaine d'un coup d'oeil. Quand est le prochain RDV client ? Quel chantier quel jour ? Quelle échéance URSSAF ?
- **Comment :** Vue semaine et mois. Synchro bidirectionnelle Google Calendar. Les chantiers, RDV clients, échéances fiscales et relances programmées apparaissent automatiquement. Accessible depuis le menu profil

### F104. Bibliothèque d'ouvrages navigable (nouveau)
- **Quoi :** Écran dédié pour naviguer, chercher, ajouter et modifier les ouvrages/matériaux/main d'oeuvre avec les prix de l'artisan
- **Pourquoi :** L'artisan doit pouvoir consulter et modifier ses prix visuellement, pas seulement via le chat. C'est la base de tous ses devis
- **Comment :** Liste cherchable par catégorie (plomberie, électricité, etc.), par mot-clé. Chaque entrée = descriptif + prix MO + prix fourniture + temps de pose + TVA. L'artisan ajoute, modifie, supprime. Les prix sont stockés en Mem0 et utilisés par l'Agent Devis

### F105. Gestion intérimaires et sous-traitants (nouveau)
- **Quoi :** Fiches pour les intérimaires et sous-traitants engagés ponctuellement sur un chantier
- **Pourquoi :** L'artisan fait appel à un intérimaire pour 2 jours ou sous-traite une partie du chantier. Il doit suivre les coûts et les heures
- **Comment :** Fiche intérimaire/sous-traitant (coordonnées, tarif, attestations). Affectation par chantier. Suivi heures et coûts. Impact sur la marge du chantier

### F106. Estimation dimensions par photo (Vision IA) — NOUVEAU
- L'artisan ou le client envoie 2-3 photos d'une pièce
- Claude Vision identifie les objets de référence (porte 204cm, prise 25cm, carrelage, fenêtre)
- Estime : longueur, largeur, hauteur, surface sol, surface murs
- Précision ~15-25%, score de confiance affiché
- Enchaîne avec le pré-devis : "Tu veux que je génère le devis sur cette base ?"
- Fonctionne sur TOUS les téléphones, via l'app ET WhatsApp (le client envoie les photos par WhatsApp)
- Intégré à la Galerie Photo Chantier et à la mémoire Mem0
- Sprint : 3 (Agent Devis + Vision IA)

### F107. Mesure AR (réalité augmentée Android) — V1.5
- L'artisan pointe la caméra, tape les 2 coins, la distance s'affiche
- ARCore via ViroReact ou expo-three
- Précision ~5-10cm
- Mesures intégrées directement dans le devis
- Stockées dans la fiche chantier
- Sprint : post-launch V1.5

### F108. Scan LiDAR pièce (iPhone Pro) — V2
- API RoomPlan d'Apple, scan 30 secondes
- Plan coté automatique (dimensions, portes, fenêtres)
- Précision ~1-2cm
- Export plan PDF annexé au devis
- Sprint : V2

### F109. Photogrammétrie multi-photos — V3
- 4-6 photos sous différents angles → reconstruction 3D → dimensions
- COLMAP ou API cloud
- Tous les téléphones, précision ~5-10%
- Sprint : V3

### F110. Pré-devis à distance (client envoie les photos) — V1
- Le client envoie des photos de sa pièce par WhatsApp ou email
- Le cerveau estime les dimensions + génère le pré-devis
- L'artisan reçoit : "Prospect Dupont, SDB ~6m², pré-devis estimé 4850€ TTC. Tu te déplaces ?"
- Filtre les prospects sérieux AVANT déplacement
- Sprint : 3 (même feature que F106 mais initiée par le client)

### F111. Intégration Plateforme Agréée (PA) — Facturation électronique conforme (NOUVEAU)
- STRUCTORAI se connecte à une PA immatriculée (FactPulse, B2Brouter ou Iopole) via API marque blanche
- Chaque facture générée est automatiquement transmise à la PA → format Factur-X → PPF → e-invoicing + e-reporting
- L'artisan ne voit rien : tout est transparent, la conformité est gérée en arrière-plan
- Gestion native des spécificités BTP : factures de situation (avancement), acomptes, avoirs, retenues de garantie 5%
- e-reporting B2C : transmission automatique des données de vente aux particuliers à l'administration fiscale
- 4 nouvelles mentions obligatoires facture auto-intégrées : SIREN client, adresse livraison, catégorie opération, TVA sur débits
- Mention déchets auto-calculée sur chaque devis (estimation quantité, tri, centre de collecte, coût)
- Attestation TVA taux réduit : mention texte sur le devis remplaçant les anciens CERFA (supprimés 01/01/2025)
- **Sprint :** Sprint 5 (avec Agent Factures)

---

## FEATURES AJOUTÉES — AUDIT V6 (22 AMÉLIORATIONS STRATÉGIQUES)

### F112. Import universel depuis concurrents (V1 — Sprint 2-3)
- **Quoi :** 6 parseurs d'import pour migrer depuis les concurrents (Obat, Tolteck, Batigest, EBP, Excel, Facture.net)
- **Pourquoi :** Faire sauter la friction #1 d'adoption — Sage Batigest a 1 132 avis 3.92/5 avec utilisateurs qui veulent migrer mais sont bloqués par 10 ans d'historique
- **Comment :** Endpoint `/v1/import/[source]` → dossier `backend/app/services/importers/` → templates mapping dans `data/imports/` → écran mobile `app/(dashboard)/settings/import.tsx` → preview avant validation
- **Sprint :** 2-3 (et non Sprint 8 comme initialement prévu dans STRATEGIE-COMPETITIVE — remonté en priorité)
- **Tag :** V1

### F113. Partenariats grossistes — scan carte fidélité (V1 — Sprint 4)
- **Quoi :** OCR carte fidélité + numéro compte client grossiste (Point P, Cedeo, BigMat) → import auto de l'historique d'achats
- **Pourquoi :** Les artisans ont un compte client chez 2-3 grossistes. Sans avoir à négocier un accord commercial, on peut ingérer leurs achats via scan
- **Comment :** V1 = OCR carte + extraction numéro compte → saisie manuelle historique. V2 = API intégration temps réel. V3 = partenariats commerciaux exclusifs post 2K MRR
- **Sprint :** 4 (V1), V2 (API), V3 (exclusif)
- **Tag :** V1 (scan) / V2 (API) / V3 (exclusif)

### F114. Code parrain structurel (V1 — Sprint 7)
- **Quoi :** Programme de parrainage : 1 mois offert parrain + filleul + 50€ crédit Point P (partenariat à venir)
- **Pourquoi :** Le BTP fonctionne au bouche-à-oreille. Formaliser cette mécanique = levier d'acquisition gratuit
- **Comment :** Migration `030_create_referrals.sql` → endpoints `/v1/referral/*` → code unique par artisan → tracking conversion → badge gamification "Ambassadeur" → dashboard filleuls
- **Écran mobile :** `app/(dashboard)/settings/parrainage.tsx`
- **Sprint :** 7
- **Tag :** V1

### F115. Sandbox publique sans inscription (V1 — Sprint 8)
- **Quoi :** Démo interactive sur structorai.app/try — chat avec le cerveau, 5 messages sans créer de compte
- **Pourquoi :** Le taux de conversion landing → signup est faible quand l'utilisateur ne peut pas tester. Laisser tester AVANT de demander l'email
- **Comment :** Endpoint `/v1/public/chat` (rate-limited IP, session Redis 15 min) → écran `app/(public)/try.tsx` → CTA "Crée ton compte" après 3 messages
- **Sprint :** 8
- **Tag :** V1

### F116. Rapport annuel de l'artisan (V1 — Sprint 7)
- **Quoi :** Bilan personnel envoyé 2×/an (1er janvier + 1er juillet) — CA, marge moyenne, top 3 clients, temps gagné, avis collectés, évolution vs période précédente
- **Pourquoi :** Renforcer la dépendance émotionnelle + fournir un argument fort pour rester abonné ("sans STRUCTORAI tu n'aurais pas ces chiffres")
- **Comment :** Cron `app/crons/rapport_annuel.py` → génération PDF + envoi email → archivage dans la fiche artisan
- **Sprint :** 7
- **Tag :** V1

### F117. Score "cerveau me connaît" — warning anti-churn (V1 — Sprint 7)
- **Quoi :** Enrichissement du composant `BrainProgress.tsx` avec warning visible : "Si tu pars, ton score repart à 0. Tu as investi X jours pour arriver à Y%."
- **Pourquoi :** Switching cost concret — le score est l'actif principal que l'artisan construit. Le matérialiser = moat psychologique
- **Comment :** Composant mobile enrichi, affichage à l'écran profil + lors de la tentative de résiliation
- **Sprint :** 7
- **Tag :** V1

### F118. Switching cost moat documenté (V1 — documentation)
- **Quoi :** Asymétrie volontaire : on accepte les imports entrants depuis concurrents (F112), on ne fournit PAS d'API export symétrique pour les concurrents
- **Pourquoi :** Douve stratégique — un artisan qui a 2 ans d'historique dans STRUCTORAI ne peut migrer facilement vers un concurrent. C'est intentionnel et assumé dans la stratégie produit
- **Comment :** Pas de feature à coder. Documenté dans PRODUCT_CONTEXT.md section "Moat" et décision dans `docs/DECISIONS-LOG.md`
- **Sprint :** — (documentation)
- **Tag :** V1

### F119. Agent Coach Business — 14ème agent (V1 — Sprint 6-7)
- **Quoi :** Agent IA dédié au conseil stratégique mensuel. Analyse automatique : taux horaire vs benchmark département, taux de conversion devis, carnet de commandes, marge moyenne, positioning concurrence, recommandations pricing/prospection
- **Pourquoi :** Les artisans ne savent pas s'ils sont bien payés, bien positionnés, rentables. Un coach humain = 500-2000€/an. Un coach IA mensuel = différenciateur massif vs concurrence
- **Comment :** Agent `agents/agent_coach.py`, modèle `claude-opus-4-7`, budget $0.10/appel. Prompt `prompts/coach_prompt.py`. Data `data/benchmarks/taux_horaires_par_metier_region.json`. Analyses déclenchées mensuellement par le Supervisor
- **Plans :** Inclus dans TOUS les plans. Starter = 1 analyse/mois, Pro/Business = illimité
- **Sprint :** 6-7
- **Tag :** V1

### F120. Formation contextuelle par capsules vidéo 60s (V1 — Sprint 7)
- **Quoi :** 50 capsules vidéo de 60 secondes déclenchées par le Supervisor selon le contexte (première facture → capsule "Comment lire ta facture", premier impayé → capsule "Mise en demeure")
- **Pourquoi :** Zéro documentation statique. Les artisans apprennent en contexte, au moment où ils en ont besoin. 60 secondes = attention soutenable
- **Comment :** Migration `031_create_capsules.sql` → data `data/capsules/index.json` (50 entrées avec `trigger_event`) → hébergement vidéo (à chiffrer dans COUT_REEL) → Supervisor déclenche capsule si trigger match contexte
- **Sprint :** 7 (infrastructure). Tournage des 50 capsules : 3 mois post-launch
- **Tag :** V1

### F121. Détection et analyse de devis concurrent (V1 — Sprint 3)
- **Quoi :** L'artisan upload un PDF/photo d'un devis concurrent → extension Agent Vision `parse_competitor_quote()` → comparaison avec référentiels BTP + Mem0 → argumentaire de réponse pour gagner le chantier
- **Pourquoi :** Arme unique — aucun concurrent ne le fait. L'artisan est régulièrement en compétition et n'a aucun outil pour analyser la concurrence
- **Comment :** Endpoint `/v1/devis/analyze-competitor` → écran `app/(dashboard)/devis/concurrent-analyzer.tsx` → sortie : analyse ligne par ligne, prix fort/faible vs marché, poste manquant chez le concurrent, argumentaire de vente
- **Sprint :** 3
- **Tag :** V1

### F122. Synchro comptable native (V2 — post-launch)
- **Quoi :** Intégration API avec Pennylane, Cegid, Sage, QuickBooks pour synchronisation bidirectionnelle
- **Pourquoi :** Les artisans Business (2-10 salariés) ont un expert-comptable qui utilise une de ces plateformes. Aujourd'hui on exporte en CSV — demain on pousse en temps réel
- **Comment :** Service `backend/app/services/accounting/` → 4 connecteurs → plan Business uniquement
- **Sprint :** V2 post-launch
- **Tag :** V2

### F123. Assignation tâches employés avec suivi photo (V1 — Sprint 7)
- **Quoi :** Extension Agent RH. Le patron assigne des tâches à ses employés → l'employé reçoit sur son mobile → prend photo début + photo fin → heures automatiquement tracké
- **Pourquoi :** Les patrons (Business) n'ont aucune visibilité sur ce que font leurs employés. Kanban tâches par employé + preuve photo = transparence totale
- **Comment :** Migration `032_create_tasks.sql` → endpoints `/v1/tasks/*` → écran Kanban patron → écran simplifié employé (voir F124)
- **Sprint :** 7
- **Tag :** V1

### F124. App mode Compagnon — écran employé simplifié (V1 — Sprint 7)
- **Quoi :** Version allégée STRUCTORAI pour les employés des Business — login par code 4 chiffres, photos début/fin de tâche, pointage, pas d'accès aux données commerciales
- **Pourquoi :** Les patrons Business ne veulent pas donner les infos CA/marge à leurs employés. Mais ils veulent que leurs employés utilisent l'app pour le pointage photo
- **Comment :** Routes `app/(compagnon)/*` dédiées → auth simplifiée → UI ultra-réduite (pointage + tâches du jour + photos)
- **Sprint :** 7
- **Tag :** V1

### F125. Onboarding inversé (V1 — Sprint 8)
- **Quoi :** Landing → chat public (F115) → premier devis VRAIMENT généré (via sandbox) → signup UNIQUEMENT pour envoyer au client
- **Pourquoi :** Inverser le funnel classique (signup → test → valeur). Ici : valeur prouvée AVANT signup. Cible conversion >25% (vs >12% actuel)
- **Comment :** Modifier UX_PARCOURS.md section onboarding → réorganiser la landing → wire sandbox (F115) avec sign-up bloquant uniquement pour l'envoi
- **Sprint :** 8
- **Tag :** V1

### F126. Télé-dépannage — extension Agent Téléphone (V2)
- **Quoi :** L'Agent Téléphone reçoit un appel avec problème urgent → diagnostic via arbre de décision (50 pannes × 11 métiers = 550 arbres) → résolution à distance si possible → artisan paie 2€ par dépannage résolu, 50/50 artisan/plateforme
- **Pourquoi :** Monétisation supplémentaire + valeur pour le client final. Plomberie : "l'eau fuit sous l'évier" → 30 secondes de diagnostic → "vérifie le siphon, serre à la main" → problème résolu sans déplacement
- **Comment :** Data `data/diagnostics/[metier].json` (11 fichiers) → migration `034_create_diagnostics.sql` → tracking paiements
- **Sprint :** V2 post-launch
- **Tag :** V2

### F127. Portail client final (V2 — post-launch)
- **Quoi :** Mini-PWA React légère sur sous-domaine `client.structorai.app/[chantier-token]` — le client final de l'artisan voit ses devis, factures, photos, signe, paye
- **Pourquoi :** Aujourd'hui le client reçoit des emails ponctuels. Un portail = expérience premium + upsell naturel (l'artisan vend plus facilement le plan Pro pour avoir le portail client)
- **Comment :** Auth par token URL unique (pas de compte client) → endpoints `/v1/client-portal/*` → migration `035_create_client_tokens.sql`
- **Sprint :** V2 post-launch
- **Tag :** V2

### F128. Validation auto RGE / Qualibat (V1 — Sprint 3)
- **Quoi :** Service `backend/app/services/certif_validation.py` qui interroge l'API data.gouv.fr / RGE.fr à l'inscription + re-check mensuel. Blocage génération devis TVA 5.5% si RGE invalide
- **Pourquoi :** La TVA 5.5% requiert une certification RGE valide. Un devis TVA 5.5% sans RGE = redressement fiscal pour l'artisan. On sécurise légalement
- **Comment :** API officielle gratuite data.gouv.fr → cache 30 jours → alerte artisan si RGE expire dans <60 jours → blocage devis TVA 5.5% si invalide
- **Sprint :** 3
- **Tag :** V1

### F129. Pack contrôle fiscal (V1 — Sprint 6)
- **Quoi :** Extension Agent Fiscalité. Endpoint `/v1/export/tax-audit` génère un ZIP signé horodatage certifié contenant : devis + factures + tickets OCR + justifs TVA + registres comptables
- **Pourquoi :** En cas de contrôle fiscal, l'artisan doit fournir un dossier complet sous 30 jours. Aujourd'hui c'est un enfer. Avec STRUCTORAI : 1 clic, ZIP généré, horodaté
- **Comment :** Service `backend/app/services/tax_audit_export.py` → signature horodatée via API qualifiée → ZIP envoyé par email + archivé 10 ans
- **Sprint :** 6
- **Tag :** V1

### F130. Tarification enrichie — 7 options (V1 — Sprint 1)
- **Quoi :** Plans Starter €0 / Pro €29 / Pro annuel €229 / Business €79 / Business annuel €629 / Lifetime €990 (100 licences) / Fédération (code `CAPEB20` / `FFB20` -20% à vie). Trial 30 jours. Add-on Agent Téléphone +15€/mois
- **Pourquoi :** Segmentation plus fine → meilleure conversion. Annuel = cash flow + rétention. Lifetime = marketing launch. Fédération = partenariats institutionnels
- **Comment :** Migration `033_update_subscriptions_plans.sql` → Stripe Products (annuels, Lifetime, Fédération) → logique validation code promo avec justificatif membre CAPEB/FFB
- **Sprint :** 1
- **Tag :** V1

### F131. Conformité AI Act (V1 — Sprint 6)
- **Quoi :** UI avec badge "Décision IA" sur toute sortie générée + bouton "Demander validation humaine" + page publique `/transparency` + documentation technique modèles
- **Pourquoi :** AI Act européen applicable août 2026. Les LLM orientés décisions financières (devis, fiscal) sont GPAI à risque limité. Documentation obligatoire
- **Comment :** Migration `036_create_ai_audit_log.sql` → log de chaque décision IA → page `/transparency` publique → doc technique créée avant août 2026
- **Sprint :** 6
- **Tag :** V1

### F132. Résilience et redondance (V1 — Sprint 8)
- **Quoi :** Fallback LLM (Anthropic down → GPT-4 backup) + read replica Supabase (+25$/mois) + monitoring UptimeRobot + Better Uptime + SLA interne 99.5% documenté dans CGV
- **Pourquoi :** Quand Anthropic a un outage (déjà arrivé plusieurs fois), toute l'app tombe. Un fallback OpenAI maintient le service. SLA = argument Business
- **Comment :** Abstraction client LLM multi-provider (déjà en place partiellement) → read replica Supabase pour les lectures chat history → monitoring externe
- **Sprint :** 8
- **Tag :** V1

### F133. Acquisition artisans étrangers en France (V1 — Sprint 8)
- **Quoi :** Adaptation Agent Planning (calendrier Ramadan, fêtes religieuses) + landings localisées `/tr`, `/ar`, `/pt`, `/es` ciblant les communautés étrangères installées en France
- **Pourquoi :** 500K artisans en France, dont ~15% ont une langue maternelle non-française (turc, arabe, portugais, espagnol). Marché invisible que les concurrents n'adressent PAS
- **Comment :** Landings déjà localisées grâce à l'i18n → ajustement du calendrier des agents pour les fêtes culturelles → distribution ciblée (Facebook groupes communauté)
- **Sprint :** 8
- **Tag :** V1

### F134. Verticalisation / éditions métier (V1 architecture, V2 lancement)
- **Quoi :** Architecture `country_code` + `edition` sur `organizations` dès V1. Éditions métier spécialisées (STRUCTORAI-Plomberie, STRUCTORAI-Électricité) lancées post 2K MRR
- **Pourquoi :** Marketing plus fort ("le logiciel des plombiers") + devis ultra-spécialisés par métier. Mais pas rentable de fragmenter avant d'avoir la base horizontale
- **Comment :** Migration `037_add_country_edition.sql` → architecture prête en V1 → éditions lancées en V2 si MRR > 2K€
- **Sprint :** V1 (schéma), V2 (lancement éditions)
- **Tag :** V1 (architecture) / V2 (éditions)

### F135. Architecture multi-pays (V1 — Sprint 1)
- **Quoi :** Colonne `country_code` sur toutes les tables dès le début. Règles BTP modulaires par pays (structure prête, contenu FR uniquement en V1). Plan d'expansion chiffré : BE 2 mois / CH 2 mois / PT 3 mois / ES 4 mois / IT 5 mois / DE 5 mois / UK 5 mois
- **Pourquoi :** Ajouter `country_code` sur 30 tables a posteriori = migration douloureuse. Le faire dès le jour 1 = zero friction pour l'expansion Europe
- **Comment :** Toutes les migrations Sprint 1 intègrent `country_code TEXT NOT NULL DEFAULT 'FR'` + index. Architecture règles métier modulaires dans `data/legal/{country}/` et `data/prix/{country}/`
- **Sprint :** 1
- **Tag :** V1

---

## RÉSUMÉ DES CHANGEMENTS

| Type | Nombre |
|------|--------|
| Features modifiées/enrichies | 23 |
| Nouvelles features V1 ajoutées (audit initial) | 24 (F87-F110) |
| F111 ajoutée audit 15/04 | 1 (F111) |
| **Nouvelles features audit V6 (17/04)** | **24 (F112-F135)** |
| **Total features** | **135** (110 + F111 + 24 = 135) |
| **Total agents V1** | **14** (+Agent Coach Business F119) + Supervisor |
| **V2** | Agent Téléphone IA + F122 (Synchro compta) + F126 (Télé-dépannage) + F127 (Portail client) |
| **V3** | F109 (Photogrammétrie) + F113 V3 (API grossistes exclusives) |

### Découpage V1 / V2 / V3

| Sprint | Features |
|--------|----------|
| **V1 launch juin 2026** | F01-F111 + F112-F121 + F123-F125 + F128-F135 (tout sauf 3 reports) |
| **V2 post-launch sept-oct 2026** | F122, F126, F127, F113 V2 (API grossistes), Agent Téléphone IA de base |
| **V3 2027+** | F109 (photogrammétrie), F113 V3 (API grossistes exclusif, bloqué jusqu'à 2K MRR) |

### Impact BUILD_PLAN audit V6
- 1 nouvel agent backend : `agent_coach.py` (14ème agent)
- 1 nouveau prompt : `coach_prompt.py`
- 8 nouvelles migrations : `030_create_referrals.sql` → `037_add_country_edition.sql`
- 5 nouveaux skills à planifier : `SKILL-IMPORT-COMPETITORS.md`, `SKILL-COMPETITOR-ANALYSIS.md`, `SKILL-ACCOUNTING-INTEGRATION.md`, `SKILL-AI-ACT-COMPLIANCE.md`, `SKILL-RETENTION-PATTERNS.md`
- 7 nouveaux docs à créer (plan `FICHIERS_A_CREER.md`) : `PARTENARIATS.md`, `TRUST-SIGNALS.md`, `docs/RETENTION.md`, `docs/AI-ACT-COMPLIANCE.md`, `docs/RESILIENCE.md`, `docs/VERTICAL-EDITIONS.md`, `docs/COUTS-V2.md`
- Écrans mobile : `settings/import.tsx`, `settings/parrainage.tsx`, `devis/concurrent-analyzer.tsx`, `public/try.tsx`, routes `(compagnon)/`
- Nouveaux fichiers data : `data/benchmarks/`, `data/capsules/`, `data/diagnostics/`, `data/imports/`
- Budget allocation Supervisor redistribué sur 14 agents (voir PRODUCT_CONTEXT.md)

### Impact chiffres de référence

| Sujet | Avant | Après |
|---|---|---|
| Agents V1 | 13 | **14** |
| Total composants | 18 | **19** |
| Features | 110 | **135** |
| Migrations SQL | 29 | **37** |
| Coût LLM/artisan/mois | $4.50 | **~$4.80** |
| Marge Pro | 82% | **80%** |
| Marge Business | 91% | **89%** |
| Trial | 14 jours | **30 jours** |
| Plans tarifaires | 3 | **7** |
