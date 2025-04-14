# 📅 Week 1 – Cosmos DB vs SQL: Foundational Architecture & Modeling (Hybrid Track)

🧠 **Learning Goal**: Understand the philosophical and architectural differences between Cosmos DB and traditional SQL systems. Learn how each database is modeled, partitioned, and scaled in enterprise-grade systems.

---

## 🗓 Day 1: Cosmos DB – What, Why, and How

### 🎯 Objectives
- Learn what Cosmos DB is and how it differs from traditional databases.
- Understand multi-region replication, partitioning, and consistency models.
- Hands-on creation of a Cosmos DB Core (SQL) API instance.

### 🔍 Key Concepts
- **Globally distributed, multi-model** database (SQL, MongoDB, Cassandra, Table, Gremlin APIs).
- **Partitioning**: Range/Hash-based.
- **Consistency**: 5 levels from Strong to Eventual.
- **Multi-region write and failover setup**.

### 🔧 Hands-On:
- Create Cosmos DB instance with Core SQL API
- Test different consistency levels
- Visualize replication via Azure CLI / Portal

```bash
az cosmosdb create \
    --name myCosmosDB \
    --resource-group myRG \
    --locations regionName=eastus failoverPriority=0 \
    --locations regionName=westeurope failoverPriority=1 \
    --default-consistency-level Session
```


### 🧠 Deep Dive: Cosmos DB as a Globally Distributed Data Platform

#### 🔍 What is Cosmos DB?
Azure Cosmos DB is Microsoft’s globally distributed, multi-model NoSQL database designed for cloud-native and planetary-scale applications. It offers:
- Low-latency, high-availability access to data globally.
- Multiple API models: Core (SQL), MongoDB, Cassandra, Table, and Gremlin.
- Multi-region writes with SLA-backed latency, throughput, availability, and consistency guarantees.

#### 🌍 Global Distribution & Multi-Region Writes
Cosmos DB supports active-active geo-replication:
- Write to any region.
- Data is synchronously or asynchronously replicated.
- Automatic failover, latency-aware routing, and SLA-bound performance.

#### 🔁 Consistency Levels
Cosmos DB provides 5 consistency levels:
| Level              | Latency | Throughput | Data Freshness | Guarantee                  |
|--------------------|---------|------------|----------------|---------------------------|
| Strong             | High    | Low        | Real-time      | Linearizability           |
| Bounded Staleness  | Medium  | Medium     | Defined lag    | Order + bounded freshness |
| Session            | Low     | High       | Per-session    | Monotonic reads/writes    |
| Consistent Prefix  | Low     | High       | Eventually correct | Preserves order       |
| Eventual           | Lowest  | Highest    | Eventual       | No ordering guarantees    |

#### 🧱 Partitioning: Cosmos' Backbone for Scale
- Data is distributed across logical partitions grouped under physical partitions.
- A partition key is mandatory and determines scalability.
- Good partition keys have high cardinality and uniform access patterns.

#### ⚙️ Provisioning and Request Units (RU/s)
Cosmos abstracts compute, IO, and memory into Request Units (RU/s). Operations like reads, writes, and queries consume RU/s based on payload and indexing.

#### 🛠️ Hands-On: Deploy Your First Cosmos DB
1. **Create a Cosmos DB SQL API Instance**
    ```bash
    az cosmosdb create \
      --name myCosmosDB \
      --resource-group myRG \
      --locations regionName=eastus failoverPriority=0 \
      --locations regionName=westeurope failoverPriority=1 \
      --default-consistency-level Session
    ```
2. **Create a Container with Partition Key**
    ```bash
    az cosmosdb sql container create \
      --account-name myCosmosDB \
      --database-name RetailDB \
      --name Orders \
      --partition-key-path "/customerId" \
      --throughput 400
    ```
3. **Sample Document Structure**
    ```json
    {
      "id": "order123",
      "customerId": "cust001",
      "region": "US",
      "items": [
         { "sku": "P001", "qty": 2 },
         { "sku": "P009", "qty": 1 }
      ]
    }
    ```

#### 🔬 Mini-Lab: Consistency vs Latency Test
- Insert 1000 documents.
- Query them under different consistency levels.
- Record RU/s, latency, and cross-region behavior.

#### ✅ Practice Tasks
- Deploy a multi-region Cosmos DB instance.
- Create a database and container with a correct partition key.
- Ingest 10K synthetic records.
- Measure RU/s per operation using Azure Monitor.

