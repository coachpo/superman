---
name: version-management
description: Use when changing, validating, or inspecting a project's version or release metadata so the declared version, package metadata, and tag expectations stay aligned
---

# Version Management

## Overview

Inspect the repository's existing versioning scheme, make the smallest safe change, and verify every version surface still agrees.

**Core principle:** Follow the repository's existing source of truth. Do not invent a new versioning workflow while doing version work.

## OpenCode / OMO Compatibility

This skill governs version-related work only. It does not override user instructions, repository policy, or controller decisions about commits, tags, pushes, releases, or delegation. When the user explicitly asks for the full version finalization flow, include the requested commit, tag, and push steps.

## When to Use

Use this skill when you need to:
- inspect the current project version
- change the version number
- validate drift between declared versions
- confirm the expected tag or release naming
- prepare version metadata for a release
- complete the requested version commit and tag flow

If the repository's versioning scheme is still unclear after inspection, ask one targeted question before editing.

## Step 1: Inspect Before Editing

Before making changes, inspect the existing versioning workflow and determine:
- the editable source of truth
- whether a `VERSION` file exists and is the source of truth
- whether the repo already has a helper script, task runner command, or release tool for version changes
- which files are expected to stay in sync
- how tags and releases are named

Look at the actual repository before guessing. Check version files, package manifests, release scripts, CI or release config, recent tags, and nearby docs.

## Step 2: Use the Existing Mechanism

Use the repository's existing mechanism in this order:
1. existing version helper script or task
2. existing release automation command
3. direct file edits only if no supported helper exists

If the repository already centralizes versioning in one file, treat that as the editable source of truth unless the codebase clearly does something else.

Do not bypass an existing helper just because direct edits are faster.

## Step 3: Apply the Requested Change

For an exact requested version:
- set that version using the existing mechanism
- update only the files the repository expects to stay aligned

For a bump request such as `patch`, `minor`, `major`, or prerelease:
- derive the new version from the current declared version and the repository's existing versioning scheme
- if prerelease or scheme rules are unclear, stop and ask

Keep the diff small. Do not refactor unrelated release logic while changing the version.

## Step 4: Finalize the Version Change When Explicitly Requested

If the user explicitly asks you to finish the versioning flow after the version update:
- commit the version-related changes using the repository's normal commit workflow
- read the final version from the repository's source of truth; if that source of truth is a `VERSION` file, use its resolved value for the version tag
- create the version tag using the repository's existing tag naming convention
- push the version tag when the user asked for that push step

Do not invent a new tag format just because a `VERSION` file exists. Use the repository's established convention unless the user explicitly says otherwise.

## Step 5: Verify Before Claiming Done

Before claiming version work is complete, gather fresh evidence for:
- the current resolved version
- all changed version surfaces now matching
- the expected tag or release name, if relevant
- any repository-provided version check succeeding

If the change affects packaging, release builds, or runtime version reporting, run the smallest relevant verification that proves those surfaces still work.

## Core Rules

1. Follow the repository's existing source of truth.
2. Prefer the repository's supported version helper over manual scattered edits.
3. Keep subprojects self-contained. Do not add runtime dependencies on repo-root version files unless the repository already works that way.
4. Do not invent a new tagging scheme, release process, or version file during a routine version update.
5. Do not commit, tag, push, or publish unless the user explicitly asks.
6. When the user explicitly asks for commit and tag finalization, derive the final version from the repository's source of truth; if that source is a `VERSION` file, use that resolved version for the tag.

## Anti-Patterns

- Editing multiple manifests by hand without first identifying the source of truth
- Leaving version drift between files that are supposed to match
- Hard-coding a release version in runtime code to avoid fixing the real source of truth
- Assuming the tag format without inspecting existing tags or release tooling
- Performing tag, release, or publish actions without explicit approval

## Stop Conditions

Stop and ask a targeted question when:
- the repository has multiple conflicting version sources
- the requested bump type does not match the observed versioning scheme
- release or tag naming is unclear
- changing the version would require inventing new release automation

## Remember

- Inspect first
- Match repository conventions
- Use existing helpers
- Keep changes atomic
- Verify with fresh evidence
- No release side effects without approval
