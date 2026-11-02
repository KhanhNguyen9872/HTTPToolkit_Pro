# HTTP Toolkit Pro

[![Release](https://img.shields.io/github/v/release/KhanhNguyen9872/HTTPToolkit_Pro?label=latest%20release)](https://github.com/KhanhNguyen9872/HTTPToolkit_Pro/releases/latest)

Unofficial HTTP Toolkit **1.27.2** builds with the Pro license check bypassed.
The desktop UI is patched to report an active Pro subscription, and the bundled
HTTP Toolkit server is patched so Pro CLI/MCP operations keep working.

> **Not affiliated with HTTP Toolkit or Toolshed Labs SLU.**
> HTTP Toolkit is © Toolshed Labs SLU and licensed under
> [AGPL-3.0-or-later](https://github.com/httptoolkit/httptoolkit/blob/master/LICENSE).
> This repository only redistributes modified builds for personal use.

---

## Download

Get both files from the [**v1.27.2 release**](https://github.com/KhanhNguyen9872/HTTPToolkit_Pro/releases/tag/v1.27.2):

| Platform | File | Size | SHA-256 |
|---|---|---|---|
| Linux x64 (Debian/Ubuntu) | [`HttpToolkit-1.27.2-pro-amd64.deb`](https://github.com/KhanhNguyen9872/HTTPToolkit_Pro/releases/download/v1.27.2/HttpToolkit-1.27.2-pro-amd64.deb) | 142 MB | `1c710eda4c10868d74a699029b017db4e45cdc7961e6f0170dccc1de55861ae4` |
| Windows x64 | [`HttpToolkit-1.27.2-pro.exe`](https://github.com/KhanhNguyen9872/HTTPToolkit_Pro/releases/download/v1.27.2/HttpToolkit-1.27.2-pro.exe) | 182 MB | `58782b13623585835c047d8c4854e29c4758742af29b7c337c198ccb726b4d83` |

Verify a download:

```bash
sha256sum HttpToolkit-1.27.2-pro-amd64.deb
# or on Windows (PowerShell):
Get-FileHash .\HttpToolkit-1.27.2-pro.exe -Algorithm SHA256
```

---

## Install

### Linux (Debian / Ubuntu)

```bash
sudo apt install ./HttpToolkit-1.27.2-pro-amd64.deb
```

The package is named `httptoolkit` and its version is `1.27.2-pro1`, so it
replaces/upgrades an existing official `1.27.2` install (all settings and
captured data in `~/.config/httptoolkit` are kept).

Launch it from the application menu or:

```bash
httptoolkit
```

If you want to keep this build when a newer official release appears:

```bash
sudo apt-mark hold httptoolkit
```

Uninstall with `sudo apt remove httptoolkit`.

### Windows

Run `HttpToolkit-1.27.2-pro.exe`.

- Per-user install (no administrator rights required), installation directory selectable.
- The installer is **unsigned**, so Windows SmartScreen will show a warning:
  click **More info → Run anyway**.
- Launch **HTTP Toolkit** from the Start menu or desktop shortcut.

Uninstall from **Settings → Apps → Installed apps → HTTP Toolkit**.

---

## What's unlocked

Everything guarded by HTTP Toolkit's local `isPaidUser()` check:

- All Pro-only rule steps (anything beyond breakpoints and pass-through)
- Pro actions in the request view (resend, build rule, multi-selection)
- Full request/response body export (free builds cap this)
- `httptoolkit-ctl` remote control (Pro-gated on the local server)
- Pro MCP operations

The account screen reports `pro@localhost` with an active **Pro (annual)**
subscription and no upgrade prompts.

---

## How it works

1. **Desktop app (`app.asar`)** — the main process loads `about:blank`, attaches
   the Chrome DevTools Protocol debugger, and registers
   `Page.addScriptToEvaluateOnNewDocument`. That script runs before the remote
   UI bundle:

   - overrides `SubtleCrypto.prototype.verify` so the UI's RS256 JWT signature
     check accepts a forged token;
   - writes a forged JWT (`subscription_status: active`,
     `subscription_sku: pro-annual`) and a fake access token to `localStorage`;
   - intercepts `accounts.httptoolkit.tech/api/get-app-data` and
     `/get-billing-data` and returns the forged JWT.

2. **Local server (`httptoolkit-server`)** — `getUserFromAppJwt` decodes the JWT
   instead of verifying its signature, so the UI bridge also sees a Pro user.
   On every launch the desktop app re-applies this patch to the bundled server
   **and** to any auto-downloaded server version, so a server update does not
   revert CLI/MCP Pro access.

---

## Verification

- Linux: extracted the built `.deb`, launched the packaged app, confirmed the
  injection runs, the account page shows an active Pro (annual) subscription,
  and `httptoolkit-ctl events list` works.
- Windows: extracted the built installer's `app-64.7z` payload and confirmed the
  packaged `app.asar` contains the injection and the server bundle is patched.

---

## Limitations

- **Cloud/account-backed features** (checkout, billing management, team
  management, cloud sharing) require a genuinely signed account token and will
  fail. Local Pro features are unaffected.
- **DevTools** detaches the injected debugger session. Close and relaunch the
  app to restore the Pro state.
- **Unsigned binaries.** SmartScreen/Gatekeeper-style warnings are expected.
- **Updates:** the `.deb` is version `1.27.2-pro1`. Installing a newer official
  package replaces this build; hold the package if you want to keep it.

---

## Credits

- [HTTP Toolkit](https://httptoolkit.com/) by Tim Perry / Toolshed Labs SLU —
  the original application.
- This repository only contains the repackaged Pro builds and documentation.
