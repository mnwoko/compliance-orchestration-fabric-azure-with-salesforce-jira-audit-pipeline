# 🚀 End-to-End Compliance Orchestration: Scaling Salesforce-Jira Identity Audits with Azure Data Lake Storage (ADLS) Generation2 and Microsoft Fabric

---
![Azure](https://img.shields.io/badge/Azure-Data%20Lake%20Gen2-blue?style=for-the-badge&logo=microsoft-azure)
![Microsoft Fabric](https://img.shields.io/badge/Microsoft%20Fabric-00A4EF?style=for-the-badge&logo=microsoft&logoColor=white)
![SQL Analytics](https://img.shields.io/badge/SQL-Analytics%20Endpoint-orange?style=for-the-badge)
![Compliance](https://img.shields.io/badge/Compliance-SOC2%20|%20ISO27001-green?style=for-the-badge)

## 📖 Overview
This project implements a scalable, automated **Identity Governance and Administration (IGA)** solution. By orchestrating data between **Salesforce** (Source of Truth) and **Jira** (Operational Access), this engine programmatically identifies "Ghost Users"—individuals who retain access to critical DevOps infrastructure after their corporate identity has been deactivated or terminated.

---
## ✨ Key Features
* **🔄 End-to-End Compliance Orchestration:** Automates the complete data lifecycle—from secure REST API extraction to final risk reconciliation in the SQL Warehouse.
* **📂 Scalable Ingestion via Azure Data Lake Storage (ADLS) Gen2:** Utilizes **Azure Data Lake Storage Generation 2** as a high-performance staging layer to decouple extraction from the warehouse write process, eliminating "Write Batch Timeouts."
* **⚡ Multi-Threaded Parallel Processing:** Leverages the **Degree of Copy Parallelism** within Microsoft Fabric to orchestrate concurrent API threads, ensuring rapid synchronization of enterprise-scale directories.
* **🛡️ Self-Healing Schema Resilience:** Employs an automated **T-SQL Pre-Copy Script** that validates destination tables and dynamically appends metadata columns to prevent ingestion failures.
* **🔍 Programmatic "Ghost User" Detection:** Features a reconciliation engine that joins disparate datasets to automatically flag terminated or unauthorized accounts.
* **📜 Immutable Audit Archiving:** Implements point-in-time snapshotting in an `audit_history` table, fulfilling **SOC2** and **ISO 27001** "Continuous Monitoring" requirements.
---
## 🤖 Automated Workflows
This solution moves beyond manual spreadsheets by automating the entire data lifecycle:
* **⏱️ Scheduled Extraction:** Fabric Pipelines automate OAuth2 handshakes and REST API extraction from global SaaS endpoints.
* **🛡️ Self-Healing Infrastructure:** A T-SQL pre-copy script automatically validates the warehouse schema and adds missing audit columns (e.g., `IdentitySource`) on-the-fly.
* **📈 Elastic Scaling:** Leverages **Parallel Copy** settings and **Azure Data Lake Storage (ADLS) Gen2** staging to prevent write-batch timeouts and handle enterprise-scale user directories.

* **🧠 Programmatic Reconciliation:** T-SQL logic automatically joins disparate datasets to flag "Access Drift" without human intervention.

---

## 🛠️ Core Technologies
* **☁️ Microsoft Fabric:** Unified analytics platform for data residency and compute.
* **🗄️ Azure Data Lake Storage (ADLS) Gen2:** High-performance staging layer for reliable ingestion.
* **🌊 Fabric Lakehouse & Warehouse:** Storage layers utilizing **Delta tables** to ensure ACID-compliant snapshots.
* **⚙️ SQL Analytics Endpoint:** Distributed T-SQL engine for cross-platform identity handshakes.

---

## 🏗️ Environment Provising & Implementation Architecture
Deployed within a unified Microsoft Fabric ecosystem, the environment follows a **Medallion Architecture** to ensure data integrity.

### 🏢 Step A: Workspace Foundation
The process began by creating an **Azure Data Factory**, followed by provisioning the **"Azure Fabric Risk and Compliance"** workspace.

*Create and Deploy Azure Data Factory*
![Deploy Azure Data Factory](<Step 2 Data Factory Deployment Complete.png>)

*Initialize Microsoft Fabric Workspace within the Azure Portal*
![Initialize Microsoft Fabric](<Step 3 create fabric workspace.png>)

*Provision "Azure Fabric Risk and Compliance" New workspace*
![Provision Azure Fabric RickandCompliance](<Step 3b Azure Fabric Risk and Compliance.png>)

🚀 Step B: Pipeline Orchestration
The IAM_Enterprise_Ingestion_Pipeline serves as the primary engine responsible for secure OAuth2 handshakes with Salesforce and Jira.

IAM Enterprise Ingestion Pipeline
![IAM Enterprise Ingestion Pipeline](<Step 4 New IAM Enterprise Ingestion Pipeline.png>)

🛠️ Step C: Build Methodology
Utilize a "Blank Canvas" approach with **Copy Job** activities to maintain granular control over parallelism.
![IAM Blank Canvas](<Step 4a pipeline build by copy data.png>)

Adding the "Copy Data" activity to facilitate REST API extraction.
![Data Copy Activity](<Step 4c Data copied rename and tag under general.png>)

## 🔗 Establishing Enterprise SaaS Connectivity (Salesforce)
Identity Governance engine is the secure extraction of the "Source of Truth" from Salesforce configuring authenticated connectors to pull active directories for cross-referencing.

🔌 Step A: Configuring the Salesforce Connector
Establish using the **Salesforce Objects** connector for direct API communication.
![Salesforce Objects](<Step 5 choose data source connection - salesforce.png>)

🛡️ Step B: Authentication & Source Verification
Verify via **OAuth2** to extract specific identity attributes (Emails, Names, and Active Status).
![Salesforce OAuth2](<Step 5a Connection to Salesforce verifcation via source.png>)

*Jira Integration Audit Logs Parallel Data Activity Copy*
![Salesforce CRM and Jira Integration Audit Logs Parallel Data Activity](<Step 16 Jira Integration Audit Logs Parallel Data Activity Copy.png>)

## 🛡️ Schema Resilience & Logic

*RiskandCompliance New Query Creation*
![```RiskandCompliance```  ```s-faccounts``` New Query Creation](<Step 15a RiskandCompliance Schemas dbo Tables sf_accounts... (eclispe) new sql query run select to 100_accounts .png>)

*RiskandCompliance sfaccounts SQL Creation*
![```RiskandCompliance```  ```s-faccounts``` SQL Account Creation](<Step 15d RiskandCompliance Schemas dbo Tables sf_accounts ParentID Removed 9 rows 26 columns preview .png>)

### 2️⃣ Schema Resilience (Pre-Copy Script)
To ensure the pipeline never fails due to schema drift, this script runs at the start of every ingestion cycle to verify the `IdentitySource` metadata column:

*T-SQL Pre-Copy Script Execution*
![T-SQL Pre-Copy Script Execution](<Step 16 Jira Integration Audit Logs Parallel Data Activity Copy.png>)

```sql
/* Automated Schema Validation */
IF NOT EXISTS (SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.audit_history') AND name = 'IdentitySource')
BEGIN
    ALTER TABLE dbo.audit_history ADD IdentitySource VARCHAR(MAX);
END
```
*Schema Mapping
![Schema Mapping](<Step 16g Mapping import schema selection and removal of source needed for project.png>)

### 3️⃣ High-Performance Orchestration
*Parallelism/REST API/ Azure Data Lake Storage Gen2 Staging and Azure Warehouse Destination*
![REST API Schema Mapping](< Step 17 Final Step SQL endpoints updated ingest_jira_audit_logs succeededrest API azure data lake storage gen2 warehouse.png>)

---
🔍 The Audit Logic: "Ghost User" Detection
The core value proposition is the automated SQL Join that identifies unauthorized personnel by comparing two primary tables:

```raw_jira_users_list```: The current active DevOps directory.
```sf_accounts```: The corporate HR/CRM source of truth.

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



✅ Evidence of Success
Final logs confirm a Succeeded status across the entire orchestration chain:

📥 REST API Extraction: Completed successfully without payload drop-offs.

🪣 Azure ADLS Gen2 Staging: Successfully buffered high-volume JSON arrays.

🎯 Warehouse Load: Finalized (Point-in-time snapshot permanently stored in audit_history).
