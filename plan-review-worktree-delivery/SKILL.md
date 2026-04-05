---
name: plan-review-worktree-delivery
description: Use when a requirement is ready for implementation and you want every task to begin by creating a git worktree, then have @Prometheus draft the plan, @Momus critique it until approval, save the approved plan in the repository, and implement inside that worktree. This skill stops after implementation with a handoff for a later finish flow that rebases only the delivery branch onto a refreshed base ref.
---

# Plan Review Worktree Delivery

## Overview

Start every qualifying task by creating a branch-backed delivery worktree from a fresh base ref, then do the planning and implementation flow from that isolated checkout.

**Core principle:** isolate first, approve the plan before coding, implement only in the delivery worktree, and stop with a handoff that preserves one authoritative base branch plus one delivery branch.

If the repository has submodules or monorepo workspace boundaries, treat them as explicit planning and verification surfaces, not invisible implementation details.

## OpenCode / OMO Compatibility

This skill owns the sequencing for worktree creation, plan drafting, critic review, plan persistence, implementation, and stop-point reporting. It does not override repository-specific rules for commits, pushes, PRs, releases, or later cleanup workflows because it stops before those steps.

If the environment supports delegation, use a dedicated planner and one or more independent critics. If delegation is unavailable, emulate the same roles locally and keep the outputs clearly separated.

## Working Terms

Use these names consistently:
- `base branch`: the canonical integration branch for this task, such as `main` or a repo-specific equivalent
- `base ref`: the refreshed remote-tracking ref or repo-approved equivalent used to create the delivery worktree
- `delivery branch`: the short-lived task branch created from the base ref and attached to the delivery worktree
- `delivery worktree`: the isolated checkout used for planning, implementation, and later finish-workflow follow-up
- `safety ref`: the backup ref format the finish workflow must create before any history rewrite

