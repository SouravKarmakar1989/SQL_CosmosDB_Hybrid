# 📅 Week 3 – Security, Multi-Region Design & Disaster Recovery (Cosmos DB + SQL)

🧠 **Learning Goal**: Understand how to secure Cosmos DB and SQL systems, design for multi-region resiliency, and meet enterprise RTO/RPO SLAs.

🕒 **Daily Time Budget**: 2–3 hours

---

## 🗓 Day 1: Cosmos DB – Multi-Region Design & Failover Strategies

### 🎯 Objectives
- Learn Cosmos DB global distribution models (single-master, multi-master)
- Configure multi-region writes and automatic failover
- Evaluate consistency models and regional latency impact

---

### 🔍 Key Concepts
- **Global Distribution**: Multi-region deployments with write and read replicas
- **Automatic Failover**: Configurable region priorities for recovery
- **Write Modes**:
  - **Single-Write Region**: Simpler, cheaper, ideal for centralized workloads
  - **Multi-Write Region**: High availability and low write latency (requires conflict resolution)
- **Consistency Levels**: Strong, Bounded Staleness, Session, Consistent Prefix, Eventual
- **Latency vs Consistency Trade-offs**

---

### 🔧 Hands-On
- Create Cosmos DB with East US and West Europe regions
- Enable multi-region writes and define failover priorities
- Run latency tests across consistency levels using SDK
- Simulate region failover and observe traffic redirection

```bash
az cosmosdb create \
  --name myGeoCosmos \
  --resource-group myRG \
  --locations regionName=eastus failoverPriority=0 isZoneRedundant=false \
  --locations regionName=westeurope failoverPriority=1 isZoneRedundant=false \
  --enable-multiple-write-locations true
```

---

### 🧠 Deep Dive: Consistency Model Scenarios
| Consistency        | Use Case                        | Write Latency | Read Latency | Availability |
|--------------------|----------------------------------|----------------|---------------|--------------|
| Strong             | Financial systems                | High           | Medium        | Medium       |
| Bounded Staleness  | Voting apps, collaborative tools | Medium         | Medium        | High         |
| Session            | User profile APIs                | Low            | Low           | High         |
| Consistent Prefix  | Social feeds                     | Very Low       | Low           | Very High    |
| Eventual           | IoT telemetry, logs              | Lowest         | Lowest        | Highest      |

---

### 🧪 Mini-Lab
- Deploy a Cosmos DB account with 2+ regions
- Insert documents via SDK and observe consistency guarantees
- Induce failover and measure recovery behavior

---

### ✅ Practice Tasks
- Create ARM template for global Cosmos setup
- Write a script to query with each consistency level
- Record latency metrics with `CosmosDiagnostics`
- Design a failover test scenario using Azure CLI

---

### 📦 Deliverables
- Geo-Distributed Cosmos ARM Template
- SDK script to toggle/read consistency levels
- Region latency + failover report (per consistency)
- Failover strategy decision matrix



---

## 🗓 Day 2: SQL – High Availability, Replication & Auto-Failover Groups

### 🎯 Objectives
- Implement high availability using Always On Availability Groups or Auto-Failover Groups
- Understand SQL Server vs Azure SQL HA options
- Design for regional failover, quorum, and traffic redirection

---

### 🔍 Key Concepts
- **HA Topologies**:
  - On-prem: Always On AG, Windows Failover Clustering
  - Azure SQL Managed Instance: Auto-failover groups
  - Azure SQL Database: Geo-replication, zone redundancy
- **Replication Types**:
  - Synchronous (low latency, zero data loss)
  - Asynchronous (cross-region, higher latency)
- **Failover Behavior**:
  - Planned (manual switchover)
  - Unplanned (auto failover on heartbeat failure)
- **Listener Endpoints & DNS Resolution**
- **Quorum Voting & Witness Node (for AGs)**

---

### 🔧 Hands-On
- Configure Auto-Failover Group in Azure SQL
- Deploy 2-region setup: Primary (East US), Secondary (West US)
- Set failover policy, connection strings, and failover grace period
- Simulate a regional failure and test auto-routing

```sql
-- Configure failover group
ALTER DATABASE MyDb
MODIFY (SERVICE_OBJECTIVE = 'S1');

-- Add secondary
-- via Azure Portal or PowerShell/Azure CLI
```

---

