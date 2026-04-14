# STRUCTORAI — RÉFÉRENTIEL TECHNIQUE ISOLATION (Thermique & Acoustique)
> Données techniques exhaustives pour le cerveau IA.
> Combles perdus, combles aménageables, murs ITI/ITE, plancher bas, sarking, acoustique.
> Ce fichier alimente le RAG et permet au cerveau de comprendre le langage artisan.
> Date : 14/04/2026 — Prix vérifiés par recherche web (sources : Travaux.com, prix-pose.com, QuelleEnergie, Effy, HelloWatt, La Prime Énergie, ENGIE, La Maison Saint-Gobain, Calculeo, abctravaux.org)

---

## 1. MATÉRIAUX ISOLANTS — COMPARATIF COMPLET

### Laines minérales
```json
{
  "laine_de_verre": {
    "lambda": "0.030-0.040 W/(m.K)",
    "densite": "10-25 kg/m³",
    "classement_feu": "A1 (incombustible)",
    "confort_ete": "Faible (déphasage 3-4h)",
    "prix_m2_100mm": {"min": 5, "max": 12},
    "prix_vrac_soufflage_m2": {"min": 1, "max": 3, "note": "par kg"},
    "duree_vie": "25-35 ans (se tasse)",
    "marques": ["Isover (Saint-Gobain)", "Knauf", "Ursa"],
    "avantages": ["Prix bas", "Ininflammable", "Très répandue", "Bon rapport qualité/prix"],
    "inconvenients": ["Se tasse avec le temps", "Mauvais confort d'été", "Irritante à poser", "Sensible à l'humidité"]
  },
  "laine_de_roche": {
    "lambda": "0.033-0.044 W/(m.K)",
    "densite": "25-200 kg/m³",
    "classement_feu": "A1 (incombustible)",
    "confort_ete": "Moyen (déphasage 4-6h)",
    "prix_m2_100mm": {"min": 8, "max": 20},
    "prix_vrac_soufflage_m2": {"min": 1.5, "max": 4},
    "duree_vie": "30-40 ans",
    "marques": ["Rockwool", "Isover", "Knauf"],
    "avantages": ["Ininflammable", "Bonne isolation phonique", "Résistante à l'humidité", "Plus dense que laine de verre"],
    "inconvenients": ["Plus chère que laine de verre", "Confort d'été moyen"]
  }
}
```

### Isolants biosourcés
```json
{
  "ouate_de_cellulose": {
    "lambda": "0.035-0.040 W/(m.K)",
    "densite": "25-70 kg/m³",
    "classement_feu": "B-s2,d0 (traitement ignifuge)",
    "confort_ete": "Bon (déphasage 8-12h)",
    "prix_m2_100mm": {"min": 8, "max": 15},
    "prix_vrac_soufflage_m2": {"min": 8, "max": 12},
    "duree_vie": "35-50 ans (ne se tasse quasiment pas)",
    "marques": ["Univercell (Soprema)", "Igloo", "Isocell"],
    "avantages": ["Écologique (papier recyclé)", "Excellent confort d'été", "Ne se tasse pas", "Bon régulateur d'humidité", "Bonne isolation phonique"],
    "inconvenients": ["Nécessite machine à souffler", "Traitement ignifuge (sel de bore)"]
  },
  "fibre_de_bois": {
    "lambda": "0.036-0.046 W/(m.K)",
    "densite": "40-240 kg/m³",
    "classement_feu": "E (combustible, traitement possible)",
    "confort_ete": "Excellent (déphasage 10-14h, le meilleur)",
    "prix_m2_100mm": {"min": 12, "max": 30},
    "duree_vie": "40-60 ans",
    "marques": ["Steico", "Pavatex", "Isonat"],
    "avantages": ["Meilleur confort d'été de tous les isolants", "Écologique", "Régulation hygroscopique", "Bonne isolation phonique"],
    "inconvenients": ["Prix élevé", "Combustible", "Plus épaisse à R équivalent"]
  },
  "laine_de_chanvre": {
    "lambda": "0.039-0.045 W/(m.K)",
    "prix_m2_100mm": {"min": 15, "max": 25},
    "note": "Bon confort d'été, écologique. Moins courant."
  },
  "laine_de_mouton": {
    "lambda": "0.035-0.042 W/(m.K)",
    "prix_m2_100mm": {"min": 15, "max": 30},
    "note": "Excellent régulateur d'humidité. Traitement anti-mites nécessaire."
  },
  "liege_expanse": {
    "lambda": "0.038-0.043 W/(m.K)",
    "prix_m2_100mm": {"min": 25, "max": 60},
    "note": "Imputrescible, idéal zones humides. Très cher mais indestructible."
  }
}
```

### Isolants synthétiques
```json
{
  "polystyrene_expanse_PSE": {
    "lambda": "0.030-0.038 W/(m.K)",
    "densite": "15-30 kg/m³",
    "prix_m2_100mm": {"min": 5, "max": 12},
    "avantages": ["Très léger", "Bon marché", "Résistant à l'humidité"],
    "inconvenients": ["Inflammable", "Pas d'isolation phonique", "Pas écologique", "Pas de confort d'été"],
    "usage_principal": "ITE sous enduit (le plus utilisé)"
  },
  "polystyrene_expanse_graphite_PSE_gris": {
    "lambda": "0.031-0.033 W/(m.K)",
    "prix_m2_100mm": {"min": 8, "max": 16},
    "note": "20% plus performant que le PSE blanc. Standard en ITE performante."
  },
  "polystyrene_extrude_XPS": {
    "lambda": "0.029-0.036 W/(m.K)",
    "prix_m2_100mm": {"min": 12, "max": 25},
    "note": "Très résistant à la compression et à l'humidité. Idéal plancher bas, soubassement."
  },
  "polyurethane_PUR": {
    "lambda": "0.022-0.028 W/(m.K)",
    "prix_m2_100mm": {"min": 15, "max": 35},
    "avantages": ["MEILLEUR lambda du marché", "Faible épaisseur à R équivalent"],
    "inconvenients": ["Très cher", "Pas recyclable", "Pas de confort d'été", "Toxique en cas d'incendie"],
    "usage_principal": "Doublage collé, sarking, contraintes d'épaisseur"
  },
  "mousse_polyurethane_projetee": {
    "lambda": "0.023-0.028 W/(m.K)",
    "prix_m2_100mm_pose": {"min": 25, "max": 50},
    "note": "Projetée in situ. Parfait pour combles difficiles d'accès. Coûteux."
  }
}
```

