# Databricks End-to-End Data Engineering Pipeline

## Overview

This project demonstrates an end-to-end data engineering pipeline built on Databricks using the Medallion Architecture. The pipeline ingests raw Parquet datasets stored in Azure Data Lake Storage Gen2 (ADLS Gen2), processes them through Bronze, Silver, and Gold layers, and produces curated datasets for analytical workloads.

The implementation showcases core Databricks capabilities, including Auto Loader, Structured Streaming, Delta Lake, PySpark, SQL functions, and notebook-based ETL development.

---

## Project Workflow

The pipeline processes multiple business datasets, including customers, orders, products, and regions.

The overall data flow follows the Medallion Architecture:

```
Source Files (Parquet)
          │
          ▼
Azure Data Lake Storage
          │
          ▼
Bronze Layer
(Raw Data Ingestion)
          │
          ▼
Silver Layer
(Data Cleaning & Transformations)
          │
          ▼
Gold Layer
(Business-Ready Data)
```

---

## Bronze Layer

The Bronze layer is responsible for ingesting raw source data without applying business transformations.

The ingestion process uses Databricks Auto Loader with Structured Streaming to continuously monitor source folders for new Parquet files. Auto Loader automatically discovers new files, infers schemas, and processes incoming data efficiently.

A parameterized notebook is used to process different datasets by passing the dataset name through Databricks widgets. This allows the same ingestion logic to be reused for multiple source tables.

Checkpoint locations are configured to ensure fault tolerance and exactly-once processing during streaming ingestion.

The ingested data is stored in the Bronze storage container while preserving the raw structure of the source data.

---

## Parameters Notebook

A dedicated parameter notebook defines the datasets that should be processed.

Instead of hardcoding dataset names, the notebook creates a collection containing datasets such as:

* Customers
* Orders
* Products

These parameters are passed between Databricks tasks, enabling the pipeline to process multiple datasets using a single ingestion notebook.

---

## Silver Layer

The Silver layer transforms raw data into clean, standardized datasets.

Each business entity is processed in its own notebook.

### Customer Processing

The customer pipeline:

* Reads data from the Bronze layer
* Removes rescued metadata columns
* Extracts email domains from customer email addresses
* Performs aggregation to analyze customer distribution across domains
* Produces a cleaned customer dataset

### Order Processing

The order pipeline:

* Reads Bronze data
* Removes unwanted metadata columns
* Converts order dates into timestamp format
* Extracts year values from order dates for downstream analysis

### Product Processing

The product pipeline demonstrates both PySpark and SQL functionality.

It includes:

* Cleaning product data
* Creating SQL user-defined functions
* Creating Python user-defined functions
* Calculating discounted product prices
* Standardizing product attributes

### Region Processing

The region notebook:

* Reads Bronze Delta tables
* Removes unnecessary metadata columns
* Stores cleaned data in the Silver layer as Delta tables

---

## Gold Layer

The Gold layer prepares business-ready datasets that can be consumed by reporting tools and analytical applications.

The current implementation focuses on building a customer dimension table.

The notebook performs operations such as:

* Removing duplicate customer records
* Generating surrogate keys using `monotonically_increasing_id()`
* Supporting both initial and incremental loading logic
* Preparing dimensional data for downstream analytics

The resulting dataset represents a curated customer dimension suitable for business intelligence and reporting.

---

## Technologies Used

* Databricks
* Apache Spark
* PySpark
* Delta Lake
* Azure Data Lake Storage Gen2
* Databricks Auto Loader
* Structured Streaming
* SQL User Defined Functions
* Python User Defined Functions

---

## End-to-End Pipeline Summary

1. Raw Parquet datasets are stored in Azure Data Lake Storage.
2. Databricks Auto Loader continuously ingests new files into the Bronze layer.
3. Bronze data is cleaned and transformed in the Silver layer using PySpark and SQL.
4. Business rules are applied to produce curated datasets.
5. The Gold layer generates analytics-ready dimensional tables that can be consumed by dashboards and reporting systems.

This project demonstrates the implementation of a modern data engineering pipeline using Databricks and the Medallion Architecture, covering data ingestion, transformation, storage, and preparation of analytical datasets in an end-to-end workflow.

