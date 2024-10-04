# COPILOT with ARM Templates for Azure Provisioning
This repo is created for GitHub Copilot Adoption Program, specifically for ARM Templates Hands On!

# First Activity: Create your Workspace and Project with VS Code and ARM Templates

- VS Code and ARM Templates

## Objectives

- Create ARM Templates project using GitHub Copilot from a Scratch.

<img src="imagesarm1.jpg" alt="ARM Templates Workspace" height="300">

## Requirements

- VS Code
- GitHub Copilot license
- GitHub Copilot Extension
- GitHub Copilot CLI Extension
- Azure CLI
- Azure Subscription

## Step 1: Create an ARM Templates Project

> @workspace /new I need to create an Azure Resource Manager templates workspace to create an azure storage account inside an azure resource group, later I'll add more azure resource manager templates to continue provisioning resources.

- Click on Create Workspace.

### Troubleshooting

- The ARM Templates ".json" files could be different because we are working with gen-ai, if you have problems use bellow ones.

- storageAccount.json
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "The name of the storage account."
      }
    },
    "location": {
      "type": "string",
      "metadata": {
        "description": "The location where the storage account will be created."
      }
    },
    "StorageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS",
        "Premium_ZRS"
      ],
      "metadata": {
        "description": "The type of the storage account."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "supportsHttpsTrafficOnly": true
      }
    }
  ]
}
```

- storageAccount.parameters.json
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "value": "myarmstorageaccount"
    },
    "storageAccountType": {
      "value": "Standard_GRS"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

- vnet.json
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vnetName": {
      "type": "string",
      "metadata": {
        "description": "The name of the virtual network."
      }
    },
    "vnetAddressPrefix": {
      "type": "string",
      "defaultValue": "10.0.0.0/16",
      "metadata": {
        "description": "The address prefix for the virtual network."
      }
    },
    "subnet1Name": {
      "type": "string",
      "metadata": {
        "description": "The name of the first subnet."
      }
    },
    "subnet1AddressPrefix": {
      "type": "string",
      "defaultValue": "10.0.0.0/24",
      "metadata": {
        "description": "The address prefix for the first subnet."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "allowedValues": [
        "eastus",
        "eastus2",
        "centralus",
        "northcentralus",
        "southcentralus",
        "westus",
        "westus2",
        "westus3"
      ],
      "metadata": {
        "description": "Location for the virtual network."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vnetName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vnetAddressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('subnet1Name')]",
            "properties": {
              "addressPrefix": "[parameters('subnet1AddressPrefix')]"
            }
          }
        ]
      }
    }
  ]
}
```

- vnet.parameters.json
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vnetName": {
      "value": "myVnet"
    },
    "vnetAddressPrefix": {
      "value": "10.0.0.0/16"
    },
    "subnet1Name": {
      "value": "mySubnet1"
    },
    "subnet1AddressPrefix": {
      "value": "10.0.0.0/24"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

## Step 2: Ask Copilot Chat to suggest how a change to storage account could be achieved

> how can I update my already created storage account type to "Standard_GRS" using arm templates?.

- Check the generated ".json", apply the suggested changes and update the storage account using bellow terminal az cli command.
```terminal
az deployment group create --resource-group <resource-group-name> --template-file ./storageAccount.json --parameters @./storageAccount.parameters.json
```
## Step 3: Ask Copilot Chat to suggest a ".json" arm template to create an Azure Vnet and subnet.

> Now I need to create an Azure VNet with his default subnet using an ARM Template for this purpose, can you please suggest me a ".json" to accomplish this?.

- CHeck Copilot Chat suggestion and try to follow the steps.
- Suggested ".json" files should be like the ones bellow:
- vnet.json
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vnetName": {
      "type": "string",
      "metadata": {
        "description": "The name of the virtual network."
      }
    },
    "vnetAddressPrefix": {
      "type": "string",
      "defaultValue": "10.0.0.0/16",
      "metadata": {
        "description": "The address prefix for the virtual network."
      }
    },
    "subnetName": {
      "type": "string",
      "metadata": {
        "description": "The name of the subnet."
      }
    },
    "subnetAddressPrefix": {
      "type": "string",
      "defaultValue": "10.0.0.0/24",
      "metadata": {
        "description": "The address prefix for the subnet."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "allowedValues": [
        "eastus",
        "eastus2",
        "centralus",
        "northcentralus",
        "southcentralus",
        "westus",
        "westus2",
        "westus3"
      ],
      "metadata": {
        "description": "Location for the virtual network."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vnetName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vnetAddressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('subnetName')]",
            "properties": {
              "addressPrefix": "[parameters('subnetAddressPrefix')]"
            }
          }
        ]
      }
    }
  ]
}
```
- vnet.parameters.json
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vnetName": {
      "value": "myVnet"
    },
    "vnetAddressPrefix": {
      "value": "10.0.0.0/16"
    },
    "subnetName": {
      "value": "mySubnet"
    },
    "subnetAddressPrefix": {
      "value": "10.0.0.0/24"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```
