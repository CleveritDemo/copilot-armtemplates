# Azure ARM Templates

This project contains Azure Resource Manager templates for provisioning Azure resources.

## Templates

- `templates/storageAccount.json`: This file is an Azure Resource Manager template for creating an Azure storage account. It defines the properties and configurations of the storage account.

- `templates/resourceGroup.json`: This file is an Azure Resource Manager template for creating an Azure resource group. It defines the properties and configurations of the resource group.

## Parameters

- `parameters/storageAccount.parameters.json`: This file contains the parameters for the storage account template. It specifies the values that can be customized when deploying the template.

- `parameters/resourceGroup.parameters.json`: This file contains the parameters for the resource group template. It specifies the values that can be customized when deploying the template.

## Scripts

- `scripts/deploy.sh`: This script is used to deploy the Azure Resource Manager templates. It uses the Azure CLI to authenticate and deploy the templates.

For detailed instructions on how to deploy the templates, please refer to the documentation provided in each template file.

```
