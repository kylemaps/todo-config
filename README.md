# Todo Application - Kubernetes Configuration

This repository serves as the single source of truth for the Kubernetes deployment of the **Todo Application Stack**. It follows the GitOps methodology and is managed by ArgoCD.

---

## Repository Structure

This is a **Configuration Repository**. It contains only the Kubernetes manifests (`.yaml` files) required to run the entire application. It does **not** contain any application source code.

The repository is structured using Kustomize to manage multiple environments:

*   **`base/`**: This directory contains the common, environment-agnostic Kubernetes manifests for all services (Deployments, Services, etc.).
*   **`overlays/`**: This directory contains environment-specific configurations.
    *   **`overlays/staging/`**: Contains patches and configurations for the **staging** environment. This includes disabling the Discord broadcaster and database backups, and setting the hostname to `staging.*`.
    *   **`overlays/production/`**: Contains patches and configurations for the **production** environment, including setting the hostname to `prod.*`.

## Application Stack

The application stack consists of three microservices:
1.  **`todo-frontend`**: The user-facing web interface.
2.  **`todo-backend`**: The core API service that manages tasks and communicates with the database.
3.  **`broadcaster`**: A notification service that listens for events and forwards them to Discord.

The source code for these applications can be found in their respective application repositories.

## GitOps Workflow with ArgoCD

This repository is designed to be managed by an automated CI/CD process.

1.  **CI Pipelines Push Updates:** When a new version of an application is built (e.g., in the `todo-backend` repository), the CI pipeline will automatically commit a change to the `kustomization.yaml` file in the appropriate overlay in this repository, updating the Docker image tag.
2.  **ArgoCD Watches This Repo:** An ArgoCD instance is configured to continuously monitor this repository.
3.  **Automatic Sync:** ArgoCD detects the new commit, compares the desired state (from this repo) with the live state (in the Kubernetes cluster), and automatically syncs the changes, triggering a deployment of the new application version to the correct environment.

### Manual Changes

Manual changes to this repository should be made with caution. It is primarily intended to be updated by automated CI processes. Manual edits are typically only for permanent configuration adjustments, such as changing a resource limit or modifying a ConfigMap.