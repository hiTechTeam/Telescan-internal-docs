# Production Operations

**Last reviewed: September 1, 2026.**

This runbook covers the P0 release candidate. Source-controlled controls are
not claims about current host state. The candidate must not be called accepted
until the production attestation, restore, rollback, telemetry, alert-delivery,
and legal-origin checks are dated and attached to the release.

## Topology

| Role | Address | Notes |
| --- | --- | --- |
| Netherlands relay | `212.69.87.142` | Public `api.tgtelescan.ru` TLS endpoint; WireGuard `10.77.0.2/30` |
| Moscow origin | `89.169.153.38` | Private API origin; WireGuard `10.77.0.1/30` |
| Moscow API | `127.0.0.1:8000` | FastAPI loopback publication only |
| Moscow MongoDB | `127.0.0.1:27017` | MongoDB loopback publication only |
| WireGuard origin | `10.77.0.1:8081` | nginx listener restricted to the EU peer |
| Monitoring | `84.201.181.214` | Grafana, Prometheus, Loki, and Alloy; UI bound to loopback |
| Monitoring network | `10.78.0.0/24` | Dedicated WireGuard transport for metrics and logs |

Normal API flow:

```text
iOS / user VPN -> api.tgtelescan.ru:443 -> EU nginx
               -> WireGuard -> Moscow nginx 10.77.0.1:8081
               -> FastAPI on the private Docker network
```

The relay terminates TLS and is therefore both a trust-boundary component and
an availability dependency. The EU-to-Moscow hop is encrypted by WireGuard.
Moscow nginx also serves the public main-domain legal pages and retains the
documented direct-origin recovery path. Normal app traffic must still resolve
`api.tgtelescan.ru` to the EU relay; verify DNS and the Moscow host firewall as
part of every edge change.

## Administrative tunnels

Swagger stays enabled in FastAPI but is blocked at every public nginx edge.
Reach the Moscow loopback port through the EU jump host:

```sh
ssh -N \
  -L 9000:127.0.0.1:8000 \
  -i ~/.ssh/telescan_cloud \
  -o 'ProxyCommand=ssh -i ~/.ssh/telescan_cloud -W %h:%p ubuntu@212.69.87.142' \
  ubuntu@89.169.153.38
```

Open `http://127.0.0.1:9000/docs` while the command is running. The final SSH
target must be Moscow; the EU host has no process on port 8000.

MongoDB uses the same path with a different local port:

```sh
ssh -N \
  -L 27018:127.0.0.1:27017 \
  -i ~/.ssh/telescan_cloud \
  -o 'ProxyCommand=ssh -i ~/.ssh/telescan_cloud -W %h:%p ubuntu@212.69.87.142' \
  ubuntu@89.169.153.38
```

Connect the database client to `127.0.0.1:27018`. Prefer the least-privilege
application account for data inspection; reserve the root account for database
lifecycle and recovery.

## Deployment ownership

- `Telescan-api`, `Telescan-bot-app`, and `Telescan-admin-bot` build hardened
  application containers and wait for health checks.
- `Telescan-db` reconciles the application user, preserves the MongoDB volume,
  verifies both database accounts, and installs backup automation.
- `Telescan-nginx` applies the Moscow SSH/Fail2ban baseline, certificate timer,
  configuration validation, and nginx deployment.
- `Telescan-relay` uploads a release over SSH, verifies immutable systemd units,
  rebuilds the relay, and runs end-to-end relay checks.
- `Telescan-monitoring` deploys the central observability stack after CI and
  verifies service readiness, production telemetry, and Telegram egress.

All production workflows reject non-`main` revisions before mutation. Services
sharing a host acquire `/tmp/telescan-production-deploy.lock` with a bounded
wait, in addition to repository-level GitHub concurrency, so separate
repositories cannot mutate that host concurrently. Preflight resolves the
expected target and validates required files and configuration. Deployment
then activates a fully uploaded immutable release through an atomic `current`
pointer and records an fsynced transaction intent, old/new image identities,
and recovery controller before changing runtime state. Boot and online recovery
watchers finish or roll back an interrupted transaction; injected child and
outer-session termination tests cover the boundaries. A failed mutation or
private health gate restores and verifies the prior release and image.

API rollout additionally closes the owner-scoped nginx maintenance gate before
migrations and keeps it closed through local health, invariant, legal, and
singleton-topology checks. The candidate is committed durably before ingress is
opened; a subsequent public-origin failure closes ingress again and requires
operator recovery instead of starting an older API that may not understand the
new journals. Database migrations must remain backward compatible because
application image rollback never rolls back data.

