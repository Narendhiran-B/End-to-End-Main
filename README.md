# End-to-End DevOps GitOps Deployment Project

## 📌 Project Overview

This project demonstrates a **production-grade End-to-End DevOps deployment** of a containerized Go web application using AWS Cloud, Kubernetes, GitHub Actions CI, Helm, and ArgoCD GitOps.

The workflow covers the complete software delivery lifecycle:

* Infrastructure provisioning using Terraform (IaC)
* Application containerization using Docker
* Image storage in Amazon ECR
* CI automation using GitHub Actions
* GitOps deployment using ArgoCD
* Multi-environment Kubernetes deployments (Dev / Staging / Prod)

This architecture follows real-world enterprise DevOps practices including OIDC authentication, Helm templating, and declarative deployments.

---

# 🏗️ Architecture Overview

**Infrastructure Flow**

VPC → EKS → Kubernetes → Helm → ArgoCD → Application Pods

**Application Delivery Flow**

Git Push → GitHub Actions CI → Docker Build → ECR Push → GitOps Repo Update → ArgoCD Sync → Kubernetes Deployment

**User Access Flow**

Ingress → Load Balancer → Route53 → Domain → Application

---

# 🧰 Tech Stack & Tools

| Category      | Tools / Services        |
| ------------- | ----------------------- |
| Cloud         | AWS                     |
| Compute       | EC2                     |
| Container     | Docker                  |
| Registry      | Amazon ECR              |
| Orchestration | Amazon EKS (Kubernetes) |
| IaC           | Terraform               |
| CI            | GitHub Actions          |
| CD / GitOps   | ArgoCD                  |
| Packaging     | Helm                    |
| DNS           | Route53                 |
| Security      | IAM, OIDC               |
| Language      | Go (Golang)             |

---

# 🌍 Environments Strategy

Three isolated environments were deployed:

* **Development**
* **Staging**
* **Production**

Each environment runs in separate namespaces and is deployed via Helm values files:

```
values-dev.yaml
values-staging.yaml
values-prod.yaml
```

---

# ☁️ Infrastructure Provisioning (Terraform)

Infrastructure was provisioned using reusable Terraform modules.

## Resources Created

* VPC
* Subnets (Multi-AZ)
* Internet Gateway
* Route Tables
* EKS Cluster
* Node Groups
* S3 Backend (State Storage)
* DynamoDB (State Locking)

## Backend Configuration

* S3 bucket for Terraform state
* DynamoDB table for locking

---

# 🖥️ EC2 Bootstrap Environment

An EC2 instance was used as an admin/bastion host.

**Configuration**

* Instance: c7i-flex.large
* OS: Ubuntu 22.04
* Storage: 30 GB
* Security Group: All TCP (Lab setup)

Installed tools:

* AWS CLI
* Docker
* Terraform
* kubectl
* Helm
* Git
* ArgoCD CLI

---

# 🚀 Application Setup

## Repository Cloned

* Go Web Application
* OpenTelemetry (Terraform reference)

## Local Build & Test

```
go build -o main .
./main
http://localhost:8080/courses
```

---

# 🐳 Containerization

## Docker Build

```
docker build -t go-web-app:v1 .
```

## Run Container

```
docker run -p 8080:8080 go-web-app:v1
```

---

# 📦 Amazon ECR Integration

## Steps

1. Created private ECR repository
2. Authenticated using AWS CLI
3. Tagged Docker image
4. Pushed image to ECR

Image URI structure:

```
<AccountID>.dkr.ecr.<region>.amazonaws.com/repository:tag
```

---

# 🔐 IAM & OIDC Authentication

GitHub Actions uses **OIDC federation** to access AWS securely.

### Flow

1. GitHub requests OIDC token
2. AWS verifies identity
3. IAM Role assumed
4. Temporary credentials issued
5. Image pushed to ECR

No static AWS keys required.

---

# 🔁 CI Pipeline — GitHub Actions

Pipeline triggers on push to:

