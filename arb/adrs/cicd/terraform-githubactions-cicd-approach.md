# Architecture Decision Record: Terraform GitHub Actions CI/CD Approach

## Status

Proposed

## Context

[Terraform](https://www.terraform.io/) is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp that allows you to define and provision infrastructure using a declarative configuration language. 

GitHub offers a robust platform for source control and [CI/CD through GitHub Actions](https://github.com/features/actions), with widespread adoption and a strong community.

Establishing a controlled CI/CD workflow that runs checks before executing Terraform ensures that any potential issues are caught early, preventing deployment failures and reducing the risk of introducing errors into the infrastructure. This proactive approach enhances the reliability and stability of the infrastructure management process.


## Decision

We will use the [Azure GitHub Actions Workflows for Terraform](https://github.com/Azure-Samples/terraform-github-actions) as the model architecture to follow for all CI/CD pipelines running on GitHub repositories.

A [modified version of this template example can be found here](https://github.com/dmeineck/terraform-cicd-template) that provides both GitHub Actions and Azure DevOps CI/CD pipelines - along with automation using idempotent PowerShell scripts of Azure Storage for the shared Terraform state file.

### From the repo readme:

![Example GitHub Actions workflow](https://user-images.githubusercontent.com/1248896/189254453-439dd558-fc6c-4377-b01c-d5e54cc49403.png)

1. Create a new branch and check in the needed Terraform code modifications.
2. Create a Pull Request (PR) in GitHub once you're ready to merge your changes into your environment.
3. A GitHub Actions workflow will trigger to ensure your code is well formatted, internally consistent, and produces secure infrastructure. In addition, a Terraform plan will run to generate a preview of the changes that will happen in your Azure environment.
4. Once appropriately reviewed, the PR can be merged into your main branch.
5. Another GitHub Actions workflow will trigger from the main branch and execute the changes using Terraform.
6. A regularly scheduled GitHub Action workflow should also run to look for any configuration drift in your environment and create a new issue if changes are detected.

## Consequences

1. **Improved Code Quality and Security**: By running checks before executing Terraform, the workflow ensures that the code is well-formatted, consistent, and secure, reducing the likelihood of introducing vulnerabilities or errors into the infrastructure.

2. **Enhanced Collaboration and Review Process**: The use of Pull Requests (PRs) and automated workflows facilitates better collaboration among team members, allowing for thorough code reviews and discussions before changes are merged into the main branch.

3. **Early Detection of Issues**: The proactive approach of running checks and generating Terraform plans helps in identifying potential issues early in the development process, preventing deployment failures and minimizing downtime.

4. **Automated Drift Detection**: Regularly scheduled workflows to detect configuration drift ensure that the infrastructure remains consistent with the defined state, helping to maintain stability and reliability over time.

5. **Increased Deployment Confidence**: With automated checks and reviews in place, the team can have greater confidence in the deployment process, knowing that potential issues have been addressed before changes are applied to the production environment.

Related ADRs:

1. [Terraform over Bicep](/arb/adrs/iac/terraform-over-bicep.md)
2. [Terraform State Management](/arb/adrs/iac/terraform-state-management.md)
