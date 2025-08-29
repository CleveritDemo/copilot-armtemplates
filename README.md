# Azure ARM Templates. Infrastructure as Code with GitHub Copilot

In this repository we will cover the creation of a small bastion infrastructure that will allow us to access a virtual machine without exposing its public IP address.

We will use various GitHub Copilot features such as:
- GitHub Copilot Extensions.
- GitHub Copilot Edits.
- GitHub Copilot Instructions.

The end result will be the ability to access our virtual machine without needing to know its IP and, at the same time, obtain a basic Azure DevOps pipeline to automatically deploy all the resources.

Finally, we will finish by deleting all the deployed resources.

# 🔧 Let's Get Started

## Objectives

- Create a bastion infrastructure using ARM Templates and GitHub Copilot

![ARM Templates Workspace](imagearm1.png)

## Requirements

- Visual Studio Code 
- GitHub Copilot License
- GitHub Copilot Extension
- Azure CLI
- Active Azure Cloud Subscription

## 0. Creating the Copilot Instructions File

Create a new folder named `.github` at the repository root and create a file called `copilot-instructions.md` inside that folder. Add the following content:

```
You are a code assistant proficient in Azure Cloud.
You have advanced knowledge in Azure Resource Manager (ARM) Templates and Terraform.
You also have extensive experience with the Azure CLI terminal and various scripting languages such as PowerShell, Bash, and Python.

Your main goal is to provide high-quality code assistance to the DevOps engineer or Developer using the GitHub Copilot chat.

If you have any doubts about the user's request, you should ask the user to clarify the specified information. This point must always be fulfilled.
```

This configures the assistant’s behavior and instructs the AI model to provide more efficient code assistance for our tasks.

Once adjusted, save the file.

## 1. Creating the ARM Templates Project and Storage Account

We use the **@workspace** extension to create a new project structure.

Prompt to use:

`@workspace /new Create a new workspace where you must generate a storage account resource in Azure using ARM Templates. The resource should be named "copilotstgdemo" and be located in the "us-east" region of Azure. Organize the workspace with two folders "templates" and "parameters" to store resource templates and their parameter files respectively. Note that this template will be modified later.`

- Click on "Create Workspace".
- Click on "Set as Root Folder" (or "Main Folder") so that the structure is generated in the repository root.

At this point, Copilot may provide two files: `storageAccount.json` and `storageAccount.parameters.json` containing the resource and parameters.

## 2. Creating the Resource Group and Deploying Initial Resources

Prompt to use:

`@workspace Using Azure ARM Templates, create a resource of type "resource group" in Azure. The resource should be named "RG_COPILOT_ARM_DEMO" and be located in the "us-east" region of Azure.`

Copilot will return template files named `resourceGroup.json` and `resourceGroup.parameters.json`.

Both files must be created inside the `templates` and `parameters` folders or inserted as new files via GitHub Copilot Chat.

Then ask:

`@workspace How can I deploy these resources to Azure #file:resourceGroup.json #file:resourceGroup.parameters.json #file:storageAccount.json #file:storageAccount.parameters.json`

Copilot will suggest the appropriate Azure CLI commands.

```sh
az deployment sub create \
  --location eastus \
  --template-file my-azure-arm-template/templates/resourceGroup.json \
  --parameters my-azure-arm-template/parameters/resourceGroup.parameters.json
```

```sh
az deployment group create \
  --resource-group RG_COPILOT_ARM_DEMO \
  --template-file my-azure-arm-template/templates/storageAccount.json \
  --parameters @my-azure-arm-template/parameters/storageAccount.parameters.json
```

> IMPORTANT: Deploy the Resource Group first. Deploying the storage account without an existing resource group will result in an error.

## 3. Creating the Networking Resources: Virtual Network, Subnets, and Bastion Host
In this step, we will create the virtual network and the necessary subnets to host servers and the bastion host that will allow us to access those servers. To do this, execute the following prompt:

_Prompt to execute: (On Agent Mode)_

`Using Azure ARM Templates, create a series of templates to create an "Azure Virtual Network" in the eastus region called "VNET_COPILOT". The network space for this VNET should be 10.100.0.0/16 and it should contain the following subnets: A subnet for virtual machines called "snet_servidores" (this subnet should have a /24 space). The goal is to be able to access the virtual machines in the future located in the servers subnet from the bastion service. A subnet for azure bastion, you should configure a bastion service that allows at least one simultaneous connection to the servers located in the snet_servidores network. Create the necessary ARM templates to meet the requirement.`

