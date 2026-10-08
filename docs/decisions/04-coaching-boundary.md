# D4: The Coaching Boundary

**Status**: In force as a policy, worked out with the company's legal and employment teams. The product's own design choices, listed below, are mine.

---

## Context

Connect, a real-time voice AI sales role-play platform, produces a score for every practice call. That is what makes it useful to the representative, and it is also what makes it risky in this business:

- **In-home senior care is HIPAA-regulated.**
- **Each franchise owner employs their own staff.** The representatives practicing in Connect work for the franchise owner, not for the corporate office that provides the product. Anything corporate builds for those staff raises joint-employer and franchise-agreement questions.
- **A per-person score invites per-person use.** Once a score exists, someone will want to rank by it, or use it to evaluate a hire.

A practice tool that quietly turns into an evaluation tool would put the franchise model's legal lines at risk.

## Decision

**The coaching rule.** Corporate coaches may use office-level trends and general best practice, never an individual representative's score, to coach or evaluate franchise employees. I worked the rule out with the company's legal and employment teams and communicated it to the corporate coaches. Any recruiting or pre-hire use waits on legal review.

**Terms of use.** I co-owned the terms of use, drafted with outside counsel and carrying a joint-employer statement. Each person accepts them once per version, inside the franchise portal.

**Design choices consistent with the rule.** The product does not enforce the rule; these choices keep it a practice space:

- **No scoreboard and no ranking.**
- **The coaching report is written for the representative.** It quotes their own words back to them with one suggested focus.
- **No automatic routing of individual results to managers.** A representative can copy a supervisor on a session's report before the call; an automatic manager copy is built but stays off until legal review.
- **No call audio is kept, and nobody listens to sessions.** Speech becomes text as the representative talks, and only the text is graded.
- **Erasure requests** cover the voice-path stores.

## Consequences

- **(+)** The product stays a practice space, consistent with the franchise model's legal lines.
- **(+)** Office owners still see where to focus their staff's practice.
- **(-)** The rule governs how scores are used, not who can read a report. As the terms of use disclose, a copy of each report goes to a corporate training mailbox, so the rule depends on the coaches who hold to it.
- **(-)** An automatic manager copy of an individual's report, and any recruiting or pre-hire use, wait on legal review before they are switched on.

---

[Back to decisions](README.md) · [Architecture](../architecture.md)
