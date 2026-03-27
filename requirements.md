
# Nigerian DISCO Medallion Data Platform - Requirements & Table Mapping

## Project Objective
Build an end-to-end batch ELT pipeline using **dlt + dbt + Bruin** to integrate multiple DISCO departments' data sources.  
The platform delivers **feeder-level energy balance**, **ATC&C loss calculations**, **revenue assurance**, and **NERC-compliant reporting** through a Medallion architecture (Bronze → Silver → Gold).

**Key Highlight**:  
- `Payment Table` comes from **streaming services** (Finance).  
- `Billing Details (Postpaid)` and `Billing Details (Prepaid)` are the **source of truth** that expand/generate the Payment table and support revenue/complaint calculations.

## Department-wise Table Mapping

### Finance Department (Streaming Source)
- Payment Table (derived/ingested from streaming service)

### HR Department
- Employee Table

### Technical Department
- Transformers Information Table
- Feeder Information Table
- Availability Table
- Tripping Table
- Injection Substation Reading Table (measures Hourly Load / Voltage / Phase / Energy reading in Injection Substation)
- DSS Substation Readings Table (measures Hourly Load, Voltage, Phase, Energy readings and Temperature)
- Feeder Readings Hourly Table

### Commercial Department
- Customer Information Table
- Bill Details for Prepaid (PPM Bill Details)
- Bill Details for Postpaid

### DSS (Distribution Sub-Station / SCADA)
- DSS Substation Readings Table (Hourly Load, Voltage, Phase, Energy readings and Temperature)
- Power Transformer Hourly Load, Voltage, Phase, Energy, Temperature (covered under DSS readings)

### GIS Department
- Geo Location of assets following network
- Transformers (with geo coordinates)
- Meters
- Customers (linked to geo / feeders)

### Metering Department
- Hourly Meter kWh + other readings (prepaid & postpaid)

### Customer Complaint
- Complaint Monthly Report (derived from billing shortfalls + outages + low collection feeders)

## Ingestion Grouping → dlt Pipelines (Planned)

1. **billing_sources**  
   - bill_details_postpaid.csv  
   - bill_details_prepaid.csv (PPM)  
   → Generates/expands **payment_table** (Finance streaming simulation)

2. **gis_assets**  
   - transformers_gis.csv  
   - feeders_info.csv  
   - geo_locations.csv  
   - meters.csv  
   - customers_gis.csv

3. **technical_readings**  
   - injection_substation_readings.csv  
   - dss_substation_readings.csv  
   - feeder_readings_hourly.csv  
   - availability_table.csv  
   - tripping_table.csv

4. **outages_events**  
   - outages_trips.csv

5. **hr_master**  
   - employee_table.csv

6. **customer_master**  
   - customer_information.csv  
   → Supports **complaint_monthly_report** derivation

## Data Flow Summary (Medallion Architecture)
- **Bronze**: Raw CSVs landed via dlt (local DuckDB / production BigQuery)
- **Silver**: Cleaned, standardized, DQ checks (dbt)
- **Gold**: Business logic – Energy Balance, ATC&C Loss %, Revenue Leakage, 3-year performance roll-ups (Service Unit → BU → Region → Company), Complaint reports
- **Orchestration**: Bruin (assets, lineage, quality gates)
- **Dashboard**: Looker Studio with ATC&C trend (temporal) + Loss distribution by feeder/area (categorical)

## Notes for Capstone
- Batch pipeline (daily/hourly readings + monthly billing)
- Postpaid & Prepaid billing tables are central → used to derive Payment Table and Revenue Assurance metrics
- All tables will be generated synthetically for the project