---
name: FEATURES_COMPLETE
description: Liste exhaustive des 136 features STRUCTORAI (F001 à F136), organisée par catégorie, avec tag V1/V2/V3, sprint, et description 1-3 lignes. Source de vérité sur la liste complète — complémentaire de FEATURES.md qui détaille les features modifiées.
type: reference
scope: all-agents, product, build
priority: immuable
date: 2026-04-17
version: 1.0
total_features: 136
v1_count: 133
v2_count: 4
v3_count: 2
---

# docs/FEATURES_COMPLETE.md — Liste complète des 136 features

> Ce fichier liste **TOUTES** les features du produit, avec tag V1/V2/V3 et sprint cible.
> Pour le détail approfondi d'une feature enrichie lors des audits → voir `FEATURES.md`.
> Pour le contexte produit global → voir `PRODUCT_CONTEXT.md`.
> Pour les chiffres et découpage → voir `docs/SINGLE_SOURCE_OF_TRUTH.md`.

---

## RÉCAP GLOBAL

| Total | 136 features |
|---|---|
| V1 (launch juin 2026) | **133 features** (tout sauf 3 reports V2/V3) |
| V2 (sept-oct 2026) | **4 features** (F122 + F126 + F127 + F113 V2 API grossistes + Agent Téléphone base) |
| V3 (2027+) | **2 features** (F109 photogrammétrie + F113 V3 exclusif) |

### Répartition par catégorie

| # | Catégorie | Features | Agent principal |
|---|---|---|---|
| 1 | Conversation cerveau IA | F01-F08 | Supervisor |
| 2 | Documents & fiscal | F09-F14 | Compta + Fiscalité |
| 3 | Devis | F15-F22 | Devis |
| 4 | Factures & paiement | F23-F30 | Devis + Relance |
| 5 | Relance & recouvrement | F31-F35 | Relance |
| 6 | Compta & trésorerie | F36-F42 | Compta + Fiscalité |
| 7 | Planning & capacité | F43-F48 | Planning |
| 8 | Chantiers & photos | F49-F56 | Planning + Vision IA |
| 9 | Réputation & marketing | F57-F65 | Réputation & Marketing |
| 10 | Prospection & CRM | F66-F73 | Prospection |
| 11 | Site Web vitrine IA | F74-F79 | Site Web |
| 12 | Gamification | F80-F86 | Module |
| 13 | Features V1 audit initial (F87-F110) | F87-F110 | Transverse |
| 14 | Feature F111 audit V5 | F111 | Transverse |
| 15 | Acquisition / Moat audit V6 (F112-F120) | F112-F120 | Transverse |
| 16 | Vente & Pricing audit V6 (F121-F130) | F121-F130 | Transverse |
| 17 | Sécurité & Scale audit V6 (F131-F135) | F131-F135 | Transverse |
| 18 | Support audit V7 (F136) | F136 | Module |

---

## CATÉGORIE 1 — CONVERSATION CERVEAU IA (F01-F08)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F01** | Chat texte avec le cerveau IA | LLM complet (Claude API) — questions métier, perso, conseil, conversation. Fallback web search pour hors-métier. Voir `FEATURES.md` pour détail enrichi. | V1 | 2 |
| **F02** | Chat vocal entrant | Vocal push-to-talk → Whisper STT → cerveau. 6 langues. Latence <1.5s. | V1 | 2 |
| **F03** | Mode mains libres | Conversation vocale continue sans appui bouton (mode chantier). | V1 | 2 |
| **F04** | Réponse vocale TTS | ElevenLabs Multilingual v2 — le cerveau parle ses réponses. | V1 | 2 |
| **F05** | Transcription temps réel | Affichage texte pendant parole (sous-titres live). | V1 | 2 |
| **F06** | Lecture vocale des infos | Toggle settings : Toujours / Jamais / Intelligent (par défaut). Voir `FEATURES.md`. | V1 | 2 |
| **F07** | Historique conversation | Scroll bulles chat style WhatsApp avec cartes inline (devis, photos, PDFs). | V1 | 2 |
| **F08** | Multi-canal WhatsApp | Artisan envoie vocal/photo par WhatsApp → cerveau répond. Meta Cloud API. | V1 | 5 |

