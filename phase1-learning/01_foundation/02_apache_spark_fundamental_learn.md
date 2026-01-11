# DAY 2 (10/01/26) – Apache Spark Fundamentals

---

# Apache Spark & Databricks Core Concepts (with PySpark)

---

## 1. Spark Architecture

*(Driver, Executors, DAG)*

### 1.1 High-level Architecture

Apache Spark follows a **master–worker architecture**:

```
User Code (Notebook / Job)
        |
      Driver
        |
  Cluster Manager
        |
   Executors (Workers)
```

### 1.2 Driver

The **Driver** is the brain of a Spark application.

**Responsibilities:**

* Runs the main program
* Creates the SparkSession / SparkContext
* Converts user code into a **DAG (Directed Acyclic Graph)**
* Schedules tasks
* Collects results

**Example:**

```python
spark = SparkSession.builder.appName("Demo").getOrCreate()
```

Here, the notebook kernel is interacting with the **driver**.

---

### 1.3 Executors

Executors run on **worker nodes**.

**Responsibilities:**

* Execute tasks assigned by the driver
* Cache data in memory
* Return results back to the driver

**Key points:**

* Each executor has:

  * CPU cores
  * Memory
* Executors live for the entire job lifecycle

---

### 1.4 DAG (Directed Acyclic Graph)

A **DAG** is a logical execution plan of transformations.

**How it works:**

1. Spark records transformations
2. Builds a DAG
3. Optimizes it
4. Breaks it into **stages**
5. Executes stages as **tasks**

**Example DAG flow:**

```python
df = spark.read.csv("/data/sales.csv", header=True)
df2 = df.filter(df.amount > 1000)
df3 = df2.groupBy("region").sum("amount")
df3.show()
```

**DAG:**

```
Read CSV → Filter → GroupBy → Action (show)
```

**Stages:**

* Stage 1: Read + Filter
* Stage 2: Shuffle + Aggregation

📌 **Shuffle happens when data moves across executors (e.g., groupBy, join)**

---

### 🔗 Official Links

* [https://spark.apache.org/docs/latest/cluster-overview.html](https://spark.apache.org/docs/latest/cluster-overview.html)
* [https://spark.apache.org/docs/latest/job-scheduling.html](https://spark.apache.org/docs/latest/job-scheduling.html)
* [https://docs.databricks.com/compute/spark/spark-architecture.html](https://docs.databricks.com/compute/spark/spark-architecture.html)

---

## 2. DataFrames vs RDDs

### 2.1 RDD (Resilient Distributed Dataset)

RDD is the **low-level abstraction** in Spark.

**Characteristics:**

* Immutable
* Distributed
* Fault-tolerant
* No schema
* No query optimization

**Example (RDD):**

```python
rdd = spark.sparkContext.parallelize([1, 2, 3, 4, 5])
rdd_squared = rdd.map(lambda x: x * x)
print(rdd_squared.collect())
```

**Output:**

```
[1, 4, 9, 16, 25]
```

---

### 2.2 DataFrame

DataFrame is a **higher-level abstraction** built on RDDs.

**Characteristics:**

* Structured (schema)
* Optimized using **Catalyst Optimizer**
* Uses **Tungsten** (memory & CPU optimization)
* Supports SQL
* Faster and safer

**Example (DataFrame):**

```python
data = [("Alice", 25), ("Bob", 30)]
df = spark.createDataFrame(data, ["name", "age"])
df.filter(df.age > 26).show()
```

---

### 2.3 Comparison Table

| Feature      | RDD    | DataFrame  |
| ------------ | ------ | ---------- |
| API Level    | Low    | High       |
| Schema       | No     | Yes        |
| Optimization | ❌      | ✅ Catalyst |
| Performance  | Slower | Faster     |
| SQL Support  | ❌      | ✅          |
| Ease of Use  | Harder | Easier     |

📌 **In Databricks, always prefer DataFrames unless low-level control is needed**

---

### 🔗 Official Links

* [https://spark.apache.org/docs/latest/rdd-programming-guide.html](https://spark.apache.org/docs/latest/rdd-programming-guide.html)
* [https://spark.apache.org/docs/latest/sql-programming-guide.html](https://spark.apache.org/docs/latest/sql-programming-guide.html)
* [https://docs.databricks.com/spark/latest/dataframes-datasets.html](https://docs.databricks.com/spark/latest/dataframes-datasets.html)

---

## 3. Lazy Evaluation

### 3.1 What is Lazy Evaluation?

Spark **does not execute transformations immediately**.
It waits until an **action** is called.

**Why?**

* Enables optimization
* Avoids unnecessary computation
* Improves performance

---

### 3.2 Transformations vs Actions

#### Transformations (Lazy)

* `select`
* `filter`
* `map`
* `groupBy`

#### Actions (Trigger execution)

* `show()`
* `collect()`
* `count()`
* `write()`

---

### 3.3 Example

```python
df = spark.read.csv("/data/sales.csv", header=True)

# No execution yet
df_filtered = df.filter(df.amount > 1000)

# Still no execution
df_grouped = df_filtered.groupBy("region").sum("amount")

# Execution happens here
df_grouped.show()
```

📌 Spark builds a DAG but **executes only at `show()`**

---

### 3.4 Visual Explanation

```
Transformations → DAG → Optimizer → Action → Execution
```

---

### 🔗 Official Links

* [https://spark.apache.org/docs/latest/rdd-programming-guide.html#lazy-evaluation](https://spark.apache.org/docs/latest/rdd-programming-guide.html#lazy-evaluation)
* [https://docs.databricks.com/spark/latest/spark-concepts.html](https://docs.databricks.com/spark/latest/spark-concepts.html)

---

## 4. Databricks Notebook Magic Commands

Magic commands allow **multiple languages in the same notebook**.

---

## 4.1 `%python`

Used to run **Python / PySpark code**.

```python
%python
df = spark.range(1, 6)
df.show()
```

---

## 4.2 `%sql`

Used to run **Spark SQL queries** directly.

```sql
%sql
SELECT count(*) FROM sales WHERE amount > 1000
```

**Using DataFrame as SQL table:**

```python
%python
df.createOrReplaceTempView("sales")
```

---

## 4.3 `%fs`

Used to interact with **Databricks File System (DBFS)**.

### List files

```bash
%fs ls /databricks-datasets
```

### Read file

```bash
%fs head /databricks-datasets/README.md
```

---

## 4.4 Common Magic Commands

| Command   | Purpose                |
| --------- | ---------------------- |
| `%python` | Run PySpark            |
| `%sql`    | Run SQL                |
| `%fs`     | File system operations |
| `%md`     | Markdown               |
| `%sh`     | Shell commands         |

---

### 🔗 Official Links

* [https://docs.databricks.com/notebooks/notebooks-code.html](https://docs.databricks.com/notebooks/notebooks-code.html)
* [https://docs.databricks.com/files/index.html](https://docs.databricks.com/files/index.html)
* [https://spark.apache.org/docs/latest/sql-programming-guide.html](https://spark.apache.org/docs/latest/sql-programming-guide.html)

---

## 📌 Summary

* **Driver** plans, **Executors** execute
* **DAG** optimizes execution
* **DataFrames > RDDs** for most use cases
* **Lazy evaluation** boosts performance
* **Magic commands** enable multi-language workflows in Databricks

---
