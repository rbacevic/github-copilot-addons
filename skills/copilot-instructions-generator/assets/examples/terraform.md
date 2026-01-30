# Terraform Infrastructure

Infrastructure as Code using Terraform best practices.

## Tech Stack

- **IaC**: Terraform 1.6+
- **Backend**: Remote state (S3/GCS/Azure Storage)
- **Linting**: tflint
- **Security**: tfsec / checkov
- **Docs**: terraform-docs

## Project Structure

- `environments/` - Environment-specific configs
  - `dev/`
  - `staging/`
  - `prod/`
- `modules/` - Reusable modules
- `scripts/` - Helper scripts

## Development Commands

- **Init**: `terraform init`
- **Validate**: `terraform validate`
- **Plan**: `terraform plan -out=tfplan`
- **Apply**: `terraform apply tfplan`
- **Destroy**: `terraform destroy`
- **Format**: `terraform fmt -recursive`
- **Lint**: `tflint --recursive`

## Before Committing

- Run `terraform fmt -recursive`
- Run `terraform validate`
- Run `tflint` for best practices
- Run security scanner (tfsec/checkov)
- Update documentation if needed

## Coding Guidelines

### File Organization
```
module/
├── main.tf        # Primary resources
├── variables.tf   # Input variables
├── outputs.tf     # Output values
├── versions.tf    # Version constraints
├── locals.tf      # Local values (optional)
├── data.tf        # Data sources (optional)
└── README.md      # Module documentation
```

### Version Constraints
```hcl
terraform {
  required_version = ">= 1.6.0"
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}
```

### Variables
```hcl
variable "name" {
  description = "Name of the resource"
  type        = string
}

variable "environment" {
  description = "Environment name"
  type        = string
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Must be dev, staging, or prod."
  }
}

variable "config" {
  description = "Configuration object"
  type = object({
    size     = string
    replicas = number
    enabled  = bool
  })
  default = {
    size     = "small"
    replicas = 1
    enabled  = true
  }
}
```

### Outputs
```hcl
output "id" {
  description = "Resource ID"
  value       = resource.example.id
}

output "endpoint" {
  description = "Resource endpoint"
  value       = resource.example.endpoint
  sensitive   = true
}
```

### Locals
```hcl
locals {
  common_tags = {
    Environment = var.environment
    ManagedBy   = "Terraform"
    Project     = var.project
  }
  
  name_prefix = "${var.project}-${var.environment}"
}
```

### Resource Patterns
```hcl
resource "example_resource" "main" {
  name = "${local.name_prefix}-resource"
  
  # Required arguments first
  required_arg = var.required_value
  
  # Optional arguments
  optional_arg = var.optional_value
  
  # Dynamic blocks
  dynamic "nested_block" {
    for_each = var.nested_configs
    content {
      key   = nested_block.value.key
      value = nested_block.value.value
    }
  }
  
  # Lifecycle rules at end
  lifecycle {
    create_before_destroy = true
  }
  
  tags = local.common_tags
}
```

### Count vs For Each
```hcl
# Use count for simple conditional or numeric iteration
resource "example" "conditional" {
  count = var.create_resource ? 1 : 0
  name  = "example"
}

# Use for_each for maps or sets
resource "example" "multiple" {
  for_each = var.resource_configs
  
  name   = each.key
  config = each.value
}
```

### Modules
```hcl
module "vpc" {
  source = "./modules/vpc"
  
  name        = local.name_prefix
  cidr_block  = var.vpc_cidr
  environment = var.environment
  tags        = local.common_tags
}
```

### Conditionals
```hcl
resource "example" "main" {
  name = var.custom_name != null ? var.custom_name : "${local.name_prefix}-default"
  
  # Conditional nested block
  dynamic "optional_config" {
    for_each = var.enable_feature ? [1] : []
    content {
      enabled = true
    }
  }
}
```

## State Management

- Use remote backend (never local for team projects)
- Enable state locking
- Use separate state per environment
- Enable encryption at rest
- Restrict access to state storage

## Module Design

- Single responsibility per module
- Clear input/output interface
- Document all variables
- Provide sensible defaults
- Include usage examples

## Key Guidelines

1. Always use version constraints
2. Run plan before apply
3. Use modules for reusability
4. Tag/label all resources
5. Never commit secrets or state files
