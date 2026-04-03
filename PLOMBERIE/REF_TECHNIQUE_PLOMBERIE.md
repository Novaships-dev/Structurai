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
  "marques": ["KME", "Wieland", "Sanco", "Trefimetaux"]
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
  "marques": ["Rehau", "Uponor", "Giacomini", "Henco", "Comap"]
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
  "marques": ["Nicoll (Fluxo)", "Giacomini", "Henco", "Comap", "Geberit (Mepla)", "Rehau"]
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
  "marques": ["Nicoll", "Girpi", "Wavin"]
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
