# Kubernetes Bare-Metal Networking Stack 🚀

This repository documents the implementation of a production-grade networking architecture for **Bare-Metal Kubernetes** clusters. It solves the "External IP" challenge in on-premise environments without relying on public cloud providers.

![Kubernetes Bare-Metal Architecture](./docs/diagram.png)
## 🏗️ About the Project
This project demonstrates the implementation of a high-availability networking stack for **Bare-Metal Kubernetes** environments. In the absence of native cloud load balancers, this architecture leverages **MetalLB** and **NGINX Ingress Controller** to provide a production-grade entry point for containerized applications.



## 🛠️ Problem Solved
Traditional Bare-Metal clusters struggle with external traffic management, often relying on unstable **NodePorts** or manual IP assignments. This solution implements a **Virtual IP (VIP)** mechanism, allowing the cluster to dynamically assign and announce local network IPs, simulating a cloud provider experience (like AWS ELB) on physical hardware.

## 🧰 Technical Stack
* **Infrastructure:** KVM/Fedora (Multi-node Homelab: Goku, Gohan, Goten).
* **Load Balancing:** MetalLB (Layer 2 Mode).
* **Ingress Control:** NGINX Ingress Controller.
* **Security:** SSL/TLS Termination, Pod Security Admissions (PSA).
* **Automation:** Helm & Shell Scripting.

## 🚀 Key Features
* **L2 VIP Failover:** Automatic IP migration between nodes if a master or worker fails, ensuring zero-downtime for external traffic.
* **Edge Routing:** Single entry point (`198.51.100.0/24`) for multiple host-based services using Ingress rules.
* **SRE Focused:** Optimized with `--publish-service` for accurate status reporting and seamless external DNS integration.
* **DBA Optimized:** Designed to handle long-lived connections and persistent traffic required for database workloads.

---
## ⚙️ Installation & Configuration

### 1. MetalLB Setup (Layer 2)
Deploy MetalLB using Helm and apply the configuration directly from this repository.

```bash
# Add and update MetalLB Helm repository
helm repo add metallb [https://metallb.github.io/metallb](https://metallb.github.io/metallb)
helm repo update

# Install MetalLB in its own namespace
kubectl create ns metallb-system
helm install metallb metallb/metallb -n metallb-system

# Apply IP Pool and Advertisement from infrastructure folder
kubectl apply -f infrastructure/metallb/
```

Note: Ensure the Ingress Controller deployment is patched with --publish-service=ingress-nginx/ingress-nginx-lb.

## 📂 Repository Structure

```text
k8s-hybrid-networking-lab/
├── README.md              # Project overview, architecture, and full guide
├── docs/                  # Diagrams, screenshots, and documentation assets
├── infrastructure/        # Core cluster networking components
│   ├── metallb/           # IP pools, L2Advertisement, MetalLB configs
│   └── ingress-nginx/     # LoadBalancer service and controller patches
└── examples/              # Demo workloads for validation and testing
    └── web-demo/
        ├── deployment.yaml
        ├── service.yaml
        └── ingress.yaml
