---
name: version-management
description: Use when changing, validating, or preparing a project's version metadata so the declared version surfaces stay aligned before optional release tagging, including repositories that must keep root and submodule VERSION files in sync
---

# Version Management

## Overview

Inspect the repository's existing versioning scheme, make the smallest safe change, and verify every version surface still agrees. In repositories that version the root repo together with owned git submodules, do not stop at the root: every required submodule `VERSION` file must exist and be bumped before the work is complete. This skill prepares version changes; if the user also wants the release commit and tag flow, hand off to `release-tagging` instead of doing those git side effects here.

**Core principle:** Follow the repository's existing source of truth. Do not invent a new versioning workflow while doing version work.

## OpenCode / OMO Compatibility

This skill governs version-related work only. It does not override user instructions, repository policy, or controller decisions about commits, tags, pushes, releases, or delegation. This skill stops before release-finalization git side effects and should ask the user whether to move to `release-tagging` when those steps are desired.

## When to Use

Use this skill when you need to:
- inspect the current project version
- change the version number
- validate drift between declared versions
- confirm the expected tag or release naming
- prepare version metadata for a release
- align root and submodule `VERSION` files in a repo that versions multiple submodules together

If the user wants the release-finalization git steps after the version change is ready, ask: `Version changes are ready. Would you like me to move to release-tagging now?`

If the repository's versioning scheme is still unclear after inspection, ask one targeted question before editing.

## Step 1: Inspect Before Editing

Before making changes, inspect the existing versioning workflow and determine:
- the editable source of truth
- whether a `VERSION` file exists and is the source of truth
- whether the repo already has a helper script, task runner command, or release tool for version changes
- which files are expected to stay in sync
- whether the repository has git submodules that participate in the same version change
- for each owned submodule in scope, whether a `VERSION` file already exists or must be created
- whether any later release-tagging flow will need extra context, such as tag naming or release automation constraints

Look at the actual repository before guessing. Check version files, package manifests, release scripts, CI or release config, recent tags, nearby docs, `.gitmodules`, and the version state inside each submodule that is part of the release surface.

## Step 2: Use the Existing Mechanism

Use the repository's existing mechanism in this order:
1. existing version helper script or task
2. existing release automation command
3. direct file edits only if no supported helper exists

If the repository already centralizes versioning in one file, treat that as the editable source of truth unless the codebase clearly does something else.

If the repository versions the root and owned submodules together through `VERSION` files, treat that set as one required aligned surface even when the root `VERSION` is the first place you inspect.

Do not bypass an existing helper just because direct edits are faster.

## Step 3: Apply the Requested Change

For an exact requested version:
- set that version using the existing mechanism
- update only the files the repository expects to stay aligned
- if the repo versions owned submodules together, ensure each in-scope submodule has a `VERSION` file and create missing ones before applying the version bump
- do not finish after changing only the root `VERSION`

For a bump request such as `patch`, `minor`, `major`, or prerelease:
- derive the new version from the current declared version and the repository's existing versioning scheme
- if the repo versions owned submodules together, apply the resolved version to the root and every required submodule `VERSION` file
- if prerelease or scheme rules are unclear, stop and ask

Keep the diff small. Do not refactor unrelated release logic while changing the version.

## Step 4: Handoff to Release Tagging When the User Wants Finalization

When the version change is complete and the user also wants the release git steps:
- stop after the version-preparation work is ready
- ask: `Version changes are ready. Would you like me to move to release-tagging now?`
- carry forward the resolved source of truth, final version value, and any release-tooling constraints you found during inspection

Do not create the release commit, tag, or tag push inside this skill. Those side effects belong to `release-tagging`.

## Step 5: Verify Before Claiming Done

Before claiming version work is complete, gather fresh evidence for:
- the current resolved version
- all changed version surfaces now matching
- every required submodule `VERSION` file existing and matching the resolved version
- any tag or release naming expectations discovered for the later `release-tagging` handoff
- any repository-provided version check succeeding

If the change affects packaging, release builds, or runtime version reporting, run the smallest relevant verification that proves those surfaces still work. In a multi-submodule repo, gather fresh evidence from the root and each touched submodule instead of checking only the root working tree.

## Core Rules

1. Follow the repository's existing source of truth.
2. Prefer the repository's supported version helper over manual scattered edits.
3. In repositories that version owned submodules together, keep the root and required submodule `VERSION` files aligned.
4. If an in-scope submodule needs a `VERSION` file and does not have one yet, create it before bumping the version.
5. Keep subprojects self-contained. Do not add runtime dependencies on repo-root version files unless the repository already works that way.
6. Do not commit, tag, push, or publish from this skill.
7. When the user wants release finalization after version preparation, ask whether to move to `release-tagging`.
8. Carry forward the final resolved version and any release constraints so `release-tagging` can execute without guesswork.

## Anti-Patterns

- Editing multiple manifests by hand without first identifying the source of truth
- Leaving version drift between files that are supposed to match
- Bumping only the root `VERSION` in a repository that versions submodules together
- Skipping a missing submodule `VERSION` file instead of creating it before the bump
- Hard-coding a release version in runtime code to avoid fixing the real source of truth
- Performing release-finalization git side effects inside this skill
- Finishing version preparation without asking whether the user wants to move to `release-tagging`
- Performing tag, release, or publish actions without explicit approval

## Stop Conditions

Stop and ask a targeted question when:
- the repository has multiple conflicting version sources
- it is unclear which submodules are owned and should participate in the shared version bump
- the requested bump type does not match the observed versioning scheme
- the repository's later release-tagging flow is unclear or appears to conflict with known release tooling
- changing the version would require inventing new release automation

## Remember

- Inspect first
- Match repository conventions
- Use existing helpers
- Do not stop at the root in multi-submodule repos
- Keep changes atomic
- Ask before moving into `release-tagging`
- Verify with fresh evidence
- No release side effects without approval
