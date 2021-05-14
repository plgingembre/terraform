# Terraform Get Started

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

```hcl
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

### Initialize the configuration

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
$ ll
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
$ cat .terraform.lock.hcl
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
$ ls -lRah .terraform
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

#### AWS credentials with SSO

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

If you're using an IAM user, not a problem, run the `aws configure` command and provide your access key and secret and that should be it. This will become your `default` profile in your `~/.aws/config` and `~/.aws/credentials` config files. Nothing to do in the `main.tf` config file, it uses your default AWS profile vy default.

```hcl
provider "aws" {
  profile = "default"
  region  = "us-west-2"
}
```

If you're using SSO credentials, there are a few ways to handle this with different tools (**_INSERT LINK TO AWS-OKTA HERE_**). I am going to use aws-okta here, but other tools will probably be similar.

In this case, there is no need to modify your main configuration file `main.tf` and specify which account or profile to use, the `default` profile will be just fine as we're authenticating the user before actually calling terraform.

Once aws-okta is configured correctly and you have verified that basic `aws` cli commands work with it (`aws-okta exec <profile_name> -- aws <command>`), you should be able to run terraform commands without any issue.

An example with `terraform plan`:

```
$ aws-okta exec okta-demosales -- terraform plan
Acquiring state lock. This may take a few moments...

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.app_server will be created
  + resource "aws_instance" "app_server" {
      + ami                                  = "ami-830c94e3"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t2.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + subnet_id                            = (known after apply)
      + tags                                 = {
          + "Name" = "ExampleAppServerInstance"
        }
      + tags_all                             = {
          + "Name" = "ExampleAppServerInstance"
        }
      + tenancy                              = (known after apply)
      + vpc_security_group_ids               = (known after apply)

      + ebs_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + snapshot_id           = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }

      + enclave_options {
          + enabled = (known after apply)
        }

      + ephemeral_block_device {
          + device_name  = (known after apply)
          + no_device    = (known after apply)
          + virtual_name = (known after apply)
        }

      + metadata_options {
          + http_endpoint               = (known after apply)
          + http_put_response_hop_limit = (known after apply)
          + http_tokens                 = (known after apply)
        }

      + network_interface {
          + delete_on_termination = (known after apply)
          + device_index          = (known after apply)
          + network_interface_id  = (known after apply)
        }

      + root_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

As a side note, other options to explore for mixing Okta, AWS and terraform. I am putting a few links down here: 
https://github.com/oktadeveloper/okta-aws-cli-assume-role <br>
https://github.com/segmentio/aws-okta <br>
https://bitbucket.org/atlassian/cloudtoken/src/master/ <br>
https://github.com/Nike-Inc/gimme-aws-creds <br>


#### Github large files limitation

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

### Check the configuration

There are two useful commands to validate your configuration with terraform: `terraform fmt` and `terraform validate`.

`terraform fmt` makes sure the format of your configuration file is consistent and updates the config file in case it is not. 

```
$ terraform fmt
```

`terraform validate` makes sure the configuration is valid from an HCL syntax perspective.

### Apply the configuration

At this point, the configuration was verified and you simulated the plan that terraform will use when applying the configuration, so you are good to go and apply the configuration, in other words, build the infrastructure you described.

In the example below, an EC2 instance running Ubuntu is created:

