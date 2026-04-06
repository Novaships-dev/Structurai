# STRUCTORAI — RÉFÉRENTIEL TECHNIQUE ÉLECTRICITÉ
> Données techniques exhaustives pour le cerveau IA.
> Câbles, sections, normes NFC 15-100, tableau électrique, appareillage, outillage, mises en oeuvre et prix.
> Date : 06/04/2026

---

## 1. TYPES DE CÂBLES & FILS ÉLECTRIQUES

### Fil rigide H07V-U (fil sous gaine)
```json
{
  "type": "H07V-U",
  "description": "Fil rigide monobrin cuivre, isolant PVC. Le plus utilisé en habitat.",
  "usage": ["Circuits éclairage", "Circuits prises", "Tirage dans gaine ICTA"],
  "sections_courantes": [
    {"section": "1.5 mm²", "disjoncteur_max": "16A", "usage": "Éclairage (max 8 points), VMC, sonnette", "prix_100m": {"min": 12, "max": 22}},
    {"section": "2.5 mm²", "disjoncteur_max": "20A", "usage": "Prises (max 12), radiateur ≤3500W", "prix_100m": {"min": 18, "max": 35}},
    {"section": "6 mm²", "disjoncteur_max": "32A", "usage": "Plaque de cuisson, cuisinière", "prix_100m": {"min": 40, "max": 70}},
    {"section": "10 mm²", "disjoncteur_max": "40A", "usage": "Alimentation tableau divisionnaire, borne recharge VE", "prix_100m": {"min": 70, "max": 120}},
    {"section": "16 mm²", "disjoncteur_max": "63A", "usage": "Alimentation tableau principal depuis disjoncteur abonné", "prix_100m": {"min": 110, "max": 190}},
    {"section": "25 mm²", "disjoncteur_max": "90A", "usage": "Liaison compteur-tableau (monophasé forte puissance)", "prix_100m": {"min": 180, "max": 300}}
  ],
  "couleurs": {
    "phase": "Rouge, Marron ou Noir",
    "neutre": "Bleu",
    "terre": "Vert/Jaune (OBLIGATOIRE, jamais d'autre couleur)",
    "note": "JAMAIS utiliser un fil vert/jaune pour autre chose que la terre"
  },
  "conditionnement": "Couronne 100m (le plus courant) ou au mètre",
  "norme": "NF C 32-201 / IEC 60227",
  "marques": ["Nexans", "Prysmian (Pirelli)", "Legrand", "Debflex", "Zenitech"]
}
```

### Câble souple H07V-K (pour tableau électrique)
```json
{
  "type": "H07V-K",
  "description": "Fil souple multibrins cuivre, pour câblage tableau électrique et raccordements d'appareils fixes.",
  "usage": ["Câblage intérieur tableau électrique", "Raccordement appareils fixes"],
  "avantage": "Plus facile à manipuler dans le tableau que le fil rigide. Nécessite des embouts de câblage à sertir.",
  "sections": ["1.5mm²", "2.5mm²", "6mm²", "10mm²", "16mm²", "25mm²"],
  "prix_100m": {
    "1.5mm²": {"min": 15, "max": 28},
    "2.5mm²": {"min": 22, "max": 40},
    "6mm²": {"min": 50, "max": 85},
    "10mm²": {"min": 80, "max": 140}
  }
}
```

### Câble extérieur H07RN-F (chantier, jardin)
```json
{
  "type": "H07RN-F",
  "description": "Câble souple caoutchouc très résistant. Usage extérieur, chantier, prolongateur.",
  "usage": ["Rallonge chantier", "Alimentation extérieure", "Piscine", "Jardin"],
  "avantage": "Résistant UV, eau, huiles, abrasion. Le plus costaud.",
  "sections": ["3G1.5", "3G2.5", "3G6"],
  "prix_au_metre": {
    "3G1.5": {"min": 2, "max": 4},
    "3G2.5": {"min": 3, "max": 5},
    "3G6": {"min": 5, "max": 9}
  }
}
```

### Câble R2V (rigide gainé, ex RO2V)
```json
{
  "type": "R2V (U-1000 R2V)",
  "description": "Câble rigide multiconducteurs avec double isolation PVC. Ne nécessite PAS de gaine ICTA.",
  "usage": ["Alimentation tableau", "Circuits extérieurs", "Circuits encastrés sans gaine", "Chemin de câbles"],
  "sections_courantes": [
    {"ref": "3G1.5", "nb_conducteurs": 3, "section": "1.5 mm²", "usage": "Éclairage", "prix_100m": {"min": 45, "max": 80}},
    {"ref": "3G2.5", "nb_conducteurs": 3, "section": "2.5 mm²", "usage": "Prises de courant", "prix_100m": {"min": 65, "max": 110}},
    {"ref": "3G6", "nb_conducteurs": 3, "section": "6 mm²", "usage": "Plaque de cuisson (monophasé)", "prix_50m": {"min": 80, "max": 140}},
    {"ref": "3G10", "nb_conducteurs": 3, "section": "10 mm²", "usage": "Borne VE, tableau divisionnaire", "prix_50m": {"min": 130, "max": 220}},
    {"ref": "5G1.5", "nb_conducteurs": 5, "section": "1.5 mm²", "usage": "Triphasé éclairage", "prix_100m": {"min": 65, "max": 110}},
    {"ref": "5G2.5", "nb_conducteurs": 5, "section": "2.5 mm²", "usage": "Triphasé prises", "prix_100m": {"min": 95, "max": 160}}
  ],
  "avantages": "Pas besoin de gaine. Protection mécanique intégrée. Pose extérieure possible.",
  "conditionnement": "Couronne 50m ou 100m, touret 500m",
  "norme": "NF C 32-321",
  "fournisseurs": ["Rexel", "Sonepar", "Leroy Merlin", "Brico Dépôt"]
}
```

### Gaine ICTA préfilée
```json
{
  "type": "Gaine ICTA préfilée",
  "description": "Gaine annelée souple avec fils déjà tirés à l'intérieur. Solution la plus rapide en neuf.",
  "diametres": [
    {"dn": 16, "usage": "1 fil 1.5mm² (commande, sonnette)"},
    {"dn": 20, "usage": "3 fils 1.5mm² (éclairage) ou 3 fils 2.5mm² (prises)"},
    {"dn": 25, "usage": "5 fils 2.5mm² ou 3 fils 6mm²"},
    {"dn": 32, "usage": "Câble 3G6 ou 3G10 (gros circuits)"},
    {"dn": 40, "usage": "Gros câbles, tableau divisionnaire"}
  ],
  "prix_prefilee_couronne_25m": {
    "3x1.5mm²_Ø20": {"min": 25, "max": 45},
    "3x2.5mm²_Ø20": {"min": 35, "max": 60},
    "3x6mm²_Ø25": {"min": 65, "max": 110}
  },
  "prix_gaine_vide_couronne_25m": {
    "Ø16": {"min": 5, "max": 10},
    "Ø20": {"min": 6, "max": 12},
    "Ø25": {"min": 8, "max": 15},
    "Ø32": {"min": 12, "max": 22}
  },
  "marques": ["Prefilzen", "Debflex", "Legrand", "Arnould"]
}
```

### Câbles courant faible
| Type | Usage | Section/Cat | Prix 100m |
|------|-------|------------|-----------|
| Câble RJ45 Cat 5e | Réseau informatique basique | Cat 5e | 30-60€ |
| Câble RJ45 Cat 6 | Réseau informatique performant | Cat 6 | 45-90€ |
| Câble RJ45 Cat 6a blindé | Réseau pro, fibre-compatible | Cat 6a FTP | 70-130€ |
| Câble coaxial TV (17 VATC) | Antenne TV, satellite | 75 Ω | 20-40€ |
| Câble PTT 1 paire | Téléphone (en disparition) | 0.6mm | 10-20€ |
| Câble alarme 4 fils | Détecteurs alarme | 4x0.22mm² | 15-30€ |
| Câble alarme 6 fils | Détecteurs + sirène | 6x0.22mm² | 20-40€ |

