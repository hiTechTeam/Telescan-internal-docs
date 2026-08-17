# Known Technical Risks

**Last reviewed: August 17, 2026.**

## BLE and client behavior

- iOS schedules background scanning and advertising; discovery is not guaranteed
  to be continuous while suspended.
- The stable BLE Telescan ID can be observed, replayed, or correlated. The
  protocol does not provide cryptographic proof of identity or proximity.
- RSSI-based distance is approximate and affected by hardware, body position,
  reflections, interference, and iOS scheduling.
- Production behavior has been verified on the current physical test devices,
  but broader model/OS coverage and long-running power tests remain required.

## API and data

- Authenticated users who learn a valid Telescan ID can request that profile;
  the API does not independently prove physical proximity.
- Rate limiting is process-local, resets on restart, and is not shared between
  replicas. Add a shared adapter before horizontal scaling.
- FastAPI 0.141.1 warns that `ORJSONResponse` is deprecated. Responses and tests
  currently work; migrate before the compatibility class is removed.
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
- Centralized metrics, alert delivery, cross-host health monitoring, documented
  log retention, and recurring disaster-recovery drills are incomplete. One
  isolated production MongoDB restore drill passed on August 18, 2026, but an
  automated schedule and a complete service recovery exercise are still absent.
- Private-repository merge and secret protections are procedural on the current
  GitHub plan.
- Shared long-lived service credentials require a documented recurring rotation
  schedule and incident drill.
