---
name: "fundamentals-first-dev"
description: "Use when starting a new software project or making changes to an existing one: grill for shared understanding, keep a ubiquitous language, design deep modules, build vertical slices with TDD in small steps."
---

# Fundamentals-first development

Condensed from Matt Pocock's skills (github.com/mattpocock/skills, MIT): grilling, domain-modeling, codebase-design, to-spec, to-tickets, tdd, implement, code-review, retro. When working in Claude Code with his skills installed, prefer calling those directly; this file is the single-skill version for everywhere else.

Good code is code that is easy to change. Never do "spec to code and walk away". The human owns decisions (what to build, module shape, vocabulary, seams). Claude owns facts and tactics (looking things up, code behind agreed interfaces, in small tested steps).

## Step 0: Read the repo's own rules first
If the repo has an `AGENTS.md` (often with `CLAUDE.md` linked to it), read it before anything else. **Its conventions win over this skill** on layout and process:
- ADR location and template (e.g. `docs/decisions/NNN-*.md`) replace the `docs/adr/` default below.
- Its commands (lint/type-check/test one file) are the commands for the TDD loop.
- Its Do NOT rules and danger zones override every step here. Never create issues, push, merge, deploy or touch anything outside the repo unless it allows that or the user asks.
- Retro findings go through its self-improvement rules: friction note in `docs/friction/` titled by the symptom, then a proposed Learned Preference, then ideally a check that fails CI.
- Read the unit's own `AGENTS.md` and README before changing that unit.
- If it has no `GLOSSARY.md`, propose adding one and linking it from `AGENTS.md`.

## Main flow: idea to ship

### 1. Grill (no code until this is done)
Map the work as a **design tree**: every decision branches into the decisions that hang off it.
- Work in **rounds**. The **frontier** is every decision whose prerequisites are settled. Ask the whole frontier in one round, numbered, each with your recommended answer:

  ```
  ❓ **Q1** - **<title>**: <question, with options if useful>

  ➡️ <recommended answer>
  ```
- Word each question so "yes" accepts your recommendation.
- A question that depends on another question still open this round waits for a later round.
- **Facts are your job, never the user's.** If the answer is in the code, files, schema or docs, look it up (use a sub-agent if available) instead of asking. Only questions downstream of a pending lookup wait.
- **Decisions are the user's.** Put each one to them.
- Done when the frontier is empty and the user confirms shared understanding. Do not act before that.

### 2. Domain model, during the grilling (in a repo)
- **GLOSSARY.md** at the repo root, created lazily when the first term is resolved and updated inline as terms resolve, never batched. (If a legacy `CONTEXT.md` or `UBIQUITOUS_LANGUAGE.md` exists, offer to rename it.) Format:
  ```md
  # {Context name}
  {1-2 sentences on what this context is.}
  ## Language
  **Order**:
  A customer request to buy one or more items.
  _Avoid_: Purchase, transaction
  ```
  Be opinionated: one canonical word, alternatives under _Avoid_. Definitions say what a term IS, in 1-2 sentences. Only project-specific concepts, no general programming terms, no implementation details.
- While grilling: call out a term that conflicts with the glossary; sharpen fuzzy or overloaded words ("account": Customer or User?); invent concrete edge-case scenarios to test concept boundaries; check the code and surface contradictions with what the user says.
- **ADRs** in the repo's ADR folder (default `docs/adr/0001-slug.md`), created lazily. Offer one only when the decision is all three: hard to reverse, surprising without context, and a real trade-off. Use the repo's template if it has one; otherwise a single paragraph: context, decision, why.

### 3. Design module shape
Use this vocabulary exactly (no "component", "service", "API", "boundary"):
- **Module**: anything with an interface and an implementation, at any scale.
- **Interface**: everything a caller must know: types plus invariants, ordering, error modes, config, performance.
- **Depth**: behaviour a caller can exercise per unit of interface learned. Deep = lots behind a small interface; shallow = interface nearly as complex as the implementation.
- **Seam**: where an interface lives; where behaviour can change without editing that place.
- **Adapter**: a concrete thing filling a seam. **Leverage** (callers' gain) and **locality** (maintainers' gain) are what depth buys.
Principles: the **deletion test** (delete the module: if complexity vanishes it was a pass-through; if it spreads across callers it earns its keep). The interface is the test surface. One adapter means a hypothetical seam, two means a real one; don't add seams nothing varies across. Accept dependencies instead of creating them; return results instead of side effects.

