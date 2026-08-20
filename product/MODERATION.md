# Moderation Workflow

**Last reviewed: August 19, 2026.**

## Current model

Authenticated users can report or block another Telescan profile from the iOS
profile sheet. Report and block behavior is enforced by the API; the iOS menu is
not the source of truth.

Reports contain a random report ID, reporter and target internal references,
public Telescan IDs, reason, optional comment, captured target name, username,
BIO and photo context,
status, timestamps, moderator actor, and optional moderator note. The reported
user is not shown the reporter's identity.

Blocks are unique per blocker/target pair. A block hides profile lookup in both
directions and is reflected in the iOS cache after the API confirms success.

## Admin bot workflow

1. An allow-listed moderator opens pending reports or receives a notification.
2. The moderator reviews the reason, comment, timestamps, and captured profile.
3. The moderator moves the report to `reviewing` when investigation begins.
4. The moderator optionally adds a concise factual note.
5. The moderator explicitly confirms `resolved` or `dismissed`.
6. The API records the acting Telegram moderator ID and decision time.

The bot has no public port, MongoDB credentials, or storage credentials. It uses
only the private moderation API and a credential distinct from the user bot.

## Operational rules

- Never paste reports or user data into GitHub issues or documentation.
- Use moderator notes for facts relevant to the decision, not speculation.
- Do not disclose the reporter's identity to the target.
- Do not promise a response time until a documented SLA exists.
- Escalate credible safety, fraud, or legal concerns outside the bot workflow.
- Revoke the admin bot token immediately if exposed and verify the allow-list.

## Future expansion

Searchable case history, assignment, role separation, metrics, and appeals can
be added when moderation volume requires them. The API remains the source of
truth and the Telegram bot remains a restricted presentation layer.
