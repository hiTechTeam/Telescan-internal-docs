# Known Technical Risks

**Last reviewed: September 5, 2026.**

- iOS background BLE is best-effort. A stable radio ID can be observed,
  correlated or replayed, and RSSI is not precise distance.
- An authenticated user who learns a Telescan ID can request the profile without
  independent proof of proximity.
- Local Met/Saved snapshots may be visible on a compromised unlocked device.
- Account merge/deletion spans MongoDB and object storage; journals reduce but
  do not remove operational recovery risk.
- Nearby notifications depend on iOS scheduling and user settings.
- Moderation has no assignment, formal SLA, appeals or multi-role console.
- EU relay, WireGuard and Moscow origin are availability dependencies.
- Monitoring and Telegram alerts are centralized and still need threshold
  tuning and recurring delivery drills.
- Current-schema restore, full recovery and signed physical-device evidence
  must be repeated before broad release.
- Private-repository controls are partly procedural on the current GitHub plan.
- Long-lived credentials need scheduled rotation; Python build inputs should be
  pinned more strictly.

Detailed acceptance evidence and risk ownership belong in `Telescan-audit`.
