# Security Model

**Last reviewed: September 5, 2026.**

## Boundaries

- iOS/BLE are untrusted; EU terminates TLS and WireGuard protects Moscow.
- The API alone owns data; bots have separate private credentials.
- MongoDB root is operational; the API uses `readWrite` only.

## Main controls

- Short-lived access tokens, rotating refresh tokens and active session checks
- One-time keyed-digest link codes and shared server-side rate limits
- Resumable account journals, bidirectional blocks and revision-safe moderation
- Production rejection of legacy auth, placeholder secrets and wildcard CORS
- Public `404` for internal routes and Swagger/OpenAPI
- Private service ports, hardened containers and SSH key authentication
- Encrypted off-host backups and centralized metrics/logs
- iOS account-generation isolation and complete local cleanup on sign-out

BLE does not prove identity, proximity or exact distance.

Never place secrets in chat, issues, logs or Git. Deploy a replacement, verify,
then revoke the old value.

## Incident order

1. Revoke credentials and contain ingress.
2. Preserve minimal private evidence.
3. Check data integrity and restore if required.
4. Record cause and corrective action.
