# STRUCTORAI — RÉFÉRENTIEL TECHNIQUE COUVERTURE (Couvreur / Zingueur)
> Données techniques exhaustives pour le cerveau IA.
> Tuiles, ardoises, zinc, zinguerie, charpente légère, étanchéité, velux.
> Ce fichier alimente le RAG et permet au cerveau de comprendre le langage artisan.
> Date : 14/04/2026 — Prix vérifiés par recherche web (sources : Travaux.com, prix-pose.com, Habitatpresto, guide-toiture.com, Ootravaux, allotravaux.com, FNFE)

---

## 1. TYPES DE COUVERTURE — MATÉRIAUX ET FOURNITURE

### TUILES TERRE CUITE (70% du marché français)
```json
{
  "materiau": "Tuile terre cuite",
  "types": [
    {
      "nom": "Tuile romane (mécanique à emboîtement)",
      "usage": "Couverture principale — toute la France sauf nord/ouest ardoisier",
      "pente_min": "25°",
      "nb_par_m2": "10 à 15",
      "duree_vie": "50-80 ans",
      "poids_m2": "40-45 kg/m²",
      "prix_fourniture_m2": {"min": 10, "max": 30},
      "prix_pose_m2": {"min": 22, "max": 40},
      "prix_total_m2": {"min": 38, "max": 65},
      "marques": ["Monier", "Terreal", "Imerys", "Wienerberger", "Edilians"],
      "note": "Tuile la plus posée en France. Emboîtement facile, rapide. Grand choix de coloris régionaux."
    },
    {
      "nom": "Tuile canal (traditionnelle sud)",
      "usage": "Sud de la France, faibles pentes",
      "pente_min": "15° (avec écran sous-toiture)",
      "nb_par_m2": "20 à 25 (courant + couvrant)",
      "duree_vie": "50-100 ans",
      "poids_m2": "55-65 kg/m²",
      "prix_fourniture_m2": {"min": 25, "max": 45},
      "prix_pose_m2": {"min": 35, "max": 55},
      "prix_total_m2": {"min": 65, "max": 95},
      "marques": ["Imerys", "Terreal", "Monier"],
      "note": "Pose plus longue (2 tuiles par rang : courant + couvrant). Esthétique traditionnelle méditerranéenne."
    },
    {
      "nom": "Tuile plate",
      "usage": "Nord, Île-de-France, Bourgogne, patrimoine",
      "pente_min": "40° (forte pente obligatoire)",
      "nb_par_m2": "60 à 75",
      "duree_vie": "80-120 ans",
      "poids_m2": "55-75 kg/m²",
      "prix_fourniture_m2": {"min": 30, "max": 55},
      "prix_pose_m2": {"min": 45, "max": 65},
      "prix_total_m2": {"min": 90, "max": 160},
      "marques": ["Imerys", "Terreal", "Edilians"],
      "note": "Très grand nombre de tuiles au m² = pose longue et coûteuse. Aspect patrimonial, souvent imposé en zone ABF."
    },
    {
      "nom": "Tuile à emboîtement grand moule (type Marseillaise, H10, HP10)",
      "usage": "Généraliste, rénovation économique",
      "pente_min": "22°",
      "nb_par_m2": "10 à 12",
      "duree_vie": "40-60 ans",
      "poids_m2": "38-42 kg/m²",
      "prix_fourniture_m2": {"min": 8, "max": 20},
      "prix_pose_m2": {"min": 15, "max": 30},
      "prix_total_m2": {"min": 35, "max": 55},
      "note": "La moins chère. Emboîtement rapide, peu de tuiles au m². Idéale petit budget."
    }
  ],
  "fournisseurs": ["Point P", "BigMat", "Samse", "Gedimat", "Larivière"],
  "normes": "NF EN 1304 (tuiles terre cuite), DTU 40.21 (tuiles canal), DTU 40.211 (tuiles plates), DTU 40.22 (tuiles à emboîtement)"
}
```

### TUILES BÉTON
```json
{
  "materiau": "Tuile béton",
  "usage": "Alternative économique à la terre cuite",
  "pente_min": "20°",
  "nb_par_m2": "10 à 12",
  "duree_vie": "30-40 ans (se décolore, mousse)",
  "poids_m2": "42-50 kg/m²",
  "prix_fourniture_m2": {"min": 8, "max": 18},
  "prix_pose_m2": {"min": 15, "max": 25},
  "prix_total_m2": {"min": 35, "max": 50},
  "marques": ["Monier (Redland)", "Braas"],
  "avantages": ["Prix bas", "Résistante au gel", "Grand choix de coloris"],
  "inconvenients": ["Durée de vie limitée", "Mousse rapidement", "Plus lourde", "Se décolore (5-10 ans)"],
  "note_ia": "DÉCONSEILLER pour résidence principale si budget permet la terre cuite. Proposer uniquement pour annexes, garages, ou budget très serré."
}
```

### ARDOISE NATURELLE
```json
{
  "materiau": "Ardoise naturelle",
  "usage": "Bretagne, Normandie, Pays de Loire, Ardennes, patrimoine, zone ABF",
  "pente_min": "30°",
  "nb_par_m2": "30 à 50 (selon format)",
  "duree_vie": "80-120 ans (Angers) / 50-80 ans (Espagne)",
  "poids_m2": "25-35 kg/m²",
  "origines": {
    "angers_france": {"prix_m2": {"min": 50, "max": 80}, "qualite": "Premium, la meilleure", "duree": "100+ ans"},
    "espagne": {"prix_m2": {"min": 30, "max": 50}, "qualite": "Standard à bonne", "duree": "50-80 ans"},
    "bresil_chine": {"prix_m2": {"min": 20, "max": 35}, "qualite": "Variable, attention pyrite", "duree": "30-50 ans"}
  },
  "pose_crochet": {
    "description": "Moderne, rapide. Crochets inox ou galvanisés",
    "prix_pose_m2": {"min": 40, "max": 75},
    "prix_total_m2": {"min": 80, "max": 120}
  },
  "pose_clouee": {
    "description": "Traditionnelle, noble. Ardoises percées et clouées sur voligeage",
    "prix_pose_m2": {"min": 55, "max": 90},
    "prix_total_m2": {"min": 110, "max": 180}
  },
  "fournisseurs": ["Cupa Pizarras (Espagne)", "Ardoisières d'Angers", "Larivière", "Point P"],
  "normes": "NF EN 12326, DTU 40.11 (ardoises naturelles)",
  "note_ia": "Tous les couvreurs ne posent pas l'ardoise. Vérifier que l'artisan est couvreur-ardoisier. En zone ABF, l'ardoise naturelle peut être imposée par le PLU."
}
```

