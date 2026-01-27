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