# App Review Runbook

**Last reviewed: September 1, 2026.**

## Review setup

Use two dedicated Telegram profiles containing no personal data:

- `Telescan Review A` is the primary reviewer identity.
- `Telescan Review B` is the second identity for optional two-device BLE testing.

Both profiles must have a public Telegram username, two-step verification, and
continued access to their phone numbers through review and any appeal. Start the
production Telescan bot once from each profile before preparing the submission.
Never commit Telegram IDs, Telescan IDs, clear review codes, phone numbers, or
App Store Connect contact details.

Issue a separate batch of ten single-use codes for each review profile, valid
for 90 days. Consume one code from each batch during the final clean-install
test and provide the remaining codes privately in App Store Connect. Revoke all
unused codes after review.

A review profile can be attached to only one Apple identity at a time. Multiple
codes from the same batch support retries or reinstalls using the same Apple
subject; they do not attach the same Telegram profile to unrelated Apple
identities. Use Review A and Review B for independent identities and two-device
BLE testing.

## Reviewer path

1. Install and open Telescan on an iPhone running iOS 17.6 or later.
2. Tap **Continue** on the welcome screen.
3. Authenticate the primary Telescan account with **Sign in with Apple**.
4. Enter one unused eight-character review code and tap **Link**. Scanning is
   enabled automatically after registration; allow Bluetooth access when asked.
5. For the normal Telegram-linking flow, tap **Telescan_bot** on the code screen, press
   **Start** in Telegram, then return to Telescan and enter the code sent by the
   bot.
6. BLE discovery requires two physical devices. Use Review A and Review B on
   separate devices, enable scanning on both, and keep both apps running.

## App Review Notes template

```text
Telescan uses Sign in with Apple for primary account authentication. A verified
Telegram identity and a public Telegram username are then linked as the profile
that nearby users can open. Telegram is not used as the primary sign-in method.

Normal sign-in flow:
1. Open Telescan and tap Continue.
2. Complete Sign in with Apple.
3. On the Telegram screen, tap Telescan_bot.
4. In Telegram, press Start to receive a short-lived, single-use linking code.
5. Return to Telescan, enter the code, and tap Link.
6. Allow Bluetooth access when asked; nearby discovery starts automatically.

For App Review, use any one of the single-use codes below. Each code links only
to a dedicated review profile containing no real user data. A code becomes
invalid immediately after one successful use. If the app is reinstalled or
tested on another device, use the next unused code.

Primary profile — Telescan Review A (@REVIEW_A_USERNAME):
REVIEW_A_CODE_1
REVIEW_A_CODE_2
REVIEW_A_CODE_3
REVIEW_A_CODE_4
REVIEW_A_CODE_5

Optional second profile for two-device BLE testing — Telescan Review B
(@REVIEW_B_USERNAME):
REVIEW_B_CODE_1
REVIEW_B_CODE_2
REVIEW_B_CODE_3
REVIEW_B_CODE_4
REVIEW_B_CODE_5

Bluetooth discovery requires two physical devices. Demonstration video:
https://github.com/user-attachments/assets/a3910e7c-c475-40b3-a3c3-43ab5ea79c30

Privacy Policy: https://tgtelescan.ru/privacy
Terms of Service: https://tgtelescan.ru/terms
Support contact: admin@tgtelescan.ru
```

## Final checks

- Production API, bot, privacy policy, and terms are reachable.
- Account deletion succeeds from the in-app profile menu.
- Review A and Review B can discover each other on two physical devices.
- The submitted video shows permissions, scanning, discovery, and profile
  opening without exposing phone numbers or private chats.
- App Store privacy answers match the shipped privacy manifest and policy.
- Review notes contain only currently unused codes and a monitored contact.
