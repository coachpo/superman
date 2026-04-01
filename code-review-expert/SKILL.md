---
name: code-review-expert
description: Use when reviewing current git changes for correctness, security, maintainability, and safe code removal before deciding whether to implement fixes.
---

# Code Review Expert

## Overview

Perform a structured review of the current git changes with a senior engineer lens.

Focus on correctness, architecture, SOLID, security, runtime risk, and safe removal planning.

Default to review-only output unless the user explicitly asks for implementation.

## OpenCode / OMO Compatibility

This skill governs review method only. In OpenCode / OMO workflows, the parent orchestrator owns routing, task tracking, verification policy, delegation choices, and irreversible actions.

This skill does not override repository instructions or the review-first rule that implementation still requires explicit user confirmation.

If the diff is large or mixed, group findings by logical feature area instead of reviewing strictly in file order.

## When to Use

Use this skill when you need to:
- review current git changes before merge, commit, or handoff
- identify correctness, security, or maintainability risks
- spot safe deletion candidates or deferred removal work
- produce prioritized findings with actionable follow-up guidance

Do not use this skill when:
- the user already asked for a known implementation and no review pass is needed
- there are no relevant changes to review and no commit range or target area was provided
- the task is purely formatting or mechanical renaming with no meaningful review scope

## Severity Levels

| Level | Name | Description | Action |
|-------|------|-------------|--------|
| **P0** | Critical | Security vulnerability, data loss risk, or correctness bug with serious impact | Must block merge |
| **P1** | High | Logic error, significant design flaw, or likely regression | Should fix before merge |
| **P2** | Medium | Maintainability concern, code smell, or limited-scope risk | Fix now or create follow-up |
| **P3** | Low | Minor suggestion, naming issue, or optional cleanup | Non-blocking |

## Review Workflow

### 1) Preflight context

- Scope the review with git status, diff summary, and the full diff.
- Read affected modules, related callers, and nearby tests before judging the change.
- Identify entry points, trust boundaries, persistence writes, network calls, and concurrency-sensitive paths.

**Edge cases:**
- **No diff**: state that clearly and ask whether to review staged changes or a specific commit range.
- **Large diff**: summarize by module or feature area first, then review in batches.
- **Mixed concerns**: separate findings by logical area so the review stays understandable.

### 2) Architecture and SOLID review

- Load `references/solid-checklist.md` for review prompts.
- Look for overloaded modules, extension-hostile branching, broken substitutions, oversized interfaces, and business logic tied directly to infrastructure.
- When proposing a refactor, explain why it improves cohesion or reduces coupling.
- For non-trivial refactors, prefer an incremental plan over a rewrite.

### 3) Removal candidates and iteration planning

- Load `references/removal-plan.md` for the deletion planning template.
- Distinguish **safe to remove now** from **defer with a plan**.
- Require evidence for deletion claims: code search, contract checks, feature flag status, and consumer impact.
- If removal is not safe now, provide explicit preconditions, validation steps, and rollback guidance.

### 4) Security and reliability scan

- Load `references/security-checklist.md` for review coverage.
- Check input and output safety, auth, secrets, runtime limits, data integrity, crypto, and race conditions.
- Call out both exploitability and impact.
- Do not inflate severity without a credible failure path.

### 5) Code quality scan

- Load `references/code-quality-checklist.md` for review coverage.
- Check error handling, async failure paths, performance, memory growth, and boundary conditions.
- Flag silent-failure risks and operational hazards explicitly.

### 6) Output format

Use plain markdown and keep the output easy to act on.

```markdown
## Code Review Summary

**Files reviewed**: X files, Y lines changed
**Overall assessment**: APPROVE | REQUEST_CHANGES | COMMENT

---

## Findings

### P0 - Critical
(none or numbered findings)

### P1 - High
1. `path/to/file.ts:42` — Brief title
   - Why it matters
   - Suggested fix

### P2 - Medium
2. ...

### P3 - Low
3. ...

---

## Removal / Iteration Plan
(if applicable)

## Areas Checked
- architecture / SOLID
- security / reliability
- code quality / boundaries

## Residual Risks
(optional)
```

For a clean review, explicitly state:
- what you checked
- what you did not check
- any residual risks or recommended follow-up tests

### 7) Next steps confirmation

After presenting findings, ask how the user wants to proceed:

```markdown
---

## Next Steps

I found X issues (P0: _, P1: _, P2: _, P3: _).

1. **Fix all** - implement all suggested fixes
2. **Fix P0/P1 only** - address only critical and high-priority items
3. **Fix specific items** - user selects the findings
4. **No changes** - review only
```

**Important:** Do not implement changes until the user explicitly asks.

## Resources

### references/

| File | Purpose |
|------|---------|
| `solid-checklist.md` | SOLID smell prompts and refactor heuristics |
| `security-checklist.md` | Security, reliability, and runtime risk checklist |
| `code-quality-checklist.md` | Error handling, performance, and boundary-condition review |
| `removal-plan.md` | Template for safe deletion and deferred removal planning |
