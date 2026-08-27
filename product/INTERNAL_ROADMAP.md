# Internal Roadmap

**Last reviewed: August 27, 2026.**

## Completed production foundations

- Authenticated device sessions with rotating refresh tokens.
- Current-device logout and account deletion with cache cleanup.
- BLE discovery using a payload-safe Telescan identifier and complete-profile
  validation before presentation.
- Nearby expiry UX in list and expanded profile views.
- Stable discovery-order rows, 10-second distance refresh, and a device-local
  24-hour history populated after a one-minute BLE heartbeat timeout.
- Optional 36-character BIO in the account profile and nearby cards.
- User reports, blocks, unblock management, and allow-listed moderation bot.
- EU HTTPS relay and encrypted WireGuard origin path.
- Public route denial for internal API and Swagger paths.
- Hardened application containers and production SSH hosts.
- Least-privilege MongoDB application user.
- Automated TLS renewal and encrypted, restore-validated database backups.
- Shared MongoDB-backed rate limiting for abuse-sensitive API flows.
- Dedicated metrics, logs, dashboards, cross-host telemetry, and Telegram
  alerts.
- Successful isolated restore of a real encrypted production MongoDB snapshot.
- Ruff, Pyright, pytest, Docker, and iOS CI coverage appropriate to each repo.

## Before broad public launch

- Obtain the Apple Developer account, configure production signing, and complete
  TestFlight/App Store review assets and privacy declarations.
- Schedule recurring restore drills and run a complete service recovery
  exercise.
- Complete release-candidate regression, accessibility, and TestFlight testing.
- Calibrate monitoring thresholds under normal production traffic.
- Define moderation response ownership, escalation, and retention review.

## Next engineering priorities

| Area | Work | State |
| --- | --- | --- |
| Migration | Disable legacy API after supported-client adoption and remove legacy code hashes | Rollout required |
| API quality | Replace deprecated `ORJSONResponse`; expand MongoDB/S3 integration and contract tests | Planned |
| Supply chain | Enable monitoring dependency alerts and pin Python image/build inputs to reviewed immutable revisions | Planned |
| BLE protocol | Version the protocol and investigate rotating authenticated identifiers | Research |
| Notifications | Add APNs only after Apple account availability and a clear aggregation/privacy design | Deferred |
| Moderation | Add case search, roles, and appeals only when usage requires them | Later |
| Repository controls | Upgrade plan or add independent enforcement for private-repo PR/CI/secret gates | Planned |
| Android | Implement only after the BLE contract is versioned and stable | Later |

## Explicit non-goals for the current MVP

- GPS tracking or movement history.
- Reading Telegram contacts or messages.
- Precise physical-distance guarantees.
- Public Swagger or internal bot endpoints.
- Remote multi-device logout without reliable delivery.
- A public release of the core implementation repositories.

## Documentation split

`Telescan-info` contains public product and legal material only. Architecture,
operations, risks, moderation internals, deployment details, and engineering
roadmap belong in this private repository.
