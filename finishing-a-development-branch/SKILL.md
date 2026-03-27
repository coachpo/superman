---
name: finishing-a-development-branch
description: Use when implementation is complete, required verification has passed, and you need to decide how to integrate the work - guides completion of development work by presenting structured options for merge, PR, or cleanup
---

# Finishing a Development Branch

## Overview

Guide completion of development work by presenting clear options and handling chosen workflow.

**Core principle:** Verify completion evidence → Present options → Execute choice → Clean up.

## The Process

### Step 1: Verify Completion Evidence

**Before presenting options, verify the repo-appropriate completion checks pass:**

```bash
# Run the checks that actually prove completion in this repo
# Examples: tests, build, lint/typecheck, manual QA for user-visible changes
npm test / cargo test / pytest / go test ./...
npm run build / cargo build / tsc --noEmit
```

**If required verification fails:**
```
Verification failing. Must fix before completing:

[Show failing checks]

Cannot proceed with merge/PR until required completion checks pass.
```

Stop. Don't proceed to Step 2.

**If required verification passes:** Continue to Step 2.

### Step 2: Determine Base Branch

```bash
# Try common base branches
git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null
```

Or ask: "This branch split from main - is that correct?"

### Step 3: Present Options

Present the standard integration options that fit the current repo/workflow. In many cases, that looks like:

```
Implementation complete. What would you like to do?

1. Merge back to <base-branch> locally
2. Push and open a review / pull request
3. Keep the branch as-is (I'll handle it later)
4. Discard this work

Which option?
```

Keep the options concise. Add or remove options only when the repo or workflow clearly requires it.

### Step 4: Execute Choice

#### Option 1: Merge Locally

```bash
# Switch to base branch
git checkout <base-branch>

# Pull latest
git pull

# Merge feature branch
git merge <feature-branch>

# Verify required completion checks on merged result
<repo-appropriate verification commands>

# If verification passes
git branch -d <feature-branch>
```

Then: Cleanup worktree if this branch uses a separate worktree (Step 5)

#### Option 2: Push and Open Review

```bash
# Push branch
git push -u origin <feature-branch>

# If your current platform supports review / PR creation, use its native workflow.
# Otherwise stop after push and report the branch name, title, and summary.
```

Then: Keep branch and checkout/worktree available for follow-up unless the user asks to clean them up.

#### Option 3: Keep As-Is

Report: "Keeping branch <name>. Current checkout preserved. If this branch uses a separate worktree, leave it in place."

**Don't cleanup the current checkout or any associated worktree.**

#### Option 4: Discard

**Confirm first:**
```
This will permanently delete:
- Branch <name>
- All commits: <commit-list>
- Worktree at <path> (if any)

Type 'discard' to confirm.
```

Wait for exact confirmation.

If confirmed:
```bash
git checkout <base-branch>
git branch -D <feature-branch>
```

Then: Cleanup worktree if this branch uses a separate worktree (Step 5)

### Step 5: Cleanup Worktree If One Exists

**For Options 1 and 4, only if this branch uses a separate worktree:**

Check if in worktree:
```bash
git worktree list | grep $(git branch --show-current)
```

If yes:
```bash
git worktree remove <worktree-path>
```

**For Options 2 and 3:** Keep the current checkout/worktree available.

## Quick Reference

| Option | Merge | Push | Keep Worktree | Cleanup Branch |
|--------|-------|------|---------------|----------------|
| 1. Merge locally | ✓ | - | - | ✓ |
| 2. Open review / PR | - | ✓ | ✓ | - |
| 3. Keep as-is | - | - | ✓ | - |
| 4. Discard | - | - | - | ✓ (force) |

## Common Mistakes

**Skipping completion verification**
- **Problem:** Merge or publish work that is not actually ready
- **Fix:** Always verify the repo-appropriate completion checks before offering options

**Open-ended questions**
- **Problem:** "What should I do next?" → ambiguous
- **Fix:** Present structured, repo-appropriate options instead of an open-ended prompt

**Automatic worktree cleanup**
- **Problem:** Remove a worktree or checkout that may still be needed
- **Fix:** Only clean up when the selected option calls for it and a separate worktree actually exists

**No confirmation for discard**
- **Problem:** Accidentally delete work
- **Fix:** Require typed "discard" confirmation

## Red Flags

**Never:**
- Proceed with failing required verification
- Merge without verifying the merged result
- Delete work without confirmation
- Force-push without explicit request

**Always:**
- Verify required completion checks before offering options
- Present structured, repo-appropriate options
- Get typed confirmation for Option 4
- Clean up a separate worktree only when the chosen option calls for it

## Integration

**Fits after:**
- Any implementation workflow that has already verified required completion checks and needs a merge / review / keep / discard decision

**Pairs with:**
- Workspace or worktree-based workflows when cleanup is needed
