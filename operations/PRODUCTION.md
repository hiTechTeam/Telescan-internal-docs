# Production Operations

**Last reviewed: September 5, 2026.**

## Topology

| Role | Address |
| --- | --- |
| EU relay | `212.69.87.142`, WireGuard `10.77.0.2` |
| Moscow origin | `89.169.153.38`, WireGuard `10.77.0.1` |
| API / MongoDB | Moscow loopback `8000` / `27017` |
| Monitoring | `84.201.181.214`, private network `10.78.0.0/24` |

API traffic goes through EU and WireGuard to Moscow. `tgtelescan.ru` serves
legal pages. Grafana, MongoDB and Swagger require administrator tunnels.

## Deployment rules

- Deploy only protected `main` revisions.
- Keep runtime secrets in mode-`600` host `.env` files.
- Shared hosts use `/tmp/telescan-production-deploy.lock`.
- Releases are immutable and switch through an atomic `current` pointer.
- Never delete a retained transaction intent or rollback image manually.
- API deployment keeps ingress closed through migrations and local checks.

## Backup and recovery

MongoDB has daily encrypted off-host backups. Test restores in isolation; never
drop production for a drill.

Recovery order:

1. DNS and EU reachability.
2. WireGuard and Moscow `/health/origin`.
3. API readiness and MongoDB health.
4. User/admin bots and legal pages.
5. TLS and backup timers.
6. Monitoring telemetry and alerts.

## Monitoring

EU and Moscow agents send metrics/logs through `wg-monitor`. Check endpoints,
containers, timers, backups, disks and WireGuard; repeat alert/recovery drills.
