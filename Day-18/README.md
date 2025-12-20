# Azure DevOps Interview Questions

## Complete CI/CD Pipeline process

### Scenario: How does the Azure DevOps CI/CD Pipeline look in your organization?

### Continuous Integration (CI):

In our organization, CI starts whenever a developer pushes code to the repository.

The CI process includes:
- Pipeline triggers automatically on code changes
- Code is cloned from Git repository
- Unit tests are executed to verify code quality
- Static code analysis is performed
- Application is built (or Docker image is created)
- Build artifacts or Docker images are stored for deployment

The main goal of CI is to **catch issues early and ensure code is always buildable**.

---

### Continuous Delivery (CD):

CD starts after CI is successful or can be triggered manually.

The CD process includes:
- Deploying the build artifacts to environments like **dev, staging, or production**
- Running integration or acceptance tests
- Using approvals or gates before production deployment
- Monitoring deployment health
- Rolling back to a previous version if deployment fails

The main goal of CD is to **deliver changes safely and reliably to users**.

---

## Securing Sensitive Information in Pipelines

### Scenario: You need to securely store API keys and other secrets used in your pipeline tasks. How would you ensure their protection while maintaining pipeline functionality?

We should never hardcode secrets like passwords or API keys in pipeline files.

Instead:
- Store secrets in **Azure Key Vault**
- Use **service connections or managed identities** to access Key Vault
- Grant only minimum required permissions
- Reference secrets securely in pipeline variables

This keeps sensitive data safe and prevents accidental exposure.

---

## Integrating Azure Container Registry (ACR) with Pipelines

### Scenario: Your application uses Docker containers. How would you integrate ACR with Azure Pipelines for building, pushing, and deploying container images?

To integrate ACR with Azure Pipelines:
- Create an **ACR service connection** in Azure DevOps
- Use **Docker tasks** in the pipeline
- Build Docker images from Dockerfile
- Authenticate to ACR using service connection
- Push images to ACR
- Use those images for deployment to Kubernetes or App Services

This enables automated container builds and deployments.

---

## Debugging Pipeline Failures

### Scenario: Your pipeline consistently fails at a specific stage. How would you approach troubleshooting and identifying the root cause of the issue?

To debug pipeline failures:
- Check pipeline **logs** to identify error messages
- Enable **debug mode** if needed
- Verify task configurations
- Check environment issues like permissions or missing tools
- Review recent code changes
- Check Azure Monitor or resource health if required

Most failures happen due to configuration mistakes or environment issues.

---

## Handling Code Merges and Rollbacks in Pipelines

### Scenario: You discover a critical bug in the recently deployed production environment. How would you leverage Azure Pipelines for a rollback and ensure safe merging of a fix?

For rollbacks and fixes:
- Use **deployment environments** in pipelines
- Roll back by redeploying the previous stable version
- Create a fix branch and apply the patch
- Merge changes safely using pull requests
- Redeploy once tests pass

This ensures production stability and safe code changes.

---

## Utilizing Azure Runners for Self-Hosted Environments

### Scenario: Your company has specific infrastructure requirements and needs to run pipelines on self-hosted machines. How would you leverage Azure Runners for this purpose?

We can use **self-hosted agents** when:
- Custom tools are required
- Private network access is needed
- Microsoft-hosted agents are not suitable

Steps:
- Install Azure DevOps agent on a VM
- Register it to an agent pool
- Secure it with network rules and access control
- Choose the correct OS and tools

This gives more control over pipeline execution.

---

## Implementing Role-Based Access Control (RBAC) in Pipelines

### Scenario: Your team has various roles with different access needs. How would you configure RBAC within Azure Pipelines to ensure users have appropriate permissions?

RBAC ensures users get only required access.

We can:
- Assign built-in roles like Reader, Contributor, Admin
- Restrict access to pipelines, repos, and environments
- Use approval checks for deployments
- Follow **least privilege principle**

This improves security and prevents accidental changes.

---

## Automating Infrastructure Provisioning with Pipelines

### Scenario: You want to automate infrastructure provisioning and deployment alongside your application code. How would you integrate infrastructure as code (IaC) tools like Terraform with Azure Pipelines?

We integrate IaC by:
- Using Terraform tasks in Azure Pipelines
- Storing Terraform files in the repo
- Running `terraform init`, `plan`, and `apply`
- Using service connections for authentication

This provides:
- Faster infrastructure creation
- Consistent environments
- Version-controlled infrastructure

---

## Maintaining Pipeline Security Throughout the CI/CD Process

### Scenario: How would you ensure overall security within your Azure Pipelines throughout the CI/CD process, from code building to deployment?

Pipeline security can be maintained by:
- Secure coding practices
- Using secret management tools
- Scanning code and container images
- Using least-privilege service principals
- Enabling approvals for production deployments
- Regular pipeline and access audits

This ensures secure and reliable CI/CD workflows.

---

## Summary

This document covers:
- CI/CD pipeline flow
- Security best practices
- Container integration
- Debugging strategies
- Infrastructure automation
- Role-based access control

It is useful for **Azure DevOps interviews and real-time project understanding**.