### ARDOISE SYNTHÉTIQUE (fibrociment)
```json
{
  "materiau": "Ardoise fibrociment",
  "usage": "Alternative économique à l'ardoise naturelle, annexes, rénovation budget",
  "duree_vie": "30-50 ans",
  "poids_m2": "15-20 kg/m² (léger)",
  "prix_fourniture_m2": {"min": 15, "max": 30},
  "prix_pose_m2": {"min": 25, "max": 45},
  "prix_total_m2": {"min": 60, "max": 95},
  "marques": ["Eternit (Cedral)", "Edilfibro"],
  "note_ia": "Interdite dans certaines zones patrimoniales/ABF. Aspect moins noble que le naturel. Bonne option pour dépendances."
}
```

### ZINC (couverture)
```json
{
  "materiau": "Zinc (toiture)",
  "usage": "Toits plats, faibles pentes, Paris/Haussmann, architecture contemporaine",
  "pente_min": "3° (joint debout) / 5° (tasseaux)",
  "duree_vie": "50-80 ans",
  "poids_m2": "5-7 kg/m² (très léger)",
  "epaisseurs": ["0.65mm (standard)", "0.70mm", "0.80mm (haut de gamme)"],
  "techniques": {
    "joint_debout": {"prix_total_m2": {"min": 100, "max": 160}, "note": "Technique la plus noble et étanche"},
    "tasseaux": {"prix_total_m2": {"min": 90, "max": 140}},
    "agrafage": {"prix_total_m2": {"min": 80, "max": 130}}
  },
  "marques": ["VM Zinc (Umicore)", "Rheinzink", "Elzinc"],
  "fournisseurs": ["Larivière", "Point P", "Descours & Cabaud"],
  "normes": "DTU 40.41 (couvertures zinc), NF EN 988",
  "note_ia": "Nécessite un couvreur-zingueur qualifié. Le zinc se dilate (5mm/m sur 10m pour 50°C d'écart) → joints de dilatation obligatoires. Pose sur voligeage ventilé."
}
```

### BAC ACIER
```json
{
  "materiau": "Bac acier (tôle profilée)",
  "usage": "Hangar, garage, extension, bâtiment agricole/industriel, maison contemporaine",
  "pente_min": "5° (simple peau) / 3° (double peau isolé)",
  "duree_vie": "30-50 ans",
  "poids_m2": "5-8 kg/m²",
  "types": {
    "simple_peau": {"prix_total_m2": {"min": 25, "max": 50}},
    "double_peau_isole": {"prix_total_m2": {"min": 50, "max": 90}},
    "imitation_tuile": {"prix_total_m2": {"min": 30, "max": 60}}
  },
  "marques": ["ArcelorMittal", "Haironville", "Joris Ide"],
  "normes": "DTU 40.35",
  "note_ia": "Très mauvaise isolation phonique (pluie = bruit). Condensation si pas de ventilation. Préconiser double peau isolé pour habitation."
}
```

### SHINGLE (bardeau bitumé)
```json
{
  "materiau": "Shingle",
  "usage": "Abri de jardin, garage, annexe, toiture légère",
  "pente_min": "20°",
  "duree_vie": "15-25 ans",
  "poids_m2": "8-15 kg/m²",
  "prix_fourniture_m2": {"min": 7, "max": 15},
  "prix_pose_m2": {"min": 15, "max": 25},
  "prix_total_m2": {"min": 25, "max": 40},
  "note_ia": "NE JAMAIS proposer pour une résidence principale. Réservé aux petites surfaces et annexes."
}
```

---

## 2. ÉLÉMENTS DE CHARPENTE — PRIX FOURNITURE

```json
{
  "litonnage": {
    "liteau_sapin_traite_25x38": {"prix_ml": {"min": 0.80, "max": 1.50}},
    "liteau_sapin_traite_27x40": {"prix_ml": {"min": 1.00, "max": 1.80}},
    "contre_liteau_30x30": {"prix_ml": {"min": 0.80, "max": 1.30}},
    "volige_sapin_18mm": {"prix_m2": {"min": 8, "max": 14}},
    "volige_sapin_22mm": {"prix_m2": {"min": 10, "max": 16}},
    "panneau_OSB_12mm": {"prix_m2": {"min": 7, "max": 12}},
    "panneau_OSB_18mm": {"prix_m2": {"min": 10, "max": 16}}
  },
  "ecran_sous_toiture": {
    "HPV_synthetique": {"prix_m2": {"min": 2, "max": 5}, "note": "Haute Perméabilité Vapeur — standard actuel, obligatoire DTU"},
    "bitume_ancien": {"prix_m2": {"min": 1.50, "max": 3}, "note": "Ancien type, non HPV — à remplacer en rénovation"},
    "ecran_rigide_fibre_bois": {"prix_m2": {"min": 8, "max": 15}, "note": "Isolation + pare-pluie combinés"}
  },
  "fixation": {
    "pointes_torsadees_70mm_1kg": {"prix": {"min": 5, "max": 9}},
    "vis_terrasse_inox_5x60_boite200": {"prix": {"min": 20, "max": 35}},
    "crochets_ardoise_inox_boite1000": {"prix": {"min": 25, "max": 45}},
    "crochets_tuile_galva_boite100": {"prix": {"min": 15, "max": 25}}
  },
  "note_ia": "En rénovation, TOUJOURS vérifier l'état du litonnage et du voligeage. Si bois pourri → remplacement obligatoire avant repose couverture. Coût supplémentaire 15-30€/m² à budgéter."
}
```

