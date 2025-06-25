# OfferFit DevOps Assessment

Hey there! This repo is my take on the OfferFit DevOps assessment. I've got two simple Python FastAPI microservices that I've containerized and deployed to Kubernetes using Helm, with a CI/CD pipeline that's security-focused from the ground up.

## What's Inside

This is pretty straightforward:
- **Service A**: Just returns a friendly greeting message
- **Service B**: Calls Service A and combines the responses

The whole thing demonstrates DevSecOps best practices with automated security scanning, container security, and deployment automation.

## Quick Start

### What You'll Need

- Docker (for building images)
- A local Kubernetes cluster (I recommend KinD or Minikube)
- Helm v3+
- kubectl

### Building Everything

Let's get those containers built:

```bash
# Build Service A
docker build -t service-a:latest ./service_a

# Build Service B
docker build -t service-b:latest ./service_b
```

If you're using a local cluster, don't forget to load the images:

```bash
# For Minikube
minikube image load service-a:latest
minikube image load service-b:latest

# For KinD
kind load docker-image service-a:latest
kind load docker-image service-b:latest
```

### Deploying with Helm

This is where the magic happens:

```bash
# Deploy both services using the umbrella chart
helm upgrade --install my-app ./charts/my-app
```

That's it! The umbrella chart handles both services as dependencies.

### Testing Everything Works

Let's make sure Service B can actually talk to Service A:

```bash
# Check that pods are happy
kubectl get pods

# Port-forward to Service B
kubectl port-forward svc/my-app-service-b 8012:8012

# In another terminal, test the services communication
curl http://localhost:8012/ping_service_a
```

You should see a response with both Service B's message and Service A's greeting.

## CI/CD Pipeline & Security

Here's where things get interesting! I've built a CI/CD pipeline using GitHub Actions that automates the process of building images, deploying them locally and running a integration test (Service B calling Service A).

### The Pipeline Does This:

1. **Security First**: Runs multiple security scans before even building anything
2. **Build & Push**: Creates container images and pushes them to GitHub Container Registry  
3. **Deploy**: Sets up a KinD cluster and deploys everything with Helm
4. **Test**: Validates that services can communicate
5. **Runtime Security**: Performs additional security checks on the running deployment

### Security Scanning Includes:
- Static code analysis (Semgrep)
- Secrets detection (GitLeaks) 
- Dependency vulnerability scanning (pip-audit)
- Container security scanning (Trivy)
- Dockerfile best practices (Hadolint)
- Infrastructure as Code security (Checkov)
- Runtime Kubernetes security validation (Polaris)

**For detailed security implementation, check out [SECURITY.md](SECURITY.md)**

### GitHub Secrets You'll Need

If you want to run this pipeline yourself:
- `MY_GITHUB_TOKEN`: For pushing to GitHub Container Registry

## My backlog for this project if I had more time
- Revisit helm charts to ensure best practices and perform a templates cleanup. (For fast bootstrapping, I used the helm create command)
- Review services helm charts looking for similar templates that can be shared through the umbrella chart
- Perform a load test and define resource requirements and limits for the services deployment
- Change GHA `security-scan` step to also use matrix
- Change the workflow to skip a service if there was no change on the service files or helm chart
- Add a ephemeral environment for each PR to test the changes before merging
- Write another workflow to deploy the services to a staging and production environment when a PR is merged to main

## Production-Ready Improvements

Here's what I'd add for a real production environment:

### Secrets Management
- **HashiCorp Vault**: Dynamic secrets management  
- **External Secret Operators**: Sync from external secret stores
- **Sealed Secrets**: Encrypt secrets that can live in Git

### Observability & Monitoring
- **Prometheus + Grafana**: Metrics and dashboards
- **Distributed Tracing**: OpenTelemetry or Jaeger
- **Centralized Logging**: ELK stack or similar
- **Alerting**: Slack or PagerDuty integration

### Scalability & Reliability  
- **Horizontal Pod Autoscaling**: Scale based on metrics
- **Vertical Pod Autoscaling**: Right-size containers
- **Pod Disruption Budgets**: Maintain availability during updates
- **Circuit Breakers**: Resilient service communication

### Security Hardening
- **Network Policies**: Strict network segmentation
- **Pod Security Standards**: Enforce security contexts
- **RBAC**: Fine-grained access control
- **Service Mesh**: mTLS between services (Istio/Linkerd)

### Deployment & GitOps
- **ArgoCD/Flux**: GitOps-based deployments
- **Blue/Green Deployments**: Zero-downtime releases
- **Canary Releases**: Gradual rollouts
- **Rollback Strategies**: Quick recovery from issues

## Final Thoughts

This was a fun little project! The focus was on demonstrating DevSecOps fundamentals with security baked into every step of the pipeline.

Feel free to poke around the code and let me know if you have any questions!
