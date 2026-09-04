# Data Catalog — Gold Layer

Business-ready tables in `workspace.gold`, consumed directly by Power BI / SQL analytics.

---

## 🥇 `gold.dim_customers`

Customer dimension, combining CRM master data with ERP demographic and location attributes.

| Column | Type | Description |
|---|---|---|
| `customer_key` | BIGINT | Surrogate key (xxhash64), primary key of the dimension |
| `customer_id` | INT | Source system customer identifier (CRM) |
| `customer_number` | STRING | Alphanumeric customer code used for joins to ERP |
| `first_name` | STRING | Customer's first name |
| `last_name` | STRING | Customer's last name |
| `marital_status` | STRING | `Single`, `Married`, or `n/a` |
| `gender` | STRING | Standardized gender value; falls back to ERP source when CRM is missing |
| `country` | STRING | Standardized country name (e.g. `DE` → `Germany`) |
| `created_date` | DATE | Date the customer record was created in CRM |

---

## 🥇 `gold.dim_products`

Current product catalog, enriched with category and subcategory from ERP.

| Column | Type | Description |
|---|---|---|
| `product_key` | BIGINT | Surrogate key (xxhash64 of product number + start date) |
| `product_id` | INT | Source system product identifier |
| `product_number` | STRING | Product code parsed from the CRM product key |
| `product_name` | STRING | Descriptive product name |
| `category_id` | STRING | Category code, parsed from the CRM product key |
| `category` | STRING | Product category (from ERP) |
| `subcategory` | STRING | Product subcategory (from ERP) |
| `maintenance_flag` | BOOLEAN | Whether the product requires maintenance |
| `product_line` | STRING | `Mountain`, `Road`, `Touring`, `Other Sales`, or `n/a` |
| `start_date` | DATE | Date the product became active |

> Only current products are modeled (historical/expired product versions are out of scope, matching the source project's requirements).

---

## 🥇 `gold.fact_sales`

Sales transactions at the order-line grain, linked to both dimensions via surrogate keys.

| Column | Type | Description |
|---|---|---|
| `order_number` | STRING | Sales order identifier |
| `product_key` | BIGINT | FK → `dim_products.product_key` |
| `customer_key` | BIGINT | FK → `dim_customers.customer_key` |
| `order_date` | DATE | Date the order was placed |
| `ship_date` | DATE | Date the order was shipped |
| `due_date` | DATE | Date the order was due |
| `sales_amount` | DOUBLE | Total value of the order line |
| `sales_quantity` | INT | Units sold |
| `sales_price` | DOUBLE | Unit price |

---

## Silver layer (intermediate)

The Silver layer is not exposed to BI tools directly, but is documented here since it's where most data-quality logic lives.

| Table | Source | Key transformations |
|---|---|---|
| `silver.crm_customers` | `source_crm/cust_info.csv` | Trim, dedupe on latest record, marital status / gender normalization, drop null customer IDs, rename to business-friendly columns |
| `silver.crm_products` | `source_crm/prd_info.csv` | Parse category code and clean product key out of `prd_key`, product line normalization, null cost handling |
| `silver.crm_sales` | `source_crm/sales_details.csv` | Invalid date detection (zero / wrong length), quantity & price type casting, sales amount recalculation where inconsistent |
| `silver.erp_customer_location` | `source_erp/loc_a101.csv` | Strip separators from customer ID, country code normalization (`DE`→`Germany`, `US`/`USA`→`United States`) |
| `silver.erp_product_category` | `source_erp/px_cat_g1v2.csv` | Maintenance flag → boolean, column renaming |

Bronze tables (`bronze.cust_info`, `bronze.prd_info`, `bronze.sales_details`, `bronze.cust_az12`, `bronze.loc_a101`, `bronze.px_cat_g1v2`) mirror the source CSVs as-is, with no transformations applied.
