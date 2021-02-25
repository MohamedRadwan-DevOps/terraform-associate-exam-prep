# Terraform Study Guide

## Terraform Configuration file

  
You describe all the components or your entire datacentre or environment in terraform configuration file which has .tf extension.

## Resource Graph

  
Terraform builds a graph of all your resources and parallelizes the creation and modification of any non-dependent resources. because of this, Terraform builds infrastructure as with efficiency as possible, and operators get insight into dependencies in their infrastructure.

## Providers

  
Terraform relies on plugins called "providers" to interact with remote systems.
Terraform configurations must declare which providers they require so that Terraform can install and use them.
Each provider adds a set of resource types and/or data sources that Terraform can manage.

## Configure a Provider

  
provider "azurerm" {
features { }  
version = ">= 2.26“ 
}
provider_x_y **à** e.x. (azurerm_resource_group)

## Control terraform version or other behaviours

  
terraform {
#...
}

## Constrain a provider version
  
terraform {
required_providers {
azurerm = {
source = "hashicorp/azurerm"
version = ">= 2.26"
}  } }

## Alias

  
I can use alias to distinguish several providers based on data centre and later I can use the alias to refer to the desired provider using this syntax inside the resource block {}

Provider_value.alias_value
e.g. provider à aws.east


## Alias

  
#..The default provider configuration

provider "aws" {

region = " us-west-2"

}

#..Additional provider configuration for east region

provider "aws" {

alias  = “east"

region = " us-east-1"

}

## Terraform init (cmd)

  
Initialize the project (folder), which means downloads plugin(s) that allows Terraform to interact with needed providers that defined in the .tf files

Re-running init with modules already installed will install the sources for any modules that were added to configuration since the last init but will not change any already-installed modules.

It can’t install third-party plugins as it needs to be manually installed.