---

## 3. ZINGUERIE — GOUTTIÈRES, DESCENTES, ACCESSOIRES

### Gouttières — Prix fourniture + pose
```json
{
  "gouttieres": [
    {"materiau": "PVC demi-ronde", "fourniture_ml": {"min": 3, "max": 8}, "pose_ml": {"min": 25, "max": 40}, "total_ml": {"min": 27, "max": 45}, "duree_vie": "15-20 ans"},
    {"materiau": "Zinc demi-ronde", "fourniture_ml": {"min": 10, "max": 25}, "pose_ml": {"min": 30, "max": 55}, "total_ml": {"min": 40, "max": 80}, "duree_vie": "30-50 ans"},
    {"materiau": "Zinc nantaise/havraise (rampante)", "fourniture_ml": {"min": 15, "max": 35}, "pose_ml": {"min": 40, "max": 65}, "total_ml": {"min": 55, "max": 100}, "duree_vie": "40-50 ans"},
    {"materiau": "Aluminium laqué", "fourniture_ml": {"min": 8, "max": 20}, "pose_ml": {"min": 30, "max": 50}, "total_ml": {"min": 38, "max": 70}, "duree_vie": "25-35 ans"},
    {"materiau": "Cuivre", "fourniture_ml": {"min": 30, "max": 60}, "pose_ml": {"min": 60, "max": 120}, "total_ml": {"min": 100, "max": 200}, "duree_vie": "50-80 ans"}
  ],
  "descentes_eaux_pluviales": [
    {"materiau": "PVC Ø80", "total_ml": {"min": 12, "max": 25}},
    {"materiau": "PVC Ø100", "total_ml": {"min": 15, "max": 30}},
    {"materiau": "Zinc Ø80", "total_ml": {"min": 25, "max": 50}},
    {"materiau": "Zinc Ø100", "total_ml": {"min": 30, "max": 60}},
    {"materiau": "Aluminium", "total_ml": {"min": 20, "max": 40}},
    {"materiau": "Cuivre Ø80", "total_ml": {"min": 50, "max": 100}}
  ],
  "accessoires": {
    "naissance_gouttiere_zinc": {"prix": {"min": 8, "max": 18}},
    "fond_gouttiere_zinc": {"prix": {"min": 5, "max": 12}},
    "coude_descente_zinc": {"prix": {"min": 6, "max": 15}},
    "dauphin_fonte": {"prix": {"min": 15, "max": 35}},
    "crapaudine_pare_feuilles": {"prix": {"min": 3, "max": 8}},
    "grille_pare_feuilles_ml": {"prix": {"min": 5, "max": 12}},
    "collier_descente_zinc": {"prix": {"min": 3, "max": 7}},
    "crochet_bandeau_zinc": {"prix": {"min": 2, "max": 5}}
  },
  "depose_ancienne_gouttiere_ml": {"min": 6, "max": 12}
}
```

### Éléments d'étanchéité toiture
```json
{
  "elements_etancheite": {
    "solin_zinc_developpe_200": {"prix_ml": {"min": 20, "max": 40}, "note": "Raccord toiture/mur ou cheminée"},
    "solin_plomb_developpé_300": {"prix_ml": {"min": 25, "max": 50}},
    "bande_de_rive_zinc_ml": {"prix_ml": {"min": 12, "max": 25}},
    "noue_zinc_developpee_500": {"prix_ml": {"min": 35, "max": 65}},
    "abergement_cheminee_zinc_forfait": {"prix": {"min": 250, "max": 600}, "note": "Habillage complet 4 faces cheminée"},
    "chatiere_ventilation_tuile": {"prix_unitaire": {"min": 5, "max": 15}, "densite": "1 pour 20m²"},
    "faitage_tuile_scelle": {"prix_ml": {"min": 20, "max": 40}},
    "faitage_tuile_a_sec_closoir_ventile": {"prix_ml": {"min": 25, "max": 50}},
    "rive_tuile_scellement": {"prix_ml": {"min": 15, "max": 30}},
    "rive_tuile_a_sec": {"prix_ml": {"min": 18, "max": 35}}
  }
}
```

---

## 4. FENÊTRES DE TOIT (VELUX et équivalents)

```json
{
  "fenetres_de_toit": [
    {"taille": "78×98 cm (CK04)", "type": "Rotation (GGL)", "vitrage": "Double", "prix_fourniture": {"min": 350, "max": 550}, "prix_pose": {"min": 250, "max": 450}, "total": {"min": 600, "max": 1000}},
    {"taille": "78×118 cm (MK06)", "type": "Rotation (GGL)", "vitrage": "Double", "prix_fourniture": {"min": 400, "max": 650}, "prix_pose": {"min": 250, "max": 450}, "total": {"min": 650, "max": 1100}},
    {"taille": "78×118 cm (MK06)", "type": "Projection (GPL)", "vitrage": "Double", "prix_fourniture": {"min": 550, "max": 800}, "prix_pose": {"min": 280, "max": 500}, "total": {"min": 830, "max": 1300}},
    {"taille": "114×118 cm (SK06)", "type": "Rotation (GGL)", "vitrage": "Double", "prix_fourniture": {"min": 550, "max": 850}, "prix_pose": {"min": 300, "max": 550}, "total": {"min": 850, "max": 1400}},
    {"taille": "78×118 cm", "type": "Motorisé solaire (GGU)", "vitrage": "Triple", "prix_fourniture": {"min": 800, "max": 1200}, "prix_pose": {"min": 300, "max": 550}, "total": {"min": 1100, "max": 1750}}
  ],
  "raccord_etancheite_velux": {"prix": {"min": 50, "max": 120}, "note": "Kit raccord obligatoire, adapté au type de couverture (tuile plate, profil, ardoise)"},
  "volet_roulant_exterieur": {"manuel": {"min": 200, "max": 350}, "solaire": {"min": 300, "max": 500}},
  "store_interieur_occultant": {"prix": {"min": 60, "max": 150}},
  "marques": ["Velux", "Roto", "Fakro"],
  "normes": "DTU 40 (selon couverture), NF EN 14351",
  "note_ia": "Création d'un Velux dans une toiture existante = modification charpente (chevêtre) + étanchéité + raccord + habillage intérieur. Budget complet 1 200-2 500€ par fenêtre. Permis de construire ou déclaration préalable nécessaire."
}
```