#### 📦 Deliverables
- CLI + ARM template for Cosmos setup.
- Lab report (latency vs consistency).
- Partition key decision matrix.
- Query samples using SDK (Node.js, .NET, Python).





---

## 🗓 Day 2: SQL – Relational Modeling & Workload Design

### 🎯 Objectives
- Grasp relational theory: set algebra, normalization, keys.
- Design OLTP (3NF) vs OLAP (Star/Snowflake) schemas.
- Use tooling like Azure Data Studio or SSDT to visualize schemas.

### 🔍 Key Concepts
- **Primary Key / Foreign Key**
- **Normalization (1NF to 3NF)**
- **Denormalization** for analytical speed
- **OLTP vs OLAP Schema Design**

### 🔧 Hands-On:
- Model retail DB in 3NF (Customer, Orders, Products)
- Transform to OLAP Star Schema
- Draw ER diagrams with Azure Data Studio / dbdiagram.io



### 🧠 Deep Dive: Core Concepts of Relational Databases

#### 🔍 What is a Relational Database?
A relational database organizes data into tables (relations). Each row (tuple) represents a unique record, and columns (attributes) define the data fields. It is based on set theory and relational algebra and uses SQL (Structured Query Language) for definition, manipulation, and querying. Integrity is enforced through constraints and keys.

#### 🔑 Keys & Constraints
| Type         | Purpose                                   |
|--------------|-------------------------------------------|
| Primary Key  | Uniquely identifies a record             |
| Foreign Key  | Ensures referential integrity between tables |
| Unique       | Prevents duplicate values in a column    |
| Check        | Validates data with custom logic         |
| Not Null     | Requires a column to always have a value |

#### 🧠 Normalization
Normalization eliminates redundancy and improves integrity. Each "normal form" builds on the previous:
- **1NF**: No repeating groups or arrays.
- **2NF**: No partial dependencies on composite keys.
- **3NF**: No transitive dependencies.

⚠️ Over-normalization can lead to excessive joins. Consider denormalization for analytics and read-optimized systems.

#### 🧱 Schema Design: OLTP vs OLAP
| Feature       | OLTP                          | OLAP                          |
|---------------|-------------------------------|-------------------------------|
| Workload      | Inserts, Updates              | Aggregations, Reads           |
| Schema        | Normalized (3NF)              | Denormalized (Star)           |
| Query Type    | Transactional                 | Analytical                    |
| Latency Target| < 100ms                       | Seconds to minutes            |

- **OLTP (Online Transaction Processing)**: Optimized for frequent reads/writes, highly normalized schema (3NF), fast lookups, minimal redundancy.
- **OLAP (Online Analytical Processing)**: Designed for complex queries and aggregations, uses Star or Snowflake schemas (denormalized), better suited for BI/reporting systems.

#### 🛠️ Hands-On: Model a Retail Database
**OLTP – Normalized Schema**
```sql
CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,
    FirstName NVARCHAR(50),
    LastName NVARCHAR(50),
    Email NVARCHAR(100) UNIQUE
);

CREATE TABLE Products (
    ProductID INT PRIMARY KEY,
    Name NVARCHAR(100),
    Price DECIMAL(10,2)
);

CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    CustomerID INT FOREIGN KEY REFERENCES Customers(CustomerID),
    OrderDate DATETIME
);

CREATE TABLE OrderItems (
    OrderItemID INT PRIMARY KEY,
    OrderID INT FOREIGN KEY REFERENCES Orders(OrderID),
    ProductID INT FOREIGN KEY REFERENCES Products(ProductID),
    Quantity INT CHECK (Quantity > 0)
);
```

### 🧠 Advanced Schema Design Patterns

#### 🌟 Star Schema Example
A star schema is optimized for analytical queries and reporting. It consists of a central fact table connected to multiple dimension tables.

**Fact Table**: Contains measurable, quantitative data (e.g., sales, revenue).  
**Dimension Tables**: Contain descriptive attributes (e.g., customer details, product categories).

