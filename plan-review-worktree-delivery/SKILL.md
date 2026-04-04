---
name: plan-review-worktree-delivery
description: Use when a requirement is ready for implementation and you want every task to begin by creating a git worktree, then have @Prometheus draft the plan, @Momus critique it until approval, save the approved plan in the repository, implement inside that worktree, and handle clean post-commit flow. Supports repositories with or without git submodules, including planning for touched submodules, submodule initialization in the worktree, submodule commit strategy, rebasing onto `main` after user approval, and worktree cleanup.
---

# Plan Review Worktree Delivery

## Overview

Start every qualifying task by isolating it in a worktree, then do the planning and implementation flow from that isolated checkout.

**Core principle:** Isolate first, critique hard, save the approved artifact, then implement against it.

If the repository has submodules, treat them as explicit planning and verification surfaces, not invisible implementation details.

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
CREATE THE WORKTREE FIRST
IMPLEMENT ONLY AFTER MOMUS APPROVES A SAVED PLAN
```

Do not draft the real task plan in the main checkout, and do not implement before the approved plan exists.

If implementation materially changes the approved approach, update the plan and run the Momus review loop again before continuing.

## Step 1: Create the Task Worktree First

Create the worktree before Prometheus drafts anything.

Before creating it, determine:
- whether the repo already has a worktree helper, wrapper script, or local skill
- whether `main` is the correct base branch for this repo, or whether the repo clearly uses a different default branch
- whether the repo has git submodules and whether this task touches any of them
- whether the current checkout is clean enough to create a worktree safely
- which verification commands will later prove the work is complete

Workflow:
1. Refresh `main` from the remote unless the repo clearly requires a different base branch.
2. Choose a task branch name that matches repo conventions.
3. Use the repo's existing worktree helper if it has one.
4. Otherwise use native git worktree commands.
5. If the repo has submodules, initialize them inside the new worktree before planning against that checkout.

Example only:
```bash
git fetch origin
git checkout main
git pull --ff-only origin main
git worktree add ../<repo>-<branch-slug> -b <branch-name> main
cd ../<repo>-<branch-slug>
git submodule update --init --recursive
```

Record the branch name and worktree path immediately.

If the repo has submodules, also record:
- whether the task changes the superproject only, one submodule, or multiple submodules
- which submodules were initialized in the worktree
- whether any submodule-specific branch or commit strategy is already required

Prometheus and Momus should review the task with that isolation context already established.

## Step 2: Ask Prometheus for the Initial Plan

Use `@Prometheus` as the planner role.

Give Prometheus:
- the requirement
- repo facts from Step 1
- the chosen worktree path
- the chosen task branch name
- any submodules in scope
- constraints, deadlines, or non-goals
- expected verification surfaces
- any file, API, or architecture clues already known

Require the plan to include:
- goal and scope
- assumptions and open risks
- file or subsystem touch points
- superproject versus submodule responsibilities
- implementation sequence
- verification strategy
- commit strategy when submodule content changes are in scope
- rollback or recovery notes

Load `references/plan-artifacts.md` for a reusable planner prompt and approved-plan template.

## Step 3: Ask Momus to Review and Audit the Plan

Use `@Momus` as the critic role. For higher-risk work, use multiple independent Momus passes instead of one shared critic.

Require every Momus review to return:
- `APPROVE` or `REVISE`
- blocking issues
- hidden risks or missing verification
- any scope mismatch between the requirement and the plan
- any missing submodule handling if the repo uses submodules
- concrete revision guidance

## Step 4: Repeat the Revision Cycle Until Momus Approves

Revision loop:
1. Send the current draft to Momus.
2. Collect blocking issues only. Do not treat stylistic preferences as blockers.
3. Send the issues back to Prometheus for a revised plan.
4. Repeat until all active Momus critics approve.

If the loop does not converge after three revision rounds, stop and surface the real decision to the user instead of cycling forever.

## Step 5: Save the Approved Plan to the Repository

Persist the approved plan in the repository after Momus approves it and before implementation starts.

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
- the created `main`-based task branch name and worktree path
- any submodules in scope and how they will be handled

Use the template in `references/plan-artifacts.md` when the repo does not already define its own plan format.

## Step 6: Implement the Approved Plan in the Worktree

Inside the worktree:
- use the approved plan as the execution checklist
- keep edits scoped to the requirement
- update the plan if implementation reveals a material change in approach
- rerun the Momus approval loop for material plan changes
- follow any repo-specific implementation skills that apply

If the repo uses submodules:
- keep the plan explicit about whether you are editing the superproject, submodule contents, or both
- initialize and inspect submodules before editing them
- avoid leaving unrelated submodules dirty
- if a submodule's code changes, treat that submodule commit and the parent repo pointer update as separate, explicit pieces of work

Do not silently drift away from the approved plan.

## Step 7: Commit the Changes, Then Pause for Approval

Before creating the commit, run the repository-appropriate verification promised in the approved plan.

Minimum checks:
- the verification steps promised in the approved plan
- any repo-mandated tests, builds, or manual checks
- a quick re-read of the plan to confirm every committed change still maps back to approved scope

If submodules are in scope, verify the right level:
- run the required checks inside each touched submodule when the task changes submodule code
- verify the parent repo reflects the intended submodule pointer updates
- confirm no unrelated submodule pointers changed accidentally

If verification fails, fix the real issue before creating the commit.

Create a focused commit in the worktree.

If submodule content changes are in scope, do not guess the commit shape. Follow the approved plan's commit strategy and keep the submodule commits and parent repo pointer update explicit.

Example only:
```bash
git add <files>
git commit -m "<type>: <summary>"
```

After the commit exists, stop and ask for explicit user approval before doing either of the remaining irreversible workflow steps:
- rebasing onto `main`
- deleting the worktree

Do not assume prior task approval also approves the post-commit cleanup flow. Ask again after the commit is created.

## Step 8: After User Approval, Rebase and Delete the Worktree

Proceed only after the user explicitly approves the post-commit steps.

First, rebase the branch onto the latest `main`.

Example only:
```bash
git fetch origin
git rebase origin/main
```

When conflicts happen:
1. Resolve the conflicts deliberately instead of blindly taking one side.
2. Re-read the approved plan before finalizing the conflict resolution.
3. Re-run the relevant verification after the rebase completes.
4. Update the saved plan only if the final post-rebase implementation materially differs from the approved version.

If submodules are in scope, also confirm the rebased result still points at the intended submodule commits.

Do not treat a conflict-free rebase as proof that the change is still correct.

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
- explicit user approval for the post-commit rebase and cleanup steps
- confirmation that the worktree was removed cleanly

If submodules were involved, also produce:
- the list of touched submodules
- confirmation that the final parent repo points at the intended submodule revisions

## Anti-Patterns

- planning from the main checkout when this workflow applies
- creating the worktree late instead of first
- implementing before the Momus loop approves the plan
- saving every draft instead of only the approved plan
- creating a worktree from a stale `main`
- ignoring `.gitmodules` or touched submodules during planning
- editing submodule code without an explicit commit strategy
- rebasing after the commit without explicit user approval
- rebasing before the first meaningful commit exists
- resolving conflicts without re-checking the approved plan
- deleting the worktree without explicit user approval
- deleting a worktree that still contains uncommitted changes
- deleting the branch when the user only asked for worktree cleanup

## Stop Conditions

Stop and ask a targeted question when:
- the requirement is not actually implementation-ready
- the `main` branch assumption appears wrong for this repo
- the verification command is unknown
- the critic loop exposes unresolved product decisions
- the repo has submodules and it is unclear whether the task should modify submodule code, the parent repo pointer, or both
- the repo has submodules and the required commit order across parent and submodule repos is unclear
- the user has not yet approved the post-commit rebase and cleanup steps
- the worktree contains uncommitted changes at cleanup time
