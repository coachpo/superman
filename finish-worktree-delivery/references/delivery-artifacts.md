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
- Local base checkout or branch owner for fast-forward:
- Verification commands to rerun:
- Base push policy note:
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
- Commit message summary:
```

## Conflict Notes

When conflicts happen, record only the facts that matter later:

```markdown
## Conflict Notes
- Rebase step: delivery-onto-base
- Files in conflict:
- Resolution summary:
- Verification rerun after resolution:
- Rebase aborted: yes/no
- ORIG_HEAD used: yes/no
- Reflog recovery needed: yes/no
```

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
- Delivery SHA after rebase:
- Delivery branch published status:

## Local Base Fast-Forward
- Local base checkout or branch owner used:
- Local base SHA before fast-forward:
- Local base SHA after fast-forward:
- Fast-forward mode used: ff-only | equivalent safe helper

## Verification
1. <command> - pass/fail
2. <command> - pass/fail

## Publication
- Push requested: yes/no
- Push allowed by repo policy: yes/no
- Push performed: yes/no

## Cleanup
- Delivery worktree removed: yes/no
- Delivery branch deleted: yes/no
- Helper used: none | exact helper

## Explicit Non-Goals
- Base branch rebased: no
- Merge commit used for base update: no
- PR performed: yes/no
- Release or deployment performed: yes/no
```

If cleanup is intentionally deferred, say so explicitly instead of leaving the report silent.
