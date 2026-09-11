# App Review Runbook

**Last reviewed: September 12, 2026.**

## What changed

This binary uses one public flow for every user. The previous server-backed
account and Telegram-linking flow has been removed. Telescan no longer requires
Sign in with Apple, a Telegram bot, an access or review code, or a Telescan
username and password.

The user creates a profile locally. While scanning is enabled, nearby Telescan
devices exchange profiles directly over Bluetooth Low Energy. The Telegram
username is a user-entered contact field and is not authenticated by Telescan.

## Reviewer setup

- Use iOS 16.0 or later.
- No account or review credentials are required.
- Nearby discovery requires two physical iPhones with the same build installed.
- Enable Bluetooth for Telescan on both devices and keep them within range.
- Telegram is not required for registration or nearby discovery. Internet and
  Telegram or a browser are needed only to test opening a shared username.

## Reviewer path

On each iPhone:

1. Launch Telescan and tap **Continue**.
2. Add a profile photo from the camera or photo library.
3. Enter a syntactically valid public Telegram username and a name, then save.
4. Allow Bluetooth access. Make sure **Scanning** is enabled in **Profile**.
5. Wait for the profile from the other iPhone to appear in **Nearby**.
6. Open the nearby profile. Test **Save** and, if desired, the Telegram handoff.
7. Open **Met** to view a profile encountered during the last 24 hours.
8. Profile editing, scanning controls, Saved profiles and blocking are available
   from **Profile** and the profile action menus.
9. Test deletion last: **Profile → … → Delete profile** removes the local Telescan
   profile and returns the app to registration.

Bluetooth discovery and distance estimates are approximate. Discovery may take
a short time and can be affected by range, interference and iOS background
limits. No GPS location is used.

## App Review Notes — paste into App Store Connect

Thank you for confirming that the previously identified Guideline 5.6 issue was
addressed. This submission contains a new binary with a simplified serverless
architecture.

The app no longer uses Sign in with Apple, Telegram bot linking, access or review
codes, Telescan accounts, or username/password authentication. No credentials are
required for review. All users receive the same public app flow.

On first launch, tap Continue, add a profile photo, enter a public Telegram
username and a name, and save the profile. The profile is stored locally. When
Scanning is enabled, two nearby iPhones running Telescan exchange the profiles
directly over Bluetooth Low Energy; the Telescan developer does not relay them
through a backend. The Telegram username is entered by the user and is not
verified by Telescan.

To test Nearby discovery, please install this build on two physical iPhones with
iOS 16.0 or later, allow Bluetooth access on both devices, enable Scanning, and
keep the devices within range. Each device should then display the other local
profile in Nearby. Saved profiles and the 24-hour Met history can also be tested.
Telegram is needed only if you choose to test opening a shared username.

Please test profile deletion last: Profile → … → Delete profile.

Privacy Policy: https://tgtelescan.ru/privacy

Terms of Service: https://tgtelescan.ru/terms

Support: admin@tgtelescan.ru

## Before submission

- Archive with the paid publishing team and bundle ID `hiTeam.TelescaniOS`.
- Increment the build number and submit the new serverless binary.
- In App Store Connect, indicate that sign-in is not required; do not enter old
  review usernames, passwords or Telegram codes.
- Use the updated serverless Privacy Policy, Terms and App Privacy answers.
- Confirm the production archive contains no test profiles or screenshot-only
  data.
- Recheck registration, Bluetooth discovery on two physical iPhones, profile
  editing, Saved, Met, blocking and Delete profile in the submitted build.
