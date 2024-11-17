# Lab: Benefits of State

## Terraform State
Terraform uses state to properly and correctly manage your infrastructure resources. The state is essential for comparing the current infrastructure with the desired configuration, planning changes, and applying them while leaving valid resources untouched.

---

## Benefits of State
- Examines the current infrastructure state during execution.
- Identifies differences between the current state and the desired state.
- Determines necessary changes and applies them without affecting valid infrastructure.

---

## Tasks Overview
1. **Show Current State**
2. **Update Configuration**
3. **Plan and Execute Changes**
4. **Show New State**

   ![image](https://github.com/user-attachments/assets/86db6132-7e0b-4d07-bca9-d56d68e619e6)


# Task 1: Show Current State
In a previous lab, you wrote some Terraform configuration using the HashiCorp Configuration Language to create a new VPC in AWS. Now that the VPC exists, we will build an EC2 instance in one of the public subnets. Since the VPC still exists, the only change that Terraform needs to address is the addition of the EC2 instance.

## Step 1.1.1
On your workstation, navigate to the `/workstation/terraform` directory. To view the applied configuration, utilize the `terraform show` command to view the resources created and find the IP address for your instance.

**Note:** If this command doesn't yield any information, you will need to redeploy your VPC infrastructure following the steps in Objective 1b. This directory should contain both a `main.tf` and `variables.tf` file.

```bash
terraform show
```
# Task 2: Update your Configuration to include EC2 instance
Terraform can perform in-place updates after changes are made to the `main.tf` configuration file. Update your `main.tf` to include an EC2 instance in the public subnet:

Append the following code to `main.tf`:

```hcl
# Terraform Data Block - To Lookup Latest Ubuntu 20.04 AMI Image
data "aws_ami" "ubuntu" {
  most_recent = true

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }

  owners = ["099720109477"]
}

# Terraform Resource Block - To Build EC2 instance in Public Subnet
resource "aws_instance" "web_server" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"
  subnet_id     = aws_subnet.public_subnets["public_subnet_1"].id
  tags = {
    Name = "Ubuntu EC2 Server"
  }
}
```

Save the configuration.

# Task 3: Plan and Execute Changes
Plan and apply the changes you just made and note the output differences for additions, deletions, and in-place changes.

## Step 3.1.1
Run a `terraform plan` to see the updates that Terraform needs to make.

```bash
terraform plan
```

## Step 3.1.2
Run a `terraform apply` to execute the updates that Terraform needs to make.

```bash
terraform apply
```

# Task 4: Show New State
To view the applied configuration, utilize the `terraform show` command to view the resources created. Look for the `aws_instance.web_server`, which is now present within Terraform's managed state.

```bash
terraform show
```

Alternatively, you can run `terraform state list` to list all items in Terraform's managed state.

Example:
```
terraform state list
data.aws_ami.ubuntu
aws_instance.web_server
...
```