### Gaines, moulures, goulottes, plinthes électriques
| Type | Usage | Prix |
|------|-------|------|
| Gaine ICTA vide Ø16 (couronne 25m) | 1 fil, commande | 5-10€ |
| Gaine ICTA vide Ø20 (couronne 25m) | Standard éclairage/prises | 6-12€ |
| Gaine ICTA vide Ø25 (couronne 25m) | Gros circuits | 8-15€ |
| Gaine ICTA vide Ø32 (couronne 25m) | Alimentation tableau | 12-22€ |
| Gaine TPC rouge Ø40 (couronne 25m) | Passage extérieur enterré (courant fort) | 15-30€ |
| Gaine TPC verte Ø40 (couronne 25m) | Passage extérieur enterré (télécom) | 15-30€ |
| Moulure PVC 20x10mm (barre 2m) | Câblage apparent 1-2 fils | 1-3€ |
| Moulure PVC 32x16mm (barre 2m) | Câblage apparent 3-4 fils | 2-5€ |
| Moulure PVC 40x25mm (barre 2m) | Câblage apparent multi-circuits | 3-7€ |
| Goulotte PVC 60x40mm (barre 2m) | Multi-circuits, industriel | 5-12€ |
| Goulotte PVC 100x60mm (barre 2m) | Chemin de câbles, tableau | 8-18€ |
| Plinthe électrique PVC (barre 2.5m) | Câblage discret au sol | 5-15€ |
| Tube IRL rigide Ø16 (barre 2m) | Protection mécanique apparente | 1-3€ |
| Tube IRL rigide Ø20 (barre 2m) | Protection mécanique apparente | 1.50-4€ |
| Collier fixation gaine ICTA Ø20 (lot 100) | Fixation gaine dans cloison/plafond | 5-10€ |
| Collier fixation tube IRL (lot 50) | Fixation tube apparent | 3-6€ |
| Accessoires moulure (angles, tés, embouts) | Finitions | 0.50-3€ pièce |

**Marques :** Legrand, Arnould (moulures), Lapp (gaines), Hegler (TPC)

### GTL — Gaine Technique Logement
| Composant | Usage | Prix |
|-----------|-------|------|
| Goulotte GTL 13 modules (Legrand Drivia) | Cheminement compteur → tableau | 30-60€ |
| Goulotte GTL 18 modules | Grande maison | 40-80€ |
| Coffret de communication (Grade 1) | Réseau RJ45 + TV | 40-100€ |
| Coffret de communication (Grade 3) | Réseau RJ45 haute perf + TV | 80-200€ |
| Platine disjoncteur abonné | Support DB dans GTL | 10-25€ |

**NFC 15-100 :** La GTL est obligatoire en neuf. Elle regroupe dans un même cheminement vertical : le disjoncteur abonné, le tableau électrique, le coffret de communication, et les arrivées/départs de câbles. Hauteur entre 0.90m et 1.80m pour le tableau.

---

## 2. NORME NF C 15-100 — RÉSUMÉ OPÉRATIONNEL

### Table des circuits — sections et protections
| Circuit | Section mini | Disjoncteur max | Nb points max | Diff 30mA | Note |
|---------|-------------|-----------------|---------------|-----------|------|
| Éclairage | 1.5 mm² | 16A (10A conseillé) | 8 points lumineux | Type AC | Min 2 circuits pour ≥2 pièces |
| Prises 16A (1.5mm²) | 1.5 mm² | 16A | 8 prises | Type AC | |
| Prises 16A (2.5mm²) | 2.5 mm² | 20A | 12 prises | Type AC | **Le plus courant** |
| Plaque cuisson / cuisinière | 6 mm² | 32A | 1 seul appareil | **Type A** | Circuit dédié obligatoire |
| Four | 2.5 mm² | 20A | 1 seul appareil | Type A ou AC | Circuit dédié |
| Lave-linge | 2.5 mm² | 20A | 1 seul appareil | **Type A** | Circuit dédié |
| Lave-vaisselle | 2.5 mm² | 20A | 1 seul appareil | **Type A** | Circuit dédié |
| Sèche-linge | 2.5 mm² | 20A | 1 seul appareil | Type AC | Circuit dédié |
| Chauffe-eau | 2.5 mm² | 20A | 1 seul appareil | Type AC | Via contacteur HP/HC |
| Chauffage (par radiateur) | 1.5 mm² | 16A (≤3500W) | variable | Type AC | Fil pilote en plus |
| Chauffage (par radiateur) | 2.5 mm² | 20A (≤4500W) | variable | Type AC | |
| VMC | 1.5 mm² | 2A | 1 seule VMC | Type AC | Circuit dédié |
| Volets roulants | 1.5 mm² | 16A | variable | Type AC | |
| PAC / Climatisation | selon puissance | selon puissance | 1 seul appareil | **Type F** (nouveau 2025) | Circuit dédié |
| Borne recharge VE (Wallbox) | 10 mm² | 40A | 1 seule borne | **Type A** | Circuit dédié |
| Piscine | selon équipement | selon équipement | - | 30mA dédié | Règles spéciales (volume) |

### Nombre minimum de prises par pièce (NFC 15-100)
| Pièce | Nb prises min | Nb éclairage min | Prises spécialisées | Note |
|-------|--------------|------------------|--------------------|----|
| Séjour ≤28m² | 5 | 1 point plafond | - | 1 prise par tranche de 4m² |
| Séjour >28m² | 7 | 1 point plafond | - | |
| Chambre | 3 | 1 point plafond | - | |
| Cuisine | 6 (dont 4 au-dessus plan travail) | 1 point plafond | Plaque + 3 dédiés (four, LV, LL) | Zone au-dessus plan de travail : 4 prises mini |
| SDB | 1 (hors volume) | 1 point plafond | - | Respect des volumes 0/1/2/hors volume |
| WC | 1 | 1 point plafond | - | |
| Couloir / entrée | 1 | 1 point | - | Prise près de l'interrupteur |
| Garage / buanderie | 1 | 1 | Congélateur recommandé | |
| Extérieur | selon besoin | 1 | - | Câble obligatoire (pas de fil sous gaine) |

### Volumes SDB (NFC 15-100)
| Volume | Zone | Ce qui est autorisé |
|--------|------|-------------------|
| Volume 0 | Intérieur baignoire/douche | RIEN (sauf TBTS 12V) |
| Volume 1 | Au-dessus bain/douche, 2.25m de haut | Éclairage IPX4 en TBTS 12V, chauffe-eau instantané |
| Volume 2 | 60cm autour du vol.1 | Éclairage IPX4, classe II, prises rasoir 20-50VA |
| Hors volume | Au-delà de 60cm | Prises, appareillage normal, radiateur |

---

## 3. TABLEAU ÉLECTRIQUE — COMPOSANTS ET PRIX

### Coffret / Tableau
| Type | Nb modules | Usage | Prix |
|------|-----------|-------|------|
| Coffret 1 rangée 13 modules | 13 | Studio, T1, extension | 15-40€ |
| Coffret 2 rangées 26 modules | 26 | Appartement T2-T3 | 25-60€ |
| Coffret 3 rangées 39 modules | 39 | Appartement T3-T4 | 35-80€ |
| Coffret 4 rangées 52 modules | 52 | Maison T4-T5 | 45-100€ |
| Coffret 5+ rangées | 65+ | Grande maison | 60-150€ |

Marques : Legrand (Drivia), Schneider (Resi9), Hager (Gamma), ABB

### Interrupteurs différentiels (DDR 30mA)
| Type | Calibre | Usage | Prix unitaire |
|------|---------|-------|--------------|
| Type AC 40A 30mA 2P | 40A | Circuits standard (éclairage, prises) | 25-55€ |
| Type AC 63A 30mA 2P | 63A | Gros circuits standards | 30-65€ |
| Type A 40A 30mA 2P | 40A | Plaque, lave-linge, lave-vaisselle, VE | 35-70€ |
| Type A 63A 30mA 2P | 63A | Gros circuits Type A | 40-80€ |
| Type F 40A 30mA 2P | 40A | PAC, clim (nouveau 2025) | 50-100€ |

**Règle NFC 15-100 :** Max 8 disjoncteurs par interrupteur différentiel. Répartir éclairage et prises sous au moins 2 DDR différents.

### Disjoncteurs divisionnaires
| Calibre | Usage | Prix unitaire |
|---------|-------|--------------|
| 2A | VMC | 5-12€ |
| 10A | Éclairage | 5-12€ |
| 16A | Éclairage, prises 1.5mm², volets | 5-12€ |
| 20A | Prises 2.5mm², four, LL, LV, CE, sèche-linge | 5-15€ |
| 32A | Plaque de cuisson | 8-18€ |
| 40A | Borne VE, tableau divisionnaire | 12-25€ |

Marques : Legrand (DX³), Schneider (Resi9), Hager, ABB