### Tableau comparatif — Épaisseur pour R cible
```json
{
  "epaisseur_pour_R_cible": {
    "note": "Épaisseur approximative en cm pour atteindre la résistance thermique R cible",
    "R_3.7_murs_aide": {
      "laine_verre": 13, "laine_roche": 14, "ouate_cellulose": 14, "fibre_bois": 16,
      "PSE_blanc": 13, "PSE_gris": 12, "polyurethane": 9
    },
    "R_6_rampants_aide": {
      "laine_verre": 21, "laine_roche": 22, "ouate_cellulose": 22, "fibre_bois": 26,
      "PSE_gris": 19, "polyurethane": 15
    },
    "R_7_combles_perdus_aide": {
      "laine_verre_soufflee": 25, "laine_roche_soufflee": 28, "ouate_cellulose_soufflee": 28,
      "fibre_bois_soufflee": 32
    }
  }
}
```

---

## 2. ISOLATION DES COMBLES PERDUS

### Techniques et prix
```json
{
  "combles_perdus": {
    "deperdition": "25-30% des pertes de chaleur (1ère priorité d'isolation)",
    "R_minimum_aides": "R ≥ 7 m².K/W",
    "R_RE2020_neuf": "R ≥ 7 m².K/W",
    "techniques": {
      "soufflage_laine_minerale": {
        "description": "Projection de laine en vrac par machine souffleuse. Rapide (3-5h pour 100m²).",
        "prix_m2_pose": {"min": 20, "max": 35},
        "epaisseur_recommandee": "30-40 cm",
        "condition": "Combles non aménageables, non circulables"
      },
      "soufflage_ouate_cellulose": {
        "prix_m2_pose": {"min": 25, "max": 50},
        "epaisseur_recommandee": "30-40 cm",
        "note": "Meilleur confort d'été, ne se tasse pas. Recommandé."
      },
      "deroulage_rouleaux": {
        "description": "Pose de rouleaux de laine minérale en 2 couches croisées sur plancher accessible.",
        "prix_m2_pose": {"min": 25, "max": 55},
        "epaisseur_recommandee": "2 × 20 cm croisés",
        "note": "Pour combles accessibles avec plancher. Plus cher que soufflage."
      },
      "panneaux_rigides": {
        "prix_m2_pose": {"min": 30, "max": 65},
        "note": "Si on veut circuler dans les combles. Pose d'un plancher par-dessus possible."
      }
    },
    "travaux_annexes": {
      "depose_ancien_isolant": {"prix_m2": {"min": 5, "max": 15}},
      "trappe_acces_isolee": {"prix_unitaire": {"min": 80, "max": 200}},
      "rehausse_trappe": {"prix_forfait": {"min": 100, "max": 250}},
      "pose_pare_vapeur": {"prix_m2": {"min": 3, "max": 6}},
      "reperage_boitiers_electriques": {"prix_forfait": {"min": 50, "max": 150}}
    },
    "budget_maison_100m2": {
      "soufflage_laine_minerale": {"min": 2000, "max": 3500},
      "soufflage_ouate": {"min": 2500, "max": 5000},
      "rouleaux_laine_minerale": {"min": 2500, "max": 5500}
    }
  }
}
```

---

## 3. ISOLATION DES COMBLES AMÉNAGEABLES (RAMPANTS)

```json
{
  "combles_amenageables": {
    "deperdition": "Inclus dans les 25-30% (toiture)",
    "R_minimum_aides": "R ≥ 6 m².K/W",
    "techniques": {
      "entre_chevrons_ITI": {
        "description": "Isolant entre et/ou sous les chevrons + pare-vapeur + parement (placo ou lambris).",
        "prix_m2_pose": {"min": 40, "max": 80},
        "epaisseur_recommandee": "20-30 cm (selon profondeur chevrons)",
        "inconvenient": "Réduit la hauteur sous plafond de 20-30 cm"
      },
      "sarking_ITE_toiture": {
        "description": "Isolant rigide posé SUR les chevrons, sous la couverture. Nécessite dépose des tuiles.",
        "prix_m2_pose": {"min": 80, "max": 160},
        "isolants": ["Fibre de bois (Steico)", "Polyuréthane", "Polystyrène extrudé"],
        "avantages": ["Pas de perte de surface habitable", "Supprime les ponts thermiques aux chevrons", "Couverture refaite à neuf"],
        "inconvenient": "Coût élevé (mais combiné avec réfection couverture = pertinent)"
      },
      "caissons_chevronnes": {
        "description": "Panneau sandwich isolant avec parement intégré. Pose entre chevrons.",
        "prix_m2_pose": {"min": 50, "max": 100}
      }
    },
    "budget_maison_60m2_rampants": {
      "ITI_entre_chevrons": {"min": 2400, "max": 4800},
      "sarking_fibre_bois": {"min": 4800, "max": 9600}
    }
  }
}
```

---

