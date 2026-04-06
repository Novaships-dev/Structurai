# STRUCTORAI — RÉFÉRENTIEL TECHNIQUE PLOMBERIE
> Données techniques exhaustives pour le cerveau IA.
> Tuyaux, raccords, dimensions, outillage, sanitaires, carrelage/faïence.
> Ce fichier alimente le RAG et permet au cerveau de comprendre le langage artisan.
> Date : 03/04/2026

---

## 1. TYPES DE TUYAUX — ALIMENTATION EAU

### CUIVRE (traditionnel, durable)
```json
{
  "materiau": "Cuivre",
  "usage": ["Alimentation eau froide", "Alimentation eau chaude", "Chauffage"],
  "avantages": ["Antibactérien", "Recyclable", "Très durable (50+ ans)", "Résiste haute pression", "Esthétique en apparent"],
  "inconvenients": ["Prix élevé", "Nécessite brasure/soudure", "Sensible eau agressive (pH < 6.5)"],
  "conditionnement": ["Barres rigides 1m, 2m, 4m, 5m", "Couronnes recuites 10m, 25m, 50m"],
  "pression_max": "16 bars",
  "temperature_max": "250°C",
  "duree_vie": "50 ans+",
  "normes": "NF EN 1057",
  "diametres_courants": [
    {"ext_x_ep": "10x1", "usage": "Raccordement robinet d'arrêt, petit appareil"},
    {"ext_x_ep": "12x1", "usage": "Lavabo, lave-mains, bidet, lave-linge, lave-vaisselle"},
    {"ext_x_ep": "14x1", "usage": "Douche, baignoire (alimentation individuelle)"},
    {"ext_x_ep": "16x1", "usage": "Distribution principale étage, baignoire"},
    {"ext_x_ep": "18x1", "usage": "Colonne montante, distribution principale"},
    {"ext_x_ep": "22x1", "usage": "Colonne montante, nourrice, gros débit"},
    {"ext_x_ep": "28x1.5", "usage": "Alimentation générale, compteur → nourrice"}
  ],
  "raccords": {
    "a_souder_par_capillarite": "Raccord cuivre à braser (le plus courant). Brasure tendre (<450°C) ou forte (>450°C).",
    "a_visser": "Raccord laiton mâle ou femelle. Filetage + joint fibre ou PTFE.",
    "a_sertir": "Raccord cuivre à sertir (Viega, Geberit Mapress). Pince à sertir nécessaire. Rapide, sans flamme.",
    "a_compression": "Raccord olive + écrou. Pas besoin de soudure. Pour réparation ou raccordement rapide.",
    "bicone": "Raccord avec bague bicône en laiton. Serrage mécanique. Dépannage."
  },
  "outillage_specifique": [
    "Chalumeau bi-gaz (oxygène + acétylène) ou mono-gaz (propane/butane)",
    "Flux décapant + baguettes de brasure (tendre ou forte)",
    "Coupe-tube cuivre (Ø10 à Ø28)",
    "Ébavureur intérieur/extérieur",
    "Cintreuse à ressort ou cintreuse d'établi (Ø10 à Ø22)",
    "Pince à sertir manuelle ou électrique (si raccords à sertir)",
    "Pied à coulisse (vérification diamètres)",
    "Clé à molette, pince multiprise"
  ],
  "fournisseurs": ["Point P", "Cedeo", "Brossette", "Rexel"],
  "marques": ["KME", "Wieland", "Sanco", "Trefimetaux"],
  "prix_tubes": {
    "barre_ecroui": [
      {"diametre": "10x1", "prix_ml": {"min": 6, "max": 9}, "conditionnement": "barre 2m (~14€) ou 4m (~28€)"},
      {"diametre": "12x1", "prix_ml": {"min": 5, "max": 8}, "conditionnement": "barre 5m (~30€)"},
      {"diametre": "14x1", "prix_ml": {"min": 6, "max": 9}, "conditionnement": "barre 4m (~30€)"},
      {"diametre": "16x1", "prix_ml": {"min": 7, "max": 10}, "conditionnement": "barre 4m (~35€)"},
      {"diametre": "18x1", "prix_ml": {"min": 8, "max": 12}, "conditionnement": "barre 4m (~39€)"},
      {"diametre": "22x1", "prix_ml": {"min": 11, "max": 15}, "conditionnement": "barre 2m (~28€) ou 4m (~50€)"},
      {"diametre": "28x1.5", "prix_ml": {"min": 14, "max": 19}, "conditionnement": "barre 2m (~33€)"}
    ],
    "couronne_recuit": [
      {"diametre": "12x1", "prix_couronne": {"25m": {"min": 180, "max": 250}, "50m": {"min": 340, "max": 450}}},
      {"diametre": "14x1", "prix_couronne": {"25m": {"min": 200, "max": 280}, "50m": {"min": 380, "max": 480}}},
      {"diametre": "16x1", "prix_couronne": {"25m": {"min": 240, "max": 320}, "50m": {"min": 440, "max": 550}}},
      {"diametre": "18x1", "prix_couronne": {"25m": {"min": 280, "max": 380}}},
      {"diametre": "22x1", "prix_couronne": {"25m": {"min": 320, "max": 420}}}
    ],
    "note_prix": "Prix cuivre indexés sur cours matières premières — variations +/-20% possibles. Prix pro (Point P, Cedeo) environ 15-25% moins cher que Leroy Merlin/Castorama."
  },
  "prix_raccords_cuivre": {
    "a_souder": [
      {"type": "Manchon égal", "diametre": 14, "prix_unitaire": {"min": 0.30, "max": 0.80}},
      {"type": "Manchon égal", "diametre": 22, "prix_unitaire": {"min": 0.80, "max": 1.50}},
      {"type": "Coude 90° FF", "diametre": 14, "prix_unitaire": {"min": 0.50, "max": 1.20}},
      {"type": "Coude 90° FF", "diametre": 22, "prix_unitaire": {"min": 1.00, "max": 2.50}},
      {"type": "Té égal", "diametre": 14, "prix_unitaire": {"min": 0.80, "max": 1.50}},
      {"type": "Té égal", "diametre": 22, "prix_unitaire": {"min": 1.50, "max": 3.00}},
      {"type": "Réduction", "diametre": "22-14", "prix_unitaire": {"min": 1.00, "max": 2.50}},
      {"type": "Raccord 2 pièces M 1/2(15/21)-14", "prix_unitaire": {"min": 0.70, "max": 1.50}},
      {"type": "Raccord 2 pièces F 1/2(15/21)-14", "prix_unitaire": {"min": 0.80, "max": 1.80}},
      {"type": "Raccord 2 pièces M 3/4(20/27)-18", "prix_unitaire": {"min": 1.00, "max": 2.00}}
    ],
    "a_compression_olive": [
      {"type": "Raccord bicône droit M 1/2-14", "prix_unitaire": {"min": 2, "max": 5}},
      {"type": "Raccord bicône coude M 1/2-14", "prix_unitaire": {"min": 3, "max": 7}},
      {"type": "Raccord bicône droit M 1/2-16", "prix_unitaire": {"min": 2.50, "max": 6}}
    ],
    "consommables_brasure": [
      {"type": "Baguette brasure tendre (étain/argent)", "prix": "8-15€ le lot 10 baguettes"},
      {"type": "Flux décapant liquide 250ml", "prix": "5-10€"},
      {"type": "Brasure forte (cuivre-phosphore)", "prix": "12-25€ le lot 10 baguettes"},
      {"type": "Tresse à dessouder", "prix": "3-6€"},
      {"type": "Cartouche gaz propane", "prix": "8-15€"}
    ]
  }
}
```