---

## CATÉGORIE 2 — DOCUMENTS & FISCAL (F09-F14)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F09** | Scan & classification tous documents + suivi fiscal annuel complet | **Feature massive** — tickets, factures, devis fournisseur, URSSAF, bancaires, attestations, amendes. Tableau de bord fiscal annuel temps réel (CA, TVA, cotisations, résultat, trésorerie). Voir `FEATURES.md`. | V1 | 3, 5 |
| **F10** | OCR Claude Vision tickets | Extraction montant/fournisseur/date/articles. Catégorisation auto. Attribution chantier. | V1 | 3 |
| **F11** | Scan courrier administratif | Photo URSSAF/impôts → lecture IA → action à faire + risques. | V1 | 5 |
| **F12** | Calendrier fiscal par statut | Micro/EURL/SAS/SASU/EI — toutes échéances adaptées au statut artisan. | V1 | 5 |
| **F13** | Suivi CA vs seuils | Alerte seuil micro (83 600€ BIC / 203 100€ vente 2026), franchise TVA. | V1 | 5 |
| **F14** | Dossier annuel comptable auto | Génération dossier complet pour expert-comptable : CA/charges/TVA + pièces classées. | V1 | 5 |

---

## CATÉGORIE 3 — DEVIS (F15-F22)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F15** | Devis vocal conversationnel | Cerveau dicte poste par poste, artisan valide/modifie prix en vocal. Chaque modification mémorisée Mem0. Voir `FEATURES.md`. | V1 | 2 |
| **F16** | Devis depuis photo | L'artisan photographie la pièce → cerveau pré-décompose postes. Voir `FEATURES.md`. | V1 | 3 |
| **F17** | Pré-devis instantané (avant visite) | Estimation rapide au téléphone → filtre prospects sérieux. | V1 | 2 |
| **F18** | 47 mentions obligatoires auto | Le PDF inclut toujours les 47 mentions (15 légales + 32 complémentaires). Voir `docs/METIER.md`. | V1 | 2 |
| **F19** | TVA multi-taux sur même devis | 5,5% / 10% / 20% combinables par ligne. Attestation TVA réduite auto. | V1 | 2 |
| **F20** | Signature électronique Yousign | Lien signature email/SMS. Plan Starter 500 signatures/an. Voir `FEATURES.md`. | V1 | 2 |
| **F21** | Acompte automatique après signature | Fiche chantier créée + demande acompte 30% max (devis >300€). | V1 | 2 |
| **F22** | Devis PDF avec photos avant | Annexes photos du chantier intégrées dans le PDF (taux acceptation +). | V1 | 7 |

---

## CATÉGORIE 4 — FACTURES & PAIEMENT (F23-F30)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F23** | Facturation Factur-X native | PDF/A-3 + XML EN 16931 profil EXTENDED + CIUS Construction. Obligatoire 09/2026. Voir `FEATURES.md`. | V1 | 5 |
| **F24** | Intégration Plateforme Agréée | FactPulse (29€/mois, sandbox) ou B2Brouter (gratuit pilote). Transmission auto. | V1 | 5 |
| **F25** | Facture d'acompte | Génération auto après signature devis (30% max si devis >300€). Voir `FEATURES.md`. | V1 | 2 |
| **F26** | Factures de situation BTP | Avancement 30%→60%→100%. Chaque situation = facture à part entière via PA. | V1 | 5 |
| **F27** | Avoirs & factures rectificatives | Numérotation distincte (`AV-2026-001`). Obligation d'avoir puis nouvelle facture. | V1 | 5 |
| **F28** | Retenue de garantie 5% | Suivi auto (1 an post-réception). Loi 16/07/1971. Proposition caution bancaire sur gros chantiers. | V1 | 5 |
| **F29** | Paiement en ligne CB | Stripe payment link sur chaque facture. CB + virement + prélèvement. | V1 | 5 |
| **F30** | Export comptable mensuel auto | CSV + PDF toutes pièces classées, TVA collectée/déductible, envoi comptable. Voir `FEATURES.md`. | V1 | 3 |

