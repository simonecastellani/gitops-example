# GitOps Example Project

This repository demonstrates a GitOps approach to managing Kubernetes clusters and applications using ArgoCD. The project includes infrastructure configurations and application deployments for both local development (Kind) and lab environments.

## Project Structure

```
├── apps/                      # Application manifests
│   ├── argocd/               # ArgoCD installation manifests
│   ├── dex/                  # Dex authentication manifests
│   ├── ingress-nginx/        # Nginx ingress controller manifests
│   ├── kubernetes-dashboard/ # Kubernetes Dashboard manifests
│   └── microservice/        # Sample microservice manifests
└── clusters/                 # Cluster-specific configurations
    ├── kind/                # Local development cluster
    └── lab/                 # Lab environment cluster
```

## Prerequisites

- Kubernetes CLI (kubectl)
- Kind (for local development)
- Git
- Helm (optional)

## Getting Started

### Setting up a Local Development Environment

1. Create a Kind cluster using the provided configuration:
```bash
kind create cluster --config clusters/kind/infrastructure/kind-config.yaml
```

2. Install ArgoCD:
```bash
kubectl create namespace argocd
kubectl apply -f apps/argocd/base/install.yaml -n argocd
```

3. Wait for ArgoCD pods to be ready:
```bash
kubectl wait --for=condition=Ready pods --all -n argocd --timeout=300s
```

4. Access ArgoCD UI:
```bash
# Port forward the ArgoCD server
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Get the initial admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

The ArgoCD UI will be available at: http://localhost:8080

### Deploying Applications

1. Deploy Ingress Controller:
```bash
kubectl apply -f clusters/kind/apps/ingress-nginx.yaml
```

2. Deploy Dex (Authentication):
```bash
kubectl apply -f clusters/kind/apps/dex.yaml
```

3. Deploy Kubernetes Dashboard:
```bash
kubectl apply -f clusters/kind/apps/kubernetes-dashboard.yaml
```

4. Deploy Sample Microservice:
```bash
kubectl apply -f clusters/kind/apps/microservice.yaml
```

## Accessing Applications

- ArgoCD UI: http://localhost:8080
- Kubernetes Dashboard: http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
- Sample Microservice: Will be available through the Ingress configuration

## Lab Environment Deployment

For deploying to the lab environment, use the manifests in the `clusters/lab` directory:

```bash
# Apply lab cluster configuration
kubectl apply -f clusters/lab/infrastructure/cluster-config.yaml

# Deploy applications
kubectl apply -f clusters/lab/apps/
```

## Monitoring and Management

1. Check application status in ArgoCD:
```bash
kubectl get applications -n argocd
```

2. View pods across all namespaces:
```bash
kubectl get pods --all-namespaces
```

3. Check ingress status:
```bash
kubectl get ingress --all-namespaces
```

## Cleanup

To delete the local Kind cluster:
```bash
kind delete cluster
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## Security

This project includes Dex for authentication. Make sure to:
- Change default passwords
- Configure proper RBAC
- Keep your cluster's security patches up to date

## Troubleshooting

Common issues and solutions:

1. If ArgoCD pods are not starting:
```bash
kubectl describe pods -n argocd
kubectl logs -n argocd <pod-name>
```

2. If applications are not syncing:
- Check network connectivity
- Verify Git repository access
- Check application manifests for syntax errors

## Support

For issues and feature requests, please create an issue in the repository.