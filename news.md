# Actualité technique des bases de données — 2026-09-14

---

## 1. PostgreSQL — cap sur la version 19

PostgreSQL 19 est attendu pour septembre 2026 (feature freeze depuis le 8 avril 2026), pendant que la branche stable reçoit ses correctifs de routine (18.6, 17.11, 16.15, 15.19, 14.24 publiés le 13 août 2026).

**Nouveautés attendues en v19 :**
- Partitionnement amélioré : fusion et découpage de partitions facilités.
- Réplication logique plus économe : réduction de la génération de WAL inutile.
- Outils de supervision et de tuning enrichis pour les DBA.
- Premiers signes d'un support vectoriel natif renforcé (au-delà de l'extension `pgvector`).

Sources : [PostgreSQL 19 : What's Coming (versionlog.com)](https://versionlog.com/blog/postgresql-19-whats-coming-september-2026/), [Releasebot – PostgreSQL](https://releasebot.io/updates/postgresql)

---

## 2. Optimisation des requêtes assistée par IA

L'IA s'installe dans le cœur de l'optimisation SQL plutôt qu'en simple assistant périphérique :

- Des copilotes de tuning affichent des requêtes **jusqu'à 72 % plus rapides** qu'un réglage manuel, avec des gains de vélocité projet de l'ordre de 23 % pour les équipes qui les adoptent.
- Les moteurs d'auto-indexation analysent l'historique d'exécution pour recommander, créer ou supprimer automatiquement des index (partiels, couvrants, sous-utilisés).
- Oracle AI Database 26ai (GA sur Linux x86-64 on-prem) et SQL Server intègrent l'indexation vectorielle **DiskANN** directement dans le moteur pour la recherche sémantique et les requêtes hybrides.

Sources : [Syncfusion – AI for SQL Performance](https://www.syncfusion.com/blogs/post/ai-sql-query-optimization-2026), [Oracle AI Database 26ai GA](https://blogs.oracle.com/database/ga-of-oracle-ai-database-26ai-for-linux-x86-64-on-premises-platforms)

---

## 3. Bases vectorielles : de la nouveauté à l'infrastructure de production

La recherche vectorielle est désormais traitée comme une brique de production classique, plus comme une curiosité liée aux LLM :

- Les déploiements atteignent l'échelle du milliard : HubSpot opère une recherche sémantique sur **20 milliards de vecteurs**.
- Le **filtrage par métadonnées**, et non le calcul de similarité lui-même, est identifié comme le principal goulot d'étranglement à grande échelle (retour d'expérience Reddit, 340 M+ de vecteurs).
- La recherche **hybride** (dense + BM25/plein texte) s'impose comme standard de facto pour le RAG en entreprise, au détriment des bases 100 % vectorielles isolées.
- Qdrant mise sur la « recherche composable » : combiner dans une même requête vecteurs denses, vecteurs épars, filtres et scoring personnalisé.
- Un phénomène de « benchmarks maison » brouille la comparaison objective des moteurs vectoriels — à prendre avec prudence.

Sources : [Redis – Vector Search Database News 2026](https://redis.io/blog/vector-search-database-news-2026-guide/), [Shakudo – Top 9 Vector Databases](https://www.shakudo.io/blog/top-9-vector-databases)

---

## 4. HTAP en recul, place au « zero-ETL » et au LTAP

Le narratif HTAP (un seul moteur pour OLTP + OLAP) marque le pas :

- En pratique, l'industrie a plutôt construit du **« zero-ETL »** : deux moteurs séparés reliés par une synchronisation managée (AWS, mais aussi Snowflake et Databricks qui ont chacun ajouté un moteur OLTP dédié plutôt que d'étendre leur moteur analytique).
- Databricks a annoncé le concept de **LTAP** (Lake Transactional/Analytical Processing) lors de son Data + AI Summit (16 juin 2026), déclarant la catégorie HTAP dépassée. Son *Lakebase* fait tourner Postgres en compute sans état au-dessus de deux services de stockage : un WAL répliqué par quorum et un service de pages qui matérialise le log vers de l'object storage.
- La séparation compute/stockage (à la TiDB : TiDB server / TiKV / TiFlash) reste le principe architectural dominant pour scaler indépendamment les deux dimensions.

Sources : [ClickHouse – Unifying OLTP and OLAP](https://clickhouse.com/resources/engineering/unifying-oltp-and-olap), [Datapace – LTAP vs HTAP](https://datapace.ai/blog/ltap-vs-htap)

---

## 5. MySQL, MongoDB, SQL Server : où en sont les écosystèmes

**MySQL**
- 8.4 LTS reste la version stable de référence (support jusqu'en avril 2032).
- MySQL 9.x introduit l'optimiseur **HyperGraph** (meilleurs plans de jointure) et le support natif du type **VECTOR**.
- Cloud : Azure Database for MySQL a ouvert en juillet 2026 le stockage Premium SSD v2 avec mise à l'échelle indépendante de la capacité, des IOPS et du débit.

**MongoDB**
- Investit sur la recherche vectorielle native, l'auto-scaling serverless et l'analytique temps réel intégrée.
- Relational Migrator suit désormais MySQL 8.4/9.0 pour les migrations.

**SQL Server / Azure SQL**
- Indexation vectorielle DiskANN et *Intelligent Query Processing* poursuivent leur montée en puissance.
- Le pilote `mssql-python` 1.5.0 ajoute le support Apache Arrow, `sql_variant` et les UUID natifs.
- Support des fuseaux horaires locaux étendu à Hyperscale, Managed Instance et SQL Database in Fabric.

Sources : [Azure Database for MySQL – What's new 2026](https://learn.microsoft.com/en-us/azure/mysql/whats-new/whats-new-2026), [Microsoft Fabric Community – SQL en 2026](https://community.fabric.microsoft.com/blog/fbc_fabricupdatesblogs/what%E2%80%99s-new-across-microsoft-sql-in-2026-so-far-sql-server-azure-sql-and-sql-data/5221163)

---

## 6. IA connectée nativement aux bases — le protocole MCP s'impose

Google Cloud a annoncé des serveurs **MCP (Model Context Protocol)** managés et distants pour AlloyDB, Bigtable, Cloud SQL, Firestore et Spanner, avec une extension prévue à d'autres services. L'objectif : permettre à des modèles d'IA de se connecter directement et de façon sécurisée aux bases de données opérationnelles, sans que le client ait à gérer l'infrastructure du serveur MCP lui-même. C'est un signal fort : l'intégration LLM ↔ base de données quitte le stade du plugin pour devenir un service managé de premier plan.

Source : [Google Cloud Blog – What's new for Google Cloud databases at Next'26](https://cloud.google.com/blog/products/databases/whats-new-for-google-cloud-databases-at-next26)

---

## 7. Oracle AI Database 26ai : l'IA comme socle, pas comme module

Avec plus de 300 nouveautés, Oracle AI Database 26ai (désormais GA on-prem sur Linux x86-64) pousse l'approche « AI-native » :
- **AI Vector Search** pour générer, indexer et interroger par similarité des vecteurs de documents, images ou sons.
- **Automatic Transaction Rollback** : détection et annulation automatique des transactions longues ou bloquantes pour protéger la performance globale.
- **Autonomous AI Lakehouse** avec support du format ouvert **Apache Iceberg**, pour unifier analytique et IA à l'échelle de l'entreprise.

Source : [Oracle – AI Database 26ai](https://www.oracle.com/database/ai-native-database-26ai/)

---

## Synthèse

| Axe | Signal fort de la rentrée 2026 |
|---|---|
| Cœur relationnel | PostgreSQL 19 en approche (partitionnement, réplication logique allégée) |
| Optimisation | Tuning piloté par IA, auto-indexation, DiskANN embarqué |
| Vecteurs | Passage à l'échelle production, filtrage métadonnées = nouveau goulot, recherche hybride dominante |
| Architecture | Recul du HTAP « un seul moteur », montée du zero-ETL et du LTAP (Databricks Lakebase) |
| Écosystème | MySQL 9 (HyperGraph, VECTOR), SQL Server (Arrow, UUID natif), MongoDB (vector + serverless) |
| IA ↔ données | MCP managé chez Google Cloud, Oracle AI Database 26ai « AI-native » |

> La tendance de fond ne change pas de cap depuis le printemps 2026, elle s'accélère : les bases de données ne sont plus jugées seulement sur leur capacité de stockage ou de calcul brut, mais sur leur aptitude à raisonner sur les données (IA embarquée, recherche hybride) et à s'intégrer nativement aux agents et modèles qui les consomment.

---

*Rapport rédigé le 2026-09-14 — Sources principales : documentation officielle PostgreSQL, Oracle, Microsoft, Google Cloud, Databricks, ClickHouse, Redis ; presse technique (versionlog.com, Releasebot, DBTA, Syncfusion, Shakudo).*