---

## CATÉGORIE 5 — RELANCE & RECOUVREMENT (F31-F35)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F31** | Relance devis J+3 | Message adapté au ton du client (mémoire Mem0). | V1 | 2 |
| **F32** | Relance facture progressive | J+15 polie email / J+30 ferme SMS / J+45 alerte artisan. Voir `FEATURES.md`. | V1 | 2 |
| **F33** | Mise en demeure auto J+45 | Texte juridiquement correct avec pénalités 7,86% + indemnité 40€. | V1 | 2 |
| **F34** | Tracking ouverture email | Détection "vu" par le client → adaptation relance. | V1 | 2 |
| **F35** | Dashboard impayés | "3 factures impayées = 4 800€, plus ancienne Martin 25j". Dans briefing matin. | V1 | 4 |

---

## CATÉGORIE 6 — COMPTA & TRÉSORERIE (F36-F42)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F36** | Catégorisation intelligente achats | Matériaux / outillage / carburant / sous-traitance / loyer. Voir `FEATURES.md`. | V1 | 3 |
| **F37** | Attribution auto chantier | "Tu étais sur Dupont, ce ticket Point P 234€ va sur Dupont". | V1 | 3 |
| **F38** | Alerte anomalie achat | "Ticket 847€ inhabituel pour ce chantier, tu confirmes ?". Voir `FEATURES.md`. | V1 | 3 |
| **F39** | Marge chantier temps réel | "Chantier Dupont : 65% budget matériaux consommé, 40% chantier fait". | V1 | 4 |
| **F40** | Trésorerie prévisionnelle | Entrées - sorties → alerte "en septembre TVA + cotisations = 4 800€". Voir `FEATURES.md`. | V1 | 5 |
| **F41** | Alerte fin de mois proactive | "12 tickets non classés, je prépare l'export ?". Voir `FEATURES.md`. | V1 | 4 |
| **F42** | Conseil optimisation statut | "En EURL tu pourrais déduire tes charges, parles-en à ton comptable". Voir `FEATURES.md`. | V1 | 5 |

---

## CATÉGORIE 7 — PLANNING & CAPACITÉ (F43-F48)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F43** | Vue chantiers en cours | Liste par statut (à commencer / en cours / terminé) avec dates. | V1 | 4 |
| **F44** | Timer chantier | "start Dupont" / "stop" → temps réel tracké. | V1 | 4 |
| **F45** | Détection dépassement | "Au rythme actuel, Dupont finit jeudi au lieu de mardi". | V1 | 4 |
| **F46** | Enchaînements auto | Dupont déborde → décaler Martin → prévenir client suivant. | V1 | 4 |
| **F47** | Calcul capacité | "3 chantiers en avril, capacité max 4, tu peux accepter 1 devis". Voir `FEATURES.md`. | V1 | 4 |
| **F48** | Rappel commande matériaux | "Martin lundi, pas encore commandé matériaux. Passer Point P ?" | V1 | 4 |

---

## CATÉGORIE 8 — CHANTIERS & PHOTOS (F49-F56)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F49** | Galerie photo chantier | 3 albums par chantier (avant/pendant/après). Horodatée + géolocalisée. Voir `FEATURES.md`. | V1 | 7 |
| **F50** | Catégorisation IA photos | Claude Vision classe auto : avant / pendant / après / problème / ticket / plan. | V1 | 3 |
| **F51** | Analyse IA plans & tableaux électriques | Extraction dimensions/surfaces. Détection non-conformité NF C 15-100. Voir `FEATURES.md`. | V1 | 3 |
| **F52** | Montage avant/après auto | Split screen exportable réseaux sociaux. | V1 | 7 |
| **F53** | Détection oublis sur photo | "Je vois un sèche-serviette non inclus au devis, l'ajouter ?". | V1 | 3 |
| **F54** | Vidéos courtes chantier | 15-60s liées au chantier, horodatée. | V1 | 7 |
| **F55** | WhatsApp photo auto-classée | Photo envoyée par WhatsApp → classée au chantier en cours. | V1 | 5 |
| **F56** | Mesure IA estimation Vision (niveau 1) | Photos → dimensions ±20%. Précision 🟡. Pré-devis sans déplacement. Voir `FEATURES.md`. | V1 | 3 |

