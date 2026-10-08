# Testing: Connect

How Connect, a real-time voice AI sales role-play platform, is tested, deployed and watched. The grader's evaluations (the live replay of reference calls, red-team cases and calibration) have their own page: [evals](evals.md).

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
- **5 tests in an inertness gate** that check that rewording a criterion cannot move a score, so editing how a criterion is phrased does not change a representative's result.

They run in the Python CI job on every pull request and every push.

## On Every Pull Request

- **The main branch is pull-request only,** behind a set of required checks, with squash-only merges.
- **Every workflow action is pinned to a commit,** not a tag.
- **The live eval gate** runs as a required check whenever grader content changes. See [evals](evals.md).

## On Every Deploy

- **Every product change deploys to staging, then to production.** Production deploys pass three approval gates, and only one of them moves traffic; I approve it in a quiet window. Documentation-only merges deploy nothing.
- **Infrastructure before code.** A deploy is blocked until any infrastructure change it carries has been applied to both staging and production.
- **Container images fail the build** on any fixable high or critical vulnerability.
- **Staging deploys run a real grading smoke test,** end to end, through the actual grader.
- **The voice routes are smoke-tested** after each deploy.
- **Rollback is a per-app image update.** Re-running an old deploy is not a rollback, and the runbook says so.

## The Voice Path

The live half is tested the way a representative uses it:

- **Before the move to direct WebRTC:** a capture-parity spike (18 of 18 runs), then a 112-session staging comparison of the old and new paths across two model versions, scored by the same grader. See [D1](decisions/01-first-voice-stack-and-direct-webrtc.md).
- **Launch-day dry run:** 18 of 18 sessions passed on the new path.
- **A detector for a model that goes silent, and a microphone check before each call.**
- **Per-office go-live:** each office passes a scripted smoke test through the real product path before it opens.
- **Pools and the busy screen:** a staging drill showed Azure letting calls past a deployment's capacity and slowing them instead of refusing them, which is why the product decides admission to each realtime pool before a call starts and shows one busy screen once both are full.
- **Deployments measured, not assumed:** scripted test calls measured the realtime model on both production deployments before the faster one became the primary pool, and a bake-off on scripted calls picked the transcription successor. See [architecture](architecture.md#capacity-two-pools-and-one-busy-screen).
- **One voice per session:** the observer records the voice setting the realtime service reports for every conversation item, and a dashboard flags any session where it changed. See [D2](decisions/02-split-pipeline-and-one-voice-check.md).

In the first design, the one-second round trip was a hard merge gate for the voice pipeline.

## Monitoring

- **A dated check for model retirement,** run both as an alert rule and as a daily scheduled job, goes red if any grade still runs on the current grading model from nine days before its retirement.
- **A daily usage digest** runs as a scheduled job.

## Security Review

In July 2026, ahead of any real client data, I commissioned a security and privacy review of the product mapped to NIST 800-53, run by AI review agents with an adversarial verification pass. The first remediation wave shipped within days: pull-request-only changes behind nine required checks, every workflow action pinned, a dead-letter retention policy, and fencing in the grader so that a transcript is treated as content to grade, not as instructions.

## How It Was Built

I built Connect alone and recorded more than thirty architecture decisions along the way, working with AI coding agents under a written rulebook: current documentation is fetched before any SDK change, and a citation check blocks a merge when a cited source does not support the code it is cited for.

---

[Back to the README](../README.md) · [Architecture](architecture.md) · [Evals](evals.md)
