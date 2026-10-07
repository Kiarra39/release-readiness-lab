# Risk Analysis

## Risk register

| ID | Risk | Severity | Likelihood | Mitigation | Owner |
|---|---|---|---|---|---|
| R1 | `DATABASE_URL` and `REDIS_URL` are absent from the production config, forcing in-memory SQLite/local cache fallback. | High | High | Add managed database/cache configuration, deploy secrets through a secret manager, and validate the health checks against live dependencies before promotion. | SRE / Operational Lead |
| R2 | The app reports `healthy` while running without external dependencies because the health endpoint only checks for fallback state. | High | High | Change the app to report a degraded state when fallback paths are used and create a production deployment gate that blocks rollout if fallback status is present. | Engineering Owner |
| R3 | No validated rollback execution was performed against a live cluster; the documented rollback plan is not yet proven. | High | Medium | Run a test rollback in staging or a dev cluster, record the output, and add a rollback checkpoint to the deployment checklist. | Release Owner |
| R4 | There is no explicit production ownership or approval record attached to the release. | Medium | High | Add a deployment ownership document, clear on-call assignment, and a required sign-off before each production deploy. | Release Owner |
| R5 | Container validation is not proven in this environment because Docker is unavailable locally and the workflow step is dry-run only. | Medium | Medium | Run Docker build and smoke validation in CI or a dedicated build environment before release approval. | DevOps / Platform |
| R6 | The repo has exact dependency pins but no lockfile or vulnerability scan, leaving dependency risk unverified. | Medium | Medium | Add a lockfile or hash-based dependency artifact and run a vulnerability scan before release gating. | Security / Platform |

## Worst open risk
The most severe issue is R1. The application will silently fall back to SQLite and local in-memory cache if production dependencies are not configured, which is not a safe production posture for a payment-related service.

## Conclusion
The service is not release-ready while high-severity operational risks remain open. The baseline issue is not code correctness alone; it is configuration integrity and operational proof.
