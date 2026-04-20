---
name: MEMORY-STRATEGY
description: Procédure opérationnelle de la stratégie mémoire STRUCTORAI. Seuils d'alerte MemPalace/Mem0, procédure de fallback Mem0-seul en cas d'instabilité MemPalace >5%, script de migration MemPalace→Mem0, monitoring détaillé, rollback. Complémentaire de docs/MEMORY.md qui décrit l'architecture (le QUOI). Ce fichier décrit le COMMENT opérationnel.
type: operational-runbook
scope: memory-layer, devops, incident-response
priority: critical
date: 2026-04-17
version: 1.0
dependency: docs/MEMORY.md (architecture), docs/AI-ACT-COMPLIANCE.md (audit), docs/SINGLE_SOURCE_OF_TRUTH.md (chiffres)
---

# docs/MEMORY-STRATEGY.md — Procédure opérationnelle mémoire

> **Ce fichier est un runbook** — procédures exécutables en cas d'incident.
> **L'architecture conceptuelle** (quoi va où) est dans `docs/MEMORY.md`.
> **Objectif** : aucun artisan ne doit perdre ses données, aucune interruption de service en cas de panne MemPalace.

---

## 0. SOMMAIRE

1. [Stratégie globale — 3 états de la mémoire](#1-stratégie-globale--3-états-de-la-mémoire)
2. [Monitoring continu — métriques et seuils](#2-monitoring-continu--métriques-et-seuils)
3. [Déclencheurs de bascule vers Mem0-seul](#3-déclencheurs-de-bascule-vers-mem0-seul)
4. [Procédure de fallback (runbook 2h)](#4-procédure-de-fallback-runbook-2h)
5. [Script de migration MemPalace → Mem0](#5-script-de-migration-mempalace--mem0)
6. [Procédure de retour MemPalace (rollback)](#6-procédure-de-retour-mempalace-rollback)
7. [Communication pendant incident](#7-communication-pendant-incident)
8. [Plan de backup et restore](#8-plan-de-backup-et-restore)
9. [Post-mortem template](#9-post-mortem-template)
10. [Tests de résilience trimestriels](#10-tests-de-résilience-trimestriels)
11. [Évolution long terme](#11-évolution-long-terme)

---

## 1. STRATÉGIE GLOBALE — 3 ÉTATS DE LA MÉMOIRE

Le système mémoire STRUCTORAI peut être dans 3 états de fonctionnement :

### 1.1 — État A : HYBRIDE (nominal)

**Architecture active** : Mem0 + MemPalace simultanément.

- Mem0 : patterns structurés légers (prix, profils, patterns clients)
- MemPalace : verbatim riche, knowledge graph temporel, archives

**Conditions pour rester en état A** :
- Taux d'erreur MemPalace < 5% sur 1 heure
- Latence p95 MemPalace < 1 seconde
- Aucune erreur critique (data corruption, OOM, disk full)

### 1.2 — État B : DÉGRADÉ (read-only MemPalace)

**Architecture active** : Mem0 en read+write / MemPalace en read seul.

**Déclencheur** : taux d'erreur MemPalace entre 5% et 15% sur 1h **OU** latence p95 > 2s.

**Comportement** :
- Toutes les écritures vont vers Mem0 uniquement
- Lectures MemPalace conservées (pour garder le verbatim historique accessible)
- Alerte ouverte (Sentry + Slack Fabrice)
- Le système tente de revenir en état A après 2h stable

### 1.3 — État C : FALLBACK Mem0-seul

**Architecture active** : Mem0 uniquement. MemPalace désactivé.

**Déclencheur** : taux d'erreur MemPalace > 15% sur 1h **OU** MemPalace complètement down (3 erreurs 5xx ou timeouts > 30s consécutifs) **OU** incident critique (data corruption).

**Comportement** :
- MemPalace désactivé complètement (reads + writes)
- Mem0 prend le relais pour tout
- Migration progressive des data MemPalace → Mem0 en arrière-plan (script dédié)
- Alerte critique ouverte
- Status page publique mise à jour : "Service opérationnel, fonctionnalités mémoire simplifiées"

---

## 2. MONITORING CONTINU — MÉTRIQUES ET SEUILS

### 2.1 — Métriques trackées en temps réel

Les métriques sont exportées par `backend/app/memory/monitoring.py` vers Sentry + Better Uptime + dashboard admin.

| Métrique | Service | Seuil Vert 🟢 | Seuil Jaune 🟡 | Seuil Rouge 🔴 |
|---|---|---|---|---|
| Taux d'erreur MemPalace (1h) | MemPalace | < 1% | 1-5% | > 5% |
| Latence p50 MemPalace (recall) | MemPalace | < 200ms | 200-500ms | > 500ms |
| Latence p95 MemPalace (recall) | MemPalace | < 600ms | 600ms-1s | > 1s |
| Latence p95 MemPalace (store) | MemPalace | < 800ms | 800ms-1.5s | > 1.5s |
| Taux d'erreur Mem0 (1h) | Mem0 | < 0.5% | 0.5-2% | > 2% |
| Latence p95 Mem0 search | Mem0 | < 300ms | 300-500ms | > 500ms |
| Taille DB MemPalace/artisan | MemPalace | < 100MB | 100-500MB | > 500MB |
| Disk usage Railway | Infra | < 70% | 70-85% | > 85% |
| Memory usage Railway | Infra | < 75% | 75-90% | > 90% |
| Taux recall précis @5 MemPalace | MemPalace | > 92% | 85-92% | < 85% |
| Temps de bascule write | Global | — | — | > 5 min |

### 2.2 — Agrégation & dashboard

Les métriques sont agrégées sur 3 fenêtres temporelles :
- **Temps réel** (dernières 5 min) — pour détection d'incident
- **Glissante 1h** — pour déclenchement des seuils bascule
- **Historique 24h / 7j / 30j** — pour analyses tendance et capacity planning

**Outils** :
- **UptimeRobot** (gratuit jusqu'à 50 monitors) : check `/health` toutes les 60s — alerte si down >2min
- **Better Uptime** (~10$/mois) : alertes multi-canal (email, SMS Fabrice, Slack) + status page publique `status.structorai.app`
- **Sentry** (déjà en place) : tracking erreurs backend + frontend mobile
- **Dashboard admin** interne : `/admin/memory-health` réservé Fabrice + support

### 2.3 — Alertes proactives

| Alerte | Canal | Priorité |
|---|---|---|
| Taux erreur MemPalace > 5% sur 10min | Slack Fabrice | 🟡 moyen |
| Taux erreur MemPalace > 10% sur 5min | SMS Fabrice + Slack | 🔴 critique |
| MemPalace 3 erreurs 5xx consécutives | SMS Fabrice + Slack + déclenchement runbook | 🔴 critique |
| Disk usage > 85% Railway | Slack Fabrice + support | 🟡 moyen |
| Mem0 latence > 500ms p95 pendant 15min | Slack Fabrice | 🟡 moyen |
| MemPalace DB/artisan > 500MB | Slack Fabrice | 🟡 moyen (optimisation) |

---

## 3. DÉCLENCHEURS DE BASCULE VERS MEM0-SEUL

### 3.1 — Déclencheurs automatiques (bascule en état B ou C)

| Condition | État cible | Action |
|---|---|---|
| Taux erreur MemPalace > 5% sur 60min | **État B** dégradé | Writes → Mem0 uniquement, reads MemPalace conservés |
| Taux erreur MemPalace > 15% sur 30min | **État C** fallback | Désactivation complète MemPalace |
| MemPalace 3 erreurs 5xx consécutives OU timeout > 30s | **État C** fallback | Désactivation immédiate |
| Disk Railway > 95% | **État C** fallback | Désactivation pour éviter corruption |
| Détection data corruption MemPalace (checksum fail) | **État C** fallback | Isolation DB pour forensic |

### 3.2 — Déclencheur manuel (circuit breaker Fabrice)

Fabrice peut forcer la bascule en état C via une commande admin :

```bash
# Via CLI admin sécurisé
curl -X POST https://api.structorai.app/admin/memory/fallback \
  -H "Authorization: Bearer $ADMIN_TOKEN" \
  -d '{"state": "C", "reason": "manual_maintenance", "duration": "2h"}'
```

**Cas d'usage** : maintenance planifiée, mise à jour MemPalace majeure, incident détecté manuellement.

### 3.3 — Ce qui NE déclenche PAS la bascule

Ne pas basculer pour :
- Pic de charge ponctuel (<10min) → circuit breaker standard gère
- Un artisan isolé en erreur → traitement cas par cas
- Latence élevée sans erreur → dégradation perf, pas bascule

---

## 4. PROCÉDURE DE FALLBACK (RUNBOOK 2H)

> **Objectif : basculer de l'état A/B à l'état C Mem0-seul sans downtime utilisateur, en moins de 2h.**

### 4.1 — Phase 1 : détection et alerte (0-5 min)

1. **Monitoring détecte le seuil** (ex: taux erreur MemPalace > 15%)
2. **Alerte automatique** : SMS Fabrice + Slack + Sentry
3. **Fabrice se connecte** au dashboard admin `/admin/memory-health`
4. **Vérification** : l'alerte n'est pas un faux positif (vérifier logs, requêtes récentes)

### 4.2 — Phase 2 : bascule writes (5-15 min)

1. **Activer le flag global** `MEMORY_STATE=C` dans l'environnement Railway
2. **Le flag active** automatiquement dans `memory/router.py` :
   - Toutes les écritures vont vers Mem0
   - Les lectures MemPalace retournent une erreur gracieuse → fallback Mem0
3. **Vérification** : aucune écriture ne tombe plus en erreur (Sentry doit être propre)
4. **Status page** : mise à jour manuelle sur `status.structorai.app` — "Mémoire verbatim en maintenance, service opérationnel"

### 4.3 — Phase 3 : migration arrière-plan (15min - 2h)

Déclenchement du script de migration (voir §5) qui :
1. **Extrait** les données MemPalace actives (dernières 30 jours)
2. **Transforme** en format Mem0 avec tags enrichis
3. **Ingère** dans Mem0 par batchs de 100 entrées
4. **Vérifie** l'intégrité par checksums

**Pendant cette phase** :
- Les nouvelles écritures vont directement dans Mem0
- Les lectures qui échouent sur Mem0 (car données pas encore migrées) → fallback gracieux "donnée temporairement indisponible, utilise tes notes"
- Les agents continuent de fonctionner avec les patterns Mem0 existants

### 4.4 — Phase 4 : validation (2h-3h)

1. **Vérifier** que 100% des artisans actifs ont leurs patterns critiques accessibles via Mem0
2. **Vérifier** que les agents fonctionnent (test E2E : génération devis, relance)
3. **Log** de la durée réelle de bascule
4. **Communication artisans** : email d'information si >1h d'impact visible

### 4.5 — Checklist exécution

Cette checklist est à afficher sur `/admin/memory-health` pendant l'incident :

```
[ ] Monitoring confirme seuil > 15% erreur MemPalace
[ ] Alerte SMS + Slack reçue
[ ] Connexion Fabrice sur dashboard
[ ] Vérification faux positif (5 min)
[ ] Flag MEMORY_STATE=C activé sur Railway
[ ] Vérification erreurs Sentry arrêtées
[ ] Status page mise à jour
[ ] Script migration lancé
[ ] Monitoring script migration (toutes les 15 min)
[ ] Validation E2E après 2h
[ ] Communication artisans si impact > 1h
[ ] Post-mortem planifié à J+2
```

---

## 5. SCRIPT DE MIGRATION MEMPALACE → MEM0

### 5.1 — Fichier `backend/app/memory/migration_mempalace_to_mem0.py`

**Objectif** : migrer les données critiques des derniers 30 jours de MemPalace vers Mem0 sans downtime.

### 5.2 — Périmètre de migration

**Migré (30 derniers jours, priorité haute)** :
- Conversations vocales verbatim → résumés Mem0 (réduction volume ~90%)
- Patterns clients dérivés → Mem0 tags clients
- Prix mentionnés dans conversations → Mem0 tags prix
- Décisions prises → Mem0 tags décisions

**Non migré immédiatement (fait en differé post-incident)** :
- Photos chantier (retrouvées via Supabase Storage même sans MemPalace)
- Archives PDF factures (Supabase Storage)
- Historique > 30 jours (accessible en lecture seule en cas de retour MemPalace)

### 5.3 — Pseudo-code du script

```python
# backend/app/memory/migration_mempalace_to_mem0.py

import logging
from datetime import datetime, timedelta
from typing import Dict, List
from mem0ai import MemoryClient
from app.memory.mempalace_client import MemPalaceClient
from app.utils.claude_client import claude_summarize

logger = logging.getLogger(__name__)

class MemPalaceToMem0Migrator:
    """Migre les données critiques MemPalace vers Mem0 en cas d'incident."""

    BATCH_SIZE = 100
    LOOKBACK_DAYS = 30

    def __init__(self, org_id: str):
        self.org_id = org_id
        self.mempalace = MemPalaceClient(read_only=True)
        self.mem0 = MemoryClient()

    async def migrate_organization(self) -> Dict[str, int]:
        """
        Migre toutes les data critiques d'une organisation.
        Retourne : { 'conversations': N, 'patterns': M, 'prices': P }
        """
        since = datetime.utcnow() - timedelta(days=self.LOOKBACK_DAYS)
        stats = {'conversations': 0, 'patterns': 0, 'prices': 0, 'errors': 0}

        # 1. Conversations verbatim → résumés Mem0
        conversations = await self.mempalace.list_conversations(
            org_id=self.org_id, since=since
        )
        for batch in chunks(conversations, self.BATCH_SIZE):
            try:
                await self._migrate_conversations_batch(batch)
                stats['conversations'] += len(batch)
            except Exception as e:
                logger.error(f"Batch conversations error: {e}")
                stats['errors'] += len(batch)

        # 2. Patterns clients
        patterns = await self.mempalace.list_client_patterns(
            org_id=self.org_id, since=since
        )
        for pattern in patterns:
            try:
                await self.mem0.add(
                    user_id=pattern['artisan_id'],
                    data=f"Pattern client {pattern['client_id']}: {pattern['description']}",
                    metadata={
                        'type': 'client_pattern',
                        'source': 'mempalace_migration',
                        'client_id': pattern['client_id'],
                        'migrated_at': datetime.utcnow().isoformat(),
                    }
                )
                stats['patterns'] += 1
            except Exception as e:
                logger.error(f"Pattern migration error: {e}")
                stats['errors'] += 1

        # 3. Prix mentionnés
        prices = await self.mempalace.list_prices_mentioned(
            org_id=self.org_id, since=since
        )
        for price in prices:
            try:
                await self.mem0.add(
                    user_id=price['artisan_id'],
                    data=f"Prix {price['item']}: {price['amount']}€ ({price['context']})",
                    metadata={
                        'type': 'prix',
                        'metier': price['metier'],
                        'zone': price['zone'],
                        'source': 'mempalace_migration',
                        'confidence': price.get('confidence', 'medium'),
                    }
                )
                stats['prices'] += 1
            except Exception as e:
                logger.error(f"Price migration error: {e}")
                stats['errors'] += 1

        logger.info(f"Migration org {self.org_id} complete: {stats}")
        return stats

    async def _migrate_conversations_batch(self, conversations: List[Dict]):
        """Résumé + migration d'un batch de conversations."""
        for conv in conversations:
            # Résumé via Claude Haiku (rapide, peu cher)
            summary = await claude_summarize(
                text=conv['transcript'],
                max_tokens=300,
                model="claude-haiku-4-5-20251001",
            )
            # Store dans Mem0
            await self.mem0.add(
                user_id=conv['artisan_id'],
                data=summary,
                metadata={
                    'type': 'conversation_summary',
                    'conversation_id': conv['id'],
                    'client_id': conv.get('client_id'),
                    'date': conv['date'],
                    'source': 'mempalace_migration',
                    'original_verbatim_lost': False,  # MemPalace verbatim reste archivé pour retour
                }
            )

# Exécution
if __name__ == "__main__":
    import asyncio
    # Récupérer toutes les orgs actives
    orgs = await get_active_organizations()
    for org in orgs:
        migrator = MemPalaceToMem0Migrator(org_id=org.id)
        stats = await migrator.migrate_organization()
        logger.info(f"Org {org.id}: {stats}")
```

### 5.4 — Orchestration

Le script est lancé en mode **worker asynchrone** via **ARQ** (déjà en place pour les tasks) :

```bash
# Déclenchement du migration job
python -m app.memory.migration_mempalace_to_mem0 --mode=full --all-orgs

# Monitoring en temps réel
tail -f /logs/migration.log

# Vérification progression
curl https://api.structorai.app/admin/memory/migration/status
# Retourne : { "total_orgs": 150, "migrated": 87, "errors": 3, "eta": "45min" }
```

### 5.5 — Safeguards du script

- **Idempotent** : peut être relancé sans duplication (vérifie `source=mempalace_migration` avant d'ajouter)
- **Rate-limited** : max 10 req/s sur Mem0 pour éviter throttling
- **Resumable** : stocke la progression en base, reprend où il s'est arrêté en cas de crash
- **Rollback-able** : toutes les entrées migrées sont taggées `source=mempalace_migration` pour suppression propre en cas de rollback

---

## 6. PROCÉDURE DE RETOUR MEMPALACE (ROLLBACK)

### 6.1 — Conditions pour retour état A

Avant de repasser en état hybride (A), **tous** ces critères doivent être verts pendant **24h consécutives** :

- Taux erreur MemPalace < 1%
- Latence p95 < 600ms (recall) et < 800ms (store)
- Aucune alerte 5xx ni timeout
- Disk usage Railway < 70%
- Root cause de l'incident identifiée et résolue
- Post-mortem validé par Fabrice

### 6.2 — Phases du retour

#### Phase 1 : état B (read-only MemPalace, 24h minimum)

1. Activer `MEMORY_STATE=B` dans Railway
2. Les lectures MemPalace se font en direct + fallback Mem0 si échec
3. Les écritures restent sur Mem0
4. Monitoring actif sur les lectures MemPalace
5. **Durée minimum** : 24h pour valider la stabilité

#### Phase 2 : réactivation writes MemPalace (état A, prudent)

1. Si état B stable 24h → passer `MEMORY_STATE=A`
2. Les nouvelles écritures vont dans Mem0 **ET** MemPalace (double-write)
3. Les écritures Mem0 "migration" ne sont PAS rétro-écrites dans MemPalace (le verbatim original est déjà archivé)
4. Monitoring renforcé pendant 7 jours

#### Phase 3 : stabilisation (7 jours en double-write)

Si 7 jours stables → retour complet en état A nominal.

### 6.3 — Commandes admin

```bash
# Passage en état B
curl -X POST https://api.structorai.app/admin/memory/state \
  -H "Authorization: Bearer $ADMIN_TOKEN" \
  -d '{"state": "B", "reason": "mempalace_recovered_24h_stable"}'

# Passage en état A (après 24h en B stable)
curl -X POST https://api.structorai.app/admin/memory/state \
  -H "Authorization: Bearer $ADMIN_TOKEN" \
  -d '{"state": "A", "reason": "full_rollback"}'
```

---

## 7. COMMUNICATION PENDANT INCIDENT

### 7.1 — Matrice de communication

| Gravité | Impact utilisateur | Canal | Délai |
|---|---|---|---|
| **P0 (critique)** | Service totalement down | Status page + email tous artisans + push mobile | < 15 min |
| **P1 (haute)** | Fonctionnalités mémoire dégradées, service principal OK | Status page + email impactés | < 30 min |
| **P2 (moyenne)** | Latence élevée, pas de perte fonctionnelle | Status page uniquement | < 1h |
| **P3 (faible)** | Incident interne sans impact utilisateur | Logs + Slack interne | — |

### 7.2 — Templates de communication

#### Email incident P1 (impact partiel)

```
Objet : Maintenance mémoire — service opérationnel

Bonjour [Prénom],

Nous rencontrons actuellement un incident sur notre système de 
mémoire avancée (MemPalace). Concrètement :

✅ Ce qui fonctionne normalement :
- Tes devis, factures, relances
- Tes agents IA (Devis, Compta, Planning, etc.)
- Tes données historiques (accessibles)

⏸ Ce qui est temporairement limité :
- La mémoire de tes conversations vocales les plus récentes 
  peut être moins précise pendant quelques heures.

Nous rétablirons le service nominal dans les prochaines heures. 
Aucune donnée n'est perdue.

Suivi en temps réel : status.structorai.app

Merci de ta patience,
L'équipe STRUCTORAI
```

#### Status page P1

```
📡 MAINTENANCE EN COURS — Mémoire verbatim
Statut : dégradé / service opérationnel
Début : [TIMESTAMP]
ETA : [X]h

Impact : fonctionnalités mémoire avancée temporairement limitées.
Non-impact : devis, factures, agents IA, données historiques.
```

#### Notification push mobile P0/P1

```
STRUCTORAI — Info maintenance
Service opérationnel ✅
Mémoire vocale temporairement simplifiée.
Rétablissement prévu : [X]h. Détails en app.
```

### 7.3 — Règle de transparence

**Règle d'or** : jamais de mensonge. Toujours dire la vérité même si elle est inconfortable. Les artisans détestent plus le silence que les mauvaises nouvelles honnêtes.

**Ce qu'on DIT toujours** :
- Ce qui fonctionne vs ce qui est limité
- L'ETA de résolution (même approximatif)
- Si des données sont temporairement inaccessibles
- Le post-mortem public (si P0)

**Ce qu'on NE DIT JAMAIS** :
- "Tout va bien" si ce n'est pas le cas
- "Ça ne peut pas arriver" (manque d'humilité)
- Détails techniques anxiogènes sans contexte (code d'erreur brut, stacktrace)

---

## 8. PLAN DE BACKUP ET RESTORE

### 8.1 — Backups quotidiens MemPalace

**Stack** : ChromaDB + SQLite embarqués sur Railway.

**Backup automatique** :
- **Fréquence** : quotidien à 03:00 UTC (heure creuse)
- **Cible** : Supabase Storage (bucket `mempalace-backups`, région EU)
- **Rétention** : 30 jours (backups quotidiens) + 12 mois (backups mensuels)
- **Chiffrement** : AES-256 at rest, clé dans Railway env var

**Script** : `scripts/backup_mempalace.sh` exécuté par cron Railway.

```bash
#!/bin/bash
# scripts/backup_mempalace.sh
set -e
TIMESTAMP=$(date -u +%Y%m%d_%H%M)
BACKUP_NAME="mempalace_${TIMESTAMP}.tar.gz.enc"

# 1. Dump ChromaDB + SQLite
tar -czf /tmp/mempalace_${TIMESTAMP}.tar.gz \
  /data/mempalace/chroma_db \
  /data/mempalace/sqlite.db

# 2. Chiffrement
openssl enc -aes-256-cbc -salt \
  -in /tmp/mempalace_${TIMESTAMP}.tar.gz \
  -out /tmp/${BACKUP_NAME} \
  -k $BACKUP_ENCRYPTION_KEY

# 3. Upload Supabase Storage
curl -X POST "${SUPABASE_URL}/storage/v1/object/mempalace-backups/${BACKUP_NAME}" \
  -H "Authorization: Bearer ${SUPABASE_SERVICE_KEY}" \
  --data-binary @/tmp/${BACKUP_NAME}

# 4. Cleanup local
rm /tmp/mempalace_${TIMESTAMP}.tar.gz /tmp/${BACKUP_NAME}

echo "Backup complete: ${BACKUP_NAME}"
```

### 8.2 — Backups Mem0

**Stratégie** : Mem0 cloud EU gère ses propres backups.

**Vérification mensuelle** : tester un restore Mem0 sur un environnement sandbox pour valider.

### 8.3 — Procédure de restore

#### Restore complet MemPalace (cas data corruption)

1. Identifier le dernier backup sain dans Supabase Storage
2. Télécharger + déchiffrer :
   ```bash
   # Download
   curl -o backup.tar.gz.enc \
     "${SUPABASE_URL}/storage/v1/object/mempalace-backups/mempalace_${DATE}.tar.gz.enc"

   # Decrypt
   openssl enc -aes-256-cbc -d -salt \
     -in backup.tar.gz.enc \
     -out backup.tar.gz \
     -k $BACKUP_ENCRYPTION_KEY
   ```
3. Arrêter MemPalace service :
   ```bash
   railway service stop mempalace
   ```
4. Restaurer les fichiers :
   ```bash
   tar -xzf backup.tar.gz -C /data/mempalace/
   ```
5. Vérifier intégrité checksum
6. Redémarrer MemPalace
7. Tester lectures sur quelques orgs échantillons
8. Passer en état B pendant 24h avant retour complet

#### Restore partiel (un artisan spécifique)

Si un artisan a perdu ses data (bug isolé, suppression accidentelle) :
1. Requête CLI admin : `python -m scripts.restore_artisan_memory --org_id=X --date=Y`
2. Le script extrait du backup les données de cet artisan uniquement
3. Réinjection dans MemPalace

### 8.4 — RTO / RPO

| Objectif | Valeur |
|---|---|
| **RTO** (Recovery Time Objective) | **2h** pour bascule Mem0-seul, **4h** pour restore complet MemPalace |
| **RPO** (Recovery Point Objective) | **24h max** (backup quotidien). Perte potentielle : data du jour avant backup |

**Amélioration V2** : passage à **backup incrémental toutes les 4h** pour réduire RPO à 4h.

---

## 9. POST-MORTEM TEMPLATE

À remplir dans les **48h suivant tout incident P0 ou P1** et publier dans `docs/post-mortems/YYYY-MM-DD-memory-incident.md`.

### 9.1 — Template

```markdown
# Post-mortem : Incident mémoire YYYY-MM-DD

## Résumé exécutif
- **Date et heure** : [ISO timestamp]
- **Durée totale** : [X]h
- **Impact** : [nombre d'artisans impactés, fonctionnalités dégradées]
- **Gravité** : P0 / P1 / P2
- **Statut** : résolu

## Chronologie
| Horaire (UTC) | Évènement |
|---|---|
| XX:XX | Seuil d'erreur MemPalace dépassé (15%) |
| XX:XX | Alerte SMS Fabrice |
| XX:XX | Connexion dashboard admin |
| XX:XX | Bascule état C activée |
| XX:XX | Status page mise à jour |
| XX:XX | Script migration lancé |
| XX:XX | Service rétabli en état C stable |
| XX:XX | Investigation root cause |
| XX:XX | Rollback état B |
| XX:XX | Rollback état A complet |

## Cause racine (Root cause)
[Analyse détaillée — bug, config, capacité, externalité]

## Impact utilisateur
- [X] artisans ont subi [quoi]
- [Y] artisans ont reçu une notification
- [Z] artisans n'ont rien remarqué

## Ce qui a bien fonctionné
- [Liste — détection, bascule, communication]

## Ce qui doit être amélioré
- [Liste actionnable]

## Actions de correction (avec propriétaire + deadline)
| # | Action | Owner | Deadline |
|---|---|---|---|
| 1 | [Action] | Fabrice | J+7 |
| 2 | [Action] | Fabrice | J+14 |

## Apprentissages
[Leçons à documenter dans ce fichier MEMORY-STRATEGY.md]
```

### 9.2 — Publication

- **Interne** : dans `docs/post-mortems/` (commit)
- **Externe** : résumé sur status page (obligatoire si P0, optionnel P1)
- **Transparence** : inclure dans le rapport trimestriel artisans si grave

---

## 10. TESTS DE RÉSILIENCE TRIMESTRIELS

### 10.1 — Tests obligatoires chaque trimestre

1. **Test de bascule état C** (fire drill)
   - Fabrice déclenche manuellement `MEMORY_STATE=C` en production hors heures de pointe
   - Vérifie que l'app continue à fonctionner
   - Mesure le temps de bascule réel
   - **Cible** : bascule < 5 min, aucun artisan impacté visiblement

2. **Test de migration MemPalace → Mem0** (sur 1 org de test)
   - Lance le script de migration sur une org sandbox
   - Vérifie l'intégrité des data migrées
   - **Cible** : 0 perte, <1% erreurs non critiques

3. **Test de restore backup MemPalace**
   - Restaure un backup de la veille sur un environnement staging
   - Vérifie que ChromaDB + SQLite sont fonctionnels
   - **Cible** : restore < 30 min, vérification OK

4. **Test de charge Mem0**
   - Simule un pic de 10x la charge nominale sur Mem0
   - Vérifie que Mem0 tient (pas d'erreurs >1%)
   - **Cible** : latence p95 < 800ms même à 10x

### 10.2 — Calendrier

| Trimestre | Tests à faire |
|---|---|
| Q3 2026 (juillet) | Bascule + migration + restore |
| Q4 2026 (octobre) | Bascule + charge |
| Q1 2027 (janvier) | Bascule + migration + restore |
| Q2 2027 (avril) | Bascule + charge + revue archi complète |

### 10.3 — Rapport de test

Chaque test donne lieu à un rapport dans `docs/resilience-tests/YYYY-Q[X]-test.md` avec :
- Résultats chiffrés
- Anomalies détectées
- Actions correctives

---

## 11. ÉVOLUTION LONG TERME

### 11.1 — Horizon 3-6 mois post-launch

- Évaluer la stabilité réelle de MemPalace en production
- Si taux d'erreur > 2% en moyenne sur 3 mois → envisager migration complète vers Mem0 + alternatives
- Si stabilité OK → renforcer l'usage MemPalace (plus de verbatim, knowledge graph enrichi)

### 11.2 — Horizon 12 mois

- Évaluer alternatives émergentes (autres bases mémoire locales, solutions européennes)
- Critères : stabilité, performance, souveraineté EU, TCO
- Décision de renouvellement architecture à la fin T4 2026

### 11.3 — Horizon V3 (2027+)

Options envisagées selon maturité marché :
- Migration vers un système propriétaire optimisé BTP
- Intégration avec outils émergents (MCP servers communautaires, nouveaux vector stores)
- Adoption d'un standard européen si un émerge (ex: Gaia-X compatible)

---

## 12. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/MEMORY.md` | Architecture conceptuelle (quoi va où) — complémentaire de ce fichier |
| `docs/SINGLE_SOURCE_OF_TRUTH.md` | Chiffres : RTO 2h, RPO 24h, seuils 5%/15% |
| `docs/AI-ACT-COMPLIANCE.md` (à créer) | Audit log des décisions IA, résilience conforme AI Act |
| `docs/SUPPORT-STRATEGY.md` (à créer) | Escalade support en cas d'incident mémoire |
| `SECURITE.md` §12 | Résilience LLM multi-provider (F132) |
| `COUT_REEL.md` | Coût read replica Supabase (+25$/mois), monitoring Better Uptime (~10$/mois) |
| `BUILD_PLAN.md` | Sprint 8 F132 résilience + `circuit_breaker.py` pattern Ouroboros |
| `FEATURES.md` | F132 Résilience et redondance |
| `CLAUDE.md` §Circuit breaker | Règle Ouroboros 3 réponses vides → fallback |

---

> **Ce fichier est un runbook exécutable.**
> **Toute modification doit être testée en staging avant production.**
> **Les procédures doivent être relues et testées par Fabrice chaque trimestre.**
> **L'objectif est que l'app tienne même si MemPalace s'effondre — l'artisan ne doit JAMAIS perdre ses données ni son service.**
