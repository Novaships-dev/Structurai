---
name: METIER
description: Constitution BTP immuable — TVA, 47 mentions obligatoires devis, mentions facture, Factur-X, e-invoicing, retenue de garantie, mention déchets, coefficients régionaux, sanctions. Source de vérité ultime pour tout le code métier. Équivalent du BIBLE.md d'Ouroboros.
type: constitution
scope: all-agents, pdf-service, tva-engine, factur-x, knowledge-base
priority: immuable
date: 2026-04-17
version: 1.0
sources:
  - legifrance.gouv.fr
  - impots.gouv.fr
  - service-public.gouv.fr
  - economie.gouv.fr
  - Code de la consommation (art. L111-1, L221-5, L221-18)
  - Code du commerce (art. L441-9, L441-10)
  - Code de la construction et de l'habitation (art. L111-13, L111-15)
  - Loi n°71-584 du 16 juillet 1971 (retenue de garantie)
  - Arrêté du 29 décembre 2020 (mention déchets)
  - Arrêté du 15 décembre 2025 (intérêt légal S1 2026)
  - Ordonnance n°2021-1190 du 15 septembre 2021 (facturation électronique)
---

# docs/METIER.md — Constitution BTP STRUCTORAI

> **Ce fichier est la LOI.** Aucun agent, aucun service, aucun prompt ne peut violer les règles ci-dessous.
> En cas de contradiction dans un autre fichier du repo, c'est ce fichier qui fait foi.
> Toute modification DOIT être validée par Fabrice ET loguée dans `docs/CHANGELOG.md`.

---

## 0. SOMMAIRE