**Example**:
```sql
CREATE TABLE FactSales (
  SaleID INT PRIMARY KEY,
  ProductID INT,
  CustomerID INT,
  SaleDate DATE,
  Amount DECIMAL(10, 2)
);

CREATE TABLE DimProduct (
  ProductID INT PRIMARY KEY,
  ProductName NVARCHAR(100),
  Category NVARCHAR(50)
);

CREATE TABLE DimCustomer (
  CustomerID INT PRIMARY KEY,
  FirstName NVARCHAR(50),
  LastName NVARCHAR(50),
  Region NVARCHAR(50)
);

CREATE TABLE DimDate (
  DateID INT PRIMARY KEY,
  Date DATE,
  Month NVARCHAR(20),
  Year INT
);
```

#### ❄️ Snowflake Schema Example
A snowflake schema normalizes dimension tables into sub-dimensions, reducing redundancy but increasing query complexity.

**Example**:
```sql
CREATE TABLE DimProduct (
  ProductID INT PRIMARY KEY,
  CategoryID INT,
  ProductName NVARCHAR(100)
);

CREATE TABLE DimCategory (
  CategoryID INT PRIMARY KEY,
  CategoryName NVARCHAR(50)
);
```

#### 🛠️ Hands-On Practice
1. Design a star schema for an e-commerce platform (e.g., FactOrders, DimCustomer, DimProduct, DimDate).
2. Normalize one dimension table into a snowflake schema.
3. Write a query to calculate total sales by product category and region.

#### 📦 Deliverables
- SQL scripts for star and snowflake schemas.
- Query examples for aggregations and joins.
- ER diagrams for both schema types.
- Performance comparison report (execution plans, query times).
- Cheat sheet: Star vs Snowflake trade-offs.

**OLAP – Star Schema Example**
- **Fact Table**: Sales
- **Dimension Tables**: Customers, Products, Time
- Use surrogate keys, flatten structure, and consider columnstore indexes.

#### 🧠 Real-World Scenarios
- **Multi-Tenant SaaS**: Normalize per-tenant schema with TenantID. Combine with Row-Level Security (RLS).
- **Inventory System**: Snapshot product metadata weekly (SCD Type 2). Partition by product category + warehouse region.
- **Customer 360 View**: OLTP for transactions, OLAP for aggregated KPIs. Periodic ETL job to sync between systems.

#### ✅ Practice Tasks
- Design a normalized schema for a blog or e-commerce site.
- Convert it into a Star schema for reporting.
- Draw ERD using Azure Data Studio / dbdiagram.io.
- Write queries that JOIN across at least 3 tables.

#### 📦 Deliverables
- SQL DDL Scripts (3NF + Star Schema).
- ERD diagrams (tool of choice).
- Schema normalization cheat sheet.
- Practice dataset (synthetic or open-source).




---

## 🗓 Day 3: Cosmos DB – Partitioning Mastery

### 🎯 Objectives
- Learn partition key selection strategy
- Avoid hot partitions, ensure scalability

### 🔍 Key Concepts
- **Logical vs Physical Partitioning**
- **PartitionKey cardinality and distribution**
- **Access patterns drive design**
- Embedding vs Referencing strategies

### 🔧 Hands-On:
- Model CRM and E-commerce systems with ideal keys
- Simulate skewed load and visualize partition heatmaps
- Use PartitionKeyAdvisor + Metrics Explorer



### 🧠 Deep Dive: Partitioning in Cosmos DB

#### 🔍 What is Partitioning?

Partitioning in Cosmos DB refers to horizontal scaling of data across logical and physical partitions:

- **Logical Partition**: Set of items sharing the same partition key value.
- **Physical Partition**: Backend unit of compute + storage (~20 GB or 10K RU/s).

Cosmos DB requires a partition key to distribute data. Poor key choices can lead to hot partitions, latency spikes, and throttling.

#### 🔑 Choosing the Right Partition Key

| Characteristic       | Good Practice                              |
|----------------------|--------------------------------------------|
| **High Cardinality** | e.g., `userId`, `deviceId`, `orderId`      |
| **Even Access Pattern** | Uniform read/write distribution         |
| **Query Filter Usage** | Frequently used in `WHERE` clause        |
| **Write Distribution** | Avoid burst writes to the same key       |

🚫 **Bad Keys**: `country`, `region`, `status`, boolean fields (low cardinality)  
✅ **Good Keys**: `tenantId`, `email`, `userId|timestamp` (compound key for time-series)

#### 🧩 Modeling Strategies

- **Embedded Document (Denormalized)**:
    ```json
    {
      "orderId": "123",
      "customerId": "C001",
      "customer": {
        "name": "Alice",
        "email": "alice@demo.com"
      },
      "items": [...]
    }
    ```
    ✅ Read-optimized  
    ❌ Update complexity, duplication  

