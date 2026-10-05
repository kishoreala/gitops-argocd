# 04 - ApplicationSet (list generator)

**Goal:** one definition generates an Application per environment, and prod stays manual.

```
kubectl apply -f scenarios/04-applicationset-envs/appset.yaml
kubectl -n argocd get applications -l env
```

1. **Generation.** Three apps appear: `envs-dev`, `envs-test`, `envs-prod`. Dev and test sync on their
   own. Prod shows `OutOfSync` and creates nothing until you run `argocd app sync envs-prod` (or
   click Sync).
2. **Add an environment.** Add an element, such as `env: staging` with `autoSync: "true"`, plus a
   `manifests/staging/` folder. Push and re-apply the appset. A fourth app appears with no new
   Application file.
3. **Promotion.** Change `replicas` in `manifests/dev/deployment.yaml`, push, and see only dev
   move. Prod changes only when you sync it.
4. **Delete behavior.** Remove an element from the list and re-apply. The matching Application and
   its resources are removed by the controller.

**Cluster generator (read-only exercise):** swap `list` for a `clusters` generator with a label
selector to target every registered cluster. kind has only one cluster, so just read the syntax.

**Cleanup:** `kubectl delete -f scenarios/04-applicationset-envs/appset.yaml`.
