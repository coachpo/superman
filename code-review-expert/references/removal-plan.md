# Removal and Iteration Plan Template

## Priority Levels

- [ ] **P0**: Immediate removal needed because of security risk, significant cost, or blocked work
- [ ] **P1**: Remove in the current iteration or sprint
- [ ] **P2**: Backlog or next iteration

---

## Safe to Remove Now

### Item: [Name / Description]

| Field | Details |
|-------|---------|
| **Location** | `path/to/file.ts:line` |
| **Rationale** | Why this should be removed |
| **Evidence** | Unused, dead feature flag, deprecated contract, or duplicate path |
| **Impact** | None or low - no active consumers |
| **Deletion steps** | 1. Remove code 2. Remove tests 3. Remove config or docs |
| **Verification** | Run relevant tests, verify no runtime errors, check logs or metrics |

---

## Defer Removal (Plan Required)

### Item: [Name / Description]

| Field | Details |
|-------|---------|
| **Location** | `path/to/file.ts:line` |
| **Why defer** | Active consumers, migration work, or stakeholder sign-off still needed |
| **Preconditions** | Feature flag disabled, telemetry shows no usage, replacement shipped |
| **Breaking changes** | Any contract or API changes that removal would cause |
| **Migration plan** | Steps consumers must take before removal |
| **Timeline** | Target date, sprint, or milestone |
| **Owner** | Person or team responsible |
| **Validation** | Metrics or tests that confirm safe removal |
| **Rollback plan** | How to restore behavior if issues appear |

---

## Checklist Before Removal

- [ ] Searched the codebase for all references
- [ ] Checked for dynamic, reflection-based, or config-driven usage
- [ ] Verified there are no external consumers such as APIs, SDKs, or docs
- [ ] Reviewed feature-flag telemetry if applicable
- [ ] Updated or removed tests
- [ ] Updated documentation
- [ ] Notified the team if the code is shared
