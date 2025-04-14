# 📅 Week 2 – Cosmos DB + SQL Architect Mastery (Hybrid Learning Plan)

🧠 **Learning Goal**: Focus on performance optimization, query tuning, and cost control patterns. We'll handle both Cosmos DB and SQL's unique internals and tools, and tackle shared topics (e.g., indexing, observability) in dual-track sessions.

🕒 **Daily Time Budget**: 2–3 hours

---

## 🗓 Day 1: Cosmos DB – Querying, Indexing, and SDK Mastery

### 🧠 Deep Dive: Cosmos SQL Querying and Indexing, RU Profiling, and SDK Behavior

#### 🎯 Objectives
- Learn Cosmos SQL API querying model and indexing strategy.
- Analyze RU consumption and reduce cost with projections and custom policies.
- Use SDKs to handle cross-partition queries and diagnostics.

#### 🔍 Key Concepts
- **SQL API Queries**: SELECT, WHERE, ORDER BY, TOP, JOIN (intra-document only).
- **Indexing**: Automatic vs custom, included/excluded paths, composite indexes.
- **Cost Model**: RU/s based on data size, filters, indexing impact.
- **SDK Features**: Pagination (continuation tokens), FeedIterators, Diagnostics.


##### 🔍 Cosmos SQL Query Anatomy
```sql
SELECT c.customerId, c.total FROM c WHERE c.region = 'US' AND c.status = 'active'
```
- Always prefer projected fields (avoid `SELECT *`).
- Filter on indexed + partition key fields for lowest RU.

##### 🔁 Indexing Policy Customization
```json
{
  "indexingMode": "consistent",
  "includedPaths": [
    { "path": "/customerId/?" },
    { "path": "/region/?" }
  ],
  "excludedPaths": [
    { "path": "/*" }
  ]
}
```
- Default policy indexes all fields.
- Exclude large fields (e.g., logs, blobs) from indexing.

##### 📊 Query Cost Breakdown
| Query Type              | Example                          | RU Estimate  |
|-------------------------|----------------------------------|--------------|
| **Point Read (id + pk)** | `ReadItemAsync("id", pk)`        | ~1 RU        |
| **Filtered Query**       | `SELECT WHERE region = 'US'`    | ~3–10 RU     |
| **Cross-Partition Query**| Without pk filter               | 10–100 RU    |
| **Ordered + Paged Query**| With OFFSET LIMIT + ORDER BY    | Higher RU    |

#### 🧪 Mini-Lab: SDK-Driven Queries + RU Analysis
- Use .NET / Node.js SDK to query documents.
- Capture diagnostics: RU cost, latency, continuation token size.
- Compare SDK vs Query Explorer.

```csharp
FeedIterator<Customer> feed = container.GetItemQueryIterator<Customer>(
    "SELECT c.customerId FROM c WHERE c.status = 'active'",
    requestOptions: new QueryRequestOptions {
        MaxItemCount = 100,
        PartitionKey = new PartitionKey("US")
    });
```

#### ✅ Practice Tasks
- Run 5 common queries and compare RU cost.
- Create and apply a custom indexing policy.
- Page through results using SDK with continuation tokens.
- Enable diagnostics and log RU vs latency trend.

#### 🔧 Hands-On
- Run filtered, ordered, and paged queries in Query Explorer
- Configure indexing policy: exclude unused fields, add composite index
- Analyze RU/s and latency with Query Metrics and SDK logs


#### 📦 Deliverables
- Cosmos Query Benchmark Report (top 5 query types)
- Custom Indexing Policy JSON
- SDK Sample Query (incl. continuation handling)
- RU vs Latency comparison chart (tool: Azure Monitor)


---

## 🗓 Day 2: SQL – Query Optimization & Index Internals

### 🧠 Deep Dive: SQL Query Optimization & Index Internals

#### 🎯 Objectives
- Learn how SQL Server optimizes queries using indexes and execution plans.
- Analyze plan regressions, parameter sniffing, and seek/scan behavior.
- Tune indexing strategies to support optimal performance.

#### 🔍 Key Concepts
- **Execution Plan Stages**: Parsing, binding, optimization, execution.
- **Index Types**: Clustered, Non-Clustered, Filtered, Covering.
- **Access Methods**: Index Seek vs Scan, RID Lookup, Bookmark Lookup.
- **Tuning Tools**: Query Store, Actual vs Estimated Plan, sys.dm_exec_* DMVs.

