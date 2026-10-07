# Dependency Checks

## Dependency inventory
The app pins the runtime and test dependencies in `app/requirements.txt`:

```text
Flask==3.0.2
gunicorn==22.0.0
pytest==8.0.2
```

## Assessment
### Pinning
This is a strong best-practice baseline because exact versions are pinned instead of using ranges.

### Compatibility
The architecture document states this project targets Python 3.11. The dependency check was validated with the installed Python runtime available in this environment, and the package set installed successfully. The app itself also ran successfully with live health checks.

### Risk note
There is no lock file, no hash-based dependency lock, and no package vulnerability scan attached to the repo. That means the exact dependency set is controlled, but the repo does not yet prove it is free of known CVEs.

## Verification evidence
Command run:
```bash
python -m pip install -r app/requirements.txt
```
Result: successful installation of all listed packages.

## Conclusion
The dependency set is pinned and installable, but it is not yet fully hardened for production because the repo lacks a vulnerability scan (for example, `pip-audit`, `trivy`, or a GitHub dependency scan) and no lockfile/hashes are attached to the release package.
