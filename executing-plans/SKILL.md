---
name: executing-plans
description: Use when an already approved implementation plan needs execution, with OpenCode-first delegation, milestone updates, and evidence-backed verification
---

# Executing Plans

## Overview

Execute an approved plan, keep work moving, and verify each milestone with evidence before advancing.

Optional start note: "I'm using the executing-plans skill to carry out the approved plan."

## Role Clarity

Under OhMyOpenCode, the parent orchestrator owns routing, task tracking policy, verification policy, and irreversible actions. This skill only guides execution after the plan is already approved.

When the active workflow is OMO-style execution, assume a delegate-first posture: the orchestrator coordinates and verifies, while specialized workers handle non-trivial implementation work.

Do not use this skill to restart planning. If the plan needs rework, hand it back to the parent workflow or the planning skill.

## When to Use

Use this skill when you already have an approved plan and need to execute it in the current session.

Do not use it for discovery, design, or plan writing.

## Execution Workflow

1. Read the plan and extract the next actionable task.
2. If context is incomplete, stop and explore first. Use targeted file reads, searches, or delegated exploration before guessing. If the unknowns are independent, gather that context in parallel.
3. If task tracking is active, keep exactly one task in progress at a time.
4. If the active workflow explicitly allows a trivially local step to stay inline, execute it directly.
5. For non-trivial, independent, or multi-file work — and by default under OMO-style execution — delegate with a self-contained prompt.
6. Run the verification the plan asks for, or the smallest fresh check that proves the step.
7. Report milestone progress updates, then move on only after evidence confirms the step is done.
8. Once the full plan is implemented and verified, archive the completed plan by moving it from `docs/plans/` to `docs/archive/`.

## Delegation Rules

Prefer delegation when work is:
- non-trivial
- independent from the current step
- easier to inspect in isolation
- likely to benefit from a fresh context

Under OMO-style execution, delegation is the default for writing, editing, testing, and similar multi-step implementation work unless the parent workflow explicitly keeps a step local.

Keep prompts self-contained. Include the task goal, exact scope, known constraints, expected output, and verification the delegated work must satisfy.

Direct execution is reserved for truly local, mechanical steps that the active workflow has intentionally kept inline, such as a tiny edit, a narrow command, or a simple verification that clearly stays in one file or one obvious action.

## Progress Updates

Send short milestone updates, not filler. Say what finished, what was verified, and what is next.

If a delegated task is running, report its status only when you have something concrete.

## Verification Gate

No step is complete until you have fresh evidence.

Before advancing or claiming success:
1. identify the check that proves the claim
2. run the full check fresh
3. read the output and exit status
4. only then mark the step complete or report completion

If verification fails, stop, fix the root cause, and verify again. Do not soften the result, and do not trust memory or agent summaries without direct evidence.

## Stop Conditions

Stop and escalate when:
- the plan is unclear or internally inconsistent
- context is missing and exploration does not resolve it
- verification keeps failing
- the work would require changing the plan itself
- the parent orchestrator changes routing or asks for a different mode

## Integration

Keep this skill narrow. Planning belongs to `writing-plans`. Parallel or multi-agent preparation belongs to the current workflow's delegation mechanism. Completion gates belong to `verification-before-completion`.

## Remember

- Parent orchestrator first
- Execute approved plans only
- Explore before guessing
- Delegate non-trivial work by default
- One in-progress task at a time when tracking is active
- Verify with evidence before advancing or claiming done
- Archive completed plans in `docs/archive/` after implementation finishes
- Keep updates brief and milestone-based
