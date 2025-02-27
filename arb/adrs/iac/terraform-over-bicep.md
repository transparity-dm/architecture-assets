# Architecture Decision Record: Terraform over Bicep

## Status

Proposed

## Context

[Terraform](https://www.terraform.io/) is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp that allows you to define and provision infrastructure using a declarative configuration language. It supports multiple cloud providers, enabling you to manage resources across different platforms consistently and efficiently. Terraform is [no longer considered fully open source](https://www.theregister.com/2023/08/11/hashicorp_bsl_licence/) because HashiCorp changed its licensing from the Mozilla Public License (MPL) to the Business Source License (BSL) 1.1 in August 2023. This new license restricts the use of Terraform's code by competitors, although the source code remains accessible for non-competitive use. 

[Bicep](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/overview?tabs=bicep) is a domain-specific language (DSL) that uses declarative syntax to deploy Azure resources, making it easier to define and manage infrastructure as code. It simplifies the authoring experience compared to JSON [ARM templates](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/overview), providing concise syntax, reliable type safety, and support for all Azure resource types and API versions. Bicep is Microsoft-specific because it is designed to work exclusively with Azure, Microsoft's cloud platform. It serves as a more user-friendly alternative to Azure Resource Manager (ARM) templates, providing a streamlined and concise syntax for defining and deploying Azure resources.

Microsoft currently provide quickstart IaC templates in both [Terraform examples](https://learn.microsoft.com/en-us/azure/aks/learn/quick-kubernetes-deploy-terraform?pivots=development-environment-azure-cli) and [Bicep examples](https://learn.microsoft.com/en-us/azure/aks/learn/quick-kubernetes-deploy-bicep?tabs=azure-cli).


Bicep supports two different modes of deployment, incremental (default) and complete. 
- In incremental mode, Resource Manager leaves unchanged resources that exist in the resource group but aren't specified in the template. Resources in the template are added to the resource group.
- In complete mode, Resource Manager deletes resources that exist in the resource group but aren't specified in the template.
- [More Information](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/deployment-modes)


Terraform only supports a single more of deployment which is equivalent to complete mode in Bicep.
- This means that resources that previously existed in a template will get destroyed in Azure if they are subsequently removed or commented out from the template. This can be destructive if this behaviour is not understood by engineers.
- Using the Terraform [Plan command](https://developer.hashicorp.com/terraform/cli/commands/plan) can highlight the changes required without them being applied.

## Decision

After considering the pros and cons of both options, we have decided to use Terraform as our default templating language for IaC projects, due to its ubiquity in the market and the ability to use it across multiple cloud platforms and software products. There is widespread existing market adoption of the language, both in enterprise and the available skills market. Adopting Bicep would limit our ability to engage in opportunities where Terraform was expected.

Azure Landing Zone Templates from the Azure Practice for core platform resources, along with Landing Zone stamps for workload specific solutions will default to be written using Terraform. Terraform will be suggested as the default IaC language asset for new customers, unless they explcitly request or demonstrate a specific lean towards Bicep.

Where Bicep is required, we can consider using a tool such as CoPilot or other generative AI combined with documentation and knowledge to convert templated Terrform scripts to their equivalent Bicep variants.

## Consequences

By choosing to use Terraform, there is a risk that Microsoft reduce the amount of support they provide for Terraform including quickstart code snippets for their various Azure resources. If this becomes an apparent problem, this decision can be reviewed. In the meantime, Bicep can be used as a fallback language for customer projects where they already have an existing usage and do not want to mix their technology usages.

### Training and Certifications
![HashiCorp Terraform Associate](https://images.credly.com/size/680x680/images/85b9cfc4-257a-4742-878c-4f7ab4a2631b/image.png)
- A good certification for Terraform is the [HashiCorp Certified Terraform Associate](https://developer.hashicorp.com/terraform/tutorials/certification-003)
- Learning paths for this exam can be found [here](https://developer.hashicorp.com/terraform/tutorials/certification-003/associate-study-003)
- Udemy courses on this can be found [here](https://www.udemy.com/course/terraform-associate-practice-exam/)
- Terraform specifically on Azure information can be found [here](https://learn.microsoft.com/en-us/azure/developer/terraform/)


## Notes
A good example of how to setup IaC for GitHub Actions with Terraform can be found [here]([https://github.com/Azure-Samples/terraform-github-actions](https://github.com/dmeineck/transparity-architecture))
![Actions Workflow](https://user-images.githubusercontent.com/1248896/189254453-439dd558-fc6c-4377-b01c-d5e54cc49403.png)
