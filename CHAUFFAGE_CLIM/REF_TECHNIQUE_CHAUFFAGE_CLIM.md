# STRUCTORAI — RÉFÉRENTIEL TECHNIQUE CHAUFFAGE & CLIMATISATION
> Données techniques exhaustives pour le cerveau IA.
> Chaudières, pompes à chaleur, climatisation, plancher chauffant, radiateurs, VMC, chauffe-eau, poêles.
> Ce fichier alimente le RAG et permet au cerveau de comprendre le langage artisan.
> Date : 14/04/2026 — Prix vérifiés par recherche web (sources : Travaux.com, prix-pose.com, HelloWatt, QuelleEnergie, Effy, ENGIE, Ootravaux, chauffageavenue.com)

---

## 1. CHAUDIÈRES GAZ

### Chaudière gaz condensation (standard 2026)
```json
{
  "type": "Chaudière gaz condensation",
  "statut_2026": "Interdite en construction neuve individuelle depuis 2022. Autorisée uniquement en REMPLACEMENT dans l'existant.",
  "aides_2026": "Plus éligible MaPrimeRénov' ni prime CEE depuis 2024. TVA réduite 10% pour remplacement. Éco-PTZ possible.",
  "rendement": "95-109% (sur PCI)",
  "duree_vie": "15-20 ans",
  "entretien_obligatoire": "Annuel, 125-200€/an",
  "modeles": {
    "murale_chauffage_seul": {
      "puissance": "12-25 kW",
      "prix_fourniture": {"min": 1000, "max": 3500},
      "prix_pose": {"min": 500, "max": 1200},
      "prix_total": {"min": 2400, "max": 5500}
    },
    "murale_mixte_instantanee": {
      "puissance": "24-35 kW",
      "prix_fourniture": {"min": 1500, "max": 4000},
      "prix_pose": {"min": 600, "max": 1500},
      "prix_total": {"min": 2800, "max": 7500}
    },
    "murale_mixte_accumulation": {
      "puissance": "24-35 kW",
      "ballon": "30-60L intégré",
      "prix_fourniture": {"min": 2000, "max": 5000},
      "prix_pose": {"min": 700, "max": 1500},
      "prix_total": {"min": 3000, "max": 8000}
    },
    "sol_mixte_accumulation": {
      "puissance": "20-45 kW",
      "ballon": "100-200L",
      "prix_fourniture": {"min": 2500, "max": 6000},
      "prix_pose": {"min": 800, "max": 2000},
      "prix_total": {"min": 3500, "max": 8500}
    }
  },
  "marques": ["Saunier Duval", "Frisquet", "De Dietrich", "Atlantic", "Chappée", "Viessmann", "Vaillant", "Bosch"],
  "normes": "NF EN 483, NF EN 677 (condensation), DTU 61.1 (installations gaz)",
  "note_ia": "TOUJOURS informer l'artisan : depuis 2024, aucune aide nationale pour le gaz. Suggérer PAC air-eau comme alternative si le client est éligible MaPrimeRénov'."
}
```

### Chaudière gaz hybride (gaz + PAC)
```json
{
  "type": "Chaudière hybride gaz + PAC",
  "description": "Combine une chaudière gaz condensation et une PAC air-eau. La PAC fonctionne quand les températures extérieures sont douces, la chaudière prend le relais par grand froid.",
  "rendement": "COP 3-4 (mode PAC), 95-109% PCI (mode gaz)",
  "prix_fourniture": {"min": 5000, "max": 10000},
  "prix_pose": {"min": 1500, "max": 3000},
  "prix_total": {"min": 6000, "max": 12000},
  "aides_2026": "Éligible MaPrimeRénov' et prime CEE (considérée comme PAC)",
  "marques": ["Daikin Altherma H Hybrid", "Atlantic Alfea Hybrid", "De Dietrich", "Saunier Duval"],
  "note_ia": "Solution pertinente en remplacement chaudière gaz si le client souhaite garder le raccordement gaz existant tout en bénéficiant des aides PAC."
}
```

---

## 2. POMPES À CHALEUR (PAC)

### PAC Air-Eau
```json
{
  "type": "PAC air-eau",
  "description": "Capte les calories de l'air extérieur, chauffe un circuit d'eau (radiateurs ou plancher chauffant). Peut produire l'ECS.",
  "COP_moyen": "3.5-5 (dépend température extérieure)",
  "duree_vie": "15-20 ans",
  "entretien_obligatoire": "Tous les 2 ans (4-70 kW), 150-300€/intervention",
  "modeles": {
    "monobloc_chauffage_seul": {
      "puissance": "6-12 kW",
      "prix_total_pose": {"min": 8000, "max": 14000}
    },
    "bibloc_chauffage_ECS": {
      "puissance": "8-16 kW",
      "prix_total_pose": {"min": 10000, "max": 18000}
    },
    "haute_temperature": {
      "puissance": "10-16 kW",
      "description": "Compatible radiateurs haute température existants (65°C+)",
      "prix_total_pose": {"min": 12000, "max": 20000}
    }
  },
  "prix_m2_pose_comprise": {"min": 90, "max": 130},
  "marques": ["Daikin", "Atlantic", "Mitsubishi", "Panasonic", "Bosch", "Viessmann", "Toshiba", "Hitachi"],
  "aides_2026": {
    "MaPrimeRenov": "Jusqu'à 5 000€ selon revenus",
    "prime_CEE": "Jusqu'à 6 880€ (prime Effy)",
    "cumul_max": "Jusqu'à 10 800€",
    "TVA_reduite": "5.5%",
    "eco_PTZ": "Jusqu'à 15 000€ sans intérêt",
    "condition": "Installation par artisan RGE (QualiPAC) obligatoire"
  },
  "normes": "DTU 65.16, NF EN 14511",
  "note_ia": "C'est LE système à proposer en 2026. Meilleur rapport coût/performance avec les aides. TOUJOURS vérifier : isolation du logement, type d'émetteurs existants (radiateurs HT → PAC haute température), espace extérieur pour l'unité."
}
```

