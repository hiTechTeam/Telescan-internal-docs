# Moderation Workflow

**Last reviewed: September 5, 2026.**

Authenticated users can submit one universal report with an optional comment
and can block another profile. The API, not the client, is the source of truth.

Reports use an idempotency ID and capture the target profile plus photo evidence
state: `available`, `absent` or `copy_failed`. Closed reports and evidence are
retained for 180 days. Blocks are unique and hide profile lookup in both
directions.

Allowed report transitions:

```text
pending   -> reviewing | resolved | dismissed
reviewing -> resolved  | dismissed
resolved/dismissed -> terminal
```

Every mutation compares the current revision, records the moderator and appends
an immutable event. A missing note keeps the old value, `null` clears it and a
string replaces it.

Moderators must keep user data out of Git, write factual notes, protect reporter
identity and escalate credible safety/legal concerns outside the bot. The bot
is allow-listed, uses only the private moderation API and has no database or
storage credentials.
