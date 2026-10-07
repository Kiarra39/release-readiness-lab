# Rollback Plan

## Stable rollback target
The known-good production version is `v2.3`, as documented in `docs/architecture.md`:

```text
| v2.3 | 2026-06-01 | Dev Team | Deployed (Prod - STABLE) | Current production release |
```

This is the rollback target for any failed v2.4 deployment.

## Immediate rollback procedure
The rollback action should be performed from the release window owner’s terminal or deployment tooling with a change window in progress.

```bash
kubectl set image deployment/checkout-api checkout-api=kalvium/checkout-api:v2.3 -n checkout-system
kubectl rollout status deployment/checkout-api -n checkout-system
kubectl get pods -n checkout-system
kubectl logs deployment/checkout-api -n checkout-system --tail 100
```

If the deployment manifest itself needs to be restored to the stable state, reapply the last known good manifest set:

```bash
kubectl apply -f k8s/configmap.yaml -n checkout-system
kubectl apply -f k8s/deployment.yaml -n checkout-system
kubectl apply -f k8s/service.yaml -n checkout-system
```

## Verification after rollback
```bash
kubectl get deployment checkout-api -n checkout-system
kubectl get pods -n checkout-system
curl -sS http://<service-ip>/health
```
Acceptable post-rollback result:
- deployment available
- pods ready and stable
- health endpoint returns HTTP 200
- version reported is `v2.3` or the known-good release

## Operational notes
- This rollback plan was prepared and validated by command structure, but no live cluster was available in this workstation for an execution test.
- The final production runbook should include a human approval gate and a the rollback decision should be made by the Release Owner and the SRE lead together.

## Conclusion
The service has a clear rollback target and command path, but the operational runbook must be executed against a live cluster and recorded in production before the release is approved.
