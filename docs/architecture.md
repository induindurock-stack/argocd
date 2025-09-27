# Architecture Overview

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Internet Traffic                          │
└─────────────────────┬───────────────────────────────────────┘
                      │
┌─────────────────────▼───────────────────────────────────────┐
│                AWS ALB (Shared)                             │
│           api.swaroopapractice.shop                         │
└─────────────────────┬───────────────────────────────────────┘
                      │
        ┌─────────────┼─────────────┐
        │             │             │
        ▼             ▼             ▼
┌─────────────┐ ┌─────────────┐ ┌─────────────┐
│ User Service│ │Order Service│ │Payment Svc  │
│ /users      │ │ /orders     │ │ /payments   │
│ Namespace   │ │ Namespace   │ │ Namespace   │
└─────────────┘ └─────────────┘ └─────────────┘
```

## 🎯 ArgoCD Apps of Apps Pattern

```
Root App of Apps
├── User Service App
├── Order Service App  
├── Payment Service App
├── Shared Ingress App
└── Velero App
```

## 🌐 Domain Routing Strategy

### Single ALB, Multiple Services
- **Domain**: api.swaroopapractice.shop
- **Path-based routing**:
  - `/users/*` → User Service
  - `/orders/*` → Order Service  
  - `/payments/*` → Payment Service
  - `/health` → Health check endpoint

### Benefits
- **Cost Efficient**: One ALB for all services
- **Centralized SSL**: Single certificate management
- **Simplified DNS**: One domain, multiple paths
- **Easy Monitoring**: Centralized traffic metrics

## 🔄 GitOps Workflow

```
Developer Push → GitHub → ArgoCD Sync → Kubernetes Deploy
```

1. **Developer** commits changes to GitHub
2. **ArgoCD** detects changes automatically
3. **ArgoCD** syncs desired state to cluster
4. **Kubernetes** deploys updated resources

## 🛡️ Security Architecture

### Network Security
- **ALB**: Internet-facing with security groups
- **Services**: ClusterIP (internal only)
- **Namespaces**: Isolated per microservice

### Access Control
- **ArgoCD RBAC**: Role-based access control
- **Kubernetes RBAC**: Service account permissions
- **AWS IAM**: ALB controller permissions

## 💾 Backup Strategy

### Velero Configuration
- **Daily Backups**: All microservice namespaces
- **Weekly Full**: Complete cluster backup
- **Retention**: 30 days daily, 90 days weekly
- **Storage**: AWS S3 backend

### Backup Scope
```
Daily Backup:
├── user-service namespace
├── order-service namespace
├── payment-service namespace
└── ingress-system namespace

Weekly Backup:
└── All namespaces (except system)
```

## 📊 Monitoring & Observability

### ArgoCD Dashboard
- **Application Health**: Sync status per service
- **Resource Status**: Pod, service, ingress health
- **Sync History**: Deployment timeline
- **Configuration Drift**: Desired vs actual state

### Kubernetes Native
- **Pod Logs**: `kubectl logs`
- **Events**: `kubectl get events`
- **Resource Status**: `kubectl get all`

## 🔧 Scalability Considerations

### Horizontal Scaling
- **Pod Replicas**: 2 per service (configurable)
- **Auto-scaling**: HPA can be added
- **Load Distribution**: ALB handles traffic distribution

### Vertical Scaling
- **Resource Requests**: 50m CPU, 64Mi memory
- **Resource Limits**: 100m CPU, 128Mi memory
- **Adjustable**: Per service configuration

## 🚀 Deployment Patterns

### Blue-Green Deployment
- **ArgoCD Sync Waves**: Controlled rollout
- **Health Checks**: Readiness/liveness probes
- **Rollback**: Git revert + ArgoCD sync

### Canary Deployment
- **Traffic Splitting**: ALB weighted routing
- **Gradual Rollout**: Percentage-based traffic
- **Monitoring**: Health metrics validation

## 🔄 Disaster Recovery

### Backup Strategy
1. **Automated Backups**: Velero schedules
2. **Cross-Region**: S3 replication (optional)
3. **Testing**: Regular restore validation

### Recovery Process
1. **Assess Damage**: Identify failed components
2. **Restore Data**: Velero restore from backup
3. **Verify Services**: Health check validation
4. **DNS Update**: Point traffic to recovered services

## 📈 Future Enhancements

### Observability
- **Prometheus**: Metrics collection
- **Grafana**: Visualization dashboards
- **Jaeger**: Distributed tracing
- **ELK Stack**: Centralized logging

### Security
- **Service Mesh**: Istio for mTLS
- **Policy Engine**: OPA Gatekeeper
- **Secrets Management**: External Secrets Operator
- **Image Scanning**: Trivy integration

### CI/CD
- **GitHub Actions**: Automated testing
- **Image Building**: Docker registry integration
- **Quality Gates**: Security and performance checks
- **Progressive Delivery**: Flagger for canary deployments