# 🪣 Athena

Serverless Interactive Querying Service.

- **Ad-hoc queries on data**: structured, semi-structured and unstructured (XML, JSON, CSV, Apache, CloudTrail, VPC Flowlogs, Glue Data Catalog, Web Server Logs, etc.)
- Pay only for data consumed (and for data stored in S3)
- **Schema-on-read** - Table-like translation (relational-like when read): **NO NEED to load or transform data before**
- **Original data never changed - remains on S3**
- Tabled are defined in advance in a data catalog and **data is projected** through when read: allows **SQL-LIKE QUERIES** on data without transforming source data
- Output can be sent to visualisation tools

> [!IMPORTANT] > **Athena Federated Query** handles other data sources than S3: RDS, DynamoDB, DocumentDB, CloudWatch Logs, MySQL, PostgreSQL, etc.

# ⚡ ElastiCache

In-memory database: can be used to cache data for **READ HEAVY** workloads with **low latency**.

- Managed **Redis**: advanced structured, multi AZ, replication, backup & restore
- Managed **Memcached**: simple data structures, no replication, multi nodes (sharding), no backups, multi threaded
- Reduces database workloads
- Can be used to store Session Data (Stateless Servers)

> [!IMPORTANT] > Requires application code changes.

# 🏢 Redshift

Petabyte-scale Data Warehouse.

- Server based (not serverless)
- **OLAP** (column based) not OLTP (row or transaction based)
- **One AZ in a VPC**
- **Leader Node**: query input, planning and aggregation
- **Compute Node**: performing queries of data
- Pay as you use... similar structure to RDS
- Direct Query S3 using Redshift Spectrum
- Direct Query other DBs using federating query
- Integrates with AWS tooling such as Quicksight
- Firehose can stream data into Redshift
- Connect with standard JDBC/ODBC compatible application and visualisation suite

> [!IMPORTANT] > Redshift Enhanced VPC Routing.

## 💥 Resilience and Recovery

- Automatic incremental backups occur every 8 hours or 5GB of data
- 1 day retention by default (configurable up to 35 days)
- Redshift can be configured to copy snapshots to another region for DR (with a separate configurable retention period)
