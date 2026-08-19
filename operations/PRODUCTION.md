# Production Operations

**Last reviewed: August 19, 2026.**

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

`telescan-mongodb-backup.timer` runs daily and:

1. creates a compressed application-database archive;
2. validates it with `mongorestore --dryRun`;
3. uploads it to a client-side encrypted off-host Restic repository;
4. downloads and validates the latest snapshot;
5. retains 7 daily, 5 weekly, and 12 monthly snapshots.

Daily snapshots are restore-validated before retention is applied. A full
isolated restore of a real encrypted production snapshot passed on August 18,
2026, including checksums, indexes, collection validation, and reads through a
temporary least-privilege application account. Detailed evidence is in
`Telescan-db/ops/restore-drills/2026-08-18.md`.

The drill proves database restoration, not a complete service RTO. Daily
backups currently imply up to roughly 24 hours of database RPO exposure; host
rebuild, service startup, storage reconciliation, and traffic cutover remain
part of a future full recovery exercise.

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

Do not regenerate WireGuard keys, rotate service secrets, replace DNS, or restore
the database during an ordinary application deployment.

## Monitoring

The dedicated monitoring host runs Grafana, Prometheus, Loki, and Alloy.
Production agents send host, container, timer, backup, and WireGuard telemetry
through `wg-monitor`; application logs are centralized in Loki. Provisioned
alerts cover endpoint failures, missing telemetry, unhealthy containers, backup
freshness, timers, WireGuard handshakes, and host capacity. Telegram delivery
uses a restricted EU egress proxy.

Remaining operational work is to calibrate alert noise under real traffic and
schedule recurring full recovery exercises.