## 4. ISOLATION DES MURS PAR L'INTÉRIEUR (ITI)

```json
{
  "ITI_murs": {
    "deperdition_murs": "20-25% des pertes de chaleur",
    "R_minimum_aides": "R ≥ 3.7 m².K/W",
    "techniques": {
      "doublage_colle": {
        "description": "Panneau isolant + plaque de plâtre collés sur mur lisse et sain (MAP). Technique la moins chère.",
        "prix_m2_pose": {"min": 30, "max": 60},
        "isolants": ["PSE+placo (Placomur)", "PUR+placo", "Laine de roche+placo"],
        "condition": "Mur parfaitement lisse et sec. Pas de remontée d'humidité."
      },
      "ossature_metallique": {
        "description": "Rails métalliques fixés au mur + isolant entre les rails + placo vissé. Technique la plus courante.",
        "prix_m2_pose": {"min": 40, "max": 80},
        "isolants": ["Laine de verre semi-rigide", "Laine de roche", "Fibre de bois", "Chanvre"],
        "avantages": ["Corrige les défauts d'aplomb", "Passage de gaines électriques", "Compatible tout isolant"],
        "inconvenient": "Perte de surface 10-15 cm par mur"
      },
      "insufflation": {
        "description": "Injection d'isolant en vrac dans la lame d'air d'un mur creux ou entre ossature + parement.",
        "prix_m2_pose": {"min": 25, "max": 50},
        "isolants": ["Ouate de cellulose", "Billes de polystyrène", "Laine de roche nodulée"],
        "note": "Technique de niche, pour murs creux ou contre-cloisons existantes."
      }
    },
    "travaux_annexes": {
      "depose_ancien_doublage": {"prix_m2": {"min": 8, "max": 15}},
      "deplacement_prises_interrupteurs": {"prix_unitaire": {"min": 30, "max": 60}},
      "reprise_encadrement_fenetres": {"prix_unitaire": {"min": 50, "max": 120}},
      "peinture_finition": {"prix_m2": {"min": 8, "max": 15}},
      "pare_vapeur": {"prix_m2": {"min": 3, "max": 6}}
    },
    "aides_2026": {
      "MaPrimeRenov": "PLUS éligible en geste seul depuis janvier 2026. Uniquement en rénovation d'ampleur (Parcours accompagné, gain ≥ 2 classes DPE).",
      "prime_CEE": "Oui, en geste seul. Environ 7-12€/m² selon revenus.",
      "TVA": "5.5% (logement > 2 ans, artisan RGE)"
    },
    "note_ia": "ATTENTION changement 2026 : l'ITI seule n'est PLUS éligible à MaPrimeRénov'. Seul le Parcours accompagné (rénovation globale) permet d'obtenir MaPrimeRénov' pour l'isolation murs. Les CEE restent accessibles en geste seul."
  }
}
```

---

## 5. ISOLATION DES MURS PAR L'EXTÉRIEUR (ITE)

```json
{
  "ITE_murs": {
    "R_minimum_aides": "R ≥ 3.7 m².K/W",
    "techniques": {
      "sous_enduit": {
        "description": "Panneaux isolants collés/chevillés sur façade + sous-enduit armé + enduit de finition.",
        "prix_m2_pose": {"min": 120, "max": 220},
        "isolants_courants": ["PSE blanc (économique)", "PSE graphité gris (standard)", "Laine de roche (phonique+feu)", "Fibre de bois (confort été)"],
        "finitions": {
          "enduit_mineral_chaux": {"prix_m2_sup": {"min": 15, "max": 25}},
          "enduit_organique_resine": {"prix_m2_sup": {"min": 10, "max": 20}},
          "enduit_taloche": "Standard",
          "enduit_gratte": "+5-10€/m² (plus de travail)"
        }
      },
      "sous_bardage": {
        "description": "Ossature (bois ou métal) + isolant + lame d'air ventilée + bardage (bois, métal, composite).",
        "prix_m2_pose": {"min": 180, "max": 280},
        "bardages": {
          "bois_douglas": {"prix_m2_sup": {"min": 30, "max": 70}},
          "composite": {"prix_m2_sup": {"min": 40, "max": 90}},
          "zinc": {"prix_m2_sup": {"min": 50, "max": 100}},
          "PVC": {"prix_m2_sup": {"min": 20, "max": 40}}
        },
        "avantages": ["Lame d'air ventilée (mur respire)", "Corrige les défauts de façade", "Esthétique contemporaine"],
        "note": "Vérifier PLU : le bardage n'est pas autorisé partout."
      },
      "sous_veture": {
        "description": "Panneau isolant + parement de finition intégré (2 en 1). Pose rapide.",
        "prix_m2_pose": {"min": 140, "max": 220}
      }
    },
    "travaux_annexes_ITE": {
      "echafaudage": {"prix_m2_facade": {"min": 8, "max": 15}},
      "depose_volets_repose": {"prix_paire": {"min": 30, "max": 60}},
      "remplacement_appuis_fenetre": {"prix_unitaire": {"min": 50, "max": 120}},
      "traitement_points_singuliers_tableaux": {"prix_ml": {"min": 20, "max": 40}},
      "profil_depart_soubassement": {"prix_ml": {"min": 5, "max": 12}}
    },
    "budget_maison_100m2_facade": {
      "ITE_sous_enduit_PSE": {"min": 12000, "max": 22000},
      "ITE_sous_bardage_bois": {"min": 18000, "max": 28000}
    },
    "aides_2026": {
      "MaPrimeRenov": "PLUS éligible en geste seul depuis janvier 2026. Uniquement Parcours accompagné (gain ≥ 2 classes DPE). Aide 40-75€/m² selon revenus.",
      "prime_CEE": "Oui, en geste seul. Jusqu'à 12€/m².",
      "TVA": "5.5%"
    },
    "note_ia": "L'ITE est 2× plus chère que l'ITI mais supprime TOUS les ponts thermiques. TOUJOURS proposer si ravalement de façade prévu en même temps (mutualisation échafaudage). Vérifier PLU et ABF avant de lancer le devis."
  }
}
```