### 🧠 Deep Dive: Resiliency Design Patterns
| Platform            | Primary Method             | Sync | Auto Failover | Notes                          |
|---------------------|-----------------------------|------|----------------|-------------------------------|
| SQL Server (VM/IaaS)| Always On AG + WSFC        | Yes  | Manual/Planned | Full control, more config     |
| Azure SQL DB        | Auto-Failover Group         | Async| Yes            | Portal/CLI support             |
| SQL MI              | Auto-Failover Group         | Async| Yes            | Supports listener endpoint     |

---

### 🧪 Mini-Lab
- Deploy SQL database in primary + geo-secondary region
- Trigger failover manually + via Azure simulated outage
- Monitor connection redirection and query retry behavior

---

### ✅ Practice Tasks
- Use Azure CLI or Portal to configure AFG
- Validate DNS-based failover endpoint
- Configure retry policy in application connection string
- Document failover time, client impact, and recovery checklist

---

### 📦 Deliverables
- SQL HA + AFG configuration scripts
- Regional failover test report
- Connection string retry strategy template
- Resiliency design decision matrix



---

## 🗓 Day 3: Cosmos DB – Conflict Resolution & Multi-Write Behavior

### 🎯 Objectives
- Understand how Cosmos DB handles conflicts in multi-write mode
- Configure conflict resolution policies (Last-Writer-Wins, custom logic)
- Explore how change feed and conflict feed support reconciliation

---

### 🔍 Key Concepts
- **Multi-Write Region Mode**:
  - Enables write operations in any region
  - Potential for write conflicts if same document is updated simultaneously
- **Conflict Resolution**:
  - **Last-Writer-Wins (LWW)**: Based on a timestamp field (default)
  - **Custom Resolution**: Trigger logic apps/functions to resolve conflict
  - **Conflict Feed**: Special API to consume conflicting records
- **Change Feed + Conflict Feed**:
  - Change feed emits committed changes
  - Conflict feed shows conflicts before they’re resolved or discarded

---

### 🔧 Hands-On
- Enable multi-region writes on a Cosmos DB account
- Create and modify documents simultaneously in two regions
- Define conflict resolution policy via ARM or CLI
- Consume conflict feed with .NET or Node SDK and log payloads

```json
"conflictResolutionPolicy": {
  "mode": "LastWriterWins",
  "conflictResolutionPath": "/updatedAt"
}
```

---

### 🧠 Deep Dive: Conflict Scenarios
| Scenario                          | Default Handling      | Best Practice                        |
|----------------------------------|------------------------|--------------------------------------|
| Simultaneous writes (same doc)   | LWW timestamp wins     | Use RFC3339 timestamp + UTC sync     |
| Logical merge needed             | Conflict Feed + Custom | Trigger Azure Function to resolve    |
| Conflict overflow / lost write   | Discard                | Use app-side merge or queue fallback |

---

### 🧪 Mini-Lab
- Write two concurrent updates to the same document from West Europe and East US
- Observe winner via conflict feed
- Trigger a function on conflict event to log or resolve

---

### ✅ Practice Tasks
- Enable and test LWW policy
- Switch to custom conflict resolution handler
- Build retry logic in SDK to verify update commitment
- Log change feed vs conflict feed behavior

---

### 📦 Deliverables
- Conflict policy ARM template (LWW + Custom)
- SDK conflict resolution example (Function + Feed Reader)
- Conflict event log with timestamps and resolution notes
- Conflict handling decision matrix (use case based)



---

## 🗓 Day 4: SQL – Backup, Restore & Disaster Recovery

### 🎯 Objectives
- Design SQL Server and Azure SQL backup & restore strategies
- Meet business RTO/RPO targets using geo-redundant options
- Validate backup integrity and automate recovery playbooks

---

### 🔍 Key Concepts
- **Backup Types**:
  - Full: entire DB snapshot
  - Differential: changes since last full
  - Transaction Log: point-in-time recovery (PITR)
  - Copy-Only + Snapshot (VM/Storage-based)
- **Restore Methods**:
  - Restore to point-in-time
  - Geo-restore from paired region
  - Auto-restore with long-term retention (LTR)
- **Disaster Recovery Patterns**:
  - Paired region backup copy
  - Automated PITR workflows
  - Immutable backups (storage-level)

---

