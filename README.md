# 🚀 Full Stack Kubernetes Deployment with CI/CD & Argo CD

## 🎯 Objective

This project demonstrates how to build and deploy a full-stack application using:

- **Angular 15** (frontend)
- **Spring Boot** (backend)
- **PostgreSQL** (database)

It implements a **CI/CD pipeline using GitHub Actions**, and **GitOps with Argo CD** to manage Kubernetes deployments from the `workloads/` directory.

---

## 🧭 Project Structure
.
├── .github/
│ └── workflows/
│ └── ci.yml # GitHub Actions workflow
├── angular-15-client/ # Angular frontend app
├── spring-boot-server/ # Spring Boot backend app
├── workloads/
│ ├── database.yaml # PostgreSQL Deployment + Service
│ ├── webback.yaml # Spring Boot Deployment + Service
│ └── webfront.yaml # Angular Deployment + Service
├── azure-pipelines.yml # (Optional) Azure DevOps pipeline
├── docker-compose.yml # For local development/testing
├── test.sh # Helper script (optional usage)
└── README.md
## ⚙️ CI/CD Pipeline (GitHub Actions)

The file `.github/workflows/ci.yml` automates the following:

1. **Trigger**: On every push to the `main` branch.
2. **Steps**:
   - Checkout source code
   - Tag Docker images with commit SHA
   - Build and push Angular and Spring Boot images to Docker Hub
   - Update Kubernetes manifests in `workloads/` with new image tags
   - Commit and push updated manifests (if changed)

---

## ☸️ Kubernetes Deployments

All Kubernetes manifests are stored in the `workloads/` directory:

### ✅ Components:
- **`database.yaml`**:
  - Deploys PostgreSQL using the official image.
  - In-memory storage via `emptyDir` (for dev/test only).
- **`webback.yaml`**:
  - Deploys Spring Boot app.
  - Connects to PostgreSQL via internal service name.
  - Hardened security context.
- **`webfront.yaml`**:
  - Deploys Angular app via NGINX.
  - Exposed externally via NodePort (`30080`).

---

## 🚀 Argo CD Integration

Argo CD is used to implement **GitOps** for managing Kubernetes deployments automatically.

- Watches the `workloads/` directory in the Git repository.
- Syncs Kubernetes manifests to the cluster whenever changes are detected.
- Supports automated sync with rollback and health checks.

### Setup steps:
1. Install Argo CD on your Kubernetes cluster.
2. Create an Argo CD application pointing to your repo and `workloads/` path.
3. Enable automated sync to deploy updates instantly.

Example command to create the Argo CD app:

```bash
argocd app create fullstack-app \
  --repo https://github.com/YOUR_USERNAME/YOUR_REPO.git \
  --path workloads \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default \
  --sync-policy automated

##👤 Author
Oussama Zaoui

