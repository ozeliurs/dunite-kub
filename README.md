# Dunite Kubernetes Configuration

This repository contains the Kubernetes configuration for the Dunite cluster.

## Directory Structure

```
dunite-kub/
├── system/              # System and infrastructure applications
│   ├── cert-manager/    # Certificate management
│   ├── sealed-secrets/  # Secret encryption
│   ├── longhorn/       # Storage system
│   ├── kubernetes-dashboard/  # Cluster dashboard
│   ├── traefik/        # Ingress controller
│   └── storage-class/  # Storage class configuration
│
└── workloads/          # User applications
    └── demo-app/       # Example application
```

## Deployment Procedure

### 1. Install ArgoCD

Install ArgoCD in the cluster:

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Retrieve the ArgoCD password:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Access the ArgoCD UI:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Open the browser and navigate to `https://localhost:8080` and connect with admin and the password retrieved.

### 2. Deploy System Components

Deploy the system components in the following order:

1. Sealed Secrets:
   ```bash
   kubectl apply -k system/sealed-secrets
   ```

2. Cert Manager:
   ```bash
   kubectl apply -k system/cert-manager
   ```

3. Storage Class and Longhorn:
   ```bash
   kubectl apply -k system/storage-class
   kubectl apply -k system/longhorn
   ```

4. Traefik Configuration:
   ```bash
   kubectl apply -k system/traefik
   ```

5. Kubernetes Dashboard:
   ```bash
   kubectl apply -k system/kubernetes-dashboard
   ```

### 3. Deploy Workloads

After all system components are running, deploy the workloads:

```bash
kubectl apply -k workloads/demo-app
```

## Verification

1. Check system components:
   ```bash
   kubectl get pods -A
   ```

2. Verify storage class:
   ```bash
   kubectl get sc
   ```

3. Check certificates:
   ```bash
   kubectl get certificates -A
   ```

4. Verify workload deployment:
   ```bash
   kubectl get pods -n default
   ```

## Accessing Services

- Kubernetes Dashboard: Access via configured ingress (typically `https://k3s.yourdomain.com`)
- Longhorn Dashboard: Available at `http://localhost:8000` after port-forwarding:
  ```bash
  kubectl port-forward -n longhorn-system svc/longhorn-frontend 8000:80
  ```

## Troubleshooting

1. Check pod logs:
   ```bash
   kubectl logs -n <namespace> <pod-name>
   ```

2. Check pod events:
   ```bash
   kubectl describe pod -n <namespace> <pod-name>
   ```

3. View ArgoCD application status:
   ```bash
   kubectl get applications -n argocd
   ```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Create a Pull Request

## License

This project is licensed under the MIT License - see the LICENSE file for details.
