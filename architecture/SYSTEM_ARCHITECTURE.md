# Telescan System Architecture

**Last reviewed: September 5, 2026.**

Telescan lets nearby iPhones exchange a random public Telescan ID over BLE and
resolve the linked public Telegram profile through the authenticated API. It
does not read Telegram messages or contacts and does not upload GPS, RSSI,
distance, Met history or Saved profiles.

## Components

- **iOS:** BLE, device sessions and local Met/Saved data.
- **Bots:** Telegram linking and allow-listed moderation.
- **API + MongoDB/S3:** application logic and data ownership.
- **EU/Moscow/monitoring:** TLS relay, private origin and observability.

```text
iOS -> EU relay -> WireGuard -> Moscow nginx -> API -> MongoDB/S3
Telegram bot -------------------------------> API
Admin bot ----------------------------------> API
```

## Identity and sessions

Apple is the production identity; Telegram is linked with a one-time digest
code. Linking preserves the public profile. Account operations are resumable,
tokens rotate and protected requests check the active device session.

## BLE and local data

A BLE ID is untrusted until the API returns a complete, non-blocked profile.
Nearby entries expire after signal loss; resolved encounters move to local
**Met** history for up to 24 hours. **Saved** is also device-only. iOS controls
background delivery, so discovery is best-effort rather than proof of presence.

## Moderation and deletion

Reports are idempotent, blocks work in both directions and bots never access the
database. Deletion removes account data; iOS clears local account state.

Public edges return `404` for `/internal/`, Swagger and OpenAPI. API and MongoDB
ports remain loopback/private only.
