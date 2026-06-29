# Actualité technique des bases de données — 2026-06-29

---

## 1. PostgreSQL 18 — les grandes nouveautés

### B-tree Skip Scans
Le planificateur peut désormais exploiter les index multi-colonnes même si la colonne de tête n'apparaît pas dans la clause `WHERE`. Résultat : suppression de nombreux index redondants et économies de stockage significatives sur les grandes tables.

### Virtual Generated Columns par défaut
Les colonnes générées calculent maintenant leur valeur à la volée lors des lectures plutôt que de la stocker sur disque, réduisant l'empreinte physique des tables.

### Checksums de données activés par défaut
PostgreSQL 18 rend les checksums obligatoires à l'initialisation, détectant la corruption silencieuse de blocs sans configuration manuelle — un changement de philosophie opérationnelle majeur.

### Async I/O et indexation vectorielle améliorés
L'I/O asynchrone généralisé réduit les latences sur les workloads mixtes OLTP/vector. Les index HNSW pour `pgvector` bénéficient de ces optimisations, consolidant PostgreSQL comme plateforme unifiée pour les embeddings et les données transactionnelles.

---

## 2. MySQL 9.x — hyper-efficacité à l'échelle

- **Parallel Read threads améliorés** : scan des grands tablespaces multi-cœurs, réduisant significativement le coût des full-table scans.
- **Native VECTOR type** : les colonnes vectorielles sont désormais un type de premier niveau, prêt pour les workloads IA.
- **Partial updates JSON** : les Innovation Releases 2026 affinent les mises à jour partielles de colonnes JSON (moins de write amplification).
- **EXPLAIN ANALYZE enrichi** : diagnostic de bottlenecks (jointures inefficaces, table scans) avec une granularité inégalée.

---

## 3. Bases de données vectorielles — consolidation du marché

RAG (Retrieval-Augmented Generation) est devenu le principal vecteur d'adoption en 2026.

| Système | Évolution clé |
|---|---|
| **pgvectorscale** (Timescale) | 471 QPS à 99% recall sur 50 M vecteurs — 11,4× mieux que Qdrant |
| **Pinecone** | BYOC (Bring Your Own Cloud) disponible sur AWS/GCP/Azure ; full-text search natif en preview ; Dedicated Read Nodes pour workloads read-heavy |
| **Qdrant** | Filtrage payload in-graph ; hybrid search production-ready ; edge sur les filtres sélectifs sur grandes collections |
| **Milvus** | GPU acceleration mature, ciblant les déploiements enterprise à très grande échelle |

**Tendance** : pour la majorité des équipes, `pgvector` + PostgreSQL reste le choix dominant jusqu'à ~50 M vecteurs — il évite la complexité opérationnelle d'un système dédié.

---

## 4. Cloud & Serverless — nouvelles annonces

### AWS Aurora Serverless v3 (20 avril 2026)
Scaling 30 % plus rapide, facturation à la milliseconde, et réduction du cold-start pour les workloads à forte variabilité.

### Google Cloud Next'26
AlloyDB a reçu des améliorations de son column store adaptatif et une intégration renforcée avec Vertex AI pour les requêtes vectorielles hybrides directement en SQL.

### ClickHouse — MongoDB CDC en Public Beta
Le connecteur ClickPipes MongoDB CDC supporte désormais les sharded clusters et Amazon DocumentDB — synchronisation temps réel vers ClickHouse pour les pipelines analytiques sur données opérationnelles MongoDB.

### Marché cloud databases
Le marché est estimé à **27 Md$ en 2026**, projeté à **110 Md$ en 2035** (CAGR 16,8 %). Le multi-model (JSON + Graph + Vector + Relationnel dans un seul moteur) devient le critère de sélection n°1 pour réduire la fatigue "polyglot persistence".

---

## 5. Optimisation : nouvelles techniques consolidées

### AI-driven auto-indexing
Azure SQL, AlloyDB et Oracle Autonomous Database utilisent des modèles ML embarqués pour recommander et créer automatiquement des index (partiels, couvrants, BRIN) basés sur les patterns d'accès observés — sans intervention DBA.

### Query Plan Management (QPM) & EXPLAIN ANALYZE
PostgreSQL 18 QPM + MySQL 9.x EXPLAIN ANALYZE fournissent des insights en temps réel sur les plans d'exécution. Identification des N+1 patterns, mauvaises estimations de cardinalité et jointures coûteuses sans instrumentation externe.

### Index composites intelligents
Les stratégies d'indexation composite `(customer_id, order_date)` ou `(created_at, status)` deviennent le pattern standard recommandé par les outils d'auto-tuning pour les requêtes OLTP typiques.

### Autonomous databases
Oracle Autonomous Database et Azure SQL Edge réduisent le tuning manuel grâce à des boucles feedback IA qui ajustent les paramètres moteur (mémoire, parallélisme, cache) dynamiquement en fonction de la charge.

---

## 6. DuckDB — "warehouse in your app"

DuckDB consolide sa position de moteur analytique embarqué avec :
- LTS lines stables pour les déploiements production
- Transaction isolation controls affinés
- Amélioration de l'import de données et de la précision des agrégations
- Intégration renforcée avec Apache Iceberg et Delta Lake via extensions officielles

---

## 7. Tendances à surveiller (horizon Q3-Q4 2026)

1. **NewSQL distribué** : CockroachDB et AlloyDB résolvent enfin le problème de consistency globale à grande échelle — projection CAGR 29,7 % pour ces architectures.
2. **Multi-model unifié** : ArangoDB, SurrealDB, FerretDB ciblent le remplacement de 3-5 bases spécialisées par une seule instance.
3. **ClickHouse pour l'observabilité** : devient le standard de facto pour stocker métriques, logs et traces à l'échelle (telemetry pattern).
4. **HTAP sans ETL** : TiDB 8.x, AlloyDB et SingleStore rendent caducs les pipelines ETL pour l'analytique temps réel sur données opérationnelles.
5. **Edge SQL** : Cloudflare D1 et Turso (libSQL distribué) apportent le SQL répliqué à la périphérie pour des applications sub-10 ms globalement.

---

## Synthèse juin 2026

| Axe | Signal fort |
|---|---|
| PostgreSQL 18 | Skip Scans, checksums par défaut, async I/O |
| Vectoriel | pgvectorscale domine < 50 M vecteurs ; Pinecone BYOC en GA |
| Cloud | Aurora Serverless +30 % scaling ; AlloyDB Next'26 |
| Analytique | ClickHouse MongoDB CDC ; DuckDB LTS |
| Auto-tuning | AI-driven indexing généralisé (Azure, AlloyDB, Oracle) |
| Marché | 27 Md$ → 110 Md$ d'ici 2035 ; multi-model comme critère #1 |

> En juin 2026, la frontière entre OLTP, OLAP et Vector DB continue de s'effacer. PostgreSQL 18 illustre parfaitement cette convergence : un seul moteur pour les transactions, l'analytique légère et les embeddings IA, avec une opérabilité renforcée par défaut.

---

*Rapport rédigé le 2026-06-29 — Sources : release notes PostgreSQL 18, MySQL 9.x, ClickHouse Blog, Timescale pgvectorscale benchmarks, Google Cloud Next'26, AWS Aurora changelog, DataCamp vector DB comparison, MarkTechPost, Medium/CodeToDeploy.*

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
