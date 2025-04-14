# 📅 Week 4 – Integration, CI/CD, Automation & Observability (Cosmos DB + SQL)

🧠 **Learning Goal**: Automate deployments, integrate Cosmos & SQL into enterprise pipelines, and build real-time observability dashboards.

🕒 **Daily Time Budget**: 2–3 hours

---

## 🗓 Day 1: Cosmos DB – Deployment Automation & IaC

### 🎯 Objectives
- Automate Cosmos DB deployment using Infrastructure-as-Code (IaC)
- Apply configuration as code for throughput, indexing, consistency, and networking
- Build reproducible, version-controlled templates for enterprise use

---

### 🔍 Key Concepts
- **IaC Tools**:
  - ARM templates (native Azure format)
  - Bicep (declarative + simplified syntax)
  - Terraform (multi-cloud IaC)
- **Deployable Components**:
  - Cosmos DB account (multi-region, consistency level)
  - Databases, containers, indexing policy, throughput config
  - Private endpoints, firewall rules, network ACLs
- **Parameterization**:
  - Environment-based variables (dev, test, prod)
  - Tags, resource names, SKUs

---

### 🔧 Hands-On
- Author Bicep template to deploy Cosmos DB Core SQL API with 2 regions
- Include indexing policy + autoscale settings
- Deploy container with custom partition key and TTL
- Add private endpoint binding to subnet

```bicep
resource cosmos 'Microsoft.DocumentDB/databaseAccounts@2021-06-15' = {
  name: 'myCosmosAccount'
  location: resourceGroup().location
  kind: 'GlobalDocumentDB'
  properties: {
    consistencyPolicy: {
      defaultConsistencyLevel: 'Session'
    }
    locations: [
      { locationName: 'East US', failoverPriority: 0 },
      { locationName: 'West Europe', failoverPriority: 1 }
    ]
    databaseAccountOfferType: 'Standard'
    enableAutomaticFailover: true
    enableFreeTier: false
  }
}
```

---

### 🧠 Deep Dive: IaC Best Practices
| Practice                  | Recommendation                      |
|---------------------------|--------------------------------------|
| Modular Templates         | Split account, DB, container modules |
| Source Control            | Store in GitHub / Azure Repos        |
| CI/CD Integration         | Use GitHub Actions / Azure DevOps    |
| Parameter Files           | Use `*.parameters.json` per stage    |
| Drift Detection           | Use `what-if`, `terraform plan`      |

---

### 🧪 Mini-Lab
- Build a Cosmos DB Bicep module with configurable consistency + autoscale
- Deploy into two environments (dev, test)
- Run a drift check and modify index policy dynamically

---

### ✅ Practice Tasks
- Write Terraform template to deploy Cosmos with VNET rules
- Convert existing ARM JSON to Bicep and refactor
- Deploy Cosmos with Key Vault + private endpoint
- Run GitHub Actions to deploy Cosmos on PR merge

---

### 📦 Deliverables
- Bicep + Terraform IaC Modules
- Multi-env deployment parameters (dev/test/prod)
- CI/CD pipeline YAML (GitHub Actions)
- Drift Report + Change History Log



---

## 🗓 Day 2: SQL – CI/CD Pipelines with DACPAC, SSDT & GitHub Actions

### 🎯 Objectives
- Implement CI/CD for Azure SQL and SQL Server using modern DevOps tooling
- Deploy schema changes via DACPAC/SSDT with approvals and rollback plans
- Integrate GitHub Actions or Azure DevOps Pipelines into enterprise workflows

---

### 🔍 Key Concepts
- **Deployment Artifacts**:
  - DACPAC (schema-only deployment package)
  - BACPAC (schema + data)
  - SQL Scripts (pre/post deployment hooks)
- **Tooling**:
  - SQL Server Data Tools (SSDT) in Visual Studio
  - sqlpackage CLI and Azure DevOps Extensions
