# 📅 Week 6 – Polyglot Persistence & System Design Patterns

🧠 **Learning Goal**: Learn to architect real-world systems that leverage both Cosmos DB and SQL strategically, applying best practices in persistence choice, integration, and cloud-native design.

🕒 **Daily Time Budget**: 2–3 hours

---

## 🗓 Day 1: Polyglot Persistence – When to Use SQL vs NoSQL

### 🎯 Objectives
- Learn architectural patterns for combining SQL and NoSQL effectively
- Understand the strengths and trade-offs of relational vs non-relational systems
- Use decision matrices to choose the right database engine for a workload

---

### 🔍 Key Concepts
- **Relational Databases (SQL)**:
  - ACID compliance, relational joins, strong schema enforcement
  - Best for structured, transactional workloads
  - Mature ecosystem (OLTP/OLAP, reporting, integrity)
- **NoSQL (Cosmos DB APIs)**:
  - Schema-flexible, horizontally scalable, eventual consistency
  - Best for high-ingest, large-scale, or rapidly evolving data models
  - Query limitations vary by API

---

### 🔧 Architecture Patterns
- **Polyglot Backend**:
  - Use SQL for billing/analytics, NoSQL for session/user data
- **Write vs Read Store Split**:
  - Write-heavy data to Cosmos, analytical layer in SQL
- **Transactional + Eventual Hybrid**:
  - SQL for financial data; Cosmos for customer activity streams

---

### 🧠 Deep Dive: SQL vs NoSQL Trade-off Matrix
| Dimension         | SQL (Relational)         | NoSQL (Cosmos DB)             |
|------------------|--------------------------|-------------------------------|
| Schema           | Fixed schema (strict)     | Dynamic (flexible JSON)       |
| Scaling          | Vertical / read-replica   | Horizontal / partitioned      |
| Transactions     | Full multi-row ACID       | Limited or partition-scoped   |
| Consistency      | Strong                    | Configurable (5 levels)       |
| Indexing         | Fine-tuned, custom        | Auto-indexing by default      |
| Latency          | High for joins, low for PK| Very low on key reads         |
| Use Cases        | OLTP, OLAP, reporting     | IoT, gaming, social, session  |

---

### 🧪 Mini-Lab
- Model an online education platform:
  - Courses, users, payments in SQL
  - Session activity, chat logs, file metadata in Cosmos
- Define table + container structure
- Map query types (JOINs, filters, aggregates)
- Annotate scaling vs cost differences

---

### ✅ Practice Tasks
- Create a use-case based SQL vs NoSQL selection chart
- Model a workload in both paradigms and benchmark RU vs query complexity
- Build a system diagram showing data flow across services
- Summarize when to use hybrid vs single DB model

---

### 📦 Deliverables
- SQL vs NoSQL Trade-off Decision Matrix
- Workload Modeling Samples (SQL + Cosmos)
- System Design Diagram with Justification
- Query Latency + Cost Comparison Sheet



---

## 🗓 Day 2: Data Modeling Patterns Across Systems

### 🎯 Objectives
- Design data models tailored for SQL and NoSQL environments
- Explore real-world examples of hybrid modeling for operational + analytical workflows
- Implement materialized views, denormalization, and entity mapping strategies

---

### 🔍 Key Concepts
- **SQL Modeling**:
  - Normalization (3NF+), foreign keys, constraints
  - Fact + dimension tables for OLAP
  - Views for logical abstraction
- **NoSQL Modeling**:
  - Embed vs reference documents
  - Partition key-driven design
  - Schema-on-read (vs schema-on-write in SQL)
- **Hybrid Modeling**:
  - OLTP in SQL + hot data in Cosmos DB
  - Aggregates in Cosmos → denormalized joins in SQL
  - Change Feed from NoSQL → update warehouse

---

### 🔧 Hands-On
- Model an e-commerce domain in both SQL and Cosmos DB
  - SQL: Customers, Orders, Products (3NF)
  - Cosmos: Orders container with embedded product summary
- Write equivalent queries in T-SQL and Cosmos SQL API
- Compare query shape, latency, and update logic

```sql
-- SQL Orders Table (Normalized)
CREATE TABLE Orders (
  OrderID INT PRIMARY KEY,
  CustomerID INT FOREIGN KEY REFERENCES Customers(CustomerID),
  OrderDate DATETIME
);

-- Cosmos Order Document (Embedded)
{
  "orderId": "123",
  "customerId": "C001",
  "products": [
    { "sku": "P100", "price": 29.99 },
    { "sku": "P200", "price": 59.99 }
  ]
}
```

---

