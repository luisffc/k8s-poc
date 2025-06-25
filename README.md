# Microservices Assessment

This repository contains two Python FastAPI microservices (Service A and Service B) that are containerized and deployed to Kubernetes using Helm.

## Architecture

- **Service A**: A simple FastAPI service that returns a greeting message
- **Service B**: A FastAPI service that calls Service A and returns a combined response

## Prerequisites

- Docker
- Kubernetes cluster (Minikube, KinD, or similar)
- Helm v3+
- kubectl

## Building the Deployment Artifacts

### Build Docker Images

```bash
# Build Service A
docker build -t service-a:latest ./service_a

# Build Service B
docker build -t service-b:latest ./service_b
```

If using a local Kubernetes cluster like Minikube or KinD, load the images:

```bash
# For Minikube
minikube image load service-a:latest
minikube image load service-b:latest

# For KinD
kind load docker-image service-a:latest
kind load docker-image service-b:latest
```

## Deploying with Helm

### Install the Helm Charts

```bash
# Install Service A
helm upgrade --install service-a ./helm/service-a

# Install Service B
helm upgrade --install service-b ./helm/service-b
```

## Verifying the Deployment

1. Check that the pods are running:

```bash
kubectl get pods
```

2. Test Service B's connection to Service A:

```bash
# Port-forward Service B
kubectl port-forward svc/service-b 8012:8012

# In another terminal, make a request to Service B
curl http://localhost:8012/call-service-a
```

You should see a response that includes both Service B's message and Service A's response.

## CI/CD Pipeline

This project includes a GitHub Actions workflow that:
1. Builds the Docker images
2. Sets up a KinD Kubernetes cluster
3. Deploys the services using Helm
4. Tests the services' functionality

### Required Secrets
Configure these GitHub secrets for full functionality:
- `MY_GITHUB_TOKEN`: GitHub Container Registry access

## Handling Secrets in Production

For a production environment, sensitive data would be handled more securely:

1. **Kubernetes Secrets**: Used for storing sensitive environment variables
2. **HashiCorp Vault**: For dynamic secrets management
3. **External Secret Operators**: To sync secrets from external providers
4. **Sealed Secrets**: For encrypting secrets that can be safely stored in Git

## Improvements for Production

1. **Monitoring and Observability**: Implement Prometheus and Grafana for monitoring
2. **Horizontal Pod Autoscaling**: Configure autoscaling based on CPU/memory usage
3. **Network Policies**: Implement stricter network security policies
4. **Resource Management**: Fine-tune resource requests and limits
5. **Liveness/Readiness Probes**: Add health checks for better resilience
6. **Database Integration**: Add persistent storage for stateful applications
7. **Zero-downtime Deployments**: Configure proper deployment strategies
8. **HTTPS/TLS**: Add ingress with TLS for secure endpoints
