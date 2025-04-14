
**FAANG-level deep dive learning path for SQL**, built to **cover and extend the Microsoft DP-300: Azure Database Administrator Associate** certification. 
It’s designed for enterprise-scale database architects and performance engineers, with real-world implementations, automation, optimization techniques, and production-grade resiliency patterns.

---

## 📘 SQL Mastery Roadmap (FAANG + DP-300)

### 🔹 **Phase 1: Core SQL Concepts & Data Modeling**
> Build a solid foundation in relational modeling and normalized schemas that scale.

**Topics:**
- Relational algebra, set theory, keys, normalization
- Star vs Snowflake schema design
- Primary/foreign keys, unique constraints, check constraints
- SQL data types, collation, NULL semantics
- Modeling transactional vs analytical workloads

**Hands-on:**
- Model a retail database using 3NF and denormalized variants
- Implement constraints and analyze referential integrity
- Use SQL Server Data Tools (SSDT) or Azure Data Studio for model validation

**FAANG Focus:**
- OLTP vs OLAP schema evolution
- Design for performance, not just correctness

---

### 🔹 **Phase 2: Advanced Querying, Joins & Window Functions**
> Go beyond CRUD — this is where real performance tuning begins.

**Topics:**
- INNER, OUTER, CROSS, and SELF joins
- CTEs, temp tables, table variables
- Window functions: `ROW_NUMBER()`, `RANK()`, `LAG()`, `LEAD()`
- Scalar vs table-valued functions

**Hands-on:**
- Write complex queries against synthetic e-commerce dataset
- Optimize windowed aggregates with indexed partitions
- Refactor multi-join legacy queries

**FAANG Focus:**
- Anti-pattern identification (RBAR, implicit conversions)
- Query plan analysis with actual vs estimated plans

---

### 🔹 **Phase 3: Indexing Internals & Query Optimization**
> The key to making or breaking large-scale systems.

**Topics:**
- B-Trees, clustered vs non-clustered indexes
- Included columns, filtered indexes
- Full-text indexing, XML and JSON indexing
- Index maintenance: fragmentation, rebuild, reorganize

**Hands-on:**
- Build index strategy for 1B+ record table
- Use `sys.dm_exec_query_stats` and `sys.dm_db_index_usage_stats`
- Tune slow queries using Query Store, Execution Plans

**FAANG Focus:**
- Index intersection, covering indexes, tipping points
- Adaptive query plans, parameter sniffing mitigation

---

### 🔹 **Phase 4: Security, RBAC, Encryption & Data Masking**
> Every enterprise demands secure-by-design systems.

**Topics:**
- SQL Server RBAC, Azure Active Directory integration
- Row-level security (RLS)
- Always Encrypted, Dynamic Data Masking
- Auditing, threat detection, access monitoring

**Hands-on:**
- Implement RLS for multi-tenant SaaS database
- Configure Azure Defender + audit log pipelines
- Encrypt columns using Always Encrypted with secure enclave

**FAANG Focus:**
- Zero Trust DB access
- Compliance-first schema design (PCI, GDPR)

---

### 🔹 **Phase 5: High Availability, Disaster Recovery & Backups**
> Design for resilience. No downtime. No data loss.

**Topics:**
- Always On availability groups, failover clustering
- Azure SQL Auto-failover groups
- Backup strategies: full, diff, log, snapshot
- Geo-replication, point-in-time restore

**Hands-on:**
- Set up an AG cluster on Azure VMs
- Simulate region failure with Auto-failover Group
- Automate PITR and validate backup integrity

**FAANG Focus:**
- Multi-region DR patterns
- SLA-aware RPO/RTO planning

---

### 🔹 **Phase 6: Automation, CI/CD & DevOps for SQL**
> Declarative infrastructure, automated deployments, DRY schemas.

**Topics:**
- SQL project pipelines with GitHub Actions / Azure DevOps
- Declarative deployments with DACPAC/BACPAC
- Drift detection and schema compare
- Secrets and key rotation with Azure Key Vault

**Hands-on:**
- End-to-end CI/CD using GitHub → DACPAC → Azure SQL
- Automate index rebuilds with SQL Agent or Logic Apps
- Track schema drift across environments

**FAANG Focus:**
- Version-controlled database as code
- Blue-Green schema deployments

---

### 🔹 **Phase 7: Monitoring, Performance Tuning & Observability**
> Build dashboards, detect anomalies, and fix performance blind spots.

**Topics:**
- Wait statistics, deadlock graphs, blocking chains
- Baselines and custom metrics (DTU, CPU, memory, IOPS)
- Azure Monitor, Log Analytics, and custom alerts
- Query Store and intelligent query processing

