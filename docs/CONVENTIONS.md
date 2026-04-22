---
name: CONVENTIONS
description: Conventions exhaustives de STRUCTORAI. Naming Python/TypeScript/SQL, patterns de code, structure fichiers, organisation backend/mobile, imports, commentaires, docstrings. Git workflow (branches, commits conventionnels, PRs, code review). Linters configurés (ruff, black, mypy, ESLint, Prettier, TypeScript strict). Pre-commit hooks (git-secrets, conventional commits). Process de review PR. Patterns spécifiques STRUCTORAI (agents IA, prompts séparés, services, erreurs StructorAIError, RLS, soft delete, multi-tenant). Migration versioning. Documentation obligatoire.
type: technical-code-conventions
scope: python-backend, typescript-mobile, sql-supabase, git-workflow, code-review
priority: important
date: 2026-04-18
version: 1.0
python_version: 3.12+
node_version: 20+
typescript_strict: true
git_workflow: trunk-based with feature branches
---

# docs/CONVENTIONS.md — Conventions code STRUCTORAI

> **Ce fichier est la source de vérité des conventions de code.**
> Chaque ligne de code écrite doit respecter ces conventions.
> Reference : `CLAUDE.md` §Conventions + `docs/ARCH.md` §Stack + `docs/TESTS.md` (tests).
> Pattern inspiré Ouroboros + AgentShield + OpenClaw (voir `BUILD_PLAN.md` §repos référence).

---

## 0. SOMMAIRE