- **Referenced Model**:
    ```json
    {
      "orderId": "123",
      "customerId": "C001"
    }

    {
      "customerId": "C001",
      "name": "Alice",
      "email": "alice@demo.com"
    }
    ```
    ✅ Update flexibility  
    ❌ Requires client-side joins  

🧠 **FAANG Rule**: Use embedded models for high-speed reads; reference models for admin/data integrity scenarios.

#### ⚙️ Partitioning Patterns by Use Case

| Use Case         | Suggested Partition Key | Strategy                        |
|------------------|--------------------------|---------------------------------|
| **IoT Logs**     | `deviceId|hour`         | Evenly distribute + TTL         |
| **Multi-Tenant SaaS** | `tenantId`          | Isolation + throttling guard    |
| **Chat App**     | `chatRoomId`            | Embed messages                  |
| **E-Commerce**   | `categoryId` / `productId` | Per product-family queries    |

🔁 **TTL (Time To Live)**: Useful for time-bounded workloads (e.g., logs, telemetry).

#### 🔧 Hands-On Practice

1. **Design a Multi-Tenant Cosmos DB for a CRM System**:
   - Try different partition key choices and simulate load skew.
   - Use Azure Metrics Explorer → Partition heatmaps.

2. **Create a Synthetic Dataset**:
   - Ingest data using the Cosmos SDK (bulk mode).

#### 📦 Deliverables

- Partition key evaluation matrix (Markdown/Excel).
- Sample document models (embedded vs referenced).
- Azure Metrics heatmap screenshot.
- Cosmos SDK workload ingestion script.



---

## 🗓 Day 4: SQL – Schema Optimization & Index-Aware Modeling

### 🎯 Objectives
- Evolve normalized models toward index-friendly forms
- Prep schema for analytics, slowly changing dimensions

### 🔍 Key Concepts
- Normal form violations (2NF, 3NF)
- Surrogate keys, composite keys
- Star vs Snowflake revisited
- Columnstore indexing for OLAP

### 🔧 Hands-On:
- Build product-order schema with surrogate keys
- Design SCD Type 2 Dimension
- Create clustered/non-clustered indexes


### 🧠 Deep Dive: Evolving Relational Schemas

#### 🔍 Surrogate Keys & Composite Keys
- Use surrogate keys (e.g., `INT IDENTITY`, GUIDs) instead of composite business keys.
- Simplifies joins, foreign key references, and indexing.

**Example**:
```sql
CREATE TABLE Customers (
    CustomerID INT IDENTITY PRIMARY KEY,
    FirstName NVARCHAR(50),
    Email NVARCHAR(100) UNIQUE
);
```

#### 🧠 Index Optimization by Use Case
| **Index Type**        | **Best For**                              |
|-----------------------|------------------------------------------|
| **Clustered Index**   | Range queries, primary sort order        |
| **Non-Clustered Index** | Point lookups, joins                   |
| **Covering Index**    | Avoiding lookups (SELECT only indexed)   |
| **Filtered Index**    | Sparse queries (e.g., `Status = 'Active'`) |
| **Columnstore Index** | Analytics, aggregations, large scans     |

⚠️ **Tip**: Avoid over-indexing to reduce write costs and fragmentation.

#### 📊 OLTP + OLAP Friendly Schema Example
- **Transactional Table with Covering Index**:
    ```sql
    CREATE NONCLUSTERED INDEX IX_Orders_CustDate
    ON Orders(CustomerID, OrderDate)
    INCLUDE (TotalAmount);
    ```

- **Columnstore Example for Reporting Table**:
    ```sql
    CREATE CLUSTERED COLUMNSTORE INDEX CCI_FactSales
    ON FactSales;
    ```

#### 🧠 Real-World FAANG Scenarios
- **Customer Order Lookup**:
  - Composite index on `(CustomerID, OrderDate)`.
  - Include `TotalAmount` to avoid lookups.

- **Product Catalog Filtering**:
  - Filtered index on `IsActive = 1`.
  - Covering index for `(CategoryID, Price)`.

- **Data Warehouse Aggregations**:
  - Columnstore index on `FactSales`.
  - Star schema with materialized aggregates.

