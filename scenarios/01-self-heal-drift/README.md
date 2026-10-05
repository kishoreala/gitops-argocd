# 01 - Self-heal and drift

**Goal:** see what selfHeal does, what happens without it, and how ignoreDifferences stops
Argo CD fighting another controller.

```
kubectl apply -f scenarios/01-self-heal-drift/app.yaml
kubectl -n lab-drift get deploy drift-demo
```

1. **selfHeal on.** `kubectl -n lab-drift scale deploy drift-demo --replicas=5`, then watch with
   `kubectl -n lab-drift get deploy drift-demo -w`. It snaps back to 2 within seconds.
2. **selfHeal off.** Set `selfHeal: false` in `app.yaml`, push, and `kubectl apply -f` the file
   again (the app is not managed by the root app, so it won't update itself). Scale to 5 again.
   Argo CD now shows `OutOfSync` but leaves 5 replicas running. Check `argocd app diff drift-demo`.
   Clicking Sync fixes it.
3. **ignoreDifferences.** Turn selfHeal back on, uncomment the `ignoreDifferences` block, push
   and re-apply. Scale to 5. Now it stays at 5 and the app remains `Synced`. This is the fix when an
   HPA or autoscaler owns `replicas`.

**Observe:** `OutOfSync` vs auto-revert, and that ignoreDifferences hides the field from the diff
entirely.
