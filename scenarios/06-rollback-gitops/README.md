# 06 - Rollback: argocd app rollback vs git revert

**Goal:** see why the durable rollback goes through Git.

```
kubectl apply -f scenarios/06-rollback-gitops/app.yaml
```

1. **Bad release.** Change the image to `nginx:9.99.99`, commit and push. New pods go
   `ImagePullBackOff` and the app goes `Progressing`/`Degraded`. The old pods keep serving
   (rolling update).
2. **Try `argocd app rollback rollback-demo`.** With automated sync on it refuses: rollback cannot
   be initiated while auto-sync is enabled. `argocd app history rollback-demo` lists revision IDs.
3. **Temporary mitigation.** Disable auto-sync (`argocd app set rollback-demo --sync-policy none`),
   then `argocd app rollback rollback-demo <last-good-id>`. Pods recover, but the app now shows
   `OutOfSync` because Git still holds the bad commit.
4. **Durable fix.** `git revert <bad-commit>`, push, and re-enable auto-sync
   (`argocd app set rollback-demo --sync-policy automated --auto-prune --self-heal`). Git and the
   cluster agree again.

**Observe:** `app rollback` changes live state only. Git is the source of truth, so the fix must
land there too.

**Cleanup:** delete the app.
