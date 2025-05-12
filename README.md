# Kubernetes Toolbox Lab

This repository provides hands-on exercises to learn and practice Kubernetes platform engineering tools using a GKE cluster (Google Cloud Free Tier compatible).

## Prerequisites

- A GCP project with billing enabled
- A GKE cluster (1-node autopilot is fine)
- kubectl and gcloud CLI installed
- Helm installed
- Tools like kubeseal, k9s, stern, argocd, flux, grafana, prometheus, etc. installed

## Tool Categories & Labs

### 🔧 Cluster Management & GitOps
- [x] Lens
- [ ] Rancher
- [ ] ArgoCD
- [ ] Flux

### 📈 Observability
- [ ] Prometheus
- [ ] Grafana
- [ ] Loki

### 🔐 Security & Compliance
- [ ] kube-bench
- [ ] kubeaudit

### 🔑 Secret Management
- [ ] kubeseal

### 🛠 Troubleshooting
- [ ] k9s
- [ ] stern

### 🧪 Testing & Validation
- [ ] sonobuoy

---

## Usage

Each folder includes:
- Setup instructions
- YAML manifests
- Helm commands
- Cleanup steps

Clone this repo and work through each tool step by step.

```bash
git clone https://github.com/<your-org>/k8s-toolbox-lab.git
cd k8s-toolbox-lab
```