---

## CATÉGORIE 9 — RÉPUTATION & MARKETING (F57-F65)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F57** | SMS avis Google auto post-chantier | J+2-3 après fin chantier, lien avis Google pré-rempli. Voir `FEATURES.md`. | V1 | 4 |
| **F58** | Réponse IA aux avis | 5★ remerciement + mots-clés SEO local / 3★ empathie + résolution. Voir `FEATURES.md`. | V1 | 4 |
| **F59** | Suivi score Google & map pack | Position locale sur mots-clés métier/ville. | V1 | 4 |
| **F60** | Publication réseaux sociaux IA | Facebook/Instagram/Google Business avec texte IA. Validation artisan obligatoire. Voir `FEATURES.md`. | V1 | 7 |
| **F61** | Connexion directe réseaux sociaux | OAuth Facebook + Instagram + Google Business Profile. | V1 | 7 |
| **F62** | Suggestion prochains avis | "Tu as 12 avis, concurrent 45. Demander à tes 8 derniers clients ?". | V1 | 4 |
| **F63** | Templates réponse avis | Par corps de métier + ton (chaleureux/professionnel). Voir `FEATURES.md`. | V1 | 4 |
| **F64** | Planification publications | Calendrier éditorial IA pour les réseaux sociaux. | V1 | 7 |
| **F65** | Dashboard réputation | Vue agrégée : note moyenne, nouveaux avis, posts programmés, concurrence. | V1 | 7 |

---

## CATÉGORIE 10 — PROSPECTION & CRM (F66-F73)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F66** | Fiche contact pro (archi/apporteur/client) | Historique complet chantiers avec chaque contact. Voir `FEATURES.md`. | V1 | 4 |
| **F67** | Rappel contact inactif | "Pas contacté Lefebvre depuis 45 jours. Message suggéré ?" | V1 | 4 |
| **F68** | Détection opportunités | "Ancien client a acheté un logement dans le quartier". Voir `FEATURES.md`. | V1 | 4 |
| **F69** | Gestion leads entrants | Appel → fiche auto → rappel planifié → pré-devis suggéré. | V1 | 4 |
| **F70** | Alerte carnet vide | "2 chantiers en juin, il faut relancer archis, 3 messages ?" | V1 | 4 |
| **F71** | Templates relance archi | Textes adaptés par type de contact (premier contact / relance / remerciement). | V1 | 4 |
| **F72** | Dashboard prospection | Contacts chauds/tièdes/froids + dernier contact + prochaine action. | V1 | 4 |
| **F73** | Import contacts existants | CSV / vCard / Google Contacts. | V1 | 4 |

---

## CATÉGORIE 11 — SITE WEB VITRINE IA (F74-F79)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F74** | Génération site vitrine IA | Onboarding → pages auto → photos galerie → avis Google → SEO local → mise en ligne. Voir `FEATURES.md`. | V1 | 7 |
| **F75** | SEO local optimisé | Balises title/meta/schema.org LocalBusiness auto. Voir `FEATURES.md`. | V1 | 7 |
| **F76** | Mise à jour mensuelle auto | Nouvelles photos + nouveaux avis intégrés. Validation artisan avant publication. | V1 | 7 |
| **F77** | Domaine personnalisé | Option `[artisan].com` + HTTPS auto. Voir `FEATURES.md`. | V1 | 7 |
| **F78** | Formulaire contact connecté | Formulaire site → fiche prospect auto dans l'app. | V1 | 7 |
| **F79** | Hébergement inclus | Vercel (artisan).structorai.app ou custom domain. | V1 | 7 |

---

