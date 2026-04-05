---
name: finish-worktree-delivery
description: "Use when `plan-review-worktree-delivery` has already stopped after implementation and you now want the follow-up git flow: commit the delivery branch, create a safety ref, refresh the base ref, rebase the delivery branch onto that refreshed base ref, verify the result, and optionally remove the delivery worktree while keeping the delivery branch for later integration."
---

# Finish Worktree Delivery

## Overview

Finish a completed implementation worktree by committing the actual work on the delivery branch, preserving a recovery point, refreshing the base ref, rebasing the delivery branch onto that base ref, verifying the result, and cleaning up only the worktree if the branch is ready to keep for later integration.

**Core principle:** keep the base branch authoritative, rebase only the delivery branch, create a safety ref before rewriting history, and do not delete the branch as part of the normal finish flow.

This skill starts where `plan-review-worktree-delivery` stops.

## OpenCode / OMO Compatibility

This skill owns the post-implementation git follow-up only: commit, safety-ref creation, delivery-branch rebase, conflict resolution, verification, and optional worktree cleanup.

It does not override repository rules for:
- commit message policy
- protected branches
- merge-only or squash-only delivery policies
- push, PR, release, or deployment steps

If the repository already has a stronger helper for worktree cleanup or branch delivery, use it only when it preserves the same one-way sequence and outcome.

## Working Terms

Use these names consistently:
- `base branch`: the canonical integration branch recorded in the earlier approved plan or handoff, such as `main`
- `base ref`: the refreshed remote-tracking ref or repo-approved equivalent that represents the current source of truth for the base branch
- `delivery branch`: the named branch checked out in the implementation worktree created for the completed task
- `delivery worktree`: the worktree path that contains the completed implementation
- `safety ref`: the backup ref created before any history rewrite so the pre-rebase state is recoverable

Preferred source of truth for the mapping:
1. the earlier handoff report from `plan-review-worktree-delivery`
2. the approved plan plus the recorded worktree inventory
3. explicit user confirmation when the recorded artifacts are incomplete

Default assumption:
- the base branch remains authoritative and is not rewritten by this skill
- the delivery branch is short-lived but retained after this skill unless a later, explicit cleanup step removes it
- the delivery worktree is optional to keep after verification; the branch is not

Concrete example:
- base branch: `main`
- base ref: `origin/main`
- delivery branch: `feat/provider-endpoint-redesign`
- required sequence: commit `feat/provider-endpoint-redesign`, create a safety ref, fetch `origin/main`, rebase `feat/provider-endpoint-redesign` onto `origin/main`, verify, optionally remove the delivery worktree, keep `feat/provider-endpoint-redesign`

## Step 1: Inspect the Handoff Before Any Git Side Effects

Before committing or rebasing, identify and record:
- the approved plan path and handoff report from `plan-review-worktree-delivery`, if available
- the repository root
- which artifact establishes the base and delivery mapping for this handoff
- the base branch name, base ref, and base SHA from the earlier handoff
- the delivery branch name, delivery worktree path, and current `HEAD`
- whether the current checkout is the delivery worktree or something else
- whether the delivery branch is local-only or already published
- the safety ref format expected by the approved plan
- whether the delivery branch is detached, unborn, or already rebasing
- whether the repository has a helper or local skill for worktree cleanup
- whether submodules were edited and, if so, which base-and-delivery mapping each one is using
- the exact verification commands that were promised before the earlier skill stopped

Load `references/delivery-artifacts.md` when you need a reusable preflight checklist or final report template.

Useful commands:

```bash
git branch --show-current
git status --short
git worktree list --porcelain
git rev-parse HEAD
git rev-parse <base-ref>
```

Do not proceed if:
- the base and delivery mapping is ambiguous
- the current checkout alone is the only evidence for which base ref should be used
- the delivery branch is detached
- the base ref is unclear or stale
- the delivery branch is already published and the later rewrite policy is unclear
- unrelated local changes would get mixed into the handoff
- a merge-only or squash-only repo policy means this rebase-based skill is the wrong tool

## Step 2: Finalize Touched Submodules First When They Changed

If the task changed submodule code, finish each touched submodule before rebasing the superproject delivery branch.

Per touched submodule:
1. Confirm the submodule base branch, base ref, delivery branch, and intended gitlink outcome from the earlier handoff.
2. Commit the intended submodule changes on the submodule delivery branch.
3. Record the submodule pre-rewrite SHA.
4. Create the submodule safety ref.
5. Refresh the submodule base ref.
6. Rebase the submodule delivery branch onto the refreshed submodule base ref.
7. Rerun the promised submodule verification.

After finishing the touched submodules:
- return to the superproject delivery worktree
- update the gitlinks to the finished submodule SHAs
- stage the updated gitlinks together with any related superproject file changes
- create or refresh the superproject delivery-branch commit so it records those exact submodule SHAs

If the repository explicitly allows a detached submodule state, record the exact exception, the exact SHA, and why detached delivery is valid here.

## Step 3: Commit the Delivery Branch Before Any Rebase

Commit the completed implementation before any history rewrite.

Rules:
- commit inside the delivery worktree
- stage only the intended task changes
- follow the repository's normal commit message style
- record the resulting commit SHA for every repo surface you commit

If submodule code did not change, create the delivery-branch commit now.

Do not start rebasing with uncommitted delivery changes still present.

