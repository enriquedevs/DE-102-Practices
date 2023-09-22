# Data Engineering Services on Cloud Providers

## Create a Cloud Account

A Version of these steps is also available with images [here][md_img]

For this course we will be using mostly a Google Cloud Platform (GCP) free account. Follow the steps to setup your account.

Notes:
>During registration is recommended fill the information with your personal data, including your personal email, this account will belong to you and you should be able to cancel any time
>
>During your registration read closely, up today: the free trial gives you $300 USD for 3 months, but this may change at any point in time
>
>All credentials, API keys, are personal, do not share them or post them in your github projects, if required for cloud actions, ensure credentials are always stored as secrets

### Pre-requirements

- Google account
- Credit/Debit Card

### Steps

- Go to [GCP site][gcp]
- Login:
  - At the top Right of the screen you should see the Sign in
  - Introduce you email/password
  - Now you should see your photo instead of the login
- Start your trial
  - Click on *Start free* button at the top right
  - Fill the form:
    - Ensure your google account is correct
    - Country (Same as your credit/debit card)
    - Needs (Recommended other)
    - Agree T&C
    - Click Continue
  - Fill the form:
    - Account type: Individual (Recommended)
    - Name (in UPPER CASE)
    - Address
    - Neighborhood
    - Postal Code
    - City
    - State
    - Card Number
    - Expiration date
    - CVC
    - Cardholder name
    - Click "Start my free trial"

>At this point you should see the welcome page, no charges will be sent to your card unless you activate the "full account"

## Exercise

In the previous lesson we saw some service equivalents for [On-Premises / AWS / GCP][services equivalences]; using that chart, your knowledge and external services you know: migrate the following pipeline into a GCP environment.

>Focus on how the services are equivalent (giving a brief service description) and investigate if any of the GCP services does something additional.
>
>Assume the programming language for the internal processes is Python or TS at your convenience.
>
>If possible list more than 1 alternatives for the migration stack.

### Data input

- Schedule for scrapping the NikeAPI to get:
  - Daily sales data after all stores are closed
  - Weekly product data, such as name, brand...
- Real time data is posted for real time report (this data is not required to be on the Datawarehouse, but it's important for daily reports)

### Architecture

- Airflow instance with all the dependencies (scheduler, database, workers) in a local company server
  - This same server have additional pods to orchestrate paralel task executions for Airflow tasks (each individual step is a DAG)
- Dedicated HDFS server to work as a temporary server for intermediate steps and as a Data Lake
- HDFS Server as DataWarehouse
- Mongo DB Server as Report database
- ElasticSearch as Log Database
- You also have a custom WebApp that shows a real time monitor for:
  - Errors during ETL & HTTPS processing
  - Records handled
  - Sales per hour

### Process (Just for context: how it works)

- Airflow handles Daily & weekly schedules
  - Based received will create 2 temporary files: product/sales and will create subfolders based on the yyyy/mm/dd of the record
  - Will trigger a thread for each file created:
    - Sales files will be transformed, normalized and add incomplete information from existing catalogs then uploaded to the fact/dimension tables
    - Product files will be normalized and uploaded to the dimension tables
  - Once the thread is complete:
    - Temporary files will be moved to the HDFS server as a datalake
    - ETL Artifacts will be deleted
- An HTTPS endpoint is always up to receive events:
  - Receives the event
  - Check relevance
    - If relevant continues
    - Else log and ignores
  - Normalize
  - Post into MongoDB

### Your solution

Fill the charts:

|Area|Original Service|Equivalent|Why/How|
|-|-|-|-|
|Data Input|Scheduler|-|-|
|Data Input|Stream|-|-|
|Architecture|HDFS Server|-|-|
|Architecture|MongoDB|-|-|
|Architecture|ElasticSearch|-|-|
|Architecture|Custom WebApp|-|-|

## Solution

### Solution - Data input

- Scheduler:
  - Cloud Composer: Allows you to manage native airflow environments
  - Cloud Scheduler: Allows you to set CRON Jobs like tasks
- Stream:
  - Sub/Pub: Event Driven Manager
  - Cloud Tasks: Tasks based on a Queue

### Solution - Architecture

- Airflow instance
  - Cloud Composer or Cloud Scheduler
- HDFS server
  - On premises: GCP service for virtual servers
  - Dataproc: GCP hadoop
- Mongo DB Server:
  - Mongo DB Atlas
  - Firebase: NoSQL GCP service
- ElasticSearch:
  - Cloud Logging: GCP log collection service
  - Cloud logging and Datadog: GCP will collect logs and will be exported to be processed in Datadog
- Custom WebApp
  - Compute Engine: Docker Manager
  - Cloud functions: Standalone functions, if WebAPP is simple at not high demand this could be an option
  - Cloud logging and Datadog: GCP will collect logs and Datadog will provide the report as demanded based on filters

## Still curious

- What architecture we use here ETL/ELT?
- Have you ever heard about ETLT?
  - Article: [What Data Pipeline Architecture should I use?][gcp architecture]
- Is there any external service such as the proposed datadog that could be more efficient?
- Is more convenient on this replace HDFS for Parquet, AVRO, JSON...?
- If you could achieve the same result with a different set of technologies, which will you choose and why?

## Links

- [GCP site][gcp]
- [What Data Pipeline Architecture should I use?][gcp architecture]

[gcp]: https://cloud.google.com/
[gcp architecture]: https://cloud.google.com/blog/topics/developers-practitioners/what-data-pipeline-architecture-should-i-use/

[md_img]: ./README_IMG.md
[services equivalences]: ../session_2_de_overview_and_cloud#solution---integration-with-data-services
