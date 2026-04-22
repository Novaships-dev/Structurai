---
name: KNOWLEDGE
description: Base de connaissances BTP de STRUCTORAI — moteur RAG vectoriel (pgvector Supabase) alimenté par 8819 lignes de données terrain dans 11 fichiers référentiels métiers (plomberie 1021L, électricité 960L, isolation 765L, couverture 703L, chauffage/clim 693L, carrelage 573L, maçonnerie 488L, peinture 465L, menuiserie-serrurerie 426L, plaquiste 401L, façadier 375L) + docs/METIER.md (TVA, 47 mentions, Factur-X). Pipeline ingestion (chunking, embeddings, upsert), query patterns semantic search + hybrid BM25, relevance scoring, reranking, multi-langue. Différenciation : aucun concurrent n'a cette profondeur de data terrain BTP structurée pour RAG IA.
type: technical-rag-knowledge-base
scope: rag-engine, embeddings, pgvector, chunking-pipeline
priority: critical
date: 2026-04-18
version: 1.0
total_knowledge_lines: 8819 (11 trade files) + docs/METIER.md
total_trades: 11
embedding_model: claude-compatible ou OpenAI text-embedding-3-small
vector_dim: 1536
chunk_size: ~500 tokens
chunk_overlap: ~100 tokens
---

# docs/KNOWLEDGE.md — Base de connaissances RAG BTP

> **Ce fichier est la source de vérité de la knowledge base BTP.**
> 8819 lignes de data terrain pure, en 11 fichiers référentiels métiers.
> **Différenciation concurrentielle critique** : ChatGPT ne connaît pas les pièges terrain BTP, les tarifs départementaux, les DTU précis. STRUCTORAI oui.
> Reference : `docs/ARCH.md` §4 Services + `docs/AGENTS.md` (tous les agents utilisent ce RAG) + `docs/PRICING-ENGINE.md` §15.

---

## 0. SOMMAIRE

