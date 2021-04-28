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

