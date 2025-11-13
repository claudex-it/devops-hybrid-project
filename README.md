#  DevOps Hybrid Project – Local GitLab CI/CD + Kubernetes (Minikube)

This repository contains a full production-like DevOps environment running entirely on a **local Linux host** with optional macOS access through Tailscale.

It demonstrates real-world DevOps engineering:  
**containerization → orchestration → CI/CD → monitoring → automation → deployment.**

The stack includes:

- GitLab CE (self-hosted, running in Docker)
- GitLab Runner (Shell executor)
- Minikube Kubernetes cluster
- Automated CI/CD pipeline
- Monitoring stack (Prometheus + Grafana + Loki)
- Dockerized application (2 replicas)
- IaC structure (Terraform + Ansible)
- macOS → Linux port forwarding (Tailscale magic)

---

#  Table of Contents
- [ DevOps Hybrid Project – Overview](#-devops-hybrid-project--overview)
- [ Architecture Overview](#-architecture-overview)
- [ Tech Stack](#-tech-stack)
- [ CI/CD Pipeline](#-cicd-pipeline)
- [ Kubernetes Deployment](#️-kubernetes-deployment)
- [ Monitoring & Observability](#-monitoring--observability)
- [ Infrastructure Automation](#-infrastructure-automation)
- [ How to Use the Project](#️-how-to-use-the-project)
- [ Accessibility via Tailscale](#-accessibility-via-tailscale)
- [ Documentation](#-documentation)
- [ Roadmap](#-roadmap)
- [ Author & Purpose](#-author--purpose)

---

#  DevOps Hybrid Project – Overview

This project provides a fully functional **local DevOps platform**, ideal for learning, portfolio, labs, and demonstrating real-world DevOps skills.

###  What it includes:
- Local GitLab CE with CI/CD
- GitLab Runner (systemd service)
- Kubernetes (Minikube)
- Monitoring stack (Grafana, Prometheus, Loki, Promtail)
- Automated Docker builds inside Minikube
- Kubernetes deployments triggered by GitLab pipeline
- Tailscale remote access
- Terraform & Ansible structure for future IaC expansion

---

#  Architecture Overview

| Component            | Technology                     | Status       | Purpose                                      |
|----------------------|---------------------------------|--------------|----------------------------------------------|
| **Host machine**     | Linux / Ubuntu                  | ✔ Active     | Runs Minikube, GitLab, GitLab Runner         |
| **Minikube** (1 node)| Kubernetes                      | ✔ Running    | Orchestrates app & monitoring stack          |
| **GitLab CE**        | Docker container                | ✔ Healthy    | Local Git + CI/CD platform                   |
| **GitLab Runner**    | Systemd service                 | ✔ Active     | Executes CI/CD pipeline (Shell executor)     |
| **Monitoring Stack** | Prometheus + Grafana + Loki     | ✔ Running    | Metrics + logging                            |
| **DevOps App**       | Docker + Kubernetes Deployment  | ✔ Running    | Demo web app (2 replicas)                    |
| **System Services**  | systemd                         | ✔ Active     | Auto-start Minikube + port-forwarding        |
| **macOS Access**     | Tailscale                       | ✔ Connected  | Remote access with port mapping              |
| **K8s Tools**        | Lens, kubectl, Visual K8s       | ✔ Configured | Cluster observability                         |

---

#  Tech Stack

### Languages
- Python (Flask/WSGI app)
- Bash automation scripts

### Infrastructure
- Kubernetes (Minikube)
- Docker
- GitLab CI/CD
- Ansible (structure ready)
- Terraform (structure ready)

### Monitoring
- Prometheus
- Grafana
- Loki
- Node Exporter
- Prometheus Alertmanager

---

#  CI/CD Pipeline

The GitLab pipeline performs two stages:

---

### **1. Build Stage**
- Uses Minikube Docker daemon  
  `eval $(minikube docker-env)`
- Builds image from `app/Dockerfile`
- Tags it as:  
  `devops-app:latest`
- Image stays **inside Minikube** → no registry needed

---

### **2. Deploy Stage**
- Applies Kubernetes manifests:
  - Deployment (2 replicas)
  - NodePort service
- Automatically rolls out new pods
- Shows final pod state

---

#  Kubernetes Deployment

```
Replicas: 2
ContainerPort: 5000
Image: devops-app:latest (local, no registry pull)
```

### Access
```bash
minikube service list
```

Example:
```
http://192.168.49.2:<NodePort>
```

---

#  Monitoring & Observability

| Component        | Port  | Purpose           |
|------------------|-------|-------------------|
| Grafana          | 32123 | Dashboards        |
| Prometheus       | 9090  | Metrics           |
| Loki             | 3100  | Logs backend      |
| Node Exporter    | 9100  | System metrics    |

Grafana auto-loads Prometheus and Loki datasources.

---

#  Infrastructure Automation

### **Ansible**  
Folder: `ansible/`  
Used for installing:
- Docker
- GitLab Runner
- K8s CLI tools
- System utilities

### **Terraform**  
Folder: `terraform/`  
Prepared for:
- AWS VPC
- EKS cluster
- S3 state backend
- IAM roles

*(Terraform phase planned in roadmap)*

---

#  How to Use the Project

### 1.Start Minikube  
```bash
minikube start --driver=docker
```

###  2.(Optional) Build the app manually  
```bash
eval $(minikube docker-env)
docker build -t devops-app:latest -f app/Dockerfile app/
```

### 3.Apply Kubernetes manifests  
```bash
kubectl apply -f kubernetes/
```

### 4.Check status  
```bash
kubectl get pods -o wide
minikube service list
```

---

#  Accessibility via Tailscale

Using Tailscale tunnel, all local services are accessible remotely (macOS ↔ Linux).

| Service        | URL                                |
|----------------|-------------------------------------|
| DevOps App     | http://100.82.136.39:32444          |
| Grafana        | http://100.82.136.39:32123          |
| GitLab         | http://100.82.136.39:8929           |
| K8s API (Lens) | https://100.82.136.39:8443          |

---

#  Documentation

Architecture diagram:  
```
docs/architecture.png
```

Additional docs will be added in the next phase.

---

#  Roadmap

- Add Helm charts for the app  
- Deploy AWS infrastructure (EKS + VPC) via Terraform  
- Move monitoring into Helm  
- Add Ingress controller (NGINX)  
- Add GitLab Kubernetes Agent  
- Implement Ansible automation for full setup  
- Add CI/CD security scanning (SAST/DAST)

---

#  Author & Purpose

Purpose of the project:

- Demonstrate a full DevOps workflow  
- Combine CI/CD + containerization + Kubernetes + monitoring  
- Provide a production-like local environment  
- Serve as a base for future cloud migration (AWS EKS)
