# 📅 Week 5 – Cosmos DB Multi-Model APIs (Mongo, Cassandra, Table)

🧠 **Learning Goal**: Explore and master Cosmos DB’s alternative APIs beyond Core SQL — including MongoDB API, Cassandra API, and Table API — for polyglot persistence and real-world migrations.

🕒 **Daily Time Budget**: 2–3 hours

---

## 🗓 Day 1: Cosmos DB – MongoDB API Overview + Hands-On

### 🎯 Objectives
- Understand Cosmos DB’s MongoDB API compatibility layer
- Query and manage data using native Mongo tools and drivers
- Compare MongoDB API modeling, indexing, and throughput behavior to Core SQL API

---

### 🔍 Key Concepts
- **Mongo API in Cosmos DB**:
  - Wire protocol compatible with MongoDB 3.2–5.0
  - Supports MongoDB SDKs, Compass, and shell
  - Cosmos still charges per RU (not ops/sec model like native Mongo)
- **Feature Compatibility**:
  - Most CRUD, aggregation, TTL, and indexing commands supported
  - Limited support for transactions, joins, $lookup, etc.
- **Data Modeling Differences**:
  - Schema-less (BSON/JSON) like native Mongo
  - Still requires a **partition key** for large containers
- **Indexing**:
  - Automatic indexing on all fields by default
  - Supports compound and TTL indexes

---

### 🔧 Hands-On
- Deploy Cosmos DB account with Mongo API (via Portal or CLI)
- Connect using Mongo Shell or Compass
- Create a database and collection with a compound shard key
- Run CRUD and aggregation commands, test performance

```bash
az cosmosdb create \
  --name myMongoCosmos \
  --resource-group myRG \
  --kind MongoDB \
  --locations regionName=eastus failoverPriority=0
```

```js
// Mongo shell
use ecommerce;
db.products.insertOne({ sku: "P001", category: "shoes", price: 99.99 });
db.products.createIndex({ sku: 1, category: 1 });
```

---

### 🧠 Deep Dive: Cosmos vs Native MongoDB Comparison
| Feature                  | Cosmos Mongo API        | Native MongoDB     |
|--------------------------|--------------------------|---------------------|
| Query Language           | MongoDB shell/SDK        | Mongo shell/SDK     |
| Pricing Model            | RU-based (Cosmos model)  | Ops/sec or VM-based |
| Global Distribution      | Built-in multi-region    | Manual sharding     |
| TTL, Indexing            | Supported (some limits)  | Full feature set    |
| Transactions             | Limited (single-partition)| Multi-doc supported |

---

### 🧪 Mini-Lab
- Insert and query 1000+ documents
- Run aggregation pipeline on a field (e.g., `$group`, `$match`)
- Create TTL index and observe automatic expiry
- Simulate write skew and observe RU consumption vs Mongo shell expectations

---

### ✅ Practice Tasks
- Test common Mongo queries: insert, update, find, $regex
- Build aggregation: group by category, sum price
- Configure autoscale and observe RU trends in Monitor
- Compare Compass stats vs Cosmos metrics (latency, throughput)

---

### 📦 Deliverables
- Mongo Shell Command Log (with RU comments)
- Cosmos Mongo API vs Native Mongo Comparison Sheet
- Aggregation Pipeline Sample Scripts
- RU Cost Breakdown Report (insert/query/aggregate)


---

## 🗓 Day 2: Cosmos DB – Cassandra API + CQL Tuning

### 🎯 Objectives
- Use Cosmos DB with Cassandra-compatible workloads and CQL queries
- Explore wide-column modeling, partition keys, and clustering keys
- Optimize read/write patterns and analyze RU costs for CQL operations

---

### 🔍 Key Concepts
- **Cassandra API in Cosmos DB**:
  - Supports CQLv4 and Cassandra drivers (Java, .NET, Python, etc.)
  - Wire-protocol compatible with open-source Cassandra
  - RU-based pricing model (not token-based throughput)