[https://www.terraform.io/docs/cli/commands/init.html](https://www.terraform.io/docs/cli/commands/init.html)

## Upgrade provider

  
terraform init –upgrade

## Terraform.lock.hcl (file)

  
Initialize a Terraform configuration for the 1st time will generate a new .terraform.lock.hcl file in the current working directory. You should include the lock file in your version control repository to ensure that Terraform uses the same provider versions across your team and in brief remote execution environments.

It is a file that belongs to the configuration as a whole, rather than to each separate module in the configuration. For that reason Terraform creates it and expects to find it in your current working directory when you run Terraform, which is also the directory containing the .tf files for the root module of your configuration.

Terraform automatically creates or updates the dependency lock file each time you run the terraform init command.

You should include this file in your version control repository so that you can discuss potential changes to your external dependencies via code review, just as you would discuss potential changes to your configuration itself.

[https://learn.hashicorp.com/tutorials/terraform/provider-versioning](https://learn.hashicorp.com/tutorials/terraform/provider-versioning)

## Terraform plan (cmd)

  
Generate an execution plan and display that to the output of the command line, without -out the plan is not saved

## Terraform apply (cmd)

  
Apply the configuration in the execution plan.
The command line output shows the execution plan and will prompt you for approval (yes) before proceeding.
I can specify which plan to apply which created using -out switch

## Terraform.tfstate (file)

Terraform writes data into this file and it has the IDs and properties of the resources real-world infrastructure .
It could also contain sensitive values in plaintext, so do not share it or check it in to source control.
Terraform also gathered the resource's properties and meta-data and stores them in this file for later changes.
For teams or larger projects, consider storing your state remotely.

## Terraform show (cmd)
Inspect or show all the current state which exists in the state file
Terraform state (cmd)  Show specific information (slice and dice) from the current state which exists in the state file.
It has many switches like, list which list resources in the state.
There are some cases where you may need to modify the Terraform state. Rather than modify the state directly, the terraform state commands can be used in many cases instead.

e.g. I can
•  Rename a resource
•  Move resource to a module
•  Move a module into a module
•  Remove a resource
•  Remove a module
https://www.terraform.io/docs/cli/commands/state/index.html

## Terraform refresh

The terraform refresh command is used to reconcile the state Terraform knows about (via its state file) with the real-world infrastructure. This can be used to detect any drift from the last-known state, and to update the state file.

## Terraform plan -out=[myplan] (cmd)

Saving an execution plan with the out flag ensures your terraform apply operation runs the exact plan rather than applying new changes you may not have approved.

## Symbols:

~.  (normal colour)
•  It means change resource

+. (green colour)
•  It means add resource

-.  (red colour)
•  It means destroy resource

-/+ (red &green colour)
•  It means replace resource as it was tainted

## Terraform destroy

Remove infrastructure, as with apply, Terraform shows its execution plan and waits for approval before making any changes


## Value [""]

Square brackets define lists. The list is a sequence of comma-separated values. When you see square brackets around an argument, it tells you that the argument accepts more than one value.

Implicit dependencies  which Terraform and the Azure provider determine automatically based on the configuration. For example, if a virtual machine (VM) resource references a network interface (NIC), Terraform creates the NIC before the virtual machine.

Explicit dependencies  which you define using the depends_on meta-argument. Use this when you want to override the default execution plan.

## Variables.tf (file)

File to hold and declare all variables, to use them inside tf configuration use:
var.<.var name>

## Terraform.tfvars (file)

File to hold the values for all variables present in the current directory, Terraform automatically loads them to populate variables.
Also, the file can be named *.auto.tfvars

## Map (data type)

It’s a collection of string values grouped together
A variable can have a map type assigned explicitly, or it can be implicitly declared as a map by specifying a default value that is a map

## Object (data type)

Group different kinds of values, for example strings, bool values, and/or numbers
Lookup (function)  It does a dynamic lookup in a map (data type) variable for a key

Cmd:
lookup (var.map_var1, var.map_var2) and map_var2 is the key to search inside map_var1

## -var (flag)

To avoid being prompted for sensitive variable, use the -var flag to assign sensitive values to your variables.

Cmd:
terraform apply -var 'var1=val1' -var var2=val2'

## Output

Terraform outputs these values after an apply operation and you query these values using the terraform output command

## Remote backends

Allow Terraform to use a shared storage space for state data and it can be several services like Terraform Cloud, Azure Storage Account, and others.

To begin the migration of local state, reinitialize your project. This causes Terraform to recognize your changed backend configuration to a remote.

During reinitialization, Terraform presents a prompt saying that it will copy the state file to the new backend. Enter "yes" and Terraform will migrate the state from your local machine to the target service/place.

Locking  Most of remote backends support locking.
If someone else try to run terraform apply, they will have to wait until the current run is finished.
You can run apply with -lock-timeout <time>

## Amazon S3 as remote backend

Amazon S3. it supports state locking via Dynamo DB, which can be enabled by setting the dynamodb_table field to an existing DynamoDB table name.

## Terraform fmt (cmd)

It is used to rewrite Terraform configuration files to a canonical format and style.
Its primary goal is to encourage consistency of style between different Terraform codebases

## Terraform validate

Validate configuration in modules, attribute names, and value types.

## Provisioners

There will always be certain behaviors that can't be directly represented in Terraform's declarative model.

Provisioners can be used to model specific actions on the local machine or on a remote machine in order to prepare servers or other infrastructure objects for service.

## Types of Provisioners

local-exec Provisioner
remote-exec Provisioner

## Local-exec Provisioner

It invokes a local executable after a resource is created. This invokes a process on the machine running Terraform, not on the resource.
https://www.terraform.io/docs/language/resources/provisioners/local-exec.html

## Remote-exec Provisioner

The remote-exec provisioner invokes a script on a remote resource after it is created. This can be used to run a configuration management tool, bootstrap into a cluster, etc
https://www.terraform.io/docs/language/resources/provisioners/remote-exec.html

## How to use Local Provisioners

resource "aws_instance" "web" {
provisioner "local-exec" {
command = "echo The server's IP address is ${self.private_ip}"
}}

## How to use Remote Provisioners

resource "aws_instance" "web" {
provisioner "remote-exec" {
inline = [
"puppet apply",
"consul join ${aws_instance.web.private_ip}",
] } }

## Provisioners as a last resort.

Provisioners should only be used as a last resort. For most common situations there are better alternatives.

## Provisioners must connect

Provisioners must connect to the remote system using SSH or WinRM. You must include a connection block so that Terraform will know how to communicate with the server.


## Add provisioner block inside resource block

resource "aws_instance" "web" {
#...
provisioner "local-exec" {
command = "echo The server's IP address is ${self.private_ip}"
}
}

## Terraform taint (cmd)

Manually marks a Terraform-managed resource as tainted, forcing it to be destroyed and recreated on the next apply.

When we use terraform show, it will show the taint resources marked with (tainted) keywords.
https://www.terraform.io/docs/cli/commands/taint.

## Auto tainted

If a resource successfully creates but fails during provisioning, Terraform will error and mark the resource as "tainted“ as it can't be considered safe to use since provisioning failed.

## Symbol version = “~> 1.0”

Any version more than 1.0 and less than 2.0
 

 # To Be Continued .........