**Hands-on:**
- Build Grafana dashboards from Azure SQL logs
- Diagnose and fix tempdb contention and plan cache bloat
- Set up alerts for expensive queries and I/O bottlenecks

**FAANG Focus:**
- Observability-first design
- Auto-mitigation strategies for top-k query issues

---

### 🔹 **Phase 8: Migration & Hybrid Strategy (On-Prem ↔ Azure)**
> Handle legacy modernization and hybrid cloud at enterprise scale.

**Topics:**
- Azure Database Migration Service (DMS)
- Offline vs Online migration
- SQL MI vs SQL DB vs SQL on IaaS
- Hybrid connectivity, ExpressRoute, VPN, Private Link

**Hands-on:**
- Migrate on-prem to Azure SQL with zero downtime
- Convert SSIS pipelines to ADF
- Design hybrid HA + DR architecture

**FAANG Focus:**
- Modernize legacy workloads with CI/CD + monitoring
- Lift-and-optimize over lift-and-shift

---

### 🧠 DP-300 Certification Domain Mapping

| DP-300 Topic Area                        | SQL Mastery Phase     |
|-----------------------------------------|------------------------|
| Plan and implement data platform        | Phase 1, 2, 8          |
| Manage and configure databases          | Phase 3, 4, 5          |
| Monitor and optimize resources          | Phase 3, 7             |
| Automate tasks                          | Phase 6                |
| Implement security                      | Phase 4                |
| Plan and configure HA/DR                | Phase 5, 8             |
| Perform administration using tools      | All phases             |

---

Would you like me to start expanding **Phase 1** in blog-style, with real-world examples and tooling next? I can also generate:
- 💾 DOCX + Markdown export
- 🧰 GitHub starter pack with SQL scripts, templates
- 🧪 Practice scenario workbook (FAANG-level challenges)





## 📘 SQL Mastery – Phase 1: Core SQL Concepts & Data Modeling (FAANG Level)

This phase lays the **foundation of relational thinking**, the heart of any SQL system, whether it’s a transactional engine like SQL Server or a distributed Azure SQL DB. Designed to go **beyond DP-300**, it prepares you to model, manage, and optimize large-scale enterprise systems from Day 1.

---

### 🎯 Objective
- Understand relational modeling
- Master normalization vs denormalization trade-offs
- Design schemas for transactional and analytical workloads
- Use tooling to visualize, validate, and optimize schemas

---

### 🔍 Key Concepts

#### 🔸 What is a Relational Database?
- Based on **set theory and relational algebra**
- Data is stored in **tables (relations)** with **rows (tuples)** and **columns (attributes)**

#### 🔸 Primary Keys & Foreign Keys
- **Primary Key (PK)** uniquely identifies each row
- **Foreign Key (FK)** enforces referential integrity between tables

#### 🔸 Constraints
- **Unique**: No duplicate values
- **Check**: Custom rule (e.g., Age > 18)
- **Not Null**: Column must have a value
- **Default**: Auto-fill column if value not supplied

#### 🔸 Normalization
- Goal: **Eliminate redundancy**, ensure **data integrity**
- Forms:
  - **1NF** – Atomic columns (no arrays)
  - **2NF** – No partial dependency (composite PKs)
  - **3NF** – No transitive dependency

#### 🔸 Denormalization
- Combine tables for **performance** in reads
- Increases **redundancy**, but reduces **joins**

#### 🔸 OLTP vs OLAP
| Feature        | OLTP                     | OLAP                          |
|----------------|--------------------------|-------------------------------|
| Workload       | Transactions             | Analytics, Reporting          |
| Schema         | Normalized (3NF)         | Star/Snowflake (Denormalized) |
| Query Type     | INSERT/UPDATE/SELECT     | SELECT (aggregations)         |
| Latency Goal   | Milliseconds             | Seconds to minutes            |

---

### 🧰 Tooling

| Tool               | Purpose                                     |
|--------------------|---------------------------------------------|
| Azure Data Studio  | Querying, ERD, schema diagrams              |
| SSMS               | SQL Server configuration and inspection     |
| dbdiagram.io       | Visual schema design (browser-based)        |
| SQL Server Data Tools (SSDT) | Declarative schema project and deployment |

---

### 🛠 Real-World Example: Retail E-Commerce DB

#### 🔸 Entity Breakdown
- Customers (User Info)
- Orders (Transactions)
- Products (Inventory)
- Order_Items (Line Items)

#### 🔸 Normalized Model (3NF)
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

#### 🔸 Star Schema for OLAP
- Fact Table: `FactSales`
- Dimensions: `DimCustomer`, `DimProduct`, `DimDate`

