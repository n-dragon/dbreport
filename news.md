# Actualité technique des bases de données — 2026-06-08

---

## 1. PostgreSQL 18 — L'I/O asynchrone change la donne

Sorti le 25 septembre 2025, PostgreSQL 18 est probablement la version la plus structurante depuis PostgreSQL 10.

### Nouveau sous-système I/O asynchrone (AIO)

La refonte complète de la couche I/O est l'ajout phare : au lieu d'attendre chaque requête disque séquentiellement, PostgreSQL 18 émet désormais plusieurs requêtes I/O en parallèle. Les benchmarks montrent jusqu'à **3× d'amélioration** sur les lectures depuis le stockage (sequential scans, bitmap heap scans, VACUUM). Paramètres clés : `io_method`, `io_combine_limit`, `io_max_combine_limit`.

### Skip Scan sur index B-tree multi-colonnes

PostgreSQL 18 introduit le *skip scan* : les requêtes qui omettent une condition `=` sur une colonne préfixe d'un index B-tree peuvent désormais exploiter cet index sans la réécriture manuelle d'une `LATERAL JOIN`. Gain significatif sur les colonnes à faible cardinalité.

### Autres nouveautés majeures

| Feature | Détail |
|---|---|
| `uuidv7()` | UUID ordonné par timestamp (tri naturel, index B-tree optimal) |
| Colonnes générées virtuelles | Calcul à la lecture, pas de stockage — remplace des vues calculées |
| Support OAuth 2.0 | Authentification native via token OAuth, sans extension tierce |
| Contraintes temporelles | `PRIMARY KEY`, `UNIQUE` et `FOREIGN KEY` avec validité `VALID BETWEEN` |
| `pg_upgrade` préserve les stats | Plus de dégradation de performances post-migration majeure |
| Hash joins / GROUP BY | Mémoire réduite, meilleure performance sur groupements larges |

### Betas en cours (18.x et 19 alpha)

PostgreSQL 18 est en phase de stabilisation avec les versions 18 Beta 3 disponibles. La roadmap PostgreSQL 19 anticipe un parallélisme amélioré pour les UDF et un support plus riche du standard SQL:2023.

---

## 2. Redis 8 — Retour en open-source, Vector Sets natifs

Redis 8.0 GA est disponible avec des changements majeurs :

- **Licence AGPL v3** ajoutée comme troisième option (en plus de RSALv2 et SSPLv1) — un retour partiel vers l'open-source après la controverse de 2024.
- **Intégration Redis Stack dans le core** : JSON, Time Series, Search/Query Engine, Graph sont désormais inclus nativement sous AGPL — plus besoin de modules séparés.
- **Vector Sets** : premier nouveau type de données depuis des années. Stockage et requête ANN (Approximate Nearest Neighbor) intégrés, avec des commandes **87% plus rapides** et un **débit 2×** par rapport aux solutions précédentes.
- **Redis acquiert Featureform** (oct. 2025) : Feature Store unifié avec pipelines batch+streaming, multi-tenancy et RBAC pour les workloads ML/AI.

---

## 3. MongoDB 8.2 — Performance et analytics

MongoDB 8.2 continue sur la lancée des 8.x avec :

- **Initial sync 40% plus rapide** sur les déploiements replica sets larges.
- **Bulk insertions time-series accélérées** : les collections time-series profitent d'une nouvelle voie de compression plus agressive.
- **Réduction du coût de multi-planning** : l'optimiseur évalue moins de plans concurrents grâce à des heuristiques améliorées sur les statistiques de collection.
- **MongoDB 9.0 (preview)** : +56% sur les bulk writes, +20% sur les écritures concurrentes vs 7.0 — la pression concurrentielle de DynamoDB et Firestore pousse à des gains agressifs.

---

## 4. MySQL 9.3 / HeatWave — L'IA dans le moteur

Oracle intensifie l'intégration IA dans MySQL :