#### 🔧 Hands-On
- Analyze a slow-running query with the actual execution plan.
- Add a filtered index on a status column and re-evaluate performance.
- Use Query Store to compare performance across plans and time.

#### 🧠 Indexing Mechanics & Query Execution Flow

##### 🔍 Clustered vs Non-Clustered Indexes
| **Type**          | **Description**                                   |
|--------------------|---------------------------------------------------|
| **Clustered**      | Defines physical order of table rows.             |
| **Non-Clustered**  | Logical structure with a pointer to the base row. |
| **Covering Index** | Includes all SELECT columns to avoid lookups.     |
| **Filtered Index** | Indexes a subset of data (e.g., active rows only).|

##### 🧠 Execution Plan Operators
- **Index Seek**: Fast and efficient → Ideal for selective queries.
- **Index Scan**: Scans the full index → Avoid for large tables.
- **Key Lookup**: Occurs when columns are not in the index → Use `INCLUDE`.
- **Joins**: Hash, Loop, Merge → Choose based on row count and sort order.

#### 🛠 Plan Analysis Example
```sql
SELECT * FROM Orders WHERE Status = 'Shipped';
```
- **Estimated Plan**: Shows missing index suggestion.
- Add:
  ```sql
  CREATE NONCLUSTERED INDEX IX_Orders_Status 
  ON Orders(Status)
  INCLUDE (OrderID, OrderDate);
  ```
- Re-run the query and compare the Actual Plan.

#### 🧪 Mini-Lab: Plan Regression & Fix
- Enable Query Store.
- Run a query with parameter variation (e.g., low vs high selectivity).
- Detect regression due to parameter sniffing.
- Apply `FORCE PLAN` or `OPTIMIZE FOR` hint and compare results.

#### ✅ Practice Tasks
- Create a filtered index for a one-hot flag (e.g., `IsActive = 1`).
- Refactor a query to use a covering index (`INCLUDE` clause).
- Run the execution plan and interpret operator costs.
- Log Query Store insights and regressions.

#### 📦 Deliverables
- Indexed Query Plan Before/After Screenshots.
- Filtered + Covering Index Script Samples.
- Query Store Plan Comparison Report.
- DMV Snapshot (top 5 queries by CPU/IO/Duration).

---

## 🗓 Day 3: Cosmos vs SQL – Indexing Strategy Deep Dive (Dual Track)

### 🧠 Deep Dive: Cosmos vs SQL – Indexing Strategy Comparison

#### 🎯 Objectives
- Compare and contrast indexing strategies in Cosmos DB and SQL Server.
- Understand how index design impacts RU/s and query latency in Cosmos, and plan efficiency in SQL.
- Learn trade-offs of indexing too little vs over-indexing in both systems.

#### 🔍 Key Concepts
- **Indexing Overhead**: Writes, storage, RU/cost (Cosmos) vs fragmentation, stats (SQL).
- **Query Pattern Matching**: Partitioned index usage vs filtered/indexed scans.
- **Coverage vs Specialization**: Composite/filtered/covering indexes.
- **Manual Tuning vs Auto-Management**: Index Advisor (Cosmos), Auto-Tuning (SQL Azure).

#### 🔧 Hands-On
- Design two equivalent indexes: Cosmos composite vs SQL filtered + include.
- Benchmark the same `SELECT + ORDER BY + WHERE` queries.
- Tune one to reduce cost/latency by 50% while preserving correctness.

#### 🧠 Deep Dive: Strategic Index Design Comparison

##### 🔍 Cosmos DB Indexing
- Default: indexes all properties (high RU on writes).
- Use `includedPaths` + `excludedPaths` to control behavior.
- Composite indexes: useful for `ORDER BY` and filtering combinations.

```json
{
  "indexingMode": "consistent",
  "includedPaths": [
    { "path": "/status/?" },
    { "path": "/region/?" }
  ],
  "compositeIndexes": [
    [
      { "path": "/status", "order": "ascending" },
      { "path": "/region", "order": "ascending" }
    ]
  ]
}
```

##### 🔍 SQL Server Indexing
- Use Filtered Indexes to exclude rarely-used rows.
- Use Covering Indexes to eliminate lookups.
- `INCLUDE` clause ensures `SELECT` fields don’t trigger key lookups.

```sql
CREATE NONCLUSTERED INDEX IX_StatusRegion
ON Orders(Status, Region)
INCLUDE (OrderID, TotalAmount);
```

