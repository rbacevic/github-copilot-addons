# Azure Bicep Infrastructure

Infrastructure as Code for Azure using Bicep.

## Tech Stack

- **IaC**: Bicep
- **Platform**: Azure
- **CI/CD**: Azure DevOps / GitHub Actions
- **Linting**: Bicep linter (built-in)
- **Testing**: Azure Deployment What-If

## Project Structure

- `modules/` - Reusable Bicep modules
  - `networking/` - VNet, subnets, NSGs
  - `compute/` - VMs, AKS, App Services
  - `storage/` - Storage accounts, databases
  - `security/` - Key Vault, identities
- `environments/` - Environment parameters
  - `dev.bicepparam`
  - `staging.bicepparam`
  - `prod.bicepparam`
- `main.bicep` - Main deployment template

## Development Commands

- **Build**: `az bicep build --file main.bicep`
- **Lint**: `az bicep lint --file main.bicep`
- **What-If**: `az deployment group what-if --template-file main.bicep --parameters @dev.bicepparam`
- **Deploy**: `az deployment group create --template-file main.bicep --parameters @dev.bicepparam`
- **Format**: `az bicep format --file main.bicep`

## Before Committing

- Run `az bicep lint` for validation
- Run `az bicep format` for consistent formatting
- Run what-if deployment to preview changes
- Review generated ARM template if needed

## Coding Guidelines

### File Organization
- `main.bicep` - Orchestration file
- `modules/*.bicep` - Reusable modules
- `*.bicepparam` - Parameter files

### Naming Conventions
- Parameters: camelCase (`resourceGroupName`)
- Variables: camelCase (`storageAccountName`)
- Resources: camelCase symbolic names
- Outputs: camelCase

### Parameters
```bicep
@description('Environment name')
@allowed(['dev', 'staging', 'prod'])
param environment string

@description('Azure region for deployment')
param location string = resourceGroup().location

@description('Tags to apply to all resources')
param tags object = {}

@secure()
@description('Admin password for VM')
param adminPassword string
```

### Variables
```bicep
var resourcePrefix = '${projectName}-${environment}'
var commonTags = union(tags, {
  Environment: environment
  ManagedBy: 'Bicep'
})
```

### Resources
```bicep
resource storageAccount 'Microsoft.Storage/storageAccounts@2023-01-01' = {
  name: '${resourcePrefix}storage'
  location: location
  tags: commonTags
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {
    minimumTlsVersion: 'TLS1_2'
    supportsHttpsTrafficOnly: true
    allowBlobPublicAccess: false
  }
}
```

### Modules
```bicep
// Module definition (modules/storage.bicep)
@description('Storage account name')
param name string

@description('Location for resources')
param location string

resource storageAccount 'Microsoft.Storage/storageAccounts@2023-01-01' = {
  name: name
  location: location
  // ...
}

output storageAccountId string = storageAccount.id

// Module consumption (main.bicep)
module storage 'modules/storage.bicep' = {
  name: 'storageDeployment'
  params: {
    name: storageAccountName
    location: location
  }
}
```

### Conditional Deployment
```bicep
resource appInsights 'Microsoft.Insights/components@2020-02-02' = if (enableMonitoring) {
  name: '${resourcePrefix}-insights'
  location: location
  kind: 'web'
  properties: {
    Application_Type: 'web'
  }
}
```

### Loops
```bicep
// Array loop
param subnets array

resource virtualNetwork 'Microsoft.Network/virtualNetworks@2023-05-01' = {
  name: '${resourcePrefix}-vnet'
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [vnetAddressSpace]
    }
    subnets: [for (subnet, i) in subnets: {
      name: subnet.name
      properties: {
        addressPrefix: subnet.addressPrefix
      }
    }]
  }
}
```

### Outputs
```bicep
@description('Storage account resource ID')
output storageAccountId string = storageAccount.id

@description('Storage account primary endpoint')
output primaryEndpoint string = storageAccount.properties.primaryEndpoints.blob
```

### Existing Resources
```bicep
resource existingVnet 'Microsoft.Network/virtualNetworks@2023-05-01' existing = {
  name: vnetName
  scope: resourceGroup(vnetResourceGroup)
}
```

## Security Best Practices

- Use Key Vault for secrets
- Mark sensitive parameters with `@secure()`
- Enable encryption by default
- Use managed identities
- Implement network security groups
- Enable diagnostic settings

## Module Design

- Single responsibility per module
- Clear parameter descriptions
- Use decorators for validation
- Provide sensible defaults
- Return useful outputs

## Key Guidelines

1. Use modules for reusability
2. Validate parameters with decorators
3. Use existing resources when referencing
4. Tag all resources consistently
5. Preview changes with what-if before deploying
