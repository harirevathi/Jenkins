# Terraform Block
terraform {
  required_version = "~> 1.6.0" 
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}

# Provider Block
provider "aws" {
  region  = "us-east-1"
  profile = "default"
  access_key = "AKIA6GBMETRIEBOIVVAF"
  secret_key = "avIkCKQPbfzEkLVqKS3wUJs5/wRcUxJxis8bks1K"
}

# Resource-1: Create VPC
resource "aws_vpc" "vpc-dev" {
  cidr_block = "10.0.0.0/16"
  tags = {
    "Name" = "vpc-dev"
  }
}

# Resource-2: Create Subnets
resource "aws_subnet" "vpc-dev-public-subnet-1" {
vpc_id = aws_vpc.vpc-dev.id
availability_zone = "us-east-1a"
cidr_block = "10.0.1.0/24"
map_public_ip_on_launch = true

tags = {
  "Name" = "subnet-1"
}
}

# Resource-3: Internet Gateway
resource "aws_internet_gateway" "vpc-dev-igw" {
vpc_id = aws_vpc.vpc-dev.id
}

# Resource-4: Create Route Table
resource "aws_route_table" "vpc-dev-public-route-table" {
vpc_id = aws_vpc.vpc-dev.id 
}

# Resource-5: Create Route in Route Table for Internet Access
resource "aws_route" "vpc-dev-public-route" {
route_table_id = aws_route_table.vpc-dev-public-route-table.id
destination_cidr_block = "0.0.0.0/0"
gateway_id = aws_internet_gateway.vpc-dev-igw.id
}

# Resource-6: Associate the Route Table with the Subnet
resource "aws_route_table_association" "vpc-dev-public-route-table-associate" {
route_table_id = aws_route_table.vpc-dev-public-route-table.id
subnet_id = aws_subnet.vpc-dev-public-subnet-1.id
}

# Resource-7: Create Security Group
resource "aws_security_group" "dev-vpc-sg" {
  name = "dev-vpc-default-sg"
  description = "Dev VPC Default Security Group"
  vpc_id = aws_vpc.vpc-dev.id

ingress  {
    description = "Allow Port 22"
    from_port   = 22
    to_port     = 22
    protocol = "tcp"
    cidr_blocks = ["0.0.0.0/0"]   
}

ingress  {
    description = "Allow Port 80"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

ingress  {
    description = "Allow all traffic"
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

 egress {
    description = "Allow all IP and Ports Outbound"
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
}
}
# Resource-8: Create EC2 Instance
resource "aws_instance" "my-ec2-vm" {
  ami                    = "ami-080e1f13689e07408" # ubuntu
  instance_type          = "t2.micro"
  key_name               = "putyyy"
  subnet_id              = aws_subnet.vpc-dev-public-subnet-1.id
  vpc_security_group_ids = [aws_security_group.dev-vpc-sg.id]
  tags = {
    "Name" = "kafka_broker"
  }
}