### Autres composants tableau
| Composant | Usage | Prix |
|-----------|-------|------|
| Disjoncteur de branchement (DB) 30/60A | Coupure générale (fourni par Enedis en principe) | 40-80€ |
| Contacteur HP/HC 20A | Commande chauffe-eau heures creuses | 15-35€ |
| Télérupteur | Commande éclairage multi-points (va-et-vient > 2 points) | 15-30€ |
| Minuterie | Éclairage temporisé (escalier, couloir) | 20-40€ |
| Parafoudre | Protection contre la foudre (obligatoire en zone AQ2) | 60-150€ |
| Horloge programmable | Programmation horaire (alternative au contacteur HC) | 25-60€ |
| Peigne d'alimentation horizontal | Raccordement rapide des disjoncteurs | 8-20€ par rangée |
| Peigne d'alimentation vertical | Liaison entre rangées | 10-25€ |
| Bornier de terre | Raccordement des fils de terre | 5-12€ |
| Obturateur (cache module vide) | Fermeture emplacements libres | 2-5€ lot |

---

## 4. APPAREILLAGE — PRISES, INTERRUPTEURS, ACCESSOIRES

### Prises de courant
| Type | Usage | Prix unitaire |
|------|-------|--------------|
| Prise 2P+T 16A (encastrée) | Standard, la plus courante | 2-8€ |
| Prise 2P+T 16A (saillie) | Rénovation, apparent | 3-10€ |
| Double prise 2P+T 16A | 2 prises sur 1 boîtier | 5-15€ |
| Prise 2P+T avec terre latérale | Ancienne norme (remplacement) | 3-8€ |
| Prise 32A (plaque cuisson) | Circuit dédié plaque/cuisinière | 8-20€ |
| Prise RJ45 Cat 6 | Réseau informatique/téléphone | 8-20€ |
| Prise TV coaxiale | Antenne/satellite | 5-15€ |
| Prise USB intégrée (2xUSB A ou C) | Chargement téléphone | 12-30€ |
| Prise étanche IP44 (extérieur) | Usage extérieur | 10-25€ |
| Prise rasoir 20-50VA | SDB (volume 2) | 25-60€ |

### Interrupteurs et commandes
| Type | Usage | Prix unitaire |
|------|-------|--------------|
| Interrupteur simple allumage | 1 point de commande | 2-8€ |
| Interrupteur va-et-vient | 2 points de commande | 3-10€ |
| Interrupteur double va-et-vient | 2 circuits, 2 points commande | 5-15€ |
| Bouton poussoir | Pour télérupteur (3+ points commande) | 3-10€ |
| Interrupteur VMC 2 vitesses | Commande VMC | 5-15€ |
| Variateur (dimmer) | Réglage intensité lumineuse | 15-40€ |
| Interrupteur volet roulant | Montée/descente | 8-20€ |
| Interrupteur automatique (détecteur présence) | Allumage auto | 25-60€ |
| Sortie de câble 20A | Raccordement fixe (radiateur, sèche-serviette) | 3-8€ |
| DCL (Dispositif Connexion Luminaire) | Point de centre au plafond | 3-8€ |

### Boîtiers d'encastrement
| Type | Usage | Prix |
|------|-------|------|
| Boîte d'encastrement simple Ø67 P40 | Prise/interrupteur dans placo | 0.30-0.80€ |
| Boîte d'encastrement simple Ø67 P50 | Prise/interrupteur dans mur maçonné | 0.30-0.80€ |
| Boîte d'encastrement double | 2 mécanismes côte à côte | 0.80-1.50€ |
| Boîte de dérivation (80x80, 100x100) | Jonction / dérivation circuits | 1-4€ |
| Boîte de dérivation étanche IP55 | Extérieur | 3-8€ |
| Boîte DCL plafond Ø67 | Point lumineux plafond | 1-3€ |
| Boîte pavillonnaire (point de centre) | Plafond béton | 2-5€ |

Gammes appareillage : **Entrée gamme** : Legrand Mosaic, Schneider Odace (3-8€). **Milieu** : Legrand Céliane, Schneider Odace Styl (8-15€). **Haut de gamme** : Legrand Dooxie, Art d'Arnould, Schneider Unica (15-40€+).

---

## 5. OUTILLAGE ÉLECTRICIEN

### Outillage de base
| Outil | Usage | Prix |
|-------|-------|------|
| Tournevis isolés 1000V (jeu plat + cruciforme) | Vissage/dévissage sous tension protégée | 15-40€ |
| Pince coupante diagonale isolée | Coupe fils | 10-25€ |
| Pince à dénuder (automatique) | Dénudage fils toutes sections | 15-35€ |
| Pince à becs (demi-ronde) isolée | Pliage, maintien fils | 10-25€ |
| Multimètre numérique | Mesure tension/continuité/résistance | 20-80€ |
| Testeur de tension sans contact (VAT) | Vérification absence tension | 15-40€ |
| Détecteur de phase (tournevis testeur) | Identification phase/neutre | 3-10€ |
| Niveau à bulle 30cm | Alignement prises/interrupteurs | 10-20€ |
| Mètre ruban 5m | Mesures | 5-15€ |
| Cutter / couteau d'électricien | Dénudage câble R2V | 5-15€ |
| Tire-fil nylon 20m | Passage fils dans gaine existante | 10-25€ |
| Aiguille tire-fil acier 15m | Passage fils dans gaine longue distance | 20-50€ |

### Outillage spécialisé
| Outil | Usage | Prix |
|-------|-------|------|
| Pince à sertir cosses | Sertissage embouts / cosses | 15-40€ |
| Jeu d'embouts de câblage (coffret) | Embouts pour tableau électrique | 10-25€ |
| Scie cloche Ø67 (bimetal) | Percement boîtiers placo | 5-15€ |
| Scie cloche Ø67 (SDS carbure) | Percement boîtiers béton/brique | 15-35€ |
| Rainureuse électrique | Saignées mur pour gaines | 200-500€ |
| Testeur d'installation (Chauvin Arnoux) | Contrôle conformité (terre, isolement, DDR) | 300-1500€ |
| Pince ampèremétrique | Mesure intensité sans déconnexion | 30-120€ |
| Lampe frontale | Travail dans combles/tableau | 15-40€ |

### Outillage électroportatif (partagé)
| Outil | Usage | Prix |
|-------|-------|------|
| Perforateur SDS+ | Percement béton, scellement | 150-400€ |
| Visseuse-perceuse 18V | Percement placo/bois, vissage | 100-300€ |
| Meuleuse Ø125 | Saignées, coupe | 50-150€ |
| Aspirateur chantier | Nettoyage poussière | 80-250€ |

Marques pro : Knipex, Wera, Wiha, Jokari, Chauvin Arnoux, Fluke, Milwaukee, Makita, Bosch Pro, DeWalt

---

## 6. MISES EN OEUVRE — TOUTES LES INTERVENTIONS ÉLECTRICITÉ AVEC PRIX

### A. DÉPOSE / DÉMONTAGE
| Intervention | Unité | Prix MO HT | Durée | Note |
|-------------|-------|-----------|-------|------|
| Dépose tableau électrique ancien | u | 80-200€ | 1-3h | Mise hors tension obligatoire |
| Dépose prise / interrupteur | u | 5-15€ | 10-20 min | |
| Dépose radiateur électrique | u | 20-50€ | 15-30 min | Déconnexion + descente |
| Dépose convecteur ancien + sortie de câble | u | 30-60€ | 20-40 min | |
| Dépose luminaire / plafonnier | u | 10-30€ | 10-20 min | |
| Dépose câblage ancien (fils apparents) | ml | 2-5€ | variable | |
| Dépose moulure / goulotte | ml | 2-5€ | variable | |
| Dépose VMC | u | 50-120€ | 0.5-1.5h | Combles : accès difficile |

### B. PRÉPARATION / GROS OEUVRE
| Intervention | Unité | Prix MO HT | Note |
|-------------|-------|-----------|------|
| Saignée dans placo | ml | 5-12€ | Rapide, peu de poussière |
| Saignée dans béton/parpaing | ml | 12-25€ | Rainureuse ou disqueuse. Bruyant. |
| Saignée dans pierre | ml | 20-40€ | Très dur |
| Rebouchage saignée (plâtre/MAP) | ml | 4-10€ | Souvent fait par plaquiste/peintre |
| Percement boîtier encastrement placo (Ø67) | u | 3-8€ | Scie cloche, rapide |
| Percement boîtier encastrement béton (Ø67) | u | 8-20€ | Scie cloche SDS, plus long |
| Percement passage de câble béton | u | 10-25€ | Perforateur SDS+ |
| Pose gaine ICTA dans cloison existante | ml | 5-12€ | |
| Pose gaine ICTA en faux plafond | ml | 4-10€ | Accès plus facile |
| Pose gaine ICTA dans dalle (neuf) | ml | 3-6€ | Avant coulage béton |
| Pose moulure / goulotte apparente | ml | 5-15€ | Rénovation, pose propre |
| Pose chemin de câbles (cave, garage) | ml | 10-25€ | Support métallique + câbles |
| Tirage de fils dans gaine existante | ml | 3-8€ | Tire-fil ou aiguille |
| Tirage de câble R2V en apparent | ml | 5-12€ | Fixation par colliers |

