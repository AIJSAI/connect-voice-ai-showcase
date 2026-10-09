# D4: Keep No Call Audio, and No Scoreboard

**Status**: In force.

---

## Context

Connect, a real-time voice AI sales role-play platform, hears every word a representative says while practicing. In-home senior care is HIPAA-regulated, and the representatives work for the franchise owners, not for the corporate office that provides the product. A practice tool earns its use only if people are willing to sound unpolished in it: to try an opening that does not work, lose the call and run it again.

Two things would undercut that. A recording of every practice call is a sensitive record that has to be stored, secured and someday explained. A scoreboard turns practice into a contest, and a low score into something to avoid rather than something to work on.

## Decision

Design both out of the product:

- **No call audio is kept.** The representative's speech becomes text inside the realtime session as they talk, and only that text is graded. There is no audio recording to store.
- **The product gives no one a way to listen to a session,** live or afterward, and there is no audio to play back later.
- **No scoreboard and no ranking** anywhere in the product. No score is ranked against anyone else's, and running the scenario again is the intended response to a low one.

These choices cover audio and ranking. The written transcript and report are kept and emailed, and the terms of use each person accepts say who receives them.

## Consequences

- **(+)** There is no audio recording to protect, and representatives practice knowing Connect never saves their voice.
- **(+)** Practice stays practice: there is no leaderboard to climb or to fall down.
- **(-)** The transcript is the only record of what was said, so the grader scores the words, not how they sounded.
- **(-)** Transcription becomes a single point of failure for grading: a failed transcriber leaves no report, and with no audio kept there is nothing to transcribe again. That is why a transcription model's retirement date is treated as a scheduled product risk; see [model retirements](../architecture.md#model-retirements-planned-ahead).

---

[Back to decisions](README.md) · [Architecture](../architecture.md)
