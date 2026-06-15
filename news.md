# Actualité technique des bases de données — 2026-06-15

---

## 1. PostgreSQL 18 — Beta en cours, faits marquants

PostgreSQL 18 (Beta 1 publié en mai 2025, RC attendue T3 2025) introduit des changements architecturaux significatifs :

### Asynchronous I/O natif

Le changement le plus attendu depuis des années : PostgreSQL 18 remplace son I/O synchrone par un système **asynchronous I/O** (AIO) utilisant `io_uring` sous Linux et `IOCP` sous Windows. Les benchmarks préliminaires montrent des gains de **2× à 5×** sur les workloads I/O-bound (bulk load, vacuum, checkpoint).

### Améliorations du planificateur

- **`enable_group_by_reordering`** : le planificateur réordonne les colonnes de GROUP BY pour minimiser les tris inutiles.
- Meilleures statistiques multi-colonnes pour les prédicats corrélés.
- Parallélisme étendu aux `FULL OUTER JOIN`.

### OAuth 2.0 natif

PostgreSQL 18 intègre OAuth 2.0 comme méthode d'authentification native, simplifiant l'intégration avec les identity providers (Okta, Azure AD, Auth0).

---

## 2. DuckDB 1.x — Consolidation de l'OLAP embarqué

Depuis la sortie de DuckDB 1.0 (juin 2024), la base analytique embarquée s'est imposée comme référence pour l'analyse locale et le data engineering léger.

### Nouveautés 1.1 / 1.2

| Feature | Impact |
|---|---|
| **Secret Manager** | Gestion des credentials cloud (S3, GCS, Azure) sans variables d'environnement |
| **Delta Lake natif** | Lecture directe des tables Delta Lake via extension `delta` |
| **Apache Iceberg** | Extension `iceberg` pour lecture des tables Iceberg REST catalog |
| **JSON auto-schema** | Inférence automatique du schéma JSON en une passe |
| **Spatial extension** | Fonctions géospatiales (H3, WKT, POINT, POLYGON) |
| **Arrow streaming** | Échange zero-copy avec Apache Arrow Flight |

### DuckDB + Python/dbt

L'intégration `dbt-duckdb` permet des pipelines de transformation full-local sans serveur. Les équipes data utilisent DuckDB comme moteur de test local avant déploiement sur BigQuery/Snowflake, réduisant les coûts de CI de 70–90%.

---

## 3. L'écosystème Valkey vs Redis — la bifurcation open source

### Chronologie de la crise

En mars 2024, Redis Ltd a rebasculé Redis sous licence **SSPL + RSALv2** (non-OSI). En réponse, la Linux Foundation a lancé **Valkey**, un fork GPL-2.0 maintenu par Amazon, Google, Oracle et Ericsson.

### État en 2025-2026

| Projet | Version | Licence | Soutien |
|---|---|---|---|
| **Redis** | 8.x | Propriétaire (SSPL) | Redis Ltd |
| **Valkey** | 8.x | BSD-3 (fork OSS) | Linux Foundation, AWS, GCP |
| **Kvrocks** | 2.x | Apache 2.0 | Apache Foundation |
| **Dragonfly** | 1.x | BSL 1.1 | Dragonfly DB |

**Valkey 8.0** apporte : slots de hash améliorés, performances single-thread +40% via I/O threading, et un module VECTOR pour la recherche de similarité.

---

## 4. Bases de données vectorielles — Maturité et consolidation

Le marché se consolide après l'explosion de 2023-2024.

### pgvector 0.8+

L'extension PostgreSQL pour vecteurs a franchi un cap décisif :
- **HNSW indexing** stable et performant (ANN Benchmarks top-5)
- **Quantisation binaire** (BQ) : compression 32× pour les vecteurs haute dimension
- **Parallel HNSW build** : construction d'index parallélisée pour les grands datasets
- Filtrage prédicatif intégré au parcours HNSW (évite le post-filtrage coûteux)

### Convergence hybride

La tendance dominante est d'ajouter la recherche vectorielle **dans les bases existantes** plutôt que de déployer un système dédié :

- **Elasticsearch / OpenSearch** : vecteurs denses + BM25 en requête hybride native
- **MongoDB Atlas Vector Search** : index HNSW intégrés aux collections existantes
- **Qdrant 1.10+** : sparse vectors + dense vectors dans un seul index hybride (HNSW + SPLADE)
- **SingleStore** : vecteurs dans tables relationnelles avec DOT_PRODUCT/EUCLIDEAN_DISTANCE natives

### Benchmark BEIR / ANN 2025

Les indices **HNSW quantifiés** dominent : recall@10 > 97% avec 4–8× moins de RAM qu'HNSW full-precision, rendant la recherche vectorielle viable à grande échelle sans GPU.

---

## 5. Data Lakehouse — Apache Iceberg v3 et la guerre des formats

### Apache Iceberg v3

La spec v3 (publiée fin 2024) ajoute :
- **Row lineage** : traçabilité par ligne pour audit et RGPD (delete physique garanti)
- **Variant type** : type semi-structuré natif pour JSON flexible sans schéma fixe
- **Geometry type** : données géospatiales dans les tables Iceberg
- **Default values** : valeurs par défaut au niveau colonne dans les métadonnées

### Catalogs REST et interopérabilité