---

### 🧠 FAANG-Level Scenarios

#### 1. **Multi-Tenant SaaS**
- Normalize per-tenant schema with `TenantID`
- Consider row-level security (Phase 4)

#### 2. **Inventory System**
- Use denormalized product snapshots for faster lookups
- Implement slowly changing dimensions (Type 2) for OLAP

---

### ✅ Practice Tasks
- Normalize a blog site schema to 3NF
- Convert a normalized schema into a star schema
- Create ERD using Azure Data Studio
- Write queries with joins across 4+ tables

---

### 📦 Deliverables
- SQL scripts for DDL & seed data
- ERD diagrams
- Dataset mockups for OLTP and OLAP
- Cheat sheet: Normalization vs Denormalization patterns


---

## 📘 SQL Mastery – Phase 2: Advanced Querying, Joins & Window Functions (FAANG Level)

This phase focuses on writing high-performance, real-world queries that power analytical dashboards, complex business logic, and large-scale reporting pipelines. Mastering this sets you apart in FAANG-style interviews and systems design.

---

### 🎯 Objective
- Master all types of joins and their execution
- Learn window functions for row-based analysis
- Use derived tables, CTEs, temp tables effectively
- Optimize for large-volume, low-latency querying

---

### 🔍 Key Concepts

#### 🔸 JOIN Types
| Join Type     | Purpose                                              | SQL Syntax                        |
|---------------|------------------------------------------------------|-----------------------------------|
| INNER JOIN    | Matching records in both tables                     | `SELECT ... FROM A INNER JOIN B`  |
| LEFT JOIN     | All from A, matches from B, NULLs if none           | `LEFT OUTER JOIN`                 |
| RIGHT JOIN    | All from B, matches from A, NULLs if none           | `RIGHT OUTER JOIN`                |
| FULL JOIN     | All from A and B, NULLs for non-matches             | `FULL OUTER JOIN`                 |
| CROSS JOIN    | Cartesian product (every row with every row)        | `FROM A CROSS JOIN B`             |
| SELF JOIN     | Join a table with itself                            | Alias table twice                 |

#### 🔸 CTEs (Common Table Expressions)
```sql
WITH TopCustomers AS (
  SELECT CustomerID, COUNT(*) AS Orders
  FROM Orders
  GROUP BY CustomerID
)
SELECT * FROM TopCustomers WHERE Orders > 10;
```

#### 🔸 Temp Tables vs Table Variables
- Temp tables persist in `tempdb` (`#temp`)
- Table variables (`@tbl`) stored in memory, but behave differently under load
- Use **temp tables for large rowsets**, **table variables for scoped logic**

#### 🔸 Derived Tables
```sql
SELECT *
FROM (
  SELECT CustomerID, COUNT(*) AS Orders FROM Orders GROUP BY CustomerID
) AS Derived
WHERE Orders > 10;
```

---

### 🔮 Window Functions (Analytics Powerhouse)

#### 🔸 Use Cases:
- Ranking (e.g. Top-N per category)
- Gap detection
- Moving averages / running totals

#### 🔸 Key Functions:
- `ROW_NUMBER()` – unique row number
- `RANK()` / `DENSE_RANK()` – with ties
- `NTILE(n)` – percentile bucketing
- `LAG()` / `LEAD()` – previous/next row
- `SUM(...) OVER(...)` – running total

#### 🔸 Example:
```sql
SELECT CustomerID, OrderDate,
       ROW_NUMBER() OVER(PARTITION BY CustomerID ORDER BY OrderDate) AS PurchaseRank
FROM Orders;
```

---

### 🛠 Real-World Scenario: Sales Leaderboard

**Problem:** Find the top 3 customers by revenue each month.
```sql
WITH MonthlyRevenue AS (
  SELECT CustomerID, MONTH(OrderDate) AS Month,
         SUM(Quantity * Price) AS Revenue
  FROM Orders O
  JOIN OrderItems I ON O.OrderID = I.OrderID
  JOIN Products P ON I.ProductID = P.ProductID
  GROUP BY CustomerID, MONTH(OrderDate)
)
SELECT * FROM (
  SELECT *,
         ROW_NUMBER() OVER(PARTITION BY Month ORDER BY Revenue DESC) AS Rank
  FROM MonthlyRevenue
) Ranked
WHERE Rank <= 3;
```

---

### 📊 Optimization Tips
- Avoid `SELECT *` in large joins
- Always use indexed columns in join predicates
- Avoid scalar UDFs in SELECT list
- Use `OPTION (RECOMPILE)` only when needed
- Filter early in derived tables or CTEs to reduce workload