### PER — Polyéthylène Réticulé (moderne, flexible)
```json
{
  "materiau": "PER (Polyéthylène Réticulé)",
  "usage": ["Alimentation eau froide", "Alimentation eau chaude", "Plancher chauffant"],
  "avantages": ["Flexible", "Pas de soudure", "Léger", "Pas de corrosion", "Économique", "Rapide à poser"],
  "inconvenients": ["Sensible aux UV (pas en apparent extérieur)", "Dilatation thermique", "Pas recyclable", "Mémoire de forme (reste courbé)"],
  "conditionnement": ["Couronnes : 10m, 25m, 50m, 100m", "Barres gainées"],
  "pression_max": "6 bars (10 bars selon fabricant)",
  "temperature_max": "60°C (pointes à 95°C)",
  "duree_vie": "25-50 ans",
  "normes": "NF EN ISO 15875",
  "diametres_courants": [
    {"ext_x_ep": "12x1.1", "dia_int": 9.8, "equiv_cuivre": "10", "usage": "WC, lave-mains, machine à laver"},
    {"ext_x_ep": "16x1.5", "dia_int": 13, "equiv_cuivre": "12-14", "usage": "Lavabo, douche, bidet"},
    {"ext_x_ep": "20x1.9", "dia_int": 16.2, "equiv_cuivre": "16-18", "usage": "Baignoire, distribution étage"},
    {"ext_x_ep": "25x2.3", "dia_int": 20.4, "equiv_cuivre": "22", "usage": "Colonne principale, nourrice"},
    {"ext_x_ep": "32x2.9", "dia_int": 26.2, "equiv_cuivre": "28", "usage": "Alimentation générale"}
  ],
  "raccords": {
    "a_glissement": "Le plus fiable. Bague de maintien glissée sur le tube, puis le tube est expansé et inséré sur le raccord. Nécessite pince à expansion + pince à glissement. Marques : Rehau, Uponor.",
    "a_compression": "Écrou + bague de serrage. Démontable. Plus simple mais moins fiable que glissement. Marques : Giacomini, Watts.",
    "a_sertir": "Pince à sertir mâchoire TH ou U selon profil. Non démontable. Rapide. Marques : Giacomini, Henco, Uponor."
  },
  "outillage_specifique": [
    "Coupe-tube PER (cisaille)",
    "Pince à expansion (si raccords à glissement) — Rehau, Uponor",
    "Pince à glissement (si raccords à glissement)",
    "Pince à sertir (si raccords à sertir) — profil TH ou U selon marque",
    "Calibreur/chanfreineur (obligatoire avant chaque raccord)",
    "Dérouleur de couronne"
  ],
  "fournisseurs": ["Point P", "Cedeo", "BigMat", "Leroy Merlin"],
  "marques": ["Rehau", "Uponor", "Giacomini", "Henco", "Comap"],
  "prix_tubes": {
    "couronne_nu": [
      {"diametre": "12x1.1", "prix_ml": {"min": 0.50, "max": 1.20}, "conditionnement": "couronne 25m (~15-25€) ou 100m (~50-90€)"},
      {"diametre": "16x1.5", "prix_ml": {"min": 0.70, "max": 1.50}, "conditionnement": "couronne 25m (~20-30€) ou 100m (~65-120€)"},
      {"diametre": "20x1.9", "prix_ml": {"min": 1.00, "max": 2.00}, "conditionnement": "couronne 25m (~28-45€) ou 100m (~90-160€)"},
      {"diametre": "25x2.3", "prix_ml": {"min": 1.50, "max": 3.00}, "conditionnement": "couronne 25m (~40-65€) ou 50m (~70-120€)"}
    ],
    "couronne_gaine": [
      {"diametre": "12 gainé", "prix_ml": {"min": 0.80, "max": 1.50}, "note": "Gaine bleue (EF) ou rouge (EC). Couronne 50m ou 100m."},
      {"diametre": "16 gainé", "prix_ml": {"min": 1.00, "max": 2.00}},
      {"diametre": "20 gainé", "prix_ml": {"min": 1.50, "max": 2.80}}
    ],
    "note_prix": "PER = le moins cher des tuyaux d'alimentation. Prix pro 20-30% moins cher que grande surface."
  },
  "prix_raccords_per": {
    "a_compression": [
      {"type": "Raccord droit mâle 1/2-16", "prix_unitaire": {"min": 2, "max": 5}},
      {"type": "Raccord droit femelle 1/2-16", "prix_unitaire": {"min": 2.50, "max": 6}},
      {"type": "Coude mâle 1/2-16", "prix_unitaire": {"min": 3, "max": 7}},
      {"type": "Té égal 16", "prix_unitaire": {"min": 4, "max": 8}},
      {"type": "Raccord droit mâle 3/4-20", "prix_unitaire": {"min": 3, "max": 7}},
      {"type": "Coude mâle 3/4-20", "prix_unitaire": {"min": 4, "max": 9}}
    ],
    "a_glissement": [
      {"type": "Raccord droit mâle 1/2-16 (Rehau/Uponor)", "prix_unitaire": {"min": 4, "max": 10}},
      {"type": "Coude mâle 1/2-16", "prix_unitaire": {"min": 5, "max": 12}},
      {"type": "Té égal 16", "prix_unitaire": {"min": 7, "max": 15}},
      {"note": "Raccords à glissement plus chers mais plus fiables que compression. Obligatoire pour encastrement."}
    ]
  }
}
```

### MULTICOUCHE (PER + aluminium, le meilleur compromis)
```json
{
  "materiau": "Multicouche (PER-AL-PER ou PE-X-AL-PE-X)",
  "usage": ["Alimentation eau froide", "Alimentation eau chaude", "Chauffage", "Climatisation"],
  "avantages": ["Garde la forme après cintrage (couche alu)", "Pas de dilatation", "Étanche à l'oxygène", "Bonnes performances acoustiques", "Pas de corrosion", "Pas de soudure"],
  "inconvenients": ["Plus cher que PER", "Plus lourd que PER", "Outillage spécifique obligatoire", "Pertes de charge aux raccords (insert interne réduit le diamètre)"],
  "conditionnement": ["Barres 3m, 5m", "Couronnes 10m, 25m, 50m, 100m"],
  "pression_max": "10 bars",
  "temperature_max": "95°C (pointes 110°C)",
  "duree_vie": "50 ans",
  "normes": "NF EN ISO 21003, certif NF 545",
  "composition": "Couche interne PER (contact eau) + Couche alu (étanchéité O2, forme) + Couche externe PER (protection)",
  "diametres_courants": [
    {"ext_x_ep": "16x2", "dia_int": 12, "equiv_cuivre": "14", "equiv_per": "12", "usage": "WC, lave-mains, lavabo, machine à laver"},
    {"ext_x_ep": "20x2", "dia_int": 16, "equiv_cuivre": "16-18", "equiv_per": "16", "usage": "Douche, baignoire, bidet"},
    {"ext_x_ep": "26x3", "dia_int": 20, "equiv_cuivre": "22", "equiv_per": "20", "usage": "Distribution étage, nourrice"},
    {"ext_x_ep": "32x3", "dia_int": 26, "equiv_cuivre": "28", "equiv_per": "25", "usage": "Colonne principale, alimentation générale"},
    {"ext_x_ep": "40x3.5", "dia_int": 33, "usage": "Gros débit, collectif"},
    {"ext_x_ep": "50x4", "dia_int": 42, "usage": "Colonne montante immeuble"}
  ],
  "equivalences_important": "ATTENTION : un multicouche Ø16 a un dia interne de 12mm seulement. Un PER Ø16 a un dia interne de 13mm. Un cuivre Ø14 a un dia interne de 12mm. Pour le même débit qu'un cuivre Ø14, prendre multicouche Ø16.",
  "raccords": {
    "a_sertir": "Le plus utilisé en pro. Douille inox + insert laiton + joints EPDM. Pince à sertir mâchoire TH ou U. NON démontable. Marques : Nicoll Fluxo, Giacomini, Henco, Comap, Geberit.",
    "a_compression": "Écrou + bague de serrage. Démontable. Pour réparation ou provisoire. Pas recommandé encastré.",
    "a_visser": "Raccord mixte multicouche → filetage laiton (mâle ou femelle). Pour raccordement sur réseau existant (cuivre, acier)."
  },
  "outillage_specifique": [
    "Coupe-tube multicouche (cisaille ou coupe-tube à molette)",
    "Calibreur/chanfreineur multicouche (OBLIGATOIRE — redresse le tube après coupe)",
    "Pince à sertir électrique ou manuelle — mâchoire TH (Henco, Giacomini) ou U (Comap, Uponor)",
    "ATTENTION : les mâchoires TH et U ne sont PAS interchangeables. Vérifier la compatibilité avec la marque de raccord.",
    "Cintreuse à ressort multicouche (rayon mini 5x le diamètre)",
    "Dérouleur de couronne",
    "Pied à coulisse"
  ],
  "fournisseurs": ["Point P", "Cedeo", "Brossette", "BigMat"],
  "marques": ["Nicoll (Fluxo)", "Giacomini", "Henco", "Comap", "Geberit (Mepla)", "Rehau"],
  "prix_tubes": {
    "couronne_nu": [
      {"diametre": "16x2", "prix_ml": {"min": 1.00, "max": 2.00}, "conditionnement": "couronne 25m (~30-45€), 50m (~55-90€), 100m (~100-170€)"},
      {"diametre": "20x2", "prix_ml": {"min": 1.50, "max": 2.80}, "conditionnement": "couronne 25m (~42-60€), 50m (~75-130€), 100m (~140-250€)"},
      {"diametre": "26x3", "prix_ml": {"min": 2.50, "max": 4.50}, "conditionnement": "couronne 25m (~70-100€), 50m (~130-200€)"},
      {"diametre": "32x3", "prix_ml": {"min": 4.00, "max": 7.00}, "conditionnement": "couronne 25m (~110-160€), 50m (~200-320€)"}
    ],
    "couronne_gaine": [
      {"diametre": "16 gainé", "prix_ml": {"min": 1.40, "max": 2.50}, "conditionnement": "couronne 50m (~79-110€) bleu ou rouge"},
      {"diametre": "20 gainé", "prix_ml": {"min": 2.00, "max": 3.50}, "conditionnement": "couronne 50m (~110-160€)"},
      {"diametre": "26 gainé", "prix_ml": {"min": 3.50, "max": 6.00}, "conditionnement": "couronne 50m (~180-300€)"}
    ],
    "barre": [
      {"diametre": "16x2", "prix_ml": {"min": 1.50, "max": 3.00}, "conditionnement": "barre 3m ou 5m"},
      {"diametre": "20x2", "prix_ml": {"min": 2.50, "max": 4.50}, "conditionnement": "barre 3m ou 5m"},
      {"diametre": "26x3", "prix_ml": {"min": 4.00, "max": 7.00}, "conditionnement": "barre 3m ou 5m (lot de 10/20 barres chez les pros)"}
    ],
    "note_prix": "Multicouche plus cher que PER mais moins cher que cuivre. Prix couronnes plus avantageux au mètre que barres. Prix pro Cedeo/Point P ~20-30% moins cher que Leroy Merlin."
  },
  "prix_raccords_multicouche": {
    "a_sertir_laiton": [
      {"type": "Raccord droit mâle 1/2(15/21) - Ø16", "prix_unitaire": {"min": 3, "max": 7}},
      {"type": "Raccord droit femelle 1/2(15/21) - Ø16", "prix_unitaire": {"min": 3.50, "max": 8}},
      {"type": "Coude mâle 1/2(15/21) - Ø16", "prix_unitaire": {"min": 4, "max": 9}},
      {"type": "Coude femelle 1/2(15/21) - Ø16", "prix_unitaire": {"min": 4, "max": 9}},
      {"type": "Coude 90° égal Ø16", "prix_unitaire": {"min": 4, "max": 8}},
      {"type": "Té égal Ø16", "prix_unitaire": {"min": 5, "max": 11}},
      {"type": "Té inégal Ø20-16-20", "prix_unitaire": {"min": 6, "max": 13}},
      {"type": "Réduction Ø20-16", "prix_unitaire": {"min": 4, "max": 9}},
      {"type": "Raccord droit mâle 3/4(20/27) - Ø20", "prix_unitaire": {"min": 5, "max": 10}},
      {"type": "Coude mâle 3/4(20/27) - Ø20", "prix_unitaire": {"min": 6, "max": 12}},
      {"type": "Té égal Ø20", "prix_unitaire": {"min": 7, "max": 14}},
      {"type": "Raccord droit mâle 1(26/34) - Ø26", "prix_unitaire": {"min": 8, "max": 16}},
      {"type": "Coude mâle 1(26/34) - Ø26", "prix_unitaire": {"min": 10, "max": 18}},
      {"type": "Té égal Ø26", "prix_unitaire": {"min": 12, "max": 22}},
      {"type": "Fixation murale double (robinetterie lavabo)", "prix_unitaire": {"min": 8, "max": 15}},
      {"type": "Fixation murale simple (robinet d'arrêt)", "prix_unitaire": {"min": 5, "max": 10}},
      {"note": "Prix unitaires HT. Lots de 10 ou 25 pièces = -15 à -25%. Marques pro (Giacomini, Henco) plus chères mais plus fiables que marques discount (Somatherm)."}
    ],
    "a_compression": [
      {"type": "Raccord droit mâle 1/2-Ø16 compression", "prix_unitaire": {"min": 4, "max": 9}},
      {"type": "Coude mâle 1/2-Ø16 compression", "prix_unitaire": {"min": 5, "max": 11}},
      {"type": "Té égal Ø16 compression", "prix_unitaire": {"min": 6, "max": 13}},
      {"note": "Compression légèrement plus cher à l'unité que sertir, mais pas besoin de pince à sertir (~300-2500€). Économique pour petits chantiers."}
    ],
    "collecteurs_nourrices": [
      {"type": "Collecteur 2 départs Ø16 + vannes", "prix_unitaire": {"min": 25, "max": 50}},
      {"type": "Collecteur 4 départs Ø16 + vannes", "prix_unitaire": {"min": 40, "max": 80}},
      {"type": "Collecteur 6 départs Ø16 + vannes", "prix_unitaire": {"min": 55, "max": 110}},
      {"type": "Collecteur 8 départs Ø16 + vannes", "prix_unitaire": {"min": 70, "max": 140}},
      {"note": "Nourrice = pièce centrale de la distribution en pieuvre. Choisir nombre de départs = nombre d'appareils. Marques : Giacomini, Caleffi, Watts, Comap."}
    ]
  },
  "prix_outillage_sertissage": {
    "pince_manuelle": {"prix": {"min": 200, "max": 500}, "note": "Mâchoires TH ou U selon marque. Ex: Somatherm ~70€ (entrée gamme), Rems ~300€ (pro), Viega ~450€ (haut de gamme)"},
    "pince_electrique": {"prix": {"min": 800, "max": 2500}, "note": "Ex: Virax ~900€, Rems ~1200€, Viega ~2000€, Geberit ~2200€. Amortie en 2-3 chantiers."},
    "jeu_machoires_supplementaire": {"prix": {"min": 50, "max": 150}, "note": "Prévoir mâchoires 16+20+26 minimum"},
    "coupe_tube_multicouche": {"prix": {"min": 15, "max": 40}},
    "calibreur_ebavureur": {"prix": {"min": 10, "max": 30}, "note": "Outil OBLIGATOIRE. Triple Ø16/20/26 = le plus pratique."}
  }
}
```