---

## 6. ISOLATION DU PLANCHER BAS

```json
{
  "plancher_bas": {
    "deperdition": "7-10% des pertes de chaleur",
    "R_minimum_aides": "R ≥ 3 m².K/W",
    "techniques": {
      "par_dessous_cave_vide_sanitaire": {
        "description": "Fixation de panneaux rigides sous le plancher (en cave ou vide sanitaire accessible).",
        "prix_m2_pose": {"min": 20, "max": 50},
        "isolants": ["Polyuréthane (PUR)", "Polystyrène extrudé (XPS)", "Laine de roche rigide"],
        "avantages": ["Pas de travaux dans le logement", "Pas de perte de hauteur", "Rapide"],
        "condition": "Vide sanitaire ou cave accessible (hauteur ≥ 60 cm)"
      },
      "par_dessus_sol_existant": {
        "description": "Dépose revêtement + pose isolant rigide + chape + nouveau revêtement.",
        "prix_m2_pose": {"min": 40, "max": 80},
        "inconvenient": "Rehausse le sol de 8-15 cm → problème de seuils, portes, escaliers."
      },
      "sur_terre_plein": {
        "description": "Cas le plus complexe : décaissement + isolant + dalle + chape.",
        "prix_m2_pose": {"min": 60, "max": 120},
        "note": "Gros oeuvre. À coupler avec rénovation globale."
      },
      "flocage_sous_plancher": {
        "description": "Projection de mousse PUR ou laine de roche sous le plancher.",
        "prix_m2_pose": {"min": 25, "max": 45},
        "note": "Pour vides sanitaires de faible hauteur."
      }
    },
    "aides_2026": {
      "prime_CEE": "Oui, en geste seul.",
      "TVA": "5.5%",
      "MaPrimeRenov": "Uniquement en Parcours accompagné (depuis 2026)"
    }
  }
}
```

---

## 7. ISOLATION ACOUSTIQUE (PHONIQUE)

```json
{
  "isolation_acoustique": {
    "note": "L'isolation acoustique est souvent combinée avec l'isolation thermique mais utilise des matériaux à forte densité.",
    "indicateurs": {
      "Rw": "Indice d'affaiblissement acoustique (dB). Plus c'est haut, meilleur c'est.",
      "Lw": "Niveau de bruit de choc transmis (dB). Plus c'est bas, meilleur c'est."
    },
    "solutions": {
      "cloison_acoustique_placo": {
        "description": "Double parement placo + laine minérale entre ossature.",
        "Rw": "45-60 dB selon épaisseur",
        "prix_m2_pose": {"min": 40, "max": 80}
      },
      "doublage_mur_mitoyen": {
        "description": "Ossature désolidarisée + laine de roche haute densité + placo.",
        "Rw_gain": "+10 à +20 dB",
        "prix_m2_pose": {"min": 50, "max": 100}
      },
      "plancher_acoustique": {
        "description": "Sous-couche résiliente + chape flottante. Réduit bruits de choc.",
        "epaisseur": "3-10 mm sous-couche + chape 5-7 cm",
        "prix_m2_pose": {"min": 30, "max": 60}
      },
      "plafond_acoustique": {
        "description": "Faux plafond suspendu désolidarisé + laine de roche.",
        "prix_m2_pose": {"min": 50, "max": 90}
      }
    },
    "meilleurs_isolants_phoniques": ["Laine de roche haute densité", "Fibre de bois dense", "Ouate de cellulose", "Liège"],
    "norme": "NRA (Nouvelle Réglementation Acoustique), DTU 25.41, DTU 58.1"
  }
}
```

---

## 8. OUTILLAGE ISOLATEUR

```json
{
  "outillage": [
    {"outil": "Machine à souffler (location)", "prix_jour": {"min": 80, "max": 150}, "note": "Pour soufflage combles. Artisan a généralement la sienne."},
    {"outil": "Couteau à laine (longueur 40 cm)", "prix": {"min": 15, "max": 35}},
    {"outil": "Agrafeuse murale pneumatique", "prix": {"min": 50, "max": 150}},
    {"outil": "Cisaille à panneaux isolants", "prix": {"min": 20, "max": 50}},
    {"outil": "Scie à guichet (découpe placo)", "prix": {"min": 8, "max": 20}},
    {"outil": "Scie circulaire plongeante (découpe panneaux)", "prix": {"min": 150, "max": 400}},
    {"outil": "Visseuse placo (embrayage)", "prix": {"min": 80, "max": 200}},
    {"outil": "Lève-plaque placo", "prix": {"min": 150, "max": 400}},
    {"outil": "Pince à sertir rails/montants", "prix": {"min": 15, "max": 40}},
    {"outil": "Niveau laser lignes", "prix": {"min": 80, "max": 300}},
    {"outil": "Perforateur burineur (fixation rails)", "prix": {"min": 100, "max": 350}},
    {"outil": "Caméra thermique (diagnostic)", "prix": {"min": 200, "max": 3000}},
    {"outil": "Hygromètre (mesure humidité mur)", "prix": {"min": 30, "max": 100}},
    {"outil": "EPI : masque FFP2, combinaison, gants", "prix_kit": {"min": 20, "max": 50}}
  ]
}
```

---

## 9. MISES EN OEUVRE — TOUTES LES INTERVENTIONS AVEC PRIX

### 9.1 Combles et toiture

