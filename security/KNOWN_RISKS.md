# Known Technical Risks

**Last reviewed: September 1, 2026.**

## BLE and client behavior

- iOS schedules background scanning and advertising; discovery is not guaranteed
  to be continuous while suspended.
- The stable BLE Telescan ID can be observed, replayed, or correlated. The
  protocol does not provide cryptographic proof of identity or proximity.
- RSSI-based distance is approximate and affected by hardware, body position,
  reflections, interference, and iOS scheduling.
- Earlier behavior was exercised on physical devices, but this P0 candidate
  still requires a signed TestFlight two-device BLE/background matrix. Simulator
  tests do not establish real radio or background behavior.
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
- Account deletion and Apple-to-Telegram adoption remain distributed operations
  across MongoDB and object storage. The P0 candidate adds durable idempotent
  journals, recovery proof, retryable cleanup, and invariant auditing, but its
  migration and interruption behavior still needs production-like staging
  evidence before rollout.
- Legacy `/v1` behavior remains available only for non-production migration
  testing. Production configuration rejects it; accepting a bypass would reopen
  the former Apple-first authorization risk.

## Sessions and notifications

- The current iOS MVP intentionally omits logout-all. The retained polling
  backend is not a reliable multi-device revocation experience without APNs.
- Nearby background notifications are best-effort and subject to iOS scheduling,
  notification permission, Focus settings, and system throttling.

## Moderation

- Moderation uses an allow-listed Telegram bot, not a case-management console.
- There is no case assignment, formal response SLA, analytics dashboard, or
  multi-role permission model.
- Report text, captured context, separately stored photo evidence, and immutable
  moderation events increase the data exposed to moderators. The 180-day closed
  retention and 30-day completed-operation retention must be re-evaluated as
  law, incident needs, and case volume change.

## Infrastructure and operations

- The EU relay improves routing for users behind VPNs but creates an additional
  TLS trust boundary and availability dependency.
- A relay, WireGuard, or Moscow-origin failure interrupts normal API traffic.
- Metrics, logs, cross-host health monitoring, and Telegram alert delivery are
  centralized. Alert thresholds still need production burn-in and tuning.
- One isolated production MongoDB restore drill passed on August 18, 2026, for
  the prior schema. A new current-schema drill with journal/evidence indexes,
  application-role boundaries, measured RPO/RTO, and recurring scheduling is a
  P0 operational acceptance gate.
- Restricted monitoring ingestion, mandatory freshness enforcement, Grafana
  secret-key migration, and Telegram firing/resolved delivery must be staged
  and evidenced on the real hosts; source validation cannot prove rollout.
- Source-controlled deployment gates do not prove current DNS, TLS, firewall,
  WireGuard, SSH, listener, container-image, or timer state. A dated read-only
  production attestation remains required.
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