#### 🧪 Mini-Lab: Index Strategy Trade-off
1. **Cosmos DB**:
   - Run a query with and without a composite index and observe RU delta.
   - Drop one over-indexed field and observe write performance improvement.

2. **SQL Server**:
   - Run a query with and without the `INCLUDE` clause and observe IO reduction.
   - Measure execution plan changes after adding a filtered index.

#### ✅ Practice Tasks
- Evaluate top 3 queries in each system and propose an optimal index set.
- Implement a composite index in Cosmos and validate `ORDER BY` clause performance.
- Create a filtered + covering index in SQL and measure plan delta.
- Track RU/s and IO/CPU before and after tuning.

#### 📦 Deliverables
- Index Cost Comparison Sheet (Cosmos vs SQL).
- Indexed Query Sample Set (JSON + T-SQL).
- RU vs CPU/IO Plan Delta Report.
- Recommendation Matrix: When to use which type of index.

### 🔍 Shared Concepts
- Index tuning impact on query cost
- Storage vs compute cost trade-offs
- Over-indexing, index fragmentation
- Indexing exclusions and filtered paths

### 🔧 Practice
- Compare Cosmos custom index policy vs SQL filtered index
- Run equivalent read patterns on both
- Log RU/s vs IO/cost for access patterns

---

## 🗓 Day 4: Cosmos DB – Performance Tuning & Throughput Engineering

### 🔍 Key Concepts
- Manual vs autoscale throughput provisioning
- Bulk ingestion, rate-limiting (429 errors)
- Retry policies, latency tuning
- Diagnostic logs + telemetry

### 🔧 Hands-On
- Build ingestion pipeline (bulk insert SDK / ADF)
- Simulate load, analyze throttling & latency
- Auto-scaler logic with Metrics + Azure Function

### 🧠 Deep Dive: Throughput Models + Retry Engineering

#### 🔁 Provisioning Models
| **Model**       | **Description**                  | **Best Use Case**          |
|------------------|----------------------------------|----------------------------|
| **Manual RU/s**  | Fixed throughput (e.g., 1000 RU/s) | Predictable traffic        |
| **Autoscale**    | Scales 10–100x dynamically       | Spiky, unpredictable traffic |

#### 💡 Bulk Insert Patterns
```csharp
BulkExecutionOptions options = new() { MaxConcurrency = 20 };

await container.CreateItemAsync(item, new PartitionKey(item.pk));
```
- Bulk mode reduces per-item overhead.
- Handle 429s using exponential backoff policies.

#### 🔍 Retry Policy Strategy
- Retry on transient failures (429, timeout).
- Use `CosmosClientOptions.RetryOptions`.
- Log `RetryAfter` duration for throttled requests.

---

### 🧪 Mini-Lab: Load Simulation + Throttle Analysis
1. Insert 100K documents across 10 partitions.
2. Record throughput under manual vs autoscale RU/s.
3. Trigger a 429 event and test recovery time + impact on latency.

---

### ✅ Practice Tasks
- Set up autoscale container and load test with SDK.
- Create custom telemetry for RU, latency, retries.
- Visualize throttling events in Azure Monitor.
- Evaluate ingestion cost vs latency trade-offs.

---

### 📦 Deliverables
- SDK Bulk Load Script (configurable RU/s + concurrency).
- RU Budgeting Table (Read, Write, Query).
- Retry + Throttle Resilience Report.
- Azure Monitor Dashboard Template (Cosmos throughput).

---

## 🗓 Day 5: SQL – Execution Plans, Statistics, and Adaptive Tuning

### 🔍 Key Concepts
- Execution plan analysis (estimated vs actual)
- Parameter sniffing + plan cache behavior
- Statistics update, auto-tuning (Azure SQL)
- Adaptive joins, interleaved execution

### 🔧 Hands-On
- Plan regressions with Query Store
- FORCE PLAN vs Recompile
- Manual stats update and performance testing

### 🎯 Objectives

- Analyze SQL query performance using execution plans.
- Learn how statistics affect plan generation.
- Use adaptive query processing and auto-tuning in Azure SQL.

### 🔍 Key Concepts

- **Execution Plan Types**: Estimated vs Actual.
- **Plan Operators**: Seek, Scan, Nested Loops, Hash Match, Merge Join.
- **Statistics**: Histogram-based cardinality estimation.
- **Adaptive Features**: Interleaved execution, memory grant feedback.
- **Plan Caching**: Parameter sniffing, plan reuse, recompilation.

### 🔧 Hands-On

