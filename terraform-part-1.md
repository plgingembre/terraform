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
    Name = "ExampleAppServerInstanceBuiltWithTerraform"
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

Resource blocks contain arguments which you use to configure the resource. Arguments can include things like machine sizes, disk image names, or VPC IDs. Check the providers reference docs to check the required and optional arguments for each resource. For this EC2 instance, the example configuration sets the AMI ID to an Ubuntu image, and the instance type to t2.micro, which qualifies for AWS' free tier. It also sets a tag to give the instance a name.

## Build Infrastucture

### Initialization

To get started with your terraform project, you need to initialize your project directory with the `terraform init` command. Initializing the directory downloads and installs the provider specified in the configuration.

```
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/aws versions matching "~> 3.27"...
- Installing hashicorp/aws v3.39.0...
- Installed hashicorp/aws v3.39.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

Now, if you explore a little more what happens in this directory, you can see a new file `.terraform.lock.hcl` and a new directory `.terraform`.

```
% ll
total 16
drwxr-xr-x@ 5 plgingembre  staff   160B May 10 10:44 .
drwxr-xr-x@ 7 plgingembre  staff   224B May 10 10:24 ..
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:44 .terraform
-rw-r--r--  1 plgingembre  staff   1.1K May 10 10:44 .terraform.lock.hcl
-rw-r--r--@ 1 plgingembre  staff   393B May 10 10:24 main.tf
```

The lock file contains the version of the provider versions used in this configuration, so that there's no doubt about which version will be executed when building the infrastructure. It's also easier to update the providers later on.

Content of the `.terraform.lock.hcl` lock file:

```
% cat .terraform.lock.hcl
# This file is maintained automatically by "terraform init".
# Manual edits may be lost in future updates.

provider "registry.terraform.io/hashicorp/aws" {
  version     = "3.39.0"
  constraints = "~> 3.27"
  hashes = [
    "h1:fjlp3Pd3QsTLghNm7TUh/KnEMM2D3tLb7jsDLs8oWUE=",
    "zh:2014b397dd93fa55f2f2d1338c19e5b2b77b025a76a6b1fceea0b8696e984b9c",
    "zh:23d59c68ab50148a0f5c911a801734e9934a1fccd41118a8efb5194135cbd360",
    "zh:412eab41d4934ca9c47083faa128e4cd585c3bb44ad718e40d67091aebc02f4e",
    "zh:4b75e0a259b56d97e66b7d69f3f25bd4cc7be2440c0fe35529f46de7d40a49d3",
    "zh:694a32519dcca5bd8605d06481d16883d55160d97c1f4039deb13c6ca8de8427",
    "zh:6a0bcef43c2d9a97aeaaac3c5d1d6728dc2464a51a014f118c691c79029d0903",
    "zh:6d78fc7c663247ca2a80f276008dcdafece4cac75e2639bbce188c08b796040a",
    "zh:78f846a505d7b64b67feed1527d4d2b40130dadaf8e3112113685e148f49b156",
    "zh:881bc969432d3ef6ec70f5a762c3415e037904338579b0a360c6818b74d26e59",
    "zh:96c1ca80c1d693a3eef80489adb45c076ee8e6878e461d6c29b05388d4b95f48",
    "zh:9be5fa342272586fc6e319e20f21c0c5c801b05dcf7d59e473ad0882c9ecfa70",
  ]
}
```

The `.terraform` directory is the place where providers will be downloaded and installed for this specific project (the current directory).

Content of the `.terraform` directory and subdirectories:

```
% ls -lRah .terraform
total 0
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 .
drwxr-xr-x@ 5 plgingembre  staff   160B May 10 10:50 ..
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 providers

.terraform/providers:
total 0
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 .
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 ..
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 registry.terraform.io

.terraform/providers/registry.terraform.io:
total 0
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 .
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 ..
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 hashicorp

.terraform/providers/registry.terraform.io/hashicorp:
total 0
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 .
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 ..
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 aws

