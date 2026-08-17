# Internal Roadmap

**Last reviewed: August 17, 2026.**

## Completed production foundations

- Authenticated device sessions with rotating refresh tokens.
- Current-device logout and account deletion with cache cleanup.
- BLE discovery using a payload-safe Telescan identifier and complete-profile
  validation before presentation.
- Nearby expiry UX in list and expanded profile views.
- User reports, blocks, unblock management, and allow-listed moderation bot.
- EU HTTPS relay and encrypted WireGuard origin path.
- Public route denial for internal API and Swagger paths.
- Hardened application containers and production SSH hosts.
- Least-privilege MongoDB application user.
- Automated TLS renewal and encrypted, restore-validated database backups.
- Ruff, Pyright, pytest, Docker, and iOS CI coverage appropriate to each repo.

## Before broad public launch

- Obtain the Apple Developer account, configure production signing, and complete
  TestFlight/App Store review assets and privacy declarations.
- Reissue and securely store any remaining provisional credentials.
- Add centralized uptime, relay/WireGuard, application, bot, backup, TLS, and
  capacity monitoring with actionable alerts.
- Schedule recurring restore drills and run a complete service recovery exercise;
  the first isolated production MongoDB restore drill passed on August 18, 2026.
- Expand physical-device and iOS-version BLE/background testing.
- Define moderation response ownership, escalation, and retention review.

## Next engineering priorities

| Area | Work | State |
| --- | --- | --- |
| Migration | Disable legacy API after supported-client adoption and remove legacy code hashes | Rollout required |
| Rate limiting | Replace process-local state with a shared adapter before horizontal scaling | Planned |
| API quality | Replace deprecated `ORJSONResponse`; expand MongoDB/S3 integration and contract tests | Planned |
| BLE protocol | Version the protocol and investigate rotating authenticated identifiers | Research |
| Notifications | Add APNs only after Apple account availability and a clear aggregation/privacy design | Deferred |
| Moderation | Build a dedicated admin service with roles, search, audit, and appeals | Planned |
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
roadmap belong in this private repository. A separate public UI laboratory may
be created later under its own license and trademark rules.
