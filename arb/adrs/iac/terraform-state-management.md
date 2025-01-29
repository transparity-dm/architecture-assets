# Architecture Decision Record: Terraform State Management in Azure Storage

## Status

Proposed

## Context

> NOTE: Some of this content is lifted directly from this [Microsoft Learn Article](https://learn.microsoft.com/en-us/azure/developer/terraform/store-state-in-azure-storage?tabs=terraform).

[Terraform](https://www.terraform.io/) is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp that allows you to define and provision infrastructure using a declarative configuration language. 

Terraform state is used to reconcile deployed resources with Terraform configurations. State allows Terraform to know what Azure resources to add, update, or delete.

By default, Terraform state is stored locally, which isn't ideal for the following reasons:

- Local state doesn't work well in a team or collaborative environment.
- Terraform state can include sensitive information.
- Storing state locally increases the chance of inadvertent deletion.

Storing Terraform state in a central location ensures consistency and prevents conflicts by allowing all team members and CI/CD pipelines to access the same state file. This practice also enhances security and reliability, as the state file can be backed up and managed with appropriate access controls.

## Decision

Azure Storage will be used as the recommended location for Terraform state file storage. Using Azure Storage for Terraform state management centralizes the state file, ensuring all team members and CI/CD pipelines have consistent access, which prevents conflicts. Additionally, Azure Storage offers robust security features and backup options, enhancing the reliability and protection of the state file.

### Dependency Loop Challenge
There is a catch-22 style situation with Terraform because a team needs a storage account to store the Terraform state file, but also needs Terraform to create that storage account. This creates a dependency loop where Terraform needs to manage its own state, but the state storage infrastructure isn't available until Terraform provisions it.

To resolve this, the team can manually create the initial storage account and container using the Azure portal or CLI. Once the storage is set up, they can configure Terraform to use it for state management, allowing Terraform to manage the rest of the infrastructure. This initial manual step breaks the loop and enables automated infrastructure management going forward.

It is therefore recommended that Powershell scripts for standing up and configuring the initial Terraform State file are stored in source control alongside the Terraform, to allow an infrastructure to be stood up from a ground-zero scenario.

### Example Scripts

Here is an example PowerShell script that provisions the Azure resources required for Terraform state management. This file along with the parameters used to invoke it should be stored in a repo so that the steps for fully standing up the environment are documented at source.

```powershell
param (
    [string]$resourceGroupName = "devops",
    [string]$location = "UK South",
    [string]$storageAccountName = "tfstate$(Get-Random)",
    [string]$containerName = "tfstate"
)

# Login to Azure
Connect-AzAccount

# Create Resource Group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create Storage Account
New-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName -Location $location -SkuName Standard_LRS -Kind StorageV2

# Get Storage Account Key
$storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]

# Create Storage Context
$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

# Create Storage Container
New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off
```

You can now run this script with custom parameters like this:

```powershell
.\Setup-TerraformStorage.ps1 -resourceGroupName "devops" -location "UK South" -storageAccountName "mystorageacct" -containerName "tfstate"
```

Storing these scripts in source control ensures that the setup process is well-documented, versioned, and accessible to all team members, promoting consistency and collaboration.

Once created, the Terraform configuration should be configured with a backend configuration block that specifies an azurerm backend - see the [Microsoft Learn example scripts](https://learn.microsoft.com/en-us/azure/developer/terraform/store-state-in-azure-storage?tabs=powershell#3-configure-terraform-backend-state) for a concise way of configuring this.

## Consequences

### Positive Consequences
1. **Consistency and Collaboration**: Centralizing the Terraform state in Azure Storage ensures that all team members and CI/CD pipelines access the same state file, preventing conflicts and promoting a collaborative environment.
2. **Enhanced Security**: Azure Storage provides robust security features, including encryption at rest and in transit, access controls, and integration with Azure Active Directory, ensuring the state file is protected.
3. **Reliability and Backup**: Azure Storage offers high availability and durability, along with backup options, ensuring the state file is reliably stored and can be recovered in case of failures.
4. **Scalability**: Azure Storage can handle large amounts of data and high transaction volumes, making it suitable for growing infrastructure needs.

### Negative Consequences
1. **Initial Manual Setup**: The initial manual creation of the storage account and container introduces a dependency on manual steps, which can be error-prone and time-consuming.
2. **Complexity in Management**: Managing access controls and permissions for the storage account requires careful planning and ongoing maintenance to ensure security and compliance.
3. **Cost**: Using Azure Storage incurs costs based on storage capacity, transactions, and data transfer, which need to be accounted for in the project budget.

## Notes

- **Documentation**: Ensure that the process for setting up the initial storage account and container is well-documented and included in the project's onboarding materials and stored in the appropriate source code repository. This helps new team members understand the setup process and reduces the risk of errors.
- **Automation**: Consider automating the initial setup process using scripts or templates that can be executed with minimal manual intervention. This can help streamline the setup process and reduce the potential for human error.
- **Access Management**: Regularly review and update access controls for the storage account to ensure that only authorized personnel have access to the Terraform state file. This helps maintain security and compliance.
