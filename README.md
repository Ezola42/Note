# 🛡️ Rancher Backup Toolkit

This repository provides Kubernetes manifests and MkDocs documentation to:

- Detect failed Rancher backup jobs
- Check job logs
- Delete failed backups
- Manually or automatically trigger backups

📂 Key folders:
- `docs/` – MkDocs-compatible documentation
- `k8s/` – Kubernetes YAML files for manual or automated backup handling

## ✅ Quickstart

1. Clone the repo
2. Customize and apply:
```bash
kubectl apply -f k8s/manual-backup.yaml
kubectl apply -f k8s/cronjob-rancher-backup-check.yaml
```
3. Run docs locally:
```bash
pip install mkdocs-material
mkdocs serve
```
