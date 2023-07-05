# AUTOMATE INFRASTRUCTURE WITH IAC USING TERRAFORM PART 1

In Project 15 earlier, The below architecture was provisioned manually using the AWS Console.

![Alt text](images/architecture.png)

## Prerequisites

- You must have completed Terraform fundamental course.
- Create an IAM user, name it terraform (ensure that the user has only programatic access to your AWS account) and grant this user AdministratorAccess permissions.
- Copy the secret access key and access key ID. Save them in a notepad temporarily.
- Configure programmatic access from your workstation to connect to AWS using the access keys copied above and a Python SDK (boto3). You must have Python 3.6 or higher on your workstation.
If you are on Windows, use gitbash, if you are on a Mac, you can simply open a terminal. Read here to configure the Python SDK properly.
- For easier authentication configuration – use AWS CLI with `aws configure` command.
![Alt text](<images/aws configure.png>)

- Create an S3 bucket to store Terraform state file. You can name it something like `<yourname>-dev-terraform-bucket`.
![Alt text](<images/s3 create bucket.png>)

**(Note: S3 bucket names must be unique unique within a region partition, you can read about S3 bucken naming in this article)**. We will use this bucket from Project-17 onwards.

- When you have configured authentication and installed boto3, make sure you can programmatically access your AWS account by running following commands.

`python`  

```python
import boto3
s3 = boto3.resource('s3')
for bucket in s3.buckets.all():
    print(bucket.name)
```
![Alt text](<images/bucket check.png>)

- Install Terraform. Check the official installation [documentation](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli#install-terraform). 

For Windows,
- Open `Powershell` in admin mode.
- Run `choco install terraform`.

**Note: You must have `Chocolatey` previously installed.**

## VPC | Subnets | Security Groups

### Create a directory structure
NB: I'd be using Visual Studio Code IDE.
- Create a folder called `PBL`
- Create a file in the folder, name it `main.tf`

- Add AWS as a provider, and a resource to create a VPC in the `main.tf` file. The provider block informs Terraform that we intend to build infrastructure within AWS.

- Resource block will create a VPC.
```python
provider "aws" {
  region = "eu-central-1"
}

# Create VPC
resource "aws_vpc" "main" {
  cidr_block                     = "172.16.0.0/16"
  enable_dns_support             = "true"
  enable_dns_hostnames           = "true"
  enable_classiclink             = "false"
  enable_classiclink_dns_support = "false"
}
```
**Note: You can change the configuration above to create your VPC in other region that is closer to you. The same applies to all configuration snippets that will follow.**

- Download necessary plugins for Terraform to work. These plugins are used by providers and provisioners. At this stage, we only have provider in our `main.tf` file. So, Terraform will just download plugin for AWS provider. 
Accomplish this with `terraform init` command as seen in the below demonstration.

![Alt text](<images/terraform init.png>)

- Ccreate the only resource we just defined. `aws_vpc`. But before we do that, we should check to see what terraform intends to create before we tell it to go ahead and create it.
Run `terraform plan`.

![Alt text](<images/terraform plan.png>)

- If you are happy with changes planned, execute `terraform apply`.

![Alt text](<images/terraform apply.png>)

## Subnets resource section

According to the architectural design, we require 6 subnets:

- 2 public
- 2 private for webservers
- 2 private for data layer

Let us create the first 2 public subnets. Add below configuration to the `main.tf` file:

```python
# Create public subnets1
    resource "aws_subnet" "public1" {
    vpc_id                     = aws_vpc.main.id
    cidr_block                 = "172.16.0.0/24"
    map_public_ip_on_launch    = true
    availability_zone          = "eu-central-1a"


}


# Create public subnet2
    resource "aws_subnet" "public2" {
    vpc_id                     = aws_vpc.main.id
    cidr_block                 = "172.16.1.0/24"
    map_public_ip_on_launch    = true
    availability_zone          = "eu-central-1b"
}
```

We are creating 2 subnets, therefore declaring 2 resource blocks – one for each of the subnets. We are using the vpc_id argument to interpolate the value of the VPC id by setting it to aws_vpc.main.id. This way, Terraform knows inside which VPC to create the subnet.

Run `terraform plan` and `terraform apply`.

### Observations:

- Hard coded values: Remember our best practice hint from the beginning? Both the availability_zone and cidr_block arguments are hard coded. We should always endeavour to make our work dynamic.
- Multiple Resource Blocks: Notice that we have declared multiple resource blocks for each subnet in the code. This is bad coding practice. We need to create a single resource block that can dynamically create resources without specifying multiple blocks. Imagine if we wanted to create 10 subnets, our code would look very clumsy. So, we need to optimize this by introducing a count argument.

Now let us improve our code by refactoring it.

First, destroy the current infrastructure. Since we are still in development, this is totally fine. Otherwise, DO NOT DESTROY an infrastructure that has been deployed to production.
To destroy whatever has been created run `terraform destroy` command, and type `yes` after evaluating the plan.

![Alt text](images/destroy1.png)
![Alt text](images/destroyed2.png)
