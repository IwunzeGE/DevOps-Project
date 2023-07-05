# AUTOMATE INFRASTRUCTURE WITH IAC USING TERRAFORM PART 2

## Networking - Private subnets & best practices

- Create 4 private subnets keeping in mind following principles:

    - Make sure you use variables or length() function to determine the number of AZs.
    - Use variables and cidrsubnet() function to allocate vpc_cidr for subnets.
    - Keep variables and resources in separate files for better code structure and readability.
    - Tags all the resources you have created so far. Explore how to use `format()` and `count` functions to automatically tag subnets with its respective number.


