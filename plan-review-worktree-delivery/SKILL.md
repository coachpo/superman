---
name: plan-review-worktree-delivery
description: Use when a requirement is ready for implementation and you want every task to begin by creating a git worktree, then have @Prometheus draft the plan, @Momus critique it until approval, save the approved plan in the repository, and implement inside that worktree. Supports repositories with or without git submodules, including submodule-aware planning, submodule initialization in the worktree, and a required stop point immediately after implementation with a complete report of every exact revision, branch-or-detached state, and worktree used.
---

# Plan Review Worktree Delivery

## Overview

Start every qualifying task by isolating it in a worktree, then do the planning and implementation flow from that isolated checkout.

**Core principle:** Isolate first, critique hard, save the approved artifact, then implement against it.

If the repository has submodules or monorepo workspace boundaries, treat them as explicit planning and verification surfaces, not invisible implementation details.

## OpenCode / OMO Compatibility

This skill owns the sequencing for worktree creation, plan drafting, critic review, plan persistence, implementation, and stop-point reporting. It does not override repository-specific rules for commits, releases, or later cleanup workflows because it stops before those steps.

If the environment supports delegation, use a dedicated planner and one or more independent critics. If delegation is unavailable, emulate the same roles locally and keep the outputs clearly separated.

## When to Use

Use this skill when:
- the requirement is clear enough to implement
- the user wants a formal planning loop before code changes
- the workflow names `@Prometheus`, `@Momus`, plan approval, worktrees, or a stop-after-implementation handoff
- the change is large enough that a saved implementation plan will reduce risk

Do not use this skill when:
- the requirement is still ambiguous or missing acceptance criteria
- the work is trivial enough that a written multi-round plan would add more ceremony than value
- the user explicitly wants direct implementation without a critic loop

## The Iron Law

```text
CREATE THE WORKTREE FIRST
IMPLEMENT ONLY AFTER MOMUS APPROVES A SAVED PLAN
STOP AS SOON AS IMPLEMENTATION IS DONE
```

Do not draft the real task plan in the main checkout, do not implement before the approved plan exists, and do not continue into commit, rebase, cleanup, or branch-deletion steps inside this skill.

If implementation materially changes the approved approach, update the plan and run the Momus review loop again before continuing.

## Step 1: Create the Task Worktree First

Create the worktree before Prometheus drafts anything.

Before creating it, determine:
- whether the repo already has a worktree helper, wrapper script, or local skill
- whether `main` is the correct base branch for this repo, or whether the repo clearly uses a different default branch
- whether the repo has git submodules and whether this task touches any of them
- whether the repo is a monorepo and which workspace root, packages, or apps are in scope
- whether the current checkout is clean enough to create a worktree safely
- which verification commands will later prove the work is complete

Workflow:
1. Refresh the base ref from the remote unless the repo clearly requires a different base branch.
2. Choose a task branch name that matches repo conventions.
3. Use the repo's existing worktree helper if it has one, especially if it already handles submodule setup, dependency bootstrap, or environment copying.
4. Otherwise create the worktree from a fresh remote or repo-approved base ref without switching the caller's active checkout just to refresh that base.
5. If the repo has submodules, initialize them inside the new worktree before planning against that checkout.
6. If the repo is a monorepo, run any required bootstrap or install step inside the new worktree before planning against that checkout, and record the exact scoped verification commands you expect to use later.

Example only:
```bash
git fetch origin
git worktree add -b <branch-name> ../<repo>-<branch-slug> origin/<base-branch>
cd ../<repo>-<branch-slug>
git submodule update --init --recursive
# If the repo is a monorepo, run the repo-approved bootstrap/install step here.
```

Record the resolved base ref, branch name, and worktree path immediately.

If the repo has submodules, also record:
- whether the task changes the superproject only, one submodule, or multiple submodules
- which submodules were initialized in the worktree
- the exact commit SHA checked out in each initialized or touched submodule
- whether each submodule is attached to a branch or left on a detached `HEAD`
- whether the superproject gitlink currently points at that same SHA for each touched submodule
- whether any submodule-specific branch or worktree is already required

If the repo is a monorepo, also record:
- the workspace root in scope
- the packages or apps in scope
- whether bootstrap or install already ran in the new worktree
- the exact scoped build, test, and manual verification commands that will prove the touched surface is correct

Prometheus and Momus should review the task with that isolation context already established.

## Step 2: Ask Prometheus for the Initial Plan

Use `@Prometheus` as the planner role.

Give Prometheus:
- the requirement
- repo facts from Step 1
- the resolved base ref
- the chosen worktree path
- the chosen task branch name
- any submodules in scope
- any monorepo workspace root, packages, or apps in scope
- any required bootstrap or install step for the new worktree
- constraints, deadlines, or non-goals
- expected verification surfaces and exact scoped verification commands
- any file, API, or architecture clues already known

Require the plan to include:
- goal and scope
- assumptions and open risks
- file, subsystem, workspace, or package touch points
- superproject versus submodule responsibilities
- monorepo workspace or package responsibilities when applicable
- implementation sequence
- bootstrap or install expectations for the worktree
- verification strategy
- branch, worktree, and exact revision tracking expectations
- rollback or recovery notes