### C. POSE APPAREILLAGE
| Intervention | Unité | Prix MO HT | Note |
|-------------|-------|-----------|------|
| Pose prise 16A encastrée (neuf, gaine prête) | u | 25-50€ | Boîtier + raccordement + mécanisme + plaque |
| Pose prise 16A encastrée (rénovation, avec saignée) | u | 45-90€ | Saignée + gaine + tirage + boîtier + raccordement |
| Pose prise 16A saillie / moulure | u | 30-60€ | |
| Pose double prise | u | 35-70€ | |
| Pose prise 32A (plaque cuisson) | u | 50-100€ | Circuit dédié depuis tableau |
| Pose prise RJ45 | u | 35-70€ | Câblage + connecteur + platine |
| Pose prise TV | u | 25-50€ | |
| Pose prise extérieure étanche IP44 | u | 40-80€ | Câble R2V obligatoire |
| Pose interrupteur simple / va-et-vient | u | 25-50€ | Neuf avec gaine prête |
| Pose interrupteur (rénovation avec saignée) | u | 40-80€ | |
| Pose bouton poussoir + télérupteur | u | 60-120€ | Télérupteur au tableau + bouton(s) + câblage |
| Pose variateur | u | 35-70€ | |
| Pose interrupteur volet roulant | u | 30-60€ | |
| Pose détecteur de mouvement | u | 40-80€ | |
| Pose sortie de câble 20A (radiateur) | u | 20-40€ | |
| Pose DCL plafond (point lumineux) | u | 30-60€ | Boîte DCL + raccordement |
| Pose DCL + luminaire simple | u | 50-100€ | |
| Pose spot encastré LED | u | 30-60€ | Percement + transformateur si besoin |
| Pose applique murale | u | 35-70€ | Boîtier + raccordement + fixation |
| Pose bandeau LED | ml | 15-30€ | Alimentation + profilé + LED |

### D. TABLEAU ÉLECTRIQUE
| Intervention | Unité | Prix MO HT | Note |
|-------------|-------|-----------|------|
| Remplacement tableau (mise aux normes, ≤3 rangées) | u | 400-800€ | Dépose ancien + pose neuf + raccordement tous circuits |
| Remplacement tableau (mise aux normes, 4+ rangées) | u | 600-1500€ | Grande maison, nombreux circuits |
| Installation tableau neuf (construction) | u | 300-700€ | Hors circuits, juste le tableau + protections |
| Ajout 1 circuit (disjoncteur + câblage ≤10m) | u | 80-180€ | Nouveau disjoncteur + tirage câble + point |
| Ajout 1 circuit (disjoncteur + câblage >10m) | u | 120-250€ | |
| Ajout interrupteur différentiel | u | 60-120€ | Fourniture + pose + raccordement |
| Ajout contacteur HP/HC | u | 50-100€ | Pour chauffe-eau |
| Ajout parafoudre | u | 80-180€ | Obligatoire zone AQ2 |
| Pose disjoncteur abonné (remplacement) | u | 50-120€ | Intervention Enedis parfois nécessaire |
| Repérage / étiquetage tableau | forfait | 30-60€ | Obligatoire pour conformité |

### E. CIRCUITS SPÉCIALISÉS
| Intervention | Unité | Prix MO HT | Note |
|-------------|-------|-----------|------|
| Création circuit plaque de cuisson 32A | u | 150-350€ | Câble 6mm² + disjoncteur 32A + prise 32A |
| Création circuit four 20A | u | 100-250€ | Câble 2.5mm² + disjoncteur 20A |
| Création circuit lave-linge 20A | u | 100-250€ | Circuit dédié + prise |
| Création circuit lave-vaisselle 20A | u | 100-250€ | |
| Création circuit chauffe-eau 20A + contacteur | u | 120-280€ | Contacteur HP/HC + disjoncteur + câblage |
| Création circuit VMC 2A | u | 80-180€ | Câblage jusqu'aux combles |
| Création circuit volets roulants | par volet | 80-180€ | Interrupteur + câblage + alimentation |
| Création circuit borne VE (Wallbox 7kW) | u | 300-600€ | Câble 10mm², disjoncteur 40A, diff Type A |
| Création circuit borne VE (Wallbox 22kW triphasé) | u | 500-1000€ | Câble 5G6, disjoncteur 40A tri, diff Type A |

### F. VMC
| Intervention | Unité | Prix MO HT | Fourniture | Note |
|-------------|-------|-----------|-----------|------|
| Pose VMC simple flux autoréglable | u | 200-400€ | 80-200€ | Caisson + bouches + gaines |
| Pose VMC simple flux hygroréglable (Type B) | u | 250-500€ | 150-400€ | Plus performante, ajuste le débit |
| Pose VMC double flux | u | 500-1200€ | 800-3000€ | Récupération chaleur, 2 réseaux de gaines |
| Remplacement caisson VMC seul | u | 100-200€ | 80-300€ | Accès combles |
| Remplacement bouche d'extraction | u | 20-40€ | 5-20€ | SDB, cuisine, WC |
| Nettoyage / entretien VMC | u | 80-150€ | 0€ | Nettoyage bouches + vérification caisson |

### G. CHAUFFAGE ÉLECTRIQUE
| Intervention | Unité | Prix MO HT | Note |
|-------------|-------|-----------|------|
| Pose radiateur électrique (remplacement) | u | 40-80€ | Raccordement sur sortie de câble existante |
| Pose radiateur électrique (création circuit) | u | 120-250€ | Tirage câble + disjoncteur + sortie de câble + radiateur |
| Pose sèche-serviette électrique | u | 50-100€ | Raccordement sur sortie de câble |
| Pose fil pilote (programmation) | u | 30-60€ | 1 fil supplémentaire par radiateur |
| Pose programmateur / thermostat centralisé | u | 60-150€ | |
| Pose plancher chauffant électrique | m² | 30-60€ | Câble chauffant + thermostat + ragréage |

### H. DÉPANNAGE / RÉPARATION
| Intervention | Unité | Prix MO HT | Note |
|-------------|-------|-----------|------|
| Recherche de panne (diagnostic) | forfait | 50-120€ | Multimètre, tests, identification circuit |
| Remplacement disjoncteur | u | 30-60€ | |
| Remplacement interrupteur différentiel | u | 50-100€ | |
| Remplacement prise / interrupteur cassé | u | 25-50€ | |
| Réparation court-circuit | forfait | 60-200€ | Selon localisation |
| Remise en service après disjonction | forfait | 40-80€ | Diagnostic + remise sous tension |
| Remplacement ampoule / spot difficile d'accès | u | 15-40€ | Hauteur, faux plafond |

### I. RÉNOVATION / MISE AUX NORMES
| Intervention | Unité | Prix MO+fourniture HT | Note |
|-------------|-------|----------------------|------|
| Mise aux normes électrique complète (studio T1) | forfait | 3000-5000€ | Tableau + circuits + prises + éclairage |
| Mise aux normes électrique (T2-T3) | forfait | 5000-8000€ | |
| Mise aux normes électrique (T4-T5 maison) | forfait | 8000-15000€ | |
| Rénovation électrique complète au m² | m² | 80-150€ | MO + fourniture |
| Diagnostic électrique (obligatoire vente) | forfait | 80-150€ | Par diagnostiqueur certifié |
| Attestation Consuel (conformité) | forfait | 120-180€ | Obligatoire pour mise en service |
| Installation électrique complète neuve au m² | m² | 80-120€ | MO + fourniture, hors chauffage |
| Installation électrique + chauffage au m² | m² | 100-160€ | |

### J. MAJORATIONS
| Majoration | Supplément |
|-----------|-----------|
| Urgence soir (18h-22h) | +50% MO |
| Urgence nuit / WE / férié | +75-100% MO |
| Frais déplacement (<15km) | 0-25€ |
| Frais déplacement (15-30km) | 25-50€ |
| Accès difficile (combles, vide sanitaire) | +15-30% MO |
| Site occupé | +5-10% MO |
| Immeuble copropriété | +10-15% |