### 🧠 Deep Dive: Modeling Comparison Matrix
| Dimension             | SQL                          | Cosmos DB (NoSQL)                |
|----------------------|-------------------------------|----------------------------------|
| Data Integrity        | Enforced via constraints      | Application-level responsibility |
| Joins                | Native                        | Manual or embedded references    |
| Denormalization      | Avoided unless for perf       | Preferred for read speed         |
| Updates              | Trickle updates via FK logic  | Must update nested documents     |
| Reporting            | Views, Materialized Views     | Change Feed to downstream sinks  |

---

### 🧪 Mini-Lab
- Model a blog platform with authors, posts, comments
- SQL: normalize into 3 tables
- Cosmos: embed comments inside posts
- Query latest 5 posts with author info and comment count

---

### ✅ Practice Tasks
- Normalize a use case into SQL tables
- Denormalize the same into Cosmos document model
- Create schema diagrams for both
- Annotate trade-offs in indexing, performance, and storage cost

---

### 📦 Deliverables
- SQL + Cosmos Data Models (ERD + JSON)
- Schema Design Comparison Sheet
- Example Query Plan Comparison (latency + RU)
- Use Case Mapping Template (SQL ↔ NoSQL)



---

## 🗓 Day 3: Transaction Patterns – ACID, BASE, Idempotency

### 🎯 Objectives
- Understand the differences between ACID and BASE models in SQL and NoSQL systems
- Design reliable transaction flows with idempotent write patterns
- Implement retry-safe, partial failure-resilient operations across systems

---

### 🔍 Key Concepts
- **ACID (SQL)**:
  - Atomicity, Consistency, Isolation, Durability
  - Multi-table, cross-row transactional support
  - Serializable and snapshot isolation
- **BASE (NoSQL)**:
  - Basically Available, Soft state, Eventual consistency
  - Writes may propagate asynchronously
  - Data anomalies expected without strong constraints
- **Cosmos DB Transactions**:
  - Limited to a single logical partition (stored procedure or batch API)
  - Cannot span collections or partitions
- **Idempotency**:
  - Crucial in distributed environments
  - Ensures safe replays or retries of the same operation
  - Implemented via unique keys, versioning, state checks

---

### 🔧 Hands-On
- Run a SQL transaction for order placement + inventory decrement
- Simulate the same in Cosmos using transactional batch (same PK)
- Implement retry logic with idempotency token (e.g., Order ID)
- Use Cosmos stored procedure to validate partial rollback logic

```sql
-- SQL Transaction
BEGIN TRANSACTION;
  INSERT INTO Orders (...) VALUES (...);
  UPDATE Inventory SET Quantity = Quantity - 1 WHERE ProductID = 'P123';
COMMIT;
```

```json
// Cosmos Batch (same partition)
[
  { "operationType": "Create", "resourceBody": { ... } },
  { "operationType": "Replace", "id": "P123", "patchBody": [ ... ] }
]
```

---

### 🧠 Deep Dive: Reliability Pattern Matrix
| Pattern                      | SQL (ACID)           | Cosmos (BASE + Idempotency)       |
|-----------------------------|----------------------|------------------------------------|
| Multi-Entity Write          | Supported (any rows) | Only within same partition         |
| Read-Modify-Write           | Transactional        | Needs ETag or conditional writes   |
| Retry on Failure            | Rollback on error    | Use retry policy + idempotent key  |
| Audit Trail                 | Built-in (logs, triggers) | Manual or Change Feed based  |

---

### 🧪 Mini-Lab
- Model a bank transfer: debit + credit in SQL vs Cosmos
- Force a retry loop and verify idempotency behavior
- Record consistency and latency per approach
- Introduce concurrency and observe conflict resolution

---

### ✅ Practice Tasks
- Design a retry-safe purchase flow using Cosmos + SQL
- Use conditional upsert (Cosmos) and compare to SQL MERGE
- Create an idempotency layer with timestamp + hash
- Analyze trade-offs in transaction granularity vs throughput

---

### 📦 Deliverables
- Transaction Scripts (SQL + Cosmos Batch)
- Idempotency Token Design Reference
- Retry Failure Simulation Logs
- ACID vs BASE Use Case Alignment Chart



---

## 🗓 Day 4: Real-Time and Batch Integration (Cosmos + SQL)

### 🎯 Objectives
- Integrate Cosmos DB and SQL Server using real-time and batch data pipelines
- Use Change Feed, ADF, Synapse Link, and Stream Analytics to sync and enrich data
- Design event-driven and scheduled workflows across systems

---

### 🔍 Key Concepts
- **Integration Modes**:
  - **Real-Time**:
    - Cosmos Change Feed → Azure Functions / Event Hub → SQL insert
    - Stream Analytics → Cosmos → SQL or Synapse
  - **Batch**:
    - Azure Data Factory: scheduled copy with transformation
    - Synapse Pipelines: integrated with Cosmos DB connector
