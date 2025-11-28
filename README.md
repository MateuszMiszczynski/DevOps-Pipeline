# DevOps Project: Automated CI/CD & GitOps Pipeline

## Project Overview

This repository demonstrates a **fully automated DevOps pipeline** integrating **CI/CD** and **GitOps** practices. The project automatically deploys a simple **Python Flask web app** to a local **MiniKube** cluster on every GitHub push.

It showcases **modern DevOps skills**: infrastructure automation, containerization, CI/CD pipelines, and GitOps deployment.

---

## Architecture & Technologies

**Infrastructure as Code (IaC) – Terraform**
Manages the MiniKube cluster lifecycle and deploys **Argo CD** using Helm, enabling reproducible environments.

**Kubernetes Cluster – MiniKube (Docker driver)**
Runs a lightweight local Kubernetes cluster with essential add-ons for storage and provisioning.

**Application – Python (Flask)**
A simple web app that returns the current timestamp, containerized using Docker.

**Continuous Integration (CI) – GitHub Actions**
Builds the Docker image, tags it with the commit SHA, pushes it to Docker Hub, and updates the Helm chart `values.yaml` automatically.

**Package Management – Helm**
Simplifies deployment and configuration management by dynamically referencing image tags.

**Continuous Delivery / GitOps – Argo CD**
Monitors the GitHub repository, ensures the cluster state matches Git, and automates deployments.

---

## Workflow

1. **Code Change:** Developer edits `app.py` and pushes to GitHub.
2. **CI (GitHub Actions):** Builds Docker image → pushes to Docker Hub → updates Helm chart `values.yaml`.
3. **GitOps/CD (Argo CD):** Detects changes → syncs cluster → deploys the new version automatically.
4. **Verification:** `kubectl port-forward svc/<service> 8080:8080` → open [http://localhost:8080](http://localhost:8080).

---

## Setup

```bash
# Initialize Terraform
terraform init -upgrade

# Plan & Apply Infrastructure
terraform plan
terraform apply (after first error will occure but the solution is to simply hit terraform apply second time, it happens because terraform first needs to create docker container and then the rest)

# Access Argo CD UI
kubectl port-forward svc/argocd-server -n argocd 8081:443
Note: Port forwarding may vary depending on your system.

# Open https://127.0.0.1:8081 in a browser.
Login credentials:
Username: admin
Password:
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
And then use it in this command to login into Argo CD CLI
argocd login localhost:8081 --username admin --password <password> --insecure

# Connect Git Repository - Generate a GitHub Personal Access Token (PAT) with repo authorization first
argocd repo add <repo_url> --username <github_username> --password <personal_access_token> --server localhost:8081 --insecure

# Deploy Application via Argo CD
kubectl apply -f argocd-path.yaml

```

---

## Skills & Learnings

* **IaC & Terraform:** Manage local Kubernetes clusters.
* **Docker & Containerization:** Build, tag, and deploy images.
* **CI/CD Pipelines:** Automate build, test, and deployment using GitHub Actions.
* **Helm:** Simplified Kubernetes deployment and configuration management.
* **GitOps:** Automated, self-healing deployments using Argo CD.
* **DevOps Practices:** End-to-end automation, version control integration, reproducible deployments.

---

## References

* [Terraform](https://www.terraform.io/docs)
* [MiniKube](https://minikube.sigs.k8s.io/docs/)
* [GitHub Actions](https://docs.github.com/en/actions)
* [Helm](https://helm.sh/docs/)
* [Argo CD]([https://argo-cd.readthedocs.io/](https://argo-cd.readthedocs.io/)
* This project is based on a tutorial from YouTube by "The DevOps Dude" , but I have fully implemented it myself, configured the pipeline, and experimented with customization in CI/CD and GitOps workflows.
