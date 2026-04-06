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
- Local base checkout for later fast-forward: <path or repo-approved owner>
- Worktree helper: <helper-or-none>
- Monorepo workspace root: <none | path>
- Packages or apps in scope: <none | list>
- Worktree bootstrap or install: <none | exact commands>
- Finish workflow contract: <commit delivery branch, create safety ref, fetch base ref, rebase delivery branch onto refreshed base ref, verify, fast-forward local base with ff-only, optionally push base only when explicitly requested and policy allows, clean up temporary delivery artifacts>
- Rewrite safety requirements: <backup ref format, published-branch policy, rollback notes>
- Verification: <exact scoped verification commands or checks>

Constraints:
- <constraints>

Return sections:
1. Goal and scope
2. Assumptions
3. Base and delivery branch model
4. Workspace touch points
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
Audit this implementation plan. Approve only if the requirement is fully covered, the workflow keeps the base branch authoritative, the delivery branch is the only rebased branch, the local base branch is updated only by fast-forward semantics, and the verification is concrete enough to prove completion at the right workspace or package scope.

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
- Local base checkout for later fast-forward: <path or repo-approved owner>

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
2. <manual check if required>

## Finish Workflow Contract
- Commit on delivery branch before any rebase: <yes>
- Create safety ref before any history rewrite: <exact ref format>
- Fetch base ref before rebasing: <exact command or repo helper>
- Rebase direction: <delivery branch onto refreshed base ref only>
- Verification after rebase: <exact commands>
- Local base update: <fast-forward only>
- Base push after fast-forward: <explicit request and policy required>
- Cleanup in finish skill: <remove temporary delivery worktree and delivery branch>

## Rewrite Safety
- Pre-rewrite backup ref name: <ref>
- Pre-rewrite SHA to preserve: <sha>
- Branch already published: <yes/no>
- Allowed rewrite policy if published: <explicit rule or stop>
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
- Push in this skill: no
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
- Local base checkout for later fast-forward: <path or repo-approved owner>
- Monorepo scope verified: <none | workspace root and packages/apps>
```

## Approval Checklist

Before calling the plan approved, confirm all of the following:
- the requirement is fully represented in the plan
- the plan names the expected files, systems, or touch points
- the plan keeps the base branch authoritative and never asks later skills to rebase it
- the plan names the delivery branch as the only branch that may be rebased in the finish flow
- the plan records the base ref, base SHA, delivery branch, delivery worktree, and local base checkout before implementation starts
- the verification section proves completion instead of hoping for it
- the verification section names exact scoped commands instead of only generic repo-root checks
- the rewrite-safety section records an explicit backup ref and rollback path
- the plan distinguishes local-only delivery branches from already-published delivery branches
- the plan updates the base branch only by fast-forward semantics and never by merge commit or rebase
- the risks and rollback notes are specific enough to use under pressure
- the plan records the stop point after implementation explicitly
- the handoff inventory records the exact fields the finish skill will require
- the plan does not call for reciprocal rebases, keeping the temporary delivery branch as the final state, or any out-of-scope repo-shape workflow
