# Nigerian DISCO Medallion Data Platform

**Data Engineering Zoomcamp Capstone Project**

## Problem Statement
Nigerian Electricity Distribution Companies (DISCOs) suffer from high Aggregate Technical, Commercial & Collection (ATC&C) losses.  
This project builds an end-to-end **batch ELT pipeline** that integrates data from multiple departments (Finance, HR, Technical, Commercial, DSS, GIS, Metering, Customer Complaints) to calculate **feeder-level energy balance**, **ATC&C losses**, **revenue leakage**, and generate **NERC-compliant reports**.

**Key Business Value**:
- Accurate daily/ hourly feeder-level injected vs billed energy
- Automatic expansion from Postpaid + Prepaid billing → Payment table
- Identification of loss hotspots (technical + commercial + collection)
- 3-year performance roll-up at Service Unit → Business Unit → Region level

## Architecture (Hybrid Stack)
![Architecture Diagram](architecture_diagram.png)

**Flow**:
Sources (CSVs) → **dlt** (ingestion) → **DuckDB** (local dev/testing) → **dbt** (Silver cleaning + Gold transformations) → **BigQuery** (production warehouse) → **Bruin** (orchestration, lineage, quality gates) → **Looker Studio** (dashboard)

## Technologies Used
- **Ingestion**: dlt (Python ELT with schema evolution)
- **Local Warehouse**: DuckDB
- **Production Warehouse**: Google BigQuery (partitioned & clustered)
- **Transformations**: dbt (models, tests, macros for ATC&C formulas)
- **Orchestration & Governance**: Bruin (assets, lineage graphs, quality gates)
- **Dashboard**: Looker Studio (2+ tiles)

## Department & Table Mapping
See [requirements.md](requirements.md) for full mapping.

## How to Run (Reproducibility)
1. `uv sync`
2. Activate environment: `source .venv/bin/activate` (Mac/Linux) or `.venv\Scripts\activate` (Windows)
3. Generate data: `python generate_data.py` (coming in Day 2)
4. Run ingestion: `python disco_pipeline/pipeline.py`
5. Run transformations: `cd dbt_energy && dbt run`
6. Orchestrate: `bruin run`
7. View dashboard: Link will be added after Day 6

## Dashboard Tiles (Will Include)
- Temporal: ATC&C loss % trend line over 3 years (monthly)
- Categorical: Loss distribution by feeder / area (bar/heatmap)

**Note**: This project uses **batch** processing (suitable for hourly readings + monthly billing).

Project developed as part of Data Engineering Zoomcamp by DataTalks.Club.
