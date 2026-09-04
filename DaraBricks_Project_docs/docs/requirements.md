# Project Requirements

## 1. Building the Lakehouse (Data Engineering)

### Objective
Build a modern lakehouse on Databricks that consolidates sales data from two source systems, enabling analytical reporting and informed decision-making.

### Specifications
- **Data Sources**: Import data from two source systems (CRM and ERP), provided as CSV files.
- **Data Quality**: Cleanse and resolve data quality issues (duplicates, invalid dates, inconsistent codes) before analysis.
- **Integration**: Combine both sources into a single, user-friendly data model designed for analytical queries.
- **Scope**: Focus on the latest dataset only — historization of data is not required.
- **Storage**: All layers persisted as Delta tables inside a Unity Catalog managed lakehouse (`workspace` catalog).
- **Documentation**: Provide clear documentation of the data model (`docs/data_catalog.md`) and naming rules (`docs/naming_conventions.md`) to support both business stakeholders and analytics teams.

## 2. Analytics & Reporting (Data Analysis)

### Objective
Deliver detailed insights into:
- **Customer Behavior** — demographics, geography, purchase patterns
- **Product Performance** — category and product-line sales performance
- **Sales Trends** — order volume and revenue over time

These insights are surfaced through the Gold-layer star schema (`dim_customers`, `dim_products`, `fact_sales`), which is designed to be queried directly by Power BI / SQL.