### K. VOLETS ROULANTS ÉLECTRIQUES
| Intervention | Unité | Prix MO HT | Fourniture | Note |
|-------------|-------|-----------|-----------|------|
| Pose moteur volet roulant (remplacement manivelle) | u | 60-120€ | 80-200€ (moteur tubulaire) | Démontage axe + pose moteur + réglage fins de course |
| Pose volet roulant complet (fenêtre standard) | u | 100-200€ | 200-600€ (volet complet motorisé) | Coffre + tablier + moteur + interrupteur |
| Pose volet roulant + raccordement électrique (création circuit) | u | 150-300€ | 200-600€ | Tirage câble + disjoncteur + interrupteur dédié |
| Remplacement moteur volet seul | u | 60-120€ | 60-150€ | |
| Remplacement tablier / lames | u | 50-100€ | 50-200€ | Selon largeur et matériau (PVC/alu) |
| Motorisation centralisée (commande groupée) | forfait | 150-400€ | 100-300€ (horloge + contacteur) | Commande tous volets en 1 bouton |
| Motorisation connectée (Somfy io/RTS) | par volet | 80-150€ | 150-350€ (moteur connecté) | App smartphone, assistant vocal |

Marques moteurs : Somfy (leader France), Bubendorff, Nice, Becker, Simu

### L. PORTAIL ET AUTOMATISME
| Intervention | Prix MO HT | Fourniture | Note |
|-------------|-----------|-----------|------|
| Pose motorisation portail battant (2 vantaux) | 200-500€ | 300-1000€ | Bras articulés ou vérins |
| Pose motorisation portail coulissant | 200-400€ | 250-800€ | Rail au sol + moteur |
| Création alimentation électrique portail (tranchée + câble) | 300-700€ | 50-150€ | Câble R2V enterré en gaine TPC |
| Pose interphone/visiophone + gâche | 200-500€ | 100-400€ | |
| Pose photocellules de sécurité | 40-80€ | 30-80€ la paire | Obligatoire pour sécurité |
| Pose feu clignotant | 30-60€ | 20-50€ | Obligatoire voie publique |

### M. ÉCLAIRAGE EXTÉRIEUR
| Intervention | Unité | Prix MO HT | Fourniture | Note |
|-------------|-------|-----------|-----------|------|
| Pose projecteur LED extérieur (sur façade) | u | 40-80€ | 15-80€ | Raccordement sur circuit existant |
| Pose projecteur LED + création circuit | u | 100-200€ | 15-80€ | Tirage câble R2V + disjoncteur + détecteur crépusculaire |
| Pose applique extérieure | u | 35-70€ | 15-60€ | |
| Pose borne / potelet jardin | u | 50-120€ | 25-100€ | Câble enterré en gaine TPC |
| Pose spot encastré sol (terrasse/allée) | u | 40-80€ | 20-60€ | Étanche IP67 minimum |
| Création circuit éclairage jardin complet | forfait | 300-800€ | 100-400€ | Tranchée + gaine TPC + câble + luminaires + disjoncteur |
| Pose détecteur crépusculaire | u | 30-60€ | 15-40€ | Allumage auto nuit |
| Pose détecteur de mouvement extérieur | u | 35-70€ | 20-50€ | IP44 minimum |

### N. BORNE DE RECHARGE VÉHICULE ÉLECTRIQUE (IRVE)
| Intervention | Prix MO HT | Fourniture | Note |
|-------------|-----------|-----------|------|
| Pose Wallbox 7kW monophasée (câblage <10m) | 300-500€ | 500-1200€ | Disjoncteur 40A + diff Type A + câble 10mm² |
| Pose Wallbox 7kW monophasée (câblage 10-25m) | 400-700€ | 500-1200€ | |
| Pose Wallbox 22kW triphasée | 500-1000€ | 800-2000€ | Câble 5G6 + disjoncteur 40A tri + diff Type A |
| Pose prise renforcée Green'Up 3.7kW | 150-300€ | 80-150€ | Alternative économique à la Wallbox |
| Étude IRVE + attestation Consuel | forfait | 80-200€ | Obligatoire pour bénéficier du crédit d'impôt |

