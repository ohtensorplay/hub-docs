# Passkeys

Passkeys provide phishing-resistant browser sign-in with a fingerprint, face, device PIN, or password-manager confirmation. MEGA stores the credential's public key; the private key remains with your device or passkey provider.

## Before you create one

Use a current browser and operating system with WebAuthn support. Registration requires an active MEGA browser session and user verification on the device.

Choose where the credential should live:

- A synced passkey can follow you through a platform account or password manager.
- A device-bound passkey stays on one authenticator and needs a second credential for redundancy.

Keep another working sign-in or recovery method until you have tested the new passkey from a signed-out session.

## Create a passkey

1. Open [Settings → Sign-in Security](/settings/authentication).
2. In **Passkeys**, select **Create passkey** or **Add passkey**.
3. Give it a recognizable name such as `MacBook Touch ID`.
4. Complete the browser or password-manager prompt with your fingerprint, face, or device PIN.

MEGA requires a discoverable credential and user verification. The registration ceremony expires after five minutes, and the browser prompt itself times out sooner, so restart the flow if either expires.

> [!NOTE]
> Passkey registration and management are browser-only. Access tokens and the CLI cannot create or export passkey credentials.

## Sign in with a passkey

Open the MEGA sign-in page and focus the account field. A supported browser can offer the saved passkey through autofill. Select it and complete local verification; MEGA then creates a normal browser session.

The credential is bound to the MEGA Web origin. A prompt on a look-alike domain cannot produce a valid MEGA assertion. Canceling the browser prompt leaves other sign-in methods available.

## Review and remove passkeys

The Passkeys section shows whether each credential is synced or device-bound, whether it reports a backup, when it was created, and when it was last used.

Remove a credential when a device is lost, a shared password-manager vault changes ownership, or the passkey is no longer needed. Deleting it in MEGA immediately prevents future MEGA authentication with that credential, but it may not remove the local entry from your device or password manager. Remove the local copy there as well.

## Recovery and account hygiene

- Register more than one passkey when you rely on a device-bound authenticator.
- Keep recovery-capable sign-in details and TOTP, when enabled, in a separate secure location.
- Review active browser sessions after losing a device; deleting a passkey does not revoke sessions that already exist.
- Revoke unfamiliar sessions and credentials before rotating other account secrets.
- Organization policies, trusted-network rules, and repository permissions still apply after passkey sign-in.

See [Account Security](/docs/hub/security) for session review, TOTP, tokens, CI identity, and incident response.
