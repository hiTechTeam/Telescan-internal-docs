# Telescan Internal Documentation

**Private repository. Last reviewed: September 1, 2026.**

This repository contains the non-public technical and operational documentation
for Telescan. It is the cross-repository source of truth for architecture,
production topology, security boundaries, known risks, moderation, and internal
planning.

The current documentation includes a local P0 release candidate. Statements
about candidate controls do not prove deployment; the required operational and
device evidence is listed in
[P0 remediation contracts](product/P0_CONTRACTS.md).

Do not place passwords, tokens, private keys, complete `.env` files, database
exports, user data, or production log payloads in this repository. Secrets stay
in GitHub environments or on the production hosts.

## Documentation map

- [System architecture](architecture/SYSTEM_ARCHITECTURE.md)
- [Production operations](operations/PRODUCTION.md)
- [Security model](security/SECURITY_MODEL.md)
- [Known technical risks](security/KNOWN_RISKS.md)
- [Moderation workflow](product/MODERATION.md)
- [P0 remediation contracts](product/P0_CONTRACTS.md)
- [App Review runbook](product/APP_REVIEW.md)
- [Internal roadmap](product/INTERNAL_ROADMAP.md)

## Repository ownership

| Repository | Responsibility |
| --- | --- |
| `Telescan-iOS` | SwiftUI client, device sessions, BLE discovery, device-local Met and Saved lists, quick actions, reports and blocks UI |
| `Telescan-bot-app` | Telegram presentation, profile synchronization, link-code requests, confirmation messages |
| `Telescan-admin-bot` | Allow-listed Telegram moderation interface |
| `Telescan-api` | Authentication, authorization, profiles, photos, reports, blocks, and data ownership |
| `Telescan-db` | MongoDB runtime, application user, encrypted backup and recovery automation |
| `Telescan-nginx` | Moscow edge, private origin, TLS policy, host hardening, Swagger boundary |
| `Telescan-relay` | Netherlands HTTPS edge and WireGuard forwarding to Moscow |
| `Telescan-monitoring` | Metrics, logs, dashboards, alerts, and monitoring network |
| `Telescan-info` | Public product, privacy, terms, security-contact, and roadmap information only |
| `Telescan-internal-docs` | Private cross-repository architecture, security, product, and operations source of truth |
| `Telescan-audit` | Frozen audit baselines, candidate remediation deltas, evidence indexes, and handoff controls |

Repository-specific commands, environment-variable names, CI behavior, and
deployment mechanics remain documented in each private implementation
repository. Update this repository when a change affects more than one service
or changes a production trust boundary.

## Documentation rules

1. Describe deployed behavior, not an intended design, unless the section is
   explicitly marked as planned.
2. Record the review date when production topology or security controls change.
3. Link to the owning private repository instead of copying long command lists.
4. Never commit secret values. Use symbolic names such as `BOT_SERVICE_SECRET`.
5. Keep the public `Telescan-info` repository free of internal endpoints,
   addresses, deployment instructions, data schemas, and security-control detail.

## License

Proprietary and confidential. See [LICENSE](LICENSE).
