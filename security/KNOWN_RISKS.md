# Known Technical Risks

**Last reviewed: September 1, 2026.**

## BLE and client behavior

- iOS schedules background scanning and advertising; discovery is not guaranteed
  to be continuous while suspended.
- The stable BLE Telescan ID can be observed, replayed, or correlated. The
  protocol does not provide cryptographic proof of identity or proximity.
- RSSI-based distance is approximate and affected by hardware, body position,
  reflections, interference, and iOS scheduling.
- Production behavior has been verified on the current physical test devices,
  but broader model/OS coverage and long-running power tests remain required.
- The local **Met** history retains public profile snapshots and last-seen times
  in the app container for up to 24 hours. It contains no GPS or distance
  history and is cleared on sign-out, deletion, blocking, or manual removal,
  but a compromised unlocked device may expose those snapshots.
- The local **Saved** list retains user-selected public profile snapshots until
  removal, sign-out, or account deletion. It is not uploaded or synchronized,
  but a compromised unlocked device may expose those snapshots.

## API and data

- Authenticated users who learn a valid Telescan ID can request that profile;
  the API does not independently prove physical proximity.
- Rate limiting uses shared MongoDB fixed-window counters. Limits and indexes
  still require recalibration as traffic patterns change.
- Account deletion and Apple-to-Telegram account adoption span several MongoDB
  collections and, for deletion, object storage. Sessions are revoked first and
  photo replacement uses compensation, but a process or storage failure can
  still require an idempotent deletion retry or operational cleanup.
- Legacy `/v1` behavior remains behind a feature flag for migration. Enabling it
  restores weaker compatibility behavior.

## Sessions and notifications

- The current iOS MVP intentionally omits logout-all. The retained polling
  backend is not a reliable multi-device revocation experience without APNs.
- Nearby background notifications are best-effort and subject to iOS scheduling,
  notification permission, Focus settings, and system throttling.

## Moderation

- Moderation uses an allow-listed Telegram bot, not a case-management console.
- There is no case assignment, formal response SLA, analytics dashboard, or
  multi-role permission model.
- Reports depend on captured profile context; retention and moderator access must
  be reviewed as the user base grows.

## Infrastructure and operations

- The EU relay improves routing for users behind VPNs but creates an additional
  TLS trust boundary and availability dependency.
- A relay, WireGuard, or Moscow-origin failure interrupts normal API traffic.
- Metrics, logs, cross-host health monitoring, and Telegram alert delivery are
  centralized. Alert thresholds still need production burn-in and tuning.
- One isolated production MongoDB restore drill passed on August 18, 2026, but
  recurring full-service recovery exercises are not yet scheduled.
- Private-repository merge and secret protections are procedural on the current
  GitHub plan.
- Shared long-lived service credentials require a documented recurring rotation
  schedule and incident drill.
- Python application images fetch the uv installer at build time without a
  committed checksum, and their base images are not digest-pinned. Pin reviewed
  image and installer inputs before treating application builds as fully
  reproducible.
- Dependabot vulnerability alerts are disabled for `Telescan-monitoring`, so
  its pinned container-image updates currently depend on manual review.