- **Data Sync Patterns**:
  - Append-only log processing
  - UPSERT to avoid duplication
  - Event Sourcing → snapshot + audit table in SQL
- **Enrichment Patterns**:
  - Cosmos writes → analytics/staging in SQL
  - Denormalized aggregates → materialized views

---

### 🔧 Hands-On
- Connect Cosmos DB to ADF and copy to Azure SQL DB
- Create Change Feed-triggered Function to insert new records in SQL
- Build Stream Analytics job to aggregate Cosmos events into SQL
- Implement logging and retry policies on failure

```json
// Stream Analytics Query
SELECT userId, COUNT(*) AS loginCount
INTO SQL_DB.LoginSummary
FROM CosmosEvents
GROUP BY userId, TumblingWindow(minute, 5)
```

---

### 🧠 Deep Dive: Integration Strategy Matrix
| Mode         | Tool / Service        | Latency   | Use Case                        |
|--------------|------------------------|-----------|----------------------------------|
| Real-Time    | Change Feed + Function | ~ms–sec   | Activity tracking, live updates |
| Real-Time    | Stream Analytics       | <1 min    | Aggregates, alerts              |
| Batch        | ADF / Synapse          | ~minutes  | Daily sync, historical ETL      |

---

### 🧪 Mini-Lab
- Trigger document insert in Cosmos
- Stream to SQL summary table in real-time
- Build ADF batch job to sync Cosmos container to reporting table
- Compare latency and error handling across pipelines

---

### ✅ Practice Tasks
- Configure Synapse Link or ADF connector for Cosmos
- Use Change Feed with Function to update SQL logs
- Simulate schema drift and build schema mapping layer
- Audit row count accuracy across systems

---

### 📦 Deliverables
- Cosmos → SQL Integration Pipeline (code or screenshot)
- Real-Time Event Processor (Stream or Function)
- Latency Comparison Report (Real-time vs Batch)
- Integration Strategy Design Document



---

## 🗓 Day 5: Multi-Tenant SaaS Architecture (Hybrid Backend)

### 🎯 Objectives
- Design a scalable multi-tenant SaaS backend using Cosmos DB and SQL
- Implement per-tenant isolation, shared schema, and cost-optimized resource management
- Leverage Cosmos for operational data and SQL for analytics/billing

---

### 🔍 Key Concepts
- **Tenant Isolation Models**:
  - **Database-per-tenant**: Strongest isolation, high cost
  - **Shared database, schema-per-tenant**: Medium isolation
  - **Shared schema, tenant ID column**: Best for scale
- **Cosmos Multi-Tenant Design**:
  - PartitionKey = `tenantId`
  - Use RU throttling to protect noisy neighbors
  - RBAC and AAD tokens to isolate access
- **SQL for Cross-Tenant Reporting**:
  - ETL or Change Feed → SQL
  - Use views to segment per-tenant reports
  - Row-level security (RLS) or secure views
- **Observability**:
  - Per-tenant logging, telemetry, and query metrics
  - Azure Monitor: PartitionKey-filtered dashboards

---

### 🔧 Hands-On
- Model users, usage logs, and tenant metadata
- Build Cosmos container with partition key on `tenantId`
- Add RLS in SQL to isolate tenant views
- Simulate load from multiple tenants and monitor RU usage

---

### 🧠 Deep Dive: Tenant Strategy Matrix
| Design Choice           | Cosmos DB                      | Azure SQL                      |
|-------------------------|--------------------------------|--------------------------------|
| Isolation               | Partition per tenant           | Schema or RLS per tenant       |
| Analytics               | Change Feed → downstream       | Views + aggregation            |
| Cost Optimization       | Shared container + RU scaling  | Elastic pools + Auto-pause     |
| Compliance              | Partition/identity + RBAC      | Auditing + classification      |

---

### 🧪 Mini-Lab
- Onboard 3 tenants (A, B, C)
- Ingest usage logs into Cosmos (PartitionKey = `tenantId`)
- Build RLS-secured billing dashboard in SQL for each tenant
- Monitor telemetry to detect usage anomalies

---

### ✅ Practice Tasks
- Create Cosmos shared container with `tenantId` partitioning
- Build SQL materialized view per tenant from Cosmos feed
- Apply per-tenant RBAC + read-only view policy
- Document billing model by RU and query cost

---

### 📦 Deliverables
- Cosmos Tenant Model + Ingestion Scripts
- SQL Views + RLS Demo Scripts
- Per-Tenant RU Usage Report
- SaaS Architecture Design Template



---

## 🗓 Day 5: Multi-Tenant SaaS Architecture (Hybrid Backend)

