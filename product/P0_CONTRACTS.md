# P0 Remediation Contracts

**Last reviewed: September 5, 2026.**

## Required behavior

- Apple authenticates; Telegram is the linked public profile. Linking preserves
  that profile through a resumable journal.
- Logout, deletion and account replacement clear all account-scoped iOS state.
  Late async/BLE callbacks from an older account generation are rejected.
- BLE is untrusted until the API resolves a complete, non-blocked profile.
- Blocks apply in both directions and purge active local encounter state.
- Reports are idempotent, revision-safe and use explicit photo evidence states.
  Closed records/evidence expire after 180 days.
- Legal source, API and public pages must share one version and digest.

## Production gates

- Reject placeholder, reused or incorrectly routed secrets and legacy auth.
- Use a least-privilege MongoDB application account and verified encrypted
  backups.
- Deploy only protected `main` under host locks with immutable releases,
  health checks and rollback.
- Keep internal APIs, Swagger, databases and telemetry off public interfaces.
- Require fresh monitoring state, alert delivery and read-only host attestation.

## Acceptance evidence

Require green tests, clean data invariants, current restore evidence, signed
TestFlight/BLE checks, healthy production paths and tested rollback. Frozen
proof belongs in `Telescan-audit`.
