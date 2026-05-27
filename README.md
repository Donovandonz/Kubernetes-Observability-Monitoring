# Kubernetes-Observability-and-Monitoring
💻k8s-prometheus-grafana-demo

## Kubernetes + Prometheus + Grafana: Local Observability Stack

<img width="1917" height="1027" alt="flask-app-with-image" src="https://github.com/user-attachments/assets/6514964e-b4d5-4763-af67-1e27d0e7facf" />

<img width="1917" height="955" alt="My-Flask-App-Monitoring" src="https://github.com/user-attachments/assets/ab0ced53-cee2-48c3-9efe-d0aead2b9d22" />

<img width="1912" height="967" alt="CPU-usage-over-time" src="https://github.com/user-attachments/assets/0c91e486-b76f-4532-8bdb-fbcc58c95335" />

<img width="1915" height="968" alt="Kubernetes-Compute Resources-Cluster" src="https://github.com/user-attachments/assets/08d52d3d-a951-47ac-bc7f-30719420c97f" />

<img width="1918" height="946" alt="Prometheus-Overview" src="https://github.com/user-attachments/assets/78df45e9-4f98-4519-8bb2-69f9c369e5f2" />

---

## 📌 Project Overview

This project demonstrates a complete **observability pipeline** for a containerized Flask application running on a local Kubernetes cluster (Minikube). It includes:
- Deployment of a Python Flask app with 2 replicas.
- Service exposure via NodePort.
- Installation of the `kube-prometheus-stack` (Prometheus, Grafana, Alertmanager) using Helm.
- Custom Grafana dashboards to visualize CPU and memory usage of the Flask app.

All resources run **locally** – no cloud costs.

## 🛠️ Technologies Used

| Category       | Tools                                                                 |
|----------------|-----------------------------------------------------------------------|
| Orchestration  | Kubernetes (Minikube), kubectl                                        |
| Container      | Docker                                                                 |
| Monitoring     | Prometheus, Grafana, Helm                                             |
| Language       | Python 3.9 + Flask                                                    |
| Version Ctrl   | Git                                                                   |

---

## 🏗️ Architecture

```text
[Flask App] → [Docker Image] → [K8s Deployment (2 replicas)] → [NodePort Service]
                                                                         ↓
[Prometheus] ← scrapes metrics from K8s components and pods
       ↓
[Grafana] ← queries Prometheus → [Custom Dashboards]
```

---

# 🚀 How to Run This Project (Locally)

### Prerequisites
Windows 10/11 with Docker Desktop

- Minikube

- kubectl

- Helm

- Docker Desktop

### Step‑by‑Step
1. Start Minikube
```
minikube start --driver=docker
```
2. Build the Docker image inside Minikube
```
eval $(minikube docker-env)
docker build -t flask-k8s-app:v1 .
```
3. Deploy the app
```
kubectl apply -f k8s/deployment.yaml.txt
kubectl apply -f k8s/service.yaml.txt
```
4. Install the monitoring stack
```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus prometheus-community/kube-prometheus-stack --namespace monitoring --create-namespace
```
5. Access Grafana
```
kubectl port-forward --namespace monitoring service/prometheus-grafana 3000:80
```
- URL: http://localhost:3000

- Username: admin

- Password: retrieve with:
```
kubectl get secret -n monitoring prometheus-grafana -o jsonpath="{.data.admin-password}" | base64 --decode
```
6. Create custom dashboards
- See Grafana Dashboard Creation section in this README.

---

## PromQL Queries Used in Grafana:
- CPU Usage Rate:
```
sum(rate(container_cpu_usage_seconds_total{pod=~"flask-app-deployment-.*"}[5m]))
```

- Memory usage:
```
sum(container_memory_working_set_bytes{pod=~"flask-app-deployment-.*"}) by (pod)
```
---

# 🧹 Cleanup
- To free system resources:
```
kubectl delete -f k8s/deployment.yaml
kubectl delete -f k8s/service.yaml
helm uninstall prometheus -n monitoring
kubectl delete namespace monitoring
minikube stop   # or minikube delete
```
---

# 🎯 What I Learned
- Deploying and managing a multi‑container application on Kubernetes.

- Instrumenting a cluster with Prometheus and visualizing metrics in Grafana.

- Writing custom PromQL queries to filter by pod labels.

- Rolling updates and local container image management.