### 🎯 Objectives
- Design a scalable multi-tenant SaaS backend using Cosmos DB and SQL
- Implement per-tenant isolation, shared schema, and cost-optimized resource management
- Leverage Cosmos for operational data and SQL for analytics/billing

---

### 🔍 Key Concepts
- **Tenant Isolation Models**:
  - **Database-per-tenant**: Strongest isolation, high cost
  - **Shared database, schema-per-tenant**: Medium isolation
  - **Shared schema, tenant ID column**: Best for scale
- **Cosmos Multi-Tenant Design**:
  - PartitionKey = `tenantId`
  - Use RU throttling to protect noisy neighbors
  - RBAC and AAD tokens to isolate access
- **SQL for Cross-Tenant Reporting**:
  - ETL or Change Feed → SQL
  - Use views to segment per-tenant reports
  - Row-level security (RLS) or secure views
- **Observability**:
  - Per-tenant logging, telemetry, and query metrics
  - Azure Monitor: PartitionKey-filtered dashboards

---

### 🔧 Hands-On
- Model users, usage logs, and tenant metadata
- Build Cosmos container with partition key on `tenantId`
- Add RLS in SQL to isolate tenant views
- Simulate load from multiple tenants and monitor RU usage

---

### 🧠 Deep Dive: Tenant Strategy Matrix
| Design Choice           | Cosmos DB                      | Azure SQL                      |
|-------------------------|--------------------------------|--------------------------------|
| Isolation               | Partition per tenant           | Schema or RLS per tenant       |
| Analytics               | Change Feed → downstream       | Views + aggregation            |
| Cost Optimization       | Shared container + RU scaling  | Elastic pools + Auto-pause     |
| Compliance              | Partition/identity + RBAC      | Auditing + classification      |

---

### 🧪 Mini-Lab
- Onboard 3 tenants (A, B, C)
- Ingest usage logs into Cosmos (PartitionKey = `tenantId`)
- Build RLS-secured billing dashboard in SQL for each tenant
- Monitor telemetry to detect usage anomalies

---

### ✅ Practice Tasks
- Create Cosmos shared container with `tenantId` partitioning
- Build SQL materialized view per tenant from Cosmos feed
- Apply per-tenant RBAC + read-only view policy
- Document billing model by RU and query cost

---

### 📦 Deliverables
- Cosmos Tenant Model + Ingestion Scripts
- SQL Views + RLS Demo Scripts
- Per-Tenant RU Usage Report
- SaaS Architecture Design Template



---

## 🗓 Day 7: Final Project – Polyglot Architecture Blueprint

### 🎯 Objectives
- Design and document a complete, enterprise-grade architecture using Cosmos DB and SQL
- Apply polyglot persistence, multi-region, security, CI/CD, observability, and DR principles
- Present a reference implementation with all week-by-week deliverables integrated

---

### 📦 Use Case: Global Order & Fulfillment Platform (B2B/B2C)
- **Operational Workloads (NoSQL)**:
  - Orders → Cosmos SQL API
  - Session + activity logs → Table API
  - Inventory stream ingestion → Cassandra API
  - Partner integrations → Mongo API
- **Transactional/Analytical Workloads (SQL)**:
  - Billing, taxes, user mgmt, fraud audit
  - Multi-tenant reporting (RLS)
  - Long-term archival (Synapse)

---

### 🔍 Key Architecture Layers
- **Data Layer**:
  - Cosmos containers by domain
  - SQL DB with elastic pools per region
- **API Layer**:
  - Azure API Management or API Gateway
  - Retry-safe endpoints + failover rules
- **Eventing + Integration**:
  - Change Feed + Event Hub → downstream sync
  - ADF + Synapse Pipelines for ETL
- **Security**:
  - RBAC, Managed Identity, Key Vault CMKs
- **Observability**:
  - Azure Monitor Workbooks + Alerts (RU, latency, DTU)
  - KQL dashboards and Logic App response triggers

---

### 🧪 Final Lab Scope
- Build ARM/Bicep + Terraform deployment for Cosmos + SQL
- Simulate order insert, partner event, and report generation
- Trigger DR drill and measure recovery + alert trace
- Deploy CI/CD pipeline with auto-monitoring on merge

---

### ✅ Final Project Deliverables
- **📄 Architecture Diagram** – End-to-end system view
- **📋 Design Decisions** – API selection, partitioning, indexing, DR
- **🔐 Security Controls** – RBAC map, CMK config, role matrix
- **📈 Dashboards** – Cosmos + SQL unified monitoring
- **🛠 CI/CD Pipelines** – GitHub Actions or DevOps YAML
- **📚 Documentation** – Migration strategy, failover runbook, SLA policy

---

### 📣 Bonus (Optional)
- Present a walkthrough deck (slides) for your reference architecture
- Package and export IaC templates + dashboards for reuse in real-world projects



