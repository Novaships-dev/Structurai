---
name: OFFLINE
description: Stratégie complète mode hors-ligne de STRUCTORAI. Différenciation critique vs concurrents (Obat, Batappli, Sage = 0 offline mobile). Contexte chantier réel (sous-sols, campagne 3G faible). Expo SQLite local côté mobile + queue de sync + POST /v1/sync/batch à reconnexion. Stratégie conflits (last-write-wins timestamp + exceptions métier). Opérations disponibles offline (création/modification devis/factures/tickets/photos/chantiers/clients). Opérations NON disponibles offline (chat LLM, pricing dynamique, envoi client). UX dégradée mais fonctionnelle. Storage encrypted SQLCipher. Schéma SQL local miroir partiel de Supabase.
type: technical-offline-sync
scope: mobile-sqlite, sync-queue, conflict-resolution, reconnection
priority: critical
date: 2026-04-18
version: 1.0
feature: F91 (Mode hors-ligne SQLite) + F92 (Synchro multi-appareils)
mobile_db: Expo SQLite + SQLCipher (chiffré AES-256)
sync_strategy: Last-write-wins (LWW) + exceptions métier documentées
reconnect_batch_endpoint: POST /v1/sync/batch
---

# docs/OFFLINE.md — Mode hors-ligne STRUCTORAI

> **Ce fichier est la spec du mode hors-ligne.**
> **Différenciation concurrentielle CRITIQUE** : Obat, Batappli, Sage, EBP = **ZÉRO offline**. Seul Tolteck a un offline mobile. STRUCTORAI offre un offline COMPLET.
> Reference : `docs/ARCH.md` §10.8 (scénario sync) + `docs/FEATURES_COMPLETE.md` F91-F92.
> Chantier artisan = réseau instable (sous-sols immeubles, campagne, dépendances mauvaise 4G).

---

## 0. SOMMAIRE

