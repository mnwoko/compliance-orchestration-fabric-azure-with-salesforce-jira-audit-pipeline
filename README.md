# Compliance-Orchestration-Fabric-Azure-with-Salesforce-Jira-Audit-Pipeline
Description: Automated Identity Governance and Administration (IGA) framework using Microsoft Fabric and Azure ADLS Gen2 to detect "Ghost Users" across Salesforce and Jira.

🚀 Overview
This project implements a scalable, automated Identity Governance and Administration (IGA) solution. By orchestrating data between Salesforce (Source of Truth) and Jira (Operational Access), this engine programmatically identifies "Ghost Users"—individuals who retain access to critical DevOps infrastructure after their corporate identity has been deactivated or terminated.

🤖 Automated Workflows
This solution moves beyond manual audits by automating the entire data lifecycle:
Scheduled Extraction: Fabric Pipelines automate OAuth2 handshakes and REST API extraction from global SaaS endpoints.
Self-Healing Infrastructure: A T-SQL pre-copy script automatically validates the warehouse schema and adds missing audit columns (e.g., IdentitySource) on-the-fly.
Elastic Scaling: Leverages Parallel Copy settings and Azure Data Lake Storage (ADLS) Gen2 staging to prevent write-batch timeouts and handle enterprise-scale user directories.
Programmatic Reconciliation: T-SQL logic automatically joins disparate datasets to flag "Access Drift" without human intervention.

🛠️ Core Technologies
Microsoft Fabric: The unified analytics "SaaS" platform for data residency and compute.
Azure Data Lake Storage (ADLS) Gen2: The high-performance staging layer that buffers API responses for reliable ingestion.
Fabric Lakehouse & Warehouse: Storage layers utilizing Delta tables to ensure ACID-compliant identity snapshots.
SQL Analytics Endpoint: The distributed T-SQL engine used for cross-platform identity handshakes.

⚙️ Implementation Architecture
1. Environment Provisioning
The framework is deployed within a Fabric Workspace using a dedicated RiskandCompliance Warehouse. The data follows a Medallion Architecture (Raw Ingestion ➔ Structured Warehouse ➔ Historical Audit).

2. Schema Resilience (Pre-Copy Script)
To ensure the pipeline never fails due to schema drift, this script runs at the start of every ingestion cycle to verify the IdentitySource metadata column:

