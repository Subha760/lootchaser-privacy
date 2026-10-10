# Docryvia v0.6.0-rc2 review and release candidate

10 October 2026 (India). This is a review and code update, not an independent security certification or store publication.

## Input and scope
The desktop-labelled ZIP contains an Android application and Windows build/signing scripts, not a native desktop implementation. The second Android ZIP largely matches it; differences include build configuration, the wrapper and UI/build documentation. The uploaded audit is historical context, not instructions or proof of verification. Uploaded APK/AAB bytes match their counterparts in the desktop archive. The original APK signature verifies under its existing RSA signing certificate.

The user clarified that “not multiple time” means avoiding repeated login prompts. No cloud accounts or cross-device login restriction were introduced.

## Changes
- Brief app switches retain the RAM-only session for at most 30 seconds. All Docryvia activities share the same session deadline. Returning within that window cancels the pending background lock. Monotonic elapsed time also checks prolonged process suspension; process death loses the session. Manual lock and screen-off lock immediately. While an app activity remains in the foreground there is no inactivity timeout.
- Autofill can reuse the authenticated session, still checks the exact HTTPS hostname and still requires explicit selection of one account. Authenticating in the autofill screen also starts the bounded shared session. An unlocked vault with no matching account shows that result instead of unnecessarily asking for the master password again.
- Save-login confirmation can reuse an unlocked session and writes through the shared session lock, keeping its in-memory records synchronized. When locked, saving still requires the master password. Replacing a password preserves the entire existing record, including expiry date, notes, category, favorite, ID and creation time.
- Repeated biometric prompt requests are guarded. Terminal prompt errors and activity destruction clean up pending setup key bytes. Per-use biometric prompts cancel on stop; Android 11+ screen-lock prompts can stop the activity while authentication proceeds. Unlock results cannot reopen a stopped/finishing activity.
- Optional recovery questions: favourite food, first school or childhood nickname. Both the answer AND an independently generated 256-bit rescue code are required. Security questions alone cannot unlock this offline encrypted vault.
- Question recovery uses PBKDF2-HMAC-SHA256 (600,000 iterations) and AES-256-GCM. The random rescue code is combined with the KDF salt and is not stored. The question identifier is bound into authenticated associated data. Only the question ID and encrypted key envelope persist; plaintext answers/codes do not.
- Answers normalize Unicode NFKC, letter case, surrounding whitespace and repeated whitespace. The answer length is bounded at 200 characters. Answer entry is masked and confirmed during setup; new master passwords are confirmed during reset and password changes.
- The separate rescue code is displayed once for offline recording and checked using selected code groups. Replacing/disabling the kit invalidates its previous code. Successful question recovery consumes the entire kit in the same preferences commit as the new password and device-unlock revocation. Recovery using the main key also invalidates the question kit. The main recovery key remains valid until explicitly rotated.
- Master-password changes and both reset routes remove device-unlock wrapping material. Enable quick unlock again from Settings afterward. The underlying data-encryption key remains the same; these changes do not invalidate previously exported backups or attackers’ earlier copies of vault/access material.
- Failed recovery validates existing encrypted data before committing access changes. Temporary decrypted byte arrays and failed-unlock key bytes are wiped in additional paths. Immutable JVM strings and decrypted item objects remain garbage-collected; this is not a guarantee of physical memory erasure.
- Import deduplication uses structured identity instead of delimiter concatenation and keeps case-sensitive usernames distinct. Backup merge work is dispatched off the UI thread.
- Safe drawing insets are applied to the main, autofill and save screens for current Android edge-to-edge behavior. Version code is 7 and version name is 0.6.0-rc2. Cryptographic vault domains and format version remain unchanged for existing v0.5 data.
- Shareable source excludes private signing keys, passwords, machine configuration and generated build caches. Existing signing material remains private in the working copy and is used only to produce compatible signed artifacts.

## Support-contact update
The user changed the support/privacy email to **help@choicematrix.in** on 10 October 2026. Version 0.6.0-rc2 (code 7) carries this contact in Settings and bundled legal text. It uses the same signing certificate for installation updates. Older rc1 binaries are historical and retain their original embedded contact.

## Publisher and public privacy policy
Publisher: **choicematrix**. Support: **help@choicematrix.in**.
Public policy: https://subha760.github.io/lootchaser-privacy/docryvia-privacy.html
Published as a separate page in `Subha760/lootchaser-privacy` on `main`, commit `e9a0cae1157ac68aa9dd332486b05696dbfb1c6a`. The policy content matches the bundled local privacy text and describes the actual release-candidate behavior. Other policy pages in that repository were not edited. Settings offers the public link via the user's browser; the app still has no INTERNET permission.

## How to use the new recovery option
1. Unlock the vault, then open Settings → Question recovery.
2. Select a question, enter and confirm its answer, accept the offline-code reminder, and create the rescue kit.
3. Write down the separate rescue code and complete the displayed group check. Keep it apart from the answer and maintain an encrypted backup.
4. If you forget the password: Forgot your master password? → Question + code. Enter the answer, saved rescue code, and matching new-password fields.
5. After successful recovery, create a new question rescue kit and re-enable device unlock if wanted. Your existing main recovery key is still available until rotated.

