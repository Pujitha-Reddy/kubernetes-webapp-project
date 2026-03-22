# Project 4 — Kubernetes on AWS EKS

## Overview
Deployed a Dockerized web app on AWS Elastic Kubernetes Service (EKS)
with auto-scaling, self-healing, and a real AWS Load Balancer.

## Live Demo
App was live at: http://a94f4b9e17dec402f9159e3c75b5e335-965421100.us-east-2.elb.amazonaws.com
(EKS cluster deleted to save credits)

## Architecture
```
Internet
    ↓
AWS Load Balancer (auto-created by Kubernetes)
    ↓
3 Pods running pujitha2366/my-webapp:latest
    ↓
2 Worker Nodes (t3.small EC2 instances)
    ↓
EKS Cluster (my-webapp-cluster, us-east-2)
```

## Tech Stack
- **Container Orchestration:** Kubernetes (EKS v1.34)
- **Cluster Tool:** eksctl v0.224.0
- **CLI:** kubectl v1.35.0
- **Cloud:** AWS EKS (us-east-2)
- **App:** Docker image from Project 1 & 2

## Project Structure
```
kubernetes-webapp-project/
├── deployment.yaml   # Runs 3 copies of the app
└── service.yaml      # Exposes app via AWS Load Balancer
```

## How to Deploy

### Prerequisites
- AWS CLI configured
- kubectl installed
- eksctl installed

### Create EKS Cluster
```bash
eksctl create cluster \
  --name my-webapp-cluster \
  --region us-east-2 \
  --zones us-east-2a,us-east-2b \
  --nodegroup-name my-nodes-small \
  --node-type t3.small \
  --nodes 2 \
  --nodes-min 1 \
  --nodes-max 2 \
  --managed \
  --timeout 40m
```

### Deploy App
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

# Get public URL
kubectl get service webapp-service
```

### Scale Up/Down
```bash
kubectl scale deployment webapp-deployment --replicas=5
kubectl scale deployment webapp-deployment --replicas=2
```

### Delete Everything (save credits!)
```bash
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
eksctl delete cluster --name my-webapp-cluster --region us-east-2
```

## What I Learned
- Kubernetes core concepts: Pods, Deployments, Services
- How EKS manages the control plane automatically
- How LoadBalancer Service creates an AWS ELB automatically
- Pod scheduling and resource limits on t3 instances
- Kubernetes self-healing — deleted a pod, K8s restarted it instantly
- Scaling from 2 to 5 replicas with one command
- How to debug pending pods using kubectl describe
- Always use t3.small or larger for EKS nodes (t3.micro too small)

## Key Commands
| Command | Purpose |
|---------|---------|
| kubectl get nodes | See cluster nodes |
| kubectl get pods | See running pods |
| kubectl get service | See load balancer URL |
| kubectl scale deployment --replicas=N | Scale app |
| kubectl delete pod <name> | Test self-healing |
| kubectl describe pod <name> | Debug issues |

## Next Project
**Project 5 — Monitoring with Prometheus + Grafana**