### PAC Air-Air (climatisation réversible)
```json
{
  "type": "PAC air-air (clim réversible)",
  "description": "Capte les calories de l'air extérieur, diffuse la chaleur/le froid via des splits muraux. PAS de circuit d'eau.",
  "COP_moyen": "3-4",
  "duree_vie": "12-20 ans",
  "entretien_obligatoire": "Tous les 2 ans (>4 kW), 150-200€/intervention",
  "modeles": {
    "monosplit": {
      "description": "1 unité extérieure + 1 split intérieur (1 pièce)",
      "prix_total_pose": {"min": 1000, "max": 3500}
    },
    "bisplit": {
      "description": "1 unité extérieure + 2 splits (2 pièces)",
      "prix_total_pose": {"min": 2500, "max": 5000}
    },
    "trisplit": {
      "description": "1 unité extérieure + 3 splits (3 pièces)",
      "prix_total_pose": {"min": 3500, "max": 6500}
    },
    "quadrisplit": {
      "description": "1 unité extérieure + 4 splits (4 pièces)",
      "prix_total_pose": {"min": 4500, "max": 8000}
    },
    "gainable": {
      "description": "Unité dans les combles, diffusion par réseau de gaines dans faux plafond",
      "prix_total_pose": {"min": 8000, "max": 18000}
    }
  },
  "prix_m2_pose_comprise": {"min": 60, "max": 100},
  "mise_en_service": {"min": 150, "max": 450, "note": "Obligatoire par frigoriste habilité"},
  "marques": ["Daikin", "Mitsubishi Electric", "Toshiba", "Panasonic", "Hitachi", "Samsung", "LG", "Fujitsu"],
  "aides_2026": {
    "MaPrimeRenov": "NON éligible (PAC air-air exclue depuis 2020)",
    "prime_CEE": "Oui (variable selon opérateur, 300-900€)",
    "TVA": "10% sur la pose (pas 5.5%)"
  },
  "note_ia": "Bien distinguer : la PAC air-air ne produit PAS d'eau chaude sanitaire. Elle est moins aidée que la PAC air-eau. En remplacement de convecteurs électriques, c'est une bonne option. Pour un système complet chauffage+ECS, préférer PAC air-eau."
}
```

### PAC Géothermique (sol-eau / eau-eau)
```json
{
  "type": "PAC géothermique",
  "COP_moyen": "4-5.5 (stable toute l'année)",
  "duree_vie": "20-25 ans (PAC), 50+ ans (capteurs sol)",
  "capteurs_horizontaux": {"prix": {"min": 10000, "max": 18000}, "surface_jardin": "1.5 à 2× surface à chauffer"},
  "capteurs_verticaux_forage": {"prix": {"min": 15000, "max": 25000}, "profondeur": "50-200m"},
  "prix_total_pose": {"min": 15000, "max": 30000},
  "aides_2026": "MaPrimeRénov' jusqu'à 11 000€ + prime CEE",
  "note_ia": "Très performante mais très chère. Réservée aux grandes maisons bien isolées avec jardin (horizontaux) ou budget élevé (verticaux). Peu fréquent chez les artisans BTP ciblés par STRUCTORAI."
}
```

---

## 3. CLIMATISATION (hors PAC réversible)

### Climatisation split (froid seul)
```json
{
  "type": "Climatisation split (froid seul)",
  "note": "De moins en moins vendue — la quasi-totalité du marché est en réversible (cf. PAC air-air ci-dessus). Prix similaires.",
  "monosplit_pose": {"min": 800, "max": 2000},
  "multisplit_pose": {"min": 1500, "max": 5000},
  "mise_en_service": {"min": 150, "max": 400}
}
```

### Climatisation gainable
```json
{
  "type": "Climatisation gainable",
  "description": "Unité centralisée dans les combles, distribution par gaines dans faux plafond. Invisible, silencieux, confort premium.",
  "prix_total_pose": {"min": 8000, "max": 18000},
  "conditions": "Nécessite combles accessibles et faux plafonds",
  "note_ia": "Solution haut de gamme. Pour maison neuve ou grosse rénovation avec faux plafonds. À proposer quand l'esthétique est importante."
}
```

---

## 4. PLANCHER CHAUFFANT

