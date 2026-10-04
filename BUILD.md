# Building VastApp Lite

This repository intentionally stores only the VastApp Lite source/configuration. The third-party PHP Desktop / CEF runtime is not committed because its Chromium binaries are large and `libcef.dll` exceeds GitHub's normal single-file repository limit.

## Current runtime base

The current VastApp Lite package is based on the Windows PHP Desktop Chrome / CEF runtime used by the original project. PHP itself is not required by VastApp Lite.

## Assemble a release

1. Obtain a clean Windows PHP Desktop Chrome runtime from the upstream PHP Desktop project.
2. Extract the runtime into an empty release directory.
3. Delete the bundled `php/` directory. VastApp Lite does not execute PHP.
4. Replace the runtime `settings.json` with this repository's `settings.json`.
5. Replace the runtime `www/` directory with this repository's `www/` directory.
6. Remove every generated profile/cache directory, especially `webcache/`.
7. Keep only the Chromium locale packs you want to ship. The current Vast build retains:
   - `en-US.pak`
   - `en-GB.pak`
   - `nl.pak`
   - `tr.pak`
8. Ensure no `debug.log`, cookies, IndexedDB, Local Storage, Session Storage, History, Login Data, or other browser-profile files are present.
9. Start `run.exe` once on a test machine and verify:
   - WhatsApp Web opens.
   - Startup remains dark with no large white flash.
   - GPU acceleration works normally.
   - QR linking works.
   - Login persists after restart.
   - Notifications work.
   - Microphone/camera work when permission is granted.
   - Minimize-to-tray works.
10. Delete the test-generated `webcache/` before packaging the public build.

## Release layout

A packaged build should look approximately like this:

```text
VastApp-Lite/
├── run.exe
├── settings.json
├── www/
│   └── main.html
├── locales/
│   ├── en-US.pak
│   ├── en-GB.pak
│   ├── nl.pak
│   └── tr.pak
└── [required CEF runtime files]
```

There must be **no `webcache/` directory** in a public release.

## GPU fallback

The optimized configuration intentionally does not set `disable-gpu`.

If a machine has a graphics-driver issue that produces a blank or black window, add this entry under `chrome.command_line_switches`:

```json
"disable-gpu": ""
```

This should remain a compatibility fallback only because software rendering can increase CPU usage and reduce responsiveness.

## Current architectural limitation

The present version still depends on a bundled CEF/Chromium runtime. A WebView2 rewrite is the preferred long-term direction because it would substantially reduce the package size and avoid shipping a fixed browser engine with the application.