- **MySQL 9.3** : optimisation des vector indexes, moteur JavaScript UDF affiné pour les traitements ML légers.
- **HeatWave 9.4.1 GA** : `AutoML` intégré — entraînement et inférence de modèles **directement en SQL**, sans déplacer les données. MySQL HeatWave devient officiellement un concurrent des solutions MLaaS pour les workloads collocated.
- **Type `VECTOR`** natif stabilisé : requêtes de similarité cosine/L2 indexées via HNSW sans plugin tiers.

---

## 5. DuckDB — Le standard de l'analytique embarquée

DuckDB s'impose en 2026 comme la référence *in-process* pour l'analytique :

- **Lecture directe** de Parquet, Iceberg, Delta Lake, JSON et CSV sans ETL.
- **ACID complet** avec transactions multi-tables.
- Lignes LTS disponibles pour les environnements de production stables.
- **Intégration `pg_duckdb`** : DuckDB comme moteur analytique d'appoint dans PostgreSQL — requêtes OLAP exécutées sur le moteur vectorisé DuckDB depuis une connexion PostgreSQL standard.
- Benchmarks 2026 : DuckDB surpasse systématiquement Pandas et Polars sur les agrégations de fichiers > 500 Mo, et reste compétitif face à ClickHouse sur les datasets < 100 Go single-node.

---

## 6. Bases vectorielles — Consolidation du marché

### Retour vers les bases relationnelles étendues

Si 2022–2025 a vu l'explosion des bases vectorielles spécialisées, 2026 marque un **reflux vers les SGBD étendus**. PostgreSQL + pgvector est désormais le choix par défaut pour la majorité des architectures RAG à taille humaine.

**Benchmark pgvector vs spécialisé :**
- pgvector (pgvectorscale) : **471 QPS** à 99% recall sur 50 M vecteurs
- Qdrant : **41 QPS** à 99% recall sur 50 M vecteurs
- → Pour les datasets < 100 M vecteurs, pgvector est un choix rationnel sans overhead opérationnel additionnel.

### État du marché vectoriel 2026

| Système | Positionnement | Différenciateur |
|---|---|---|
| **pgvector / pgvectorscale** | SGBDR étendu | Zéro infrastructure supplémentaire |
| **Qdrant** | Natif vectoriel | Filtrage payload, quantification avancée |
| **Weaviate** | Hybride vect./graph | Multi-model, modules ML natifs |
| **Milvus / Zilliz Cloud** | Scale-out | GPU acceleration, milliards de vecteurs |
| **Pinecone** | Serverless managed | Developer experience, serverless pricing |

**Marché** : $2.46 Mds en 2024 → projection $10.6 Mds en 2032 (CAGR 27.5%).

### Hybrid Search comme standard

La combinaison **ANN + BM25** (Reciprocal Rank Fusion) devient le pattern RAG de référence — recherche vectorielle pour la sémantique, full-text pour les termes exacts. Implémenté nativement dans pgvector 0.8+, Weaviate, et Elasticsearch 8.x.

---

## 7. Cloud-native & Serverless — L'ère du branching

### Neon et la "database-as-a-branch"

Neon (PostgreSQL serverless) généralise le concept de **branches de base de données** calquées sur les branches Git :
- Branch instantanée (copy-on-write) pour chaque PR / environnement de test.
- Scale-to-zero automatique : coût nul en idle.
- Adoption croissante dans les pipelines CI/CD : chaque pipeline de test démarre avec sa propre instance PostgreSQL fraîche.

### Tendances cloud-native 2026

- **HTAP serverless** : TiDB Cloud Serverless apporte la convergence OLTP/OLAP sans gestion d'infrastructure.
- **Marché serverless DB** : $21.1 Mds projetés d'ici fin 2026 (MarketsandMarkets).
- **HTAP CAGR** : 16.2% jusqu'en 2031 — l'élimination des pipelines ETL temps réel reste le moteur principal.
- **AlloyDB (Google)** : columnar store adaptatif activé automatiquement, performances analytiques 4–100× vs PostgreSQL standard sur les workloads mixtes.

