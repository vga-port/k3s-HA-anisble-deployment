# K3s High Availability Ansible Deployment

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Ansible](https://img.shields.io/badge/ansible-2.9+-blue.svg)](https://docs.ansible.com/)
[![K3s](https://img.shields.io/badge/k3s-v1.29.1+-green.svg)](https://k3s.io/)

Production-ready K3s Kubernetes cluster deployment with Ansible automation. This project provides a complete, battle-tested solution for deploying high-availability K3s clusters with comprehensive monitoring, storage, and management tools built-in.

> **🚨 IMPORTANT**: This repository uses placeholders (starting with `YOUR_`) that must be configured. See the **"Configuration Required"** section below for detailed setup instructions.

## 🚀 Quick Setup Overview

| Step | What to Do | File | Key Placeholders to Replace |
|------|-------------|------|----------------------------|
| 1️⃣ | Configure kubeconfig | `kubeconfig` | `YOUR_SERVER_IP` |
| 2️⃣ | Set server IPs | `inventory/sample/hosts.ini` | `YOUR_MASTER_IP`, `YOUR_WORKER_IP`, `YOUR_SSH_USER` |
| 3️⃣ | Configure network | `inventory/sample/group_vars/all.yml` | `YOUR_VIP_IP`, `YOUR_LB_IP_*` |
| 4️⃣ | Set passwords | `inventory/sample/group_vars/all.yml` | `YOUR_*_PASSWORD`, `YOUR_EMAIL@example.com` |
| 5️⃣ | Generate token | `inventory/sample/group_vars/all.yml` | `YOUR_SECURE_TOKEN_HERE` |

👉 **See "Configuration Required" section below for detailed instructions and examples**

## ✨ Features

- 🚀 **Automated Deployment** - One-command setup with comprehensive validation
- 🎯 **High Availability** - Multi-server control plane with external database support
- 📊 **Complete Monitoring** - Prometheus + Grafana stack with persistent storage
- 🧰 **Load Balancing** - MetalLB for LoadBalancer service type support
- 💾 **Distributed Storage** - Longhorn block storage with configurable replication
- 🔐 **Production Ready** - Security best practices, TLS, RBAC, and network policies
- 🤠 **Management Interface** - Rancher cluster management platform
- 🛡️ **Certificate Management** - cert-manager for automated TLS certificates
- 📝 **Beginner Friendly** - Simple configuration and deployment process
- 🧪 **Comprehensive Testing** - Built-in validation and health checks
- 🔄 **Easy Scaling** - Add nodes with minimal configuration
- 📚 **Professional Documentation** - Complete guides and examples

## 🚀 Quick Start

### 1. Clone Repository
\`\`\`bash
git clone https://github.com/yourusername/k3s-ha-ansible.git
cd k3s-ha-ansible
\`\`\`

### 2. Configure Your Cluster
\`\`\`bash
# Copy kubeconfig template and configure
cp kubeconfig.template kubeconfig
vim kubeconfig  # Replace YOUR_SERVER_IP with your k3s API server IP

# Edit inventory with your server IPs and SSH user
vim inventory/sample/hosts.ini  # Replace YOUR_*_IP and YOUR_SSH_USER

# Edit main configuration with your network and security settings
vim inventory/sample/group_vars/all.yml  # Replace all YOUR_* placeholders

# See the "Configuration Required" section below for detailed placeholder guide
\`\`\`

### 3. Deploy Cluster
\`\`\`bash
# IMPORTANT: Make sure all YOUR_* placeholders are replaced before deploying!
# See "Configuration Required" section above for complete guide.

# Deploy everything with one command
./deploy.sh
\`\`\`

### 4. Verify Deployment
\`\`\`bash
# Check cluster health and get access information
./verify-cluster.sh
\`\`\`

## ⚙️ Configuration Required - Placeholders Guide

This repository uses placeholders (values starting with `YOUR_`) that must be replaced with your actual configuration. Below is a complete guide to what you need to configure and where.

### 📋 **Configuration Files Overview**

| File | Purpose | Key Placeholders |
|------|---------|------------------|
| `inventory/sample/hosts.ini` | Server IPs and SSH access | `YOUR_MASTER_IP`, `YOUR_WORKER_IP`, `YOUR_SSH_USER` |
| `inventory/sample/group_vars/all.yml` | Main cluster configuration | Network, passwords, tokens, services |
| `kubeconfig.template` → `kubeconfig` | Kubernetes access | `YOUR_SERVER_IP` |

### 🔧 **Step 1: Network Configuration**

#### Server IPs (`inventory/sample/hosts.ini`)
```ini
[master]
YOUR_MASTER_IP ansible_user=YOUR_SSH_USER ansible_become=true

[node] 
YOUR_WORKER_IP ansible_user=YOUR_SSH_USER ansible_become=true
```

**What to fill in:**
- `YOUR_MASTER_IP`: Your k3s master/control plane server IP (e.g., `192.168.1.10`)
- `YOUR_WORKER_IP`: Your k3s worker node server IP (e.g., `192.168.1.20`)
- `YOUR_SSH_USER`: SSH username for accessing servers (e.g., `ubuntu`, `admin`, `root`)

#### Virtual IP & Load Balancer (`inventory/sample/group_vars/all.yml`)
```yaml
# API Server Virtual IP (must be unused in your network)
apiserver_endpoint: YOUR_VIP_IP  # e.g., 192.168.1.100

# MetalLB LoadBalancer IP range (must not conflict with your network)
metal_lb_ip_range: YOUR_LB_IP_START-YOUR_LB_IP_END  # e.g., 192.168.1.150-192.168.1.199
```

**What to fill in:**
- `YOUR_VIP_IP`: Virtual IP for k3s API server (single unused IP)
- `YOUR_LB_IP_START-YOUR_LB_IP_END`: Range for LoadBalancer services

#### BGP Configuration (if using BGP)
```yaml
cilium_bgp_peer_address: YOUR_BGP_PEER_IP     # e.g., 192.168.1.1
cilium_bgp_lb_cidr: YOUR_LB_CIDR              # e.g., 192.168.100.0/24
kube_vip_bgp_peeraddress: "YOUR_BGP_PEER_IP"  # e.g., "192.168.1.1"
```

### 🔐 **Step 2: Security Configuration**

#### Cluster Authentication (`inventory/sample/group_vars/all.yml`)
```yaml
# Generate secure token: openssl rand -hex 16
k3s_token: YOUR_SECURE_TOKEN_HERE
```

**What to fill in:**
- `YOUR_SECURE_TOKEN_HERE`: Secure token for k3s cluster authentication
- **Generate with:** `openssl rand -hex 16` or use a secure random generator

#### Service Passwords
```yaml
# Rancher Management Platform
rancher_bootstrap_password: "YOUR_RANCHER_PASSWORD"

# Grafana Visualization  
grafana_admin_password: "YOUR_GRAFANA_PASSWORD"

# Certificate Manager
cert_manager_default_issuer_email: "YOUR_EMAIL@example.com"
```

**What to fill in:**
- `YOUR_RANCHER_PASSWORD`: Strong password for Rancher admin
- `YOUR_GRAFANA_PASSWORD`: Strong password for Grafana admin
- `YOUR_EMAIL@example.com`: Your email for Let's Encrypt certificates

### 🌐 **Step 3: Optional Network Configuration**

#### Proxy Settings (if you use a proxy)
```yaml
proxy_env:
  HTTP_PROXY: "http://YOUR_PROXY:3128"
  HTTPS_PROXY: "http://YOUR_PROXY:3128" 
  NO_PROXY: "*.YOUR_DOMAIN,127.0.0.0/8,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16"
```

#### Private Registry (if you use one)
```yaml
custom_registries_yaml: |
  mirrors:
    docker.io:
      endpoint:
        - "https://YOUR_REGISTRY_DOMAIN/v2/dockerhub"
    # ... other registry configs
  configs:
    "YOUR_REGISTRY_DOMAIN":
      auth:
        username: YOUR_REGISTRY_USER
        password: YOUR_REGISTRY_PASSWORD
```

### 🔑 **Step 4: Kubernetes Access**

#### Setup kubeconfig
```bash
# Copy template and configure
cp kubeconfig.template kubeconfig

# Edit and replace YOUR_SERVER_IP with your VIP or first master IP
vim kubeconfig
```

**What to fill in:**
- `YOUR_SERVER_IP`: Your k3s API server IP (same as `apiserver_endpoint`)

### 📝 **Quick Configuration Script**

You can use this script to quickly replace common placeholders:

```bash
#!/bin/bash
# quick-setup.sh - Replace common placeholders

# Network Configuration
sed -i "s/YOUR_MASTER_IP/192.168.1.10/g" inventory/sample/hosts.ini
sed -i "s/YOUR_WORKER_IP/192.168.1.20/g" inventory/sample/hosts.ini  
sed -i "s/YOUR_SSH_USER/ubuntu/g" inventory/sample/hosts.ini

# Main Configuration
sed -i "s/YOUR_VIP_IP/192.168.1.100/g" inventory/sample/group_vars/all.yml
sed -i "s/YOUR_LB_IP_START-YOUR_LB_IP_END/192.168.1.150-192.168.1.199/g" inventory/sample/group_vars/all.yml

# Generate secure token
K3S_TOKEN=$(openssl rand -hex 16)
sed -i "s/YOUR_SECURE_TOKEN_HERE/$K3S_TOKEN/g" inventory/sample/group_vars/all.yml

# Service passwords (change these!)
sed -i "s/YOUR_RANCHER_PASSWORD/ChangeMeNow123/g" inventory/sample/group_vars/all.yml
sed -i "s/YOUR_GRAFANA_PASSWORD/ChangeMeNow456/g" inventory/sample/group_vars/all.yml

# Email
sed -i "s/YOUR_EMAIL@example.com/your.email@domain.com/g" inventory/sample/group_vars/all.yml

# kubeconfig
sed -i "s/YOUR_SERVER_IP/192.168.1.100/g" kubeconfig

echo "Configuration complete! Review the files and change passwords before deploying."
```

### ⚠️ **Important Security Notes**

1. **Change Default Passwords**: Never use the example passwords in production
2. **Secure Token**: Generate a unique, random token for each deployment  
3. **Network Planning**: Ensure IP ranges don't conflict with existing infrastructure
4. **Firewall Rules**: Configure firewall to allow necessary ports
5. **SSH Keys**: Use SSH key authentication instead of passwords where possible

### ✅ **Verification Checklist**

Before deploying, verify you've replaced:
- [ ] All `YOUR_*_IP` placeholders with actual IP addresses
- [ ] `YOUR_SSH_USER` with your SSH username  
- [ ] `YOUR_SECURE_TOKEN_HERE` with a generated token
- [ ] `YOUR_*_PASSWORD` with strong passwords
- [ ] `YOUR_EMAIL@example.com` with your email
- [ ] `YOUR_SERVER_IP` in kubeconfig
- [ ] Optional: BGP, proxy, and registry settings if used

## 🏗️ Architecture

\`\`\`
┌─────────────────────────────────────────────────────┐
│                    🌐 Internet Access                     │
│                                                     │
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐ │
│  │    Grafana  │    │   Longhorn  │    │   Rancher   │ │
│  │  Prometheus  │    │   Storage   │    │ Management  │ │
│  │ (Monitoring)│    │ (Distributed)│    │    (UI)     │ │
│  └─────────────┘    └─────────────┘    └─────────────┘ │
│                                                     │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │              🌐 K3s Kubernetes Cluster                │ │
│  │  ┌─────────────┐    ┌─────────────┐                   │ │
│  │  │ K3s Server  │    │ K3s Agent   │                   │ │
│  │  │ Control     │    │  Worker      │                   │ │
│  │  │ Plane       │    │ Node        │                   │ │
│  │  └─────────────┘    └─────────────┘                   │ │
│  │                                                     │
│  │  ┌─────────────────────────────────────────────────────────────┐ │
│  │  │           🌐 MetalLB Load Balancer              │ │
│  │  │  IP Pool: YOUR_LB_IP_START-YOUR_LB_IP_END │ │
│  │  │  L2 Advertisement for LoadBalancer services   │ │
│  │  └─────────────────────────────────────────────────────────────┘ │
│  └─────────────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────┘
\`\`\`

## 📋 Requirements

| Requirement | Minimum | Recommended |
|-------------|----------|-------------|
| Ansible Controller | 2.9+ | 2.16+ |
| Target Nodes | Linux | Ubuntu 20.04+ |
| CPU | 2 cores | 4+ cores |
| RAM | 4GB | 8GB+ |
| Storage | 20GB | 50GB+ SSD |
| Network | Open ports 6443, 8472, 10250, 2379-2380 | Gigabit connectivity |

## 🎯 Configuration Options

### Core K3s Settings
\`\`\`yaml
# K3s Configuration
k3s_version: "v1.29.1+k3s1"
k3s_ha_enabled: true
k3s_cluster_domain: "cluster.local"
k3s_cluster_cidr: "10.42.0.0/16"
k3s_service_cidr: "10.43.0.0/16"

# High Availability Database
k3s_datastore_endpoint: "postgresql://user:pass@db-host:5432/k3s"

# TLS Configuration
k3s_tls_san:
  - "k3s.local"
  - "YOUR_CONTROLLER_IP_1"  # Controller IP 1
  - "YOUR_CONTROLLER_IP_2"  # Controller IP 2  
  - "YOUR_CONTROLLER_IP_3"  # Controller IP 3
  - "YOUR_VIP_ADDRESS"       # VIP Address

# Virtual IP Configuration
vip_enabled: true
vip_address: "YOUR_VIP_ADDRESS"
vip_interface: "eth0"
\`\`\`

### Platform Services
\`\`\`yaml
# MetalLB Load Balancer
metallb_enabled: true
metallb_ip_range: "YOUR_LB_IP_START-YOUR_LB_IP_END"
metallb_protocol: "layer2"

# Longhorn Storage
longhorn_enabled: true
longhorn_replica_count: 3
longhorn_backup_target: "s3://backup-bucket/longhorn"
longhorn_storage_class: "longhorn"

# Monitoring Stack
prometheus_enabled: true
prometheus_storage: "50Gi"
prometheus_retention: "30d"

grafana_enabled: true
grafana_service_type: "LoadBalancer"
grafana_admin_password: "secure-password"

# Certificate Management
cert_manager_enabled: true
cert_manager_version: "v1.13.3"

# Rancher Management
rancher_enabled: true
rancher_hostname: "k3s.example.com"
rancher_bootstrap_password: "rancher123"
rancher_replicas: 2

# CLI Tools
k9s_enabled: true
k9s_version: "v0.27.4"
\`\`\`

## 📊 Monitoring & Management

| Component | Purpose | Access | Configuration |
|-----------|---------|--------|-------------|
| **Grafana** | Metrics visualization | http://YOUR_VIP_ADDRESS:3000 | admin/YOUR_GRAFANA_PASSWORD |
| **Prometheus** | Metrics collection | Internal service | 30-day retention |
| **AlertManager** | Alert management | Integrated | High availability |
| **Longhorn** | Distributed storage | Web UI | 3-way replication |
| **Rancher** | Cluster management | https://YOUR_RANCHER_DOMAIN | HA deployment |
| **k9s** | Terminal CLI | \`k9s\` command | Interactive interface |
| **MetalLB** | Load balancing | Auto IP assignment | L2 advertisement |

## 🔧 Deployment Methods

### Method 1: Interactive Deployment (Beginner Friendly)
\`\`\`bash
# Deploy with guided menu
./deploy.sh
# Interactive menu guides you through setup
# All components pre-configured with sensible defaults
# Deployment completes in 10-15 minutes
\`\`\`

### Method 2: Direct Ansible (Traditional)
\`\`\`bash
# Full Ansible playbook execution
ansible-playbook -i inventory.ini site.yml -v
# Complete access to all Ansible features
# Custom playbooks and roles supported
\`\`\`

## 🛠️ Troubleshooting

### Common Issues

#### 1. Deployment Failures
\`\`\`bash
# Check prerequisites
ansible -i inventory.ini all -m ping

# Validate syntax
ansible-playbook -i inventory.ini site.yml --syntax-check

# Check logs
journalctl -u k3s -f
\`\`\`

#### 2. Network Issues
\`\`\`bash
# Check MetalLB status
kubectl get pods -n metallb-system
kubectl get addresspools -n metallb-system

# Check VIP status
ping YOUR_VIP_ADDRESS
\`\`\`

#### 3. Storage Issues
\`\`\`bash
# Check Longhorn status
kubectl get pods -n longhorn-system
kubectl get storageclass longhorn
kubectl get volumes
\`\`\`

### Debug Commands
\`\`\`bash
# System level debugging
systemctl status k3s
journalctl -u k3s -f

# Kubernetes level debugging
kubectl get nodes -o wide
kubectl get events --all-namespaces
kubectl describe pod <pod-name> -n <namespace>
kubectl logs <pod-name> -n <namespace> --follow
\`\`\`

## 🔄 Scalability

### Adding Controller Nodes
\`\`\`ini
# Add to inventory.ini
[controller3]
controller3 ansible_host=YOUR_NEW_CONTROLLER_IP ansible_user=YOUR_SSH_USER

# Update k3s_tls_san in group_vars/all.yml
k3s_tls_san:
  - "YOUR_NEW_CONTROLLER_IP"  # New controller IP
\`\`\`

### Adding Worker Nodes
\`\`\`ini
# Add to inventory.ini
[worker3]
worker3 ansible_host=YOUR_NEW_WORKER_IP ansible_user=YOUR_SSH_USER
\`\`\`

### Scaling Services
\`\`\`yaml
# Scale Longhorn replication
longhorn_replica_count: 5

# Expand MetalLB range
metallb_ip_range: "YOUR_LB_IP_START-YOUR_LB_IP_END"

# Increase monitoring storage
prometheus_storage: "100Gi"
\`\`\`

## 🔐 Security Best Practices

### Network Security
\`\`\`bash
# Required firewall rules
sudo ufw allow 6443/tcp    # Kubernetes API
sudo ufw allow 8472/udp    # Flannel VXLAN
sudo ufw allow 10250/tcp    # Kubelet API
sudo ufw allow 2379:2380/tcp # etcd
sudo ufw allow 2380/udp    # etcd
\`\`\`

### SSH Security
\`\`\`bash
# Use SSH keys instead of passwords
ssh-keygen -t ed25519 -C "k3s-deploy"
ssh-copy-id user@server-ip

# Ansible SSH configuration
ansible_ssh_common_args='-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null'
\`\`\`

## 📚 Documentation

- **[INSTALL.md](INSTALL.md)** - Step-by-step installation instructions
- **[CONFIGURATION.md](CONFIGURATION.md)** - Complete configuration reference
- **[TROUBLESHOOTING.md](TROUBLESHOOTING.md)** - Common issues and solutions
- **[SCALING.md](SCALING.md)** - Cluster scaling guide
- **[SECURITY.md](SECURITY.md)** - Security best practices
- **[CONTRIBUTING.md](CONTRIBUTING.md)** - Development guidelines

## 🧪 Testing & Validation

### Automated Testing
\`\`\`bash
# Syntax validation
ansible-playbook -i inventory.ini site.yml --syntax-check

# Pre-deployment validation
./deploy.sh --check

# Post-deployment verification
./verify-cluster.sh
\`\`\`

## 📄 License

This project is licensed under the MIT License - see [LICENSE](LICENSE) file for details.

## 🤝 Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### Contribution Areas
- 🐛 **Bug fixes** and error handling improvements
- ✨ **New features** and component integrations
- 📚 **Documentation** and guide improvements
- 🧪 **Test cases** and validation scripts
- 🔧 **Configuration options** and templates
- 🛡️ **Security** enhancements and best practices

### Development Workflow
\`\`\`bash
# 1. Fork the repository
git clone https://github.com/yourusername/k3s-ha-ansible.git

# 2. Create a feature branch
git checkout -b feature/amazing-feature

# 3. Make your changes
# Test with multiple configurations
# Ensure all tests pass

# 4. Submit pull request
git push origin feature/amazing-feature
# Create PR with clear description
\`\`\`

## 🙏 Acknowledgments

This project builds upon the excellent work of:

- [K3s](https://k3s.io/) - Lightweight Kubernetes distribution
- [Ansible](https://www.ansible.com/) - Configuration management
- [MetalLB](https://metallb.universe.tf/) - LoadBalancer implementation
- [Longhorn](https://longhorn.io/) - Distributed block storage
- [Prometheus](https://prometheus.io/) - Monitoring and alerting
- [Grafana](https://grafana.com/) - Analytics and visualization
- [cert-manager](https://cert-manager.io/) - Certificate automation
- [Rancher](https://rancher.com/) - Kubernetes management platform
- [k9s](https://github.com/derailed/k9s) - Terminal Kubernetes manager

## 📞 Support

- 📋 [Issues](https://github.com/yourusername/k3s-ha-ansible/issues) - Bug reports and feature requests
- 💬 [Discussions](https://github.com/yourusername/k3s-ha-ansible/discussions) - Questions and ideas
- 📖 [Wiki](https://github.com/yourusername/k3s-ha-ansible/wiki) - Documentation and guides
- 📊 [Releases](https://github.com/yourusername/k3s-ha-ansible/releases) - Version history and changelogs

## 🚀 Quick Summary

| Feature | Status | Description |
|---------|--------|-------------|
| **K3s Deployment** | ✅ Production Ready | HA cluster with external DB support |
| **MetalLB** | ✅ Production Ready | LoadBalancer with configurable IP ranges |
| **Longhorn** | ✅ Production Ready | Distributed storage with replication |
| **Monitoring** | ✅ Production Ready | Prometheus + Grafana with AlertManager |
| **cert-manager** | ✅ Production Ready | Automated TLS certificate management |
| **Rancher** | ✅ Production Ready | Cluster management with HA deployment |
| **k9s** | ✅ Production Ready | Terminal-based cluster management |
| **Security** | ✅ Production Ready | RBAC, policies, and best practices |
| **Documentation** | ✅ Complete | Comprehensive guides and examples |
| **Testing** | ✅ Complete | Built-in validation and troubleshooting |

**🚀 Production-ready K3s cluster with enterprise features and beginner-friendly deployment!** 🚀

---

⭐ **If this project helps you, please give it a star and share it with your team!** ⭐
