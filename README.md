# Formula 1 Data Engineering & Analytics Pipeline

This project implements an end-to-end data engineering pipeline for Formula 1 data using Azure Data Lake Storage and Databricks.
It follows a Bronze → Silver → Gold architecture and supports incremental data loading using business keys.

This project demonstrates how large-scale sports data can be processed using modern data engineering practices.
Raw Formula 1 race data is ingested, cleaned, transformed, and stored in analytics-ready tables to enable meaningful insights and reporting.

## Dataset
The dataset used in this project is publicly available Formula 1 data.
Source: https://ergast.com/mrd/

## Incremental Data Ingestion

Raw Formula 1 data is stored in Azure Data Lake using date-based folders.
Each folder represents a single ingestion cycle.

raw/
├── 2021-03-21/
│   ├── results.json
│   ├── lap_times/
│   ├── qualifying/
├── 2021-03-28/
├── 2021-04-18/

During incremental loads, processed and presentation layer tables are stored using Delta Lake.
Both table metadata and underlying data files are maintained in Delta format to ensure ACID compliance,
schema enforcement, and support for incremental processing.

### Initial Load and Incremental Loads

The raw data is organized in date-based folders in Azure Data Lake.

- **2021-03-21**  
  This folder contains historical Formula 1 data and was processed using a **full load** to initialize the pipeline.

- **2021-03-28** and **2021-04-18**  
  These folders contain newly arrived data and were processed using **incremental loads**.  
  Only new records were ingested while preserving previously loaded data.

Tech Stack:
- Azure Databricks
- PySpark
- Spark SQL
- Delta Lake
- Azure Data Lake Storage

Databases Created:
f1_raw – stores raw ingested data
f1_processed – stores cleaned and standardized data
f1_presentation – stores analytics-ready tables

Data Pipeline Architecture:
1. Bronze Layer (Raw)
   - Ingests raw JSON files from ADLS
   - Stores data in Parquet format
   - Data is ingested date-wise using file_date
2. Silver Layer (Processed)
   - Cleans and standardizes data
   - Applies schema enforcement
   - Uses incremental processing
   - Avoids overwrites and duplicates
   - Data stored in Delta format during incremental loads.
3. Gold Layer (Presentation)
   - Analytics-ready tables
   - Incremental loads using Delta MERGE
   - Designed for dashboards and reporting

### Azure Data Factory Orchestration

Azure Data Factory (ADF) is used to orchestrate the execution of Databricks notebooks and manage end-to-end pipeline workflows.

#### Pipelines Implemented

1. pl_ingest_formula1_data
   - Orchestrates ingestion of raw Formula 1 data into the Raw (Bronze) layer
   - Passes file_date as a parameter to Databricks notebooks
   - Handles historical and incremental loads using date-based folders
2. pl_process_formula1_data
   - Executes Databricks notebooks for transforming raw data into the Processed (Silver) layer
   - Applies schema enforcement and incremental logic
   - Ensures idempotent execution by avoiding duplicate records
3. pl_presentation_formula1_data
   - Builds analytics-ready tables in the Presentation (Gold) layer
   - Uses Delta Lake MERGE operations for incremental updates
   - Produces fact and aggregate tables used for reporting and dashboards

#### Triggers

Tumbling Window Trigger: Pipelines are orchestrated using an Azure Data Factory Tumbling Window Trigger, which runs at fixed time intervals and ensures exactly-once processing for each time window.
This trigger design is well-suited for incremental data ingestion, as it guarantees:
   - No overlapping pipeline executions
   - Reliable processing of each ingestion window
   - Automatic handling of late or missing data windows

#### ADF–Databricks Integration

   - Azure Data Factory is connected to Azure Databricks using a managed identity
   - Notebook execution is parameterized using file_date
   - Pipelines are version-controlled using GitHub integration
   - ARM templates are generated automatically in the adf_publish branch for deployment

### Unity Catalog and Data Governance

This project leverages Databricks Unity Catalog to implement centralized data governance, fine-grained access control, and secure data access.
   - All databases and tables (f1_raw, f1_processed, f1_presentation) are managed under Unity Catalog.
   - Access to schemas and tables is controlled using role-based permissions (SELECT, MODIFY, USE SCHEMA).
   - External Azure Data Lake paths are registered as External Locations and accessed securely using managed identity.
   - Unity Catalog ensures: controlled read/write access across pipeline layers, secure access to cloud storage without exposing credentials, clear separation of       ingestion, transformation, and analytics responsibilities.
   - Permission enforcement prevents unauthorized reads or writes, simulating real-world enterprise data governance.

Key Insights:
- Each load is driven by a file_date parameter
- Business keys are used to prevent duplicates
- Tables are created once and updated incrementally
- Event tables (results, race_results) store row-level data
- Standings tables store snapshots per processing date
- This allows analysis of standings over time

Key Learnings:
- Difference between append, overwrite, and merge
- Importance of business keys in incremental pipelines
- Snapshot vs cumulative data modeling
- Designing re-runnable and idempotent pipelines
- Practical experience with Delta Lake for incremental data storage at both file and table levels.

Future Enhancements:
- Add constructor and driver performance trends
- Create views for latest standings
- Integrate Power BI / Tableau dashboards
- Add data quality checks