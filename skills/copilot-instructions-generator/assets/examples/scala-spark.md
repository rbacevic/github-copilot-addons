# Scala Spark Application

Apache Spark application using Scala for large-scale data processing.

## Tech Stack

- **Language**: Scala 2.12 / 2.13
- **Framework**: Apache Spark 3.5+
- **Build**: sbt / Maven
- **Storage**: Delta Lake / Parquet
- **Testing**: ScalaTest + spark-testing-base
- **Platform**: Databricks / EMR / Dataproc

## Project Structure

- `src/main/scala/` - Source code
  - `jobs/` - Spark job entry points
  - `transformations/` - DataFrame transformations
  - `models/` - Case classes and schemas
  - `utils/` - Utility functions
  - `config/` - Configuration management
- `src/test/scala/` - Test files
- `project/` - sbt build configuration
- `conf/` - Environment configurations

## Development Commands

- **Compile**: `sbt compile`
- **Test**: `sbt test`
- **Package**: `sbt assembly`
- **Run locally**: `sbt "runMain com.example.MainJob"`
- **Submit**: `spark-submit --class com.example.MainJob target/app.jar`

## Before Committing

- Run `sbt compile` to verify compilation
- Run `sbt test` to verify tests pass
- Run `sbt scalafmtAll` for formatting
- Check for compiler warnings

## Coding Guidelines

### Scala Style
- Follow Scala style guide
- Use immutable data structures
- Prefer case classes for data
- Use Option instead of null
- Pattern matching over if/else chains

### Case Classes for Schemas
```scala
case class User(
  userId: String,
  name: String,
  email: Option[String],
  createdAt: java.sql.Timestamp
)

// With Encoder for Dataset
implicit val userEncoder: Encoder[User] = Encoders.product[User]
```

### DataFrame vs Dataset
- Use DataFrame for dynamic schemas
- Use Dataset[T] for type-safe operations
- Convert between with `.as[T]` and `.toDF()`
- Prefer DataFrame API for performance-critical code

### Transformation Functions
```scala
def transformUsers(df: DataFrame): DataFrame = {
  df
    .withColumn("fullName", concat_ws(" ", col("firstName"), col("lastName")))
    .withColumn("createdDate", to_date(col("createdAt")))
    .filter(col("status") === "active")
    .select("userId", "fullName", "createdDate")
}
```

### Column Operations
- Import `org.apache.spark.sql.functions._`
- Use `col("name")` or `$"name"` for columns
- Use `lit()` for literal values
- Chain operations fluently

### Joins
```scala
val result = users
  .join(orders, users("userId") === orders("userId"), "left")
  .select(
    users("userId"),
    users("name"),
    orders("orderId"),
    orders("amount")
  )
```

### Aggregations
```scala
df.groupBy("category")
  .agg(
    count("*").as("totalCount"),
    sum("amount").as("totalAmount"),
    avg("amount").as("avgAmount")
  )
```

### Window Functions
```scala
val windowSpec = Window
  .partitionBy("userId")
  .orderBy(col("createdAt").desc)

df.withColumn("rowNum", row_number().over(windowSpec))
  .filter(col("rowNum") === 1)
```

### Error Handling
- Use Try/Success/Failure for recoverable errors
- Use Either for expected failures
- Log errors with context
- Implement retry logic for transient failures

### Performance
- Cache DataFrames used multiple times
- Use broadcast for small lookup tables
- Repartition based on downstream operations
- Enable Adaptive Query Execution
- Use Delta Lake optimizations

## Implicit Classes for Extensions
```scala
implicit class DataFrameOps(df: DataFrame) {
  def withNormalizedColumns: DataFrame = {
    df.columns.foldLeft(df) { (acc, colName) =>
      acc.withColumnRenamed(colName, colName.toLowerCase.replace(" ", "_"))
    }
  }
}
```

## Testing Standards

- Use ScalaTest FlatSpec or FunSuite
- Create SparkSession in test fixtures
- Use small test datasets
- Test transformations in isolation
- Mock external dependencies

## Build Configuration (build.sbt)
```scala
libraryDependencies ++= Seq(
  "org.apache.spark" %% "spark-sql" % sparkVersion % "provided",
  "io.delta" %% "delta-core" % deltaVersion,
  "org.scalatest" %% "scalatest" % "3.2.17" % Test
)
```

## Key Guidelines

1. Prefer immutable transformations
2. Use case classes for type safety
3. Define schemas explicitly
4. Write idempotent jobs
5. Partition data appropriately for query patterns