- Enable Query Store and capture historical plans.
- Compare plans with and without updated statistics.
- Test parameter sniffing scenarios and mitigation strategies.

### 🧠 Deep Dive: Plan Evolution and Adaptive Optimization

#### 🛠 Query Plan Example
```sql
SELECT CustomerID, COUNT(*) 
FROM Orders
WHERE OrderDate > '2024-01-01'
GROUP BY CustomerID;
```
- Use `SET STATISTICS IO ON` to analyze logical reads.
- Compare performance of a clustered index scan vs a filtered index seek.

#### 🧠 Statistics Insight
- Update statistics to ensure accurate cardinality estimation:
  ```sql
  UPDATE STATISTICS Orders;
  DBCC SHOW_STATISTICS ('Orders', IX_OrderDate);
  ```
- Validate histogram range steps and density for better query optimization.

#### 🔁 Adaptive Query Features
- **Adaptive Joins**: Switch join type at runtime based on row count.
- **Interleaved Execution**: Defer plan choice until subquery/cardinality is known.
- **Automatic Plan Correction (Azure SQL)**: Force the last known good plan to prevent regressions.

#### 🧪 Mini-Lab: Query Regression + Fix
1. Run queries with skewed parameters to observe plan regressions.
2. Mitigate issues using:
   - `OPTIMIZE FOR` hint.
   - `RECOMPILE` option.
   - `FORCE PLAN` directive.
3. Log plan duration, IO, and CPU usage across parameter variants.

#### ✅ Practice Tasks
- Update outdated statistics and measure the performance impact.
- Enable and configure Query Store for capturing historical plans.
- Run a regression lab with parameter sniffing examples.
- Use `sys.dm_exec_query_stats` and query plan XMLs to analyze trends.

#### 📦 Deliverables
- Query Plan Snapshot Series (Estimated + Actual).
- Updated Statistics Script + Analysis Notes.
- Adaptive Plan Behavior Report.
- Query Store Configuration & Regression Tracking Sheet.


---

## 🗓 Day 6: Observability & Monitoring – Cosmos DB + SQL (Dual Track)

### 🔍 Key Concepts
- Metrics: latency, RU/s, CPU, blocking chains
- Logs: query telemetry, deadlocks, throttling
- Dashboards: Azure Monitor, Grafana, Workbooks
- Alerts, KQL rules, SLO thresholds

### 🔧 Practice
- Create Cosmos dashboard: RU vs latency + alert
- SQL dashboard: top queries, deadlocks, IO wait
- Stream logs to Log Analytics + Event Hub


### 🎯 Objectives

- Set up observability pipelines across Cosmos DB and SQL.
- Build dashboards for RU/s, DTU, latency, and query performance.
- Configure alerts for anomalies, throttling, and failed queries.

### 🔍 Key Concepts

#### Telemetry Sources:
- **Cosmos**: RU consumption, latency, 429s, diagnostics logs.
- **SQL**: Query Store, DMV stats, blocking chains, DTU/vCore usage.

#### Dashboards:
- **Azure Monitor Workbooks**.
- **Log Analytics + KQL**.
- **Grafana integration** (optional).

#### Alerting Mechanisms:
- KQL alerts, budget caps, autoscale triggers, high-CPU events.

### 🔧 Hands-On

- Create Azure Monitor workbook: Cosmos RU/s, throttling, latency.
- Create Log Analytics dashboard: SQL CPU, waits, query duration.
- Write KQL queries for custom alerts (spikes, failures, retry count).

### 🧠 Deep Dive: Multi-Layer Observability Strategy

#### Cosmos Metrics:
| **Metric**               | **Use Case**                     |
|---------------------------|-----------------------------------|
| RU/s per operation        | Throughput tuning                |
| Latency (P50, P95, P99)   | Query performance                |
| Throttled Requests (429)  | Retry strategy tuning            |
| Storage Size              | Partition pressure planning      |

#### SQL Metrics:
| **Metric**       | **Use Case**                     |
|-------------------|-----------------------------------|
| CPU/DTU usage     | Resource saturation              |
| Wait Stats        | Bottleneck root cause            |
| Query Duration    | Execution plan tuning            |
| Blocking/Deadlocks| Concurrency control              |

### 🧪 Mini-Lab: Cross-System Observability Drill

1. Set alerts for RU/s > 80% in Cosmos.
2. Track DTU and CPU usage for SQL under concurrent query load.
3. Use Log Analytics to correlate latency spikes to specific queries.

