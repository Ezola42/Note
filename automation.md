# 🤖 Automating Rancher Backup Checks

You can automate the Rancher backup health check using a Kubernetes CronJob.

This CronJob:
- Runs daily at 2 AM
- Checks for failed backups
- Logs their pod output
- Deletes the failed backups

To deploy it:

```bash
kubectl apply -f k8s/cronjob-rancher-backup-check.yaml
```

---

If you'd like to auto-rerun the failed backup too, we can extend the CronJob logic with a `kubectl apply -f` call at the end.
