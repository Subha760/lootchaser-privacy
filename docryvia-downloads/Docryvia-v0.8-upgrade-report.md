# Docryvia v0.8.0-rc1 upgrade

Publisher: choicematrix. Support: help@choicematrix.in.

## Organization and safe duplicate review

Passwords can be filtered by category and favorites, searched across metadata, and sorted by title or creation date. Categories can be edited. Documents also support metadata search. Secrets and private notes are excluded from search.

Duplicate review groups active password accounts by exact HTTPS host and exact username. Subdomains and case-sensitive usernames remain distinct. Choose a keeper and explicitly confirm moving another entry to Trash. Differing passwords are flagged. No passwords, notes, TOTP seeds, history or app links are merged or overwritten; trashed entries remain restorable under the existing retention policy. Import now skips identical password content rather than dropping differing metadata. Colliding password IDs receive a new ID, preserving the existing entry.

## Existing features retained

Encrypted offline vaults, legacy vault/recovery compatibility, biometric/device unlock, browser and linked native-app autofill, secure RAM-backed document viewing, offline TOTP and QR scanning, password history, custom fields, Trash, password/passphrase studio, encrypted backups/import, foreground/background locking, Quick Settings lock tile, dark theme, and protected clipboard remain available. Production screenshots remain blocked.

## Screenshots

Real app screens are captured from an isolated Android 9 emulator demo application with fictional fixtures. Its separate application ID and compile-time demo mode permit screenshots without weakening the signed release. Demo mode cannot be activated by release intents and cannot initialize a production vault. Do not enter real credentials into the demo build.

## Validation

Final debug and release unit suites each contain 71 cases, run twice (284 executions), with zero failures/errors/skips. Debug and release Android lint each report zero errors and 39 warnings. Release APK and AAB signatures, original certificate continuity, ZIP integrity, restricted permissions, private-key exclusion, native RAM file contracts and 16 KB ELF/APK alignment are checked during packaging. Actual Android 9 emulator navigation and one-page PDF rendering succeeded. The signed production window retained FLAG_SECURE, and capture attempts yielded no image bytes. Ten 1080×1920 screenshots are provided from the isolated demo.

Physical Samsung, OnePlus, Oppo and Motorola fingerprint/OS autofill behavior requires device testing; emulator screenshots do not establish that coverage. Opening a website alone does not trigger Android autofill: Docryvia must be selected as the autofill provider and a supported login field focused. This is a release candidate, not an app-store approval. Indus submission still requires the registered listing and repository API-token secret.