### Plancher chauffant hydraulique (à eau)
```json
{
  "type": "Plancher chauffant hydraulique",
  "description": "Réseau de tubes PER ou multicouche noyé dans une chape, alimenté en eau chaude basse température (30-45°C).",
  "temperature_sol_max": "28°C (réglementation française)",
  "generateurs_compatibles": ["Chaudière gaz condensation", "PAC air-eau", "PAC géothermique", "Chaudière bois", "Solaire thermique"],
  "composants_prix_m2": {
    "kit_isolant_tubes_agrafes": {"min": 35, "max": 60},
    "pose_tubes_collecteur": {"min": 35, "max": 60},
    "chape_fluide_autonivelante": {"min": 25, "max": 45},
    "collecteur_equipe": {"prix_unitaire": {"min": 350, "max": 650}, "note": "1 par zone/étage"}
  },
  "prix_total_m2_pose": {"min": 70, "max": 120, "note": "Hors générateur et revêtement sol"},
  "prix_total_m2_rafraichissant": {"min": 100, "max": 150, "note": "Nécessite PAC réversible"},
  "budget_maison_100m2": {"min": 7000, "max": 12000},
  "budget_maison_120m2_neuf": {"min": 10000, "max": 15000},
  "entretien": "Désembouage tous les 3-5 ans (350-700€)",
  "normes": "DTU 65.14 (plancher chauffant), NF EN 1264",
  "note_ia": "Le plancher chauffant est le MEILLEUR émetteur pour une PAC (fonctionne à 35°C → COP optimal). En neuf, TOUJOURS proposer plancher chauffant + PAC air-eau. En rénovation, vérifier la hauteur disponible sous plafond (+7-10cm de rehausse)."
}
```

### Plancher chauffant électrique
```json
{
  "type": "Plancher chauffant électrique",
  "usage": "Salle de bain, appoint, petites surfaces, rénovation",
  "types": {
    "rayonnant_PRE": {"prix_m2_pose": {"min": 80, "max": 150}, "note": "Chape max 5cm, chauffe rapide"},
    "accumulation": {"prix_m2_pose": {"min": 100, "max": 200}, "note": "Chape épaisse, inertie, heures creuses"}
  },
  "prix_trame_seule_m2": {"min": 25, "max": 50},
  "note_ia": "Intéressant UNIQUEMENT en appoint (salle de bain) ou petite surface. Pour le chauffage principal, TOUJOURS préférer l'hydraulique + PAC."
}
```

---

## 5. RADIATEURS

### Radiateurs à eau (chauffage central)
```json
{
  "radiateurs_eau": [
    {"type": "Acier panneau (type 21/22)", "prix_unitaire": {"min": 100, "max": 400}, "note": "Le plus courant, bon rapport qualité/prix"},
    {"type": "Fonte (classique)", "prix_unitaire": {"min": 200, "max": 800}, "note": "Grande inertie, esthétique rétro, très lourd"},
    {"type": "Aluminium", "prix_unitaire": {"min": 80, "max": 300}, "note": "Léger, montée en température rapide"},
    {"type": "Sèche-serviettes eau", "prix_unitaire": {"min": 100, "max": 500}, "note": "Salle de bain"}
  ],
  "pose_radiateur_eau": {"min": 150, "max": 350, "note": "Par radiateur, raccordement inclus"}
}
```

### Radiateurs électriques
```json
{
  "radiateurs_electriques": [
    {"type": "Convecteur (grille-pain)", "prix_unitaire": {"min": 30, "max": 100}, "note": "Entrée de gamme, mauvais confort, à remplacer"},
    {"type": "Panneau rayonnant", "prix_unitaire": {"min": 80, "max": 250}, "note": "Milieu de gamme, confort correct"},
    {"type": "Radiateur à inertie sèche (pierre, céramique)", "prix_unitaire": {"min": 200, "max": 800}, "note": "Bon confort, chaleur douce"},
    {"type": "Radiateur à inertie fluide", "prix_unitaire": {"min": 250, "max": 700}, "note": "Chaleur homogène, bonne régulation"},
    {"type": "Radiateur à double corps de chauffe", "prix_unitaire": {"min": 400, "max": 1200}, "note": "Haut de gamme électrique, confort optimal"},
    {"type": "Sèche-serviettes électrique", "prix_unitaire": {"min": 100, "max": 600}, "note": "Salle de bain, soufflerie optionnelle"}
  ],
  "pose_radiateur_electrique": {"min": 80, "max": 200, "note": "Par radiateur, tirage ligne électrique inclus"}
}
```

---

## 6. EAU CHAUDE SANITAIRE (ECS)

```json
{
  "chauffe_eau": [
    {"type": "Chauffe-eau électrique (cumulus) 100L", "prix_fourniture": {"min": 150, "max": 400}, "prix_pose": {"min": 200, "max": 400}, "total": {"min": 350, "max": 800}},
    {"type": "Chauffe-eau électrique 200L", "prix_fourniture": {"min": 300, "max": 600}, "prix_pose": {"min": 250, "max": 500}, "total": {"min": 550, "max": 1100}},
    {"type": "Chauffe-eau électrique 300L", "prix_fourniture": {"min": 400, "max": 900}, "prix_pose": {"min": 300, "max": 550}, "total": {"min": 700, "max": 1450}},
    {"type": "Chauffe-eau thermodynamique (CET) 200L", "prix_fourniture": {"min": 1500, "max": 3000}, "prix_pose": {"min": 500, "max": 1000}, "total": {"min": 2000, "max": 4000}, "COP": "2.5-3.5", "note": "PAC intégrée pour ECS — très économe. Éligible MaPrimeRénov' (jusqu'à 1 200€) et CEE."},
    {"type": "Chauffe-eau thermodynamique 300L", "prix_fourniture": {"min": 2000, "max": 4000}, "prix_pose": {"min": 600, "max": 1200}, "total": {"min": 2600, "max": 5200}},
    {"type": "Chauffe-eau solaire individuel (CESI) 200-300L", "prix_total": {"min": 4000, "max": 7000}, "note": "2-4 m² de capteurs + ballon. Éligible MaPrimeRénov' + CEE."},
    {"type": "Chauffe-eau gaz instantané", "prix_total": {"min": 400, "max": 1500}, "note": "Eau chaude à la demande, pas de stockage"},
    {"type": "Ballon tampon (pour PAC)", "prix_total": {"min": 500, "max": 2000}, "note": "100-500L, lisse les cycles PAC"}
  ],
  "marques": ["Atlantic", "Thermor", "Ariston", "De Dietrich", "Chaffoteaux", "Viessmann"],
  "normes": "NF EN 12897, DTU 60.1",
  "note_ia": "En remplacement de cumulus électrique, TOUJOURS proposer le chauffe-eau thermodynamique — 3× moins cher en consommation et éligible aux aides."
}
```