- After we create the VNet ".json" files, we can apply the changes running the following terminal az cli command.
```terminal
az deployment group create --resource-group myResourceGroup --template-file ./vnet.json --parameters @./vnet.parameters.json
```

## Step 4: As Copilot Chat a suggestion to modify the already created VNet Subnet

> How can I update my already created Vnet with ARM Templates, to add another Subnet on the same allowed VNet ip adresses range?.

- Check the generated suggestion and update ".json" Vnet files.
- vnet.json
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vnetName": {
      "type": "string",
      "metadata": {
        "description": "The name of the virtual network."
      }
    },
    "vnetAddressPrefix": {
      "type": "string",
      "defaultValue": "10.0.0.0/16",
      "metadata": {
        "description": "The address prefix for the virtual network."
      }
    },
    "subnet1Name": {
      "type": "string",
      "metadata": {
        "description": "The name of the first subnet."
      }
    },
    "subnet1AddressPrefix": {
      "type": "string",
      "defaultValue": "10.0.0.0/24",
      "metadata": {
        "description": "The address prefix for the first subnet."
      }
    },
    "subnet2Name": {
      "type": "string",
      "metadata": {
        "description": "The name of the second subnet."
      }
    },
    "subnet2AddressPrefix": {
      "type": "string",
      "defaultValue": "10.0.1.0/24",
      "metadata": {
        "description": "The address prefix for the second subnet."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "allowedValues": [
        "eastus",
        "eastus2",
        "centralus",
        "northcentralus",
        "southcentralus",
        "westus",
        "westus2",
        "westus3"
      ],
      "metadata": {
        "description": "Location for the virtual network."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vnetName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vnetAddressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('subnet1Name')]",
            "properties": {
              "addressPrefix": "[parameters('subnet1AddressPrefix')]"
            }
          },
          {
            "name": "[parameters('subnet2Name')]",
            "properties": {
              "addressPrefix": "[parameters('subnet2AddressPrefix')]"
            }
          }
        ]
      }
    }
  ]
}
```
- vnet.parameters.json
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vnetName": {
      "value": "myVnet"
    },
    "vnetAddressPrefix": {
      "value": "10.0.0.0/16"
    },
    "subnet1Name": {
      "value": "mySubnet1"
    },
    "subnet1AddressPrefix": {
      "value": "10.0.0.0/24"
    },
    "subnet2Name": {
      "value": "mySubnet2"
    },
    "subnet2AddressPrefix": {
      "value": "10.0.1.0/24"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```
- After we apply the changes on VNet ".json" files, we can run the following terminal az cli command to privision the new Subnet.
```terminal
az deployment group create --resource-group myResourceGroup --template-file ./vnet.json --parameters @./vnet.parameters.json
```

## Step 5: Ask Copilot Chat a suggestion to delete the latest VNet Subnet created using ARM Templates.

> How can I delete with ARM templates one of the subnets already created on my Azure vnet?

- Check the generated suggestion and update ".json" Vnet files.
- vnet.json
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vnetName": {
      "type": "string",
      "metadata": {
        "description": "The name of the virtual network."
      }
    },
    "vnetAddressPrefix": {
      "type": "string",
      "defaultValue": "10.0.0.0/16",
      "metadata": {
        "description": "The address prefix for the virtual network."
      }
    },
    "subnet1Name": {
      "type": "string",
      "metadata": {
        "description": "The name of the first subnet."
      }
    },
    "subnet1AddressPrefix": {
      "type": "string",
      "defaultValue": "10.0.0.0/24",
      "metadata": {
        "description": "The address prefix for the first subnet."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "allowedValues": [
        "eastus",
        "eastus2",
        "centralus",
        "northcentralus",
        "southcentralus",
        "westus",
        "westus2",
        "westus3"
      ],
      "metadata": {
        "description": "Location for the virtual network."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vnetName')]",
      "location": "[parameters('location')]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vnetAddressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('subnet1Name')]",
            "properties": {
              "addressPrefix": "[parameters('subnet1AddressPrefix')]"
            }
          }
        ]
      }
    }
  ]
}
```
- vnet.parameters.json
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vnetName": {
      "value": "myVnet"
    },
    "vnetAddressPrefix": {
      "value": "10.0.0.0/16"
    },
    "subnet1Name": {
      "value": "mySubnet1"
    },
    "subnet1AddressPrefix": {
      "value": "10.0.0.0/24"
    },
    "location": {
      "value": "eastus"
    }
  }
}
```

- After we apply the changes on VNet ".json" files, we can run the following terminal az cli command to delete the Subnet.
```terminal
az deployment group create --resource-group myResourceGroup --template-file ./vnet.json --parameters @./vnet.parameters.json
```

## Step 6: Finally ask Copilot Chat how to delete an entire resource group to avoid extra charges for the resources created with ARM Templates.

> How can I delete an Azure Resource group using the Azure CLI with my terminal?.

- Check GitHub Copilot suggestion and Delete the resource group. You can use the bellow az cli terminal command.

```terminal
az group delete --name myResourceGroup --yes --no-wait
```
