# App Review Runbook

**Last reviewed: September 5, 2026.**

## Accounts and codes

Prepare **Telescan Review A/B** without personal data. Each needs a public
Telegram username, protected access and one `/start` with the production bot.

Issue separate 90-day single-use code batches. Store them only in App Store
Connect and revoke unused codes. Never commit review credentials.

The archive must use the paid publishing team, bundle ID
`hiTeam.TelescaniOS`, Sign in with Apple and a new build number.

## Reviewer path

1. Open Telescan on iOS 17.6+ and tap **Continue**.
2. Complete Sign in with Apple.
3. Enter one unused review code and tap **Link**.
4. Allow Bluetooth access.
5. For BLE testing, use Review A and B on two physical iPhones.

## Review notes

State that Apple authenticates and Telegram supplies the public profile. Add:

- the steps above;
- expiry time and unused codes for Review A/B;
- the two-device BLE requirement and [demonstration video](https://github.com/user-attachments/assets/a3910e7c-c475-40b3-a3c3-43ab5ea79c30);
- `https://tgtelescan.ru/privacy` and `/terms`;
- `admin@tgtelescan.ru` as the monitored contact.

Verify API/bot/legal pages, deletion, two-device BLE, privacy answers and signing.