```
aws-okta exec okta-demosales -- terraform apply
Acquiring state lock. This may take a few moments...

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.app_server will be created
  + resource "aws_instance" "app_server" {
      + ami                                  = "ami-830c94e3"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)

##...

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.app_server: Creating...
aws_instance.app_server: Still creating... [10s elapsed]
aws_instance.app_server: Still creating... [20s elapsed]
aws_instance.app_server: Still creating... [30s elapsed]
aws_instance.app_server: Creation complete after 35s [id=i-012082c54447ff572]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

A few things to note in this output:
- The _execution plan_ describes the actions terraform will take to build or change the infrastructure to match your configuration.
- The output format looks like what you get with a diff in Git, the `+` means a resource is created.
- `(known after apply)` is self-explanatory, you will only know the value for this key when the plan is executed (when the resource is actually created).

After a successful execution of the plan, you can now open your AWS console and check if the instance with the id `i-012082c54447ff572` (or name `ExampleAppServerInstance`) was created.

### Inspect state

First, a quick way to find out which resource is tracked by Terraform is by using the `terraform state list` command. This command lists all resources that Terraform has created.

```
$ terraform state list
aws_instance.app_server
```

Terraform keeps track or state of the resources created through the configuration and store data into a file called `terraform.tfstate`. In this file, you can find resources IDs and other properties, that will be used by Terraform to update or destroy those resources.

This file is the only way for Terraform to keep track of managed resources. Information in this file can be sensitive or confidential and therefore should only be securely stored and shared only with trustful people within your team.

An example with the EC2 instance you have created earlier:

```
$ terraform show
# aws_instance.app_server:
resource "aws_instance" "app_server" {
    ami                                  = "ami-830c94e3"
    arn                                  = "arn:aws:ec2:us-west-2:931505774614:instance/i-012082c54447ff572"
    associate_public_ip_address          = true
    availability_zone                    = "us-west-2b"
    cpu_core_count                       = 1
    cpu_threads_per_core                 = 1
    disable_api_termination              = false
    ebs_optimized                        = false
    get_password_data                    = false
    hibernation                          = false
    id                                   = "i-012082c54447ff572"
    instance_initiated_shutdown_behavior = "stop"
    instance_state                       = "running"
    instance_type                        = "t2.micro"
    ipv6_address_count                   = 0
    ipv6_addresses                       = []
    monitoring                           = false
    primary_network_interface_id         = "eni-0af5364558e5feb52"
    private_dns                          = "ip-172-31-44-230.us-west-2.compute.internal"
    private_ip                           = "172.31.44.230"
    public_dns                           = "ec2-54-148-10-19.us-west-2.compute.amazonaws.com"
    public_ip                            = "54.148.10.19"
    secondary_private_ips                = []
    security_groups                      = [
        "default",
    ]
    source_dest_check                    = true
    subnet_id                            = "subnet-0ec2de6c"
    tags                                 = {
        "Name" = "ExampleAppServerInstance"
    }
    tags_all                             = {
        "Name" = "ExampleAppServerInstance"
    }
    tenancy                              = "default"
    vpc_security_group_ids               = [
        "sg-1d658278",
    ]

    credit_specification {
        cpu_credits = "standard"
    }

    enclave_options {
        enabled = false
    }

    metadata_options {
        http_endpoint               = "enabled"
        http_put_response_hop_limit = 1
        http_tokens                 = "optional"
    }

    root_block_device {
        delete_on_termination = true
        device_name           = "/dev/sda1"
        encrypted             = false
        iops                  = 0
        tags                  = {}
        throughput            = 0
        volume_id             = "vol-07ec0430c027a9bab"
        volume_size           = 8
        volume_type           = "standard"
    }
}
```

By default the `terraform.tfstate` file is created in the project directory. You can decide to move this file in a different directory on your local computer by defining a `backend` block in your configuration file with the `local` keyword.

An example of a local backend:

```hcl
terraform {
  backend "local" {
    path = "relative/path/to/terraform.tfstate"
  }
}
```

There are options to remotely store these files by defining a `backend` block in your configuration file with the `remote` keyword. Several options are available for a remote backend, like artifactory, consul, etcd, s3, kubernetes, and many others (see here: https://www.terraform.io/docs/language/settings/backends/index.html).

An example of a remote backend (Terraform Cloud workspace):

```hcl
terraform {
  backend "remote" {
    hostname = "app.terraform.io"
    organization = "company"

    workspaces {
      name = "my-app-prod"
    }
  }
}
```

Another example of a remote backend (Artifactory):

```hcl
terraform {
  backend "artifactory" {
    username = "SheldonCooper"
    password = "AmyFarrahFowler"
    url      = "https://custom.artifactoryonline.com/artifactory"
    repo     = "foo"
    subpath  = "terraform-bar"
  }
}
```

## Change Infrastructure

Changing the infrastructure with Terraform is as simple as changing your `main.tf` configuration file and applying the new configuration.

One of the prerequisites is to make sure the computer that applies the new configuration has access to the Terraform state files used to keep state of the current infrastructure. This is particularly important when you're with multiple machines or within a team. At this point, using shared secure remote backends or Terraform Enterprise/Cloud can make a lot of sense.

I am using the same example of the EC2 instance in AWS that I am going to modify from `ami-830c94e3` (ubuntu/images/hvm/ubuntu-precise-12.04-amd64-server-20170502) to `ami-08d70e59c07c61a3a` (ubuntu/images/hvm-ssd/ubuntu-xenial-16.04-amd64-server-20200814).

The modification in the `main.tf` file is done in the `resource` block:

```hcl
resource "aws_instance" "app_server" {
  ami           = "ami-08d70e59c07c61a3a" ### Modification of the public AMI
  instance_type = "t2.micro"

  tags = {
    Name = "ExampleAppServerInstance"
  }
}
```

After saving `main.tf`, you can safely apply this new configuration with a `terraform apply`:

```
$ terraform apply
Acquiring state lock. This may take a few moments...
aws_instance.app_server: Refreshing state... [id=i-012082c54447ff572]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
-/+ destroy and then create replacement

