# Actualité technique des bases de données — 2026-09-21

---

## 1. PostgreSQL 19 — la bêta s'éternise, GA repoussée à fin octobre

PostgreSQL 19 ne suivra pas le calendrier habituel de sortie à l'automne : la Beta 4 est prévue le 24 septembre 2026 et l'équipe de release management vise désormais une disponibilité générale « d'ici fin octobre ».

**Ce qui a été retiré en cours de cycle bêta** (53 fonctionnalités au total) :
- Support SQL des **Property Graph Queries**.
- `ALTER TABLE ... MERGE/SPLIT PARTITION(S)`.
- `GROUP BY ALL`.
- Compression TOAST en `lz4`.

**Ce qui reste au programme :**
- `pg_plan_advice` : indices de plan d'exécution (query hints) enfin standardisés dans le cœur du moteur.
- Autovacuum parallélisé.
- `ON CONFLICT DO SELECT`.
- `IGNORE NULLS` dans les fonctions fenêtrées.

La branche stable continue en parallèle ses correctifs de routine (18.6, 17.11, 16.15, 15.19, 14.24 publiés en août, corrigeant 28 CVE et plus de 110 bugs).

Sources : [PostgreSQL 19 Beta 2 Released](https://www.postgresql.org/about/news/postgresql-19-beta-2-released-3350/), [PostgreSQL 19 Delayed: Key Feature Reversions (daily.dev)](https://daily.dev/posts/postgresql-19-delayed-key-feature-reversions-release-updates-cxkaxazw6), [Layerbase – When will Postgres 19 be released](https://layerbase.com/blog/when-will-postgres-19-be-released)

---

## 2. Bases vectorielles : le marché se consolide, la fonctionnalité s'efface derrière la base

Le vecteur natif est devenu un standard : chaque grand SGBD relationnel (SQL Server, MongoDB, PostgreSQL via `pgvector`) l'expose désormais nativement, ce qui rebat les cartes du marché des bases 100 % vectorielles.

- **Consolidation** : Pinecone explorerait une vente tout en luttant contre le churn client, et le CEO d'Elastic a publiquement qualifié la recherche vectorielle de « fonctionnalité, jamais un business ».
- **Architecture polystore** : combiner stockage relationnel et recherche vectorielle avec des frontières claires (plutôt qu'une base 100 % vectorielle isolée) est désormais l'architecture par défaut des équipes qui livrent des produits IA en production, sans sacrifier la cohérence des données.
- **Marché** toujours en forte croissance en valeur : de 2,46 Md$ en 2024 à une projection de 10,6 Md$ en 2032 (TCAC ~27,5 %), malgré la pression concurrentielle sur les purs acteurs spécialisés (Pinecone, Weaviate, Qdrant, Milvus face aux extensions intégrées).

Sources : [Shakudo – Top 9 Vector Databases (Sept. 2026)](https://www.shakudo.io/blog/top-9-vector-databases), [Medium – Vector databases are dying: the production evidence](https://medium.com/data-science-collective/vector-databases-are-dying-heres-the-production-evidence-8c17b54687e2)

---

## 3. Analytique : DuckDB et ClickHouse accélèrent la course à l'optimisation

**DuckDB**
- Sortie de **DuckDB v2.0-alpha**.
- **DuckLabs** (l'éditeur derrière DuckDB) rejoint AWS, tandis que **MotherDuck** annonce l'acquisition de **Tower**.
- Nouveautés côté écosystème : fonctions de table écrites en pur Java, bulk loads directs vers SQL Server, et lecture de stores **Zarr** comme des tables SQL.

**ClickHouse**
- **On-Demand Compute** entre en preview privée : possibilité de délocaliser les charges lourdes vers des workers dédiés, à la demande.
- Un nouvel **optimiseur basé sur les coûts (cost-based optimizer)** et un framework d'exécution de requêtes distribué sont annoncés.
- Après une levée en Series D de 400 M$ portant sa valorisation à 15 Md$, ClickHouse a acquis **Langfuse**, plateforme open source d'observabilité pour applications LLM — signe que l'analytique et l'observabilité IA convergent chez le même éditeur.

Sources : [MotherDuck – DuckDB Ecosystem Newsletter, September 2026](https://motherduck.com/blog/duckdb-ecosystem-newsletter-september-2026/), [ClickHouse – September 2026 newsletter](https://clickhouse.com/blog/202609-newsletter), [Runtime – ClickHouse takes aim at Databricks and Snowflake](https://www.runtime.news/clickhouse-takes-aim-at-databricks-and-snowflake/)

---

## 4. IA connectée nativement aux bases — Oracle généralise son serveur MCP

Après l'annonce de serveurs **MCP (Model Context Protocol)** managés chez Google Cloud (AlloyDB, Bigtable, Cloud SQL, Firestore, Spanner), c'est au tour d'**Oracle** de passer en disponibilité générale son **Autonomous AI Database MCP Server** : une fonctionnalité multi-tenant intégrée nativement à Autonomous AI Database Serverless, compatible avec les versions 19c et 26ai.

Ce serveur expose via MCP les outils définis dans le framework **Select AI Agent**, et permet à des clients comme **Claude Desktop**, **VS Code (extension Cline)** ou **OCI AI Agent** de s'y connecter directement, sans que l'équipe applicative n'ait à héberger elle-même l'infrastructure du serveur MCP.

Le signal est désormais clair sur l'ensemble de l'industrie : l'intégration LLM ↔ base de données quitte définitivement le stade du plugin pour devenir un service managé de premier plan, standardisé autour de MCP plutôt que d'API propriétaires.

Source : [Oracle – Autonomous AI Database MCP Server](https://www.oracle.com/autonomous-database/mcp-server/)

---

## 5. MySQL, MongoDB : petits pas côté écosystème

**MySQL**
- **MySQL Workbench 26** sort en septembre 2026 avec support des **SQL notebooks** et de **Visual Explain**, construit sur MySQL Shell.
- Azure Database for MySQL reçoit sa mise à jour de septembre (montées de version mineures, correctifs de fiabilité) ; tous les nouveaux serveurs créés depuis le 19 septembre utilisent automatiquement cette version.

**MongoDB (Atlas)**
- **Maintenance Waves** passe en GA : les clients Atlas peuvent désormais contrôler l'ordre dans lequel la maintenance est appliquée à leurs clusters.
- **Automatic Alerting and Recovery for External Log Sink Failures** passe également en GA : Atlas détecte et se rétablit automatiquement en cas d'échec d'export de logs vers un puits externe.

Sources : [Microsoft Learn – What's new, Azure Database for MySQL, 2026](https://learn.microsoft.com/en-us/azure/mysql/whats-new/whats-new-2026), [MongoDB – New in MongoDB](https://www.mongodb.com/products/updates/)

---

## Synthèse

| Axe | Signal fort de la semaine |
|---|---|
| Cœur relationnel | PostgreSQL 19 repoussé à fin octobre, 53 features reportées (Property Graph Queries, MERGE/SPLIT PARTITION...) |
| Vecteurs | Le vecteur natif banalise la fonctionnalité ; consolidation du marché des pure players (Pinecone, Elastic) |
| Analytique | DuckDB v2.0-alpha + rachat de DuckLabs par AWS ; ClickHouse lève 400 M$ (valo 15 Md$), rachète Langfuse |
| IA ↔ données | Oracle généralise son serveur MCP natif (Autonomous AI Database), suit Google Cloud |
| Écosystème | MySQL Workbench 26 (SQL notebooks, Visual Explain) ; MongoDB Atlas Maintenance Waves en GA |

> La tendance de la rentrée se confirme semaine après semaine : la base de données individuelle s'efface derrière la plateforme — polystore côté données, MCP côté intégration IA — pendant que les acteurs analytiques (DuckDB, ClickHouse) consolident leur écosystème à coups de levées et de rachats plutôt que de nouvelles features isolées.

---

*Rapport rédigé le 2026-09-21 — Sources principales : documentation officielle PostgreSQL, Oracle, Microsoft, MongoDB ; blogs techniques MotherDuck, ClickHouse ; presse technique (Shakudo, daily.dev, Layerbase, Runtime).*
