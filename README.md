# Data_Engineering-project
End to End project description
# End-to-End Azure Data Engineering Project

## Project Overview

In this project, we are creating an end-to-end data platform covering **Data Ingestion**, **Data Transformation**, **Data Loading**, and **Reporting**.

![Project Architecture](https://github.com/MithunDataPro/End-to-End-Azure-Data-Engineering-Project/blob/main/Data%20Engineering%20End%20to%20End%20Project%20Architecture.png)

## Tools Covered
1. Azure Data Factory
2. Azure Data Lake Storage Gen2
3. Azure Databricks
4. Azure Synapse Analytics
5. Azure Key Vault
6. Azure Active Directory (AAD)
7. Microsoft Power BI

## Use Case

The use case for this project involves building an end-to-end solution by:
1. Ingesting tables from an on-premise SQL Server database using Azure Data Factory.
2. Storing the data in Azure Data Lake.
3. Using Azure Databricks to transform the raw data.
4. Loading the clean data into Azure Synapse Analytics.
5. Integrating with Microsoft Power BI to build an interactive dashboard.
6. Utilizing Azure Active Directory (AAD) and Azure Key Vault for monitoring and governance.

## Agenda
- **Part 1:** Environment setup
- **Part 2:** Data Ingestion
- **Part 3:** Data Transformation
- **Part 4:** Data Loading
- **Part 5:** Data Reporting
- **Part 6:** End-to-End Pipeline Testing

---

## Part 1: Environment Setup

We start by creating Azure Data Factory and downloading the AdventureWorks DB. Follow the link below to import the database used in this project to SSMS (I used the lightweight version):

[AdventureWorks Installation and Configuration](https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver16&tabs=ssms)

After adding the lightweight version to MSSQL using Microsoft SSMS, we ingested data from this database to Azure Data Lake Storage using Azure Data Factory. Additionally, to connect with Azure, a new user was created in MSSQL with read access.

### SQL Code:

```sql
-- Create a login
CREATE LOGIN mithun WITH PASSWORD = '@Welcome2024#';

-- Create a user in the database and map to the login
USE AdventureWorksLT2017;
CREATE USER Mike FOR LOGIN mithun;

-- Assign roles/permissions
ALTER ROLE db_datareader ADD MEMBER Mike;
ALTER ROLE db_datawriter ADD MEMBER Mike;


