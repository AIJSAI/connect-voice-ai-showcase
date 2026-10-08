# D3: Audit the Grader Against the Official Rubrics, Then Gate Every Grader Change

**Status**: The realignment shipped in February 2026; the live replay below has been a required check since July 2026.

---

## Context

The grader is the part of Connect, a real-time voice AI sales role-play platform, that a representative has to trust. A score only means something if it matches the rubric representatives are already held to: the company's official inside-sales and outside-sales rubrics.

In February 2026 I tested the AI grader against those rubrics, criterion by criterion, and it did not match them:

- **Half the outside-sales criteria were missing,** ten of the twenty.
- **Inside-sales empathy was weighted at double** what the rubric allowed.
- **Five criteria from the official inside-sales rubric were missing.**

None of this showed up as an error. The grader returned confident, complete-looking scores against a rubric that was not the business's.

## Decision

**Realign, with the rubric owners.** I took the findings to the rubric owners, who signed off on the corrections before anything changed. The scoring schema was restructured and both grading prompts were rewritten. On rubric and persona content, the rubric owners co-decide; the engineering is mine.

**Score only what a transcript can show.** The inside-sales rubric was written for human mystery shoppers, who can check things a transcript cannot, such as whether the phone was answered within three rings. Rather than let the model guess at those and return a score that was partly invented, the grader translates the rubric's observer judgments into behaviors visible in text. The February design granted the unobservable points automatically. Today's grader scores the unmeasurable item at zero as a coaching note and carries that category's points on what a transcript does show, such as the greeting and the use of the caller's name. Category totals stay comparable to the official rubric, and every scored point traces to something a person can read.

**Let the model judge and let code count.** The model returns its judgments under a strict schema; the server re-validates them and recomputes every total, bonus and band deterministically.

**Make every later change prove itself.** A realignment fixes today's drift. The gate below is what stops the next one.

## The Drift Gate

Two layers, one deterministic and one live:

1. **The arithmetic is pinned.** 101 tests fix the scoring math across bands, rounding, normalization, bonus caps, point enforcement, floors and totals, and the mapping to the difficulty label, which never changes a score; five of them form a gate that checks that rewording a criterion cannot move a score. They run on every pull request and every push.
2. **The judgment is replayed (since July 2026).** A required check runs on every pull request. When grader content changes (prompts, scoring code, rubrics, schemas or eval fixtures), it replays fourteen reference transcripts across eight personas, three times each, against the live staging grader, and fails the merge if the mean score drifts beyond a per-transcript tolerance that widens with measured run-to-run noise above a fixed floor. The reference scores are the grader's own, recorded on staging in July 2026, so the gate catches drift beyond tolerance from that baseline; agreement with human grades is what the report-only golden set will measure once those grades are final. One of the nine personas has no reference transcript yet. Nine red-team cases, including prompt injection and score manipulation, must hold. The same check runs weekly, because a model can drift with no change on our side.

Details of both layers are in [testing](../testing.md) and [evals](../evals.md).

## Fairness at Each Difficulty

Hard personas are written to decline. Grading a hard call on whether the AI said yes would punish the representative for the persona doing its job. Since July 2026 the grader judges closing skill against each persona's expected outcome, never curves the total, and records difficulty only as a label. It was switched on only after a do-no-harm calibration in which eight of nine personas moved within model noise; the ninth moved a little more, was reviewed, accepted and kept as a watch item.

## Consequences

- **(+)** Scores mirror the official rubric's structure, and the business's rubric owners approved the mapping.
- **(+)** A change to the grader's prompts, scoring code, rubrics or schemas cannot merge on the strength of looking right; it has to score within tolerance of the reference scores. The weekly run covers changes on the model's side.
- **(+)** Prompt injection and score manipulation are tested on every grader change, not assumed away.
- **(-)** The live replay costs paid model calls and time, so it runs only when grader content changes, plus a weekly run instead of a daily one.
- **(-)** Some dimensions (consistency, coaching quality, evidence grounding, a draft golden set) are report-only today, so they inform rather than block; the golden set waits on final human grades.

---

[Back to decisions](README.md) · [Architecture](../architecture.md)