Terraform will perform the following actions:

  # aws_instance.app_server must be replaced
-/+ resource "aws_instance" "app_server" {
      ~ ami                                  = "ami-830c94e3" -> "ami-08d70e59c07c61a3a" # forces replacement
      ~ arn                                  = "arn:aws:ec2:us-west-2:931505774614:instance/i-012082c54447ff572" -> (known after apply)
      ~ associate_public_ip_address          = true -> (known after apply)
      ~ availability_zone                    = "us-west-2b" -> (known after apply)
      ~ cpu_core_count                       = 1 -> (known after apply)
      ~ cpu_threads_per_core                 = 1 -> (known after apply)
      - disable_api_termination              = false -> null
      - ebs_optimized                        = false -> null
      - hibernation                          = false -> null
      + host_id                              = (known after apply)
      ~ id                                   = "i-012082c54447ff572" -> (known after apply)
      ~ instance_initiated_shutdown_behavior = "stop" -> (known after apply)
      ~ instance_state                       = "running" -> (known after apply)
      ~ ipv6_address_count                   = 0 -> (known after apply)
      ~ ipv6_addresses                       = [] -> (known after apply)
      + key_name                             = (known after apply)
      - monitoring                           = false -> null
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      ~ primary_network_interface_id         = "eni-0af5364558e5feb52" -> (known after apply)
      ~ private_dns                          = "ip-172-31-44-230.us-west-2.compute.internal" -> (known after apply)
      ~ private_ip                           = "172.31.44.230" -> (known after apply)
      ~ public_dns                           = "ec2-54-148-10-19.us-west-2.compute.amazonaws.com" -> (known after apply)
      ~ public_ip                            = "54.148.10.19" -> (known after apply)
      ~ secondary_private_ips                = [] -> (known after apply)
      ~ security_groups                      = [
          - "default",
        ] -> (known after apply)
      ~ subnet_id                            = "subnet-0ec2de6c" -> (known after apply)
        tags                                 = {
            "Name" = "ExampleAppServerInstance"
        }
      ~ tenancy                              = "default" -> (known after apply)
      ~ vpc_security_group_ids               = [
          - "sg-1d658278",
        ] -> (known after apply)
        # (4 unchanged attributes hidden)

      - credit_specification {
          - cpu_credits = "standard" -> null
        }

      + ebs_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + snapshot_id           = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }

      ~ enclave_options {
          ~ enabled = false -> (known after apply)
        }

      + ephemeral_block_device {
          + device_name  = (known after apply)
          + no_device    = (known after apply)
          + virtual_name = (known after apply)
        }

      ~ metadata_options {
          ~ http_endpoint               = "enabled" -> (known after apply)
          ~ http_put_response_hop_limit = 1 -> (known after apply)
          ~ http_tokens                 = "optional" -> (known after apply)
        }

      + network_interface {
          + delete_on_termination = (known after apply)
          + device_index          = (known after apply)
          + network_interface_id  = (known after apply)
        }

      ~ root_block_device {
          ~ delete_on_termination = true -> (known after apply)
          ~ device_name           = "/dev/sda1" -> (known after apply)
          ~ encrypted             = false -> (known after apply)
          ~ iops                  = 0 -> (known after apply)
          + kms_key_id            = (known after apply)
          ~ tags                  = {} -> (known after apply)
          ~ throughput            = 0 -> (known after apply)
          ~ volume_id             = "vol-07ec0430c027a9bab" -> (known after apply)
          ~ volume_size           = 8 -> (known after apply)
          ~ volume_type           = "standard" -> (known after apply)
        }
    }