### 4. Spec and tickets (multi-session work)
- **Spec**: synthesize from the conversation, no new interview. Sections: Problem, Solution (both from the user's view), a long numbered list of user stories ("As a <actor>, I want <feature>, so that <benefit>"), Implementation decisions (modules, interfaces, schema, contracts; no file paths or code), Testing decisions, Out of scope. First sketch the test seams: prefer existing ones, as high as possible, as few as possible, and confirm with the user.
- **Tickets**: tracer-bullet vertical slices, each a narrow but complete path through every layer, demoable alone, sized to fit one fresh context window. Each declares **Blocked by**. Prefactor first ("make the change easy, then make the easy change"). Wide mechanical refactors use expand, migrate in batches, contract. Show the breakdown and ask: granularity right? blocking edges right? merge or split anything? Iterate until approved.
- Keep specs and tickets as local files (e.g. `.scratch/<feature>/`) unless the user asks to publish them to an issue tracker.
- Single-session work skips straight to step 5.

### 5. Implement with TDD
- **New project: feedback loops before features.** Static types, a test runner, a linter, CI and a one-command way to run the app.
- **Agree the seams first.** Write down the seams under test, each with a one-line note on what it catches and misses, and confirm with the user. No test at an unconfirmed seam.
- Loop: one seam, one failing test, the minimum code to pass it. Repeat. Don't anticipate future tests or add speculative features. Refactoring is not part of this loop; it belongs to review.
- Tests verify behaviour through public interfaces and read like specifications. Avoid: implementation-coupled tests (mocking internal collaborators, testing private methods, checking the database instead of the interface); tautological tests (expected values computed the way the code computes them; use known-good literals or worked examples); horizontal slicing (all tests first, then all code).
- Mock only at system boundaries (external APIs, sometimes DB, time, randomness, sometimes filesystem). Never mock your own modules.
- Run the typechecker and single test files regularly, the full suite at the end. Commit to the current branch, one logical change per commit.

### 6. Review
Review the diff against a fixed point (branch, commit) on two axes, separately: **Standards** (the repo's documented standards and AGENTS.md rules; repo rules override the baseline; skip anything tooling already enforces) and **Spec** (does it do what the ticket/spec asked). Baseline smells, flagged as judgement calls: mysterious name, duplicated code, feature envy, data clumps, primitive obsession, repeated switches, shotgun surgery, divergent change, speculative generality, message chains, middle man, refused bequest. Refactor here.

### 7. Retro (after a build, especially one that went sideways)
Suggest changes to the environment, not the code: mechanical mistakes become automated checks; judgement calls become written standards. In a repo with `AGENTS.md`, write the friction note first, then propose the rule, and wait for approval.

## Existing codebase: onboarding and health
- First session in a repo: read AGENTS.md/CLAUDE.md, GLOSSARY.md and the ADR folder. Missing glossary? Build it from the code and confirm terms with the user.
- To find **deepening opportunities**: scope first (the user's named area, else hot spots from `git log --oneline`), then look for concepts that need bouncing between many small modules, shallow modules, pure functions extracted for testability while bugs hide in the callers, leaky coupling, and code that is hard to test through its interface. Present candidates with files, problem, solution, benefits (in leverage and locality terms) and strength (Strong / Worth exploring / Speculative), with no interface proposals yet. The user picks one; grill on it. Don't re-litigate ADRs unless the friction is real, and say so.

## Bugs
Check `docs/friction/` for the symptom first. Get a tight feedback loop: one command that goes red on this bug. Then fix with a regression test. Afterwards, retro: what would have prevented it? If there is no good seam to test it at, that is a deepening opportunity.

## Detours and context hygiene
- A question that can't be settled on paper (state model, UI feel, business logic)? Build a small throwaway **prototype** in its own directory or branch to answer it, then feed the answer back.
- Keep grilling, spec and tickets in one unbroken context. Start each ticket's implementation fresh, working from the ticket.
- A handoff document is only for moving to a new directory, a new tool, or a colleague; otherwise continue or compact. Reference existing artifacts instead of repeating them, and redact secrets.

## Guardrails
- Never write more code than the next test needs.
- Use glossary terms only; no new synonyms.
- Ask before changing any public interface or anything an ADR records.
- Be concise. Say when a shortcut would hurt changeability.