# ytm-tui for Windows

A ready-to-run Windows build of [ytm-tui](https://github.com/shuka0158/ytm-tui), a terminal music player for your own YouTube Music account. Nothing to install: extract the zip and run it.

**This repository contains no source code.** The program is distributed as a zip on the [Releases](../../releases/latest) page. The source of ytm-tui itself is at <https://github.com/shuka0158/ytm-tui>; the other programs bundled in the zip are listed, with links to their source, in [THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md).

> **Honest status:** this build was made on Linux and tested under Wine, not on a real Windows machine. It is a one-off personal build with **no promised updates or support**. If something breaks on Windows, it is probably something small and Windows-specific.

> **Using Claude Code (or another agent)?** Point it at this page and ask it to install ytm-tui. The exact commands are in [Automated install](#automated-install-for-claude-code-or-a-script); only the Google sign-in needs you.

## What you need

- Windows 10 or 11, 64-bit
- **Windows Terminal** (preinstalled on Windows 11; on Windows 10 get it from the Microsoft Store). The old `cmd.exe` window draws the interface badly.
- Chrome or Edge, for signing in
- A YouTube Music account

mpv, ffmpeg and a JavaScript runtime are already inside the zip, so you do not install any of them.

## Setup

### 1. Download and extract

1. Download `ytm-tui-1.6.2-windows-x64.zip` from the [latest release](../../releases/latest).
2. Right-click it, choose **Extract All…**, then **Extract**. You get a folder called `ytm-tui-1.6.2-windows-x64` with `ytm-tui.exe` directly inside it.
3. Move that folder somewhere simple, for example `C:\ytm-tui` (not inside `Program Files`).
4. Keep every file in the folder together. `ytm-tui.exe` needs `mpv.exe`, `ffmpeg.exe`, `ffprobe.exe`, `deno.exe` and the `_internal` folder next to it.

Optional: check the download against the SHA-256 on the release page (`Get-FileHash .\ytm-tui-1.6.2-windows-x64.zip` in PowerShell).

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

## Automated install (for Claude Code or a script)

Everything except the Google sign-in can be done by an agent. Run these in **PowerShell** on the Windows machine. They install per user, need no administrator rights, and touch nothing outside the install folder, your user `PATH` and `%APPDATA%\ytm-tui`.

### 1. Download, verify, extract, add to PATH

```powershell
$ErrorActionPreference = "Stop"
$ProgressPreference    = "SilentlyContinue"          # makes Invoke-WebRequest much faster
$version  = "1.6.2"
$name     = "ytm-tui-$version-windows-x64.zip"
$url      = "https://github.com/shuka0158/ytm-tui-windows/releases/download/v$version/$name"
$sha256   = "c6f36e6aa6bc75640068f1a8bba840142e83cbb4b422e26de5314e7cebafeafc"
$dir      = Join-Path $env:LOCALAPPDATA "Programs\ytm-tui"
$zip      = Join-Path $env:TEMP $name

Invoke-WebRequest -Uri $url -OutFile $zip
$actual = (Get-FileHash -Algorithm SHA256 -Path $zip).Hash.ToLower()
if ($actual -ne $sha256) { throw "Checksum mismatch: expected $sha256, got $actual" }

New-Item -ItemType Directory -Force -Path $dir | Out-Null
tar -xf $zip -C $dir                                  # tar.exe ships with Windows 10 1803+ and reads zips
Get-ChildItem -Path $dir -Recurse -File | Unblock-File   # clears the "downloaded from the internet" mark
Remove-Item $zip

$userPath = [Environment]::GetEnvironmentVariable("Path", "User")
if (($userPath -split ";") -notcontains $dir) {
    [Environment]::SetEnvironmentVariable("Path", "$userPath;$dir", "User")
}
$env:Path += ";$dir"                                  # also usable in this session

& "$dir\ytm-tui.exe" --version
& "$dir\ytm-tui.exe" doctor
```

If the checksum differs, stop: the file was corrupted or is not the release described here. Do not run it.

### 2. What success looks like

`--version` prints `ytm-tui 1.6.2` and the licence notice. `doctor` should print:

```
mpv                 found
JavaScript runtime  found (deno)
account             MISSING - run `ytm setup`
gated-track session none - tracks needing sign-in will not play
```

`account MISSING` is expected at this point. Everything else should say `found`. If `mpv` or the JavaScript runtime say otherwise, the extraction is incomplete: `mpv.exe`, `ffmpeg.exe`, `ffprobe.exe`, `deno.exe` and the `_internal` folder must all sit next to `ytm-tui.exe` (delete the install folder and repeat step 1).

### 3. Sign in (needs the person)

An agent cannot do this step: it needs the user's Google login in a browser window. Ask the user to run, in their own terminal:

```powershell
ytm-tui setup
```

and choose **1**. In Claude Code the user can type `! ytm-tui setup` at the prompt to run it in the session. Chrome (or Edge) opens; they sign in; the window closes itself.

Alternative if they already have ytm-tui working on another machine: copy that machine's `config/browser.json` to `%APPDATA%\ytm-tui\config\browser.json` (create the folders first), then continue.

### 4. Check it worked

```powershell
ytm-tui verify
```

Success prints `Connected. N playlists visible:` followed by playlist names. Then tell the user to start the player in Windows Terminal with `ytm-tui` (press `?` inside for the keys).

### Notes for the agent

- **Do not run `ytm-tui` with no arguments from an agent shell.** It is a full-screen interface that needs a real terminal. Only run the non-interactive commands: `--version`, `doctor`, `verify`, `logout`.
- `ytm-tui setup` is interactive; leave it to the user.
- If Windows Defender, SmartScreen or another antivirus blocks or deletes the files, **do not disable the security software.** Tell the user, and ask them to add `%LOCALAPPDATA%\Programs\ytm-tui` as an exclusion themselves.
- To uninstall: delete `%LOCALAPPDATA%\Programs\ytm-tui`, remove that folder from the user `PATH`, and delete `%APPDATA%\ytm-tui` (this removes the saved login and settings).
- To upgrade to a later release, repeat step 1 with the new `$version` and `$sha256` from the release page. Settings and login in `%APPDATA%\ytm-tui` are kept.

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

ytm-tui is free software under the [GNU GPL v3 or later](LICENSE), with **no warranty**. This build is version 1.6.2 with Windows-specific changes (mpv control over a named pipe, DevTools-based sign-in, file-based visualizer levels).
