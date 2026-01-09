# 🚀 SynergyChat - Cloud Native Microservices Platform

> A production-ready Kubernetes-based microservices application featuring API Gateway, Rate Limiting, HPA, and comprehensive Observability stack.

[![Kubernetes](https://img.shields.io/badge/kubernetes-1.28+-326CE5?style=flat&logo=kubernetes&logoColor=white)](https://kubernetes.io/)
[![Envoy Gateway](https://img.shields.io/badge/envoy-gateway-AC6199?style=flat&logo=envoyproxy&logoColor=white)](https://gateway.envoyproxy.io/)
[![Grafana](https://img.shields.io/badge/grafana-observability-F46800?style=flat&logo=grafana&logoColor=white)](https://grafana.com/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Architecture](#-architecture)
- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Getting Started](#-getting-started)
- [Project Structure](#-project-structure)
- [Monitoring](#-monitoring)
- [API Documentation](#-api-documentation)
- [What I Learned](#-what-i-learned)
- [Future Improvements](#-future-improvements)
- [Contributing](#-contributing)

---

## 🎯 Overview

SynergyChat is a cloud-native microservices platform demonstrating modern DevOps practices and infrastructure patterns. The project showcases:

- **API Gateway Pattern** with Envoy Gateway for centralized traffic management
- **Rate Limiting** to prevent abuse and ensure fair usage
- **Horizontal Pod Autoscaling (HPA)** for dynamic resource management
- **Comprehensive Observability** with Grafana, Prometheus, and Loki
- **Infrastructure as Code** with Kubernetes manifests and GitOps principles

This is a **learning project** built to demonstrate practical Kubernetes skills and cloud-native architecture patterns.

---

## 🏗️ Architecture

```
                                 ┌─────────────────┐
                                 │   Internet      │
                                 └────────┬────────┘
                                          │
                                          ▼
                              ┌───────────────────────┐
                              │   Envoy Gateway        │
                              │  - Rate Limiting       │
                              │  - Load Balancing      │
                              │  - TLS Termination     │
                              └──────────┬────────────┘
                                         │
                 ┌───────────────────────┼───────────────────────┐
                 │                       │                       │
                 ▼                       ▼                       ▼
         ┌───────────────┐      ┌───────────────┐      ┌───────────────┐
         │  Web Service  │      │  API Service  │      │ Crawler Svc   │
         │  (Frontend)   │      │  (Backend)    │      │ (Background)  │
         │  - HPA        │      │  - PVC        │      │  - HPA        │
         │  - ConfigMap  │      │  - ConfigMap  │      │  - ConfigMap  │
         └───────────────┘      └───────────────┘      └───────────────┘
                 │                       │                       │
                 └───────────────────────┼───────────────────────┘
                                         │
                                         ▼
                              ┌───────────────────────┐
                              │  Monitoring Stack      │
                              │  - Prometheus          │
                              │  - Loki                │
                              │  - Grafana             │
                              └───────────────────────┘
```

### Components:

- **Web Service**: Frontend application serving user interface
- **API Service**: Backend REST API handling business logic
- **Crawler Service**: Background job processor for data collection
- **Envoy Gateway**: API Gateway with built-in rate limiting (30 req/s)
- **Monitoring Stack**: Full observability with metrics, logs, and alerts

---

## ✨ Features

### 🔐 Traffic Management
- ✅ API Gateway with Envoy Gateway
- ✅ Rate Limiting (30 requests/second per client)
- ✅ HTTP/HTTPS routing with HTTPRoute CRDs
- ✅ Custom traffic policies

### 📊 Observability & Monitoring
- ✅ Prometheus for metrics collection
- ✅ Loki for log aggregation
- ✅ Grafana dashboards for visualization
- ✅ Alert rules for critical events:
  - Rate limit breaches (429 errors)
  - Server errors (5xx responses)
  - Pod health issues

### 🔄 Auto-Scaling & Resource Management
- ✅ Horizontal Pod Autoscaler (HPA) for web and crawler
- ✅ CPU/Memory-based scaling
- ✅ ConfigMaps for application configuration
- ✅ PersistentVolumeClaims for stateful data

### 🛠️ Infrastructure as Code
- ✅ Declarative Kubernetes manifests
- ✅ GitOps-ready configuration
- ✅ Version-controlled monitoring configs
- ✅ Documented architecture and setup

---

## 🛠️ Tech Stack

| Category | Technologies |
|----------|-------------|
| **Orchestration** | Kubernetes 1.28+ |
| **API Gateway** | Envoy Gateway |
| **Metrics** | Prometheus |
| **Logging** | Loki, Promtail |
| **Visualization** | Grafana |
| **Container Runtime** | Docker |
| **IaC** | Kubernetes YAML manifests |
| **Version Control** | Git, GitHub |

---

## 🚀 Getting Started

### Prerequisites

```bash
# Required tools
- kubectl (v1.28+)
- docker
- minikube or kind (for local development)
```

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/oghuzkhandev/kubernetes.git
   cd kubernetes
   ```

2. **Deploy infrastructure (Gateway)**
   ```bash
   kubectl apply -f infrastructure/gateway/
   ```

3. **Deploy applications**
   ```bash
   # API Service
   kubectl apply -f apps/api/

   # Web Service
   kubectl apply -f apps/web/

   # Crawler Service
   kubectl apply -f apps/crawler/
   ```

4. **Deploy monitoring stack**
   ```bash
   # Assuming you have Prometheus, Loki, and Grafana installed
   kubectl apply -f monitoring/alerts/
   ```

5. **Access the application**
   ```bash
   # Port-forward to access locally (no domain)
   kubectl port-forward svc/envoy-gateway -n envoy-gateway-system 8080:80
   
   # Access Grafana
   kubectl port-forward -n monitoring svc/grafana 3000:3000
   ```

### Quick Test

```bash
# Test rate limiting (send 30+ req/s)
for i in {1..50}; do
  curl -w "\n" http://localhost:8080/api/test
  sleep 0.03
done

# You should see HTTP 429 responses after hitting the limit
```

---

## 📂 Project Structure

```
.
├── README.md                           # This file
├── apps/                               # Application services
│   ├── api/                           # Backend API service
│   │   ├── deployment.yaml           # API deployment
│   │   ├── service.yaml              # API service
│   │   ├── configmap.yaml            # API configuration
│   │   ├── pvc.yaml                  # Persistent storage
│   │   └── httproute.yaml            # API routing
│   ├── web/                          # Frontend web service
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   ├── configmap.yaml
│   │   ├── hpa.yaml                  # Horizontal Pod Autoscaler
│   │   ├── httproute.yaml
│   │   └── trafficpolicy/
│   │       └── ratelimit.yaml        # Rate limiting config
│   └── crawler/                      # Background job service
│       ├── deployment.yaml
│       ├── service.yaml
│       ├── configmap.yaml
│       └── hpa.yaml
├── infrastructure/                    # Infrastructure resources
│   └── gateway/
│       ├── app-gateway.yaml          # Gateway configuration
│       └── app-gatewayclass.yaml     # GatewayClass definition
├── monitoring/                        # Observability configs
│   ├── README.md                     # Monitoring documentation
│   ├── dashboards/
│   │   └── production-overview.json  # Grafana dashboard
│   └── alerts/
│       └── loki-alerts.json          # Alert rules
├── base/                             # Base configurations
└── overlays/                         # Environment overlays
```

---

## 📊 Monitoring

### Dashboards

The project includes a comprehensive Grafana dashboard featuring:

- **HTTP 429 Rate Limit Errors**: Real-time tracking of rate limit breaches
- **HTTP 5xx Server Errors**: Server error monitoring
- **Pod Health Metrics**: CPU, Memory, Restart count
- **HPA Status**: Autoscaling activity
- **Request Rate**: Requests per minute

**Import:** `monitoring/dashboards/production-overview.json`

### Alert Rules

Configured alerts in `monitoring/alerts/loki-alerts.json`:

| Alert | Condition | Severity | Description |
|-------|-----------|----------|-------------|
| **Too Many Requests (429)** | Count > 0 for 1m | Warning | Rate limit exceeded |
| **HTTP 5xx Errors** | Count > 0 for 1m | Critical | Server errors detected |

**Note:** Alerts use Loki for log-based monitoring. Logs are collected from the `default` namespace where Envoy proxy data plane runs.

### Accessing Grafana

```bash
kubectl port-forward -n monitoring svc/grafana 3000:3000
# Open http://localhost:3000
# Default credentials: admin/admin
```

---

## 📖 API Documentation

### Rate Limiting

All API endpoints are protected by rate limiting:

- **Limit**: 30 requests per second per client
- **Response**: HTTP 429 when limit exceeded
- **Headers**: `X-RateLimit-*` headers included in responses

### Example Endpoints

```bash
# Web frontend
GET http://gateway/

# API endpoints
GET http://gateway/api/test
GET http://gateway/api/health
```

---

## 🎓 What I Learned

Building this project taught me:

### Kubernetes Concepts
- ✅ **API Gateway pattern** with Envoy Gateway
- ✅ **Rate limiting** implementation and tuning
- ✅ **Horizontal Pod Autoscaling** based on CPU/Memory metrics
- ✅ **ConfigMaps** and **PersistentVolumeClaims** for stateful apps
- ✅ **HTTPRoute CRDs** for modern routing

### Observability
- ✅ **Log aggregation** with Loki and Promtail
- ✅ **Metrics collection** with Prometheus
- ✅ **Dashboard creation** in Grafana
- ✅ **Alert rule configuration** for critical events
- ✅ **LogQL** query language for log analysis

### DevOps Practices
- ✅ **Infrastructure as Code** (IaC) with Kubernetes manifests
- ✅ **GitOps principles** - configuration as code
- ✅ **Version control** for infrastructure
- ✅ **Documentation** best practices

### Technical Skills
- ✅ **Envoy proxy configuration** and troubleshooting
- ✅ **Kubernetes debugging** (logs, describe, events)
- ✅ **Namespace isolation** (control plane vs data plane)
- ✅ **JSON/YAML** manifest management

---

## 🤝 Contributing

This is a learning project, but feedback and suggestions are welcome!

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👤 Author

**Oğuzhan Doğan**

- LinkedIn: [Your LinkedIn](https://linkedin.com/in/your-profile](https://www.linkedin.com/in/oguzhandogandev/))
- Email: oguzhandogandev@hotmail.com
- Website: [Portfolio](https://oguzhandogan.com)

---

## 🙏 Acknowledgments

- Envoy Gateway team for excellent documentation
- Grafana Labs for the observability stack
- Kubernetes community for extensive resources
- All the open-source contributors

---

<div align="center">

**⭐ Star this repo if you find it helpful!**

Made with ❤️ by [oghuzkhandev](https://github.com/oghuzkhandev)

</div>