- **Schema Design**:
  - Tables = wide-column families
  - Partition Key = hash-distribution for scalability
  - Clustering Columns = sorting and range queries within partition
- **Compatibility Considerations**:
  - Supports basic data types, static columns, TTL
  - Does NOT support Lightweight Transactions (LWT), user-defined types (UDT), or materialized views

---

### 🔧 Hands-On
- Deploy Cosmos DB with Cassandra API (via CLI or Portal)
- Connect via `cqlsh` or DataStax Studio
- Create table with compound primary key and TTL
- Run CQL: INSERT, SELECT, UPDATE, BATCH, CREATE INDEX

```sql
CREATE TABLE inventory (
  product_id text,
  region text,
  stock int,
  last_updated timestamp,
  PRIMARY KEY ((product_id), region)
) WITH default_time_to_live = 3600;
```

---

### 🧠 Deep Dive: Partitioning, Clustering & Performance
| Design Choice          | Impact on RU / Latency               |
|------------------------|--------------------------------------|
| Large partition key    | High availability, balanced load     |
| Small partitions       | Poor read aggregation performance    |
| Clustering keys        | Range scan within partition (efficient)|
| Secondary indexes      | High RU, avoid for large datasets    |

---

### 🧪 Mini-Lab
- Insert 5000 rows with varying `product_id`
- Query with and without clustering key
- Compare query latency + RU for secondary index vs partition scan
- Enable TTL and validate expiry using `SELECT *` after 1 hour

---

### ✅ Practice Tasks
- Build CQL model with compound PK
- Analyze RU for insert, query, update, and secondary index
- Enable autoscale + diagnostic logs to monitor latency
- Generate data heatmap based on partition key cardinality

---

### 📦 Deliverables
- CQL schema + workload script (.cql file)
- Cosmos Cassandra API vs Native Cassandra Feature Matrix
- RU Analysis Report for read vs write patterns
- TTL Validation Report + Expiry Logs



---

## 🗓 Day 3: Cosmos DB – Table API + Key-Value Storage Patterns

### 🎯 Objectives
- Use Cosmos DB Table API for scalable key-value data access
- Build and query partitioned tables with .NET and REST APIs
- Understand storage schema, indexing, and performance tuning

---

### 🔍 Key Concepts
- **Table API Overview**:
  - Compatible with Azure Table Storage SDKs
  - REST-based or Storage SDK-based access
  - Seamless migration path from classic Table Storage
- **Data Structure**:
  - Key-Value store with fixed schema: `PartitionKey`, `RowKey`, `Timestamp`
  - Additional properties are schema-less
- **Indexing & Throughput**:
  - All properties are indexed automatically
  - RU/s charged per operation (writes, queries)
  - Supports autoscale + partition-level scaling
- **Use Cases**:
  - IoT device metrics, audit logs, user profiles, lookup tables

---

### 🔧 Hands-On
- Deploy Cosmos DB with Table API using Azure CLI
- Insert and query entities using Azure.Data.Tables SDK (.NET)
- Build a basic API to retrieve entities by PartitionKey/RowKey
- Enable TTL on entities and validate auto-expiry

```csharp
var tableClient = new TableClient(connectionString, "Users");
await tableClient.AddEntityAsync(new TableEntity("region1", "user42")
{
    { "Name", "Alice" },
    { "Email", "alice@example.com" },
    { "Role", "admin" }
});
```

---

### 🧠 Deep Dive: Table API Design Best Practices
| Practice                     | Benefit                                  |
|-----------------------------|------------------------------------------|
| Narrow entities (fewer fields) | Lower RU for reads/writes               |
| High cardinality PartitionKey | Better write scale-out                  |
| RowKey = unique within partition | Ideal for fast key-based lookups      |
| Use `Timestamp`              | Built-in for sync and freshness tracking |

---