## CATÉGORIE 12 — GAMIFICATION (F80-F86)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F80** | Système XP + niveaux | XP par action (devis créé, facture payée, avis collecté). Voir `FEATURES.md`. | V1 | 7 |
| **F81** | Quêtes onboarding | "Fais ton premier devis", "Donne 10 prix habituels", "Collecte 5 avis". | V1 | 1 |
| **F82** | Badges métier | "Ambassadeur", "Serial Devis", "Impayé Zéro", "30 jours d'affilée". | V1 | 7 |
| **F83** | Niveaux d'artisan | Débutant → Confirmé → Expert → Maître. Bénéfices déblocables. | V1 | 7 |
| **F84** | Classement régional | Top artisans du département (opt-in, privé). | V1 | 7 |
| **F85** | Streak quotidien | 🔥 "30 jours consécutifs d'utilisation". | V1 | 7 |
| **F86** | Récompenses | Codes promo, mois offerts, accès exclusifs (webinars, beta V2). | V1 | 7 |

---

## CATÉGORIE 13 — FEATURES V1 AUDIT INITIAL (F87-F110)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F87** | Gestion fiches clients CRM | Voir `FEATURES.md`. Historique, segmentation, préférences. | V1 | 4 |
| **F88** | Mémoire prix par type de chantier | Voir `FEATURES.md`. Mem0 apprend les prix habituels artisan. | V1 | 2 |
| **F89** | Templates devis par corps de métier | Voir `FEATURES.md`. 11 corps : plomberie, élec, carrelage, peinture, etc. | V1 | 2 |
| **F90** | Suggestions intelligentes IA devis | Voir `FEATURES.md`. Détection oublis (évac gravats, protection client). | V1 | 3 |
| **F91** | Mode hors-ligne SQLite | Voir `FEATURES.md`. Création devis offline + sync auto. | V1 | 1 |
| **F92** | Synchro multi-appareils | Voir `FEATURES.md`. Phone + tablette + web temps réel. | V1 | 1 |
| **F93** | Multi-langues i18n | Voir `FEATURES.md`. 6 langues (FR prio + EN + TR + PT + AR + ES). | V1 | 1 |
| **F94** | Import clients CSV/Excel | Voir `FEATURES.md`. Mapping auto colonnes. | V1 | 1 |
| **F95** | Export complet données RGPD | Voir `FEATURES.md`. 1 clic ZIP complet. | V1 | 1 |
| **F96** | Suppression compte + données | Voir `FEATURES.md`. Droit à l'effacement RGPD, 30j rétention avant purge. | V1 | 1 |
| **F97** | Signature manuscrite sur tablette | Voir `FEATURES.md`. Stylet/doigt sur écran tactile. | V1 | 2 |
| **F98** | Archivage légal 10 ans | Voir `FEATURES.md`. Factures conservées 10 ans (obligation légale). | V1 | 5 |
| **F99** | Numérotation continue garantie | Voir `FEATURES.md`. Pas de trou dans la séquence (obligation CGI). | V1 | 2 |
| **F100** | Géolocalisation chantier | Voir `FEATURES.md`. Distance domicile → chantier auto. | V1 | 4 |
| **F101** | Notifications push mobile | Voir `FEATURES.md`. Expo push. Urgences + rappels programmés. | V1 | 1 |
| **F102** | Briefing matin quotidien | Voir `FEATURES.md`. "Tu as 3 devis à relancer, 2 impayés, chantier Dupont 80%". | V1 | 6 |
| **F103** | Résumé soir quotidien | Voir `FEATURES.md`. "Journée : 2 chantiers avancés, 1 devis envoyé, 340€ encaissés". | V1 | 6 |
| **F104** | Mode "Congés" | Voir `FEATURES.md`. Auto-reply mails + pause relances + info clients. | V1 | 5 |
| **F105** | Partage fiche artisan client | Voir `FEATURES.md`. QR code contact + coordonnées + site. | V1 | 7 |
| **F106** | Chat interne employés (Business) | Voir `FEATURES.md`. Messages entre patron et compagnons. | V1 | 7 |
| **F107** | Statistiques annuelles | Voir `FEATURES.md`. Bilan visuel CA, marges, chantiers, évolution. | V1 | 7 |
| **F108** | Devis récurrents (contrats entretien) | Voir `FEATURES.md`. Clients avec entretien annuel. Génération auto. | V1 | 5 |
| **F109** | **Photogrammétrie multi-photos** | Voir `FEATURES.md`. Reconstruction 3D ±5-10%. Exclue V1 (coût dev + GPU). | **V3** | V3 |
| **F110** | Mesure AR ARCore Android | Voir `FEATURES.md`. ±5-10cm. Post-launch V1.5 (Android uniquement). | V1.5 | V1.5 |