### ✅ Practice Tasks

- Build Cosmos + SQL dashboards using Azure Monitor.
- Configure autoscale RU/s trigger and alert group.
- Set up failed login and throttling detection queries.
- Tune one high-latency SQL query using Query Store drilldown.

### 📦 Deliverables

- Azure Monitor Workbooks: Cosmos + SQL views.
- KQL Alerting Scripts: latency, errors, CPU thresholds.
- Cosmos 429+Latency Log Summary.
- SQL Plan Regression + Blocking Report.


---

## 🗓 Day 7: Cost Optimization & Self-Healing Workloads (Hybrid Day)

### 🔍 Concepts
- Cosmos: TTL, RU budgeting, storage optimization
- SQL: DTU/vCore tuning, serverless, pause/resume
- Auto-scaling, retry + fallback patterns
- Billing alerts + throttling automation

### 🔧 Practice
- TTL + archiving design (Cosmos)
- Create SQL elasticity policy (Auto-Pause)
- Build auto-throttling alert + recovery pipeline


### 🧠 Deep Dive: Cost Optimization & Self-Healing Workloads

#### 🎯 Objectives
- Minimize database costs while maintaining performance.
- Use TTL, autoscale, and partitioning for Cosmos DB.
- Use serverless, auto-pause, and elastic pools in SQL.
- Implement self-healing workflows triggered by alerts.

#### 🔍 Key Concepts

##### Cost Levers – Cosmos DB:
- **RU Provisioning Model**: Manual vs autoscale.
- **TTL (Time to Live)**: Auto-expiring documents to reduce storage costs.
- **Partitioning**: Spread load and isolate workloads for better RU efficiency.
- **Index Exclusion**: Reduce RU/write costs by excluding unused fields.

##### Cost Levers – SQL:
- **DTU/vCore Pricing Tiers**: Choose based on workload patterns.
- **Serverless Compute**: Auto-pause/resume for infrequent workloads.
- **Elastic Pools**: Share resources across multiple databases.
- **Backup/Retention**: Optimize backup frequency and retention policies.

##### Self-Healing Patterns:
- **Trigger Logic Apps**: Respond to threshold breaches (e.g., high RU/s or CPU).
- **Autoscaler Function**: Adjust RU/s dynamically based on metrics.
- **Alert → Queue → Action Pipeline**: Automate remediation workflows.

#### 🔧 Hands-On
- **Cosmos DB**:
  - Enable TTL for a collection and simulate archival of expired documents.
  - Set up autoscale rules and analyze RU consumption patterns.
- **SQL**:
  - Configure auto-pause policy for a serverless database and monitor resume behavior.
  - Use elastic pools to optimize multi-tenant workloads.
- **Self-Healing**:
  - Build a Logic App or Azure Function to auto-scale Cosmos DB or notify the team on alerts.

#### 🧠 Deep Dive: Balancing Cost, Scale & Resilience
- **Cosmos Workload Patterns**: Analyze pricing impact for IoT, CRM, and retail scenarios.
- **SQL Workload Patterns**: Compare serverless vs provisioned for burst vs baseline usage.
- **Alert-Based Policies**: Implement fallback patterns triggered by monitoring thresholds.

#### 🧪 Mini-Lab: Budget Guardrails + Auto-Remediation
1. Simulate a spike in Cosmos RU/s and trigger an autoscaler alert.
2. Create a Log Analytics alert for SQL CPU > 90%.
3. Route events to Slack/email and trigger autoscale (Cosmos) or pause (SQL).

#### ✅ Practice Tasks
- Create a TTL policy for expiring test records in Cosmos DB.
- Define auto-pause thresholds for a SQL database and test wake-up time.
- Track cost over 3 days using Azure Monitor and Cost Management.
- Build an alert-response chain (Log Analytics → Logic App).

#### 📦 Deliverables
- **Cosmos**: TTL + Archival Config JSON.
- **SQL**: Auto-pause ARM Template or Portal Config.
- **Cost Tracker**: Cost vs performance tracking sheet.
- **Self-Healing**: Logic App flow or script for auto-scaling/notification.

---

## ✅ Deliverables for Week 2
- Query tuning playbook (Cosmos + SQL)
- Index strategy matrix (dual platform)
- Workbooks for RU/s, DTU, latency
- Architecture diagrams: ingest → store → monitor
- Cost optimization cheat sheet (per platform)

📍Next: Week 3 – Security, Multi-Region Design & Disaster Recovery  