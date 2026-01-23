# Formula 1 Data Engineering & Analytics Pipeline
This project implements an end-to-end data engineering pipeline for Formula 1 data using Azure Data Lake Storage and Databricks.
It follows a Bronze → Silver → Gold architecture and supports incremental data loading using business keys.

This project demonstrates how large-scale sports data can be processed using modern data engineering practices.
Raw Formula 1 race data is ingested, cleaned, transformed, and stored in analytics-ready tables to enable meaningful insights and reporting.

## Dataset
The dataset used in this project is publicly available Formula 1 data.
Source: https://ergast.com/mrd/

Tech Stack:
- Azure Databricks
- PySpark
- Spark SQL
- Delta Lake
- Azure Data Lake Storage

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
3. Gold Layer (Presentation)
   - Analytics-ready tables
   - Incremental loads using Delta MERGE
   - Designed for dashboards and reporting

Key Insights:
- Each load is driven by a file_date parameter
- Business keys are used to prevent duplicates
- Tables are created once and updated incrementally
- Example:
| Table                 | Business Key                        | Load Type   |
| --------------------- | ----------------------------------- | ----------- |
| results               | race_id + driver_id                 | Incremental |
| race_results          | race_id + driver_name               | Incremental |
| driver_standings      | race_year + driver_name + file_date | Snapshot    |
| constructor_standings | race_year + team + file_date        | Snapshot    |
- Event tables (results, race_results) store row-level data
- Standings tables store snapshots per processing date
- This allows analysis of standings over time

Key Learnings:
- Difference between append, overwrite, and merge
- Importance of business keys in incremental pipelines
- Snapshot vs cumulative data modeling
- Designing re-runnable and idempotent pipelines

Future Enhancements:
- Add constructor and driver performance trends
- Create views for latest standings
- Integrate Power BI / Tableau dashboards
- Add data quality checks