---

## 7. VMC (VENTILATION MÉCANIQUE CONTRÔLÉE)

```json
{
  "vmc": {
    "simple_flux_autoreg": {
      "description": "Débit constant, basique",
      "prix_total_pose": {"min": 250, "max": 1000},
      "aides": "TVA 10% uniquement"
    },
    "simple_flux_hygroreglable": {
      "description": "Débit adapté à l'humidité, meilleure performance",
      "types": {
        "hygro_A": {"prix_total_pose": {"min": 350, "max": 1500}, "note": "Bouches hygroréglables, entrées d'air fixes"},
        "hygro_B": {"prix_total_pose": {"min": 600, "max": 2400}, "note": "Bouches ET entrées d'air hygroréglables — recommandée"}
      },
      "aides": "TVA 10%, prime CEE possible (hygro B)"
    },
    "double_flux": {
      "description": "2 réseaux de gaines. Récupère 75-95% de la chaleur de l'air extrait. Réduit 15-25% de la facture chauffage.",
      "prix_total_pose": {"min": 2000, "max": 7700},
      "prix_total_haut_rendement": {"min": 4500, "max": 10000},
      "aides_2026": {
        "MaPrimeRenov": "Jusqu'à 2 500€",
        "prime_CEE": "200-700€",
        "TVA": "5.5%"
      },
      "note": "Rentable uniquement dans les logements bien isolés (après ITE/isolation combles)."
    },
    "double_flux_thermodynamique": {
      "description": "Double flux + PAC intégrée pour préchauffer l'air entrant",
      "prix_total_pose": {"min": 6000, "max": 15000}
    }
  },
  "marques_VMC": ["Aldes", "Atlantic", "Unelvent", "Helios", "Brink", "Zehnder"],
  "obligation": "VMC obligatoire dans les logements neufs depuis 1982. Arrêté du 24 mars 1982.",
  "normes": "DTU 68.3, NF EN 13141",
  "note_ia": "En rénovation globale, la VMC double flux est le complément idéal de l'ITE. Sans ventilation adaptée, l'isolation peut créer des problèmes d'humidité et de qualité d'air."
}
```

---

## 8. POÊLES ET INSERTS

```json
{
  "poeles": {
    "poele_a_bois_buches": {
      "puissance": "6-14 kW",
      "rendement": "70-85%",
      "prix_fourniture": {"min": 500, "max": 5000},
      "prix_pose_tubage": {"min": 1000, "max": 3000},
      "prix_total": {"min": 1500, "max": 8000},
      "aides": "MaPrimeRénov' jusqu'à 2 500€ + CEE"
    },
    "poele_a_granules": {
      "puissance": "6-14 kW",
      "rendement": "85-95%",
      "prix_fourniture": {"min": 1500, "max": 6000},
      "prix_pose_tubage": {"min": 1000, "max": 3000},
      "prix_total": {"min": 2500, "max": 9000},
      "aides": "MaPrimeRénov' jusqu'à 2 500€ + CEE",
      "note": "Programmable, régulation automatique. Nécessite électricité."
    },
    "insert_cheminee": {
      "rendement": "70-85% (vs 10-15% cheminée ouverte)",
      "prix_fourniture": {"min": 1000, "max": 5000},
      "prix_pose_tubage": {"min": 1500, "max": 4000},
      "prix_total": {"min": 2500, "max": 9000}
    },
    "poele_a_granules_canalise": {
      "description": "Distribue la chaleur dans plusieurs pièces par gaines",
      "prix_total": {"min": 4000, "max": 12000}
    }
  },
  "tubage_cheminee": {
    "tubage_inox_flexible_ml": {"min": 40, "max": 80},
    "tubage_inox_rigide_ml": {"min": 60, "max": 120},
    "ramonage_obligatoire": "2 fois/an dont 1 pendant la période de chauffe (règlement sanitaire départemental). Prix : 50-100€/ramonage."
  },
  "normes": "DTU 24.1 (conduits de fumée), NF EN 13240 (poêles), NF EN 13229 (inserts)",
  "note_ia": "En chauffage d'appoint, le poêle à granulés est le plus pertinent (automatique, propre, bon rendement). L'insert valorise une cheminée existante. TOUJOURS vérifier l'état du conduit de fumée avant devis."
}
```

---

## 9. OUTILLAGE CHAUFFAGISTE / CLIMATICIEN