Le standard **Iceberg REST Catalog** permet à n'importe quel moteur (Spark, Trino, DuckDB, Snowflake, BigQuery) de lire les mêmes tables. **Polaris Catalog** (Snowflake, open-sourcé) et **Unity Catalog** (Databricks) s'intègrent désormais nativement dans l'écosystème.

---

## 6. IA native dans les bases de données

### Génération de requêtes (NL2SQL) — État de l'art 2025

Les modèles NL2SQL ont atteint des performances remarquables sur le benchmark **BIRD** (~ 70–75% accuracy sur SQL complexe). Les intégrations production incluent :

- **Snowflake Cortex Analyst** : génération de SQL analytique avec contexte métier (YAML sémantique)
- **Google BigQuery Gemini** : assistance au debug de requêtes et génération d'insights automatiques
- **Amazon Q in QuickSight** : requêtes en langage naturel sur RDS, Redshift, Aurora

### Colonnes calculées par IA

**Azure SQL Database** expérimente des colonnes dont la valeur est calculée par un modèle d'inférence embarqué (`AS (PREDICT MODEL=…)`) — permettant de scorer chaque ligne à l'insertion sans ETL externe.

### Détection d'anomalies in-database

**Oracle Autonomous Database** et **AlloyDB AI** intègrent des détecteurs d'anomalies temps réel sur les séries temporelles, directement dans le moteur SQL, sans export vers un service ML séparé.

---

## 7. Optimisation matérielle — GPU et FPGA

### Bases de données GPU

- **RAPIDS cuDF** (NVIDIA) : accélération GPU pour pandas-compatible dataframes, intégrable comme backend DuckDB/Spark
- **HeavyDB (ex OmniSci)** : SQL analytique full-GPU, 100× plus rapide sur les agrégations columnar massives
- **PGVECTO.RS** : extension PostgreSQL utilisant le GPU pour l'indexation HNSW (5–10× plus rapide à la construction)

### CXL (Compute Express Link)

L'émergence de **CXL 3.0** va permettre le partage de pools de mémoire entre nœuds — rendant possible une architecture « shared memory » sans les contraintes NUMA actuelles. Les premiers serveurs CXL 3.0 arrivent chez les hyperscalers en 2025-2026, avec des bases de données tirant parti du *memory pooling* (réduction des coûts RAM de 30-50%).

---

## 8. SQLite — Toujours indétrônable à l'edge

### Extensions récentes

| Extension | Fonctionnalité |
|---|---|
| **sqlite-vec** | Recherche vectorielle ANN dans SQLite |
| **cr-sqlite** | CRDT pour synchronisation multi-maîtres (Turso) |
| **libSQL** (Turso) | Fork SQLite avec réplication async et REST API |
| **sqlite-xsv** | Lecture directe CSV/TSV comme tables virtuelles |

### Cloudflare D1

Cloudflare D1 (SQLite distribué à l'edge) est passé en GA fin 2024 avec :
- Réplication read sur +300 edge locations
- Transactions distribuées via DO (Durable Objects)
- Time Travel : rollback point-in-time sur 30 jours

---

## 9. Observabilité avancée

### OpenTelemetry + Databases

Le standard **OpenTelemetry Semantic Conventions for Databases** (v1.20+) normalise les traces et métriques pour PostgreSQL, MySQL, MongoDB et Redis. Les spans incluent désormais le texte SQL sanitisé, le nombre de lignes affectées et le plan d'exécution résumé.

### eBPF profiling en production

**Pixie** (open-source, CNCF) et **Odigos** permettent d'injecter du profiling zero-instrumentation sur des bases containerisées via eBPF, capturant les requêtes lentes, les flamegraphs CPU et les latences réseau sans modifier le code applicatif.

---

## 10. Tendances émergentes Q2-Q3 2026

| Tendance | Maturité | Signal |
|---|---|---|
| **AI-native databases** (ex: MotherDuck AI, Weaviate Generative) | Émergent | LLMs comme first-class citizens dans le query engine |
| **Distributed SQLite** (Turso, D1, LibSQL) | En croissance | Edge computing, WASM |
| **WASM UDFs** | Expérimental | Cloudflare Workers, SingleStore |
| **CXL memory pooling** | Pré-prod | Hyperscalers H2 2026 |
| **Iceberg REST Catalog** | Mature | Adoption universelle |
| **Schéma-on-read JSON natif** (Variant type) | Croissance | Iceberg v3, DuckDB |

---

## Synthèse juin 2026

Le paysage des bases de données se structure autour de **trois forces** :

1. **Intelligence embarquée** : NL2SQL, ML in-database, détection d'anomalies autonome — la frontière entre base de données et pipeline ML s'efface.
2. **Convergence des formats ouverts** : Apache Iceberg v3 + REST Catalog comme backbone universel ; DuckDB comme moteur de lecture universel.
3. **Compute distribué vers le bas** : SQLite à l'edge (Turso, D1), WASM UDFs, CXL memory pooling — la puissance analytique descend vers la périphérie.

> **À retenir** : PostgreSQL 18 AIO + DuckDB Iceberg + pgvector quantifié forment une stack open-source de référence capable de rivaliser avec des offres cloud managées pour 80% des cas d'usage.

---

*Rapport rédigé le 2026-06-15. Sources : PostgreSQL release notes, DuckDB changelog, Valkey GitHub, pgvector releases, Apache Iceberg spec, Cloudflare blog, NVIDIA RAPIDS docs, VLDB 2025 proceedings, DB-Engines ranking.*

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
