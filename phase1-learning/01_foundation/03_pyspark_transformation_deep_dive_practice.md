# DAY 3 (11/01/26) – PySpark Transformations Deep Dive

---

## 1. PySpark vs Pandas Comparison

---

## 1.1 Fundamental Difference

| Aspect          | Pandas         | PySpark             |
| --------------- | -------------- | ------------------- |
| Execution       | Single machine | Distributed cluster |
| Data size       | MB–few GB      | GB–TB–PB            |
| Memory          | RAM only       | RAM + Disk          |
| Parallelism     | Limited        | Massive             |
| Fault tolerance | ❌              | ✅                   |
| Lazy evaluation | ❌              | ✅                   |

📌 **Golden Rule**

> Use Pandas for **local analysis**, PySpark for **production pipelines**.

---

## 1.2 Example: Same Operation

### Pandas

```python
import pandas as pd

df = pd.read_csv("sales.csv")
df[df["amount"] > 1000].groupby("region")["amount"].sum()
```

### PySpark

```python
df = spark.read.csv("sales.csv", header=True)
df.filter("amount > 1000") \
  .groupBy("region") \
  .sum("amount")
```

---

## 1.3 Performance & Memory

### Pandas Issues

* Out-of-memory errors
* No fault recovery
* Slower joins on large data

### PySpark Advantages

* Disk spill
* DAG optimization
* Executor-level parallelism

---

## 1.4 Pandas API on Spark (Databricks)

```python
import pyspark.pandas as ps

ps_df = ps.read_csv("/mnt/data/sales.csv")
```

📌 **Note**: Not a replacement for Spark DataFrames at scale.

