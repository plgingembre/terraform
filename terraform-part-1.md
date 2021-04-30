# Terraform

## Introduction

As stated in the official documentation, Terraform - created by HashiCorp - is an open source tool _for building, changing, and versioning infrastructure safely and efficiently. Terraform can manage existing and popular service providers as well as custom in-house solutions_.

Terraform offers a set of useful features such as:
- Infrastructure as Code: define your infrastructure in configuration files, versioned and stored as you would for application code.
- Execution Plans: before applying your new configuration, Terraform tells you what the plan is with your infrastructure and how things are going to be set up.
- Resource Graph: Terrafrom looks for dependancies in the infrastructure and serializes and/or parallelizes based on the observed dependancies. The goal of this is to make the creation process efficient and fast and also to provide insights to the operator creating those resources.
- Change Automation: less human intervention is better for the stability of your infrastructure. The execution plan combined with the resource graph makes operator aware of what is going to happen without having to manually validate or imperatively configure anything.

## Workflow

Terraform has a 3-steps workflow:
1. Write - Author infrastructure as code
2. Plan - Preview changes before applying
3. Apply - Provision the infrastructure

### Write

This step is basically writing the code that describes what you want to build as an infrastructure. It is common practice to store this code or config file in a version controlled repository (github, gitlab or bitbucket being good examples).

Configuration files in Terraform are using **.tf** as file extension. 

**_Is main.tf mandatory or can it be named differently??_** 

When the first iteration of a configuration file is ready, it is time to initialize terraform.

To initialize terraform and read the main configuration file, use the following command:
```
$ terraform init
```

It will typically be the case that you require multiple iterations of your configuration file to achieve your desired infrastructure design. Running plans can help you quickly spot any error or get a good sense of what terraform is about to do based on what you wrote in your code.

To check what terraform will do with your infrastructure based on your code/config, use the following command:
```
$ terraform plan
```

`terraform plan` is not an interactive command, it only displays what terraform will do with the resources that you create or modify in your code.

### Plan

Once you feel your changes look good in your code, it's time to run your final plan and commit your changes to your local copy of your verion controlled repository.

```
$ git add main.tf
$ git commit -m "Creating a new infrastructure with code!"
```

Before applying those changes, a final review of the plan will be displayed and will require validation from the operator.

To make the final review and provision the infrastructure, use the following command:
```
$ terraform apply
```

### Apply

After a last verification of the plan, Terraform will provision the real infrastructure.

Once the infrastructure is provisioned, it is a safe practice to push your version of the config to a remote version control repo.

```
$ git remote add origin https://github.com/*user*/*repo*.git
$ git push origin main
```

### As a Team

Nothing fundamentally changed, except that instead of working on the main branch, you need to work on your branch as you make changes to the original scenario, get your changes approved by the team in a code review and then merged into the main branch.

To create a new branch and make changes to the main config, use the the following command:
```
$ git checkout -b add-load-balancer
```

## Language

Terraform has its own naming convention to describe the different components or actors to be used:
* Configuration: a Terraform _configuration_ is the file (or set of files) that describes the infrastructure. Each configuration must be in its own directory
* Provider: a Terraform _provider_ is **_- TO BE COMPLETED -_**

## Configuration

An example of a simple configuration file is shown below:

```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.27"
    }
  }

  required_version = ">= 0.14.9"
}

provider "aws" {
  profile = "default"
  region  = "us-west-2"
}

resource "aws_instance" "app_server" {
  ami           = "ami-830c94e3"
  instance_type = "t2.micro"

  tags = {
    Name = "ExampleAppServerInstance"
  }
}
```

Each code block has its own importance:

### Terraform Block

Summary of the terraform settings, including the providers to be used to provision the infrastructure.

`source` defines an optional hostname, a namespace, and the provider type. Terraform installs providers from the Terraform Registry by default. In this example, `hashicorp/aws` is a short version for `registry.terraform.io/hashicorp/aws`.

A version constraint for each provider can be defined in the `required_providers` block. The version attribute is optional, but it is recommended to constrain the provider version so that Terraform does not install a version of the provider that does not work with your configuration. If you do not specify a provider version, Terraform will automatically download the most recent version during initialization.

### Providers

The `provider` block configures the specified provider, in this case `aws`. A provider is a plugin that Terraform uses to create and manage your resources.

The `profile` attribute in the `aws` provider block refers Terraform to the AWS credentials stored in your AWS config file, which you created when you configured the AWS CLI. **Reminder:** Never hard-code credentials or other secrets in your configuration files!

You can use multiple provider blocks in your Terraform configuration to manage resources from different providers. You can even use different providers together.

### Resources

The `resource` blocks define components of your infrastructure. A resource might be a physical or virtual component such as an EC2 instance, or it can be a logical resource such as a Heroku application.

Resource blocks have two strings before the block: the resource type and the resource name. In this example, the resource type is `aws_instance` and the name is `app_server`. The prefix of the type maps to the name of the provider. In the example configuration, Terraform manages the `aws_instance` resource with the `aws` provider. Together, the resource type and resource name form a unique ID for the resource. For example, the ID for your EC2 instance is `aws_instance.app_server`.

Resource blocks contain arguments which you use to configure the resource. Arguments can include things like machine sizes, disk image names, or VPC IDs. Check the providers reference dosc to check the required and optional arguments for each resource. For this EC2 instance, the example configuration sets the AMI ID to an Ubuntu image, and the instance type to t2.micro, which qualifies for AWS' free tier. It also sets a tag to give the instance a name.