---

### 🧠 FAANG-Level Scenarios
1. **Fraud Detection**: Use `LAG()` to detect gaps or sudden value spikes
2. **Top-N Analysis**: Multi-tier leaderboards using `DENSE_RANK()`
3. **Time Series**: Use `LEAD()` to calculate duration between events

---

### ✅ Practice Tasks
- Write 3-join queries with different join types
- Generate row numbers per group
- Detect churn with `LAG()` on subscription status
- Convert a nested subquery into a CTE

---

### 📦 Deliverables
- SQL scripts with query samples
- Benchmark dataset for join tuning
- FAANG challenge: Order retention with `LAG()`


---


## 📘 SQL Mastery – Phase 3: Indexing Internals & Query Optimization (FAANG Level)

This phase focuses on **index strategies and query optimization**—the most critical area for performance engineering. Poor indexing leads to slow systems, wasted CPU, and customer frustration. FAANG engineers obsess over every scan, seek, and execution plan.

---

### 🎯 Objective
- Understand how SQL Server physically stores and accesses data
- Design optimal indexing strategies for different query types
- Analyze query plans and fix bottlenecks
- Master optimizer behavior and metadata usage

---

### 🔍 Key Concepts

#### 🔸 Types of Indexes
| Type                  | Use Case                                                 |
|-----------------------|----------------------------------------------------------|
| Clustered Index       | Controls physical order of rows (only one per table)     |
| Non-Clustered Index   | Alternate access paths, multiple allowed                 |
| Covering Index        | Includes all columns in SELECT (avoids lookups)          |
| Filtered Index        | For sparse data (e.g. `WHERE Status = 'Active'`)         |
| Composite Index       | Multi-column index with ordering                         |
| Full-Text Index       | For searching unstructured text                          |
| XML / JSON Index      | On document-type columns                                 |

---

#### 🔸 Anatomy of a B-Tree (Index Structure)
- **Root Page** → points to **Intermediate Pages** → points to **Leaf Pages**
- **Clustered index leaf** = actual data
- **Non-clustered index leaf** = pointers (RID or clustered key)

---

### 🧪 Query Optimization Process
1. **Parsing** → Syntax check
2. **Binding** → Metadata resolution
3. **Optimization** → Multiple plan generations
4. **Execution** → Best plan picked and executed

---

### 🧠 Key DMV Queries for Analysis
```sql
-- Top 5 expensive queries
SELECT TOP 5 qs.total_elapsed_time / qs.execution_count AS avg_time, st.text
FROM sys.dm_exec_query_stats qs
JOIN sys.dm_exec_sql_text(qs.sql_handle) st ON 1=1
ORDER BY avg_time DESC;

-- Index usage
SELECT * FROM sys.dm_db_index_usage_stats;
```

---

### 🔧 Real-World Indexing Scenarios

#### 1. **High Read Workload (OLTP)**
- Use **covering indexes** to avoid key lookups
- Composite index on selective WHERE + ORDER BY columns

#### 2. **Write-Heavy System**
- Avoid over-indexing
- Consider filtered indexes and minimal maintenance

#### 3. **Analytics System**
- Use indexed views or materialized aggregates
- Pre-aggregate via computed columns or batch ETL

---

### 📊 Execution Plan Analysis
| Icon               | Meaning                    | Tune Tip                      |
|--------------------|----------------------------|-------------------------------|
| Table Scan         | No usable index            | Add index or rewrite filter   |
| Index Seek         | Efficient search           | Optimal                       |
| Key Lookup         | Additional fetch from base | Avoid with covering index     |
| Nested Loop Join   | Good for small input       | Use with indexed join keys    |
| Hash Join          | For large inputs            | Check memory grant            |
| Merge Join         | Sorted inputs               | Use if pre-ordered            |

---

### 🧠 FAANG-Level Scenarios
- **Query Plan Regression**: Use Query Store to detect and fix
- **Parameterized Query Bottleneck**: Parameter sniffing fixes with OPTION (RECOMPILE) or OPTIMIZE FOR
- **Ad-hoc Plan Bloat**: Tune `optimize for ad hoc workloads` setting

---

### ✅ Practice Tasks
- Build indexes for 1B+ row table (simulate with dummy data)
- Tune queries using actual execution plans
- Create filtered index + test read/write tradeoff
- Compare scalar UDF vs inline TVF in SELECT list

---

### 📦 Deliverables
- Benchmark queries + plan screenshots
- Indexed vs non-indexed performance reports
- Index strategy template by workload type
- DMV-based performance checklist

---