---

## 2. TUYAUX — ÉVACUATION

### PVC EU (Eaux Usées) — évacuation gravitaire
```json
{
  "materiau": "PVC EU (Eaux Usées)",
  "usage": ["Évacuation eaux usées", "Évacuation eaux vannes (WC)"],
  "couleur": "Gris",
  "assemblage": "Collage à la colle PVC ou joints à lèvre (emboîtement)",
  "pente_minimum": "1 cm/m (eaux usées) à 3 cm/m (WC)",
  "diametres_courants": [
    {"dn": 32, "usage": "Lavabo, lave-mains, bidet"},
    {"dn": 40, "usage": "Douche, baignoire, évier cuisine, lave-linge, lave-vaisselle"},
    {"dn": 50, "usage": "Évier cuisine double bac, colonne secondaire"},
    {"dn": 75, "usage": "Colonne secondaire, raccordement cuisine"},
    {"dn": 100, "usage": "WC, colonne principale, collecteur horizontal"},
    {"dn": 110, "usage": "WC (norme actuelle), colonne principale"},
    {"dn": 125, "usage": "Colonne de chute, ventilation primaire"},
    {"dn": 160, "usage": "Collecteur horizontal cave/vide sanitaire"},
    {"dn": 200, "usage": "Évacuation extérieure, raccordement tout-à-l'égout"}
  ],
  "raccords_types": [
    "Coude 30°, 45°, 87°30 (JAMAIS 90° en horizontal — risque bouchon)",
    "Culotte 45°, 67°30 (raccordement en Y)",
    "Té 87°30 (piquage 90° en vertical uniquement)",
    "Manchon (prolongation droite)",
    "Réduction (passage d'un diamètre à l'autre)",
    "Tampon de visite (accès pour débouchage)",
    "Siphon de sol, siphon de machine",
    "Bouchon (fermeture temporaire)"
  ],
  "outillage": [
    "Scie à métaux ou scie PVC",
    "Ébavureur",
    "Colle PVC (+ décapant/nettoyant obligatoire avant collage)",
    "Papier abrasif grain 120 (préparation surface avant collage)",
    "Niveau à bulle (vérification pente)",
    "Colliers isophoniques (fixation)"
  ],
  "fournisseurs": ["Point P", "BigMat", "Leroy Merlin"],
  "marques": ["Nicoll", "Girpi", "Wavin"],
  "prix_tubes_pvc": {
    "tubes_barre": [
      {"dn": 32, "prix_ml": {"min": 1.50, "max": 3.00}, "conditionnement": "barre 2m ou 4m"},
      {"dn": 40, "prix_ml": {"min": 2.00, "max": 4.00}, "conditionnement": "barre 2m ou 4m"},
      {"dn": 50, "prix_ml": {"min": 2.50, "max": 5.00}, "conditionnement": "barre 2m ou 4m"},
      {"dn": 75, "prix_ml": {"min": 3.50, "max": 6.00}, "conditionnement": "barre 4m"},
      {"dn": 100, "prix_ml": {"min": 4.00, "max": 8.00}, "conditionnement": "barre 4m (~20-28€)"},
      {"dn": 110, "prix_ml": {"min": 4.50, "max": 9.00}, "conditionnement": "barre 4m"},
      {"dn": 125, "prix_ml": {"min": 6.00, "max": 11.00}, "conditionnement": "barre 4m"},
      {"dn": 160, "prix_ml": {"min": 8.00, "max": 15.00}, "conditionnement": "barre 4m"}
    ],
    "note_prix": "PVC EU = le matériau le moins cher en évacuation. Prix stable (pas indexé sur cours matières premières comme le cuivre)."
  },
  "prix_raccords_pvc": {
    "a_coller": [
      {"type": "Coude 45° FF", "dn": 32, "prix_unitaire": {"min": 0.50, "max": 1.50}},
      {"type": "Coude 45° FF", "dn": 40, "prix_unitaire": {"min": 0.60, "max": 2.00}},
      {"type": "Coude 87°30 FF", "dn": 40, "prix_unitaire": {"min": 0.70, "max": 2.00}},
      {"type": "Coude 87°30 FF", "dn": 100, "prix_unitaire": {"min": 1.50, "max": 4.00}},
      {"type": "Culotte 45° FF", "dn": 40, "prix_unitaire": {"min": 1.00, "max": 3.00}},
      {"type": "Culotte 45° FF", "dn": 100, "prix_unitaire": {"min": 2.00, "max": 5.00}},
      {"type": "Manchon FF", "dn": 40, "prix_unitaire": {"min": 0.40, "max": 1.00}},
      {"type": "Manchon FF", "dn": 100, "prix_unitaire": {"min": 0.80, "max": 2.00}},
      {"type": "Réduction 40-32", "prix_unitaire": {"min": 0.80, "max": 2.00}},
      {"type": "Réduction 100-40", "prix_unitaire": {"min": 1.50, "max": 3.50}},
      {"type": "Tampon de visite", "dn": 100, "prix_unitaire": {"min": 3, "max": 7}},
      {"type": "Bouchon", "dn": 40, "prix_unitaire": {"min": 0.50, "max": 1.50}},
      {"type": "Bouchon", "dn": 100, "prix_unitaire": {"min": 1.00, "max": 3.00}}
    ],
    "consommables": [
      {"type": "Colle PVC pot 250ml", "prix": {"min": 5, "max": 12}},
      {"type": "Colle PVC pot 500ml", "prix": {"min": 8, "max": 18}},
      {"type": "Décapant/nettoyant PVC 250ml", "prix": {"min": 4, "max": 10}},
      {"type": "Papier abrasif grain 120 (rouleau)", "prix": {"min": 3, "max": 8}},
      {"note": "TOUJOURS décaper + nettoyer AVANT coller. Sinon le joint ne tient pas. Erreur n°1 des apprentis."}
    ],
    "siphons_bondes": [
      {"type": "Siphon lavabo (laiton)", "prix_unitaire": {"min": 8, "max": 25}},
      {"type": "Siphon lavabo (PVC)", "prix_unitaire": {"min": 3, "max": 10}},
      {"type": "Siphon baignoire à câble (Geberit)", "prix_unitaire": {"min": 30, "max": 70}},
      {"type": "Siphon douche extra-plat Ø90", "prix_unitaire": {"min": 15, "max": 40}},
      {"type": "Bonde de douche Ø90 (grille inox)", "prix_unitaire": {"min": 10, "max": 30}},
      {"type": "Caniveau de douche inox (30-90cm)", "prix_unitaire": {"min": 40, "max": 200}},
      {"type": "Siphon machine à laver encastré", "prix_unitaire": {"min": 10, "max": 25}}
    ]
  }
}
```

