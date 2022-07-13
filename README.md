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
    - Create the following databricks nb: 
      - read the transform parquet and the raw csv. 
      - Check whether there are new or updated entries (rows). If there are new or updated rows, append it in the transform parquet. Do not upload records which have not been changed. 
      - Incl a upload timestame as the new meta column in transfom parquet.
- Databricks Notebook from Transform to Serving
  - Collect the transform parquet and store only the newest records in serving


## Task 2: Create a data factory pipeline
- Data Factory: Trigger both notebooks using the data factory: https://docs.microsoft.com/en-us/azure/data-factory/transform-data-using-databricks-notebook
  - adjust nb such that you can provide the main parameters via data factory
- We want to store the meta data in an sql table, like raw path, transform path, serving path in a sql table
  - create the Azure Sql DB: https://docs.microsoft.com/en-us/azure/azure-sql/database/single-database-create-quickstart?view=azuresql&tabs=azure-portal
  - create a sql table [data_lake_control].[data_lake_entity] with id, entity, raw_path, transform_path, serving_path, primary_key
  - Look up the db table with databricks: https://docs.microsoft.com/en-us/azure/data-factory/control-flow-lookup-activity
- For each entry in the database run the notebooks via data factory

## Task 3: Collect and store real data
### Task 3.1: Daily Collection of API data
- Find a suitable api (like weather, yahoo finance, ... --> not too complex)
- Create NB "collect_api_data" which collects the data and stores it in raw --> Folder schema /<source>/<yyyymmdd>/<file_name>
- Create a adf pipline which runs on a daily basis

### Task 3.2: Update or insert into transform
- Create a new sql table - [data_lake_log].[raw_to_transform_state] with columns id, entity, raw_path, load_state
- After data is collect via  NB "collect_api_data" insert a new row in [data_lake_log].[raw_to_transform_state] where
 - id is a counter and just increased by +1 (can be done by sql table automatically)
 - entity (has to be provided)
 - raw_path: path where new data is stored
 - load_state = 'NEW'
- Trigger transform pipeline where all entries from [data_lake_log].[raw_to_transform_state] are porcessed




