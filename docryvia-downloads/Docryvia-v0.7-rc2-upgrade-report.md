# Docryvia v0.7.0-rc2 upgrade

Publisher: choicematrix. Support: help@choicematrix.in. Version code: 9. Package: app.kavorynx.vault.

## Implemented features

| Feature | Final implementation |
|---|---|
| Vault compatibility | Reads schema v1 and v2; optional fields default correctly; original encryption domains and key wraps retained. New writes use v2. Downgrades after migration are unsupported. |
| Device unlock and setup | Android 11+ time-bound authenticated Keystore flow, legacy wrappers retained, explicit setup instructions and service-enabled status. Android 9–10 strong biometric fallback. |
| Recovery | New question kits normalize punctuation with authenticated v2 metadata. Original and modified unversioned rc2 question kits are both supported: legacy normalization is attempted first, then punctuation-stripped normalization only after authenticated decryption fails. Answers still require a separate random rescue code. |
| Secure documents | PDFs use a seekable anonymous RAM-file bridge and native PdfRenderer; images are decoded into bounded bitmaps. FLAG_SECURE, closed descriptors and owned-byte cleanup. No plaintext preview files. Explicit user-requested external document export remains available. |
| Document storage | Separate AES-GCM files with authenticated random references, bounded sizes and path validation. Legacy embedded documents migrate atomically on vault write; backups materialize documents and remain self-contained. Orphans are cleaned after successful unlock/permanent deletion. |
| TOTP | Standalone Authenticator page and optional per-login seeds, offline QR scanning, SHA1/SHA256/SHA512, 6–8 digits and periods 15–120 seconds. Live progress and explicit copy in the authenticator, editor and login account cards. QR results are registered at the root so editor disposal during camera navigation does not discard them; unsaved drafts stay only in RAM and are dropped on lock. Device clock must be correct. |
| Password history | Migrates modified rc2 string histories (newest first) into modern dated entries (oldest first), with unknown legacy dates explicitly labeled. Masked by default with explicit reveal/hide. Keeps five previous passwords; covers editor and autofill-save changes, restores through the editor, remains encrypted. |
| Trash | Soft deletion, restore, individually confirmed permanent deletion, confirmed empty-trash operation, excluded from normal views/autofill. Entries older than 30 days purge at unlock. Backups include trash and history. |
| Custom fields | Label/value pairs with optional visual masking; all values are encrypted, regardless of masking. |
| Password studio | SecureRandom, selectable character groups, ambiguous-character exclusion, uniform rejection sampling. EFF 7,776-word phrases with 4–10 words, default six. Space, underscore and dot separators avoid ambiguity with hyphens inside EFF words. Optional word capitalization and a random three-digit suffix retain the large dictionary. |
| Locking | One/two/five minute foreground inactivity options and Never while open; two-minute default. Existing screen-off/manual/30-second background locks retained. Quick Settings lock tile. |
| Autofill | Expanded browser recognition; HTML password/email field detection; exact HTTPS origin requirements; rejects insecure/mixed-origin forms. API 30+ inline keyboard chips with dropdown fallback and authenticated selection. |
| Native app associations | Explicit installed-app picker, package plus signing-identity pin. Signature changes or absent apps block filling. Web content is not silently treated as verified native content. No unverified automatic site-to-app linking. |
| Imports | Google, 1Password, KeePass CSV and unencrypted Bitwarden JSON. Bounded parser, pre-import ready/skipped summary, confirmation, case-sensitive credential identity, existing-data retention. Unsupported entries remain visibly skipped. |
| Theme and resources | System dark mode, semantic surfaces, resource-backed core interface text, 359 string resources. English supplied; additional translations can be added without changing stable schema/routing identifiers. |
| Publishing resources | Updated public/bundled privacy policy, listing text, source, resource pack, signed APK/AAB, checksums and pinned Indus workflow kit. |

## Verification

Final debug and release regression suites each passed twice after the fixes: 256 successful test executions (64 cases per variant per run). Each has 64 passing cases, including tests modeled on Android 9/API 28 and Android 15/API 35. The packaged verification folders contain the exact run results, build logs and lint reports. Both final runs have zero failures/errors/skips. Debug and release lint have zero errors and 37 warnings each. Lint warnings are retained openly, mostly dependency updates, icon styling, KTX suggestions and an expected API-30 inline attribute.

Tests cover legacy schema and recovery compatibility, authenticated portable document backups, tampering/path rejection, history through editor/autofill, trash retention and restoration, native signing-identity replacement, secure-viewer owned-buffer cleanup, mixed/insecure form origins, foreground/background/screen-off locking, tile locking, CSV/JSON imports, generator options, the full published RFC 6238 vectors for SHA1/SHA256/SHA512, and clipboard ownership.

The native RAM-file bridge has C source and rebuild instructions for all four Android ABIs. Its ELF identities, non-executable stack and 16 KB LOAD alignment are checked. On the Linux build host, 100 anonymous seekable RAM-file write/read/close cycles verify file size, no pathname and close-on-exec behavior. Android 9 upstream app seccomp allowlists include memfd_create. These host checks do not substitute for Android native PDF/device tests.

APK/AAB signatures, matching upgrade certificate, version/package/permissions, ZIP integrity and 16 KB APK/64-bit native alignment are verified before publishing. The supplied APK uses the same signing certificate as earlier builds. No private signing material is included in source/resources.

## Limits and release acceptance

No physical Samsung/OnePlus/Oppo/Motorola device or Android emulator is connected to this workspace. Hardware fingerprint/PIN, real camera QR capture, actual PDF rendering on OEM devices, keyboard chips and real-browser autofill have not been exercised on those devices. Robolectric is simulated Android behavior, not a hardware certification. There is no independent penetration-test certification or store approval.

Documents remain limited to 8 MB each. Portable backups have a bounded approximately 45 MB plaintext payload budget; very large vaults need a future streaming backup format. Unsupported native/browser form metadata is rejected safely instead of guessed. Native app links are explicit user trust plus certificate pinning, not automatic Digital Asset Links verification. Imports support canonical unencrypted manager exports; resolve skipped records before removing their originals. Clipboard/managed-memory copies and historical backups cannot be guaranteed physically erased.

Initial Indus registration/listing and the INDUS_APP_STORE_API_TOKEN repository secret remain external setup requirements. The supplied marketplace action upgrades an existing registered app; no store upload was performed by this build.