## 📘 SQL Mastery – Phase 4: Security, RBAC, Encryption & Data Masking (FAANG Level)

This phase focuses on building **secure-by-design SQL systems** for enterprise, compliance-driven, and multi-tenant environments. FAANG-level DBAs ensure that every table, query, and login operates within the boundaries of **least privilege** and **zero trust.**

---

### 🎯 Objective
- Implement SQL Server and Azure SQL security best practices
- Design RBAC and tenant-isolated models
- Apply encryption at rest and in-transit
- Enforce masking and auditing for compliance (e.g., GDPR, HIPAA, PCI)

---

### 🔐 Key Security Models

#### 🔸 Authentication
- SQL Authentication (username/password)
- Azure AD Authentication (centralized RBAC)
- Managed Identity for app-to-database access

#### 🔸 Authorization
- **Database Roles**: `db_datareader`, `db_datawriter`, `db_owner`
- **Custom Roles**: Granular permissions (`GRANT`, `DENY`, `REVOKE`)
- **Schemas**: Logical isolation of tables, procedures, and views

#### 🔸 Row-Level Security (RLS)
- Predicate-based filtering applied automatically to queries
```sql
CREATE FUNCTION fnSecurityPredicate(@TenantID INT)
RETURNS TABLE WITH SCHEMABINDING AS
RETURN SELECT 1 AS result WHERE @TenantID = CAST(SESSION_CONTEXT(N'TenantID') AS INT);

CREATE SECURITY POLICY TenantIsolationPolicy
ADD FILTER PREDICATE fnSecurityPredicate(TenantID) ON Customers
WITH (STATE = ON);
```

---

### 🔒 Encryption Techniques

| Encryption Type         | Use Case                                 |
|-------------------------|------------------------------------------|
| Transparent Data Encryption (TDE) | Encrypts data at rest (entire DB)          |
| Always Encrypted        | Encrypts column values (client-side)     |
| TLS Encryption          | Encrypts data in transit (network-level) |
| Cell-level Encryption   | Manual encryption of column data         |

#### 🔸 Always Encrypted Example
```sql
CREATE COLUMN MASTER KEY CMK WITH (
  KEY_STORE_PROVIDER_NAME = 'AZURE_KEY_VAULT',
  KEY_PATH = 'https://vault-name.vault.azure.net/keys/your-key/'
);

CREATE COLUMN ENCRYPTION KEY CEK WITH VALUES (
  COLUMN_MASTER_KEY = CMK,
  ALGORITHM = 'RSA_OAEP',
  ENCRYPTED_VALUE = 0x...
);
```

---

### 🛡️ Data Masking

#### Dynamic Data Masking (DDM)
```sql
ALTER TABLE Customers
ALTER COLUMN Email ADD MASKED WITH (FUNCTION = 'email()');
```
- Applies to SELECT queries for users without `UNMASK` permission
- Built-in mask functions: `default()`, `email()`, `partial()`, `random()`

#### Use Cases:
- Developers accessing production data
- Analysts running queries with obfuscated PII

---

### 📋 Auditing, Threat Detection & Alerts

| Feature                      | Purpose                                    |
|------------------------------|--------------------------------------------|
| SQL Audit                    | Track login, permission, DDL, and query events |
| Azure SQL Threat Detection   | Detects SQL injection, abnormal access     |
| Log Analytics + Workbooks    | Visualize failed logins, blocked users     |
| Auditing to Blob / Event Hub | Export for compliance or SIEM systems      |

---

### 🧠 FAANG-Level Scenarios

1. **Multi-Tenant SaaS DB**:
   - Use RLS with SESSION_CONTEXT for tenant-based access
   - Encrypt tenant PII with Always Encrypted

2. **Zero-Trust DevOps**:
   - Block direct user access; route through managed identities
   - Disable SQL Auth in favor of Azure AD + conditional access

3. **Audit & Compliance**:
   - Mask all PII from analysts, audit access to GDPR-protected columns
   - Export logs to Sentinel or Splunk

---

### ✅ Practice Tasks
- Implement RLS for a `Customers` table scoped to a `TenantID`
- Enable TDE and Always Encrypted on `SSN` and `CreditCard`
- Configure dynamic masking on `Email` and `Phone`
- Enable auditing to Log Analytics and trigger alert on failed login

---

### 📦 Deliverables
- Sample RLS policy with session context
- TDE and AE setup scripts for Azure SQL
- Auditing workbook template (Azure Monitor)
- Zero-trust DB architecture diagram


---

## 📘 SQL Mastery – Phase 5: High Availability, Disaster Recovery & Backups (FAANG Level)

