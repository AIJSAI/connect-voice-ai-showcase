# Testing: Connect

How Connect is tested, deployed and watched. The grader's evaluations (the live replay of reference calls, red-team cases and calibration) have their own page: [evals](evals.md).

---

## The Suite

More than five thousand test definitions across three layers (a static count, October 2026):

| Layer | What it covers |
|-------|----------------|
| Python (pytest) | The voice agent and observer, the grader, the schemas, operational scripts and drill fixtures |
| Web unit tests (Vitest) | The web tier, the session service and the browser client |
| Browser end-to-end (Playwright) | End-to-end flows in a real browser |

The suite grew with the product, from 199 passing tests right after the February 2026 rubric realignment to more than a thousand by June.

## Score Tests

The grader's arithmetic is pinned by 101 deterministic tests, separate from any model call:

- **96 tests across thirteen score classes:** score bands for each rubric, rounding and floating-point edges, normalization to a common scale, bonus caps, per-criterion point enforcement, score floors, total validation, difficulty bands, and the edges between bands.
- **5 tests in an inertness gate** that prove rewording a criterion cannot move a score, so editing how a criterion is phrased never changes a representative's result.

They run in the Python CI job on every pull request and every push.

## On Every Pull Request

- **The main branch is pull-request only,** behind nine required checks, with squash-only merges.
- **Every workflow action is pinned to a commit,** not a tag.
- **The live eval gate** runs as a required check whenever grader content changes. See [evals](evals.md).

## On Every Deploy

- **Every product change deploys to staging, then to production** behind an approval gate.
- **Container images fail the build** on any high or critical vulnerability finding.
- **Staging deploys run a real grading smoke test,** end to end, through the actual grader.
- **The voice routes are smoke-tested** after each deploy.
- **Rollback is a per-app image update.** Re-running an old deploy is not a rollback, and the runbook says so.

## The Voice Path

The live half is tested the way a representative uses it:

- **Before the move to direct WebRTC:** a capture-parity spike (18 of 18 runs), then a 112-session staging comparison of the old and new paths across two model versions, scored by the same grader. See [D1](decisions/01-first-voice-stack-and-direct-webrtc.md).
- **Launch-day dry run:** 18 of 18 sessions passed on the new path.
- **A silent-model detector and a microphone check before each call,** both added after the first production switch went quiet mid-call.
- **Per-office go-live:** each office passes a scripted smoke test through the real product path before it opens.
- **One voice per session:** the observer records the voice the realtime service reports for every conversation item, and a dashboard flags any session where it changed. See [D2](decisions/02-split-pipeline-and-one-voice-check.md).

In the first design, the one-second round trip was a hard merge gate for the voice pipeline, and the time to first token, a pilot launch criterion, measured under seven tenths of a second at the 95th percentile over 25 samples.

## Monitoring and Incidents

Two incidents shaped the monitoring more than any plan did.

**The grading stall (June 2026, mid-pilot).** A fix for duplicate coaching emails left grading claims stuck. For about three days grading fell from nearly every session to about one in seven, and no alert fired. I recovered the stranded reports, moved grading onto a Service Bus queue with duplicate detection and a dead-letter queue, replaced the email guard with a single terminal "already emailed" marker that cannot deadlock, and added the alerts that would have caught it.

**The probe that nobody read.** On launch night, the only production availability test turned out to be probing the product's old address and failing every run, with nobody reading the results. A new probe with a severity-one alert replaced it.

Other standing signals:

- **A dated alert for model retirement** fires if any grade still runs on the current grading model close to its retirement date.
- **A daily usage digest** runs as a scheduled job.

## Security Review

In July 2026, ahead of any real client data, I commissioned a security and privacy review of the product mapped to NIST 800-53, run by AI review agents with an adversarial verification pass. The first remediation wave shipped within days: pull-request-only changes behind nine required checks, every workflow action pinned, a dead-letter retention policy, and fencing in the grader so that a transcript is treated as content to grade, not as instructions.

## How It Was Built

I am Connect's only engineer. I built it with AI coding agents working under a written rulebook: current documentation is fetched before any SDK change, and a citation check blocks a merge when a cited source does not support the code it is cited for.

---

[Back to the README](../README.md) · [Architecture](architecture.md) · [Evals](evals.md)
