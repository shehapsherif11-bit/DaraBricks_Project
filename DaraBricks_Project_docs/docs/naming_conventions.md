# Naming Conventions

Consistent naming rules applied across catalogs, schemas, tables, columns, and notebooks in this project.

## General principles
- `snake_case` for everything — schemas, tables, columns.
- English, descriptive, business-friendly names in Silver and Gold (source system abbreviations like `cst_`, `prd_`, `sls_` are only used in Bronze, mirroring the raw files).
- No spaces or special characters.

## Catalog / schema structure
Unity Catalog: `workspace`
```
workspace.bronze.*   -- raw, as-is
workspace.silver.*   -- cleansed, standardized
workspace.gold.*     -- business-ready, star schema
```

## Table naming

| Layer | Pattern | Examples |
|---|---|---|
| Bronze | `<source_table_name>` (unchanged from source file) | `cust_info`, `prd_info`, `sales_details` |
| Silver | `<system>_<entity>` | `crm_customers`, `crm_products`, `crm_sales`, `erp_customer_location`, `erp_product_category` |
| Gold | `dim_<entity>` / `fact_<entity>` | `dim_customers`, `dim_products`, `fact_sales` |

## Column naming
- Surrogate keys: `<entity>_key` (e.g. `customer_key`, `product_key`), generated with `xxhash64` during the Silver → Gold transform.
- Foreign keys reference the dimension's surrogate key by the same name as it appears in the dimension.
- Dates: `<event>_date` (e.g. `order_date`, `ship_date`, `due_date`, `start_date`).
- Flags/booleans: `<attribute>_flag` (e.g. `maintenance_flag`).
- Avoid abbreviations outside Bronze — `customer_number` instead of `cst_key`, `sales_amount` instead of `sls_sales`.

## Notebook naming
```
<Layer>/<System>_<Entity>.ipynb
```
Examples: `Silver/CRM/Silver_CRM_Sales.ipynb`, `Gold/Gold_Fact_Sales.ipynb`.