In production, **data durability and availability** are non-negotiable. This phase teaches you how to keep SQL environments resilient, compliant with RPO/RTO SLAs, and prepared for failovers. FAANG engineers architect multi-region disaster recovery strategies with auto-healing infrastructure.

---

### 🎯 Objective
- Design for high uptime and business continuity
- Configure Always On availability groups and failover policies
- Architect disaster recovery across regions
- Implement backup automation and verification

---

### 🏗️ High Availability (HA) Architectures

#### 🔸 HA Strategies
| Strategy                    | Target Use Case                            |
|-----------------------------|--------------------------------------------|
| Failover Cluster Instance   | On-prem SQL Server shared storage setup    |
| Always On Availability Group | Multi-node read-write replicas (sync/async) |
| Azure SQL Auto-Failover     | Built-in HA for PaaS databases             |
| Geo-Replication             | Read-scale and regional DR for Azure SQL   |

#### 🔸 AG Key Concepts
- Synchronous vs Asynchronous replication
- Automatic vs Manual failover modes
- Quorum and witness configuration

---

### 🔁 Disaster Recovery (DR)

#### 🔸 DR Objectives
| Term | Meaning                          |
|------|----------------------------------|
| RTO  | Recovery Time Objective – how fast you recover |
| RPO  | Recovery Point Objective – max tolerable data loss |

#### 🔸 DR Design Principles
- Separate control and data planes
- Use cross-region deployments (e.g., East US ↔ West US)
- Include DNS failover or front-end rerouting

---

### 💾 Backup & Restore Strategy

#### 🔸 Backup Types
| Backup Type | Purpose                     |
|-------------|-----------------------------|
| Full        | Complete snapshot of DB     |
| Differential| Changes since last full     |
| Transaction Log | Point-in-time recovery    |
| Snapshot    | File-level VM-based backups |

#### 🔸 Restore Options
- Restore to point in time
- Geo-restore from paired region
- PITR (Point-In-Time Restore) via Azure Portal or CLI

#### 🔸 Backup Automation
```sql
-- Simple SQL Agent Job Example
BACKUP DATABASE [Sales] TO DISK = N'D:\Backups\Sales.bak'
WITH FORMAT, COMPRESSION, INIT;
```
- Azure SQL handles backups automatically (7-35 days retention)
- Use Azure Automation or Logic Apps for notifications

---

### 🧠 FAANG-Level Scenarios

1. **Global E-commerce Site**
   - Use Geo-replication to reduce latency in Asia + DR coverage
   - Failover policies triggered by monitoring alerts

2. **Finance App**
   - Dual-region failover group with automatic RPO < 5 min
   - Transaction log backups every 5 mins with test restores daily

3. **Banking Compliance**
   - Immutable blob storage for backup compliance
   - Restore verification via DBCC CHECKDB on standby DB

---

### ✅ Practice Tasks
- Set up Always On AG with 2-node SQL Server VMs on Azure
- Simulate failover using Auto-Failover Group on Azure SQL DB
- Test backup/restore strategy with differential and log backups
- Monitor RPO drift with custom telemetry

---

### 📦 Deliverables
- AG deployment ARM/Bicep templates
- Backup policy documentation for SQL MI and Azure SQL
- DR playbook with failover + validation steps
- PITR test script library


---

## 📘 SQL Mastery – Phase 6: Automation, CI/CD & DevOps for SQL (FAANG Level)

This phase focuses on turning database deployment into **automated, reliable, repeatable pipelines** — critical for modern DevOps culture. FAANG teams treat database changes like code: tested, versioned, and deployed through CI/CD.

---

### 🎯 Objective
- Adopt Database-as-Code practices
- Build Git-integrated deployment pipelines
- Enforce schema drift detection and environment parity
- Automate change validation, rollback, and secrets management

---

### 🏗️ DevOps Foundations for SQL

#### 🔸 SQL Deployment Options
| Approach       | Tool / Format         | Description                           |
|----------------|------------------------|---------------------------------------|
| Declarative    | DACPAC + SSDT         | Desired schema state                  |
| Imperative     | T-SQL scripts          | Step-by-step changes                  |
| Hybrid         | Flyway / Liquibase     | Version-controlled migrations         |

#### 🔸 DevOps Tools
- Azure DevOps Pipelines / GitHub Actions
- Azure CLI / PowerShell for provisioning
- Azure Key Vault for secure credential management
- SQLPackage for DACPAC deployment

---

### 🛠 Pipeline Architecture (Git → Dev → Stage → Prod)

#### Example GitHub Actions Workflow:
```yaml
jobs:
  build-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Build DACPAC
        run: sqlpackage /Action:Extract ...

      - name: Deploy to Dev
        run: sqlpackage /Action:Publish /TargetConnectionString:$DEV_CONN
```