| Intervention | Unité | Prix MO+fourniture HT | Note |
|-------------|-------|----------------------|------|
| Soufflage combles perdus laine minérale (R≥7) | m² | 20-35€ | 30-40 cm d'épaisseur |
| Soufflage combles perdus ouate de cellulose (R≥7) | m² | 25-50€ | Recommandé |
| Déroulage rouleaux laine minérale combles (R≥7) | m² | 25-55€ | 2 couches croisées |
| Isolation rampants sous chevrons (ITI, R≥6) | m² | 40-80€ | + placo ou lambris |
| Sarking fibre de bois 140mm (ITE toiture) | m² | 100-160€ | Hors couverture |
| Sarking polyuréthane 100mm | m² | 80-130€ | |
| Dépose ancien isolant combles + évacuation | m² | 5-15€ | |
| Pose trappe combles isolée | u | 80-200€ | |

### 9.2 Murs

| Intervention | Unité | Prix MO+fourniture HT | Note |
|-------------|-------|----------------------|------|
| ITI doublage collé (PSE+placo) | m² | 30-60€ | Mur lisse et sain |
| ITI ossature métallique + laine + placo | m² | 40-80€ | Standard |
| ITI ossature + fibre de bois + placo | m² | 55-90€ | Haut de gamme confort été |
| ITE sous enduit PSE blanc 120mm | m² | 120-180€ | Économique |
| ITE sous enduit PSE graphité 140mm | m² | 140-200€ | Standard performant |
| ITE sous enduit laine de roche 140mm | m² | 150-220€ | Phonique + feu |
| ITE sous enduit fibre de bois 140mm | m² | 160-240€ | Confort été |
| ITE sous bardage bois | m² | 180-280€ | Esthétique, PLU à vérifier |
| Insufflation ouate en mur creux | m² | 25-50€ | Murs doubles existants |

### 9.3 Plancher bas

| Intervention | Unité | Prix MO+fourniture HT | Note |
|-------------|-------|----------------------|------|
| Isolation sous plancher (cave/vide sanitaire) PUR | m² | 25-50€ | Le plus courant |
| Isolation sous plancher laine de roche rigide | m² | 20-40€ | |
| Isolation sur plancher existant (rehausse) | m² | 40-80€ | + chape + revêtement |
| Flocage mousse PUR sous plancher | m² | 25-45€ | Vides sanitaires bas |

### 9.4 Fenêtres (pour mémoire — coordination isolateur)

| Intervention | Unité | Prix MO+fourniture HT | Note |
|-------------|-------|----------------------|------|
| Remplacement fenêtre double vitrage PVC | u | 400-800€ | Standard |
| Remplacement fenêtre double vitrage bois | u | 600-1200€ | Patrimoine/ABF |
| Remplacement fenêtre double vitrage alu | u | 500-1000€ | Contemporain |
| Remplacement fenêtre triple vitrage | u | 700-1500€ | RE2020 / très froid |
| Remplacement porte-fenêtre PVC | u | 600-1200€ | |
| Remplacement baie vitrée coulissante alu | u | 1000-3000€ | |

### 9.5 Forfaits courants

| Type de maison | Travaux | Budget HT | Note |
|---------------|---------|-----------|------|
| Maison 100m² combles perdus | Soufflage laine minérale R≥7 | 2 000-3 500€ | 3-5h de chantier |
| Maison 100m² ITI murs | Ossature + laine + placo (80m² de murs) | 3 200-6 400€ | 5-8 jours |
| Maison 100m² ITE sous enduit | PSE graphité + enduit (100m² façade) | 12 000-20 000€ | 2-4 semaines |
| Maison 100m² plancher bas cave | PUR sous plancher (80m²) | 2 000-4 000€ | 1-2 jours |
| Rénovation globale complète | Combles + murs ITI + plancher + fenêtres | 25 000-50 000€ | Parcours accompagné |

---

## 10. TAUX HORAIRE ISOLATEUR / PLAQUISTE

```json
{
  "taux_horaire": {
    "plaquiste_isolateur": {"min": 35, "max": 55, "unite": "€ HT/h"},
    "facade_ITE": {"min": 40, "max": 65, "unite": "€ HT/h"},
    "souffleur_combles": {"min": 35, "max": 50, "unite": "€ HT/h"},
    "equipe_2_personnes": {"min": 70, "max": 110, "unite": "€ HT/h"}
  },
  "rendement": {
    "soufflage_combles": "80-120 m²/jour (équipe 2 pers)",
    "ITI_doublage_colle": "10-15 m²/jour/compagnon",
    "ITI_ossature_placo": "8-12 m²/jour/compagnon",
    "ITE_sous_enduit": "5-8 m²/jour/compagnon",
    "ITE_sous_bardage": "4-7 m²/jour/compagnon"
  }
}
```

---

## 11. RÉGLEMENTATION ET OBLIGATIONS

