---
name: release-tagging
description: "Use when version changes are ready and the user wants the release-finalization git flow: version commit, v{VERSION} tag, and tag push, including repositories where the root release tag must mirror the latest relevant submodule version tag"
---

# Release Tagging

## Overview

Finalize an already-prepared version change by creating the version commit, creating the release tag, and pushing the tag. This skill assumes the version-editing work is already complete or has been handed off from `version-management`.

In repositories with release-driving submodules, the root release tag may need to mirror the latest relevant submodule version tag instead of a stale root-only version file. A root git tag cannot directly target a submodule tag object, so interpret this requirement as: create the root tag on the root commit that records the submodule commit for that tagged submodule version.

**Core principle:** Finalize the release from the repository's resolved version source of truth. Do not guess the version, tag name, or release steps.

## OpenCode / OMO Compatibility

This skill handles release-finalization git side effects only. It does not override repository policy, branch protections, hooks, or controller decisions. Enter this skill only when the user has explicitly asked to perform the commit-and-tag finalization flow, whether directly or by agreeing to move here from `version-management`.

## When to Use

Use this skill when:
- version changes are already prepared and verified
- the user wants the version-related commit created
- the user wants the release tag created
- the user wants the release tag pushed
- the repository has submodules and the root release tag should follow the latest relevant submodule version tag

If the repository's version source of truth or release target is still unclear, ask one targeted question before performing any git side effects.

## Step 1: Confirm the Inputs Before Git Side Effects

Before creating the commit or tag, confirm:
- which file or helper defines the final version
- the exact resolved version value
- which files are part of the version-related diff
- whether the repository has a helper or release command that should be used instead of manual git steps
- whether any hooks, protections, or release tooling constraints affect the flow
- whether the repository has git submodules that participate in the release surface
- which submodule, if any, is authoritative for the root release tag
- which tagged submodule commit the root repository currently points at

If this work came from `version-management`, reuse its findings instead of rediscovering them.

## Step 2: Create the Version Commit

Create the version-related commit using the repository's normal commit workflow.

- stage only the version-related files that belong in the release-finalization change
- if the release includes a submodule update, stage the root gitlink change that pins the intended tagged submodule commit
- use the repository's usual commit style if one is established
- do not include unrelated files just because they are present in the working tree

If the commit fails because of hooks or validation, fix the actual issue and create a new commit that still reflects the version finalization work.

## Step 3: Create the Release Tag

Resolve the final version from the repository's source of truth and create the tag as `v{VERSION}`.

In a repository where a submodule tag is authoritative for the root release, derive `VERSION` from the latest relevant submodule version tag instead of a stale root-only version surface. Create the root tag with that same `v{VERSION}` name on the root commit that records the corresponding tagged submodule commit.

Examples:
- `VERSION=1.2.3` -> `v1.2.3`
- `VERSION=2.0.0-rc.1` -> `v2.0.0-rc.1`

Submodule-driven example:
- submodule release tag `v3.4.5` is the latest relevant tag and the root commit points at that tagged submodule commit -> create root tag `v3.4.5`

If the repository has a stronger helper or release command, use it only when it preserves the same `v{VERSION}` tag outcome.

Do not invent a different tag format unless the user explicitly overrides it.
Do not create a root tag for a submodule version that the root commit does not actually reference.

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
- the tagged root commit referencing the expected submodule commit, when submodules are part of the release surface
- the submodule version tag used to derive the root tag, when applicable
- any repository-provided release check succeeding, if applicable

## Core Rules

1. Use the resolved version from the repository's source of truth.
2. If a submodule tag is authoritative for the root release, derive the root tag from the latest relevant submodule version tag and tag the root commit that references that submodule commit.
3. Create the release tag as `v{VERSION}` unless the user explicitly overrides it.
4. Commit only the version-related release-finalization changes, including required submodule pointer updates.
5. Push only the expected release tag.
6. Prefer repository-provided release helpers when they preserve the same requested outcome.
7. Do not publish releases or perform broader deployment steps unless the user explicitly asks.

## Anti-Patterns

- Tagging from a guessed version instead of the resolved source of truth
- Deriving the root tag from a stale root version when a submodule tag is the authoritative release source
- Creating a tag that does not match `v{VERSION}`
- Creating a root tag for a submodule version the root commit does not actually reference
- Assuming a root git tag can directly target a submodule tag object
- Mixing unrelated working tree changes into the version commit
- Pushing extra tags or unrelated refs
- Treating this skill as a substitute for version preparation work that should have happened first

## Stop Conditions

Stop and ask a targeted question when:
- the final version value is ambiguous
- multiple in-scope submodules expose different candidate release tags and the repository does not define which one drives the root release
- the working tree contains unrelated changes and it is unclear what belongs in the release commit
- the repository's mandatory release tooling conflicts with the required `v{VERSION}` tag output
- the requested action appears to require publishing or deployment beyond commit and tag finalization

## Remember

- Confirm the resolved version first
- In submodule-driven repos, resolve the root tag from the latest relevant submodule version tag
- Create the version commit cleanly
- Tag exactly `v{VERSION}`
- Tag the root commit that pins the intended tagged submodule commit
- Push the expected tag
- Verify with fresh evidence
