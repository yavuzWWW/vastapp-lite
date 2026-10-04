# VastApp Lite

**VastApp Lite by Vast Hosting** is a lightweight Windows desktop wrapper for WhatsApp Web, focused on low resource usage, fast startup, dark mode, and a minimal desktop experience.

> Unofficial project. VastApp Lite is not affiliated with, endorsed by, or sponsored by WhatsApp LLC or Meta Platforms, Inc. WhatsApp is a trademark of its respective owner.

## Goals

- Keep the desktop wrapper as small and simple as possible.
- Avoid unnecessary application frameworks and backend code.
- Prefer GPU-accelerated rendering for smoother scrolling and lower CPU usage.
- Start in dark mode and avoid a bright white startup flash.
- Keep login/session data local to each installation.
- Disable development/debugging features in release builds.

## Current implementation

The current release uses a stripped PHP Desktop / CEF runtime, but **does not use PHP**. The app serves one tiny local HTML bootstrap page and immediately opens `https://web.whatsapp.com/`.

The repository contains the project-owned source/configuration only. Large third-party CEF runtime binaries are intentionally not committed because GitHub's normal repository file limit is smaller than the bundled `libcef.dll`.

### Optimizations in this version

- PHP runtime removed.
- Clean profile on first launch; no development WhatsApp session is distributed.
- Chromium dark color preference forced.
- Dark `#111b21` bootstrap page to avoid a white startup flash.
- GPU acceleration enabled by default.
- Remote debugging disabled.
- Verbose Chromium disk logging disabled.
- Browser and media caches capped.
- Only English, Dutch and Turkish Chromium locale packs retained in packaged builds.
- Single-instance mode enabled.
- DevTools and source-view context-menu entries disabled.
- Downloads, drag/drop, microphone, camera and WebRTC capability preserved.

## Project files

```text
.
├── settings.json       # Runtime/window/browser configuration
├── www/
│   └── main.html       # Dark bootstrap + WhatsApp Web redirect
├── BUILD.md            # How to assemble the Windows package
└── .gitignore          # Excludes sessions, caches and third-party runtime files
```

## First run

After assembling a release package:

1. Start `run.exe`.
2. Link WhatsApp using the QR code.
3. A local `webcache` directory will be created for that installation.

Never distribute the generated `webcache` directory. It can contain account/session data.

## Dark mode

A fresh profile advertises `prefers-color-scheme: dark` to WhatsApp Web. If an old profile is copied into the app, WhatsApp's previously saved theme can override that preference. In that case set **WhatsApp → Settings → Chats → Theme → Dark** once.

## GPU compatibility fallback

GPU rendering is intentionally enabled because software rendering caused noticeably higher CPU usage and worse responsiveness on the original build.

If a specific machine has a broken graphics driver and shows a black or blank window, add this switch under `chrome.command_line_switches` in `settings.json`:

```json
"disable-gpu": ""
```

Use that only as a compatibility fallback.

## Security / privacy

Do not commit or publish:

- `webcache/`
- cookies
- IndexedDB data
- local/session storage
- login databases
- debug logs containing browsing/session information

The `.gitignore` in this repository excludes those paths.

## Future direction

A future version can replace the bundled CEF runtime with Microsoft WebView2. That would remove the large bundled Chromium runtime and allow the browser engine to stay updated through the installed WebView2 runtime.
