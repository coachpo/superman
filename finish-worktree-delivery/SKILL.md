---
name: finish-worktree-delivery
description: "Use when `plan-review-worktree-delivery` has already stopped after implementation and you now want the follow-up git flow: commit the completed work on the intended feature or fix branches, rebase the current HEAD branch onto the corresponding delivery worktree branch, resolve conflicts, rebase the delivery worktree branch onto the updated HEAD branch, and then clean up the finished worktree and its temporary branch."
---

# Finish Worktree Delivery

## Overview

Finish a completed implementation worktree by committing the actual work, synchronizing the long-lived branch and the temporary delivery branch in the requested order, and cleaning up only after both sides line up.

**Core principle:** Commit first, update the branch that must survive, mirror the temporary delivery branch to that updated branch, then remove only the temporary worktree and branch.

This skill starts where `plan-review-worktree-delivery` stops.

## OpenCode / OMO Compatibility

This skill owns the post-implementation git follow-up only: commit, rebase, conflict resolution, branch alignment, and cleanup.

It does not override repository rules for:
- commit message policy
- protected branches
- merge-only policies
- push, PR, release, or deployment steps

If the repository already has a stronger helper for worktree cleanup or branch delivery, use it only when it preserves the same requested sequence and outcome.

## Working Terms

Use these names consistently:
- `surviving branch`: the named branch checked out in the caller's current checkout when this skill begins. This branch stays after cleanup.
- `delivery branch`: the named branch checked out in the implementation worktree created for the completed task. This branch is temporary unless the repository clearly says otherwise.
- `delivery worktree`: the worktree path that contains the completed implementation.
- `corresponding branches`: the branch pair for the main repository, plus any in-scope submodule branch pairs when submodule code changed.

Default assumption:
- the `surviving branch` is the branch the user wants to keep
- the `delivery branch` is the branch the user wants to remove after the handoff

If the repository or user intent clearly says the opposite, stop and confirm before rebasing anything.

## Step 1: Inspect the Handoff Before Any Git Side Effects

Before committing or rebasing, identify and record:
- the approved plan path and stop report from `plan-review-worktree-delivery`, if available
- the repository root
- the `surviving branch` name, path, and current `HEAD`
- the `delivery branch` name, `delivery worktree` path, and current `HEAD`
- whether either side is detached, unborn, or already rebasing
- whether the repository has a helper or local skill for worktree cleanup
- whether submodules were edited and, if so, which branch or detached state each one is using
- the exact verification commands that were promised before the earlier skill stopped

Load `references/delivery-artifacts.md` when you need a reusable preflight checklist or final report template.

Useful commands:

```bash
git branch --show-current
git status --short
git worktree list --porcelain
git rev-parse HEAD
git rev-parse <branch-name>
```

Do not proceed if:
- the branch mapping is ambiguous
- the `surviving branch` is detached
- the `delivery branch` is detached and the repository did not explicitly plan for that
- unrelated changes in the `surviving branch` checkout would get mixed into the handoff

## Step 2: Commit the Delivery Work on the Intended Branches

Commit the completed implementation before any rebase.

Rules:
- commit inside the `delivery worktree` first
- stage only the intended task changes
- follow the repository's normal commit message style
- record the resulting commit SHA for every repo surface you commit

If submodules were edited:
1. Commit each touched submodule on its intended feature or fix branch first.
2. Return to the superproject in the `delivery worktree`.
3. Stage the updated gitlinks together with any superproject file changes.
4. Create the superproject commit that records those exact submodule commits.

Do not start rebasing with uncommitted delivery changes still present.

## Step 3: Rebase the Surviving Branch Onto the Delivery Branch

Move to the checkout that owns the `surviving branch` and rebase it onto the committed `delivery branch`.

Example only:

```bash
git checkout <surviving-branch>
git rebase <delivery-branch>
```

During conflict resolution:
- resolve conflicts in favor of the intended final code, not by blindly taking one side
- rerun the narrowest promised verification after meaningful conflict resolution
- if a submodule gitlink conflicts, inspect the intended submodule commit directly instead of choosing a SHA by guesswork
- record whether the rebase rewrote commits and what final SHA the `surviving branch` now points to

