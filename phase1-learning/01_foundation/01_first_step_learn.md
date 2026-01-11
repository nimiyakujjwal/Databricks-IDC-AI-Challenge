# DAY 1 (09/01/26)– Platform Setup & First Steps
## Databricks vs Pandas:

| Aspect            | Databricks (Spark)                          | Pandas                                 |
|-------------------|---------------------------------------------|----------------------------------------|
| Scale             | Distributed, handles big data                | Single machine, limited by RAM         |
| Performance       | Optimized for parallel processing            | Slower for large datasets              |
| Ease of Use       | Requires cluster setup, notebooks            | Simple, easy for small data            |
| API               | Spark DataFrame API, SQL, MLlib              | Python DataFrame API                   |
| Cost              | Cloud-based, pay for compute                 | Free, local resources                  |
| Integration       | Integrates with cloud, ML, BI tools          | Integrates with Python ecosystem       |
| Fault Tolerance   | Built-in via Spark                           | None                                   |

**Advantages of Databricks over Pandas:**
- Handles large-scale data
- Distributed computing
- Fault tolerance

**Disadvantages:**
- More complex setup
- Higher cost

---
## Databricks vs Hadoop:

| Aspect            | Databricks (Spark)                          | Hadoop (MapReduce)                     |
|-------------------|---------------------------------------------|----------------------------------------|
| Processing Model  | In-memory, fast                             | Disk-based, slower                     |
| Ease of Use       | Notebooks, interactive                      | Batch jobs, less interactive           |
| API               | High-level APIs (SQL, DataFrame, MLlib)      | Low-level Java APIs                    |
| Performance       | Faster for iterative workloads               | Slower for iterative workloads         |
| Cost              | Cloud-based, pay for compute                 | Can be on-prem or cloud                |
| Fault Tolerance   | Built-in via Spark                           | Built-in via HDFS                      |
| Integration       | ML, BI, cloud services                       | Ecosystem (Hive, Pig, HBase)           |

**Advantages of Databricks over Hadoop:**
- Faster, in-memory processing
- Easier to use, interactive notebooks
- Richer APIs for data science

**Disadvantages:**
- May be more expensive in cloud
- Less mature for some legacy batch workloads

---
## Lakehouse architecture basics
### What is Lakehouse Architecture?
The Lakehouse architecture is a modern data platform design that combines the best features of data lakes and data warehouses. It enables organizations to store vast amounts of raw data inexpensively (like a data lake), while also supporting high-performance analytics and business intelligence (like a data warehouse). Lakehouse platforms, such as Databricks, unify data engineering, data science, and analytics workflows in a single system.

### Advantages over Other Systems
* Combines low-cost storage of data lakes with the reliability and performance of data warehouses.
* Supports both structured and unstructured data, enabling advanced analytics and machine learning.
* Simplifies data management by reducing the need for multiple systems and complex data pipelines.
* Provides ACID transactions, schema enforcement, and governance, which are often missing in traditional data lakes.

### Key Features of Lakehouse Architecture
* **Unified Storage:** Stores all data (structured, semi-structured, unstructured) in open formats, typically on cloud object storage.
* **ACID Transactions:** Ensures data reliability and consistency with transactional guarantees, making it suitable for concurrent workloads.
* **Schema Enforcement & Evolution:** Supports data validation and flexible schema changes, improving data quality and adaptability.
* **Data Versioning:** Tracks changes over time, enabling reproducibility and rollback for analytics and machine learning.
* **Performance Optimization:** Uses indexing, caching, and advanced query engines to deliver fast analytics on large datasets.
* **Governance & Security:** Integrates with enterprise security and governance tools for access control, auditing, and compliance.
* **Support for BI & ML:** Enables both business intelligence reporting and machine learning workflows on the same platform.

By leveraging these features, Lakehouse architecture streamlines data operations, reduces costs, and accelerates insights for organizations.

---

## Databricks workspace structure

The Databricks workspace is an integrated environment designed for collaborative data analytics, engineering, and machine learning. It organizes resources and tools to streamline workflows for individuals and teams.

