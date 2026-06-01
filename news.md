# Actualité technique des bases de données — 2026-06-01

---

## 1. PostgreSQL 18 — ce qui change vraiment

PostgreSQL 18 a été publié en 2025. La refonte majeure concerne le sous-système d'I/O : le nouveau moteur **asynchronous I/O (AIO)** permet d'émettre plusieurs requêtes disque en parallèle au lieu d'attendre chaque opération. Les gains mesurés atteignent **2× à 3× sur les scans séquentiels**, les bitmap heap scans et VACUUM.

| Nouveauté | Impact |
|---|---|
| **Async I/O (AIO)** | ×2–3 sur sequential scans et VACUUM |
| **Skip scan B-tree** | Index multi-colonnes utilisables même si la 1ʳᵉ colonne n'est pas filtrée |
| **`uuidv7()`** | UUIDs ordonnés temporellement nativement en SQL |
| **Colonnes générées virtuelles** | Calculées à la lecture, sans stockage physique (nouveau défaut) |
| **`RETURNING OLD / NEW`** | Clauses RETURNING dans INSERT, UPDATE, DELETE, MERGE |
| **Authentification OAuth 2.0** | Intégration native avec les fournisseurs d'identité modernes |
| **Temporal constraints** | Contraintes PRIMARY KEY / UNIQUE / FK sur des plages de temps |
| **Statistiques préservées à l'upgrade** | Plus de dégradation post-`pg_upgrade` en attendant `ANALYZE` |

---

## 2. SQL Server 2025 — le premier SGBD « AI-native »

Microsoft positionne SQL Server 2025 comme le premier moteur où l'IA fait partie intégrante du moteur, pas une extension périphérique.

### DiskANN — vecteurs à l'échelle du milliard

L'algorithme **DiskANN** (Microsoft Research) est intégré nativement via `CREATE VECTOR INDEX`. Contrairement aux approches HNSW purement en mémoire, DiskANN exploite le stockage NVMe haute vitesse, permettant des recherches ANN sur **milliards de vecteurs** sans RAM proportionnelle.

```sql
-- Exemple de création d'index vectoriel DiskANN
CREATE VECTOR INDEX idx_embeddings ON documents(embedding)
WITH (ALGORITHM = DISKANN, METRIC = COSINE);
```

### Modèles IA directement en T-SQL

SQL Server 2025 intègre un gestionnaire de modèles IA référençant Azure AI Foundry, Azure OpenAI, OpenAI, Ollama via REST API — sans quitter le moteur SQL. Intégration native avec LangChain, Semantic Kernel et Entity Framework Core.

### Autres apports

- **Intelligent Query Processing v4** : PSP Optimization étendu aux procédures stockées complexes.
- **Parité Azure Arc** : fonctionnalités identiques on-prem et cloud.

---

## 3. DuckDB 1.4 LTS « Andium » — OLAP embarqué mature

DuckDB 1.4.0 consolide la production-readiness avec des features longtemps attendues :

| Feature | Détail |
|---|---|
| **Chiffrement AES-256-GCM** | Fichiers `.duckdb` chiffrés nativement |
| **MERGE statement** | Upserts idiomatiques en SQL standard |
| **Iceberg writes** | Écriture native au format Apache Iceberg |
| **Spatial joins R-tree** | ×58 plus rapide pour les requêtes géospatiales |
| **CTE materialization** | Optimisation automatique des Common Table Expressions |

`pg_duckdb` 1.0 est sorti en parallèle, amenant les capacités analytiques vectorisées de DuckDB directement dans PostgreSQL sans changer de connexion.

---

## 4. Apache Iceberg + Apache Polaris — le lakehouse se standardise

### Apache Polaris — catalogue officiel Iceberg

Co-créé par Dremio et Snowflake, **Apache Polaris** a été diplômé comme **Apache Top-Level Project en février 2026** après 18 mois d'incubation avec Google, Microsoft, Confluent. Il implémente l'Iceberg REST Catalog spec et devient le plan de contrôle de référence pour les lakehouses multi-moteurs.

La **version 1.4 d'avril 2026** apporte :
- AWS STS Session Tag Customization
- Storage-Scoped Key Management
- Metrics Persistence

### Interopérabilité universelle

Spark, Flink, Trino, Dremio, StarRocks lisent et écrivent les mêmes tables Iceberg stockées sur S3/Azure Blob/GCS. L'ère des silos de formats propriétaires s'estompe.

---

## 5. Bases de données vectorielles — marché en hypercroissance

Le marché des bases de données vectorielles croît de **377 % en glissement annuel** (2025), porté par l'adoption massive du **RAG (Retrieval-Augmented Generation)** en entreprise. Il passe de 2,8 Mds$ en 2025 à 8,5 Mds$ attendus en 2028.

