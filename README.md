# Databricks Medallion Architecture ETL Pipeline

## Project Overview
This repository contains a complete Extract, Transform, Load (ETL) pipeline implemented on **Databricks** using the **Medallion Architecture** (Bronze, Silver, Gold layers). The project demonstrates best practices for data warehousing and data lake management using Delta Lake and PySpark.

The pipeline processes raw customer data from a source system (simulated via CSV) through three distinct stages to create a clean, transformed, and business-ready dimensional model.

## Architecture: The Medallion Layer
The project is structured around the Medallion Architecture, which provides a logical separation of data quality and transformation stages:

| Layer | Purpose | Data Quality | Notebook |
| :--- | :--- | :--- | :--- |
| **Bronze** | Raw data ingestion, minimal cleaning. | Raw, untrusted. | `Bronze.ipynb` |
| **Silver** | Data cleansing, filtering, and initial transformations. | Clean, validated, and reconciled. | `Silver_crm_cust_info.ipynb` |
| **Gold** | Business-level aggregates and dimensional models. | Highly refined, business-ready. | `Gold_customer.ipynb` |

## Data Flow
The ETL process follows a clear path:

`Source CSV (cust_info.csv) -> Bronze Table (crm_cust_info) -> Silver Table (crm_customers) -> Gold Table (dim_customers)`

## Notebook Breakdown

### 1. `Bronze.ipynb` (Ingestion)
- **Goal:** Ingest raw customer data into the Bronze layer.
- **Key Operations:**
    - Reads a CSV file (`cust_info.csv`) from a specified volume path.
    - Infers schema and reads the header.
    - Writes the raw data to the Delta Lake Bronze table: `workspace.bronze.crm_cust_info`.

### 2. `Silver_crm_cust_info.ipynb` (Cleansing & Transformation)
- **Goal:** Clean and standardize the Bronze data for reliability.
- **Key Operations:**
    - Reads data from the Bronze table.
    - **Data Cleansing:** Trims whitespace from all string columns.
    - **Standardization:** Renames columns (e.g., `cst_id` to `customer_id`) for clarity and consistency.
    - Writes the cleaned data to the Delta Lake Silver table: `silver.crm_customers`.

### 3. `Gold_customer.ipynb` (Modeling)
- **Goal:** Apply business logic and create a dimensional model for reporting.
- **Key Operations:**
    - Reads data from the Silver table.
    - **Dimensional Modeling:** Creates a surrogate key (`customer_key`) using a window function (`ROW_NUMBER()`) to form the `dim_customers` table.
    - Writes the final, business-ready data to the Delta Lake Gold table: `gold.dim_customers`.

## Prerequisites and Setup
To run this project, you will need:
1.  An active **Databricks Workspace**.
2.  A cluster with **Delta Lake** support (Databricks Runtime).
3.  The source CSV file (`cust_info.csv`) must be accessible via a Databricks Volume or DBFS path, as referenced in `Bronze.ipynb`.
4.  The necessary Catalogs and Schemas (`workspace.bronze`, `silver`, `gold`) must be created in your Unity Catalog environment.

## Contributing
Feel free to fork this repository and submit pull requests. For major changes, please open an issue first to discuss what you would like to change.
