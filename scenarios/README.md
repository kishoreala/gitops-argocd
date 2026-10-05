# Argo CD scenario labs

Hands-on labs for a local kind cluster (`kind-argocd-lab`) with Argo CD installed.

```
kubectl config use-context kind-argocd-lab
kubectl apply -f bootstrap/            # once: AppProject `lab` + root app
```

Each folder is self-contained: `README.md` (goal, steps, what to observe), an Application or
ApplicationSet to apply **by hand**, and the manifests it deploys. `scenarios/` is not watched by
`lab-root`, so nothing here deploys until you apply it.

Argo CD pulls from GitHub, so **commit and push before applying**, and push every edit you make
during a lab, because the cluster only sees what is on `main`.

| # | Lab | Topic |
|---|-----|-------|
| 01 | [self-heal-drift](01-self-heal-drift/) | selfHeal, manual drift, `ignoreDifferences` |
| 02 | [sync-waves-hooks](02-sync-waves-hooks/) | PreSync migration Job, sync waves |
| 03 | [appproject-tenancy](03-appproject-tenancy/) | AppProject as a multi-tenancy boundary |
| 04 | [applicationset-envs](04-applicationset-envs/) | List generator, dev/test auto and prod manual |
| 05 | [shared-namespace-conflict](05-shared-namespace-conflict/) | Two apps owning the same resource |
| 06 | [rollback-gitops](06-rollback-gitops/) | `argocd app rollback` vs `git revert` |
| 07 | [stuck-progressing](07-stuck-progressing/) | Debugging an app that never turns Healthy |

Cleanup for any lab: `kubectl delete -f scenarios/NN-name/<app file>`. The finalizer cascades to
the deployed resources.
