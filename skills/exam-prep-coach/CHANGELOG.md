# Changelog — exam-prep-coach

**Note on v1:** v1 below was the initial draft written before any live testing — it was never actually
packaged or installed on its own. Testing (rounds 1-4, using a real GATE CS prep scenario) found four
real bugs before the skill was ever handed over, all fixed in the same pass. v2 is the first version
actually packaged and offered for install. It's kept here as v1 anyway so the "what problem did this fix"
reasoning is preserved and diffable, not lost.

## v2 — 2026-07-12 (first installed version)
Fixes found through live testing on a real GATE CS prep scenario, all applied before hand-off:

- **Two-stage diagnostic**: replaced the single flat 10-20 question diagnostic (which gave only 1-2
  questions per topic — not enough to reliably judge any one topic) with a broad screening pass for
  initial plan sequencing, followed by a proper ~10-question deep-dive diagnostic per topic, run right
  before that topic starts in the plan. The topic's first quiz now does double duty as its deep dive.
- **Study plan is provisional early on**: Step 4 now explicitly says topic ordering may shift once a
  topic's deep-dive data comes in, rather than treating the screening-pass plan as fixed.
- **Diagnostic/quiz fidelity**: quiz questions must now mirror the real exam's actual difficulty,
  format, and question types (MCQ/MSQ/NAT etc.), not generic textbook-definition recall — a diagnostic
  built on easy conceptual questions scored 12/12 in testing for a candidate who scored 28/100 on the
  real exam, which is exactly the false-confidence failure mode this fix targets. If a diagnostic makes
  someone look much stronger than a real past exam score they report, the skill now flags it as
  unreliable and re-tests harder instead of building the plan on it.
- **Resume search lag ≠ no prior session**: if the candidate signals continuation but conversation
  search comes back empty (search indexes with a lag), the skill now says so and asks the candidate to
  recap or paste their last Progress Snapshot, instead of silently restarting them from scratch.
- **Pasted snapshots get confirmed too**: previously only search-recovered snapshots were checked back
  with the candidate before being trusted; pasted ones can go stale too (candidate may have studied more
  since), so now both are confirmed.

## v1 — 2026-07-12 (initial draft, not separately installed)
Full pipeline: identify exam → research syllabus/pattern via web search → intake questions + diagnostic
quiz → build study plan → deliver notes/high-yield concepts → quiz (strict syllabus default, adaptive
weak-area on request or when warranted) → decide next steps based on performance → session-continuity
via Progress Snapshots and conversation search.