### PVC PRESSION — alimentation eau froide
```json
{
  "materiau": "PVC Pression",
  "usage": ["Alimentation eau froide UNIQUEMENT (pas d'eau chaude)", "Piscine", "Arrosage"],
  "couleur": "Gris foncé ou bleu",
  "pression_nominale": "PN 10 (10 bars) ou PN 16 (16 bars)",
  "assemblage": "Collage PVC pression (colle spéciale + décapant)",
  "temperature_max": "25°C (PAS pour eau chaude)",
  "diametres_courants": [
    {"dn": 20, "usage": "Petite alimentation"},
    {"dn": 25, "usage": "Alimentation individuelle"},
    {"dn": 32, "usage": "Distribution étage"},
    {"dn": 40, "usage": "Colonne"},
    {"dn": 50, "usage": "Alimentation générale"},
    {"dn": 63, "usage": "Réseau extérieur"}
  ],
  "attention": "NE PAS confondre avec PVC EU (pas la même résistance à la pression). PVC pression = paroi plus épaisse."
}
```

---

## 3. TABLE D'ÉQUIVALENCE DIAMÈTRES

| Appareil | Cuivre | PER | Multicouche | PVC évacuation |
|----------|--------|-----|-------------|----------------|
| WC / Lave-mains | 10 | 12 | 16 | 100-110 (évac) |
| Lavabo / Bidet | 12 | 12-16 | 16 | 32 (évac) |
| Douche | 14 | 16 | 20 | 40 (évac) |
| Baignoire | 14-16 | 16-20 | 20 | 40 (évac) |
| Évier cuisine | 12 | 12-16 | 16 | 40 (évac) |
| Machine à laver | 12 | 12 | 16 | 40 (évac) |
| Lave-vaisselle | 12 | 12 | 16 | 40 (évac) |
| Chauffe-eau | 16-18 | 20 | 20-26 | - |
| Nourrice / collecteur | 18-22 | 20-25 | 26 | - |
| Alimentation générale | 22-28 | 25-32 | 26-32 | - |

### Équivalence pouces ↔ mm (raccords acier/laiton)
| Pouces | Ancienne désignation | Diamètre ext tube acier | Usage courant |
|--------|---------------------|------------------------|---------------|
| 3/8" | 12/17 | 17.2mm | Petit appareil, robinet d'arrêt |
| 1/2" | 15/21 | 21.3mm | Lavabo, WC, robinetterie standard |
| 3/4" | 20/27 | 26.9mm | Douche, baignoire, distribution |
| 1" | 26/34 | 33.7mm | Colonne, nourrice, compteur |
| 1"1/4 | 33/42 | 42.4mm | Alimentation générale |
| 1"1/2 | 40/49 | 48.3mm | Gros débit |
| 2" | 50/60 | 60.3mm | Collecteur |

---

## 4. DIMENSIONS SANITAIRES STANDARD

### Receveurs de douche
| Format | Dimensions courantes | Usage type |
|--------|---------------------|------------|
| Carré | 70x70, 80x80, 90x90, 100x100 cm | Petite SDB (< 5m²), remplacement douche existante |
| Rectangle | 100x80, 120x80, 120x90, 140x90, 160x90, 180x90 cm | SDB moyenne à grande, remplacement baignoire |
| Quart de cercle | 80x80, 90x90, 100x100 cm | Optimisation angle, petite SDB |
| Extra-plat | Épaisseur 1.5 à 5 cm (vs 15cm receveur classique) | Douche italienne, accès PMR |
| À carreler | Sur mesure (polystyrène extrudé recoupable) | Douche italienne maçonnée, forme libre |

Matériaux receveur :
- **Céramique** : 100-300€, lourd, résistant, classique
- **Acrylique** : 80-250€, léger, économique, moins durable
- **Résine minérale** : 200-700€, sur mesure possible, extra-plat, haut de gamme
- **Acier émaillé** : 100-400€, solide, bon rapport qualité/prix (Kaldewei, Bette)

Marques : Ideal Standard, Kaldewei, Bette, Kinedo, Jacob Delafon, Wedi, Duravit

### Baignoires
| Type | Dimensions courantes | Usage |
|------|---------------------|-------|
| Droite standard | 150x70, 160x70, 170x70, 170x75, 180x80 cm | Standard, petit budget |
| Droite confort | 170x80, 180x80, 190x80 cm | Plus large, confort |
| D'angle | 120x120, 130x130, 140x140, 150x150 cm | Grande SDB, design |
| Asymétrique | 150x100, 160x85, 170x90 cm | Optimisation espace |
| Îlot | 170x75, 180x80 cm | Grande SDB, design haut de gamme |

Matériaux baignoire :
- **Acrylique** : 150-800€, léger, standard, bon rapport qualité/prix
- **Acier émaillé** : 200-1200€, solide, durable (Kaldewei, Bette)
- **Fonte** : 500-3000€, très lourd (~120kg vide), luxe, durée de vie 100 ans
- **Solid surface** : 1000-5000€, haut de gamme, Corian

### Parois de douche
| Type | Largeurs courantes | Épaisseur verre |
|------|-------------------|-----------------|
| Paroi fixe | 30, 40, 50, 60, 70, 80, 90, 100, 110, 120 cm | 6mm ou 8mm |
| Porte pivotante | 70, 80, 90, 100 cm | 6mm |
| Porte coulissante | 100, 110, 120, 140, 160 cm | 6mm ou 8mm |
| Porte pliante | 70, 80, 90, 100 cm | 4mm ou 6mm |
| Walk-in (sans porte) | 80, 90, 100, 110, 120, 140 cm | 8mm ou 10mm |

Hauteur standard : 190 cm ou 200 cm
Marques : Kinedo, SanSwiss, Schulte, Novellini, Paroi & Co

### WC
| Type | Dimensions au sol | Hauteur assise | Sortie |
|------|------------------|----------------|--------|
| WC posé classique | 35-40 x 60-70 cm | 40 cm | Horizontale ou verticale |
| WC suspendu | 35-40 x 50-55 cm (hors bâti) | 40-46 cm (réglable) | Horizontale (dans le mur) |
| Bâti-support suspendu | 50-60 x 15-20 cm (profondeur) | Hauteur bâti : 82, 98, 112 cm | Évacuation Ø90 ou Ø100 |

### Lavabos / Vasques
| Type | Dimensions courantes |
|------|---------------------|
| Lavabo standard | 50x40, 55x45, 60x48, 65x50 cm |
| Lave-mains (petit) | 25x18, 30x20, 38x24, 40x25 cm |
| Vasque à poser | Ø30, Ø35, Ø40, Ø42, Ø45 cm (ronde) / 40x30 à 60x40 cm (rect) |
| Vasque à encastrer | 50x40, 55x40, 60x45 cm |
| Double vasque | 100x48, 120x48, 140x48 cm (meuble) |

---

## 5. CARRELAGE & FAÏENCE

### Formats carrelage sol courants
| Format | Usage type | Note |
|--------|-----------|------|
| 20x20 cm | Mosaïque, petites pièces | Classique, beaucoup de joints |
| 30x30 cm | Sol standard, cuisine | Format traditionnel |
| 33x33 cm | Sol standard | |
| 45x45 cm | Sol SDB, cuisine | Bon compromis |
| 60x60 cm | Sol SDB moderne, séjour | Grand format, peu de joints |
| 60x120 cm | Sol tendance, effet dalle | Nécessite sol bien plan |
| 80x80 cm | Sol design, grands espaces | Très tendance 2025-2026 |
| 120x120 cm | Sol XXL, haut de gamme | Pose par 2 personnes |
| 30x60 cm | Sol ou mur | Format rectangulaire classique |

### Formats faïence murale courants
| Format | Usage type | Note |
|--------|-----------|------|
| 10x10 cm | Crédence cuisine, rétro | Beaucoup de joints |
| 15x15 cm | Crédence, salle d'eau | |
| 20x20 cm | Mur standard | |
| 25x40 cm | Faïence murale classique SDB | Le plus courant |
| 30x60 cm | Faïence murale moderne | Tendance, pose verticale ou horizontale |
| 30x90 cm | Faïence murale grand format | Peu de joints, effet design |
| Mosaïque (2x2, 5x5 cm sur trame) | Frise, niche douche, crédence | Sur trame pour pose facile |

### Prix carrelage (fourniture seule)
| Gamme | Prix €/m² | Exemples |
|-------|----------|---------|
| Entrée de gamme | 10-25€ | Grès cérame standard, faïence basique |
| Milieu de gamme | 25-60€ | Grès cérame émaillé, imitation bois/pierre |
| Haut de gamme | 60-150€ | Grès cérame grand format, pierre naturelle |
| Luxe | 150-300€+ | Marbre, travertin, zellige artisanal |

### Prix pose carrelage (main d'oeuvre)
| Type | Prix €HT/m² | Note |
|------|------------|------|
| Pose sol droite | 30-50€ | Pose la plus simple |
| Pose sol diagonale | 35-55€ | +10% environ |
| Pose sol grand format (60x60+) | 40-65€ | Nécessite double encollage |
| Pose murale (faïence) | 35-55€ | Plus technique que sol |
| Pose mosaïque | 50-80€ | Long, minutieux |
| Plinthes carrelage | 8-15€/ml | |
| Joints époxy | +10-15€/m² | Plus résistant que joint ciment |

Fournisseurs carrelage : Point P (Panofrance), Cedeo, Leroy Merlin, Castorama, La Plateforme du Bâtiment, négociants spécialisés (Carrelages Dépôt, etc.)

---

## 6. OUTILLAGE PLOMBIER — INVENTAIRE COMPLET

### Outillage de base (tout plombier)
| Outil | Usage | Prix indicatif |
|-------|-------|---------------|
| Clé à molette (jeu 3 tailles) | Serrage raccords | 20-60€ |
| Pince multiprise (Knipex Cobra) | Serrage, desserrage | 25-50€ |
| Clé à griffe / clé serre-tube | Tuyaux acier | 30-60€ |
| Coupe-tube cuivre mini + gros | Coupe tubes cuivre | 15-40€ |
| Coupe-tube PER / multicouche | Coupe tubes plastiques | 15-35€ |
| Ébavureur intérieur/extérieur | Préparer les coupes | 10-20€ |
| Calibreur multicouche | Reformer le tube après coupe | 15-30€ |
| Niveau à bulle (30cm + 60cm) | Vérification pente évacuation | 15-40€ |
| Mètre ruban 5m | Mesures | 5-15€ |
| Crayon de chantier | Traçage | 2€ |
| Ruban PTFE (téflon) | Étanchéité filetages | 2-5€ |
| Filasse + pâte à joint | Étanchéité filetages (pro) | 5-15€ |
| Joint fibre + joint caoutchouc (assortiment) | Étanchéité raccords | 10-20€ |