#### 🔧 Practice Tasks
1. Refactor an OLTP schema using surrogate keys.
2. Add indexes and measure performance gain with `SET STATISTICS IO`.
3. Convert a fact table to use a columnstore index and run aggregations.
4. Build execution plans and tune costly operations.

#### 📦 Deliverables
- Schema refactoring SQL scripts.
- Index creation and tuning report (before/after).
- Execution plan snapshots (Graphical/Estimated/Actual).
- Cheat sheet: Index types + usage.


---

## 🗓 Day 5: RU vs DTU/vCore – Throughput Models Compared (Dual Track)

### 🎯 Objectives
- Understand cost and scaling unit in Cosmos vs SQL
- Explore when to use provisioned vs autoscale vs elastic pools

### 🔍 Key Concepts
- **Cosmos RU/s = request charge abstraction**
- **SQL DTU (legacy) vs vCore (modern)**
- Autoscale vs Reserved capacity models
- Budgeting throughput for unpredictable workloads

### 🔧 Tools:
- Azure Pricing Calculator
- RU Analyzer + Query Explorer (Cosmos)
- SQL Query Store + Monitor


### 🧠 Cosmos DB: Request Units (RU/s) vs SQL: DTU/vCore Model

#### 🔍 Key Concepts

| Feature                  | Cosmos DB (RU/s)                          | SQL (DTU/vCore)                     |
|--------------------------|-------------------------------------------|--------------------------------------|
| **Throughput Unit**      | Request Units (RU/s)                     | DTU (legacy) / vCore (modern)       |
| **Scaling**              | Autoscale (10–100x), Manual Provisioning | Elastic Pools, Serverless, vCore    |
| **Cost Model**           | Pay-per-RU (provisioned/autoscale)       | Fixed tiers (DTU) or pay-as-you-go  |
| **Ideal Use Case**       | NoSQL, bursty workloads, global scale     | Relational, predictable workloads   |

#### 🔧 Hands-On Comparison

1. **Simulate RU Costs in Cosmos DB**:
    - Insert 10K documents (1 KB each).
    - Measure RU consumption for point reads, writes, and cross-partition queries.

2. **Run SQL Workloads**:
    - Deploy in DTU and vCore tiers.
    - Measure latency and cost for OLTP and analytical queries.

3. **Enable Autoscale in Cosmos DB**:
    - Configure autoscale for a collection.
    - Simulate bursty traffic and observe RU scaling.

4. **Monitor Throughput**:
    - Use Azure Monitor for Cosmos DB (RU/s, throttling).
    - Query Store for SQL (CPU, IO, waits).

#### 📦 Deliverables

- RU/DTU Cost Comparison Sheet.
- Autoscale Configuration (Cosmos CLI/JSON).
- SQL Tier Benchmarking Report.
- Monitoring Dashboard (KQL/Log Analytics).


---

## 🗓 Day 6: Practice + Design Drill
- Implement a CRM database in **Cosmos DB (Core SQL)** and **SQL Server**
- Compare data modeling patterns, query patterns, and partitioning
- Deploy each and run load simulation


### 🧠 Design Challenge: Global Order Tracking System

### Shared Scenario

A company wants to build a globally available order tracking system that supports:
- **Real-time writes** from multiple regions.
- **Analytical queries** on sales, delivery time, and inventory.
- **Event-driven integrations** (notifications, audit logs).

---

### 🧩 Cosmos DB Design

#### Key Features
- **API**: Core (SQL).
- **Partition Key**: `customerId` or `region|timestamp`.
- **TTL**: Enable for stale orders after 90 days.
- **Consistency Level**: Session.
- **Change Feed**: Enable for Azure Functions trigger.

#### Simulated Operations
1. **10K concurrent order inserts** via SDK.
2. **Query**: Fetch last 100 orders per customer.
3. **Monitor**: RU/s, latency, throttling via Azure Monitor.

#### Hands-On Tasks
- Deploy Cosmos DB with Core SQL API.
- Configure partition key and TTL.
- Enable Change Feed for event-driven processing.
- Simulate concurrent inserts using Cosmos SDK.
- Query and monitor performance metrics.

---

### 🧩 SQL Server Design

#### Key Features
- **Schema**: Orders, Customers, Inventory (3NF).
- **Indexes**: Composite (`CustomerID`, `OrderDate`) + filtered index on `Status = 'Shipped'`.
- **Stored Procedure**: Summarize delivery time per region.
- **Execution Plan**: Analyze IO + CPU from Query Store.

