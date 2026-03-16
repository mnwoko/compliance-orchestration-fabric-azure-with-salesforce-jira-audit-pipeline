# 🚀 End-to-End Compliance Orchestration: Scaling Salesforce-Jira Identity Audits with Azure ADLS Gen2 and Microsoft Fabric

---
![Azure](https://img.shields.io/badge/Azure-Data%20Lake%20Gen2-blue?style=for-the-badge&logo=microsoft-azure)
![Microsoft Fabric](https://img.shields.io/badge/Microsoft%20Fabric-00A4EF?style=for-the-badge&logo=microsoft&logoColor=white)
![SQL Analytics](https://img.shields.io/badge/SQL-Analytics%20Endpoint-orange?style=for-the-badge)
![Compliance](https://img.shields.io/badge/Compliance-SOC2%20|%20ISO27001-green?style=for-the-badge)

## 📖 Overview
This project implements a scalable, automated **Identity Governance and Administration (IGA)** solution. By orchestrating data between **Salesforce** (Source of Truth) and **Jira** (Operational Access), this engine programmatically identifies "Ghost Users"—individuals who retain access to critical DevOps infrastructure after their corporate identity has been deactivated or terminated.

---
⚡ Key Features
Automated Data Ingestion: Uses Azure Fabric Data Factory to pull from Salesforce (CRM) and Jira (DevOps) REST APIs.
Schema Resilience: T-SQL guardrails to ensure consistent metadata across ingestion cycles.
Audit Archiving: Automatic snapshotting of user states into an audit_history table for SOC2/ISO 27001 compliance.
Risk Detection: SQL-based reconciliation engine to surface unauthorized access in real-time.

---
## 🤖 Automated Workflows
This solution moves beyond manual spreadsheets by automating the entire data lifecycle:
* **⏱️ Scheduled Extraction:** Fabric Pipelines automate OAuth2 handshakes and REST API extraction from global SaaS endpoints.
* **🛡️ Self-Healing Infrastructure:** A T-SQL pre-copy script automatically validates the warehouse schema and adds missing audit columns (e.g., `IdentitySource`) on-the-fly.
* **📈 Elastic Scaling:** Leverages **Parallel Copy** settings and **Azure Data Lake Storage (ADLS) Gen2** staging to prevent write-batch timeouts and handle enterprise-scale user directories.
* **🧠 Programmatic Reconciliation:** T-SQL logic automatically joins disparate datasets to flag "Access Drift" without human intervention.

---

## 🛠️ Core Technologies
* **☁️ Microsoft Fabric:** The unified analytics "SaaS" platform for data residency and compute.
* **🗄️ Azure Data Lake Storage (ADLS) Gen2:** The high-performance staging layer that buffers API responses for reliable ingestion.
* **🌊 Fabric Lakehouse & Warehouse:** Storage layers utilizing **Delta tables** to ensure ACID-compliant identity snapshots.
* **⚙️ SQL Analytics Endpoint:** The distributed T-SQL engine used for cross-platform identity handshakes.

---

## 🏗️ Implementation Architecture

### 1️⃣ Environment Provisioning
The framework is deployed within a Fabric Workspace using a dedicated **`RiskandCompliance`** Warehouse. The data follows a Medallion Architecture (Raw Ingestion ➔ Structured Warehouse ➔ Historical Audit).

### 2️⃣ Schema Resilience (Pre-Copy Script)
To ensure the pipeline never fails due to schema drift, this script runs at the start of every ingestion cycle to verify the `IdentitySource` metadata column:

```sql
/* Automated Schema Validation */
IF NOT EXISTS (SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.audit_history') AND name = 'IdentitySource')
BEGIN
    ALTER TABLE dbo.audit_history ADD IdentitySource VARCHAR(MAX);
END
```
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

✅ Evidence of Success
Final logs confirm a Succeeded status across the entire orchestration chain:

📥 REST API Extraction: Completed successfully without payload drop-offs.

🪣 Azure ADLS Gen2 Staging: Successfully buffered high-volume JSON arrays.

🎯 Warehouse Load: Finalized (Point-in-time snapshot permanently stored in audit_history).
