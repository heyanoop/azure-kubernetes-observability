# Azure Kubernetes Service (AKS) Terraform Repository

This repository contains Terraform configurations to provision an Azure Kubernetes Service (AKS) cluster along with the necessary networking components.

## Overview

This Terraform project deploys:
- A resource group in the East US region
- A virtual network with a dedicated subnet
- An AKS cluster with autoscaling enabled
- Kubernetes resources including namespaces, role bindings, service accounts, and secrets

## Prerequisites

- [Terraform](https://www.terraform.io/downloads.html) (v1.0.0+)
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) installed and configured
- Azure subscription with appropriate permissions

## Configuration Details

### Infrastructure Components

| Resource | Name | Details |
|----------|------|---------|
| Resource Group | aks-resource-group | Location: East US |
| Virtual Network | aks-vnet | Address space: 10.0.0.0/16 |
| Subnet | aks-subnet | Address prefix: 10.0.0.0/24 |
| AKS Cluster | example-aks1 | DNS prefix: exampleaks1 |

### AKS Configuration

- **Node Pool**: Default node pool with autoscaling (2-5 nodes)
- **VM Size**: Standard_D2_v2
- **Identity**: System-assigned managed identity
- **Environment Tag**: Production

## Getting Started

1. Clone this repository:
   ```bash
   git clone <repository-url>
   cd aks-terraform-repo
   ```

2. Initialize Terraform:
   ```bash
   terraform init
   ```

3. Review the planned changes:
   ```bash
   terraform plan
   ```

4. Apply the configuration:
   ```bash
   terraform validate
   terraform apply
   ```

5. Confirm by typing `yes` when prompted.

## Accessing the Cluster

After deployment, you can access your AKS cluster using:

```bash
az aks get-credentials --resource-group aks-resource-group --name example-aks1
kubectl get nodes
```

## Kubernetes Resources

In addition to the infrastructure, this repository includes YAML configurations for:
- Namespaces
- Role Bindings
- Service Accounts
- Service Account Token Secrets

These resources can be applied using:

```bash
kubectl apply -f kubernetes/
```

## Monitoring Setup with Prometheus and Grafana

This repository also includes instructions for setting up monitoring with Prometheus and Grafana:

1. Add the necessary Helm repositories:
   ```bash
   helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
   helm repo add stable https://charts.helm.sh/stable
   helm repo update
   ```

2. Install the Prometheus stack (includes Grafana):
   ```bash
   helm install prometheus prometheus-community/kube-prometheus-stack
   ```

3. Expose Grafana using a LoadBalancer service:
   ```bash
   kubectl patch svc prometheus-grafana -p '{"spec": {"type": "LoadBalancer"}}'
   ```

4. Access Grafana using the LoadBalancer's external IP:
   ```bash
   kubectl get svc prometheus-grafana
   ```

5. Grafana default credentials:
   - Username: `admin`
   - Password: `prom-operator`

> **Important**: Consider changing the default credentials immediately for production environments.

## Security Considerations

- The configuration uses a system-assigned managed identity for enhanced security
- Client certificate and kubeconfig are marked as sensitive outputs
- Service account tokens should be rotated regularly
- Consider implementing Azure RBAC for AKS for more granular access control
- Change default Grafana credentials immediately in production environments
- Consider implementing network policies to restrict traffic flow

## Clean Up

To destroy the resources when no longer needed:

```bash
terraform destroy
```

## Troubleshooting

If you encounter issues:
1. Verify your Azure CLI is authenticated correctly
2. Check that your subscription ID is correctly set in the provider configuration
3. Ensure you have appropriate permissions in the Azure subscription
4. Review the Terraform and Azure documentation for specific error messages

For monitoring-related issues:
1. Check if Prometheus pods are running: `kubectl get pods -n default | grep prometheus`
2. Verify Grafana service is properly exposed: `kubectl get svc prometheus-grafana`
3. Check Grafana logs: `kubectl logs -l app.kubernetes.io/name=grafana`

## Contributing

1. Fork the repository
2. Create a feature branch
3. Submit a pull request with a clear description of the changes