- **CI/CD Integration**:
  - GitHub Actions
  - Azure Pipelines
  - Manual Approvals & Environments
- **Schema Drift and Conflicts**:
  - Drift detection before deployment
  - Safe publish profile configs (`BlockOnPossibleDataLoss = true`)

---

### 🔧 Hands-On
- Create SSDT SQL project and import schema
- Generate DACPAC and deploy to dev SQL DB
- Create GitHub Actions workflow to deploy DACPAC on PR merge
- Simulate drift and run schema comparison via sqlpackage

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Deploy DACPAC
        run: |
          sqlpackage /Action:Publish \
                    /SourceFile:"./MyApp.dacpac" \
                    /TargetServerName:"$(SQL_SERVER)" \
                    /TargetDatabaseName:"MyAppDb" \
                    /TargetUser:"$(SQL_USER)" /TargetPassword:"$(SQL_PASS)"
```

---

### 🧠 Deep Dive: Deployment Models & Environments
| Environment | Deployment Model | Approval Gate | Rollback Strategy |
|-------------|------------------|----------------|-------------------|
| Dev         | CI auto-deploy   | None           | Re-deploy DACPAC  |
| Test/UAT    | Manual approve   | Required       | Schema restore    |
| Prod        | Scheduled window | Required       | Point-in-time restore (PITR) |

---

### 🧪 Mini-Lab
- Generate DACPAC and deploy to dev/test
- Introduce breaking change and test deployment failure
- Review pipeline logs and deployment summaries

---

### ✅ Practice Tasks
- Build GitHub Actions pipeline for SQL schema deploy
- Set up environment approvals and branch policies
- Test rollback using a backup or saved DACPAC
- Monitor deployment using Azure SQL Insights

---

### 📦 Deliverables
- SSDT Project + DACPAC Build Artifacts
- GitHub Actions Pipeline YAML
- Schema Drift Report (before/after comparison)
- Change Management Log (manual approvals + rollback actions)



---

## 🗓 Day 3: Cosmos DB – Data Ingestion & Stream Processing Pipelines

### 🎯 Objectives
- Design and implement high-throughput Cosmos DB ingestion pipelines
- Use Change Feed for downstream streaming and event processing
- Integrate Cosmos DB with Azure Data Factory, Event Hub, and Azure Functions

---

### 🔍 Key Concepts
- **Ingestion Methods**:
  - SDK Bulk APIs (Node.js, .NET, Python)
  - Azure Data Factory Copy Activity
  - Event Hub → Stream Analytics → Cosmos
- **Change Feed**:
  - Emits all inserts/updates (ordered by _ts)
  - Supports pull (SDK, Azure Functions) or push-based triggers
- **Materialized Views & Stream Analytics**:
  - Cosmos → Change Feed → Functions → Output Collection
  - ETL with transformation logic, filtering, partition rebalance

---

### 🔧 Hands-On
- Build ingestion pipeline from Event Hub to Cosmos DB
- Enable Change Feed processing using Azure Functions
- Create a materialized view (summary container) with transformed data
- Set retry and dead-letter strategy for high-throughput ingestion

```csharp
public static async Task Run(
    [CosmosDBTrigger(
        databaseName: "RetailDB",
        collectionName: "Orders",
        ConnectionStringSetting = "CosmosDBConnection",
        LeaseCollectionName = "leases",
        CreateLeaseCollectionIfNotExists = true)]
    IReadOnlyList<Document> input,
    ILogger log)