Deployment credentials belong to GitHub production environments. Runtime
secrets stay in mode-`600` host `.env` files. Never copy them into images,
documentation, Actions logs, or repository secrets shared with unrelated jobs.

## TLS renewal

Moscow and EU each run a systemd timer twice daily with a randomized delay.
Certbot reloads nginx only after a successful renewal.

Operational checks:

```sh
systemctl list-timers telescan-certbot-renew.timer
journalctl -u telescan-certbot-renew.service
```

nginx and Certbot images are pinned by version and immutable multi-platform
digest. Upgrade only through a reviewed repository change.

## Database backup and recovery

The production rollout must enable and verify
`telescan-mongodb-backup.timer`. Each daily run:

1. validates database, application-role, storage, and Restic configuration;
2. acquires the backup lock and creates a compressed application-database
   archive under `fsyncLock` or another documented quiesced boundary;
3. validates it with `mongorestore --dryRun`;
4. uploads it to a client-side encrypted off-host Restic repository;
5. downloads and validates the latest snapshot;
6. retains 7 daily, 5 weekly, and 12 monthly snapshots.

Daily archives receive a structural dry run before retention is applied. A full
isolated restore of a real encrypted production snapshot passed on August 18,
2026 for the pre-P0 schema. That evidence is historical and does not accept the
current candidate. The new drill restores to an isolated Docker network and
must validate current journal/evidence/moderation indexes, domain references,
no active merge pointing to a missing source/target, the temporary application's
read/write ability in its own database, and denial of admin or foreign-database
authority. It records snapshot identity, elapsed restore time, and observed
backup age without recording credentials or user data.

The drill proves database restoration, not a complete service RTO. Daily
backups currently imply up to roughly 24 hours of database RPO exposure; host
rebuild, service startup, object-storage reconciliation, and traffic cutover
must be measured separately. A current-schema production-snapshot drill remains
mandatory before P0 acceptance.

Never test recovery by dropping the production database. Restore the selected
snapshot into an isolated database first, validate documents and indexes, then
plan a controlled production cutover.

## Host hardening

Both production hosts disable root login, password and keyboard-interactive SSH,
and X11 forwarding. Fail2ban protects SSH. The EU firewall exposes only SSH,
HTTP, HTTPS, and WireGuard. Deployment users are separated from administrator
users; Docker membership is treated as root-equivalent.

Application containers use unprivileged users, read-only root filesystems,
bounded tmpfs mounts, dropped capabilities, `no-new-privileges`, restart
policies, and health checks appropriate to each service.

## Recovery order

1. Confirm DNS and EU host reachability.
2. Confirm `wg-quick@wg0` and the WireGuard peer handshake.
3. Confirm the Moscow origin responds from EU at `/health/origin`.
4. Confirm API liveness/readiness and MongoDB health on Moscow.
5. Confirm user-bot and admin-bot health.
6. Validate public legal pages and confirm documentation/internal routes return
   `404` publicly.
7. Validate certificate timers and the most recent database snapshot.
8. Verify the canonical legal version/digest at both `api.tgtelescan.ru` and
   `tgtelescan.ru`, including TLS and same-origin route behavior.

Do not regenerate WireGuard keys, rotate service secrets, replace DNS, or restore
the database during an ordinary application deployment.

## Monitoring

The dedicated monitoring host runs Grafana, Prometheus, Loki, and Alloy.
Production agents send host, container, timer, backup, and WireGuard telemetry
through `wg-monitor`; application logs are centralized in Loki. The restricted
ingest profile exposes write-only Prometheus/Loki paths to the monitoring
network and denies read/query/admin paths. Migrate every agent to that endpoint,
verify ingestion, then bind legacy direct ingest to loopback; do not reverse the
order.

Provisioned rules cover endpoint/container failure, missing or stale host state,
backup age, required timers, WireGuard handshakes, the monitoring data mount,
disk and inode pressure, and Loki absence/growth. Deployment must require fresh
state metrics rather than leave the compatibility switch disabled. The
monitoring data filesystem must be mounted before startup.

Grafana secret-key replacement is a guarded data migration, not a normal
environment-variable rotation. Rehearse against a copy, back up the database,
run the repository preparation/check procedure, and prove existing encrypted
data can still be read before enabling the new key. Telegram acceptance requires
a bounded interactive drill that observes both firing and resolved messages and
cleans up its synthetic alert.

Before handoff, run the read-only attestation collector on each role. It must
fail closed when mandatory DNS/TLS, listener, firewall, WireGuard, SSH effective
policy, filesystem, timer, port, or image-digest evidence is unavailable. Review
and sanitize the output before adding it to the private audit channel.

After those gates, remaining operational work is alert-noise calibration and
recurring full-service recovery exercises.