---

## 5. OUTILLAGE COUVREUR — INVENTAIRE

```json
{
  "outillage_couvreur": [
    {"outil": "Marteau de couvreur (panne pointue)", "prix": {"min": 20, "max": 50}},
    {"outil": "Enclume de couvreur (bigorne)", "prix": {"min": 60, "max": 150}},
    {"outil": "Cisaille à zinc droite", "prix": {"min": 25, "max": 60}},
    {"outil": "Cisaille à zinc gauche", "prix": {"min": 25, "max": 60}},
    {"outil": "Pince à sertir les gouttières", "prix": {"min": 30, "max": 70}},
    {"outil": "Plieuse à zinc manuelle (longueur 2m)", "prix": {"min": 300, "max": 700}},
    {"outil": "Chalumeau propane (soudure zinc)", "prix": {"min": 40, "max": 100}},
    {"outil": "Baguettes de soudure zinc", "prix_kg": {"min": 15, "max": 30}},
    {"outil": "Meuleuse avec disque diamant", "prix": {"min": 60, "max": 150}},
    {"outil": "Grignoteuse (découpe tôle)", "prix": {"min": 150, "max": 350}},
    {"outil": "Cloueur pneumatique (ardoise)", "prix": {"min": 200, "max": 500}},
    {"outil": "Tire-clou (arrache-clou couvreur)", "prix": {"min": 10, "max": 25}},
    {"outil": "Corde de couvreur (20m)", "prix": {"min": 15, "max": 30}},
    {"outil": "Harnais antichute complet", "prix": {"min": 80, "max": 200}},
    {"outil": "Ligne de vie temporaire (sangle)", "prix": {"min": 50, "max": 120}},
    {"outil": "Échelle de toit (crochet faîtage)", "prix": {"min": 100, "max": 300}},
    {"outil": "Crochet de sécurité toiture (ancrage)", "prix": {"min": 30, "max": 80}}
  ]
}
```

---

## 6. MISES EN OEUVRE — TOUTES LES INTERVENTIONS COUVERTURE AVEC PRIX

### 6.1 Réfection couverture complète (dépose + repose)

| Intervention | Unité | Prix MO+fourniture HT | Note |
|-------------|-------|----------------------|------|
| Réfection complète tuiles romanes (dépose + écran HPV + litonnage + repose tuiles neuves) | m² | 70-120€ | Inclut dépose, tri déchets, écran sous-toiture |
| Réfection complète tuiles plates terre cuite | m² | 120-200€ | Plus de tuiles au m², pose plus longue |
| Réfection complète tuiles canal traditionnelles | m² | 90-150€ | |
| Réfection complète ardoise naturelle (Espagne, pose crochet) | m² | 110-170€ | Couvreur ardoisier obligatoire |
| Réfection complète ardoise naturelle (Angers, pose clouée) | m² | 160-250€ | Haut de gamme |
| Réfection complète ardoise synthétique | m² | 70-110€ | |
| Réfection complète zinc joint debout | m² | 120-180€ | Zingueur qualifié |
| Réfection complète bac acier simple peau | m² | 35-60€ | |

### 6.2 Réparations et interventions ponctuelles

