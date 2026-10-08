# D1: The First Voice Stack, and the Move to a Direct WebRTC Connection

**Status**: Done. Live calls moved to the new path in August 2026; the first stack was retired in September 2026.

---

## Context

When I started building Connect, a real-time voice AI sales role-play platform, in January 2026, the realtime voice models and the ways to reach them were still maturing. Connect's first production stack ran on LiveKit: LiveKit Cloud carried the audio through its media servers to an agent worker, and that worker drove Azure OpenAI's realtime model.

That was the right call at the time. It let me build and ship what mattered to the business (the buyer personas, the grader, the pilots) without first solving real-time media transport myself.

By July 2026 three things had changed:

1. **Azure offered browser-direct WebRTC** for its realtime model as a generally available service.
2. **GPT Realtime 2.1 reached Azure OpenAI** in early July 2026, so Azure no longer trailed OpenAI by a model release.
3. **A network launch was coming.** Growing past the pilots would have meant moving up to a much more expensive voice-platform tier, on top of usage.

The dependency was the media transport, not the model. The model was already Azure OpenAI's; the platform sat between the browser and it.

## Options Considered

| Option | Outcome |
|--------|---------|
| Stay on the first stack | Workable, but the company would pay a platform fee that grows with the network for a layer it no longer needed |
| An alternate Azure voice engine behind the same transport | Tried in June and July. It kept the first stack's transport, so it removed neither the dependency nor the fee, and its parity checks never went green |
| Connect to OpenAI directly | Rejected: the product's model calls stay on Azure OpenAI, inside the Azure environment the rest of the product runs in |
| **Azure OpenAI's own browser-direct WebRTC** | **Chosen** |

## Decision

Build the product's own WebRTC connection to Azure OpenAI's GPT realtime model, with the product's server in control of every call:

- The server reserves a capacity slot, mints a short-lived session that carries the persona's instructions, and relays the WebRTC handshake. The browser never holds a key and never sees the instructions.
- A server-side observer joins each call over a separate channel and captures the transcript, so the graded record does not depend on the browser.

The decision was first recorded as the target for whenever the product left the platform, then brought forward to before the network launch once the newer model reached Azure.

## How It Shipped

The move was staged so that each step could be undone:

1. **A capture-parity sweep** in July 2026 checked that the server-side capture on the direct path matched the old path: six scripted scenarios, three runs each, and all 18 runs passed.
2. **Built dark.** The session service, browser client, observer and capacity ledger shipped dormant behind a flag.
3. **Compared side by side.** A 112-session staging comparison of the old and new paths, across two model versions and scored by the same grader, came back green for the new path on the newer model.
4. **Switched** in August 2026, with the first stack kept as a hot fallback, a detector for a model that goes silent, and a microphone check before each call. On August 18, 2026, the day the slow rollout opened, an onboarding dry run in production, two sessions with each of the nine personas, passed all 18 on the new path.
5. **Proved on production traffic before retiring.** An audit a month later showed the fallback had gone unused. I decided to run the new path only, and decommissioned the first stack's agents, the fallback and the platform project.

## Consequences

- **(+)** The company owns its scaling, within its own Azure quota, and pays no voice-platform fees.
- **(+)** One fewer vendor and one fewer hop between a representative and the buyer.
- **(+)** The live audio path now runs on the same Azure platform as the rest of the product.
- **(-)** Little expected latency gain: well under a fifth of a second, because model inference dominates a turn. Speed was never the reason.
- **(-)** The browser now holds the media connection, so the observer exists to keep the transcript and the persona authoritative. See [honest practice](../architecture.md#honest-practice-by-construction).
- **(-)** Azure offers realtime WebRTC in a limited set of regions, so capacity is planned per deployment: two pools of the same model, the faster one first, and one busy screen with a retry countdown once both are full. See [capacity](../architecture.md#capacity-two-pools-and-one-busy-screen).
- **(-)** The realtime model now speaks in its own built-in voice, so the speech split of [D2](02-split-pipeline-and-one-voice-check.md) no longer applies; the voice check is now a dashboard query over the voice setting the observer records.

---

[Back to decisions](README.md) · [Architecture](../architecture.md)
