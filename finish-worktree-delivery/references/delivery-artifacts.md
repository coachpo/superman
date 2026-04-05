# Delivery Artifacts

Use this file when you need a reusable preflight checklist, commit log, or final cleanup report for `finish-worktree-delivery`.

## Preflight Handoff Record

Capture this before the first commit or rebase:

```markdown
## Handoff Inputs
- Approved plan path:
- Earlier handoff report available: yes/no
- Repository root:
- Base branch:
- Base ref:
- Base SHA from handoff:
- Delivery branch:
- Delivery worktree path:
- Delivery branch starting SHA:
- Delivery branch published status: local-only | already published
- Safety ref to create before rewrite:
- Current checkout role: delivery worktree | other
- Touched submodules: none | list
- Verification commands to rerun:
- Cleanup helper: none | exact helper
```

Example mapping:
- Base branch: `main`
- Base ref: `origin/main`
- Delivery branch: `feat/provider-endpoint-redesign`
- Delivery worktree path: `../repo-feat-provider-endpoint-redesign`

Useful commands:

```bash
git branch --show-current
git status --short
git worktree list --porcelain
git rev-parse HEAD
git rev-parse origin/<base-branch>
```

## Commit Record

Use this when the handoff creates one or more commits before rebasing:

```markdown
## Commit Record
- Delivery branch commit before rebase:
- Submodule commits:
  - <path>: <sha> on <delivery-branch> | approved exception <reason>
- Superproject gitlink refresh commit:
- Commit message summary:
```

If submodules changed, finish each touched submodule first, then record the superproject gitlink update that points at those finished SHAs.

## Conflict Notes

When conflicts happen, record only the facts that matter later:

```markdown
## Conflict Notes
- Rebase step: delivery-onto-base | submodule-delivery-onto-base
- Files or gitlinks in conflict:
- Resolution summary:
- Verification rerun after resolution:
- Rebase aborted: yes/no
- ORIG_HEAD used: yes/no
- Reflog recovery needed: yes/no
```

For submodule gitlink conflicts, record both candidate SHAs and the final chosen SHA.

## Final Follow-Up Report

```markdown
# Worktree Delivery Follow-Up Report

## Inputs
- Approved plan path:
- Base branch:
- Base ref:
- Delivery branch:
- Delivery worktree path:

## Safety and Rewrite
- Safety ref created:
- Delivery SHA before rebase:
- Refreshed base SHA before rebase:
- Delivery branch published status:
- Publication rule if later pushed: force-with-lease | local-only | stop required

## Commits Created
- Delivery branch commit before rebase:
- Submodules:
- Superproject gitlink refresh commit:

## Rebase Results
- Delivery branch rebased onto refreshed base ref: yes/no
- Delivery branch final SHA:
- Conflicts resolved: none | summary
- Rollback used: none | rebase --abort | ORIG_HEAD | reflog

## Verification
1. <command> - pass/fail
2. <command> - pass/fail

## Cleanup
- Delivery worktree removed: yes/no
- Delivery branch retained: yes/no
- Helper used: none | exact helper

## Explicit Non-Goals
- Base branch rewritten: no
- Branch deleted in this skill: no
- Push performed: yes/no
- PR performed: yes/no
- Release or deployment performed: yes/no
```

If cleanup is intentionally deferred, say so explicitly instead of leaving the report silent.
