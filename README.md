# Terraform-String-Variable

Variables in Terraform
----------------------
1. Local Variables

2. Input Variables
-------------------
Values which are required before creating any resource.
eg:- ami-id, instance type, key name etc.,

Types of Input variables
------------------------
string, list(string), map, count 


/c/Linux AWS DevOps/Terraform/variables

String Varibles
---------------

provider.tf
-----------
provider "aws" {
  region     = "us-east-1"
  version = "~> 4.0"
  access_key = "AKIAS5QJFNKWB2YIVC22"
  secret_key = "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"
} 


aws_instance.tf
---------------
resource "aws_instance" "web" {
  ami           = var.ami-id
  instance_type = var.instance_type
  key_name   = var.key_name 

  tags = {
    Name = var.tag_name
  }
}


variables.tf
------------
variable "ami-id" {
  type    = string
  default = "ami-090fa75af13c156b4"
}

variable "instance_type" {
  type    = string
  default = "t2.micro"
}

variable "key_name" {
  type    = string
  default = "8ambatch"
}

variable "tag_name" {
  type    = string
  default = "Terraform"
}


Count Variable
--------------
If i want to create 2 instances then i need to provide count = 2 in aws_instance.tf. 

aws_instance.tf
---------------
resource "aws_instance" "web" {
  ami           = var.ami-id
  instance_type = var.instance_type
  key_name   = var.key_name 

  tags = {
    Name = var.tag_name
  }
}


Now the created 2 instances will be with same name, same availability zones and all other will be same, but i want to create my 2 instances in 2 different availability zones. So, for that we need to use list(string).


aws_instance.tf
---------------
resource "aws_instance" "web" {
  ami           = var.ami-id
  instance_type = var.instance_type
  key_name   = var.key_name 
  count             = var.instance_count
  availability_zone = element(var.availability_zones, count.index)

  tags = {
    Name = element(var.instance_tags, count.index)
  }
}


variables.tf
------------
variable "ami-id" {
  type    = string
  default = "ami-090fa75af13c156b4"
}

variable "instance_type" {
  type    = string
  default = "t2.micro"
}

variable "key_name" {
  type    = string
  default = "8ambatch"
}

variable "tag_name" {
  type    = string
  default = "Terraform"
}


variable "ami_id" {
  type = map
  default = {
    "us-east-1" = "ami-04d29b6f966df1537"
    "us-east-2" = "ami-09558250a3419e7d0"
  } 
}
variable "instance_count" {
  default = 2
}

variable "availability_zones" {
  type    = list(string)
  default = ["us-east-1a","us-east-1b"]
}

variable "instance_tags" {
  type = list(string)
  default = ["HelloWorld-1","HelloWorld-2"]
}