### 🧪 Mini-Lab
- Insert 1000 entities across 5 partitions
- Query by PartitionKey, then by PartitionKey+RowKey
- Validate TTL by setting expiry on a batch of entities
- Compare performance vs traditional Table Storage (if available)

---

### ✅ Practice Tasks
- Model a key-value entity store (user sessions, IoT, etc.)
- Measure RU for point read vs filtered query
- Enable logs and analyze throughput vs latency
- Build a retry wrapper using exponential backoff for throttling

---

### 📦 Deliverables
- Table API CRUD Script (SDK or REST)
- Cosmos Table vs Azure Table Comparison Sheet
- TTL Entity Test Log
- RU vs Operation Report (Read, Insert, Filter)



---

## 🗓 Day 4: Multi-API Comparison – Design & Indexing Behavior

### 🎯 Objectives
- Compare Cosmos DB APIs (Core SQL, Mongo, Cassandra, Table) across modeling, indexing, and performance
- Evaluate use cases and architectural trade-offs for each API
- Build a selection matrix to guide API choice by workload

---

### 🔍 Key Concepts
- **API Differences Overview**:
  - Core (SQL API): JSON documents, strong consistency support, SQL-like querying
  - Mongo API: BSON format, supports aggregation pipeline, partial feature coverage
  - Cassandra API: CQL interface, wide-column model, partition + clustering keys
  - Table API: Classic Azure Table compatibility, PartitionKey/RowKey for key-value
- **Indexing Behavior**:
  - Core SQL: All fields indexed by default, customizable
  - Mongo: Automatic indexing with support for compound indexes
  - Cassandra: Primary key + secondary indexes (limited)
  - Table: PartitionKey + RowKey only, full automatic indexing
- **RU Behavior + SDK Support**:
  - SDKs available for all APIs; usage models differ (e.g., bulk insert for Core SQL vs batch for Cassandra)

---

### 🔧 Hands-On
- Compare CRUD, Query, Index creation across APIs using scripts
- Measure RU cost per operation using Monitor or SDK diagnostics
- Run equivalent aggregation or query patterns across APIs
- Analyze support for TTL, unique constraints, secondary indexes

---

### 🧠 Deep Dive: API Comparison Matrix
| Feature / Behavior      | Core SQL     | Mongo API    | Cassandra API | Table API     |
|-------------------------|--------------|--------------|----------------|---------------|
| Data Format             | JSON         | BSON         | Rows/Columns   | Key-Value     |
| Schema Flexibility      | High         | High         | Medium         | Low           |
| Index Customization     | Yes          | Yes          | Limited        | Minimal       |
| Multi-Region Writes     | Supported    | Supported    | Supported      | Supported     |
| TTL Support             | Yes          | Yes          | Yes            | Yes           |
| Transactions            | Limited      | Single doc   | Not supported  | Not supported |
| Best for                | Rich querying| Mongo apps   | Time-series    | Lookups/logs  |

---

### 🧪 Mini-Lab
- Insert and query similar schema in all 4 APIs
- Capture RU for single insert, read, and aggregation (where applicable)
- Identify limitations per API and log unsupported operations

---

### ✅ Practice Tasks
- Design the same entity model (e.g., order processing) in each API
- Run RU benchmark across CRUD operations
- Build an API selection decision tree by workload profile
- Document interoperability risks and migration strategy

---

### 📦 Deliverables
- Cosmos API Comparison Matrix (Markdown/Excel)
- CRUD + Query Script Bundle (4 API variants)
- RU vs Latency Report per API
- Decision Tree Chart: "Which API Should I Use?"



---

## 🗓 Day 4: Multi-API Comparison – Design & Indexing Behavior

### 🎯 Objectives
- Compare Cosmos DB APIs (Core SQL, Mongo, Cassandra, Table) across modeling, indexing, and performance
- Evaluate use cases and architectural trade-offs for each API
- Build a selection matrix to guide API choice by workload

---