### Outillage spécialisé
| Outil | Usage | Prix indicatif |
|-------|-------|---------------|
| Chalumeau bi-gaz (oxygène/acétylène) | Brasure cuivre (tendre et forte) | 80-200€ |
| Poste à souder mono-gaz (propane) | Brasure tendre cuivre | 40-80€ |
| Pince à sertir multicouche (manuelle) | Sertissage raccords multicouche | 200-500€ |
| Pince à sertir multicouche (électrique) | Sertissage rapide, gros chantiers | 800-2500€ |
| Pince à expansion PER (Rehau/Uponor) | Raccords à glissement PER | 300-800€ |
| Cintreuse à ressort (jeu) | Cintrage cuivre/multicouche | 20-60€ |
| Cintreuse d'établi / arbalète | Cintrage précis cuivre gros Ø | 100-300€ |
| Pompe d'épreuve (manuelle ou élec) | Test d'étanchéité sous pression | 100-300€ |
| Caméra d'inspection | Recherche de fuite | 200-2000€ |
| Furet manuel / électrique | Débouchage canalisation | 30-300€ |
| Ventouse force 10 | Débouchage WC/évier | 10-30€ |

### Outillage électroportatif (partagé tous corps de métier)
| Outil | Usage | Prix indicatif |
|-------|-------|---------------|
| Perforateur / burineur SDS+ | Percement béton, saignées | 150-400€ |
| Visseuse-perceuse 18V | Percement, vissage | 100-300€ |
| Rainureuse / Scie à disque | Saignées mur pour tuyaux | 200-500€ |
| Meuleuse Ø125 | Coupe PVC, acier, carrelage | 50-150€ |
| Scie sauteuse | Découpe placo, bois | 50-150€ |
| Aspirateur de chantier | Nettoyage | 80-250€ |

Marques outillage pro : Knipex, Virax, Rothenberger, Geberit, Ridgid, Rems, Milwaukee, Makita, Bosch Pro, Hilti, DeWalt

---

> **Note :** Ce référentiel est le socle technique. Le cerveau IA l'utilise pour :
> 1. Comprendre quand l'artisan dit "du 16 multicouche" → il sait que c'est Ø16 ext, Ø12 int, équivalent cuivre Ø14
> 2. Proposer les bons diamètres dans un devis selon l'appareil (douche → multicouche Ø20, WC → multicouche Ø16)
> 3. Calculer les quantités de tuyaux, raccords, colle, joints
> 4. Connaître les marques et fournisseurs habituels
> 5. Estimer la durée de pose en fonction du type de raccord (sertissage plus rapide que brasure cuivre)
> 6. Détecter les erreurs : "Vous avez mis du Ø16 pour la baignoire — il faut du Ø20 minimum pour un bon débit"

---

## 7. JOINTS — TYPES, DIMENSIONS, USAGE, PRIX

### Joint fibre (le plus courant en plomberie)
| Dimension (pouces) | Dimension (mm) | Usage | Prix unitaire | Prix lot |
|-------|------|-------|--------------|----------|
| 1/4" | 8x13 | Petit robinet, purge | 0.03-0.08€ | Lot 100 : 2-4€ |
| 3/8" | 12x17 | Robinet WC, lavabo, machine à laver | 0.04-0.10€ | Lot 100 : 3-5€ |
| 1/2" | 15x21 | Robinetterie standard, raccords sanitaires | 0.05-0.12€ | Lot 100 : 4-7€ |
| 3/4" | 20x27 | Douche, baignoire, distribution | 0.06-0.15€ | Lot 50 : 3-5€ |
| 1" | 26x34 | Colonne, nourrice, compteur | 0.08-0.20€ | Lot 75 : 5-8€ |
| 1"1/4 | 33x42 | Alimentation générale | 0.10-0.25€ | Lot 50 : 5-8€ |
| 1"1/2 | 40x49 | Gros débit | 0.12-0.30€ | Lot 25 : 4-6€ |
| 2" | 50x60 | Collecteur | 0.15-0.40€ | Lot 25 : 5-8€ |

**Épaisseurs :** ECO = 1.5mm (eau froide/tiède, usage courant). PRO = 2mm (eau chaude, chauffage, plus résistant). BAU Bleu = 2mm (eau, gaz, fuel, chimie).
**Coffret assortiment :** Coffret 100 joints fibre multi-dimensions (Comap) : 8-15€ — **indispensable dans la caisse à outils**.
**Marques :** Comap, Watts, Noyon & Thiebault, Sferaco

### Joint caoutchouc (EPDM)
| Dimension | Usage | Prix unitaire |
|-----------|-------|--------------|
| 3/8" (12x17) | Raccords portée plate, eau froide/tiède | 0.05-0.15€ |
| 1/2" (15/21) | Raccords portée plate, eau froide/tiède | 0.06-0.18€ |
| 3/4" (20/27) | Raccords portée plate | 0.08-0.20€ |
| 1" (26/34) | Raccords portée plate | 0.10-0.25€ |
| Ø40 plat (siphon) | Siphon lavabo, évier, baignoire | 0.20-0.50€ |
| Ø50 plat (siphon) | Siphon évier double bac | 0.25-0.60€ |

**Usage :** Eau froide et mitigée (max 70°C). PAS pour eau chaude > 70°C (utiliser fibre PRO).
**Coffret assortiment :** 15-20€ pour coffret multi-dimensions

### Joint torique (NBR)
| Usage | Dimensions courantes | Prix |
|-------|---------------------|------|
| Cartouche mitigeur | Ø spécifique par marque | 0.30-1€ pièce |
| Tête de robinet à clapet | Ø10 à Ø18 | 0.20-0.80€ pièce |
| Inverseur bain/douche | Ø spécifique | 0.30-1€ pièce |
| Bec de robinet | Ø spécifique | 0.20-0.60€ pièce |
| Flexible | Ø spécifique | 0.15-0.50€ pièce |

**Coffret joints toriques assortis :** 419 pièces / 32 tailles : 8-15€ — **indispensable dépannage**
**Matière :** NBR (nitrile butadiène) pour robinetterie. EPDM pour eau chaude. Silicone pour haute température.

### Étanchéité filetage
| Produit | Usage | Prix |
|---------|-------|------|
| Ruban PTFE (téflon) 12m x 12mm | Filetage eau froide/chaude, basique | 0.50-2€ le rouleau |
| Filasse (chanvre) + pâte à joint | Filetage pro, plus fiable que PTFE | Filasse 5-8€ bobine + pâte 3-6€ pot |
| Résine anaérobie (Loctite 577) | Filetage métal permanent | 12-20€ le tube |
| Pâte d'étanchéité Geb | Filetage, raccord, réparation | 5-10€ le tube |

**Règle terrain :** Les pros utilisent filasse + pâte (plus fiable, plus durable). Le PTFE c'est pour le dépannage rapide ou les bricoleurs.

---

## 8. ROBINETS & VANNES

### Robinets d'arrêt
| Type | Dimensions | Usage | Prix fourniture |
|------|-----------|-------|----------------|
| Robinet d'arrêt droit 1/4 tour | 3/8" (12/17) | WC, lave-mains | 3-8€ |
| Robinet d'arrêt droit 1/4 tour | 1/2" (15/21) | Lavabo, douche | 4-10€ |
| Robinet d'arrêt droit 1/4 tour | 3/4" (20/27) | Baignoire, distribution | 5-12€ |
| Robinet d'arrêt équerre 1/4 tour | 3/8" (12/17) | WC (le plus courant) | 3-7€ |
| Robinet d'arrêt équerre chromé | 3/8" (12/17) | WC/lavabo (visible) | 5-12€ |
| Robinet d'arrêt avec filtre intégré | 1/2" (15/21) | Protection robinetterie | 8-18€ |
| Robinet de machine à laver équerre | M15/21 - M20/27 | Machine à laver/lave-vaisselle | 5-12€ |
| Robinet autoperceur | - | Raccordement rapide sur cuivre (dépannage) | 8-15€ |

**Marques :** Noyon & Thiebault, Sferaco, Somatherm, Comap, GRK Pro

### Vannes à boisseau sphérique (passage intégral)
| Raccordement | Dimensions | Usage | Prix fourniture |
|-------------|-----------|-------|----------------|
| Vanne FF (femelle-femelle) | 3/8" (12/17) | Isolement petit appareil | 3-8€ |
| Vanne FF | 1/2" (15/21) | Isolement sanitaire | 4-10€ |
| Vanne FF | 3/4" (20/27) | Isolement circuit, distribution | 5-12€ |
| Vanne FF | 1" (26/34) | Colonne, arrivée générale | 7-16€ |
| Vanne FF | 1"1/4 (33/42) | Compteur, arrivée générale | 10-22€ |
| Vanne MF (mâle-femelle) | 1/2" à 1" | Selon configuration | +1-2€ vs FF |
| Vanne MM (mâle-mâle) | 1/2" à 1" | Selon configuration | +1-2€ vs FF |
| Vanne avec purge | 1/2" à 1" | Circuit chauffage (vidange) | +2-5€ vs standard |
| Vanne papillon (passage intégral) | 3/8" à 2" | Pro, meilleur débit | +3-8€ vs standard |

**Pression :** PN25 (standard) à PN40 (pro, NF). **Température max :** 90°C (standard), 180°C (solaire/inox).
**Certif :** ACS obligatoire pour eau potable. NF recommandé (Pronorm/Somatherm).

