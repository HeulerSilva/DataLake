# DataFrame_Base1 — PySpark & Delta Lake Fundamentals

## Overview

This project demonstrates core PySpark DataFrame operations, Delta Lake capabilities, and external data access patterns using Databricks. It progresses from basic DataFrame manipulation through Delta Lake versioning to external connectivity — covering the practical foundations of a modern data engineering workflow.

## Project Structure

```
DataFrame_Base1/
├── 1. Dataframes.ipynb      # PySpark DataFrame exploration and transformations
├── 2. Scripts_SQL.ipynb     # Persisting DataFrames as managed tables
├── 3. Deltalake.ipynb       # Delta Lake format, updates, and version history
├── 4. Conexao.ipynb         # External connection via Databricks SQL Connector
└── README.md
```

## Technical Stack

- **Language:** Python, SQL
- **Data Processing:** PySpark
- **Storage Format:** Delta Lake
- **Compute:** Databricks Serverless
- **External Access:** Databricks SQL Connector (Google Colab)
- **Version Control:** GitHub

---

## 1. Dataframes — PySpark Exploration

Covers foundational DataFrame operations using the `despachantes` and `carros` datasets:

- **Schema handling:** explicit schema definition vs. `inferSchema`, and comparing the resulting structures with `printSchema()`
- **Actions vs. transformations:** `show()`, `take()`, `collect()`, `count()`
- **Filtering:** single and compound conditions using `where()` / `filter()`
- **Column operations:** renaming columns, type conversion (string → timestamp)
- **Date functions:** extracting year, grouping by time period
- **Sorting:** single and multi-column, ascending/descending
- **Aggregation:** `groupBy()` with `sum()`, alias naming for readable output
- **Multi-format export:** writing the same DataFrame to Parquet, CSV, JSON, and ORC for format comparison

## 2. Scripts_SQL — Persisting as Managed Tables

Demonstrates the transition from an in-memory DataFrame to a governed, queryable table in Unity Catalog:

- Loading `despachantes.csv` with inferred schema
- Persisting via `saveAsTable()` using the fully qualified path (`catalog.schema.table`)
- Validating table contents directly in SQL

## 3. Deltalake — Format, Updates & Time Travel

Explores Delta Lake as a storage format and its version control capabilities:

- Converting a table to Delta format and saving to a Unity Catalog Volume
- Applying conditional column updates (`when()` / `otherwise()`) and overwriting the Delta table
- Inspecting version history with `DESCRIBE HISTORY`
- Reading historical versions using **Time Travel** (`versionAsOf`)
- Comparing current vs. historical DataFrame states

## 4. Conexao — External Connectivity

Demonstrates consuming Databricks data from an external environment (Google Colab), simulating a real-world integration scenario — such as a BI backend or scheduled external job:

- Connecting via `databricks-sql-connector`
- Querying a Delta table and loading results into pandas
- **Security practice:** credentials (access token) are managed via Colab Secrets, never hardcoded in the notebook — following the same principle as production secret managers (Databricks Secrets, AWS Secrets Manager, environment variables)

---

## Key Takeaways

This project demonstrates the full lifecycle of a dataset within Databricks:

1. **Ingest & explore** — load raw data, understand its shape, transform it with PySpark
2. **Persist & govern** — save as a managed table under Unity Catalog
3. **Version & audit** — convert to Delta, track changes, and recover historical states via Time Travel
4. **Expose externally** — make the data queryable from outside the platform, with credentials handled securely

## References

- [PySpark SQL Documentation](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/index.html)
- [Delta Lake Documentation](https://docs.delta.io/latest/index.html)
- [Delta Lake Time Travel](https://docs.databricks.com/en/delta/history.html)
- [Databricks SQL Connector for Python](https://docs.databricks.com/en/dev-tools/python-sql-connector.html)

---

**Author:** Heuler Ferreira Silva
**Repository:** https://github.com/HeulerSilva/DataLake/tree/main/DataFrame_Base1
