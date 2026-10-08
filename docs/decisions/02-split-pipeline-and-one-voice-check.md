# D2: Split the Voice Pipeline, and Check for One Voice per Session

**Status**: Done in the first design (May 2026). The split was retired with the first voice stack; the one-voice check carried over in a narrower form.

---

## Context

In Connect, a real-time voice AI sales role-play platform, a buyer persona only works if it sounds like the same person from the first word to the last. In the first design, the realtime model both chose the words and spoke them, and partway through some sessions the persona's voice changed: listeners heard a different-sounding person mid-conversation. For a representative practicing a pitch, the buyer they were talking to stopped being one person, and the exercise broke.

It surfaced in development sessions and in live demonstrations in April 2026. Telemetry showed the voice setting the product sent stayed fixed for the whole session. The drift came from inside the realtime model's own speech synthesis, downstream of every setting the application controlled. No instruction to the model could fix a problem in how the model produced sound.

## Decision

Split the pipeline so that speaking was no longer the model's job:

- **The realtime model produced text only.** It still listened, reasoned and decided what the buyer said.
- **Azure's speech service spoke that text in one fixed voice per persona.** The same persona always used the same voice, by construction.
- **A per-session check** counted the distinct speech voices in every session and treated any session with more than one as a regression.
- **The one-second round trip stayed a hard gate** for the merge, since the split added a hop.

## Results

- Every staging session held one voice before the change merged.
- In staging, the speech engine began speaking in about a quarter of a second on average, and never took longer than about a third of a second across a seven-session smoke test.
- Warming the speech service's sign-in token ahead of the first turn cut that step from more than three seconds to a few hundredths of a second.
- The fix shipped in mid-May 2026, before the franchise pilots began in June.

## Consequences

- **(+)** One voice per persona, by construction rather than by hope.
- **(+)** Model quality and voice quality were decoupled: a model upgrade could no longer change how a persona sounded.
- **(+)** Any of Azure's catalog voices could be assigned to a persona with a single setting.
- **(-)** An extra hop in every reply, and a second Azure service in the live path.

## What Happened Next

The split did not survive the move to a direct WebRTC connection ([D1](01-first-voice-stack-and-direct-webrtc.md)). On today's path the realtime model speaks in its own built-in voice, chosen per persona when the session is minted, and the separate speech engine is kept only for an end-to-end smoke test.

The check survived in a narrower form. The server-side observer records the voice setting the realtime service reports, for the session and for every conversation item, and a dashboard query flags any session where that reported setting changed. It is a check on the service-reported voice setting, shown as a dashboard tile. The trade is plain: the first design ruled drift out by construction; today's design relies on the model's own voice holding steady, and the check catches a changed voice setting, which in the first design stayed fixed even while the audio drifted.

---

[Back to decisions](README.md) · [Architecture](../architecture.md)