At this point, Copilot will most likely suggest a structure with two files `networking.json` and `networking.parameters.json` which will contain resources and parameters to use respectively.

If we are not suggested how to execute these files, we can use the following prompt to obtain the execution command in the GitHub CLI:

_Prompt to execute: (On Agent Mode)_

`How can I deploy these resources to azure #file:networking.json #file:networking.parameters.json`

This will suggest a command similar to the following:

```sh
az deployment group create \
  --resource-group RG_COPILOT_ARM_DEMO \
  --template-file my-azure-arm-template/templates/networking.json \
  --parameters @my-azure-arm-template/parameters/networking.parameters.json
```

At this point, we can wait a few minutes and then open our Azure Cloud subscription to verify that the resources are deployed.\

### Troubleshooting: scaleUnit Parameter Error

If you encounter an error stating that a "scaleUnit" entry is missing in the Bastion resource, use the prompt:

`@workspace /fix #terminalSelection I need your help to resolve the following error since it indicates that an entry for "scaleUnit" is missing in the Bastion object #file:networking.json . Provide a direct solution that allows deployment of all resources.`

This should instruct you to remove or adjust the "scaleUnit" parameter.

## 4. Creating the Virtual Machine

In this step, we will create a virtual machine using the Standard_B2s size from Microsoft's general-purpose machines and specify the basic configurations to deploy it.

_Prompt to use:_

`@workspace Using Azure ARM templates, generate a template to create a virtual machine in the eastus region of Azure. This virtual machine will be of size Standard_B2s, which has 2 vCores and 4GB of RAM. The operating system image to use is Windows Server, and you should use the Datacenter 2022 version of Windows Server. Additionally, the virtual machine's hard disk should be of the standard SSD type, and the machine should be associated with the subnet: snet_servidores defined in the template of #file:networking.json. If I have omitted any other required parameters to deploy this virtual machine, please let me know so I can provide the information.`

At this point, the templates `vm.json` and `vm.parameters.json` will be generated, containing the resources and parameters respectively.

> **Suggestion: GitHub Copilot Edits 💡**  
> GitHub Copilot might ask at this point if you want to replace the default username and password values. You can either manually replace the default values in the `vm.parameters.json` file or use **GitHub Copilot Edits** to instruct Copilot to make the replacement for you.

Command to execute the deployment:
```sh
az deployment group create --resource-group RG_COPILOT_ARM_DEMO --template-file "my-azure-arm-template/templates/vm.json" --parameters "my-azure-arm-template/parameters/vm.parameters.json"
```

## 5. Building an Azure DevOps CI/CD Pipeline

In this step, we will use **GitHub Copilot Edits** to build an Azure DevOps pipeline that will handle the deployment of all previously generated templates.

- First, create a new file named `azure-pipelines.yml` at the root of the repository.
- Then, select the **Copilot Edits** option in the GitHub Copilot chat.
- Once in the chat in "Edits" mode, use the following prompt to start the modification.
- _Prompt to use:_
  ```
  I need you to build an Azure DevOps pipeline in YAML format that sequentially deploys all the Azure ARM templates within the workspace of this project. The deployment order should be as follows:

  Deploy the resource group #file:resourceGroup.json
  Deploy the storage account #file:storageAccount.json #file:storageAccount.parameters.json
  Deploy the networking resources #file:networking.json
  Deploy the virtual machine #file:vm.json #file:vm.parameters.json
  The pipeline should retrieve credentials from variables defined at the pipeline level within Azure DevOps and should run on a Linux instance.
  ```
The result of this execution will include all the required template files in the "working set" of Copilot Edits and will develop the pipeline code for us simultaneously within the `azure-pipelines.yml` file.

Once the file creation is complete, it will be available for subsequent execution in an Azure Pipelines account.

## 6. Deleting All Created Resources via Azure CLI

In this final step, we ask GitHub Copilot how we can delete all the resources created during the exercise.

`How can I delete all the resources created in my resource group RG_COPILOT_ARM_DEMO?`

- Review the suggestion from GitHub Copilot and delete the resource group with the following Azure CLI command:
```sh
az group delete --name RG_COPILOT_ARM_DEMO --yes --no-wait
```
