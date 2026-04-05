# Delivery Artifacts

Use this file when you need a reusable preflight checklist, commit log, or final cleanup report for `finish-worktree-delivery`.

## Preflight Handoff Record

Capture this before the first commit or rebase:

```markdown
## Handoff Inputs
- Approved plan path:
- Earlier stop report available: yes/no
- Repository root:
- Branch mapping source: stop report | approved plan + worktree inventory | explicit user confirmation
- Surviving branch:
- Surviving branch path:
- Surviving branch starting SHA:
- Surviving branch upstream ref:
- Delivery branch:
- Delivery worktree path:
- Delivery branch starting SHA:
- Current checkout role: surviving branch checkout | delivery worktree | other
- Touched submodules: none | list
- Verification commands to rerun:
- Cleanup helper: none | exact helper
```

Example mapping:
- Surviving branch: `main`
- Delivery branch: `feat/provider-endpoint-redesign`
- Cleanup target branch: `feat/provider-endpoint-redesign`

Useful commands:

```bash
git branch --show-current
git status --short
git worktree list --porcelain
git rev-parse HEAD
```

## Commit Record

Use this when the handoff creates one or more commits before rebasing:

```markdown
## Commit Record
- Superproject commit:
- Submodule commits:
  - <path>: <sha> on <branch-or-detached-state> | detached delivery approved: yes/no
- Commit message summary:
```

If submodules changed, record the submodule commit before you record the superproject gitlink commit.

## Conflict Notes

When conflicts happen, record only the facts that matter later:

```markdown
## Conflict Notes
- Rebase step: surviving-onto-delivery | delivery-onto-surviving
- Files or gitlinks in conflict:
- Resolution summary:
- Verification rerun after resolution:
```

For submodule gitlink conflicts, record both candidate SHAs and the final chosen SHA.

## Final Follow-Up Report

```markdown
# Worktree Delivery Follow-Up Report

## Inputs
- Approved plan path:
- Surviving branch:
- Delivery branch:
- Delivery worktree path:

## Commits Created
- Superproject:
- Submodules:

## Rebase Results
- Upstream ref pulled for surviving branch:
- Surviving branch SHA after pull:
- Surviving branch final SHA:
- Delivery branch final SHA before deletion:
- Surviving branch rebased onto delivery branch: yes/no
- Delivery branch rebased onto surviving branch: yes/no
- Conflicts resolved: none | summary

## Verification
1. <command> - pass/fail
2. <command> - pass/fail

## Cleanup
- Delivery worktree removed: yes/no
- Delivery branch deleted: yes/no
- Helper used: none | exact helper

## Explicit Non-Goals
- Push performed: yes/no
- PR performed: yes/no
- Release or deployment performed: yes/no
```

If cleanup is intentionally deferred, say so explicitly instead of leaving the report silent.
