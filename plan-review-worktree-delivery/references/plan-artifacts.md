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
- Task branch: <branch-name>
- Worktree path: <worktree-path>
- Submodules in scope: <none | list>
- Plan path: <plan-path>
- Worktree helper: <helper-or-none>
- Verification: <real verification commands or checks>

Constraints:
- <constraints>

Return sections:
1. Goal and scope
2. Assumptions
3. Superproject and submodule touch points
4. Step-by-step implementation
5. Verification
6. Commit strategy
7. Risks and rollback
```

## Momus Critic Prompt

Example prompt:

```text
You are @Momus (Plan Critic).
Audit this implementation plan. Approve only if the requirement is fully covered, the sequence is safe, and the verification is specific enough to prove completion.

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

## Implementation Steps
1. <step>
2. <step>
3. <step>

## Verification
1. <check>
2. <check>

## Commit Strategy
- Submodule commits needed: <none | list>
- Parent repo pointer update needed: <yes/no>
- Commit order: <order>

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
- Implementation branch: <branch-name>
- Worktree path: <worktree-path>

## Post-Commit Control
- Rebase after commit requires explicit user approval: yes
- Worktree deletion requires explicit user approval: yes
```

## Approval Checklist

Before calling the plan approved, confirm all of the following:
- the requirement is fully represented in the plan
- the plan names the expected files, systems, or touch points
- the plan distinguishes superproject work from submodule work when submodules exist
- the verification section proves completion instead of hoping for it
- the risks and rollback notes are specific enough to use under pressure
- the created worktree path and task branch are recorded before implementation starts
- the plan records that post-commit rebase and worktree deletion require explicit user approval
- the plan records the commit strategy for touched submodules instead of leaving it implicit
