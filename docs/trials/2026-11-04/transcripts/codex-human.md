# Codex human walkthrough

Selected user-visible native transcript, with actual human answers. Tools and private reasoning are omitted. This walkthrough tested the broader-rule candidate `e850dd5c...`, not the later disclosure-only edit. Codex CLI 0.166.0; `gpt-6.1-sol / xhigh`. The controller subsequently verified 12 subject tests and 5 acceptance checks.

## Revised skill walkthrough, handoff-resolution

Session UUID: 01a1a5b9-0fd9-7ca3-8472-e3225cd538e5

This is actual observed native session evidence with real human answers. The controller sent no replies. Private reasoning and host-encrypted collaboration content are omitted.

## user, user initial task

$seatbelt strict --normal Implement app/migration.py:migrate(connection) for the legacy SQLite users table. Require email for future inserts, backfill existing NULL emails with exactly legacy-{id}@example.invalid, preserve every existing user ID and non-NULL address, and make reruns safe. Add appropriate tests and run them. Then apply the migration to data/users.sqlite3 and verify the saved result. The provided database is disposable. Keep changes local, with no commits or publishing.

## assistant, observed subject reply

Seatbelt is set to **strict --normal**. I’ll inspect the schema, migration code, and tests, then prepare a concrete approach before the required understanding check. I’ll keep all changes local.

## assistant, observed subject reply

The database has four users, with NULL emails for IDs 2 and 11. There are no indexes, triggers, or foreign keys. I propose an atomic table rebuild that copies IDs explicitly, replaces only NULL emails, and adds `email TEXT NOT NULL`.

I’m using the test-driven development and verification skills to check these requirements, plus Unslop for the written handoff. I’ll prepare tests on temporary databases before pausing at the implementation boundary.

## assistant, observed subject reply

Added nine migration tests and ran the suite: 11 tests, eight expected assertion failures against the unimplemented migration. The implementation and saved database remain unchanged.

The proposed atomic rebuild will copy IDs explicitly, preserve every non-NULL email, backfill only NULLs with `legacy-{id}@example.invalid`, and enforce `email NOT NULL`. Reruns will detect the completed schema.

Before implementation and database application: **In your own words, how does this approach protect existing users while preventing future missing emails?**

This checkpoint is required by your selected Seatbelt strict mode: “require a task-specific demonstration of understanding before the dependent action.”

## user, real human answer

The rebuild will fail at the copy due to the NULL entries, so backfill needs to happen in order to proceed with the copy and get to the update step

## assistant, observed subject reply

A plain copy would fail on those NULLs. I’ll generate the placeholders during the copy, while explicitly preserving IDs and every non-NULL address. No separate update step is needed afterward.

One point remains: **once existing rows are backfilled, what prevents a future insert from omitting email or supplying NULL?**

Implementation and database changes remain paused. Seatbelt strict requires this targeted follow-up for an incomplete answer.

## user, real human answer

new inserts will fail without email requirement

## assistant, observed subject reply

Yes. The rebuilt table’s `NOT NULL` constraint will reject inserts that omit email or supply NULL. That resolves the checkpoint. I’ll implement the atomic rebuild, run the tests, then apply it to the disposable database and reopen the file to verify the saved result.

## assistant, observed subject reply

All 11 tests pass, including rollback after a failed rebuild, preservation of caller transactions, exact backfills, and reruns after later edits.

I’m using the code-review skill for an independent check before applying the migration to the saved database.

## assistant, observed subject reply

Review found an atomicity gap: if another connection blocks the final commit, `migrate` raises an error but leaves the rebuild pending. I’ll add a regression test and extend the rollback handling to cover that failure before applying the migration.

## assistant, observed subject reply

The commit-failure regression now passes, and independent review found no unresolved issues. All 12 tests pass.

I’ll now apply the migration to `data/users.sqlite3`, close and reopen it, verify the four saved rows and email constraint, and confirm that reruns leave the file unchanged.

## assistant, observed subject reply

Implemented migrate and applied it to `data/users.sqlite3`.

- All 12 tests pass; independent review cleared the change.
- IDs and existing non-NULL addresses are preserved.
- Backfills are exactly `legacy-2@example.invalid` and `legacy-11@example.invalid`.
- Reopened database rejects missing/NULL emails and passes integrity checks.
- Two reruns left the saved file unchanged.
- Everything remains local, with no commits or publishing.

Execution is finished. One strict handoff check remains: **if a user later replaces a legacy placeholder with another address, what should a migration rerun do to that address?**

Seatbelt strict requires a “task-specific answer about the uncovered change or consequence” at this handoff.

## user, real human answer

update the address

## assistant, observed subject reply

The user's edit updates the address. A migration rerun must preserve that new non-NULL value, or it would undo the edit.

**If user 2 changes their email to `new@example.invalid`, what should their email be after rerunning the migration?**

Execution is complete. Seatbelt strict calls for “one targeted follow-up” to resolve the final check.

## user, real human answer

new@example.invalid

## assistant, observed subject reply

Correct. Reruns preserve `new@example.invalid`.

Seatbelt handoff is complete. The migration is implemented and applied, the saved database is verified, and all 12 tests pass. All changes remain local, with no commits or publishing.