## Step 4: Create the Safety Ref and Refresh the Base Ref

Preserve a recovery point before rewriting history.

Minimum expectations:
- record the current delivery-branch SHA before rebasing
- create the safety ref required by the approved plan
- refresh the base ref from the remote or repo-approved source without switching the caller's main checkout just to do so
- record the refreshed base SHA before starting the rebase

Example only:

```bash
git branch <safety-ref> HEAD
git fetch origin
git rev-parse origin/<base-branch>
```

If the delivery branch is already published, continue only when the approved plan or repository policy explicitly allows a later `--force-with-lease` publication. If that rule is absent, stop.

## Step 5: Rebase the Delivery Branch Onto the Refreshed Base Ref

Rebase only the delivery branch.

Example only:

```bash
git rebase origin/<base-branch>
```

Concrete example:

```bash
git rebase origin/main
```

During conflict resolution:
- resolve conflicts on the delivery branch only
- rerun the narrowest promised verification after meaningful conflict resolution
- if a submodule gitlink conflicts, inspect the candidate SHAs directly instead of choosing by guesswork
- record whether the rebase completed cleanly, was aborted, or needed `ORIG_HEAD` or reflog-based recovery
- if the branch graph no longer matches the intended one-way model, stop instead of improvising a second rebase

This skill does not rebase the base branch and does not perform reciprocal branch alignment.

## Step 6: Verify the Final State Before Cleanup

Before removing anything, gather fresh evidence for:
- the base ref used for the rebase
- the refreshed base SHA before rebase
- the safety ref that preserves the pre-rewrite delivery state
- the delivery SHA before rebase
- the delivery SHA after rebase
- the verification commands rerun after the rebase
- any conflicts that were resolved and why
- whether the delivery worktree is clean enough to remove

Useful checks:

```bash
git rev-parse HEAD
git status --short
git merge-base --is-ancestor <base-ref> HEAD
```

If submodules were in scope, also confirm:
- the final submodule commit SHA for each touched submodule
- the delivery branch or approved exception used in each touched submodule
- the superproject gitlink now points at the intended finished submodule commit

Do not clean up until the final state is reproducible from recorded SHAs and backup refs.

## Step 7: Optionally Remove the Delivery Worktree

Clean up only after the final state is verified.

Order:
1. Leave the delivery worktree so you are not standing inside the directory you are about to remove.
2. Remove the delivery worktree with the repository helper when one exists.
3. Otherwise use the repository-approved manual worktree removal flow.
4. Keep the delivery branch in place for later integration, review, or publication.

Example only:

```bash
git worktree remove <delivery-worktree-path>
```

Concrete example:

```bash
git worktree remove ../<repo>-feat-provider-endpoint-redesign
```

Use force only when the repository already expects it and you have verified no uncommitted work is being discarded.

Do not:
- delete the delivery branch in this skill
- rewrite the base branch
- remove a worktree that still contains uncommitted changes you need
- push, tag, or open a PR unless the user explicitly asks

## Required Outputs

Before calling this workflow complete, report:
- approved plan path, if known
- base branch and base ref used for the rebase
- refreshed base SHA before rebase
- delivery branch name, delivery worktree path, and final SHA
- safety ref created before rewrite
- commit SHAs created during the handoff
- rebase results, including any conflicts resolved or rollback mechanisms used
- verification commands run and their status
- worktree cleanup result
- explicit note that the delivery branch was retained
- explicit note that push, PR, release, and deployment steps were not performed unless the user asked for them

## Core Rules

1. Derive the base-and-delivery mapping from the earlier handoff first, not from guesswork.
2. Commit the delivery work before any rebase.
3. Create the safety ref before any history rewrite.
4. Refresh the base ref before rebasing.
5. Rebase only the delivery branch onto the refreshed base ref.
6. Verify with exact SHAs before cleanup.
7. Remove only the delivery worktree in the normal finish flow.
8. Keep the delivery branch for later integration or publication.
9. Handle submodule commits and gitlinks explicitly when submodules changed.

## Anti-Patterns

- rebasing before the delivery work is committed
- rebasing the base branch
- performing reciprocal rebases between two branches
- skipping the safety ref before rewriting history
- relying on the current checkout alone to decide the base ref
- deleting the delivery branch in the normal finish flow
- deleting the worktree before the final branch state is proven
- treating a submodule gitlink conflict like a normal text conflict
- pushing a rewritten published branch without an explicit `--force-with-lease` policy

## Stop Conditions

Stop and ask a targeted question when:
- the delivery branch is detached or unborn
- the base-to-delivery mapping is unclear
- the earlier handoff is missing and the current repo state still does not prove the right base ref
- the delivery branch is already published and the rewrite policy is unclear
- the repository requires merge, cherry-pick, or squash delivery instead of rebase
- the delivery worktree contains unrelated local changes and it is unclear whether they belong in scope
- cleanup would require force and you cannot prove the needed work is already preserved elsewhere
- a touched submodule does not have a clear base-and-delivery mapping

## Remember

- Start from the earlier handoff when one exists
- Keep the base branch authoritative
- Commit in the delivery worktree first
- Create the safety ref before rebasing
- Refresh the base ref before rebasing
- Rebase the delivery branch onto the refreshed base ref
- Verify exact SHAs and rerun promised checks
- Remove only the delivery worktree if cleanup is desired
- Keep the delivery branch for later integration
