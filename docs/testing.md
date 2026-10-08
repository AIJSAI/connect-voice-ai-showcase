# Testing: Connect

How Connect, a real-time voice AI sales role-play platform, is tested, deployed and watched. The grader's evaluations (the live replay of reference calls, red-team cases and calibration) have their own page: [evals](evals.md).

---

## The Suite

More than five thousand test definitions across three layers (a static count of the code in October 2026; parametrized tests expand further when they run):

| Layer | What it covers |
|-------|----------------|
| Python (pytest) | The voice agent and observer, the grader, the schemas, operational scripts and drill fixtures |
| Web unit tests (Vitest) | The web tier, the session service and the browser client |
| Browser end-to-end (Playwright) | End-to-end flows in a real browser |

## Score Tests

The grader's arithmetic is pinned by 101 deterministic tests, separate from any model call:

- **96 tests in thirteen test classes,** covering score bands for each rubric, rounding and floating-point edges, normalization to a common scale, bonus caps, per-criterion point enforcement, score floors, total validation, the mapping to the difficulty label, and the edges between bands.
- **5 tests in an inertness gate** that check that rewording a criterion cannot change the score arithmetic. How the model reads a reworded criterion is the live eval gate's job.

They run in the Python CI job on every pull request and every push.

## On Every Pull Request

- **The main branch is pull-request only,** behind a set of required checks, with squash-only merges.
- **Every workflow action is pinned to a commit,** not a tag.
- **The live eval gate** is a required check on every pull request, and it replays reference transcripts when grader content changes. See [evals](evals.md).

## On Every Deploy

- **Every product change deploys to staging, then to production.** Production deploys pass three approval gates, and only one of them moves traffic; it is approved in a quiet window. Documentation-only merges deploy nothing.
- **Infrastructure before code.** A deploy is blocked until any infrastructure change it carries has been applied to both staging and production.
- **Container images fail the build** on any fixable high or critical vulnerability.
- **Staging deploys run a grading smoke test** end to end, through the deployed staging grader.
- **The voice routes are smoke-tested** after each deploy.
- **Rollback is a per-app image update.** Re-running an old deploy is not a rollback, and the runbook says so.

## The Voice Path

The live half is tested the way a representative uses it:

- **Before the move to direct WebRTC:** a capture-parity sweep (six scenarios, three runs each, all 18 passed), then a 112-session staging comparison, two by two, of the old and new paths on two model versions, scored by the same grader. See [D1](decisions/01-first-voice-stack-and-direct-webrtc.md).
- **Onboarding dry run, August 18, 2026:** two production sessions with each of the nine personas, all 18 passed on the new path.
- **A detector for a model that goes silent, and a microphone check before each call.**
- **Per-office go-live:** each office passes a scripted smoke test through the same sign-in and call path a representative uses before it opens.
- **Pools and the busy screen:** a first staging drill showed Azure letting calls past a deployment's capacity and slowing them instead of refusing them, which is why the product decides admission to each realtime pool before a call starts. A second drill, before the pools went live in production, sent the first call to the primary pool and the second to the overflow, and turned the next ones away with the busy screen.
- **Deployments compared:** scripted test calls measured the realtime model on both production deployments before the faster one became the primary pool, and a bake-off on scripted calls picked the transcription successor. See [architecture](architecture.md#capacity-two-pools-and-one-busy-screen).
- **One voice per session:** the observer records the voice setting the realtime service reports for every conversation item, and a dashboard flags any session where it changed. See [D2](decisions/02-split-pipeline-and-one-voice-check.md).

In the first design, the one-second round trip was a hard merge gate for the voice pipeline.

## Monitoring

- **A dated check for model retirement,** run both as an alert rule and as a daily scheduled job, goes red if any grade still runs on the current grading model from nine days before its retirement.
- **A daily usage digest** runs as a scheduled job.

## Security Review

In July 2026 I commissioned a security and privacy review of the product, mapped to NIST 800-53 and run by AI review agents with an adversarial verification pass.

## How It Was Built

I built Connect alone and recorded more than thirty architecture decisions along the way, working with AI coding agents under a written rulebook: current documentation is fetched before any SDK change, and a citation check blocks a merge when a cited source does not support the code it is cited for.

---

[Back to the README](../README.md) · [Architecture](architecture.md) · [Evals](evals.md)
