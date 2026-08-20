# App Review Runbook

**Last reviewed: August 20, 2026.**

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
for 30 days. Consume one code from each batch during the final clean-install
test and provide the remaining codes privately in App Store Connect. Revoke all
unused codes after review.

## Reviewer path

1. Install and open Telescan on an iPhone running iOS 17.6 or later.
2. Tap **Continue** on the welcome screen.
3. Enter one unused eight-character review code and tap **Next**.
4. Tap **Go**, allow Bluetooth access, and enable scanning.
5. For the normal user flow, tap **Telescan_bot** on the code screen, press
   **Start** in Telegram, then return to Telescan and enter the code sent by the
   bot.
6. BLE discovery requires two physical devices. Use Review A and Review B on
   separate devices, enable scanning on both, and keep both apps running.

## App Review Notes template

```text
Telescan is designed specifically for Telegram users. A verified Telegram
identity and a public Telegram username are core product data: Telescan helps
nearby users exchange those Telegram profiles over Bluetooth. Telegram is not
offered as an optional social-login shortcut.

Normal sign-in flow:
1. Open Telescan and tap Continue.
2. On the code screen, tap Telescan_bot.
3. In Telegram, press Start to receive a short-lived, single-use linking code.
4. Return to Telescan, enter the code, and tap Next.
5. Tap Go and allow Bluetooth access to enable nearby discovery.

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
BLE_VIDEO_URL

Privacy Policy: https://tgtelescan.ru/privacy
Terms of Service: https://tgtelescan.ru/terms
Support contact: REVIEW_CONTACT
```

## Final checks

- Production API, bot, privacy policy, and terms are reachable.
- Account deletion succeeds from the in-app profile menu.
- Review A and Review B can discover each other on two physical devices.
- The submitted video shows permissions, scanning, discovery, and profile
  opening without exposing phone numbers or private chats.
- App Store privacy answers match the shipped privacy manifest and policy.
- Review notes contain only currently unused codes and a monitored contact.