1. [Principes fondamentaux](#1-principes-fondamentaux)
2. [Python (backend)](#2-python-backend)
3. [TypeScript (mobile)](#3-typescript-mobile)
4. [SQL (Supabase)](#4-sql-supabase)
5. [Conventions transverses](#5-conventions-transverses)
6. [Structure projet](#6-structure-projet)
7. [Imports](#7-imports)
8. [Documentation code](#8-documentation-code)
9. [Patterns STRUCTORAI](#9-patterns-structorai)
10. [Git workflow](#10-git-workflow)
11. [Branches](#11-branches)
12. [Commits conventionnels](#12-commits-conventionnels)
13. [Pull Requests](#13-pull-requests)
14. [Code review](#14-code-review)
15. [Linters et formatters](#15-linters-et-formatters)
16. [Pre-commit hooks](#16-pre-commit-hooks)
17. [CI/CD checks](#17-cicd-checks)
18. [Refactoring](#18-refactoring)
19. [Versioning](#19-versioning)
20. [Documentation obligatoire](#20-documentation-obligatoire)
21. [Anti-patterns](#21-anti-patterns)
22. [Références croisées](#22-références-croisées)

---

## 1. PRINCIPES FONDAMENTAUX

### 1.1 — 10 règles immuables

1. **Cohérence > préférence personnelle** — suis les conventions même si tu préfères autre chose
2. **Lisibilité > cleverness** — le code doit être lu 10× plus qu'il n'est écrit
3. **Explicite > implicite** — nommage clair, pas de magie
4. **Simple > complexe** — commencer simple, ajouter complexité seulement si besoin
5. **Async partout** (backend) — pas de mélange sync/async
6. **TypeScript strict** (mobile) — pas de `any`
7. **Tests co-localisés** — test à côté du code testé
8. **Commits petits et fréquents** — pas de giant commits
9. **Un PR = un concern** — pas de PR fourre-tout
10. **Docs à jour** — si le code change, la doc aussi

### 1.2 — Philosophie

**STRUCTORAI build = 25 jours** avec Claude Code. Pragmatisme > perfectionnisme.

Cela signifie :
- **Pattern éprouvé** > innovation
- **Convention standard** > réinvention
- **Boring code** > clever code
- **Tests ciblés** > TDD strict

### 1.3 — Quand déroger

Dans ~5% des cas, une convention peut être contournée. Dans ce cas :
- **Commentaire explicite** expliquant pourquoi
- **Review manuelle** obligatoire
- **Décision tracée** dans `docs/DECISIONS-LOG.md` si impact durable

---

## 2. PYTHON (BACKEND)

### 2.1 — Version

**Python 3.12+** obligatoire. Utilise :
- Type hints modernes (`list[str]`, `dict[str, int]`, `X | None`)
- Pattern matching (`match/case`) quand pertinent
- `asyncio.TaskGroup` pour parallélisme

### 2.2 — Naming

```python
# Variables, fonctions, modules
snake_case
user_id = "..."
def calculate_tva(...): ...

# Classes
PascalCase
class DevisService: ...

# Constantes, env vars
UPPER_SNAKE_CASE
MAX_DEVIS_PER_DAY = 100
ANTHROPIC_API_KEY = os.getenv("ANTHROPIC_API_KEY")

# Méthodes privées
_private_method()
__dunder__

# Variables "non utilisées" dans tuples
_, value = tuple_unpacking()

# Enums
class DevisStatus(str, Enum):
    DRAFT = "draft"
    SENT = "sent"
```

### 2.3 — Type hints obligatoires

```python
# ✅ Bon
async def get_devis(devis_id: str, user: User) -> Devis:
    ...

# ❌ Mauvais
async def get_devis(devis_id, user):
    ...
```

### 2.4 — Async partout

```python
# ✅ Bon — async complet
async def create_devis(data: DevisInput) -> Devis:
    async with db.transaction():
        devis = await db.insert("devis", data.dict())
        await event_bus.publish("devis.created", devis)
    return devis

# ❌ Mauvais — mélange sync/async
def create_devis_sync(data):  # Pas async
    await db.insert(...)  # Erreur
```

### 2.5 — Pydantic v2 partout

```python
# Models : Pydantic, PAS dataclass ni dict
from pydantic import BaseModel, Field, validator

class DevisLine(BaseModel):
    poste: str = Field(min_length=1, max_length=500)
    quantite: float = Field(gt=0)
    unite: Literal["m2", "ml", "u", "h", "forfait"]
    prix_unitaire_ht_centimes: int = Field(ge=0)
    tva_rate: float = Field(ge=0, le=20)
    
    @validator("tva_rate")
    def valid_tva_btp(cls, v):
        if v not in [0, 5.5, 10, 20]:
            raise ValueError("TVA BTP France : 0, 5.5, 10 ou 20 uniquement")
        return v
```

### 2.6 — Fichiers et modules

```python
# Nommage fichiers
app/api/v1/devis.py                 # endpoints REST
app/services/pdf_service.py          # logique métier
app/agents/agent_devis.py            # agent IA (prefix agent_)
app/models/devis.py                  # modèles Pydantic
app/prompts/devis_prompt.py          # prompts (prefix nom + _prompt)
app/utils/errors.py                  # utilitaires
app/middleware/auth.py               # middleware FastAPI
app/database.py                      # connexion DB (root)

# Un fichier = une responsabilité
# Pas de "utils.py" fourre-tout — créer sous-modules (utils/tva.py, utils/dates.py)
```

### 2.7 — Classes vs fonctions

**Préférer les fonctions** pour la logique métier. Classes pour :
- Services avec état (`PricingService`, `KnowledgeService`)
- Pydantic models
- Agents IA (pattern BaseAgent)

```python
# ✅ Bon — fonction pure
def calculate_tva(ht: Decimal, rate: float) -> Decimal:
    return (ht * Decimal(rate) / 100).quantize(Decimal("0.01"))

# ❌ Mauvais — classe sans raison
class TVACalculator:
    @staticmethod
    def calculate(ht, rate):
        ...
```

### 2.8 — Erreurs : StructorAIError

Voir `docs/ERRORS.md` §2.

```python
# ✅ Bon
from app.utils.errors import NotFoundError

if not devis:
    raise NotFoundError(
        code="DEVIS_NOT_FOUND",
        message_fr="Le devis demandé n'existe pas",
        resource_id=devis_id,
    )

# ❌ Mauvais
raise Exception("Devis not found")
raise HTTPException(status_code=404)  # Utiliser StructorAIError
```

### 2.9 — Logging

Utilisation de **structlog** (JSON structuré) :

```python
import structlog

logger = structlog.get_logger()

# ✅ Bon
logger.info(
    "devis_created",
    devis_id=devis.id,
    total_ht=devis.total_ht,
    user_id=user.id,
)

# ❌ Mauvais — print, f-strings, pas de context
print(f"Devis créé {devis.id}")
logger.info(f"Devis {devis.id} créé par {user.id}")  # Pas de kwargs
```

### 2.10 — Docstrings

```python
async def get_price(
    poste: str,
    metier: str,
    context: PricingContext,
) -> PricingResult:
    """
    Récupère le prix d'un poste BTP.
    
    Pipeline de lookup :
    1. Mem0 (prix perso artisan)
    2. Référentiel data/prix/*.json
    3. Benchmark département
    4. Web fallback (dernier recours)
    
    Args:
        poste: Description du poste (ex: "pose carrelage 40x40 rectifié")
        metier: Métier (plomberie, electricite, carrelage, ...)
        context: Contexte devis (zone, type chantier, organization)
    
    Returns:
        PricingResult avec prix min/max/median + source + confidence
    
    Raises:
        NotFoundError: Si aucun prix trouvé (code METIER_REFERENCE_PRICE_NOT_FOUND)
    """
```

Docstrings **obligatoires** pour fonctions publiques des services + agents. Pas nécessaire pour helpers internes triviaux.

### 2.11 — Imports

Voir §7.

### 2.12 — Line length

**88 caractères** max (default Black). Dépasser uniquement pour URLs ou regex complexes.

---

## 3. TYPESCRIPT (MOBILE)

### 3.1 — Version

**TypeScript 5.3+** avec strict mode activé :

```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "noImplicitReturns": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true
  }
}
```

### 3.2 — Naming

```typescript
// Variables, fonctions
camelCase
const userId = "...";
function calculateTVA(...) { ... }

// Composants React, types, interfaces
PascalCase
function DevisCard({ devis }: Props) { ... }
interface DevisProps { ... }
type DevisStatus = "draft" | "sent";

// Constantes
UPPER_SNAKE_CASE (module-level) OR camelCase (local)
const MAX_UPLOAD_SIZE_MB = 10;
const defaultConfig = {...};  // local

// Hooks
camelCase starting with "use"
function useDevis(id: string) { ... }

// Props types
interface ComponentNameProps { ... }
// ou
type ComponentNameProps = { ... }
```

### 3.3 — Fichiers

```
app/(tabs)/devis.tsx              # page Expo Router (kebab ou lowercase)
components/DevisCard.tsx          # composant (PascalCase)
components/ui/Button.tsx          # composant UI
hooks/useDevis.ts                 # hook custom
lib/api.ts                        # service
lib/repository/devisRepo.ts       # repository
types/devis.ts                    # types partagés
i18n/fr.json                      # traductions
contexts/AuthContext.tsx          # Context React
```

### 3.4 — Composants fonctionnels

```tsx
// ✅ Bon — composant fonctionnel + FC typé via Props
interface DevisCardProps {
  devis: Devis;
  onPress?: (devis: Devis) => void;
}

export function DevisCard({ devis, onPress }: DevisCardProps) {
  return (
    <TouchableOpacity onPress={() => onPress?.(devis)}>
      <Text>{devis.numero}</Text>
    </TouchableOpacity>
  );
}

// ❌ Mauvais — class component (sauf exception Error Boundary)
class DevisCard extends React.Component { ... }
```

### 3.5 — Types stricts (no any)

```typescript
// ✅ Bon
function parseDevis(raw: unknown): Devis | null {
  if (!isDevis(raw)) return null;
  return raw as Devis;
}

// ❌ Mauvais
function parseDevis(raw: any) {
  return raw;
}
```

### 3.6 — Async / Promises

```typescript
// ✅ Bon — async/await propre
async function loadDevis() {
  try {
    const devis = await api.get<Devis[]>("/v1/devis");
    setDevis(devis);
  } catch (err) {
    showError(err);
  }
}

// ❌ Mauvais — .then chains imbriqués
api.get("/v1/devis").then(res => {
  return res.json();
}).then(data => {
  setData(data);
}).catch(err => console.log(err));
```

### 3.7 — State management

- **Local state** : `useState`
- **Server state** : TanStack Query (`useQuery`, `useMutation`)
- **Global state** : Zustand (`useAuthStore`, `useNetworkStore`)

```typescript
// ✅ Bon
const { data: devis, isLoading } = useQuery({
  queryKey: ["devis", id],
  queryFn: () => api.getDevis(id),
});

// ❌ Mauvais — Redux/Context pour du server state
```

### 3.8 — Styling

**NativeWind** (Tailwind RN) — pas de StyleSheet :

```tsx
// ✅ Bon
<View className="p-4 bg-white rounded-lg shadow-sm">
  <Text className="text-lg font-bold text-gray-900">{title}</Text>
</View>

// ❌ Mauvais
<View style={{ padding: 16, backgroundColor: "#fff" }}>
  <Text style={{ fontSize: 18, fontWeight: "bold" }}>{title}</Text>
</View>
```

### 3.9 — i18n

Voir `docs/I18N.md`.

```tsx
// ✅ Bon
import { useTranslation } from "react-i18next";

function Header() {
  const { t } = useTranslation();
  return <Text>{t("dashboard.welcome", { name: user.prenom })}</Text>;
}

// ❌ Mauvais — hardcoded
<Text>Bienvenue {user.prenom}</Text>
```

### 3.10 — Error boundaries

Envelopper chaque screen principal dans un Error Boundary :

```tsx
<ErrorBoundary fallback={<ErrorScreen />}>
  <DevisScreen />
</ErrorBoundary>
```

---

## 4. SQL (SUPABASE)

### 4.1 — Naming

```sql
-- Tables : snake_case, pluriel
CREATE TABLE organizations ( ... );
CREATE TABLE devis ( ... );          -- pas devis(singular) - devis français invariant
CREATE TABLE devis_lines ( ... );    -- table de jointure : singulier_singulier

-- Colonnes : snake_case
user_id UUID
created_at TIMESTAMPTZ

-- Index : idx_<table>_<colonnes>
CREATE INDEX idx_devis_user_status ON devis(user_id, status);

-- Contraintes : <type>_<description>
CONSTRAINT pk_devis PRIMARY KEY (id)
CONSTRAINT fk_devis_client FOREIGN KEY (client_id) REFERENCES clients(id)
CONSTRAINT check_positive_ht CHECK (total_ht >= 0)
CONSTRAINT unique_numero_per_org UNIQUE (organization_id, numero)

-- Triggers : tg_<table>_<action>
CREATE TRIGGER tg_devis_updated_at BEFORE UPDATE ON devis ...

-- Functions : verb_noun
CREATE FUNCTION update_updated_at() ...
CREATE FUNCTION calculate_devis_total(devis_id UUID) ...

-- RLS policies : <table>_<action>_<audience>
CREATE POLICY devis_select_own ON devis FOR SELECT USING (...);
CREATE POLICY devis_insert_own ON devis FOR INSERT WITH CHECK (...);
```

### 4.2 — Migrations

```sql
-- Filename : NNN_verb_description.sql
-- Ex: 015_create_devis.sql, 028_add_rge_to_organizations.sql

-- Structure obligatoire
-- backend/migrations/015_create_devis.sql

-- Migration : 015_create_devis
-- Auteur : fabrice
-- Date : 2026-04-15
-- Description : Création table devis avec TVA multi-taux

BEGIN;

CREATE TABLE IF NOT EXISTS devis ( ... );

CREATE INDEX IF NOT EXISTS idx_devis_user_status ON devis(user_id, status);

-- RLS (obligatoire)
ALTER TABLE devis ENABLE ROW LEVEL SECURITY;
CREATE POLICY devis_select_own ON devis FOR SELECT ...;

-- Trigger updated_at
CREATE TRIGGER tg_devis_updated_at BEFORE UPDATE ON devis
    FOR EACH ROW EXECUTE FUNCTION update_updated_at();

COMMIT;
```

### 4.3 — Règles obligatoires par table

Voir `CLAUDE.md` §SQL + `docs/MIGRATIONS.md` §3.

Chaque table métier doit avoir :
- `id UUID PRIMARY KEY DEFAULT gen_random_uuid()`
- `organization_id UUID NOT NULL REFERENCES organizations(id)` (multi-tenant)
- `created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()`
- `updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()` + trigger
- `deleted_at TIMESTAMPTZ NULL` (soft delete)
- **RLS ACTIVÉE** sans exception

### 4.4 — Soft delete

```sql
-- ✅ Bon
UPDATE devis SET deleted_at = NOW() WHERE id = $1;

-- Queries filtrent deleted_at IS NULL
SELECT * FROM devis WHERE id = $1 AND deleted_at IS NULL;

-- ❌ Mauvais — DELETE physique (perte data, audit trail cassé)
DELETE FROM devis WHERE id = $1;
```

### 4.5 — Types recommandés

```sql
-- IDs : UUID (jamais INT auto-increment)
id UUID PRIMARY KEY DEFAULT gen_random_uuid()

-- Timestamps : TIMESTAMPTZ (jamais TIMESTAMP)
created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()

-- Strings : TEXT (jamais VARCHAR(n) limité)
nom TEXT NOT NULL

-- Monnaie : INTEGER en centimes (jamais FLOAT)
total_ht INTEGER NOT NULL CHECK (total_ht >= 0)

-- JSON : JSONB (jamais JSON)
metadata JSONB NOT NULL DEFAULT '{}'::jsonb

-- Enums : TEXT avec CHECK constraint OU type enum PostgreSQL
status TEXT CHECK (status IN ('draft', 'sent', 'signed'))
```

---

## 5. CONVENTIONS TRANSVERSES

### 5.1 — Langue code et commentaires

- **Code** : anglais (variable names, function names, etc.)
- **Commentaires** : anglais OU français (cohérence dans un fichier)
- **Docs utilisateur** : français prioritaire
- **Logs** : anglais (tech context)
- **Erreurs user-facing** : français (voir `message_fr` dans StructorAIError)

### 5.2 — Unités et formats

- **Monnaie** : toujours en **centimes** (entiers) dans code + DB
  - `total_ht: 480000` (= 4 800,00 €)
  - Conversion UI-facing au dernier moment
- **Pourcentages TVA** : `5.5`, `10.0`, `20.0` (floats)
- **Dates** : ISO 8601 UTC (`2026-04-18T14:32:00Z`)
- **UUIDs** : v4 (format standard `550e8400-e29b-41d4-a716-446655440000`)
- **Téléphones** : E.164 (`+33612345678`)
- **Sizes fichiers** : bytes (entiers)

### 5.3 — Gestion du null / absence

```python
# ✅ Bon — Optional explicite
def get_user(user_id: str) -> User | None:
    ...

# ❌ Mauvais
def get_user(user_id):
    ...  # Peut retourner None sans doc
```

```typescript
// ✅ Bon
function getUser(id: string): User | null { ... }

// ❌ Mauvais
function getUser(id: string): User { ... }  // Peut retourner undefined en réalité
```

### 5.4 — Constantes vs magic numbers

```python
# ✅ Bon
MAX_DEVIS_STARTER_MONTHLY = 5
MAX_VOICE_QUOTA_PRO = 100

if user.plan == "starter" and devis_count_this_month >= MAX_DEVIS_STARTER_MONTHLY:
    raise QuotaError(...)

# ❌ Mauvais
if user.plan == "starter" and devis_count_this_month >= 5:  # Magic number
    ...
```

### 5.5 — Commentaires : pourquoi, pas quoi

```python
# ✅ Bon — explique le pourquoi
# On prend le cours moyen des 3 derniers devis signés pour lisser
# les variations liées aux négociations clients.
median_price = median(last_3_signed_prices)

# ❌ Mauvais — décrit le code
# On calcule la médiane des 3 prix précédents
median_price = median(last_3_signed_prices)
```

---

## 6. STRUCTURE PROJET

### 6.1 — Backend

```
backend/
├── app/
│   ├── api/                        # Endpoints HTTP
│   │   ├── v1/
│   │   │   ├── auth.py
│   │   │   ├── devis.py
│   │   │   └── ...
│   │   └── deps.py                 # Dependency injection FastAPI
│   ├── agents/                     # Agents IA
│   │   ├── base_agent.py
│   │   ├── supervisor.py
│   │   ├── agent_devis.py
│   │   └── ...
│   ├── prompts/                    # System prompts (séparés du code)
│   │   ├── devis_prompt.py
│   │   └── ...
│   ├── services/                   # Logique métier
│   │   ├── pricing_service.py
│   │   ├── knowledge_service.py
│   │   ├── pdf_service.py
│   │   └── ...
│   ├── models/                     # Modèles Pydantic
│   │   ├── user.py
│   │   ├── devis.py
│   │   └── ...
│   ├── middleware/                 # Middlewares FastAPI
│   │   ├── auth.py
│   │   ├── error_handler.py
│   │   └── rate_limit.py
│   ├── utils/                      # Utilitaires
│   │   ├── errors.py
│   │   ├── scrubbing.py
│   │   └── logging.py
│   ├── database.py                 # Connexion DB
│   ├── config.py                   # Settings (pydantic-settings)
│   └── main.py                     # FastAPI app
├── migrations/                     # SQL migrations
│   ├── 001_create_organizations.sql
│   └── ...
├── data/                           # Data statique (prix, TVA)
│   ├── prix/
│   └── legal/
├── tests/                          # Voir docs/TESTS.md
├── pyproject.toml
└── Dockerfile
```

### 6.2 — Mobile

```
mobile/
├── app/                            # Expo Router (file-based routing)
│   ├── (auth)/
│   │   ├── login.tsx
│   │   └── signup.tsx
│   ├── (tabs)/
│   │   ├── index.tsx               # Dashboard
│   │   ├── devis.tsx
│   │   └── chantiers.tsx
│   └── _layout.tsx                 # Root layout
├── components/                     # Composants React
│   ├── ui/                         # Composants UI bas niveau
│   │   ├── Button.tsx
│   │   └── Input.tsx
│   └── DevisCard.tsx               # Composants métier
├── hooks/                          # Hooks custom
│   ├── useDevis.ts
│   └── useI18n.ts
├── lib/                            # Librairies internes
│   ├── api.ts
│   ├── db.ts                       # SQLite
│   ├── repository/                 # Pattern repository offline
│   │   ├── devisRepo.ts
│   │   └── ...
│   └── sync.ts
├── contexts/                       # Context React
│   ├── AuthContext.tsx
│   └── NetworkContext.tsx
├── i18n/                           # Traductions
│   ├── fr.json
│   ├── en.json
│   └── ...
├── types/                          # Types partagés
├── assets/                         # Images, fonts
├── app.json                        # Config Expo
├── package.json
└── tsconfig.json
```

### 6.3 — Docs

```
docs/
├── METIER.md                       # Constitution BTP
├── ARCH.md                         # Architecture
├── API.md                          # Spec API
├── AGENTS.md                       # 14 agents
├── MIGRATIONS.md                   # Migrations SQL
├── ERRORS.md                       # Catalogue erreurs
├── TESTS.md                        # Stratégie tests
├── CONVENTIONS.md                  # (ce fichier)
└── ...
```

---

## 7. IMPORTS

### 7.1 — Python — ordre

```python
# 1. Standard library
import os
import asyncio
from datetime import datetime
from uuid import UUID

# 2. Third-party
import httpx
from fastapi import FastAPI, Depends
from pydantic import BaseModel

# 3. Local (app.*)
from app.config import settings
from app.models.devis import Devis
from app.services.pricing_service import pricing_service
from app.utils.errors import NotFoundError
```

Séparer par **ligne vide** entre groupes. Géré automatiquement par `ruff` / `isort`.

### 7.2 — Python — style

```python
# ✅ Bon — imports explicites
from app.services.pricing_service import pricing_service

# ❌ Mauvais — star imports
from app.services import *

# ❌ Mauvais — imports relatifs sauf tests
from .utils import ...  # Évitez
```

### 7.3 — TypeScript — ordre

```typescript
// 1. React / RN
import React, { useState, useEffect } from "react";
import { View, Text, TouchableOpacity } from "react-native";

// 2. Third-party
import { useQuery } from "@tanstack/react-query";
import { useTranslation } from "react-i18next";

// 3. Local absolute
import { api } from "@/lib/api";
import { Devis } from "@/types/devis";

// 4. Local relative
import { DevisCardStyles } from "./DevisCard.styles";
```

Géré par ESLint `import/order`.

### 7.4 — Alias paths

Configurer `@/` = root mobile :

```json
// tsconfig.json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./*"]
    }
  }
}
```

```typescript
// ✅ Bon
import { api } from "@/lib/api";

// ❌ Mauvais
import { api } from "../../../lib/api";
```

---

## 8. DOCUMENTATION CODE

### 8.1 — Docstrings Python (obligatoire)

Voir §2.10. Obligatoire pour :
- Services (`PricingService`, `KnowledgeService`)
- Agents IA (méthodes publiques)
- Endpoints API (auto-généré dans OpenAPI via FastAPI)
- Helpers publics dans `app/utils/`

Pas obligatoire pour :
- Fonctions 1-3 lignes triviales
- Models Pydantic (champs self-describing)
- Tests

### 8.2 — JSDoc TypeScript (selectif)

Pour fonctions complexes :

```typescript
/**
 * Synchronise la queue locale avec le backend.
 * Procède par batch de 50 ops, avec retry backoff en cas d'erreur.
 * 
 * @param force - Si true, ignore le lock syncInProgress
 * @returns Promise résolue quand la sync est terminée
 */
export async function triggerSync(force = false): Promise<void> {
  ...
}
```

Optionnel sinon (le typing TS fait déjà beaucoup).

### 8.3 — Commentaires de section

```python
# ==================================================================
# SECTION: Helpers TVA
# ==================================================================

def calculate_tva(...): ...
```

Utile pour fichiers >200 lignes. Éviter trop de décoration.

### 8.4 — TODO / FIXME

```python
# TODO(fabrice): Optimiser avec batch DB (actuellement N+1 query)
# FIXME: Edge case si locale=null — voir ticket #123
# NOTE: Ce delay est intentionnel, voir docs/OFFLINE.md §22.3
```

Format : `TODO(author): description`. Trackés par grep.

---

## 9. PATTERNS STRUCTORAI

### 9.1 — Pattern BaseAgent

```python
# app/agents/base_agent.py

class BaseAgent(ABC):
    """Classe de base pour tous les agents IA."""
    
    name: str                        # "devis", "relance", ...
    model: str                       # "claude-opus-4-7"
    system_prompt: str
    tools: list[Tool]
    budget_max_per_call: Decimal     # En USD
    
    @abstractmethod
    async def handle(self, message: str, user: User, context: dict) -> AgentResponse:
        ...
    
    async def _call_claude(self, messages: list[Message]) -> ClaudeResponse:
        # Logique commune : rate limit, logging, error handling, budget tracking
        ...
```

Chaque agent hérite :
```python
class DevisAgent(BaseAgent):
    name = "devis"
    model = "claude-opus-4-7"
    system_prompt = DEVIS_SYSTEM_PROMPT  # from prompts/devis_prompt.py
    tools = [get_price, generate_devis_pdf, send_devis, search_knowledge]
    budget_max_per_call = Decimal("0.15")
    
    async def handle(self, message, user, context):
        ...
```

### 9.2 — Pattern Service

```python
# app/services/pricing_service.py

class PricingService:
    def __init__(
        self,
        mem0_client: Mem0Client,
        referentiel_loader: ReferentielLoader,
        benchmark_service: BenchmarkService,
        web_search_service: WebSearchService,
    ):
        self.mem0 = mem0_client
        self.referentiel = referentiel_loader
        self.benchmark = benchmark_service
        self.web = web_search_service
    
    async def get_price(self, poste: str, context: PricingContext) -> PricingResult:
        ...


# Singleton module-level
pricing_service = PricingService(...)
```

Instantiation via DI FastAPI :

```python
# app/api/deps.py

def get_pricing_service() -> PricingService:
    return pricing_service
```

### 9.3 — Pattern Repository (mobile)

```typescript
// mobile/lib/repository/devisRepo.ts

export const devisRepo = {
  async getAll(): Promise<Devis[]> {
    const db = await getDatabase();
    return db.getAllAsync<Devis>("SELECT * FROM devis WHERE deleted_at IS NULL ORDER BY updated_at DESC");
  },
  
  async getById(id: string): Promise<Devis | null> {
    const db = await getDatabase();
    return db.getFirstAsync<Devis>("SELECT * FROM devis WHERE id = ?", [id]);
  },
  
  async create(data: DevisInput): Promise<Devis> {
    // Création locale + enqueue sync
    ...
  },
};
```

Les **components ne font JAMAIS de SQL direct** — toujours via repository.

### 9.4 — Pattern erreur métier

```python
# ✅ Bon
if not organization.rge_valid:
    raise BusinessLogicError(
        code="FISCAL_RGE_INVALID",
        message_fr="TVA 5,5% nécessite une certification RGE valide.",
    )

# ❌ Mauvais
if not organization.rge_valid:
    return {"error": "RGE invalide"}  # Réponse non structurée
```

---

## 10. GIT WORKFLOW

### 10.1 — Stratégie

**Trunk-based development** simplifié :
- `main` = production (déploiement auto Railway/Vercel/EAS)
- `develop` = staging (merge régulier avant release)
- `feature/*` = branches de travail (courtes, <1 semaine)
- `hotfix/*` = correctifs urgents

### 10.2 — Flow type

```
1. git checkout -b feature/add-agent-rh
2. [code + commit locally]
3. git push origin feature/add-agent-rh
4. Ouvrir PR vers develop
5. CI green + 1 review
6. Merge develop
7. [tests staging OK]
8. PR develop → main
9. Merge → deploy prod
```

### 10.3 — Rebase vs merge

- **Sur feature branches** : `git pull --rebase origin develop` pour rester à jour
- **Merge vers develop/main** : **merge commit** (pas squash) pour préserver l'historique détaillé des features importantes
- **Squash** : OK pour petites features (1-3 commits désordonnés)

### 10.4 — Commits fréquents

```
✅ Bon — commits petits et fréquents
- feat(devis): add TVA 5.5% validation
- test(devis): add TVA validation tests
- fix(devis): handle RGE expired case

❌ Mauvais — giant commit
- Update everything (3000 lines changed)
```

### 10.5 — Préserver l'historique

- **Pas de force-push sur main/develop** (protection branch obligatoire)
- **Force-push OK sur features personnelles** (rebase autorisé)
- **Pas de rewriting d'historique partagé**

---

## 11. BRANCHES

### 11.1 — Naming

```
feature/<scope>-<description>
  feature/add-agent-rh
  feature/whatsapp-templates-turkish
  feature/offline-sync-improvements

fix/<scope>-<description>
  fix/tva-calculation-edge-case
  fix/login-redirect-loop

hotfix/<scope>
  hotfix/payment-webhook-crash

chore/<task>
  chore/upgrade-fastapi
  chore/cleanup-old-migrations

docs/<topic>
  docs/update-api-endpoints
  docs/add-deployment-guide

refactor/<scope>
  refactor/extract-pricing-service
```

### 11.2 — Durée de vie

- **Feature branch** : **max 1 semaine** — au-delà, rebase quotidien ou merger plus tôt
- **Hotfix** : **<24h** — merge direct en main + backport develop
- **Long-running branches** : **interdites** (gel de code, merge hell)

### 11.3 — Protection

**main** :
- PR obligatoire
- 1 review minimum
- CI green obligatoire
- Pas de force push
- Pas de delete

**develop** :
- PR obligatoire
- CI green obligatoire
- Pas de force push

**feature/*, fix/*** :
- Libre

---

## 12. COMMITS CONVENTIONNELS

### 12.1 — Format

**Conventional Commits** (https://www.conventionalcommits.org/) :

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

### 12.2 — Types

| Type | Usage |
|---|---|
| `feat` | Nouvelle feature |
| `fix` | Bug fix |
| `docs` | Documentation seulement |
| `style` | Formatting, pas de logic change |
| `refactor` | Refactor sans changement fonctionnel |
| `test` | Ajout/modif tests |
| `chore` | Maintenance (deps, config) |
| `perf` | Amélioration performance |
| `ci` | Config CI/CD |
| `build` | Système de build |
| `revert` | Revert commit |

### 12.3 — Scopes

Alignés sur modules :
- `devis`, `factures`, `relance`, `compta`, `planning`, `reputation`, `prospection`, `email-pro`, `fiscalite`, `deplacements`, `rh`, `vision`, `site-web`, `coach`, `support`, `supervisor`
- `auth`, `api`, `db`, `mobile`, `offline`, `sync`
- `i18n`, `voice`, `whatsapp`, `stripe`, `yousign`
- `ci`, `deploy`, `docs`, `tests`

### 12.4 — Exemples

```
✅ Bon
feat(devis): add TVA 5.5% validation with RGE check

Validates that TVA 5.5% can only be applied if organization has
valid RGE certification. Raises FISCAL_RGE_INVALID if not.

Closes #123

---

fix(offline): handle network disconnect during sync batch

Previously, a disconnect during sync could leave ops in "in_flight"
status permanently. Now resets to "pending" on error.

---

docs(api): document POST /v1/sync/batch endpoint

---

refactor(pricing): extract PricingContext from get_price args

---

test(agents): add anti-invention test for Devis agent

---

chore(deps): bump anthropic-sdk to 0.45

❌ Mauvais
update code          ← pas de type/scope
wip                  ← temporaire, pas descriptif
Add stuff            ← imprécis
fixed the bug        ← quel bug ?
```

### 12.5 — Description

- Verbe à l'**impératif présent** : "add", "fix", "update" (pas "added", "fixed")
- **Lowercase** (sauf acronymes : "TVA", "RGE", "WhatsApp")
- **<72 caractères** pour la 1ère ligne
- **Pas de ponctuation finale**

### 12.6 — Body (optionnel)

Explique **pourquoi** + contexte. Wraper à 72 chars.

### 12.7 — Footer (optionnel)

```
Closes #123
Fixes #456
Relates-to #789
BREAKING CHANGE: API endpoint /v1/devis renamed to /v1/quotes
Co-authored-by: Claude <claude@anthropic.com>
```

### 12.8 — Automatisation

Outils :
- **commitizen** (`cz`) : CLI interactive pour générer commits
- **commitlint** : lint des commits (pre-commit hook)
- **husky** : hooks git

---

## 13. PULL REQUESTS

### 13.1 — Template

**Fichier** : `.github/pull_request_template.md`

```markdown
## 🎯 Objectif

Explique en 1-2 phrases le but de ce PR.

## 📋 Changements

- [ ] Change 1
- [ ] Change 2

## 🧪 Tests

- [ ] Unit tests ajoutés
- [ ] Integration tests ajoutés
- [ ] Testé manuellement (screenshot ou vidéo)

## 📸 Screenshots (si UI)

[Screenshots ou vidéos]

## 🔗 Liens

- Closes #ISSUE
- Related docs: docs/...

## ⚠️ Breaking changes

- [ ] Non
- [ ] Oui (détailler ci-dessous)

## 📝 Checklist

- [ ] Conventions CONVENTIONS.md respectées
- [ ] Tests ajoutés / mis à jour
- [ ] Documentation mise à jour si nécessaire
- [ ] Migration SQL ajoutée si schema change
- [ ] i18n traductions ajoutées si UI
```

### 13.2 — Bonnes pratiques

- **Petits PRs** : <400 lignes changées idéalement
- **Un PR = un concern** : pas de "fix + feature + refactor" dans le même PR
- **Self-review** avant demande review
- **Draft PR** si WIP : `[DRAFT]` prefix ou GitHub Draft mode
- **Réponse reviews** : répondre à chaque commentaire (fix ou explication)

### 13.3 — Titre PR

Format identique à commit :
```
feat(devis): add TVA 5.5% validation with RGE check
```

### 13.4 — Labels

- `feature` / `bug` / `docs` / `refactor`
- `mobile` / `backend` / `docs`
- `priority:high` / `priority:low`
- `breaking-change`

### 13.5 — Taille

Si PR >1000 lignes → **découper** en plusieurs PRs séquentiels :
- PR 1 : migration SQL
- PR 2 : services backend
- PR 3 : endpoints API
- PR 4 : UI mobile

---

## 14. CODE REVIEW

### 14.1 — Obligations reviewer

**Lire entièrement** le PR avant de commenter.

**Vérifier** :
- [ ] Conventions respectées
- [ ] Tests ajoutés
- [ ] Pas de régression évidente
- [ ] Logique métier correcte (métier BTP si applicable)
- [ ] Sécurité (pas de secrets, RLS correcte, validation inputs)
- [ ] Performance (pas de N+1 queries, pas de boucles inutiles)
- [ ] Documentation à jour

### 14.2 — Ton review

**Constructif, pas hostile** :

```
✅ Bon
"Que penses-tu d'extraire ce bloc dans une fonction ? Ça clarifierait le flow."

"Nit: petit typo ligne 42."

"Je ne comprends pas bien pourquoi on fait ça — peux-tu ajouter un commentaire expliquant le contexte ?"

"⚠️ Attention, ici on risque un N+1 query. On peut utiliser joinedload()."

❌ Mauvais
"C'est horrible."
"Why did you do that?"
"Fix this."
```

### 14.3 — Types de commentaires

- **nit:** (nitpick) — suggestion mineure, pas bloquante
- **q:** (question) — demande clarification
- **suggestion:** — amélioration possible
- **blocker:** — doit être fixé avant merge
- **praise:** — compliment bien mérité 🎉

### 14.4 — Approval criteria

Approve si :
- Tests passent
- Conventions respectées
- Logique correcte
- Pas de blocker non résolu

Block si :
- Sécurité compromise
- Logique métier fausse
- Régression potentielle
- Tests manquants sur chemin critique

### 14.5 — Vitesse

- **Review dans les 24h** (idéal 2-4h)
- **Response auteur dans les 24h** aussi

### 14.6 — Self-review

Avant de demander review :
1. Relire son diff comme si on était reviewer
2. Tester manuellement en local
3. Vérifier tests passent
4. Ajouter description complète au PR

---

## 15. LINTERS ET FORMATTERS

### 15.1 — Python

**Ruff** (remplacer black + isort + flake8) :

```toml
# pyproject.toml
[tool.ruff]
line-length = 88
target-version = "py312"

[tool.ruff.lint]
select = ["E", "F", "W", "I", "B", "C4", "UP", "SIM", "ARG", "ERA"]
ignore = ["E501"]  # line too long, handled by formatter

[tool.ruff.format]
quote-style = "double"
```

**mypy** (type checking strict) :

```toml
[tool.mypy]
python_version = "3.12"
strict = true
plugins = ["pydantic.mypy"]
```

**Commands** :
```bash
ruff check app/ tests/
ruff format app/ tests/
mypy app/
```

### 15.2 — TypeScript

**ESLint** :

```json
// .eslintrc.json
{
  "extends": [
    "expo",
    "plugin:@typescript-eslint/recommended-type-checked"
  ],
  "rules": {
    "import/order": ["error", {...}],
    "@typescript-eslint/no-explicit-any": "error",
    "@typescript-eslint/no-unused-vars": "error"
  }
}
```

**Prettier** :

```json
// .prettierrc
{
  "semi": true,
  "trailingComma": "all",
  "singleQuote": false,
  "printWidth": 100,
  "tabWidth": 2
}
```

**Commands** :
```bash
npx eslint . --fix
npx prettier --write .
npx tsc --noEmit
```

### 15.3 — SQL

**sqlfluff** (optional) :

```ini
[sqlfluff]
dialect = postgres
max_line_length = 100

[sqlfluff:rules:capitalisation.keywords]
capitalisation_policy = upper
```

### 15.4 — Markdown

**markdownlint** (léger) :

```json
// .markdownlint.json
{
  "MD013": false,        // ligne trop longue OK
  "MD033": false         // HTML inline OK
}
```

---

## 16. PRE-COMMIT HOOKS

### 16.1 — Configuration

**Fichier** : `.pre-commit-config.yaml`

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files
        args: ['--maxkb=500']
  
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.9
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format
  
  - repo: https://github.com/awslabs/git-secrets
    rev: master
    hooks:
      - id: git-secrets
  
  - repo: https://github.com/compilerla/conventional-pre-commit
    rev: v3.0.0
    hooks:
      - id: conventional-pre-commit
        stages: [commit-msg]
```

### 16.2 — Install

```bash
pip install pre-commit
pre-commit install
pre-commit install --hook-type commit-msg
```

### 16.3 — Hooks actifs

- **Trailing whitespace** : retire
- **End of file** : ajoute newline final
- **YAML validation**
- **Large files** : bloque >500KB (sauf lfs)
- **ruff lint + format** : auto-fix
- **git-secrets** : bloque secrets AWS/GCP/API keys
- **Conventional commit** : valide format commit

### 16.4 — Bypass

```bash
# En urgence uniquement
git commit -m "..." --no-verify
```

---

## 17. CI/CD CHECKS

Voir `docs/DEPLOY.md` §8 + `docs/TESTS.md` §21.

### 17.1 — Sur chaque PR

- [ ] Lint Python (ruff)
- [ ] Lint TypeScript (ESLint)
- [ ] Format check (ruff format, prettier)
- [ ] Type check (mypy, tsc)
- [ ] Tests backend (pytest)
- [ ] Tests mobile (jest)
- [ ] Coverage report (Codecov)
- [ ] Validation i18n (script custom)
- [ ] Secrets scan
- [ ] Dependency audit (npm audit, pip-audit)

### 17.2 — Blocking vs warning

**Blocking** (merge bloqué) :
- Tests fail
- Lint errors
- Type errors
- Secrets detected
- Coverage drop >10%

**Warning** (visible mais non bloquant) :
- Coverage drop 5-10%
- Outdated deps
- Size PR >1000 lignes

### 17.3 — Deploy

- **Merge develop** → auto-deploy staging (Railway staging + Vercel preview)
- **Merge main** → auto-deploy prod (Railway prod + Vercel prod + EAS Build)
- Voir `docs/DEPLOY.md`.

---

## 18. REFACTORING

### 18.1 — Quand refactorer

- Code dupliqué 3+ fois
- Fonction >50 lignes (split en plus petites)
- Fichier >500 lignes (split en modules)
- Nommage obscur
- Mélange de concerns

### 18.2 — PR dédié

Refactoring **dans un PR dédié** (pas mélangé à feature). Review plus facile.

```
Commit message:
refactor(pricing): extract PricingContext from get_price args
```

### 18.3 — Pas de refactor sans tests

**Avant refactor** : tests qui couvrent le comportement existant.
**Après refactor** : tests verts → garantie pas de régression.

### 18.4 — Boy scout rule

"Leave the code better than you found it" — petits fix opportunistes quand on touche un fichier.

Mais : pas dans le même commit qu'une feature (séparer `chore` et `feat`).

---

## 19. VERSIONING

### 19.1 — Semantic versioning

**MAJOR.MINOR.PATCH** (SemVer) :
- **MAJOR** : breaking changes (API incompatible)
- **MINOR** : nouvelles features (compat arrière)
- **PATCH** : bug fixes

### 19.2 — Version app

Dans `mobile/app.json` :
```json
{
  "version": "1.0.0",
  "ios": { "buildNumber": "1" },
  "android": { "versionCode": 1 }
}
```

### 19.3 — Version backend

Dans `backend/pyproject.toml` :
```toml
[project]
version = "1.0.0"
```

### 19.4 — Tags git

```bash
git tag v1.0.0
git push origin v1.0.0
```

Auto-génère **GitHub Release** avec changelog.

### 19.5 — Changelog

**Fichier** : `docs/CHANGELOG.md` (déjà existe).

Format **Keep a Changelog** (https://keepachangelog.com/) :

```markdown
# Changelog

## [1.1.0] - 2026-05-15
### Added
- Agent RH avec conventions collectives BTP
- Support 6 langues (FR, EN, TR, PT, AR, ES)

### Changed
- Migration TVA multi-taux améliorée

### Fixed
- Bug sync offline après 24h
```

---

## 20. DOCUMENTATION OBLIGATOIRE

### 20.1 — Quand documenter

- **Nouvelle feature** → doc dans `docs/` + mention `CHANGELOG.md`
- **Nouveau endpoint API** → doc dans `docs/API.md`
- **Nouvelle migration SQL** → doc dans `docs/MIGRATIONS.md`
- **Nouveau code d'erreur** → doc dans `docs/ERRORS.md`
- **Nouveau agent** → doc dans `docs/AGENTS.md`
- **Nouveau prompt** → doc dans `docs/PROMPTS.md`
- **Nouveau modèle BDD** → doc dans `docs/MIGRATIONS.md`
- **Changement architecture** → update `docs/ARCH.md` + `docs/DECISIONS-LOG.md`

### 20.2 — Format docs

Suivre le format de `docs/` existant :
- Frontmatter YAML avec metadata
- Sommaire numéroté
- Sections structurées
- Exemples de code
- Références croisées

### 20.3 — README racine

`README.md` racine : overview rapide (3-5 min lecture).

Détails → `docs/`.

### 20.4 — Doc d'un service / agent

Chaque nouveau service / agent doit avoir :
- Docstring de classe (purpose)
- Docstring méthodes publiques
- Mention dans `docs/AGENTS.md` (si agent) ou `docs/ARCH.md` §4 (si service)
- Tests unitaires + intégration

---

## 21. ANTI-PATTERNS

### 21.1 — À NE JAMAIS FAIRE

1. ❌ **Force push sur main/develop** — perte historique
2. ❌ **Commit directement sur main** — bypass review
3. ❌ **Merge sans CI green** — risque régression prod
4. ❌ **PR >2000 lignes** — review impossible
5. ❌ **Giant commits** ("update everything")
6. ❌ **Secret en dur dans code** — vol potentiel
7. ❌ **`any` TypeScript** sauf justification explicite
8. ❌ **Fonctions >100 lignes** — splitter
9. ❌ **Fichiers >800 lignes** — splitter
10. ❌ **Magic numbers** — utiliser constantes nommées
11. ❌ **Hardcoded strings i18n** — tout passe par `t()`
12. ❌ **console.log en prod** — utiliser logger structuré
13. ❌ **Comments obsolètes** — maintenir ou supprimer
14. ❌ **TODO sans ticket** — `TODO(fabrice):` + décrire ou créer issue
15. ❌ **Copy-paste > 20 lignes** — factoriser

### 21.2 — À FAIRE

1. ✅ Petits commits fréquents
2. ✅ Branches courtes (<1 semaine)
3. ✅ Self-review avant demande PR
4. ✅ CI verte avant merge
5. ✅ Tests co-localisés
6. ✅ Conventions respectées
7. ✅ Docs à jour
8. ✅ Type hints / strict TS
9. ✅ Logging structuré
10. ✅ Erreurs custom (StructorAIError)

---

## 22. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `CLAUDE.md` §Conventions | Version courte pour Claude Code |
| `docs/ARCH.md` §Stack | Stack technique |
| `docs/TESTS.md` | Stratégie tests (convention pytest/jest) |
| `docs/DEPLOY.md` §8 | CI/CD workflows |
| `docs/ERRORS.md` | Pattern StructorAIError |
| `docs/MIGRATIONS.md` | Convention migrations SQL |
| `docs/API.md` | Convention endpoints REST |
| `docs/AGENTS.md` | Pattern BaseAgent |
| `docs/PROMPTS.md` §2 | Pattern prompts séparés (Ouroboros) |
| `docs/MEMORY.md` | Convention namespacing Mem0 |
| `docs/I18N.md` | Convention traductions |
| `docs/OFFLINE.md` | Pattern Repository mobile |
| `docs/CHANGELOG.md` | Format Keep a Changelog |
| `docs/DECISIONS-LOG.md` | Décisions architecture tracées |
| `BUILD_PLAN.md` | Structure projet complète |
| `SECURITE.md` | Conventions sécurité |

### 22.1 — Références externes

- **Conventional Commits** : https://www.conventionalcommits.org/
- **Semantic Versioning** : https://semver.org/
- **Keep a Changelog** : https://keepachangelog.com/
- **PEP 8** (Python style) : https://peps.python.org/pep-0008/
- **Airbnb JS Style Guide** : https://github.com/airbnb/javascript
- **Ruff docs** : https://docs.astral.sh/ruff/
- **Prettier docs** : https://prettier.io/
- **ESLint docs** : https://eslint.org/
- **pre-commit framework** : https://pre-commit.com/

---

> **Ce fichier est la source de vérité des conventions de code.**
> **Règle d'or n°1** : cohérence > préférence personnelle.
> **Règle d'or n°2** : commits petits et fréquents, PRs focalisés.
> **Règle d'or n°3** : un bug = un test de régression.
> **Règle d'or n°4** : docs à jour = obligatoire, pas optionnel.
> **Règle d'or n°5** : sécurité systématique (secrets, RLS, validation inputs).
