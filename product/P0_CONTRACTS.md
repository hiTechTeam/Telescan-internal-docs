# P0 Remediation Contracts

**Approved target: September 1, 2026.**

This document fixes the cross-repository behavior required by the P0
stabilization cycle. It describes the target contract, not deployed production
state. A contract becomes operational only after its implementation, tests,
runtime evidence, and rollback evidence are linked from the new audit baseline.

## Account identity and Apple-to-Telegram merge

- Sign in with Apple owns primary authentication. Telegram remains a required
  linked public contact for a complete profile.
- When an Apple-created placeholder is linked to an existing Telegram-created
  user, the Telegram user is canonical. Its public Telescan ID, Telegram
  identity, BIO, live photo, and creation history are preserved.
- All placeholder device sessions are migrated to the canonical user or
  explicitly revoked. No session may reference a deleted user.
- Blocks and reports are migrated with deterministic deduplication. Temporary
  link/review codes and pending confirmations belonging to the placeholder are
  cancelled.
- The MongoDB transition is atomic when transactions are available. Otherwise
  it uses a durable, idempotent merge journal with resumable steps. Direct
  irreversible multi-document writes without a journal are prohibited.
- Before a merge or deletion starts mutating account data, it installs a
  durable per-account operation fence. Authentication, session creation, and
  domain writes reject fenced source accounts, including requests that
  authenticated before the operation began. The saga repeats its idempotent
  sweeps immediately before source deletion or terminal completion, so no
  late session or domain reference can survive.
- Photo cleanup occurs after the database transition through an idempotent
  outbox. A retry must not delete the canonical user's photo.
- Link-code validation is a preflight. The durable merge operation is prepared
  before the code is consumed; after consumption every failure is resumed from
  that exact operation proof. An account may have at most one active normal
  Telegram-bot link code; separately governed App Review batches retain their
  scoped multi-code workflow.
- A consumed HMAC code hash and consuming device identifier may remain for up
  to 30 days solely as linking/merge-recovery proof. Completed merge/deletion
  journals expire after 30 days; incomplete journals remain durable until
  reconciled.

## Device-local account boundary

- Logout, confirmed account deletion, invalid-session reset, and account
  replacement stop BLE work and cancel account-scoped asynchronous tasks.
- They clear encounter history, the encounter buffer, pending resolutions,
  last-update maps, unviewed counters, Nearby, Blocked, Saved, quick actions,
  profile/photo caches, and session credentials.
- The installation's non-account device identifier may remain. No user profile,
  token, list entry, or pending callback from the previous account may remain.
- Every asynchronous profile resolution captures an account generation. A
  result from an obsolete generation is discarded and cannot repopulate a
  cleared store.

## BLE encounter and notification boundary

- A raw BLE identifier is untrusted input. It does not create a profile row,
  Met entry, badge, quick action, or notification.
- Scan-epoch changes are ordered on the Core Bluetooth source queue. Service,
  advertising, and GATT callbacks retain the operation's originating epoch;
  per-peripheral work is single-flight and an older account must reach a
  terminal callback before the next account can reuse that peripheral.
- Encounter state becomes user-visible only after the API returns a complete,
  authenticated, current, non-blocked profile for the active account
  generation.
- Transient failures may be retried, but a successful profile creates at most
  one logical encounter notification for the applicable deduplication window.

## Blocking

- A block hides profile access in both directions at the API boundary.
- On device, blocking removes the target from Nearby, Met, the encounter
  buffer, pending resolution, update maps, unviewed counts, and quick actions.
- A blocked target may remain as an identifier in the user's Saved collection,
  but it is not rendered or opened while blocked. Unblocking does not recreate
  a Met encounter; a separately saved entry may become visible again.
- Server block synchronization applies the same purge behavior as a local block.

## Reports and moderation evidence

- A report has one universal category and an optional comment of zero to 500
  characters.
- Report creation attempts to copy the target's current live photo into a
  separate moderation-evidence object. The report records `available`,
  `absent`, or `copy_failed` evidence state explicitly.
- Live photo replacement and account deletion do not remove moderation evidence
  before the report retention deadline. Closed reports and their evidence are
  scheduled for deletion after 180 days.
- Moderation transitions are `pending -> reviewing|resolved|dismissed` and
  `reviewing -> resolved|dismissed`. Closed states are terminal.
- A missing moderator-note field preserves the current note; explicit null
  clears it; a string replaces it. Every mutation uses a revision compare and
  swap, and every accepted transition appends an event with actor, revision,
  previous/new state, note change, and timestamp.

## Legal source and publication

- `Telescan-info/legal/legal_documents.json` is the canonical localized legal
  source. Public Markdown and API presentation data are generated or vendored
  exactly from it.
- The source manifest records the version, effective dates, and SHA-256 digest.
  API CI rejects source inequality. Production `/privacy` and `/terms` expose
  the approved version and digest, and deployment smoke checks both.
- The legal description of moderation evidence, deletion, local account reset,
  blocking, providers, jurisdictions, and retention must match this contract.

## Production safety gates

- Production startup rejects default, empty, placeholder, cross-role, and
  incorrectly routed secrets or origins. The application database may not be
  `admin`, and root/application MongoDB credentials must be distinct.
- Legacy authentication is prohibited in production. Production migration
  removes the legacy user-code field before the release is accepted.
- Database backups must be point-in-time consistent or taken from a quiesced
  snapshot. Restore verification checks domain references and every required
  index's ordered key, uniqueness, partial filter, and TTL semantics, not only
  BSON structure or index names.
- Production deploys originate only from the protected `main` revision, have
  one concurrency lock per environment, run semantic preflight checks, preserve
  the previous image, and automatically restore it after a failed health gate.
- Monitoring deployment runs both stack readiness and full telemetry checks.
  Alerts cover missing and stale telemetry, the monitoring data disk and
  inodes, storage growth, backup age, and notification delivery.
- Production handoff requires a read-only state attestation covering DNS, TLS,
  listeners, firewall, WireGuard, SSH effective policy, published Docker ports,
  and running image digests without collecting secret values.

## Compatibility order

1. Deploy additive database indexes, journal/event fields, and API responses.
2. Deploy API support for old and new bot payloads.
3. Deploy user bot and admin bot clients that send revisions and new evidence
   fields.
4. Enable mandatory CAS and terminal-transition enforcement.
5. Deploy the iOS account-boundary and notification changes.
6. Publish generated legal content only with the matching API behavior.
7. Remove compatibility fields only in a later, separately audited release.

## P0 acceptance evidence

- Full unit, integration, concurrency, and injected-failure suites are green.
- Account A to account B tests show no data or callback crossover.
- Account merge leaves zero unexplained domain-invariant violations.
- A current-schema backup restores successfully and measured RPO/RTO are
  recorded.
- Signed TestFlight Apple authentication and the physical BLE matrix pass.
- Relay, origin, public readiness, monitoring telemetry, alert delivery, and
  rollback drills pass against the frozen release candidate.
- The new audit baseline maps every control and risk to a commit, image digest,
  test result, runtime evidence, owner, or explicit risk acceptance.
