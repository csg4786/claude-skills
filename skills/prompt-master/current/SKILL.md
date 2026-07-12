---
name: prompt-master
description: Use this skill whenever the user wants Claude to write, craft, build, generate, or improve a prompt for an AI system or model — including phrases like "write me a prompt for X", "create a system prompt for...", "help me prompt-engineer this", "make an exhaustive/detailed/thorough prompt", or "turn this into a really good prompt". Also trigger when the user wants an existing prompt tightened up, made more robust, or checked for missing edge cases. This skill produces a genuinely exhaustive prompt — one that covers role, context, task steps, output format, constraints, examples, edge cases, and quality checks — not a quick one-liner. Do not use for simple one-off questions the user just wants answered directly; use it when the deliverable is itself a prompt meant to be reused or handed to a model.
---

# Prompt Master

A skill for producing exhaustive, professional-grade prompts: the kind that anticipate edge cases, specify format precisely, and leave as little as possible to chance when handed to a model.

## Why exhaustiveness matters

A vague prompt forces the model receiving it to guess — about scope, format, tone, what counts as "done," and what to do when something unusual comes up. Every guess is a chance for the output to miss what the user actually wanted. An exhaustive prompt front-loads those decisions so the executing model doesn't have to invent them. The goal isn't length for its own sake — a bloated prompt full of restating and hedging is its own failure mode — it's *coverage*: every angle a careful person would think to ask about has been considered and, if relevant, addressed.

## Process

### Step 1: Understand the target task

Before anything else, figure out what the prompt is *for*. Read what the user has already told you — sometimes the full picture is already in the conversation (an example of the desired output, a rough draft they want expanded, a task they've described in detail). Extract everything usable from that before asking questions; don't make the user repeat themselves.

### Step 2: Ask clarifying questions

The user has asked to be consulted before you write, so do that — but keep it efficient. Use `ask_user_input_v0` where the question has a small set of natural options (this is much easier for the user than free-form typing). Reserve open-ended text questions for things that can't be reduced to options, like "what should the output actually contain."

Cover whichever of these are still unknown and relevant — skip ones already answered by context:

1. **The core task** — what is the executing model actually supposed to *do*? (e.g., write code, analyze data, draft copy, make a decision, hold a conversation)
2. **Target model / platform** — is this prompt for Claude, another LLM, a coding agent, an image generator, or unspecified? This affects conventions (e.g., XML tags work well for Claude; other models may need different structuring).
3. **Audience for the output** — who or what consumes what the model produces? (an end user, another system, a file, a human reviewer)
4. **Inputs available** — what will the model have access to when it runs this prompt (documents, data, prior conversation, tools)?
5. **Output format** — free text, structured data (JSON/table/etc.), a specific document type, code, something else? Any length or structure constraints?
6. **Constraints and guardrails** — things it must never do, must always do, tone to avoid, safety or compliance boundaries, scope limits.
7. **Success criteria** — how would the user (or an evaluator) know the output was good? What does failure look like?
8. **Known edge cases** — has the user already run into specific failure modes they want prevented?

Don't ask about things that don't apply — e.g., "output format" barely matters for a casual conversational persona prompt. Use judgment about which of the eight areas are load-bearing for this particular prompt, and skip the rest rather than padding the interview.

### Step 3: Draft the prompt

Write the actual prompt using the coverage checklist in `references/coverage_checklist.md` as a mental pass — not as a rigid template to fill in verbatim. Not every prompt needs every section; a prompt for a short creative-writing task doesn't need the same scaffolding as a system prompt for a customer-support agent. Include a section only if it adds real information the executing model would otherwise have to guess.

General shape most exhaustive prompts benefit from, adapted to the task:

- **Role / framing** — who or what the model should act as, if relevant. Skip this for tasks with no persona.
- **Objective** — one clear statement of the goal, stated early so nothing after it is ambiguous about what it's in service of.
- **Context** — the background the model needs: domain, audience, prior state, why this task exists.
- **Inputs** — what the model will be given and in what form.
- **Instructions** — the actual steps or requirements, in the order that matters. Use imperative language ("Do X", not "You might want to X"). Number them if sequence matters; group them under headers if they cover distinct concerns (e.g., content vs. formatting vs. tone).
- **Output format** — the exact structure expected. If there's a template, show it. If it's free text, say what sections/length/style are expected.
- **Constraints** — hard boundaries: things never to do, things always to do, compliance or safety limits.
- **Examples** — at least one worked example when the task is non-obvious or the format is specific; examples resolve ambiguity faster than more prose. Use realistic, concrete examples over abstract placeholders.
- **Edge cases** — explicitly named situations that could trip up a naive execution (missing input, conflicting instructions, ambiguous requests, out-of-scope asks) and what to do about each.
- **Quality bar / self-check** — a short list the model can check its own output against before finishing, if that's useful for this task.

Explain *why* a rule matters wherever it isn't obvious, rather than issuing a bare command — this is what separates a prompt a model can reason about from a checklist it just pattern-matches against. Avoid restating the same requirement in three different sections; each addition should be new information, not reassurance.

### Step 4: Save and present

This is a deliverable meant to be copied and reused, so save it as a markdown file (see file-creation conventions: short files can be written directly; use clear headers). Use the `md` conventions already available to you — no special skill is needed for a plain markdown file. Name the file descriptively based on what the prompt is for (e.g., `customer-support-agent-prompt.md`, not `prompt.md`). Present the file to the user with `present_files`.

After presenting it, briefly flag anything you had to assume or leave underspecified because the user didn't have a strong preference — this gives them an easy way to course-correct without re-reading the whole thing.

## Judgment calls

- **If the user provides a rough or existing prompt to improve**, diff mentally against the coverage checklist, and tell them what you added and why, rather than silently rewriting it wholesale — they may have had reasons for choices you'd otherwise "fix."
- **If the task is small and genuinely simple** (e.g., "write me a one-line prompt to summarize articles"), don't force the full scaffold onto it — an exhaustive prompt for a trivial task is itself a failure of judgment. Confirm with the user if a lightweight version seems more appropriate than what they asked for.
- **If the user seems to want the prompt as a system prompt for a persistent agent/assistant** (as opposed to a one-off task prompt), lean harder on role, constraints, and edge-case handling, since that prompt will face inputs the user hasn't anticipated.