.terraform/providers/registry.terraform.io/hashicorp/aws:
total 0
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 .
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 ..
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 3.39.0

.terraform/providers/registry.terraform.io/hashicorp/aws/3.39.0:
total 0
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 .
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 ..
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 darwin_amd64

.terraform/providers/registry.terraform.io/hashicorp/aws/3.39.0/darwin_amd64:
total 416768
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 .
drwxr-xr-x@ 3 plgingembre  staff    96B May 10 10:50 ..
-rwxr-xr-x  1 plgingembre  staff   194M May 10 10:44 terraform-provider-aws_v3.39.0_x5
```

#### AWS credentials

In case you're trying to create a configuration in AWS, terraform will download and install the aws terraform provider into the local project directory. One issue that you can encounter is about using valid aws credentials, usable by terraform. 

If you're using SSO with an external IDP, you may run into the message below when running `terraform plan` command:

```
$ terraform plan
Acquiring state lock. This may take a few moments...
╷
│ Error: error configuring Terraform AWS Provider: no valid credential sources for Terraform AWS Provider found.
│
│ Please see https://registry.terraform.io/providers/hashicorp/aws
│ for more information about providing credentials.
│
│ Error: NoCredentialProviders: no valid providers in chain. Deprecated.
│ 	For verbose messaging see aws.Config.CredentialsChainVerboseErrors
│
│
│   with provider["registry.terraform.io/hashicorp/aws"],
│   on main.tf line 12, in provider "aws":
│   12: provider "aws" {
│
```

#### Work around Github filesize limitation

Another issue that you can have when syncing your code and configs from your local repo to the remote one on Github is the max filesize limit (100MB).

The source code of your applications or terraform configurations is just a few kilobytes (KB), so there should not be any issue here. When using a provider, remember that terraform downloads and installs the provider file in the project directory under the `.terraform` directory.

Once added to the list of files/directories to sync between repos with git, we end up with an error from github refusing to sync files above 100MB in size. Although frustrating at first, it makes sense for Github to limit the size of each repo and not store large files from each project you create, especially in this case, the provider is a static file that will never change over time (it's a dead file for Github, but using plenty of disk space!).

One way to work around this constraint is to ignore these directories that contain large files.

Create a gitignore file:

```
$ touch .gitignore
```

Edit this file in your preferred text editor and add the following content:

```
# Directories
.terraform/

# Compiled files
*.tfstate
*.tfstate.backup
*.tfstate.lock.info
```

You can add more than these files, like `*.pem`, `*.log`, or other `.DS_Store` if you're running git on your Mac.

**_It is important to add this gitignore file at the very beginning of your project to avoid any issue later on when trying to sync local repos with the remote on Github_**

Once you have this `.gitignore` file in your local repo, you can stage your files, and push them to the remote repo.

An example of a terraform project with a `.gitignore` file, initialized:

```
$ ll
total 16
drwxr-xr-x@ 5 plgingembre  staff   160B May 12 07:55 .
drwxr-xr-x@ 8 plgingembre  staff   256B May 12 07:55 ..
drwxr-xr-x@ 3 plgingembre  staff    96B May 12 07:47 .terraform
-rw-r--r--  1 plgingembre  staff   1.1K May 12 07:47 .terraform.lock.hcl
-rw-r--r--  1 plgingembre  staff   375B May 12 07:38 main.tf
$
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	learn-terraform-aws-instance/.terraform.lock.hcl

nothing added to commit but untracked files present (use "git add" to track)
```

Only the file `.terraform.lock.hcl` is staged, but not the `.terraform` directory. You can now commit this chnage with `git commit -m "..."` and push it to the remote repo in Github with a `git push origin main`.

### Check configuration

There are two useful commands to validate your configuration with terraform: `terraform fmt` and `terraform validate`.

`terraform fmt` makes sure the format of your configuration file is consistent and updates the config file in case it is not. 

```
% terraform fmt
```

`terraform validate` makes sure the configuration is valid from an HCL syntax perspective.

### Apply configuration

