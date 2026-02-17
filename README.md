# End-to-End DevOps GitOps Project – Kubernetes Static Website Deployment
---
## Project Overview
This project demonstrates a complete end-to-end DevOps workflow implemented on AWS using a GitOps deployment model.
I deployed a static website on Kubernetes with fully automated infrastructure provisioning, container build pipelines, and continuous delivery using Argo CD.
The focus of this project was to simulate real-world production practices  including modular infrastructure design, environment isolation, automated rollback, and Git-driven deployments.
---
## Architecture Summary
The system is structured into three independent but integrated layers:
1. **Infrastructure Layer** → Provisioned using Terraform
2. **Continuous Integration Layer** → Builds and pushes container images
3. **GitOps Continuous Deployment Layer** → Deploys workloads via Argo CD
Each layer operates independently to ensure scalability, maintainability, and deployment safety.
---
## Tools & Technologies Used
* AWS (EKS, IAM, VPC, Route 53, Load Balancer)
* Terraform (Infrastructure as Code)
* Docker (Containerization)
* Kubernetes (Container orchestration)
* Helm (Kubernetes packaging)
* Argo CD (GitOps CD tool)
* GitHub (Source control & pipelines)
---
## Infrastructure Provisioning (Terraform)
The entire AWS infrastructure was provisioned using Terraform with a modular architecture.
### Module Structure
* Networking Module → VPC, subnets, routing
* IAM Module → Roles & policies (least privilege)
* EKS Module → Kubernetes cluster & node groups
### Remote State Management
* State stored in S3
* State locking handled via DynamoDB
This ensures safe collaboration and prevents state conflicts.
---
## Kubernetes Cluster Setup
After provisioning:
* Worker nodes joined the EKS cluster
* IAM roles mapped for Kubernetes access
* Ingress controller deployed
* Load balancer integration configured
The cluster is prepared to receive GitOps deployments.
---
## Continuous Integration (CI Pipeline)
The CI pipeline is connected to the application source repository.
### Pipeline Workflow
1. Code pushed to repository
2. Pipeline triggers automatically
3. Docker image is built
4. Image is tagged with commit/version
5. Image pushed to container registry
This produces immutable deployment artifacts for every change.
---
## GitOps Repository Design
I used a separate repository to manage deployments.
### Repository Separation
**Application Repository**
* Source code
* Docker build pipeline
**GitOps Repository**
* Helm charts
* Environment configurations
This separation ensures deployment changes are controlled and auditable.
---
## End-to-End Workflow Integration
This project is designed as a fully automated delivery system where infrastructure, build, and deployment workflows are integrated but independently managed.
### Step 1 – Infrastructure Provisioning
Terraform provisions:
* VPC and networking
* IAM roles
* EKS cluster
Remote state is stored in S3 and locked via DynamoDB.
---
### Step 2 – Cluster Readiness
Post-provisioning:
* Node groups join the cluster
* Access roles configured
* Ingress controller installed
Cluster becomes deployment-ready.
---
### Step 3 – Continuous Integration
When application code is pushed:
1. CI pipeline triggers
2. Docker image builds
3. Image pushed to registry
---
### Step 4 – GitOps Deployment Trigger
Instead of deploying from CI:
* Image tag is updated in GitOps Helm values
* Git becomes deployment authority
---
### Step 5 – Argo CD Synchronization
Argo CD monitors the GitOps repository.
When drift is detected:
1. Pulls updated manifests
2. Compares desired vs live state
3. Syncs changes to EKS
No manual kubectl deployments are used.
---
## Environment-Based Deployments
Helm charts manage three isolated environments:
* **Dev** → Feature testing
* **Staging** → Pre-production validation
* **Production** → Live workloads
Each environment has independent configs and releases.
---
## Traffic Management & DNS
Application traffic is exposed using:
* Kubernetes Ingress Controller
* AWS Load Balancer
* Route 53 Hosted Zones
Three subdomains are configured:
* dev.
* staging.
* prod.
Each routes traffic to its respective environment.
---
## Rollback Strategy
Rollback is fully automated and Git-driven.
If a deployment fails:
1. Git commit is reverted
2. Argo CD detects state change
3. Previous stable version is restored
No manual cluster intervention required.
---
## DevOps Practices Implemented
* Infrastructure as Code
* Modular Terraform design
* Remote state & locking
* GitOps deployment model
* CI automated builds
* Helm packaging
* Multi-environment isolation
* Ingress & load balancing
* DNS routing
* Automated rollback
* Least-privilege IAM
---
## Project Outcomes
Through this project, I gained hands-on experience in:
* Designing production-style AWS infrastructure
* Managing Kubernetes deployments
* Implementing GitOps workflows
* Handling environment promotion strategies
* Troubleshooting deployment and sync issues
---
## Architecture Diagram
![end to end image](https://github.com/user-attachments/assets/953ce8da-8887-4a61-aa6c-52a9edfba8b0)

---

## Deployment Proof / Screenshots

* **CI Pipeline Execution**
  
  ![github actions](https://github.com/user-attachments/assets/cd6a1e53-d199-4c40-aa5f-55fb85c3b8a9)

---

* **Argo CD Sync Dashboard**
  
 <img width="1920" height="1143" alt="Screenshot (64)" src="https://github.com/user-attachments/assets/d56eee2f-faef-4115-bd7c-60acb02674ce" />

 <img width="1920" height="1135" alt="Screenshot (67)" src="https://github.com/user-attachments/assets/b302f3e1-f76a-43ab-bf27-36bd67e65b6b" />

---

* **Route53 - Hosted zone - Sub-Domains**
  
  <img width="1920" height="1200" alt="dns" src="https://github.com/user-attachments/assets/66f6d5f6-78d3-4a72-abe9-4f3906244106" />

---

* **Live Application URL**
  
  <img width="1920" height="1094" alt="Final application output" src="https://github.com/user-attachments/assets/6f159f93-3084-4cc2-a37e-04c575a299c1" />

---

## Source Code Repositories
* Application & CI Pipeline → https://github.com/Narendhiran-B/End-to-End-Main.git
* GitOps Deployment Repo → https://github.com/Narendhiran-B/End-to-End-GitOps.git
---
## Author Note
This project was built as a production-style DevOps implementation to demonstrate practical skills in cloud infrastructure, Kubernetes, CI/CD automation, and GitOps deployment strategies as a fresher entering the DevOps domain.
---
