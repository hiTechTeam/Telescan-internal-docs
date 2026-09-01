# Moderation Workflow

**Last reviewed: September 1, 2026.**

This document describes the P0 release-candidate contract. It is not production
evidence.

## Current model

Authenticated users can report or block another Telescan profile from the iOS
profile sheet. Report and block behavior is enforced by the API; the iOS menu is
not the source of truth.

Reports contain a random report ID, a client-supplied idempotency UUID, reporter
and target internal references, public Telescan IDs, an optional zero-to-500
character comment, captured target name, username and BIO, explicit photo
evidence state, status, revision, timestamps, moderator actor, and optional
moderator note. The user-facing contract submits one universal report without a
selectable category; stored records retain an internal universal `other` value
for schema compatibility. The reported user is not shown the reporter's
identity.

The same idempotency UUID is retained while iOS retries a report-and-block
operation, including the case where report creation succeeded but its response
was lost. The API attempts to copy the current live photo to a separately named
moderation-evidence object and records `available`, `absent`, or `copy_failed`.
Live profile-photo replacement and account deletion do not remove that evidence
before the report retention deadline. Closed reports and evidence expire after
180 days.

Blocks are unique per blocker/target pair. A block hides profile lookup in both
directions and is reflected in the iOS cache after the API confirms success.

## Admin bot workflow

1. An allow-listed moderator opens pending reports or receives a notification.
2. The moderator reviews the optional comment, timestamps, captured profile,
   evidence state, current status, and revision.
3. The moderator moves a pending report to `reviewing` when investigation
   begins, or closes it directly as `resolved` or `dismissed`.
4. From `reviewing`, the moderator explicitly confirms `resolved` or
   `dismissed`; `reviewing -> reviewing` is not a note-only operation.
5. A submitted string replaces the note, explicit null clears it, and an
   omitted note preserves it.
6. The API compare-and-swaps the supplied revision, records the acting Telegram
   moderator ID, and appends an event containing prior/new status, note change,
   revision, and timestamp. A stale revision is rejected and the bot reloads.

Allowed transitions are:

```text
pending   -> reviewing | resolved | dismissed
reviewing -> resolved  | dismissed
resolved  -> (terminal)
dismissed -> (terminal)
```

The bot has no public port, MongoDB credentials, or storage credentials. It uses
only the private moderation API and a credential distinct from the user bot.

## Operational rules

- Never paste reports or user data into GitHub issues or documentation.
- Use moderator notes for facts relevant to the decision, not speculation.
- Do not disclose the reporter's identity to the target.
- Do not promise a response time until a documented SLA exists.
- Escalate credible safety, fraud, or legal concerns outside the bot workflow.
- Revoke the admin bot token immediately if exposed and verify the allow-list.
- Treat `copy_failed` as a visible evidence limitation; never render it as if no
  photo existed.

## Future expansion

Searchable case history, assignment, role separation, metrics, and appeals can
be added when moderation volume requires them. The API remains the source of
truth and the Telegram bot remains a restricted presentation layer.
