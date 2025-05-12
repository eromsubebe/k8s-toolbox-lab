# Real-World ArgoCD Use Case: Multi-Environment Promotion

This lab demonstrates a GitOps workflow using ArgoCD and Kustomize to promote application changes from `dev` to `staging` to `prod`.

## 🔧 Structure

- `base/`: Core guestbook deployment config
- `environments/dev/`: Dev-specific overrides
- `environments/staging/`: Staging-specific settings (e.g., replicas)
- `environments/prod/`: Production configuration

## 🚀 Steps

1. Deploy ArgoCD and login
2. Register your cluster:
   ```bash
   argocd cluster add <your-gke-context>

   .
   .
   Cluster URL: 'https://34.170.157.3' added

   Argocd server: 34.56.141.59 (for logging into)
   ```

3. Deploy apps for each environment:
   ```bash
   argocd app create guestbook-dev \
     --repo https://github.com/<your-org>/<repo>.git \
     --path guestbook/environments/dev \
     --dest-server https://<your-gke-endpoint> \
     --dest-namespace default
```
   argocd app create guestbook-dev \
     --repo https://github.com/eromsubebe/argocd-example-apps.git \
     --path guestbook/environments/dev \
     --dest-server https://34.170.157.3 \
     --dest-namespace default
```
## Note: If 'argocd create' throws a permission error - workaround this using below manifest file - and do a "kubectl apply -f <filename>"

Error:
Above throws errors "FATA[0001] rpc error: code = PermissionDenied desc = permission denied" 
```

```bash - guestbook-dev.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook-dev
spec:
  destination:
    namespace: default
    server: https://34.170.157.3
  source:
    path: guestbook/environments/dev
    repoURL: https://github.com/eromsubebe/argocd-example-apps.git
    targetRevision: HEAD
  sources: []
  project: default
```
```
```
   argocd app create guestbook-staging \
     --repo https://github.com/<your-org>/<repo>.git \
     --path guestbook/environments/staging \
     --dest-server https://<your-gke-endpoint> \
     --dest-namespace default

   argocd app create guestbook-prod \
     --repo https://github.com/<your-org>/<repo>.git \
     --path guestbook/environments/prod \
     --dest-server https://<your-gke-endpoint> \
     --dest-namespace default
   ```

4. Promote a change by editing `dev`, testing, then copying to `staging`, then `prod`.

