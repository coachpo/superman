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
- Base branch: <base-branch>
- Plan path: <plan-path>
- Worktree helper: <helper-or-none>
- Verification: <real verification commands or checks>

Constraints:
- <constraints>

Return sections:
1. Goal and scope
2. Assumptions
3. Touch points
4. Step-by-step implementation
5. Verification
6. Risks and rollback
7. Proposed branch name
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

## Implementation Steps
1. <step>
2. <step>
3. <step>

## Verification
1. <check>
2. <check>

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
- Base branch: <base-branch>
- Implementation branch: <branch-name>
- Worktree path: <worktree-path>
```

## Approval Checklist

Before calling the plan approved, confirm all of the following:
- the requirement is fully represented in the plan
- the plan names the expected files, systems, or touch points
- the verification section proves completion instead of hoping for it
- the risks and rollback notes are specific enough to use under pressure
- the branch and worktree intent are recorded before implementation starts