1. [Principes fondateurs](#1-principes-fondateurs)
2. [Contenu knowledge base](#2-contenu-knowledge-base)
3. [Architecture RAG](#3-architecture-rag)
4. [Pipeline d'ingestion](#4-pipeline-dingestion)
5. [Stratégie de chunking](#5-stratégie-de-chunking)
6. [Embeddings](#6-embeddings)
7. [Stockage pgvector](#7-stockage-pgvector)
8. [Query patterns](#8-query-patterns)
9. [Hybrid search (semantic + BM25)](#9-hybrid-search-semantic--bm25)
10. [Reranking](#10-reranking)
11. [Relevance scoring](#11-relevance-scoring)
12. [Métadonnées par chunk](#12-métadonnées-par-chunk)
13. [Multi-langue](#13-multi-langue)
14. [Intégration par agent](#14-intégration-par-agent)
15. [Service `knowledge_service.py`](#15-service-knowledge_servicepy)
16. [Enrichissement continu](#16-enrichissement-continu)
17. [Performance et cache](#17-performance-et-cache)
18. [Évaluation qualité RAG](#18-évaluation-qualité-rag)
19. [Multi-pays F135](#19-multi-pays-f135)
20. [Anti-patterns](#20-anti-patterns)
21. [Monitoring](#21-monitoring)
22. [Migration et versioning](#22-migration-et-versioning)
23. [Références croisées](#23-références-croisées)

---

## 1. PRINCIPES FONDATEURS

### 1.1 — Pourquoi un RAG BTP est critique

**Ce que ChatGPT NE sait PAS** :
- Les pièges terrain ("Ø16 pour la baignoire ? Il faut du Ø20 minimum")
- Les prix réels FR par région (évolution 2025-2026)
- Les DTU précis (52.2 pour carrelage, 60.1/60.5 plomberie, 20 maçonnerie)
- Les règles fiscales BTP (47 mentions obligatoires, TVA 5.5/10/20 conditions)
- Les durées de pose réalistes par artisan expérimenté
- Les erreurs classiques (132 erreurs terrain documentées)

**Ce que STRUCTORAI SAIT via son RAG** :
- **8819 lignes** de data terrain vérifiée
- **1080+ postes** BTP chiffrés avec fourchettes min/max/médian
- **132 erreurs terrain** documentées par métier
- **DTU normes** référencées et interprétées
- **Pièges techniques** par métier
- **TVA + mentions** obligatoires (couvert par docs/METIER.md)

### 1.2 — 8 règles immuables

1. **Source unique de vérité** : les 11 fichiers métiers + `docs/METIER.md` = TOUT ce que l'IA sait sur le BTP
2. **Jamais d'hallucination** : toute info doit provenir de la knowledge base
3. **Tracabilité** : chaque réponse cite la source (fichier + ligne approximative)
4. **Mise à jour versionnée** : chaque évolution de la knowledge base a un tag version
5. **Embeddings régénérés** à chaque update des fichiers source
6. **Accès RLS-bypass** : la knowledge base est **publique** (pas d'org_id, partagée)
7. **Latence < 200ms** pour lookup sémantique simple
8. **Multi-langue** : les chunks peuvent être interrogés dans toutes les langues supportées

### 1.3 — Différenciation concurrentielle

| Aspect | Concurrents BTP | STRUCTORAI |
|---|---|---|
| Bibliothèque de postes | Statique, peu détaillée (~500) | 1080+ postes dynamiques avec métadonnées |
| Pièges terrain documentés | ❌ | ✅ 132 documentées |
| RAG IA | ❌ | ✅ pgvector + embeddings |
| Mise à jour | Annuelle manuelle | Continue + auto-enrichissement |
| DTU référencés | Basique | Précis avec interprétation IA |
| Adaptation régionale | ❌ | ✅ 11 zones coefficients |

---

## 2. CONTENU KNOWLEDGE BASE

### 2.1 — Les 11 fichiers référentiels métiers

Path : `docs/metier/*.md` (chargés en knowledge base + prix extraits dans `data/prix/FR/*.json`).

| Métier | Fichier | Lignes | Contenu |
|---|---|---|---|
| **Plomberie** | `plomberie.md` | **1021** | Sanitaires, alim cuivre/PER, évac PVC, chauffe-eau, DTU 60.1/60.5, pièges fréquents |
| **Électricité** | `electricite.md` | **960** | Circuits, tableau, NF C 15-100, VDI, bornes VE, domotique énergétique |
| **Isolation** | `isolation.md` | **765** | ITE/ITI/mur/sol/toit, soufflage, RGE, MaPrimeRénov', CEE |
| **Couverture** | `couverture.md` | **703** | Tuiles, ardoise, zinc, bac acier, isolation toiture, ABF zones classées |
| **Chauffage-Clim** | `chauffage_climatisation.md` | **693** | PAC air-air/air-eau, chaudière, RGE QualiPAC, ramonage |
| **Carrelage** | `carrelage.md` | **573** | Grès cérame/faïence/mosaïque, DTU 52.2, joints dilatation |
| **Maçonnerie** | `maconnerie.md` | **488** | Démolition, ouvertures, sous-œuvre BE, dalle, DTU 20 |
| **Peinture** | `peinture.md` | **465** | Préparation, acrylique/glycéro, impressions, nombre couches |
| **Menuiserie-Serrurerie** | `menuiserie_serrurerie.md` | **426** | Portes, fenêtres PVC/alu/bois, volets, serrures multipoints |
| **Plaquiste** | `plaquiste.md` | **401** | Cloisons placo, BA13 types (hydro/phonique), faux plafonds |
| **Façadier** | `facadier.md` | **375** | Ravalement, ITE, échafaudage, bardage bois, DP mairie |
| **TOTAL** | | **8819** | |

### 2.2 — Structure d'un fichier métier

Chaque fichier suit une structure uniforme :

```markdown
# [Métier] — Référentiel technique STRUCTORAI

## Section 1 — Vue d'ensemble
[Présentation métier, périmètre, spécificités]

## Section 2 — Outils et matériaux
[Matériaux courants, marques, fournisseurs typiques]

## Section 3 — Prix par poste
### 3.1 — [Catégorie A]
#### Poste : [Nom du poste]
- Unité : m²/ml/u/h/forfait
- Prix : X-Y €/unité HT (zone de référence)
- Main-d'œuvre : Zh par unité
- Matériaux courants : ...
- DTU/Norme : ...
- TVA possible : 5.5% / 10% / 20% (conditions)
- Pièges fréquents : ...
- Variations : +X% pour [condition]

## Section 4 — Normes et DTU
[DTU applicables, dates, extraits clés]

## Section 5 — Erreurs terrain fréquentes
[Liste des ~10-15 erreurs classiques par métier = 132 total]

## Section 6 — TVA et fiscalité
[Règles spécifiques métier]

## Section 7 — Sécurité chantier
[EPI, règles sécurité, assurance]
```

### 2.3 — docs/METIER.md (constitution BTP)

Voir `docs/METIER.md` — **Constitution immuable** :
- 3 taux TVA BTP (5.5/10/20) avec règles
- **47 mentions obligatoires** devis/facture (multi-langue FR/EN/TR/PT/AR/ES)
- Retenue de garantie 5%
- Dates e-invoicing / e-reporting
- Sanctions (3000€ devis absent, 75000€ décennale, etc.)
- **11 zones coefficients régionaux**
- Règle d'or zéro invention

### 2.4 — Documents additionnels indexés

En plus des 11 fichiers + METIER :
- `docs/FEATURES_COMPLETE.md` (136 features — pour Agent Support répondre aux questions fonctionnelles)
- `docs/COACH-DISCLAIMER.md` (disclaimer Coach Business F119)
- `docs/AI-ACT-COMPLIANCE.md` (compliance pour Agent Support)

### 2.5 — Terminologie BTP multi-langue

Voir `docs/I18N.md` §11 — fichiers `data/btp_terminology_{locale}.json` permettent de matcher requêtes en TR/PT/AR/ES vers chunks français.

---

## 3. ARCHITECTURE RAG

### 3.1 — Vue d'ensemble

```
┌───────────────────────────────────────────────────┐
│  INGESTION PIPELINE (offline, au build/update)    │
│  1. Parse docs/metier/*.md + docs/METIER.md       │
│  2. Chunking (~500 tokens avec overlap)           │
│  3. Enrichir métadonnées (métier, catégorie, etc) │
│  4. Embeddings (Claude / OpenAI ada)              │
│  5. Upsert dans pgvector                          │
└──────────────────────┬────────────────────────────┘
                       │
                       ▼
┌───────────────────────────────────────────────────┐
│  STOCKAGE pgvector (Supabase)                     │
│  Table: knowledge_chunks                          │
│  Colonnes: id, source_file, chunk_index,          │
│            chunk_text, embedding vector(1536),    │
│            metadata JSONB                         │
│  Index: ivfflat (cosine similarity, 100 lists)    │
└──────────────────────┬────────────────────────────┘
                       │
                       ▼
┌───────────────────────────────────────────────────┐
│  QUERY PIPELINE (runtime, par agent)              │
│  1. Query texte de l'agent                        │
│  2. Normalisation + expansion (synonymes BTP)     │
│  3. Embedding de la query                         │
│  4. Semantic search (top-K)                       │
│  5. [Optional] BM25 search (keywords)             │
│  6. Fusion + reranking                            │
│  7. Return top-N chunks + metadata                │
└──────────────────────┬────────────────────────────┘
                       │
                       ▼
┌───────────────────────────────────────────────────┐
│  AGENT LLM (Claude Sonnet/Opus)                   │
│  - Context injecté : chunks pertinents            │
│  - Raisonnement + décision                        │
│  - Réponse avec citation source                   │
└───────────────────────────────────────────────────┘
```

### 3.2 — Composants

- **Ingestion** : `scripts/ingest_knowledge.py` (run au build ou manuellement)
- **Stockage** : Supabase pgvector (`knowledge_chunks` table, voir `docs/MIGRATIONS.md` §5.4)
- **Service** : `backend/app/services/knowledge_service.py` (runtime query)
- **Utilisateurs** : tous les 14 agents + Supervisor

### 3.3 — Pas de RAG pour les prix structurés

**Important** : le **pricing** n'utilise **pas** ce RAG directement — il utilise `data/prix/*.json` (structuré, lookup direct). Voir `docs/PRICING-ENGINE.md` §4.

Le RAG est utilisé pour :
- Explications techniques (DTU, normes, pièges)
- Réponses questions métier
- Validation cohérence (Agent Devis vérifie contraintes techniques)
- Coach Business (contexte comparatif)
- Support F136 (questions fonctionnelles)

---

## 4. PIPELINE D'INGESTION

### 4.1 — Déclencheurs

L'ingestion est lancée :
- **Au build CI** : après merge de modifications sur `docs/metier/*.md` ou `docs/METIER.md`
- **Manuellement** via `python scripts/ingest_knowledge.py`
- **Automatiquement** nightly pour re-vérifier cohérence (cheap si pas de change)

### 4.2 — Étapes

```python
# scripts/ingest_knowledge.py

async def ingest_knowledge_base():
    """Pipeline complet d'ingestion."""
    
    # 1. Parse sources
    sources = [
        *glob.glob("docs/metier/*.md"),
        "docs/METIER.md",
        "docs/FEATURES_COMPLETE.md",
        "docs/COACH-DISCLAIMER.md",
    ]
    
    chunks_to_upsert = []
    
    for source_file in sources:
        # 2. Load
        content = Path(source_file).read_text(encoding='utf-8')
        
        # 3. Chunk
        chunks = chunk_markdown(content, source_file)
        
        # 4. Enrich metadata
        for i, chunk in enumerate(chunks):
            chunk.metadata = extract_metadata(chunk, source_file)
            chunk.chunk_index = i
        
        chunks_to_upsert.extend(chunks)
    
    # 5. Embed (batch)
    print(f"Embedding {len(chunks_to_upsert)} chunks...")
    embeddings = await embed_batch([c.text for c in chunks_to_upsert])
    for chunk, emb in zip(chunks_to_upsert, embeddings):
        chunk.embedding = emb
    
    # 6. Upsert (delete existing for these sources, insert fresh)
    await db.execute("DELETE FROM knowledge_chunks WHERE source_file = ANY($1)", sources)
    await db.copy_records_to_table("knowledge_chunks", records=chunks_to_upsert)
    
    # 7. Update version
    await db.execute(
        "INSERT INTO knowledge_versions (version, ingested_at, total_chunks) VALUES ($1, $2, $3)",
        generate_version(), datetime.now(), len(chunks_to_upsert)
    )
    
    print(f"✅ Ingestion OK : {len(chunks_to_upsert)} chunks indexés")
```

### 4.3 — Invocation CI

**Fichier** : `.github/workflows/knowledge-ingest.yml`

```yaml
name: Knowledge Base Ingestion
on:
  push:
    branches: [main]
    paths:
      - 'docs/metier/*.md'
      - 'docs/METIER.md'
  workflow_dispatch:

jobs:
  ingest:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - name: Install deps
        run: pip install -r backend/requirements.txt
      - name: Run ingestion
        env:
          SUPABASE_DB_URL: ${{ secrets.SUPABASE_DB_URL_PROD }}
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
        run: python scripts/ingest_knowledge.py
      - name: Notify Slack
        if: always()
        uses: slackapi/slack-github-action@v1
        with:
          channel-id: 'deploys'
          slack-message: "Knowledge ingestion: ${{ job.status }}"
```

### 4.4 — Validation post-ingestion

Après chaque ingestion :
- Sanity check : `SELECT COUNT(*) FROM knowledge_chunks` > 1000
- Test queries standards : "TVA 5.5%", "DTU 52.2", "prix pose baignoire" doivent retourner résultats pertinents
- Alerte Sentry si une source est absente

---

## 5. STRATÉGIE DE CHUNKING

### 5.1 — Paramètres

- **Taille cible** : ~500 tokens par chunk (~2000 caractères français)
- **Overlap** : ~100 tokens (~400 caractères) entre chunks adjacents
- **Préservation structure** : ne coupe pas une section au milieu si possible

### 5.2 — Respect sémantique

Le chunker respecte :
- **Headers markdown** (`## Section`) comme bornes prioritaires
- **Postes complets** (un chunk = un poste BTP complet ou un groupe cohérent)
- **Tableaux** gardés intacts si possible
- **Listes** gardées intactes si < 500 tokens

### 5.3 — Implémentation

```python
from langchain.text_splitter import MarkdownHeaderTextSplitter, RecursiveCharacterTextSplitter

def chunk_markdown(content: str, source_file: str) -> list[Chunk]:
    # 1. Split sur headers
    headers_to_split_on = [
        ("#", "h1"),
        ("##", "h2"),
        ("###", "h3"),
        ("####", "h4"),
    ]
    md_splitter = MarkdownHeaderTextSplitter(headers_to_split_on=headers_to_split_on)
    header_chunks = md_splitter.split_text(content)
    
    # 2. Split si chunk trop gros
    text_splitter = RecursiveCharacterTextSplitter(
        chunk_size=2000,
        chunk_overlap=400,
        separators=["\n\n", "\n", ". ", " ", ""],
    )
    
    final_chunks = []
    for hc in header_chunks:
        if len(hc.page_content) > 2500:
            sub_chunks = text_splitter.split_text(hc.page_content)
            for sc in sub_chunks:
                final_chunks.append(Chunk(
                    text=sc,
                    source_file=source_file,
                    headers=hc.metadata,
                ))
        else:
            final_chunks.append(Chunk(
                text=hc.page_content,
                source_file=source_file,
                headers=hc.metadata,
            ))
    
    return final_chunks
```

### 5.4 — Cas spéciaux

**Chunks trop courts** (<100 tokens) : mergés avec le chunk suivant (sauf si différent métier/section majeure).

**Chunks avec tableau** : intact jusqu'à 1000 tokens, sinon split par sous-catégorie.

**Listes numérotées** (erreurs terrain) : 1 erreur = 1 chunk (chunks courts mais sémantiquement cohérents).

---

## 6. EMBEDDINGS

### 6.1 — Choix du modèle

**Modèle principal** : **OpenAI `text-embedding-3-small`**
- Dim : 1536
- Prix : $0.02 / 1M tokens
- Multilingue (109 langues)
- Qualité excellent rapport prix/perf

**Alternative** : **`text-embedding-3-large`** (dim 3072)
- 2x plus cher
- ~2% meilleur qualité
- Pas justifié pour V1

**Fallback** : **Cohere embed-multilingual-v3.0** si OpenAI down.

### 6.2 — Configuration

```python
# backend/app/services/embedding_service.py

class EmbeddingService:
    def __init__(self, openai_client):
        self.client = openai_client
        self.model = "text-embedding-3-small"
        self.dim = 1536
    
    async def embed(self, text: str) -> list[float]:
        response = await self.client.embeddings.create(
            model=self.model,
            input=text,
            encoding_format="float",
        )
        return response.data[0].embedding
    
    async def embed_batch(self, texts: list[str]) -> list[list[float]]:
        # OpenAI permet batch jusqu'à 2048 inputs
        batches = [texts[i:i+100] for i in range(0, len(texts), 100)]
        all_embeddings = []
        for batch in batches:
            response = await self.client.embeddings.create(
                model=self.model,
                input=batch,
                encoding_format="float",
            )
            all_embeddings.extend([d.embedding for d in response.data])
        return all_embeddings
```

### 6.3 — Coûts estimés

**Ingestion initiale** :
- 8819 lignes × ~30 tokens/ligne moy = ~265K tokens total
- Chunking avec overlap → ~330K tokens embeddings
- Coût : ~$0.01 par ingestion complète

**Re-ingestion mensuelle** : $0.01-0.05

**Queries runtime** :
- ~10K queries/mois × 50 tokens = 500K tokens
- Coût : ~$0.01/mois

**TOTAL embeddings** : **< $1/mois** en phase launch, négligeable.

### 6.4 — Préprocessing avant embedding

```python
def preprocess_for_embedding(text: str) -> str:
    # 1. Normaliser les abréviations BTP
    replacements = {
        'SDB': 'salle de bain',
        'WC': 'toilettes',
        'RGE': 'Reconnu Garant Environnement',
        'TVA': 'taxe valeur ajoutée TVA',
        'DTU': 'Document Technique Unifié DTU',
        # ... pas remplacer tout, juste les très rares
    }
    # Garde l'acronyme ET ajoute forme complète pour multi-match
    # "SDB 8m²" → "SDB salle de bain 8m²"
    
    # 2. Normaliser unicode (accents corrects)
    import unicodedata
    text = unicodedata.normalize('NFC', text)
    
    # 3. Trim whitespace
    text = ' '.join(text.split())
    
    return text
```

---

## 7. STOCKAGE PGVECTOR

### 7.1 — Schéma

Voir `docs/MIGRATIONS.md` §5.4 (migration `019_create_knowledge_base.sql`) :

```sql
CREATE EXTENSION IF NOT EXISTS vector;

CREATE TABLE IF NOT EXISTS knowledge_chunks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    source_file TEXT NOT NULL,
    chunk_index INTEGER NOT NULL,
    chunk_text TEXT NOT NULL,
    embedding vector(1536),
    metadata JSONB,
    created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_knowledge_embedding
    ON knowledge_chunks
    USING ivfflat (embedding vector_cosine_ops)
    WITH (lists = 100);

CREATE INDEX idx_knowledge_source ON knowledge_chunks(source_file);
CREATE INDEX idx_knowledge_metadata_metier ON knowledge_chunks((metadata->>'metier'));
```

### 7.2 — Choix index ivfflat

**ivfflat vs HNSW** :
- ivfflat : plus simple, rapide pour <1M vecteurs (notre cas ~3000-5000 chunks)
- HNSW : meilleur pour grandes tailles, plus gourmand en RAM

Pour STRUCTORAI V1 : **ivfflat avec 100 lists** suffit largement.

### 7.3 — Paramètre `lists`

Règle : `lists ≈ sqrt(N)` où N = nombre de chunks.
- 5000 chunks → ~70 lists → on met **100** (safe)
- 20000 chunks → ~140 lists → augmenter

### 7.4 — Table auxiliaire `knowledge_versions`

```sql
CREATE TABLE knowledge_versions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    version TEXT UNIQUE NOT NULL,        -- 'v1.0.0', 'v1.1.0',...
    ingested_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    total_chunks INTEGER NOT NULL,
    source_hashes JSONB,                  -- { 'plomberie.md': 'sha256', ... }
    notes TEXT
);
```

Permet de tracker les versions de la knowledge base.

### 7.5 — Pas de RLS

La table `knowledge_chunks` est **publique** (tous les utilisateurs accèdent à la même base). Pas d'`organization_id`, pas de RLS activée.

Exception : les chunks propres à un pays (country_code) sont filtrables (voir §19).

---

## 8. QUERY PATTERNS

### 8.1 — Query basique

```python
async def search_knowledge(
    query: str,
    metier: Optional[str] = None,
    top_k: int = 5,
    min_score: float = 0.6,
) -> list[KnowledgeChunk]:
    # 1. Preprocessing
    query_processed = preprocess_for_embedding(query)
    
    # 2. Embedding
    query_emb = await embedding_service.embed(query_processed)
    
    # 3. SQL search
    conditions = []
    params = [query_emb, top_k]
    
    if metier:
        conditions.append("metadata->>'metier' = $3")
        params.append(metier)
    
    where_clause = "WHERE " + " AND ".join(conditions) if conditions else ""
    
    sql = f"""
    SELECT 
        id, source_file, chunk_text, metadata,
        1 - (embedding <=> $1) AS similarity
    FROM knowledge_chunks
    {where_clause}
    ORDER BY embedding <=> $1
    LIMIT $2
    """
    
    results = await db.fetch(sql, *params)
    
    # 4. Filter min_score
    return [
        KnowledgeChunk(**row)
        for row in results
        if row['similarity'] >= min_score
    ]
```

### 8.2 — Query avec expansion synonymes

```python
async def search_with_expansion(query: str, **kwargs) -> list[KnowledgeChunk]:
    # Expand avec synonymes BTP connus
    expanded = expand_btp_synonyms(query)
    # Ex: "SDB" → "SDB salle de bain bathroom"
    
    return await search_knowledge(expanded, **kwargs)


def expand_btp_synonyms(query: str) -> str:
    SYNONYMS = {
        'sdb': ['salle de bain', 'bathroom'],
        'wc': ['toilettes', 'sanitaires'],
        'chauffe-eau': ['ballon eau chaude', 'ECS'],
        'ite': ['isolation thermique extérieure'],
        'iti': ['isolation thermique intérieure'],
        # ... liste métier
    }
    
    query_lower = query.lower()
    expansions = []
    for term, syns in SYNONYMS.items():
        if term in query_lower:
            expansions.extend(syns)
    
    if expansions:
        return f"{query} {' '.join(expansions)}"
    return query
```

### 8.3 — Query multi-métier

```python
# Quand plusieurs métiers potentiels (ex: "rénovation SDB")
results_plomberie = await search_knowledge(query, metier="plomberie", top_k=3)
results_carrelage = await search_knowledge(query, metier="carrelage", top_k=3)
results_peinture = await search_knowledge(query, metier="peinture", top_k=2)

# Merge + dedupe + rerank global
```

### 8.4 — Query contextuelle (chain-of-thought)

L'agent peut faire plusieurs queries successives :

```python
# 1. Query initiale
results = await search_knowledge("DTU carrelage", top_k=3)
# → chunks sur DTU 52.2

# 2. Query de précision suite
if "DTU 52.2" in results[0].text:
    precise = await search_knowledge("joints de dilatation DTU 52.2", top_k=2)
    # → détail joints

# 3. Answer avec context combiné
```

---

## 9. HYBRID SEARCH (SEMANTIC + BM25)

### 9.1 — Pourquoi hybrid

Semantic search (embeddings) est excellent pour :
- Requêtes floues ("comment faire...")
- Synonymes, reformulations

BM25 (keyword-based) est excellent pour :
- Références exactes ("DTU 52.2", "Art. R.111-1")
- Codes, numéros
- Mots techniques spécifiques

**Hybrid combine les deux** → meilleurs résultats.

### 9.2 — Implémentation PostgreSQL FTS

PostgreSQL a du Full-Text Search natif. Activer FTS sur `knowledge_chunks` :

```sql
-- Ajouter colonne tsvector générée
ALTER TABLE knowledge_chunks
ADD COLUMN search_tsv tsvector
GENERATED ALWAYS AS (to_tsvector('french', chunk_text)) STORED;

CREATE INDEX idx_knowledge_fts ON knowledge_chunks USING GIN(search_tsv);
```

### 9.3 — Query hybride

```python
async def hybrid_search(query: str, top_k: int = 5) -> list[KnowledgeChunk]:
    # 1. Semantic search
    query_emb = await embedding_service.embed(query)
    
    # 2. BM25 keywords (FTS PostgreSQL)
    # 3. Reciprocal Rank Fusion
    
    sql = """
    WITH semantic AS (
        SELECT id, chunk_text, metadata,
               1 - (embedding <=> $1) AS sem_score,
               ROW_NUMBER() OVER (ORDER BY embedding <=> $1) AS sem_rank
        FROM knowledge_chunks
        ORDER BY embedding <=> $1
        LIMIT 20
    ),
    keyword AS (
        SELECT id, chunk_text, metadata,
               ts_rank(search_tsv, plainto_tsquery('french', $2)) AS kw_score,
               ROW_NUMBER() OVER (ORDER BY ts_rank(search_tsv, plainto_tsquery('french', $2)) DESC) AS kw_rank
        FROM knowledge_chunks
        WHERE search_tsv @@ plainto_tsquery('french', $2)
        LIMIT 20
    ),
    fused AS (
        SELECT
            COALESCE(s.id, k.id) AS id,
            COALESCE(s.chunk_text, k.chunk_text) AS chunk_text,
            COALESCE(s.metadata, k.metadata) AS metadata,
            -- Reciprocal Rank Fusion
            (1.0 / (60 + COALESCE(s.sem_rank, 100))) + (1.0 / (60 + COALESCE(k.kw_rank, 100))) AS fused_score
        FROM semantic s
        FULL OUTER JOIN keyword k ON s.id = k.id
    )
    SELECT * FROM fused
    ORDER BY fused_score DESC
    LIMIT $3
    """
    
    return await db.fetch(sql, query_emb, query, top_k)
```

### 9.4 — Quand activer hybrid

- **Queries courtes avec codes** : "DTU 52.2", "Art. L.241-1" → hybrid strongly recommended
- **Queries longues descriptives** : "comment poser un carrelage 40x40" → semantic suffit
- Par défaut : hybrid

---

## 10. RERANKING

### 10.1 — Pourquoi reranker

Le top-20 semantic + BM25 n'est pas forcément ordonné par pertinence réelle. Un **reranker** neural raffine le classement.

### 10.2 — Modèle

**Cohere rerank-multilingual-v3.0** :
- Coût : ~$0.002 par 1000 queries
- Latence : ~100ms
- Qualité : +20-30% précision sur top-5

**Alternative** : Cross-encoder HuggingFace (self-hosted, gratuit mais >500ms latence)

### 10.3 — Intégration

```python
import cohere

cohere_client = cohere.Client(api_key=settings.COHERE_API_KEY)

async def rerank(query: str, documents: list[KnowledgeChunk], top_n: int = 5) -> list[KnowledgeChunk]:
    if len(documents) <= top_n:
        return documents
    
    response = cohere_client.rerank(
        model="rerank-multilingual-v3.0",
        query=query,
        documents=[d.chunk_text for d in documents],
        top_n=top_n,
    )
    
    # Reorder selon rerank
    return [documents[r.index] for r in response.results]
```

### 10.4 — Quand reranker

- **Toujours** si top-5 est retourné à un LLM (coût acceptable)
- **Jamais** si top-1 suffit (ex: recherche exacte DTU)
- **Optionnel** pour Agent Coach (contexte long, reranking utile)

---

## 11. RELEVANCE SCORING

### 11.1 — Score par résultat

Chaque chunk retourné a un score composite :

```python
@dataclass
class KnowledgeChunk:
    id: UUID
    source_file: str
    chunk_text: str
    metadata: dict
    semantic_score: float       # 0-1 (cosine similarity)
    keyword_score: Optional[float]  # BM25 rank-based
    rerank_score: Optional[float]   # Cohere rerank
    final_score: float          # Composite weighted
```

### 11.2 — Composite score

```python
def compute_final_score(chunk: KnowledgeChunk) -> float:
    # Base semantic
    score = chunk.semantic_score * 0.6
    
    # Keyword bonus
    if chunk.keyword_score:
        score += chunk.keyword_score * 0.2
    
    # Rerank bonus
    if chunk.rerank_score:
        score += chunk.rerank_score * 0.2
    
    # Metadata boosts
    if chunk.metadata.get('importance') == 'high':
        score += 0.1
    
    return min(score, 1.0)
```

### 11.3 — Seuils

- **> 0.8** : excellente match
- **0.6 - 0.8** : match raisonnable (à afficher)
- **< 0.6** : probablement pas pertinent (à filtrer)
- **< 0.4** : aucun résultat pertinent → agent doit dire "je ne sais pas"

### 11.4 — Retour à l'artisan

Quand l'agent cite une source :

> *"D'après les référentiels plomberie (source : plomberie.md), les tuyaux Ø16 sont pour l'eau froide. Pour alimenter une baignoire, il faut du Ø20 minimum afin d'éviter les pertes de pression."*

---

## 12. MÉTADONNÉES PAR CHUNK

### 12.1 — Schéma metadata

```json
{
  "metier": "plomberie",
  "categorie": "alimentation",
  "sous_categorie": "tuyauterie_cuivre",
  "country_code": "FR",
  "language": "fr",
  "section_header_h2": "Section 3 — Prix par poste",
  "section_header_h3": "3.1 — Alimentation",
  "section_header_h4": "Tuyauterie cuivre Ø20",
  "contains_price": true,
  "contains_dtu": false,
  "contains_tva_rule": false,
  "contains_piege_terrain": false,
  "dtu_refs": ["DTU 60.1"],
  "norme_refs": ["NF EN 1057"],
  "keywords": ["tuyau", "cuivre", "alimentation", "plomberie"],
  "importance": "normal",    // 'low', 'normal', 'high'
  "last_updated": "2026-04-01"
}
```

### 12.2 — Extraction automatique

```python
def extract_metadata(chunk: Chunk, source_file: str) -> dict:
    text = chunk.text.lower()
    
    metier = extract_metier_from_filename(source_file)  # 'plomberie.md' → 'plomberie'
    
    # Détection contenu
    contains_price = bool(re.search(r'\d+\s*(€|€/m²|€/ml)', chunk.text))
    contains_dtu = bool(re.search(r'DTU\s+\d+', chunk.text))
    contains_tva_rule = 'tva' in text and any(r in text for r in ['5.5', '10%', '20%'])
    contains_piege_terrain = any(k in text for k in ['piège', 'erreur', 'attention', 'vigilance'])
    
    # Extraction refs
    dtu_refs = re.findall(r'DTU\s+(\d+\.?\d*)', chunk.text)
    norme_refs = re.findall(r'NF\s+[A-Z]+\s+\d+', chunk.text)
    
    # Keywords
    keywords = extract_keywords_tfidf(chunk.text, top_k=10)
    
    return {
        'metier': metier,
        'section_header_h2': chunk.headers.get('h2'),
        'section_header_h3': chunk.headers.get('h3'),
        'section_header_h4': chunk.headers.get('h4'),
        'contains_price': contains_price,
        'contains_dtu': contains_dtu,
        'contains_tva_rule': contains_tva_rule,
        'contains_piege_terrain': contains_piege_terrain,
        'dtu_refs': dtu_refs,
        'norme_refs': norme_refs,
        'keywords': keywords,
        'country_code': 'FR',
        'language': 'fr',
        'last_updated': datetime.now().strftime('%Y-%m-%d'),
    }
```

### 12.3 — Utilisation pour filtrage

```python
# Recherche uniquement chunks avec TVA rules
await search_knowledge(
    query="TVA rénovation",
    metadata_filter={'contains_tva_rule': True}
)
```

---

## 13. MULTI-LANGUE

### 13.1 — Knowledge base en français

Les 11 fichiers métiers + METIER.md sont **en français**. On ne traduit **pas** la knowledge base source.

### 13.2 — Queries cross-langue

Les embeddings OpenAI `text-embedding-3-small` sont multilingues :
- User TR demande "fayans döşeme nasıl yapılır"
- Embedding capture sens
- Match avec chunk français "pose carrelage..."
- Réponse traduite par Claude au moment du LLM call

### 13.3 — Bonus : dictionnaire terminologie BTP

Voir `docs/I18N.md` §11 : `data/btp_terminology_{locale}.json` peut enrichir les queries avec traduction préalable :

```python
async def search_multilingual(query: str, user_locale: str, **kwargs):
    if user_locale != 'fr':
        # Traduire termes techniques vers FR avant embedding
        query_fr = translate_btp_terms(query, source=user_locale, target='fr')
        # "fayans döşeme" → "pose de carrelage"
        return await search_knowledge(query_fr, **kwargs)
    return await search_knowledge(query, **kwargs)
```

### 13.4 — Pas de duplication

**On ne duplique PAS** la knowledge base dans les 6 langues (coût embeddings ×6, maintenance ×6). Le modèle multilingue suffit.

### 13.5 — Exception : mentions obligatoires

Les **mentions obligatoires** FR/EN/TR/PT/AR/ES sont en DB (table `mentions_obligatoires`), pas dans la knowledge base. Voir `docs/MIGRATIONS.md` §5.8.

---

## 14. INTÉGRATION PAR AGENT

### 14.1 — Agent Devis (primary user)

Usage : valider cohérence technique d'un devis.

```python
# Exemple : artisan crée un devis pose carrelage 40×40
chunks = await knowledge_service.search(
    "pose carrelage 40x40 DTU pièges",
    metier="carrelage",
    top_k=3
)

# Agent Devis ajoute en contexte au prompt Claude :
# "Références techniques : 
#  - DTU 52.2 applicable
#  - Joints de dilatation tous les 40 m² ou 8 m linéaires
#  - Piège fréquent : vérifier planéité, tolérance 5mm sur 2m"
```

### 14.2 — Agent Coach Business (F119)

Usage : informations sectorielles benchmark.

```python
chunks = await knowledge_service.search(
    "taux horaire moyen plombier France benchmark",
    top_k=5
)

# Combine avec benchmark prices (pricing_service) pour recommandations
```

### 14.3 — Agent Fiscalité

Usage : règles TVA, mentions, seuils fiscaux.

```python
chunks = await knowledge_service.search(
    "TVA 5.5% rénovation énergétique conditions RGE",
    source_file="docs/METIER.md",
    top_k=3
)
```

### 14.4 — Agent Support (F136)

Usage : répondre questions fonctionnelles.

```python
chunks = await knowledge_service.search(
    "comment supprimer mon compte",
    source_file="docs/FEATURES_COMPLETE.md",
    top_k=2
)
```

### 14.5 — Supervisor

Usage : routing intelligent.

```python
# Pour décider quel agent router
chunks = await knowledge_service.search(
    user_query,
    top_k=1,
    min_score=0.7
)
# Si chunk high-confidence → Supervisor sait que c'est du métier → route vers Devis
```

### 14.6 — Agents qui n'utilisent PAS le RAG

- Agent Relance : templates + Mem0 artisan (pas de RAG technique)
- Agent Planning : calendrier + heuristiques (pas technique)
- Agent Réputation : templates + avis (pas technique)
- Agent Prospection : CRM + Mem0 (pas technique)

---

## 15. SERVICE `KNOWLEDGE_SERVICE.PY`

### 15.1 — Structure

**Fichier** : `backend/app/services/knowledge_service.py`

```python
class KnowledgeService:
    def __init__(
        self,
        db: Database,
        embedding_service: EmbeddingService,
        reranker: Optional[Reranker] = None,
    ):
        self.db = db
        self.embedding = embedding_service
        self.reranker = reranker
    
    async def search(
        self,
        query: str,
        metier: Optional[str] = None,
        source_file: Optional[str] = None,
        top_k: int = 5,
        min_score: float = 0.6,
        use_hybrid: bool = True,
        use_rerank: bool = False,
        metadata_filter: Optional[dict] = None,
        user_locale: str = 'fr',
    ) -> list[KnowledgeChunk]:
        """Recherche dans la knowledge base."""
        # Cross-langue si besoin
        if user_locale != 'fr':
            query = translate_btp_terms(query, user_locale, 'fr')
        
        # Expansion synonymes
        query_expanded = expand_btp_synonyms(query)
        
        # Search
        if use_hybrid:
            results = await self._hybrid_search(query_expanded, top_k=top_k * 4)
        else:
            results = await self._semantic_search(query_expanded, top_k=top_k * 4)
        
        # Filter
        if metier:
            results = [r for r in results if r.metadata.get('metier') == metier]
        if source_file:
            results = [r for r in results if r.source_file == source_file]
        if metadata_filter:
            results = self._apply_metadata_filter(results, metadata_filter)
        results = [r for r in results if r.semantic_score >= min_score]
        
        # Rerank
        if use_rerank and self.reranker and len(results) > top_k:
            results = await self.reranker.rerank(query, results, top_n=top_k)
        else:
            results = results[:top_k]
        
        return results
    
    async def get_chunk(self, chunk_id: UUID) -> KnowledgeChunk:
        """Récupère chunk par ID (pour citations)."""
        row = await self.db.fetchrow(
            "SELECT * FROM knowledge_chunks WHERE id = $1", chunk_id
        )
        return KnowledgeChunk(**row)
    
    async def get_related_chunks(
        self, chunk_id: UUID, top_k: int = 3
    ) -> list[KnowledgeChunk]:
        """Chunks similaires à un chunk donné (même fichier, sections voisines)."""
        # ...
```

### 15.2 — Format de retour

```python
@dataclass
class KnowledgeChunk:
    id: UUID
    source_file: str
    chunk_index: int
    chunk_text: str
    metadata: dict
    semantic_score: float
    keyword_score: Optional[float] = None
    rerank_score: Optional[float] = None
    
    @property
    def final_score(self) -> float:
        return compute_final_score(self)
    
    @property
    def citation(self) -> str:
        """Format citation pour afficher à l'artisan."""
        metier = self.metadata.get('metier', 'général')
        section = self.metadata.get('section_header_h3', '')
        return f"{self.source_file} / {metier} / {section}"
```

---

## 16. ENRICHISSEMENT CONTINU

### 16.1 — Sources d'enrichissement

**Automatique** :
- Web search fallback (pricing_service §6) → prix trouvés ajoutés à cache, pas directement au RAG
- Retours d'artisans : questions non-répondues → issues à traiter manuellement

**Manuel** :
- Review trimestrielle : identifier manques
- Ajout de nouvelles sections dans les fichiers métier
- Nouvelles normes/DTU (chaque année)

### 16.2 — Workflow enrichissement

```
1. Identification manque (artisan demande "X" → score <0.4 → fallback dégradé)
2. Ticket backlog
3. Sprint dédié : Fabrice ou expert métier rédige section
4. PR sur docs/metier/[metier].md
5. Merge → CI → ingestion automatique
6. Test retrograde : mêmes queries donnent meilleur résultat
```

### 16.3 — Sélection de nouvelles sections

Priorisation selon :
- Volume de queries similaires loggées
- Impact business (TVA, fiscalité = prioritaire)
- Cohérence référentiels (si un métier est moins complet, enrichir)

### 16.4 — Budget enrichissement

- ~50€/mois budget rédacteur métier (quand >500 clients)
- ~$5/mois coûts embedding re-ingestion

---

## 17. PERFORMANCE ET CACHE

### 17.1 — Latences cibles

| Opération | Cible P95 |
|---|---|
| Embedding query | < 100ms |
| Semantic search (top-20) | < 50ms |
| Hybrid search | < 100ms |
| Reranking Cohere (top-20 → 5) | < 150ms |
| **Total search end-to-end** | **< 300ms** |

### 17.2 — Cache Redis

```python
async def search_with_cache(query: str, **kwargs) -> list[KnowledgeChunk]:
    cache_key = f"knowledge:search:{hash_query(query, kwargs)}"
    
    cached = await redis.get(cache_key)
    if cached:
        return deserialize(cached)
    
    results = await knowledge_service.search(query, **kwargs)
    
    # Cache 1h (knowledge change rarement)
    await redis.set(cache_key, serialize(results), ex=3600)
    
    return results
```

### 17.3 — Pre-warm

Au démarrage backend, pré-warm cache avec queries fréquentes :
- "TVA BTP"
- "DTU"
- "prix pose carrelage"
- Top 50 queries historiques

### 17.4 — Index memory

ivfflat en RAM Postgres : consommation ~100 MB pour 5000 chunks 1536d.

Supabase Pro Instance : 4 GB RAM → largement OK.

---

## 18. ÉVALUATION QUALITÉ RAG

### 18.1 — Dataset de test

**Fichier** : `backend/tests/rag_eval_dataset.json`

100 queries typiques avec réponses attendues :

```json
[
  {
    "query": "TVA carrelage rénovation",
    "expected_chunks": ["docs/metier/carrelage.md§tva", "docs/METIER.md§tva_10"],
    "expected_facts": [
      "TVA 10% si rénovation et logement > 2 ans",
      "Attestation TVA 10% signée par client obligatoire"
    ]
  },
  {
    "query": "Quel diamètre pour alimentation baignoire ?",
    "expected_chunks": ["docs/metier/plomberie.md§alimentation"],
    "expected_facts": ["Ø20 minimum pour baignoire"]
  },
  // ... 98 autres
]
```

### 18.2 — Métriques

- **Recall@5** : % queries où le chunk attendu est dans top 5
- **Precision@5** : % top 5 chunks qui sont pertinents
- **MRR (Mean Reciprocal Rank)** : qualité du ranking

**Cibles** :
- Recall@5 > 90%
- Precision@5 > 75%
- MRR > 0.7

### 18.3 — Regression testing

Script `scripts/eval_rag.py` lancé à chaque ingestion :

```bash
python scripts/eval_rag.py
# Output:
# Recall@5: 92.5%
# Precision@5: 78.3%
# MRR: 0.72
# ✅ All metrics above threshold
```

CI échoue si une métrique drop >5%.

---

## 19. MULTI-PAYS F135

Voir `docs/SINGLE_SOURCE_OF_TRUTH.md` §14.

### 19.1 — Architecture pays

Quand STRUCTORAI lancera d'autres pays :
- Ajout `docs/metier/{country_code}/*.md` par pays
- Ingestion spécifique par pays
- Filtrage query par `country_code`

### 19.2 — Différences techniques

Certaines règles sont **universelles** (DTU techniques partiels), d'autres **country-specific** (TVA, mentions).

Structure finale :
```
docs/metier/
├── plomberie.md              # FR (base launch)
├── electricite.md
├── ...
├── BE/
│   ├── plomberie.md          # Spécificités BE (TVA 6/21, normes NBN)
│   └── ...
├── CH/
│   └── ...
```

### 19.3 — Métadonnée `country_code`

Chaque chunk taggé avec son country_code :
```json
{"country_code": "FR"}        # Valable FR uniquement
{"country_code": "UNIVERSAL"} # Technique pur, valable partout
```

Filtrage :
```python
await search_knowledge(
    query=...,
    country_code=user.organization.country_code  # ou 'UNIVERSAL'
)
```

### 19.4 — Launch V1 : FR uniquement

V1 = uniquement FR. Pas de colonne country_code sur knowledge_chunks au départ (ajout via migration future).

---

## 20. ANTI-PATTERNS

### 20.1 — À NE JAMAIS FAIRE

1. ❌ **Hallucination** : agent invente une règle BTP → toujours citer source
2. ❌ **Chunks trop gros** (> 2000 tokens) : noyage contexte
3. ❌ **Chunks trop petits** (<100 tokens) : pas de contexte utile
4. ❌ **Pas de métadonnées** : impossible de filtrer par métier
5. ❌ **Ignorer le fallback** : score <0.6 → dire "je ne sais pas" plutôt qu'inventer
6. ❌ **Embedding sans normalisation** : résultats sous-optimaux
7. ❌ **Re-ingestion partielle** : laisse des orphelins
8. ❌ **Cache permanent** : données obsolètes
9. ❌ **Index sans update** : perf dégradée après insertions massives
10. ❌ **Oublier `source_file` dans citation** : artisan ne peut pas vérifier

### 20.2 — À FAIRE

1. ✅ Cite toujours la source
2. ✅ Scores minimum pour filtrer
3. ✅ Hybrid search quand pertinent
4. ✅ Reranking pour top 5
5. ✅ Metadata riches pour filtrage
6. ✅ Re-ingest complet à chaque update
7. ✅ Tests de régression post-ingest
8. ✅ Monitoring des "not found"
9. ✅ Preprocessing queries (synonymes BTP)
10. ✅ Cache Redis sur queries fréquentes

---

## 21. MONITORING

### 21.1 — Métriques à suivre

- **Queries/jour** par agent
- **Avg semantic score** des top-1 résultats
- **Ratio "not found"** (score <0.4) par query
- **Latence P95** query
- **Cache hit rate**
- **Coûts embeddings mensuels**

### 21.2 — Alerte qualité

Si ratio "not found" > 10% sur 24h → alerte : knowledge base probablement incomplète.

### 21.3 — Dashboard admin

`/admin/knowledge` :
- Volume chunks par source
- Top queries dernière semaine
- Queries avec faible score (cohorte à enrichir)
- Sources les plus cited / jamais cited

---

## 22. MIGRATION ET VERSIONING

### 22.1 — Versions knowledge base

Chaque update majeur → nouvelle version :
- `v1.0.0` (launch juin 2026)
- `v1.1.0` (ajout section nouvelle norme)
- `v1.2.0` (trimestriel)

### 22.2 — Rollback

Si ingestion produit régression → rollback via restore de la table précédente :

```sql
-- Sauvegarde avant ingest
CREATE TABLE knowledge_chunks_backup AS SELECT * FROM knowledge_chunks;

-- Si problème
TRUNCATE knowledge_chunks;
INSERT INTO knowledge_chunks SELECT * FROM knowledge_chunks_backup;
```

### 22.3 — Zero-downtime update

Pour éviter interruption service :
1. Créer nouvelle table `knowledge_chunks_v2`
2. Ingester dedans
3. Tester
4. Swap noms : `knowledge_chunks` → `knowledge_chunks_old`, `knowledge_chunks_v2` → `knowledge_chunks`
5. Drop `knowledge_chunks_old` après 7 jours

---

## 23. RÉFÉRENCES CROISÉES

| Fichier | Contenu |
|---|---|
| `docs/METIER.md` | Constitution BTP (source knowledge) |
| **`docs/metier/*.md`** (11 fichiers) | **8819 lignes data terrain** |
| `docs/PRICING-ENGINE.md` §15 | Référentiels prix (complémentaire) |
| `docs/AGENTS.md` | Utilisation par agents |
| `docs/API.md` | Pas d'endpoint public (interne aux agents) |
| `docs/MIGRATIONS.md` §5.4 | Schéma `knowledge_chunks` + pgvector |
| `docs/ARCH.md` §4 | knowledge_service dans services |
| `docs/ENV.md` §7 | `OPENAI_API_KEY` pour embeddings |
| `docs/I18N.md` §11 | Terminologie BTP multi-langue |
| `docs/FEATURES_COMPLETE.md` | Contenu indexé |
| `docs/COACH-DISCLAIMER.md` | Indexé pour Coach Business |
| `docs/AI-ACT-COMPLIANCE.md` | Indexé pour Agent Support |
| `CLAUDE.md` §Knowledge Base (RAG) | Vue d'ensemble |
| `BUILD_PLAN.md` | `knowledge_service.py` + migration 019 |
| `COUT_REEL.md` | Coûts embeddings (<$1/mois) |
| `ANALYSE_CONCURRENCE.md` | 8819 lignes = différenciation |

### 23.1 — Références externes

- **pgvector** : https://github.com/pgvector/pgvector
- **OpenAI Embeddings** : https://platform.openai.com/docs/guides/embeddings
- **Cohere Rerank** : https://docs.cohere.com/docs/reranking
- **PostgreSQL FTS** : https://www.postgresql.org/docs/current/textsearch.html
- **LangChain chunking** : https://python.langchain.com/docs/modules/data_connection/document_transformers/

---

> **Ce fichier est la spec complète de la knowledge base RAG BTP.**
> **8819 lignes data terrain** = différenciation concurrentielle majeure.
> **Règle d'or n°1** : jamais d'hallucination, toujours citer source.
> **Règle d'or n°2** : hybrid search (semantic + BM25) pour meilleurs résultats.
> **Règle d'or n°3** : reranking quand top 5 est retourné à un LLM.
> **Performance cible** : <300ms end-to-end query.