### 🔧 Hands-On
- Configure backup retention in Azure SQL (7–35 days or LTR up to 10 years)
- Perform point-in-time restore (PITR) to test recovery
- Enable geo-restore on deleted database from backup vault

```sql
-- Check available restore points
SELECT * FROM sys.dm_database_restore_status;
```

---

### 🧠 Deep Dive: RPO/RTO Mapping to Recovery Strategy
| Use Case                | Backup Type       | Restore Scope   | RTO Target | RPO Target |
|-------------------------|-------------------|------------------|------------|------------|
| Transactional app       | Full + Log Backup | PITR             | <15 min    | <5 min     |
| Reporting warehouse     | Daily snapshot    | Daily copy       | <1 hr      | 24 hr      |
| Long-term compliance    | Weekly + LTR      | 7+ year archive  | Hours      | Weekly     |

---

### 🧪 Mini-Lab
- Backup SQL DB (full + log)
- Simulate outage by deleting DB or region
- Restore from backup and validate business continuity

---

### ✅ Practice Tasks
- Define backup schedule and policies for 2 workloads
- Trigger PITR from portal and log duration
- Test LTR recovery and compare to recent backup
- Document SLA compliance metrics

---

### 📦 Deliverables
- PITR runbook with CLI + Portal options
- Backup strategy matrix (hot, cold, compliance workloads)
- Restore validation log (time, data integrity)
- Backup/DR automation script (PowerShell or Bicep)



---

## 🗓 Day 5: Cosmos DB – Security, RBAC, Network Isolation

### 🎯 Objectives
- Apply enterprise-grade access control to Cosmos DB
- Implement role-based access control (RBAC) and private networking
- Secure data at rest and in transit using encryption + identity-based access

---

### 🔍 Key Concepts
- **Authentication Options**:
  - Primary/secondary keys (not recommended for prod)
  - Azure AD with RBAC (preferred)
  - Managed Identity for app access
- **Authorization**:
  - Built-in roles (Cosmos DB Account Reader, Data Reader/Writer)
  - Custom roles with granular scope (per DB/collection/action)
- **Network Security**:
  - Private Endpoints (disable public access)
  - VNET integration (with NSG and route filters)
  - IP Firewall rules (legacy, less secure)
- **Encryption**:
  - Encryption at rest (default)
  - Customer-managed keys (CMK) with Azure Key Vault

---

### 🔧 Hands-On
- Assign built-in Reader role via Azure AD to a user/service principal
- Create custom role definition for limited write access to a container
- Enable private endpoint in Cosmos DB and restrict access from VNET
- Integrate with Key Vault for CMK configuration

```bash
az cosmosdb update \
  --name mySecureCosmos \
  --resource-group myRG \
  --enable-virtual-network true \
  --virtual-network-rules "subnet-id"
```

---

### 🧠 Deep Dive: Access Strategy Matrix
| Use Case               | Identity Type     | Recommended Access    | Notes                            |
|------------------------|-------------------|------------------------|----------------------------------|
| Web App (Prod)         | Managed Identity  | Cosmos DB Data Reader | Secure, auto-rotated identity    |
| Analyst (Read Only)    | Azure AD User     | Data Reader Role       | No write or metadata access      |
| Microservice (Write)   | SPN / MSI         | Custom Role            | Write to container, block schema |

---

### 🧪 Mini-Lab
- Configure Cosmos DB with private endpoint + deny public access
- Assign RBAC to two identities and test scoped access
- Trigger fail access log when non-RBAC identity queries the DB

---

### ✅ Practice Tasks
- Apply RBAC at container level using Azure CLI
- Configure CMK via Key Vault and rotate key
- Validate IP Firewall vs VNET restrictions
- Build policy enforcement using Azure Policy + Defender

---

### 📦 Deliverables
- Cosmos DB RBAC Role Assignment Scripts
- Private Endpoint + NSG Policy Config
- Access Test Matrix (Identity → Resource Mapping)
- CMK Configuration Template + Rotation Plan



---

## 🗓 Day 6: SQL – RBAC, Encryption, Threat Protection

### 🎯 Objectives
- Secure Azure SQL and SQL Server with RBAC, encryption, and auditing
- Implement least privilege access, managed identities, and encryption-at-rest
- Enable threat detection and integrate with Azure Defender

---

### 🔍 Key Concepts
- **Authentication**:
  - SQL Auth (user/pass – legacy, fallback only)
  - Azure AD Auth (integrated, recommended)
  - Managed Identity (for apps + automation)