### Clarinettes / Collecteurs de distribution
| Type | Nb sorties | Dimensions entrée/sortie | Prix |
|------|-----------|-------------------------|------|
| Clarinette laiton simple | 2 sorties | Entrée 3/4" → sorties 1/2" | 8-15€ |
| Clarinette laiton simple | 3 sorties | Entrée 3/4" → sorties 1/2" | 10-20€ |
| Clarinette laiton simple | 4 sorties | Entrée 3/4" → sorties 1/2" | 12-25€ |
| Clarinette laiton simple | 5 sorties | Entrée 1" → sorties 1/2" | 15-30€ |
| Clarinette avec vannes intégrées | 2 sorties | Entrée 3/4" → sorties 1/2" | 15-30€ |
| Clarinette avec vannes intégrées | 4 sorties | Entrée 3/4" → sorties 1/2" | 25-50€ |
| Clarinette avec vannes intégrées | 6 sorties | Entrée 1" → sorties 1/2" | 35-70€ |
| Nourrice multicouche avec vannes | 4 sorties Ø16 | Entrée 3/4" | 40-80€ |
| Nourrice multicouche avec vannes | 6 sorties Ø16 | Entrée 3/4" | 55-110€ |
| Nourrice multicouche avec vannes | 8 sorties Ø16 | Entrée 1" | 70-140€ |

**Usage :** La clarinette (ou nourrice) est le point central de distribution. Chaque appareil sanitaire a sa propre alimentation depuis la nourrice. Permet d'isoler chaque appareil individuellement.
**Marques :** Giacomini, Caleffi, Watts, Comap, Somatherm

---

## 9. CHAUFFE-EAU — TYPES, CAPACITÉS, SPÉCIFICITÉS, PRIX

### Chauffe-eau électrique (cumulus / ballon d'eau chaude)
| Capacité | Nb personnes | Dimensions approx (H×Ø) | Poids à vide / plein | Prix fourniture | Usage |
|----------|-------------|------------------------|---------------------|----------------|-------|
| 50L | 1 personne | 57×50 cm (compact) | 15kg / 65kg | 150-300€ | Studio, point d'eau isolé |
| 75L | 1-2 personnes | 75×50 cm | 20kg / 95kg | 180-350€ | Petit appartement |
| 100L | 2-3 personnes | 90×50 cm | 25kg / 125kg | 200-400€ | Appartement |
| 150L | 3-4 personnes | 110×53 cm | 30kg / 180kg | 250-500€ | Appartement/maison |
| 200L | 4-5 personnes | 130×55 cm | 40kg / 240kg | 300-600€ | Maison |
| 250L | 5-6 personnes | 145×57 cm | 45kg / 295kg | 350-700€ | Grande maison |
| 300L | 6+ personnes | 160×60 cm | 55kg / 355kg | 400-800€ | Grande famille |

**Types de pose :**
- **Vertical mural** (le plus courant) : fixé au mur par 2 pattes. Mur porteur obligatoire pour > 100L.
- **Vertical sur socle** (trépied) : posé au sol sur trépied. Pour murs non porteurs ou > 200L.
- **Horizontal mural** : fixé au mur horizontal (sous plafond). Prend moins de place en hauteur. +50-100€ vs vertical.
- **Stable (sur pieds)** : posé au sol directement. Pour les très gros volumes (200-300L).

**Support et fixation :**
| Accessoire | Usage | Prix |
|-----------|-------|------|
| Trépied / support au sol | Chauffe-eau vertical sur sol (>150L ou mur non porteur) | 30-80€ |
| Kit de fixation murale (chevilles + tiges filetées) | Chauffe-eau mural | 10-25€ (souvent inclus) |
| Tablette + pieds | Surélever le chauffe-eau | 25-50€ |
| Console murale renforcée | Mur en placo (avec renfort) | 15-35€ |
| Sangle anti-sismique | Obligatoire en zone sismique | 15-25€ |

**Accessoires obligatoires :**
| Accessoire | Usage | Prix |
|-----------|-------|------|
| Groupe de sécurité (NF) | Obligatoire, protège contre surpression. Se change à chaque remplacement chauffe-eau. | 12-35€ |
| Siphon de groupe de sécurité | Évacuation eau de vidange du groupe | 5-15€ |
| Flexible inox alimentation EF | Raccordement arrivée eau froide | 5-15€ |
| Flexible inox sortie EC | Raccordement sortie eau chaude | 5-15€ |
| Raccord diélectrique | Évite corrosion galvanique (cuivre/acier) | 5-12€ la paire |
| Vase d'expansion sanitaire | Recommandé (absorbe dilatation eau) | 30-60€ |
| Mitigeur thermostatique de sécurité | Obligatoire si > 50°C en sortie (anti-brûlure) | 40-80€ |

**Marques principales :** Atlantic (Zeneo, Linéo, Vizengo), Thermor (Duralis, Malicio), De Dietrich, Ariston (Velis, Pro), Chaffoteaux

### Chauffe-eau thermodynamique (CET)
| Capacité | Nb personnes | Prix fourniture | TVA | Aides |
|----------|-------------|----------------|-----|-------|
| 200L | 3-4 personnes | 1500-2500€ | 5.5% | MaPrimeRénov jusqu'à 1200€ |
| 250L | 4-5 personnes | 1800-3000€ | 5.5% | MaPrimeRénov jusqu'à 1200€ |
| 270L | 5-6 personnes | 2000-3500€ | 5.5% | MaPrimeRénov jusqu'à 1200€ |

**Spécificités :** Volume de local ventilé min 20m³ requis. Bruit ~40-50 dB (attention placement). COP moyen 2.5-3.5 (économise 60-75% vs électrique). Artisan RGE obligatoire pour les aides.

### Chauffe-eau instantané (sans réservoir)
| Type | Puissance | Usage | Prix |
|------|-----------|-------|------|
| Électrique sous évier | 2-5 kW | Point d'eau isolé (lave-mains, évier) | 100-250€ |
| Électrique douche | 7-11 kW | Douche uniquement | 200-500€ |
| Gaz instantané | 17-28 kW | Multi-points | 250-800€ |

---

## 10. SANI-BROYEUR / WC BROYEUR

| Type | Usage | Prix fourniture | Bruit | Note |
|------|-------|----------------|-------|------|
| Sani-broyeur compact | WC uniquement | 200-400€ | 45-55 dB | Le plus courant. SFA Sanibroyeur Pro. |
| Sani-broyeur + lavabo | WC + lavabo | 300-500€ | 45-55 dB | 2 entrées (WC + 1 appareil) |
| Sani-broyeur multifonction | WC + lavabo + douche + bidet | 400-700€ | 50-60 dB | 3-4 entrées. SFA Sanipro, Sanibest Pro. |
| Pompe de relevage seule | Douche, lavabo, machine (sans WC) | 150-350€ | 35-45 dB | SFA Sanidouche, Sanivite. Pas de broyage. |

**Marques :** SFA (leader, inventeur du sani-broyeur), Setma (W12P), Aquasani
**Spécificités :**
- Évacuation Ø22-32mm (vs Ø100mm pour WC classique) → permet d'installer un WC n'importe où
- Raccordement électrique 230V obligatoire (prise dédiée)
- Entretien : détartrage 2x/an, ne JAMAIS jeter de lingettes/coton-tiges
- Durée de vie : 5-15 ans selon usage et entretien
- **Attention :** non recommandé en résidence principale si WC classique possible. Bruit + entretien + panne = contrainte.

---

## 11. CHASSE D'EAU & KITS REMPLACEMENT

### Mécanisme de chasse d'eau
| Type | Usage | Prix |
|------|-------|------|
| Mécanisme complet simple volume | WC standard ancienne génération | 10-25€ |
| Mécanisme complet double chasse (3/6L) | WC standard (norme actuelle NF) | 15-40€ |
| Mécanisme complet universel | Remplacement sans démonter le réservoir | 20-45€ |
| Robinet flotteur seul | Remplacement flotteur uniquement | 8-20€ |
| Clapet / joint de soupape | Fuite entre réservoir et cuvette | 3-8€ |
| Kit joint + vis fixation réservoir | Réservoir qui fuit à la base | 5-12€ |
| Bouton poussoir chasse (chrome) | Remplacement bouton cassé | 5-15€ |
| Plaque de commande WC suspendu | Geberit Sigma 01 (blanc) | 30-50€ |
| Plaque de commande WC suspendu | Geberit Sigma 20 (chromé) | 50-80€ |
| Plaque de commande WC suspendu | Geberit Sigma 30 (design) | 60-120€ |
| Plaque de commande WC suspendu | Grohe Skate Cosmopolitan | 40-100€ |

**Marques mécanisme :** Geberit (le leader), Wirquin (bon rapport qualité/prix), Siamp (classique), Grohe (pour Grohe/DAL)
**Kit complet remplacement :** Mécanisme + flotteur + joint = 25-50€ (Wirquin Jollyflush, Geberit Type 290)

