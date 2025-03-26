
Terraform project for AWS VPC and EC2 deployment.
## Terraform AWS Infrastructure - VPC and EC2

### Step 1: Install Terraform
- Download Terraform from the official website: https://developer.hashicorp.com/terraform/downloads
- Extract the downloaded file and move it to a directory in your system's PATH
- Verify installation using:
  ```sh
  terraform -version
  ```

### Step 2: Configure AWS CLI
- Install AWS CLI from: https://aws.amazon.com/cli/
- Verify installation using:
  ```sh
  aws --version
  ```
- Configure AWS CLI with access keys:
  ```sh
  aws configure
  ```
  - Enter AWS Access Key ID
  - Enter AWS Secret Access Key
  - Specify a default AWS region (e.g., us-east-1)
  - Select output format (default: json)

### Step 3: Create a New GitHub Repository
- Go to GitHub and create a new repository
- Clone the repository locally:
  ```sh
  git clone https://github.com/jagadesh999/terraform-aws-vpc-ec2.git
  cd terraform-aws-vpc-ec2
  ```

### Step 4: Write Terraform Configuration Files
- Create a file `main.tf` and add the following content:

  ```hcl
  provider "aws" {
    region = "us-east-1"
  }

  resource "aws_vpc" "terraform vpc" {
    cidr_block = "10.0.0.0/16"
    tags = {
      Name = "MyVPC"
    }
  }

  resource "aws_subnet" "my_subnet" {
    vpc_id                  = aws_vpc.my_vpc.id
    cidr_block              = "10.0.1.0/24"
    map_public_ip_on_launch = true
    availability_zone       = "us-east-1a"
    tags = {
      Name = "MySubnet"
    }
  }

  resource "aws_internet_gateway" "my_gw" {
    vpc_id = aws_vpc.my_vpc.id
  }

  resource "aws_route_table" "my_route_table" {
    vpc_id = aws_vpc.my_vpc.id
  }

  resource "aws_route" "default_route" {
    route_table_id         = aws_route_table.my_route_table.id
    destination_cidr_block = "0.0.0.0/0"
    gateway_id             = aws_internet_gateway.my_gw.id
  }

  resource "aws_route_table_association" "subnet_association" {
    subnet_id      = aws_subnet.my_subnet.id
    route_table_id = aws_route_table.my_route_table.id
  }

  resource "aws_security_group" "my_sg" {
    vpc_id = aws_vpc.my_vpc.id
    ingress {
      from_port   = 22
      to_port     = 22
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
    ingress {
      from_port   = 80
      to_port     = 80
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
    }
    egress {
      from_port   = 0
      to_port     = 0
      protocol    = "-1"
      cidr_blocks = ["0.0.0.0/0"]
    }
    tags = {
      Name = "MySecurityGroup"
    }
  }

  resource "aws_instance" "my_ec2" {
    ami                    = "ami-0c55b159cbfafe1f0"
    instance_type          = "t2.micro"
    subnet_id              = aws_subnet.my_subnet.id
    vpc_security_group_ids = [aws_security_group.my_sg.id]
    key_name               = "jagga.pem"
    tags = {
      Name = "jagadesh"
    }
  }
  ```

### Step 5: Initialize Terraform
- Run the following command:
  ```sh
  terraform init
  ```

### Step 6: Plan and Apply Terraform Configuration
- Preview the execution plan:
  ```sh
  terraform plan
  ```
- Apply the configuration:
  ```sh
  terraform apply -auto-approve
  ```

### Step 7: Verify Deployment
- Check AWS Console to verify the created VPC, Subnet, Internet Gateway, Route Table, Security Group, and EC2 instance.
- Use SSH to connect to the EC2 instance:
  ```sh
  ssh -i jagga.pem ec2-user@<192.16.80.0
  ```


### Step 9: Destroy Resources (Optional)
- To delete all resources created by Terraform:
  ```sh
  terraform destroy -auto-approve
  ```


