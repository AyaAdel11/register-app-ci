# 🚀 End-to-End GitOps CI/CD Pipeline with EKS, Jenkins & Argo CD

This project demonstrates a complete GitOps workflow for a Java-based application. It automates everything from code commit to deployment on Amazon EKS (Elastic Kubernetes Service) using a multi-tool pipeline.

## 🏗️ Architecture Overview
The pipeline follows these steps:

Con## 🚀 The Pipeline Workflow

### 1\. Continuous Integration (CI) - The Build Phase
  * **Source Control:** Jenkins triggers on every `git push` to the application repository.
  * **Maven Build:** Compiles the Java code and generates artifacts.
  * **Dockerization:** Builds a Docker image and tags it with the Jenkins Build Number (e.g., `v1.0.0-7`).
  * **Image Registry:** Pushes the newly built Docker image to **Docker Hub**.
  * **Remote Trigger:** Upon success, it uses a **Secure API Token** to trigger the downstream CD Job.

### 2\. Continuous Deployment (CD) - The Manifest Update
  * **Manifest Manipulation:** The CD job clones the GitOps repo and uses `sed` to update the `imageTag` in `deployment.yaml`.
  * **Automated Commit:** Pushes the updated manifest back to GitHub using Jenkins credentials.

### 3\. GitOps - The Sync Phase
  * **Declarative Setup:** Argo CD monitors the GitOps repository.
  * **Automated Sync:** Once it detects the new image tag in GitHub, it pulls the latest image from Docker Hub and updates the EKS deployment via a **Rolling Update** strategy.

-----tinuous Integration (CI): Jenkins builds the Java application using Maven, creates a Docker image, and pushes it to Docker Hub.
Trigger Mechanism: The CI job triggers a CD Job in Jenkins via API token.

![Task Proof](./screenshots/ci.png)


Continuous Deployment (CD): The CD job updates the Kubernetes manifest (YAML) in a dedicated GitOps repository with the new image tag.

![Task Proof](./screenshots/cd.png)


GitOps Synchronization: Argo CD detects changes in the GitOps repo and automatically syncs the state to the Amazon EKS cluster.

![Task Proof](./screenshots/argocd.png)

## 🛠️ Tech Stack
Cloud: AWS (EC2, EKS, VPC, IAM)

CI/CD: Jenkins (Master-Agent Architecture)

GitOps: Argo CD

Containerization: Docker

Orchestration: Kubernetes (EKS)

Build Tool: Maven

Infrastructure Tool: eksctl

---

## 🚀 The Pipeline Workflow

### 1\. Continuous Integration (CI) - The Build Phase
  * **Source Control:** Jenkins triggers on every `git push` to the application repository.
  * **Maven Build:** Compiles the Java code and generates artifacts.
  * **Dockerization:** Builds a Docker image and tags it with the Jenkins Build Number (e.g., `v1.0.0-7`).
  * **Image Registry:** Pushes the newly built Docker image to **Docker Hub**.
  * **Remote Trigger:** Upon success, it uses a **Secure API Token** to trigger the downstream CD Job.

### 2\. Continuous Deployment (CD) - The Manifest Update
  * **Manifest Manipulation:** The CD job clones the GitOps repo and uses `sed` to update the `imageTag` in `deployment.yaml`.
  * **Automated Commit:** Pushes the updated manifest back to GitHub using Jenkins credentials.

### 3\. GitOps - The Sync Phase
  * **Declarative Setup:** Argo CD monitors the GitOps repository.
  * **Automated Sync:** Once it detects the new image tag in GitHub, it pulls the latest image from Docker Hub and updates the EKS deployment via a **Rolling Update** strategy.

-----

## 🚀 Getting Started
### 1. Infrastructure Setup
Create the EKS cluster using eksctl in the us-east-1 region:

```bash
eksctl create cluster --name virtualtechbox-cluster \
--region us-east-1 \
--node-type t3.small \
--nodes 2 \
--managed
```

![Task Proof](./screenshots/cluster.png)


### 2. Jenkins Pipeline Configuration
CI Job: Configured to build code and push images with dynamic tags (e.g., 1.0.0-${BUILD_NUMBER}).

CD Job: Configured with a "Remote Trigger" and an Authentication Token to allow CI-to-CD communication.

![Task Proof](./screenshots/last-verion-in-ci.png)
![Task Proof](./screenshots/last-verion-in-cd.png)
![Task Proof](./screenshots/last-verion-in-argocd.png)

### 3. Argo CD Setup
Deployed on EKS using the standard manifest.

Connected to the GitOps repository to monitor deployment.yaml.

![Task Proof](./screenshots/last-verion-repo.png)

Image Synchronization: Once a new image is pushed to Docker Hub by Jenkins, Argo CD pulls the updated manifest and triggers the EKS cluster to pull the latest image from the Docker Registry.

![Task Proof](./screenshots/last-verion-in-dockerhub.png)

## 🖼️ Application Showcase

Here's a preview of the deployed `register-app` microservice running successfully on Amazon EKS, accessible via its AWS Load Balancer URL.

**Screenshot of the Live Application:**
![Register App Live Preview](screenshots/my-app.png)

## 📋 Key Pipeline Features
Automated Image Tagging: Uses sed to update the deployment manifest dynamically in every build.

Security: Implements Jenkins API tokens for secure remote job triggering.

Managed Nodes: Utilizes AWS Managed Node Groups for better stability and easier maintenance.

Self-Healing: Argo CD ensures the cluster state always matches the Git repository.

## 🧹 Cleanup
To avoid unnecessary AWS costs, the following steps were taken to decommission the environment:

Deleted EKS cluster: eksctl delete cluster --name virtualtechbox-cluster.

Terminated EC2 Jenkins instances.

Released Elastic IPs and deleted unused EBS volumes.

👩‍💻 Author
Aya Adel DevOps Engineer in Training