🔗 [https://spark.apache.org/docs/latest/api/python/user_guide/pandas_on_spark/](https://spark.apache.org/docs/latest/api/python/user_guide/pandas_on_spark/)

---

## 2. Joins in PySpark

---

## 2.1 Join Types Overview

| Join Type | Result              |
| --------- | ------------------- |
| Inner     | Matching rows       |
| Left      | All left + matches  |
| Right     | All right + matches |
| Outer     | All rows from both  |

---

## 2.2 Sample Data

```python
employees = spark.createDataFrame(
    [(1, "Alice"), (2, "Bob"), (3, "Charlie")],
    ["emp_id", "name"]
)

departments = spark.createDataFrame(
    [(1, "HR"), (2, "IT")],
    ["emp_id", "dept"]
)
```

---

## 2.3 Inner Join

```python
employees.join(departments, "emp_id", "inner").show()
```

**Output**

```
1 Alice HR
2 Bob   IT
```

✔ Most common
✔ Fastest

---

## 2.4 Left Join

```python
employees.join(departments, "emp_id", "left").show()
```

```
1 Alice   HR
2 Bob     IT
3 Charlie NULL
```

---

## 2.5 Right Join

```python
employees.join(departments, "emp_id", "right").show()
```

---

## 2.6 Full Outer Join

```python
employees.join(departments, "emp_id", "outer").show()
```

---

## 2.7 Production Join Optimization

### Broadcast Join

```python
from pyspark.sql.functions import broadcast

employees.join(broadcast(departments), "emp_id")
```

📌 Avoids shuffle
📌 Best for small dimension tables

---

## 3. Window Functions

---

## 3.1 What Are Window Functions?

Window functions:

* Operate on **logical partitions**
* Do NOT collapse rows (unlike groupBy)
* Enable ranking, running totals, lag/lead

---

## 3.2 Window Specification

```python
from pyspark.sql.window import Window
```

```python
windowSpec = Window.partitionBy("department") \
                   .orderBy("salary")
```

---

## 3.3 Running Total

```python
from pyspark.sql.functions import sum

df.withColumn(
    "running_total",
    sum("salary").over(windowSpec)
).show()
```

📌 Used in:

* Financial reporting
* Cumulative metrics

---

## 3.4 Ranking Functions

### Row Number

```python
from pyspark.sql.functions import row_number

df.withColumn(
    "row_num",
    row_number().over(windowSpec)
)
```

---

### Rank vs Dense Rank

```python
from pyspark.sql.functions import rank, dense_rank

df.withColumn("rank", rank().over(windowSpec)) \
  .withColumn("dense_rank", dense_rank().over(windowSpec))
```

| Function   | Gaps |
| ---------- | ---- |
| rank       | Yes  |
| dense_rank | No   |

---

## 3.5 Lag & Lead

```python
from pyspark.sql.functions import lag

df.withColumn(
    "prev_salary",
    lag("salary", 1).over(windowSpec)
)
```

📌 Used for:

* Time-series analysis
* Trend detection

---

## 3.6 Window Performance Notes

❌ Expensive (requires shuffle)
✅ Partition wisely
✅ Filter before window

---

## 4. User-Defined Functions (UDFs)

---

## 4.1 What is a UDF?

A **UDF** allows custom Python logic inside Spark.

📌 **But** Python UDFs are **slow** due to serialization.

---

## 4.2 Python UDF Example

```python
from pyspark.sql.functions import udf
from pyspark.sql.types import StringType

def categorize(amount):
    return "HIGH" if amount > 1000 else "LOW"

categorize_udf = udf(categorize, StringType())

df.withColumn("category", categorize_udf(df.amount))
```

---

## 4.3 Why Python UDFs Are Slow

* JVM ↔ Python serialization
* No Catalyst optimization
* No predicate pushdown

---

## 4.4 Prefer Built-in Functions (BEST PRACTICE)

### ❌ Bad

```python
udf(lambda x: x.upper())
```

### ✅ Good

```python
from pyspark.sql.functions import upper

df.withColumn("name_upper", upper("name"))
```

---

## 4.5 Pandas UDF (Vectorized UDF)

### Faster Alternative

```python
from pyspark.sql.functions import pandas_udf

@pandas_udf("string")
def categorize_udf(amount):
    return amount.apply(lambda x: "HIGH" if x > 1000 else "LOW")
```

✔ Vectorized
✔ Uses Apache Arrow
✔ Much faster

---

## 4.6 When to Use UDFs

| Scenario               | Recommendation              |
| ---------------------- | --------------------------- |
| Simple logic           | Built-in functions          |
| Complex business rules | Pandas UDF                  |
| ML logic               | Spark ML or batch inference |
| Last resort            | Python UDF                  |

---

## 5. Interview Cheat Sheet

### Common Questions

**Q: Why avoid Python UDFs?**

> They bypass Catalyst and cause JVM–Python overhead.

**Q: Difference between rank and dense_rank?**

> `rank()` creates gaps; `dense_rank()` does not.

**Q: Window vs groupBy?**

> Window keeps row-level granularity.

---

## Spark SQL vs DataFrame API

### (Execution, Performance, When to Use What)

---

## 1. Big Picture

Spark provides **two ways** to express transformations:

1. **Spark SQL** (declarative)
2. **DataFrame API** (programmatic)

📌 **Key fact**

> Both Spark SQL and DataFrame API compile down to the **same Catalyst-optimized execution plan**

There is **no inherent performance difference** if written correctly.

---

## 2. Conceptual Difference

| Aspect              | Spark SQL         | DataFrame API      |
| ------------------- | ----------------- | ------------------ |
| Style               | Declarative       | Imperative         |
| Language            | SQL               | Python / Scala     |
| Readability         | High for analysts | High for engineers |
| Compile-time checks | ❌                 | ❌ (PySpark)        |
| Optimization        | Catalyst          | Catalyst           |
| Execution engine    | Same              | Same               |

---

## 3. Example: Same Logic, Two APIs

### Business Requirement

> Find total revenue per country for orders > 1000

---

### 3.1 DataFrame API

```python
df = spark.read.parquet("/data/orders")

result = (
    df.filter("amount > 1000")
      .groupBy("country")
      .sum("amount")
)

result.show()
```

---

### 3.2 Spark SQL

```python
df.createOrReplaceTempView("orders")
```

```sql
SELECT
  country,
  SUM(amount) AS total_amount
FROM orders
WHERE amount > 1000
GROUP BY country
```

---

## 4. Execution Plan Comparison (Proof)

```python
result.explain(True)
```

```sql
EXPLAIN EXTENDED
SELECT country, SUM(amount)
FROM orders
WHERE amount > 1000
GROUP BY country
```

📌 **You’ll see identical physical plans**

This proves:

> SQL vs DataFrame is a **developer choice**, not a performance one.

---

## 5. When Spark SQL is Better

### Use Spark SQL When:

* Business logic is **query-heavy**
* Analysts / BI teams consume data
* Complex joins & aggregations
* Reporting & dashboards

### Example: Complex SQL Logic

```sql
SELECT
  customer_id,
  SUM(amount) OVER (
    PARTITION BY customer_id
    ORDER BY order_date
  ) AS running_total
FROM orders
```

✔ Clear
✔ Concise
✔ Analyst-friendly

---

## 6. When DataFrame API is Better

### Use DataFrames When:

* Conditional logic
* Loops / branching
* Reusable functions
* Pipeline orchestration
* Dynamic column handling

### Example: Dynamic Logic

```python
cols = ["amount", "tax", "discount"]

df.select([col(c) * 1.1 for c in cols])
```

SQL would be awkward here.

---

## 7. Mixing SQL + DataFrame (REAL Databricks Pattern)

Databricks production pipelines **mix both**.

### Example

```python
df = spark.read.table("silver.orders")
df.createOrReplaceTempView("orders")
```

```sql
CREATE OR REPLACE TEMP VIEW high_value_orders AS
SELECT * FROM orders WHERE amount > 1000
```

```python
spark.table("high_value_orders") \
     .write.format("delta") \
     .saveAsTable("gold.orders")
```

📌 **This is the most common enterprise pattern**

---

## 8. Spark SQL Internals (Important)

Spark SQL uses:

* **Catalyst Optimizer**
* **Logical → Physical plans**
* **Whole-stage code generation**

### You’ll see this in `explain()`:

```
WholeStageCodegen
```

✔ Faster execution
✔ JVM bytecode generation

---

## 9. Common Myths (Interview Traps)

❌ *“Spark SQL is slower than DataFrames”*
✅ False

❌ *“SQL can’t scale”*
✅ False

❌ *“DataFrames bypass SQL engine”*
✅ False

📌 Both go through **the same engine**

---

## 10. Production Best Practices

✅ Prefer **SQL** for:

* Aggregations
* Joins
* Window functions

✅ Prefer **DataFrames** for:

* ETL pipelines
* Reusable logic
* Conditional transformations

✅ Always:

* Check `explain()`
* Monitor Spark UI
* Optimize shuffles

---

## 11. Interview-Ready Answer

**Q: SQL vs DataFrame — which is faster?**

> Both compile to the same execution plan using Catalyst. Performance depends on query design, not API choice.

---

## Official Documentation

* Pandas vs Spark
  [https://spark.apache.org/docs/latest/sql-programming-guide.html](https://spark.apache.org/docs/latest/sql-programming-guide.html)

* Spark Joins
  [https://spark.apache.org/docs/latest/sql-performance-tuning.html#joins](https://spark.apache.org/docs/latest/sql-performance-tuning.html#joins)

* Window Functions
  [https://spark.apache.org/docs/latest/sql-ref-syntax-qry-select-window.html](https://spark.apache.org/docs/latest/sql-ref-syntax-qry-select-window.html)

* UDFs & Pandas UDF
  [https://spark.apache.org/docs/latest/sql-pyspark-pandas-with-arrow.html](https://spark.apache.org/docs/latest/sql-pyspark-pandas-with-arrow.html)


* Spark SQL Programming Guide
  [https://spark.apache.org/docs/latest/sql-programming-guide.html](https://spark.apache.org/docs/latest/sql-programming-guide.html)

* Catalyst Optimizer
  [https://spark.apache.org/docs/latest/sql-performance-tuning.html](https://spark.apache.org/docs/latest/sql-performance-tuning.html)

* Databricks SQL
  [https://docs.databricks.com/sql/index.html](https://docs.databricks.com/sql/index.html)

---