1. [Principes fondamentaux](#1-principes-fondamentaux)
2. [Contexte chantier — réalité du terrain](#2-contexte-chantier--réalité-du-terrain)
3. [Architecture offline](#3-architecture-offline)
4. [Stockage local — Expo SQLite](#4-stockage-local--expo-sqlite)
5. [Schéma DB local (miroir Supabase)](#5-schéma-db-local-miroir-supabase)
6. [Sync queue](#6-sync-queue)
7. [Opérations disponibles OFFLINE](#7-opérations-disponibles-offline)
8. [Opérations NON disponibles OFFLINE](#8-opérations-non-disponibles-offline)
9. [Détection réseau](#9-détection-réseau)
10. [Endpoint POST /v1/sync/batch](#10-endpoint-post-v1syncbatch)
11. [Stratégie conflits](#11-stratégie-conflits)
12. [UX en mode offline](#12-ux-en-mode-offline)
13. [Chiffrement local SQLCipher](#13-chiffrement-local-sqlcipher)
14. [Sync multi-appareils (F92)](#14-sync-multi-appareils-f92)
15. [Limitations techniques](#15-limitations-techniques)
16. [Gestion des médias (photos, audio)](#16-gestion-des-médias-photos-audio)
17. [PWA iOS — limitations offline](#17-pwa-ios--limitations-offline)
18. [Monitoring et métriques](#18-monitoring-et-métriques)
19. [Tests](#19-tests)
20. [Troubleshooting](#20-troubleshooting)
21. [Références croisées](#21-références-croisées)

---

## 1. PRINCIPES FONDAMENTAUX

### 1.1 — 10 règles immuables

1. **L'app DOIT fonctionner sans réseau** pour les opérations métier core (devis, factures, tickets, photos)
2. **Aucune perte de données** même en cas de crash offline
3. **Sync automatique** dès le réseau revient (pas d'action user requise)
4. **UX transparente** — l'artisan ne doit pas "penser" à l'offline
5. **Indication visuelle** claire mode online/offline
6. **Chiffrement local** (SQLCipher AES-256 obligatoire)
7. **Conflicts resolution** déterministe (last-write-wins par défaut)
8. **Sync idempotente** — réessai OK même si partiel
9. **Batch efficace** — grouper plusieurs events en 1 POST
10. **Dégradation explicite** pour features non offline (chat LLM, pricing, envoi)

### 1.2 — Pourquoi c'est stratégique

**Citation STRATEGIE-COMPETITIVE.md** :
> "Nous on marche même au fond du chantier"

**Vraie différenciation** :
- Obat/Batappli/Sage/EBP : 0 offline mobile → l'artisan doit retaper ses devis le soir
- Tolteck : offline partiel
- **STRUCTORAI : offline complet + sync automatique**

### 1.3 — Ce qui doit fonctionner offline

**Core business** (non négociable) :
- Créer/modifier/consulter devis
- Créer/modifier/consulter factures
- Scanner tickets de caisse (photo stockée, OCR batch à reconnexion)
- Prendre photos chantier
- Créer/modifier fiches client
- Créer/modifier chantiers
- Pointer heures (timer chantier)
- Valider tâches "À faire"

**Nice to have** :
- Consulter historique récent
- Consulter mémoire Mem0 (cache local)
- Consulter prix perso (cache local)

### 1.4 — Ce qui nécessite le réseau

- Chat vocal avec cerveau IA (LLM Claude)
- Génération devis par la voix (STT Whisper + LLM)
- Pricing dynamique (lookup Mem0 cloud + benchmark + web)
- Envoi devis/facture au client (email/WhatsApp/SMS)
- Signature électronique Yousign
- Paiements Stripe
- Agent Téléphone (V2)
- Agent Coach Business (analyses)
- Mise à jour cerveau IA

---

## 2. CONTEXTE CHANTIER — RÉALITÉ DU TERRAIN

### 2.1 — Où l'artisan perd la connexion

- **Sous-sols d'immeubles** (caves, parkings)
- **Cages d'ascenseur** (béton armé)
- **Campagne reculée** (3G faible ou absent)
- **Zones montagnardes** (vallées, tunnels)
- **Grandes surfaces commerciales** (réseau saturé)
- **Véhicule** (entre 2 chantiers, tunnels)
- **Forfait épuisé** en fin de mois

### 2.2 — Données réelles

- **~30% du temps chantier** : connexion instable ou absente
- **~10% des devis** créés sans réseau
- **~5% des artisans** sur forfait limité (mode data restreint)

### 2.3 — Impact business sans offline

Sans offline :
- L'artisan ressaisit le soir (perte 30-60 min/jour)
- Oublis fréquents (devis non enregistrés, tickets perdus)
- Frustration → churn élevé

Avec offline :
- L'artisan travaille normalement
- Sync auto à reconnexion
- Zéro perte

---

## 3. ARCHITECTURE OFFLINE

### 3.1 — Vue d'ensemble

```
┌───────────────────────────────────────────────────┐
│  MOBILE APP (React Native Expo)                    │
│                                                    │
│  ┌─────────────────────────────────────────────┐   │
│  │  UI Layer                                    │   │
│  │  (écrans : devis, factures, chantiers...)    │   │
│  └────────────────┬────────────────────────────┘   │
│                   │                                 │
│                   ▼                                 │
│  ┌─────────────────────────────────────────────┐   │
│  │  Local Repository Layer                      │   │
│  │  (lit/écrit dans SQLite local)               │   │
│  └────────────────┬────────────────────────────┘   │
│                   │                                 │
│        ┌──────────┴──────────┐                     │
│        ▼                     ▼                     │
│  ┌──────────┐          ┌──────────┐                │
│  │ SQLite   │          │ Sync     │                │
│  │ (data)   │          │ Queue    │                │
│  └──────────┘          └────┬─────┘                │
│                             │                       │
└─────────────────────────────┼───────────────────────┘
                              │ Quand réseau
                              ▼
┌───────────────────────────────────────────────────┐
│  BACKEND (FastAPI)                                 │
│  POST /v1/sync/batch                               │
│  → valide, écrit Supabase, retourne acks           │
└───────────────────────────────────────────────────┘
```

### 3.2 — Pattern repository local

Tous les accès aux données passent par un **Local Repository** qui :
1. Lit dans SQLite local pour GET
2. Écrit dans SQLite local pour PUT/POST/DELETE
3. Enqueue l'opération dans la sync queue pour replay distant
4. Retourne immédiatement (pas d'attente réseau)

L'UI ne connaît **que** le local repository — elle ne fait **jamais** d'appel HTTP direct.

### 3.3 — Strategy "local-first"

Principe : **le local est la vérité à un instant T côté mobile**.

Quand sync OK :
- Récupérer delta distant
- Merger avec local (conflicts résolution)
- Mettre à jour UI

Quand sync KO :
- Continuer en local
- Re-tenter plus tard

---

## 4. STOCKAGE LOCAL — EXPO SQLITE

### 4.1 — Package

**`expo-sqlite`** (intégré Expo SDK).

Supporte :
- SQLite 3
- Requêtes SQL standard
- Transactions
- WAL mode (performance)
- **Extension SQLCipher** pour chiffrement

### 4.2 — Init

```typescript
// mobile/lib/db.ts

import * as SQLite from 'expo-sqlite';

let db: SQLite.SQLiteDatabase | null = null;

export async function getDatabase(): Promise<SQLite.SQLiteDatabase> {
  if (!db) {
    db = await SQLite.openDatabaseAsync('structorai.db', {
      useNewConnection: false,
      enableChangeListener: true,
    });
    
    // Activer WAL pour perf
    await db.execAsync('PRAGMA journal_mode = WAL;');
    
    // Clé de chiffrement SQLCipher (voir §13)
    const encryptionKey = await getEncryptionKey();
    await db.execAsync(`PRAGMA key = "${encryptionKey}";`);
    
    // Schema
    await applyMigrations(db);
  }
  return db;
}
```

### 4.3 — Limites Expo SQLite

- Taille DB : **~50 GB max** (largement suffisant pour artisan)
- Transaction : limitée par mémoire (batch <1000 rows préféré)
- Full-text search : supporté via FTS5
- JSON : supporté via fonctions `json_extract()`
- Pas de pgvector (pas de RAG local)

### 4.4 — Performance

- Read : ~1000 rows/s
- Write : ~100 rows/s (avec transactions : ~5000/s)
- Taille devis avec 20 lignes : ~5 KB en DB

Pour 1000 devis stockés localement = ~5 MB → négligeable.

---

## 5. SCHÉMA DB LOCAL (MIROIR SUPABASE)

### 5.1 — Principe

Le schéma local est un **miroir partiel** de Supabase (voir `docs/MIGRATIONS.md`).

**Miroir** :
- Mêmes noms de tables
- Mêmes noms de colonnes
- Mêmes types (adaptés SQLite)

**Partiel** :
- Seulement les tables nécessaires offline
- Seulement les rows de l'artisan courant (pas toutes orgs)
- Seulement rows récents (dernière 1 an max)

### 5.2 — Tables mirrored

```sql
-- Tables métier
CREATE TABLE IF NOT EXISTS clients (
    id TEXT PRIMARY KEY,
    organization_id TEXT NOT NULL,
    nom TEXT,
    prenom TEXT,
    email TEXT,
    telephone TEXT,
    adresse_json TEXT,  -- JSON stringified
    -- ...
    created_at TEXT NOT NULL,
    updated_at TEXT NOT NULL,
    deleted_at TEXT,
    sync_status TEXT DEFAULT 'synced'  -- 'pending', 'synced', 'conflict'
);

CREATE TABLE IF NOT EXISTS chantiers (
    id TEXT PRIMARY KEY,
    organization_id TEXT NOT NULL,
    client_id TEXT REFERENCES clients(id),
    nom TEXT,
    status TEXT,
    -- ...
    sync_status TEXT DEFAULT 'synced'
);

CREATE TABLE IF NOT EXISTS devis (
    id TEXT PRIMARY KEY,
    organization_id TEXT NOT NULL,
    client_id TEXT,
    chantier_id TEXT,
    numero TEXT,
    status TEXT,
    total_ht INTEGER,
    total_tva INTEGER,
    total_ttc INTEGER,
    tva_breakdown_json TEXT,
    -- ...
    sync_status TEXT DEFAULT 'synced'
);

CREATE TABLE IF NOT EXISTS devis_lines (
    id TEXT PRIMARY KEY,
    devis_id TEXT REFERENCES devis(id),
    position INTEGER,
    designation TEXT,
    quantite REAL,
    unite TEXT,
    prix_unitaire_ht INTEGER,
    tva_rate REAL,
    total_ht INTEGER,
    -- ...
    sync_status TEXT DEFAULT 'synced'
);

CREATE TABLE IF NOT EXISTS factures ( ... );
CREATE TABLE IF NOT EXISTS tickets ( ... );        -- tickets caisse OCR
CREATE TABLE IF NOT EXISTS depenses ( ... );
CREATE TABLE IF NOT EXISTS time_entries ( ... );   -- pointage heures
CREATE TABLE IF NOT EXISTS photos_chantier ( ... );

-- Cache lecture
CREATE TABLE IF NOT EXISTS cached_prices (       -- Mem0 prix perso cache
    id TEXT PRIMARY KEY,
    poste TEXT,
    metier TEXT,
    prix_min INTEGER,
    prix_max INTEGER,
    prix_median INTEGER,
    last_fetched_at TEXT
);

CREATE TABLE IF NOT EXISTS cached_user_profile (
    organization_id TEXT PRIMARY KEY,
    nom TEXT,
    siret TEXT,
    decennale_json TEXT,
    rge_json TEXT,
    last_fetched_at TEXT
);
```

### 5.3 — Queue de sync

```sql
CREATE TABLE IF NOT EXISTS sync_queue (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    operation TEXT NOT NULL,      -- 'create', 'update', 'delete'
    resource_type TEXT NOT NULL,  -- 'devis', 'facture', 'ticket', 'client', ...
    resource_id TEXT NOT NULL,
    payload_json TEXT NOT NULL,   -- full resource state
    created_at TEXT NOT NULL,
    attempts INTEGER DEFAULT 0,
    last_error TEXT,
    status TEXT DEFAULT 'pending'  -- 'pending', 'in_flight', 'synced', 'failed'
);

CREATE INDEX idx_sync_queue_status ON sync_queue(status, created_at);
```

### 5.4 — Index

Index critiques pour performance offline :

```sql
CREATE INDEX idx_devis_status ON devis(status, updated_at DESC);
CREATE INDEX idx_devis_sync ON devis(sync_status);
CREATE INDEX idx_factures_echeance ON factures(date_echeance);
CREATE INDEX idx_clients_search ON clients(nom, prenom);
CREATE INDEX idx_photos_chantier ON photos_chantier(chantier_id);
```

### 5.5 — Migrations locales

Table `local_migrations` pour tracker le schéma :

```sql
CREATE TABLE IF NOT EXISTS local_migrations (
    id INTEGER PRIMARY KEY,
    name TEXT UNIQUE,
    applied_at TEXT DEFAULT CURRENT_TIMESTAMP
);
```

Script `applyMigrations()` au lancement app :
1. Check which migrations already applied
2. Apply new ones sequentially
3. Always forward-only (pas de rollback côté mobile)

---

## 6. SYNC QUEUE

### 6.1 — Fonctionnement

Chaque opération d'écriture crée :
1. Un update dans la table concernée (ex: `devis`)
2. Un row dans `sync_queue` avec l'opération à replayer distant

### 6.2 — Pattern wrapper

```typescript
// mobile/lib/repository/devisRepo.ts

export async function createDevis(data: DevisInput): Promise<Devis> {
  const db = await getDatabase();
  const devis: Devis = {
    id: generateUUID(),  // Client-side UUID
    ...data,
    created_at: new Date().toISOString(),
    updated_at: new Date().toISOString(),
    sync_status: 'pending',
  };
  
  await db.runAsync(
    `INSERT INTO devis (id, organization_id, client_id, status, total_ht, ...) 
     VALUES (?, ?, ?, ?, ?, ...)`,
    [devis.id, devis.organization_id, devis.client_id, devis.status, devis.total_ht, ...]
  );
  
  // Enqueue sync
  await enqueueSync({
    operation: 'create',
    resource_type: 'devis',
    resource_id: devis.id,
    payload: devis,
  });
  
  return devis;
}

export async function enqueueSync(op: SyncOperation): Promise<void> {
  const db = await getDatabase();
  await db.runAsync(
    `INSERT INTO sync_queue (operation, resource_type, resource_id, payload_json, created_at) 
     VALUES (?, ?, ?, ?, ?)`,
    [op.operation, op.resource_type, op.resource_id, JSON.stringify(op.payload), new Date().toISOString()]
  );
  
  // Trigger sync si online
  if (await isOnline()) {
    triggerSync();  // async, fire-and-forget
  }
}
```

### 6.3 — Sync process

```typescript
// mobile/lib/sync.ts

let syncInProgress = false;

export async function triggerSync(): Promise<void> {
  if (syncInProgress) return;
  syncInProgress = true;
  
  try {
    while (true) {
      const batch = await getNextBatch(50);  // Max 50 ops par batch
      if (batch.length === 0) break;
      
      // Mark as in_flight
      await markBatchInFlight(batch);
      
      try {
        const response = await api.post('/v1/sync/batch', {
          operations: batch.map(op => ({
            id: op.id,
            operation: op.operation,
            resource_type: op.resource_type,
            resource_id: op.resource_id,
            payload: JSON.parse(op.payload_json),
            client_timestamp: op.created_at,
          })),
        });
        
        // Process response
        await processBatchResponse(response.data);
      } catch (err) {
        // Rollback status
        await markBatchFailed(batch, err.message);
        break;  // Retry plus tard
      }
    }
  } finally {
    syncInProgress = false;
  }
}
```

### 6.4 — Triggers de sync

La sync est déclenchée :
- **Immediate** : après chaque opération si online (fire-and-forget)
- **Network regained** : event NetInfo `onConnectionChange` → full sync
- **Periodic** : toutes les 5 min si online (backup si immédiate rate)
- **Manual** : bouton "Synchroniser maintenant" dans settings
- **Startup** : au lancement app si online

### 6.5 — Ordre des operations

Respecter **l'ordre de création** (FK constraints) :
1. `clients` avant `chantiers`
2. `chantiers` avant `devis` / `factures` / `photos`
3. `devis` avant `devis_lines` / `factures`

La queue FIFO respecte naturellement cet ordre si les ops sont créées dans le bon ordre côté mobile.

### 6.6 — Retry strategy

| Attempt | Délai |
|---|---|
| 1 | 5s |
| 2 | 30s |
| 3 | 2 min |
| 4 | 10 min |
| 5 | 1h |
| 6+ | marker failed permanent |

Après 6 échecs → marker `status='failed'` → alerte user + report via support.

---

## 7. OPÉRATIONS DISPONIBLES OFFLINE

### 7.1 — Liste complète

**CRUD complet offline** :

| Opération | Offline ? | Notes |
|---|---|---|
| Créer devis | ✅ | Sans LLM IA (manuel), UUID client-side |
| Modifier devis | ✅ | |
| Consulter devis | ✅ | |
| Créer facture | ✅ | À partir d'un devis signé |
| Modifier facture | ✅ | |
| Consulter facture | ✅ | |
| Scanner ticket | ✅ | Photo stockée, OCR différé |
| Consulter tickets | ✅ | |
| Ajouter dépense | ✅ | |
| Prendre photo chantier | ✅ | Stockée local, upload différé |
| Consulter photos | ✅ | Récentes uniquement |
| Créer client | ✅ | |
| Modifier client | ✅ | |
| Consulter clients | ✅ | |
| Créer chantier | ✅ | |
| Modifier chantier | ✅ | |
| Consulter chantiers | ✅ | |
| Pointer heures (timer) | ✅ | |
| Consulter planning | ✅ | Récent uniquement |
| Valider tâche "À faire" | ✅ | Action syncable |
| Consulter avis Google | ✅ | Cache récent |

### 7.2 — Dégradations acceptables

**Devis offline** :
- Prix manuels (pas de lookup Mem0 cloud, juste cache local)
- Pas de suggestion IA
- Pas de génération vocal → clavier required

**Ticket OCR offline** :
- Photo stockée
- OCR fait quand reconnecté (Claude Vision)

**Photo chantier offline** :
- Stockée local (JPEG compressé)
- Upload Supabase Storage quand reconnecté
- Analyse IA Vision différée

---

## 8. OPÉRATIONS NON DISPONIBLES OFFLINE

### 8.1 — Liste

| Opération | Pourquoi impossible offline | Fallback UI |
|---|---|---|
| **Chat vocal IA** | LLM Claude cloud | Message "Pas de réseau, essaie en texte" |
| **Chat texte IA** | LLM Claude cloud | Message "Reconnexion nécessaire" |
| **Génération devis vocal** | STT + LLM + TTS | "Utilise le formulaire manuel" |
| **Pricing dynamique** | Mem0 cloud + benchmark | Prix depuis cache local uniquement |
| **Envoi devis client** | SMTP Brevo / WhatsApp Meta | "Envoi mis en attente, sera envoyé à la reconnexion" |
| **Envoi facture client** | Idem + PA Factur-X | Idem |
| **Signature Yousign** | API Yousign | "Reconnexion nécessaire" |
| **Paiement Stripe** | Stripe API | "Reconnexion nécessaire" |
| **Agent Téléphone V2** | Twilio Voice | Désactivé offline |
| **Coach Business analyse** | LLM + benchmarks cloud | Cache dernier rapport |
| **Recherche Mem0** | Mem0 cloud | Cache local |
| **Recherche knowledge base** | pgvector cloud | Désactivé offline |
| **Liste complète historique** | Paginated fetch cloud | Cache 50 derniers items |
| **Mise à jour cerveau IA** | Claude API | Désactivé offline |

### 8.2 — UX dégradée

Quand une feature non-offline est tentée offline :
- **Toast informatif** : "🔌 Pas de réseau — cette action nécessite Internet"
- **Action enqueue** si possible (ex: envoi devis → enqueue, envoyé à reconnexion)
- **Pas de bouton grisé** agressif, mais icône discrète 📴 à côté du bouton

### 8.3 — Actions "enqueue later"

Certaines actions peuvent être mise en attente :
- Envoi devis client → enqueue (marqué "En attente envoi")
- Envoi facture client → enqueue
- Demande signature → enqueue

L'artisan voit "📬 3 envois en attente" dans le dashboard. Quand reconnexion → auto-envoi.

---

## 9. DÉTECTION RÉSEAU

### 9.1 — Package

**`@react-native-community/netinfo`** :

```typescript
import NetInfo from '@react-native-community/netinfo';

// Subscribe
const unsubscribe = NetInfo.addEventListener(state => {
  const isConnected = state.isConnected && state.isInternetReachable;
  setNetworkState(isConnected ? 'online' : 'offline');
  
  if (isConnected) {
    // Reconnexion détectée
    triggerSync();
  }
});
```

### 9.2 — Distinguer "connecté" vs "Internet accessible"

- `isConnected` : Wi-Fi ou cellular actif (peut être hotspot sans Internet)
- `isInternetReachable` : vraie connectivité vérifiée

STRUCTORAI utilise `isConnected && isInternetReachable` pour éviter faux positifs.

### 9.3 — Ping backend

Pour cas edge (réseau OK mais backend down) :

```typescript
async function isBackendReachable(): Promise<boolean> {
  try {
    const response = await fetch(`${API_URL}/health`, { 
      method: 'GET',
      timeout: 5000,
    });
    return response.ok;
  } catch {
    return false;
  }
}
```

Appelé avant toute sync batch.

### 9.4 — Context React

```typescript
// mobile/contexts/NetworkContext.tsx

const NetworkContext = createContext<NetworkState>({
  isOnline: true,
  lastOnlineAt: new Date(),
  pendingSyncCount: 0,
});

export function NetworkProvider({ children }) {
  const [isOnline, setIsOnline] = useState(true);
  const [pendingSyncCount, setPendingSyncCount] = useState(0);
  
  useEffect(() => {
    const unsub = NetInfo.addEventListener(state => {
      const online = state.isConnected && state.isInternetReachable;
      setIsOnline(online);
      if (online) triggerSync();
    });
    return () => unsub();
  }, []);
  
  // Update pending count périodiquement
  useEffect(() => {
    const interval = setInterval(async () => {
      const count = await getPendingSyncCount();
      setPendingSyncCount(count);
    }, 2000);
    return () => clearInterval(interval);
  }, []);
  
  return <NetworkContext.Provider value={{ isOnline, pendingSyncCount }}>
    {children}
  </NetworkContext.Provider>;
}
```

---

## 10. ENDPOINT POST /V1/SYNC/BATCH

### 10.1 — Request

```http
POST /v1/sync/batch
Authorization: Bearer <jwt>
Content-Type: application/json

{
  "operations": [
    {
      "id": 42,                                    // Mobile sync_queue.id
      "operation": "create",
      "resource_type": "devis",
      "resource_id": "dev_abc123",
      "payload": { ...full devis state... },
      "client_timestamp": "2026-04-18T14:32:05Z"
    },
    {
      "id": 43,
      "operation": "update",
      "resource_type": "devis",
      "resource_id": "dev_abc123",
      "payload": { ...updated state... },
      "client_timestamp": "2026-04-18T14:35:12Z"
    },
    {
      "id": 44,
      "operation": "create",
      "resource_type": "ticket",
      "resource_id": "tic_xyz789",
      "payload": { ... },
      "client_timestamp": "2026-04-18T14:40:00Z"
    }
  ]
}
```

### 10.2 — Response

```json
{
  "results": [
    { "id": 42, "status": "success", "resource_id": "dev_abc123" },
    { "id": 43, "status": "success", "resource_id": "dev_abc123" },
    { "id": 44, "status": "conflict", "resource_id": "tic_xyz789", "reason": "already_exists", "remote_state": {...} }
  ],
  "remote_deltas": [
    // Changements remote depuis last sync (pull-side)
    { "resource_type": "client", "operation": "update", "data": {...} }
  ],
  "server_timestamp": "2026-04-18T14:45:00Z"
}
```

### 10.3 — Traitement backend

```python
# backend/app/api/v1/sync.py

@router.post("/sync/batch")
async def sync_batch(
    request: SyncBatchRequest,
    user: User = Depends(get_current_user),
):
    results = []
    
    for op in request.operations:
        try:
            # Valide payload
            validated = validate_payload(op.resource_type, op.payload)
            
            # Vérifier conflit
            existing = await get_resource(op.resource_type, op.resource_id)
            if op.operation == 'create' and existing:
                results.append({
                    'id': op.id,
                    'status': 'conflict',
                    'reason': 'already_exists',
                    'remote_state': existing,
                })
                continue
            
            # Appliquer
            if op.operation == 'create':
                await create_resource(op.resource_type, validated, user.organization_id)
            elif op.operation == 'update':
                # Last-write-wins : check timestamp
                if existing and existing.updated_at > op.client_timestamp:
                    # Conflit : serveur plus récent
                    results.append({
                        'id': op.id,
                        'status': 'conflict',
                        'reason': 'server_newer',
                        'remote_state': existing,
                    })
                    continue
                await update_resource(op.resource_type, validated)
            elif op.operation == 'delete':
                await soft_delete_resource(op.resource_type, op.resource_id)
            
            results.append({
                'id': op.id,
                'status': 'success',
                'resource_id': op.resource_id,
            })
        
        except Exception as e:
            results.append({
                'id': op.id,
                'status': 'failed',
                'error': str(e),
            })
    
    # Récupère deltas remote (changements serveur depuis last sync client)
    remote_deltas = await get_remote_deltas(user, since=request.last_sync_timestamp)
    
    return {
        'results': results,
        'remote_deltas': remote_deltas,
        'server_timestamp': datetime.utcnow().isoformat(),
    }
```

### 10.4 — Rate limit

- 1 batch / 5s par user
- Max 50 ops / batch
- Max 500 ops / min
- Au-delà → 429 avec retry-after

### 10.5 — Validation

Chaque payload validé avec Pydantic :
- Schema exact
- Contraintes métier (TVA valide, montants positifs, etc.)
- FK cohérence (client_id existe ?)

Si validation fail → `status: "validation_error"` + détails.

### 10.6 — Idempotence

Key idempotence : `(organization_id, resource_id, operation)`.

Si le même op est envoyé 2× :
- 1ère fois : appliqué
- 2ème fois : détecté, return `status: "already_processed"` (non erreur)

Permet safe retry côté client.

---

## 11. STRATÉGIE CONFLITS

### 11.1 — Principe par défaut : LWW (Last-Write-Wins)

**Timestamp-based** : la version avec `updated_at` le plus récent gagne.

**Source timestamp** : horloge **serveur** (faisant foi) ou **client** (si offline).

### 11.2 — Cas possibles

**Cas 1 — CREATE pas de conflit** :
- Mobile crée `devis_abc` offline à 14h32
- Sync envoie → serveur crée à 14h50
- Pas de conflit → OK

**Cas 2 — CREATE avec même ID** (rare) :
- UUID collision (1 chance sur 2^122 = négligeable)
- Si détecté → serveur retourne conflict, mobile regénère UUID

**Cas 3 — UPDATE concurrent** :
- Mobile A modifie devis à 14h32 (offline)
- Mobile B modifie même devis à 14h45 (online, sync direct)
- Mobile A reconnecte à 15h00, envoie update 14h32
- Serveur détecte `server_updated_at (14h45) > client_timestamp (14h32)`
- **Conflit server_newer** : serveur retourne état actuel, mobile A doit merger

**Cas 4 — DELETE vs UPDATE** :
- Mobile A supprime devis à 14h32
- Mobile B modifie devis à 14h45
- Last-write-wins : le plus récent (B modifie) gagne → devis restauré

### 11.3 — Exceptions métier

Pour certains cas, LWW **n'est pas suffisant** :

**Devis envoyé** → pas de modification possible :
- Si mobile tente modif sur devis `sent` → conflit métier (`DEVIS_ALREADY_SENT`)
- Résolution : l'artisan doit dupliquer le devis pour faire un nouveau

**Facture payée** → pas de modification :
- Conflit métier `FACTURE_ALREADY_PAID`

**Numéros de devis / factures** :
- Générés côté **serveur** uniquement pour éviter doublons
- Client mobile crée devis avec `numero: null`
- Server assigne numéro définitif lors de la sync
- Response retourne le numéro officiel → mobile update

### 11.4 — Merge intelligent (cas avancés)

Pour champs additifs (ex: `notes` texte libre) :
- Server merge = server_notes + " || " + client_notes
- Affiche à l'artisan pour review manuel

Pour collections (ex: photos chantier) :
- Union des 2 listes (pas d'écrasement)

### 11.5 — UX résolution conflit

Si conflit **non résolvable auto** → UI dédiée :

```
⚠️ Conflit détecté sur devis DEV-2026-042

Version locale (14h32) :
  Total : 4 800 € HT

Version serveur (14h45) :
  Total : 5 200 € HT (modifié par toi sur un autre appareil)

Quelle version garder ?
[ Garder locale ]  [ Garder serveur ]  [ Merger manuellement ]
```

### 11.6 — Logging

Tous les conflits sont loggés dans `ai_decisions` / `sync_conflicts` pour audit et amélioration stratégie.

---

## 12. UX EN MODE OFFLINE

### 12.1 — Indicateur visuel

**Bandeau persistant** en haut de l'app si offline :

```
┌──────────────────────────────────────┐
│ 🔌 Hors ligne — 3 modifs en attente  │
└──────────────────────────────────────┘
```

Discret si online :
- Rien de visible (default), ou petit point vert dans coin

### 12.2 — Indicateurs par ressource

Chaque row dans une liste peut avoir un indicateur :
- 🟢 Synchronisé
- 🟡 En attente de sync
- 🔴 Conflit (à résoudre)
- ⏳ Sync en cours

### 12.3 — Bouton sync manuel

Dans settings :
```
État de la synchronisation
━━━━━━━━━━━━━━━━━━━━━━━━━
📡 Connecté — dernière sync : il y a 2 min
📬 0 modifs en attente
[ Synchroniser maintenant ]
```

Si offline :
```
🔌 Hors ligne — dernière sync : il y a 35 min
📬 12 modifs en attente
[ Synchroniser dès reconnexion ]
```

### 12.4 — Messages clairs

**Création devis offline** : "💾 Devis sauvegardé sur ton téléphone. Il sera synchronisé à la reconnexion."

**Envoi devis offline** : "📬 Envoi en attente. Le devis sera envoyé au client dès que tu retrouves le réseau."

**Action impossible offline** : "🔌 Cette action nécessite Internet. Essaie à la reconnexion."

### 12.5 — Pas d'angoisse

**Règle UX** : ne pas paniquer l'artisan. Offline = **normal**, pas exception.

Pas de popup alarmiste. Juste information claire.

---

## 13. CHIFFREMENT LOCAL SQLCIPHER

### 13.1 — Pourquoi

La DB locale contient des données sensibles :
- Infos clients (nom, téléphone, email)
- Montants devis/factures
- Photos chantier
- Mémoire Mem0 cache

Si vol / perte téléphone → risque RGPD.

### 13.2 — Setup SQLCipher

SQLCipher = extension SQLite avec chiffrement AES-256 natif.

**Package Expo** : `expo-sqlite` supporte SQLCipher via `enableCRSQLite` (disponible Expo SDK 50+).

**Alternative** : `react-native-sqlite-storage` avec SQLCipher.

### 13.3 — Gestion clé de chiffrement

```typescript
// mobile/lib/encryption.ts

import * as SecureStore from 'expo-secure-store';
import * as Crypto from 'expo-crypto';

export async function getEncryptionKey(): Promise<string> {
  let key = await SecureStore.getItemAsync('db_encryption_key');
  
  if (!key) {
    // Generate new 256-bit key
    const randomBytes = await Crypto.getRandomBytesAsync(32);
    key = Array.from(randomBytes, b => b.toString(16).padStart(2, '0')).join('');
    
    await SecureStore.setItemAsync('db_encryption_key', key, {
      requireAuthentication: true,  // Biometric lock if available
    });
  }
  
  return key;
}
```

**Stockage clé** :
- **iOS** : Keychain (hardware-backed)
- **Android** : Keystore (hardware-backed si TEE)

### 13.4 — Activation SQLCipher

```typescript
await db.execAsync(`PRAGMA key = "${encryptionKey}";`);
```

Tous les reads/writes sont chiffrés automatiquement.

### 13.5 — Performance

SQLCipher overhead : ~5-15% sur reads/writes. Négligeable pour les volumes STRUCTORAI.

### 13.6 — Rotation clé

Rotation clé = re-chiffrer toute la DB. Coût : quelques secondes.

Déclenchement :
- Change de device (migration)
- Événementiel (suspicion compromission)

Pas de rotation périodique automatique (trop coûteux, pas nécessaire).

---

## 14. SYNC MULTI-APPAREILS (F92)

Voir `docs/FEATURES_COMPLETE.md` F92.

### 14.1 — Cas d'usage

L'artisan utilise :
- Son téléphone Android sur chantier
- Sa tablette le soir pour paperasse
- L'ordi (PWA) au bureau

Tous doivent être synchronisés.

### 14.2 — Mécanisme

Le serveur Supabase est **source de vérité**. Chaque device :
- Sync up ses changements locaux
- Sync down les changements remote (`remote_deltas`)

### 14.3 — Conflit cross-device

Ex : téléphone modifie devis à 14h32, tablette modifie même devis à 14h35.

Via LWW + timestamps → tablette gagne (plus récent).

### 14.4 — Realtime Supabase

Pour un téléphone **online**, Supabase Realtime peut push les changements en temps réel :

```typescript
supabase
  .channel('devis_changes')
  .on('postgres_changes', { event: '*', schema: 'public', table: 'devis' }, (payload) => {
    applyRemoteChange(payload);
  })
  .subscribe();
```

Pas de polling, push instantané.

### 14.5 — Limite

- Realtime seulement online
- Si 2 devices offline → sync à reconnexion (LWW)

---

## 15. LIMITATIONS TECHNIQUES

### 15.1 — Storage limité

- **iOS** : limité par iPhone (32 GB+ typique)
- **Android** : dépend du device

Pour STRUCTORAI, ~100 MB suffit pour 1000 devis + 500 photos compressées.

### 15.2 — Pagination locale

Si artisan a 5000 devis → ne pas tout charger en RAM.

Pagination SQLite avec `LIMIT 50 OFFSET X`.

### 15.3 — Recherche textuelle

FTS5 (Full-Text Search SQLite) pour recherche rapide sur :
- Clients (nom, email, téléphone)
- Devis (numéro, intitulé, nom client)
- Photos (tags, notes)

### 15.4 — Pas de calculs LLM offline

Impossible :
- Suggestions IA
- Détection d'oublis dans devis
- Résumés

Workaround : **ré-analyser à la reconnexion** tous les devis créés offline.

### 15.5 — Pas de benchmark offline

Le moteur pricing offline = uniquement prix perso cache + référentiel local (si packagé).

**Référentiel local embarqué** : option future d'embarquer les `data/prix/*.json` dans le bundle mobile (~5 MB).

---

## 16. GESTION DES MÉDIAS (PHOTOS, AUDIO)

### 16.1 — Photos chantier

Capturées offline :
1. Photo prise via `expo-camera`
2. Compressée localement (~500 KB JPEG)
3. Sauvegardée sur `FileSystem.documentDirectory + 'photos/' + uuid + '.jpg'`
4. Row créée dans `photos_chantier` avec `local_uri` + `sync_status='pending'`
5. Enqueue sync (upload Supabase Storage)

### 16.2 — Upload différé

Quand reconnexion :
```typescript
async function uploadPendingPhotos(): Promise<void> {
  const pending = await db.getAllAsync(`
    SELECT * FROM photos_chantier WHERE sync_status = 'pending' AND local_uri IS NOT NULL
  `);
  
  for (const photo of pending) {
    try {
      const file = await FileSystem.readAsStringAsync(photo.local_uri, { encoding: 'base64' });
      const response = await api.post('/v1/photos/upload', {
        photo_id: photo.id,
        file_base64: file,
      });
      
      await db.runAsync(
        `UPDATE photos_chantier SET sync_status = 'synced', remote_url = ? WHERE id = ?`,
        [response.data.url, photo.id]
      );
      
      // Clean local file (optionnel, conserve quelques jours)
    } catch (err) {
      // Retry plus tard
    }
  }
}
```

### 16.3 — Audio offline

Vocaux enregistrés offline :
1. `expo-audio` enregistre local
2. Row dans `audio_messages` pending
3. **Pas de STT offline** (Whisper cloud uniquement)
4. À la reconnexion : upload + STT + process

Attention : si batch de vocaux, potentiel coût Whisper cumulé important → monitoring.

### 16.4 — Nettoyage local

Photos sync + > 30 jours → supprimées localement (conserve cache recent only).

Espace disque préservé.

---

## 17. PWA IOS — LIMITATIONS OFFLINE

### 17.1 — IndexedDB au lieu de SQLite

Safari iOS ne supporte pas SQLite natif en PWA. Fallback : **IndexedDB**.

Package : `idb` (wrapper Promise pour IndexedDB).

Mêmes patterns (stores miroir, queue, sync).

### 17.2 — Service Worker

Gestion cache HTTP + offline pages via Service Worker :

```javascript
// Expo Web génère automatiquement
self.addEventListener('install', event => { ... });
self.addEventListener('fetch', event => {
  if (event.request.url.includes('/api/')) {
    // Strategy : Network first, fallback IndexedDB
  }
});
```

### 17.3 — Limites iOS PWA

- Pas de background sync
- Pas de push notifications (iOS 16.4+ uniquement web push, limité)
- Storage limité (~50 MB avant prompt)
- Purge storage possible (iOS peut supprimer data si espace critique)

### 17.4 — Recommandation

Pour offline lourd, **app native** recommandée. PWA iOS = offline "best effort".

---

## 18. MONITORING ET MÉTRIQUES

### 18.1 — Métriques mobile

Envoyées vers analytics :
- `sync_success_count` / `sync_failure_count`
- `sync_pending_count` au démarrage
- `offline_duration_minutes` (temps passé offline)
- `conflicts_encountered`
- `sync_latency_ms`

### 18.2 — Logs backend

- `sync_batch_size` par request
- `sync_validation_errors`
- `sync_conflicts_resolved`
- `sync_client_timestamp_drift` (écart entre client et server)

### 18.3 — Alertes

- User avec >100 ops pending > 24h → signe bug local ou réseau chronique
- Ratio conflits > 5% → revoir stratégie LWW

### 18.4 — Dashboard admin

`/admin/sync` :
- Users actuellement offline
- Sync failures récentes
- Distribution temps offline

---

## 19. TESTS

### 19.1 — Tests unitaires

**Fichier** : `mobile/tests/offline.test.ts`

- Repository CRUD offline (mock SQLite)
- Queue enqueue + process
- Conflict resolution (cas 1, 2, 3, 4 du §11)

### 19.2 — Tests E2E

Simulation mode avion avec Detox :
```typescript
describe('Offline flow', () => {
  it('creates devis offline and syncs on reconnect', async () => {
    await device.setOnline(false);
    await createDevisViaUI({...});
    await expect(element(by.id('sync-badge'))).toHaveText('1');
    
    await device.setOnline(true);
    await waitFor(element(by.id('sync-badge'))).toHaveText('0').withTimeout(10000);
  });
});
```

### 19.3 — Tests stress

- 1000 ops en queue → sync
- Conflit sur 100 devis simultanés
- Disconnect/reconnect rapide

### 19.4 — Tests réels

Beta-testeurs : utiliser en vrais sous-sols (métro parisien, parkings) pendant 1 mois → feedback.

---

## 20. TROUBLESHOOTING

### 20.1 — Problèmes fréquents

| Symptôme | Cause | Solution |
|---|---|---|
| "Mes modifs ne sont pas synchronisées" | Erreur sync silencieuse | Bouton "Sync manuel" + check logs |
| "J'ai perdu des données" | Soft delete non synchronisé | Restore depuis serveur via `/v1/sync/full` |
| "App plante à l'ouverture" | DB locale corrompue | Reset DB + re-fetch (onboarding léger) |
| "Sync très lente" | Beaucoup d'ops en queue | Batch par 50, attendre |
| "Conflit permanent sur un devis" | LWW ambigu | UI résolution manuelle |
| "Photo pas uploadée" | Fichier local supprimé avant sync | Renvoi impossible, alerter user |

### 20.2 — Debug tools

Écran admin `/debug/sync` (visible si `user.role == 'admin'`) :
- Liste des ops en queue
- Logs récents
- Bouton "Reset sync state"
- Bouton "Force full re-sync from server"

### 20.3 — Full resync

Si état local corrompu :
- User déclenche "Réinitialiser depuis le serveur"
- Mobile : wipe local DB
- Full fetch depuis serveur (last 1 year)
- UX : message "Synchronisation initiale en cours..."
- Durée : 30s - 2 min selon volume

---

## 21. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/ARCH.md` §10.8 | Scénario sync offline |
| `docs/API.md` | Endpoint `/v1/sync/batch` |
| `docs/ERRORS.md` §7 | `CONCURRENT_MODIFICATION`, conflicts |
| `docs/MIGRATIONS.md` | Schémas tables mirrored |
| `docs/MEMORY.md` | Cache Mem0 local (partial) |
| `docs/VOICE.md` | Pipeline vocal (online-only) |
| `docs/DEPLOY.md` | Mobile EAS build |
| `docs/SECURITE.md` | SQLCipher AES-256 |
| `docs/FEATURES_COMPLETE.md` F91 | Mode offline |
| `docs/FEATURES_COMPLETE.md` F92 | Multi-device sync |
| `docs/I18N.md` | Offline messages UI |
| `docs/TESTS.md` (à créer) | Tests offline |
| `PRODUCT_CONTEXT.md` | Contexte chantier |
| `STRATEGIE-COMPETITIVE.md` §FAIBLESSE 7 | Différenciation offline |
| `UX_PARCOURS.md` | Cache SQLite local |

### 21.1 — Références externes

- **Expo SQLite** : https://docs.expo.dev/versions/latest/sdk/sqlite/
- **Expo FileSystem** : https://docs.expo.dev/versions/latest/sdk/filesystem/
- **NetInfo** : https://github.com/react-native-netinfo/react-native-netinfo
- **SQLCipher** : https://www.zetetic.net/sqlcipher/
- **IndexedDB (idb)** : https://github.com/jakearchibald/idb
- **Realm (alternative)** : https://realm.io/
- **WatermelonDB (alternative)** : https://watermelondb.dev/

---

> **Ce fichier est la spec complète du mode hors-ligne.**
> **Règle d'or n°1** : l'app DOIT fonctionner sans réseau pour les opérations core.
> **Règle d'or n°2** : aucune perte de données, sync automatique à reconnexion.
> **Règle d'or n°3** : UX transparente, offline est normal pas exception.
> **Différenciation** : Obat/Batappli/Sage/EBP = 0 offline mobile. STRUCTORAI = offline complet.
