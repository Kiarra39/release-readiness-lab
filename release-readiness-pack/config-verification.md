# Configuration Verification

## Objective
Verify that the candidate’s runtime configuration matches the operational requirements defined in the app and Kubernetes manifests.

## Source review
- Application config defaults: `app/app.py`
- Deployment manifest: `k8s/configmap.yaml`
- Architecture doc: `docs/architecture.md`

## Required production variables
According to the app and architecture docs, the service requires:

| Variable | Source | Required for prod | Status | Evidence |
|---|---|---:|---|---|
| `PORT` | `k8s/configmap.yaml` | Yes | Pass | Set to `5000` |
| `LOG_LEVEL` | `k8s/configmap.yaml` | Yes | Pass | Set to `info` |
| `DATABASE_URL` | `app/app.py` + `docs/architecture.md` | Yes | Fail | App uses `os.getenv("DATABASE_URL")` and falls back to SQLite if unset |
| `REDIS_URL` | `app/app.py` + `docs/architecture.md` | Yes | Fail | App uses `os.getenv("REDIS_URL")` and falls back to local in-memory cache if unset |

## Observed behavior in app
From the health endpoint:

```python
if db_url:
    db_status = "connected"
else:
    db_status = "warning_fallback_sqlite"
```

```python
if redis_url:
    redis_status = "connected"
else:
    redis_status = "warning_fallback_local"
```

This means the application is explicitly behaving in a degraded mode when production dependencies are not configured.

## Missing production controls
The following gaps remain:
1. `DATABASE_URL` is missing from `k8s/configmap.yaml`.
2. `REDIS_URL` is missing from `k8s/configmap.yaml`.
3. The config is not sourced from a managed secret or deployment secret store.
4. There is no production-specific `APP_ENV`, `SECRET_KEY`, or deployment identity configuration defined for a full release.

## Recommended production configuration
A compliant production config should reference managed services, not local fallback values. For example:

```yaml
data:
  APP_ENV: "production"
  LOG_LEVEL: "info"
  PORT: "5000"
  DATABASE_URL: "postgresql://...@prod-db.internal:5432/checkout"
  REDIS_URL: "redis://redis.internal:6379/0"
```

These values should be supplied by a secret manager or deployment platform secret store, not committed directly to the repository.

## Conclusion
The application cannot be considered production-ready while `DATABASE_URL` and `REDIS_URL` remain unset in the deployment config. The service is operational only in a degraded fallback mode and must not be promoted without real external dependencies configured.
