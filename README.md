
---
# 🚀 Flask Metrics App on Kubernetes with Prometheus & Grafana

A modern cloud-native monitoring setup using **Flask**, **Docker**, **Kubernetes (Minikube)**, **Prometheus**, and **Grafana**.

This project simulates a metrics-enabled Flask API and demonstrates how to:
- Containerize it using Docker
- Deploy it on a Minikube Kubernetes cluster
- Monitor it using Prometheus
- Visualize metrics using Grafana

---

## 📦 Features

- Lightweight Flask API exposing Prometheus-compatible metrics
- Containerized with Docker
- Deployed on a local Kubernetes cluster using Minikube
- Auto-scraping Flask metrics with Prometheus
- Beautiful dashboards powered by Grafana

---

## 🛠️ Setup Instructions

### 1. 🧪 Run Flask App Locally (Optional)

> Test your app locally before containerizing.
```bash
python app.py
````

---

### 2. 🐳 Containerize Flask App

```bash
# Start Docker Engine
docker build -t flask-metrics-app:latest .
```

---

### 3. ☸️ Deploy on Minikube

```bash
# Start Minikube
minikube start

# Load Docker image into Minikube
minikube image load flask-metrics-app:latest

# Apply Kubernetes manifest
kubectl apply -f flask-app.yaml
```

✅ **Access the Flask App**:

```bash
minikube service flask-metrics-app --url
```

---

## 📈 Monitoring with Prometheus

### 4. 📥 Install Prometheus with Helm

```bash
# Install Helm (pick one)
winget install Helm.Helm
scoop install helm
choco install kubernetes-helm

# Add Prometheus repo
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

# Install Prometheus
helm install prometheus prometheus-community/prometheus --namespace monitoring --create-namespace
```

### 5. 🌐 Access Prometheus Dashboard

```bash
kubectl get svc -n monitoring

kubectl port-forward -n monitoring svc/prometheus-server 9090:80
```

Visit: [http://localhost:9090](http://localhost:9090)

---

## 🔗 Scraping Flask App Metrics

### 6. Add Flask App to Prometheus Targets

1. Get Flask Service IP:

```bash
kubectl get svc
```

Example:

```
flask-metrics-app   ClusterIP   10.108.215.0   5000/TCP
```

2. Edit Prometheus config:

```bash
kubectl edit configmap prometheus-server -n monitoring
```

3. Add under `scrape_configs`:

```yaml
- job_name: 'flask-app'
  static_configs:
    - targets: ['10.108.215.0:5000']
```

---

### 🔄 Restart Prometheus to Apply Changes

#### Option A: Delete Pod via Label

```bash
kubectl get pods -n monitoring --show-labels
kubectl delete pod -l app.kubernetes.io/name=prometheus -n monitoring
```

#### Option B: Restart Deployment

```bash
kubectl rollout restart deployment prometheus-server -n monitoring
```

✅ Recheck metrics at:

```bash
kubectl port-forward -n monitoring svc/prometheus-server 9090:80
```

In Prometheus UI, search for:

```
total_api_requests_total
```

---

## 📊 Grafana Setup

### 7. 📥 Install Grafana via Helm

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update

helm install grafana grafana/grafana -n monitoring --create-namespace
```

### 8. 🌐 Access Grafana Dashboard

```bash
kubectl get svc -n monitoring

kubectl port-forward svc/grafana -n monitoring 3000:80
```

Visit: [http://localhost:3000](http://localhost:3000)

---

### 🔐 Grafana Login

* **Username**: `admin`
* **Password**:

```bash
# Bash:
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode

# PowerShell:
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}"
# Then decode using:
[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String("<your_base64_password>"))
```

---

## 📡 Add Prometheus as a Data Source in Grafana

1. Go to:
   `Connections > Data Sources > Add Data Source > Prometheus`

2. Set URL:

```
http://prometheus-server.monitoring.svc.cluster.local:80
```

3. Click: **Save & Test**

---

## 📊 Create Beautiful Dashboards

1. Go to `Dashboard > New > New Panel`
2. Query:
   `total_api_requests_total` or `request_processing_latency_seconds`
3. Customize the graph
4. Save the panel or dashboard

---

## ✅ Technologies Used

| Tool       | Purpose                    |
| ---------- | -------------------------- |
| Flask      | Backend Web App            |
| Docker     | Containerization           |
| Minikube   | Local Kubernetes Cluster   |
| Kubernetes | Deployment + Service Mgmt  |
| Prometheus | Monitoring & Metrics       |
| Grafana    | Visualization & Dashboards |
| Helm       | Kubernetes Package Manager |

---

## 📬 Questions?

Feel free to reach out if you'd like to collaborate, ask questions, or hire me!

## 👨‍💻 Author

**Sk Mahiduzzaman**
📫 [Email](mailto:mohiduz03@gmail.com)
🔗 [LinkedIn](https://www.linkedin.com/in/sk-mahiduzzaman)

---

## 🌟 Want to Collaborate?

Feel free to fork, star ⭐ this repo, or open an issue! Let’s build something cloud-native together.

