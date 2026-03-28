---
name: release-tagging
description: Use when version changes are ready and the user wants the release-finalization git flow: version commit, v{VERSION} tag, and tag push
---

# Release Tagging

## Overview

Finalize an already-prepared version change by creating the version commit, creating the release tag, and pushing the tag. This skill assumes the version-editing work is already complete or has been handed off from `version-management`.

**Core principle:** Finalize the release from the repository's resolved version source of truth. Do not guess the version, tag name, or release steps.

## OpenCode / OMO Compatibility

This skill handles release-finalization git side effects only. It does not override repository policy, branch protections, hooks, or controller decisions. Enter this skill only when the user has explicitly asked to perform the commit-and-tag finalization flow, whether directly or by agreeing to move here from `version-management`.

## When to Use

Use this skill when:
- version changes are already prepared and verified
- the user wants the version-related commit created
- the user wants the release tag created
- the user wants the release tag pushed

If the repository's version source of truth or release target is still unclear, ask one targeted question before performing any git side effects.

## Step 1: Confirm the Inputs Before Git Side Effects

Before creating the commit or tag, confirm:
- which file or helper defines the final version
- the exact resolved version value
- which files are part of the version-related diff
- whether the repository has a helper or release command that should be used instead of manual git steps
- whether any hooks, protections, or release tooling constraints affect the flow

If this work came from `version-management`, reuse its findings instead of rediscovering them.

## Step 2: Create the Version Commit

Create the version-related commit using the repository's normal commit workflow.

- stage only the version-related files that belong in the release-finalization change
- use the repository's usual commit style if one is established
- do not include unrelated files just because they are present in the working tree

If the commit fails because of hooks or validation, fix the actual issue and create a new commit that still reflects the version finalization work.

## Step 3: Create the Release Tag

Resolve the final version from the repository's source of truth and create the tag as `v{VERSION}`.

Examples:
- `VERSION=1.2.3` -> `v1.2.3`
- `VERSION=2.0.0-rc.1` -> `v2.0.0-rc.1`

If the repository has a stronger helper or release command, use it only when it preserves the same `v{VERSION}` tag outcome.

Do not invent a different tag format unless the user explicitly overrides it.

## Step 4: Push the Release Tag

Push the release tag after it is created.

- push the tag that matches the resolved version exactly
- if the repository's workflow also expects the branch to be pushed first, follow the repository's normal non-destructive workflow
- do not push unrelated tags

## Step 5: Verify Before Claiming Done

Before claiming release tagging is complete, gather fresh evidence for:
- the resolved final version
- the created commit identity
- the created tag name
- the push result showing the expected tag was pushed
- any repository-provided release check succeeding, if applicable

## Core Rules

1. Use the resolved version from the repository's source of truth.
2. Create the release tag as `v{VERSION}` unless the user explicitly overrides it.
3. Commit only the version-related release-finalization changes.
4. Push only the expected release tag.
5. Prefer repository-provided release helpers when they preserve the same requested outcome.
6. Do not publish releases or perform broader deployment steps unless the user explicitly asks.

## Anti-Patterns

- Tagging from a guessed version instead of the resolved source of truth
- Creating a tag that does not match `v{VERSION}`
- Mixing unrelated working tree changes into the version commit
- Pushing extra tags or unrelated refs
- Treating this skill as a substitute for version preparation work that should have happened first

## Stop Conditions

Stop and ask a targeted question when:
- the final version value is ambiguous
- the working tree contains unrelated changes and it is unclear what belongs in the release commit
- the repository's mandatory release tooling conflicts with the required `v{VERSION}` tag output
- the requested action appears to require publishing or deployment beyond commit and tag finalization

## Remember

- Confirm the resolved version first
- Create the version commit cleanly
- Tag exactly `v{VERSION}`
- Push the expected tag
- Verify with fresh evidence