#### Automation Best Practices:
- Deploy using **DACPAC** for safety and drift detection
- Run `sqlcmd` smoke tests post-deploy
- Gate production deploys via approval workflows

---

### 🔍 Schema Drift & Change Tracking

#### 🔸 Tools & Techniques
| Tool              | Use Case                          |
|-------------------|-----------------------------------|
| SSDT Compare Tool | Detect drift from desired schema  |
| Redgate SQL Compare | Visual and CLI-based diff tools |
| Schema snapshots  | Historical comparisons and audits |

#### 🔸 Drift Alerting
- Schedule periodic validation
- Alert on unauthorized changes
- Auto-rollback unsafe commits

---

### 🔐 Secrets & Identity

- Use **Azure Key Vault** to inject secrets into pipelines
- Use **Managed Identity** for app authentication to Azure SQL
- Avoid SQL Authentication in production pipelines

---

### 🧠 FAANG-Level Scenarios

1. **Global SaaS Deployment**
   - Multi-tenant schemas managed with Flyway
   - Blue-green schema deployment with rollback plan

2. **Regulated Industry**
   - Enforce 4-eye deploy review with Azure DevOps gates
   - DACPAC deployment validation with pre/post schema snapshots

3. **Data Platform Startup**
   - Auto-provision new SQL databases per environment using Terraform + GitHub Actions

---

### ✅ Practice Tasks
- Create GitHub Actions pipeline with DACPAC deployment to Azure SQL
- Use SSDT to detect schema drift across Dev and Prod
- Secure connection strings using Azure Key Vault references
- Build rollback-ready migration pipeline using Flyway

---

### 📦 Deliverables
- GitHub Actions CI/CD YAML for SQL
- DACPAC build + deploy templates
- Drift detection playbook
- Azure SQL deployment readiness checklist


---

## 📘 SQL Mastery – Phase 7: Monitoring, Performance Tuning & Observability (FAANG Level)

This phase is about ensuring your SQL infrastructure is **visible, measurable, and proactive**. Monitoring is not just about uptime — it’s about preempting bottlenecks, detecting anomalies, and correlating slowdowns to root causes. At FAANG scale, telemetry is king.

---

### 🎯 Objective
- Design observability-first database environments
- Monitor system health, performance, and access patterns
- Proactively tune based on telemetry
- Build custom dashboards and alerting pipelines

---

### 📊 Monitoring Dimensions

#### 🔸 Resource Monitoring
| Metric            | What It Shows                    |
|-------------------|----------------------------------|
| CPU & Memory      | Resource saturation              |
| I/O latency       | Disk bottlenecks                 |
| TempDB usage      | Scratch space under pressure     |
| DTU / vCore usage | Azure SQL resource health        |

#### 🔸 Query Monitoring
| Tool/Metric       | Purpose                          |
|-------------------|----------------------------------|
| Query Store       | Plan history + regressions       |
| `sys.dm_exec_*`   | Real-time query behavior         |
| Wait stats        | Bottleneck root cause (e.g., IO, CPU) |
| Deadlock graphs   | Transaction contention mapping   |

---

### 📦 Azure Monitoring Stack

| Component             | Role                                      |
|------------------------|-------------------------------------------|
| Azure Monitor          | Platform-level telemetry                  |
| Log Analytics          | Queryable logs and metrics                |
| Diagnostic Settings    | Push logs to Event Hub / Storage / LA     |
| Workbooks              | Custom dashboards and metrics visualizers |
| Alerts + Action Groups | Trigger emails, webhooks, automation      |

#### Sample Kusto Query (CPU Alert)
```kusto
AzureDiagnostics
| where Resource == "sqlserver" and CPU_percent > 80
| summarize max(CPU_percent) by bin(TimeGenerated, 5m)
```

---

### 🛠 Query Tuning Workflow
1. Identify slow query via Query Store or DMVs
2. Analyze actual execution plan (not just estimated!)
3. Identify scans, lookups, implicit conversions
4. Tune indexes, rewrite joins, filter earlier
5. Revalidate plan post-deploy

#### Key Queries:
```sql
-- Top CPU Queries
SELECT TOP 5 total_worker_time/1000 AS CPU_ms, text
FROM sys.dm_exec_query_stats
CROSS APPLY sys.dm_exec_sql_text(sql_handle)
ORDER BY CPU_ms DESC;

-- Wait Statistics
SELECT wait_type, wait_time_ms
FROM sys.dm_os_wait_stats
ORDER BY wait_time_ms DESC;
```

