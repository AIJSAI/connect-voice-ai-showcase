# Decisions: Connect

Connect is a real-time voice AI sales role-play platform: franchise sales representatives practice spoken calls against AI buyer personas, and a separate AI grader scores each finished call against the company's sales rubrics. Its private repository holds more than thirty recorded architecture decisions.

The foundational one splits the product into a live half and a grading half joined only through storage; it is described in [architecture](../architecture.md#two-halves-joined-only-through-storage). The four below built on it, rewritten here in my own words.

| # | Decision | In one line |
|---|----------|-------------|
| [D1](01-first-voice-stack-and-direct-webrtc.md) | The first voice stack, and the move to a direct WebRTC connection | Start on LiveKit until the realtime voice models mature on Azure OpenAI; then build the product's own WebRTC connection, prove it on production traffic and retire LiveKit |
| [D2](02-split-pipeline-and-one-voice-check.md) | Split the voice pipeline, and check for one voice per session | First design: have the model write only the words and a speech engine speak them in one fixed voice per persona, and flag any session with more than one voice. Live calls left it in August 2026; a narrower check on the reported voice setting carried over |
| [D3](03-grader-audit-and-drift-gate.md) | Audit the grader against the official rubrics, then gate every grader change | Realign the grader with the rubric owners, score only what a transcript shows, and make every later change replay fourteen reference transcripts and hold nine red-team cases before it can merge |
| [D4](04-no-call-audio-and-no-scoreboard.md) | Keep no call audio, and no scoreboard | Turn speech into text as the representative talks, grade only the text, give no one a way to listen in, and rank no one |

---

[Back to the README](../../README.md)