**Crédit d'impôt IRVE :** 75% du coût (fourniture + pose), plafonné à 500€ par point de charge (2025-2026). Installateur certifié IRVE obligatoire (qualification QUALIFELEC IRVE).
Marques Wallbox : Wallbox (Pulsar Plus), Schneider (EVlink), Legrand (Green'Up), Hager (Witty), EVBox

### O. CONSUEL — PROCÉDURE ET COÛT
| Étape | Détail | Coût |
|-------|--------|------|
| Demande Consuel en ligne | Formulaire sur consuel.com | Gratuit (la demande) |
| Attestation Consuel (visa) | Visite inspecteur + certificat conformité | 120-180€ TTC |
| Contre-visite (si non-conformité) | Correction + nouvelle visite | 60-90€ supplémentaire |
| Délai moyen | 2-4 semaines après demande | |

**Quand est-ce obligatoire ?** Construction neuve, rénovation lourde avec nouveau branchement, installation photovoltaïque. PAS obligatoire pour ajout de prise ou remplacement tableau sur branchement existant.

### P. INSTALLATIONS SPÉCIFIQUES
| Intervention | Unité | Prix MO HT | Fourniture | Note |
|-------------|-------|-----------|-----------|------|
| Pose extracteur SDB (aérateur mural) | u | 40-80€ | 20-60€ | Alternative à la VMC pour SDB sans gaine |
| Pose hotte cuisine (raccordement élec + évac) | u | 80-180€ | - (hotte fournie client) | Prise dédiée + gaine évacuation |
| Pose climatisation split (partie élec uniquement) | u | 150-300€ | - | Circuit dédié + disjoncteur + diff Type F |
| Pose store banne motorisé (partie élec) | u | 80-150€ | - | Circuit + interrupteur + raccordement moteur |
| Pose chauffe-eau (raccordement élec seul) | u | 60-120€ | - | Disjoncteur 20A + contacteur HC + câble |
| Pose four encastrable (raccordement élec seul) | u | 40-80€ | - | Vérification circuit dédié 20A |
| Pose plaque induction (raccordement élec seul) | u | 50-100€ | - | Vérification circuit 32A + câble 6mm² |
| Pose machine à laver (raccordement élec seul) | u | 30-60€ | - | Vérification circuit dédié 20A |
| Mise en sécurité (a minima, pas mise aux normes) | forfait | 500-1500€ | variable | Diff 30mA + terre + protection circuits vitaux |
| Passage Triphasé → Monophasé | forfait | 300-600€ | variable | Rééquilibrage tableau + demande Enedis |
| Passage Monophasé → Triphasé | forfait | 400-800€ | variable | Nouveau tableau + demande Enedis |
| Augmentation puissance abonnement (Enedis) | forfait | 0-200€ | - | Gratuit si Linky, payant si ancien compteur |

---

## 7. TAUX HORAIRE ÉLECTRICIEN

| Zone | Taux horaire HT |
|------|----------------|
| Province | 35-60€/h |
| Grandes métropoles (Lyon, Marseille, Bordeaux) | 45-75€/h |
| Île-de-France | 55-100€/h |
| Paris intra-muros | 70-140€/h |

TVA : 10% en rénovation (logement > 2 ans), 20% en neuf, 5.5% en rénovation énergétique (PAC, isolation).

---

> **Note pour le cerveau IA :** Ce référentiel couvre l'intégralité de la chaîne électricité :
> 1. Câbles et fils (H07V-U, R2V, gaine ICTA préfilée, courant faible) avec prix au mètre et par couronne
> 2. Norme NFC 15-100 résumée en tableaux opérationnels (sections, protections, nb points, prises par pièce)
> 3. Volumes SDB
> 4. Tableau électrique complet (coffret, DDR, disjoncteurs, contacteur, parafoudre) avec prix
> 5. Appareillage complet (prises, interrupteurs, boîtiers) avec gammes et prix
> 6. Outillage électricien
> 7. Toutes les mises en oeuvre chiffrées : dépose, saignée, pose appareillage, tableau, circuits spécialisés, VMC, chauffage, dépannage, rénovation complète
> 8. Majorations et taux horaires par zone
> 9. Connecteurs et accessoires de câblage avec prix
> 10. Mise à la terre et protection foudre
> 11. Éclairage — types et prix fourniture
> 12. Chauffage électrique — radiateurs par type avec prix et puissance/m²
> 13. Interphone, visiophone, sonnette — fourniture et pose
> 14. Alarme et détection
> 15. Domotique et connecté
> 16. Consommables et petites fournitures

---

## 8. CONNECTEURS, DOMINOS, WAGOS, COSSES — PRIX DÉTAILLÉS

### Bornes de connexion WAGO (le standard pro actuel)
| Référence | Type | Nb entrées | Section max | Usage | Prix |
|-----------|------|-----------|------------|-------|------|
| Wago 2273 (automatique) | Fil rigide uniquement | 2 entrées | 2.5mm² | Éclairage, prises (le plus rapide) | 0.10-0.20€/u — Lot 100 : 10-18€ |
| Wago 2273 | Fil rigide uniquement | 3 entrées | 2.5mm² | | 0.12-0.25€/u — Lot 100 : 12-22€ |
| Wago 2273 | Fil rigide uniquement | 5 entrées | 2.5mm² | | 0.15-0.30€/u — Lot 100 : 15-28€ |
| Wago 2273 | Fil rigide uniquement | 8 entrées | 2.5mm² | | 0.25-0.45€/u — Lot 50 : 12-20€ |
| Wago 221 (à levier) | Fil rigide + souple | 2 entrées | 4mm² | Universel, réutilisable | 0.25-0.50€/u — Lot 50 : 12-22€ |
| Wago 221 (à levier) | Fil rigide + souple | 3 entrées | 4mm² | | 0.30-0.60€/u — Lot 50 : 15-28€ |
| Wago 221 (à levier) | Fil rigide + souple | 5 entrées | 4mm² | | 0.40-0.80€/u — Lot 25 : 10-18€ |
| Wago 773 (classique) | Fil rigide uniquement | 3 entrées | 6mm² | Gros circuits, plaque cuisson | 0.15-0.35€/u |
| Coffret assortiment Wago (221+2273) | Mixte | Mixte | 4mm² | **Indispensable dans la caisse** | 25-50€ le coffret |

**Note terrain :** Les Wago ont remplacé les dominos chez les pros. Plus rapide, plus compact, plus fiable. Le coffret assortiment est rentabilisé en 1 chantier.

### Dominos (barrettes de connexion à vis)
| Section max | Intensité max | Usage | Prix |
|------------|--------------|-------|------|
| 2.5mm² | 18A | Éclairage | Barrette 12 dominos : 0.50-1.50€ |
| 4mm² | 24A | Prises | 0.60-1.80€ |
| 6mm² | 41A | Prises, petit spécialisé | 0.80-2.00€ |
| 10mm² | 57A | Circuits moyens | 1.00-2.50€ |
| 16mm² | 80A | Gros circuits | 1.50-3.50€ |
| 25mm² | 100A | Alimentation tableau | 2.00-5.00€ |

**Note :** Le domino est en voie de disparition chez les pros (remplacé par le Wago), mais reste utilisé en rénovation et dépannage.

### Embouts de câblage (pour fils souples dans le tableau)
| Section | Couleur | Prix lot 100 |
|---------|---------|-------------|
| 0.5mm² | Blanc | 2-4€ |
| 0.75mm² | Gris | 2-4€ |
| 1mm² | Rouge | 2-4€ |
| 1.5mm² | Noir | 2-5€ |
| 2.5mm² | Bleu | 3-5€ |
| 6mm² | Vert | 4-7€ |
| 10mm² | Marron | 5-9€ |
| Coffret assortiment complet | Multi | 10-25€ |

### Cosses et accessoires
| Type | Usage | Prix |
|------|-------|------|
| Cosses à sertir (lot assorti) | Raccordement sur bornier, appareil | 5-15€ le lot 100 |
| Colliers rilsan (lot 100) | Fixation gaines, câbles | 2-5€ |
| Gaine thermorétractable (assortiment) | Isolation raccords | 5-12€ le lot |
| Scotch d'électricien (rouleau) | Isolation provisoire, repérage | 1-3€ le rouleau |
| Attaches câbles auto-adhésives (lot 50) | Fixation câbles en apparent | 3-8€ |

---

## 9. MISE À LA TERRE ET PROTECTION

### Mise à la terre
| Composant | Usage | Prix |
|-----------|-------|------|
| Piquet de terre cuivre 1.5m | Prise de terre principale | 15-30€ |
| Piquet de terre cuivre 2m | Prise de terre (sol dur) | 20-40€ |
| Câble de terre cuivre nu 25mm² (au mètre) | Liaison piquet → barrette | 3-6€/ml |
| Barrette de coupure | Sectionnement terre au tableau | 5-15€ |
| Borne principale de terre | Raccordement au tableau | 5-10€ |
| Liaison équipotentielle SDB (câble 4mm²) | Relier tous les éléments métalliques SDB | 1-2€/ml |

**Résistance de terre :** Max 100Ω (valeur mesurée). Idéal < 50Ω. Si > 100Ω → piquet supplémentaire ou amélioration du sol.

### Protection foudre (parafoudre)
| Type | Usage | Prix |
|------|-------|------|
| Parafoudre Type 2 monophasé | Habitat individuel (obligatoire zone AQ2) | 60-120€ |
| Parafoudre Type 2 triphasé | Habitat triphasé | 80-150€ |
| Parafoudre Type 1+2 combiné | Zone très exposée | 100-200€ |

---

## 10. ÉCLAIRAGE — TYPES ET PRIX FOURNITURE

### Types d'ampoules / sources LED
| Type | Culot | Puissance LED | Équiv. ancien | Flux lumineux | Prix unitaire |
|------|-------|-------------|---------------|--------------|--------------|
| Ampoule LED standard | E27 | 5-15W | 40-100W | 400-1500 lm | 2-8€ |
| Ampoule LED flamme | E14 | 3-7W | 25-60W | 250-800 lm | 2-8€ |
| Ampoule LED spot | GU10 | 3-7W | 35-50W halogène | 250-600 lm | 2-8€ |
| Ampoule LED spot | GU5.3 (MR16) | 3-7W | 35-50W | 250-600 lm | 3-10€ |
| Ampoule LED tube | G13 (T8) | 10-22W | 36-58W fluo | 900-2200 lm | 5-15€ |
| Spot LED encastré (complet) | - | 5-10W | 50W halogène | 400-800 lm | 5-20€ |
| Bandeau LED (au mètre) | - | 5-15W/m | - | 300-1500 lm/m | 5-25€/ml |
| Dalle LED 60x60 | - | 30-45W | 4x18W fluo | 3000-5000 lm | 20-60€ |

### Types de luminaires (fourniture)
| Type | Usage | Prix fourniture |
|------|-------|----------------|
| Plafonnier LED simple | Couloir, chambre, WC | 15-60€ |
| Plafonnier LED design | Séjour, chambre | 30-150€ |
| Suspension / lustre | Séjour, salle à manger | 20-300€ |
| Applique murale | Couloir, SDB, chambre | 15-80€ |
| Spot encastré LED (lot 3-6) | SDB, cuisine, couloir | 15-50€ le lot |
| Réglette LED (étanche ou pas) | Garage, cave, buanderie | 10-30€ |
| Hublot LED extérieur | Entrée, jardin | 15-50€ |
| Projecteur LED extérieur | Jardin, façade, parking | 15-80€ |
| Borne / potelet extérieur | Allée, jardin | 25-100€ |

### Règle puissance éclairage
| Pièce | Lux recommandés | Puissance LED indicative |
|-------|----------------|------------------------|
| Séjour | 300 lux | 3-5W/m² |
| Cuisine (plan de travail) | 400-500 lux | 5-8W/m² |
| Chambre | 150-200 lux | 2-3W/m² |
| SDB | 300-500 lux | 4-6W/m² |
| Bureau | 400-500 lux | 5-8W/m² |
| Couloir / entrée | 100-200 lux | 2-3W/m² |
| Garage | 200-300 lux | 3-5W/m² |
| Extérieur (éclairage ambiance) | 50-100 lux | 1-2W/m² |

---

## 11. CHAUFFAGE ÉLECTRIQUE — RADIATEURS DÉTAILLÉS

### Types de radiateurs et prix fourniture
| Type | Technologie | Prix fourniture (1000W) | Confort | Conso | Pièces adaptées |
|------|-----------|------------------------|---------|-------|----------------|
| Convecteur | Air chauffé par résistance | 30-200€ | ★☆☆ | ★★★ (gourmand) | Pièces peu utilisées, garage |
| Panneau rayonnant | Rayonnement infrarouge + convection | 100-600€ | ★★☆ | ★★☆ | Bureau, chambre |
| Radiateur à inertie sèche | Résistance + matériau réfractaire (céramique, fonte, pierre) | 250-1000€ | ★★★ | ★☆☆ (économe) | Séjour, chambre, toutes pièces |
| Radiateur à inertie fluide | Résistance + huile/fluide caloporteur | 200-800€ | ★★★ | ★☆☆ | Toutes pièces |
| Radiateur double cœur de chauffe | Inertie sèche + façade rayonnante | 400-1500€ | ★★★ | ★☆☆ | Séjour, pièces de vie (le meilleur) |
| Sèche-serviette électrique | Résistance + fluide ou sec | 100-600€ | ★★☆ | ★★☆ | SDB |
| Sèche-serviette mixte (eau+élec) | Raccordé chauffage central + résistance | 200-800€ | ★★★ | ★☆☆ | SDB avec chauffage central |

### Puissance recommandée par m²
| Isolation | Puissance nécessaire |
|-----------|---------------------|
| Logement ancien mal isolé | 100W/m² |
| Logement moyen (années 90-2000) | 80W/m² |
| Logement RT2012 | 50-60W/m² |
| Logement RE2020 / BBC | 40-50W/m² |

**Exemple :** Chambre 12m² isolée moyenne → 12 × 80 = 960W → choisir radiateur 1000W.

**Marques radiateurs électriques :** Atlantic (Galapagos, Divali), Thermor (Equateur, Mozart), Sauter (Malao, Bolero), Noirot (Calidou, Axiom), Acova (Atoll, Fassane), Rothelec, Campa

---

## 12. INTERPHONE, VISIOPHONE, SONNETTE

### Fourniture
| Type | Technologies | Prix fourniture |
|------|-------------|----------------|
| Sonnette filaire | Bouton + carillon | 10-30€ |
| Sonnette sans fil | Bouton RF + carillon | 15-50€ |
| Interphone audio filaire | Platine rue + combiné intérieur | 40-150€ |
| Interphone audio sans fil | Platine + combiné sans fil | 50-200€ |
| Visiophone filaire couleur | Platine caméra + écran 4-7" | 100-400€ |
| Visiophone filaire + digicode | Platine + écran + clavier | 200-600€ |
| Visiophone connecté (WiFi/smartphone) | Platine + écran + app mobile | 150-500€ |
| Gâche électrique | Ouverture porte à distance | 25-80€ |

### Prix pose
| Intervention | Prix MO HT |
|-------------|-----------|
| Pose sonnette filaire | 40-80€ |
| Pose sonnette sans fil | 20-40€ |
| Pose interphone audio (filaire, câblage existant) | 80-200€ |
| Pose interphone audio (filaire, création câblage) | 200-500€ |
| Pose visiophone (câblage existant) | 100-250€ |
| Pose visiophone (création câblage complet) | 300-800€ |
| Pose gâche électrique | 80-200€ |
| Pose portier d'immeuble (parties communes) | 500-1500€ |

Marques : Legrand, Aiphone, Extel, Ring, Bticino, Urmet, Dahua

---

## 13. ALARME ET DÉTECTION

### Fourniture
| Type | Composants | Prix fourniture |
|------|-----------|----------------|
| Kit alarme sans fil basique | Centrale + 2 détecteurs + 1 sirène + 1 télécommande | 150-400€ |
| Kit alarme sans fil pro | Centrale + 4 détecteurs + 1 sirène ext + 2 télécommandes + app | 300-800€ |
| Détecteur de mouvement (additionnel) | Infrarouge | 20-60€ |
| Détecteur d'ouverture (porte/fenêtre) | Magnétique | 15-40€ |
| Sirène intérieure | 85-110 dB | 20-50€ |
| Sirène extérieure avec flash | 95-110 dB + LED | 40-120€ |
| Caméra intérieure WiFi | HD 1080p + app | 30-100€ |
| Caméra extérieure WiFi | HD + vision nocturne + app | 50-200€ |
| Détecteur de fumée (DAAF) | **Obligatoire** (loi Morange) | 10-30€ |
| Détecteur de CO (monoxyde de carbone) | Recommandé si chauffage combustion | 20-50€ |

### Prix pose alarme
| Intervention | Prix MO HT |
|-------------|-----------|
| Pose kit alarme sans fil (basique) | 150-350€ |
| Pose kit alarme sans fil (pro, 6+ détecteurs) | 300-700€ |
| Pose alarme filaire (neuf, avec câblage) | 500-1500€ |
| Pose caméra intérieure | 40-80€ |
| Pose caméra extérieure | 60-150€ |
| Pose DAAF (détecteur fumée) | 15-30€ |

Marques : Somfy (Protect), Ajax Systems, Diagral, Visonic, Hikvision, Dahua, Ring, Netatmo

---

## 14. DOMOTIQUE ET CONNECTÉ

### Fourniture
| Type | Usage | Prix |
|------|-------|------|
| Prise connectée WiFi | Commande à distance appareil | 10-30€ |
| Interrupteur connecté (remplacement) | Commande éclairage via app | 25-60€ |
| Module Legrand/Schneider (connecté) | Remplacement mécanisme existant | 40-80€ |
| Volet roulant connecté (moteur + commande) | Commande via app/assistant vocal | 150-400€ (moteur seul) |
| Thermostat connecté | Pilotage chauffage à distance | 80-250€ |
| Hub domotique (box) | Centralisation protocoles (Zigbee, Z-Wave, WiFi) | 50-200€ |
| Serrure connectée | Ouverture porte via app/badge | 150-400€ |
| Détecteur de mouvement connecté | Éclairage/alarme auto | 25-60€ |

**Protocoles :** WiFi (le plus simple), Zigbee (basse conso, nécessite hub), Z-Wave (domotique avancée), Matter (nouveau standard universel 2024+), Bluetooth (courte portée).
**Marques :** Legrand (with Netatmo), Schneider (Wiser), Somfy (TaHoma), Philips Hue, Ikea (Dirigera), Google Home, Amazon Alexa, Apple HomeKit

---

## 15. CONSOMMABLES ET PETITES FOURNITURES — PRIX

| Fourniture | Usage | Prix |
|-----------|-------|------|
| Scotch isolant (rouleau) | Isolation provisoire, repérage | 1-3€ |
| Ruban adhésif bleu (repérage) | Marquage circuits | 1-2€ |
| Cheville Ø6 nylon (lot 100) | Fixation boîtier, chemin de câbles | 3-6€ |
| Cheville Ø8 nylon (lot 100) | Fixation lourde | 4-8€ |
| Vis Ø3.5x25 (lot 100) | Fixation appareillage | 3-5€ |
| Vis Ø4x40 (lot 100) | Fixation boîtier, support | 3-6€ |
| Plâtre rapide / MAP (sac 5kg) | Scellement boîtier dans maçonnerie | 5-10€ |
| Silicone gris (cartouche 300ml) | Étanchéité passage câbles | 4-8€ |
| Mousse polyuréthane (bombe 500ml) | Rebouchage passages de câbles | 5-10€ |
| Tire-fil nylon 20m | Passage fils dans gaine existante | 10-25€ |
| Aiguille tire-fil acier 30m | Passage fils dans gaine longue distance | 25-60€ |
| Marqueurs de câbles (lot) | Repérage circuits au tableau | 3-8€ |
| Huile de coude (illimité) | Fait avancer le chantier | Gratuit 😄 |

---

## 16. PISCINE / SPA — RACCORDEMENT ÉLECTRIQUE

| Intervention | Prix MO HT | Fourniture | Note |
|-------------|-----------|-----------|------|
| Raccordement piscine (pompe + éclairage + coffret) | 400-1000€ | 200-600€ | Circuit dédié, diff 30mA dédié, câble R2V enterré TPC |
| Raccordement spa / jacuzzi (monophasé) | 200-500€ | 50-150€ | Circuit dédié 20A ou 32A selon puissance |
| Raccordement spa / jacuzzi (triphasé) | 350-700€ | 100-250€ | |
| Pose coffret technique piscine | 100-250€ | 80-200€ | Coffret étanche IP55 avec disjoncteurs dédiés |
| Pose projecteur piscine LED | 80-150€ | 50-200€ | TBTS 12V obligatoire dans l'eau |
| Pose pompe à chaleur piscine (raccord élec) | 100-200€ | - | Circuit dédié + diff Type F |
| Liaison équipotentielle piscine | 100-250€ | 20-50€ | **Obligatoire** — relier toutes masses métalliques |

**Règles NFC 15-100 piscine :** Volume 0 (bassin) = TBTS 12V uniquement. Volume 1 (2m autour bassin, 2.5m haut) = TBTS, luminaires IPX5. Volume 2 (1.5m au-delà du vol.1) = appareils classe II, IP44. Diff 30mA dédié obligatoire pour tous les circuits piscine.

---

## 17. PANNEAUX PHOTOVOLTAÏQUES — PARTIE ÉLECTRIQUE

| Intervention | Prix MO HT | Note |
|-------------|-----------|------|
| Raccordement onduleur au tableau électrique | 150-400€ | Circuit dédié + disjoncteur + diff |
| Pose coffret AC/DC (protection solaire) | 100-250€ | Parafoudre DC + sectionneur + fusibles |
| Raccordement compteur Linky (injection réseau) | 100-300€ | Demande Enedis + Consuel obligatoire |
| Câblage panneaux → onduleur (courant continu) | 200-500€ | Câble solaire H1Z2Z2-K, connecteurs MC4 |
| Consuel photovoltaïque | 120-180€ | Obligatoire pour mise en service |

**Note :** L'électricien ne pose pas les panneaux (c'est le couvreur ou l'installateur PV). L'électricien fait le raccordement électrique : onduleur → tableau → compteur. Certification QUALIPV ou QUALIFELEC PV obligatoire pour les aides.

**TVA :** 10% pour installations ≤3kWc (≤6kWc si conditions). TVA 5.5% possible depuis oct 2025 sous conditions (arrêté sept 2025).

---

## 18. PARATONNERRE

| Type | Usage | Prix fourniture + pose |
|------|-------|----------------------|
| Paratonnerre à tige (Franklin) | Protection directe bâtiment | 1500-4000€ posé |
| Paratonnerre à dispositif d'amorçage (PDA) | Protection rayon élargi | 3000-8000€ posé |
| Parafoudre (dans le tableau) | Protection indirecte (surtension) | 60-200€ (voir section 9) |

**Obligation :** Le paratonnerre n'est PAS obligatoire pour les maisons individuelles. Le parafoudre dans le tableau EST obligatoire en zone AQ2 (zones orageuses). Le paratonnerre est obligatoire pour les ERP, IGH, et bâtiments > 28m.

---

## 19. MODULES TABLEAU AVANCÉS — DÉLESTEUR, COMPTEUR, GESTION ÉNERGIE

| Composant | Usage | Prix |
|-----------|-------|------|
| Délesteur (classique) | Coupe circuits non prioritaires si surconsommation | 80-200€ |
| Délesteur connecté (Legrand Drivia Netatmo) | Idem + pilotage app + gestion priorités | 150-350€ |
| Compteur d'énergie modulaire (monophasé) | Mesure conso par circuit ou globale | 40-100€ |
| Compteur d'énergie modulaire (triphasé) | Idem triphasé | 60-150€ |
| Compteur de production solaire | Mesure production PV | 50-120€ |
| Contacteur connecté (Legrand/Schneider) | Pilotage chauffe-eau, piscine, VE à distance | 60-150€ |
| Indicateur de consommation (écran déporté) | Affichage conso temps réel | 50-150€ |

**Note terrain :** Le délesteur est recommandé pour les installations avec beaucoup de postes énergivores (chauffage élec + VE + piscine) et un abonnement modéré. Évite l'augmentation de puissance souscrite.

---

## 20. ANTENNE TV / SATELLITE / TNT

| Intervention | Prix MO HT | Fourniture | Note |
|-------------|-----------|-----------|------|
| Pose antenne TNT (râteau) + orientation | 80-200€ | 30-80€ | Mât + antenne + câble coax |
| Pose antenne satellite (parabole) | 100-250€ | 50-150€ | Orientation + décodeur |
| Tirage câble coaxial (intérieur) | ml | 3-8€/ml | + prise TV |
| Pose prise TV coaxiale | u | 25-50€ | |
| Pose amplificateur d'antenne | u | 30-60€ | 20-50€ | Si signal faible |
| Pose répartiteur TV (multi-pièces) | u | 20-40€ | 10-25€ | |

---

## 21. COLONNE DE COMMUNICATION / RÉSEAU

| Intervention | Prix MO HT | Note |
|-------------|-----------|------|
| Tirage câble RJ45 Cat6 (intérieur) | 5-12€/ml | Dans gaine existante ou moulure |
| Pose prise RJ45 Cat6 | 35-70€/u | Boîtier + connecteur + platine |
| Pose coffret de communication Grade 1 | 60-120€ | Coffret + brassage + test |
| Pose coffret de communication Grade 3 | 100-250€ | Coffret + brassage + bandeau optique |
| Pose switch réseau (dans coffret) | 30-60€ | Raccordement + configuration basique |
| Pose point d'accès WiFi (encastré plafond) | 50-100€ | 60-200€ fourniture | Pour couverture WiFi pro |
| Passage fibre optique intérieur | 50-150€ | Tirage + raccordement ONT → coffret |

---

## 22. TABLEAU DIVISIONNAIRE / EXTENSION

| Intervention | Prix MO HT | Note |
|-------------|-----------|------|
| Pose tableau divisionnaire (garage, dépendance, extension) | 200-500€ | Coffret + DDR + disjoncteurs + câblage |
| Tirage câble alimentation tableau divisionnaire (R2V 3G10 ou 3G16) | 10-20€/ml | Depuis tableau principal |
| Raccordement tableau divisionnaire sur principal | 80-150€ | Disjoncteur dédié au principal |

---

## 23. DIAGNOSTIC ÉLECTRIQUE — DÉTAIL

| Type | Contexte | Prix | Obligatoire ? |
|------|----------|------|-------------- |
| Diagnostic électrique (vente) | Installation > 15 ans, vente bien immobilier | 80-150€ | **OUI** (loi) |
| Diagnostic électrique (location) | Installation > 15 ans, mise en location | 80-150€ | **OUI** (depuis 2018) |
| Diagnostic électrique (volontaire) | Vérification état installation | 80-150€ | Non |
| Durée de validité | Vente : 3 ans / Location : 6 ans | | |
| Réalisé par | Diagnostiqueur certifié (pas l'électricien) | | |

**Points contrôlés (87 points) :** Présence terre, DDR 30mA, protection surintensités, état câblage, respect volumes SDB, liaison équipotentielle, etc.

---

## 24. ERREURS COURANTES TERRAIN (pour le cerveau IA — aide au devis)

1. **Sous-dimensionnement câble** : Mettre du 1.5mm² pour des prises → disjonction, échauffement. Rappel : prises = 2.5mm² mini.
2. **Oubli circuit dédié plaque** : La plaque de cuisson DOIT avoir son propre circuit 32A en 6mm². Pas de prise standard.
3. **Oubli DDR Type A** : Plaque, lave-linge, lave-vaisselle, VE = **Type A obligatoire**, pas Type AC.
4. **Trop de disjoncteurs sous 1 DDR** : Max 8 disjoncteurs par interrupteur différentiel (NFC 15-100).
5. **Pas de répartition éclairage** : L'éclairage doit être sur au moins 2 circuits sous 2 DDR différents (sinon tout s'éteint en cas de défaut).
6. **Prises SDB dans mauvais volume** : Pas de prise dans volumes 0, 1 ni 2. Uniquement hors volume (>60cm du bord douche/baignoire).
7. **Câble au lieu de fil en gaine** : En intérieur, on tire du fil dans une gaine ICTA. Le câble R2V c'est pour l'extérieur ou quand on ne peut pas passer de gaine.
8. **Fil vert/jaune utilisé en phase** : Le vert/jaune est EXCLUSIVEMENT la terre. JAMAIS pour autre chose. Amende + danger mortel.
9. **Absence de DCL** : Tout point lumineux dans une pièce > 4m² DOIT avoir un DCL au plafond (NFC 15-100).
10. **Oubli liaison équipotentielle SDB** : Tous les éléments métalliques de la SDB (tuyaux, baignoire, bonde, huisserie métallique) doivent être reliés à la terre par un câble 4mm².
11. **Tableau trop petit** : Toujours prévoir 20% de réserve dans le tableau pour les futurs circuits. Un tableau plein = pas d'évolution possible.
12. **Pas de repérage** : Chaque disjoncteur doit être étiqueté (cuisine prises, chambre 1, SDB éclairage...). Obligatoire NFC 15-100.
13. **Oubli parafoudre zone AQ2** : Vérifier la zone géographique. En zone AQ2 (Sud-Est, Aquitaine, etc.), le parafoudre est OBLIGATOIRE.