```json
{
  "reglementation_isolation": [
    {"regle": "RE 2020 (construction neuve)", "detail": "Entrée en vigueur 2022. Impose R ≥ 7 pour combles, R ≥ 4.2 pour murs, R ≥ 3 pour plancher bas. Indicateur Bbio (besoin bioclimatique) + IC (impact carbone) des matériaux."},
    {"regle": "RT Existant par élément", "detail": "En rénovation, si on isole un élément : R minimum obligatoire. Murs : R ≥ 2.3 à 3.2 (selon altitude). Combles : R ≥ 4.8 à 5.2. Plancher bas : R ≥ 2 à 2.7."},
    {"regle": "R minimum pour aides", "detail": "Pour MaPrimeRénov'/CEE : combles perdus R ≥ 7, rampants R ≥ 6, murs R ≥ 3.7, plancher bas R ≥ 3. Plus exigeant que la RT Existant."},
    {"regle": "RGE obligatoire pour aides", "detail": "L'artisan DOIT être certifié RGE (Qualibat, RGE Qualibat 7141, etc.) pour que le client bénéficie des aides."},
    {"regle": "MaPrimeRénov' 2026 — Changement majeur", "detail": "Depuis janvier 2026, l'isolation seule (murs, fenêtres) n'est PLUS éligible au Parcours par geste. Elle est uniquement finançable via le Parcours accompagné (rénovation d'ampleur, gain ≥ 2 classes DPE, audit énergétique, Mon Accompagnateur Rénov'). EXCEPTION : l'isolation des combles perdus reste éligible aux CEE en geste seul."},
    {"regle": "DPE et passoires thermiques", "detail": "Depuis 2025, les logements classés G sont interdits à la location. En 2028, les F seront interdits. En 2034, les E. La rénovation énergétique est URGENTE pour les propriétaires bailleurs."},
    {"regle": "Amiante", "detail": "Tout bâtiment construit avant juillet 1997 peut contenir de l'amiante (flocage, dalles de sol, colles, enduits). Diagnostic amiante avant travaux (DAT) obligatoire avant dépose d'anciens matériaux."},
    {"regle": "DTU isolation", "detail": "DTU 45.10 (isolation combles perdus), DTU 45.11 (isolation combles aménageables), DTU 25.41 (doublage placo), DTU 45.4 (ITE sous enduit), DTU 41.2 (bardage)."}
  ]
}
```

---

## 12. ERREURS COURANTES TERRAIN (pour le cerveau IA)

1. **Isolation sans traitement de l'humidité** : Poser un isolant sur un mur humide = moisissures garanties sous 1-2 ans. TOUJOURS diagnostiquer et traiter l'humidité AVANT d'isoler.
2. **Pare-vapeur oublié ou mal posé** : En ITI, le pare-vapeur doit être côté CHAUD (intérieur). S'il est percé ou absent → condensation dans l'isolant → dégradation. Chaque percement (prise, interrupteur) doit être colmaté.
3. **Pont thermique aux jonctions mur/plancher** : L'isolation s'arrête au niveau du plancher → le froid passe par le nez de dalle. Prévoir un retour d'isolant sur la tranche du plancher.
4. **Soufflage sur ancien isolant humide** : Si l'ancien isolant est mouillé ou moisi, il faut le DÉPOSER avant de souffler par-dessus. Sinon le nouvel isolant s'imbibe.
5. **ITE sans traitement des points singuliers** : Tableaux de fenêtres, angles, soubassement, débord de toit → si ces zones ne sont pas traitées, c'est là que le froid passe (ponts thermiques résiduels).
6. **ITE en zone ABF sans autorisation** : L'ITE modifie l'aspect extérieur → déclaration préalable obligatoire. En zone ABF, l'architecte des Bâtiments de France peut refuser l'ITE.
7. **Épaisseur insuffisante pour les aides** : L'artisan pose 10 cm de laine de verre (R=2.5) en combles au lieu de 30 cm (R=7) → le client perd les CEE. TOUJOURS vérifier le R cible.
8. **Ventilation non adaptée après isolation** : Isoler sans ventiler = problème d'humidité + qualité d'air. TOUJOURS vérifier la VMC et la proposer si absente.
9. **Combles soufflés avec boîtiers électriques non repérés** : Les boîtiers DCL et les spots encastrés sont des sources d'incendie si recouverts d'isolant. Poser des capots de protection (4-8€/pièce).
10. **ITE sans prolongation du débord de toit** : L'épaisseur de l'ITE (12-16 cm) dépasse parfois sous le débord de toit → l'eau coule sur l'isolant. Prolonger le débord ou poser un larmier.
11. **Isolation du plancher sans isolation des murs** : Isoler le plancher sans les murs = effet "pieds chauds, corps froid". Prioriser : combles d'abord, puis murs, puis plancher.
12. **Devis sans mention du R atteint** : Le devis DOIT mentionner la résistance thermique R de l'isolant posé. Sans cette mention, les aides ne peuvent pas être déclenchées.

---

## 13. COEFFICIENTS RÉGIONAUX — ISOLATION

| Zone | Coefficient | Note |
|------|------------|------|
| Paris intra-muros | ×1.20 à ×1.35 | Contraintes copropriété, accès, stationnement échafaudage |
| Petite couronne IdF | ×1.10 à ×1.25 | |
| Grande couronne IdF | ×1.00 à ×1.15 | |
| Lyon, Marseille, Bordeaux, Toulouse | ×1.00 à ×1.10 | |
| Grandes villes | ×0.95 à ×1.05 | |
| Villes moyennes | ×0.90 à ×1.00 | Base du référentiel |
| Zones rurales | ×0.85 à ×0.95 | |
| Montagne (altitude > 800m) | ×1.05 à ×1.15 | Épaisseurs plus importantes (R plus élevés) |
| DOM-TOM | ×1.15 à ×1.40 | Isolation principalement pour le confort été (déphasage) |
| Zone ABF | ×1.10 à ×1.25 | Techniques et matériaux parfois imposés |

---

## 14. COORDINATION AVEC LES AUTRES CORPS DE MÉTIER

| Situation | Corps de métier | Séquence |
|-----------|----------------|----------|
| ITI + électricité | Isolateur/plaquiste + électricien | Électricien tire les gaines AVANT la fermeture du doublage |
| ITI + plomberie | Isolateur/plaquiste + plombier | Plombier passe les tuyaux AVANT le doublage |
| ITE + couverture | Façadier ITE + couvreur | Coordonner : débord de toit peut nécessiter rallonge. Couvreur d'abord si réfection couverture |
| ITE + menuiseries | Façadier ITE + menuisier | Menuiseries posées AVANT ou PENDANT l'ITE (pose en tunnel ou en applique extérieure) |
| Combles + VMC | Isolateur + chauffagiste | VMC installée AVANT le soufflage (gaines protégées). Si VMC double flux → passage gaines avant isolation |
| Isolation + chauffage | Isolateur + chauffagiste | TOUJOURS isoler d'abord, PUIS dimensionner le chauffage. Un logement bien isolé a besoin d'une PAC moins puissante (= moins chère). |

