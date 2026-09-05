# Telescan Internal Documentation

**Private repository. Last reviewed: September 5, 2026.**

Short cross-repository reference for architecture, production, security and
product operations. Detailed commands stay in the repository that owns them.

- [Architecture](architecture/SYSTEM_ARCHITECTURE.md)
- [Production](operations/PRODUCTION.md)
- [Security model](security/SECURITY_MODEL.md)
- [Known risks](security/KNOWN_RISKS.md)
- [Moderation](product/MODERATION.md)
- [P0 contracts](product/P0_CONTRACTS.md)
- [App Review](product/APP_REVIEW.md)
- [Roadmap](product/INTERNAL_ROADMAP.md)

The API owns data and authorization; iOS owns BLE and device-local state; bots
are restricted presentation clients; database, nginx, relay and monitoring
repositories own their infrastructure. `Telescan-info` is public, while
`Telescan-audit` holds frozen evidence.

Never commit secrets, user data, database exports or production log payloads.

## License

Proprietary and confidential. See [LICENSE](LICENSE).
