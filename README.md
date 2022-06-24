# AZURE DATA Lake Tutorial

## GOAL 
Creation of a data lake in Azure using
- Storage Accounts
- Databricks
- Data Factory
- Key Vault

## Task 0: Setup
Create the following resources:
- Create Data Lake Storage: 
  - Create Storage Account: https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal
  - Inside the storage account create 3 container: raw, transform, serving https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-portal
  - Create and upload a csv file in raw
- Create a data factory: 
  - Initial Tuorial: copy the csv file to a new location https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal
  - Link the git repo to data factory: https://docs.microsoft.com/en-us/azure/data-factory/source-control
- Create Databricks Cluster (SINGLE NODE): 
  - Link git repo and create databricks folder https://docs.microsoft.com/en-us/azure/databricks/repos/
  - Access 
  - Read the CSV file from storage account and store it as parquet:
    - Link Databricks and Storage account https://docs.microsoft.com/en-us/azure/databricks/scenarios/store-secrets-azure-key-vault
    **DO NOT COMMIT CONNECTION STRINGS**: Store them in the key vault


## Task 1: Manage Data Lake with Databricks
**STARTING NOW:** Create a feature branch for each task. After the task is finish, create a Pull Request and present it in a Code Review. 
- Databricks Notebook from Raw to Transform
  - Step 1: NB which collect the csv file and stores it as parquet in transform
    - What is the main difference between csv and parquet?
  - Step 2: Now we want to upload multiple csv file and check whether the entries are new or have to be updated. 
    - Define a primary key in csv. No actual todo just ensure that your csv has a column with unique values. **SIMPLIFICATION**: use fixed naming in the notebook. hence, if you update the raw csv, use the same name. 
    - Create the following databricks nb: read the transform parquet and the raw parquet. Check whether there are new or updated columns. If there are new or updated, append it in transform. Do not upload records which have not changed. Incl a upload timestame in the meta column.
- Databricks Notebook from Transform to Serving
  - Collect the transform parquet and store only the newest records in serving


## Task 2: Create a data factory pipeline
- Data Factory: Trigger both notebooks using the data factory: https://docs.microsoft.com/en-us/azure/data-factory/transform-data-using-databricks-notebook
  - adjust nb such that you can provide the main parameters via data factory
- We want to store the meta data in an sql table, like raw path, transform path, serving path in a sql table
  - create the Azure Sql DB: https://docs.microsoft.com/en-us/azure/azure-sql/database/single-database-create-quickstart?view=azuresql&tabs=azure-portal
  - Look up the dql table with databricks: https://docs.microsoft.com/en-us/azure/data-factory/control-flow-lookup-activity
- For each entry in the database run the notebooks via data factory