### Flexible de raccordement
| Type | Longueur | Usage | Prix |
|------|----------|-------|------|
| Flexible inox F3/8 - F3/8 | 30, 40, 50 cm | WC (réservoir → robinet d'arrêt) | 3-8€ |
| Flexible inox F3/8 - F1/2 | 30, 40, 50 cm | Lavabo (robinetterie → alimentation) | 3-10€ |
| Flexible inox F3/8 - M10/100 | 30, 40, 50 cm | Robinet lavabo (raccord spécial) | 4-10€ |
| Flexible inox F1/2 - F1/2 | 50, 80, 100 cm | Douche, baignoire | 4-12€ |
| Flexible machine à laver | 150, 200 cm | Machine à laver, lave-vaisselle | 5-15€ |
| Flexible inox coudé | 30-50 cm | Espace restreint (sous évier) | 5-12€ |

**Marques :** Noyon & Thiebault, Watts, Somatherm, Tucai
**Attention :** Remplacer les flexibles tous les 8-10 ans. Flexible qui pète = dégât des eaux garanti.

---

> **Note enrichie :** Ce référentiel couvre maintenant l'intégralité de la chaîne plomberie :
> 1. Tuyaux (cuivre, PER, multicouche, PVC EU, PVC pression) avec prix au mètre et par couronne
> 2. Raccords (à souder, sertir, compression, coller) avec prix unitaires
> 3. Joints (fibre, caoutchouc, torique) avec toutes les dimensions et prix
> 4. Robinets et vannes (arrêt, 1/4 tour, boisseau sphérique) avec dimensions et prix
> 5. Clarinettes et nourrices avec nombre de sorties et prix
> 6. Chauffe-eau (électrique, thermo, instantané) avec capacités, dimensions, poids, accessoires obligatoires
> 7. Sani-broyeur avec types et spécificités
> 8. Chasse d'eau et mécanismes avec kits remplacement et plaques de commande
> 9. Flexibles de raccordement
> 10. Dimensions sanitaires (receveurs, baignoires, parois, WC, vasques)
> 11. Carrelage et faïence (formats, prix fourniture et pose)
> 12. Outillage complet avec prix
> 13. Mises en oeuvre complètes avec prix (section 12)

---

## 12. MISES EN OEUVRE — TOUTES LES INTERVENTIONS PLOMBERIE AVEC PRIX

### A. DÉPOSE (démontage de l'existant)
| Intervention | Unité | Prix MO HT | Durée | Note terrain |
|-------------|-------|-----------|-------|-------------|
| Dépose baignoire acrylique | u | 100-180€ | 1-2h | Découpe possible pour faciliter l'évacuation |
| Dépose baignoire fonte | u | 150-250€ | 2-3h | Très lourde (~100kg). Prévoir 2 personnes. Benne obligatoire. |
| Dépose receveur douche | u | 80-150€ | 1-2h | Attention à ne pas abîmer les évacuations |
| Dépose cabine de douche intégrale | u | 120-200€ | 1.5-3h | Démontage parois + bac + plomberie |
| Dépose WC posé au sol | u | 50-100€ | 0.5-1h | Fermer eau, vider, dévisser, reboucher évac |
| Dépose WC suspendu + bâti-support | u | 100-200€ | 1.5-3h | Démontage coffrage placo inclus |
| Dépose lavabo / vasque sur meuble | u | 50-100€ | 0.5-1h | Déconnexion alimentation + siphon |
| Dépose meuble vasque complet | u | 60-120€ | 0.5-1.5h | Meuble + vasque + robinetterie |
| Dépose bidet | u | 50-90€ | 0.5-1h | |
| Dépose chauffe-eau électrique ≤100L | u | 80-150€ | 1-1.5h | Vidange obligatoire avant dépose |
| Dépose chauffe-eau électrique >100L | u | 120-250€ | 1.5-3h | Vidange + poids (200L plein = 240kg). 2 personnes. |
| Dépose radiateur eau chaude | u | 40-80€ | 0.5-1h | Purge + vidange circuit avant |
| Dépose sèche-serviette | u | 40-80€ | 0.5-1h | |
| Dépose robinetterie (mitigeur, mélangeur) | u | 30-60€ | 0.25-0.5h | |
| Dépose tuyauterie apparente (cuivre/acier) | ml | 8-20€ | variable | Dessoudage ou coupe |
| Dépose tuyauterie encastrée | ml | 20-45€ | variable | Ouverture du mur/sol nécessaire |
| Dépose carrelage mural (faïence SDB) | m² | 15-30€ | variable | Bruit + poussière |
| Dépose carrelage sol | m² | 15-35€ | variable | Plus long si collé sur chape vs posé sur lit de sable |

### B. ÉVACUATION / GRAVATS / NETTOYAGE
| Intervention | Unité | Prix MO HT | Note terrain |
|-------------|-------|-----------|-------------|
| Évacuation gravats sacs (descente manuelle) | forfait | 80-200€ | Selon volume et étage. Sans ascenseur = plus cher. |
| Location benne 3m³ (petits gravats) | u | 150-250€ | Livraison + enlèvement. Délai 24-48h. |
| Location benne 5m³ (gros chantier SDB) | u | 200-350€ | |
| Location benne 8m³ (rénovation complète) | u | 300-500€ | |
| Nettoyage fin de chantier | forfait | 50-150€ | Aspiration, lavage sol, évacuation derniers déchets |
| Protection chantier (bâchage, scotch, cartons) | forfait | 30-80€ | Protection sols et meubles existants |
| Tri déchets (déchèterie) | forfait | 50-100€ | Si pas de benne. Transport en utilitaire. |

### C. PRÉPARATION / GROS OEUVRE PLOMBERIE
| Intervention | Unité | Prix MO HT | Durée | Note terrain |
|-------------|-------|-----------|-------|-------------|
| Saignée dans mur placo (rainureuse) | ml | 8-15€ | 0.1-0.2h/ml | Facile, rapide. Rebouchage MAP + bande. |
| Saignée dans mur béton/parpaing (rainureuse) | ml | 15-30€ | 0.2-0.4h/ml | Bruyant, poussière. Disqueuse ou rainureuse. |
| Saignée dans mur pierre | ml | 25-45€ | 0.3-0.5h/ml | Très dur. Burineur ou rainureuse diamant. |
| Saignée dans sol/chape | ml | 12-25€ | 0.15-0.3h/ml | Attention aux réseaux existants (élec, chauffage) |
| Rebouchage saignée (MAP + enduit + lissage) | ml | 5-12€ | 0.1-0.2h/ml | Souvent fait par le plaquiste/peintre, pas le plombier |
| Percement mur placo (passage tube) | u | 5-15€ | 5-15 min | Scie cloche |
| Percement mur béton/parpaing (passage tube Ø20-40) | u | 15-30€ | 15-30 min | Perforateur SDS+ ou carotteuse |
| Percement mur béton (passage tube Ø80-120 évac) | u | 30-60€ | 30-60 min | Carotteuse diamant obligatoire |
| Percement plancher/dalle béton | u | 40-80€ | 30-60 min | Carotteuse. Attention ferraillage. |
| Création trémie dans plancher (passage colonne) | u | 80-200€ | 1-3h | Gros oeuvre, étaiement si nécessaire |
| Scellement au sol (pattes, supports, fixations) | u | 10-25€ | 15-30 min | Chevilles chimiques ou mécaniques selon support |
| Pose fourreau dans dalle (neuf, avant coulage) | ml | 3-8€ | variable | Gaine TPC ou fourreau annelé |

### D. ALIMENTATION — TIRAGE DE TUYAUX
| Intervention | Unité | Prix MO HT | Note terrain |
|-------------|-------|-----------|-------------|
| Tirage tuyau PER en apparent | ml | 8-15€ | Fixation par colliers tous les 50-80cm |
| Tirage tuyau PER en encastré (dans saignée existante) | ml | 12-22€ | Pose dans gaine + rebouchage hors plombier |
| Tirage tuyau multicouche en apparent | ml | 10-18€ | Cintrage possible, plus rigide que PER |
| Tirage tuyau multicouche en encastré | ml | 15-28€ | |
| Tirage tuyau cuivre en apparent + fixation | ml | 18-35€ | Brasure à chaque raccord. Plus long. |
| Tirage tuyau cuivre en encastré | ml | 25-45€ | |
| Création point d'alimentation (EC+EF) depuis nourrice | u | 200-450€ | Comprend 2 tuyaux (chaud+froid) + raccords + fixation murale |
| Déplacement point d'alimentation existant (<2m) | u | 150-300€ | Rallonge ou déroute des tuyaux existants |
| Déplacement point d'alimentation existant (>2m) | u | 250-500€ | Nouveau tirage depuis nourrice recommandé |
| Pose nourrice/collecteur (installation complète) | u | 150-350€ | Fixation murale + raccordement entrée + vannes |
| Raccordement appareil sur alimentation existante | u | 60-120€ | Flexibles + robinet d'arrêt + joints |

### E. ÉVACUATION — POSE TUYAUX PVC
| Intervention | Unité | Prix MO HT | Note terrain |
|-------------|-------|-----------|-------------|
| Pose PVC évacuation en apparent + colliers | ml | 12-25€ | Pente 1-3 cm/m. Colliers isophoniques. |
| Pose PVC évacuation en encastré (dans plancher/cloison) | ml | 20-40€ | |
| Création point d'évacuation (piquage sur colonne existante) | u | 100-250€ | Culotte 45° + raccords + tuyau horizontal |
| Déplacement évacuation existante (<2m) | u | 120-280€ | Rallonge PVC + respect pente |
| Déplacement évacuation existante (>2m) | u | 200-500€ | Attention pente : +2m = risque hauteur insuffisante |
| Raccordement évacuation WC (Ø100-110) | u | 80-150€ | Pipe + manchette + joints |
| Raccordement évacuation douche/baignoire (Ø40) | u | 50-100€ | Siphon + bonde + tuyau horizontal |
| Raccordement évacuation lavabo/évier (Ø32-40) | u | 40-80€ | Siphon + bonde |
| Raccordement tout-à-l'égout (extérieur) | ml | 250-500€ | Terrassement + tuyau + remblai + taxes |
| Mise en pente évacuation (reprise) | forfait | 100-300€ | Si évacuation existante mal pentée = stagnation |

### F. POSE SANITAIRES (main d'oeuvre seule, hors fourniture)
| Intervention | Unité | Prix MO HT | Durée | Note terrain |
|-------------|-------|-----------|-------|-------------|
| Pose lavabo sur colonne | u | 80-150€ | 1-1.5h | Fixation murale + raccordements |
| Pose meuble vasque + robinetterie | u | 120-250€ | 1.5-3h | Montage meuble + fixation + raccordements |
| Pose double vasque + robinetterie x2 | u | 200-400€ | 2.5-4h | |
| Pose receveur douche extra-plat | u | 200-350€ | 2-4h | Calage + étanchéité + raccordement bonde |
| Pose receveur à carreler (douche italienne) | u | 350-600€ | 4-8h | Étanchéité SPEC + pente + caniveau. Plus technique. |
| Pose paroi de douche fixe | u | 100-200€ | 1.5-3h | Percement verre interdit. Fixation murale. |
| Pose porte de douche (pivotante/coulissante) | u | 120-250€ | 2-3h | Réglage + joints silicone |
| Pose colonne de douche | u | 80-150€ | 1-2h | Raccordement sur alimentation existante |
| Pose baignoire acrylique + tablier | u | 250-450€ | 3-5h | Calage + raccordement vidage + tablier |
| Pose baignoire balnéo | u | 400-700€ | 4-7h | Raccordement électrique en plus (électricien) |
| Pose WC posé au sol | u | 100-200€ | 1-2h | Fixation + raccordement eau + évac |
| Pose WC suspendu complet (bâti + coffrage + cuvette) | u | 300-600€ | 4-7h | Le plus technique. 1 journée. |
| Pose bidet | u | 80-150€ | 1-1.5h | |
| Pose urinoir | u | 100-200€ | 1-2h | |
| Pose évier cuisine 1 bac | u | 80-150€ | 1-2h | |
| Pose évier cuisine 2 bacs | u | 100-200€ | 1.5-2.5h | |
| Pose sèche-serviette eau chaude | u | 150-300€ | 2-4h | Raccordement sur circuit chauffage |
| Pose sèche-serviette électrique | u | 80-150€ | 1-2h | Raccordement électrique (électricien si pas de prise) |

### G. POSE CHAUFFE-EAU
| Intervention | Unité | Prix MO HT | Durée | Note terrain |
|-------------|-------|-----------|-------|-------------|
| Remplacement chauffe-eau à l'identique (≤100L) | u | 200-350€ | 2-3h | Vidange ancien + dépose + pose nouveau + raccords |
| Remplacement chauffe-eau à l'identique (150-200L) | u | 250-450€ | 3-4h | 2 personnes si mural |
| Remplacement chauffe-eau à l'identique (250-300L) | u | 300-550€ | 3-5h | Obligatoirement 2 personnes |
| Installation chauffe-eau neuf (création) | u | 350-600€ | 4-6h | Tirage alimentation + évac + groupe sécu + flexibles |
| Remplacement chauffe-eau + modification réseau | u | 400-700€ | 4-8h | Déplacement, changement diamètre, adaptation |
| Pose chauffe-eau thermodynamique | u | 500-1000€ | 4-8h | Raccord hydraulique + électrique + condensats |
| Vidange chauffe-eau (entretien) | u | 60-120€ | 0.5-1h | |
| Détartrage chauffe-eau + changement anode | u | 100-200€ | 1-2h | Recommandé tous les 2-3 ans |

### H. ROBINETTERIE
| Intervention | Unité | Prix MO HT | Durée | Note terrain |
|-------------|-------|-----------|-------|-------------|
| Remplacement robinet/mitigeur lavabo | u | 50-100€ | 0.5-1h | Le plus fréquent. Fermer eau, dévisser, poser, joints. |
| Remplacement mitigeur évier cuisine | u | 50-100€ | 0.5-1h | |
| Remplacement mitigeur douche/baignoire apparent | u | 60-120€ | 0.5-1.5h | |
| Remplacement mitigeur douche encastré | u | 100-200€ | 1-2h | Accès par trappe ou démontage faïence |
| Remplacement robinet d'arrêt WC | u | 40-80€ | 0.25-0.5h | Intervention rapide et très courante |
| Remplacement robinet d'arrêt sous lavabo | u | 40-80€ | 0.25-0.5h | |
| Remplacement vanne générale | u | 80-180€ | 0.5-1.5h | Coupure eau au compteur obligatoire |
| Pose robinet machine à laver (création) | u | 60-120€ | 0.5-1h | Piquage sur alimentation existante |
| Remplacement flexible de douche | u | 25-50€ | 15 min | Intervention minimale |
| Remplacement douchette / pomme de douche | u | 25-50€ | 15 min | |

### I. DÉPANNAGE / RÉPARATION
| Intervention | Unité | Prix MO HT | Note terrain |
|-------------|-------|-----------|-------------|
| Réparation fuite raccord accessible | u | 50-120€ | Resserrage ou changement joint |
| Réparation fuite tuyau apparent (soudure/manchon) | u | 80-200€ | Coupe + raccord ou brasure |
| Réparation fuite tuyau encastré | u | 150-400€ | Ouverture mur + réparation + rebouchage |
| Réparation fuite sous dalle/chape | u | 300-800€ | Carottage + réparation + rebouchage. Le plus cher. |
| Recherche de fuite (caméra thermique) | u | 200-600€ | Rapport écrit souvent demandé par assurance |
| Recherche de fuite (gaz traceur) | u | 300-700€ | Plus précis que caméra. Réseau sous pression. |
| Débouchage canalisation (furet manuel) | u | 60-150€ | |
| Débouchage canalisation (furet électrique) | u | 100-250€ | Plus efficace sur gros bouchons |
| Débouchage canalisation (haute pression) | u | 150-400€ | Camion hydrocureur si très gros bouchon |
| Débouchage WC | u | 50-150€ | Ventouse ou furet |
| Remplacement siphon (lavabo, évier, douche) | u | 40-80€ | |
| Remplacement mécanisme chasse d'eau | u | 50-120€ | Kit complet recommandé vs pièce par pièce |
| Remplacement joint de cuvette WC | u | 40-80€ | Démontage réservoir obligatoire |
| Réparation dégât des eaux (première intervention) | forfait | 150-400€ | Coupure eau + colmatage provisoire + constat |
| Passage caméra inspection canalisation | u | 100-300€ | Vérification état réseau existant |

### J. RÉNOVATION COMPLÈTE (forfaits par pièce)
| Intervention | Unité | Prix MO HT | Note terrain |
|-------------|-------|-----------|-------------|
| Rénovation plomberie salle de bain (sans sanitaires) | forfait | 1500-4000€ | Dépose ancien réseau + tirage neuf + raccordements |
| Rénovation plomberie cuisine | forfait | 800-2500€ | Évier + lave-vaisselle + machine à laver |
| Rénovation plomberie appartement complet (50-70m²) | forfait | 3000-8000€ | SDB + cuisine + WC + distributions |
| Rénovation plomberie maison complète (100-120m²) | forfait | 5000-15000€ | 2 SDB + cuisine + 2 WC + buanderie + distributions |
| Création salle de bain complète dans pièce existante | forfait | 2500-6000€ | Tirage alimentation + évacuation + sanitaires |
| Création salle d'eau (douche + lavabo + WC) | forfait | 1800-4500€ | |
| Mise aux normes réseau ancien (remplacement plomb) | forfait | 2000-6000€ | Remplacement réseau plomb par cuivre/multicouche |

### K. CONSTRUCTION NEUVE (prix au m² habitable)
| Intervention | Unité | Prix MO+fourniture HT | Note terrain |
|-------------|-------|----------------------|-------------|
| Plomberie complète maison neuve (hors chauffage) | m² | 50-110€ | Réseaux alimentation + évac + sanitaires + chauffe-eau |
| Plomberie + chauffage complet maison neuve | m² | 80-150€ | Idem + chaudière/PAC + radiateurs/plancher chauffant |
| Pose tuyauterie neuve (alimentation seule) | ml | 50-120€ | Tirage depuis compteur → nourrice → appareils |
| Pose tuyauterie neuve (évacuation seule) | ml | 40-100€ | PVC horizontal + verticale → tout-à-l'égout |

### L. INTERVENTIONS SPÉCIALES
| Intervention | Unité | Prix MO HT | Note terrain |
|-------------|-------|-----------|-------------|
| Pose sani-broyeur | u | 150-300€ | Raccordement WC + électrique + évac Ø32 |
| Dégorgement colonne immeuble | u | 200-500€ | Intervention en parties communes. Syndic à prévenir. |
| Mise en conformité gaz (attestation) | forfait | 200-500€ | ROAI/RCAI obligatoire. Plombier RGE PG. |
| Pose adoucisseur d'eau | u | 200-400€ | Raccordement sur arrivée générale + bypass |
| Pose filtre anti-calcaire/anti-impuretés | u | 80-150€ | Sur arrivée générale |
| Test d'étanchéité réseau (épreuve pression) | u | 80-200€ | Obligatoire avant mise en eau réseau neuf. Pompe d'épreuve. |
| Calorifugeage tuyauterie (isolation) | ml | 5-12€ | Gaine isolante mousse. Obligatoire locaux non chauffés. |
| Pose vanne d'arrêt générale | u | 80-180€ | Coupure compteur + pose vanne + remise en eau |
| Pose réducteur de pression | u | 60-150€ | Si pression réseau > 3.5 bars |
| Pose clapet anti-retour | u | 40-100€ | Obligatoire sur certaines installations |
| Pose disconnecteur | u | 80-200€ | Protection anti-pollution réseau. Obligatoire si risque retour. |

### M. MAJORATIONS COURANTES
| Majoration | Supplément | Note |
|-----------|-----------|------|
| Urgence soir (18h-22h) | +50% sur MO | |
| Urgence nuit (22h-6h) | +75 à +100% sur MO | |
| Urgence week-end | +50 à +75% sur MO | |
| Urgence jour férié | +75 à +100% sur MO | |
| Frais de déplacement (<15km) | 0-20€ | Souvent offert si gros chantier |
| Frais de déplacement (15-30km) | 20-45€ | |
| Frais de déplacement (>30km) | 35-80€ | |
| Accès difficile (étage sans ascenseur) | +10-20% forfait | Transport matériel + gravats à la main |
| Accès très difficile (combles, vide sanitaire) | +20-40% MO | Pénibilité physique |
| Travaux en site occupé | +5-10% MO | Protection supplémentaire, ménage quotidien |
| Intervention en immeuble (copropriété) | +10-15% | Autorisations syndic, horaires restreints, parking |
| Diagnostic / visite technique avant devis | 0-60€ | Souvent gratuit. Certains facturent 30-60€ déduits du chantier. |

---

> **Note pour le cerveau IA :** Ces prix sont des MOYENNES marché France 2025-2026. Le cerveau doit :
> 1. Appliquer le coefficient régional (×1.25 en Île-de-France, ×1.15 en PACA, etc.)
> 2. Remplacer par les prix RÉELS de l'artisan dès qu'il les fournit (via Mem0)
> 3. Adapter la durée estimée au profil de l'artisan (un plombier expérimenté va 30% plus vite qu'un débutant)
> 4. Ne JAMAIS donner un prix unique — toujours une fourchette basse/haute
> 5. Avertir si le prix de l'artisan est anormalement bas ou haut par rapport au marché