```json
{
  "outillage": [
    {"outil": "Manomètre de pression (circuit chauffage)", "prix": {"min": 30, "max": 80}},
    {"outil": "Détecteur de fuites (gaz / fluide frigorigène)", "prix": {"min": 80, "max": 300}},
    {"outil": "Pompe de mise en pression (épreuve circuit)", "prix": {"min": 150, "max": 400}},
    {"outil": "Pompe à vide (tirage au vide climatisation)", "prix": {"min": 200, "max": 600}},
    {"outil": "Manifold frigoriste (jeu de manomètres fréon)", "prix": {"min": 80, "max": 250}},
    {"outil": "Balance de charge (fluide frigorigène)", "prix": {"min": 50, "max": 150}},
    {"outil": "Dudgeonnière (évasement tubes cuivre)", "prix": {"min": 50, "max": 200}},
    {"outil": "Coupe-tube cuivre/inox (6-42mm)", "prix": {"min": 15, "max": 50}},
    {"outil": "Pince à sertir multicouche/PER", "prix": {"min": 200, "max": 600}},
    {"outil": "Analyseur de combustion (chaudière gaz)", "prix": {"min": 300, "max": 1500}},
    {"outil": "Thermomètre infrarouge", "prix": {"min": 20, "max": 80}},
    {"outil": "Caméra thermique", "prix": {"min": 200, "max": 3000}},
    {"outil": "Débitmètre (circuit hydraulique)", "prix": {"min": 50, "max": 200}},
    {"outil": "Clé à molette / clés plates / jeu de clés hexagonales", "prix": {"min": 30, "max": 100}},
    {"outil": "Poste de soudure autogène (chalumeau bi-gaz)", "prix": {"min": 150, "max": 400}},
    {"outil": "Cintreuse cuivre/multicouche", "prix": {"min": 80, "max": 300}}
  ]
}
```

---

## 10. MISES EN OEUVRE — TOUTES LES INTERVENTIONS AVEC PRIX

### 10.1 Installation systèmes complets

| Intervention | Prix MO+fourniture HT | Note |
|-------------|----------------------|------|
| Installation PAC air-eau (complète, remplacement chaudière) | 9 000-18 000€ | Hors aides. Avec aides : 5 500-10 000€ |
| Installation PAC air-air monosplit | 1 000-3 500€ | 1 pièce |
| Installation PAC air-air multisplit (3 splits) | 3 500-6 500€ | 3 pièces |
| Remplacement chaudière gaz condensation (même emplacement) | 2 500-6 000€ | Dépose ancienne + pose neuve |
| Installation chaudière gaz hybride | 6 000-12 000€ | Éligible aides |
| Installation plancher chauffant hydraulique (maison neuve 100m²) | 7 000-12 000€ | Hors générateur |
| Installation poêle à granulés + tubage | 2 500-9 000€ | |
| Installation insert cheminée + tubage | 2 500-9 000€ | |
| Installation VMC simple flux hygroréglable | 600-2 400€ | |
| Installation VMC double flux (neuf) | 3 000-6 500€ | |
| Installation VMC double flux (rénovation) | 4 500-10 000€ | Passage gaines complexe |
| Installation chauffe-eau thermodynamique | 2 000-4 000€ | Éligible aides |
| Installation plancher chauffant rafraîchissant + PAC réversible | 15 000-25 000€ | Système complet neuf |

### 10.2 Remplacement et réparation