### État des forces en 2026

| Système | Position | Différenciateur clé |
|---|---|---|
| **Qdrant** | Leader cloud | Filtrage payload, quantification vectorielle avancée |
| **Pinecone** | Leader cloud managé | Simplicité opérationnelle, serverless |
| **Turbopuffer** | Nouvel entrant agressif | Stockage objet comme backend, coût × faible |
| **pgvector** (PG18) | Standard SQL | ACID + vector dans un seul système |
| **Milvus / Zilliz** | Échelle massive | GPU acceleration, milliards de vecteurs |
| **Chroma** | Dev/prototypage | Intégration LangChain/LlamaIndex triviale |

### Tendances techniques

- **Hybrid search BM25 + ANN** : combinaison systématique recherche sémantique + textuelle.
- **Matryoshka Embedding** : vecteurs tronquables permettant d'ajuster coût/précision dynamiquement.
- **Sparse + dense vectors** : SPLADE et autres modèles sparse pour améliorer le rappel.

---

## 6. La renaissance SQLite à l'edge

2026 marque la **maturité de production du SQLite distribué** avec trois plateformes arrivées simultanément :

| Plateforme | Latence | Particularité |
|---|---|---|
| **Turso (libSQL)** | ~625 µs (embedded replicas) | Architecture database-per-user, vecteur natif |
| **Cloudflare D1** | < 10 ms | SQLite au plus près des 300 PoPs Cloudflare |
| **Fly.io LiteFS** | < 1 ms régional | Réplication Raft sur SQLite standard |

**Architecture database-per-user** : Turso permet des milliers de bases de données isolées par tenant à coût marginal nul — pattern qui était économiquement infaisable avec les SGBD traditionnels.

**Convergence IA/edge** : Turso intègre la recherche vectorielle native (sans extension) pour les workloads RAG à la périphérie — chaque agent IA peut avoir sa propre base locale.

---

## 7. Bases de données agentiques — la prochaine frontière

Avec l'essor des agents IA autonomes, un nouveau pattern émerge : **chaque agent possède sa propre base de données** pour stocker mémoire, fichiers, état de tâches. Cela implique :

- **Stockage de mémoire long-terme** structuré (facts, preferences, history)
- **Coordination multi-agents** via des tables partagées à accès concurrent
- **Provenance des données** : traçabilité complète des décisions de l'agent

Les acteurs qui se positionnent : Turso (database-per-agent), LanceDB (multimodal + agents), et les extensions pgvector + pg_trgm de PostgreSQL pour les agents à état riche.

---

## 8. Récapitulatif des signaux forts — Juin 2026

| Axe | Signal | Maturité |
|---|---|---|
| **Performance I/O** | PostgreSQL 18 AIO (+200%) | Prod |
| **IA native en SGBD** | SQL Server 2025 DiskANN + T-SQL AI | Prod |
| **OLAP embarqué** | DuckDB 1.4 LTS + pg_duckdb | Prod |
| **Formats ouverts** | Apache Iceberg + Polaris TLP | Prod |
| **Vecteurs** | Turbopuffer, Qdrant, pgvector 0.8 | Prod |
| **Edge/SQLite** | Turso, D1, LiteFS | Prod |
| **Agents IA** | Database-per-agent, mémoire structurée | Émergent |

> **Résumé :** en juin 2026, les bases de données ne se distinguent plus par leur modèle de données mais par leur capacité à intégrer l'IA et à opérer à la périphérie. PostgreSQL 18 et SQL Server 2025 matérialisent cette convergence côté moteur ; DuckDB et Apache Iceberg la matérialisent côté analyse distribuée.

---

*Rapport rédigé le 2026-06-01 — Sources : release notes PostgreSQL 18, SQL Server 2025 Blog Microsoft, DuckDB 1.4 changelog, Apache Polaris TLP announcement (fév. 2026), Ailog RAG Vector DB Trends 2026, DEV.to SQLite Edge Production 2026.*

---

# Actualité technique des bases de données — 2026-05-19

---

## 1. Optimisation des requêtes et des moteurs

### Adaptive Query Execution (AQE) — généralisation

L'exécution adaptative des requêtes, popularisée par Apache Spark, s'est imposée dans les bases relationnelles classiques. PostgreSQL 17+ intègre désormais des mécanismes de réécriture de plan en cours d'exécution basés sur les statistiques réelles collectées à mi-chemin. Oracle et SQL Server raffinement leurs propres implémentations avec un retour d'information plus fin sur la cardinalité des jointures.

**Impact :** réduction des cas catastrophiques de mauvaise estimation de cardinalité sans intervention DBA.

### Vectorisation SIMD

