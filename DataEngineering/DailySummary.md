# Day 1: Storage (data lakes, warehouses) and schema design

## Fundamental GCP components
Jupyter and Colossus enable separation of compute and storage
1. Colossus File System = distributed file system storage. Ensures durability by using erasure encoding to store redundant chunks of data on multiple physical disks. Data is also replicated to multiple data centers.
2. Jupiter network (1 PB/s of bandwidth communication between compute and storage - Process the data where it is stored without copying it)
3. Borg:  Cluster OS Google uses for managing internal workloads; Manages the lifecycles of tasks and machines. https://www.quora.com/What-is-Google-Borg-system
4. Dremel: execution management; Highly available cluster compute

## Storage 
* Cloud Storage (~ S3): object storage
    * simulates a file system but actually just long object names
* Cloud SQL (~RDS): fully managed RDBMS - single source, high throughput writes
* BigQuery (~Serverless Redshift + Athena): high read data warehouse. Column based. Storage (Collosus) > Jupiter network > Compute (Borg/Dremel). Fast transfer between BQ storage and compute

## Partitioning and clustering - BigQuery
* **Partitioning**: Filtering storage before query execution
begins on date, time or integer. Reduces a full table scan to the
partitions specified.
* **Clustering**: Storage optimization within columnar segments to improve filtering and record colocation. Best results on high cardinality columns

## Denormalised data structures
* Normalization: going from one table to many
* Denormalisation: joining many tables into one
* Nested and Repeated Fields allow you to have multiple levels of data granularity, and potential performance uplift by avoiding joins
* ARRAY values give you the flexibility to go deep into the granularity of your fields
* STRUCTS are a data type that allows you to go wide in your schema by grouping related fields together.

## Other Serivces
* DLP API (data loss prevention): Find PII in data programatically in text, images, videos https://cloud.google.com/dlp
* Cloud Data Catalogue: Metadata managed service - data governance
* Cloud Composer: Managed orchestration service on top of Apache Airflow
* gcloud is the CLI tool for GCP
* gsutils for creating buckets and moving data in Cloud Storage from the CLI


# Day 2: Data processing - batch data pipelines

## Jupyter magic
The `%%writefile spark_analysis.py` Jupyter magic command creates a new output file to contain code in the cell it it applies to

## Services / Products
* Cloud Dataproc: managed Spark and Hadoop service (~EMR)
* Apache Beam: open source platform for executing data processing workflows. Programming model that can process batch and stream data with the same pipeline: Apache BEAM = Batch + strEAM
* Cloud Dataflow: Pipeline execution (batch and realtime) managed service (runs pipelines written using Apache Beam.)
* Cloud Data Fusion: Build data pipelines with visual drag and drop UI
* Cloud Composer: Orchestrate work between GCP services using Apache Airflow




# Day 3: Streaming data pipelines
* Accessing BigQuery data from a Jupyter notebook: 
https://cloud.google.com/bigquery/docs/visualize-jupyter

## Services / Products
* Data Studio: Google's open source live dashboard viz tool https://datastudio.google.com/u/0/navigation/reporting
* Cloud Pub/Sub: Data distribution and messaging delivery system (~SNS)
* Cloud Spanner: Globally consistent SQL data base that scales (horizontally) across regions
* BigQuery Geo Viz (beta):  web tool for visualization of geospatial data in BigQuery using Google Maps APIs. https://cloud.google.com/bigquery/docs/gis-visualize
* Cloud Bigtable: Fully managed, scalable NoSQL database with low latency. Efficient for certain types of use cases; choice of single index has direct performance implications


# Day 4
* ML experiments with source code on GCP: https://experiments.withgoogle.com/
* GCP Python client library: https://github.com/googleapis/google-cloud-python#google-cloud-python-client
* Use ML on GCP using either
    * AI Platform (your model, your data)
    * AutoML (our models, your data)
    * Pretrained models (our models, our data)
        * Vision, Natural Language, Translation, Dialogueflow etc

## Services / Products
* Cloud AI Platform: fully managed service for custom machine learning models (serverless)
    * NLP AI: https://cloud.google.com/natural-language/docs/categories
    * Text category classifcation: https://cloud.google.com/natural-language/docs/classify-text-tutorial
* Kubeflow: Open source. ML Pipelines that leverage Kubernetes clusters built