---

## 15. AIDES FINANCIÈRES 2026 — SYNTHÈSE ISOLATION

```json
{
  "aides_isolation_2026": {
    "combles_perdus": {
      "MaPrimeRenov_geste": "NON (supprimé en 2026 pour isolation seule)",
      "prime_CEE": "Oui, en geste seul. Environ 5-13€/m² selon revenus.",
      "TVA": "5.5%",
      "eco_PTZ": "Oui, 15 000€ max"
    },
    "rampants_combles_amenageables": {
      "MaPrimeRenov": "Uniquement Parcours accompagné (rénovation ampleur)",
      "prime_CEE": "Oui, en geste seul. Environ 8-25€/m² selon revenus.",
      "TVA": "5.5%"
    },
    "murs_ITI_ou_ITE": {
      "MaPrimeRenov": "Uniquement Parcours accompagné depuis janvier 2026. Aide 40-75€/m² (ITE) selon revenus.",
      "prime_CEE": "Oui, en geste seul. Environ 7-12€/m².",
      "TVA": "5.5%"
    },
    "plancher_bas": {
      "MaPrimeRenov": "Uniquement Parcours accompagné",
      "prime_CEE": "Oui, en geste seul.",
      "TVA": "5.5%"
    },
    "fenetres": {
      "MaPrimeRenov": "Uniquement Parcours accompagné",
      "prime_CEE": "Limitée",
      "TVA": "5.5%"
    },
    "parcours_accompagne": {
      "condition": "Gain ≥ 2 classes DPE, audit énergétique préalable, Mon Accompagnateur Rénov', au moins 2 gestes d'isolation",
      "aide_max": "Jusqu'à 80% du coût HT pour ménages très modestes",
      "plafond": "70€/m² pour murs, 75€/m² pour combles"
    },
    "condition_RGE": "TOUTES les aides nécessitent un artisan RGE certifié",
    "note_ia": "En 2026, pour maximiser les aides, TOUJOURS proposer un Parcours accompagné (rénovation globale) plutôt que des gestes isolés. Combles + murs + fenêtres + PAC = gain de 2-3 classes DPE et aides maximales."
  }
}
```

---

## 16. RÈGLES IA PRICING — CERVEAU STRUCTORAI

### Règle 1 — Toujours donner des FOURCHETTES
Prix min-max, JAMAIS un prix unique.

### Règle 2 — Appliquer le coefficient régional

### Règle 3 — Déférer aux prix de l'artisan (Mem0)

### Règle 4 — Alerter sur les prix anormaux (< 70% ou > 150%)

### Règle 5 — TOUJOURS mentionner le R atteint
```
SI devis isolation → VÉRIFIER
  "Le devis mentionne-t-il la résistance thermique R atteinte ?
   R ≥ 7 (combles perdus), R ≥ 6 (rampants), R ≥ 3.7 (murs), R ≥ 3 (plancher).
   Sans cette mention, les aides CEE ne peuvent pas être déclenchées."
```

### Règle 6 — Informer du changement MaPrimeRénov' 2026
```
SI isolation murs ou fenêtres seules → INFORMER
  "Depuis janvier 2026, l'isolation des murs/fenêtres seule n'est plus éligible à MaPrimeRénov'.
   Pour bénéficier de MaPrimeRénov', il faut un Parcours accompagné (rénovation globale, gain ≥ 2 classes DPE).
   Les primes CEE restent accessibles en geste seul."
```

### Règle 7 — Proposer le Parcours accompagné
```
SI plusieurs gestes d'isolation prévus → SUGGÉRER
  "En combinant combles + murs + fenêtres, vous êtes éligible au Parcours accompagné MaPrimeRénov'.
   Aide jusqu'à 80% pour les ménages modestes. Un audit énergétique est nécessaire (300-800€, partiellement aidé)."
```

### Règle 8 — Vérifier l'humidité avant ITI
```
SI isolation murs par l'intérieur → ALERTER
  "Avez-vous vérifié l'humidité du mur ? Un mur humide NE DOIT PAS être isolé sans traitement préalable.
   Diagnostiquer : remontées capillaires, infiltrations, condensation."
```

### Règle 9 — Coupler isolation et VMC
```
SI travaux d'isolation → RAPPELER
  "Après isolation, le logement sera plus étanche. La ventilation devient CRITIQUE.
   Si pas de VMC → proposer a minima une VMC simple flux hygro B (600-2 400€).
   Si rénovation globale → proposer VMC double flux (4 500-10 000€)."
```

---

## 17. DEVIS TYPES — EXEMPLES CHIFFRÉS

### Devis type 1 : Soufflage combles perdus ouate de cellulose — Maison 100m²
| Poste | Quantité | Prix unitaire HT | Total HT |
|-------|----------|-----------------|----------|
| Vérification état charpente et repérage électrique | 1 forfait | 100€ | 100€ |
| Pose capots de protection spots/boîtiers | 8 u | 6€ | 48€ |
| Dépose ancien isolant laine de verre tassée + évacuation | 100 m² | 8€/m² | 800€ |
| Soufflage ouate de cellulose Univercell (R=7, ép. 33 cm) | 100 m² | 30€/m² | 3 000€ |
| Pose trappe combles isolée 60×60 | 1 u | 150€ | 150€ |
| Nettoyage fin de chantier | 1 forfait | 80€ | 80€ |
| **TOTAL HT** | | | **4 178€** |
| TVA 5.5% (logement > 2 ans, artisan RGE) | | | 230€ |
| **TOTAL TTC** | | | **4 408€** |
| **Aides estimées** | | | |
| Prime CEE (ménage intermédiaire, ~10€/m²) | 100 m² | | -1 000€ |
| **RESTE À CHARGE** | | | **~3 408€** |