**Key Components of Databricks Workspace:**

 Component                | Description                                                                                                   |
--------------------------|---------------------------------------------------------------------------------------------------------------|
 **Home Page**            | Starting point for users, showing recent activity, favorite assets, and personalized recommendations.         |
 **Workspace Folders**    | Hierarchical directories for storing notebooks, libraries, queries, dashboards, and files. Private or shared. |
 **Notebooks**            | Interactive documents supporting Python, SQL, Scala, and R for data exploration, analysis, and development.   |
 **Dashboards**           | Visualizations and reports from queries and notebooks for data-driven decision-making.                        |
 **Data Catalog (Unity Catalog)** | Centralized governance for data assets, organizing tables, views, and volumes by catalog, schema, and table. Supports fine-grained access control and lineage tracking. |
 **Jobs**                 | Automated workflows for running notebooks, scripts, or pipelines on a schedule or in response to events.      |
 **Clusters & Compute**   | Managed Spark clusters and serverless compute resources for scalable data processing.                         |
 **Repos**                | Git-integrated repositories for version control and collaborative development.                                |
 **SQL Editor**           | Interface for writing, running, and sharing SQL queries, with access to query history and results.            |

### AI/ML Components in Databricks Workspace

 Component                | Features                                                                                                      | Usage                                                                                           |
--------------------------|---------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------|
 **MLflow**               | Experiment tracking, model management, reproducibility, deployment tools.                                     | Track ML experiments, log metrics, manage models, deploy to production.                         |
 **Model Registry**       | Model versioning, stage transitions (Staging, Production), lineage, access control.                           | Register models, manage lifecycle, promote/demote models, enable collaboration and governance.  |
 **Feature Store**        | Centralized feature management, feature sharing, lineage, point-in-time correctness, governance.              | Create, discover, and reuse features for ML models, ensure consistency across teams/projects.   |
 **Lakehouse AI**         | Generative AI tools, LLM APIs, scalable training/inference, integration with data and ML workflows.           | Build, train, and deploy generative AI and LLM applications, leverage unified data platform.    |
 **AutoML**               | Automated model selection, hyperparameter tuning, feature engineering, experiment tracking.                    | Quickly build and evaluate ML models with minimal code, accelerate prototyping and benchmarking.|
 **ML Runtime**           | Optimized ML libraries, pre-installed frameworks (TensorFlow, PyTorch, XGBoost), scalable distributed training.| Run ML workloads efficiently, leverage built-in libraries and scalable compute resources.        |
 **Serving Endpoints**    | Real-time model serving, REST API endpoints, autoscaling, monitoring, security controls.                      | Deploy ML models for real-time inference, integrate with applications via APIs.                 |
 **Experiment UI**        | Visual interface for tracking experiments, comparing runs, analyzing metrics and parameters.                   | Monitor and compare ML experiments, visualize results, facilitate collaboration.                |

This structure enables secure, scalable, and collaborative analytics across the organization, supporting both ad hoc exploration and production-grade data pipelines.

---
## Industry Use Cases of Databricks

| Company  | Use Case Summary |
|----------|------------------|
| **Netflix** | Leverages Databricks for large-scale data analytics and machine learning to personalize content recommendations, optimize streaming quality, and analyze user engagement. Enables rapid experimentation and scalable processing of billions of daily events, supporting real-time insights and improved customer experience. Reference: [Netflix Case Study on Databricks](https://databricks.com/customers/netflix) |
| **Shell** | Uses Databricks to accelerate energy exploration, production optimization, and predictive maintenance. Unifies data from sensors, IoT devices, and operational systems to apply advanced analytics and AI for equipment failure detection, drilling optimization, and downtime reduction, driving efficiency and sustainability. Reference: [Shell Case Study on Databricks](https://databricks.com/customers/shell) |
| **Comcast** | Utilizes Databricks for customer analytics, network optimization, and fraud detection. Processes massive volumes of streaming and transactional data, enabling real-time insights into customer behavior, proactive service improvements, and enhanced security across its telecommunications infrastructure. Reference: [Comcast Case Study on Databricks](https://databricks.com/customers/comcast) |
