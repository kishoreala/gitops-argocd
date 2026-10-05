# 05 - Two Applications, one namespace

**Goal:** observe what happens when two Applications both claim the same resource, then fix the
ownership boundary rather than turning prune off.

```
kubectl apply -f scenarios/05-shared-namespace-conflict/team-a.yaml
kubectl apply -f scenarios/05-shared-namespace-conflict/team-b.yaml
kubectl -n lab-shared get cm shared-config -o yaml
```

1. **Conflict.** Both apps declare `ConfigMap/shared-config` in `lab-shared`. Look at both apps in
   the UI or with `argocd app get`: you may see a *SharedResourceWarning*, and the `owner` value
   may flip between `team-a` and `team-b` as each app's selfHeal reasserts itself. Record what you
   actually see. It depends on the Argo CD version and its tracking method
   (`kubectl -n argocd get cm argocd-cm -o yaml | grep resourceTrackingMethod`).
2. **Prune risk.** Delete `manifests-b/configmap.yaml` from Git and push. Check whether team-b's
   sync removes a resource that team-a still expects. Verify; don't assume.
3. **Fix.** Give team-b its own namespace: set its `destination.namespace` to `lab-shared-b`, push, and
   re-apply `team-b.yaml`. Each app now owns its own namespace, the warning goes away, and
   `prune: true` is safe again.

**Principle:** one namespace per owning Application, scoped by AppProject. `prune: false` only hides
the drift.

**Cleanup:** delete both apps.
