# STRUCTORAI — FICHIERS MÉTIER À PRÉPARER
> Ces fichiers sont le SAVOIR du cerveau IA.
> Sans eux, le cerveau hallucine des prix et oublie des mentions légales.
> Ils sont dans le dossier `data/` du repo.
> Fabrice valide le contenu terrain, Claude Code les intègre dans le RAG.
> Date : 03/04/2026

---

## 1. RÉGLEMENTATION & MENTIONS LÉGALES

### data/legal/mentions_obligatoires_devis.json
Les 15 mentions LÉGALES obligatoires d'un devis BTP 2026 (sur 47 mentions totales — voir PRODUCT_CONTEXT.md pour la liste complète incluant les 32 mentions complémentaires BTP) :
1. Terme "devis" ou "proposition de prix"
2. Date du devis
3. Numéro de devis (format : DEV-2026-001)
4. Identité artisan : raison sociale, forme juridique (EI, SARL, SAS, SASU, EURL), adresse siège
5. Numéro SIRET (14 chiffres, PAS le SIREN 9 chiffres)
6. Numéro TVA intracommunautaire (ou mention "TVA non applicable – art. 293 B du CGI" si franchise)
7. Code APE/NAF
8. Assurance décennale : nom assureur, numéro de police, couverture géographique
9. Qualifications : RGE, Qualibat, Qualifelec, QualiPAC (si applicable)
10. Identité client : nom, adresse
11. Adresse du chantier (si différente de l'adresse client)
12. Description détaillée des travaux : chaque poste avec désignation, quantité, unité, prix unitaire HT
13. Prix : total HT par poste, total HT global, taux TVA, montant TVA, total TTC
14. Durée de validité du devis (recommandé : 30-90 jours)
15. Conditions de paiement : acompte (max 30% > 300€), échéancier, pénalités de retard (min 3x taux intérêt légal), indemnité forfaitaire 40€

Mentions complémentaires BTP :
- Date prévisionnelle de début des travaux
- Durée estimée des travaux
- Conditions de révision de prix
- Droit de rétractation 14 jours (démarchage à domicile)
- Attestation TVA réduite (à faire signer par le client si taux réduit)
- Clause "Toute prestation non prévue fera l'objet d'un devis complémentaire"
- Mention manuscrite client : "Devis reçu avant l'exécution des travaux, lu et accepté, bon pour accord" + date + signature

Sanctions :
- Devis absent : jusqu'à 3 000€ (personne physique) / 15 000€ (société)
- Mention décennale absente : 75 000€ + 6 mois d'emprisonnement
- Facture non conforme : 75 000€ (personne physique) / 375 000€ (société)

### data/legal/mentions_obligatoires_facture.json
Similaire au devis + :
- Numéro de facture (chronologique, continu, sans trou)
- Date de la facture
- Date de livraison / exécution
- SIREN client (obligatoire B2B depuis 01/07/2024)
- Numéro de bon de commande (si applicable)

### data/legal/tva_rules.json
```json
{
  "taux": [
    {
      "taux": 5.5,
      "label": "Taux réduit - rénovation énergétique",
      "conditions": [
        "Logement achevé depuis + de 2 ans",
        "Usage d'habitation (résidence principale ou secondaire)",
        "Travaux d'amélioration de la performance énergétique",
        "Pose, installation et entretien de matériaux et équipements éligibles",
        "Exemples : isolation thermique, chaudière condensation, pompe à chaleur, fenêtres double vitrage"
      ],
      "attestation_client": true,
      "note": "Le client doit attester sur le devis que les conditions sont remplies"
    },
    {
      "taux": 10,
      "label": "Taux intermédiaire - travaux de rénovation",
      "conditions": [
        "Logement achevé depuis + de 2 ans",
        "Usage d'habitation",
        "Travaux d'amélioration, transformation, aménagement, entretien",
        "S'applique à la main d'oeuvre ET aux matériaux fournis par l'artisan"
      ],
      "attestation_client": true,
      "exceptions": [
        "Ne s'applique PAS aux équipements ménagers/mobiliers",
        "Ne s'applique PAS à la climatisation (sauf réversible)",
        "Ne s'applique PAS si les travaux augmentent la surface > 10%"
      ]
    },
    {
      "taux": 20,
      "label": "Taux normal",
      "conditions": [
        "Construction neuve",
        "Logement de - de 2 ans",
        "Locaux professionnels",
        "Fournitures achetées séparément par le client",
        "Équipements non éligibles au taux réduit"
      ],
      "attestation_client": false
    }
  ],
  "cas_speciaux": {
    "autoliquidation_sous_traitance": "Le sous-traitant facture HT, le donneur d'ordre auto-liquide la TVA",
    "franchise_base": "TVA non applicable – art. 293 B du CGI (auto-entrepreneurs sous seuil)",
    "mixte_sur_meme_devis": "Un devis peut contenir des lignes à 10% (MO rénovation) et 20% (fournitures neuves)"
  }
}
```

### data/legal/factur_x.json
Spécifications Factur-X pour la conformité septembre 2026 :
- Format : PDF/A-3 + fichier XML embarqué
- Standard : EN 16931
- Plateformes agréées (PDP) : liste des 106+ plateformes immatriculées
- Calendrier : réception obligatoire sept 2026, émission obligatoire sept 2027 (TPE/PME)
- Champs XML obligatoires : SIRET, TVA, montants, dates, numérotation

### data/legal/retractation.json
Formulaire de rétractation 14 jours (démarchage à domicile) — modèle légal

### data/legal/penalites_retard.json
Taux d'intérêt légal en vigueur (mis à jour chaque semestre) + calcul pénalités

---

## 2. PRIX DU MARCHÉ PAR CORPS DE MÉTIER

### data/prix/plomberie.json
```json
{
  "corps_metier": "plomberie",
  "derniere_maj": "2026-04",
  "source": "Travaux.com, TarifArtisan.fr, AlloTravaux, Ootravaux, Prix-Pose.com, forums artisans — prix moyens France 2025-2026",
  "taux_horaire_mo": {"min": 40, "max": 70, "moyen": 55, "unite": "€HT/h"},
  "frais_deplacement": {"min": 20, "max": 60, "moyen": 35, "note": "Gratuit si < 15km du siège, facturable au-delà"},
  
  "postes": [
    {
      "categorie": "DÉPOSE / DÉMOLITION",
      "items": [
        {
          "designation": "Dépose baignoire + évacuation gravats",
          "unite": "u",
          "prix_mo_ht": {"min": 120, "max": 200, "moyen": 150},
          "prix_fourniture_ht": 0,
          "duree_heures": {"min": 1.5, "max": 3},
          "tva_mo": 10,
          "fournisseurs": [],
          "note": "Prévoir benne si baignoire fonte (lourde). Déconnexion eau + évacuation avant dépose."
        },
        {
          "designation": "Dépose receveur de douche existant",
          "unite": "u",
          "prix_mo_ht": {"min": 80, "max": 150, "moyen": 100},
          "prix_fourniture_ht": 0,
          "duree_heures": {"min": 1, "max": 2},
          "tva_mo": 10,
          "note": "Plus rapide que baignoire. Attention à ne pas abîmer les évacuations."
        },
        {
          "designation": "Dépose WC (posé au sol)",
          "unite": "u",
          "prix_mo_ht": {"min": 50, "max": 100, "moyen": 70},
          "prix_fourniture_ht": 0,
          "duree_heures": {"min": 0.5, "max": 1},
          "tva_mo": 10,
          "note": "Fermer l'arrivée d'eau, vider le réservoir, dévisser les fixations, reboucher l'évacuation."
        },
        {
          "designation": "Dépose WC suspendu + bâti-support",
          "unite": "u",
          "prix_mo_ht": {"min": 100, "max": 200, "moyen": 150},
          "prix_fourniture_ht": 0,
          "duree_heures": {"min": 1.5, "max": 3},
          "tva_mo": 10,
          "note": "Démontage coffrage placo inclus. Plus long que WC posé."
        },
        {
          "designation": "Dépose lavabo / vasque sur meuble",
          "unite": "u",
          "prix_mo_ht": {"min": 50, "max": 100, "moyen": 70},
          "prix_fourniture_ht": 0,
          "duree_heures": {"min": 0.5, "max": 1},
          "tva_mo": 10,
          "note": "Déconnexion alimentation + siphon. Reboucher si trous dans mur."
        },
        {
          "designation": "Évacuation gravats / déchets chantier",
          "unite": "forfait",
          "prix_mo_ht": {"min": 80, "max": 200, "moyen": 120},
          "prix_fourniture_ht": {"min": 150, "max": 350, "note": "Location benne"},
          "duree_heures": {"min": 1, "max": 2},
          "tva_mo": 10,
          "tva_fourniture": 20,
          "fournisseurs": ["Location benne: Kiloutou, Loxam, Paprec"],
          "note": "Benne 3m³ = 150-250€. Benne 5m³ = 200-350€. Délai de livraison 24-48h."
        }
      ]
    },
    {
      "categorie": "PLOMBERIE — ALIMENTATION & ÉVACUATION",
      "items": [
        {
          "designation": "Création point d'eau complet (alimentation eau chaude/froide + évacuation)",
          "unite": "u",
          "prix_mo_ht": {"min": 350, "max": 600, "moyen": 450},
          "prix_fourniture_ht": {"min": 50, "max": 150},
          "duree_heures": {"min": 4, "max": 8},
          "tva_mo": 10,
          "tva_fourniture": 20,
          "fournisseurs": ["Point P", "Cedeo", "Rexel"],
          "note": "Comprend tirage de tuyaux, percement, raccordement. Prix très variable selon distance depuis nourrice existante."
        },
        {
          "designation": "Modification / déplacement réseau d'alimentation existant",
          "unite": "forfait",
          "prix_mo_ht": {"min": 200, "max": 500, "moyen": 350},
          "prix_fourniture_ht": {"min": 30, "max": 100},
          "duree_heures": {"min": 2, "max": 5},
          "tva_mo": 10,
          "tva_fourniture": 20,
          "note": "Quand on déplace un lavabo ou une douche. Inclut raccords, coudes, colliers."
        },
        {
          "designation": "Tuyauterie cuivre (fourniture + pose)",
          "unite": "ml",
          "prix_mo_ht": {"min": 30, "max": 45, "moyen": 35},
          "prix_fourniture_ht": {"min": 10, "max": 20, "moyen": 15},
          "duree_heures_par_ml": 0.5,
          "tva_mo": 10,
          "tva_fourniture": 20,
          "fournisseurs": ["Point P", "Cedeo", "Brossette"],
          "marques_courantes": ["KME", "Wieland", "Sanco"],
          "note": "Diamètres courants : 12/14 (lavabo), 14/16 (douche), 16/18 (baignoire). Le cuivre est plus cher mais plus durable que le PER."
        },
        {
          "designation": "Tuyauterie PER (fourniture + pose)",
          "unite": "ml",
          "prix_mo_ht": {"min": 20, "max": 30, "moyen": 25},
          "prix_fourniture_ht": {"min": 5, "max": 12, "moyen": 8},
          "duree_heures_par_ml": 0.3,
          "tva_mo": 10,
          "tva_fourniture": 20,
          "fournisseurs": ["Point P", "Cedeo"],
          "marques_courantes": ["Giacomini", "Uponor", "Rehau", "Henco"],
          "note": "Plus rapide à poser que le cuivre. Raccords à sertir ou à compression. Diamètres courants : 12 (lavabo), 16 (douche/baignoire), 20 (colonne)."
        },
        {
          "designation": "Tuyauterie PVC évacuation (fourniture + pose)",
          "unite": "ml",
          "prix_mo_ht": {"min": 15, "max": 30, "moyen": 20},
          "prix_fourniture_ht": {"min": 5, "max": 15, "moyen": 8},
          "duree_heures_par_ml": 0.3,
          "tva_mo": 10,
          "tva_fourniture": 20,
          "fournisseurs": ["Point P", "BigMat"],
          "marques_courantes": ["Nicoll", "Girpi", "Wavin"],
          "note": "Diamètres : 32mm (lavabo), 40mm (douche/baignoire), 100mm (WC). Pente min 1% pour bonne évacuation."
        },
        {
          "designation": "Pose nourrice / collecteur (distribution sanitaire)",
          "unite": "u",
          "prix_mo_ht": {"min": 150, "max": 300, "moyen": 200},
          "prix_fourniture_ht": {"min": 80, "max": 250},
          "duree_heures": {"min": 2, "max": 4},
          "tva_mo": 10,
          "tva_fourniture": 20,
          "fournisseurs": ["Cedeo", "Point P"],
          "marques_courantes": ["Giacomini", "Caleffi", "Watts"],
          "note": "Distribution en pieuvre depuis la nourrice. Plus propre et plus pratique que le cuivre en série."
        }
      ]
    },
    {
      "categorie": "SANITAIRES — POSE",
      "items": [
        {
          "designation": "Pose receveur de douche extra-plat (sans fourniture receveur)",
          "unite": "u",
          "prix_mo_ht": {"min": 200, "max": 350, "moyen": 250},
          "prix_fourniture_ht": {"note": "Receveur fourni par client ou artisan — 150€ à 600€ selon marque/taille"},
          "duree_heures": {"min": 2, "max": 4},
          "tva_mo": 10,
          "fournisseurs": ["Cedeo", "Espace Aubade", "Leroy Merlin", "Point P"],
          "marques_courantes": {
            "entree_gamme": ["Bette", "Ideal Standard — 150-250€"],
            "milieu_gamme": ["Wedi Fundo", "Kaldewei — 250-450€"],
            "haut_gamme": ["Bette Floor", "Duravit Stonetto — 400-600€"]
          },
          "note": "Vérifier la hauteur d'encastrement. Prévoir ragréage si sol pas plan. Étanchéité SOUS le receveur obligatoire."
        },
        {
          "designation": "Pose paroi de douche fixe (sans fourniture paroi)",
          "unite": "u",
          "prix_mo_ht": {"min": 100, "max": 200, "moyen": 150},
          "prix_fourniture_ht": {"note": "Paroi fournie par client ou artisan — 150€ à 800€ selon marque/taille"},
          "duree_heures": {"min": 1.5, "max": 3},
          "tva_mo": 10,
          "marques_courantes": ["Kinedo", "SanSwiss", "Schulte", "Novellini"],
          "note": "Vérifier l'équerrage du mur. Silicone transparent ou blanc selon finition."
        },
        {
          "designation": "Pose colonne de douche thermostatique (fourniture + pose)",
          "unite": "u",
          "prix_mo_ht": {"min": 100, "max": 180, "moyen": 120},
          "prix_fourniture_ht": {"min": 100, "max": 500, "moyen": 250},
          "duree_heures": {"min": 1.5, "max": 2.5},
          "tva_mo": 10,
          "tva_fourniture": 20,
          "fournisseurs": ["Cedeo", "Espace Aubade", "Leroy Merlin"],
          "marques_courantes": {
            "entree_gamme": ["GRB, Rousseau — 100-180€"],
            "milieu_gamme": ["Grohe Euphoria, Hansgrohe Crometta — 180-350€"],
            "haut_gamme": ["Grohe Rainshower, Hansgrohe Raindance — 350-600€"]
          },
          "note": "Thermostatique = sécurité anti-brûlure (obligatoire dans certains cas). Vérifier entraxe 150mm."
        },
        {
          "designation": "Pose baignoire acrylique (sans fourniture baignoire)",
          "unite": "u",
          "prix_mo_ht": {"min": 250, "max": 500, "moyen": 350},
          "prix_fourniture_ht": {"note": "Baignoire fournie — 150€ à 1500€ selon modèle"},
          "duree_heures": {"min": 3, "max": 6},
          "tva_mo": 10,
          "marques_courantes": ["Jacob Delafon", "Allibert", "Ideal Standard", "Villeroy & Boch"],
          "note": "Calage, raccordement vidage, tablier. Prévoir accès au siphon pour entretien futur."
        },
        {
          "designation": "Pose meuble vasque + robinetterie (sans fourniture meuble)",
          "unite": "u",
          "prix_mo_ht": {"min": 120, "max": 250, "moyen": 150},
          "prix_fourniture_ht": {"note": "Meuble fourni — 200€ à 1500€ selon marque/taille"},
          "duree_heures": {"min": 1.5, "max": 3},
          "tva_mo": 10,
          "marques_courantes": {
            "entree_gamme": ["Allibert, Cooke & Lewis (Castorama) — 200-400€"],
            "milieu_gamme": ["Ikea Godmorgon, Leroy Merlin — 400-800€"],
            "haut_gamme": ["Jacob Delafon, Duravit, Villeroy & Boch — 800-1500€"]
          },
          "note": "Fixation murale ou posé. Vérifier si le mur supporte le poids. Raccordement siphon + robinetterie."
        },
        {
          "designation": "Pose lavabo sur colonne (fourniture + pose)",
          "unite": "u",
          "prix_mo_ht": {"min": 80, "max": 200, "moyen": 120},
          "prix_fourniture_ht": {"min": 50, "max": 300, "moyen": 150},
          "duree_heures": {"min": 1, "max": 2},
          "tva_mo": 10,
          "tva_fourniture": 20,
          "note": "Solution économique. Raccordement alimentation + siphon + fixation murale."
        },
        {
          "designation": "Pose WC posé au sol (fourniture + pose)",
          "unite": "u",
          "prix_mo_ht": {"min": 100, "max": 250, "moyen": 150},
          "prix_fourniture_ht": {"min": 80, "max": 400, "moyen": 200},
          "duree_heures": {"min": 1, "max": 2},
          "tva_mo": 10,
          "tva_fourniture": 20,
          "fournisseurs": ["Cedeo", "Point P", "Leroy Merlin", "Castorama"],
          "marques_courantes": ["Geberit", "Grohe", "Ideal Standard", "Jacob Delafon", "Villeroy & Boch"],
          "note": "Vérifier sortie horizontale ou verticale. Joint cire ou manchette. Vis de fixation au sol."
        },
        {
          "designation": "Pose WC suspendu complet (bâti-support + cuvette + plaque + coffrage placo)",
          "unite": "u",
          "prix_mo_ht": {"min": 250, "max": 600, "moyen": 400},
          "prix_fourniture_ht": {"min": 250, "max": 1000, "moyen": 500},
          "duree_heures": {"min": 4, "max": 7},
          "tva_mo": 10,
          "tva_fourniture": 20,
          "fournisseurs": ["Cedeo", "Espace Aubade", "Point P"],
          "marques_courantes": {
            "bati_support": ["Geberit Duofix — 200-350€", "Grohe Rapid SL — 180-300€", "Viega Prevista — 250-400€"],
            "cuvette": ["Villeroy & Boch O.Novo — 100-200€", "Geberit Renova — 150-250€", "Duravit Starck 3 — 200-350€"],
            "plaque_commande": ["Geberit Sigma — 50-150€", "Grohe Skate — 40-120€"]
          },
          "note": "Le poste le plus cher en sanitaire. Inclut : fixation bâti au mur/sol, raccordement eau+évacuation, coffrage BA13 hydrofuge, pose plaque commande. Hors carrelage sur coffrage (poste carreleur). Durée 1 journée complète. Prévoir accès pour maintenance future."
        },
        {
          "designation": "Pose bidet (fourniture + pose)",
          "unite": "u",
          "prix_mo_ht": {"min": 100, "max": 200, "moyen": 150},
          "prix_fourniture_ht": {"min": 100, "max": 400},
          "duree_heures": {"min": 1.5, "max": 2.5},
          "tva_mo": 10,
          "tva_fourniture": 20,
          "note": "Rare en 2026 mais encore demandé par certains clients. Raccordement similaire à un lavabo."
        },
        {
          "designation": "Pose sèche-serviette eau chaude (fourniture + pose)",
          "unite": "u",
          "prix_mo_ht": {"min": 150, "max": 300, "moyen": 200},
          "prix_fourniture_ht": {"min": 100, "max": 600, "moyen": 250},
          "duree_heures": {"min": 2, "max": 4},
          "tva_mo": 10,
          "tva_fourniture": 20,
          "fournisseurs": ["Cedeo", "Point P", "Leroy Merlin"],
          "marques_courantes": ["Acova", "Atlantic", "Thermor", "Zehnder"],
          "note": "Raccordement sur circuit chauffage existant. Si électrique seul : pas besoin de plombier (électricien suffit). Mixte (eau + résistance) = plus cher."
        },
        {
          "designation": "Pose robinet / mitigeur (remplacement simple)",
          "unite": "u",
          "prix_mo_ht": {"min": 50, "max": 100, "moyen": 60},
          "prix_fourniture_ht": {"min": 30, "max": 300, "moyen": 100},
          "duree_heures": {"min": 0.5, "max": 1},
          "tva_mo": 10,
          "tva_fourniture": 20,
          "marques_courantes": ["Grohe", "Hansgrohe", "Ideal Standard", "Rousseau"],
          "note": "Le plus rapide. Fermer eau, dévisser ancien, poser nouveau, joints neufs, ouvrir eau, vérifier étanchéité."
        }
      ]
    },
    {
      "categorie": "EAU CHAUDE",
      "items": [
        {
          "designation": "Pose chauffe-eau électrique (remplacement, 100-200L)",
          "unite": "u",
          "prix_mo_ht": {"min": 200, "max": 400, "moyen": 280},
          "prix_fourniture_ht": {"min": 250, "max": 800, "moyen": 450},
          "duree_heures": {"min": 2, "max": 4},
          "tva_mo": 10,
          "tva_fourniture": 20,
          "fournisseurs": ["Cedeo", "Point P", "Leroy Merlin"],
          "marques_courantes": ["Atlantic Zeneo", "Thermor Duralis", "Ariston Velis", "De Dietrich"],
          "note": "Vérifier capacité support mural (un 200L plein pèse ~250kg). Groupe de sécurité neuf obligatoire. Raccordement électrique par l'électricien si pas de prise dédiée."
        },
        {
          "designation": "Pose chauffe-eau thermodynamique",
          "unite": "u",
          "prix_mo_ht": {"min": 500, "max": 1000, "moyen": 700},
          "prix_fourniture_ht": {"min": 1500, "max": 3500, "moyen": 2200},
          "duree_heures": {"min": 4, "max": 8},
          "tva_mo": 5.5,
          "tva_fourniture": 5.5,
          "fournisseurs": ["Cedeo", "Point P"],
          "marques_courantes": ["Atlantic Explorer", "Thermor Aéromax", "De Dietrich Kaliko"],
          "note": "TVA 5.5% si éligible MaPrimeRénov (rénovation énergétique). Aide MaPrimeRénov jusqu'à 1200€ revenus modestes. Local ventilé min 20m³ requis."
        },
        {
          "designation": "Détartrage / entretien chauffe-eau",
          "unite": "u",
          "prix_mo_ht": {"min": 100, "max": 200, "moyen": 150},
          "prix_fourniture_ht": {"min": 0, "max": 50},
          "duree_heures": {"min": 1, "max": 2},
          "tva_mo": 10,
          "note": "Remplacement anode si nécessaire (50-80€ fourniture). Recommandé tous les 2-3 ans."
        }
      ]
    },
    {
      "categorie": "DÉPANNAGE / RÉPARATION",
      "items": [
        {
          "designation": "Débouchage canalisation (furet manuel)",
          "unite": "u",
          "prix_mo_ht": {"min": 60, "max": 150, "moyen": 80},
          "prix_fourniture_ht": 0,
          "duree_heures": {"min": 0.5, "max": 1.5},
          "tva_mo": 10,
          "note": "Si furet insuffisant → débouchage haute pression (150-400€)."
        },
        {
          "designation": "Débouchage WC",
          "unite": "u",
          "prix_mo_ht": {"min": 50, "max": 150, "moyen": 80},
          "prix_fourniture_ht": 0,
          "duree_heures": {"min": 0.5, "max": 1},
          "tva_mo": 10,
          "note": "Ventouse, furet, ou démontage WC si objet coincé."
        },
        {
          "designation": "Réparation fuite (joint, raccord, soudure)",
          "unite": "u",
          "prix_mo_ht": {"min": 60, "max": 200, "moyen": 100},
          "prix_fourniture_ht": {"min": 5, "max": 50},
          "duree_heures": {"min": 0.5, "max": 2},
          "tva_mo": 10,
          "note": "Variable selon accessibilité. Fuite sous dalle = beaucoup plus cher (recherche + réparation)."
        },
        {
          "designation": "Recherche de fuite (caméra thermique / endoscopique)",
          "unite": "u",
          "prix_mo_ht": {"min": 200, "max": 600, "moyen": 350},
          "prix_fourniture_ht": 0,
          "duree_heures": {"min": 1, "max": 3},
          "tva_mo": 10,
          "note": "Nécessaire quand fuite non visible (sous dalle, dans mur). Rapport écrit pour assurance souvent demandé."
        },
        {
          "designation": "Remplacement groupe de sécurité chauffe-eau",
          "unite": "u",
          "prix_mo_ht": {"min": 60, "max": 120, "moyen": 80},
          "prix_fourniture_ht": {"min": 15, "max": 40, "moyen": 25},
          "duree_heures": {"min": 0.5, "max": 1},
          "tva_mo": 10,
          "tva_fourniture": 20,
          "note": "Intervention rapide mais souvent urgente (fuite au groupe)."
        },
        {
          "designation": "Remplacement mécanisme WC (chasse d'eau)",
          "unite": "u",
          "prix_mo_ht": {"min": 50, "max": 100, "moyen": 70},
          "prix_fourniture_ht": {"min": 15, "max": 60, "moyen": 30},
          "duree_heures": {"min": 0.5, "max": 1},
          "tva_mo": 10,
          "tva_fourniture": 20,
          "marques_courantes": ["Geberit", "Wirquin", "Siamp"],
          "note": "Intervention fréquente. Kit mécanisme complet souvent plus fiable que réparer l'ancien."
        }
      ]
    },
    {
      "categorie": "MAJORATION / SUPPLÉMENT",
      "items": [
        {
          "designation": "Intervention urgence (soir, week-end, jour férié)",
          "unite": "forfait",
          "prix_mo_ht": {"note": "Majoration +50% à +100% sur le taux horaire"},
          "note": "Légal mais doit être annoncé avant intervention. Certains plombiers facturent un forfait nuit fixe (80-150€)."
        },
        {
          "designation": "Frais de déplacement",
          "unite": "forfait",
          "prix_mo_ht": {"min": 20, "max": 60, "moyen": 35},
          "note": "Variable selon distance. Souvent offert si chantier important. Facturable si > 15km."
        }
      ]
    }
  ]
}
```

### data/prix/electricite.json
Même structure — postes typiques :
- Pose tableau électrique (remplacement)
- Ajout circuit (disjoncteur + câblage + prises)
- Pose prise électrique
- Pose interrupteur (simple, double, va-et-vient)
- Pose point lumineux (plafonnier, spot encastré)
- Pose VMC simple flux / double flux
- Mise aux normes NF C 15-100
- Pose radiateur électrique
- Tirage de câble (par ml)
- Pose prise RJ45 / TV
- Pose sonnette / interphone / visiophone

### data/prix/peinture.json
- Préparation murs (rebouchage, ponçage, sous-couche) — par m²
- Peinture murs (2 couches) — par m²
- Peinture plafond (2 couches) — par m²
- Peinture boiseries (portes, fenêtres, plinthes) — par ml ou unité
- Enduit de lissage — par m²
- Pose papier peint — par m²
- Pose toile de verre — par m²
- Dépose papier peint / crépi — par m²
- Peinture extérieure / ravalement — par m²

### data/prix/carrelage.json
- Pose carrelage sol (avec colle, joints) — par m²
- Pose faïence murale — par m²
- Pose mosaïque — par m²
- Pose plinthes carrelage — par ml
- Dépose ancien carrelage — par m²
- Ragréage / chape — par m²
- Pose seuil / barre de seuil — par u
- Pose receveur à carreler — par u

### data/prix/maconnerie.json
- Ouverture dans mur porteur (avec IPN) — par u
- Montage cloison placo (fourniture + pose) — par m²
- Montage cloison béton cellulaire — par m²
- Création saignée (électricité/plomberie) — par ml
- Pose de linteau — par u
- Coulage dalle béton — par m²
- Montage mur parpaing — par m²
- Enduit extérieur — par m²
- Démolition / dépose — par m² ou m³

### data/prix/menuiserie.json
- Pose fenêtre PVC (fourniture + pose) — par u
- Pose porte intérieure (bloc porte) — par u
- Pose porte blindée — par u
- Pose volets roulants — par u
- Pose cuisine (meubles + plan de travail) — forfait ou par ml
- Pose parquet flottant — par m²
- Pose parquet massif — par m²
- Pose escalier — par u
- Pose terrasse bois — par m²

### data/prix/couverture.json
- Réfection toiture tuiles — par m²
- Réfection toiture ardoise — par m²
- Pose gouttière — par ml
- Pose fenêtre de toit (Velux) — par u
- Étanchéité toiture terrasse — par m²
- Pose isolation sous toiture — par m²
- Démoussage / nettoyage toiture — par m²
- Zinguerie (noue, faîtage, solin) — par ml

### data/prix/chauffage_climatisation.json
- Pose chaudière gaz condensation — par u
- Pose pompe à chaleur air/eau — par u
- Pose pompe à chaleur air/air (climatisation réversible) — par u
- Pose plancher chauffant — par m²
- Pose radiateur eau chaude — par u
- Pose sèche-serviette — par u
- Pose poêle à bois/granulés — par u
- Entretien chaudière annuel — par u

### data/prix/isolation.json
- Isolation murs intérieurs (laine de verre + placo) — par m²
- Isolation murs extérieurs (ITE) — par m²
- Isolation combles perdus (soufflage) — par m²
- Isolation combles aménagés — par m²
- Isolation plancher bas — par m²

### data/prix/coefficients_regionaux.json
Coefficients multiplicateurs par région :
```json
{
  "ile_de_france": 1.25,
  "paca": 1.15,
  "auvergne_rhone_alpes": 1.05,
  "occitanie": 1.00,
  "nouvelle_aquitaine": 0.98,
  "bretagne": 0.95,
  "hauts_de_france": 0.95,
  "grand_est": 0.95,
  "normandie": 0.95,
  "pays_de_la_loire": 0.98,
  "bourgogne_franche_comte": 0.95,
  "centre_val_de_loire": 0.95,
  "corse": 1.20
}
```

### data/prix/taux_horaire_mo.json
Taux horaire moyen main d'oeuvre par corps de métier :
```json
{
  "plombier": {"min": 40, "moyen": 55, "max": 75, "unite": "€HT/h"},
  "electricien": {"min": 38, "moyen": 50, "max": 70},
  "peintre": {"min": 30, "moyen": 40, "max": 55},
  "carreleur": {"min": 35, "moyen": 50, "max": 65},
  "macon": {"min": 40, "moyen": 55, "max": 75},
  "menuisier": {"min": 40, "moyen": 55, "max": 75},
  "couvreur": {"min": 45, "moyen": 60, "max": 80},
  "chauffagiste": {"min": 45, "moyen": 60, "max": 80},
  "plaquiste": {"min": 30, "moyen": 42, "max": 55},
  "serrurier": {"min": 45, "moyen": 65, "max": 90}
}
```

---

## 3. TEMPLATES DE DEVIS PAR CHANTIER TYPE

### data/templates/sdb_complete.json
Salle de bain complète — postes standards :
1. Dépose sanitaires existants (baignoire/douche, lavabo, WC)
2. Évacuation gravats
3. Plomberie (modification alimentation/évacuation)
4. Électricité (mise aux normes, prises, éclairage)
5. Ragréage sol
6. Pose receveur douche + paroi
7. Pose carrelage sol
8. Pose faïence murale
9. Pose meuble vasque + robinetterie
10. Pose WC
11. Pose sèche-serviette
12. Joints silicone
13. Nettoyage fin de chantier

### data/templates/sdb_renovation_legere.json
Remplacement équipements sans toucher au carrelage

### data/templates/cuisine_complete.json
### data/templates/renovation_appartement.json
### data/templates/renovation_salle_eau.json
### data/templates/creation_sdb_dans_chambre.json
### data/templates/mise_aux_normes_electrique.json
### data/templates/remplacement_tableau_electrique.json
### data/templates/peinture_appartement.json
### data/templates/peinture_maison.json
### data/templates/pose_parquet.json
### data/templates/ouverture_mur_porteur.json
### data/templates/isolation_combles.json
### data/templates/remplacement_chaudiere.json
### data/templates/pose_pompe_chaleur.json
### data/templates/renovation_toiture.json
### data/templates/pose_fenetre.json
### data/templates/terrasse_bois.json
### data/templates/cloison_placo.json

Chaque template contient :
- Liste des postes avec désignation, unité, quantité type, prix indicatif
- TVA applicable par poste (5.5, 10, ou 20%)
- Durée estimée totale
- Points d'attention / oublis fréquents
- Options courantes (ex: "Option sèche-serviette 750W")

---

## 4. COMPTABILITÉ & ADMIN

### data/compta/categories_depenses.json
Catégories pour la classification automatique des tickets de caisse :
```json
[
  {"code": "MAT", "label": "Matériaux", "exemples": ["carrelage", "placo", "câble", "tuyau", "colle", "joint"]},
  {"code": "OUT", "label": "Outillage", "exemples": ["perceuse", "disqueuse", "niveau", "mètre"]},
  {"code": "EPI", "label": "Équipements de protection", "exemples": ["gants", "lunettes", "casque", "chaussures"]},
  {"code": "CAR", "label": "Carburant", "exemples": ["diesel", "essence", "gazole"]},
  {"code": "DEP", "label": "Frais de déplacement", "exemples": ["péage", "parking", "ferry"]},
  {"code": "SOU", "label": "Sous-traitance", "exemples": ["facture sous-traitant"]},
  {"code": "LOC", "label": "Location matériel", "exemples": ["nacelle", "benne", "échafaudage", "mini-pelle"]},
  {"code": "FOU", "label": "Fournitures bureau", "exemples": ["papier", "encre", "classeur"]},
  {"code": "ASS", "label": "Assurances", "exemples": ["décennale", "RC pro", "véhicule"]},
  {"code": "TEL", "label": "Téléphone / Internet", "exemples": ["abonnement", "forfait"]},
  {"code": "DIV", "label": "Divers", "exemples": []}
]
```

### data/compta/fournisseurs_connus.json
Liste des grossistes BTP courants pour l'OCR :
```json
[
  {"nom": "Point P", "variantes": ["POINT P", "POINT.P", "POINTP", "SAINT-GOBAIN DISTRIBUTION"]},
  {"nom": "Cedeo", "variantes": ["CEDEO", "CEDEO DÉPÔT"]},
  {"nom": "BigMat", "variantes": ["BIG MAT", "BIGMAT"]},
  {"nom": "Brico Dépôt", "variantes": ["BRICO DEPOT", "BRICODEPOT"]},
  {"nom": "Leroy Merlin", "variantes": ["LEROY MERLIN", "LM"]},
  {"nom": "Castorama", "variantes": ["CASTO", "CASTORAMA"]},
  {"nom": "Rexel", "variantes": ["REXEL"]},
  {"nom": "Sonepar", "variantes": ["SONEPAR"]},
  {"nom": "Prolians", "variantes": ["PROLIANS"]},
  {"nom": "Rubix", "variantes": ["RUBIX"]},
  {"nom": "La Plateforme du Bâtiment", "variantes": ["PLATEFORME BATIMENT", "LPB"]},
  {"nom": "Réseau Pro", "variantes": ["RESEAU PRO"]},
  {"nom": "Total / Station", "variantes": ["TOTAL", "TOTALENERGIES", "STATION"]},
  {"nom": "BP", "variantes": ["BP STATION"]},
  {"nom": "Esso", "variantes": ["ESSO"]}
]
```

---

## 5. PROSPECTION & CRM

### data/prospection/types_contacts_pro.json
```json
[
  {"type": "architecte", "label": "Architecte", "frequence_relance_jours": 45},
  {"type": "maitre_oeuvre", "label": "Maître d'oeuvre", "frequence_relance_jours": 45},
  {"type": "promoteur", "label": "Promoteur immobilier", "frequence_relance_jours": 60},
  {"type": "agence_immo", "label": "Agence immobilière", "frequence_relance_jours": 30},
  {"type": "syndic", "label": "Syndic de copropriété", "frequence_relance_jours": 60},
  {"type": "gestionnaire_biens", "label": "Gestionnaire de biens", "frequence_relance_jours": 30},
  {"type": "autre_artisan", "label": "Autre artisan (apporteur)", "frequence_relance_jours": 30},
  {"type": "fournisseur", "label": "Fournisseur", "frequence_relance_jours": 90},
  {"type": "comptable", "label": "Comptable", "frequence_relance_jours": 90}
]
```

### data/prospection/templates_relance.json
Templates de messages de relance par situation :
- Relance devis sans réponse (J+3, J+7, J+15)
- Relance facture impayée (J+15, J+30, J+45)
- Relance architecte (maintien de contact)
- Relance ancien client (proposition entretien/travaux complémentaires)
- Demande d'avis Google (post-chantier)

---

## 6. RÉPUTATION & AVIS GOOGLE

### data/reputation/templates_reponse_avis.json
Templates de réponses aux avis Google par note :
- 5★ : remerciement chaleureux + mention du type de travaux
- 4★ : remerciement + empathie sur le point négatif
- 3★ : empathie + proposition de résolution
- 2★ : excuses + contact direct proposé
- 1★ : ton professionnel + demande de contact privé

### data/reputation/mots_cles_seo_local.json
Mots-clés SEO à intégrer dans les réponses aux avis :
```json
{
  "plombier": ["plombier {ville}", "dépannage plomberie {ville}", "salle de bain {ville}"],
  "electricien": ["électricien {ville}", "mise aux normes {ville}", "tableau électrique {ville}"],
  "peintre": ["peintre {ville}", "peinture appartement {ville}", "ravalement {ville}"],
  "carreleur": ["carreleur {ville}", "pose carrelage {ville}", "faïence {ville}"],
  "general": ["artisan {ville}", "travaux {ville}", "rénovation {ville}", "devis gratuit {ville}"]
}
```

---

## 7. DONNÉES FISCALITÉ & GESTION D'ENTREPRISE

### Fichiers data fiscalité

| Fichier | Contenu | Priorité |
|---------|---------|----------|
| data/fiscalite/micro_entreprise.json | Seuils CA 2026 (203 100€/83 600€), taux cotisations (12.3%/21.2%), franchise TVA, ACRE 50%→25%, versement libératoire, CFP, TFCC | Sprint 6 |
| data/fiscalite/eurl.json | Charges déductibles, cotisations TNS (~45% bénéfice), IS 15%/25%, TVA collectée/déductible, bilan annuel | Sprint 6 |
| data/fiscalite/sas_sasu.json | Président assimilé salarié, cotisations ~65% salaire brut, dividendes flat tax 30%, AG | Sprint 6 |
| data/fiscalite/ei.json | Régime réel simplifié/normal, charges déductibles, cotisations TNS | Sprint 6 |
| data/fiscalite/calendrier_fiscal.json | Toutes les échéances par statut : URSSAF (mensuel/trimestriel), TVA, CFE, IS, bilan, déclaration revenus | Sprint 6 |
| data/fiscalite/tva_regimes.json | Franchise base, réel simplifié, réel normal, seuils, taux, conditions | Sprint 6 |

### Fichiers data RH / conventions collectives BTP

| Fichier | Contenu | Priorité |
|---------|---------|----------|
| data/rh/conventions_btp.json | 6 IDCC (1596, 1597, 2609, 2420, 1702, 2614), champ d'application, classifications | Sprint 7 |
| data/rh/grilles_salaires_btp_2026.json | Grilles salariales ouvriers (N1P1→N4P2, coefficients 150→270) par région | Sprint 7 |
| data/rh/indemnites_btp_2026.json | Barèmes indemnités trajet, transport, panier repas par zone (1A→5) et par région | Sprint 7 |
| data/rh/cotisations_specifiques_btp.json | CIBTP (20.70%), OPPBTP (0.11%), PRO BTP (prévoyance), CCCA-BTP, DFS (7% en 2026) | Sprint 7 |
| data/rh/conges_btp.json | Période référence (1er avril → 31 mars), prime vacances 30%, congés intempéries | Sprint 7 |

### Fichiers data déplacements

| Fichier | Contenu | Priorité |
|---------|---------|----------|
| data/deplacements/bareme_kilometrique_2026.json | Barème fiscal km par puissance CV (3CV→7CV+), seuils km | Sprint 6 |
| data/deplacements/zones_btp.json | 5 zones concentriques (0-5km → 40-50km), plafonds URSSAF exonération | Sprint 6 |
| data/deplacements/paniers_repas_regions.json | Montant panier repas par région BTP (10.50€ Normandie → 14€ Occitanie) | Sprint 6 |

### Fichiers data réseaux sociaux

| Fichier | Contenu | Priorité |
|---------|---------|----------|
| data/social/templates_publications.json | Templates de texte pour publications avant/après par réseau (Facebook, Instagram, Google Business) avec hashtags métier | Sprint 7 |

---

## 8. GAMIFICATION & ONBOARDING

### data/gamification/quests.json
Les 7 quêtes d'onboarding avec XP et conditions de complétion

### data/gamification/badges.json
Les 8+ badges avec conditions de déverrouillage

### data/gamification/levels.json
Les 5 niveaux avec seuils XP

---

## 9. I18N — TERMES MÉTIER PAR LANGUE

### data/i18n/metier_terms.json
Termes métier BTP traduits dans les 6 langues (FR, EN, TR, ES, PT, AR) :
- Corps de métier (plombier, électricien, peintre...)
- Matériaux courants
- Unités de mesure (m², ml, u, forfait)
- Types de travaux

---

## RÉSUMÉ — NOMBRE DE FICHIERS DATA

| Catégorie | Nombre de fichiers | Priorité |
|-----------|-------------------|----------|
| Réglementation & mentions légales | 5 | 🔴 CRITIQUE (sans ça, devis illégaux) |
| Prix du marché (10 corps de métier + coefficients + taux horaire) | 13 | 🔴 CRITIQUE (sans ça, devis avec prix inventés) |
| Templates devis (20 chantiers types) | 20 | 🟡 HAUTE (sans ça, devis incomplets) |
| Comptabilité (catégories + fournisseurs) | 2 | 🟡 HAUTE (sans ça, OCR mal classé) |
| Prospection (contacts + relances) | 2 | 🟢 MOYENNE |
| Réputation (avis + SEO) | 2 | 🟢 MOYENNE |
| Fiscalité (statuts, calendrier, TVA) | 6 | 🟡 HAUTE (sans ça, alertes fiscales impossibles) |
| RH / conventions BTP (grilles, indemnités, cotisations) | 5 | 🟡 HAUTE (sans ça, calculs paie faux) |
| Déplacements (barème km, zones, paniers) | 3 | 🟡 HAUTE (sans ça, frais non suivis) |
| Réseaux sociaux (templates publications) | 1 | 🟢 MOYENNE |
| Gamification | 3 | 🟢 MOYENNE |
| i18n termes métier | 1 | 🟢 MOYENNE |
| **TOTAL** | **~63 fichiers** | |

**Les 18 fichiers 🔴 CRITIQUES doivent être prêts AVANT le Sprint 3 (Agent Devis).**
Les autres peuvent être ajoutés progressivement.

---

> **Note pour Fabrice :** Les prix dans ces fichiers sont des MOYENNES marché.
> Chaque artisan qui utilise STRUCTORAI les remplacera par SES propres prix via Mem0.
> Mais pour le premier devis (avant que le cerveau ne connaisse l'artisan), il faut des prix de base réalistes.
> Fabrice : valide les fourchettes de prix et corrige ce qui te semble faux.
