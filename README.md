# 🗳️ K8s Kind Voting App

A comprehensive guide to setting up a **Kubernetes cluster using KIND** on an **AWS EC2 instance**, deploying a **microservices-based voting application**, implementing **GitOps using Argo CD**, and enabling **observability using Prometheus & Grafana**.

---

## 📌 Overview

This project demonstrates **end-to-end Kubernetes DevOps practices**, including:

- Launching and configuring an AWS EC2 instance
- Creating a multi-node Kubernetes cluster using **KIND**
- Deploying a microservices-based voting application
- Implementing **GitOps** with **Argo CD**
- Setting up **monitoring and observability** using **Prometheus & Grafana**

---

## 🧱 Architecture

![Architecture diagram](k8s-kind-voting-app.png)

---

## 📊 Observability

![Grafana diagram](grafana.png)
![Prometheus diagram](prometheus.png)

---

## 🧩 Application Components

The Voting App is composed of the following microservices:

- **Vote App (Python)** – Frontend web app for voting  
- **Redis** – Temporarily stores votes  
- **Worker (.NET)** – Processes votes and stores them  
- **PostgreSQL** – Persistent database  
- **Result App (Node.js)** – Displays voting results in real time  

---

## 🏗️ Architecture Overview

EC2 (Ubuntu)
├── Docker
├── KIND (3-node Kubernetes Cluster)
│ ├── Voting App (Pods & Services)
│ ├── Argo CD (GitOps)
│ └── Prometheus Stack
│ ├── Prometheus
│ ├── Grafana
│ └── Alertmanager

yaml
Copy code

---

## 📌 Prerequisites

- **AWS EC2 Instance**
  - Recommended: `t2.medium` or `t3.medium`
  - OS: Ubuntu 20.04 / 22.04
- Open Ports (for port-forwarding):
  - `3000`, `5000`, `8080`, `9090`

---

## 📁 Repository

https://github.com/LondheShubham153/k8s-kind-voting-app.git

yaml
Copy code

---

## 🚀 Step-by-Step Setup Guide

---

### 1️⃣ System Update & Docker Installation

```bash
sudo apt update
sudo apt install docker.io -y
sudo usermod -aG docker $USER && newgrp docker
Verify:

bash
Copy code
docker version
2️⃣ Clone the Repository
bash
Copy code
git clone https://github.com/LondheShubham153/k8s-kind-voting-app.git
cd k8s-kind-voting-app
3️⃣ Install KIND
bash
Copy code
cd kind-cluster
chmod +x install_kind.sh
./install_kind.sh
Verify:

bash
Copy code
kind version
4️⃣ Install kubectl
bash
Copy code
chmod +x install_kubectl.sh
./install_kubectl.sh
Verify:

bash
Copy code
kubectl version --client
5️⃣ Create KIND Kubernetes Cluster
bash
Copy code
kind create cluster --config=config.yml --name=my-cluster
Verify cluster:

bash
Copy code
kubectl get nodes
Expected:

1 control-plane

2 worker nodes

6️⃣ Deploy Voting Application
bash
Copy code
cd ../k8s-specifications
kubectl apply -f .
Verify:

bash
Copy code
kubectl get pods
kubectl get svc
7️⃣ Access Voting Application
bash
Copy code
kubectl port-forward svc/vote 5000:5000 --address=0.0.0.0
Open in browser:

cpp
Copy code
http://<EC2-IP>:5000
🔁 GitOps with Argo CD
8️⃣ Install Argo CD
bash
Copy code
kubectl create namespace argocd
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
Verify:

bash
Copy code
kubectl get pods -n argocd
9️⃣ Access Argo CD UI
bash
Copy code
kubectl port-forward -n argocd svc/argocd-server 8080:443 --address=0.0.0.0
Open:

cpp
Copy code
https://<EC2-IP>:8080
🔐 Argo CD Login
Username

nginx
Copy code
admin
Password

bash
Copy code
kubectl get secret -n argocd argocd-initial-admin-secret \
-o jsonpath="{.data.password}" | base64 -d
📌 GitOps Note
Voting App can be managed via Argo CD Applications

Any change in Git repository → automatically synced to Kubernetes cluster

📊 Observability with Prometheus & Grafana
🔟 Install Helm
bash
Copy code
cd ../kind-cluster
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
Verify:

bash
Copy code
helm version
1️⃣1️⃣ Add Prometheus Helm Repositories
bash
Copy code
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add stable https://charts.helm.sh/stable
helm repo update
1️⃣2️⃣ Install kube-prometheus-stack
bash
Copy code
kubectl create namespace monitoring
bash
Copy code
helm install kind-prometheus \
  prometheus-community/kube-prometheus-stack \
  -n monitoring
Verify:

bash
Copy code
kubectl get pods -n monitoring
1️⃣3️⃣ Access Prometheus
bash
Copy code
kubectl port-forward svc/kind-prometheus-kube-prometheus-prometheus \
-n monitoring 9090:9090 --address=0.0.0.0
URL:

cpp
Copy code
http://<EC2-IP>:9090
1️⃣4️⃣ Access Grafana
bash
Copy code
kubectl port-forward svc/kind-prometheus-grafana \
-n monitoring 3000:80 --address=0.0.0.0
URL:

cpp
Copy code
http://<EC2-IP>:3000
🔐 Grafana Login
Username

nginx
Copy code
admin
Password

bash
Copy code
kubectl get secret -n monitoring kind-prometheus-grafana \
-o jsonpath="{.data.admin-password}" | base64 -d
📈 Sample Prometheus Queries
promql
Copy code
sum(rate(container_cpu_usage_seconds_total{namespace="default"}[1m]))
promql
Copy code
sum(container_memory_usage_bytes{namespace="default"}) by (pod)
promql
Copy code
sum(rate(container_network_receive_bytes_total{namespace="default"}[5m])) by (pod)
promql
Copy code
sum(rate(container_network_transmit_bytes_total{namespace="default"}[5m])) by (pod)
📊 Grafana Dashboards
Import dashboards from Grafana Marketplace:

6417 – Kubernetes Cluster Monitoring

315 – Node Exporter Full

🧠 Key Learnings (Interview Ready)
KIND enables local multi-node Kubernetes clusters

Argo CD implements GitOps-based deployments

Prometheus collects Kubernetes metrics

Grafana visualizes infrastructure and application metrics

Helm simplifies complex Kubernetes installations

🧹 Cleanup (Optional)
bash
Copy code
kind delete cluster --name=my-cluster
✅ Project Status
✔ Kubernetes Cluster
✔ Voting App Deployment
✔ GitOps using Argo CD
✔ Monitoring with Prometheus & Grafana

🙏 Aapke DevOps Wale Bhaiya
🔗 TrainWithShubham
