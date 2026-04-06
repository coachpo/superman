---
name: finish-worktree-delivery
description: "Use when `plan-review-worktree-delivery` has already stopped after implementation and you now want the follow-up git flow: commit the delivery branch, create a safety ref, refresh the base ref, rebase the delivery branch onto that refreshed base ref, verify the result, fast-forward the local base branch, optionally push the base branch when explicitly requested and allowed, and clean up the temporary delivery worktree and branch."
---

# Finish Worktree Delivery

## Overview

Finish a completed implementation worktree by committing the actual work on the delivery branch, preserving a recovery point, refreshing the base ref, rebasing the delivery branch onto that base ref, verifying the rebased result, fast-forwarding the local base branch to that verified delivery commit, optionally pushing the base branch when explicitly requested and allowed, and then cleaning up the temporary delivery artifacts.

**Core principle:** keep the base branch authoritative, rebase only the delivery branch, update the local base branch only by fast-forward semantics, and never default to pushing or keeping the delivery branch as the final state.

This skill starts where `plan-review-worktree-delivery` stops.

## OpenCode / OMO Compatibility

This skill owns the post-implementation git follow-up only: commit, safety-ref creation, delivery-branch rebase, verification, local base fast-forward, optional base push, and cleanup.

It does not override repository rules for:
- commit message policy
- protected branches
- merge-only or squash-only delivery policies
- PR, release, or deployment steps

If the repository already has a stronger helper for worktree cleanup or base-branch delivery, use it only when it preserves the same one-way sequence and outcome.

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
- the delivery branch is temporary and is deleted after the verified local base fast-forward
- the delivery worktree is temporary and is removed during cleanup
- pushing the base branch is optional and requires explicit request plus repo-policy approval

Concrete example:
- base branch: `main`
- base ref: `origin/main`
- delivery branch: `feat/provider-endpoint-redesign`
- required sequence: commit `feat/provider-endpoint-redesign`, create a safety ref, fetch `origin/main`, rebase `feat/provider-endpoint-redesign` onto `origin/main`, verify, fast-forward local `main` to the verified delivery commit, optionally push `main`, then remove `feat/provider-endpoint-redesign` artifacts

## Step 1: Inspect the Handoff Before Any Git Side Effects

Before committing or rebasing, identify and record:
- the approved plan path and handoff report from `plan-review-worktree-delivery`, if available
- the repository root
- which artifact establishes the base and delivery mapping for this handoff
- the base branch name, base ref, and base SHA from the earlier handoff
- the delivery branch name, delivery worktree path, and current `HEAD`
- whether the delivery branch is local-only or already published
- the safety ref format expected by the approved plan
- which clean local checkout or repo-approved owner will fast-forward the base branch later
- whether the delivery branch is detached, unborn, or already rebasing
- whether the repository has a helper or local skill for worktree cleanup
- the exact verification commands that were promised before the earlier skill stopped
- whether repo policy allows an explicit base-branch push after verification

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
- there is no clean local checkout or repo-approved owner available to fast-forward the base branch
- unrelated local changes would get mixed into the handoff
- a merge-only or squash-only repo policy means this rebase-plus-fast-forward skill is the wrong tool

## Step 2: Commit the Delivery Branch Before Any Rebase

Commit the completed implementation before any history rewrite.

Rules:
- commit inside the delivery worktree
- stage only the intended task changes
- follow the repository's normal commit message style
- record the resulting delivery-branch commit SHA

Do not start rebasing with uncommitted delivery changes still present.

## Step 3: Create the Safety Ref and Refresh the Base Ref

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

If the delivery branch is already published, continue only when the approved plan or repository policy explicitly allows this rewrite-based finish flow. If that rule is absent, stop.

## Step 4: Rebase the Delivery Branch Onto the Refreshed Base Ref

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
- record whether the rebase completed cleanly, was aborted, or needed `ORIG_HEAD` or reflog-based recovery
- if the branch graph no longer matches the intended one-way model, stop instead of improvising a second rebase or a merge commit

This skill does not rebase the base branch and does not perform reciprocal branch alignment.

## Step 5: Verify the Rebasing Result and Fast-Forward the Local Base Branch

Before moving the base branch, gather fresh evidence for:
- the base ref used for the rebase
- the refreshed base SHA before rebase
- the safety ref that preserves the pre-rewrite delivery state
- the delivery SHA before rebase
- the delivery SHA after rebase
- the verification commands rerun after the rebase
- any conflicts that were resolved and why

Useful checks:

```bash
git rev-parse HEAD
git status --short
git merge-base --is-ancestor <base-ref> HEAD
```