```

---

### 🧠 Deep Dive: Change Feed Use Cases
| Pattern                    | Use Case                     | Downstream Target         |
|----------------------------|------------------------------|---------------------------|
| Materialized Views         | Inventory counters           | Cosmos (summary container)|
| Event Propagation          | Order placement → ship queue| Service Bus / Event Grid  |
| Real-Time Analytics        | IoT sensor streaming         | Azure Stream Analytics    |
| Data Lake Archiving        | Long-term storage            | ADLS Gen2 / Synapse       |

---

### 🧪 Mini-Lab
- Simulate 10K writes into Cosmos
- Trigger Change Feed listener via Azure Function
- Route updates to a transformed summary container
- Use Application Insights to monitor throughput and latency

---

### ✅ Practice Tasks
- Ingest mock telemetry via Event Hub to Cosmos
- Process change events to produce rolling aggregates
- Implement retry + DLQ pattern in Azure Function
- Generate metrics dashboard (RU, latency, failures)

---

### 📦 Deliverables
- Cosmos Change Feed Trigger Function App
- Event Hub → Stream → Cosmos pipeline diagram
- Summary Container Schema + RU Budgeting
- Monitoring dashboard with App Insights/KQL



---

## 🗓 Day 4: SQL – ETL, CDC, and Data Orchestration

### 🎯 Objectives
- Implement Extract, Transform, Load (ETL) pipelines for SQL workloads
- Use Change Data Capture (CDC) for incremental sync
- Build orchestration workflows using Azure Data Factory and Synapse Pipelines

---

### 🔍 Key Concepts
- **ETL Components**:
  - Extract: Source tables via linked services
  - Transform: SQL scripts, data flows, stored procs
  - Load: Into Azure SQL, Synapse, or downstream targets
- **Change Data Capture (CDC)**:
  - Log-based incremental change tracking
  - Stores changes in system tables (`cdc.*`)
  - Ideal for data lakes, near real-time reporting
- **Data Orchestration**:
  - Azure Data Factory: pipelines, triggers, integration runtimes
  - Synapse Pipelines: unified Spark + SQL workflows

---

### 🔧 Hands-On
- Enable CDC on SQL table and validate system metadata
- Build ADF pipeline to copy data from SQL to ADLS incrementally
- Use mapping data flows for transformation logic (filter, join, derive)
- Integrate with Event Grid for on-demand or scheduled runs

```sql
-- Enable CDC
EXEC sys.sp_cdc_enable_db;
EXEC sys.sp_cdc_enable_table
  @source_schema = 'dbo',
  @source_name = 'Orders',
  @role_name = NULL;