Plan: 1 to add, 0 to change, 1 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.app_server: Destroying... [id=i-012082c54447ff572]
aws_instance.app_server: Still destroying... [id=i-012082c54447ff572, 10s elapsed]
aws_instance.app_server: Still destroying... [id=i-012082c54447ff572, 20s elapsed]
aws_instance.app_server: Destruction complete after 21s
aws_instance.app_server: Creating...
aws_instance.app_server: Still creating... [10s elapsed]
aws_instance.app_server: Still creating... [20s elapsed]
aws_instance.app_server: Still creating... [30s elapsed]
aws_instance.app_server: Creation complete after 34s [id=i-093373c8461b7955f]

Apply complete! Resources: 1 added, 0 changed, 1 destroyed.
```

What is interesting to note in this output is the prefixes before each line: `-/+/~`.
- `-` means something is destroyed
- `+` means something is created
- `~` means something is updated instead of being destroyed/recreated

In this case, because it is an AMI change, the previous EC2 instance is destroyed and a new one is created.

## Destroy Infrastructure

Delete infrastructure with Terraform is as simple as one command: `terraform destroy`. This command is the reverse of `terraform apply` and terminates all the resources in your Terraform state. As any other stateful software, it only destroys resources created through Teerraform.

An example using the EC2 instance:

```
$ terraform destroy
Acquiring state lock. This may take a few moments...
aws_instance.app_server: Refreshing state... [id=i-093373c8461b7955f]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # aws_instance.app_server will be destroyed
  - resource "aws_instance" "app_server" {
      - ami                                  = "ami-08d70e59c07c61a3a" -> null
      - arn                                  = "arn:aws:ec2:us-west-2:931505774614:instance/i-093373c8461b7955f" -> null
      - associate_public_ip_address          = true -> null
      - availability_zone                    = "us-west-2b" -> null
      - cpu_core_count                       = 1 -> null
      - cpu_threads_per_core                 = 1 -> null
      - disable_api_termination              = false -> null
      - ebs_optimized                        = false -> null
      - get_password_data                    = false -> null
      - hibernation                          = false -> null
      - id                                   = "i-093373c8461b7955f" -> null
      - instance_initiated_shutdown_behavior = "stop" -> null
      - instance_state                       = "running" -> null
      - instance_type                        = "t2.micro" -> null
      - ipv6_address_count                   = 0 -> null
      - ipv6_addresses                       = [] -> null
      - monitoring                           = false -> null
      - primary_network_interface_id         = "eni-0f656db1a752514e1" -> null
      - private_dns                          = "ip-172-31-45-74.us-west-2.compute.internal" -> null
      - private_ip                           = "172.31.45.74" -> null
      - public_dns                           = "ec2-52-39-170-178.us-west-2.compute.amazonaws.com" -> null
      - public_ip                            = "52.39.170.178" -> null
      - secondary_private_ips                = [] -> null
      - security_groups                      = [
          - "default",
        ] -> null
      - source_dest_check                    = true -> null
      - subnet_id                            = "subnet-0ec2de6c" -> null
      - tags                                 = {
          - "Name" = "ExampleAppServerInstance"
        } -> null
      - tags_all                             = {
          - "Name" = "ExampleAppServerInstance"
        } -> null
      - tenancy                              = "default" -> null
      - vpc_security_group_ids               = [
          - "sg-1d658278",
        ] -> null

      - credit_specification {
          - cpu_credits = "standard" -> null
        }

      - enclave_options {
          - enabled = false -> null
        }

      - metadata_options {
          - http_endpoint               = "enabled" -> null
          - http_put_response_hop_limit = 1 -> null
          - http_tokens                 = "optional" -> null
        }

      - root_block_device {
          - delete_on_termination = true -> null
          - device_name           = "/dev/sda1" -> null
          - encrypted             = false -> null
          - iops                  = 100 -> null
          - tags                  = {
              - "Name" = "ExampleAppServerInstance"
            } -> null
          - throughput            = 0 -> null
          - volume_id             = "vol-0e882ef55604ca9f2" -> null
          - volume_size           = 8 -> null
          - volume_type           = "gp2" -> null
        }
    }

Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