| Intervention | Prix MO+fourniture HT | Note |
|-------------|----------------------|------|
| Remplacement chauffe-eau électrique 200L | 550-1 100€ | |
| Remplacement radiateur eau (à l'identique) | 250-600€ | Par radiateur |
| Remplacement radiateur électrique | 150-500€ | Par radiateur |
| Remplacement thermostat d'ambiance (programmable) | 100-350€ | Pose incluse |
| Remplacement thermostat connecté | 200-500€ | Netatmo, Tado, Google Nest |
| Remplacement vase d'expansion chaudière | 150-350€ | |
| Remplacement circulateur chaudière | 200-500€ | |
| Purge complète circuit chauffage | 100-250€ | |
| Désembouage circuit chauffage | 350-700€ | Préventif tous les 3-5 ans |
| Recharge fluide frigorigène PAC/clim | 150-400€ | Signe de fuite à investiguer |
| Remplacement compresseur PAC | 1 500-3 500€ | Grosse réparation |
| Ramonage conduit de fumée | 50-100€ | 2×/an obligatoire (bois) |

### 10.3 Entretien et contrats

| Intervention | Prix HT | Note |
|-------------|---------|------|
| Entretien annuel chaudière gaz | 125-200€ | Obligatoire |
| Entretien PAC (tous les 2 ans) | 150-300€ | Obligatoire |
| Entretien clim/PAC air-air (tous les 2 ans) | 150-250€ | Obligatoire |
| Contrat entretien annuel chaudière gaz | 150-250€/an | Inclut 1 visite + dépannage |
| Contrat entretien PAC | 200-400€/an | Inclut 1 visite + pièces |
| Mise en service chaudière neuve | inclus dans pose | |
| Mise en service PAC / climatisation | 150-450€ | Si non inclus dans pose |

---

## 11. TAUX HORAIRE CHAUFFAGISTE / CLIMATICIEN

```json
{
  "taux_horaire": {
    "chauffagiste": {"min": 40, "max": 65, "unite": "€ HT/h"},
    "frigoriste_climaticien": {"min": 45, "max": 75, "unite": "€ HT/h"},
    "plombier_chauffagiste": {"min": 40, "max": 60, "unite": "€ HT/h"},
    "depannage_urgence": {"min": 80, "max": 150, "unite": "€ HT/h", "note": "Nuit, week-end, jours fériés"},
    "deplacement_forfait": {"min": 30, "max": 80}
  },
  "note": "Le taux horaire est souvent intégré dans un forfait par intervention. Méfiance si le devis ne détaille pas main d'oeuvre / fourniture / déplacement."
}
```

---

## 12. RÉGLEMENTATION ET OBLIGATIONS

```json
{
  "reglementation_chauffage": [
    {"regle": "RE 2020 (construction neuve)", "detail": "Depuis 2022, les chaudières 100% gaz sont interdites dans les maisons individuelles neuves. Les logements neufs doivent utiliser des énergies renouvelables pour le chauffage (PAC, bois, solaire, réseau de chaleur)."},
    {"regle": "Interdiction fioul neuf", "detail": "Depuis juillet 2022, interdiction d'installer des chaudières fioul neuves. Réparation autorisée, remplacement interdit."},
    {"regle": "Entretien obligatoire chaudière", "detail": "Entretien annuel par un professionnel qualifié. Attestation remise au propriétaire. Amende possible si non respecté."},
    {"regle": "Entretien obligatoire PAC/clim", "detail": "Décret n°2020-912 : entretien tous les 2 ans pour les PAC de 4 à 70 kW. Tous les 5 ans au-delà de 70 kW."},
    {"regle": "Fluides frigorigènes (F-Gas)", "detail": "Règlement EU F-Gas : seuls les techniciens titulaires de l'attestation de capacité peuvent manipuler les fluides frigorigènes. Obligation de récupération et traitement."},
    {"regle": "Ramonage", "detail": "2 ramonages/an obligatoires pour les conduits de fumée bois (dont 1 en période de chauffe). Attestation de ramonage = obligatoire pour l'assurance incendie."},
    {"regle": "Diagnostic de performance énergétique (DPE)", "detail": "Obligatoire pour la vente/location. Le système de chauffage impacte directement la note DPE. PAC = meilleure note que gaz."},
    {"regle": "RGE obligatoire pour aides", "detail": "Pour bénéficier de MaPrimeRénov', CEE, Éco-PTZ, l'installation DOIT être réalisée par un artisan certifié RGE (QualiPAC, Qualigaz, Qualibois, etc.)."},
    {"regle": "TVA chauffage", "detail": "5.5% : PAC air-eau, PAC géothermique, VMC double flux, chauffe-eau thermodynamique, poêle/insert (si logement > 2 ans + artisan RGE). 10% : chaudière gaz remplacement, clim/PAC air-air, VMC simple flux (logement > 2 ans). 20% : neuf, locaux professionnels."}
  ]
}
```

---

## 13. ERREURS COURANTES TERRAIN (pour le cerveau IA)

1. **PAC sous-dimensionnée** : L'artisan installe une PAC trop faible → inconfort + surconsommation + usure prématurée. TOUJOURS faire une étude thermique (bilan déperditions) avant de dimensionner.
2. **PAC surdimensionnée** : L'artisan installe une PAC trop puissante → cycles courts (on/off permanents) → usure compresseur + bruit + surcoût à l'achat. Le juste dimensionnement est CRITIQUE.
3. **PAC air-eau sur vieux radiateurs fonte** : Les radiateurs fonte fonctionnent à 65-70°C. Une PAC standard produit 45-55°C → le logement n'est pas assez chaud. Solution : PAC haute température OU remplacement radiateurs OU ajout plancher chauffant.
4. **Oubli du ballon tampon avec PAC** : Sans ballon tampon, la PAC fait des cycles courts en mi-saison → usure. Prévoir 50-100L de tampon.
5. **Clim/PAC sans étude acoustique** : L'unité extérieure d'une PAC fait 45-65 dB. Si trop près de la chambre du voisin → nuisances → plainte → obligation de déplacer. Vérifier les règles de distance.
6. **VMC double flux sans isolation** : Installer une VMC double flux dans un logement mal isolé = gaspillage. La double flux est rentable UNIQUEMENT dans un logement étanche (après ITE/isolation combles).
7. **Plancher chauffant avec parquet massif épais** : Le parquet massif > 15mm bloque la chaleur du plancher chauffant. Préconiser carrelage, vinyle, ou parquet contrecollé compatible.
8. **Pas de contrat d'entretien proposé** : Le chauffagiste oublie de proposer un contrat d'entretien → le client ne fait pas entretenir → panne + perte de garantie. TOUJOURS proposer un contrat.
9. **Oubli du désembouage** : En remplacement de chaudière, le circuit existant contient des boues. Si on installe une PAC sur un circuit encrassé → perte de performance + panne. Désembouage OBLIGATOIRE avant installation PAC.
10. **Pas d'attestation RGE vérifiée** : Le client perd ses aides si l'artisan n'est pas RGE. Vérifier sur le site officiel France Rénov'.
11. **Chaudière gaz proposée sans mentionner les alternatives** : En 2026, le chauffagiste DOIT informer le client que la PAC est souvent plus rentable (avec aides). Obligation de conseil.
12. **Tubage cheminée pas vérifié avant pose insert/poêle** : Un conduit de fumée non conforme (fissures, mauvais diamètre, pas tubé) = risque d'intoxication CO. TOUJOURS vérifier + tuber.

---

## 14. COEFFICIENTS RÉGIONAUX — CHAUFFAGE/CLIMATISATION

| Zone | Coefficient | Note |
|------|------------|------|
| Paris intra-muros | ×1.20 à ×1.35 | Contraintes accès, stationnement |
| Petite couronne IdF | ×1.10 à ×1.25 | |
| Grande couronne IdF | ×1.00 à ×1.15 | |
| Lyon, Marseille, Bordeaux, Toulouse | ×1.00 à ×1.10 | |
| Grandes villes | ×0.95 à ×1.05 | |
| Villes moyennes | ×0.90 à ×1.00 | Base du référentiel |
| Zones rurales | ×0.85 à ×0.95 | |
| Montagne (altitude > 800m) | ×1.05 à ×1.20 | PAC air-eau moins performante (froid), prévoir appoint |
| DOM-TOM | ×1.25 à ×1.50 | Peu de chauffage, forte demande climatisation |
| Corse | ×1.10 à ×1.20 | Transport maritime matériel |

**Particularité climatisation :** En zone sud (Marseille, Montpellier, Nice), la demande de climatisation est très forte et les prix de pose peuvent être plus élevés en été (période de rush).

---

## 15. COORDINATION AVEC LES AUTRES CORPS DE MÉTIER

| Situation | Corps de métier | Séquence |
|-----------|----------------|----------|
| PAC + plancher chauffant | Chauffagiste + chapiste + carreleur | Chauffagiste (tubes) → chapiste (chape) → carreleur |
| Climatisation + électricité | Climaticien + électricien | Électricien (ligne dédiée 230V/400V) → climaticien |
| VMC double flux + isolation | Plaquiste/isolateur + chauffagiste | Isolation d'abord → VMC ensuite (pour adapter l'étanchéité) |
| PAC + panneau photovoltaïque | Chauffagiste + électricien | PAC installée → PV pour autoconsommer l'électricité PAC |
| Poêle + maçonnerie cheminée | Fumiste/chauffagiste + maçon | Maçon (souche/conduit) → fumiste (tubage + pose poêle) |
| Plancher chauffant + plomberie | Chauffagiste + plombier | Si même artisan : combiné. Si différents : chauffagiste avant plombier (chape en commun) |