This is the branch that survives cleanup, so make the hard conflict decisions here.

## Step 4: Rebase the Delivery Branch Onto the Updated Surviving Branch

Return to the `delivery worktree` and rebase the `delivery branch` onto the updated `surviving branch`.

Example only:

```bash
git checkout <delivery-branch>
git rebase <surviving-branch>
```

Purpose:
- make the temporary delivery side reflect the final resolved history
- confirm the temporary branch can be removed without losing unique commits
- leave a reproducible branch inventory before cleanup

If this step is a no-op because both branches already point at the same commit, record that fact explicitly.

## Step 5: Verify the Final State Before Cleanup

Before removing anything, gather fresh evidence for:
- the final `surviving branch` SHA
- the final `delivery branch` SHA
- whether the `delivery branch` tip is identical to, or fully contained in, the `surviving branch`
- the verification commands rerun after the rebases
- any conflicts that were resolved and why

Useful checks:

```bash
git rev-parse <surviving-branch>
git rev-parse <delivery-branch>
git merge-base --is-ancestor <delivery-branch> <surviving-branch>
```

If submodules were in scope, also confirm:
- the final submodule commit SHA for each touched submodule
- the branch or detached state used in each touched submodule
- the superproject gitlink now points at the intended submodule commit

Do not clean up until the final state is reproducible from recorded SHAs.

## Step 6: Clean Up the Delivery Worktree and Temporary Branch

Clean up only after the final state is verified.

Order:
1. Leave the `delivery worktree` so you are not standing inside the directory you are about to remove.
2. Remove the `delivery worktree` with the repository helper when one exists.
3. Otherwise use the repository-approved manual worktree removal flow.
4. Delete the `delivery branch` only after confirming the `surviving branch` contains the final work.

Example only:

```bash
git worktree remove <delivery-worktree-path>
git branch -d <delivery-branch>
```

Use force only when the repository already expects it and you have verified no unique work will be lost.

Do not:
- delete the `surviving branch`
- remove a worktree that still contains uncommitted changes you need
- push, tag, or open a PR unless the user explicitly asks

## Required Outputs

Before calling this workflow complete, report:
- approved plan path, if known
- `surviving branch` name, path, and final SHA
- `delivery branch` name, worktree path, and final SHA before deletion
- commit SHAs created during the handoff
- rebase results, including any conflicts resolved
- verification commands run and their status
- cleanup result for the worktree and temporary branch
- explicit note that push, PR, release, and deployment steps were not performed unless the user asked for them

## Core Rules

1. Treat the named branch in the caller's current checkout as the `surviving branch` unless the repository clearly says otherwise.
2. Commit the delivery work before any rebase.
3. Rebase the `surviving branch` onto the `delivery branch` first.
4. Rebase the `delivery branch` onto the updated `surviving branch` second.
5. Verify with exact SHAs before cleanup.
6. Clean up only the temporary delivery worktree and branch.
7. Handle submodule commits and gitlinks explicitly when submodules changed.

## Anti-Patterns

- rebasing before the delivery work is committed
- making conflict decisions in the temporary branch first and hoping the surviving branch works out later
- deleting the worktree before the final branch relationship is proven
- force-deleting the temporary branch without checking whether the surviving branch contains its work
- treating a submodule gitlink conflict like a normal text conflict
- pushing or releasing as part of this skill without explicit user direction

## Stop Conditions

Stop and ask a targeted question when:
- either side that must survive the workflow is detached
- the branch-to-worktree mapping is unclear
- the repository requires merge, cherry-pick, or squash delivery instead of rebase
- the `surviving branch` checkout contains unrelated local changes and it is unclear whether they belong in scope
- cleanup would require force and you cannot prove the unique work is already preserved elsewhere

## Remember

- Start from the earlier stop report when one exists
- Commit in the delivery worktree first
- Rebase the surviving branch onto the delivery branch
- Rebase the delivery branch onto the updated surviving branch
- Verify exact SHAs
- Remove only the temporary worktree and branch
