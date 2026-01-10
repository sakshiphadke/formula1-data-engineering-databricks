# Formula 1 Data Engineering & Analytics Pipeline
An end-to-end data engineering project built using Azure Databricks to ingest, transform, and analyze Formula 1 racing data using PySpark and Azure data Lake Storage.

This project demonstrates how large-scale sports data can be processed using modern data engineering practices.
Raw Formula 1 race data is ingested, cleaned, transformed, and stored in analytics-ready tables to enable meaningful insights and reporting.

Tech Stack:
- Azure Databricks
- PySpark
- Spark SQL
- Delta Lake
- Azure Data Lake Storage

Data Pipeline Architecture:
1. Ingest raw CSV and JSON files into the Bronze Layer
2. Apply schema validation and data cleaning
3. Transform data into curated Silver tables
4. Create analytics-ready Gold tables for reporting

Key Insights:
- Driver performance analysis
- Team-wise race results
- Season-level statistics
- Identifying top drivers and constructors

Key Learnings:
- Building scalable ETL pipelines using PySpark
- Working with Delta Lake for reliable data storage
- Handling structured and semi-structured data
- Writing efficient Spark SQL transformations

Future Enhancements:
- Implement incremental data loading
- Add streaming data ingestion
- Build dashboards using Power BI