- **Authorization**:
  - Roles: db_datareader, db_datawriter, db_owner
  - Custom roles + stored proc access control
- **Encryption**:
  - Transparent Data Encryption (TDE): enabled by default
  - Always Encrypted: column-level encryption (client-side keys)
  - TLS for in-transit protection
- **Threat Protection**:
  - Azure Defender for SQL: alerts, vulnerability assessments
  - Auditing: Log Analytics, Event Hub, Storage
  - SQL Data Discovery & Classification

---

### 🔧 Hands-On
- Enable Azure AD login and assign db_datareader to user
- Set up Always Encrypted for SSN column with Key Vault integration
- Enable Azure Defender and simulate a SQL injection alert
- Route audit logs to Log Analytics for query inspection

```sql
-- Enable TDE (default)
ALTER DATABASE MyDB SET ENCRYPTION ON;
```

---

### 🧠 Deep Dive: Access Model & Encryption Matrix
| Use Case           | Identity          | Role / Control     | Encryption         |
|--------------------|-------------------|--------------------|--------------------|
| App connection     | Managed Identity  | db_datawriter      | TDE (default)      |
| Analyst access     | Azure AD User     | db_datareader      | TDE + DDC          |
| Finance data       | Azure AD + SPN    | Custom Schema Role | Always Encrypted   |

---

### 🧪 Mini-Lab
- Query audit logs for suspicious patterns
- Classify sensitive data using Data Discovery
- Force TLS-only connections and validate
- Rotate column encryption key using Key Vault

---

### ✅ Practice Tasks
- Set up RBAC and login policy with AD integration
- Configure audit and threat policies with action groups
- Apply Always Encrypted to 2+ sensitive fields
- Generate and export security posture report

---

### 📦 Deliverables
- Role assignment scripts (SQL + Azure)
- Always Encrypted config with CMK setup
- Defender alert summary with sample attack detection
- Auditing dashboard in Log Analytics



---

## 🗓 Day 7: Architecture Review + Security & Resiliency Scenarios

### 🎯 Objectives
- Consolidate all Week 3 learnings into enterprise-ready architectural decisions
- Evaluate security and disaster recovery posture across Cosmos DB and SQL
- Simulate real-world incident scenarios and response planning

---

### 🔍 Key Concepts
- **Security Architecture Layers**:
  - Identity + Access (RBAC, Azure AD, Managed Identity)
  - Network Isolation (Private Endpoints, VNETs, NSGs)
  - Data Protection (TDE, Always Encrypted, CMK)
  - Auditing & Monitoring (Defender, Azure Monitor, Log Analytics)

- **Resiliency Patterns**:
  - Multi-region replication and failover (Cosmos, SQL AFG)
  - PITR, LTR, and geo-restore
  - Self-healing triggers and budget-based alerting
  
- **Compliance & SLA Alignment**:
  - RPO/RTO matrix for different workloads
  - Data classification and retention policies

---

### 🔧 Hands-On
- Create architecture diagrams for Cosmos + SQL HA & security layers
- Run simulation: Cosmos region failure with multi-write enabled
- Run simulation: SQL failover group outage with alert + recovery
- Review posture with Azure Security Center + Defender reports

---

### 🧠 Deep Dive: Resiliency Playbook Template
| Component         | Design              | Response Strategy          |
|------------------|----------------------|----------------------------|
| Cosmos DB        | Multi-region writes  | Conflict feed + app retry |
| Azure SQL        | Auto-failover group  | Retry + read routing      |
| Audit & Alerting | Log Analytics, KQL   | Email + Logic App webhook |

---

### 🧪 Mini-Lab
- Perform a DR drill and time failover execution
- Trigger a simulated data access breach and monitor alert chain
- Validate user RBAC scoping through role abuse testing

---

### ✅ Practice Tasks
- Define an SLA-backed architecture plan for Cosmos + SQL
- Generate unified security scorecard (Azure Policy, Defender)
- Build runbook: alert → incident ticket → recovery steps
- Design fault injection tests for readiness validation

---

### 📦 Deliverables
- Cosmos + SQL Resiliency Architecture Diagram
- DR/Failover Test Report + Latency Logs
- RBAC & Threat Model Review Sheet
- Final Week 3 Summary: Security, DR, Observability Readiness



