---
name: MIGRATIONS
description: Les 37 migrations SQL Supabase de STRUCTORAI (001-029 base historique + 030-037 audit V6/V7). Pour chaque migration — description, tables créées/modifiées, RLS policies, index, seed data, dépendances. Workflow Supabase CLI (migration new, db reset, db push). Règles d'idempotence, transactions, rollback. Seed data pour staging et production. Guidelines pour nouvelles migrations post-launch.
type: technical-database-schema
scope: backend-database, supabase-migrations, rls, seed-data
priority: critical
date: 2026-04-18
version: 1.0
total_migrations: 37
base_migrations: 29 (001-029)
audit_v6_v7_migrations: 8 (030-037)
database: PostgreSQL 15+ (Supabase Frankfurt EU)
rls_enforced: true
---

# docs/MIGRATIONS.md — Migrations SQL STRUCTORAI

> **Ce fichier est la source de vérité des 37 migrations SQL.**
> Toute nouvelle migration DOIT être ajoutée ici AVANT implémentation.
> Reference deploy : `docs/DEPLOY.md` §9.
> Règle d'or : **jamais modifier une migration déjà appliquée en prod** — créer une nouvelle.

---

## 0. SOMMAIRE

1. [Principes fondamentaux](#1-principes-fondamentaux)
2. [Workflow Supabase CLI](#2-workflow-supabase-cli)
3. [Vue d'ensemble des 37 migrations](#3-vue-densemble-des-37-migrations)
4. [Migrations 001-015 — Tables de base](#4-migrations-001-015--tables-de-base)
5. [Migrations 016-025 — Agents IA + helper](#5-migrations-016-025--agents-ia--helper)
6. [Migrations 026-029 — Extensions BTP](#6-migrations-026-029--extensions-btp)
7. [Migrations 030-037 — Audit V6/V7](#7-migrations-030-037--audit-v6v7)
8. [Row-Level Security (RLS) — patterns globaux](#8-row-level-security-rls--patterns-globaux)
9. [Index et performance](#9-index-et-performance)
10. [Seed data](#10-seed-data)
11. [Rollback procedures](#11-rollback-procedures)
12. [Schéma entités et relations](#12-schéma-entités-et-relations)
13. [Règles pour nouvelles migrations post-launch](#13-règles-pour-nouvelles-migrations-post-launch)
14. [Backups et PITR](#14-backups-et-pitr)
15. [Références croisées](#15-références-croisées)

---

## 1. PRINCIPES FONDAMENTAUX

### 1.1 — Règles immuables

1. **Jamais modifier une migration déjà appliquée en prod** → créer une nouvelle migration
2. **Migrations idempotentes** quand possible (`IF NOT EXISTS`, `IF EXISTS`)
3. **Transactions explicites** autour des changements complexes
4. **RLS activé sur TOUTES les tables** sauf exceptions documentées
5. **`organization_id` + `country_code`** sur toutes les tables multi-tenant
6. **Index sur toutes les FK** et colonnes de filtre fréquent
7. **Timestamps partout** : `created_at`, `updated_at` avec triggers auto
8. **Soft delete préféré** à DELETE (`deleted_at` nullable)
9. **Naming** : snake_case, pluriel pour tables (`clients`, `devis`, `factures`)
10. **Type UUID v4** pour toutes les PK

### 1.2 — Conventions

**Filename** : `NNN_description.sql` (3 digits, action claire)

Exemples :
- `001_create_organizations.sql`
- `023_create_metier_rules.sql`
- `036_create_ai_audit_log.sql`

**Structure type d'une migration** :

```sql
-- ==============================================================
-- Migration 005 — Create devis table
-- Feature: F15-F22 (Agent Devis)
-- Dependencies: 001 organizations, 002 users, 003 clients, 004 chantiers
-- ==============================================================

BEGIN;

-- Table
CREATE TABLE IF NOT EXISTS devis (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    country_code TEXT NOT NULL DEFAULT 'FR',
    client_id UUID REFERENCES clients(id) ON DELETE SET NULL,
    chantier_id UUID REFERENCES chantiers(id) ON DELETE SET NULL,
    numero TEXT NOT NULL,
    status TEXT NOT NULL DEFAULT 'draft' CHECK (status IN (
        'draft', 'sent', 'signed', 'accepted', 'refused', 'expired'
    )),
    -- ...autres colonnes
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at TIMESTAMPTZ,
    UNIQUE (organization_id, numero)
);

-- Index
CREATE INDEX IF NOT EXISTS idx_devis_org_status ON devis(organization_id, status);
CREATE INDEX IF NOT EXISTS idx_devis_client ON devis(client_id) WHERE deleted_at IS NULL;
CREATE INDEX IF NOT EXISTS idx_devis_chantier ON devis(chantier_id);

-- Trigger updated_at
CREATE TRIGGER devis_set_updated_at
BEFORE UPDATE ON devis
FOR EACH ROW EXECUTE FUNCTION set_updated_at();

-- RLS
ALTER TABLE devis ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users see own org devis"
    ON devis FOR SELECT
    USING (organization_id = (auth.jwt() ->> 'org_id')::uuid AND deleted_at IS NULL);

CREATE POLICY "Users create own org devis"
    ON devis FOR INSERT
    WITH CHECK (organization_id = (auth.jwt() ->> 'org_id')::uuid);

CREATE POLICY "Users update own org devis"
    ON devis FOR UPDATE
    USING (organization_id = (auth.jwt() ->> 'org_id')::uuid);

CREATE POLICY "Users soft delete own org devis"
    ON devis FOR UPDATE
    USING (organization_id = (auth.jwt() ->> 'org_id')::uuid);

COMMIT;
```

### 1.3 — Colonnes standard sur toutes les tables

| Colonne | Type | Défaut | Contenu |
|---|---|---|---|
| `id` | UUID | `gen_random_uuid()` | PK |
| `organization_id` | UUID | — | FK organizations |
| `country_code` | TEXT | `'FR'` | Code pays (F135 multi-pays) |
| `created_at` | TIMESTAMPTZ | `NOW()` | Création |
| `updated_at` | TIMESTAMPTZ | `NOW()` | Dernière mise à jour (trigger) |
| `deleted_at` | TIMESTAMPTZ | `NULL` | Soft delete |

Voir `docs/ARCH.md` §8 et `docs/SINGLE_SOURCE_OF_TRUTH.md` §14 (country_code F135).

---

## 2. WORKFLOW SUPABASE CLI

### 2.1 — Commandes essentielles

```bash
# Init projet local (Docker Supabase)
cd backend
supabase start                           # démarre Supabase local sur port 54321
supabase stop                            # arrête services
supabase status                          # vérifie services up

# Créer une nouvelle migration
supabase migration new add_users_table   # crée supabase/migrations/AAAAMMDDHHMMSS_add_users_table.sql

# Appliquer toutes les migrations localement
supabase db reset                        # drop + recreate + apply all

# Link vers projet cloud
supabase link --project-ref <project-ref>

# Push vers Supabase cloud
supabase db push                         # applique migrations non-appliquées

# Pull schéma depuis cloud (inverse)
supabase db pull                         # récupère schéma cloud

# Diff entre local et cloud
supabase db diff

# Gérer seed data
supabase db seed                         # applique supabase/seed.sql
```

### 2.2 — Cycle de dev

```
1. Dev local :
   supabase migration new add_feature_x
   → édite le fichier SQL
   supabase db reset
   → tests via pytest
   
2. Commit + PR :
   git add supabase/migrations/
   git commit -m "Add feature X migration"

3. CI staging (auto sur merge staging) :
   supabase link --project-ref $STAGING_REF
   supabase db push
   
4. Validation staging :
   Tests manuels + smoke tests
   
5. CI production (auto sur merge main) :
   supabase link --project-ref $PROD_REF
   supabase db push
```

### 2.3 — Variables nécessaires

Voir `docs/ENV.md` §4 :
- `SUPABASE_ACCESS_TOKEN` (pour CLI cloud)
- `SUPABASE_DB_URL` (direct DB access)

### 2.4 — Intégration GitHub Actions

Voir `docs/DEPLOY.md` §7.1 — workflow `deploy.yml` job `migrations`.

---

## 3. VUE D'ENSEMBLE DES 37 MIGRATIONS

### 3.1 — Tableau synthétique

| # | Fichier | Feature | Tables principales |
|---|---|---|---|
| 001 | `create_organizations.sql` | Base | `organizations` |
| 002 | `create_users.sql` | Base | `users` (+ WhatsApp fields dans 037 ou future) |
| 003 | `create_clients.sql` | F66 | `clients` |
| 004 | `create_chantiers.sql` | F43 | `chantiers` |
| 005 | `create_devis.sql` | F15-F22 | `devis` |
| 006 | `create_devis_lines.sql` | F15-F22 | `devis_lines` |
| 007 | `create_factures.sql` | F23-F30 | `factures` |
| 008 | `create_tickets.sql` | F10, F36 | `tickets` |
| 009 | `create_depenses.sql` | F36-F41 | `depenses` |
| 010 | `create_time_entries.sql` | F43-F48 | `time_entries` |
| 011 | `create_photos.sql` | F49-F56 | `photos_chantier` |
| 012 | `create_contacts_pro.sql` | F66-F72 | `contacts_pro` |
| 013 | `create_avis_google.sql` | F57-F58 | `avis_google` |
| 014 | `create_emails.sql` | F11 | `emails_summary`, `emails_imap_config` |
| 015 | `create_relances.sql` | F31-F35 | `relances` |
| 016 | `create_agent_tasks.sql` | Architecture | `agent_tasks`, `tasks_to_validate` |
| 017 | `create_agent_memory.sql` | Memory | `agent_memory` (lien Mem0) |
| 018 | `create_agent_budget.sql` | Budget LLM | `agent_budget`, `llm_costs_daily` |
| 019 | `create_knowledge_base.sql` | RAG | `knowledge_chunks` (embeddings vectoriels) |
| 020 | `create_gamification.sql` | F80-F86 | `xp_events`, `quests`, `badges`, `user_quests` |
| 021 | `create_api_keys.sql` | Business API | `api_keys` |
| 022 | `create_subscriptions.sql` | Billing | `subscriptions`, `stripe_customers` |
| 023 | `create_metier_rules.sql` | BTP | `tva_rules`, `mentions_obligatoires`, `templates_devis` |
| 024 | `create_helper_functions.sql` | Utils | Fonctions SQL (set_updated_at, etc.) |
| 025 | `create_rls_policies.sql` | Sécurité | (consolidation RLS) |
| 026 | `create_deplacements.sql` | F100 | `deplacements` |
| 027 | `create_employes.sql` | F123-F124 | `employes` |
| 028 | `create_fiscal_calendar.sql` | F09, F14 | `fiscal_calendar`, `fiscal_alerts` |
| 029 | `create_deplacements_frais.sql` | F100 | `deplacements_frais` |
| **030** | `create_referrals.sql` | **F114** | `referral_codes`, `referrals` |
| **031** | `create_capsules.sql` | **F120** | `capsules_index`, `capsule_views` |
| **032** | `create_tasks.sql` | **F123** | `employee_tasks` |
| **033** | `update_subscriptions_plans.sql` | **F130** | Extension `subscriptions` (7 plans) |
| **034** | `create_diagnostics.sql` | **F126** (V2) | `diagnostic_trees`, `diagnostic_sessions` |
| **035** | `create_client_tokens.sql` | **F127** (V2) | `client_portal_tokens` |
| **036** | `create_ai_audit_log.sql` | **F131** | `ai_decisions` (audit AI Act) |
| **037** | `add_country_edition.sql` | **F134+F135** | Extension organizations + toutes tables |

### 3.2 — Ordre de dépendances

```
001 organizations
  ├─ 002 users
  │  ├─ 020 gamification (users)
  │  ├─ 021 api_keys (users)
  │  └─ 022 subscriptions (users)
  │
  ├─ 003 clients
  │  ├─ 004 chantiers (clients)
  │  │  ├─ 005 devis (chantiers, clients)
  │  │  │  ├─ 006 devis_lines (devis)
  │  │  │  └─ 007 factures (devis)
  │  │  ├─ 008 tickets (chantiers)
  │  │  ├─ 009 depenses (chantiers)
  │  │  ├─ 010 time_entries (chantiers)
  │  │  ├─ 011 photos (chantiers)
  │  │  ├─ 026 deplacements (chantiers)
  │  │  └─ 029 deplacements_frais (chantiers)
  │  │
  │  ├─ 013 avis_google (clients)
  │  ├─ 015 relances (clients, devis, factures)
  │  └─ 035 client_tokens (clients) [V2]
  │
  ├─ 012 contacts_pro
  ├─ 014 emails
  ├─ 016 agent_tasks
  ├─ 017 agent_memory
  ├─ 018 agent_budget
  ├─ 019 knowledge_base (pas org-specific)
  ├─ 023 metier_rules (pas org-specific — global)
  ├─ 024 helper_functions (pas table)
  ├─ 025 rls_policies (pas table)
  ├─ 027 employes
  ├─ 028 fiscal_calendar
  ├─ 030 referrals
  ├─ 031 capsules (pas org-specific)
  ├─ 032 employee_tasks (employes)
  ├─ 033 update subscriptions (alter 022)
  ├─ 034 diagnostics [V2]
  ├─ 036 ai_decisions (users, agents)
  └─ 037 alter all (country_code, edition)
```

---

## 4. MIGRATIONS 001-015 — TABLES DE BASE

### 4.1 — `001_create_organizations.sql`

**Table centrale multi-tenant**. Chaque artisan = 1 organization.

```sql
CREATE TABLE IF NOT EXISTS organizations (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    name TEXT NOT NULL,
    siret TEXT UNIQUE,
    tva_intra TEXT,
    naf_code TEXT,                          -- ex: 43.22A plomberie
    forme_juridique TEXT,                   -- Micro, EI, EURL, SAS
    adresse_siege JSONB,                    -- {rue, cp, ville, pays}
    logo_url TEXT,
    couleurs_brand JSONB,                   -- {primary, secondary}
    rge_certifications JSONB,               -- [{organisme, qualification, validite}]
    decennale JSONB,                        -- {compagnie, numero, validite}
    cgv_url TEXT,
    plan_actuel TEXT DEFAULT 'starter' CHECK (plan_actuel IN ('starter','pro','pro_annuel','business','business_annuel','lifetime')),
    plan_expires_at TIMESTAMPTZ,
    grace_period_until TIMESTAMPTZ,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at TIMESTAMPTZ
);

CREATE INDEX idx_orgs_plan ON organizations(plan_actuel);
CREATE INDEX idx_orgs_siret ON organizations(siret) WHERE siret IS NOT NULL;
```

### 4.2 — `002_create_users.sql`

```sql
CREATE TABLE IF NOT EXISTS users (
    id UUID PRIMARY KEY,                    -- = auth.users.id Supabase
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    email TEXT UNIQUE NOT NULL,
    role TEXT NOT NULL DEFAULT 'owner' CHECK (role IN ('owner','admin','employee','readonly')),
    prenom TEXT,
    nom TEXT,
    telephone TEXT,
    whatsapp_phone TEXT,                    -- E.164
    whatsapp_verified_at TIMESTAMPTZ,
    whatsapp_opt_in_marketing BOOLEAN DEFAULT FALSE,
    locale TEXT NOT NULL DEFAULT 'fr-FR',
    voice_preference TEXT DEFAULT 'male_pro' CHECK (voice_preference IN ('male_pro','female_pro','male_casual','female_casual')),
    voice_speed DECIMAL(2,1) DEFAULT 1.0 CHECK (voice_speed BETWEEN 0.8 AND 1.3),
    notification_preferences JSONB DEFAULT '{"push_enabled":true,"email_digest":"daily","sms_urgences":true}',
    onboarding_completed_at TIMESTAMPTZ,
    last_active_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at TIMESTAMPTZ
);

CREATE UNIQUE INDEX idx_users_whatsapp_phone_org
    ON users(organization_id, whatsapp_phone)
    WHERE whatsapp_phone IS NOT NULL;

CREATE INDEX idx_users_org ON users(organization_id);
```

### 4.3 — `003_create_clients.sql`

```sql
CREATE TABLE IF NOT EXISTS clients (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    type TEXT NOT NULL DEFAULT 'particulier' CHECK (type IN ('particulier','entreprise')),
    civilite TEXT,
    prenom TEXT,
    nom TEXT,
    raison_sociale TEXT,
    siret TEXT,
    tva_intra TEXT,
    email TEXT,
    telephone TEXT,
    adresse JSONB,                          -- {rue, cp, ville, pays}
    source TEXT,                            -- bouche_a_oreille, architecte, site_web, facebook
    apporteur_id UUID REFERENCES contacts_pro(id),
    tags TEXT[],
    whatsapp_opt_in BOOLEAN DEFAULT FALSE,
    first_whatsapp_contact_at TIMESTAMPTZ,
    notes TEXT,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at TIMESTAMPTZ
);

CREATE INDEX idx_clients_org ON clients(organization_id) WHERE deleted_at IS NULL;
CREATE INDEX idx_clients_phone ON clients(telephone);
CREATE INDEX idx_clients_tags ON clients USING GIN(tags);
```

### 4.4 — `004_create_chantiers.sql`

```sql
CREATE TABLE IF NOT EXISTS chantiers (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    client_id UUID NOT NULL REFERENCES clients(id) ON DELETE RESTRICT,
    nom TEXT NOT NULL,
    adresse JSONB,
    type_chantier TEXT CHECK (type_chantier IN ('renovation','construction_neuve','depannage')),
    status TEXT NOT NULL DEFAULT 'prospect' CHECK (status IN (
        'prospect','devis_sent','accepte','en_cours','termine','facture','paye','abandonne'
    )),
    date_debut_prevue DATE,
    date_fin_prevue DATE,
    date_debut_reelle DATE,
    date_fin_reelle DATE,
    budget_estime_ht BIGINT,                -- centimes
    ca_total_ht BIGINT,
    cout_total_ht BIGINT,
    marge_brute_pct DECIMAL(5,2),
    postes TEXT[],                          -- ['plomberie','carrelage',...]
    notes TEXT,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at TIMESTAMPTZ
);

CREATE INDEX idx_chantiers_org_status ON chantiers(organization_id, status) WHERE deleted_at IS NULL;
CREATE INDEX idx_chantiers_client ON chantiers(client_id);
CREATE INDEX idx_chantiers_dates ON chantiers(date_debut_prevue, date_fin_prevue);
```

### 4.5 — `005_create_devis.sql`

```sql
CREATE TABLE IF NOT EXISTS devis (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    client_id UUID REFERENCES clients(id) ON DELETE SET NULL,
    chantier_id UUID REFERENCES chantiers(id) ON DELETE SET NULL,
    numero TEXT NOT NULL,
    status TEXT NOT NULL DEFAULT 'draft' CHECK (status IN (
        'draft','sent','signed','accepted','refused','expired'
    )),
    intitule TEXT,
    adresse_chantier JSONB,
    total_ht BIGINT NOT NULL DEFAULT 0,      -- centimes
    total_tva BIGINT NOT NULL DEFAULT 0,
    total_ttc BIGINT NOT NULL DEFAULT 0,
    tva_breakdown JSONB,                     -- [{rate, base_ht, montant}]
    remise_globale_pct DECIMAL(5,2),
    acompte_pct DECIMAL(5,2),
    conditions_paiement TEXT,
    validite_jours INTEGER NOT NULL DEFAULT 30,
    notes_client TEXT,
    mentions_appliquees TEXT[],             -- ['TVA_10_RENOVATION', ...]
    pdf_url TEXT,
    facturx_pdf_url TEXT,                   -- PDF/A-3 + XML (anticipation)
    signed_pdf_url TEXT,
    signature_provider TEXT,                -- 'yousign'
    signature_id TEXT,                      -- Yousign document ID
    signature_status TEXT,
    signed_at TIMESTAMPTZ,
    sent_at TIMESTAMPTZ,
    expires_at TIMESTAMPTZ,
    confidence_score TEXT CHECK (confidence_score IN ('high','medium','low')),
    ai_model_used TEXT,
    created_by_ai BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at TIMESTAMPTZ,
    UNIQUE (organization_id, numero)
);

CREATE INDEX idx_devis_org_status ON devis(organization_id, status) WHERE deleted_at IS NULL;
CREATE INDEX idx_devis_client ON devis(client_id);
CREATE INDEX idx_devis_chantier ON devis(chantier_id);
CREATE INDEX idx_devis_sent_at ON devis(sent_at) WHERE status = 'sent';
```

### 4.6 — `006_create_devis_lines.sql`

```sql
CREATE TABLE IF NOT EXISTS devis_lines (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    devis_id UUID NOT NULL REFERENCES devis(id) ON DELETE CASCADE,
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    position INTEGER NOT NULL,
    designation TEXT NOT NULL,
    description TEXT,
    quantite DECIMAL(10,2) NOT NULL,
    unite TEXT NOT NULL,                    -- 'unit','m2','ml','m3','h','forfait'
    prix_unitaire_ht BIGINT NOT NULL,       -- centimes
    remise_pct DECIMAL(5,2),
    tva_rate DECIMAL(4,2) NOT NULL CHECK (tva_rate IN (0, 5.5, 10, 20)),
    total_ht BIGINT NOT NULL,
    total_tva BIGINT NOT NULL,
    total_ttc BIGINT NOT NULL,
    poste TEXT,                             -- 'plomberie','carrelage',...
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_devis_lines_devis ON devis_lines(devis_id, position);
```

### 4.7 — `007_create_factures.sql`

```sql
CREATE TABLE IF NOT EXISTS factures (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    devis_id UUID REFERENCES devis(id) ON DELETE SET NULL,
    client_id UUID REFERENCES clients(id) ON DELETE SET NULL,
    chantier_id UUID REFERENCES chantiers(id) ON DELETE SET NULL,
    numero TEXT NOT NULL,
    type_facture TEXT DEFAULT 'standard' CHECK (type_facture IN ('standard','acompte','avancement','solde')),
    pct_avancement DECIMAL(5,2),
    status TEXT NOT NULL DEFAULT 'draft' CHECK (status IN (
        'draft','sent','paid','partial','overdue','cancelled'
    )),
    total_ht BIGINT NOT NULL DEFAULT 0,
    total_tva BIGINT NOT NULL DEFAULT 0,
    total_ttc BIGINT NOT NULL DEFAULT 0,
    tva_breakdown JSONB,
    date_emission DATE NOT NULL,
    date_echeance DATE NOT NULL,
    date_paiement DATE,
    montant_paye BIGINT DEFAULT 0,
    moyen_paiement TEXT,
    reference_paiement TEXT,
    pdf_url TEXT,
    facturx_pdf_url TEXT,
    pa_envoi_status TEXT,                   -- 'pending','sent','delivered','failed' (Plateforme Agréée)
    pa_envoi_id TEXT,
    attestation_tva_10_url TEXT,            -- si travaux rénovation
    sent_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at TIMESTAMPTZ,
    UNIQUE (organization_id, numero)
);

CREATE INDEX idx_factures_org_status ON factures(organization_id, status) WHERE deleted_at IS NULL;
CREATE INDEX idx_factures_echeance ON factures(date_echeance) WHERE status IN ('sent','partial','overdue');
```

### 4.8 — Tables 008-015

Migrations créant les tables essentielles pour les opérations quotidiennes :

**008 `tickets`** — tickets de caisse OCR (fournisseur, date, montants, catégorie, chantier, confidence OCR, photo_url)

**009 `depenses`** — dépenses chantier (manuelles ou depuis ticket, lien chantier, catégorie, marge impact)

**010 `time_entries`** — pointage heures chantier (start/stop timestamps, employé si plan Business, chantier_id)

**011 `photos_chantier`** — galerie photos (chantier_id, type avant/pendant/après/problème/référence, url, thumbnail, GPS, taken_at, analyse IA JSONB)

**012 `contacts_pro`** — architectes + apporteurs d'affaires + fournisseurs privilégiés (type, relations)

**013 `avis_google`** — avis reçus + réponses (rating, text, date, response_text, response_by_ai)

**014 `emails_summary` + `emails_imap_config`** — résumés quotidiens Agent Email Pro + config IMAP (plan Business)

**015 `relances`** — relances envoyées (niveau J+3/J+15/J+30/J+45/mise_en_demeure, ton, devis_or_facture_id, message_sent, channel)

---

## 5. MIGRATIONS 016-025 — AGENTS IA + HELPER

### 5.1 — `016_create_agent_tasks.sql`

Table centrale des tâches des agents + Dossier "À faire" :

```sql
CREATE TABLE IF NOT EXISTS agent_tasks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    user_id UUID REFERENCES users(id),
    agent_name TEXT NOT NULL,               -- 'devis','relance','coach',...
    task_type TEXT NOT NULL,                -- 'create_devis','send_relance',...
    priority TEXT DEFAULT 'medium' CHECK (priority IN ('low','medium','high','urgent')),
    status TEXT NOT NULL DEFAULT 'pending' CHECK (status IN ('pending','running','done','failed','cancelled')),
    input_data JSONB,
    output_data JSONB,
    error_message TEXT,
    requires_validation BOOLEAN DEFAULT TRUE,
    validated_at TIMESTAMPTZ,
    validated_by UUID REFERENCES users(id),
    validation_action TEXT,                 -- 'approved','rejected','modified'
    executed_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE IF NOT EXISTS tasks_to_validate (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    agent_task_id UUID REFERENCES agent_tasks(id) ON DELETE CASCADE,
    user_id UUID NOT NULL REFERENCES users(id),
    summary TEXT NOT NULL,
    action_type TEXT NOT NULL,
    data JSONB,
    priority TEXT DEFAULT 'medium',
    status TEXT DEFAULT 'pending' CHECK (status IN ('pending','approved','rejected','expired')),
    expires_at TIMESTAMPTZ,
    decided_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_agent_tasks_org_status ON agent_tasks(organization_id, status);
CREATE INDEX idx_tasks_to_validate_user_pending ON tasks_to_validate(user_id, status) WHERE status = 'pending';
```

### 5.2 — `017_create_agent_memory.sql`

Table de lien avec Mem0 (voir `docs/MEMORY.md`) :

```sql
CREATE TABLE IF NOT EXISTS agent_memory (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    user_id UUID REFERENCES users(id),
    mem0_user_id TEXT NOT NULL,              -- ID utilisé dans Mem0 cloud
    mem0_session_id TEXT,
    agent_name TEXT,
    memory_layer TEXT NOT NULL CHECK (memory_layer IN ('user','session','agent','organization')),
    last_sync_at TIMESTAMPTZ,
    mempalace_synced BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_agent_memory_org ON agent_memory(organization_id);
CREATE INDEX idx_agent_memory_mem0 ON agent_memory(mem0_user_id);
```

### 5.3 — `018_create_agent_budget.sql`

Tracking budget LLM (voir `docs/SINGLE_SOURCE_OF_TRUTH.md` §4) :

```sql
CREATE TABLE IF NOT EXISTS llm_costs_daily (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    date DATE NOT NULL,
    agent_name TEXT NOT NULL,
    model_used TEXT NOT NULL,
    tokens_input BIGINT DEFAULT 0,
    tokens_output BIGINT DEFAULT 0,
    calls_count INTEGER DEFAULT 0,
    cost_usd DECIMAL(10,6) DEFAULT 0,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    UNIQUE (organization_id, date, agent_name, model_used)
);

CREATE INDEX idx_llm_costs_org_date ON llm_costs_daily(organization_id, date DESC);
```

### 5.4 — `019_create_knowledge_base.sql`

RAG vectoriel pour base métier BTP :

```sql
CREATE EXTENSION IF NOT EXISTS vector;   -- pgvector Supabase

CREATE TABLE IF NOT EXISTS knowledge_chunks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    source_file TEXT NOT NULL,              -- 'metier.md','plomberie.md',...
    chunk_index INTEGER NOT NULL,
    chunk_text TEXT NOT NULL,
    embedding vector(1536),                  -- OpenAI ada-002 1536d ou Claude-compatible
    metadata JSONB,                          -- {metier, region, topic, ...}
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_knowledge_embedding
    ON knowledge_chunks
    USING ivfflat (embedding vector_cosine_ops)
    WITH (lists = 100);
```

### 5.5 — `020_create_gamification.sql`

Voir `docs/FEATURES_COMPLETE.md` F80-F86, F114, F117 :

```sql
CREATE TABLE xp_events (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id),
    event_type TEXT NOT NULL,
    xp_amount INTEGER NOT NULL,
    metadata JSONB,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE quests (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    slug TEXT UNIQUE NOT NULL,
    titre TEXT NOT NULL,
    description TEXT,
    xp_reward INTEGER,
    criteria JSONB,
    order_index INTEGER
);

CREATE TABLE user_quests (
    user_id UUID REFERENCES users(id),
    quest_id UUID REFERENCES quests(id),
    status TEXT DEFAULT 'pending',
    completed_at TIMESTAMPTZ,
    PRIMARY KEY (user_id, quest_id)
);

CREATE TABLE badges (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    slug TEXT UNIQUE NOT NULL,
    nom TEXT NOT NULL,
    description TEXT,
    image_url TEXT,
    criteria JSONB
);

CREATE TABLE user_badges (
    user_id UUID REFERENCES users(id),
    badge_id UUID REFERENCES badges(id),
    earned_at TIMESTAMPTZ DEFAULT NOW(),
    PRIMARY KEY (user_id, badge_id)
);
```

### 5.6 — `021_create_api_keys.sql`

API keys pour intégrations (plan Business uniquement) :

```sql
CREATE TABLE IF NOT EXISTS api_keys (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    user_id UUID NOT NULL REFERENCES users(id),
    name TEXT NOT NULL,
    key_hash TEXT NOT NULL UNIQUE,          -- bcrypt hash
    key_prefix TEXT NOT NULL,               -- premiers chars visibles
    scopes TEXT[] NOT NULL,                 -- ['devis:read','factures:read',...]
    last_used_at TIMESTAMPTZ,
    expires_at TIMESTAMPTZ,
    revoked_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_api_keys_hash ON api_keys(key_hash);
```

### 5.7 — `022_create_subscriptions.sql`

Voir `docs/SINGLE_SOURCE_OF_TRUTH.md` §7 :

```sql
CREATE TABLE IF NOT EXISTS subscriptions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    plan TEXT NOT NULL,
    stripe_customer_id TEXT UNIQUE,
    stripe_subscription_id TEXT UNIQUE,
    stripe_price_id TEXT,
    status TEXT NOT NULL,                   -- active, past_due, canceled, trialing
    current_period_start TIMESTAMPTZ,
    current_period_end TIMESTAMPTZ,
    cancel_at_period_end BOOLEAN DEFAULT FALSE,
    trial_ends_at TIMESTAMPTZ,
    -- Extended in 033
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

### 5.8 — `023_create_metier_rules.sql`

**Constitution BTP en DB** (voir `docs/METIER.md`) :

```sql
CREATE TABLE IF NOT EXISTS tva_rules (
    id SERIAL PRIMARY KEY,
    country_code TEXT NOT NULL DEFAULT 'FR',
    rate DECIMAL(4,2) NOT NULL,
    description TEXT NOT NULL,
    conditions JSONB NOT NULL,              -- {type_travaux, age_logement, rge_required}
    valid_from DATE,
    valid_until DATE
);

CREATE TABLE IF NOT EXISTS mentions_obligatoires (
    id SERIAL PRIMARY KEY,
    country_code TEXT NOT NULL DEFAULT 'FR',
    slug TEXT NOT NULL UNIQUE,              -- ex: 'TVA_10_RENOVATION'
    document_type TEXT NOT NULL CHECK (document_type IN ('devis','facture','facture_acompte')),
    texte_fr TEXT NOT NULL,
    texte_en TEXT,
    texte_es TEXT,
    texte_pt TEXT,
    texte_tr TEXT,
    texte_ar TEXT,
    obligatoire BOOLEAN DEFAULT TRUE,
    conditions JSONB,
    reference_legale TEXT                   -- 'Art. D.111-1 CCH',...
);

CREATE TABLE IF NOT EXISTS templates_devis (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID REFERENCES organizations(id),  -- NULL = template public
    metier TEXT,
    nom TEXT NOT NULL,
    structure JSONB NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

**Seed data Factur-X + TVA + 47 mentions obligatoires** appliqué après cette migration.

### 5.9 — `024_create_helper_functions.sql`

Fonctions SQL utilitaires :

```sql
-- Trigger pour mettre à jour updated_at
CREATE OR REPLACE FUNCTION set_updated_at()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = NOW();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Calcul marge chantier
CREATE OR REPLACE FUNCTION calculate_chantier_margin(p_chantier_id UUID)
RETURNS DECIMAL AS $$
DECLARE
    v_ca BIGINT;
    v_couts BIGINT;
BEGIN
    SELECT COALESCE(SUM(total_ttc), 0) INTO v_ca
    FROM factures WHERE chantier_id = p_chantier_id AND status = 'paid';
    
    SELECT COALESCE(SUM(montant_ttc), 0) INTO v_couts
    FROM depenses WHERE chantier_id = p_chantier_id;
    
    IF v_ca = 0 THEN RETURN 0; END IF;
    RETURN ROUND(((v_ca - v_couts)::DECIMAL / v_ca) * 100, 2);
END;
$$ LANGUAGE plpgsql STABLE;

-- Génération numéro devis
CREATE OR REPLACE FUNCTION next_devis_number(p_org_id UUID)
RETURNS TEXT AS $$
DECLARE
    v_year TEXT := TO_CHAR(NOW(), 'YYYY');
    v_count INTEGER;
BEGIN
    SELECT COUNT(*) + 1 INTO v_count
    FROM devis
    WHERE organization_id = p_org_id
      AND EXTRACT(YEAR FROM created_at) = EXTRACT(YEAR FROM NOW());
    RETURN 'DEV-' || v_year || '-' || LPAD(v_count::TEXT, 3, '0');
END;
$$ LANGUAGE plpgsql;

-- Autres fonctions : next_facture_number, compute_tva_breakdown, etc.
```

### 5.10 — `025_create_rls_policies.sql`

Migration de consolidation — vérifie que RLS est bien activé sur TOUTES les tables métier. Génère les policies manquantes.

Voir §8 pour les patterns RLS standards.

---

## 6. MIGRATIONS 026-029 — EXTENSIONS BTP

### 6.1 — `026_create_deplacements.sql`

Agent Déplacements (F100) :

```sql
CREATE TABLE IF NOT EXISTS deplacements (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    user_id UUID REFERENCES users(id),
    employe_id UUID,                        -- FK employes (027)
    chantier_id UUID REFERENCES chantiers(id) ON DELETE SET NULL,
    date DATE NOT NULL,
    adresse_depart JSONB,
    adresse_arrivee JSONB,
    km_distance DECIMAL(6,2),
    duree_minutes INTEGER,
    gps_trace JSONB,                        -- {start, end, waypoints}
    zone_btp TEXT,                          -- '1A','1B','2',...
    frais_km_amount BIGINT,                 -- centimes, calculés
    indemnite_trajet BIGINT,                -- centimes, selon zone
    panier_repas BIGINT,
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### 6.2 — `027_create_employes.sql`

Agent RH (F123-F124), Business plan :

```sql
CREATE TABLE IF NOT EXISTS employes (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    prenom TEXT NOT NULL,
    nom TEXT NOT NULL,
    email TEXT,
    telephone TEXT,
    convention_collective TEXT CHECK (convention_collective IN ('IDCC_1596','IDCC_1597','IDCC_2609','IDCC_2420','IDCC_1702','IDCC_2614')),
    classification TEXT,                    -- 'Ouvrier niveau III','ETAM E',...
    coefficient INTEGER,
    competences TEXT[],
    date_embauche DATE,
    date_sortie DATE,
    salaire_brut_mensuel BIGINT,
    actif BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

### 6.3 — `028_create_fiscal_calendar.sql`

Agent Fiscalité (F09, F14) :

```sql
CREATE TABLE IF NOT EXISTS fiscal_calendar (
    id SERIAL PRIMARY KEY,
    country_code TEXT DEFAULT 'FR',
    forme_juridique TEXT NOT NULL,          -- Micro,EURL,SAS,EI
    type_echeance TEXT NOT NULL,            -- URSSAF,TVA,CFE,IS,Bilan
    frequence TEXT NOT NULL,                -- mensuel,trimestriel,annuel
    jour_mois INTEGER,
    notes TEXT
);

CREATE TABLE IF NOT EXISTS fiscal_alerts (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    type TEXT NOT NULL,                     -- 'threshold_approaching','due_soon',...
    title TEXT NOT NULL,
    message TEXT NOT NULL,
    severity TEXT DEFAULT 'info',
    due_date DATE,
    read_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### 6.4 — `029_create_deplacements_frais.sql`

Frais détaillés par chantier (carburant, parking, péages) — extension F100.

---

## 7. MIGRATIONS 030-037 — AUDIT V6/V7

### 7.1 — `030_create_referrals.sql` (F114)

```sql
CREATE TABLE referral_codes (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID NOT NULL REFERENCES users(id),
    code TEXT UNIQUE NOT NULL,
    reward_type TEXT DEFAULT 'commission',
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE referrals (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    referrer_user_id UUID REFERENCES users(id),
    referred_user_id UUID REFERENCES users(id),
    code_used TEXT,
    status TEXT DEFAULT 'pending',
    reward_amount_eur DECIMAL(10,2),
    converted_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### 7.2 — `031_create_capsules.sql` (F120)

```sql
CREATE TABLE capsules_index (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    slug TEXT UNIQUE NOT NULL,
    titre TEXT NOT NULL,
    description TEXT,
    video_url TEXT NOT NULL,
    duree_secondes INTEGER DEFAULT 60,
    trigger_events TEXT[],                  -- ['first_devis','first_relance',...]
    locale TEXT DEFAULT 'fr-FR'
);

CREATE TABLE capsule_views (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    capsule_id UUID REFERENCES capsules_index(id),
    viewed_at TIMESTAMPTZ DEFAULT NOW(),
    completed BOOLEAN DEFAULT FALSE,
    feedback TEXT
);
```

### 7.3 — `032_create_tasks.sql` (F123 — tâches employés)

```sql
CREATE TABLE employee_tasks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    employe_id UUID NOT NULL REFERENCES employes(id) ON DELETE CASCADE,
    chantier_id UUID REFERENCES chantiers(id),
    title TEXT NOT NULL,
    description TEXT,
    status TEXT DEFAULT 'todo' CHECK (status IN ('todo','in_progress','done','cancelled')),
    priority TEXT DEFAULT 'medium',
    assigned_at TIMESTAMPTZ DEFAULT NOW(),
    due_date DATE,
    completed_at TIMESTAMPTZ,
    proof_photo_urls TEXT[],
    created_at TIMESTAMPTZ DEFAULT NOW(),
    updated_at TIMESTAMPTZ DEFAULT NOW()
);
```

### 7.4 — `033_update_subscriptions_plans.sql` (F130 — 7 plans)

```sql
ALTER TABLE subscriptions
    ADD COLUMN IF NOT EXISTS is_annual BOOLEAN DEFAULT FALSE,
    ADD COLUMN IF NOT EXISTS is_lifetime BOOLEAN DEFAULT FALSE,
    ADD COLUMN IF NOT EXISTS federation_code TEXT,        -- 'CAPEB20' / 'FFB20'
    ADD COLUMN IF NOT EXISTS federation_verification_url TEXT,
    ADD COLUMN IF NOT EXISTS addon_telephone BOOLEAN DEFAULT FALSE,
    ADD COLUMN IF NOT EXISTS grace_period_ends_at TIMESTAMPTZ;

ALTER TABLE subscriptions
    DROP CONSTRAINT IF EXISTS subscriptions_plan_check,
    ADD CONSTRAINT subscriptions_plan_check CHECK (plan IN (
        'starter','pro','pro_annuel','business','business_annuel','lifetime'
    ));

CREATE INDEX IF NOT EXISTS idx_subs_federation ON subscriptions(federation_code) WHERE federation_code IS NOT NULL;
```

### 7.5 — `034_create_diagnostics.sql` (F126 — V2 uniquement)

```sql
CREATE TABLE diagnostic_trees (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    metier TEXT NOT NULL,                   -- 'plomberie','electricite',...
    panne_slug TEXT NOT NULL,               -- 'fuite_sous_evier'
    question_arbre JSONB NOT NULL,          -- structure d'arbre décision
    locale TEXT DEFAULT 'fr-FR',
    UNIQUE (metier, panne_slug, locale)
);

CREATE TABLE diagnostic_sessions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id),
    tree_id UUID REFERENCES diagnostic_trees(id),
    caller_phone TEXT,
    resolved BOOLEAN,
    steps JSONB,
    paid BOOLEAN DEFAULT FALSE,
    paid_amount_eur DECIMAL(10,2),
    artisan_share_eur DECIMAL(10,2),
    platform_share_eur DECIMAL(10,2),
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### 7.6 — `035_create_client_tokens.sql` (F127 — V2 uniquement)

```sql
CREATE TABLE client_portal_tokens (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    client_id UUID NOT NULL REFERENCES clients(id) ON DELETE CASCADE,
    chantier_id UUID REFERENCES chantiers(id) ON DELETE CASCADE,
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    token TEXT UNIQUE NOT NULL,
    expires_at TIMESTAMPTZ NOT NULL,
    access_scopes TEXT[],                   -- ['view_devis','sign_devis','view_photos','pay_facture']
    last_accessed_at TIMESTAMPTZ,
    access_count INTEGER DEFAULT 0,
    revoked_at TIMESTAMPTZ,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE INDEX idx_client_tokens_hash ON client_portal_tokens(token);
```

### 7.7 — `036_create_ai_audit_log.sql` (F131 — AI Act **CRITIQUE**)

Voir `docs/AI-ACT-COMPLIANCE.md` §9 pour détails complets.

```sql
CREATE TABLE IF NOT EXISTS ai_decisions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
    user_id UUID NOT NULL REFERENCES users(id),
    agent_name TEXT NOT NULL,
    model_used TEXT NOT NULL,
    model_version TEXT NOT NULL,
    input_summary TEXT,
    output_summary TEXT,
    confidence_score TEXT CHECK (confidence_score IN ('high','medium','low')),
    human_validated BOOLEAN DEFAULT FALSE,
    disclaimer_shown BOOLEAN DEFAULT FALSE,
    user_acknowledged BOOLEAN DEFAULT FALSE,
    redirect_to_professional TEXT,
    action_type TEXT,
    action_result TEXT,
    error_code TEXT,
    latency_ms INTEGER,
    tokens_input INTEGER,
    tokens_output INTEGER,
    cost_usd DECIMAL(10, 6),
    created_at TIMESTAMPTZ DEFAULT NOW(),
    retained_until TIMESTAMPTZ DEFAULT (NOW() + INTERVAL '12 months')
);

ALTER TABLE ai_decisions ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Users see own org decisions"
    ON ai_decisions FOR SELECT
    USING (organization_id = (auth.jwt() ->> 'org_id')::uuid);

CREATE POLICY "System can insert"
    ON ai_decisions FOR INSERT
    WITH CHECK (TRUE);

CREATE INDEX idx_ai_decisions_org_created ON ai_decisions(organization_id, created_at DESC);
CREATE INDEX idx_ai_decisions_agent ON ai_decisions(agent_name, created_at DESC);
CREATE INDEX idx_ai_decisions_retained ON ai_decisions(retained_until) WHERE retained_until IS NOT NULL;
```

**Job de purge quotidien** :
```sql
DELETE FROM ai_decisions WHERE retained_until < NOW();
```

### 7.8 — `037_add_country_edition.sql` (F134 + F135)

Architecture multi-pays :

```sql
-- Ajout edition sur organizations (F134 verticalisation)
ALTER TABLE organizations
    ADD COLUMN IF NOT EXISTS edition TEXT DEFAULT 'generic'
        CHECK (edition IN ('generic','plomberie','electricite','maconnerie','carrelage',
                           'peinture','couverture','menuiserie','plaquiste','isolation',
                           'chauffage_clim','facade'));

-- country_code sur toutes les tables principales
ALTER TABLE organizations ADD COLUMN IF NOT EXISTS country_code TEXT NOT NULL DEFAULT 'FR';
ALTER TABLE users ADD COLUMN IF NOT EXISTS country_code TEXT NOT NULL DEFAULT 'FR';
ALTER TABLE clients ADD COLUMN IF NOT EXISTS country_code TEXT NOT NULL DEFAULT 'FR';
ALTER TABLE chantiers ADD COLUMN IF NOT EXISTS country_code TEXT NOT NULL DEFAULT 'FR';
ALTER TABLE devis ADD COLUMN IF NOT EXISTS country_code TEXT NOT NULL DEFAULT 'FR';
ALTER TABLE factures ADD COLUMN IF NOT EXISTS country_code TEXT NOT NULL DEFAULT 'FR';
-- ... (toutes les tables)

-- Index
CREATE INDEX IF NOT EXISTS idx_orgs_country_edition ON organizations(country_code, edition);
CREATE INDEX IF NOT EXISTS idx_devis_country ON devis(country_code);
-- ... (toutes les tables métier)

-- Contrainte pays supportés
ALTER TABLE organizations
    DROP CONSTRAINT IF EXISTS organizations_country_code_check,
    ADD CONSTRAINT organizations_country_code_check CHECK (
        country_code IN ('FR','BE','CH','PT','ES','IT','DE','GB')
    );
```

Voir `docs/SINGLE_SOURCE_OF_TRUTH.md` §14 pour pays supportés.

---

## 8. ROW-LEVEL SECURITY (RLS) — PATTERNS GLOBAUX

### 8.1 — Pattern standard tables org-scoped

Pour toute table avec `organization_id` :

```sql
ALTER TABLE <table> ENABLE ROW LEVEL SECURITY;

-- SELECT
CREATE POLICY "Users see own org <table>"
    ON <table> FOR SELECT
    USING (organization_id = (auth.jwt() ->> 'org_id')::uuid AND deleted_at IS NULL);

-- INSERT
CREATE POLICY "Users create own org <table>"
    ON <table> FOR INSERT
    WITH CHECK (organization_id = (auth.jwt() ->> 'org_id')::uuid);

-- UPDATE
CREATE POLICY "Users update own org <table>"
    ON <table> FOR UPDATE
    USING (organization_id = (auth.jwt() ->> 'org_id')::uuid);

-- DELETE (soft via UPDATE deleted_at, DELETE hard réservé admin)
CREATE POLICY "Users soft-delete own org <table>"
    ON <table> FOR UPDATE
    USING (organization_id = (auth.jwt() ->> 'org_id')::uuid);
```

### 8.2 — Exceptions RLS

**Tables publiques (pas de RLS ou RLS lecture seule publique)** :
- `knowledge_chunks` — base métier BTP partagée
- `tva_rules`, `mentions_obligatoires` — règles publiques
- `quests`, `badges` — catalogues publics
- `capsules_index` — capsules vidéo F120
- `diagnostic_trees` — arbres décision F126 (V2)

### 8.3 — Tables avec RLS custom

**`ai_decisions`** : INSERT système (sans JWT), SELECT org-scoped (voir §7.7).

**`api_keys`** : accès uniquement via service role backend (pas RLS utilisateur).

### 8.4 — Helper JWT

```sql
-- Récupère org_id depuis JWT Supabase
CREATE OR REPLACE FUNCTION auth.current_org_id()
RETURNS UUID AS $$
BEGIN
    RETURN (auth.jwt() ->> 'org_id')::UUID;
END;
$$ LANGUAGE plpgsql STABLE;
```

### 8.5 — Rôle admin (Fabrice)

Role custom `structorai_admin` avec bypass RLS pour endpoints admin :

```sql
CREATE ROLE structorai_admin;
GRANT structorai_admin TO service_role;

-- Policies admin seulement
CREATE POLICY "Admin full access organizations"
    ON organizations FOR ALL
    USING (auth.jwt() ->> 'role' = 'admin');
```

---

## 9. INDEX ET PERFORMANCE

### 9.1 — Index standards

- **FK** : toujours indexées
- **Colonnes filtrage fréquent** : `status`, `sent_at`, `created_at DESC`
- **Composites** : `(org_id, status)`, `(org_id, created_at DESC)`
- **Partial indexes** : `WHERE deleted_at IS NULL` (évite scan rows supprimés)

### 9.2 — Index GIN pour JSONB

```sql
CREATE INDEX idx_devis_tva_breakdown ON devis USING GIN(tva_breakdown);
CREATE INDEX idx_clients_tags ON clients USING GIN(tags);
```

### 9.3 — Index vectoriel pgvector

Pour RAG (`knowledge_chunks.embedding`) — index `ivfflat` avec 100 lists.

### 9.4 — Index pour rétention `ai_decisions`

`idx_ai_decisions_retained` sur `retained_until` pour purge job rapide.

### 9.5 — Monitoring slow queries

Supabase Dashboard → Database → Query Performance → identifier queries > 100ms → optimiser.

---

## 10. SEED DATA

### 10.1 — Fichier `supabase/seed.sql`

Données de référence appliquées en local + staging (**jamais production**, sauf données publiques) :

```sql
-- TVA rules FR (voir docs/METIER.md)
INSERT INTO tva_rules (country_code, rate, description, conditions) VALUES
    ('FR', 20.0, 'Taux normal', '{"type":"all"}'),
    ('FR', 10.0, 'Rénovation logement > 2 ans', '{"type_travaux":"renovation","age_logement_years_gte":2}'),
    ('FR', 5.5, 'Rénovation énergétique RGE', '{"type_travaux":"renovation_energetique","rge_required":true}');

-- 47 mentions obligatoires (FR)
INSERT INTO mentions_obligatoires (country_code, slug, document_type, texte_fr, obligatoire, reference_legale) VALUES
    ('FR', 'IDENTITE_ARTISAN', 'devis', '[Nom] [SIRET] [Adresse siège]', true, 'Art. R.111-1 CCH'),
    ('FR', 'DECENNALE', 'devis', 'Assurance décennale : [Compagnie] — police n°[N°] — validité [Date]', true, 'Art. L.241-1 C.Ass.'),
    ('FR', 'TVA_10_RENOVATION', 'devis', 'TVA 10% - attestation de travaux fournie', true, 'Art. 279-0 bis CGI'),
    -- ... les 44 autres mentions
    ('FR', 'ECO_PARTICIPATION', 'facture', 'Éco-participation mobiliers incluse', true, 'Art. L.541-10-6 C.Env.');

-- Conventions collectives BTP (IDCC)
INSERT INTO conventions_collectives (idcc, nom, metier) VALUES
    ('1596', 'Ouvriers bâtiment <10 salariés', 'batiment'),
    ('1597', 'Ouvriers bâtiment >10 salariés', 'batiment'),
    ('2609', 'ETAM bâtiment', 'batiment'),
    ('2420', 'Cadres BTP', 'btp'),
    ('1702', 'Ouvriers TP', 'tp'),
    ('2614', 'Cadres TP', 'tp');

-- Zones BTP (indemnités trajet)
INSERT INTO zones_btp (code, nom, coefficient_trajet, panier_repas) VALUES
    ('1A', 'Grand Paris', 1.35, 1050),      -- 10.50€
    ('1B', 'Petite couronne', 1.20, 1050),
    ('2', 'Moyenne couronne', 1.10, 1050);

-- Fiscal calendar FR
INSERT INTO fiscal_calendar (forme_juridique, type_echeance, frequence, jour_mois) VALUES
    ('Micro', 'URSSAF', 'mensuel', 5),
    ('Micro', 'URSSAF', 'trimestriel', 5),
    ('EURL', 'TVA', 'mensuel', 19),
    ('SAS', 'IS_acompte', 'trimestriel', 15);

-- Quests onboarding (F80-F86)
INSERT INTO quests (slug, titre, xp_reward, order_index, criteria) VALUES
    ('first_profile', 'Complète ton profil', 50, 1, '{"action":"profile_completed"}'),
    ('first_devis', 'Crée ton premier devis', 200, 2, '{"action":"devis_created"}'),
    ('first_send', 'Envoie ton premier devis', 150, 3, '{"action":"devis_sent"}'),
    ('first_signature', 'Premier devis signé', 300, 4, '{"action":"devis_signed"}'),
    ('first_facture', 'Première facture envoyée', 200, 5, '{"action":"facture_sent"}'),
    ('first_payment', 'Première facture payée', 300, 6, '{"action":"facture_paid"}');
```

### 10.2 — Seed staging (`scripts/seed_staging.py`)

Génère data synthétique **anonymisée** pour staging :
- 10 organizations fake
- 50 users fake
- 500 clients
- 1000 devis, 800 factures
- 2000 tickets

Réexécutable (idempotent via slugs/emails fixés).

### 10.3 — Production

**Aucun seed data artisan** en production. Seuls les seeds des tables publiques (`tva_rules`, `mentions_obligatoires`, `quests`, etc.) sont appliqués.

---

## 11. ROLLBACK PROCEDURES

### 11.1 — Fichiers `_down.sql`

Chaque migration structurante a son fichier de rollback :

```
supabase/migrations/
├── 036_create_ai_audit_log.sql
└── 036_create_ai_audit_log_down.sql    -- DROP TABLE ai_decisions CASCADE;
```

### 11.2 — Rollback en cas de problème

```bash
# 1. Identifier la migration à rollback
supabase migration list

# 2. Revert spécifique via CLI
supabase db reset --to-migration <timestamp_precedent>

# 3. Ou manuellement via psql
psql $SUPABASE_DB_URL -f supabase/migrations/036_create_ai_audit_log_down.sql
```

### 11.3 — Rollback risqué (data loss)

Si migration inclut DROP COLUMN avec data → **restore backup** obligatoire :

```bash
# Restore point-in-time Supabase (si Pro tier, <24h)
# Ou restore backup manuel
```

Voir `docs/DEPLOY.md` §12.5 et §20 DR.

### 11.4 — Migrations non-réversibles

Certaines migrations sont **irréversibles** sans data loss :
- DROP TABLE (si data déjà présente)
- DROP COLUMN (data perdue)
- Type changes destructifs

→ **Toujours backup avant** + documenter dans PR.

---

## 12. SCHÉMA ENTITÉS ET RELATIONS

### 12.1 — Diagramme logique

```
organizations
  ├── users
  │    ├── xp_events
  │    ├── user_quests
  │    ├── user_badges
  │    ├── api_keys
  │    └── referral_codes
  │
  ├── subscriptions (+ stripe_customers)
  │
  ├── clients
  │    └── client_portal_tokens  [V2]
  │
  ├── contacts_pro
  │
  ├── chantiers
  │    ├── devis ──→ devis_lines
  │    │       └── factures
  │    ├── tickets
  │    ├── depenses
  │    ├── time_entries
  │    ├── photos_chantier
  │    ├── deplacements ──→ deplacements_frais
  │    └── employee_tasks
  │
  ├── relances (ref devis/factures)
  ├── avis_google
  ├── emails_summary
  ├── emails_imap_config
  │
  ├── agent_tasks
  │    └── tasks_to_validate (Dossier À faire)
  ├── agent_memory (lien Mem0)
  ├── llm_costs_daily
  ├── ai_decisions (audit AI Act)
  │
  ├── employes ──→ employee_tasks
  │
  ├── fiscal_alerts
  │
  └── diagnostic_sessions  [V2]

-- Tables publiques (pas org-scoped)
knowledge_chunks (RAG)
tva_rules
mentions_obligatoires
templates_devis (si NULL org_id)
quests
badges
capsules_index
diagnostic_trees
fiscal_calendar
```

### 12.2 — Contraintes ON DELETE

**CASCADE** : `organization_id` FKs (suppression org → tout supprimer)

**SET NULL** : relations optionnelles (ex: `devis.client_id`, `photos.chantier_id`)

**RESTRICT** : relations fortes (ex: `chantiers.client_id` — ne pas supprimer client avec chantier actif)

---

## 13. RÈGLES POUR NOUVELLES MIGRATIONS POST-LAUNCH

### 13.1 — Numérotation

Après 037, continuer à 038, 039, 040...

**Règle** : ne JAMAIS réutiliser un numéro, même après rollback.

### 13.2 — Checklist nouvelle migration

- [ ] Nom clair : `NNN_action_table.sql`
- [ ] Idempotent (`IF NOT EXISTS`)
- [ ] Transactions BEGIN/COMMIT
- [ ] RLS policies définies
- [ ] Index sur FK
- [ ] Trigger `set_updated_at`
- [ ] `country_code` + `organization_id` (si org-scoped)
- [ ] Fichier `_down.sql` si réversible
- [ ] Testé en local avec `supabase db reset`
- [ ] Appliqué staging, validé
- [ ] Ajouté dans ce fichier `MIGRATIONS.md` + `SINGLE_SOURCE_OF_TRUTH.md` §6

### 13.3 — Migrations qui cassent le code

**Destructives** (DROP COLUMN, rename TABLE) :
- Deploy multi-étapes :
  1. Migration A : ajout nouvelle colonne/table + code accepte les deux
  2. Migration de data : copie ancienne → nouvelle
  3. Migration B : suppression ancienne (après code 100% migré)
- Tests staging extensifs
- Rollback planifié

### 13.4 — Zero-downtime migrations

- Pas de `LOCK TABLE`
- Pas de migration >5s sur tables >100K rows
- `CREATE INDEX CONCURRENTLY` pour gros volumes

---

## 14. BACKUPS ET PITR

### 14.1 — Backups Supabase automatiques

- **Free/Starter** : backup quotidien conservé 7 jours
- **Pro** : daily + PITR (Point-in-Time Recovery) 7 jours (+$25/mois)
- **Team** : PITR 28 jours

STRUCTORAI : **Pro tier dès 50 clients** (déjà budgété, voir `COUT_REEL.md`).

### 14.2 — Backups manuels

Hebdomadaire : export manuel via `pg_dump` vers bucket Supabase Storage (retenu 30 jours).

```bash
pg_dump $SUPABASE_DB_URL > backups/prod-$(date +%Y%m%d).sql
# Upload vers Supabase Storage bucket 'backups' (chiffré AES-256)
```

### 14.3 — Restore

**Depuis dashboard Supabase** (Backups tab) :
- Sélection date
- Clic "Restore"
- Durée : ~30 min à 2h selon taille

**Depuis backup manuel** :
```bash
psql $SUPABASE_DB_URL < backups/prod-20260415.sql
```

### 14.4 — Retention

- Backups Supabase natifs : 7 jours (Pro) / 28 jours (Team)
- Backups manuels : 30 jours
- Exports complets annuels : archivage 10 ans (obligation fiscale FR)

Voir `SECURITE.md` pour politique de rétention.

---

## 15. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/ARCH.md` §8 | Couche données |
| `docs/API.md` | Endpoints qui utilisent ces tables |
| `docs/AGENTS.md` | Tables utilisées par chaque agent |
| `docs/MEMORY.md` | agent_memory (lien Mem0) |
| `docs/METIER.md` | Règles BTP (seed data) |
| `docs/AI-ACT-COMPLIANCE.md` §9 | `ai_decisions` détails |
| `docs/COACH-DISCLAIMER.md` | Redirections professionnel dans ai_decisions |
| `docs/DEPLOY.md` §9 | Workflow migrations CI/CD |
| `docs/ENV.md` §4 | Variables Supabase |
| `docs/SUPPORT-STRATEGY.md` | tasks_to_validate |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` §6 | Liste synthétique 37 migrations |
| `docs/FEATURES_COMPLETE.md` | Mapping features ↔ migrations |
| `docs/MEMORY-STRATEGY.md` | Backup MemPalace |
| `SECURITE.md` | RLS + chiffrement + rétention |
| `BUILD_PLAN.md` | Structure `backend/supabase/migrations/` |
| `COUT_REEL.md` | Budget Supabase Pro |

### 15.1 — Références externes

- **Supabase CLI** : https://supabase.com/docs/reference/cli
- **PostgreSQL 15 docs** : https://www.postgresql.org/docs/15/
- **pgvector** : https://github.com/pgvector/pgvector
- **RLS Supabase** : https://supabase.com/docs/guides/auth/row-level-security

---

> **Ce fichier est la source de vérité des 37 migrations SQL.**
> **Règle d'or** : JAMAIS modifier une migration déjà appliquée en prod.
> **RLS obligatoire** sur toutes les tables org-scoped.
> **`organization_id` + `country_code`** sur toutes les tables multi-tenant.
> **Numérotation continue** : migrations post-launch = 038, 039, 040... (jamais de réutilisation).