After the rebased delivery branch is verified:
1. Move to the clean local checkout that owns the base branch.
2. Record the local base SHA before fast-forward.
3. Update the local base branch to the verified delivery commit using `ff-only` semantics or a repo-approved equivalent.
4. Record the local base SHA after fast-forward.
5. Confirm the local base SHA now matches the verified delivery SHA.

Example only:

```bash
git checkout <base-branch>
git merge --ff-only <delivery-branch>
```

If the base branch cannot be updated by fast-forward, stop. Do not create a merge commit and do not rebase the base branch.

## Step 6: Optionally Push the Base Branch

Push only when the user explicitly asked for it and repository policy allows direct base-branch pushes.

Before pushing:
- confirm the local base branch points at the verified delivery commit
- confirm the working tree is clean enough for the repository's normal push flow
- confirm no protected-branch or release rule blocks this push

If any of those checks fail, stop instead of improvising a different publication path.

Do not force-push the base branch as part of this skill.

## Step 7: Clean Up the Temporary Delivery Artifacts

Clean up only after the final state is verified and any explicitly requested base push has completed.

Order:
1. Leave the delivery worktree so you are not standing inside the directory you are about to remove.
2. Remove the delivery worktree with the repository helper when one exists.
3. Otherwise use the repository-approved manual worktree removal flow.
4. Delete the temporary delivery branch after confirming the local base branch now points at the verified delivery commit.

Example only:

```bash
git worktree remove <delivery-worktree-path>
git branch -d <delivery-branch>
```

Concrete example:

```bash
git worktree remove ../<repo>-feat-provider-endpoint-redesign
git branch -d feat/provider-endpoint-redesign
```

Use force only when the repository already expects it and you have verified no uncommitted work is being discarded.

Do not:
- rebase the base branch
- create a merge commit to update the base branch
- retain the delivery branch as the normal final state
- remove a worktree that still contains uncommitted changes you need
- push, tag, or open a PR unless the user explicitly asked for that specific side effect

## Required Outputs

Before calling this workflow complete, report:
- approved plan path, if known
- base branch and base ref used for the rebase
- refreshed base SHA before rebase
- delivery branch name, delivery worktree path, and delivery SHA before and after rebase
- safety ref created before rewrite
- local base SHA before fast-forward and after fast-forward
- commit SHAs created during the handoff
- rebase results, including any conflicts resolved or rollback mechanisms used
- verification commands run and their status
- whether push was requested, allowed, and performed
- cleanup result for the temporary delivery worktree and delivery branch
- explicit note that the base branch was not rebased and was updated only by fast-forward semantics

## Core Rules

1. Derive the base-and-delivery mapping from the earlier handoff first, not from guesswork.
2. Commit the delivery work before any rebase.
3. Create the safety ref before any history rewrite.
4. Refresh the base ref before rebasing.
5. Rebase only the delivery branch onto the refreshed base ref.
6. Verify the rebased delivery branch before moving the base branch.
7. Update the local base branch only by `ff-only` semantics or a repo-approved equivalent.
8. Push the base branch only when explicitly requested and allowed.
9. Clean up only the temporary delivery worktree and temporary delivery branch.

## Anti-Patterns

- rebasing before the delivery work is committed
- rebasing the base branch
- performing reciprocal rebases between two branches
- skipping the safety ref before rewriting history
- relying on the current checkout alone to decide the base ref
- updating the base branch with a merge commit instead of fast-forward only
- retaining the delivery branch as the normal final state
- deleting the worktree before the verified local base fast-forward is complete
- pushing a rewritten branch without the explicit policy the plan requires

## Stop Conditions

Stop and ask a targeted question when:
- the delivery branch is detached or unborn
- the base-to-delivery mapping is unclear
- the earlier handoff is missing and the current repo state still does not prove the right base ref
- the delivery branch is already published and the rewrite policy is unclear
- the repository requires merge, cherry-pick, or squash delivery instead of rebase plus fast-forward
- the delivery worktree contains unrelated local changes and it is unclear whether they belong in scope
- there is no clean local base checkout or repo-approved owner available for fast-forward
- cleanup would require force and you cannot prove the needed work is already preserved elsewhere

## Remember

- Start from the earlier handoff when one exists
- Keep the base branch authoritative
- Commit in the delivery worktree first
- Create the safety ref before rebasing
- Refresh the base ref before rebasing
- Rebase the delivery branch onto the refreshed base ref
- Verify exact SHAs and rerun promised checks
- Fast-forward the local base branch to the verified delivery commit
- Push the base branch only when explicitly requested and allowed
- Remove the temporary delivery worktree and delivery branch when cleanup is complete
