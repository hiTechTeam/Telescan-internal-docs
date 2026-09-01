# Security Model

**Last reviewed: September 1, 2026.**

## Trust boundaries

- iOS is an untrusted network client with a per-installation device session.
- BLE identifiers are public radio data and do not prove identity or proximity.
- The EU relay is a trusted TLS endpoint and can observe request contents before
  forwarding them through WireGuard.
- The API is the sole owner of application data and photo storage.
- The user bot and admin bot are separate service principals with different
  long-lived credentials.
- MongoDB root credentials are operational credentials; the API uses a dedicated
  `readWrite` application account only.

## P0 release-candidate controls

The following controls are implemented in the local release candidate. Runtime
claims still require the production acceptance evidence in `P0_CONTRACTS.md`.

- Random short-lived, one-time link codes stored as keyed digests, with one
  active normal code per Telegram target and bounded consumed-proof retention.
- Short-lived access JWTs, rotating refresh tokens, and active session checks on
  every protected request.
- Constant-time verification for bot and moderation service credentials.
- Idempotency keys and shared MongoDB-backed rate limits on abuse-sensitive
  flows; durable, resumable journals for account merge and deletion.
- Server-side bidirectional block enforcement, immutable moderation events,
  revision compare-and-swap, and separately retained report evidence.
- Production startup rejection of development/placeholder/duplicated secrets,
  unsafe origins, wildcard CORS, and legacy authentication. An empty CORS
  allow-list is valid and is the safest configuration for native-only clients.
- Public `404` policy for internal services and Swagger/OpenAPI paths at both
  nginx edges.
- TLS 1.2/1.3, HSTS, restrictive browser headers, hidden nginx versions, and an
  encrypted WireGuard origin hop.
- Unprivileged/read-only containers with minimal write mounts, dropped
  capabilities, bounded tmpfs, and `no-new-privileges`.
- `.dockerignore` boundaries that exclude `.env`, Git metadata, virtual
  environments, tests, and caches from application image contexts.
- Mode-`600` runtime secret files and separate GitHub production environments.
- Root/password/keyboard-interactive SSH and X11 disabled; Fail2ban enabled.
- Client-side encrypted, off-host, point-in-time-consistent MongoDB backups with
  current-schema, domain-reference, index, and application-role restore checks.
- Dedicated cross-host metrics and log collection with restricted write-only
  ingestion, missing/stale-signal and capacity alerts, and restricted Telegram
  delivery.
- App-only UserDefaults declaration in the iOS privacy manifest, a 24-hour
  device-local encounter history, a device-only Saved list, generation-bound
  asynchronous work, complete account-state cleanup on every invalid/changed
  session boundary, and no advertising or third-party analytics SDK.

## BLE threat model

Anyone in radio range can observe, replay, or correlate a stable advertised
Telescan ID. Authenticated profile lookup limits access to Telescan accounts but
does not establish physical proximity or ownership of the radio transmission.

Do not describe distance as precise, use discovery for safety decisions, or
present BLE visibility as cryptographic identity. A future protocol should add
versioning and rotating signed identifiers before the threat model can change.

## Secrets and rotation

Secret classes include Telegram bot tokens, API signing/service secrets,
moderation credentials, MongoDB accounts, photo-storage keys, Restic credentials,
WireGuard private keys, SSH keys, and TLS account material.

Rotation procedure:

1. identify every producer and consumer;
2. add the replacement without logging it;
3. deploy consumers and verify health;
4. revoke the old value at the issuing system;
5. remove the old value from hosts and GitHub;
6. record the rotation date without recording the value.

Treat any secret pasted into chat, an issue, a log, or Git history as disclosed
and revoke it at the source rather than merely deleting the text.

## Repository controls

Dependabot vulnerability alerts report no open findings in the application,
database, edge, relay, iOS, and public-information repositories. They are
currently disabled for `Telescan-monitoring` and the dependency-free internal
documentation repository. The public information repository protects `main`
and enables secret scanning with push protection.

The implementation repositories are private. On the current GitHub plan they
cannot enforce branch protection, required checks, secret scanning, or push
protection. Pull requests and green CI are procedural gates and can be bypassed
by an administrator. Upgrade the plan or add an independent receiving-side gate
before treating repository controls as mandatory production enforcement.

## Incident priorities

1. Revoke exposed credentials and active sessions where applicable.
2. Preserve relevant logs and timestamps without copying personal data into Git.
3. Contain the affected ingress, service, account, or deployment workflow.
4. Validate database and object-storage integrity.
5. Restore from a verified clean release or isolated backup when required.
6. Document root cause, scope, corrective action, and rotation completion.
