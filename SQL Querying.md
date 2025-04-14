# 🎯 Deep SQL Querying – Mastery Learning Path

### 🧠 Goal:
Become a power user of SQL by mastering advanced querying techniques, execution plan analysis, optimization patterns, and analytical functions across enterprise-grade databases (SQL Server, PostgreSQL, Azure SQL, etc.).

---

## 📅 Phase 1 – Core Querying Foundations (Week 1–2)

### ✅ Topics:
- SELECT, WHERE, GROUP BY, ORDER BY (with indexing impact)
- Joins: INNER, LEFT, RIGHT, FULL
- Subqueries: Correlated, Scalar, IN/EXISTS
- Set operators: UNION, INTERSECT, EXCEPT
- Null-safe logic, CASE statements

### 🧪 Lab Tasks:
- Multi-join sales dashboard queries
- Build pivot-style queries with CASE and GROUP BY
- Refactor correlated vs non-correlated subqueries

---

## 📅 Phase 2 – Analytical & Window Functions (Week 3–4)

### ✅ Topics:
- ROW_NUMBER(), RANK(), DENSE_RANK(), NTILE()
- LAG(), LEAD(), FIRST_VALUE(), LAST_VALUE()
- OVER() partitions and frames (RANGE vs ROWS)
- Advanced aggregates and running totals

### 🧪 Lab Tasks:
- Build cohort analysis report
- Create real-time ranking dashboards
- Implement moving averages and time windows

---

## 📅 Phase 3 – Query Optimization & Execution Plans (Week 5–6)

### ✅ Topics:
- Reading execution plans (EST + ACT)
- Seek vs Scan, Loop vs Hash Join
- Index usage, covering indexes, key lookups
- SARGability, statistics, parameter sniffing
- Query Store, sys.dm_exec_* views

### 🧪 Lab Tasks:
- Optimize slow multi-join query
- Rewrite filters to be SARGable
- Compare estimated vs actual cost

---

## 📅 Phase 4 – Advanced Filtering & Aggregations (Week 7–8)

### ✅ Topics:
- CTEs vs derived tables vs temp tables
- GROUPING SETS, CUBE, ROLLUP
- Conditional aggregation
- PIVOT / UNPIVOT

### 🧪 Lab Tasks:
- Multi-level aggregate with GROUPING SETS
- Build sales summary cube report
- Write dynamic pivot query for UI grids

---

## 📅 Phase 5 – Temporal & Hierarchical Queries (Week 9–10)

### ✅ Topics:
- Recursive CTEs
- Adjacency list vs path enumeration
- Temporal tables
- Gaps & islands problems

### 🧪 Lab Tasks:
- Parse and flatten hierarchy to tree
- Build org chart with recursive logic
- Fill missing dates for time series

---

## 📅 Phase 6 – Security-Aware Querying & Row Filtering (Week 11)

### ✅ Topics:
- Row-level security
- Dynamic data masking
- Data classification filtering
- Access-controlled views

### 🧪 Lab Tasks:
- Build RLS policy by tenant ID
- Mask email and PII fields in SELECT
- Write audit-friendly views with metadata

---

## 📅 Phase 7 – Real-Time & Streaming SQL (Week 12)

### ✅ Topics:
- T-SQL and PostgreSQL streaming extensions
- CDC and change tracking queries
- Materialized views with refresh
- Time-windowed aggregations

### 🧪 Lab Tasks:
- Real-time event summary using Stream Analytics SQL
- Build low-latency leaderboard with window joins

---

## 📦 Final Capstone Project
- Design and implement a reporting engine with:
  - Multi-level aggregation views
  - RLS for tenant-level access
  - Query tuning and benchmark report
  - Streaming change audit table

---

Let me know if you want hands-on exercises, SQL code packs, or dataset recommendations next.

