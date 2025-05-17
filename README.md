# 🚀 Full Stack Kubernetes Deployment with CI/CD & Argo CD

## 🎯 Objective

This project demonstrates how to build and deploy a full-stack application using:

-   **Angular 15** (frontend)
-   **Spring Boot** (backend)
-   **PostgreSQL** (database)

It implements a **CI/CD pipeline using GitHub Actions**, and **GitOps with Argo CD** to manage Kubernetes deployments from the `workloads/` directory.

---

## ✨ Core Technologies

-   **Frontend**: Angular 15
-   **Backend**: Spring Boot
-   **Database**: PostgreSQL
-   **Containerization**: Docker
-   **Orchestration**: Kubernetes
-   **CI/CD**: GitHub Actions
-   **GitOps**: Argo CD
-   **Local Development**: Docker Compose

---

## 🧭 Project Structure

![image](https://github.com/user-attachments/assets/45fef265-fff9-4be5-a29c-ba53972afa80)


---

## ⚙️ CI/CD Pipeline (GitHub Actions)

The file `.github/workflows/ci.yml` automates the following:

1.  **Trigger**: On every push to the `main` branch.
2.  **Steps**:
    -   Checkout source code
    -   Tag Docker images with the commit SHA
    -   Build and push Angular and Spring Boot images to Docker Hub (or your configured container registry)
    -   Update Kubernetes manifests in `workloads/` with new image tags
    -   Commit and push updated manifests back to the `main` branch (if changed)

⚠️ **Note**: The GitHub Action requires secrets `DOCKER_USERNAME` and `DOCKER_PASSWORD` to be configured in your repository settings for pushing images to Docker Hub. It also needs permissions to push changes back to the repository (usually default for `GITHUB_TOKEN`).

---

## ☸️ Kubernetes Deployments

All Kubernetes manifests are stored in the `workloads/` directory:

### ✅ Components:

-   **`database.yaml`**:
    -   Deploys PostgreSQL using the official `postgres` image.
    -   Uses in-memory storage via `emptyDir` for simplicity ( **⚠️ Not suitable for production! Use PersistentVolumeClaims for production.**).
    -   Exposes PostgreSQL on a ClusterIP service named `postgres-svc`.
-   **`webback.yaml`**:
    -   Deploys the Spring Boot backend application.
    -   Connects to PostgreSQL via the internal service name `postgres-svc`.
    -   Environment variables are used to configure database connection details.
    -   Includes a basic security context for the Pod.
    -   Exposes the backend on a ClusterIP service named `webback-svc`.
-   **`webfront.yaml`**:
    -   Deploys the Angular frontend application, served by NGINX.
    -   The `Dockerfile` for Angular should build the static files and copy them to an NGINX image.
    -   Exposed externally via a NodePort service on port `30080` (named `webfront-svc`).

---

## 🚀 Argo CD Integration

Argo CD is used to implement **GitOps** for managing Kubernetes deployments automatically.

-   It continuously watches the `workloads/` directory in your Git repository.
-   It automatically syncs the Kubernetes manifests to your cluster whenever changes are detected in the specified path.
-   This enables declarative configuration and automated deployment, with features like rollback and health checks.

### Setup Steps for Argo CD:

1.  **Install Argo CD** on your Kubernetes cluster.
    Follow the official documentation: [Argo CD Getting Started](https://argo-cd.readthedocs.io/en/stable/getting_started/)

2.  **Access the Argo CD UI/CLI**.

3.  **Create an Argo CD application** pointing to your repository and the `workloads/` path.
    Replace `YOUR_USERNAME` and `YOUR_REPO` with your actual GitHub username and repository name.

    ```bash
    argocd app create fullstack-app \
      --repo https://github.com/YOUR_USERNAME/YOUR_REPO.git \
      --path workloads \
      --dest-server https://kubernetes.default.svc \
      --dest-namespace default \
      --sync-policy automated \
      --auto-prune \
      --self-heal
    ```
    -   `--sync-policy automated`: Enables automated synchronization.
    -   `--auto-prune`: Automatically deletes resources that are no longer defined in Git.
    -   `--self-heal`: Automatically re-syncs if the live state drifts from the Git state.

4.  Once the application is created, Argo CD will detect the manifests in `workloads/` and deploy them to your cluster. Any subsequent changes pushed to `workloads/` by the CI pipeline will be automatically synced.

---

## 📋 Prerequisites

-   Git
-   Docker & Docker Hub Account (or other container registry)
-   A Kubernetes Cluster (e.g., Minikube, Kind, k3s, or a cloud provider's K8s service)
-   `kubectl` configured to connect to your cluster
-   Argo CD CLI (optional, but useful for managing Argo CD from the command line)

---

## 🚀 Getting Started

1.  **Clone the Repository:**
    ```bash
    git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
    cd YOUR_REPO
    ```

2.  **Configure GitHub Actions Secrets:**
    -   Go to your repository on GitHub > Settings > Secrets and variables > Actions.
    -   Add the following repository secrets:
        -   `DOCKER_USERNAME`: Your Docker Hub username.
        -   `DOCKER_PASSWORD`: Your Docker Hub password or access token.

3.  **Update Image Names (If Necessary):**
    -   In `.github/workflows/ci.yml`, ensure the Docker image names (e.g., `yourdockerhubusername/angular-app`) are correctly set to use your Docker Hub username.
    -   The Kubernetes manifest files (`workloads/*.yaml`) use image names like `yourdockerhubusername/spring-boot-app:latest` or similar. The CI pipeline will update the tags, but the base image name (repository path) must match what your CI pipeline pushes. Make sure these are consistent or update your `ci.yml` to use a consistent naming pattern that it then applies to the manifests.

4.  **Set up Argo CD:**
    -   Follow the Argo CD setup steps mentioned [above](#setup-steps-for-argo-cd).

5.  **Initial Push & CI/CD Trigger:**
    -   Make a small commit and push to the `main` branch to trigger the GitHub Actions CI/CD pipeline.
        ```bash
        git commit --allow-empty -m "Initial trigger for CI/CD pipeline"
        git push origin main
        ```
    -   The pipeline will:
        -   Build and push Docker images.
        -   Update image tags in the `workloads/` directory and push these changes back to `main`.
    -   Argo CD will detect the changes in `workloads/` and sync the applications to your Kubernetes cluster.

6.  **Access Your Application:**
    -   Frontend (Angular): Find the NodePort of the `webfront-svc` service and access it via `http://<NodeIP>:<NodePort>`. If using Minikube, you can get the URL with `minikube service webfront-svc --url`. The default NodePort is `30080`.
    -   Backend (Spring Boot): Typically accessed by the frontend internally within the cluster.

---

## 🐳 Local Development (Docker Compose)

The `docker-compose.yml` file is provided for local development and testing:

```bash
docker-compose up --build
