# Logistics Data Lakehouse Pipeline

This repository contains an end-to-end Medallion Architecture (Lakehouse) built on Databricks. It processes streaming logistics data—specifically drivers, vehicles, and trips—transforming raw JSON payloads into a fully optimized Star Schema ready for Business Intelligence (BI).

## 🏗️ Architecture Overview

* **Bronze Layer:** Ingests raw JSON streams of driver, vehicle, and trip events using PySpark Structured Streaming.
* **Silver Layer:** Parses, cleans, and enforces schemas on the streaming data. This layer utilizes Delta Lake features like **Schema Evolution** (adding new columns on the fly) and **Time Travel**.
* **Gold Layer:** A fully modeled Star Schema (Fact and Dimension tables). It is enriched with business logic (e.g., driver experience levels, premium statuses, and calculated fare metrics) and optimized for fast BI queries using **Liquid Clustering** and **Z-Ordering**.

## 📂 Project Structure

The pipeline is modularized into five Databricks notebooks for clean execution and separation of concerns:

1. **`Setup Infrastructure.ipynb`**
   * Initializes the Unity Catalog, Databases (Bronze, Silver, Gold), and the Volume for streaming checkpoints.
   * Contains the DDL (Data Definition Language) for the Gold layer Star Schema, explicitly defining business-level columns and Liquid Clustering keys.

2. **`IAM config for Logistics.ipynb`**
   * Handles Data Governance using Unity Catalog.
   * Applies the Principle of Least Privilege by granting specific `USE CATALOG`, `USE SCHEMA`, and `SELECT` permissions to the `innowise` group using a positive grant model.
   * Instead of an explicit deny, Unity Catalog uses an allow-by-default / fail-closed security model - Users have zero access to data unless explicitly given a GRANT

3. **`Logistics ETL pipeline.ipynb`**
   * The core data engineering engine. 
   * Uses PySpark and the Python `faker` library to simulate and stream live data.
   * Handles Schema Evolution (dynamically adding a `phone` column).
   * Demonstrates Delta Lake Time Travel capabilities.
   * Uses DML (`INSERT INTO`) to transform Silver data into the enriched Gold layer, followed by `OPTIMIZE` commands to compact and sort the files.

4. **`Queries for Logistics.ipynb`**
   * Contains DQL (`SELECT`) statements that act as stand-ins for BI dashboards.
   * Performs complex multi-table joins across the Star Schema to extract meaningful business KPIs (e.g., average revenue by driver experience).

5. **`Cleanup Tables.ipynb`**
   * A utility notebook to delete table records and wipe streaming checkpoint directories, allowing the pipeline to be reset and run from scratch.

## 🚀 Key Databricks Features Demonstrated
* PySpark Structured Streaming (`AvailableNow` triggers)
* Delta Lake Schema Evolution (`mergeSchema`)
* Delta Lake Time Travel & `RESTORE`
* Liquid Clustering & Z-Ordering (`OPTIMIZE`)
* Unity Catalog Access Controls (IAM)

## 🔒 Security Note
The user and group `innowise` were provisioned manually via the Databricks UI in accordance with security best practices. The explicit `GRANT` statements applying permissions to this group are located in the `IAM config for Logistics` notebook. (See included screenshot for Admin UI validation).
