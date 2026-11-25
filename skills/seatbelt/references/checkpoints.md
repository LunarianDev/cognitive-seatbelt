# Selecting and evaluating checkpoints

Read this reference when designing or evaluating a `strict` or `mentor` checkpoint. Pick one question tied to the actual decision being delegated. Rotate formats when it helps, not on a fixed schedule.

## Core craft rules

- **No echo questions.** Never ask something whose answer appears verbatim in the explanation right above it. If your explanation says "two requests compete to rotate the same token," do not ask "what does serializing requests prevent?" with "two requests competing to rotate the same token" as an option. Ask about an applied consequence, failure mode, caller impact, or design tradeoff instead.
- **No hypothetical trivia.** Base every question on the real code, schema, callers, or constraints in front of you. Do not invent future features (like an index that does not exist yet) just to force a handoff question.
- **No policy citations.** Let the `Checkpoint (<mode>, <--flag>) | ...` footer mark the pause on its own. Do not add a sentence justifying why you paused, do not quote rules from `SKILL.md`, and do not link any skill file path (never write "This pause follows `[Seatbelt](.../SKILL.md)`" or "Seatbelt requires...").

## Calibrating to user experience

Match checkpoint depth to the signal already in the prompt, without asking the user to set a skill-level flag:

- **Experienced user (states invariants, constraints, or exact domain terms):** Skip textbook basics. Aim the check at blast radius, hidden assumptions, schema or API lock-in, concurrency or retry failure modes, and tradeoffs discovered during inspection.
  - *Avoid:* "Why do we need a `NOT NULL` constraint?" when the user already specified `email TEXT NOT NULL`.
  - *Prefer:* "Rebuilding the table drops attached indexes and triggers, and callers passing `None` will now hit `IntegrityError`. Which existing caller or downstream script is most likely to break first?"
- **Learning or unfamiliar user:** Translate jargon once in plain words, anchor questions in concrete examples from the task (a specific row, request, or file), and acknowledge partial mental models before filling the gap.

## Question formats

**Alignment:** Use when requirements leave a genuine human choice.
"The export covers completed orders only. Should refunded orders be excluded entirely or included with a zeroed net total?"
There is no hidden right answer; the user's reply sets the requirement.

**Recognition (non-echo):** Use when a quick choice catches a real misconception without copying wording from your explanation. Explain the mechanism (for example, an in-memory lock serializes token refreshes per session with a 5-second grace window for the old token), then ask about an unstated consequence:
"With an in-memory lock and a 5-second grace window, which failure can still happen?"

- A. Two parallel requests on the same server instance racing each other.
- B. A stolen refresh token remaining usable for up to 5 seconds after rotation.
- C. Valid single-tab refreshes failing under normal load.

Put distractors on plausible misunderstandings. Never label an option "recommended," keep the correct answer in a predictable letter, reveal the key early, or bundle multiple quizzes in one turn.

**Teach-back:** After explaining an expand/backfill/constrain migration, ask about the failure mode if steps change:
"If we created the `NOT NULL` table first and planned to backfill with an `UPDATE` afterward, what would fail during the copy?"
Accept any answer capturing that existing NULL rows get rejected before an update runs. Exact SQL terms are unnecessary.

**Prediction (`mentor`):** Present the premises without spoiling the outcome. Show that two requests arrive with the same refresh token and the first request immediately invalidates it on rotation. Before showing the test failure, ask:
"When the second request reaches the auth handler a millisecond later, what happens and why?"
Accept any accurate prediction qualified by actual system behavior. Always supply the premises needed for a fair guess; never ask the user to guess undisclosed code.

**Error spotting:** Show a short proposed sequence and ask which step breaks a stated constraint, such as adding a `NOT NULL` check before backfilling legacy rows. Avoid trick phrasing, double negatives, and arbitrary attention checks.

**Validation design:** After outlining a concurrency or retry fix, ask:
"What test would catch a regression where the code still works for one request at a time but fails under concurrent refreshes?"
Accept any test idea that exercises the actual failure condition; the agent writes and runs the test afterward.

## Mentor pacing guardrails

- **Do not spoil predictions.** If you intend to ask the user to predict a failure or behavior, ask before running or narrating the result that gives it away.
- **Keep teaching proportional.** Offer one focused learning moment per real boundary. Do not lecture through routine boilerplate or every edited line.
- **Let the user steer.** If the user says they only want to understand one area (such as the auth boundary) and delegate the rest (such as UI wiring), focus checkpoints there and move quietly through the rest.
- **Debrief, do not grill at the end.** Frame post-execution handoff in `mentor` as a short takeaway ("what to remember next time this pattern shows up") rather than a pop quiz.

## Handoffs by mode

Report actual changes and verification results first, then resolve the handoff boundary according to mode:

- **`standard`:** Close clean execution with a concise "what you now own" summary (what changed, what passed, and any caller impact). Pause for a handoff question only when execution materially diverged from the plan or uncovered a new risk or limitation.
- **`strict`:** Compare the actual outcome against the user's earlier demonstrated understanding. If earlier answers already cover what changed and its real consequences, briefly name that coverage and close without another question. Require a final check when:
  1. the initial check was skipped (a skip supplies no understanding to reuse),
  2. execution materially diverged from the agreed plan,
  3. a real caveat or limitation surfaced during implementation, or
  4. callers face a breaking behavior change not covered earlier.
  Never invent hypothetical future trivia or drop into verbatim-value recall just to ask a question.
- **`mentor`:** Close with a short debrief takeaway, or one reflection question if a key consequence remained uncovered.

## Fair evaluation and state

Decide the essential idea before asking. Grade meaning, not phrasing, length, grammar, or fluency. Accept short answers in plain words. If the question was ambiguous or your earlier explanation missed a premise, fix the premise instead of counting it as a failed attempt.

Handling replies:
- Partial understanding: confirm what is right and narrow the follow-up to the single missing point.
- `"idk"`, `"I don't know"`, or `"hint"`: offer a brief hint or concrete anchor without penalty and re-ask a narrower version.
- `"explain"`: explain the mechanism cleanly, then invite the user to put it in their own words right away, lower the mode, or skip.
- After two unsuccessful attempts: explain the missed point clearly, then let the user answer immediately in their next reply, lower the mode, or skip in one turn. Never force an intermediate `"retry"` turn.

Track in chat context only: pending boundary, question, essential idea, attempt count, and brief notes on demonstrated understanding for reuse. Keep skipped checks separate from answered checks. Do not keep scores, learner profiles, or persistent records, and do not invent claimed learning gains or token savings.