---

### 📈 Dashboards & Alerts
- CPU/memory/IO heatmaps across instances
- Long-running queries grouped by database
- Query plan change alerts from Query Store
- Failed logins or access anomalies via Azure Defender

---

### 🧠 FAANG-Level Scenarios

1. **Query Regression Detection**
   - Enable Query Store with automatic plan forcing
   - Alert when a plan change increases duration by >50%

2. **Real-Time Analytics Platform**
   - Use Workbooks to monitor DTU and query throughput per minute
   - Auto-scale recommendations based on burst telemetry

3. **Security-Driven Monitoring**
   - Failed login alerts from unexpected IPs
   - Correlate activity to Log Analytics + Sentinel

---

### ✅ Practice Tasks
- Build a dashboard showing top 10 queries by CPU and IO
- Analyze 3+ execution plans and tune them
- Create Log Analytics alert for blocked queries or tempdb spills
- Simulate deadlock and resolve using trace + plan tuning

---

### 📦 Deliverables
- Azure Monitor Workbook JSON template
- Custom KQL alerting queries
- DMV tuning toolkit
- Query Store tuning playbook


---

## 📘 SQL Mastery – Phase 8: Migration & Hybrid Strategy (On-Prem ↔ Azure) (FAANG Level)

In this final phase, we master the complex process of **migrating, modernizing, and managing SQL workloads across hybrid cloud environments.** Whether you're moving from an on-prem monolith to Azure SQL or modernizing a multi-region system, this phase prepares you for FAANG-grade decisions.

---

### 🎯 Objective
- Evaluate cloud-native vs hybrid SQL options
- Plan and execute seamless migrations
- Handle compatibility, latency, and performance constraints
- Automate deployment and governance across environments

---

### 🏗️ SQL Deployment Models in Azure

| Option                  | Ideal For                            |
|-------------------------|----------------------------------------|
| SQL Server on Azure VM | Lift-and-shift, full control needed    |
| Azure SQL Managed Instance | Feature parity with SQL Server       |
| Azure SQL Database     | Cloud-native, auto-patching/scaling    |

---

### 🔁 Migration Strategy Blueprint

#### 🔸 Migration Stages
1. **Discovery & Assessment**
   - Use Azure Migrate, Data Migration Assistant (DMA)
   - Detect blockers: SQL Server version, deprecated features, size

2. **Planning & Design**
   - Choose target: SQL VM, MI, or DB
   - Define RTO/RPO, downtime windows

3. **Schema & Data Migration**
   - Tools: Azure Database Migration Service (DMS), SSMS scripts, BACPAC
   - Online vs Offline modes

4. **Validation & Cutover**
   - Test queries, performance, and access controls
   - Switch DNS, application strings

---

### 🔧 Migration Tools

| Tool                     | Use Case                              |
|--------------------------|----------------------------------------|
| Azure Migrate            | Discovery and workload analysis        |
| Data Migration Assistant | Compatibility checks and remediation   |
| Azure DMS                | Online/offline data movement           |
| BACPAC / DACPAC          | Schema and lightweight data transfer   |

---

### 🌍 Hybrid Connectivity

#### 🔸 Cross-Environment Access
- Private Link, ExpressRoute, or VPN Gateway
- Azure Arc-enabled SQL Server
- AD/LDAP federation between domains

#### 🔸 Multi-Region Sync
- Use geo-replication and failover groups
- DNS routing via Azure Traffic Manager or Front Door

---

### 🔐 Governance & Compliance
- Use Azure Policy to enforce backup, encryption, SKU types
- Integrate with Azure Purview for data lineage and cataloging
- Track configuration drift with Azure Blueprints or Terraform compliance checks

---

### 🧠 FAANG-Level Scenarios

1. **Hybrid DR Setup**
   - On-prem SQL Server with Azure SQL MI as async replica
   - Auto-failover DNS routing with Front Door

2. **Legacy Modernization**
   - Migrate from SQL 2008 to Azure SQL with compatibility level updates
   - Wrap legacy procedures with modern APIs

3. **Global Expansion**
   - Deploy geo-replicated Azure SQL DB per continent
   - Write-local/read-anywhere design for latency optimization

---

### ✅ Practice Tasks
- Use DMA to assess a legacy SQL Server for Azure readiness
- Perform schema migration using DACPAC and test workloads
- Configure Azure SQL MI with private endpoint from on-prem
- Implement geo-replication between two Azure SQL DBs

---

### 📦 Deliverables
- Migration runbook (assessment → cutover)
- Compatibility remediation report template
- Terraform templates for hybrid topology
- Azure DMS automation script examples


---

