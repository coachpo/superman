# AGENTS.md
## Purpose
This repository is a personal skills library for agentic development work. It is documentation-first, not an application with one shared build pipeline.
When you edit this repo, optimize for clear instructions, reusable patterns, and safe handoff to future agents.

## Repository Shape
- Top-level folders are skills such as `brainstorming/`, `systematic-debugging/`, `test-driven-development/`, `verification-before-completion/`, `writing-plans/`, and `webapp-testing/`.
- Most skill folders have a canonical `SKILL.md`.
- Some skills also include adjacent support docs, scripts, or examples.
- The root currently has no `package.json`, no repo-wide Python config, and no CI/test config.

## Source of Truth
When working inside a skill folder, use this order:
1. the local `SKILL.md`
2. adjacent docs in the same folder
3. local scripts and examples in that folder
Do not invent repo-wide workflow rules when the folder already defines them.

## Repo-Level Rules Files
As of the current snapshot:
- no `.cursorrules`
- no `.cursor/rules/`
- no `.github/copilot-instructions.md`
If any of those files are added later, update this document and treat them as repo-level instructions.

## First-Read Checklist
Before substantial edits:
1. Read the target folder's `SKILL.md` end to end.
2. Read any referenced local docs such as `visual-companion.md`, `testing-anti-patterns.md`, or `root-cause-tracing.md`.
3. Inspect nearby examples or scripts before changing patterns.
4. Match local style instead of normalizing the repo.
5. Keep edits narrow and justified by the skill's purpose.

## Build, Lint, and Test Commands
### Reality Check
This repo does **not** currently define a single root build, lint, or test runner.
That means:
- there is no grounded repo-wide `npm test`, `pnpm test`, `pytest`, or `make test`
- there is no grounded repo-wide single-test command
- do not claim a root build/lint/test step exists unless you add the config for it

### Runnable Commands That Actually Exist
Use only commands backed by files in this repo:
```bash
# Inspect helper usage first
python3 webapp-testing/scripts/with_server.py --help

# Example Playwright scripts
python3 webapp-testing/examples/element_discovery.py
python3 webapp-testing/examples/static_html_automation.py
python3 webapp-testing/examples/console_logging.py

# Brainstorming helper server
node brainstorming/scripts/server.cjs
```
Notes:
- `webapp-testing/SKILL.md` explicitly says to run helper scripts with `--help` first.
- `webapp-testing/scripts/with_server.py` is a Python 3 script.
- Some example scripts assume external dependencies like Playwright and a local app at `http://localhost:5173`.
- These are folder-specific utilities, not a repo-wide test suite.

### Single-Test Guidance
There is no built-in single-test command for this repository today.
If a future skill introduces tests, document all of the following in that skill folder and update this file:
- the full-suite command
- the single-test command
- any setup needed to run them
Do not present illustrative commands from skill prose as real repo commands. For example, `writing-plans/SKILL.md` and `test-driven-development/SKILL.md` include example commands such as `pytest tests/path/test.py::test_name -v` and `npm test path/to/test.test.ts`; those are teaching examples, not verified repo-level commands.

## Editing Priorities
Prefer:
- clearer operating guidance
- stronger guardrails
- better examples
- explicit verification steps
- minimal helper scripts only when prose is not enough
Avoid:
- unrelated refactors
- speculative architecture
- repo-wide tooling churn without a real need
- broad reformatting that hides the meaningful change

## Skill Structure Conventions
Unless a folder already proves a different pattern:
- skill folders use kebab-case
- the primary file is `SKILL.md`
- support docs use descriptive kebab-case names
- scripts live under `scripts/`
- runnable examples live under `examples/`
For new skills, start `SKILL.md` with frontmatter like:
```yaml
---
name: skill-name
description: Short, action-oriented description.
---
```
Optional metadata is fine when the folder already uses it, such as `license:` in `webapp-testing/SKILL.md`.

## Writing Style
The dominant repo style is direct, prescriptive, and structured.
Use:
- `##` and `###` headings
- short overview paragraphs
- numbered workflows and checklists
- strong guardrail words like **ALWAYS**, **NEVER**, and **MUST** when truly warranted
- tables for good/bad patterns or excuse/reality comparisons
- code fences for commands, examples, and templates
Write like an operator's manual. Avoid marketing language and vague, high-level prose.

## Naming, Imports, Types, and Formatting
- Folder names: kebab-case
- Support docs: descriptive kebab-case
- Python functions and variables: snake_case
- JS/TS functions and variables: camelCase
- Types/interfaces: PascalCase
- True constants: `UPPER_SNAKE_CASE`
Match the language-local style of the file you touch:
- Python: Python 3, standard-library imports at top, four-space indentation, straightforward helper functions
- JavaScript/CommonJS: `require(...)`, single quotes, semicolons, `const` by default
- TypeScript: prefer `import type`, keep signatures explicit when helpful, do not introduce `any` or `@ts-ignore`
There is no repo-level formatter config right now, so preserve surrounding style instead of reformatting large sections.

## Error Handling and Safety
Follow the repo's debugging mindset:
- fix root causes, not symptoms
- never add empty catch blocks
- raise or return errors with concrete context
- include timeouts and failure messages when waiting on external state
- stop after repeated failed attempts and re-evaluate
If a skill defines an iron law, hard gate, or anti-pattern section, treat it as binding for work in that folder.

## Verification Expectations
Because this repo is mostly instructions, verification is often document-based rather than CI-based.
Before claiming a docs change is complete:
1. Re-read the changed file end to end.
2. Verify that every command you mention is backed by files in the repo.
3. Verify that every file path you mention actually exists.
4. Label examples clearly when they are examples rather than repo commands.
5. If you changed a script, run the narrowest command that proves it still works.

## Onboarding a New Skill
When adding a new skill:
1. Create a new kebab-case folder.
2. Add `SKILL.md` with frontmatter, overview, when-to-use guidance, process, rules, and stop conditions.
3. Add support docs only when they reduce ambiguity.
4. Add scripts or examples only when the workflow truly benefits from them.
5. Keep the skill self-contained so another agent can use it without cross-repo guesswork.
6. If the skill introduces real runnable commands, document them here.

## Final Reminders
- This repo is a skill library, not an app scaffold.
- Local folder guidance beats generic assumptions.
- Repo-wide build/lint/test commands do not currently exist.
- Examples in prose are not automatically runnable repo commands.
- Favor precise, durable instructions over cleverness.
