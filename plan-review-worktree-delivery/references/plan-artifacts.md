# Plan Artifacts

Use this file when you need reusable prompt skeletons or a default approved-plan file format.

## Prometheus Planner Prompt

Example prompt:

```text
You are @Prometheus (Plan Builder).
Draft an implementation plan for this requirement.

Requirement:
<requirement>

Repository facts:
- Base branch: <main-or-repo-specific-default>
- Base ref used for worktree creation: <origin/main | repo-approved remote-tracking ref>
- Base SHA at worktree creation: <sha>
- Delivery branch: <branch-name>
- Delivery worktree path: <worktree-path>
- Published branch expectation: <local-only | may already exist remotely>
- Worktree helper: <helper-or-none>
- Monorepo workspace root: <none | path>
- Packages or apps in scope: <none | list>
- Worktree bootstrap or install: <none | exact commands>
- Submodules in scope: <none | list>
- Submodule branch expectations: <none | for each submodule: base branch/ref, delivery branch, gitlink expectation>
- Finish workflow contract: <commit delivery branch, create safety ref, fetch base ref, rebase delivery branch onto refreshed base ref, verify, optionally remove worktree, retain branch>
- Rewrite safety requirements: <backup ref format, published-branch policy, rollback notes>
- Verification: <exact scoped verification commands or checks>

Constraints:
- <constraints>

Return sections:
1. Goal and scope
2. Assumptions
3. Base and delivery branch model
4. Superproject, submodule, and workspace touch points
5. Step-by-step implementation
6. Verification
7. Finish workflow contract
8. Rewrite safety
9. Risks and rollback
```

## Momus Critic Prompt

Example prompt:

```text
You are @Momus (Plan Critic).
Audit this implementation plan. Approve only if the requirement is fully covered, the workflow keeps the base branch authoritative, the delivery branch is the only rebased branch, the rewrite-safety checkpoints are specific, and the verification is concrete enough to prove completion at the right workspace or package scope.

Requirement:
<requirement>

Current plan:
<plan>

Return exactly:
Verdict: APPROVE | REVISE
Blocking issues:
- <issue or none>
Non-blocking suggestions:
- <suggestion or none>
```

For multiple Momus passes, require every active critic to approve before moving forward.

## Approved Plan Template

Default to the repository's own plan format when one exists. Otherwise use this template.

```markdown
# <Requirement Title>

## Status
Approved

## Requirement Summary
<plain-language summary>

## Scope
- In scope: <items>
- Out of scope: <items>

## Assumptions
- <assumption>

## Base and Delivery Model
- Base branch: <main-or-repo-specific-default>
- Base ref used for worktree creation: <origin/main | repo-approved remote-tracking ref>
- Base SHA at worktree creation: <sha>
- Delivery branch: <branch-name>
- Delivery worktree path: <worktree-path>
- Published branch expectation: <local-only | may already exist remotely>

## Submodule Scope
- In scope: <none | list>
- Superproject-only changes: <yes/no>
- Submodule content changes: <yes/no, which ones>
- Expected submodule branch model: <none | for each submodule: base branch/ref, delivery branch, gitlink expectation>

## Monorepo Scope
- Workspace root: <none | path>
- Packages or apps in scope: <none | list>
- Bootstrap or install in worktree: <none | exact commands>

## Implementation Steps
1. <step>
2. <step>
3. <step>

## Verification
1. <exact scoped repo, workspace, or package check>
2. <exact submodule check if applicable>
3. <manual check if required>

## Finish Workflow Contract
- Commit on delivery branch before any rebase: <yes>
- Create safety ref before any history rewrite: <exact ref format>
- Fetch base ref before rebasing: <exact command or repo helper>
- Rebase direction: <delivery branch onto refreshed base ref only>
- Verification after rebase: <exact commands>
- Worktree cleanup in finish skill: <optional>
- Branch deletion in finish skill: <no>

## Rewrite Safety
- Pre-rewrite backup ref name: <ref>
- Pre-rewrite SHA to preserve: <sha>
- Branch already published: <yes/no>
- Allowed rewrite policy if published: <explicit --force-with-lease policy or stop>
- Rollback notes: <rebase --abort | ORIG_HEAD | reflog>

## Risks and Rollback
- Risk: <risk>
- Rollback: <rollback step>

## Approval Record
- Planner: Prometheus
- Critics: Momus
- Verdict: Approved
- Revision rounds: <count>
- Approval date: YYYY-MM-DD

## Branching
- Base branch: <main-or-repo-specific-default>
- Base ref used for worktree creation: <origin/main | repo-approved remote-tracking ref>
- Base SHA at worktree creation: <sha>
- Delivery branch: <branch-name>
- Delivery worktree path: <worktree-path>

## Stop Point
- Stop after implementation: yes
- Commit in this skill: no
- Rebase in this skill: no
- Worktree cleanup in this skill: no

## Handoff Inventory
- Base branch: <branch-name>
- Base ref used: <origin/main | repo-approved remote-tracking ref>
- Base SHA at worktree creation: <sha>
- Delivery branch: <branch-name>
- Delivery worktree path: <worktree-path>
- Delivery HEAD before finish: <sha>
- Published branch status: <local-only | already published>
- Promised safety ref format: <ref pattern>
- Monorepo scope verified: <none | workspace root and packages/apps>
- Submodules used:
  - <submodule-path>: base <branch/ref>, delivery <branch>, commit <sha>, gitlink expectation <sha>, modified <yes/no>
```

## Approval Checklist

Before calling the plan approved, confirm all of the following:
- the requirement is fully represented in the plan
- the plan names the expected files, systems, or touch points
- the plan keeps the base branch authoritative and never asks later skills to rebase it
- the plan names the delivery branch as the only branch that may be rebased in the normal finish flow
- the plan records the base ref, base SHA, delivery branch, and delivery worktree before implementation starts
- the verification section proves completion instead of hoping for it
- the verification section names exact scoped commands instead of only generic repo-root checks
- the rewrite-safety section records an explicit backup ref and rollback path
- the plan distinguishes local-only delivery branches from already-published delivery branches
- the submodule entries follow the same one-way base-and-delivery model instead of paired rebases
- the risks and rollback notes are specific enough to use under pressure
- the plan records the stop point after implementation explicitly
- the handoff inventory records the exact fields the finish skill will require
- the plan does not call for reciprocal rebases or default branch deletion in the finish flow