1. [TVA BTP — les 3 taux](#1-tva-btp--les-3-taux)
2. [Les 47 mentions obligatoires devis BTP](#2-les-47-mentions-obligatoires-devis-btp)
3. [Mentions obligatoires facture (+4 nouvelles 2026)](#3-mentions-obligatoires-facture-4-nouvelles-2026)
4. [Mention déchets obligatoire](#4-mention-déchets-obligatoire)
5. [Retenue de garantie 5%](#5-retenue-de-garantie-5)
6. [Factur-X — spécifications techniques](#6-factur-x--spécifications-techniques)
7. [Dates e-invoicing / e-reporting](#7-dates-e-invoicing--e-reporting)
8. [Sanctions exhaustives](#8-sanctions-exhaustives)
9. [11 zones coefficients régionaux](#9-11-zones-coefficients-régionaux)
10. [Règle d'or — zéro invention](#10-règle-dor--zéro-invention)
11. [Sources officielles](#11-sources-officielles)

---

## 1. TVA BTP — LES 3 TAUX

### 1.1 — Taux à 5,5% — Rénovation énergétique

**Conditions cumulatives (TOUTES requises) :**
- Logement achevé depuis + de 2 ans
- Usage d'habitation (résidence principale ou secondaire)
- Travaux d'amélioration de la performance énergétique
- Artisan certifié RGE (obligatoire pour ce taux)
- Pose ET installation ET entretien de matériaux/équipements éligibles

**Exemples éligibles :**
- Isolation thermique (combles, murs, planchers bas)
- Chaudière à condensation
- Pompe à chaleur (PAC air/eau, géothermique)
- Fenêtres double/triple vitrage
- Chauffe-eau thermodynamique ou solaire
- Système de ventilation double flux

**Attestation client (post-01/01/2025 — CERFA 1300-SD/1301-SD SUPPRIMÉS) :**
> Texte mention obligatoire sur devis/facture :
> *"Le client atteste que les travaux sont réalisés dans un logement achevé depuis plus de 2 ans et affecté à l'habitation. Le client reconnaît avoir vérifié l'éligibilité des travaux au taux réduit de TVA et engage sa responsabilité en cas de contrôle fiscal."*

**Règle IA** : si l'artisan n'a pas de certification RGE valide (vérification F128), **BLOQUER** la génération d'un devis à 5,5% et proposer le taux à 10%.

---

### 1.2 — Taux à 10% — Travaux de rénovation

**Conditions cumulatives :**
- Logement achevé depuis + de 2 ans
- Usage d'habitation
- Travaux d'amélioration, transformation, aménagement ou entretien
- S'applique à la main d'oeuvre ET aux matériaux fournis par l'artisan

**Exclusions (ne s'applique PAS) :**
- Équipements ménagers ou mobiliers
- Climatisation simple (sauf réversible)
- Travaux qui augmentent la surface habitable de + 10%
- Matériaux achetés séparément par le client (restent à 20%)

**Attestation client** : même mention texte que 1.1 (post-CERFA 2025).

---

### 1.3 — Taux à 20% — Taux normal

**Conditions (l'un des cas suivants) :**
- Construction neuve
- Logement de - de 2 ans
- Locaux professionnels (bureaux, commerces, entrepôts)
- Fournitures achetées séparément par le client
- Équipements non éligibles aux taux réduits

**Attestation client** : aucune.

---

### 1.4 — Cas particuliers

**Autoliquidation sous-traitance BTP :**
- Le sous-traitant facture HT (sans TVA)
- Le donneur d'ordre auto-liquide la TVA (déclare à la fois collectée et déductible)
- Mention obligatoire sur la facture : *"Autoliquidation — Art. 283, 2 nonies du CGI"*

**Franchise en base (auto-entrepreneurs sous seuils) :**
- Seuil 2026 vente : 203 100€ — Seuil 2026 services BIC : 83 600€
- Mention obligatoire : *"TVA non applicable, art. 293 B du CGI"*

**Devis mixte multi-taux :**
- Un même devis PEUT contenir des lignes à taux différents
- Exemple : rénovation salle de bain → 10% (pose) + 20% (meuble vasque fourni séparément)
- Chaque ligne doit afficher son taux, son HT, sa TVA, son TTC

---

## 2. LES 47 MENTIONS OBLIGATOIRES DEVIS BTP

> **Répartition** : 15 mentions légales obligatoires + 32 mentions complémentaires BTP = **47 mentions totales**.
> **Toutes DOIVENT figurer sur tout devis généré par STRUCTORAI.**
> Sanction si absent : voir §8.

### 2.1 — Les 15 mentions LÉGALES obligatoires

| # | Mention | Source légale | Détail |
|---|---|---|---|
| 1 | Terme "Devis" ou "Proposition de prix" | Art. L111-1 Code conso | Dès le titre du document |
| 2 | Date du devis | Art. L441-9 C. com | Format JJ/MM/AAAA |
| 3 | Numéro de devis unique | Art. L441-9 C. com | Format recommandé : `DEV-2026-001` (chrono continu) |
| 4 | Identité artisan : raison sociale, forme juridique (EI/SARL/SAS/SASU/EURL/MicroEntreprise), adresse siège | Art. R123-237 C. com | Tous les champs obligatoires |
| 5 | Numéro SIRET (14 chiffres) | Art. R123-237 C. com | ⚠️ **PAS le SIREN** (9 chiffres) |
| 6 | Numéro TVA intracommunautaire | Art. 286 ter CGI | Ou mention franchise : *"TVA non applicable – art. 293 B du CGI"* |
| 7 | Code APE/NAF | Art. R123-237 C. com | Format : `4334Z`, `4321A`, etc. |
| 8 | Assurance décennale : nom assureur, n° de police, zone géographique de couverture | Art. L241-1 C. assur + L111-15 CCH | Obligatoire pour tous travaux BTP |
| 9 | Qualifications professionnelles (si applicable) | Art. 16 loi 96-603 | RGE, Qualibat, Qualifelec, QualiPAC, Qualit'EnR |
| 10 | Identité client : nom, prénom, adresse | Art. L111-1 Code conso | Si personne morale : raison sociale + SIRET |
| 11 | Adresse du chantier | Bonne pratique BTP | Si différente de l'adresse client |
| 12 | Description détaillée des travaux : désignation, quantité, unité, prix unitaire HT par poste | Art. L111-1 Code conso | Chaque poste séparé, pas de forfait opaque |
| 13 | Prix : total HT par poste, total HT global, taux TVA, montant TVA par taux, total TTC | Art. L441-9 C. com | Multi-taux autorisé (voir §1.4) |
| 14 | Durée de validité du devis | Art. L111-1 Code conso | Recommandé : 30 à 90 jours |
| 15 | Conditions de paiement | Art. L111-1 Code conso | Acompte (max 30% pour devis > 300€), échéancier, pénalités retard (min 3× taux légal = 7,86% S1 2026), indemnité forfaitaire recouvrement 40€ |

---

### 2.2 — Les 32 mentions COMPLÉMENTAIRES BTP

> Ces mentions sont imposées par la pratique BTP, la jurisprudence, les conventions collectives BTP et/ou les obligations sectorielles. STRUCTORAI les applique toutes.

| # | Mention | Source / motif |
|---|---|---|
| 16 | Date prévisionnelle de début des travaux | Art. L111-1 Code conso |
| 17 | Durée estimée des travaux (en jours ouvrés) | Art. L111-1 Code conso |
| 18 | Conditions de révision de prix (si révisable) | Bonne pratique BTP, art. 1710 C. civ |
| 19 | Droit de rétractation 14 jours (démarchage à domicile B2C) | Art. L221-18 Code conso |
| 20 | Formulaire de rétractation type annexé | Art. L221-5 Code conso |
| 21 | Attestation TVA réduite (texte signé par client) | Art. 279-0 bis CGI — post-CERFA 2025 |
| 22 | Clause *"Toute prestation non prévue fera l'objet d'un devis complémentaire"* | Jurisprudence Cass. civ. 3e, 12/06/2013 |
| 23 | Mention manuscrite client : *"Devis reçu avant l'exécution des travaux, lu et accepté, bon pour accord"* + date + signature | Art. 1376 C. civ |
| 24 | Montant de l'acompte demandé (en % et en €) | Art. L111-1 Code conso |
| 25 | Modalités de paiement (virement, chèque, CB, espèces ≤ 1 000€) | Art. L112-6 C. monét. fin. |
| 26 | IBAN / BIC artisan (si virement) | Obligation SEPA |
| 27 | Coordonnées bancaires pour acompte | Bonne pratique |
| 28 | Conditions de sous-traitance (si applicable) | Loi n°75-1334 du 31/12/1975 |
| 29 | Mention déchets obligatoire (voir §4) | Arrêté 29/12/2020 |
| 30 | Estimation quantité déchets (tonnes ou m³) | Arrêté 29/12/2020 |
| 31 | Modalités de tri des déchets sur chantier | Arrêté 29/12/2020 |
| 32 | Centre de collecte prévu | Arrêté 29/12/2020 |
| 33 | Coût estimé de gestion des déchets (ligne séparée) | Arrêté 29/12/2020 |
| 34 | Référentiel DTU applicable (ex: DTU 60.1 plomberie, DTU 25.41 plaques de plâtre) | Norme NF DTU |
| 35 | Normes électriques applicables (NF C 15-100 pour logements) | Arrêté 24/12/2015 |
| 36 | Garantie de parfait achèvement (1 an) | Art. 1792-6 C. civ |
| 37 | Garantie biennale de bon fonctionnement (2 ans) | Art. 1792-3 C. civ |
| 38 | Garantie décennale (10 ans) — rappel | Art. 1792 et s. C. civ |
| 39 | Attestation RGE (numéro + date validité) si TVA 5,5% | Décret 2014-812 du 16/07/2014 |
| 40 | Attestation Qualibat (si applicable) | Cahier des charges Qualibat |
| 41 | Conditions d'intervention (horaires, protection client, nettoyage) | Bonne pratique |
| 42 | Clause de propriété des matériaux (transfert à réception) | Art. 1582 C. civ |
| 43 | Clause de force majeure | Art. 1218 C. civ |
| 44 | Juridiction compétente en cas de litige | Bonne pratique |
| 45 | Mention médiateur de la consommation (B2C) | Art. L612-1 Code conso |
| 46 | Référence CGV (Conditions Générales de Vente) | Bonne pratique — obligatoire B2B |
| 47 | Option validité prix après révision réglementaire (TVA, barèmes) | Bonne pratique |

---

### 2.3 — Règle IA pour génération devis

Le service PDF (`backend/app/services/pdf_service.py`) :
1. Charge `data/legal/mentions_obligatoires_devis.json` (47 items)
2. Pour chaque mention, vérifie sa présence avant finalisation
3. Si une mention manque → **LÈVE une exception** et n'envoie pas le devis
4. Logue la validation dans `ai_decisions` (audit AI Act)

Voir `SKILL-MENTIONS-LEGALES.md` (à créer) pour implémentation.

---

## 3. MENTIONS OBLIGATOIRES FACTURE (+4 NOUVELLES 2026)

### 3.1 — Mentions standard facture (équivalentes au devis)

Toutes les mentions 1 à 15 du devis s'appliquent + spécificités :
- **Numéro de facture** : chronologique, continu, sans trou
- **Date de la facture**
- **Date de livraison / d'exécution de la prestation**
- **Numéro de bon de commande** (si applicable)

### 3.2 — Les 4 NOUVELLES mentions obligatoires facture électronique 2026

Ces 4 mentions deviennent obligatoires à partir du **1er septembre 2026** (réception) et **1er septembre 2027** (émission) pour la conformité e-invoicing :

| # | Mention | Source | Détail |
|---|---|---|---|
| F1 | SIREN du client (B2B uniquement) | Art. 242 nonies A annexe II CGI | Obligatoire depuis 01/07/2024 pour B2B |
| F2 | Adresse de livraison (si différente de facturation) | Art. 242 nonies A | Fréquent BTP : livraison matériaux sur chantier |
| F3 | Catégorie de l'opération | Art. 242 nonies A | Valeurs possibles : `livraison de biens` / `prestation de services` / `mixte` |
| F4 | Option TVA sur les débits | Art. 269 CGI | Mention obligatoire SI l'artisan a opté pour ce régime |

### 3.3 — Factures de situation BTP (avancement)

- Facturation par tranches de pourcentage d'avancement (30% → 60% → 100%)
- Chaque situation = facture à part entière avec toutes les mentions obligatoires
- Transite via la PA (Plateforme Agréée) au même titre qu'une facture classique (voir §7)
- Le cerveau IA calcule le reste à facturer et numérote chronologiquement

### 3.4 — Autres cas particuliers

- **Factures d'acompte** : mentions complètes + référence au devis signé
- **Avoirs** : numérotation distincte (format recommandé `AV-2026-001`) + référence facture d'origine
- **Factures rectificatives** : obligation d'émettre un avoir puis une nouvelle facture (jamais d'écrasement)

---

## 4. MENTION DÉCHETS OBLIGATOIRE

**Base légale** : Arrêté du 29/12/2020 — obligation entrée en vigueur le **01/07/2021**.

**Champ d'application** : TOUS les devis BTP, tous travaux qui génèrent des déchets, sans exception.

**4 éléments OBLIGATOIRES sur chaque devis :**

1. **Estimation de la quantité totale** de déchets générés (en tonnes ou m³)
2. **Modalités de tri sur le chantier** (tri sur site / tri en déchetterie / tri par filière : gravats / bois / métal / plâtre / plastique / DEEE)
3. **Centre de collecte / installation de traitement prévu** (nom + adresse + n° d'agrément ICPE si applicable)
4. **Estimation des coûts de gestion des déchets** (ligne séparée sur le devis, pas dans un forfait global)

**Règle IA** : l'Agent Devis calcule automatiquement les déchets en fonction du type de chantier (table de correspondance `data/diagnostics/dechets_par_chantier.json` à créer).

**Sanction si absent** : voir §8.

---

## 5. RETENUE DE GARANTIE 5%

**Base légale** : Loi n°71-584 du 16 juillet 1971.

### 5.1 — Principes

- Le maître d'ouvrage (client) **PEUT** retenir 5% du montant TTC des travaux
- Durée de retenue : **1 an** après réception des travaux
- Objectif : garantir la levée des réserves émises à la réception
- Libération automatique à l'expiration du délai de 1 an **si aucune réserve notifiée**

### 5.2 — Substitution par caution bancaire

L'artisan **a le droit** de substituer la retenue par une caution bancaire :
- Caution personnelle et solidaire d'un établissement financier agréé
- L'artisan conserve sa trésorerie
- Coût caution : ~0,5% à 1,5% du montant TTC / an (à intégrer dans le devis si l'artisan fait ce choix)

### 5.3 — Impact trésorerie — rôle du cerveau IA

- Le cerveau IA (Agent Fiscalité & Trésorerie F119-compagnon F129) **DOIT** intégrer la retenue 5% dans le suivi prévisionnel
- Alerter l'artisan : *"Retenue 5% sur chantier Dupont = 2 450€. Libération prévue le 15/09/2027 si aucune réserve."*
- Proposer la caution bancaire pour les gros chantiers (> 20 000€ TTC)

---

## 6. FACTUR-X — SPÉCIFICATIONS TECHNIQUES

**Standard européen** : EN 16931 (facture électronique hybride).

### 6.1 — Format technique

- **Conteneur** : PDF/A-3 (archivage long terme)
- **XML embarqué** : structure conforme EN 16931
- **Profils disponibles** (du plus simple au plus complet) :
  - `MINIMUM` — données minimales
  - `BASIC WL` (Without Lines) — sans lignes de détail
  - `BASIC` — avec lignes de détail
  - `EN 16931` — profil complet européen
  - `EXTENDED` — avec extensions BTP (recommandé)
  - `XRECHNUNG` — profil allemand (pour expansion DE)

**Profil par défaut STRUCTORAI** : `EXTENDED` avec extension **CIUS Construction** (Core Invoice Usage Specification spécifique BTP).

### 6.2 — Extension CIUS Construction BTP

Champs additionnels obligatoires pour le BTP :
- Référence au marché / bon de commande
- Adresse du chantier (distincte de l'adresse de facturation)
- Situation de travaux (n° de situation, pourcentage d'avancement)
- Acomptes déjà versés
- Retenue de garantie 5% (montant + durée)
- Pénalités de retard appliquées
- Informations TVA multi-taux par ligne

### 6.3 — Champs XML obligatoires (non exhaustif)

- SIRET émetteur et destinataire
- Numéro TVA intracommunautaire
- Montant HT par ligne, TVA, TTC
- Taux TVA appliqué par ligne (code : `S` standard, `AA` réduit, `Z` franchise)
- Date de facture, date d'échéance
- Numérotation chronologique continue

### 6.4 — Plateforme Agréée (PA)

Toute facture électronique B2B (et e-reporting B2C) DOIT transiter par une **Plateforme Agréée (PA)** immatriculée par la DGFiP.

**Options évaluées** (à ré-évaluer en Sprint 5) :
- **FactPulse** : 29€/mois minimum, intégration en 2h, sandbox gratuit
- **B2Brouter** : PA immatriculée, phase pilote gratuite jusqu'au 31/08/2026
- **Iopole** : PA #0018, SecNumCloud, ISO 27001

**Choix recommandé STRUCTORAI** : FactPulse en V1 pour vitesse d'intégration, B2Brouter évaluation post-launch.

### 6.5 — Règle IA génération PDF

Le service `pdf_service.py` DOIT :
1. Générer un PDF/A-3 valide
2. Embarquer le XML EN 16931 profil `EXTENDED` avec CIUS Construction
3. Valider le XML contre le schéma XSD officiel avant envoi
4. Transmettre à la PA via API marque blanche (pas de saisie manuelle)

Voir `SKILL-FACTURX.md` (à créer) pour implémentation.

---

## 7. DATES E-INVOICING / E-REPORTING

### 7.1 — Calendrier officiel

| Date | Obligation | Public concerné |
|---|---|---|
| **1er septembre 2026** | Réception obligatoire de factures électroniques | TOUTES les entreprises (y compris TPE/PME/micro) |
| **1er septembre 2026** | Émission obligatoire | Grandes entreprises + ETI |
| **1er septembre 2027** | Émission obligatoire | PME, TPE, micro-entreprises |

### 7.2 — E-reporting (distinct de l'e-invoicing)

**À partir du 1er septembre 2027** pour TPE/PME :

- Transmission des données de transaction **B2C** (particuliers) à l'administration fiscale via la PA
- Transmission des données de **paiement** pour les prestations de services
- Périodicité calquée sur le régime TVA de l'artisan (mensuel, trimestriel, annuel)

### 7.3 — Flux obligatoires BTP

TOUS les flux financiers BTP doivent transiter via la PA à partir de leur date d'obligation :
- Factures classiques
- **Factures de situation** (avancement chantier)
- **Factures d'acompte**
- **Avoirs** et **factures rectificatives**
- Mémoires de travaux (marchés publics)

### 7.4 — Règle IA — transmission invisible

L'artisan n'a rien à faire. Le service `pdf_service.py` + intégration PA rend la transmission automatique et invisible :
1. Devis signé → Facture générée
2. Facture → PDF/A-3 + XML EN 16931 profil EXTENDED CIUS Construction
3. Transmission API PA (FactPulse ou B2Brouter)
4. PA → PPF (Portail Public de Facturation)
5. PPF → destinataire (client B2B) ou administration (B2C + e-reporting)
6. Confirmation de dépôt retournée à l'artisan

---

## 8. SANCTIONS EXHAUSTIVES

### 8.1 — Devis non conforme

| Infraction | Sanction (personne physique) | Sanction (société) |
|---|---|---|
| Devis absent avant travaux | Jusqu'à **3 000€** | Jusqu'à **15 000€** |
| Mention décennale absente | **75 000€** + 6 mois d'emprisonnement | — |
| Mention obligatoire manquante (1 sur les 47) | Amende contraventionnelle selon gravité | Idem |
| Mention déchets absente | Jusqu'à **3 000€** | Jusqu'à **15 000€** |

**Base légale** : Art. L111-1 et L111-2 du Code de la consommation, art. 1792 C. civ, arrêté 29/12/2020.

### 8.2 — Facture non conforme

| Infraction | Sanction (personne physique) | Sanction (société) |
|---|---|---|
| Facture non conforme aux mentions | **75 000€** | **375 000€** |
| Numérotation non chronologique/continue | Idem | Idem |
| Absence SIREN client B2B | Idem | Idem |

**Base légale** : Art. L441-9 et L441-10 du Code de commerce, art. 242 nonies A annexe II CGI.

### 8.3 — Facturation électronique (à partir de sept 2026/2027)

| Infraction | Sanction |
|---|---|
| Facture non émise au format électronique (e-invoicing) | **50€/facture**, plafonnée 15 000€/an |
| Transmission e-reporting manquante | **500€/transmission**, plafonnée 15 000€/an |
| Non-désignation d'une PA en réception | **500€ à 1 000€** |

**Droit à l'erreur** : pas de sanction pour la 1ère infraction si régularisation sous **30 jours**.

**Base légale** : Ordonnance n°2021-1190 du 15/09/2021, art. 1737 CGI.

### 8.4 — TVA frauduleuse

| Infraction | Sanction |
|---|---|
| TVA réduite appliquée sans conditions remplies | Redressement TVA + intérêts 0,2%/mois + majoration 40% si mauvaise foi, 80% si manoeuvres frauduleuses |
| Absence d'attestation client TVA réduite | Rehaussement TVA (5,5% ou 10% → 20%) |

**Règle IA — protection artisan** : si F128 (validation RGE) détecte RGE invalide, **BLOQUER** tout devis à 5,5%.

### 8.5 — Pénalités de retard de paiement (référence)

**Taux S1 2026** (Arrêté du 15/12/2025 — JO 26/12/2025) :
- Créances dues aux particuliers : **6,67%**
- Créances dues aux professionnels : **2,62%**
- **Taux plancher pénalités B2B** : 3 × taux professionnel = **7,86%** (Art. L441-10 C. com)
- **Indemnité forfaitaire recouvrement** : **40€** par facture (Art. L441-10 C. com)

**Règle IA** : toujours afficher ces montants sur devis/facture — pas de valeur inférieure autorisée.

---

## 9. 11 ZONES COEFFICIENTS RÉGIONAUX

> Référence pour adapter les prix marché aux réalités régionales. Base = 1,00 (Province hors zones tendues).

| # | Zone | Coefficient | Détail |
|---|---|---|---|
| 1 | **Paris intra-muros** | **1,35** | Zone la plus tendue, coûts MO et fournisseurs élevés |
| 2 | **Île-de-France** (hors Paris) | **1,20** | 92, 93, 94, 77, 78, 91, 95 |
| 3 | **Grand Est** | **0,95** | 08, 10, 51, 52, 54, 55, 57, 67, 68, 88 |
| 4 | **Nord / Hauts-de-France** | **0,95** | 59, 62, 02, 60, 80 |
| 5 | **Ouest** | **1,00** | 44, 49, 53, 72, 85, 35, 22, 29, 56, 50, 14, 61 |
| 6 | **Sud-Ouest** | **0,95** | 33, 40, 64, 47, 24, 17, 16, 79, 86, 87, 19, 23, 31, 82, 32, 65, 12, 46, 81 |
| 7 | **Sud-Est** | **1,10** | 69, 01, 42, 26, 07, 38, 73, 74 |
| 8 | **Méditerranée / PACA** | **1,15** | 13, 84, 04, 05, 06, 83, 30, 34, 11, 66, 48 |
| 9 | **Montagne** | **1,10** | Altitude > 800m (stations, Alpes, Pyrénées, Massif central, Jura, Vosges) — supplément logistique |
| 10 | **DOM** (Guadeloupe/Martinique/Guyane/Réunion/Mayotte) | **1,25** | Coûts logistiques majorés, TVA spécifique (8,5% au lieu de 20% dans certains cas) |
| 11 | **Corse** | **1,15** | Coûts logistiques majorés, spécificités fiscales Corse |

### 9.1 — Application par le cerveau IA

1. Le cerveau détecte l'adresse du chantier (mention 11)
2. Il identifie la zone (code postal → zone)
3. Il applique le coefficient sur le prix référentiel : `prix_final = prix_ref × coefficient`
4. Il affiche : *"Prix ajusté zone [Paris intra-muros] : ×1,35"*

**Règle IA — précision** :
- Si l'artisan a des prix Mem0 sur la zone → **prix Mem0 prioritaires** (Règle 3 pricing)
- Si pas de Mem0 → prix référentiel × coefficient
- Alerter si prix final < 70% ou > 150% du référentiel ajusté (Règle 4)

### 9.2 — Priorité future : affinement par département / code postal

V2/V3 : possibilité d'affiner les coefficients par code postal si volumétrie Mem0 suffisante.

---

## 10. RÈGLE D'OR — ZÉRO INVENTION

> **Le cerveau IA ne dit JAMAIS de connerie en silence.**

### 10.1 — Les 4 sources de vérité hiérarchisées

Lorsque le cerveau IA doit produire une information réglementaire, il cherche **dans cet ordre strict** :

1. **Mem0 / MemPalace** (mémoire artisan) — PRIORITÉ ABSOLUE si disponible
2. **Base de connaissances BTP** (`data/legal/*.json`, `data/prix/*.json`, ce fichier `docs/METIER.md`)
3. **Claude** (raisonnement général) — **TOUJOURS FLAGGÉ** "estimation, à confirmer"
4. **Recherche web** (fallback) + enregistrement dans la base pour apprentissage

### 10.2 — Règles d'invention interdite

- **Prix** : JAMAIS inventé. Toujours source Mem0 ou référentiel. Sinon → demander à l'artisan.
- **TVA** : JAMAIS improvisée. Toujours source `data/legal/tva_rules.json` + règles ci-dessus.
- **Mentions obligatoires** : JAMAIS raccourcies. Les 47 ou rien.
- **Réglementation** : JAMAIS approximée. Source officielle (Légifrance, impots.gouv) ou silence + question à l'artisan.
- **Dates légales** : JAMAIS supposées. Toujours vérifiées dans ce fichier.

### 10.3 — Score de confiance obligatoire

Chaque information produite par le cerveau IA est flagguée :
- 🟢 **Haute confiance** (>90%) : info vérifiée réglementairement OU confirmée par 10+ artisans dans Mem0
- 🟡 **Confiance moyenne** (60-90%) : info de source web fiable OU confirmée par 3-9 artisans
- 🔴 **Basse confiance** (<60%) : info d'une seule source OU estimation Claude non confirmée

**Règle** : si confiance 🔴, le cerveau DIT : *"Prix estimé à 380€/m² (confiance basse — 1 seule source). Tu confirmes ?"*

### 10.4 — En cas de doute

Le cerveau IA préfère **TOUJOURS** dire *"je ne suis pas sûr, tu confirmes ?"* plutôt que d'inventer une réponse fausse. Un artisan qui envoie un devis avec un prix faux perd sa crédibilité. Un artisan qui envoie un devis avec TVA fausse risque un redressement.

---

## 11. SOURCES OFFICIELLES

### 11.1 — Textes de loi de référence

| Source | URL |
|---|---|
| Code de la consommation (articles L111-1, L221-5, L221-18, L612-1) | https://www.legifrance.gouv.fr/codes/texte_lc/LEGITEXT000006069565 |
| Code du commerce (articles L441-9, L441-10) | https://www.legifrance.gouv.fr/codes/texte_lc/LEGITEXT000005634379 |
| Code de la construction et de l'habitation (art. L111-13, L111-15, L241-1) | https://www.legifrance.gouv.fr/codes/texte_lc/LEGITEXT000006074096 |
| Code civil (articles 1218, 1376, 1582, 1710, 1792 et s.) | https://www.legifrance.gouv.fr/codes/texte_lc/LEGITEXT000006070721 |
| Code général des impôts (articles 279-0 bis, 283, 286 ter, 293 B) | https://www.legifrance.gouv.fr/codes/texte_lc/LEGITEXT000006069577 |
| Loi n°71-584 du 16/07/1971 (retenue de garantie) | https://www.legifrance.gouv.fr/loda/id/JORFTEXT000000874235 |
| Arrêté du 29/12/2020 (mention déchets BTP) | https://www.legifrance.gouv.fr/jorf/id/JORFTEXT000043017813 |
| Arrêté du 15/12/2025 (intérêt légal S1 2026) | https://www.legifrance.gouv.fr/jorf/id/JORFTEXT000053165408 |
| Ordonnance n°2021-1190 du 15/09/2021 (facturation électronique) | https://www.legifrance.gouv.fr/loda/id/JORFTEXT000044056885 |

### 11.2 — Portails administratifs

| Sujet | URL |
|---|---|
| Facturation électronique (DGFiP) | https://www.impots.gouv.fr/professionnel/je-passe-la-facturation-electronique |
| Portail Public de Facturation (PPF) | https://www.chorus-pro.gouv.fr/ |
| Service-public.gouv.fr — Devis BTP | https://entreprendre.service-public.gouv.fr/vosdroits/F31144 |
| Service-public.gouv.fr — TVA BTP | https://entreprendre.service-public.gouv.fr/vosdroits/F23570 |
| economie.gouv.fr — Taux intérêt légal | https://www.economie.gouv.fr/particuliers/gerer-mon-argent/emprunter-et-sassurer/tout-savoir-sur-le-taux-dinteret-legal |
| Liste PA immatriculées DGFiP | https://www.impots.gouv.fr/facturation-electronique-liste-des-plateformes-de-dematerialisation-partenaires-immatriculees |

### 11.3 — Normes techniques

| Sujet | Référence |
|---|---|
| Factur-X / EN 16931 | https://fnfe-mpe.org/factur-x/ |
| CIUS Construction BTP | https://fnfe-mpe.org/ (extension française EN 16931) |
| DTU (normes techniques BTP) | https://www.afnor.org/ |
| NF C 15-100 (électricité logements) | https://www.promotelec.com/ |
| Qualibat / RGE | https://www.qualibat.com/ — https://france-renov.gouv.fr/ |

---

## 12. MAINTENANCE DE CE FICHIER

- **Fréquence de mise à jour** : semestrielle (alignement intérêt légal) + à chaque évolution réglementaire
- **Qui peut modifier** : Fabrice uniquement, après validation avocat pour les changements majeurs
- **Log de modifications** : `docs/CHANGELOG.md`
- **Tests de non-régression** : `tests/test_metier.py` (à créer) doit tester chaque règle ci-dessus

**Prochaines mises à jour prévues** :
- **Juillet 2026** : taux intérêt légal S2 2026
- **Septembre 2026** : activation e-invoicing — vérifier PA choisie
- **Septembre 2027** : activation émission TPE/PME + e-reporting
- **Janvier 2027** : taux intérêt légal S1 2027

---

> **Rappel final :** Ce fichier est la LOI. Le cerveau IA la respecte ABSOLUMENT. En cas de doute, il demande à l'artisan plutôt que d'inventer. En cas d'évolution réglementaire, Fabrice met à jour ce fichier AVANT de toucher au code.
