# ETL_Base1 - IBGE Districts Data Pipeline

## Overview

This project implements an ETL (Extract, Transform, Load) pipeline to ingest, process, and store Brazilian districts data from the IBGE (Brazilian Institute of Geography and Statistics) public API into a Databricks Delta Lake table.

## Data Source

**API Endpoint:** `https://servicodados.ibge.gov.br/api/v1/localidades/distritos`

The IBGE API provides comprehensive geographical data about Brazilian administrative divisions, including:
- Districts (Distritos)
- Municipalities (Municípios)
- Micro-regions (Microrregiões)
- Meso-regions (Mesorregiões)
- States (Unidades Federativas - UF)
- Regions (Regiões)

## Project Structure

```
ETL_Base1/
├── ETL_IBGE (notebook)    # Main ETL pipeline notebook
└── README.md              # Project documentation
```

## ETL Pipeline Workflow

### 1. Extract
- Fetches JSON data from the IBGE API using Python `requests` library
- Retrieves information about all Brazilian districts with nested geographical hierarchy

### 2. Transform
- Defines a nested PySpark schema to handle the hierarchical structure:
  ```
  District
  └── Municipality
      └── Micro-region
          └── Meso-region
              └── State (UF)
                  └── Region
  ```
- Creates a PySpark DataFrame with proper schema enforcement
- Preserves the nested structure for efficient querying

### 3. Load
- Writes the DataFrame to a Delta Lake table: `db_heuler.distritos_ibge`
- Uses `overwrite` mode to ensure data freshness
- Stores data in the Databricks unified catalog for easy access and governance

## Key Features

### Nested Column Access
The pipeline demonstrates how to query nested/struct columns in Spark SQL:

```sql
SELECT 
    id,
    nome,
    municipio.microrregiao.nome AS microrregiao,
    municipio.microrregiao.mesorregiao.nome AS mesorregiao,
    municipio.microrregiao.mesorregiao.UF.nome AS uf
FROM db_heuler.distritos_ibge
WHERE id = 520005005;
```

This approach:
- Maintains data normalization while allowing denormalized querying
- Reduces storage footprint by avoiding column explosion
- Enables efficient filtering and aggregation on hierarchical data

### Delta Lake Benefits
- **ACID Transactions:** Ensures data consistency during writes
- **Schema Evolution:** Supports future schema changes
- **Time Travel:** Enables historical data queries
- **Performance:** Optimized storage and query performance

## Technical Stack

- **Language:** Python, SQL
- **Data Processing:** PySpark (Spark Connect)
- **Storage Format:** Delta Lake
- **Compute:** Databricks Serverless (CPU)
- **Version Control:** GitHub repository

## Table Schema

**Table:** `db_heuler.distritos_ibge`

| Column Name | Data Type | Description |
|-------------|-----------|-------------|
| `id` | INT | District unique identifier |
| `nome` | STRING | District name |
| `municipio` | STRUCT | Nested structure containing municipality and hierarchical geographical data |

### Nested Structure Details
- `municipio.id`: Municipality ID
- `municipio.nome`: Municipality name
- `municipio.microrregiao.nome`: Micro-region name
- `municipio.microrregiao.mesorregiao.nome`: Meso-region name
- `municipio.microrregiao.mesorregiao.UF.sigla`: State abbreviation
- `municipio.microrregiao.mesorregiao.UF.nome`: State name
- `municipio.microrregiao.mesorregiao.UF.regiao.nome`: Region name

## Usage Examples

### Query All Districts in a Specific State
```sql
SELECT id, nome, municipio.nome AS municipio
FROM db_heuler.distritos_ibge
WHERE municipio.microrregiao.mesorregiao.UF.sigla = 'SP'
```

### Count Districts by Region
```sql
SELECT 
    municipio.microrregiao.mesorregiao.UF.regiao.nome AS regiao,
    COUNT(*) AS total_distritos
FROM db_heuler.distritos_ibge
GROUP BY municipio.microrregiao.mesorregiao.UF.regiao.nome
ORDER BY total_distritos DESC
```

## Data Refresh

To update the table with the latest IBGE data:
1. Open the `ETL_IBGE` notebook
2. Run all cells sequentially
3. The pipeline will fetch fresh data and overwrite the existing table

## Future Enhancements

- [ ] Add incremental load logic for changed records only
- [ ] Implement data quality checks and validation rules
- [ ] Create aggregated views for common queries
- [ ] Schedule periodic refreshes using Databricks Jobs
- [ ] Add error handling and logging mechanisms
- [ ] Expand to include other IBGE datasets (municipalities, states)

## References

- [IBGE API Documentation](https://servicodados.ibge.gov.br/api/docs)
- [PySpark SQL Documentation](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/index.html)
- [Delta Lake Documentation](https://docs.delta.io/latest/index.html)

## License

This project uses public data from IBGE, which is freely available for use.

---

**Last Updated:** July 9, 2026  
**Author:** Heuler Silva  
**Repository:** https://github.com/HeulerSilva/DataLake/tree/main/ETL_Base1