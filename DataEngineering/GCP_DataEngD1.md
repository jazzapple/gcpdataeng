# GCP Data Engineering

## Admin
* http://googlecloud.qwiklabs.com/
* Slides available for 2 years
* Labs open until 9th July 2020
* Lab instructions remain for 2 years so could run on GCP free tier

## Resources
* BigQuery Free sandbox: https://cloud.google.com/bigquery/docs/sandbox?hl=en_US
* BigQuery Function reference: https://cloud.google.com/bigquery/docs/reference/standard-sql/string_functions
* Uptime SLA calculator: https://uptime.is/
* gsutil for creating buckets and moving data in CloudStorage from the CLI
* App Engine: Build highly scalable applications on a fully managed serverless platform https://cloud.google.com/appengine
* Common BQ UDFs on Github: https://github.com/GoogleCloudPlatform/bigquery-utils/tree/master/udfs/community
* Github repo for demos: https://github.com/GoogleCloudPlatform/training-data-analyst/tree/master/courses/data-engineering/demos
* Architecture recipes: https://gcp.solutions/


# Intro to Data Eng

## Layers of learning on GCP
* Architecture with GCE: 3 days course covering 40-50 services relating to base architecture
* DE course: 15 services to be an effective data engineer
* DE Exam: Cloud functions, no case studies. Online. $200 USD
    * 50 Qs, 2 hours         
    * Not yet subsidised by NAB
    * Cloud Composer: 3 Qs
    * BigQuery: 7 Qs


## Google CLoud vs GCP
* Google Cloud includes Google Maps, GSuite (E.g. Drive, Meet, Sheets, Active Directory for user management)
* GCP: Cloud platform for storage and compute

## Data Lakes
* Cloud storage
* Key considerations:
    * Any data format
    * Scale to meet demand
    * High throughput ingestion
    * fine-grained access control
    * Connectivity with other tools

## Block vs object storage
* Object storage (e.g. CloudStorage, S3). Elastic. Cannot incrementally edit one part of a file. Works well for unstructured data sets where data is generally written once and read once or many times.
* Block storage = Disk. Can incrementally edit one part of a file. Because block level storage devices are accessible as volumes and accessed directly by the operating system, they can perform well for use cases like structured database storage, random read/write loads, and virtual machine file system (VMFS) volumes.
* Durability - data is preserved
* Availability - data is available for retrieval

## ETL Services
* Batch
    * Cloud Dataproc
    * Cloud Dataflow
* Streaming
    * Cound Pub/Sub > Cloud Dataflow > BigQuery

## BigQuery
* Google's data warehouse solution and Analytics engine
* Serverless, minimal set up compared to HDFS and Redshift
* ANSII SQL queries
* BQML enables ML training with SQL commands
* BI Engine for dashboard reporting
* Free sandbox: https://cloud.google.com/bigquery/docs/sandbox?hl=en_US
* Function reference: https://cloud.google.com/bigquery/docs/reference/standard-sql/string_functions
* Enables query data direcly from GCS (Cloud Storage)
* Ingesting batch data into BQ is free. Daily batch job limit is 1500.     
    * Streaming Inserts is charged    
    * Processing is charged. Free tier provides 1TB/month free

## Using BigQuery
1. Create a project. Project dictates the environment (top left of console)
2. Create a dataset
3. Create tables and views by specifying source
* Ways to build solutions:
    1. CLI (Top right of console - Cloud Shell with Google SDK installed, including gcloud. Blocked by NAB firewall)
    2. Console
    3. Mobile app (recommended not to use)
    4. REST APIs


## Fundamental GCP components
Jupyter and Colossus enable separation of compute and storage
1. Colossus File System = distributed file system storage. Ensures durability by using erasure encoding to store redundant chunks of data on multiple physical disks. Data is also replicated to multiple data centers.
2. Jupiter network (1 PB/s of bandwidth communication between compute and storage - Process the data where it is stored without copying it)
3. Borg:  Cluster OS and container management system Google uses for managing internal workloads; Manages the lifecycles of tasks and machines. https://www.quora.com/What-is-Google-Borg-system
4. Dremel: execution management; Highly available cluster compute. Query engine used in BQ - fast query of petabytes of info

## Cloud Shell
* Cloud Shell is a virtual machine that is loaded with development tools. 
* Provides CLI access to GCP resources
* gcloud is the command-line tool for Google Cloud. It comes pre-installed on Cloud Shell and supports tab-completion
* Reference: https://cloud.google.com/sdk/gcloud
```
# List the active account name with this command:
gcloud auth list

# list the project ID with this command:
gcloud config list project
```

## Cloud Data Catalogue
* Metadata managed service - data governance

