# Deployment Ownership

## Required owners for the deploy window

### 1. Release Owner
- Owns the release decision and final approval gate
- Confirms the candidate version and deployment window
- Authorizes the production rollout and rollback decision
- Responsible for communication to stakeholders

### 2. SRE / Operational Lead
- Owns rollout health and the production runbook
- Validates service health, alerts, and deployment progress
- Coordinates rollback action when service health is degraded

### 3. On-Call Support
- Monitors alerts and error dashboards during the deployment window
- Validates early traffic behavior and service stability
- Escalates to the release owner if any critical issue is observed

### 4. Escalation path
1. On-call support identifies an incident or service degradation
2. SRE/Operational Lead validates and determines rollback or mitigation
3. Release Owner approves and communicates the production decision
4. Engineering owner is engaged for patch or mitigation if root cause is code-related

## Approval gate
Production deployment should require:
- release owner approval
- SRE validation of config and health checks
- explicit rollback readiness sign-off
- final notification of the deploy window owner

## Conclusion
The deployment is not ready to be released without a named owner, a support path, and a pre-approved rollback decision. This is a documented operational control requirement and should be enforced before any production promotion.