DuckDB continue de définir l'état de l'art en matière de moteur vectorisé colonne par colonne avec exploitation intensive des instructions SIMD (AVX-512). Cette approche est adoptée par ClickHouse, CockroachDB et, plus récemment, par les extensions analytiques de PostgreSQL (`pg_analytics`, `pg_duckdb`).

### Auto-tuning des index

Les systèmes d'auto-indexing (Microsoft Azure SQL, Google AlloyDB) utilisent désormais des modèles de ML embarqués pour recommander — voire créer automatiquement — des index partiels, couvrants ou de type BRIN selon les patterns de charge observés. L'ère du DBA « gestionnaire d'index manuel » s'estompe.

---

## 2. Nouvelles fonctionnalités notables

### PostgreSQL 17 (sorti oct. 2024)

| Feature | Détail |
|---|---|
| `MERGE` étendu | Support de `MERGE … RETURNING` |
| Streaming logical replication | Réplication logique des grandes transactions sans blocage |
| `pg_upgrade` parallélisé | Migrations majeures drastiquement accélérées |
| Vacuum amélioré | Réduction de la contention sur les tables très actives |
| JSON_TABLE | Intégration native SQL/JSON (norme SQL:2016) |

### MySQL 9.x / HeatWave

Oracle a accéléré la cadence : MySQL 9 introduit les **vecteur columns** natives (`VECTOR` type) pour l'IA, un optimiseur de jointures revu, et l'intégration HeatWave (accélération analytique in-memory) désormais disponible on-prem via HeatWave Lakehouse.

### SQL Server 2025 (preview)

- **DiskANN intégré** : index vectoriel de haute performance basé sur l'algorithme DiskANN de Microsoft Research.
- **Intelligent Query Processing v4** : Parameter Sensitive Plan (PSP) optimization étendu aux procédures stockées complexes.
- **Azure Arc-enabled** : parité fonctionnelle totale entre on-prem et cloud.

### MongoDB 8.x

- **Queryable Encryption** améliorée : chiffrement côté client avec possibilité d'effectuer des opérations d'agrégation sur données chiffrées.
- **Time Series collections** : compression Gorilla/Delta-of-Delta, requêtes temporelles optimisées, fenêtres glissantes natives.
- **Atlas Stream Processing** : pipeline Kafka-like directement dans l'écosystème MongoDB.

---

## 3. Bases de données vectorielles — l'essor post-LLM

L'explosion des LLMs (Large Language Models) a propulsé les bases de données vectorielles au premier plan.

### Acteurs clés

| Système | Type | Points forts |
|---|---|---|
| **pgvector** (PostgreSQL) | Extension SQL | Intégration native, HNSW + IVFFlat |
| **Qdrant** | Natif vectoriel | Filtrage payload haute perf, quantification scalaire/produit |
| **Weaviate** | Hybride vect./graph | Multi-tenancy, modules ML embarqués |
| **Milvus / Zilliz** | Natif vectoriel | Echelle massive, GPU acceleration |
| **Chroma** | Embarqué | Dev-friendly, idéal prototypage RAG |

### Tendances

- **Hybrid search** : combinaison de la recherche vectorielle (ANN) et de la recherche plein-texte (BM25) pour de meilleurs résultats RAG.
- **Quantification des vecteurs** : Product Quantization (PQ), Scalar Quantization (SQ8) réduisent la mémoire requise de 4× à 32×.
- **Filtrage pré/post-index** : le filtrage pré-index (filtered HNSW) devient le standard pour éviter les faux positifs coûteux.

---

## 4. HTAP — Convergence OLTP / OLAP

Les architectures HTAP (Hybrid Transactional/Analytical Processing) mûrissent rapidement.

- **TiDB 8.x** : séparation physique TiKV (OLTP) / TiFlash (OLAP) avec synchronisation raft sub-seconde. Le planner décide automatiquement du moteur cible.
- **AlloyDB (Google)** : colonne store adaptatif activé automatiquement sans configuration, co-localisé avec le moteur PostgreSQL.
- **SingleStore (ex-MemSQL)** : support unifié row store + column store dans une même table avec politique de tiering automatique.

**Bénéfice majeur :** élimination des pipelines ETL/ELT pour les analyses temps réel sur données opérationnelles.

---

## 5. Stockage et architecture

### Séparation compute / storage

Le modèle désormais dominant dans le cloud (Aurora, Neon, CockroachDB Serverless, Spanner) découple le calcul du stockage :

- Scale-to-zero du compute en idle
- Snapshots et branching quasi-instantanés (copy-on-write)
- **Neon** popularise le concept de « branches de base de données » alignées sur les branches Git pour le dev/test

### Tiering automatique

Les bases comme CockroachDB et YugabyteDB implémentent du tiering automatique chaud/froid vers S3-compatible, avec des politiques basées sur la fréquence d'accès aux données.

