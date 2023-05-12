# Resource Creation – Introduction

In this practice, you will learn the basics of doing *Infrastructure as Code* (IaC) with Terraform. We will have an overview of the Terraform language and how to use it to create resources locally and in the cloud.

## What you will learn

- What is IaC
- HashiCorp Configuration Language (HCL) basics
  - Resources
  - Variables
  - Dependencies
  - Outputs
  - Lifecycle
  - Meta-arguments
  - Version constraints
- Providers
- Modules
- State, state locking and remote state with backends

## Pre-requisites

[Install Terraform](https://developer.hashicorp.com/terraform/downloads?product_intent=terraform). The instructions are available for Windows, Mac and Linux.

## Introduction

In this practice we will learn the basics of Terraform, focusing on basic Terraform commands, the syntax of HCL and local resources. We will also learn how to use modules and how to manage state.

## Practice

### Infrastructure as Code (IaC)

Infrastructure as Code (IaC) is a practice of managing and provisioning infrastructure resources using code, instead of manually configuring them. IaC provides a more efficient, consistent, and reproducible way of managing infrastructure, reducing the risk of human error and increasing scalability.

With Terraform, you can define your infrastructure as code, and version it like any other software code. This way, you can easily share, collaborate, and maintain your infrastructure, enabling teams to quickly and safely provision and manage cloud resources across multiple environments.

#### Exercise

To get started with Terraform, let's create a simple IaC example. Create a new directory and a file named `main.tf` inside it. Copy and paste the following code:

```arduino
provider "local" {}

resource "local_file" "example" {
  content  = "Hello, Terraform!"
  filename = "example.txt"
}
```

This code defines a local provider, which is used to create local resources, and a `local_file` resource that creates a new file named `example.txt` in the current directory with the content "Hello, Terraform!".

Now, open a terminal in the directory where the `main.tf` file is located and run `terraform init` to initialize the Terraform working directory. After that, run `terraform apply` to create the local file. You should see an output like this:

```log
local_file.example: Creating...
local_file.example: Creation complete after 0s [id=example.txt]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

Check that the file was created with the following command: `cat example.txt`. The output should be `Hello, Terraform!`.

Finally, run `terraform destroy` to destroy the local file. You should see an output like this:

```log
local_file.example: Destroying... [id=example.txt]
local_file.example: Destruction complete after 0s

Destroy complete! Resources: 1 destroyed.
```

### 
