# 03 - AppProject as the tenancy boundary

**Goal:** show that a project limits where an app may deploy and which kinds it may create.

```
kubectl apply -f scenarios/03-appproject-tenancy/project.yaml
kubectl apply -f scenarios/03-appproject-tenancy/team-a-ok.yaml
```

1. **Allowed.** `team-a-ok` deploys to `team-a-web` and goes `Synced` / `Healthy`.
2. **Disallowed destination.**
   `kubectl apply -f scenarios/03-appproject-tenancy/team-a-bad-destination.yaml`. The app sits in an
   error state: *destination ... is not permitted in project 'team-a'*. Nothing lands in `kube-system`.
3. **Disallowed kind.** `kubectl apply -f scenarios/03-appproject-tenancy/team-a-bad-kind.yaml`. The
   sync fails because `ClusterRole` is not in `clusterResourceWhitelist`.
4. **Loosen it on purpose.** Add `ClusterRole` (group `rbac.authorization.k8s.io`) to the whitelist
   in `project.yaml`, re-apply the project, then re-sync `team-a-bad-kind`. It now succeeds. The
   project, not the Application, is the guard.

**Extension (not scripted):** in `argocd-rbac-cm` add `p, role:team-a, applications, sync, team-a/*, allow`
and bind it to a group. Local kind has no SSO, so just read the policy syntax.

**Cleanup:** delete the three apps, then the project. Run `kubectl delete clusterrole team-a-escalation`
if step 4 created it.
