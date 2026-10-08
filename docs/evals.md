# Evals: Connect

In Connect, a real-time voice AI sales role-play platform, the grader's output is the product: a representative reads the score and decides what to practice next. So the grader is evaluated like a model, continuously, and not only tested like code. The deterministic score tests are on the [testing](testing.md) page; this page covers the evaluations of the model's judgment.

---

## The Baseline: Alignment With the Official Rubrics

Every eval below measures drift from a baseline, and the baseline had to be right first. In February 2026, testing the grader criterion by criterion against the official rubrics found half the outside-sales criteria missing and inside-sales empathy weighted at double what the rubric allowed. The rubric owners signed off on the realignment before it shipped. The full story is [D3](decisions/03-grader-audit-and-drift-gate.md).

## The Live Eval Gate

A required check on every pull request since July 2026.

- **When it runs.** Whenever grader content changes: prompts, scoring code, rubrics, schemas or eval fixtures. It also runs weekly, to catch drift in the model when nothing on the product side changed.
- **What it replays.** Fourteen reference transcripts spread across eight buyer personas, each graded three times against the real staging grader.
- **What fails it.** A mean score that drifts beyond a per-transcript tolerance. The tolerance widens with the measured run-to-run noise of that transcript, above a fixed floor, so a naturally noisy transcript does not raise false alarms.
- **Why weekly and not daily.** Each run makes real model calls. Weekly catches model drift at a fraction of the cost.

## Red-Team Cases

Nine adversarial transcripts run in the same gate, including prompt injection (a representative speaking instructions at the grader) and score manipulation. Any case that fails blocks the merge. Inside the grader, the transcript is fenced so that what is said in a call is treated as content to grade rather than as instructions.

## Calibration Before a Grading Change Goes Live

Difficulty-aware grading judges closing skill against each persona's expected outcome instead of whether the AI agreed, because hard personas are written to decline. Before it was switched on, a do-no-harm calibration compared scores with and without it: eight of nine personas moved within model noise. Only then did it go live.

## Report-Only Dimensions

Four more measures run today but do not block a merge: consistency, coaching quality, evidence grounding, and a draft golden set. They become blocking once the human grades behind them are final; until then they inform rather than gate.

## Model Changes

- **The grading model's named successor is deployed and switched off.** Shadow grading is built so real reports can be compared side by side before the switch, which is one setting. A dated check, run both as an alert rule and as a daily scheduled job, goes red if any grade still runs on the current model from nine days before its retirement.
- **The transcription model** was replaced ahead of its retirement after a bake-off on scripted calls, where the successor had no failed transcriptions and a shorter transcript lag (0.58 against 0.72 seconds). See [architecture](architecture.md#model-retirements-planned-ahead).
- **The realtime model** was evaluated through the voice-path comparison: 112 staging sessions across two model versions, scored by the same grader, before the production switch. See [D1](decisions/01-first-voice-stack-and-direct-webrtc.md).

## What the Evals Do Not Prove

The evals prove that the grader is stable, matches the rubric and resists manipulation. They do not prove that practice changes sales results. Connect is built to raise conversion by giving every representative realistic practice; offices running it report that effect, though it has not been formally measured, and a rising score inside the tool is not evidence of it on its own.

---

[Back to the README](../README.md) · [Architecture](architecture.md) · [Testing](testing.md)
