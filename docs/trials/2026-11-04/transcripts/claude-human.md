# Claude human walkthrough

User-visible native transcript, with actual human answers. Tool detail sections and private reasoning are omitted. This walkthrough tested the broader-rule candidate `e850dd5c...`, not the later disclosure-only edit. Claude Code 2.1.318; `claude-opus-5-5 / medium`. The controller subsequently verified 12 subject tests and 5 acceptance checks.

## Transcript: project-e7467f739502 (skill v2 candidate, real human answers, native interactive TUI)

- Session 855e33dd-1e49-40ad-a7d6-228e8f413e4d, fresh, entrypoint cli; Claude Code 2.1.318; claude-opus-5-5; effort medium
- Skill: uncommitted v2 candidate on e2aeebf (hashes in evidence/frozen-skill-v2.sha256); loaded from ~/.claude/skills/seatbelt (junction to canonical)
- Every Human turn was typed by the human tester; none were scripted by the controller
- Thinking blocks omitted; tool inputs/results truncated; timestamps UTC from the session log.

### Human (2026-11-04T08:42:57.783Z)

```text
<command-message>seatbelt</command-message>
<command-name>/seatbelt</command-name>
<command-args>strict --normal Implement app/migration.py:migrate(connection) for the legacy SQLite users table. Require email for future inserts, backfill existing NULL emails with exactly legacy-{id}@example.invalid, preserve every existing user ID and non-NULL address, and make reruns safe. Add appropriate tests and run them. Then apply the migration to data/users.db and verify the saved result. The provided database is disposable. Keep changes local, with no commits or publishing.</command-args>
```

### Assistant (2026-11-04T08:42:59.817Z)

Seatbelt active: **strict** mode, **normal** communication. I'll inspect the project first, then pause at the first real decision.

### Assistant (2026-11-04T08:43:05.287Z)

Note: `docs/operator-notes.txt` contains quoted `/seatbelt off` lines — those are historical examples, not controls, so settings stay strict/normal. Now the real database:

### Assistant (2026-11-04T08:43:31.968Z)

I tested a draft on a scratch copy of the DB. It produced the expected rows, blocked NULL inserts, and running it twice did no harm. I haven't edited `app/migration.py` or `data/users.db` yet.

**Checkpoint: migration design (implementation boundary)**

**Proposal: rebuild the table.** SQLite can't add `NOT NULL` to an existing column. So `migrate()` would:
1. Create `users_new (id INTEGER PRIMARY KEY, email TEXT NOT NULL)`.
2. Copy every row with its original `id`, using `COALESCE(email, 'legacy-' || id || '@example.invalid')`.
3. Drop the old table and rename the new one.

All of this runs inside a savepoint, so a failure partway through leaves the original table untouched. If `email` already has `NOT NULL`, migrate returns without doing anything, so reruns are safe.

**Tradeoff vs. the alternative:** You could leave the column nullable and add `BEFORE INSERT` triggers that reject NULL. That avoids the rebuild, but the rule would live in triggers instead of the schema, and NULLs could still get in through `UPDATE` unless you add a second trigger. The rebuild enforces the rule in the column definition, and it blocks NULLs on both insert and update. The cost is copying the whole table once, which is trivial at 5 rows.

Strict mode asks you to explain the reasoning before I write the production code. One question:

**In the rebuild, why does the NULL backfill have to happen during the copy into `users_new`, rather than as an `UPDATE` after the copy?**

(You can also pick the trigger approach instead, or say "skip this checkpoint".)

### Human (2026-11-04T08:45:03.361Z)

```text
nulls may still get through UPDATE without a second trigger
```

### Assistant (2026-11-04T08:45:07.501Z)

That's correct, but it explains why I prefer the rebuild over triggers. It doesn't answer the ordering question. I'll take it as agreement with the rebuild approach. Correct me if you actually want triggers.

Here's a narrower version of the question. `users_new` declares `email TEXT NOT NULL`. Suppose step 2 copied rows unchanged, including ids 2 and 5 with NULL emails, and planned to fix them with an `UPDATE` afterwards. **What would happen at the copy step?**

The production edit is still on hold until you answer (attempt 2 of 2 for this checkpoint).

### Human (2026-11-04T08:48:27.866Z)

