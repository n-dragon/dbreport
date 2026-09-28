# Actualité technique des bases de données — 2026-09-28

---

## 1. PostgreSQL 19 : Beta 4 publiée, cap sur la RC début octobre

**PostgreSQL 19 Beta 4** est sortie le 24 septembre 2026. Le calendrier se confirme : après une bêta particulièrement longue, l'équipe de release management vise désormais une **release candidate début octobre**, puis une disponibilité générale « d'ici fin octobre ».

**Nouveautés majeures confirmées pour la GA :**
- **`REPACK`** : nouvelle commande qui fusionne les usages de `VACUUM FULL` et `CLUSTER` pour récupérer l'espace disque et réorganiser une table, avec une option `CONCURRENTLY` permettant de repacker sans bloquer les lectures/écritures.
- **Réplication logique des séquences** : les valeurs de séquences sont désormais répliquées logiquement, comblant un manque historique.
- **Autovacuum parallélisé** avec un nouveau système de scoring qui priorise les tables ayant le plus besoin d'un VACUUM/ANALYZE.

**Fonctionnalités définitivement retirées du cycle bêta :** les Property Graph Queries (SQL/PGQ), le changement de checksum en ligne (« online checksum switching »), `FOR PORTION OF` et les commandes `MERGE/SPLIT PARTITION` — reportées à PostgreSQL 20.