---

## 16. AIDES FINANCIÈRES 2026 — SYNTHÈSE

```json
{
  "aides_chauffage_2026": {
    "MaPrimeRenov_parcours_geste": {
      "PAC_air_eau": {"bleu": 5000, "jaune": 4000, "violet": 3000, "rose": 0},
      "PAC_geothermique": {"bleu": 11000, "jaune": 9000, "violet": 6000, "rose": 0},
      "chauffe_eau_thermo": {"bleu": 1200, "jaune": 800, "violet": 400, "rose": 0},
      "poele_granules": {"bleu": 2500, "jaune": 2000, "violet": 1500, "rose": 0},
      "VMC_double_flux": {"bleu": 2500, "jaune": 2000, "violet": 1500, "rose": 0},
      "PAC_air_air": "NON éligible",
      "chaudiere_gaz": "NON éligible depuis 2023"
    },
    "prime_CEE": "Variable selon opérateur (300-6 880€ selon revenus et équipement)",
    "eco_PTZ": "Jusqu'à 15 000€ (action seule) ou 50 000€ (rénovation globale), sans intérêt",
    "TVA_reduite": "5.5% pour PAC, VMC DF, CET, bois — 10% pour remplacement gaz, clim",
    "condition_RGE": "TOUTES les aides nécessitent un artisan RGE certifié",
    "note_ia": "TOUJOURS estimer les aides pour le client dans le devis. Un devis PAC air-eau à 13 000€ peut descendre à 5 000€ après aides pour un ménage modeste."
  }
}
```

---

## 17. RÈGLES IA PRICING — CERVEAU STRUCTORAI

### Règle 1 — Toujours donner des FOURCHETTES
Prix min-max, JAMAIS un prix unique.

### Règle 2 — Appliquer le coefficient régional
Prix × coefficient zone.

### Règle 3 — Déférer aux prix de l'artisan (Mem0)
Ses prix > référentiel.

### Règle 4 — Alerter sur les prix anormaux
< 70% ou > 150% du référentiel → alerte.

### Règle 5 — TOUJOURS estimer les aides financières
```
SI devis PAC ou poêle ou VMC DF ou CET → CALCULER
  "Avec MaPrimeRénov' + prime CEE, votre reste à charge estimé serait de X€ au lieu de Y€."
  Vérifier l'éligibilité RGE de l'artisan.
```

### Règle 6 — Proposer la PAC en alternative au gaz
```
SI remplacement chaudière gaz → SUGGÉRER
  "Avez-vous envisagé une pompe à chaleur air-eau ? Avec les aides 2026, 
   le reste à charge peut être similaire à une chaudière gaz, pour des économies 
   de 50-70% sur la facture de chauffage."
```

### Règle 7 — Vérifier la compatibilité émetteurs/PAC
```
SI installation PAC sur circuit existant → VÉRIFIER
  "Quels émetteurs sont en place ? Radiateurs fonte haute température → PAC haute température 
   ou remplacement radiateurs. Plancher chauffant → PAC standard (optimal)."
```

### Règle 8 — Ne jamais oublier l'entretien
```
SI installation système de chauffage → RAPPELER
  "N'oubliez pas de proposer un contrat d'entretien.
   Entretien annuel chaudière : obligatoire (125-200€/an).
   Entretien PAC : obligatoire tous les 2 ans (150-300€)."
```

### Règle 9 — Désembouage avant remplacement
```
SI remplacement chaudière par PAC ET circuit existant → ALERTER
  "Un désembouage du circuit est fortement recommandé avant l'installation
   de la PAC (350-700€). Un circuit encrassé réduit les performances de 15-30%."
```

---

## 18. DEVIS TYPES — EXEMPLES CHIFFRÉS

