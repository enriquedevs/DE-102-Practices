# Terraform – Introduction

In this practice, you will learn the basics of doing *Infrastructure as Code* (IaC) with Terraform. We will have an overview of the Terraform language and how to use it to create resources locally.

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

[Install Google Cloud SDK](https://cloud.google.com/sdk/docs/install). The instructions are available for Windows, Mac and Linux.

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

### HashiCorp Configuration Language (HCL)

The HashiCorp Configuration Language (HCL) is a declarative language used to define infrastructure resources in Terraform. It is designed to be easy to read and write, and to be compatible with JSON.

Here are some of the main features of HCL:

1. Resources: Resources are the fundamental building blocks of Terraform configurations. They represent the infrastructure components you want to create and manage. Each resource block declares a single resource and specifies its attributes.

Exercise:
Create a resource block for a local file that defines the content, filename, and file permissions. Use the following code as a reference:

```arduino
resource "local_file" "example" {
  content  = "Hello, Terraform!"
  filename = "example.txt"
  file_permission = "0644"
}
```

2. Variables: Variables allow you to parameterize your configurations and pass in values at runtime. They can be defined within a configuration file, or in a separate variable file.

Here we explore the different ways to create and pass variables to Terraform:

   1. Variable declaration in Terraform configuration files: In Terraform, you can declare variables directly in your configuration files using the `variable` block. This allows you to define variables that are specific to your infrastructure and use them throughout your configuration files. Here's an example:


  ```arduino

variable filename {
    type = string 
    default = "sample.txt"
}


variable content{
    type = string
    default = "I love terraform"
}

resource local_file sample_res {
  filename             = var.filename
  content              = var.content
}
  ```

  2. Command line flags: You can pass variables to Terraform at runtime using the `-var` flag followed by the variable name and value. This is useful when you want to override a variable value defined in your configuration file. For example:

  ```arduino
  terraform apply -var="filename=sample2.txt"
  ```

   3. Environment variables: You can also set variables using environment variables. Terraform will look for environment variables with a specific prefix (`TF_VAR_` by default) and use their values as variable values. For example:

  ```arduino
  export TF_VAR_filename=sample3.txt
  terraform apply
  ```

   4. Variable files: Variable files allow you to define variables in a separate file, which can be shared across multiple Terraform configurations. This can be useful for defining variables that are used across different environments or projects. Here's an example of a variable file:

  ```arduino
  filename = "sample3.txt"
  content = "I love Terraform"
  ```

  You can load variable files using the `-var-file` flag followed by the path to the file. For example:

  ```arduino
  terraform apply -var-file="vars.tfvars"
  ```

Exercise:
Create a variable using each of the methods described above. Declare a variable in a configuration file, override it using a command line flag, set it using an environment variable, and load it from a variable file. Use the `filename` variable as an example. Define it as "my_file1" in the configuration file, override it with "my_file2" using a command line flag, set it to "my_file3" using an environment variable, and load it from a variable file named `vars.tfvars`.


3. Dependencies: Dependencies allow you to define relationships between resources, specifying the order in which they should be created or destroyed.

Exercise:
Create a dependency between two local files. The first file should be created before the second file. Use the following code as a reference:

```arduino
resource "local_file" "file1" {
  content  = "This is file 1"
  filename = "file1.txt"
}

resource "local_file" "file2" {
  content  = "This is file 2"
  filename = "file2.txt"
  depends_on = [
    local_file.file1
  ]
}
```


4. Lifecycle: The lifecycle argument allows you to specify certain configuration behaviors that occur during the lifecycle of a resource, such as creating, updating, or destroying it.

The `lifecycle` argument is used to control when certain operations are performed on a resource. There are several lifecycle sub-arguments that you can use to customize the behavior of a resource:

   1. `create_before_destroy`: This sub-argument controls whether the resource should be created before the old resource is destroyed. This is useful when you want to avoid downtime by ensuring that the new resource is available before the old one is removed.

   Example:

   ```arduino
   resource "local_file" "sampleFile" {
        content     = "Hello world!"
        filename = "./sample4.txt"
        lifecycle {
            create_before_destroy = true
        }
    }

   ```

   Exercise:

   Create a local file, and use the `create_before_destroy` lifecycle sub-argument to ensure that the new file is created before the old one is destroyed.

   2. `prevent_destroy`: This sub-argument controls whether the resource can be destroyed. This is useful when you want to prevent accidental deletion of an important resource.

   Example:

   ```arduino
   resource "local_file" "sampleFile2" {
        content     = "Hello world!"
        filename = "./sample5.txt"

        lifecycle {
            prevent_destroy = true
        }
    }

   ```

   Exercise:

   Create a local file with Terraform, and use the `prevent_destroy` lifecycle sub-argument to prevent accidental deletion of the local file.

   3. `ignore_changes`: This sub-argument controls which attributes of the resource should be ignored when determining whether the resource needs to be updated. This is useful when you have attributes that change frequently but don't affect the behavior of the resource.

   Example:

   ```arduino
    resource "local_file" "sampleFile3" {
        content     = "Hello world!"
        filename = "./sample6.txt"

        lifecycle {
            ignore_changes = [content]
        }
    }

   ```

   Exercise:

   Create a local file with Terraform, and use the `ignore_changes` lifecycle sub-argument to ignore changes to the file name, and content attributes.

   4. `prevent_replacement`: This sub-argument controls whether the resource can be replaced. This is useful when you have a resource that is critical and should never be replaced.

   Example:

   ```arduino
    resource "local_file" "sampleFile4" {
        content     = "Hello world!"
        filename = "./sample7.txt"

        lifecycle {
            prevent_replacement = true
        }
    }

   ```

  

   5. `create_before_destroy_max_wait`: This sub-argument controls the maximum amount of time that Terraform will wait for the new resource to become available before giving up and destroying the old resource. This is useful when you have a long-running resource that can't be recreated quickly.

   Example:

   ```arduino

    resource "local_file" "sampleFile5" {
        content     = "Hello world!"
        filename = "./sample8.txt"

        lifecycle {
            create_before_destroy = true
            create_before_destroy_max_wait = 600
        }
    }

   ```

  

5. Meta arguments: Meta-arguments are arguments that are applied to a resource or module block, rather than to a specific attribute.

Exercise:
Add a meta-argument to the `local_file` resource that specifies the resource mode. Use the following code as a reference:

```arduino
resource "local_file" "example" {
  content  = var.file_content
  filename = "example.txt"
  file_permission = "0644"

  meta-argument "mode" {
    type = string
    default = "0666"
  }
}
```



### HCL Data Types

Sure, here are some of the data types and data structures that Terraform supports:

1. String: A sequence of characters. Example: `"Hello, world!"`

2. Number: An integer or a floating-point number. Example: `42` or `3.14`

3. Boolean: A value that is either true or false. Example: `true`

4. List: An ordered sequence of values of the same type. Example: `[ "apple", "banana", "cherry" ]`

5. Tuple: An ordered sequence of values of different types. Example: `["John", "Doe", 42]`

6. Map: A collection of key-value pairs, where the keys are strings and the values can be of any type. Example: `{ name = "John", age = 42 }`

7. Object: An unordered collection of attributes, where each attribute is a key-value pair. Example: `{ name = "John", age = 42 }`

8. Set: An unordered collection of unique values of the same type. Example: `["apple", "banana", "cherry"]`

Here are some examples of how you might use these data types and structures in Terraform:

```arduino
variable "greeting" {
  type = string
  default = "Hello, world!"
}

variable "count" {
  type = number
  default = 3
}

variable "is_enabled" {
  type = bool
  default = true
}

variable "fruits" {
  type = list(string)
  default = [ "apple", "banana", "cherry" ]
}

variable "person" {
  type = tuple([string, string, number])
  default = ["John", "Doe", 42]
}

variable "person_details" {
  type = map(any)
  default = {
    name = "John"
    age = 42
  }
}

variable "users" {
  type = set(string)
  default = ["alice", "bob", "charlie"]
}

variable "config" {
  type = object({
    name = string,
    age = number,
    fruits = list(string),
  })
  default = {
    name = "John",
    age = 42,
    fruits = ["apple", "banana", "cherry"],
  }
}
```

In this example, we've defined several variables, each with a different data type. The `greeting` variable is a string, the `count` variable is a number, the `is_enabled` variable is a boolean, the `fruits` variable is a list of strings, the `person` variable is a tuple containing a string, a string, and a number, the `person_details` variable is a map with two keys, `name` and `age`, both of which have a different data type, the `users` variable is a set of strings, and the `config` variable is an object with three keys, `name`, `age`, and `fruits`.

### HCL: Count and For Each

The `count` and `for_each` arguments in Terraform allow you to create multiple instances of a resource with different configurations.

The `count` argument creates multiple instances of a resource based on a single configuration block. It takes a number as input and creates that many instances of the resource. The instances are numbered starting from 0 and can be accessed using the `element` function.

Example:

```arduino
resource "local_file" "example" {
  count    = 3
  filename = "/tmp/example${count.index}.txt"
  content  = "example"
}
```

This will create three instances of the `local_file` resource, with filenames `/tmp/example0.txt`, `/tmp/example1.txt`, and `/tmp/example2.txt`.

The `for_each` argument creates multiple instances of a resource based on a map or set of maps. It takes a map as input, where each key corresponds to a unique identifier for the resource, and the value is a map containing the configuration for that resource. The instances can be accessed using the `each` object.

Example:

```arduino
variable "example_files" {
  type = map(object({
    filename = string
    content  = string
  }))
  default = {
    example1 = {
      filename = "/tmp/example1.txt"
      content  = "example1"
    }
    example2 = {
      filename = "/tmp/example2.txt"
      content  = "example2"
    }
  }
}

resource "local_file" "example" {
  for_each = var.example_files
  filename = each.value.filename
  content  = each.value.content
}
```

This will create two instances of the `local_file` resource, one with filename `/tmp/example1.txt` and content `example1`, and the other with filename `/tmp/example2.txt` and content `example2`.

Exercise:

Create two local file resources in Terraform, one using `count` and the other using `for_each`. Use different filenames and content for each instance.



## Providers

Providers are plugins in Terraform that allow you to interact with various infrastructure platforms or services. Providers allow Terraform to manage resources on different cloud providers or services in a uniform way. Providers can be official or community-maintained and are typically distributed as separate binary plugins. In order to include a provider for the first time, it has to be included in your prject.


To use a provider in your Terraform configuration, you need to declare it using the `provider` block. The `provider` block contains the name of the provider, its version, and any configuration parameters that are required.

Example:

```arduino
terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "4.69.1"
    }
  }
}
```

This declares the `google` provider.

Once the provider is declared, you can use the resources and data sources defined by that provider in your Terraform configuration.

Providers can also be used to manage multiple environments or accounts. You can create multiple provider blocks with different configuration parameters to manage different environments or accounts.


# -------------------------------------------------
### Summary of Terraform commands

Terraform CLI (Command Line Interface) is a tool that provides a set of commands for managing and deploying infrastructure using Terraform. Here are some of the basic Terraform CLI commands:

1. `terraform init`: This command initializes a working directory containing Terraform configuration files. It downloads and installs the required providers and initializes the backend. Example:

```arduino
terraform init
```

2. `terraform plan`: This command generates an execution plan that shows what changes Terraform will make to the infrastructure. It compares the current state of the infrastructure with the desired state specified in the Terraform configuration files. Example:

```arduino
terraform plan
```

3. `terraform apply`: This command applies the changes to the infrastructure. It creates, updates, or destroys resources as necessary to match the desired state specified in the Terraform configuration files. Example:

```arduino
terraform apply
```

4. `terraform fmt`: This command formats the Terraform configuration files to follow the standard syntax and style guide. Example:

```arduino
terraform fmt
```

5. `terraform validate`: This command validates the Terraform configuration files for syntax errors and other issues. Example:

```arduino
terraform validate
```

6. `terraform state`: This command shows the current state of the infrastructure as recorded in the state file. It can also be used to modify the state file directly. Example:

```arduino
terraform state list
```

7. `terraform show`: This command shows the resources that Terraform created or updated. It displays the attributes and values of each resource. Example:

```arduino
terraform show
```

8. `terraform destroy`: This command destroys all the resources created by Terraform. Example:

```arduino
terraform destroy
```

9. `terraform version`: This command shows the version of Terraform that is installed. Example:

```arduino
terraform version
```

These are some of the basic Terraform CLI commands. There are many more commands and options available for managing and deploying infrastructure with Terraform. To learn more, you can refer to the Terraform documentation or run `terraform --help` to see a list of all the available commands.