aws_instance.app_server: Destroying... [id=i-093373c8461b7955f]
aws_instance.app_server: Still destroying... [id=i-093373c8461b7955f, 10s elapsed]
aws_instance.app_server: Still destroying... [id=i-093373c8461b7955f, 20s elapsed]
aws_instance.app_server: Still destroying... [id=i-093373c8461b7955f, 30s elapsed]
aws_instance.app_server: Still destroying... [id=i-093373c8461b7955f, 40s elapsed]
aws_instance.app_server: Destruction complete after 41s

Destroy complete! Resources: 1 destroyed.
```

## Using Input Variables

With Terraform, you have the ability to define variables and make your configuration more dynamic and flexible. Once defined, those variables can be used in the configuration.

Variables are created and stored in a separate file from the `main.tf` config file.

An example with a file named `varibales.tf`, but any `.tf` filename will work, see below:

```hcl
variable "instance_name" {
  description = "Value of the Name tag for the EC2 instance"
  type        = string
  default     = "AppServerInstanceDefaultName"
}
```

In the `main.tf` configuration file, you can modify the `resource` block to use this variable instead of the hard-coded name:

```hcl
resource "aws_instance" "app_server" {
  ami           = "ami-08d70e59c07c61a3a"
  instance_type = "t2.micro"

  tags = {
    Name = var.instance_name ### Pointing to the newly created variable
  }
}
```
Using `terraform apply` without specifying the variable will end up with the `default` value for the varibale, in this case `AppServerInstanceDefaultName`. To specify the value for a given variable, you need to use `-var 'variable_name=value'` with the `terraform apply` command.

```
$ terraform apply -var 'instance_name=BlahServerName'
aws_instance.app_server: Refreshing state... [id=i-04c96ab1acbb4317d]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # aws_instance.app_server will be updated in-place
  ~ resource "aws_instance" "app_server" {
        id                                   = "i-04c96ab1acbb4317d"
      ~ tags                                 = {
          ~ "Name" = "AppServerInstanceDefaultName" -> "BlahServerName"
        }
      ~ tags_all                             = {
          ~ "Name" = "AppServerInstanceDefaultName" -> "BlahServerName"
        }
        # (27 unchanged attributes hidden)




        # (4 unchanged blocks hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.app_server: Modifying... [id=i-04c96ab1acbb4317d]
aws_instance.app_server: Modifications complete after 2s [id=i-04c96ab1acbb4317d]

Apply complete! Resources: 0 added, 1 changed, 0 destroyed.
```

> **_IMPORTANT_** - The value set for this variable is not set forever in your configuration file, so you need to enter it each time you execute a command. Terraform supports many ways to use and set variables, see here: https://learn.hashicorp.com/tutorials/terraform/variables?in=terraform/configuration-language.

## Query Data with Outputs

With Terraform you can query your existing resources and define a list desired `outputs`. To do so, you need to define in your project directory a `.tf` file that includes `output` blocks.

An example below:

```hcl
output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.app_server.id
}

