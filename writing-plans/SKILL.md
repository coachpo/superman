---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the implementer has zero context for the codebase and little knowledge of the project. Document everything they need to know: which files to touch for each task, code, testing, docs they might need to check, how to test it. Give them the whole plan as bite-sized tasks. DRY. YAGNI. Make verification explicit.

Assume they are a skilled developer, but know almost nothing about the toolset or problem domain. Assume they don't know good test design very well.

**Context:** If the current workflow already uses a dedicated workspace or branch convention, write the plan for that workspace. If the current workflow uses OhMyOpenCode / OMO-style plan handoff, write the plan where execution expects to find it.

**Save plans to:**
- `docs/plans/YYYY-MM-DD-<sequent>-<plan-name>.md` when the active workflow uses OMO-style `/start-work` handoff
- otherwise, follow the workspace convention or user preference for the directory, but keep the same filename format unless the user explicitly requires a different one

**Plan filename convention:**
- Plan filenames MUST use `YYYY-MM-DD-<sequent>-<plan-name>.md`
- `<sequent>` is the sequential plan number for the entire target plans folder, using a max+1 approach across existing plan filenames in that folder
- If there are no existing plan files in the target plans folder, use `<sequent>` = `1`
- If the highest existing `<sequent>` in the folder is `1`, the next plan uses `2`; if the highest is `7`, the next plan uses `8`
- Do not reset `<sequent>` by date or by plan name; always use the highest existing `<sequent>` in the target plans folder plus `1`
- `<plan-name>` should be a short kebab-case name that describes the plan

## Scope Check

If the spec covers multiple independent subsystems, it should have been broken into sub-project specs during brainstorming. If it wasn't, suggest breaking this into separate plans — one per subsystem. Each plan should produce working, testable software on its own.

## File Structure

Before defining tasks, map out which files will be created or modified and what each one is responsible for. This is where decomposition decisions get locked in.

- Design units with clear boundaries and well-defined interfaces. Each file should have one clear responsibility.
- You reason best about code you can hold in context at once, and your edits are more reliable when files are focused. Prefer smaller, focused files over large ones that do too much.
- Files that change together should live together. Split by responsibility, not by technical layer.
- In existing codebases, follow established patterns. If the codebase uses large files, don't unilaterally restructure - but if a file you're modifying has grown unwieldy, including a split in the plan is reasonable.

This structure informs the task decomposition. Each task should produce self-contained changes that make sense independently.

## Bite-Sized Task Granularity

Choose the smallest steps that keep progress clear, verifiable, and low-guesswork.

Many good steps are roughly 2-5 minutes, but that is guidance, not a hard rule. Split or combine work based on risk, verification needs, and handoff clarity.

Examples of appropriately-scoped steps:
- "Write the failing test"
- "Run it to make sure it fails"
- "Implement the minimal code to make the test pass"
- "Run the tests and make sure they pass"
- "Commit"

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For agentic workers:** Follow the current workflow's execution process. Steps use checkbox (`- [ ]`) syntax for task tracking.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Task Structure

````markdown
### Task N: [Component Name]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

- [ ] **Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

- [ ] **Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

- [ ] **Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

- [ ] **Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

- [ ] **Step 5: Verify checkpoint**

```bash
pytest tests/path/test.py::test_name -v
```
````

## Remember
- Exact file paths always
- Be exact enough that the implementer can act without guessing
- Prefer concrete edits, acceptance criteria, and verification steps over vague instructions like "add validation"
- Exact commands with expected output
- Reference related skills or workflows only when they materially help the implementer
- DRY, YAGNI, explicit verification

## Plan Review Loop

After writing the complete plan, request review only if the user or current workflow wants a reviewed plan. Use the active review mechanism and provide the plan path plus any supporting spec path.

## Execution Handoff

After saving the plan, return the plan path, major risks, and any assumptions to the current workflow or user. Do not force a specific execution mode from inside this skill.
