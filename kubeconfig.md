# kubeconfig

This file contains sensitive cluster credentials and should not be committed to version control.

## Setup Instructions

1. Copy the template file:
   ```bash
   cp kubeconfig.template kubeconfig
   ```

2. Replace the placeholder values:
   - Replace `YOUR_SERVER_IP` with your actual k3s server IP address
   - Update certificate data if needed (the template uses placeholder certificates)

3. Alternative: Generate from running cluster:
   ```bash
   # If you have a running k3s cluster, copy the config:
   scp user@k3s-master:/etc/rancher/k3s/k3s.yaml ./kubeconfig
   
   # Then update the server IP in the copied file
   ```

## Security Notes

- The kubeconfig contains cluster certificates and credentials
- Never commit this file to version control
- Keep it secure and only share with trusted team members
- Consider using file permissions: `chmod 600 kubeconfig`

## Git Configuration

The real kubeconfig file is excluded from git via .gitignore to prevent accidental commits of sensitive credentials.