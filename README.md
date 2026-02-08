# 🚀 End-to-End DevOps + GitOps Deployment Project

**Static Website + Containerized Application | AWS | Kubernetes | GitOps**

---

# 📌 Project Overview

This project demonstrates a **complete End-to-End DevOps and GitOps deployment architecture** implemented using AWS Cloud, Kubernetes, Terraform, GitHub Actions, Helm, and ArgoCD.

The goal of this project was to simulate how real enterprise companies deploy and manage applications — starting from infrastructure provisioning to automated production deployments.

Two workloads were deployed as part of this implementation:

1. **Static Website Deployment**
   Hosted and delivered using cloud storage, containerization, and Kubernetes.

2. **Containerized Go Web Application**
   Deployed using a full GitOps workflow with CI/CD automation.

This project covers the entire Software Delivery Lifecycle:

* Infrastructure provisioning (Terraform)
* Containerization (Docker)
* Image registry (Amazon ECR)
* CI automation (GitHub Actions)
* GitOps deployment (ArgoCD)
* Kubernetes orchestration (EKS)
* Multi-environment releases (Dev / Staging / Prod)

---

# 🏗️ Architecture Overview

## Infrastructure Flow

VPC → Subnets → Internet Gateway → Route Tables → EKS Cluster → Node Groups → Kubernetes

## Application Delivery Flow

Developer Push → GitHub Actions → Docker Build → ECR Push → Helm Update → GitOps Repo → ArgoCD Sync → Kubernetes Deployment

## User Access Flow

User → Route53 → AWS ALB Ingress → Kubernetes Service → Pods → Application

---

# 🧰 Tech Stack & Tools

| Category      | Tools / Services        |
| ------------- | ----------------------- |
| Cloud         | AWS                     |
| Compute       | EC2                     |
| Storage       | S3                      |
| Container     | Docker                  |
| Registry      | Amazon ECR              |
| Orchestration | Amazon EKS (Kubernetes) |
| IaC           | Terraform               |
| CI            | GitHub Actions          |
| CD / GitOps   | ArgoCD                  |
| Packaging     | Helm                    |
| DNS           | Route53                 |
| Security      | IAM, OIDC, IRSA         |
| Language      | Go (Golang)             |
| OS            | Ubuntu 22.04            |

---

# 🌍 Multi-Environment Strategy

Three isolated environments were created to simulate enterprise release workflows:

* **Development**
* **Staging**
* **Production**

Each environment runs in a separate Kubernetes namespace and uses dedicated Helm values files:

```
values-dev.yaml
values-staging.yaml
values-prod.yaml
```

This allows controlled promotions across environments.

---

# ☁️ Infrastructure Provisioning — Terraform

Infrastructure was provisioned using **Infrastructure as Code (IaC)** principles with reusable Terraform modules.

## Resources Created

* VPC with CIDR block
* Public & Private Subnets (Multi-AZ)
* Internet Gateway
* NAT Gateway
* Route Tables
* Security Groups
* EKS Cluster
* Managed Node Groups
* S3 Bucket (Terraform backend)
* DynamoDB Table (State locking)

## Backend Configuration

Terraform remote backend was configured using:

* **S3** → Stores state file
* **DynamoDB** → Prevents concurrent state modification

---

# 🖥️ EC2 Bastion / Admin Host Setup

An EC2 instance was provisioned to act as a central DevOps management server.

## Configuration

* Instance Type: c7i-flex.large
* OS: Ubuntu 22.04
* Storage: 30 GB
* Security Group: All TCP (Lab purpose)

## Tools Installed

* AWS CLI
* Terraform
* Docker
* kubectl
* Helm
* Git
* ArgoCD CLI

This server was used to manage infrastructure, clusters, and deployments.

---

# 🚀 Application Workloads

## 1️⃣ Static Website

A static website was deployed as part of the DevOps pipeline to demonstrate frontend hosting and container deployment.

Key activities:

* Website source prepared
* Docker image created
* Deployed into Kubernetes
* Exposed via Ingress + Load Balancer

---

## 2️⃣ Go Web Application

A production-style Go application was used to simulate backend workloads.

### Repository Cloned

* Go Web App source code
* OpenTelemetry reference project

### Local Testing

```
go build -o main .
./main
http://localhost:8080/courses
```

---

# 🐳 Containerization — Docker

Both applications were containerized.

## Build Image

```
docker build -t go-web-app:v1 .
```

## Run Container

```
docker run -p 8080:8080 go-web-app:v1
```

Docker ensures consistency across environments.

---

# 📦 Amazon ECR — Image Registry

Private repositories were created in Amazon ECR to store container images.

## Steps Performed

1. Created ECR repository
2. Authenticated Docker to AWS
3. Tagged image
4. Pushed image to ECR

Image URI format:

