
# Cloud Resource Creation – Introduction

In this practice, you will learn the basics of doing *Infrastructure as Code* (IaC) with Terraform. We will have an overview of the Terraform language and how to use it to create resources  in the cloud.



## Pre-requisites

[Install Google Cloud SDK](https://cloud.google.com/sdk/docs/install). The instructions are available for Windows, Mac and Linux.

## Practice


### GCP Provider

1. `GCS`: 

Google Cloud Storage (GCS) buckets are  basic containers where users can upload any amount of data, in any format. Data stored in GCS buckets are organized into objects which are essentially the individual pieces of data that you upload to your bucket. GCS buckets provide high-capacity, durable, and scalable storage suitable for a wide range of use cases, including serving website content, storing data for archival and disaster recovery, or distributing large data objects to users via direct download. They can be configured with different storage classes to optimize cost-efficiency based on the frequency and speed of data access needed.

```arduino
# Adding the providers to the project
terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "4.69.1"
    }
  }
}

# Adding the provider configuration
provider "google" {
  # Configuration options
  project = "terraform-gcp-project-389602"
  region  = "us-east1"
  zone    = "us-east1-b"
}

#name of the bucket has to be globally unique
resource "google_storage_bucket" "GCS1" {
  name = "my-test-bucket-1221"
  location      = "US"
  storage_class = "STANDARD"
}
```

This Terraform script deploys a Google Cloud Storage (GCS) bucket in Google Cloud Platform (GCP). It first specifies the Google provider, obtained from HashiCorp and using version 4.69.1, then sets the GCP project, region, and zone to which resources will be deployed. The script creates a GCS bucket named "my-test-bucket-1221" in the 'US' location with a 'STANDARD' storage class. It's crucial to note that the bucket name must be globally unique across all existing bucket names in Google Cloud Storage.

2. `GCP Big Query`:

Google BigQuery is a fully-managed, serverless data warehouse solution provided by Google Cloud Platform (GCP). It enables super-fast SQL queries using the processing power of Google's infrastructure. BigQuery allows you to analyze large datasets in real time by running SQL-like queries on structured and semi-structured data. It can handle billions of rows, making it a great choice for big data analytics. It also integrates well with popular data processing frameworks like Apache Beam and Apache Spark. Through its ability to execute SQL queries over vast amounts of data in a matter of seconds, BigQuery enables businesses to gain real-time insights and make data-driven decisions promptly.

```arduino
resource "google_bigquery_dataset" "bd_ds" {
  dataset_id = "ds_from_tf"
}

resource "google_bigquery_table" "table_tf" {
  table_id = "table_from_tf"
  dataset_id = google_bigquery_dataset.bd_ds.dataset_id

}
```

This Terraform script sets up a BigQuery dataset and a table within it on Google Cloud Platform. The resource block then creates a BigQuery table within this dataset, with the table ID 'table_from_tf'. The 'dataset_id' argument is set to the ID of the previously created dataset.

3. `GCP Big Table`:

Google Cloud Bigtable, is a NoSQL big data database service. It's designed to handle massive workloads at consistent low latency and high throughput, making it ideal for running high-performance applications and analytics on large volumes of single-keyed data. Bigtable shines when used for applications like real-time analytics, IoT data processing, and machine learning. As a managed service, Bigtable eliminates much of the administrative overhead involved in maintaining such a high-performance database system. It is also designed to seamlessly integrate with popular big data tools like Hadoop and Dataflow, and it's the underlying storage mechanism for several Google services like Search, Analytics, and Gmail.

```arduino
resource "google_bigtable_instance" "bt-from-tf" {
  
  name = "bt-from-tf"
  deletion_protection = false
  labels = {
    "env" = "testing"
  }
  cluster {
    cluster_id = "bt-from-tf-1"
    num_nodes = 1
    storage_type = "SSD"
  }

}


resource "google_bigtable_table" "tb1" {
  name = "tb-from-tf"
  instance_name = google_bigtable_instance.bt-from-tf.name
}
```

This Terraform script creates a Google Bigtable instance and a table within that instance. The google_bigtable_instance resource block defines a Bigtable instance named 'bt-from-tf' with 'deletion_protection' set to false, meaning it can be deleted without a confirmation prompt. It also assigns a label 'env' with the value 'testing'. The instance consists of a single cluster with an ID of 'bt-from-tf-1', using SSD storage and containing one node. The google_bigtable_table resource block then creates a table named 'tb-from-tf' within the previously defined Bigtable instance. The 'instance_name' argument refers to the name of the Bigtable instance created in the previous resource block.


4. `GCP Pub Sub`:

Google Cloud Pub/Sub is a robust, scalable, real-time messaging service that allows for secure and highly available communication between independently written applications. It follows the publisher-subscriber pattern, enabling the decoupling of sender (publisher) and receiver (subscriber) applications. Publishers send messages to topics, and subscribers receive those messages via subscriptions, which can either 'pull' messages or have them 'pushed' to a configured endpoint. Google Pub/Sub is globally distributed and can automatically scale as the need arises, making it a great solution for building event-driven systems and IoT applications, implementing microservices communication, or handling data streaming for analytics.

```arduino
resource "google_pubsub_topic" "topic_tf" {
  name = "topic_tf"
}

resource "google_pubsub_subscription" "sub_tf" {
  name = "sub_tf"
  topic = google_pubsub_topic.topic_tf.name
}
```

This Terraform script is used to set up a Google Cloud Pub/Sub system with a topic and a subscription. The google_pubsub_topic resource block creates a Pub/Sub topic named 'topic_tf'. Then, the google_pubsub_subscription resource block sets up a subscription named 'sub_tf' for that topic. In Pub/Sub, a publisher application creates and sends messages to a topic, and subscriber applications create a subscription to a topic to receive those messages. Here, the 'topic' argument in the subscription block is set to the name of the previously created topic, linking the subscription to the topic.

### Versioning constraints

Terraform provides several ways to specify version constraints for providers and modules.

For providers, you can specify version constraints in the `required_providers` block of your Terraform configuration. There are several ways to specify version constraints:

- `version` constraint: This allows you to specify a single version or range of versions for the provider.

Example:

```arduino
terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = ">= 4.69.1, < 5.0.0"
    }
  }
}
```

The reason for using a range of versions is that you might want your configuration to benefit from provider updates, but still restrict to versions that are known to be compatible with your configuration

- `source` constraint: This allows you to specify a specific provider release by its source URL.

This will require the latest release of the `google` provider from the HashiCorp registry.

For modules, you can specify version constraints in the `module` block of your Terraform configuration. There are several ways to specify version constraints:

- `version` constraint: This allows you to specify a single version or range of versions for the module.

Example:


```arduino
module "example" {
  source  = "hashicorp/google"
  version = "~> 4.0"
}
```

This will use version 4.x of the `hashicorp/google` module.

- `source` constraint: This allows you to specify a specific module release by its source URL.



Exercise:

Declare a required provider block for the `google` provider with a version constraint that requires version 3.0 or later. 

When specifying version constraints in Terraform, you can use several operators to specify the version range you need. The most commonly used operators are:

- `=`: Specifies an exact version. For example, `version = "1.2.3"`.

- `>=`: Specifies that any version greater than or equal to the specified version is acceptable. For example, `version >= "1.2.0"`.

- `<=`: Specifies that any version less than or equal to the specified version is acceptable. For example, `version <= "1.2.0"`.

- `~>`: Specifies that any version greater than or equal to the specified version, but less than the next major version is acceptable. For example, `version ~> "1.2"` will allow any version between `1.2` and `2.0` (excluding `2.0`).

- `~`: Specifies that any version greater than or equal to the specified version, but less than the next minor version is acceptable. For example, `version ~ "1.2"` will allow any version between `1.2` and `1.3` (excluding `1.3`).

- `!=`: Specifies that any version that does not match the specified version is acceptable. For example, `version != "1.2.0"`.

Example:

```arduino
terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "~> 4.0"
    }
  }
}

module "network" {
  source  = "terraform-google-modules/network/google"
  version = ">= 2.0, < 3.0"
}
```

In this example, the `required_providers` block specifies that any version greater than or equal to 3.0, but less than 4.0, of the `google` provider is acceptable.

The `module` block is used to include the network module from the Terraform Google modules collection. The version constraint ">= 2.0, < 3.0" specifies that you want to use any 2.x version of this module.


### State, state locking, and remote state

In Terraform, the state refers to a file that contains all the information about the resources that Terraform is managing. This file is created after the execution of the `terraform apply` command and is used to track the current state of the infrastructure. 

The state file is an important artifact that Terraform uses to manage the infrastructure. Without it, Terraform would not be able to know which resources are currently deployed, what their current configuration is, and what changes need to be applied when executing a new plan. 

State locking is a mechanism used to prevent concurrent access to the state file. When you execute a Terraform command that needs to access the state file, such as `terraform apply`, Terraform will lock the file to prevent other processes or users from modifying it at the same time. State locking is important to prevent data corruption and conflicts that can arise when multiple users try to modify the same infrastructure at the same time.

Remote state is a mechanism that allows Terraform to store the state file remotely. This means that the state file is not stored locally on your machine but instead in a remote storage system. The most commonly used remote state storage options are Amazon S3, Google Cloud Storage, and HashiCorp's Terraform Cloud.

Using remote state has several advantages. First, it allows multiple users to collaborate on the same infrastructure by sharing the same state file. Second, it provides an additional layer of security by encrypting the state file at rest. Third, it enables the state file to be versioned and backed up.

To use remote state in Terraform, you need to configure a remote backend. This can be done in the `terraform` block of your configuration file. Here is an example:

```arduino
terraform {
  backend "gcs" {
    bucket  = "my-terraform-state"
    prefix  = "terraform/state"
  }
}
```

Here is an example of how you can configure the Google Cloud Storage (GCS) backend in Terraform to store the state file in a GCS bucket. The bucket name here is "my-terraform-state", and the "prefix" argument is used to specify the name of the state file.

### Modules

Terraform modules are reusable collections of Terraform resources that can be shared and versioned independently. Modules help to organize Terraform code, make it more reusable, and reduce duplication.

A Terraform module consists of one or more .tf files that define a set of resources, variables, outputs, and other configuration elements. Modules can be used to represent infrastructure components such as a Compute Engine instance, a Cloud SQL database cluster, or a Cloud Load Balancer in Google Cloud Platform (GCP).

Modules can be stored in a variety of locations, including a local directory, a Git repository, or a module registry. To use a module, you can call it from within another Terraform configuration file using the module block and passing any necessary variables. For example:

```arduino
module "compute_instance" {
  source = "./modules/compute_instance"

  instance_count = 3
  instance_type  = "n1-standard-1"
}

```

In this example, the `module` block references a module located in the `./modules/compute_instance` directory. It passes two variables, `instance_count` and `instance_type`, to the module.

Modules can also have input variables that can be set from the calling configuration file, and output variables that can be used in other parts of the configuration. Here is an example of a module with input and output variables:

```arduino
// In modules/compute_instance/main.tf

resource "google_compute_instance" "compute_instance" {
  count        = var.instance_count
  name         = "instance-${count.index}"
  machine_type = var.instance_type
  zone         = "us-central1-a"

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-9"
    }
  }
}

// In modules/compute_instance/variables.tf

variable "instance_count" {
  description = "Number of compute instances to create"
  type        = number
}

variable "instance_type" {
  description = "Instance type for the compute instance"
  type        = string
}

// In modules/compute_instance/outputs.tf

output "instance_ips" {
  value = google_compute_instance.compute_instance.*.network_interface.0.access_config.0.nat_ip
}

```

In this example, the `google_compute_instance` resource is defined with variables `instance_count` and `instance_type`. These variables are defined in the `variables.tf` file. The outputs.tf file defines an output variable `instance_ips` that returns the external IP addresses of the created instances.

Using modules in Terraform allows for better organization, reuse, and sharing of infrastructure code. It also makes it easier to maintain and update infrastructure components in a centralized way.