## DLP API (data loss prevention)**
* Find PII in data programatically in text, images, videos
https://cloud.google.com/dlp

## Cloud Composer
* Managed orchestration service - Apache Airflow

## Transfer Service
* Transfer data from other clouds or on premise onto GCP

# Building Data Lakes

## Data lake vs Data Warehouse
* A data lake is a capture of every aspect of your business
operation. The data is stored in its natural/raw format, usually as object blobs or files.
    * Retain all data in its native format
    * Support all data types and all users
    * Adapt to changes easily
    * Tends to be application-specific
* Data Warehouse: 
    * Typically loaded only after a use case is defined
    * Processed/organized/transformed
    * Provide faster insights
    * Current/historical data for reporting
    * Tends to have consistent schema shared across applications

## Cloud Storage
* Data lake solution - object based storage
* Use gsutils to create storage objects
* Supports versioning via generation numbers so that multiple versions of an object are tracked and available if necessary.
* Simulates a file system via single long object name 
    * gcsfuse is an open source resource that allows you to mount a filesystem-like structure on a bucket (note that it's not an actual file system like EFS on AWS)
* Global namespace
    * cannot nest buckets. Only folders
    * objects are stored in buckets, not files
    * best practice is to avoid the use of sensitive information as part of bucket names because bucket names are in a global namespace.
* data is replicated for high durability
* 2 key aspects when creating storage:
    * bucket location: can be regional, replicated across regions (multi regional), dual region (US and Euro only)
    * storage classes: choose based on how long you plan to store and how often it will be accessed - Standard, Nearline, Coldline, Archive. 
        * Cost implications - standard most expensive
        * Minimum time committment to store data is different
        * Underlying technology same (Collossus File System)
        * Retrieval time same 


### Access control
* Access Control List (ACL) applies access on an object level but high maintenance
* Instead, use Cloud IAM for best pratice **
    * Role = set of permissions
    * Role can be assigned to a policy
    * Policies bind roles to users

### Data Encryption
* GCP encrypts data at rest and transit as a mandatory requirement
* Options: 
1. GMEK: Google-managed encryption keys
    * Cloud KMS key management service that encrypts automatically
2. CMEK: Customer-managed encryption keys
    * You control creation and existnece of KEK key in Cloud KMS
3. CSEK: Customer-suplied encryption keys - most popular
    * You provide the KEK key


# Building a Data Warehouse
* What is it? Consolidates data from many sources which has been transformed to a specified schema and optimised for high-speed query performance

## BigQuery
* Services make it an ideal data warehouse
    1. Fast SQL engine
    2. Managed storage
* data source naming format: ```<project>.<dataset>.<table>```
* BQ does not use indexes
* Serverless - no infrastructure needs to be created to use
* in Cloudshell, use bq SDK to access BQ ```bq ls -a```
* pay for **data processed**

### BQ Architecture
* Compute and storage separated by Jupiter network. 
    * Jupiter network transfer between compute and storage is REALLY fast (1 petabit per second)
    * Process the data where it is without copying it
    * Storage (Collosus) > Jupiter network > Compute (Borg/Dremel)
* Stores data in columnar format (OLAP)- each column is stored in a proprietary columnar format called Capacitor
    * leverages the fact that most queries involve few columns, and so it only reads the columns required for the query.
    * capacitors files are grouped together into "storage sets"
    * metadata about storage sets is stored within Spanner DB
    * data is physically stored on Google's distributed file system, called Colossus
    * capacitors optimise how the data is stored (length-encoded and
dictionary-encoded) so that it can reduce the amount of data read
* "slots" are units of CPU, memory, network resource
    * on demand and flat rate options available
    * on demand (not recommended): 2000 slots available 
        * fair scheduling of slots equally to queries submitted, dynamically on-demand
        * till then, slots will be queued
        * pricing calculator for queries can tell you cost of query
    * flat rate plan (recommended) - buying slots upfront
* "Anonymous" datasets are results of the query are cached for 24 hours after query is run, if "cache results" option is ticked. This is accessed if the query is run again (underscore before name when you run ```bq ls -a```)
* can also run BQ queries on data outside of BQ storage, e.g. data stored in Cloud Storage, Google Drive, or Cloud Bigtable, by using federated data sources. However, these sources are not optimized for BQ operations, so they might not perform as well as data stored in BQ storage.

### BQ Project structure
* Datasets belong to a project
* Namespace is global within a project 

### BQ Access control
* BQ access to run a query managed via Cloud IAM
* Access control granularity at dataset level, but table level access (Table ACL) currently in beta

### Loading data into BigQuery 
* Loading data into BQ is free    
    * Processing is charged
* Choosing the method dpends on how the data will be used:
    1. EL is used when data is imported as-is where the source and target have the same schema.
    2. ELT is used when raw data will be loaded directly into the target and transformed there.
    3. ETL is used when transformation occurs in an intermediate service before it is loaded into the target.
* Use BigQuery Data Transfer Service (DTS) if the data is usable in its original form
* API (mainly used from either Cloud Dataproc or Cloud Dataflow)
* can set up Cloud Functions to listen to a Cloud Storage event that is associated with new files arriving in a given bucket and launch a BigQuery load job.
* load via Cloud Storage using gsutil 
    ```
    > gsutil -m cp *.csv gs://mybucket
    # Append to an existing table
    > bq load \
        --source_format=CSV \
        --autodetect \
        --noreplace  \
        nyctaxi.2018trips \
        gs://cloud-training/OCBL013/nyc_tlc_yellow_trips_2018_subset_2.csv
    ```

### BQ supports UDFs
* Creates as an object within a dataset which can be persisted
    ```
    CREATE FUNCTION <your-dataset>.ParseJsonAsStruct(json_str STRING)
    RETURNS STRUCT<x INT64, y STRING, z ARRAY<FLOAT64>> LANGUAGE js AS '''
    var obj = JSON.parse(json_str);
    return obj;
    ''';
    ```
* https://github.com/GoogleCloudPlatform/bigquery-utils/tree/master/udfs/community
* DML queries are not suggested within BQ (expensive) - recommend transformation of data before loading into BQ

### BQ "time travel" query functionality
* Allows you to query a table as it was at a previous point in time. Useful for streaming data when data is being added in real time. 
```sql
-- returns rows from the average_speeds table that existed at 10 minutes ago
SELECT *
FROM `demos.average_speeds`
FOR SYSTEM_TIME AS OF TIMESTAMP_SUB(CURRENT_TIMESTAMP, INTERVAL 10 MINUTE)
ORDER BY timestamp DESC
```

## Schema Design

### Normalisation / denormalisation
* normalization (going from one table to many). Common approach for transactional databases like mySQL.
* denormalization: For data warehousing, data analysts often go the reverse direction and bring many separate tables into one large reporting table.
* Data can be stored at different levels of granularity all in one table using repeated fields - arrays

### General guidelines to design the optimal schema for BigQuery
* Instead of joins, take advantage of nested and repeated fields in denormalized tables.
* Keep a dimension table smaller than 10 GB normalized, unless the table rarely goes through UPDATE and DELETE operations.
* Denormalize a dimension table larger than 10 GB, unless data
manipulation or costs outweigh benefits of optimal queries.

As a dataset’s tables increase in size, the performance impact of a join increases. At some point, it can be better to denormalize your data. The crossover point is around
10 GB. If your tables are less than 10 GB, keep the tables separate and do a join.

### Arrays and Structs - ways to denormalise tables
* Nested and Repeated Fields allow you to have multiple levels of data granularity
    * Store complex data with nested fields (ARRAYS)
    * Report on all data in once place with STRUCTS Event
    * STRUCTS are like pre-joined tables within a table.
* Normalise / de-normalise storage decision depends on results of performance testing
* Storing your large reporting tables as STRUCTs (pre-joined "tables") and ARRAYs (deep granularity) allows you to:
 1. gain significant performance advantages by avoiding multiple table JOINs
 2. get granular data from ARRAYs when you need it but not be punished if you dont (BQ stores each column individually on disk)
 3. have all the business context in one table as opposed to worrying about JOIN keys and which tables have the data you need
* STRUCTs (and ARRAYs) must be unpacked before you can operate over their elements. Wrap an ```UNNEST()``` around the name of the struct itself or the struct field that is an array in order to unpack and flatten it. 

#### Arrays
* Array schema: Schema mode = "REPEATED" ()
* To query tables that contain arrays, must first break the arrays back into rows. 
    * Use ```UNNEST(<array>)``` to bring the array elements back into rows
    * UNNEST() always follows the table name in your FROM clause (think of it conceptually like a pre-joined table)
    ```
    SELECT DISTINCT
            visitId,
            h.page.pageTitle
    FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`,
    UNNEST(hits) AS h
    WHERE visitId = 1501570398
    LIMIT 10
    ```
* Data in an array must all be the same type
* The BigQuery Public Dataset for Google Analytics  ```bigquery-public-data.google_analytics_sample``` stores field values like products, pages, and transactions natively as ARRAYs.
* Create arrays with ```ARRAY_AGG(columname)```
* Get number of array elements ```ARRAY_LENGTH(<array>)```
* deduplicating elements with ```ARRAY_AGG(DISTINCT <field>)```
* order elements with ```ARRAY_AGG(<field> ORDER BY <field>)```
* limiting number of elements ```ARRAY_AGG(<field> LIMIT 5)```
```
SELECT
  fullVisitorId,
  date,
  ARRAY_AGG(v2ProductName) AS products_viewed,
  ARRAY_LENGTH(ARRAY_AGG(v2ProductName)) AS num_products_viewed,
  ARRAY_AGG(pageTitle) AS pages_viewed,
  ARRAY_LENGTH(ARRAY_AGG(pageTitle)) AS num_pages_viewed
  FROM `data-to-insights.ecommerce.all_sessions`
WHERE visitId = 1501570398
GROUP BY fullVisitorId, date
ORDER BY date
```

#### Structs
* Struct schema: Type = "RECORD"
* Field alias example: ```hit.page.pageTitle```, ```hit```
* A STRUCT can have another STRUCT as one of its fields (you can nest STRUCTs)
* Data types that "go wide" in your schema by grouping related fields together. 
    * allows you to run queries like this one without having to do any JOINs
* The easiest way to think about a STRUCT is to consider it conceptually like a separate table that is already pre-joined into your main table.
* A STRUCT can have:
    * one or many fields in it
    * the same or different data types for each field
    * it's own alias
* Query example of a table containing STRUCT data types (totals, device). The .* syntax tells BQ to return all fields for that STRUCT (much like it would if totals.* was a separate table we joined against)
    ```
    SELECT
    visitId,
    totals.*,
    device.*
    FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`
    WHERE visitId = 1501570398
    LIMIT 10
    ```

### Arrays + Structs
* Structs are containers that can have multiple field names and data types nested inside.
* arrays can be one of the field types inside of a Struct
* Create a struct with array
    ```
    SELECT STRUCT("Rudisha" as name, [23.4, 26.3, 26.4, 26.1] as splits) AS runner
    ```
    | row | runner.name | runner.splits |
    |-----|-------------|---------------|
    | 1   | Rudisha     | 23.4          |
    |     |             | 22.6          |
    |     |             | 21.4          |
    


### Partitioning
* Partitioning can improve query cost and performance by reducing data being queried: https://github.com/GoogleCloudPlatform/training-data-analyst/blob/master/courses/data-engineering/demos/partition.md
* Table Details will show if table is partitioned
* A partitioned table maintains that all data in a block belong to the same partition, across all operations that modify it: query jobs, DML statements, DDL statements, load jobs, and copy jobs. This requires BigQuery to maintain more metadata than a non-partitioned table. As the number of partitions increases, the amount of metadata overhead increases.
* BigQuery supports three ways of partitioning tables
1. Ingestion time
    ```
    bq query --destination_table mydataset.mytable
    --time_partitioning_type=DAY
    ```
2. Any column that is of type DATE or TIMESTAMP
    ```
    bq mk --table --schema a:STRING,tm:TIMESTAMP
    --time_partitioning_field tm
    ```
3. Integer-typed column 
    ```
    bq mk --table --schema "customer_id:integer,value:integer"
    --range_partitioning=customer_id,0,100,10 my_dataset.my_table
    ```
* SQL: The PARSE_DATE function is used on the date field (str) to transform to DATE type for partitioning.
    ```SQL
    CREATE OR REPLACE TABLE ecommerce.partition_by_day
    PARTITION BY date_formatted
    OPTIONS(
    description="a table partitioned by date"
    ) AS
    SELECT DISTINCT
    PARSE_DATE("%Y%m%d", date) AS date_formatted,
    fullvisitorId
    FROM `data-to-insights.ecommerce.all_sessions_raw`
    ```
* Un-assigned records will either be sent to "Null" partition or "unpartitioned" partition


### Clustering
* Requires a partitioned table
* Clustering can improve the performance of certain types of queries, such as queries that use filter clauses and those that aggregate data.
* works best on high cardinality columns
* The columns you specify in the cluster are used to sort the column values and co-locate related data in multiple blocks. 
* When a query is submitted containing a clause that filters data based on the clustering columns, BQ uses the sorted blocks to eliminate scans
of unnecessary data.
* Similarly, when a query is submitted that aggregates data based on the values in the clustering columns, performance is improved because BQ jumps to the row range and read only those rows for each of the columns needed.
* When you cluster a table using multiple columns, the order of columns you specify is important. The order of the specified columns determines the sort order of the data.
* BQ automatically re-clusters data as it becomes less sorted with modifictaions
```
CREATE TABLE mydataset.myclusteredtable
(
c1 NUMERIC,
userId STRING,
c3 STRING,
eventDate TIMESTAMP,
C5 GEOGRAPHY
)
PARTITION BY DATE(eventDate)
CLUSTER BY userId
OPTIONS (
partition_expiration_days=3,
description="cluster")
AS SELECT * FROM mydataset.myothertable
```