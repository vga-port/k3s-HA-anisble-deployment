# Personal Information Redaction

This document outlines all personal information that has been redacted from this Ansible repository to make it safe for public distribution.

## What Was Redacted

### Network Configuration
- `apiserver_endpoint`: Changed from specific IP to `YOUR_VIP_IP`
- `metal_lb_ip_range`: Changed to `YOUR_LB_IP_START-YOUR_LB_IP_END`
- `cilium_bgp_peer_address`: Changed to `YOUR_BGP_PEER_IP`
- `cilium_bgp_lb_cidr`: Changed to `YOUR_LB_CIDR`
- `kube_vip_bgp_peeraddress`: Changed to `YOUR_BGP_PEER_IP`

### Authentication & Security
- `k3s_token`: Changed to `YOUR_SECURE_TOKEN_HERE`
- `rancher_bootstrap_password`: Changed to `YOUR_RANCHER_PASSWORD`
- `grafana_admin_password`: Changed to `YOUR_GRAFANA_PASSWORD`
- `cert_manager_default_issuer_email`: Changed to `YOUR_EMAIL@example.com`

### SSH & User Configuration
- `ansible_user`: Changed to `YOUR_SSH_USER` in inventory files
- Host IPs: Changed to `YOUR_MASTER_IP` and `YOUR_WORKER_IP`
- SSH user references in inventory: Changed from specific usernames to placeholder

### Registry Configuration
- Registry domain references: Changed to `YOUR_REGISTRY_DOMAIN`
- Registry credentials: Changed to `YOUR_REGISTRY_USER` and `YOUR_REGISTRY_PASSWORD`

### Proxy Configuration
- Proxy URLs: Changed to `YOUR_PROXY`
- Proxy domains: Changed to `YOUR_DOMAIN`

## What You Need to Configure

Before running this Ansible playbook, you must update the following files with your actual values:

### 1. Main Configuration (`inventory/sample/group_vars/all.yml`)
```yaml
# Replace all YOUR_* placeholders with actual values
apiserver_endpoint: YOUR_VIP_IP  # Your virtual IP for k3s API server
k3s_token: YOUR_SECURE_TOKEN_HERE  # Generate a secure token
metal_lb_ip_range: YOUR_LB_IP_START-YOUR_LB_IP_END  # Your LoadBalancer IP range
rancher_bootstrap_password: YOUR_RANCHER_PASSWORD  # Set strong password
grafana_admin_password: YOUR_GRAFANA_PASSWORD  # Set strong password
cert_manager_default_issuer_email: YOUR_EMAIL@example.com  # Your email
```

### 2. Inventory Configuration (`inventory/sample/hosts.ini`)
```ini
[master]
YOUR_MASTER_IP ansible_user=YOUR_SSH_USER ansible_become=true

[node]
YOUR_WORKER_IP ansible_user=YOUR_SSH_USER ansible_become=true
```

### 3. Network Configuration
Update all network-related placeholders:
- BGP peer addresses
- LoadBalancer CIDR ranges  
- Proxy settings (if used)
- Private registry domains (if used)

## Security Best Practices

1. **Generate strong passwords** for all services
2. **Use secure tokens** for k3s cluster authentication
3. **Update default usernames** to match your environment
4. **Configure proper network ranges** that don't conflict with your existing infrastructure
5. **Keep sensitive values out of version control** - consider using Ansible Vault for production deployments

## Files Modified

- `inventory/sample/group_vars/all.yml` - Main configuration with network and security settings
- `inventory/sample/hosts.ini` - Server inventory with SSH configuration
- `roles/grafana/defaults/main.yml` - Grafana password
- `roles/rancher/defaults/main.yml` - Rancher bootstrap password  
- `roles/cert_manager/defaults/main.yml` - Email for certificates
- `kubeconfig.template` - Kubernetes configuration template
- `.gitignore` - Already excludes sensitive files

## Automated Setup Script (Optional)

You can create a script to automatically replace placeholders:

```bash
#!/bin/bash
# setup.sh - Replace configuration placeholders

sed -i "s/YOUR_VIP_IP/192.168.1.222/g" inventory/sample/group_vars/all.yml
sed -i "s/YOUR_MASTER_IP/192.168.1.10/g" inventory/sample/hosts.ini
sed -i "s/YOUR_WORKER_IP/192.168.1.11/g" inventory/sample/hosts.ini
# Add more sed commands as needed
```

Remember to never commit your actual configuration values to version control!