---

## 8. Apache Iceberg — Le format universel s'impose

Apache Iceberg est devenu le **standard de facto** pour les data lakehouses :

- **Snowflake, Databricks, AWS, Google, Azure** : tous supportent nativement la lecture/écriture Iceberg.
- **Open Table Format war terminée** : Delta Lake et Hudi convergent vers l'interopérabilité avec Iceberg via des connecteurs bidirectionnels.
- **Iceberg REST Catalog** : API standardisée permettant à n'importe quel moteur (DuckDB, Trino, Spark, Flink) d'accéder aux mêmes tables sans vendor lock-in.
- **Time travel & branching** : snapshots avec retention configurable, `AS OF TIMESTAMP` natif dans Trino/DuckDB.

---

## 9. Observabilité et IA opérationnelle

### NL-driven query analysis

- **pganalyze** et **AWS RDS Performance Insights** intègrent des LLMs pour expliquer en langage naturel les lenteurs et proposer des correctifs (index manquants, réécriture de requête).
- **Snowflake Cortex** : assistant NL2SQL dans le data warehouse — écriture de requêtes sans quitter l'interface SQL.

### eBPF profiling zero-overhead

L'adoption d'eBPF pour le profiling continu des SGBD containerisés progresse : outils comme **Pixie** et **Parca** permettent un profilage CPU/I/O sans instrumentation dans le code de la base.

---

## 10. Tendances à surveiller — Horizon 6–12 mois

1. **PostgreSQL 18 GA + adoption** : l'AIO va devenir le benchmark de référence — pression sur Oracle et SQL Server pour suivre.
2. **Redis 8 AGPL adoption** : impact sur les forks (Valkey, Redict) et les distributions managées.
3. **DuckDB + Iceberg** : DuckDB comme moteur de requête universel sur les lakehouses.
4. **pgvector vs spécialisés** : à quel seuil de volumétrie vecteur bascule-t-on vers Milvus/Qdrant ?
5. **FalkorDB (graph + vector)** : convergence graphe/vectoriel pour les RAG sur Knowledge Graphs.
6. **Edge SQL** : Turso (libSQL), Cloudflare D1, SQLite WASM — SQL répliqué à la périphérie pour les apps offline-first.
7. **Chiffrement homomorphique** : encore limité en perf, mais les cas réglementaires (RGPD, HIPAA) accélèrent l'adoption.

---

## Synthèse juin 2026

| Axe | Signal fort |
|---|---|
| Performance | PostgreSQL 18 AIO (3×), Redis 8 Vector Sets (87% faster), DuckDB SIMD |
| Architecture | Iceberg universel, branching DB (Neon), HTAP serverless |
| IA / Vecteurs | pgvector consolide, Redis Vector Sets natif, MySQL HeatWave AutoML |
| Cloud-native | Scale-to-zero généralisé, serverless $21 Mds en 2026 |
| Opérations | NL2SQL embarqué, eBPF profiling, auto-indexing ML |
| Edge | libSQL/Turso, D1 Cloudflare, SQLite WASM |

> En juin 2026, la convergence IA × données s'accélère à tous les niveaux : stockage vectoriel dans les moteurs relationnels, ML embarqué dans le query engine, et interfaces en langage naturel qui démocratisent l'accès aux données sans passer par le SQL brut.

---

*Rapport rédigé le 2026-06-08 — Sources : release notes officielles PostgreSQL 18, Redis 8, MongoDB 8.2, MySQL HeatWave 9.4 ; benchmarks pgvectorscale ; blogs techniques Neon, Tinybird, Kestra, DEV Community ; MarketsandMarkets (serverless DB market) ; db-engines.com.*

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