Sources : [PostgreSQL 19 Beta 4 Released](https://www.postgresql.org/about/news/postgresql-19-beta-4-released-3386/), [PostgreSQL 19 GA target moves to October, SQL/PGQ graph queries pulled (faun.dev)](https://faun.dev/news/postgresql-19-ga-slips-to-october-sqlpgq-graph-queries-reverted/), [PostgreSQL 19 release date: what is actually scheduled (Layerbase)](https://layerbase.com/blog/when-will-postgres-19-be-released)

---

## 2. ClickHouse Open House 2026 : réplication Postgres en temps réel, SQL en pipeline

ClickHouse a ouvert **Open House 2026**, sa conférence utilisateurs annuelle, avec l'un de ses trimestres les plus denses en annonces depuis sa création.

**ClickHouse 26.8 (LTS)** introduit :
- Des **background queries** et un nouvel opérateur **`|>`** pour écrire du SQL sous forme de pipeline (à la manière de PRQL).
- De nouveaux **tokenizers de texte** pour le japonais et le chinois dans l'indexation full-text.
- Des intégrations data lake étendues, et des requêtes Parquet/agrégation/jointure plus rapides.

**WalShadow**, un moteur open source qui réplique les données PostgreSQL vers ClickHouse directement depuis le WAL physique (réplication sub-seconde), est disponible en open source ou en preview privée sur ClickHouse Managed Postgres. **On-Demand Compute** (scaling de requêtes individuelles sur des workers dédiés à la demande) progresse également, avec des previews d'AI functions et de support PromQL.

Source : [ClickHouse — September 2026 newsletter](https://clickhouse.com/blog/202609-newsletter)

---

## 3. Vecteurs : le type de données se banalise, MySQL rejoint le mouvement

La tendance amorcée ces derniers mois se confirme : le vecteur devient un **type de donnée standard** plutôt qu'une catégorie de base à part entière, et un mouvement de retour vers l'infrastructure relationnelle traditionnelle s'observe face aux bases 100 % vectorielles.

- **Percona Server for MySQL 9.7.2-2** ajoute une fonction **`DISTANCE()`** (métriques COSINE, EUCLIDEAN, MANHATTAN, DOT) permettant de classer des embeddings directement en SQL — mais sans indexation ANN native (HNSW) pour l'instant, qui nécessite encore des extensions.
- **Oracle** (23ai), **PostgreSQL** (`pgvector`/`pgvectorscale`) et **MongoDB Atlas Vector Search** poursuivent l'intégration native du vecteur dans leur moteur généraliste.
- Signal de marché : **MySQL Galera Cluster atteint sa fin de vie le 30 septembre 2026**, tandis que **MySQL Workbench 26.7**, entièrement reconstruit sur les fondations de MySQL Shell, remplace l'ancien Workbench 8 (C++, désormais EOL) — un changement d'architecture plutôt qu'une simple mise à jour.

Sources : [Percona — Building the Future of MySQL: Vector Support and Binlog Server](https://www.percona.com/blog/building-the-future-of-mysql-announcing-plans-for-mysql-vector-support-and-a-mysql-binlog-server/), [Village News: MySQL News + Events, 21 September 2026](https://villagesql.com/blog/village-news-mysql-news-events-21-september-2026/), [Top 9 Vector Databases as of September 2026 (Shakudo)](https://www.shakudo.io/blog/top-9-vector-databases)

---

## 4. IA ↔ bases de données : Oracle généralise MCP, Databricks et Snowflake accélèrent sur l'IA embarquée

**Oracle** a mis en disponibilité générale l'intégration du **Model Context Protocol (MCP)** dans Oracle Database, ouvrant l'accès IA-natif à la base sur toute plateforme supportant MCP. Les développeurs peuvent s'y connecter via Oracle SQL Developer (avec Copilot pour VS Code) ou via le CLI SQLcl, pour interroger des données, générer des rapports ou exécuter des requêtes vectorielles. L'accès en langage naturel est désormais aussi directement intégré à la console Oracle Database et à l'OCI Enterprise AI SQL Assistant.

Côté entrepôts de données :
- **Snowflake** a mis en disponibilité générale l'**évolution de partitions** pour les tables Iceberg gérées par Snowflake (ajout/suppression/remplacement de clés de partition sans réécrire les données), et introduit **Cortex AI Function Evaluation** (preview publique) ainsi que **Cortex AI Function Optimization** pour des opérations IA plus efficaces.
- **Databricks** a ajouté la recherche web publique à **Genie One**, généralisé le **Genie One MCP server**, et rendu OpenSharing disponible pour partager des metric views entre metastores et comptes.

Sources : [Oracle Database MCP integration opens up AI-driven database access (SDxCentral)](https://www.sdxcentral.com/news/oracle-database-mcp-integration-opens-up-ai-driven-database-access/), [Announcing the Oracle Autonomous AI Database MCP Server](https://blogs.oracle.com/machinelearning+selectai/announcing-the-oracle-autonomous-ai-database-mcp-server), [Databricks Release Notes — September 2026 (Releasebot)](https://releasebot.io/updates/databricks), [Snowflake — All release notes](https://docs.snowflake.com/en/release-notes/all-release-notes)

---

## 5. Microsoft SQL : Database Hub et cadence de correctifs

Microsoft pousse son offensive « gouvernance unifiée » avec **Database Hub**, qui centralise exploration, observabilité, gouvernance et optimisation sur l'ensemble de l'estate SQL (SQL Server, Azure SQL, SQL database in Fabric) sans changer la manière dont chaque service est déployé — un thème central de la Microsoft Fabric and SQL Community Conference 2026 (Barcelone, 28 septembre – 1er octobre).

Côté maintenance : **SQL Server 2022 Cumulative Update 27** livre 41 correctifs, dont un fix pour une fuite mémoire touchant l'exécution parallèle des requêtes `SHORTEST_PATH` dans les bases de graphes (croissance persistante d'`OBJECTSTORE_SOSTASK`). **SSMS 22.6.0** ajoute la création de projet depuis une base dans l'Object Explorer et l'authentification Entra pour Storage Browser et l'import/export DACPAC.

Sources : [What's next for SQL performance, scale, AI, and developer productivity (Microsoft SQL Server Blog)](https://www.microsoft.com/en-us/sql-server/blog/2026/08/13/whats-next-for-sql-performance-scale-ai-and-developer-productivity-at-the-european-sql-community-conference/), [SQL Server 2022 Updates — September 2026 (Releasebot)](https://releasebot.io/updates/microsoft/sql-server-2022), [What's new across Microsoft SQL in 2026 so far (Azure SQL Dev Corner)](https://devblogs.microsoft.com/azure-sql/whats-new-across-microsoft-sql-in-2026-so-far-sql-server-azure-sql-and-sql-database-in-fabric/)

---

## Synthèse

| Axe | Signal fort de la semaine |
|---|---|
| Cœur relationnel | PostgreSQL 19 Beta 4 publiée (24/09) ; RC visée début octobre ; `REPACK`, autovacuum parallélisé confirmés |
| Analytique | ClickHouse 26.8 LTS (pipeline SQL `\|>`, WalShadow pour réplication Postgres temps réel) ; Open House 2026 |
| Vecteurs | Le vecteur devient un type de donnée standard ; MySQL/Percona ajoute `DISTANCE()` ; fin de vie de MySQL Galera Cluster |
| IA ↔ données | Oracle généralise MCP en GA ; Snowflake (Cortex AI Function Optimization) et Databricks (Genie One MCP) accélèrent |
| Écosystème Microsoft | Database Hub unifie gouvernance/optimisation multi-service ; SQL Server 2022 CU27, SSMS 22.6.0 |

> Cette semaine confirme deux mouvements de fond : l'intégration IA quitte le stade expérimental pour devenir un standard managé (MCP généralisé chez Oracle, Snowflake, Databricks), tandis que les moteurs analytiques (ClickHouse, PostgreSQL) rivalisent désormais sur la réplication temps réel et la réduction de la friction opérationnelle plutôt que sur des features isolées.

---

*Rapport rédigé le 2026-09-28 — Sources principales : documentation et blogs officiels PostgreSQL, ClickHouse, Oracle, Microsoft, Snowflake, Databricks ; presse technique (SDxCentral, Releasebot, Layerbase, faun.dev, Shakudo, Village News/daily.dev).*