```

---

### 🧠 Deep Dive: ETL Strategy Matrix
| Source      | Trigger Type     | Frequency     | Pattern              |
|-------------|------------------|---------------|----------------------|
| OLTP SQL DB | Change Detection | Every 15 mins | ADF + CDC            |
| Staging DB  | Scheduled         | Nightly       | Full load + merge    |
| External API| Event Grid       | Realtime      | Azure Function + ELT |

---

### 🧪 Mini-Lab
- Enable CDC on a transactional table (e.g., `Orders`)
- Build ETL: copy from CDC → staging table → clean layer
- Run data flow with transformation logic (e.g., filter shipped orders)
- Monitor pipeline activity + error handling via ADF UI

---

### ✅ Practice Tasks
- Build CDC-enabled incremental load from SQL to Synapse
- Use Data Flow to flatten JSON field or pivot column
- Add alerting to pipeline failures via Azure Monitor
- Automate ETL trigger via Logic App or Event Grid

---

### 📦 Deliverables
- CDC-enabled SQL schema script
- ADF pipeline JSON (or ARM export)
- Data flow + transformation mapping logic
- Pipeline run log + success/failure monitoring output



---

## 🗓 Day 5: Observability – Cosmos DB Telemetry, Alerts & Dashboards

### 🎯 Objectives
- Instrument Cosmos DB for real-time performance and availability metrics
- Create Azure Monitor dashboards for RU/s, latency, and 429 throttling
- Automate alerting and trend analysis using KQL + Log Analytics

---

### 🔍 Key Concepts
- **Metrics to Track**:
  - RU Consumption (provisioned vs consumed)
  - Latency (P50, P95, P99)
  - 429 Throttled Requests
  - Partition-level metrics (hot keys)
  - Storage consumption
- **Tools & Dashboards**:
  - Azure Monitor → Workbooks
  - Log Analytics + Kusto Query Language (KQL)
  - Azure Metrics Alerts + Action Groups
  - App Insights (for SDK-level diagnostics)

---

### 🔧 Hands-On
- Create custom workbook with RU/s trend per operation type
- Build alert rule for RU/s approaching 80% of provisioned limit
- Log throttled operations to Log Analytics
- Visualize latency across regions using TimeChart + KQL

```kql
AzureDiagnostics
| where ResourceType == "COSMOSDB_ACCOUNTS"
| where OperationName == "Query" and statusCode_s == "429"
| summarize count() by bin(TimeGenerated, 5m), databaseName_s
```

---

### 🧠 Deep Dive: Monitoring Strategy Matrix
| Metric                | Target Audience   | Alert Threshold     | Dashboard View               |
|------------------------|--------------------|----------------------|------------------------------|
| RU Consumption         | SRE / DBA          | 80% of provisioned   | Time series (per container)  |
| Latency (P95)          | Developer          | > 200ms              | Geo heatmap / region charts  |
| Throttling (429)       | Architect / Ops    | > 5/min              | Alerts + retry impact chart  |
| Partition Heat         | Platform Eng       | Hot partition spike  | Pie chart or key histogram   |

---

### 🧪 Mini-Lab
- Write KQL query to detect throttling patterns
- Configure alert and connect to Slack or Logic App
- Create partition usage chart and identify skew
- Embed dashboard in DevOps wiki or Power BI (optional)

---

### ✅ Practice Tasks
- Build Cosmos Monitor Workbook for top-3 metrics
- Create KQL alert with dynamic thresholding
- Use Diagnostic Logs to trace request latency
- Simulate load and observe alert/response

---

### 📦 Deliverables
- Azure Workbook Template JSON
- KQL Scripts for RU, Latency, 429 Monitoring
- Alert Rule ARM Template (email + webhook)
- Ops Guide: Cosmos Performance Troubleshooting



---

## 🗓 Day 6: Observability – SQL Monitoring, Query Store & Log Analytics

### 🎯 Objectives
- Monitor SQL workloads using Query Store, DMVs, and Azure Monitor
- Analyze slow queries, blocking chains, DTU/vCore pressure
- Create alerting rules and dashboards for real-time visibility

---

### 🔍 Key Concepts
- **Telemetry Sources**:
  - Query Store: execution plan history, runtime stats
  - DMVs: system views for live diagnostics
  - Azure Monitor: built-in metrics and logs
  - Log Analytics: cross-database telemetry and trend analysis
- **Key Metrics**:
  - CPU usage, DTU %, vCore consumption
  - Query Duration (avg, P95)
  - Wait stats (PAGEIOLATCH, LCK_M_*, etc.)
  - Blocking + deadlocks

---

### 🔧 Hands-On
- Enable and configure Query Store (read/write, retention, capture mode)
- Query `sys.query_store_runtime_stats` and visualize top queries
- Create custom dashboard: Top 5 queries by CPU & duration
- Configure alert rule for DTU > 85% with Action Group

```sql
SELECT TOP 5
  qs.query_id, qt.query_sql_text,
  rs.avg_duration, rs.avg_cpu_time