#### Simulated Operations
1. **Bulk insert 1M order rows** using `bcp` or `SqlBulkCopy`.
2. **Run dashboard queries** with window functions.
3. **Monitor**: CPU/DTU usage, blocking chains.

#### Hands-On Tasks
- Create normalized schema with indexes.
- Write stored procedures for analytical queries.
- Simulate bulk inserts and analyze execution plans.
- Monitor performance using Query Store and SSMS.

---

### 🛠 Tooling
- **Cosmos DB SDK**: Python, .NET.
- **SQL Server Management Studio (SSMS)**.
- **Azure Monitor + Log Analytics**.
- **JMeter / Locust** for load testing.

---

### ✅ Practice Tasks
1. Deploy and configure both databases using script/CLI.
2. Simulate concurrent inserts and measure cost/latency.
3. Build monitoring dashboards (KQL + SSMS).
4. Tune one query per system to reduce cost/response time by 30%.

---

### 📦 Deliverables
- **Architecture diagrams**: Cosmos and SQL models.
- **Load test result summaries**.
- **Monitoring dashboard snapshots**.
- **Tuning report** with before/after metrics.


---




## 🗓 Day 7: Design Review + Scenario Q&A
- Discuss 3 real-world systems:
    - IoT Platform
    - E-Commerce Site
    - Multi-Region CRM
- Compare Cosmos DB vs SQL design for each
- Discuss trade-offs: latency, cost, consistency, query model



### ⚔️ Architecture Showdown: Cosmos DB vs SQL

### Feature Comparison

| Feature              | Cosmos DB                              | SQL Server / Azure SQL                  |
|----------------------|-----------------------------------------|-----------------------------------------|
| **Data Model**       | NoSQL (document, graph, etc.)          | Relational (tables + constraints)       |
| **Scale**            | Horizontally (partitioned RUs)         | Vertical + limited horizontal scale     |
| **Multi-Region**     | Active-active writes, low-latency      | Geo-replication, read-scale             |
| **Consistency Models** | 5 levels (Strong → Eventual)         | Strong consistency                      |
| **Schema Flexibility** | Schema-free (JSON)                   | Strict schema (DDL-bound)               |
| **Query Language**   | Cosmos SQL, LINQ, SDKs                 | T-SQL, procedural extensions            |
| **Use Cases**        | IoT, Retail, Gaming, Analytics         | OLTP, BI, Legacy, Finance               |

---

### 🧠 Scenario-Based Q&A

1. **You’re designing a real-time ride-hailing app backend. Which DB and why?**  
  **Answer**: Cosmos DB with partition key on `userId|hour`, session consistency, and autoscale RU/s for handling traffic spikes.

2. **You need daily aggregates of order data with deep joins. Which is more efficient?**  
  **Answer**: SQL Server with a denormalized star schema and columnstore index. It is optimized for analytical queries and aggregates.

3. **You want to expose APIs that support flexible querying and global low latency.**  
  **Answer**: Cosmos DB. It supports schema flexibility, multiple APIs (SQL, MongoDB), and multi-region latency optimization.

4. **You need strict transactional support and ACID guarantees across multiple tables.**  
  **Answer**: SQL Server. It provides robust multi-statement ACID transactions and relational integrity constraints.

---

### ✅ Practice Challenge: Global Food Delivery Platform

#### Design Overview
- **Cosmos DB**:  
  - Use for live order ingestion and event streams.  
  - Partition key: `region|timestamp` for scalability.  
  - Enable Change Feed for real-time notifications.  

- **SQL Server**:  
  - Use for billing, analytics, and compliance.  
  - Schema: Normalized for OLTP, denormalized for OLAP.  
  - Indexing: Use columnstore for analytical queries.  

- **Shared Identity**:  
  - Use Azure AD for unified authentication.  
  - Externalize user identity for cross-system access.

---

### 📦 Deliverables

1. **Architecture Comparison Matrix**:  
  A detailed table comparing Cosmos DB and SQL Server for the platform's requirements.

2. **Design Proposal Slide**:  
  A concise slide outlining the backend architecture for the food delivery app.

3. **Scenario Q&A Sheet**:  
  A document with scenario-based questions and answers, including reasoning.

4. **Mock Interview Questions**:  
  A set of questions with answers to test understanding of Cosmos DB and SQL Server trade-offs.


---