### Devis type 1 : Remplacement chaudière gaz par PAC air-eau — Maison 100m²
| Poste | Quantité | Prix unitaire HT | Total HT |
|-------|----------|-----------------|----------|
| Dépose ancienne chaudière gaz + évacuation | 1 forfait | 300€ | 300€ |
| PAC air-eau Atlantic Alfea Extensa 8kW + module intérieur | 1 | 6 500€ | 6 500€ |
| Ballon ECS thermodynamique 200L | 1 | 2 200€ | 2 200€ |
| Raccordement hydraulique (circuit existant) | 1 forfait | 800€ | 800€ |
| Raccordement électrique (ligne dédiée 400V) | 1 forfait | 350€ | 350€ |
| Désembouage circuit chauffage | 1 forfait | 450€ | 450€ |
| Mise en service + programmation thermostat | 1 forfait | 250€ | 250€ |
| **TOTAL HT** | | | **10 850€** |
| TVA 5.5% (PAC, logement > 2 ans, artisan RGE) | | | 597€ |
| **TOTAL TTC** | | | **11 447€** |
| **Aides estimées (ménage jaune)** | | | |
| MaPrimeRénov' | | | -4 000€ |
| Prime CEE | | | -4 000€ |
| **RESTE À CHARGE** | | | **~3 447€** |

### Devis type 2 : Climatisation réversible tri-split — Appartement 80m²
| Poste | Quantité | Prix unitaire HT | Total HT |
|-------|----------|-----------------|----------|
| Unité extérieure Daikin 3MXM52 (5.2 kW) | 1 | 1 800€ | 1 800€ |
| Split mural Daikin FTXM25 (salon 30m²) | 1 | 650€ | 650€ |
| Split mural Daikin FTXM20 (chambre 1) | 1 | 550€ | 550€ |
| Split mural Daikin FTXM20 (chambre 2) | 1 | 550€ | 550€ |
| Liaison frigorifique (3 × 5m) | 15 ml | 30€/ml | 450€ |
| Support anti-vibration unité extérieure | 1 | 80€ | 80€ |
| Raccordement électrique (ligne dédiée) | 1 forfait | 250€ | 250€ |
| Mise en service (tirage au vide + charge) | 1 forfait | 350€ | 350€ |
| **TOTAL HT** | | | **4 680€** |
| TVA 10% (clim réversible, logement > 2 ans) | | | 468€ |
| **TOTAL TTC** | | | **5 148€** |

### Devis type 3 : Installation poêle à granulés + tubage — Maison R+1
| Poste | Quantité | Prix unitaire HT | Total HT |
|-------|----------|-----------------|----------|
| Poêle à granulés Edilkamin Nara 8kW | 1 | 2 800€ | 2 800€ |
| Tubage inox rigide Ø80mm (8m hauteur) | 8 ml | 65€/ml | 520€ |
| Plaque de propreté + chapeau de conduit | 1 forfait | 120€ | 120€ |
| Raccord T de purge | 1 | 45€ | 45€ |
| Protection murale (plaque silicate calcium) | 1 | 180€ | 180€ |
| Pose complète + raccordement conduit | 1 forfait | 600€ | 600€ |
| Mise en service + programmation | 1 forfait | 100€ | 100€ |
| **TOTAL HT** | | | **4 365€** |
| TVA 5.5% (poêle granulés, logement > 2 ans, RGE) | | | 240€ |
| **TOTAL TTC** | | | **4 605€** |
| **Aides estimées (ménage violet)** | | | |
| MaPrimeRénov' | | | -1 500€ |
| Prime CEE | | | -800€ |
| **RESTE À CHARGE** | | | **~2 305€** |

---

> **Note pour le cerveau IA :** Ce référentiel couvre l'intégralité du métier chauffagiste/climaticien :
> 1. Chaudières gaz (4 types : murale/sol, chauffage seul/mixte, condensation/hybride) avec prix et aides 2026
> 2. Pompes à chaleur (PAC air-eau 3 gammes, PAC air-air 5 configs, PAC géothermique) avec prix, COP, aides
> 3. Climatisation (split, multisplit, gainable) avec prix par nombre de splits
> 4. Plancher chauffant (hydraulique standard/rafraîchissant, électrique rayonnant/accumulation) avec prix/m²
> 5. Radiateurs (eau : acier/fonte/alu, électrique : convecteur→double corps) avec prix unitaire
> 6. ECS (cumulus, thermodynamique, solaire, gaz instantané, ballon tampon) avec prix
> 7. VMC (simple flux auto/hygro A/hygro B, double flux, thermodynamique) avec prix et aides
> 8. Poêles et inserts (bûches, granulés, granulés canalisé, insert) avec tubage et prix
> 9. Outillage chauffagiste (16 outils) avec prix
> 10. Mises en oeuvre (25+ interventions installation + 12 réparations + 6 entretiens) avec prix
> 11. Taux horaires par spécialité + urgence
> 12. Réglementation complète (RE 2020, interdiction fioul, entretien obligatoire, F-Gas, ramonage, RGE, TVA)
> 13. 12 erreurs courantes terrain (dimensionnement PAC, compatibilité émetteurs, désembouage, acoustique...)
> 14. Coefficients régionaux (10 zones)
> 15. Coordination inter-corps de métier (6 situations)
> 16. Synthèse aides financières 2026 complète (MaPrimeRénov', CEE, éco-PTZ, TVA par équipement)
> 17. Règles IA pricing (9 règles impératives dont estimation aides, alternative PAC, entretien)
> 18. 3 devis types chiffrés (PAC air-eau remplacement gaz 100m² → reste 3 447€, clim tri-split 80m² → 5 148€, poêle granulés → reste 2 305€)