FROM sys.query_store_runtime_stats rs
JOIN sys.query_store_plan p ON rs.plan_id = p.plan_id
JOIN sys.query_store_query qs ON p.query_id = qs.query_id
JOIN sys.query_store_query_text qt ON qs.query_text_id = qt.query_text_id
ORDER BY rs.avg_cpu_time DESC;
```

---

### 🧠 Deep Dive: SQL Monitoring Strategy Matrix
| Metric               | Alert Type     | Action Strategy              | Visualization         |
|----------------------|----------------|------------------------------|------------------------|
| DTU / CPU %          | Threshold      | Scale-up alert + tuning      | Time series            |
| Long-running Queries | Static list    | Query optimization or index  | Bar/heatmap (Query ID) |
| Deadlocks            | Event pattern  | Review isolation, retry logic| Sparkline & logs       |
| Wait Stats           | Classification | Tune indexes / IO config     | Pie chart by type      |

---

### 🧪 Mini-Lab
- Generate high CPU query and capture in Query Store
- Run blocking simulation and trace via DMVs
- Export dashboard to Azure DevOps wiki or share via Power BI

---

### ✅ Practice Tasks
- Enable Query Store with auto-capture
- Create KQL workbook from Log Analytics for DTU + query duration
- Simulate contention and capture with live query stats
- Set alerting rules with thresholds for long-duration queries

---

### 📦 Deliverables
- Query Store Configuration Script
- KQL Dashboards: CPU, DTU, Query Duration
- DMV Snapshot Report + Wait Stats Chart
- Alerting Setup Export (ARM/CLI)



---

## 🗓 Day 7: Integrated Observability Drill + CI/CD + Incident Response

### 🎯 Objectives
- Simulate real-world failure and monitor response via dashboards and alerts
- Combine Cosmos DB + SQL insights into a unified observability and DevOps strategy
- Automate incident response workflows with Logic Apps, ServiceNow, or Slack integration

---

### 🔍 Key Concepts
- **Observability Stack**:
  - Cosmos: RU/s, latency, 429s, partition heat
  - SQL: DTU, blocking, Query Store stats
  - App Insights: SDK tracing, telemetry correlation
- **Unified Dashboards**:
  - Azure Monitor Workbooks (multi-source)
  - KQL-based performance & failure heatmaps
- **CI/CD Alert Integration**:
  - GitHub Actions / Azure Pipelines failure tracing
  - Deployment health checks, post-deploy monitors
- **Incident Automation**:
  - Alert → Action Group → Logic App
  - Slack/Teams notifications, issue creation (e.g. Jira, ServiceNow)

---

### 🔧 Hands-On
- Build unified dashboard with Cosmos + SQL metrics
- Simulate Cosmos write throttling + SQL blocking
- Configure GitHub Action to deploy Cosmos + SQL and notify on failure
- Route alert to ServiceNow (or mock webhook + email)

```kql
// Alert on correlated throttling & CPU spike
union
  (AzureDiagnostics | where ResourceType == "COSMOSDB_ACCOUNTS" and statusCode_s == "429"),
  (AzureMetrics | where Resource == "sqlserver" and MetricName == "cpu_percent")
| summarize count() by bin(TimeGenerated, 5m), Resource
```

---

### 🧠 Deep Dive: DevOps + Incident Strategy Matrix
| Event Type         | Tool             | Response Type         | Automation Flow               |
|--------------------|------------------|------------------------|-------------------------------|
| Cosmos 429 spike   | Azure Monitor    | Alert + scale-up      | Alert → Logic App → Webhook   |
| SQL plan regression| Query Store      | Tuning + re-deploy    | Detect → PR → DACPAC pipeline |
| CI/CD failure      | GitHub Actions   | Notify + Rollback     | Job fail → Notify → Rollback  |

---

### 🧪 Mini-Lab
- Inject high write load into Cosmos → observe throttling
- Trigger SQL index drop and observe query slowdown
- Deploy via GitHub Actions and simulate pipeline failure
- Auto-create incident alert via Logic App webhook

---

### ✅ Practice Tasks
- Build multi-service dashboard for Cosmos + SQL
- Configure CI/CD failure alert → Slack/Teams channel
- Auto-remediate Cosmos scale-up via Logic App
- Simulate end-to-end alert → incident ticket → remediation

---

### 📦 Deliverables
- Integrated Dashboard JSON (Cosmos + SQL)
- GitHub Actions pipeline with alert hooks
- Logic App Template for incident response
- Incident Simulation Playbook + Test Results