### 🔍 Key Concepts
- **API Differences Overview**:
  - Core (SQL API): JSON documents, strong consistency support, SQL-like querying
  - Mongo API: BSON format, supports aggregation pipeline, partial feature coverage
  - Cassandra API: CQL interface, wide-column model, partition + clustering keys
  - Table API: Classic Azure Table compatibility, PartitionKey/RowKey for key-value
- **Indexing Behavior**:
  - Core SQL: All fields indexed by default, customizable
  - Mongo: Automatic indexing with support for compound indexes
  - Cassandra: Primary key + secondary indexes (limited)
  - Table: PartitionKey + RowKey only, full automatic indexing
- **RU Behavior + SDK Support**:
  - SDKs available for all APIs; usage models differ (e.g., bulk insert for Core SQL vs batch for Cassandra)

---

### 🔧 Hands-On
- Compare CRUD, Query, Index creation across APIs using scripts
- Measure RU cost per operation using Monitor or SDK diagnostics
- Run equivalent aggregation or query patterns across APIs
- Analyze support for TTL, unique constraints, secondary indexes

---

### 🧠 Deep Dive: API Comparison Matrix
| Feature / Behavior      | Core SQL     | Mongo API    | Cassandra API | Table API     |
|-------------------------|--------------|--------------|----------------|---------------|
| Data Format             | JSON         | BSON         | Rows/Columns   | Key-Value     |
| Schema Flexibility      | High         | High         | Medium         | Low           |
| Index Customization     | Yes          | Yes          | Limited        | Minimal       |
| Multi-Region Writes     | Supported    | Supported    | Supported      | Supported     |
| TTL Support             | Yes          | Yes          | Yes            | Yes           |
| Transactions            | Limited      | Single doc   | Not supported  | Not supported |
| Best for                | Rich querying| Mongo apps   | Time-series    | Lookups/logs  |

---

### 🧪 Mini-Lab
- Insert and query similar schema in all 4 APIs
- Capture RU for single insert, read, and aggregation (where applicable)
- Identify limitations per API and log unsupported operations

---

### ✅ Practice Tasks
- Design the same entity model (e.g., order processing) in each API
- Run RU benchmark across CRUD operations
- Build an API selection decision tree by workload profile
- Document interoperability risks and migration strategy

---

### 📦 Deliverables
- Cosmos API Comparison Matrix (Markdown/Excel)
- CRUD + Query Script Bundle (4 API variants)
- RU vs Latency Report per API
- Decision Tree Chart: "Which API Should I Use?"



---

## 🗓 Day 6: Migration Paths – Mongo/Cassandra to Cosmos

### 🎯 Objectives
- Plan and execute migrations from native MongoDB and Cassandra to Cosmos DB
- Use first-party and open-source tools for data + schema migration
- Validate consistency, feature compatibility, and rollback strategy

---

### 🔍 Key Concepts
- **Migration Tooling**:
  - **Mongo to Cosmos**:
    - Azure Data Migration Service (DMS)
    - `mongodump` / `mongorestore` with Cosmos Mongo endpoint
    - Studio 3T or Compass (manual import/export)
  - **Cassandra to Cosmos**:
    - Spark + DataStax connector
    - Cassandra `COPY` command for export/import
    - Custom ETL using Apache Beam, Azure Data Factory

- **Key Migration Steps**:
  1. Schema validation
  2. Partition key + throughput planning
  3. Bulk data export and transformation
  4. Data load and integrity checks
  5. Application retargeting (connection string switch)

- **Feature Compatibility Gaps**:
  - Mongo: `$lookup`, schema validation rules, transactions
  - Cassandra: UDTs, triggers, materialized views

---

### 🔧 Hands-On
- Export MongoDB data using `mongodump`
- Import to Cosmos Mongo API using `mongorestore`
- Run Spark job to read Cassandra and write to Cosmos Cassandra API
- Compare performance, RU usage, and data shape