---

## CATÉGORIE 14 — FEATURE F111 AUDIT V5

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F111** | Intégration data 11 corps de métier | Voir `FEATURES.md`. 8 819 lignes de référentiels techniques. 1 080+ postes chiffrés. 132 erreurs terrain documentées. | V1 | 1 (données chargées avant build) |

---

## CATÉGORIE 15 — ACQUISITION / MOAT AUDIT V6 (F112-F120)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F112** | Import universel concurrents | 6 parseurs : Obat, Tolteck, Batigest, EBP, Excel, Facture.net. Migration clients + historique devis en 10 minutes. Voir `FEATURES.md`. | V1 | 2-3 |
| **F113** | Partenariats grossistes (3 niveaux) | V1 scan carte fidélité (Sprint 4) + V2 API temps réel + V3 exclusivité (post 2K MRR). Voir `FEATURES.md`. | V1-V2-V3 | 4 / V2 / V3 |
| **F114** | Code parrain structurel | 1 mois offert parrain + filleul. 50€ crédit Point P (partenariat). Badge gamification "Ambassadeur". Voir `FEATURES.md`. | V1 | 7 |
| **F115** | Sandbox publique sans inscription | Landing `/try` chat public, rate-limited, session Redis 15min. Conversion ×3 vs signup-first. Voir `FEATURES.md`. | V1 | 8 |
| **F116** | Rapport annuel de l'artisan | Généré chaque 1er janvier + 1er juillet. CA, marge moyenne, top 3 clients, temps gagné, avis collectés. Export PDF. Voir `FEATURES.md`. | V1 | 7 |
| **F117** | Score "cerveau me connaît à X%" | Indicateur visible de la profondeur de mémoire. Warning "Si tu pars, ton score repart à 0" (anti-churn). Voir `FEATURES.md`. | V1 | 7 |
| **F118** | Switching cost moat | Pas de feature à coder — documentation : import entrant OK, pas d'API export symétrique pour concurrents. Voir `FEATURES.md`. | V1 | Doc uniquement |
| **F119** | **Agent Coach Business (14ème agent)** | Conseil stratégique mensuel : taux horaire vs benchmark, conversion, marge, positioning. **Positionnement "éclaireur"** — disclaimers UI obligatoires, voir `docs/COACH-DISCLAIMER.md`. Voir `FEATURES.md`. | V1 | 6-7 |
| **F120** | Formation contextuelle capsules vidéo 60s | 50 capsules tournées sur 3 mois. Déclenchées contextuellement par Supervisor. Data `data/capsules/index.json`. Voir `FEATURES.md`. | V1 | 7 |

---

## CATÉGORIE 16 — VENTE & PRICING AUDIT V6 (F121-F130)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F121** | Détection & analyse devis concurrent | Vision IA `parse_competitor_quote()`. Upload PDF concurrent → comparaison référentiels BTP + Mem0 → argumentaire. Voir `FEATURES.md`. | V1 | 3 |
| **F122** | **Synchro compta native** (Pennylane/Cegid/Sage/QuickBooks) | API OAuth. Sync quotidien factures + tickets + TVA. Business only. Voir `FEATURES.md`. | **V2** | post-launch |
| **F123** | Assignation tâches employés avec suivi photo | Migration 032. Patron → employé : "Ahmed, chantier Dupont, carrelage 20m², photos début+fin". Kanban. Voir `FEATURES.md`. | V1 | 7 |
| **F124** | App mode Compagnon | Version allégée pour employés. Login code 4 chiffres. Écran simplifié. Photos début/fin tâche. Voir `FEATURES.md`. | V1 | 7 |
| **F125** | Onboarding inversé 90 secondes | Landing → chat public → 1er devis → signup pour envoyer. Cible conversion >25%. Voir `FEATURES.md`. | V1 | 8 |
| **F126** | **Télé-dépannage client** (extension Agent Téléphone) | 550 arbres de décision (50 pannes × 11 métiers). 2€/dépannage résolu, 50/50 artisan/plateforme. Voir `FEATURES.md`. | **V2** | post-launch |
| **F127** | **Portail client final mini-PWA** | `client.structorai.app/[chantier-token]`. Signature devis, suivi chantier, photos, chat artisan. Migration 035. Voir `FEATURES.md`. | **V2** | post-launch |
| **F128** | Validation auto RGE/Qualibat temps réel | API data.gouv.fr / RGE.fr / Qualibat. Re-check mensuel. Blocage devis TVA 5,5% si RGE invalide. Voir `FEATURES.md`. | V1 | 3 |
| **F129** | Pack contrôle fiscal | ZIP signé horodaté contenant devis + factures + tickets OCR + justifs TVA + registres. Argument de vente. Voir `FEATURES.md`. | V1 | 6 |
| **F130** | Tarification enrichie 7 options | Starter €0 / Pro €29 / Pro annuel €229 / Business €79 / Business annuel €629 / Lifetime €990 / Fédération -20%. Trial 30 jours. Migration 033. Voir `FEATURES.md`. | V1 | 1 |

