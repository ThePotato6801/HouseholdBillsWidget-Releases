# Household Bills Widget — Android downloads

This **public** repository distributes signed Android APK releases for Household Bills Widget.

The application source is maintained separately in a **private** repository. Do not upload
source code, signing keys/keystores, account data, Google tokens, bank information, or
personal financial records here.

## Updates

The Android application checks the public `update.json` feed on startup and when the
user taps **Check updates**. A valid new release is offered for download inside the app
without a GitHub login. The app checks the APK checksum, package ID, signing certificate
and version before asking Android to install it.

Android still requires the user to approve installation. It is not a silent installer.

## Developer release publishing

The private source repository contains a one-command Windows PowerShell publisher:
`tools/Publish-HouseholdBillsRelease.ps1`.

It fetches a successful GitHub Actions APK, signs it with the existing developer
signing key **locally on the PC**, validates the signer and application version,
uploads only the signed APK, verifies its public download checksum, and updates the
manifest **last**. The signing key never leaves the developer computer.

The publishing PC needs an authenticated GitHub CLI session to access the private
source/build artifacts and publish to this public distribution repository.
**Installed Android users do not need a GitHub account.**

The public `.github/workflows/validate-update.yml` independently checks the
published APK's SHA-256, package name, version and signing certificate after
manifest changes. A CI check is not a substitute for downloading and verifying
on an Android device.

## Publishing requirements

Only publish an APK once its build/tests have passed and it has been signed with the
**same certificate as the currently installed app**. GitHub Actions' ordinary debug
APK uses another signing certificate and must not be published directly.

For each new release:
1. Produce the signed APK and verify its package name, version code, and signature.
2. Upload that verified APK to a **public GitHub Release** in this repository.
3. Compute the SHA-256 checksum of the *signed APK*.
4. Update `update.json` on `main` to reference that released APK, its version code,
   display version, and SHA-256 digest. Do this **last**, after the asset exists.
5. Confirm the app recognizes the new version and can hand it to Android's installer.

Until the first signed APK is published and `update.json` is created, the public
update feed is intentionally inactive. No placeholder or unsigned APK should be
advertised as an available update.
