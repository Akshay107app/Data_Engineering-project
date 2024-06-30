
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
```

### Setting up Azure Key Vault

A Key Vault was created in Azure to save the username and password securely for further usage.

---

## Part 2: Data Ingestion

### Steps:

1. **Created Azure Data Factory:**
    - Saved the username & password in Azure Key Vault for encryption.
    - Provided required permissions to the Admin.

2. **Created a Pipeline:**
    - **Lookup Activity:** Created in the source dataset.
    - **Linked Service:** Installed Microsoft Self-hosted Integration Runtime to connect with the on-premises database.
    - Provided the on-premises SQL server name, database name, username & password from the vault, and checked the connections.
    - In the query box, used the following query to get all tables from the database:
    
    ```sql
    SELECT 
      s.name AS SchemaName,
      t.name AS TableName
    FROM sys.tables t
    INNER JOIN sys.schemas s ON t.schema_id = s.schema_id
    WHERE s.name = 'SalesLT';
    ```

3. **Created a Foreach Activity:**
    - Connected Lookup to Foreach.
    - In settings, selected items -> dynamic content -> `@activity('look for all tables').output.value`
    - In the activities -> For Each column, selected edit option and dragged Copy Data activity.
    - Provided source and sink. In the source, used the following query:
    
    ```sql
    @{concat('SELECT * FROM ', item().SchemaName, '.', item().TableName)}
    ```
    
    - In user properties, added new properties SchemaName & TableName and provided values `@item().SchemaName` & `@item().TableName`.

    **Explanation:**
    - This dynamic query constructs the SELECT statement for each table and copies the data.
    - Imported data in Parquet format:
        - **Reason for using Parquet format:** Parquet is efficient for storage and retrieval, supporting advanced compression techniques and efficient encoding schemes.

    - In Parquet tables connection, selected linked service file path:
        - The container created in Data Storage has:
            - **Bronze:** for raw data
            - **Silver:** for semi-transformed data
            - **Gold:** for cleaned data

        - Selecting `bronze / @{concat(dataset().SchemaName, '/', dataset().TableName)} / @{concat(dataset().TableName, '.parquet')}` to automate file extensions.


```



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

---

###Created Azure Data Factory:

Saved the username & password in Azure Key Vault for encryption.
Provided required permissions to the Admin.
Created a Pipeline:

Lookup Activity: Created in the source dataset.
Linked Service: Installed Microsoft Self-hosted Integration Runtime to connect with the on-premises database.
Provided the on-premises SQL server name, database name, username & password from the vault, and checked the connections.
In the query box, used the following query to get all tables from the database:
sql
SELECT 
  s.name AS SchemaName,
  t.name AS TableName
FROM sys.tables t
INNER JOIN sys.schemas s ON t.schema_id = s.schema_id
WHERE s.name = 'SalesLT';


###Created a Foreach Activity:

Connected Lookup to Foreach.
In settings, selected items -> dynamic content -> @activity('look for all tables').output.value
In the activities -> For Each column, selected edit option and dragged Copy Data activity.
Provided source and sink. In the source, used the following query:
@{concat('SELECT * FROM ', item().SchemaName, '.', item().TableName)}
This dynamic query constructs the SELECT statement for each table and copies the data.

##Imported data in Parquet format:

Reason for using Parquet format: Parquet is efficient for storage and retrieval, supporting advanced compression techniques and efficient encoding schemes.
In Parquet tables connection, selected linked service file path:

The container created in Data Storage has:
Bronze: for raw data
Silver: for semi-transformed data
Gold: for cleaned data
Selecting bronze / @{concat(dataset().SchemaName, '/', dataset().TableName)} / @{concat(dataset().TableName, '.parquet')} to automate file extensions.
