# Docryvia v0.8.0-rc2 production readiness

Publisher: choicematrix. Support: help@choicematrix.in.

This build adds production hardening to the tested v0.8 organization upgrade. It is a release candidate; a version number and a signature do not establish real-device acceptance or store approval.

## Implemented hardening

- Vault ciphertext reads are bounded to 64 MiB. Writes enforce the same limit before replacing the atomic vault file. Documents are bounded to 8 MiB plus their authenticated-encryption envelope, including recovered AtomicFile backups. Oversized/corrupt files are rejected without replacing existing vault data. Existing oversized legacy vaults require a bounded export using the earlier version; their on-disk data is retained.
- Setup refuses to overwrite orphaned vault ciphertext or atomic recovery copies when configuration is missing.
- Regression tests cover exact boundaries, endless and unusual input streams, and oversized atomic document fallback copies.
- Gradle dependency locks record resolved versions; committed SHA-256 verification metadata detects artifact changes against this reviewed build baseline. A CycloneDX inventory records release dependency coordinates and artifact SHA-256 values for review; an OSV Maven check of 74 resolved release artifacts returned no matching advisory records. This is automated advisory coverage, not an independent security certification.
- The bundled CI workflow checks both debug and release tests and lint, with read-only repository permissions, no signing secrets and commit-pinned actions. Signed artifacts retain the original certificate and production screenshot protection. The separate demo build remains isolated.
- Existing encryption formats, recovery wraps, normal-size vaults, organization, explicit duplicate review, secure viewer, TOTP, QR, backups, app/browser autofill and session locking are retained.

## Validation evidence

The final debug and release suites each passed 76 cases twice (304 executions), with no failures/errors/skips. Both lint reports have zero errors. Strict dependency checksum verification passed against the committed metadata. Packaging checks APK/AAB signatures, certificate continuity, permissions, demo-fixture exclusion, ZIP integrity, private-key exclusion and 16 KiB native alignment. Previous v0.8 emulator evidence demonstrates navigation, PDF rendering and production capture blocking; it does not substitute for physical phones or the exact new build's device acceptance.

## Remaining acceptance gates

Test installing this signed update over a populated v0.5/v0.6/v0.8 vault on real Samsung, OnePlus, Oppo and Motorola phones. Verify fingerprint enrollment, cancel/lockout, Android device PIN, selecting Docryvia as autofill provider, exact-origin fills and saves in supported browsers/native apps, rotation/background/screen-off, recovery and encrypted backup restoration. Verify accessibility/font scaling and low-memory behavior. An independent security review and publisher review of store/privacy declarations are still needed before a broad rollout.

Indus submission requires a registered app listing and INDUS_APP_STORE_API_TOKEN in the release repository. The prepared deployment workflow is not evidence of an upload or store approval. Keep the release signing key private, backed up separately, and never upload it with source or screenshots.
