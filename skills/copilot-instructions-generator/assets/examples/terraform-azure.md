# Terraform Azure Infrastructure

Infrastructure as Code for Azure using Terraform.

## Tech Stack

- **IaC**: Terraform 1.6+
- **Provider**: azurerm 3.x
- **Backend**: Azure Storage Account
- **CI/CD**: Azure DevOps / GitHub Actions
- **Linting**: tflint + tfsec

## Project Structure

- `environments/` - Environment-specific configurations
  - `dev/` - Development environment
  - `staging/` - Staging environment
  - `prod/` - Production environment
- `modules/` - Reusable Terraform modules
  - `networking/` - VNet, subnets, NSGs
  - `compute/` - VMs, AKS, App Services
  - `storage/` - Storage accounts, databases
  - `security/` - Key Vault, identities
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

- Run `terraform fmt -recursive` for formatting
- Run `terraform validate` for syntax check
- Run `tflint` for best practices
- Run `tfsec` for security scan
- Review `terraform plan` output

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
- Resources: `{resource_type}_{purpose}` (e.g., `rg_main`, `vnet_hub`)
- Variables: lowercase with underscores
- Use consistent naming across environments
- Azure resources: follow Azure naming conventions

### Provider Configuration
```hcl
terraform {
  required_version = ">= 1.6.0"
  
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }
  
  backend "azurerm" {
    resource_group_name  = "rg-terraform-state"
    storage_account_name = "stterraformstate"
    container_name       = "tfstate"
    key                  = "terraform.tfstate"
  }
}

provider "azurerm" {
  features {}
}
```

### Variables
```hcl
variable "environment" {
  description = "Environment name (dev, staging, prod)"
  type        = string
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

variable "tags" {
  description = "Tags to apply to all resources"
  type        = map(string)
  default     = {}
}
```

### Resource Patterns
```hcl
resource "azurerm_resource_group" "main" {
  name     = "rg-${var.project}-${var.environment}"
  location = var.location
  tags     = local.common_tags
}

resource "azurerm_virtual_network" "main" {
  name                = "vnet-${var.project}-${var.environment}"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  address_space       = [var.vnet_address_space]
  tags                = local.common_tags
}
```

### Locals
```hcl
locals {
  common_tags = merge(var.tags, {
    Environment = var.environment
    ManagedBy   = "Terraform"
    Project     = var.project
  })
}
```

### Modules
```hcl
module "aks" {
  source = "../../modules/aks"
  
  cluster_name        = "aks-${var.project}-${var.environment}"
  resource_group_name = azurerm_resource_group.main.name
  location            = azurerm_resource_group.main.location
  kubernetes_version  = var.kubernetes_version
  node_pool_config    = var.node_pool_config
  tags                = local.common_tags
}
```

### Data Sources
```hcl
data "azurerm_client_config" "current" {}

data "azurerm_key_vault_secret" "db_password" {
  name         = "db-password"
  key_vault_id = azurerm_key_vault.main.id
}
```

## Security Best Practices

- Use Azure Key Vault for secrets
- Enable encryption at rest
- Use Private Endpoints where possible
- Implement NSG rules (deny by default)
- Use Managed Identities over service principals
- Enable diagnostic logging

## State Management

- Use remote state in Azure Storage
- Enable state locking with blob lease
- Use workspaces or separate state files per environment
- Never commit state files to git

## Key Guidelines

1. Use modules for reusable components
2. Tag all resources consistently
3. Use data sources for existing resources
4. Implement least-privilege access
5. Plan before apply - always review changes
