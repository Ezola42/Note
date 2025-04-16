# 🔄 Rancher Backup Recovery Guide

This guide walks you through detecting and handling failed Rancher backup jobs in Kubernetes.

It includes the following steps:
1. ✅ Check for failed backup jobs  
2. 📜 View logs from the failed job  
3. 🧹 Delete the failed job  
4. 🔁 Rerun the job manually  

---

## ✅ 1. Check for Failed Backup Jobs

To identify failed Rancher backup jobs:

```bash
kubectl get backups.management.cattle.io -n cattle-resources-system -o json | jq -r '
  .items[] 
  | select(.status.conditions[]?.type == "Failed" and .status.conditions[]?.status == "True") 
  | .metadata.name
'
```

If any names are returned, those jobs have failed and need to be inspected.

---

## 📜 2. Check Logs of Failed Job

After identifying a failed backup (e.g., `rancher-backup-1234`), find the pod related to that job:

```bash
kubectl get pods -n cattle-resources-system | grep rancher-backup-1234
```

Then check the logs:

```bash
kubectl logs <pod-name> -n cattle-resources-system
```

Replace `<pod-name>` with the actual pod name from the previous command.

---

## 🧹 3. Delete the Failed Backup Job

Once you’ve reviewed the logs, clean up the failed job:

```bash
kubectl delete backup rancher-backup-1234 -n cattle-resources-system
```

This removes the backup resource so it can be retried cleanly.

---

## 🔁 4. Rerun Backup Job Manually

Create a new Rancher backup by applying a new `Backup` resource:

**manual-backup.yaml**
```yaml
apiVersion: management.cattle.io/v3
kind: Backup
metadata:
  name: rancher-backup-manual-{{ timestamp }}
  namespace: cattle-resources-system
spec:
  resourceSetName: rancher-resource-set
  storageLocation:
    s3:
      bucketName: rancher-backups
      folder: daily
      credentialSecretName: s3-creds
      region: us-east-1
```

> 💡 Tip: Replace `{{ timestamp }}` with a current timestamp or unique name.

Apply the YAML:

```bash
kubectl apply -f manual-backup.yaml
```

Monitor the backup status:

```bash
kubectl get backups.management.cattle.io -n cattle-resources-system
```

And check logs as needed.

---

## 📌 Notes

- Requires `kubectl` and `jq` installed.
- Assumes Rancher is installed and backups are configured using `backups.management.cattle.io`.
- Namespace and resource names may vary depending on your Rancher setup.

---

## 📂 Related Files

- [manual-backup.yaml](../k8s/manual-backup.yaml) – Sample file to trigger a new Rancher backup
- [CronJob Automation (optional)](../k8s/cronjob-rancher-backup-check.yaml) – Automate this logic via a CronJob

---

## 🧭 Next Steps

Want to automate these checks with a Kubernetes CronJob or integrate with a monitoring system?  
→ Check out the [Automation Guide](automation.md).