```bash
mongodump --uri="mongodb://localhost:27017/ecommerce" --out=./dump/
mongorestore --uri="mongodb://<cosmos-endpoint>:10255/ecommerce" ./dump/
```

---

### 🧠 Deep Dive: Migration Strategy Matrix
| Source     | Cosmos API Target | Method         | Notes                                |
|------------|-------------------|----------------|--------------------------------------|
| MongoDB    | Mongo API         | DMS / Dump     | Watch for unsupported operators      |
| Cassandra  | Cassandra API     | Spark / COPY   | Validate partitioning + data types   |
| MongoDB    | SQL API           | Custom ETL     | Allows richer query features         |

---

### 🧪 Mini-Lab
- Migrate 10K documents from MongoDB to Cosmos Mongo API
- Export Cassandra table and insert to Cosmos Cassandra API
- Run read/write workload to compare RU and latency
- Enable diagnostic logs and validate parity

---

### ✅ Practice Tasks
- Map Mongo schema to Cosmos BSON format and test CRUD
- Build Spark pipeline to migrate Cassandra dataset
- Validate row counts, TTL behavior, and indexing
- Document delta in features and config recommendations

---

### 📦 Deliverables
- Migration Execution Scripts (mongodump, spark job, etc.)
- Compatibility Mapping Sheet (source → Cosmos API)
- Pre/Post Migration RU & Latency Report
- Rollback + Retargeting Strategy Document



---

## 🗓 Day 7: Polyglot Patterns & Evaluation Lab

### 🎯 Objectives
- Apply all Cosmos DB APIs (Core SQL, Mongo, Cassandra, Table) in a real-world polyglot architecture
- Evaluate trade-offs, performance, and modeling differences
- Create reusable decision framework for API selection

---

### 🔍 Key Concepts
- **Polyglot Persistence**:
  - Use the right API for each subdomain within a system
  - Avoid one-size-fits-all modeling
- **Domain Examples**:
  | Domain             | Recommended API     | Reason                          |
  |--------------------|----------------------|---------------------------------|
  | Product Catalog     | Core SQL             | Query flexibility + filters     |
  | Shopping Cart       | Mongo API            | Schema flexibility, JSON nested |
  | Inventory Events    | Cassandra API        | Time-series, fast inserts       |
  | User Sessions       | Table API            | Fast key-value lookup           |
- **API Contracts**:
  - Normalize API inputs/outputs for multi-model reads
  - Use abstraction in app services (Repository pattern)
- **Performance Profile**:
  - Benchmark RU/s, latency, cost across API for same use case

---

### 🔧 Hands-On
- Create one logical workload (e.g., e-commerce app)
- Implement each microservice with a different Cosmos API
- Standardize monitoring across APIs using RU, latency, diagnostics
- Write façade service to aggregate results across APIs

---

### 🧠 Deep Dive: API Selection Decision Tree
| Requirement                          | Recommended Cosmos API |
|--------------------------------------|--------------------------|
| Complex queries, joins, filters       | Core SQL                |
| Nested documents, quick prototyping  | Mongo API               |
| Write-heavy, time-ordered rows       | Cassandra API           |
| High-speed key-value reads/writes    | Table API               |

---

### 🧪 Mini-Lab
- Simulate e-commerce workflow:
  - Add product → Core SQL
  - Add item to cart → Mongo API
  - Inventory deduction → Cassandra API
  - Session update → Table API
- Track RU usage per API and consolidate metrics

---

### ✅ Practice Tasks
- Model same data in 4 APIs and compare schema size and cost
- Build abstraction layer in backend code
- Create visualization: cost vs flexibility vs consistency
- Prepare recommendation sheet for future polyglot projects

---

### 📦 Deliverables
- End-to-end Demo App (4 APIs working in tandem)
- Unified Cosmos Monitoring Dashboard
- API Comparison Summary (performance + cost)
- Polyglot Recommendation Framework



