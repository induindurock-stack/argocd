# ArgoCD Apps of Apps - Production Ready Microservices

## 🎯 Project Overview

Complete GitOps implementation with ArgoCD Apps of Apps pattern featuring:
- **Multiple Microservices**: User, Order, Payment services
- **Shared Domain Ingress**: All services on swaroopapractice.shop
- **Velero Backup**: Automated disaster recovery
- **Production Ready**: SSL, monitoring, health checks

## 🏗️ Architecture

```
swaroopapractice.shop
├── argocd.swaroopapractice.shop → ArgoCD UI
├── api.swaroopapractice.shop/users → User Service
├── api.swaroopapractice.shop/orders → Order Service
└── api.swaroopapractice.shop/payments → Payment Service
```

## 📁 Project Structure

```
argocd-apps-of-apps-github/
├── apps/
│   ├── root-app/              # Main App of Apps
│   └── applications/          # Individual application definitions
├── microservices/
│   ├── user-service/          # User management microservice
│   ├── order-service/         # Order processing microservice
│   └── payment-service/       # Payment processing microservice
├── velero/                    # Backup and restore configuration
├── shared-ingress/           # Shared ALB ingress configuration
└── docs/                     # Documentation
```

## 🚀 Quick Start

1. **Fork this repository**
2. **Update repository URL** in all ArgoCD applications
3. **Deploy root app**: `kubectl apply -f apps/root-app/`
4. **Configure DNS**: Point subdomains to ALB
5. **Access services**: All services available on your domain

## 🌐 Service Endpoints

- **ArgoCD**: http://argocd.swaroopapractice.shop
- **User Service**: http://api.swaroopapractice.shop/users
- **Order Service**: http://api.swaroopapractice.shop/orders  
- **Payment Service**: http://api.swaroopapractice.shop/payments

## 🔧 Configuration

Repository configured for GitHub user: `induindurock-stack`

### Prerequisites
- EKS cluster with AWS Load Balancer Controller
- ArgoCD installed
- Domain: swaroopapractice.shop configured in DNS

## 📚 Documentation

See `docs/` directory for detailed setup and configuration guides.# argocd