output "instance_public_ip" {
  description = "Public IP address of the EC2 instance"
  value       = aws_instance.app_server.public_ip
}
```

Running `terraform apply` will inspect the local directory for `.tf` files and will render outputs if there are `output` resources defined in one of these configuration files. `terraform apply` is mandatory for Terraform to acknowledge that outputs are requested after executing wrking on the infrastructure.

```
$ terraform apply -var 'instance_name=BlahServerName'
aws_instance.app_server: Refreshing state... [id=i-04c96ab1acbb4317d]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:

Terraform will perform the following actions:

Plan: 0 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + instance_id        = "i-04c96ab1acbb4317d"
  + instance_public_ip = "54.184.118.153"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes


Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

instance_id = "i-04c96ab1acbb4317d"
instance_public_ip = "54.184.118.153"
```

You can see that the `Outputs` are displayed at the end of the output of the `terraform apply` command. A better way to display the outputs of a given Terraform project is to use the `terraform output` command.

```
$ terraform output
instance_id = "i-04c96ab1acbb4317d"
instance_public_ip = "54.184.118.153"
```

## Store State on Remote Backends

As mentioned earlier, state data can contain sensitive or confidential information and should not be stored anywhere or accessible by everybody in a production environment. Ideally, you should keep your state secure and encrypted in a place controlled by user access and audit logs, etc.

Remote backends are available in Terraform to store your state data in a secure remote location that you can share with teammates that works on the same infrastructure.

One of the options is to use Terraform Cloud to store state date remotely, but also variables, API tokens or access keys.

First of all, you need to login to Terraform Cloud using your API key. Creating an account is free, and then you can create an organization.

A few terms to know to get started with Terraform Cloud
- An `organization` is like a tenant, a private space for teams to share projects and collaborate on infrastructure.
- A `workspace` is like a namespace, a directory or space where you can store your configuration, secrets, variables, state and logs. A `workspace` can be created from the terraform cli.

I will create an organization named `plgingembre` and will leave it with no workspace for now.

I also need to modify the `main.tf` configuration to specify this information as a remote `backend` in the `terraform` block.

```hcl
terraform {
  backend "remote" {              ##
    organization = "plgingembre"  ##
                                  ##
    workspaces {                  ## New section for Terraform Cloud remote backend
      name = "tfc-aws-instance"   ##
    }                             ##
  }                               ##

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.27"
    }
  }

  required_version = ">= 0.14.9"
}
```

After modifying the `main.tf` configuration, it's time to login to my Terraform Cloud account from the terminal, so that we can store variables or state in a workspace after the plan execution.

```
$ terraform login
Terraform will request an API token for app.terraform.io using your browser.

If login is successful, Terraform will store the token in plain text in
the following file for use by subsequent commands:
    /Users/plgingembre/.terraform.d/credentials.tfrc.json

Do you want to proceed?
  Only 'yes' will be accepted to confirm.

  Enter a value: yes


---------------------------------------------------------------------------------

Terraform must now open a web browser to the tokens page for app.terraform.io.

If a browser does not open this automatically, open the following URL to proceed:
    https://app.terraform.io/app/settings/tokens?source=terraform-login


---------------------------------------------------------------------------------

Generate a token using your browser, and copy-paste it into this prompt.

Terraform will store the token in plain text in the following file
for use by subsequent commands:
    /Users/plgingembre/.terraform.d/credentials.tfrc.json

Token for app.terraform.io:
  Enter a value:


Retrieved token for user plgingembre


---------------------------------------------------------------------------------

                                          -
                                          -----                           -
                                          ---------                      --
                                          ---------  -                -----
                                           ---------  ------        -------
                                             -------  ---------  ----------
                                                ----  ---------- ----------
                                                  --  ---------- ----------
   Welcome to Terraform Cloud!                     -  ---------- -------
                                                      ---  ----- ---
   Documentation: terraform.io/docs/cloud             --------   -
                                                      ----------
                                                      ----------
                                                       ---------
                                                           -----
                                                               -


   New to TFC? Follow these steps to instantly apply an example configuration:

   $ git clone https://github.com/hashicorp/tfc-getting-started.git
   $ cd tfc-getting-started
   $ scripts/setup.sh


