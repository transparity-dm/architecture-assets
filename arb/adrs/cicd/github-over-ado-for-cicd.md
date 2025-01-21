# Architecture Decision Record: GitHub over Azure DevOps for Source Control and CI/CD

## Status

Proposed

## Context

Microsoft is strategically shifting its focus towards GitHub, planning to invest more in GitHub and gradually divest in Azure DevOps. GitHub offers a robust platform for source control and CI/CD, with widespread adoption and a strong community. This shift aligns with Microsoft's long-term vision and ensures continued support and innovation.

## Decision

After considering the pros and cons of both options, we have decided to default to using and recommending GitHub for both Source Control and GitHub Actions for CI/CD, instead of Azure DevOps. This decision is influenced by Microsoft's strategic direction to invest more in GitHub and gradually divest in Azure DevOps. GitHub provides a comprehensive and integrated platform for managing code and automating workflows.

Source code and Infrastructure as Code from all practice areas within the company will default to be managed using GitHub. GitHub Actions will be suggested as the default CI/CD tool for new projects, unless there is a specific requirement or preference for Azure DevOps.

## Consequences

By choosing to use GitHub, we align with Microsoft's long-term investment strategy, ensuring continued support and innovation. However, there is a risk that existing projects heavily reliant on Azure DevOps may face challenges during the transition. If this becomes a significant issue, this decision can be reviewed. In the meantime, Azure DevOps can be used as a fallback for projects where it is already deeply integrated and switching to GitHub would be disruptive.
