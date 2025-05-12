# ArgoCD Lab

This lab helps you bootstrap ArgoCD into your GKE cluster and deploy a sample GitOps application (e.g., the `guestbook` app).

---

## ✅ Prerequisites

- GKE cluster is running and `kubectl` is configured
- `argocd` CLI is installed (`brew install argocd`)
- Helm is installed

---

## 🧰 Step 1: Install ArgoCD

```bash
kubectl create namespace argocd

kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

---

## 🌐 Step 2: Expose the ArgoCD Server
```bash
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

Then run:
```bash
kubectl get svc -n argocd
```

Note the `EXTERNAL-IP` and use it in the next step.
--> 34.56.141.59
---

## 🔐 Step 3: Login to ArgoCD CLI

```bash
# Get the default admin password
export ARGOCD_PASSWORD=$(kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo)

❯ echo $ARGOCD_PASSWORD
p2EbV4DG4a68NroZ
# Login
argocd login <EXTERNAL-IP> --username admin --password <password> --insecure

argocd login 34.56.141.59 --username admin --password $ARGOCD_PASSWORD --insecure
```
## Step 3b: Verification of login and argocd setup

```
argocd account list
❯ argocd account list
NAME   ENABLED  CAPABILITIES
admin  true     login

argocd proj get default
❯ argocd proj get default
Name:                        default
Description:
Destinations:                *,*
Repositories:                *
Scoped Repositories:         <none>
Allowed Cluster Resources:   */*
Scoped Clusters:             <none>
Denied Namespaced Resources: <none>
Signature keys:              <none>
Orphaned Resources:          disabled

argocd account get-user-info
❯ argocd account get-user-info
Logged In: true
Username: admin
Issuer: argocd
Groups:
```
---

## 🧩 Step 4: Register Your GKE Cluster

List contexts:
```bash
kubectl config get-contexts

OR
❯ kubectx
docker-desktop
gke_erok8s-tools_us-central1-b_gitops-cluster  <--
gke_eroms-demo-app_us-central1-a_citi-demo-solo-cluster
gke_eroms-demo-app_us-central1_citi-demo-scale-cluster
```

Register the correct one:
```bash
argocd cluster add <your-gke-context>

argocd cluster add gke_erok8s-tools_us-central1-b_gitops-cluster

```
❯ argocd cluster add gke_erok8s-tools_us-central1-b_gitops-cluster
WARNING: This will create a service account `argocd-manager` on the cluster referenced by context `gke_erok8s-tools_us-central1-b_gitops-cluster` with full cluster level privileges. Do you want to continue [y/N]? y
INFO[0007] ServiceAccount "argocd-manager" created in namespace "kube-system"
INFO[0008] ClusterRole "argocd-manager-role" created
INFO[0008] ClusterRoleBinding "argocd-manager-role-binding" created
INFO[0013] Created bearer token secret for ServiceAccount "argocd-manager"
Cluster 'https://34.170.157.3' added
```
```
This ensures ArgoCD is authorized to deploy to the correct cluster.

---

## 🚀 Step 5a: Create App from CLI (Corrected)

Use the actual GKE API server from:
```bash
kubectl config view --minify -o jsonpath='{.clusters[0].cluster.server}'
```

Then run:
```bash
argocd app create guestbook \
--repo https://github.com/argoproj/argocd-example-apps.git \
--path guestbook   --dest-server https://<your-gke-api-endpoint> \   --dest-namespace default
```
Above throws errors "FATA[0001] rpc error: code = PermissionDenied desc = permission denied" 
```bash
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: <guestbook>
spec:
  destination:
    namespace: default
    server: <https://34.170.157.3>
  source:
    path: guestbook
    repoURL: https://github.com/argoproj/argocd-example-apps.git
    targetRevision: HEAD
  sources: []
  project: default
```
- Run kubectl apply -f <argocd-app-filename>

> ❗️ Do NOT use `https://kubernetes.default.svc` unless you're sure it's recognized by ArgoCD.
---

## ✅ Step 5: Sync and Verify

```bash
argocd app sync guestbook
argocd app get guestbook
```

You should see the deployment rollout.

---

## 🧹 Cleanup

```bash
argocd app delete guestbook
kubectl delete namespace argocd
```
