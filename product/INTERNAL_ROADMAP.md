# Internal Roadmap

**Last reviewed: September 1, 2026.**

## Implemented P0 release-candidate foundations

These items are implemented and locally verifiable in the P0 candidate; they
are not all deployed or accepted in production.

- Authenticated device sessions with rotating refresh tokens.
- Current-device logout and durable idempotent account deletion, with complete
  account-scoped client cleanup and obsolete-callback rejection.
- Durable Apple-to-Telegram account adoption with canonical-profile preservation,
  resumable migration, bounded proof retention, and retryable photo cleanup.
- BLE discovery using a payload-safe Telescan identifier and complete-profile
  validation before presentation.
- Nearby expiry UX in list and expanded profile views.
- Stable discovery-order rows, 10-second distance refresh, and a device-local
  24-hour history populated after a one-minute BLE heartbeat timeout, with a
  persistent pre-history buffer recovered before scanning after app termination.
- Optional 36-character BIO in the account profile and nearby cards.
- Idempotent user reports with separately retained evidence, 180-day closed-case
  cleanup, revision-safe moderation events, blocks, unblock management, and an
  allow-listed moderation bot.
- Universal user reports with legacy stored-record compatibility.
- Device-local Saved profiles, configurable quick actions, new-Met state, and
  application/profile badge aggregation.
- EU HTTPS relay and encrypted WireGuard origin path.
- Public route denial for internal API and Swagger paths.
- Hardened application containers and production SSH hosts.
- Least-privilege MongoDB application user.
- Automated TLS renewal and consistent encrypted off-host database backup plus
  current-schema isolated-restore validation tooling.
- Shared MongoDB-backed rate limiting for abuse-sensitive API flows.
- Dedicated metrics, logs, dashboards, restricted ingestion, cross-host
  freshness/capacity alerts, and Telegram delivery-drill tooling.
- Main-only, host-serialized, preflighted deployment with application and edge
  rollback paths.
- A canonical localized legal source, generated public documents, exact API
  vendoring, and deployment digest checks at both public origins.
- Ruff, Pyright, pytest, Docker, and iOS unit/build CI across implementation
  repositories; gesture and cross-version UI coverage remains a launch task.

## Before broad public launch

- Obtain the Apple Developer account, configure production signing, and complete
  TestFlight/App Store review assets and privacy declarations.
- Schedule recurring restore drills and run a complete service recovery
  exercise.
- Run a current-schema encrypted production-snapshot restore in isolation and
  record observed backup age and restore time.
- Freeze the coordinated P0 commits/images, deploy them in the documented order,
  run production read-only attestation, and exercise every rollback path.
- Complete restricted-ingest and Grafana-key migrations, require fresh host
  state, and observe both firing/resolved Telegram drill messages.
- Confirm the canonical legal version/digest and TLS on both public origins.
- Complete release-candidate regression, accessibility, and TestFlight testing.
- Calibrate monitoring thresholds under normal production traffic.
- Define moderation response ownership, escalation, and retention review.

## Next engineering priorities

| Area | Work | State |
| --- | --- | --- |
| Migration | Run the production legacy-removal and journal/moderation migrations; legacy auth is prohibited in production | Rollout required |
| API quality | Expand real MongoDB/S3 integration and chaos coverage beyond the implemented deterministic saga/failure suites | Planned |
| Supply chain | Enable monitoring dependency alerts and pin Python image/build inputs to reviewed immutable revisions | Planned |
| BLE protocol | Version the protocol and investigate rotating authenticated identifiers | Research |
| Notifications | Add APNs only after Apple account availability and a clear aggregation/privacy design | Deferred |
| Moderation | Add case search, roles, and appeals only when usage requires them | Later |
| Repository controls | Upgrade plan or add independent enforcement for private-repo PR/CI/secret gates | Planned |
| iOS quality | Add an iOS 18/26 device matrix and XCUITest coverage for swipe, hold, navigation-bar, and context-menu behavior | Planned |
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