---

## CATÉGORIE 17 — SÉCURITÉ & SCALE AUDIT V6 (F131-F135)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F131** | Conformité AI Act | Classification "Système IA à usage limité avec supervision humaine". Page `/transparency`. Badge "Décision IA". Audit log. Revue juridique 1 500€. Voir `FEATURES.md` + `docs/AI-ACT-COMPLIANCE.md` (à créer). | V1 | 6 |
| **F132** | Résilience et redondance | Fallback LLM (Anthropic down → GPT-4). Supabase read replica +25$/mois. UptimeRobot + Better Uptime. SLA interne 99,5%. Voir `FEATURES.md`. | V1 | 8 |
| **F133** | Acquisition artisans étrangers en France | 6 langues + adaptation culturelle (Ramadan Agent Planning, fêtes religieuses). Landings dédiées `/tr` `/ar` `/pt` `/es`. Voir `FEATURES.md`. | V1 | 8 |
| **F134** | Verticalisation éditions métier | Architecture `edition` dans organizations (V1). Lancement "STRUCTORAI Plomberie" etc. (V2+ post 2K MRR). Voir `FEATURES.md`. | V1 archi / V2+ | 1 archi |
| **F135** | Architecture multi-pays | `country_code` sur toutes tables. Règles BTP modulaires par pays. Migration 037. Voir `FEATURES.md`. | V1 | 1 |

---

## CATÉGORIE 18 — SUPPORT AUDIT V7 (F136)

| # | Feature | Description | Tag | Sprint |
|---|---|---|---|---|
| **F136** | Support hybride IA + humain | **3 niveaux** : IA N1 (70% résolution, 24/7) / escalade async <2h (20%) / urgence SMS Fabrice <1h (10%). Widget Crisp (défaut) ou Intercom. Dashboard admin : taux résolution IA, temps escalade, NPS. Voir `FEATURES.md` + `docs/SUPPORT-STRATEGY.md` (à créer). | V1 | 6 |

---

## DÉCOUPAGE V1 / V2 / V3 DÉTAILLÉ

### V1 — Launch juin 2026 (133 features)

**Tout SAUF** :
- F109 → V3
- F122 → V2
- F126 → V2
- F127 → V2
- F113 niveau V2 et V3 → V2 / V3

**Répartition V1 par sprint** :

| Sprint | Features principales |
|---|---|
| **0** | Setup infra (Fabrice) |
| **1** | F91, F92, F93, F94, F95, F96, F101, F130, F134 archi, F135, F81 onboarding |
| **2** | F01-F08, F15-F22, F25, F31, F32, F33, F34, F88, F89, F97, F99 |
| **3** | F09, F10, F16, F36, F37, F38, F50, F51, F53, F56, F90, F121, F128 |
| **4** | F35, F39, F41, F43-F48, F57-F59, F62, F63, F66-F73, F87, F100, F113 V1 |
| **5** | F11, F12, F13, F14, F23, F24, F26, F27, F28, F29, F30, F40, F42, F55, F98, F104, F108, F08 WhatsApp |
| **6** | F102, F103, F119, F129, F131, F136 |
| **7** | F22, F49, F52, F54, F60, F61, F64, F65, F74-F86, F105-F107, F114, F116, F117, F120, F123, F124 |
| **8** | F115, F118 doc, F125, F132, F133 |

