# Retail Store - Kubernetes Deployments

This repository manages the Kubernetes deployment manifests for the Retail Store
microservices application running on Amazon EKS.

## Architecture

```
                    ┌──────────────────────────────────────┐
                    │         Amazon EKS Cluster            │
   Users ──▶ ALB ──▶│                                      │
                    │  UI ──▶ Catalog ──▶ Aurora MySQL      │
                    │     ──▶ Carts   ──▶ DynamoDB         │
                    │     ──▶ Orders  ──▶ Aurora PostgreSQL │
                    │     ──▶ Checkout ──▶ Redis            │
                    └──────────────────────────────────────┘
```

## Repository Structure

```
k8s/
├── namespaces.yaml      # Namespace definitions for all services
├── catalog.yaml         # Catalog service (Go) - Product catalog API
├── carts.yaml           # Carts service (Java) - Shopping cart API
├── orders.yaml          # Orders service (Java) - Order processing API
├── checkout.yaml        # Checkout service (Node.js) - Checkout orchestration
├── ui.yaml              # UI service (Java) - Store frontend
└── ingress.yaml         # ALB Ingress for external access
```

## Deployment

Deployments are automated via GitHub Actions:

- **On push to `main`**: Validates manifests → deploys to EKS cluster
- **On pull request**: Validates YAML syntax only (no deploy)

### Manual deployment

```bash
aws eks update-kubeconfig --name retail-store --region us-east-1
kubectl apply -f k8s/namespaces.yaml
kubectl apply -f k8s/catalog.yaml
kubectl apply -f k8s/carts.yaml
kubectl apply -f k8s/orders.yaml
kubectl apply -f k8s/checkout.yaml
kubectl apply -f k8s/ui.yaml
kubectl apply -f k8s/ingress.yaml
```

## Monitoring

This repository is connected to AWS DevOps Agent for automated incident
investigation. Deployment events from this repo are correlated with
operational incidents to help identify root causes.

## Setup

1. Add `AWS_ROLE_ARN` as a GitHub Actions secret (IAM role with EKS access via OIDC)
2. Connect this repo to AWS DevOps Agent via CI/CD → GitHub integration
3. Associate the EKS cluster resources with this project in DevOps Agent