Load `references/plan-artifacts.md` for a reusable planner prompt and approved-plan template.

## Step 3: Ask Momus to Review and Audit the Plan

Use `@Momus` as the critic role. For higher-risk work, use multiple independent Momus passes instead of one shared critic.

Require every Momus review to return:
- `APPROVE` or `REVISE`
- blocking issues
- hidden risks or missing verification
- any scope mismatch between the requirement and the plan
- any missing submodule revision or gitlink handling if the repo uses submodules
- any missing monorepo workspace scope, bootstrap, or scoped verification if the repo is a monorepo
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
- the resolved base ref, created task branch name, and worktree path
- any monorepo workspace root, packages or apps in scope, and required bootstrap or install steps
- any submodules in scope, their expected SHA or gitlink state, and how they will be handled
- the expected final branch, worktree, and revision inventory format

Use the template in `references/plan-artifacts.md` when the repo does not already define its own plan format.

## Step 6: Implement the Approved Plan in the Worktree

Inside the worktree:
- use the approved plan as the execution checklist
- keep edits scoped to the requirement
- update the plan if implementation reveals a material change in approach
- rerun the Momus approval loop for material plan changes
- follow any repo-specific implementation skills that apply

If the repo is a monorepo:
- keep the plan explicit about the workspace root and packages or apps in scope
- run the repo-required bootstrap or install steps for that scope before implementation when needed
- verify the touched scope with the exact commands promised in the approved plan instead of falling back to a generic repo-root check

If the repo uses submodules:
- keep the plan explicit about whether you are editing the superproject, submodule contents, or both
- initialize and inspect submodules before editing them
- avoid leaving unrelated submodules dirty
- record every submodule exact revision, branch-or-detached state, gitlink state, and worktree you actually use during implementation

Do not silently drift away from the approved plan.

## Step 7: Stop After Implementation and Report What Was Used

When implementation is done, stop inside the worktree workflow. Do not commit, rebase, delete the worktree, delete branches, or move into release flow from this skill.

Before stopping, run the repository-appropriate verification promised in the approved plan.

Minimum checks:
- the verification steps promised in the approved plan
- any repo-mandated tests, builds, or manual checks
- for monorepos, the exact scoped verification promised for each touched package, app, or affected-project surface
- a quick re-read of the plan to confirm every implemented change still maps back to approved scope

If submodules are in scope, verify the right level:
- run the required checks inside each touched submodule when the task changes submodule code
- confirm the exact SHA in each touched or initialized submodule and whether the superproject gitlink matches it
- confirm no unrelated submodules were touched accidentally
- confirm the branch, detached-state, worktree, and revision inventory for submodules is complete

If verification fails, fix the real issue before stopping.

Then produce a stop report that includes every branch, worktree, and exact revision used during the task.

Required stop report fields:
- approved plan path
- resolved base ref used
- main repo branch used
- main repo worktree path used
- any additional main-repo branches or worktrees used
- if the repo is a monorepo:
  - workspace root in scope
  - packages or apps in scope
  - bootstrap or install commands run in the worktree
- for each submodule used:
  - submodule path
  - exact commit SHA used
  - branch used or explicit detached `HEAD` state
  - worktree path used
  - whether the superproject gitlink points at that SHA
  - whether it was modified
- verification commands run and their status
- explicit statement that the skill stopped before commit, rebase, and cleanup

If no submodule branches, detached states, or extra worktrees were used, say so explicitly instead of omitting the section.

## Required Outputs

Before calling this workflow complete, produce:
- an approved plan file saved in the repository
- implementation status from the active worktree state
- verification evidence from the worktree state you are about to leave in place
- a complete branch/worktree inventory for the main repo
- scoped verification evidence for each monorepo package or app in scope when applicable
- a complete branch, worktree, and revision inventory for every submodule used
- explicit statement that commit, rebase, and cleanup were not performed by this skill

## Anti-Patterns

- planning from the main checkout when this workflow applies
- creating the worktree late instead of first
- implementing before the Momus loop approves the plan
- saving every draft instead of only the approved plan
- creating a worktree from a stale base ref
- refreshing the base ref by switching the caller's active checkout when a direct fetch plus worktree creation would do
- ignoring `.gitmodules` or touched submodules during planning
- approving a monorepo plan without naming workspace scope, bootstrap requirements, and scoped verification commands
- failing to record which branches and worktrees were actually used
- editing submodule code without recording the exact submodule SHA and whether the superproject gitlink points at it
- continuing into commit, rebase, or cleanup after implementation is complete
- leaving the stop report vague about what remains undone

## Stop Conditions

Stop and ask a targeted question when:
- the requirement is not actually implementation-ready
- the `main` branch assumption appears wrong for this repo
- the verification command is unknown
- the repo is a monorepo and the workspace scope or bootstrap and verification commands are unknown
- the critic loop exposes unresolved product decisions
- the repo has submodules and it is unclear whether the task should modify submodule code, the parent repo pointer, or both
- the repo has submodules and it is unclear which exact submodule SHAs, gitlinks, branches, or detached states were used
- the branch/worktree inventory is incomplete
