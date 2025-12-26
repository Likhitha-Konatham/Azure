# Automated Deployment to Kubernetes

This document explains the **automated Kubernetes deployment workflow**. The approach shows how to deploy applications to a Kubernetes cluster using **only application source code**, without manually writing Dockerfiles or Kubernetes manifests.

---

## High-Level Workflow

Git Repository (Code Only)
↓
Kubernetes Platform – Automated Deployment
↓
Docker Image Build (Auto-generated)
↓
Kubernetes Manifests Creation
↓
Pull Request Created in GitHub
↓
Merge PR → Application Deployed to K8s

---

## 1️. Start with Kubernetes Cluster

- Access your **Kubernetes cluster dashboard**
- Navigate to:
Kubernetes Cluster → Automated Deployment

- This feature enables CI/CD directly from a Git repository.

---

## 2️. Add Git Repository (Only Source Code)

- Click **Add Git Repository**
- Provide:
- GitHub repository URL
- Branch (for example: `main`)
- Repository contains:
- ✅ Application source code
- ❌ No Dockerfile
- ❌ No Kubernetes manifests

The platform handles containerization and deployment automatically.

---

## 3️. Configure Application Runtime

In the next step:
- Platform detects the programming language automatically
- You provide the **application port**, for example:

3000 / 5000 / 8080
This port is used for:
- Docker image creation
- Kubernetes Service configuration

## 4️. Automatic Docker Image Build

Behind the scenes:
- A Dockerfile is auto-generated
- The application image is built
- The image is pushed to a container registry

No manual Dockerfile creation is required.

## 5️. Automatic Kubernetes Manifests Generation

The platform automatically creates:
- Deployment YAML
- Service YAML
- Optional Ingress YAML

These are generated based on:
- Application runtime
- Exposed port
- Kubernetes best practices

## 6️. Pull Request Creation in GitHub

A Pull Request (PR) is created automatically
- The PR includes:
- Generated Dockerfile
- Kubernetes manifests
- Deployment configuration files

This ensures:
- Code review
- Audit trail
- GitOps best practices

## 7️. Review and Merge the Pull Request

- Developer reviews the PR
- Confirms generated files
- Merges the PR into the main branch
- The merge becomes the deployment trigger.

## 8️. Automated Deployment to Kubernetes

After PR merge:
- CI/CD pipeline executes automatically
- Kubernetes cluster pulls updated configuration
- Application is deployed using rolling updates
- Zero downtime is ensured

## 9️. Continuous Deployment Flow

Once configured, the process is fully automated:
- Code Change → PR Created → PR Merged → Auto Deployment
- No manual build or deployment steps are required.

## Key Takeaways

- No Dockerfiles written manually
- No Kubernetes YAML authored by developers
- Kubernetes deployments become developer-friendly
- Git is the single source of truth
- CI/CD setup completed in minutes
