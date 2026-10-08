# Decisions: Connect

Connect is a real-time voice AI sales role-play platform. Its private repository holds more than thirty recorded architecture decisions. These four are the ones that shaped the product most, rewritten here in my own words.

| # | Decision | In one line |
|---|----------|-------------|
| [D1](01-first-voice-stack-and-direct-webrtc.md) | The first voice stack, and the move to a direct WebRTC connection | Start on a voice platform while the models mature; once Azure caught up, build the product's own WebRTC connection to Azure OpenAI, prove it on production traffic, then retire the platform |
| [D2](02-split-pipeline-and-one-voice-check.md) | Split the voice pipeline, and check for one voice per session | When the model's own speech made a persona stop sounding like one person, take speaking away from the model, and check every session for a single voice |
| [D3](03-grader-audit-and-drift-gate.md) | Audit the grader against the official rubrics, then gate every grader change | Realign the grader with the rubric owners, score only what a transcript shows, and make every later change replay reference calls before it can merge |
| [D4](04-coaching-boundary.md) | The coaching boundary | Office-level trends for corporate coaching, never one representative's score, and a product designed to support the rule |

The foundational choice, splitting the product into a live half and a grading half joined only through storage, is described in [architecture](../architecture.md#two-halves-joined-only-through-storage).

---

[Back to the README](../../README.md)
