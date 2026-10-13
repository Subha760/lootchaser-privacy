# Docryvia v0.9.0-rc1 — requested fixes

Publisher: choicematrix. Support: help@choicematrix.in.

| Request | Result |
| --- | --- |
| Copy the secure key | Both recovery screens have an explicit Copy secure key button. Copies use the sensitive clipboard flag where supported and an owned-copy 30-second cleanup. The app does not copy keys automatically. |
| Remove Group 2 / Group 6 | Removed the partial-key challenge, its two input fields and their resources. Each key now has a simple offline-save acknowledgement. The compatible recovery-token format is unchanged. |
| Fingerprint on creation | New-vault setup has an Enable fingerprint/device unlock option. Android authentication starts after both recovery keys have been acknowledged. Cancellation leaves device unlock disabled. |
| Mandatory secret question | New setup requires a valid answer, confirmation and a question. The encrypted question kit is configured with the master/recovery wraps in one settings commit. The answer alone never unlocks the vault; the independent rescue code is required. Existing vaults remain usable and are not reinitialized. |
| Impossible photo instructions | Removed screen-capture wording from production UI resources. Recovery now describes offline saving and explicit copying. Production protected-window flags remain enabled. |
| Saved-document popup | Successful document import/save displays a short Snackbar; failure does not display success. Import shows working feedback and ignores repeated picker requests while busy. |
| EPFO / Google autofill | Public EPFO HTML was inspected on 13 October 2026: userName/UAN and password fields have autocomplete off. Scanner now recognizes explicit account identifiers and web-password input types while excluding CAPTCHA, one-time codes and hidden fields. Exact HTTPS origin checks remain enforced. No device/browser login was submitted. |
| Enable status stale | Device-unlock state updates after callback/disable and on resume. Autofill compares the selected Docryvia component, refreshes on resume and changes the action to Manage autofill when selected. |
| Wallet-style popup | Android 13+ fill-dialog presentation is supplied when Android requests it, alongside keyboard/dropdown fallback. Suggestions retain authentication and do not return plaintext passwords before account approval. Android/browser controls popup timing; the app does not use a floating credential overlay. |
| Startup permissions | First-run Access and permissions page offers Camera access and autofill selection, and explains per-file storage grants. Camera denial blocks QR scanning. File access requires Android picker grants. The core offline vault needs neither broad storage nor Accessibility/overlay permissions, so these are not requested. |
| Screen-lock alternatives | Separate strong-biometric and Android screen PIN/pattern/password buttons are available on supported Android versions. Android chooses the eligible fingerprint/strong-face sensor; weak 2D face cannot decrypt the vault. Android 9/10 retain strong-biometric/master-password fallback. Older per-use wrappers remain supported; re-enable device unlock after an OS upgrade to add credential support. |
| Smoothness/security | Added short navigation fades, disabled repeated saves during work, retained new-item drafts across benign recompositions and excluded vault/password-entry windows from other autofill providers. Secrets remain RAM-only and clear on lock/background events. |

## Verification

Final debug and release regression suites each contain 98 cases and each passed twice: 392 successful executions, with zero failures/errors/skips. Both lint reports have zero errors and 49 warnings. Strict dependency checksum verification passed. Coverage includes mandatory question setup and invalid-answer rejection, no plaintext answer/code in settings, recovery clipboard privacy/cleanup, real EPFO field identifiers in synthetic Android structures, exact provider status, strong-only authenticator masks, protected/excluded vault windows, and authenticated initial dropdown/fill-dialog responses on Android 9 and 15 runtimes.

APK/AAB signature continuity, permission metadata, native 16 KiB alignment, strict dependency verification, ZIP checks and private-key exclusion are checked at packaging. UI captures use a separate emulator demo with fictional data; physical Samsung/OnePlus/Oppo/Motorola authentication and actual browser/keyboard behavior are not claimed as tested. A website can suppress autocomplete or not expose usable fields. Use the exact login host and the browser’s long-press Autofill action where supported. Google Credential Manager/passkey prompts are separate from the selected autofill service. Store approval and independent security certification are not claimed.

The signed production update installed over the prior emulator build; its protected window blocked capture, and APK inspection confirmed demo preview routes and fixture data were absent. Seven actual demo UI images verify copy feedback, required-question/device-option controls and selected-provider status.