```

We can initialize the configuration with the `terraform init` command. That will also initialize the backend and propose to copy existing local state to the remote backend. Not mandatory, but can be helpful to "migrate" en existing local project to Terraform Cloud.

```
$ terraform init

Initializing the backend...
Acquiring state lock. This may take a few moments...
Do you want to copy existing state to the new backend?
  Pre-existing state was found while migrating the previous "local" backend to the
  newly configured "remote" backend. No existing state was found in the newly
  configured "remote" backend. Do you want to copy this state to the new "remote"
  backend? Enter "yes" to copy and "no" to start with an empty state.

  Enter a value: yes


Successfully configured the backend "remote"! Terraform will automatically
use this backend unless the backend configuration changes.

Initializing provider plugins...
- Reusing previous version of hashicorp/aws from the dependency lock file
- Using previously-installed hashicorp/aws v3.39.0

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

When using Terraform Cloud as a remote backend with the CLI-driven workflow, we can choose to have Terraform run remotely, or on our local machine. When using local execution, Terraform Cloud will execute Terraform on our local machine and remotely store our state file in Terraform Cloud. Here, we will use the remote execution mode.

After migrating the state file to the remote backend in a Terraform Cloud backend, we can safely delete the local state.

```
$ rm terraform.state.*
```

Our workspace needs to be configured with our AWS credentials to authenticate the AWS provider. This done by using two `Environment Variables` named `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_TOKEN` (and the values from our AWS account).

We can apply the configuration to make sure that, with the state now being in our Workspace in Terraform Cloud, there's no difference with how it was before with our local machine.

```
$ terraform apply
Running apply in the remote backend. Output will stream here. Pressing Ctrl-C
will cancel the remote apply if it's still pending. If the apply started it
will stop streaming the logs, but will not stop the apply running remotely.

Preparing the remote apply...

To view this run in a browser, visit:
https://app.terraform.io/app/plgingembre/tfc-aws-instance/runs/run-Bg6r4gPx3QBxobn8

Waiting for the plan to start...

Terraform v0.15.3
on linux_amd64
Configuring remote state backend...
Initializing Terraform configuration...

Terraform used the selected providers to generate the following execution
plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.app_server will be created
  + resource "aws_instance" "app_server" {
      + ami                                  = "ami-08d70e59c07c61a3a"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t2.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + subnet_id                            = (known after apply)
      + tags_all                             = (known after apply)
      + tenancy                              = (known after apply)
      + vpc_security_group_ids               = (known after apply)

      + ebs_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + snapshot_id           = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }

      + enclave_options {
          + enabled = (known after apply)
        }

      + ephemeral_block_device {
          + device_name  = (known after apply)
          + no_device    = (known after apply)
          + virtual_name = (known after apply)
        }

      + metadata_options {
          + http_endpoint               = (known after apply)
          + http_put_response_hop_limit = (known after apply)
          + http_tokens                 = (known after apply)
        }

      + network_interface {
          + delete_on_termination = (known after apply)
          + device_index          = (known after apply)
          + network_interface_id  = (known after apply)
        }

      + root_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + instance_id        = (known after apply)
  + instance_public_ip = (known after apply)

Do you want to perform these actions in workspace "tfc-aws-instance"?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.app_server: Still creating... [10s elapsed]
aws_instance.app_server: Still creating... [20s elapsed]
aws_instance.app_server: Creation complete after 24s [id=i-01e6d7fd0c8de5474]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

instance_id = "i-01e6d7fd0c8de5474"
instance_public_ip = "34.213.39.10"
```

We can now destroy this instance to save some $$ with our AWS account. Just run a `terraform destory` command. 

## Conclusion

This concludes this first part on Terraform, extremely inspired by the official Terraform documentation, that is really well written in my opinion (very pragmatic approach of a doc). In future posts, I will focus more on specific examples to build an infrastructure for a given use case (Kubernetes, serverless functions, etc.).

**_Sources:_**
https://www.terraform.io/intro/index.html <br>
https://learn.hashicorp.com/tutorials/terraform/aws-remote?in=terraform/aws-get-started <br>
