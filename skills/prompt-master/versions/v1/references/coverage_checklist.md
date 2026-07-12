# Coverage Checklist

A reference list of angles to consider when drafting an exhaustive prompt. Not every item applies to every prompt — use this to spot gaps, not as a form to fill in mechanically. If an item genuinely doesn't matter for the task at hand, leave it out rather than padding the prompt with boilerplate.

## 1. Framing
- **Role/persona**: Should the model adopt a specific identity, expertise level, or point of view? Does the persona affect vocabulary, tone, or the kinds of judgment calls it should make?
- **Objective**: Is there one unambiguous sentence stating the goal? Would two reasonable people reading only that sentence agree on what "done" looks like?

## 2. Context
- **Domain background**: What does the model need to know about the subject matter that it might not otherwise assume?
- **Audience**: Who ultimately reads or uses the output? Does that change the level of technicality, formality, or detail?
- **History/state**: Is this a one-shot task or part of an ongoing process? Does prior context matter?
- **Why this task exists**: Sometimes stating the underlying purpose helps the model make good judgment calls on things the instructions didn't anticipate.

## 3. Inputs
- What exact inputs will the model receive, and in what form (files, pasted text, structured data, images)?
- What should the model do if an expected input is missing, malformed, or contradictory?
- Are there inputs the model should explicitly ignore or treat with skepticism (e.g., untrusted user-supplied content)?

## 4. Instructions
- Are the steps in the order that actually matters, or could reordering break something?
- Are there decision points where the model needs criteria to choose between options?
- Is there a distinction between "must do," "should do," and "may do at its discretion"? Exhaustive doesn't mean everything is mandatory — flagging what's flexible is itself useful information.
- Are there things explicitly out of scope that a model might otherwise wander into?

## 5. Output format
- Exact structure: sections, headers, ordering, required fields.
- Length: target, minimum, maximum — and what to do if the natural answer doesn't fit.
- Style: formal/casual, technical/plain-language, first-person/third-person.
- Machine-readability: if downstream systems parse the output, is the format specified precisely enough to parse reliably (e.g., exact JSON schema, delimiters)?
- What the output should NOT include (e.g., no preamble, no meta-commentary, no markdown if it's going into a plain-text field).

## 6. Constraints and guardrails
- Hard prohibitions (things that must never appear or happen).
- Compliance, legal, safety, or brand boundaries relevant to the domain.
- Tone or content to avoid (e.g., no hedging, no fabricated citations, no over-promising).
- Resource or scope limits (e.g., word count, number of options offered, number of tool calls).

## 7. Examples
- At least one worked example for any task where the desired output is hard to describe in the abstract.
- Consider showing a *counter-example* — a plausible but wrong output — when a common failure mode exists and prose alone hasn't reliably prevented it in similar prompts.
- Keep examples realistic and specific rather than generic placeholders (a real-sounding customer complaint beats "insert issue here").

## 8. Edge cases and failure modes
- Ambiguous or underspecified requests — should the model ask, guess, or pick the safest default?
- Out-of-scope requests — what's the model's fallback?
- Conflicting instructions from context vs. this prompt — which wins?
- Missing or corrupted inputs.
- Requests that push toward a boundary in section 6 — how should the model decline or redirect?

## 9. Quality bar and self-check
- What would make the executing model's own output obviously wrong or low-quality if it reread it?
- Is there a short checklist worth having the model verify before finalizing (e.g., "does this address every part of the request," "is every claim grounded in the provided input")?
- Should the model iterate internally before presenting a final answer, or is a single pass expected?

## 10. Meta
- Should the prompt itself explain *why* rules exist (recommended — models handle novel situations better when they understand intent, not just the letter of a rule)?
- Is the prompt length proportional to the task's real complexity, or has restating crept in? Every sentence should add information the model didn't already have.
