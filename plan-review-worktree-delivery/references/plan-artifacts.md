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
- Main branch: <main-or-repo-specific-default>
- Base ref used for the worktree: <origin/main | repo-approved default>
- Task branch: <branch-name>
- Worktree path: <worktree-path>
- Monorepo workspace root: <none | path>
- Packages or apps in scope: <none | list>
- Worktree bootstrap or install: <none | exact commands>
- Submodules in scope: <none | list>
- Submodule revision expectations: <none | for each submodule: exact SHA or gitlink expectation, branch or detached state>
- Plan path: <plan-path>
- Worktree helper: <helper-or-none>
- Verification: <exact scoped verification commands or checks>

Constraints:
- <constraints>

Return sections:
1. Goal and scope
2. Assumptions
3. Superproject, submodule, and workspace touch points
4. Step-by-step implementation
5. Bootstrap and verification
6. Branch, worktree, and revision tracking
7. Risks and rollback
```

## Momus Critic Prompt

Example prompt:

```text
You are @Momus (Plan Critic).
Audit this implementation plan. Approve only if the requirement is fully covered, the sequence is safe, the submodule revision tracking is reproducible, and the verification is specific enough to prove completion at the right workspace or package scope.

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

## Submodule Scope
- In scope: <none | list>
- Superproject-only changes: <yes/no>
- Submodule content changes: <yes/no, which ones>
- Expected submodule revision tracking: <none | for each submodule: exact SHA or gitlink expectation, branch or detached state>

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

## Branch, Worktree, and Revision Tracking
- Base ref used for the worktree: <origin/main | repo-approved default>
- Main repo branch: <branch-name>
- Main repo worktree: <worktree-path>
- Additional main repo branches/worktrees: <none | list>
- Submodule revision tracking required: <yes/no>

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
- Main branch: <main-or-repo-specific-default>
- Base ref used for worktree creation: <origin/main | repo-approved default>
- Implementation branch: <branch-name>
- Worktree path: <worktree-path>

## Stop Point
- Stop after implementation: yes
- Commit in this skill: no
- Rebase in this skill: no
- Worktree cleanup in this skill: no

## Branch, Worktree, and Revision Inventory
- Base ref used: <origin/main | repo-approved default>
- Main repo branch used: <branch-name>
- Main repo worktree used: <worktree-path>
- Additional main repo branches/worktrees used: <none | list>
- Monorepo scope verified: <none | workspace root and packages/apps>
- Submodules used:
  - <submodule-path>: commit <sha>, branch <branch> or detached, worktree <path>, superproject gitlink matches <yes/no>, modified <yes/no>
```

## Approval Checklist

Before calling the plan approved, confirm all of the following:
- the requirement is fully represented in the plan
- the plan names the expected files, systems, or touch points
- the plan distinguishes superproject work from submodule work when submodules exist
- the plan names workspace root and packages or apps in scope when the repo is a monorepo
- the verification section proves completion instead of hoping for it
- the verification section names exact scoped commands instead of only generic repo-root checks
- the submodule entries record exact revision and gitlink expectations instead of only branch names
- the risks and rollback notes are specific enough to use under pressure
- the created worktree path and task branch are recorded before implementation starts
- the plan records the stop point after implementation explicitly
- the plan records the required branch/worktree inventory instead of leaving it implicit
