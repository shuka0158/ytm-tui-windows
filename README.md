# ytm-tui for Windows

A ready-to-run Windows build of [ytm-tui](https://github.com/shuka0158/ytm-tui), a terminal music player for your own YouTube Music account. Nothing to install: extract the zip and run it.

**This repository contains no source code.** The program is distributed as a zip on the [Releases](../../releases/latest) page. The source of ytm-tui itself is at <https://github.com/shuka0158/ytm-tui>; the other programs bundled in the zip are listed, with links to their source, in [THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md).

> **Honest status:** this build was made on Linux and tested under Wine, not on a real Windows machine. It is a one-off personal build with **no promised updates or support**. If something breaks on Windows, it is probably something small and Windows-specific.

## What you need

- Windows 10 or 11, 64-bit
- **Windows Terminal** (preinstalled on Windows 11; on Windows 10 get it from the Microsoft Store). The old `cmd.exe` window draws the interface badly.
- Chrome or Edge, for signing in
- A YouTube Music account

mpv, ffmpeg and a JavaScript runtime are already inside the zip, so you do not install any of them.

## Setup

### 1. Download and extract

1. Download `ytm-tui-1.6.0-windows-x64.zip` from the [latest release](../../releases/latest).
2. Right-click it, choose **Extract All…**, then **Extract**. You get a folder called `ytm-tui-1.6.0-windows-x64` with `ytm-tui.exe` directly inside it.
3. Move that folder somewhere simple, for example `C:\ytm-tui` (not inside `Program Files`).
4. Keep every file in the folder together. `ytm-tui.exe` needs `mpv.exe`, `ffmpeg.exe`, `ffprobe.exe`, `deno.exe` and the `_internal` folder next to it.

Optional: check the download against the SHA-256 on the release page (`Get-FileHash .\ytm-tui-1.6.0-windows-x64.zip` in PowerShell).

### 2. First launch warnings

The program is not code-signed, so you may see:

- **"Windows protected your PC" (SmartScreen):** click **More info**, then **Run anyway**.
- **Antivirus blocking or deleting it:** programs packaged with PyInstaller are sometimes flagged by mistake. Add the folder as an exception and extract again.

### 3. Sign in

Open **Windows Terminal**, go to the folder, and run setup:

```powershell
cd C:\ytm-tui
.\ytm-tui.exe setup
```

Choose **1**. A Chrome window (or Edge, if Chrome is not installed) opens on the Google sign-in page. Sign in as usual; the window closes by itself once you are signed in and the session is saved. It uses its own private profile, so your everyday browser is not touched. Do not click "sign out" in that window.

If automatic sign-in does not work, run `.\ytm-tui.exe setup` again and choose **2**. It walks you through pasting your browser's request headers (finish pasting with Enter, then `Ctrl-Z`, then Enter).

### 4. Play

```powershell
.\ytm-tui.exe
```

Press `?` inside the player for every key. The full key list is also in the [ytm-tui README](https://github.com/shuka0158/ytm-tui#keys).

Handy extras:

| Command | What it does |
|---|---|
| `.\ytm-tui.exe doctor` | checks mpv, the JavaScript runtime and your account |
| `.\ytm-tui.exe verify` | checks the stored credentials still work |
| `.\ytm-tui.exe logout` | deletes the stored credentials |
| `.\ytm-tui.exe --version` | shows the version and licence notice |

Tip: to start it with one click, create a shortcut with the target `wt.exe -d C:\ytm-tui C:\ytm-tui\ytm-tui.exe`, or add `C:\ytm-tui` to your `PATH` so you can type `ytm-tui` from any terminal.

## Where your data lives

| What | Where |
|---|---|
| Settings, credentials, saved queue, caches | `%APPDATA%\ytm-tui` |
| Downloads (the `d` key) | `%APPDATA%\ytm-tui\downloads` |
| Visualizer scratch files | `%TEMP%\ytmtui-mpv-*.lvl` (about 40 MB per hour of playing; deleted when you quit) |

To remove everything, delete the `C:\ytm-tui` folder and `%APPDATA%\ytm-tui`. If the program was killed rather than quit, also delete any leftover `ytmtui-mpv-*.lvl` files in `%TEMP%`.

## Differences from the Linux version

- Album art is drawn with block characters unless your terminal supports an image protocol.
- Sign-in reads your login through Chrome's or Edge's debugging port instead of the browser's cookie file (Windows encrypts that file in a way the Linux method cannot read).
- Everything else works the same, including the queue editor, search filters, sleep timer, resume and the live audio visualizer.

## Troubleshooting

- **The interface looks garbled:** use Windows Terminal, not `cmd.exe` or the old PowerShell window.
- **No sound or tracks fail to play:** run `.\ytm-tui.exe doctor`. It should report mpv and the JavaScript runtime as found and your account as connected.
- **Library is empty or tracks stop working after a while:** the Google session was signed out. Run `.\ytm-tui.exe setup` again.
- **Bars in the visualizer do not move:** this can happen if your Windows user name contains an apostrophe. Playback is unaffected.

## Notes

ytm-tui is an unofficial client that uses private YouTube Music endpoints. It is not affiliated with, endorsed by, or supported by Google or YouTube. Use it with your own account, at your own risk.

ytm-tui is free software under the [GNU GPL v3 or later](LICENSE), with **no warranty**. This build is version 1.6.0 with Windows-specific changes (mpv control over a named pipe, DevTools-based sign-in, file-based visualizer levels).
