# Selecting and evaluating checkpoints

Read this when generating or evaluating a Strict or Mentor checkpoint. Choose one question that targets the decision actually being delegated. Rotate formats when useful, not on a fixed schedule.

## Question formats

**Alignment:** "The export should include completed orders only. How should refunded orders be handled?" Use this when requirements still leave a human decision. An answer resolves the requirement; there is no hidden correct preference.

**Recognition:** Explain that two simultaneous refresh requests can compete to rotate the same token and that the proposed serialization adds some waiting time. Ask: "What does serializing refresh requests prevent?"

- A. Tokens reaching their expiration time.
- B. Two requests competing to rotate the same token.
- C. Every possible authentication failure.

The key concept is concurrent rotation. The distractors represent plausible misunderstandings. Do not label the correct option as recommended, consistently put it first, reveal the key before the user responds, or bundle several quizzes into one message.

**Teach-back:** After explaining an expand/backfill/constrain migration, ask: "Why must existing rows be backfilled before the new constraint is enforced?" Accept any explanation that existing rows would otherwise violate the constraint. Do not require SQL terminology. If the user says it makes queries faster, explain the constraint issue and ask a narrower follow-up.

**Prediction:** In Mentor, show that two requests use the same original token while each refresh rotates it. Before revealing the observed failure, ask: "What might happen to the second request after the first rotates the token?" Accept the prediction that it may use a stale or rejected token, qualified by the actual system behavior. Supply missing premises rather than expecting the user to guess an undisclosed implementation.

**Error spotting:** Show a short proposed sequence and ask which step violates the stated prerequisite. Use a mistake relevant to the work, such as enabling a required-field constraint before backfill. Avoid grammatical traps, double negatives, obscure trivia, and arbitrary attention checks.

**Validation design:** Explain the concurrency fix and ask: "What test would distinguish this fix from code that only works for a single refresh request?" Accept a simultaneous-request test that checks the documented outcome. The agent can write and run it afterward.

**Handoff:** After reporting the result and checks, ask: "In one sentence, what changed and what consequence should the next maintainer remember?" Assess against the actual result, including a known limitation where relevant.

## Fair evaluation

Decide the essential idea before asking. Evaluate meaning rather than phrasing, length, grammar, or fluency. Accept equivalent language and short answers. If the question was ambiguous or the agent's explanation was wrong, repair the premise rather than counting it as a user failure. Recognize partial understanding and correct only the missing point.

Track only the pending boundary, question, essential idea, and attempts in chat context. Do not build scores, psychological profiles, or persistent learner records. A correct selection establishes the chosen recognition check, not proof of general competence. Do not invent measured cognitive benefits or token savings.