### Devis type 2 : ITE sous enduit PSE graphité — Maison plain-pied (100m² façade)
| Poste | Quantité | Prix unitaire HT | Total HT |
|-------|----------|-----------------|----------|
| Location + montage/démontage échafaudage | 100 m² | 12€/m² | 1 200€ |
| Préparation support + nettoyage façade | 100 m² | 5€/m² | 500€ |
| Fourniture + pose PSE graphité 140mm (R=4.5) + chevilles | 100 m² | 35€/m² | 3 500€ |
| Traitement points singuliers (tableaux, angles, soubassement) | 60 ml | 25€/ml | 1 500€ |
| Profilé de départ alu + profilé d'angle | 40 ml | 8€/ml | 320€ |
| Sous-enduit armé treillis fibre de verre | 100 m² | 18€/m² | 1 800€ |
| Enduit de finition taloché teinté masse | 100 m² | 15€/m² | 1 500€ |
| Remplacement 6 appuis de fenêtre alu (prolongés ITE) | 6 u | 80€ | 480€ |
| Dépose/repose volets (4 paires) | 4 u | 40€ | 160€ |
| Protection sols + nettoyage fin chantier | 1 forfait | 200€ | 200€ |
| **TOTAL HT** | | | **11 160€** |
| TVA 5.5% | | | 614€ |
| **TOTAL TTC** | | | **11 774€** |
| **Aides estimées (Parcours accompagné, ménage jaune)** | | | |
| MaPrimeRénov' Parcours accompagné (~55€/m²) | | | -5 500€ |
| Prime CEE (~10€/m²) | | | -1 000€ |
| **RESTE À CHARGE** | | | **~5 274€** |

### Devis type 3 : ITI murs ossature + isolation combles — Rénovation globale
| Poste | Quantité | Prix unitaire HT | Total HT |
|-------|----------|-----------------|----------|
| **COMBLES PERDUS** | | | |
| Soufflage ouate de cellulose (R=7, 33cm) | 90 m² | 30€/m² | 2 700€ |
| **MURS ITI** | | | |
| Dépose ancien doublage (3 pièces) | 50 m² | 10€/m² | 500€ |
| Ossature métallique + laine de roche 120mm (R=3.7) + placo BA13 | 80 m² | 55€/m² | 4 400€ |
| Déplacement prises et interrupteurs | 12 u | 45€ | 540€ |
| Reprise encadrements fenêtres | 6 u | 80€ | 480€ |
| Bande à joints + enduit + ponçage | 80 m² | 8€/m² | 640€ |
| Peinture blanche 2 couches | 80 m² | 10€/m² | 800€ |
| **PLANCHER BAS** | | | |
| Isolation sous plancher cave PUR 100mm (R=4.5) | 70 m² | 35€/m² | 2 450€ |
| **TOTAL HT** | | | **12 510€** |
| TVA 5.5% | | | 688€ |
| **TOTAL TTC** | | | **13 198€** |
| **Aides estimées (Parcours accompagné, ménage bleu)** | | | |
| MaPrimeRénov' Parcours accompagné | | | -8 000€ |
| Prime CEE | | | -2 500€ |
| **RESTE À CHARGE** | | | **~2 698€** |

---

> **Note pour le cerveau IA :** Ce référentiel couvre l'intégralité du métier isolation :
> 1. Matériaux isolants (13 isolants comparés : 2 laines minérales, 5 biosourcés, 4 synthétiques, 1 projeté, + tableau épaisseur/R)
> 2. Combles perdus (4 techniques : soufflage laine/ouate, déroulage, panneaux, + travaux annexes) avec prix
> 3. Combles aménageables / rampants (3 techniques : entre chevrons ITI, sarking ITE, caissons) avec prix
> 4. ITI murs (3 techniques : doublage collé, ossature métallique, insufflation) avec prix + travaux annexes
> 5. ITE murs (3 techniques : sous enduit, sous bardage, sous vêture + détail finitions) avec prix
> 6. Plancher bas (4 techniques selon configuration : cave, vide sanitaire, sur sol, terre-plein) avec prix
> 7. Isolation acoustique (4 solutions : cloison, doublage, plancher, plafond) avec prix
> 8. Outillage isolateur (14 outils) avec prix
> 9. Mises en oeuvre (25+ interventions) avec prix + forfaits par type de maison
> 10. Taux horaires et rendements par technique
> 11. Réglementation complète (RE 2020, RT Existant, R minimaux, aides, DPE passoires, amiante, DTU)
> 12. 12 erreurs courantes terrain (humidité, pare-vapeur, ponts thermiques, spots, VMC...)
> 13. Coefficients régionaux (10 zones)
> 14. Coordination inter-corps de métier (6 situations critiques)
> 15. Synthèse aides 2026 (CHANGEMENT MAJEUR : MaPrimeRénov' geste seul supprimé pour murs/fenêtres, Parcours accompagné uniquement)
> 16. Règles IA pricing (9 règles dont R obligatoire, changement MaPrimeRénov', couplage VMC)
> 17. 3 devis types chiffrés (combles ouate 100m² → reste 3 408€, ITE PSE graphité 100m² → reste 5 274€, rénovation globale combles+murs+plancher → reste 2 698€)
