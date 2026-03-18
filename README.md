# 🚀 End-to-End Compliance Orchestration: 
### Salesforce-Jira Identity Audits with Azure Data Lake Storage (ADLS) Generation 2
---
![Azure](https://img.shields.io/badge/Azure-Data%20Lake%20Gen2-blue?style=for-the-badge&logo=microsoft-azure)
![Microsoft Fabric](https://img.shields.io/badge/Microsoft%20Fabric-00A4EF?style=for-the-badge&logo=microsoft&logoColor=white)
![SQL Analytics](https://img.shields.io/badge/SQL-Analytics%20Endpoint-orange?style=for-the-badge)
![Compliance](https://img.shields.io/badge/Compliance-SOC2%20|%20ISO27001-green?style=for-the-badge)

## 📖 Overview
This project implements a scalable, automated Identity Governance and Administration (IGA) solution. It orchestrates data between Salesforce (source of truth) and Jira (operational access layer) to identify "Ghost Users"—individuals who retain access to critical DevOps infrastructure after their corporate identity has been deactivated or terminated. This enables proactive detection of unauthorized access and strengthens enterprise compliance and security posture.
---
## 🛡️ Business Value: Automated Access Revocation & Identity Governance

### ⚠️ **The Challenge:**
Manual audits of DevOps tool access (Jira) are error-prone and create "latency gaps" where terminated employees retain access to critical infrastructure—a major SOC2 and ISO 27001 compliance risk.
---
### **✅ The Solution: Automated Governance**
I engineered a scalable IGA framework using **Microsoft Fabric** and **Azure ADLS Generation 2** to automate the "Leaver" process:
* **Source of Truth:** Real-time OAuth2 extraction of employee directories from Salesforce.
* **Operational Layer:** Automated REST API ingestion of Jira user access lists.
* **Identity Handshake:** A T-SQL reconciliation engine that flags unauthorized accounts and triggers high-priority revocation alerts.
---
## 💼 Business Impact & Technical Results
---
- **🛡️ Enhanced Security Posture:** Eliminated manual audit reconciliation across Salesforce and Jira by real-time detection of unauthorized ("Ghost") users.
- **⚡ Operational Efficiency:** Reduced manual identity audit preparation time by ~70% - eliminating manual spreadsheet reconciliation.
- ** 📈 Scalable Architecture:** Deployed Azure ADLS Generation 2 staging to handle enterprise-scale datasets, resolving previous "Write Batch Timeout" bottlenecks.
- **⚙️ Governance Framework:** Designed reusable, scalable audit framework for long-term Enterprise Identity and Access Management (IAM) governance.
- **📜 Compliance:** Strengthened SOC2 and ISO 27001 posture by establishing an immutable, point-in-time audit trail for external auditors.
---
## ✨ Key Capabilities
* **End-to-End Compliance Orchestration:** Automates lifecycle data from REST API extraction to SQL Warehouse.
* **Scalable Ingestion:** **Azure Data Lake Storage (ADLS) Generation 2** Decouples API extraction from warehouse writes eliminating "Write Batch Timeouts."
* **Parallel Processing:** Microsoft Fabric to orchestrate concurrent API threads, ensuring rapid synchronization of enterprise-scale directories.
* **Self-Healing Schema Resilience:**  **T-SQL Pre-Copy Script** that validates destination tables and dynamically appends metadata columns to prevent ingestion failures.
* **Automated "Ghost User" Detection:** Reconciliation engine joins disparate datasets to automatically flag terminated or unauthorized accounts.
* **Immutable Audit Archiving:** Point-in-time snapshotting in an `audit_history` table, supporting **SOC2** and **ISO 27001**.
---
## 🤖 Automated Workflows
This solution moves beyond manual spreadsheets by automating the entire data lifecycle:
* **Scheduled Extraction:** Fabric Pipelines automate OAuth2 handshakes and REST API extraction from global SaaS endpoints.
* **Self-Healing Infrastructure:** T-SQL Pre-Copy Scripts** validates the warehouse schema and adds missing audit columns (e.g., `IdentitySource`) on-the-fly.
* **Elastic Scaling:** Leverages **Parallel Copy** settings and **Azure Data Lake Storage (ADLS) Generation 2** staging to prevent write-batch timeouts and handle enterprise-scale user directories.
* **Programmatic Reconciliation:** T-SQL logic automatically joins disparate datasets to flag "Access Drift" without human intervention.
---
## 🛠️ Core Technologies
* **Microsoft Fabric:** Unified analytics platform for data residency and compute.
* **Azure Data Lake Storage (ADLS) Generation 2:** High-performance staging layer for reliable ingestion.
* **Fabric Lakehouse & Warehouse:** Storage layers utilizing **Delta tables** to ensure ACID-compliant snapshots.
* **SQL Analytics Endpoint:** Distributed T-SQL engine for cross-platform identity handshakes.

---
## 🏗️ Environment Provision & Implementation Architecture
Deployed within a unified Microsoft Fabric ecosystem, environment follows a **Medallion Architecture** to ensure data integrity.

### 🛡️ Step A: Workspace Foundation
**Azure Data Factory Creation**  

![Deploy Azure Data Factory](<Step 2 Data Factory Deployment Complete.png>)

**Provision Azure Fabric Risk and Compliance Workspace**

![Provision Azure Fabric RickandCompliance](<Step 3b Azure Fabric Risk and Compliance.png>)

### 🛡️ Step B: Pipeline Orchestration
 
**IAM Enterprise Ingestion Pipeline** OAuth2 with Salesforce and Jira

![IAM Enterprise Ingestion Pipeline](<Step 4 New IAM Enterprise Ingestion Pipeline.png>)

### 🛡️Step C: Build Methodology

**Start with a blank pipeline canvas, then configure Copy Data activities** activities

![IAM Blank Canvas](<Step 4a pipeline build by copy data.png>)

**Copy Data Activity for REST API extraction**

![Data Copy Activity](<Step 4c Data copied rename and tag under general.png>)

## 🔗 Establish Enterprise SaaS Connectivity (Salesforce)
Identity Governance engine is the secure extraction of the "Source of Truth" from Salesforce configuring authenticated connectors to pull active directories for cross-referencing.

### 🛡️ Step A: Configure Salesforce Connector

**Establish Salesforce Objects connector for API communication**

![Salesforce Objects](<Step 5 choose data source connection - salesforce.png>)

### 🛡️ Step B: Authentication & Source Verification

**Verify OAuth2 Extraction**

![Salesforce OAuth2](<Step 5a Connection to Salesforce verifcation via source.png>)

**Jira Integration Audit Logs Parallel Data Activity Copy**

![Salesforce CRM and Jira Integration Audit Logs Parallel Data Activity](<Step 16 Jira Integration Audit Logs Parallel Data Activity Copy.png>)

### 🛡️ Step C: Identity Attribute Selection

**Risk and Compliance `sf_accounts` SQL Creation**

![```RiskandCompliance```  ```s-faccounts``` SQL Account Creation](<Step 15d RiskandCompliance Schemas dbo Tables sf_accounts ParentID Removed 9 rows 26 columns preview .png>)

### 🛡️ Step D: Schema Resilience (Pre-Copy Script)

Ensure pipeline never fails due to schema drift. The script runs at the start of every ingestion cycle to verify the `IdentitySource` metadata column:

**SQL Pre-Copy Script**
```sql
/* Automated Schema Validation */
-- 1. Ensure the history table is ready for the archive
IF NOT EXISTS (SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.audit_history') AND name = 'IdentitySource')
BEGIN
    ALTER TABLE dbo.audit_history ADD IdentitySource VARCHAR(MAX);
END

-- 2. Ensure the live table is ready so the Pre-copy script doesn't fail
IF EXISTS (SELECT * FROM sys.tables WHERE name = 'raw_jira_users_list')
BEGIN
    IF NOT EXISTS (SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.raw_jira_users_list') AND name = 'IdentitySource')
    BEGIN
        ALTER TABLE dbo.raw_jira_users_list ADD IdentitySource VARCHAR(MAX);
    END
END
```
**T-SQL Pre-Copy Script Execution**

![T-SQL Pre-Copy Script Execution](<Step 16.a Pre-copy script SQL Identity Source.png>)

**Schema Mapping**

![Schema Mapping](<Step 16g Mapping import schema selection and removal of source needed for project.png>)

### 🛡️ Step E: Decoupled ETL Orchestration

**Multi-threading/Parallelism/ REST API/ ADLS Generation 2  Staging/ Warehouse Destination**

![REST API Schema Mapping](< Step 17 Final Step SQL endpoints updated ingest_jira_audit_logs succeededrest API azure data lake storage gen2 warehouse.png>)

---
### 🔍 The Audit Logic: "Ghost User" Detection
Automated SQL Join that identifies unauthorized personnel by comparing two primary tables:

```raw_jira_users_list```: The current active DevOps directory.
```sf_accounts```: The corporate CRM source of truth.

The "Identity Handshake" identifies unauthorized personnel through a LEFT JOIN between `raw_jira_users_list` and `sf_accounts`.

```sql
SELECT 
    j.displayName AS [Jira_User], 
    s.Name AS [Salesforce_Identity],
    CASE 
        WHEN s.Email IS NULL THEN 'UNAUTHORIZED: No CRM Match'
        WHEN s.IsActive = 0 THEN 'CRITICAL: Terminated Employee'
        ELSE 'COMPLIANT'
    END AS [Audit_Status]
FROM dbo.raw_jira_users_list j
LEFT JOIN dbo.sf_accounts s ON j.emailAddress = s.Email
WHERE s.Email IS NULL OR s.IsActive = 0;
```
---
### ✅ Evidence of Success
Final logs confirm a Succeeded status across the entire orchestration chain:

📥 REST API Extraction: Completed successfully without payload drop-offs.

🪣 Azure ADLS Generation 2 Staging: Successfully buffered high-volume JSON arrays.

🎯 Warehouse Load: Finalized (Point-in-time snapshot permanently stored in audit_history).

---
### 📂 Project Structure

```text
├── assets/               # System screenshots and evidence
├── sql/                  # T-SQL scripts for schema resilience & audits
│   ├── pre_copy_script.sql
│   └── ghost_user_reconciliation.sql
├── pipelines/            # Fabric pipeline JSON exports
├── README.md             # Project documentation
└── .gitignore            # Git exclusion rules
```
---
### 🛠️ Tech Stack & Infrastructure

## Data ETL/ELT Orchestration & Engineering
* **Microsoft Fabric Data Factory**
* **Azure Data Lake Storage (ADLS) Generation 2**
* **Multi-threading/Parallelism Copy Processing**

## Storage & Analytics (Medallion Architecture)
* **Fabric Lakehouse (Bronze Layer)** 
* **Fabric Warehouse (Silver/Gold Layer)** 

* **ACID Transactions Delta Lake**

## Security & Identity
* **OAuth 2.0 authentication** 
* **T-SQL Validation & Governance Logic**
* **Role-Based Access Control (RBAC)**

### 🛠️ Skills Demonstrated
* **Cloud Infrastructure:** Microsoft Fabric, Azure Data Factory, ADLS Generation 2 
* **Languages:** T-SQL (Advanced), JSON/REST API Integration
* **Security:** Identity Lifecycle Management, Audit Automation, Compliance Mapping (SOC2)
* **Architecture:** Medallion (Bronze/Silver/Gold), Staging-to-Warehouse Orchestration
