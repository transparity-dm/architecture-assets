# Architecture Decision Record: Terraform over Bicep

## Status

Proposed

## Context

[Terraform](https://www.terraform.io/) is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp that allows you to define and provision infrastructure using a declarative configuration language. It supports multiple cloud providers, enabling you to manage resources across different platforms consistently and efficiently. Terraform is [no longer considered fully open source](https://www.theregister.com/2023/08/11/hashicorp_bsl_licence/) because HashiCorp changed its licensing from the Mozilla Public License (MPL) to the Business Source License (BSL) 1.1 in August 2023. This new license restricts the use of Terraform's code by competitors, although the source code remains accessible for non-competitive use. 

[Bicep](https://learn.microsoft.com/en-us/azure/azure-resource-manager/bicep/overview?tabs=bicep) is a domain-specific language (DSL) that uses declarative syntax to deploy Azure resources, making it easier to define and manage infrastructure as code. It simplifies the authoring experience compared to JSON ARM templates, providing concise syntax, reliable type safety, and support for all Azure resource types and API versions. Bicep is Microsoft-specific because it is designed to work exclusively with Azure, Microsoft's cloud platform. It serves as a more user-friendly alternative to Azure Resource Manager (ARM) templates, providing a streamlined and concise syntax for defining and deploying Azure resources.

Microsoft currently provide quickstart IaC templates in both [Terraform examples](https://learn.microsoft.com/en-us/azure/aks/learn/quick-kubernetes-deploy-terraform?pivots=development-environment-azure-cli) and [Bicep examples](https://learn.microsoft.com/en-us/azure/aks/learn/quick-kubernetes-deploy-bicep?tabs=azure-cli).

## Decision

After considering the pros and cons of both options, we have decided to use Terraform as our default templating language for IaC projects, due to its ubiquity in the market and the ability to use it across multiple cloud platforms and software products. There is widespread existing market adoption of the language, both in enterprise and the available skills market. Adopting Bicep would limit our ability to engage in opportunities where Terraform was expected.

Azure Landing Zone Templates from the Azure Practice for core platform resources, along with Landing Zone stamps for workload specific solutions will default to be written using Terraform. Terraform will be suggested as the default IaC language asset for new customers, unless they explcitly request or demonstrate a specific lean towards Bicep.

Where Bicep is required, we can consider using a tool such as CoPilot or other generative AI combined with documentation and knowledge to convert templated Terrform scripts to their equivalent Bicep variants.

## Consequences

By choosing to use Terraform, there is a risk that Microsoft reduce the amount of support they provide for Terraform including quickstart code snippets for their various Azure resources. If this becomes an apparent problem, this decision can be reviewed. In the meantime, Bicep can be used as a fallback language for customer projects where they already have an existing usage and do not want to mix their technology usages.
