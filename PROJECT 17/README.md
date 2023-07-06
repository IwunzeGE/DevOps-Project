# AUTOMATE INFRASTRUCTURE WITH IAC USING TERRAFORM PART 2

## Networking - Private subnets & best practices

- Create 4 private subnets keeping in mind following principles:

    - Make sure you use variables or length() function to determine the number of AZs.
    - Use variables and cidrsubnet() function to allocate vpc_cidr for subnets.
    - Keep variables and resources in separate files for better code structure and readability.
    - Tags all the resources you have created so far. Explore how to use `format()` and `count` functions to automatically tag subnets with its respective number.

**Note:** You can add multiple tags as a default set. for example, in out `terraform.tfvars` file we can have default tags defined.

```
tags = {
  Enviroment      = "production" 
  Owner-Email     = "dare@darey.io"
  Managed-By      = "Terraform"
  Billing-Account = "1234567890"
}
```
![Alt text](images/tag2.png)


Now you can tag all you resources using the format below

```
tags = merge(
    var.tags,
    {
      Name = "Name of the resource"
    },
  )
```
![Alt text](images/tag.png)

**NOTE:** Update the `variables.tf` to declare the variable tags used in the format above;

```
variable "tags" {
  description = "A mapping of tags to assign to all resources."
  type        = map(string)
  default     = {}
}
```
![Alt text](images/variable.png)

Anytime we need to make a change to the tags, we simply do that in one single place `(terraform.tfvars)`. But, our key-value pairs are hard coded. So, go ahead and work out a fix for that. Simply create variables for each value and use var.variable_name as the value to each of the keys.

## Internet Gateways & format() function

- Create an Internet Gateway in a separate Terraform file `internet_gateway.tf`

```python
resource "aws_internet_gateway" "ig" {
  vpc_id = aws_vpc.main.id


  tags = merge(
    var.tags,
    {
      Name = format("%s-%s!", aws_vpc.main.id,"IG")
    } 
  )
}
```

The `format()` functionis used to dynamically generate a unique name for this resource? The first part of the `%s` takes the interpolated value of `aws_vpc.main.id` while the second `%s` appends a literal string `IG` and finally an exclamation mark is added in the end. If any of the resources being created is either using the `count` function, or creating multiple resources using a loop, then a key-value pair that needs to be unique must be handled differently.
For example, each of our subnets should have a unique name in the tag section. Without the `format()` function, we would not be able to see uniqueness. With the format function, each private subnet’s tag will look like this.

```
Name = PrvateSubnet-0
Name = PrvateSubnet-1
Name = PrvateSubnet-2
```

Lets try and see that in action.

```python
  tags = merge(
    var.tags,
    {
      Name = format("PrivateSubnet-%s", count.index)
    } 
  )
```

## NAT Gateways

- Create 1 NAT Gateways and 1 Elastic IP (EIP) addresses

    - Create the NAT Gateways in a new file called `natgateway.tf`.

**Note:** We need to create an Elastic IP for the NAT Gateway, and you can see the use of depends_on to indicate that the Internet Gateway resource must be available before this should be created. Although Terraform does a good job to manage dependencies, but in some cases, it is good to be explicit.
You can read more on dependencies [here](https://www.terraform.io/docs/language/meta-arguments/depends_on.html)

```python
resource "aws_eip" "nat_eip" {
  vpc        = true
  depends_on = [aws_internet_gateway.ig]


  tags = merge(
    var.tags,
    {
      Name = format("%s-EIP", var.name)
    },
  )
}


resource "aws_nat_gateway" "nat" {
  allocation_id = aws_eip.nat_eip.id
  subnet_id     = element(aws_subnet.public.*.id, 0)
  depends_on    = [aws_internet_gateway.ig]


  tags = merge(
    var.tags,
    {
      Name = format("%s-Nat", var.name)
    },
  )
}
```

## AWS Routes

- Create a file called `route_tables.tf` and use it to create routes for both public and private subnets, create the below resources. Ensure they are properly tagged.
    - aws_route_table
    - aws_route
    - aws_route_table_association

```python    
# create private route table
resource "aws_route_table" "private-rtb" {
  vpc_id = aws_vpc.main.id


  tags = merge(
    var.tags,
    {
      Name = format("%s-Private-Route-Table", var.name)
    },
  )
}

# associate all private subnets to the private route table
resource "aws_route_table_association" "private-subnets-assoc" {
  count          = length(aws_subnet.private[*].id)
  subnet_id      = element(aws_subnet.private[*].id, count.index)
  route_table_id = aws_route_table.private-rtb.id
}


# create route table for the public subnets
resource "aws_route_table" "public-rtb" {
  vpc_id = aws_vpc.main.id


  tags = merge(
    var.tags,
    {
      Name = format("%s-Public-Route-Table", var.name)
    },
  )
}


# create route for the public route table and attach the internet gateway
resource "aws_route" "public-rtb-route" {
  route_table_id         = aws_route_table.public-rtb.id
  destination_cidr_block = "0.0.0.0/0"
  gateway_id             = aws_internet_gateway.ig.id
}


# associate all public subnets to the public route table
resource "aws_route_table_association" "public-subnets-assoc" {
  count          = length(aws_subnet.public[*].id)
  subnet_id      = element(aws_subnet.public[*].id, count.index)
  route_table_id = aws_route_table.public-rtb.id
}
```

Now if you run `terraform plan` and `terraform apply` it will add the following resources to AWS in multi-az set up:

```
– Our main vpc
– 2 Public subnets
– 4 Private subnets
– 1 Internet Gateway
– 1 NAT Gateway
– 1 EIP
– 2 Route tables
```
Now, we are done with Networking part of AWS set up, let us move on to Compute and Access Control configuration automation using Terraform!

## IaM and Roles

Lets pass an IAM role our EC2 instances to give them access to some specific resources, so we need to do the following:

1. Create `AssumeRole` : Assume Role uses Security Token Service (STS) API that returns a set of temporary security credentials that you can use to access AWS resources that you might not normally have access to. These temporary credentials consist of an access key ID, a secret access key, and a security token. Typically, you use AssumeRole within your account or for cross-account access.

- Add the following code to a new file named `roles.tf`

```python
resource "aws_iam_role" "ec2_instance_role" {
name = "ec2_instance_role"
  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Sid    = ""
        Principal = {
          Service = "ec2.amazonaws.com"
        }
      },
    ]
  })


  tags = merge(
    var.tags,
    {
      Name = "aws assume role"
    },
  )
}
```

In above code we are creating `AssumeRole` with `AssumeRole policy`. It is granting EC2, permissions to assume the role.

2. Create `IAM policy` for this role: This is where we need to define a required policy (i.e., permissions) according to our requirements. For example, allowing an IAM role to perform action describe applied to EC2 instances:

```python
resource "aws_iam_policy" "policy" {
  name        = "ec2_instance_policy"
  description = "A test policy"
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = [
          "ec2:Describe*",
        ]
        Effect   = "Allow"
        Resource = "*"
      },
    ]


  })


  tags = merge(
    var.tags,
    {
      Name =  "aws assume policy"
    },
  )


}
```

3. Attach the Policy to the IAM Role: This is where, we will be attaching the policy which we created above, to the role we created in the first step.

```python
    resource "aws_iam_role_policy_attachment" "test-attach" {
        role       = aws_iam_role.ec2_instance_role.name
        policy_arn = aws_iam_policy.policy.arn
    }
    
#Create an Instance Profile and interpolate the IAM Role
    resource "aws_iam_instance_profile" "ip" {
        name = "aws_instance_profile_test"
        role =  aws_iam_role.ec2_instance_role.name
    }
```


