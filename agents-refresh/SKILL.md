---
name: agents-refresh
description: Use when creating or refreshing hierarchical AGENTS.md files after repo structure, ownership, workflow, or policy changes, including first-time AGENTS coverage and forward-only rule updates that must state future changes do not need backward compatibility and must avoid shim code or tech debt.
---

# AGENTS Creation and Refresh

## Overview

Create or refresh an AGENTS hierarchy without turning it into a generic docs rewrite. Inventory the live tree first, decide whether the repo needs a root-only file or a hierarchy, update existing files by default, and add new child docs only when the parent can no longer describe an area clearly.

Every new or materially refreshed AGENTS hierarchy should encode a forward-only engineering posture: future updates do not need backward compatibility unless the user explicitly asks for it, the best current shape beats legacy preservation, and shim code or tech-debt-preserving transition layers are not acceptable.

This skill is for AGENTS-style instruction maintenance. It is not for README edits, product docs, or feature implementation.

## When to Use

Use when:
- a repo has no `AGENTS.md` yet and needs first-time coverage
- a repo already has one or more `AGENTS.md` files
- directory ownership, routes, entrypoints, or workflow rules have changed
- a repo-wide policy sentence about compatibility, shims, or debt needs to be propagated consistently
- root or docs hierarchy summaries have drifted from the actual tree
- you need either a create-mode pass or an update-mode pass instead of ad hoc doc edits

## Do Not Use

Do not use when:
- the task is ordinary README or product-doc editing
- the requested change is a tiny single-file wording tweak
- the user actually wants code changes rather than instruction maintenance
- the request is a feature spec with no repo-structure or instruction-maintenance component

## Core Workflow

1. Inventory first.
   - Find every existing `AGENTS.md`.
   - If none exist, map the root, major subtrees, entrypoints, workflow boundaries, and any submodule boundaries before drafting anything.
   - Read the root file, the major subtree files, and any dense leaf docs that already exist.
2. Decide the hierarchy shape before editing.
   - Root owns repo purpose, high-level structure, verified commands, and global rules.
   - Child docs exist only when a parent would otherwise become implementation-heavy or ownership clearly splits.
   - Prefer the fewest AGENTS files that keep responsibilities clear.
3. Choose create mode or refresh mode deliberately.
   - If a usable hierarchy already exists, update it before creating new files.
   - If the repo has no AGENTS coverage, create the root file first and add child docs only where the structure genuinely needs them.
4. Draft or refresh the root AGENTS file.
   - Root should route readers to child docs, not restate their internals.
   - Root should describe the current repo shape, source-of-truth order, and only the commands that are actually backed by the repo.
5. Draft or refresh child AGENTS files only where justified.
   - Child docs should stay local, operational, and narrow.
   - Remove repeated leaf detail from parents once a child doc owns that area.
6. Install the forward-only engineering policy.
   - State clearly that future updates do not need backward compatibility unless the user explicitly requires it.
   - Prefer the best current shape over preserving old seams.
   - Forbid shim code, compatibility wrappers, temporary dual paths, and debt-preserving migration layers unless the user explicitly asks for them.
7. Propagate shared policy changes consistently.
   - If the repo has a repo-wide AGENTS rule change, land it everywhere it belongs.
   - Keep the wording identical unless a local subtree needs a narrower version.
8. Deduplicate before finishing.
   - Parent files keep shared facts, ownership, and handoff.
   - Child files keep local detail.
   - Remove repeated leaf detail from parents when a child doc already owns it.

## Required Forward-Only Policy

- Add this rule somewhere obvious in the root `AGENTS.md`, and repeat it in child docs when local engineering rules matter:
  - future updates do not need backward compatibility unless the user explicitly asks for it
  - the best current design or file layout wins over preserving legacy seams
  - do not add shim code, compatibility wrappers, temporary dual implementations, or debt-preserving transitions
- If exact wording helps, use this sentence:
  - `Prefer the best current shape over backward compatibility. Future updates do not need shim code, compatibility wrappers, or debt-preserving transition layers unless the user explicitly asks for them.`

## Creation and Update Rules

- Create the root `AGENTS.md` first when a repo has no AGENTS coverage.
- Keep parent docs summary-oriented and child docs implementation-oriented.
- Treat new child docs as justified exceptions, not the default.
- Do not create leaf docs for:
  - test-only folders already explicitly parent-covered
  - local helper clusters that a parent doc already names as intentionally local
  - dense folders that a parent doc explicitly says should remain parent-owned
- Prefer the current repo shape over stale compatibility wording when the user wants the latest structure reflected.
- Remove outdated rules cleanly instead of documenting old and new paths in parallel, unless the user explicitly wants a migration note.
- If a repo mixes root code with git submodules, document the ownership split clearly.
- If root only owns launcher, version, docs, or CI wiring, keep submodule internals out of root AGENTS files.

## Verification

Before claiming completion:
1. Re-read every file you changed.
2. Grep for any repo-wide sentence you intended to propagate, especially the forward-only compatibility rule, and count the matches.
3. Re-check the few files that carried factual drift, not just the bulk-edited ones.
4. If you created new AGENTS files, verify that each one is necessary and that parent coverage would otherwise be insufficient.
5. If you changed hierarchy summaries, verify that every mentioned path actually exists.
6. Run `git status --short` in the root repo.
7. If the repo uses git submodules, run `git status --short` inside each touched submodule too.

## Output Expectations

When reporting the work:
- summarize the inventory result
- state whether the pass was create-mode, refresh-mode, or mixed
- say whether any new root or child `AGENTS.md` files were added
- say where the forward-only compatibility rule was inserted
- name the small set of files with factual drift
- cite the verification evidence you actually ran

## Anti-Patterns

- Do not start writing before inventorying the existing AGENTS tree.
- Do not assume a repo needs no AGENTS files just because none exist yet.
- Do not create new leaf docs just because a folder is large.
- Do not preserve legacy instructions by writing "old path/new path" dual guidance unless the user explicitly wants migration documentation.
- Do not recommend shim code, compatibility wrappers, or tech-debt-preserving transitions as the default engineering policy.
- Do not let root files pretend submodule code is root-owned.
- Do not repeat child implementation detail in parent docs.
- Do not claim the AGENTS pass is complete without fresh readback and git-status evidence.

## Remember

- Inventory first.
- Create the root doc when AGENTS coverage is missing.
- Update existing docs by default once a hierarchy exists.
- New child docs are rare and must be justified.
- Shared policy sentences need exact, repo-wide propagation.
- Future updates default to best current shape, not backward compatibility.
- No shim code or tech-debt-preserving transitions unless the user explicitly asks for them.
- Fresh verification evidence is required before any completion claim.
