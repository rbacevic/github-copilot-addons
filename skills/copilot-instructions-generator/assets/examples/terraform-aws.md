# Terraform AWS Infrastructure

Infrastructure as Code for AWS using Terraform.

## Tech Stack

- **IaC**: Terraform 1.6+
- **Provider**: aws 5.x
- **Backend**: S3 + DynamoDB
- **CI/CD**: GitHub Actions / AWS CodePipeline
- **Linting**: tflint + checkov

## Project Structure

- `environments/` - Environment configurations
  - `dev/` - Development
  - `staging/` - Staging  
  - `prod/` - Production
- `modules/` - Reusable modules
  - `vpc/` - VPC, subnets, routing
  - `eks/` - EKS cluster
  - `rds/` - Database instances
  - `s3/` - S3 buckets
  - `iam/` - IAM roles and policies
- `scripts/` - Helper scripts

## Development Commands

- **Init**: `terraform init`
- **Validate**: `terraform validate`
- **Plan**: `terraform plan -out=tfplan`
- **Apply**: `terraform apply tfplan`
- **Destroy**: `terraform destroy`
- **Format**: `terraform fmt -recursive`
- **Lint**: `tflint --recursive`
- **Security**: `checkov -d .`

## Before Committing

- Run `terraform fmt -recursive`
- Run `terraform validate`
- Run `tflint` for AWS best practices
- Run `checkov` for security scanning
- Review plan output carefully

## Coding Guidelines

### File Organization
- `main.tf` - Primary resources
- `variables.tf` - Input variables
- `outputs.tf` - Output values
- `providers.tf` - Provider configuration
- `versions.tf` - Version constraints
- `locals.tf` - Local values
- `data.tf` - Data sources

### Naming Conventions
- Resources: descriptive names with environment suffix
- Use consistent tagging strategy
- Follow AWS naming best practices
- S3 buckets: globally unique, lowercase

### Provider Configuration
```hcl
terraform {
  required_version = ">= 1.6.0"
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
  
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "env/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}

provider "aws" {
  region = var.aws_region
  
  default_tags {
    tags = local.common_tags
  }
}
```

### Variables
```hcl
variable "environment" {
  description = "Environment name"
  type        = string
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Valid values: dev, staging, prod."
  }
}

variable "vpc_cidr" {
  description = "CIDR block for VPC"
  type        = string
  default     = "10.0.0.0/16"
}
```

### Resource Patterns
```hcl
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_hostnames = true
  enable_dns_support   = true
  
  tags = {
    Name = "${var.project}-${var.environment}-vpc"
  }
}

resource "aws_subnet" "private" {
  count             = length(var.availability_zones)
  vpc_id            = aws_vpc.main.id
  cidr_block        = cidrsubnet(var.vpc_cidr, 8, count.index)
  availability_zone = var.availability_zones[count.index]
  
  tags = {
    Name = "${var.project}-${var.environment}-private-${count.index + 1}"
    Tier = "private"
  }
}
```

### Locals
```hcl
locals {
  common_tags = {
    Environment = var.environment
    Project     = var.project
    ManagedBy   = "Terraform"
    Owner       = var.owner
  }
  
  azs = slice(data.aws_availability_zones.available.names, 0, 3)
}
```

### Data Sources
```hcl
data "aws_caller_identity" "current" {}

data "aws_availability_zones" "available" {
  state = "available"
}

data "aws_ami" "amazon_linux" {
  most_recent = true
  owners      = ["amazon"]
  
  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }
}
```

### IAM Patterns
```hcl
resource "aws_iam_role" "lambda" {
  name = "${var.project}-lambda-role"
  
  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Action = "sts:AssumeRole"
      Effect = "Allow"
      Principal = {
        Service = "lambda.amazonaws.com"
      }
    }]
  })
}
```

## Security Best Practices

- Use AWS Secrets Manager for secrets
- Enable encryption at rest (S3, RDS, EBS)
- Use VPC endpoints for AWS services
- Implement least-privilege IAM policies
- Enable CloudTrail and VPC Flow Logs
- Use security groups as allowlists

## State Management

- Use S3 backend with versioning enabled
- Enable DynamoDB locking
- Use separate state files per environment
- Enable server-side encryption
- Restrict bucket access with IAM

## Key Guidelines

1. Use modules for repeated patterns
2. Tag all resources consistently
3. Use count/for_each for similar resources
4. Implement proper IAM boundaries
5. Always review plan before apply