* dev
* staging
* main

## CI Stages

1. Checkout code
2. Build Go application
3. Run lint checks
4. Build Docker image
5. Tag image using Git SHA
6. Push image to ECR
7. Update Helm values.yaml

Image tagging ensures traceability.

---

# 📂 Git Repository Structure

## Application Repo

```
go-web-app/
├── app/
│   ├── main.go
│   └── go.mod
├── Dockerfile
├── .github/workflows/ci.yaml
└── README.md
```

## GitOps Repo

```
End-To-End-GitOps/
├── helm/go-web-app/
│   ├── Chart.yaml
│   ├── values-dev.yaml
│   ├── values-staging.yaml
│   ├── values-prod.yaml
│   └── templates/
│       ├── deployment.yaml
│       ├── service.yaml
│       └── ingress.yaml
```

---

# ⎈ Kubernetes Deployment

Manifests created:

* Deployment
* Service
* Ingress

Namespaces:

* dev
* staging
* prod
* argocd

---

# 📦 Helm Implementation

Helm was used to templatize Kubernetes manifests for multi-environment reuse.

## Helm Structure

```
helm create go-web-app-chart
```

Templates include:

* deployment.yaml
* service.yaml
* ingress.yaml

Dynamic values injected via:

```
{{ .Values.image.tag }}
```

---

# 🔄 GitOps Deployment — ArgoCD

ArgoCD monitors the GitOps repository.

## Workflow

1. CI updates Helm image tag
2. Git commit pushed
3. ArgoCD detects change
4. Syncs cluster automatically
5. New version deployed

Supports:

* Rollbacks
* Audit history
* Declarative deployments

---

# 🌐 AWS Ingress & Domain Setup

Application traffic is exposed using **AWS Application Load Balancer (ALB) Ingress Controller** instead of a generic ingress controller.

## Components Used

* AWS Load Balancer Controller
* Application Load Balancer (ALB)
* Kubernetes Ingress Resource
* Route53 Hosted Zone
* Subdomain Mapping

## Traffic Flow

User → Route53 → ALB (AWS Ingress) → Kubernetes Service → Pods

## Implementation Steps

1. Installed AWS Load Balancer Controller in EKS cluster
2. Configured IAM role for service account (IRSA)
3. Created Ingress YAML with ALB annotations
4. ALB automatically provisioned by AWS
5. Listener rules mapped to services
6. Subdomains attached via Route53

## Example Ingress Behavior

* Dev → dev.example.com
* Staging → staging.example.com
* Prod → app.example.com

Each environment routes traffic to its respective namespace.

---

## Benefits of AWS ALB Ingress

* Native AWS integration
* Automatic Load Balancer provisioning
* Path & host-based routing
* SSL termination support
* Better production scalability

---

# 🔒 Security Best Practices Implemented

* IAM Roles instead of root credentials
* OIDC federation for CI access
* Private ECR repositories
* Namespace isolation
* RBAC for cluster access

---

# 📊 Deployment Strategy

Supported strategies:

* Blue-Green
* Canary

Enables zero-downtime releases.

---

# 🧪 CI/CD + GitOps Automation Flow

```
Developer Push →
GitHub Actions Build →
Docker Image →
ECR Push →
Helm Values Update →
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
* Multi-environment deployments
* GitOps-driven Kubernetes releases
* Production-style cloud infrastructure
* Scalable and rollback-safe delivery model

---

# 📚 Key Learnings

* Infrastructure as Code design
* Kubernetes multi-env deployments
* Helm templating
* OIDC authentication
* GitOps workflows
* ECR image lifecycle
* ArgoCD automation

---

# 🔮 Future Enhancements

* Prometheus & Grafana monitoring
* AWS Load Balancer Controller
* WAF integration
* Cost optimization policies
* Secrets management (Vault / AWS Secrets Manager)

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

> This project was built to simulate real-world enterprise DevOps deployment architecture and demonstrate production-ready cloud engineering skills.
