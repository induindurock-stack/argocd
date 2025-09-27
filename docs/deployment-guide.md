# Deployment Guide - ArgoCD Apps of Apps

## 🚀 Quick Deployment

### Step 1: Fork and Clone Repository
```bash
# Fork this repository on GitHub
# Clone your fork
git clone https://github.com/YOUR_GITHUB_USERNAME/argocd-apps-of-apps-github.git
cd argocd-apps-of-apps-github
```

### Step 2: Update Configuration
```bash
# Replace YOUR_GITHUB_USERNAME with your actual GitHub username
find . -name "*.yaml" -exec sed -i 's/YOUR_GITHUB_USERNAME/your-actual-username/g' {} +

# Update Velero credentials in velero/velero-deployment.yaml
# Update S3 bucket name in velero/velero-deployment.yaml
```

### Step 3: Deploy Root App of Apps
```bash
kubectl apply -f apps/root-app/app-of-apps.yaml
```

### Step 4: Verify Deployment
```bash
# Check ArgoCD applications
kubectl get applications -n argocd

# Check all pods
kubectl get pods -A

# Check ingress
kubectl get ingress -A
```

## 🌐 DNS Configuration

### Get ALB DNS Name
```bash
ALB_DNS=$(kubectl get ingress shared-microservices-ingress -n ingress-system -o jsonpath='{.status.loadBalancer.ingress[0].hostname}')
echo "ALB DNS: $ALB_DNS"
```

### Create DNS Records
In your DNS provider, create:
- **CNAME**: `api.swaroopapractice.shop` → `$ALB_DNS`

## 🧪 Testing Services

### Test Endpoints
```bash
# User Service
curl http://api.swaroopapractice.shop/users

# Order Service  
curl http://api.swaroopapractice.shop/orders

# Payment Service
curl http://api.swaroopapractice.shop/payments

# Health Check
curl http://api.swaroopapractice.shop/health
```

## 🔧 Velero Configuration

### Update Velero Credentials
1. Edit `velero/velero-deployment.yaml`
2. Replace `YOUR_ACCESS_KEY` and `YOUR_SECRET_KEY`
3. Replace `YOUR_S3_BUCKET` with your bucket name
4. Commit and push changes

### Manual Backup
```bash
# Create manual backup
velero backup create manual-backup --include-namespaces user-service,order-service,payment-service

# List backups
velero backup get

# Restore from backup
velero restore create restore-1 --from-backup manual-backup
```

## 📊 Monitoring

### ArgoCD Dashboard
- Access: http://argocd.swaroopapractice.shop
- Username: admin
- Password: Get from `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`

### Application Status
```bash
# Check sync status
kubectl get applications -n argocd

# Check application details
kubectl describe application user-service -n argocd
```

## 🔄 Making Changes

### Update Microservice
1. Modify files in `microservices/SERVICE_NAME/`
2. Commit and push to GitHub
3. ArgoCD will automatically sync changes

### Add New Microservice
1. Create new directory in `microservices/`
2. Add deployment and service YAML files
3. Create application definition in `apps/applications/`
4. Update shared ingress with new routes
5. Commit and push changes

## 🛠️ Troubleshooting

### Application Not Syncing
```bash
# Force sync
kubectl patch application APP_NAME -n argocd --type merge -p '{"operation":{"sync":{"syncStrategy":{"hook":{"force":true}}}}}'

# Check application logs
kubectl logs -n argocd deployment/argocd-application-controller
```

### Ingress Issues
```bash
# Check ALB controller logs
kubectl logs -n kube-system deployment/aws-load-balancer-controller

# Check ingress status
kubectl describe ingress shared-microservices-ingress -n ingress-system
```

### Service Discovery Issues
```bash
# Check external services
kubectl get svc -n ingress-system

# Test service connectivity
kubectl run test-pod --image=busybox --rm -it -- nslookup user-service.user-service.svc.cluster.local
```