### V1.5 — Post-launch juillet-août 2026

| Feature | Détail |
|---|---|
| **F110** | Mesure AR ARCore Android (±5-10cm) |

### V2 — Sept-oct 2026

| Feature | Détail |
|---|---|
| **F122** | Synchro Pennylane/Cegid/Sage/QuickBooks (Business only) |
| **F126** | Télé-dépannage (extension Agent Téléphone) |
| **F127** | Portail client final (mini-PWA `client.structorai.app`) |
| **F113 V2** | API temps réel grossistes (Point P, Cedeo, BigMat) |
| **Agent Téléphone IA de base** | Add-on +15€/mois |

### V3 — 2027+

| Feature | Détail |
|---|---|
| **F109** | Photogrammétrie multi-photos (reconstruction 3D) |
| **F113 V3** | API grossistes exclusives (bloqué jusqu'à 2K MRR pour négocier) |
| **F134 post 2K MRR** | Lancement éditions métier verticales (Plomberie, Électricité, etc.) |

---

## FEATURES PAR AGENT

### Agent Devis
F15, F16, F17, F18, F19, F20, F21, F22, F25, F88, F89, F90, F97, F99, F108, F22, F121

### Agent Relance
F31, F32, F33, F34, F35

### Agent Compta
F09, F10, F30, F36, F37, F38, F39, F41

### Agent Planning
F43, F44, F45, F46, F47, F48, F102, F103

### Agent Réputation & Marketing
F57, F58, F59, F60, F61, F62, F63, F64, F65

### Agent Prospection
F66, F67, F68, F69, F70, F71, F72, F73, F87

### Agent Email Pro
F11 (extension), F69 (leads entrants), briefing F102

### Agent Fiscalité & Trésorerie
F09 (suivi annuel), F11, F12, F13, F14, F40, F42, F129

### Agent Déplacements
F100, barème km, indemnités zones BTP

### Agent RH
F123, F124, cotisations CIBTP, grilles salariales BTP, export paie

### Agent Vision IA
F10, F50, F51, F53, F56, F121

### Agent Site Web
F74, F75, F76, F77, F78, F79

### Agent Coach Business (F119)
Analyses mensuelles, benchmarks, conseil stratégique éclaireur

### Agent Téléphone IA (V2)
Décrochage prospects + F126 télé-dépannage

### Transverse / Modules
- Gamification : F80-F86, F114, F117
- Galerie Photo : F49-F56 (+ Agent Vision)
- Dossier "À faire" : validation toutes actions automatiques
- Support hybride (F136)
- Sandbox publique (F115)
- Architecture multi-pays (F135)
- AI Act (F131)
- Résilience (F132)

---

## LÉGENDE TAGS

- **V1** : Launch juin 2026 (133 features)
- **V1.5** : Post-launch juillet-août 2026 (F110 uniquement — Mesure AR Android)
- **V2** : Sept-oct 2026 (4 features)
- **V3** : 2027+ (2 features principales, éditions métier en plus post 2K MRR)
- **Doc** : Pas de code, juste documentation (F118)

---

## MAINTENANCE DE CE FICHIER

- **Qui peut modifier** : Fabrice, après log dans `docs/DECISIONS-LOG.md` + `docs/CHANGELOG.md`
- **Synchronisation** : ce fichier DOIT rester aligné avec `FEATURES.md` (détail) + `docs/SINGLE_SOURCE_OF_TRUTH.md` (chiffres)
- **Fréquence** : mise à jour à chaque audit (V8, V9...)
- **Règle d'or** : toute feature ajoutée → numérotation F137, F138 etc. (jamais de réutilisation de numéro)

---

> **Ce fichier liste 136 features. En cas de contradiction avec un autre document, ce fichier + `SINGLE_SOURCE_OF_TRUTH.md` font foi.**
