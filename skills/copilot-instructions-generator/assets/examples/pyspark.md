# PySpark Data Engineering

Apache Spark application using PySpark for large-scale data processing.

## Tech Stack

- **Framework**: PySpark 3.5+
- **Language**: Python 3.10+
- **Platform**: Databricks / EMR / Dataproc
- **Storage**: Delta Lake / Parquet
- **Orchestration**: Airflow / Databricks Workflows
- **Testing**: pytest + chispa

## Project Structure

- `src/` - Source code
  - `jobs/` - Spark job entry points
  - `transformations/` - DataFrame transformations
  - `schemas/` - Schema definitions
  - `utils/` - Utility functions
  - `config/` - Configuration management
- `tests/` - Test files
  - `unit/` - Unit tests with local Spark
  - `integration/` - Integration tests
- `notebooks/` - Exploratory notebooks
- `conf/` - Environment configurations

## Development Commands

- **Local run**: `spark-submit src/jobs/main_job.py`
- **Test**: `pytest tests/ -v`
- **Lint**: `ruff check src/`
- **Format**: `ruff format src/`

## Coding Guidelines

### DataFrame Operations
- Use DataFrame API over RDD API
- Chain transformations for readability
- Use meaningful column aliases
- Avoid UDFs when built-in functions exist

### Schema Management
```python
from pyspark.sql.types import StructType, StructField, StringType, IntegerType

USER_SCHEMA = StructType([
    StructField("user_id", StringType(), nullable=False),
    StructField("name", StringType(), nullable=True),
    StructField("age", IntegerType(), nullable=True),
])
```

### Transformation Patterns
```python
def transform_users(df: DataFrame) -> DataFrame:
    """Transform user data with standardized columns."""
    return (
        df
        .withColumn("full_name", F.concat_ws(" ", "first_name", "last_name"))
        .withColumn("created_date", F.to_date("created_at"))
        .filter(F.col("status") == "active")
        .select("user_id", "full_name", "created_date")
    )
```

### Column References
- Use `F.col("name")` for column references
- Avoid string column names in expressions
- Use `F.lit()` for literal values
- Alias columns explicitly after operations

### Joins
- Specify join type explicitly
- Use broadcast joins for small tables
- Rename ambiguous columns before join
- Filter before join when possible

### Aggregations
- Use `groupBy().agg()` pattern
- Name aggregated columns with alias
- Use window functions for running calculations
- Partition large aggregations appropriately

### Performance
- Persist/cache DataFrames used multiple times
- Repartition for parallelism, coalesce to reduce
- Use predicate pushdown with partitioned data
- Avoid shuffle-heavy operations when possible
- Use Delta Lake for ACID and optimization

### Null Handling
- Use `F.coalesce()` for default values
- Use `na.fill()` for bulk null replacement
- Check for nulls explicitly in filters
- Document null handling decisions

### Configuration
```python
spark = (
    SparkSession.builder
    .appName("MyJob")
    .config("spark.sql.adaptive.enabled", "true")
    .config("spark.sql.shuffle.partitions", "200")
    .getOrCreate()
)
```

## Delta Lake Patterns

- Use Delta format for all tables
- Enable Change Data Feed for CDC
- Use MERGE for upserts
- Implement proper partitioning strategy
- Run OPTIMIZE and VACUUM regularly

## Testing Standards

- Use chispa for DataFrame assertions
- Create small test DataFrames locally
- Test transformations in isolation
- Mock external data sources
- Test schema validation

## Error Handling

- Use try/except around job entry points
- Log errors with job context
- Implement retry logic for transient failures
- Write failed records to error tables

## Key Guidelines

1. Define schemas explicitly - never infer
2. Write idempotent jobs (re-runnable)
3. Partition data by date/region appropriately
4. Log transformation metrics
5. Use Delta Lake merge for incremental loads
