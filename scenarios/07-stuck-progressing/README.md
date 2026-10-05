# 07 - App stuck in Progressing

**Goal:** debug an Argo CD status that is really a Kubernetes problem.

```
kubectl apply -f scenarios/07-stuck-progressing/app.yaml
```

The readiness probe hits `/nope`, which returns 404, so pods never become Ready. Work through it
without reading the manifest first.

1. `argocd app get stuck-app`: which resource is not `Healthy`?
2. `argocd app resources stuck-app`, then the UI resource tree.
3. `kubectl -n lab-stuck rollout status deploy/stuck-app`
4. `kubectl -n lab-stuck get rs,pods`
5. `kubectl -n lab-stuck describe pod <pod>`: find the "Readiness probe failed: HTTP probe failed
   with statuscode: 404" event.
6. Change `path: /nope` to `path: /`, push, and watch the app go `Healthy`.

**Observe:** Argo CD's health for a Deployment means rollout complete. It can't tell you why the
rollout is stuck. The Events do.

**Variants to try:** a bad image tag (`ImagePullBackOff`) or a too-high `requests.memory`
(`Pending`/unschedulable), and see how each looks in `describe`.
