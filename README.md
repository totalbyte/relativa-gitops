# relativa-gitops

Desired state of the [Relativa](https://github.com/totalbyte/devops-practice) deployment on minikube, applied by Argo CD.

```
bootstrap/root.yaml          # root Application (App of Apps), the only object applied by hand
argocd-apps/relativa.yaml    # Application for the chart -> namespace antoniuk
charts/relativa/             # Helm chart (copy of devops-practice/helm/relativa)
  values-minikube.yaml       # image tags, replicas, existing Secret for this cluster
```

## Bootstrap

```powershell
kubectl create namespace antoniuk
kubectl -n antoniuk create secret generic relativa-secrets --from-literal=DB_PASS=... --from-literal=RABBITMQ_PASS=... --from-literal=JWT_SECRET=...
kubectl apply -f bootstrap/root.yaml
```

Argo CD must already run in namespace `argocd`. Passwords are never committed: the chart uses the existing Secret `relativa-secrets`.

## Changing the deployment

Edit `charts/relativa/values-minikube.yaml` (e.g. `image.tag`), commit, push to `main`. Argo CD syncs automatically; `argocd app get relativa --refresh` skips the polling interval. Roll back with `git revert`.

Details: [GITOPS.md](https://github.com/totalbyte/devops-practice/blob/main/docs/ai-guides/GITOPS.md) · step-by-step: [GITOPS-LAB5.md](https://github.com/totalbyte/devops-practice/blob/main/docs/runbooks/GITOPS-LAB5.md)