### Format de fichiers ouverts

L'écosystème converge vers des formats ouverts :
- **Apache Iceberg** : devient le standard de facto pour les data lakehouses (Snowflake, Databricks, AWS, Google tous l'adoptent)
- **Apache Parquet + Delta Lake** : toujours très présents dans l'écosystème Spark/Databricks
- **Lance** (format de LanceDB) : optimisé pour les workloads vectoriels et multimodaux

---

## 6. Sécurité et conformité

### Chiffrement homomorphique et MPC

Des solutions comme **CipherMode** (acquis par Intel) et **Inpher** progressent vers un chiffrement qui permet de requêter des données sans jamais les déchiffrer côté serveur — encore limité en performance mais les cas d'usage réglementaires (santé, finance) tirent la demande.

### Row-Level Security (RLS) généralisé

PostgreSQL, BigQuery, Snowflake et Databricks Unity Catalog standardisent le RLS déclaratif avec politiques dynamiques basées sur l'identité de la session — simplifiant drastiquement la conformité RGPD / HIPAA.

### Audit natif unifié

Les plateformes cloud proposent maintenant un audit complet (DDL, DML, accès) avec export automatique vers SIEM (Splunk, Elastic Security) sans agent externe.

---

## 7. IA embarquée dans les bases de données

### Text-to-SQL

Les interfaces naturelles (NL2SQL) s'intègrent directement :
- **Snowflake Cortex** : NL2SQL dans le data warehouse, sans quitter l'interface SQL
- **Google BigQuery Gemini** : assistance à l'écriture de requêtes et détection d'anomalies
- **DBeaver + LLM plugins** : l'outillage client intègre des suggestions de requêtes contextuelles

### ML in-database

- **BigQuery ML** et **Redshift ML** permettent l'entraînement et l'inférence de modèles directement en SQL
- **MindsDB** s'interface avec PostgreSQL/MySQL pour exposer des modèles comme des tables virtuelles

---

## 8. Observabilité et opérations

### Slow query analysis automatisé

Les outils comme **pganalyze**, **SolarWinds DPA**, et les fonctions natives d'AWS RDS Performance Insights utilisent désormais des LLMs pour expliquer en langage naturel pourquoi une requête est lente et proposer des corrections.

### eBPF pour le profiling zero-overhead

L'utilisation d'eBPF (Extended Berkeley Packet Filter) permet un profiling continu des bases de données avec un overhead quasi nul en production. Des outils comme **Pixie** ou **Parca** l'appliquent aux bases containerisées.

### Chaos engineering pour les BDD

Des frameworks comme **Chaos Mesh** et **Gremlin** intègrent des scénarios spécifiques aux bases de données : injection de latence réseau entre nœuds, simulation de corruption de disque, tests de failover automatisés dans les pipelines CI/CD.

---

## 9. Tendances à surveiller

1. **Serverless databases** : les modèles pay-per-query (Neon, PlanetScale serverless, CockroachDB Serverless) s'imposent pour les workloads variables.
2. **Graph + Vector convergence** : des bases comme **FalkorDB** (successeur RedisGraph) combinent traversée de graphe et recherche vectorielle pour des cas RAG complexes.
3. **Multi-model databases** : ArangoDB, SurrealDB et Couchbase unifient document, graphe, clé-valeur et recherche full-text dans une seule instance.
4. **Edge databases** : SQLite reste le roi de l'edge, mais **Turso** (libSQL fork de SQLite distribué) et **Cloudflare D1** apportent du SQL répliqué à la périphérie.
5. **WASM runtimes** : l'exécution de logique métier directement dans le moteur via WebAssembly (voir l'UDF WASM de Cloudflare Workers) ouvre de nouveaux patterns d'architecture.

---

## Synthèse

| Axe | Signal fort |
|---|---|
| Performance | Vectorisation SIMD, AQE, auto-indexing ML |
| Architecture | Compute/storage split, HTAP, formats ouverts (Iceberg) |
| IA | Vecteurs natifs, NL2SQL embarqué, ML in-database |
| Sécurité | RLS déclaratif, chiffrement homomorphique |
| Opérations | eBPF profiling, NL-driven slow query analysis |
| Edge | Distributed SQLite, WASM UDFs |

> Les bases de données de 2026 ne sont plus de simples systèmes de persistance : elles deviennent des plateformes de traitement actif — analytiques, intelligentes, et capables d'exécuter de la logique métier et des modèles ML directement au plus près des données.

---

*Rapport rédigé le 2026-05-19 — Sources : release notes officielles PostgreSQL, MySQL, MongoDB, Microsoft SQL Server, DuckDB, TiDB ; blogs techniques Cloudflare, Google Cloud, AWS, Snowflake ; publications ACM SIGMOD / VLDB 2024-2025.*