| Intervention | Unité | Prix MO+fourniture HT | Note |
|-------------|-------|----------------------|------|
| Remplacement tuiles cassées (< 10 tuiles) | u | 10-20€/tuile | + déplacement 80-150€ |
| Remplacement tuiles cassées (> 10 tuiles) | m² | 30-60€ | |
| Remplacement ardoises cassées (< 10) | u | 15-30€/ardoise | |
| Recherche et réparation fuite ponctuelle | forfait | 150-400€ | Diagnostic + réparation |
| Remplacement faîtage scellé (dépose + repose) | ml | 35-65€ | Faîtière + mortier |
| Remplacement faîtage à sec (closoir ventilé) | ml | 40-70€ | Closoir + faîtière + fixation |
| Remplacement rives (scellées ou à sec) | ml | 25-50€ | |
| Reprise solin cheminée zinc | forfait | 200-500€ | Selon complexité |
| Reprise abergement cheminée complet | forfait | 400-900€ | Zinc + plomb + soudure |
| Pose écran sous-toiture (par l'intérieur, rénovation) | m² | 15-30€ | Sans dépose couverture |
| Traitement charpente (injection insecticide/fongicide) | m² | 15-30€ | Surface projetée |

### 6.3 Travaux de zinguerie

| Intervention | Unité | Prix MO+fourniture HT | Note |
|-------------|-------|----------------------|------|
| Remplacement gouttière PVC complète (dépose + pose) | ml | 30-50€ | |
| Remplacement gouttière zinc demi-ronde (dépose + pose) | ml | 50-90€ | Soudure incluse |
| Remplacement gouttière zinc rampante | ml | 65-110€ | |
| Remplacement descente EP PVC | ml | 15-30€ | |
| Remplacement descente EP zinc | ml | 30-60€ | |
| Pose noue zinc (neuf ou remplacement) | ml | 50-90€ | Pièce critique d'étanchéité |
| Réparation soudure gouttière zinc (ponctuelle) | forfait | 80-200€ | |
| Pose bandeau de rive PVC ou bois | ml | 15-30€ | |
| Pose bandeau de rive zinc | ml | 20-40€ | |
| Habillage dessous de toit (lambris PVC) | m² | 30-55€ | |
| Habillage dessous de toit (bois peint) | m² | 40-70€ | |

### 6.4 Fenêtres de toit (Velux)

| Intervention | Unité | Prix MO+fourniture HT | Note |
|-------------|-------|----------------------|------|
| Création Velux neuf (chevêtre + fenêtre + raccord + habillage) | u | 1200-2500€ | Selon taille et motorisation |
| Remplacement Velux existant (même taille) | u | 600-1200€ | Réutilisation chevêtre |
| Remplacement Velux avec agrandissement | u | 1500-3000€ | Modification charpente |
| Pose volet roulant sur Velux existant | u | 350-650€ | Solaire recommandé (pas de câblage) |
| Remplacement vitrage Velux (bris) | u | 200-400€ | |

### 6.5 Isolation toiture (par l'extérieur — sarking)

| Intervention | Unité | Prix MO+fourniture HT | Note |
|-------------|-------|----------------------|------|
| Sarking fibre de bois 100mm (sur chevrons) | m² | 80-130€ | Hors couverture |
| Sarking fibre de bois 140mm | m² | 100-160€ | Performance thermique élevée |
| Sarking polyuréthane 80mm | m² | 60-100€ | Moins écologique, plus fin à performance égale |
| Surélévation toiture pour aménagement combles | m² emprise | 800-1500€ | Gros oeuvre + couverture |

### 6.6 Démoussage et entretien

| Intervention | Unité | Prix MO+fourniture HT | Note |
|-------------|-------|----------------------|------|
| Démoussage toiture (traitement + rinçage) | m² | 10-25€ | Selon état et produit |
| Hydrofuge toiture (imperméabilisant après démoussage) | m² | 8-18€ | Prolonge durée de vie |
| Nettoyage haute pression toiture | m² | 5-12€ | ATTENTION : interdit sur ardoise et tuile fragile ! |
| Nettoyage basse pression + traitement anti-mousse | m² | 12-22€ | Recommandé |
| Peinture toiture (résine colorée) | m² | 25-50€ | Plutôt cosmétique, durée limitée (5-8 ans) |

### 6.7 Forfaits courants par type de maison

| Type de maison | Surface toiture | Budget couverture tuiles | Budget couverture ardoise | Note |
|---------------|----------------|------------------------|--------------------------|------|
| Maison plain-pied 90m² (toiture ~110m²) | 110 m² | 8 000-14 000€ | 13 000-22 000€ | 2 pans simples |
| Maison R+1 100m² (toiture ~80m²) | 80 m² | 6 000-10 000€ | 10 000-17 000€ | Pente plus forte |
| Maison R+1 avec lucarnes | 100 m² | 9 000-16 000€ | 14 000-25 000€ | Complexité raccords |
| Pavillon 4 pans | 120 m² | 10 000-18 000€ | 16 000-28 000€ | 4 noues + faîtage |

---

## 7. TAUX HORAIRE COUVREUR

```json
{
  "taux_horaire_couvreur": {
    "couvreur_ouvrier": {"min": 35, "max": 50, "unite": "€ HT/h"},
    "couvreur_confirme": {"min": 45, "max": 60, "unite": "€ HT/h"},
    "couvreur_zingueur": {"min": 45, "max": 70, "unite": "€ HT/h"},
    "couvreur_ardoisier": {"min": 50, "max": 75, "unite": "€ HT/h"},
    "chef_equipe": {"min": 50, "max": 70, "unite": "€ HT/h"}
  },
  "rendement_pose_par_jour": {
    "tuiles_romanes": "20-30 m²/jour/compagnon",
    "tuiles_plates": "8-15 m²/jour/compagnon",
    "ardoise_crochet": "10-15 m²/jour/compagnon",
    "ardoise_clouee": "6-10 m²/jour/compagnon",
    "zinc_joint_debout": "6-10 m²/jour/compagnon",
    "bac_acier": "30-50 m²/jour/compagnon",
    "gouttiere_zinc": "6-10 ml/jour/compagnon"
  },
  "note": "Un chantier couverture est toujours à 2 compagnons minimum (sécurité). Pour une maison standard, équipe de 2-3 personnes. Durée type réfection complète maison R+1 : 5-10 jours ouvrés."
}
```

---

## 8. RÉGLEMENTATION ET OBLIGATIONS

```json
{
  "reglementation": [
    {"regle": "DTU série 40", "detail": "Les DTU de la série 40 couvrent tous les types de couverture (40.11 ardoise, 40.21 tuile canal, 40.211 tuile plate, 40.22 tuile mécanique, 40.35 bac acier, 40.41 zinc, 40.43 étanchéité...). Respect obligatoire pour la décennale."},
    {"regle": "Écran sous-toiture HPV", "detail": "Obligatoire en construction neuve (DTU). En rénovation, fortement recommandé et exigé par la plupart des assureurs. HPV = Haute Perméabilité Vapeur (laisse passer la vapeur d'eau mais pas la pluie)."},
    {"regle": "Ventilation sous couverture", "detail": "Lame d'air de 2cm minimum entre isolant et couverture. Chatières ou faîtage ventilé. Sans ventilation → condensation → pourrissement charpente."},
    {"regle": "PLU et zone ABF", "detail": "Le Plan Local d'Urbanisme impose le type de couverture, la couleur, la pente. En zone ABF, l'Architecte des Bâtiments de France peut imposer l'ardoise, la tuile plate, ou un coloris précis."},
    {"regle": "Déclaration préalable / permis", "detail": "Changement de matériau ou de couleur → déclaration préalable obligatoire. Modification de pente ou surélévation → permis de construire."},
    {"regle": "Sécurité travaux en hauteur", "detail": "Échafaudage ou nacelle obligatoire. Harnais antichute si pas d'échafaudage. Filet de sécurité pour chutes d'objets. Plan de prévention si co-activité. Intempéries : arrêt travaux si vent > 60 km/h."},
    {"regle": "Garantie décennale", "detail": "Couverture = ouvrage de structure → garantie décennale 10 ans obligatoire. L'artisan DOIT fournir son attestation décennale en cours de validité. Attention : elle doit couvrir l'activité 'couverture' spécifiquement."},
    {"regle": "Évacuation eaux pluviales", "detail": "Code civil art. 681 : interdiction de déverser les eaux de pluie sur le terrain voisin. Gouttières obligatoires si toiture en limite de propriété."},
    {"regle": "Amiante", "detail": "Toute toiture en fibrociment avant 1997 peut contenir de l'amiante. Diagnostic amiante OBLIGATOIRE avant dépose. Si positif → dépose par entreprise certifiée SS3 ou SS4. Coût diagnostic : 150-400€. Coût dépose amiante : +30-60€/m² en sus."}
  ]
}
```

---

## 9. ERREURS COURANTES TERRAIN (pour le cerveau IA — aide au devis)

1. **Pas de diagnostic amiante avant dépose** : Sur toute toiture fibrociment/ardoise synthétique d'avant 1997, le diagnostic amiante est OBLIGATOIRE. Amende pénale si dépose sans diagnostic.
2. **Oubli de l'échafaudage au devis** : L'échafaudage représente 8-15% du budget couverture. L'oublier = marge morte. Location : 8-15€/m² de façade pour 2-4 semaines.
3. **Litonnage pas remplacé en rénovation** : Poser des tuiles neuves sur du vieux litonnage pourri = fuite garantie sous 2-3 ans. TOUJOURS inspecter et remplacer si nécessaire.
4. **Écran sous-toiture oublié** : En rénovation, profiter de la dépose pour poser un écran HPV. Coût marginal (+5-10€/m²) mais protection décisive contre infiltrations et condensation.
5. **Tuile non conforme au PLU** : Vérifier le PLU AVANT de commander les tuiles. Mauvaise couleur ou mauvais type = obligation de refaire aux frais de l'artisan.
6. **Ventilation sous couverture insuffisante** : Pas de chatière ou closoir non ventilé = condensation sous les tuiles → pourrissement liteaux et chevrons en 5-10 ans.
7. **Noue mal réalisée** : La noue (creux entre deux pans) est le point le plus fragile d'une toiture. Noue en zinc trop étroite ou mal soudée = fuite certaine.
8. **Solin cheminée pas refait** : Lors d'une réfection couverture, le solin autour de la cheminée DOIT être refait. Un vieux solin = fuite assurée.
9. **Pas de protection anti-chute** : La couverture est l'un des métiers les plus dangereux du BTP. Chute de hauteur = 1ère cause de décès sur chantier. Échafaudage + harnais + filet = non négociable.
10. **Sous-estimation du temps sur tuile plate/ardoise** : La tuile plate (65-75 pièces/m²) et l'ardoise (30-50 pièces/m²) prennent 2-3× plus de temps que la tuile romane. Ne pas appliquer le même rendement.
11. **Gouttière PVC proposée sur maison de caractère** : Sur une maison en pierre ou en zone patrimoniale, proposer du zinc ou de l'alu laqué. Le PVC dévalorise le bien.
12. **Démoussage au Kärcher sur ardoise** : INTERDIT. Le nettoyeur haute pression casse les ardoises et les rend poreuses. Toujours basse pression + traitement chimique.

---

## 10. COORDINATION AVEC LES AUTRES CORPS DE MÉTIER

| Situation | Corps de métier impliqué | Séquence |
|-----------|-------------------------|----------|
| Création Velux | Couvreur + plaquiste (habillage intérieur) + électricien (si motorisé) | Couvreur d'abord → plaquiste après |
| ITE + couverture | Façadier (ITE murs) + couvreur (débord toiture) | Coordonner : le débord de toit peut devoir être rallongé si ITE épaisse |
| Réfection + isolation combles | Couvreur (par l'extérieur) + plaquiste (par l'intérieur) | Si sarking : couvreur fait tout. Si isolation intérieure : plaquiste après couvreur |
| Cheminée + couverture | Couvreur (solin, abergement) + maçon (réparation souche) + fumiste (tubage) | Maçon d'abord (souche) → fumiste (tubage) → couvreur (abergement zinc) |
| Gouttières + assainissement | Couvreur-zingueur (gouttières) + terrassier/maçon (raccordement EP au réseau) | Couvreur descend la gouttière → maçon raccorde au réseau |
| Panneaux photovoltaïques | Couvreur (préparation support) + électricien (raccordement) | Couvreur prépare → électricien câble |

---

## 11. COEFFICIENTS RÉGIONAUX — COUVREUR

| Zone | Coefficient | Note |
|------|------------|------|
| Paris intra-muros | ×1.25 à ×1.40 | Contraintes accès, stationnement, arrêté voirie |
| Petite couronne IdF | ×1.15 à ×1.25 | |
| Grande couronne IdF | ×1.05 à ×1.15 | |
| Lyon, Marseille, Bordeaux, Toulouse, Nice | ×1.00 à ×1.10 | |
| Grandes villes | ×0.95 à ×1.05 | |
| Villes moyennes | ×0.90 à ×1.00 | Base du référentiel |
| Zones rurales | ×0.85 à ×0.95 | |
| Montagne (altitude > 800m) | ×1.10 à ×1.25 | Accès difficile, neige, conditions météo |
| DOM-TOM | ×1.30 à ×1.55 | Import matériaux, cyclones (normes paraycycloniques) |
| Corse | ×1.10 à ×1.25 | Transport maritime |
| Zone ABF (monuments historiques) | ×1.15 à ×1.35 | Matériaux imposés (ardoise, tuile plate, coloris), techniques traditionnelles |

---

## 12. TEMPS DE TRAVAIL RÉALISTE PAR POSTE

| Poste | Temps pour 1 compagnon | Note |
|-------|----------------------|------|
| Dépose tuiles romanes | 30-40 m²/jour | Inclut tri et palettisation |
| Dépose ardoises | 20-30 m²/jour | Fragile, une par une |
| Pose litonnage | 40-60 m²/jour | Après pose écran sous-toiture |
| Pose écran sous-toiture HPV | 50-80 m²/jour | |
| Pose tuiles romanes | 20-30 m²/jour | Standard |
| Pose tuiles plates | 8-15 m²/jour | 65-75 pièces/m² ! |
| Pose ardoise crochet | 10-15 m²/jour | |
| Pose ardoise clouée | 6-10 m²/jour | |
| Pose zinc joint debout | 6-10 m²/jour | Technique |
| Pose faîtage à sec | 15-25 ml/jour | |
| Pose gouttière zinc soudée | 6-10 ml/jour | |
| Pose gouttière PVC | 15-25 ml/jour | |
| Création Velux (complet) | 1 Velux/jour | Découpe + chevêtre + pose + raccord |

---

## 13. CONSOMMABLES ET PETITES FOURNITURES — PRIX

| Fourniture | Consommation type | Prix |
|-----------|-------------------|------|
| Mortier de faîtage (sac 25kg) | 1 sac pour 5-6 ml de faîtage | 8-15€/sac |
| Closoir ventilé (rouleau 5m) | 1 rouleau pour 5 ml de faîtage | 15-30€/rouleau |
| Mastic polyuréthane 310ml | 1 cartouche pour 8-10 points de scellement | 6-12€/cartouche |
| Bande d'étanchéité plomb autocollante (150mm) | Solin, raccord fenêtre de toit | 12-20€/ml |
| Mousse expansive (750ml) | Calfeutrement | 5-10€/bombe |
| Flux de soudure zinc | 1 pot pour 50+ soudures | 8-15€/pot |
| Baguettes soudure zinc (kg) | 1 kg pour 20-30 ml de gouttière | 15-30€/kg |
| Pointes galvanisées (kg) | 1 kg pour 5-8 m² | 5-9€/kg |
| Produit anti-mousse concentré (5L) | 1L pour 10-15 m² | 25-50€/5L |
| Hydrofuge toiture (20L) | 1L pour 3-5 m² | 60-120€/20L |
| Bâche de protection (4×5m) | Protection sols/végétation | 10-25€/bâche |

---

## 14. ASSURANCES ET GARANTIES

```json
{
  "assurances_couverture": {
    "garantie_decennale": {
      "obligatoire": true,
      "duree": "10 ans",
      "couvre": ["Défaut d'étanchéité", "Déformation charpente", "Défaut de pose rendant l'ouvrage impropre à sa destination", "Infiltrations dues à un vice de pose"],
      "ne_couvre_pas": ["Usure normale", "Défaut d'entretien", "Intempéries exceptionnelles (sauf si pose non conforme aux normes vent/neige)", "Matériaux fournis par le client"]
    },
    "garantie_biennale": {
      "duree": "2 ans",
      "couvre": ["Fenêtres de toit", "Volets roulants Velux", "Motorisation", "Gouttières"]
    },
    "garantie_parfait_achevement": {
      "duree": "1 an",
      "couvre": ["Tout défaut signalé dans l'année suivant la réception"]
    },
    "responsabilite_civile_pro": {
      "obligatoire": true,
      "couvre": ["Dommages causés aux tiers pendant les travaux", "Chute de tuiles sur véhicule voisin", "Dégât des eaux chez le voisin"]
    }
  },
  "note_ia": "TOUJOURS vérifier l'attestation décennale AVANT signature du devis. L'attestation doit mentionner l'activité 'couverture'. Une décennale 'maçonnerie' ne couvre pas les travaux de couverture."
}
```

---

## 15. RÈGLES IA PRICING — CERVEAU STRUCTORAI

### Règle 1 — Toujours donner des FOURCHETTES
Le cerveau ne donne JAMAIS un prix unique. Toujours min-max.

### Règle 2 — Appliquer le coefficient régional
Prix affiché = prix référentiel × coefficient zone du chantier. En zone ABF ou montagne, coefficients cumulatifs.

### Règle 3 — Déférer aux prix de l'artisan (Mem0)
Si l'artisan a ses propres prix → utiliser SES prix. Sinon → prix référentiel.

### Règle 4 — Alerter sur les prix anormaux
Si prix < 70% du référentiel → alerte. Si > 150% → alerte.

### Règle 5 — Toujours inclure l'échafaudage
```
SI devis couverture ET pas de ligne échafaudage → ALERTER
  "Attention, l'échafaudage n'est pas dans le devis.
   Pour une maison R+1, prévoir 1 500-3 000€ d'échafaudage (8-15€/m² de façade)."
```

### Règle 6 — Vérifier le diagnostic amiante
```
SI rénovation toiture ET bâtiment construit avant 1997 → ALERTER
  "Un diagnostic amiante est obligatoire avant dépose de l'ancienne couverture.
   Coût : 150-400€. Si positif, la dépose coûtera 30-60€/m² en plus."
```

### Règle 7 — Proposer l'écran sous-toiture
```
SI réfection couverture ET pas d'écran sous-toiture → SUGGÉRER
  "Profitez de la réfection pour poser un écran sous-toiture HPV (+5-10€/m²).
   Protection contre les infiltrations et la condensation. Exigé par la plupart des assureurs."
```

### Règle 8 — Vérifier le PLU
```
SI changement de matériau ou coloris couverture → RAPPELER
  "Avez-vous vérifié le PLU de la commune ? Un changement de couverture nécessite
   une déclaration préalable. En zone ABF, l'accord de l'architecte des Bâtiments de France est requis."
```

### Règle 9 — Ne jamais sous-estimer la tuile plate et l'ardoise
```
SI devis tuile plate OU ardoise → APPLIQUER rendement spécifique
  Tuile plate : 8-15 m²/jour (vs 25 m² pour romane)
  Ardoise naturelle : 6-15 m²/jour
  → Le temps de pose est 2-3× plus élevé. Le prix au m² DOIT refléter cette réalité.
```

---

## 16. DEVIS TYPES — EXEMPLES CHIFFRÉS

### Devis type 1 : Réfection couverture tuiles romanes — Maison R+1 (90m² de toiture)
| Poste | Quantité | Prix unitaire HT | Total HT |
|-------|----------|-----------------|----------|
| Location + montage/démontage échafaudage | 80 m² façade | 12€/m² | 960€ |
| Dépose couverture existante + évacuation | 90 m² | 12€/m² | 1 080€ |
| Remplacement liteaux pourris (30%) | 27 m² | 12€/m² | 324€ |
| Pose écran sous-toiture HPV | 90 m² | 6€/m² | 540€ |
| Pose litonnage neuf | 90 m² | 8€/m² | 720€ |
| Fourniture + pose tuiles romanes terre cuite neuves | 90 m² | 45€/m² | 4 050€ |
| Faîtage à sec (closoir ventilé + faîtière) | 12 ml | 45€/ml | 540€ |
| Rives à sec | 24 ml | 28€/ml | 672€ |
| Chatières ventilation (1/20m²) | 5 u | 12€/u | 60€ |
| Reprise solin cheminée zinc | 1 forfait | 350€ | 350€ |
| Nettoyage fin de chantier | 1 forfait | 150€ | 150€ |
| **TOTAL HT** | | | **9 446€** |
| TVA 10% (logement > 2 ans) | | | 945€ |
| **TOTAL TTC** | | | **10 391€** |

### Devis type 2 : Réfection couverture ardoise naturelle — Maison bretonne R+1 (80m²)
| Poste | Quantité | Prix unitaire HT | Total HT |
|-------|----------|-----------------|----------|
| Location + montage/démontage échafaudage | 70 m² façade | 14€/m² | 980€ |
| Diagnostic amiante (bâtiment ancien) | 1 forfait | 250€ | 250€ |
| Dépose couverture ardoise existante + évacuation | 80 m² | 18€/m² | 1 440€ |
| Remplacement voligeage abîmé (40%) | 32 m² | 18€/m² | 576€ |
| Pose écran sous-toiture HPV | 80 m² | 7€/m² | 560€ |
| Fourniture ardoise naturelle Espagne (32×22) + crochets inox | 80 m² | 45€/m² | 3 600€ |
| Pose ardoise au crochet | 80 m² | 55€/m² | 4 400€ |
| Faîtage zinc | 10 ml | 55€/ml | 550€ |
| Rives zinc | 20 ml | 35€/ml | 700€ |
| Noue zinc | 6 ml | 70€/ml | 420€ |
| Remplacement gouttières zinc demi-ronde (dépose + pose) | 30 ml | 70€/ml | 2 100€ |
| Remplacement descentes EP zinc | 12 ml | 45€/ml | 540€ |
| Nettoyage fin de chantier | 1 forfait | 200€ | 200€ |
| **TOTAL HT** | | | **16 316€** |
| TVA 10% (logement > 2 ans) | | | 1 632€ |
| **TOTAL TTC** | | | **17 948€** |

### Devis type 3 : Démoussage + hydrofuge toiture tuiles — Maison plain-pied (120m²)
| Poste | Quantité | Prix unitaire HT | Total HT |
|-------|----------|-----------------|----------|
| Mise en place protections sols + végétation | 1 forfait | 100€ | 100€ |
| Nettoyage basse pression toiture | 120 m² | 8€/m² | 960€ |
| Traitement anti-mousse (pulvérisation) | 120 m² | 5€/m² | 600€ |
| Temps de pause 48h (traitement agit) | - | - | - |
| Rinçage basse pression | 120 m² | 3€/m² | 360€ |
| Application hydrofuge incolore | 120 m² | 10€/m² | 1 200€ |
| Nettoyage gouttières + descentes | 1 forfait | 120€ | 120€ |
| Remplacement 8 tuiles cassées (repérées lors du nettoyage) | 8 u | 15€/u | 120€ |
| Nettoyage fin de chantier | 1 forfait | 80€ | 80€ |
| **TOTAL HT** | | | **3 540€** |
| TVA 10% (logement > 2 ans) | | | 354€ |
| **TOTAL TTC** | | | **3 894€** |

---

> **Note pour le cerveau IA :** Ce référentiel couvre l'intégralité du métier couvreur/zingueur :
> 1. Types de couverture (7 matériaux : tuiles terre cuite 4 types, tuile béton, ardoise naturelle/synthétique, zinc, bac acier, shingle) avec prix terrain
> 2. Éléments de charpente et sous-couverture (litonnage, écran HPV, fixations) avec prix
> 3. Zinguerie complète (5 matériaux gouttière, descentes EP, solins, noues, abergements) avec prix
> 4. Fenêtres de toit (5 configurations Velux avec prix fourniture + pose)
> 5. Outillage couvreur (17 outils) avec prix
> 6. Mises en oeuvre (7 catégories, 40+ postes chiffrés) avec prix
> 7. Forfaits par type de maison (4 exemples)
> 8. Taux horaires et rendement par poste + temps de travail réaliste
> 9. Réglementation (9 règles dont DTU, amiante, PLU/ABF, sécurité)
> 10. 12 erreurs courantes terrain
> 11. Coordination inter-corps de métier (6 situations)
> 12. Coefficients régionaux (11 zones dont montagne et ABF)
> 13. Consommables détaillés (11 fournitures avec consommation type)
> 14. Assurances et garanties (décennale, biennale, parfait achèvement)
> 15. Règles IA pricing (9 règles impératives dont amiante, échafaudage, PLU)
> 16. Devis types chiffrés (réfection tuiles 90m², réfection ardoise 80m², démoussage 120m²)