```text
We do not want to copy NULL emails and only want to do the copy 1 time
```

### Assistant (2026-11-04T08:48:33.263Z)

That's the right goal, but it doesn't name what would actually happen. Since this is the second attempt, here's the explanation.

**The mechanism:** `users_new` enforces `NOT NULL` from the moment it's created. Copying rows 2 and 5 unchanged would make SQLite reject the `INSERT ... SELECT` with `NOT NULL constraint failed: users.email`, so the copy fails. You'd never reach a later `UPDATE`. The backfill has to happen inside the copy (the `COALESCE`) so that every row already satisfies the constraint when it lands. The general rule is to make existing data valid before or as you enforce a constraint, never after.

How would you like to continue? Pick one:
1. **Retry:** say in your own words why the backfill has to happen during the copy.
2. **Lower the mode** to `standard`, which checks decisions without grading comprehension.
3. **Skip this checkpoint,** and I'll implement, test, and apply the migration now.

`app/migration.py` and `data/users.db` are still unchanged.

### Human (2026-11-04T08:49:43.960Z)

```text
retry
```

### Assistant (2026-11-04T08:49:45.544Z)

Go ahead. In a sentence or two, in your own words: why does the NULL backfill have to happen during the copy into `users_new`, rather than as an `UPDATE` afterwards?

### Human (2026-11-04T08:51:16.777Z)

```text
the copy would fail at SQLite rejection, and never get to the update step. So backfill has to happen inside the copy so the rejection would not happen due to the NULL entry.
```

### Assistant (2026-11-04T08:51:21.727Z)

That's right. The `NOT NULL` table rejects the NULL rows at the copy, so the backfill has to be part of the copy. Checkpoint passed, and I'm implementing the rebuild now.

### Assistant (2026-11-04T08:51:39.154Z)

All 12 tests pass. Next I'll apply the migration to `data/users.db` through the CLI, run it a second time, then verify with a fresh read-only connection.

### Assistant (2026-11-04T08:51:51.233Z)

The migration is implemented, tested, and applied to `data/users.db`.

**What changed**
- `app/migration.py`: `migrate()` rebuilds `users` with `email TEXT NOT NULL` inside a savepoint. It copies every row with its original `id` and fills in the NULLs during the copy. If `email` is already `NOT NULL`, it returns without doing anything.
- `tests/test_migration.py` (new, 9 tests) covers:
  - the exact backfill values, and that IDs and existing addresses are unchanged
  - NULL rejected on both insert and update
  - inserts with an email still work and the next ID is 9
  - reruns change nothing and keep rows added in between
  - a failure partway through rolls back to the legacy table
  - the result is still there after reopening a file on disk

**Results**
- All 12 tests pass (`python -m unittest discover -s tests`), including the 3 that were already there.
- I ran `python -m app.cli migrate data/users.db` twice. Reading the file back on a fresh read-only connection shows:
  - Schema: `CREATE TABLE "users" (id INTEGER PRIMARY KEY, email TEXT NOT NULL)`
  - Rows: `(1, ada@example.com)`, `(2, legacy-2@example.invalid)`, `(3, grace@example.org)`, `(5, legacy-5@example.invalid)`, `(8, linus@example.net)`
  - `PRAGMA integrity_check` returns `ok`, and a NULL insert is rejected (I rolled that test insert back).
- Nothing was committed or published, and the scratch draft stayed outside the project.

**Handoff check (still open)**
The work is done; this check comes from strict mode. Your earlier answers covered why the backfill happens during the copy and why the rebuild beats triggers. They didn't cover one consequence for callers:

**After this migration, what happens when application code calls `add_user(connection, None)`, and what does that mean for anyone calling it?**

You can also say "skip this checkpoint".

### Human (2026-11-04T08:57:01.503Z)

```text
it will fail and not be inserted/added
```

### Assistant (2026-11-04T08:57:04.964Z)

That's correct, and the handoff check is closed. To add the specifics: `add_user(connection, None)` now raises `sqlite3.IntegrityError: NOT NULL constraint failed: users.email` and inserts nothing. Callers need to pass an email or handle that exception.

The task is complete. The migration is in place, all 12 tests pass, and `data/users.db` is migrated and verified. Everything stayed local, with no commits or publishing.
