# Docryvia feature and release roadmap

Publisher: choicematrix. Support: help@choicematrix.in.

## Current release candidate
Encrypted local vault; passwords, documents and wallet entries; password generator and health checks; encrypted backup/restore and existing CSV import; master password and recovery key; optional question plus independent random rescue code; device unlock; conservative browser autofill; brief background session grace; screen-off/manual lock; screenshot protection.

rc2 corrects clipboard ownership, configuration-stop handling in autofill, and adds Samsung Internet, Brave, Edge, Opera and Vivaldi to browser recognition. Reported HTTPS origin and supported fields remain required. Recognizes HTML password/email field types even when autocomplete hints are absent. Adds setup guidance on the lock screen and enabled-service status in Settings. API 30+ new device wraps use a ten-second authentication-bound Keystore key and a non-CryptoObject prompt, initializing the cipher after authentication. Older wrappers remain readable via their original alias. Re-enable Device unlock after master-password login to migrate an old wrapper.

## Physical-device release gate
No device or emulator was connected during review. JVM tests do not verify hardware fingerprint, vendor credential UI, browser AssistStructure or actual autofill suggestions. Test upgrade from v0.5 and rc1 with existing vaults, fingerprint enrollment/re-enrollment, lockout, cancellation, PIN fallback, screen-off, process death, rotation and app backgrounding. Test Samsung, OnePlus, Oppo and Motorola using actual Android versions. Select Docryvia in Android autofill settings; focus a supported HTTPS login field. Browser support depends on the browser reporting origin and form metadata. Opening a website alone is not a trigger. No runtime biometric permission or Accessibility request is expected.

## Assessment of the proposed Antigravity blueprint
Useful direction, but its timeline and some implementation details are incomplete. A ten-second key cannot simply replace a per-use key while keeping the same CryptoObject flow. Clipboard label checks alone do not distinguish two copies from the same app; unique ownership markers are used. Do not change recovery normalization for existing encrypted kits: add a normalization version for new kits and retain legacy derivation. Rotation guards alone do not guarantee preservation of activity state. PdfRenderer requires a seekable file descriptor, not a byte array.

## Stage 1: stabilization and onboarding
Complete the physical-device gate above. Add an onboarding setup checklist and vendor-specific settings fallback where necessary. Keep exact HTTPS domain matching; verify malicious frames, insecure origins and mixed-domain forms never receive credentials. Audit third-party browser origin reporting before advertising compatibility.

## Stage 2: schema and migration
Add optional TOTP, bounded password history, custom fields, trash metadata and associated-app metadata with default values. Readers must accept existing schema; test old backups and interrupted writes. Version encryption envelopes and preserve associated-data rules. New standalone encrypted documents require atomic migration, authenticated file identity, path validation, size limits, backups containing all referenced data and orphan cleanup. Older apps may not read schema v2: do not promise downgrade compatibility.

## Stage 3: secure viewer, history and trash
Provide secure in-app image/PDF viewing with bounded memory and a seekable private descriptor; avoid unencrypted external exports by default. Close descriptors, recycle images and wipe owned buffers on lock. Password history must cover editor, autofill save and import changes; keep it encrypted and capped. Exclude trash from search/autofill, support restore and explicit permanent deletion, and define backup and 30-day purge behavior.

## Stage 4: TOTP and password studio
Implement RFC 6238 with strict Base32 and otpauth parsing, supported algorithm/digit/period settings, published test vectors and clock-warning behavior. Add a bundled offline QR scanner; ask for camera permission only when scanning. Protect TOTP seeds in all views, logs and exports. Generator options must use SecureRandom and unbiased selection. Choose a licensed Diceware list; 2,000 words gives about 11 bits per word, so four words are insufficient for a strong vault master password recommendation.

## Stage 5: Android integration and interface
Add user-configurable foreground inactivity locking, keyboard inline autofill with dropdown fallback, a lock tile, system dark mode and externalized strings. Native-app autofill needs trusted package/signing identity and verified Digital Asset Links where applicable; a linked package string alone is not sufficient origin verification. Apply lock and trash rules to every integration.

## Stage 6: imports and release
Add Bitwarden JSON, 1Password and KeePass parsers using real format fixtures, resource limits, import preview and per-record failure reporting. Avoid plaintext staging and secret logs. Finish localization, accessibility, actual device screenshots and store metadata. Indus initial listing and app verification must be completed in its console; the selected GitHub action upgrades an existing registered app and requires its token stored securely as INDUS_APP_STORE_API_TOKEN. No Indus release has been submitted by this review.
