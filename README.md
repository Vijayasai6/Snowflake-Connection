## Snowflake ↔ Azure Data Lake Storage Gen2 (ADLS Gen2) Integration via Azure Data Factory
## Overview
This project documents the end-to-end process for securely moving data between **Snowflake**, **Azure Data Lake Storage Gen2 (ADLS Gen2)**, and **Azure Blob Storage** leveraging **Azure Data Factory (ADF)**. It covers the required Azure resources, pipeline configurations, best practices, and error handling.

## Features
-Data movement from **Snowflake to ADLS Gen2** and vice-versa
- Direct movement between **Snowflake and Blob Storage** (in both directions)
- Use of **Blob Storage as a staging layer** for Snowflake ↔ ADLS Gen2 processes
- End-to-end **pipelining using Azure Data Factory**
- Secure management of credentials and secrets with **Azure Key Vault**
- Sample master pipeline chaining all scenarios

*Blob Storage acts as a mandatory staging layer for data transfers between Snowflake and ADLS Gen2.*
- *Transfers between Blob Storage and Snowflake can often be direct.*

## Data Movement Scenarios

| Source                | Destination      | Staging Layer Needed | Format          |
|-----------------------|------------------|---------------------|-----------------|
| Snowflake             | ADLS Gen2        | Yes (Blob Storage)  | CSV             |
| ADLS Gen2             | Snowflake        | Yes (Blob Storage)  | CSV             |
| Blob Storage          | Snowflake        | No*                 | CSV (no header) |
| Snowflake             | Blob Storage     | No*                 | CSV             |

*\*Direct if not connecting to ADLS Gen2*

## Step-by-Step Setup

### 1. **Azure Resource Provisioning**
- **Resource Group**
- **ADLS Gen2 Storage Account**
- **Blob Storage Account** (for staging/intermediate layer)
- **Azure Data Factory**
- **Azure Key Vault** (for secrets management)

### 2. **Store Credentials in Key Vault**
- Snowflake username/password
- ADLS Gen2 storage account key
- Blob Storage SAS token

### 3. **ADF Linked Services**
- Key Vault Linked Service
- Blob Storage Linked Service (auth using Key Vault/SAS)
- ADLS Gen2 Linked Service (auth using Key Vault/account key)
- Snowflake Linked Service: account, region, username, password, database, warehouse

### 4. **Pipeline Building in ADF**
- Use Copy Activities for each scenario
- **Enable the staging layer** (via Blob Storage) for Snowflake ↔ ADLS Gen2
- **Remove headers** from CSVs if loading from Blob to Snowflake
- Create datasets for each source/sink

### 5. **Master Pipeline**
- Chain individual pipelines using **Execute Pipeline** activities
- Add triggers to automate schedules as needed

## Best Practices
- Store all sensitive information in **Key Vault**
- Activate staging when transferring between **Snowflake and ADLS Gen2**
- Always test connections for each Linked Service in ADF
- Remove headers when loading from Blob to Snowflake
- Monitor pipeline execution and check row counts before/after transfers

## Troubleshooting
- Most errors relate to missing/incorrect staging layer configuration, secrets, or Linked Service settings
- Verify storage paths and credentials
- Check logs and use "Debug" in ADF for step-by-step error tracing


