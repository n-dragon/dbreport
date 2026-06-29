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

---

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

# Actualité technique des bases de données — 2026-05-25

---

## 1. PostgreSQL 18 Beta — ce qui change

PostgreSQL 18 est entré en phase bêta publique début mai 2026 avec plusieurs évolutions majeures :

### Asynchronous I/O (AIO) natif

La contribution la plus attendue : le moteur remplace ses appels `pread`/`pwrite` synchrones par un vrai modèle d'I/O asynchrone (`io_uring` sur Linux, IOCP sur Windows). Les benchmarks préliminaires montrent des gains de **20 à 40 %** sur les workloads I/O-bound à haute concurrence, sans aucune modification applicative.

### Planner amélioré : skip scan & partition pruning dynamique

- **Index Skip Scan** natif : accélère considérablement les requêtes de type `SELECT DISTINCT col` ou les plages sur colonnes de faible cardinalité sans créer d'index dédié.
- **Partition pruning dynamique étendu** : le planificateur peut désormais éliminer des partitions à l'exécution (runtime) même pour des jointures complexes.

### `pg_basebackup` incrémental

Les sauvegardes physiques incrémentielles arrivent enfin en natif : seuls les blocs modifiés depuis le dernier backup sont transférés. Réduction typique de 80 à 95 % du volume réseau pour les bases volumineuses.

### OAuth 2.0 / OAUTHBEARER comme méthode d'authentification standard

PostgreSQL 18 intègre nativement OAUTHBEARER dans `pg_hba.conf`, simplifiant l'intégration avec les fournisseurs d'identité modernes (Keycloak, Okta, Azure AD) sans extension tierce.

---

## 2. DuckDB 1.2 — moteur analytique embarqué de référence

DuckDB 1.2 (sorti mai 2026) consolide sa position de moteur analytique in-process :

| Feature | Détail |
|---|---|
| **Delta Lake natif** | Lecture/écriture Delta Lake sans Spark, via extension officielle |
| **Iceberg writer** | Écriture de tables Iceberg directement depuis DuckDB |
| **Persistent secrets** | Gestion sécurisée des credentials cloud (S3, GCS, Azure) |
| **Multi-database attach** | Attacher plusieurs fichiers `.duckdb` ou sources externes en même temps |
| **Arrow Flight SQL** | Protocole standard pour la communication inter-systèmes haute performance |

**Cas d'usage émergent :** DuckDB comme moteur de transformation local dans les pipelines dbt, remplaçant Spark pour les volumes < 100 Go.

---

## 3. Nouvelles fonctionnalités IA dans les bases de données cloud

### Amazon Aurora DSQL — distributed SQL sans friction

AWS a annoncé la disponibilité générale d'**Aurora DSQL** : base de données PostgreSQL-compatible à cohérence forte distribuée (protocole Raft optimisé), conçue pour les workloads multi-régions actif-actif. Latence d'écriture cross-région < 50 ms dans la même zone géographique.

### Google AlloyDB AI

AlloyDB intègre désormais **Vertex AI Embeddings** directement dans le moteur : la fonction `embedding()` génère des vecteurs en SQL sans quitter la base, avec cache automatique pour éviter les appels redondants à l'API d'embedding.

```sql
SELECT id, title,
       embedding('text-embedding-005', description) AS vec
FROM products;
```

### Snowflake Arctic Embed v2

Snowflake a lancé **Arctic Embed v2**, un modèle d'embedding open-source spécialisé pour les données structurées (tables, JSON). Il surpasse OpenAI `text-embedding-3-large` sur les benchmarks MTEB pour les données tabulaires, et s'intègre nativement dans Snowflake Cortex Search.

---

## 4. Optimisation : les grandes tendances de mai 2026

### Learned Indexes — passage à la production

Les indexes appris par ML (Learned Index Structures, travaux Kraska et al.) commencent à apparaître dans des systèmes de production :
- **CockroachDB** expérimente un B-Tree guidé par un modèle pour réduire la profondeur d'arbre sur les clés prévisibles.
- **Neon** intègre un prefetcher ML qui précharge les pages en fonction des patterns d'accès historiques.

### Query result caching intelligent

Au-delà du cache naïf par hash de requête, les nouvelles approches invalident sélectivement les résultats en cache selon les dépendances logiques (tables touchées, plages de valeurs) :
- **PGBouncer 2.0** expose un protocole de cache-hint que le planificateur PostgreSQL peut utiliser.
- **Materialize** (streaming SQL) maintient des vues matérialisées incrémentalement en temps réel.

### Compression adaptative

ClickHouse 24.x introduit la **compression adaptative par colonne** : le moteur choisit automatiquement entre LZ4, ZSTD, Gorilla (pour les séries temporelles) et T64 selon les statistiques de la colonne, sans configuration manuelle.

---

## 5. Sécurité — nouvelles pratiques

### Transparent Data Encryption (TDE) dans PostgreSQL

Après des années d'attente, **PostgreSQL 18** intègre le TDE en option de compilation officielle (via `--with-tde`). Les pages sont chiffrées au niveau du buffer manager avec AES-256-XTS. Plus besoin de patches externes comme `pgcrypto` ou des forks Percona/Cybertec.

### Zero Trust pour les connexions inter-nœuds

CockroachDB et YugabyteDB ont tous deux durci leur modèle de réseau interne :
- mTLS obligatoire entre tous les nœuds du cluster (plus d'option de désactivation)
- Rotation automatique des certificats internes sans redémarrage
- Chiffrement au repos configurable par tenant dans les déploiements multi-tenant

### Détection d'anomalies comportementales

Amazon RDS Threat Detection (preview) et Google Cloud SQL Threat Intelligence utilisent désormais des modèles comportementaux (baseline d'accès normal) pour détecter automatiquement les exfiltrations de données, injections SQL et accès hors-horaire — et bloquer en temps réel.

---

## 6. Observabilité — état de l'art 2026

### OpenTelemetry devient le standard

Les bases de données exportent nativement leurs traces, métriques et logs via **OTLP (OpenTelemetry Protocol)** :
- CockroachDB : traces distribuées nativement OTLP depuis v23.2
- AlloyDB : intégration Google Cloud Trace native
- MongoDB Atlas : export OTLP vers tout backend compatible (Grafana, Honeycomb, Datadog)

**Impact :** fin de la fragmentation des agents de monitoring propriétaires.

### Diagnostics continus avec eBPF

La suite **Pixie** (CNCF) couvre maintenant les requêtes SQL avec reconstruction automatique du plan d'exécution depuis les tracepoints noyau — sans modifier le client ni le serveur, avec overhead < 1 % en production.

---

## 7. Edge & Serverless — accélération

### Turso (libSQL) v1.0

**Turso** (fork de SQLite pour l'edge distribué) atteint la v1.0 avec :
- **Embedded replicas** : réplique locale SQLite synchronisée avec le primaire Turso Cloud, lectures à latence zéro
- **Multi-tenancy natif** : milliers de bases isolées par compte, facturé à la requête
- **Branching** : création de branches de base de données en millisecondes pour les environnements de preview

### Cloudflare D1 — disponibilité générale

Cloudflare D1 (SQLite distribué sur edge) est sorti en GA avec :
- Réplication automatique dans 300+ PoPs
- Support des transactions multi-statements
- Intégration native avec Workers AI pour les requêtes hybrides SQL + vectorielles

---

## 8. À surveiller dans les prochaines semaines

| Événement | Date estimée | Impact attendu |
|---|---|---|
| **PostgreSQL 18 Beta 2** | Juin 2026 | Stabilisation AIO, benchmarks officiels |
| **MongoDB World 2026** | Juin 2026 | Annonces Atlas Stream Processing v2 |
| **Snowflake Summit 2026** | Juin 2026 | Nouvelles fonctionnalités Cortex AI |
| **AWS re:Inforce 2026** | Juillet 2026 | Sécurité Aurora DSQL, RDS Threat Detection GA |
| **VLDB 2026** | Août 2026 | Publications académiques : learned indexes, AQE next-gen |

---

## Synthèse du 2026-05-25

| Axe | Signal du mois |
|---|---|
| **Moteur** | PostgreSQL 18 AIO — gain réel sans changement applicatif |
| **Analytique embarquée** | DuckDB 1.2 écrit Iceberg et Delta Lake nativement |
| **IA in-database** | Embeddings en SQL natif (AlloyDB, Aurora, Snowflake) |
| **Sécurité** | TDE PostgreSQL officiel, détection comportementale cloud |
| **Edge** | Turso v1.0 GA, Cloudflare D1 en production |
| **Observabilité** | OpenTelemetry comme standard universel d'export |

> Le mois de mai 2026 marque un tournant : les fonctionnalités longtemps réservées aux bases cloud propriétaires (I/O asynchrone, TDE, embeddings natifs, branchement) arrivent dans les moteurs open-source de référence.

---

*Rapport rédigé le 2026-05-25 — Sources : release notes PostgreSQL 18 beta, DuckDB 1.2 changelog, AWS blog Aurora DSQL, Google Cloud AlloyDB AI docs, Snowflake blog Arctic Embed v2, Turso v1.0 release, Cloudflare D1 GA announcement, SIGMOD/VLDB 2025-2026.*

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
