---
name: plan-review-worktree-delivery
description: Use when a requirement is ready for implementation and you want a gated workflow where @Prometheus drafts the implementation plan, @Momus critiques it until approval, the approved plan is saved in the repository, and the work is implemented in a fresh git worktree, committed, rebased onto the base branch, and cleaned up afterward.
---

# Plan Review Worktree Delivery

## Overview

Turn a ready requirement into an approved plan and a clean implementation branch without mixing planning noise into the main checkout.

**Core principle:** Draft hard, critique harder, save the approved artifact, then implement in isolation.

## OpenCode / OMO Compatibility

This skill owns the sequencing for plan drafting, critic review, plan persistence, worktree execution, rebasing, and worktree cleanup. It does not override repository-specific rules for verification, approvals, commits, or release steps.

If the environment supports delegation, use a dedicated planner and one or more independent critics. If delegation is unavailable, emulate the same roles locally and keep the outputs clearly separated.

## When to Use

Use this skill when:
- the requirement is clear enough to implement
- the user wants a formal planning loop before code changes
- the workflow names `@Prometheus`, `@Momus`, plan approval, worktrees, rebasing, or cleanup
- the change is large enough that a saved implementation plan will reduce risk

Do not use this skill when:
- the requirement is still ambiguous or missing acceptance criteria
- the work is trivial enough that a written multi-round plan would add more ceremony than value
- the user explicitly wants direct implementation without a critic loop

## The Iron Law

```text
NO IMPLEMENTATION WORKTREE BEFORE MOMUS APPROVES A SAVED PLAN
```

If implementation materially changes the approved approach, update the plan and run the Momus review loop again before continuing.

## Step 1: Inspect Repository Facts Before Planning

Before drafting anything, inspect the target repository and determine:
- the expected base branch (`main`, `master`, or repo-specific default)
- whether the repo already has a plan directory or naming convention
- whether the repo already has a worktree helper, wrapper script, or local skill
- which verification commands actually prove the work is complete
- whether the current checkout is clean enough to create a worktree safely

Prefer local repo conventions over inventing new ones.

## Step 2: Ask Prometheus for the Initial Plan

Use `@Prometheus` as the planner role.

Give Prometheus:
- the requirement
- repo facts from Step 1
- constraints, deadlines, or non-goals
- expected verification surfaces
- any file, API, or architecture clues already known

Require the plan to include:
- goal and scope
- assumptions and open risks
- file or subsystem touch points
- implementation sequence
- verification strategy
- rollback or recovery notes

Load `references/plan-artifacts.md` for a reusable planner prompt and approved-plan template.

## Step 3: Run the Momus Audit Loop

Use `@Momus` as the critic role. For higher-risk work, use multiple independent Momus passes instead of one shared critic.

Require every Momus review to return:
- `APPROVE` or `REVISE`
- blocking issues
- hidden risks or missing verification
- any scope mismatch between the requirement and the plan
- concrete revision guidance

Revision loop:
1. Send the current draft to Momus.
2. Collect blocking issues only. Do not treat stylistic preferences as blockers.
3. Send the issues back to Prometheus for a revised plan.
4. Repeat until all active Momus critics approve.

If the loop does not converge after three revision rounds, stop and surface the real decision to the user instead of cycling forever.

## Step 4: Save the Approved Plan to the Repository

Persist the approved plan before creating a worktree.

Plan location order:
1. Use the repository's existing planning directory if one exists.
2. Otherwise create `plans/` at the repo root.
3. Use a durable filename such as `plans/YYYY-MM-DD-<requirement-slug>.md`.

Save the final approved plan only. Do not save raw critic chatter unless the repo already expects review logs.

At minimum, the saved plan must capture:
- requirement summary
- approved implementation steps
- verification plan
- risks and rollback notes
- approval record
- intended base branch and worktree branch name

Use the template in `references/plan-artifacts.md` when the repo does not already define its own plan format.

## Step 5: Create the Implementation Worktree

Create the worktree only after the plan is approved and saved.

Workflow:
1. Refresh the base branch from the remote.
2. Create a branch name that matches repo conventions.
3. Use the repo's existing worktree helper if it has one.
4. Otherwise use native git worktree commands.

Example only:
```bash
git fetch origin
git checkout <base-branch>
git pull --ff-only origin <base-branch>
git worktree add ../<repo>-<branch-slug> -b <branch-name> <base-branch>
```

Record the chosen branch name and worktree path in the approved plan if they were not known earlier.

## Step 6: Implement Against the Approved Plan

Inside the worktree:
- use the approved plan as the execution checklist
- keep edits scoped to the requirement
- update the plan if implementation reveals a material change in approach
- rerun the Momus approval loop for material plan changes
- follow any repo-specific implementation skills that apply

Do not silently drift away from the approved plan.

## Step 7: Verify Before Committing

Run the repository-appropriate verification before claiming the work is ready to commit.

Minimum checks:
- the verification steps promised in the approved plan
- any repo-mandated tests, builds, or manual checks
- a quick re-read of the plan to confirm every committed change still maps back to approved scope

If verification fails, fix the real issue before creating the commit.

## Step 8: Commit and Rebase Cleanly

Create a focused commit in the worktree. Then rebase the branch onto the latest base branch.

Example only:
```bash
git add <files>
git commit -m "<type>: <summary>"
git fetch origin
git rebase origin/<base-branch>
```

When conflicts happen:
1. Resolve the conflicts deliberately instead of blindly taking one side.
2. Re-read the approved plan before finalizing the conflict resolution.
3. Re-run the relevant verification after the rebase completes.
4. Update the saved plan only if the final post-rebase implementation materially differs from the approved version.

Do not treat a conflict-free rebase as proof that the change is still correct.

## Step 9: Remove the Worktree After Completion

Remove the worktree only after:
- the implementation commit exists
- any required post-rebase verification passes
- there are no uncommitted changes left in the worktree

Prefer the repo's helper when one exists. Otherwise use native git worktree removal.

Example only:
```bash
git worktree remove <worktree-path>
git worktree prune
```

Delete the worktree. Do not delete the branch unless the user explicitly asks for branch cleanup too.

## Required Outputs

Before calling this workflow complete, produce:
- an approved plan file saved in the repository
- a committed implementation branch based on that plan
- verification evidence from the worktree state you are about to keep
- confirmation that the worktree was removed cleanly

## Anti-Patterns

- implementing before the Momus loop approves the plan
- saving every draft instead of only the approved plan
- creating a worktree from a stale base branch
- rebasing before the first meaningful commit exists
- resolving conflicts without re-checking the approved plan
- deleting a worktree that still contains uncommitted changes
- deleting the branch when the user only asked for worktree cleanup

## Stop Conditions

Stop and ask a targeted question when:
- the requirement is not actually implementation-ready
- the base branch or repo workflow is unclear
- the verification command is unknown
- the critic loop exposes unresolved product decisions
- the worktree contains uncommitted changes at cleanup time