This optional method does not recover data if you have lost the required code, and no developer-managed recovery server exists.

## Verification
- Final JDK 17 / Gradle 8.13 / AGP 8.13.2 / SDK 36 build succeeded: `lintDebug`, `testDebugUnitTest`, `assembleRelease`, `bundleRelease`.
- **26 JVM/Robolectric tests passed**, zero failures/errors/skips. Includes application lifecycle tests for a brief return preserving the session, background timeout locking, and screen-off locking. These tests exposed and led to correcting compatibility receiver registration for the protected screen-off broadcast on older Android versions.
- Debug lint: **zero errors, 30 warnings**. Warnings are mainly available dependency upgrades, icon design/themed-icon gaps, unnecessary SDK checks and KTX/style suggestions. Release vital lint also passed. Warnings remain recorded rather than suppressed.
- Source/XML permission, crypto, autofill, export and UI wiring contracts passed.
- Signed APK verifies using the existing certificate SHA-256 `8d8e66854f371cfe8c47e96053cdc943929a4a6151d6e660935fb7dd8dd16f3f`. The new AAB signature verifies. The self-signed certificate is normal for Android signing, not a security audit or approval.
- APK metadata confirms package `app.kavorynx.vault`, version code `7`, version `0.6.0-rc2`, minimum SDK 28 and target SDK 36.
- Merged release permissions contain biometric/fingerprint and the AndroidX signature-level receiver permission. No INTERNET, SMS, location, broad-storage or notification permissions are present. Debug builds include diagnostic dependencies and are not the provided signed release downloads.
- `zipalign -c -P 16 4` passed for the release APK. Both packaged 64-bit native libraries have ELF LOAD alignment at least 0x4000 (16 KB). This verifies artifact alignment, not execution on a 16 KB-page device.
- GitHub Pages privacy policy returned HTTP 200, includes the supplied publisher/support details, and exactly matches the prepared public HTML. Commit: `e9a0cae1157ac68aa9dd332486b05696dbfb1c6a`.
- Evidence is included in `docs/verification/`; SHA-256 checksums accompany the downloads.
- **Not executed:** emulator/physical-device UI flows, actual biometric/Keystore/device PIN behavior, real browser/iframe autofill, real file-provider interruptions, or independent penetration testing. No Android device was attached during this review.

## Features reviewed
| Area | Available | Remaining limits |
| --- | --- | --- |
| Passwords | Encrypted create/edit/delete; random generator; local health checks; CSV import | No online breach check, password history, passkeys or Credential Manager provider |
| Browser autofill | Exact HTTPS host matching, chosen-account fill, explicit save confirmation | Supported browser allowlist only; no verified native-app associations; real browser/iframe testing still needed |
| Documents | Local encrypted file import, categories, favourites, dates, explicit decrypted export | 8 MB single-file import cap; entire vault stored in one blob; no scanner, OCR, encrypted streaming or full secure viewer |
| Wallet/notes | Identity, address, travel, ticket, health, finance, vehicle, membership and note records | User-entered records; not issuer-verified official documents |
| Recovery | Main recovery key; optional question plus separate random code | Offline-only; answers alone are insufficient; exported backups retain their own passphrases |
| Backup | Password-encrypted ZIP export, archive limits, read-back digest where permitted, merge restore | Picker/process-death interruption and real storage-provider testing remain |
| Expiry | Encrypted dates and in-app due/expired labels | No background notification scheduler |
| Privacy | Restricted release permissions, no internet/server/analytics, screenshot protection, private encrypted storage | Rooted/compromised devices, keyboards, OS and recipients remain outside the encryption boundary |

## Required before public publication
- Run real device/browser/biometric tests across supported Android versions, especially new Android 11+ time-bound authentication and legacy device-credential CryptoObject behavior, OEM cancellation, screen-off, rotation, process death, file pickers and account-fill origins. Robolectric tests do not emulate the Android Keystore or certify UI/browser behavior.
- Obtain independent security review of recovery, phishing/iframe origin handling, Android Keystore integration and the dependency set; perform accessibility and low-memory tests.
- Validate migration/update and backup restore with synthetic v0.5 vaults on real devices before valuable data is used. Keep encrypted backups; uninstalling or clearing app data destroys the local vault.
- Publisher and support details were supplied by the user: choicematrix, help@choicematrix.in. The factual privacy policy has been published through the existing GitHub Pages policy repository; legal review of the operator identity and terms is still required. Complete store data declarations, brand clearance and internal testing/staged rollout.
- Keep the uploaded private signing key and signing passwords confidential. Preserve key continuity for updates; app signing alone does not mean store approval or security certification.

Historical audit/build documents in the source describe earlier versions; this report is the current review status.

## rc2 stability update

Added browser recognition for Samsung Internet, Brave, Edge, Opera and Vivaldi while retaining HTTPS origin restrictions. Clipboard expiry now checks unique copy ownership. Autofill configuration stops no longer invalidate the unlock UI event. New Android 11+ device wraps use time-bound Keystore authentication with a non-crypto prompt; legacy wrappers are retained. See FEATURE_ROADMAP.md for migration requirements and remaining physical-device validation. Two clipboard regression tests bring the automated total to 26.