Default model:
- the base branch remains authoritative
- the delivery branch is the only branch rebased in the normal finish flow
- the base branch is not rebased by these skills
- real task work does not happen on a detached `HEAD`

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
CREATE THE DELIVERY WORKTREE FIRST
IMPLEMENT ONLY AFTER MOMUS APPROVES A SAVED PLAN
STOP WITH A CLEAN HANDOFF FOR ONE-WAY DELIVERY FINALIZATION
```

Do not draft the real task plan in the main checkout, do not implement before the approved plan exists, and do not continue into commit, rebase, push, PR, release, or cleanup steps inside this skill.

If implementation materially changes the approved approach, update the plan and run the Momus review loop again before continuing.

## Step 1: Create the Delivery Worktree First

Create the delivery worktree before Prometheus drafts anything.

Before creating it, determine:
- whether the repo already has a worktree helper, wrapper script, or local skill
- whether `main` is the correct base branch for this repo, or whether the repo clearly uses a different default branch
- whether the repo has git submodules and whether this task touches any of them
- whether the repo is a monorepo and which workspace root, packages, or apps are in scope
- whether the current checkout is clean enough to create a worktree safely
- which verification commands will later prove the work is complete

Workflow:
1. Refresh the base ref from the remote unless the repo clearly requires a different source of truth.
2. Choose a delivery branch name that matches repo conventions.
3. Use the repo's existing worktree helper if it already handles submodule setup, dependency bootstrap, or environment copying.
4. Otherwise create the delivery worktree from the refreshed base ref without switching the caller's active checkout just to refresh that base.
5. If the repo has submodules, initialize them inside the new worktree before planning against that checkout.
6. If the repo is a monorepo, run any required bootstrap or install step inside the new worktree before planning against that checkout, and record the exact scoped verification commands you expect to use later.
7. If the worktree lands on a detached `HEAD`, stop and attach a named delivery branch before planning or coding.

Example only:
```bash
git fetch origin
git worktree add -b <delivery-branch> ../<repo>-<branch-slug> origin/<base-branch>
cd ../<repo>-<branch-slug>
git submodule update --init --recursive
# If the repo is a monorepo, run the repo-approved bootstrap/install step here.
```

Record the following immediately:
- base branch
- base ref used for worktree creation
- base SHA at worktree creation
- delivery branch
- delivery worktree path
- whether the delivery branch is expected to stay local or may later be published

If the repo has submodules, also record:
- whether the task changes the superproject only, one submodule, or multiple submodules
- which submodules were initialized in the worktree
- for each touched submodule, the expected base branch or base ref, delivery branch, and gitlink outcome
- whether any repo-specific exception allows a detached submodule state during finish, and why that exception is valid

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
- the resolved base branch and base ref
- the base SHA at worktree creation
- the chosen delivery worktree path
- the chosen delivery branch name
- whether the delivery branch is expected to stay local or may later be published
- any submodules in scope
- any monorepo workspace root, packages, or apps in scope
- any required bootstrap or install step for the delivery worktree
- constraints, deadlines, or non-goals
- expected verification surfaces and exact scoped verification commands
- any file, API, or architecture clues already known

Require the plan to include:
- goal and scope
- assumptions and open risks
- the base-and-delivery branch model
- file, subsystem, workspace, or package touch points
- superproject versus submodule responsibilities
- monorepo workspace or package responsibilities when applicable
- implementation sequence
- bootstrap or install expectations for the worktree
- verification strategy
- finish workflow contract for later delivery-branch finalization
- rewrite-safety rules, including backup ref format and rollback path

Load `references/plan-artifacts.md` for a reusable planner prompt and approved-plan template.

## Step 3: Ask Momus to Review and Audit the Plan

Use `@Momus` as the critic role. For higher-risk work, use multiple independent Momus passes instead of one shared critic.

Require every Momus review to return:
- `APPROVE` or `REVISE`
- blocking issues
- hidden risks or missing verification
- any scope mismatch between the requirement and the plan
- any attempt to rebase the base branch later
- any missing rewrite-safety rule, backup ref, or rollback note
- any missing submodule base-and-delivery mapping if the repo uses submodules
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
- the resolved base branch, base ref, and base SHA used for worktree creation
- the created delivery branch name and delivery worktree path
- whether the delivery branch is expected to stay local or may later be published
- any monorepo workspace root, packages or apps in scope, and required bootstrap or install steps
- any submodules in scope, their expected base-and-delivery mapping, and the intended gitlink outcome
- the finish workflow contract, including the safety ref format and the rule that only the delivery branch is rebased later

Use the template in `references/plan-artifacts.md` when the repo does not already define its own plan format.

## Step 6: Implement the Approved Plan in the Delivery Worktree

Inside the delivery worktree:
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
- treat each touched submodule as its own base-and-delivery workflow surface unless the repository documents a stronger rule

Do not silently drift away from the approved plan.

## Rewrite Safety Rules

Before handing off to the finish workflow, make sure the approved plan already defines:
- the exact safety ref format the finish workflow must create before any history rewrite
- the published-branch policy for the delivery branch
- the rollback path if rebase fails or the branch graph looks wrong
- whether any repo-specific rule forbids rebasing and would require a different finish workflow entirely

Do not assume later steps can infer these rules from the current checkout.

## Step 7: Stop After Implementation and Produce the Handoff

When implementation is done, stop inside the delivery worktree workflow. Do not commit, rebase, push, delete the worktree, delete branches, or move into release flow from this skill.

Before stopping, run the repository-appropriate verification promised in the approved plan.

Minimum checks:
- the verification steps promised in the approved plan
- any repo-mandated tests, builds, or manual checks
- for monorepos, the exact scoped verification promised for each touched package, app, or affected-project surface
- a quick re-read of the plan to confirm every implemented change still maps back to approved scope

If submodules are in scope, verify the right level:
- run the required checks inside each touched submodule when the task changes submodule code
- confirm the current commit SHA in each touched submodule
- confirm the intended gitlink outcome is still documented for the superproject handoff
- confirm no unrelated submodules were touched accidentally

If verification fails, fix the real issue before stopping.

Then produce a handoff report that gives the later finish workflow everything it needs.

Required handoff fields:
- approved plan path
- base branch
- base ref used for worktree creation
- base SHA at worktree creation
- delivery branch
- delivery worktree path
- delivery `HEAD` before finish
- whether the delivery branch is local-only or already published
- promised safety ref format
- verification commands run and their status
- if the repo is a monorepo:
  - workspace root in scope
  - packages or apps in scope
  - bootstrap or install commands run in the worktree
- for each touched submodule:
  - submodule path
  - base branch or base ref
  - delivery branch or approved exception
  - exact commit SHA used
  - intended gitlink outcome
  - whether it was modified
- explicit statement that commit, rebase, push, PR, release, and cleanup were not performed by this skill

If no submodule branches or extra worktrees were used, say so explicitly instead of omitting the section.

## Required Outputs

Before calling this workflow complete, produce:
- an approved plan file saved in the repository
- implementation status from the active delivery worktree state
- verification evidence from the delivery worktree state you are about to leave in place
- a complete base-ref and delivery-branch handoff for the main repo
- scoped verification evidence for each monorepo package or app in scope when applicable
- a complete base-and-delivery inventory for every submodule used
- explicit statement that commit, rebase, push, PR, release, and cleanup were not performed by this skill

## Anti-Patterns

- planning from the main checkout when this workflow applies
- creating the worktree late instead of first
- implementing before the Momus loop approves the plan
- saving every draft instead of only the approved plan
- creating a worktree from a stale base ref
- letting the delivery worktree stay on a detached `HEAD`
- refreshing the base ref by switching the caller's active checkout when a direct fetch plus worktree creation would do
- planning a finish flow that rebases the base branch
- planning reciprocal rebases between two branches
- approving a plan without a backup-ref format or rollback path
- ignoring `.gitmodules` or touched submodules during planning
- approving a monorepo plan without naming workspace scope, bootstrap requirements, and scoped verification commands
- defaulting to branch deletion in the later finish flow
- leaving the handoff vague about which branch is authoritative and which branch is disposable

## Stop Conditions

Stop and ask a targeted question when:
- the requirement is not actually implementation-ready
- the base branch assumption appears wrong for this repo
- the verification command is unknown
- the repo is a monorepo and the workspace scope or bootstrap and verification commands are unknown
- the critic loop exposes unresolved product decisions
- the repo has submodules and it is unclear which submodule branch model should be used
- the delivery branch may already be published and the rewrite policy is unclear
- the later finish flow would need to preserve merge commits or follow a merge-only policy instead of rebase
- the base-ref and delivery-branch handoff inventory is incomplete
