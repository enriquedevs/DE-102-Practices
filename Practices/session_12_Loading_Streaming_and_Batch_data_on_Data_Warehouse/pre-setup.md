# Pre-setup

## Prerequisites

* Basic knowledge of Python.
* Familiarity with Apache Spark and GCP.
* [Terraform][terraform].

## Steps

### Step 1 - Create a GCP Bucket

* Create your own GCP Bucket

### Step 2 - Terraform setup

* Set up a service account with the necessary permissions.

  ```terraform
  provider "google" {
    credentials = file("<PATH_TO_SERVICE_ACCOUNT_JSON>")
    project     = "<YOUR_PROJECT_ID>"
    region      = "us-central1"
  }
  
  resource "google_storage_bucket" "data_bucket" {
    name     = "spark-streaming-bucket"
    location = "US"
  }
  
  resource "google_service_account" "spark_service_account" {
    account_id   = "spark-streaming-account"
    display_name = "Spark Streaming Service Account"
  }
  
  resource "google_storage_bucket_iam_member" "bucket_writer" {
    bucket = google_storage_bucket.data_bucket.name
    role   = "roles/storage.objectAdmin"
    member = "serviceAccount:${google_service_account.spark_service_account.email}"
  }
  ```

* Replace ```<PATH_TO_SERVICE_ACCOUNT_JSON>``` with the path to your GCP service account JSON file and ```<YOUR_PROJECT_ID>``` with your GCP project ID.

### Step 3 - Terraform init

* Run the following commands:

  ```bash
  terraform init
  terraform apply
  ```

## Links

* [Install Terraform][terraform]

[terraform]: https://developer.hashicorp.com/terraform/downloads?product_intent=terraform


