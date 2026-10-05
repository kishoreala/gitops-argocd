# 02 - PreSync hook and sync waves

**Goal:** a migration Job must finish before the Deployment rolls forward, and a failing migration
must block the rollout.

```
kubectl apply -f scenarios/02-sync-waves-hooks/app.yaml
kubectl -n lab-waves get job,pods -w
```

1. **Happy path.** The `db-migrate` Job (PreSync, wave -1) runs for about 15s. The Deployment is only
   created after the Job succeeds. Confirm the ordering in the UI's sync result, or with
   `argocd app get waves-demo`.
2. **Failing migration.** In `migration-job.yaml`, change `exit 0` to `exit 1`, and in
   `deployment.yaml` bump the image tag (e.g. `nginx:1.27.1`). Push. The sync fails at the hook and
   the Deployment is not updated: confirm the pods are not replaced.
3. **Fix** the Job and push. The sync proceeds.

**Observe:** hooks are not part of the normal diff. Only a sync operation runs them, which is why an
automated sync after a Git push is what triggers them here.

**Note:** `hook-delete-policy: BeforeHookCreation` removes the previous Job before each run, so a
re-sync does not collide with the old Job.