```
<AccountID>.dkr.ecr.<region>.amazonaws.com/repo:tag
```

---

# 🔐 IAM & OIDC Authentication

GitHub Actions was integrated with AWS using **OIDC Federation**.

## Authentication Flow

1. GitHub requests OIDC token
2. AWS validates identity
3. IAM Role assumed
4. Temporary credentials issued
5. Secure ECR push executed

No static AWS access keys were used.

---

# 🔁 CI Pipeline — GitHub Actions

CI pipelines were configured for automated builds.

## Trigger Branches

* dev
* staging
* main

## Pipeline Stages

1. Checkout repository
2. Build application
3. Run lint checks
4. Build Docker image
5. Tag image using Git SHA
6. Push image to ECR
7. Update Helm values file

This ensures traceable and versioned deployments.

---

# 📂 Repository Structure

## Application Repository

```
go-web-app/
├── app/
├── Dockerfile
├── .github/workflows/
└── README.md
```

## GitOps Repository

```
End-To-End-GitOps/
├── helm/
│   └── go-web-app/
│       ├── Chart.yaml
│       ├── values-dev.yaml
│       ├── values-staging.yaml
│       ├── values-prod.yaml
│       └── templates/
```

---

# ⎈ Kubernetes Deployment

Kubernetes manifests were created for:

* Deployment
* Service
* Ingress

Namespaces used:

* dev
* staging
* prod
* argocd

---

# 📦 Helm Templating

Helm was used to templatize Kubernetes manifests.

Dynamic injection example:

```
{{ .Values.image.tag }}
```

This enabled reusable deployments across environments.

---

# 🔄 GitOps — ArgoCD Deployment

ArgoCD continuously monitors the GitOps repository.

## Workflow

1. CI updates Helm image tag
2. Git commit pushed
3. ArgoCD detects change
4. Auto-sync triggered
5. New version deployed

Supports rollback and audit history.

---

# 🌐 AWS ALB Ingress + Domain

Traffic was exposed using AWS Application Load Balancer Ingress Controller.

## Components

* AWS Load Balancer Controller
* ALB
* Kubernetes Ingress
* Route53 Hosted Zone

## Traffic Flow

User → Route53 → ALB → Service → Pods

## Subdomain Routing

* dev.example.com
* staging.example.com
* app.example.com

Each mapped to its namespace.

---

# 🔒 Security Best Practices

* IAM roles instead of root access
* OIDC federation
* Private ECR repositories
* Namespace isolation
* RBAC authorization
* IRSA for controllers

---

# 📊 Deployment Strategies

Implemented:

* Blue-Green Deployment
* Canary Releases

Ensures zero downtime.

---

# 🧪 End-to-End Automation Flow

```
Code Push →
CI Build →
Docker Image →
ECR Push →
Helm Update →
GitOps Repo →
ArgoCD Sync →
Kubernetes Deploy
```

---

# 🛠️ Prerequisites

* AWS CLI
* Terraform
* Docker
* kubectl
* Helm
* Git
* ArgoCD CLI

---

# 🚀 Final Outcome

* Fully automated CI/CD pipeline
* GitOps-driven deployments
* Multi-environment Kubernetes setup
* Production-grade AWS infrastructure
* Scalable microservice deployment model

---

# 📚 Key Learnings

* Infrastructure as Code
* Kubernetes orchestration
* Helm templating
* GitOps workflows
* OIDC authentication
* ECR lifecycle management
* Enterprise CI/CD design

---

# 🔮 Future Enhancements

* Prometheus & Grafana monitoring
* Centralized logging
* WAF integration
* Secrets Manager / Vault
* Cost optimization policies

---

# 👨‍💻 Author

**Narendhiran B**
DevOps Engineer (Fresher)

Specializing in:

* AWS Cloud
* Kubernetes
* Terraform
* CI/CD Automation
* GitOps

---

> This project was built to replicate real-world enterprise DevOps and GitOps deployment practices and demonstrate production-ready cloud engineering capabilities.

---

## 📸 Project Implementation Proof

### 1️⃣ End-to-End Architecture

![end to end image](https://github.com/user-attachments/assets/b29b406c-20c3-4b00-b3a7-8caa8b8b8ed3)

---

### 2️⃣ GitHub Actions — CI Pipeline

![github actions](https://github.com/user-attachments/assets/9760ab5f-3026-45c1-949a-d490ee42c681)

---

### 3️⃣ ArgoCD — GitOps Deployment

<img width="1920" height="1200" alt="ArgoCD UI" src="https://github.com/user-attachments/assets/e77f7b82-9af2-46a1-9597-19ea42357cf7" />

---

### 4️⃣ Application — Live UI

<img width="1920" height="1094" alt="Final application output" src="https://github.com/user-attachments/assets/bbd823fb-1e02-445b-bb39-bfaa71a256db" />
