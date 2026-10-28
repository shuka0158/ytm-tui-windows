# Third-party components

The release zip bundles the programs and libraries below. Each is distributed under its own licence, and the source for each is available from the links given. This repository holds no source code; these links are the places to get it.

## Programs shipped next to `ytm-tui.exe`

| Component | Version | Licence | Source |
|---|---|---|---|
| ytm-tui | 1.6.0 | GPL-3.0-or-later | <https://github.com/shuka0158/ytm-tui> (tag `v1.6.0`) |
| mpv | 0.41.0-1227-ge471496c5 (build 2026-10-21) | GPL-2.0-or-later (this build) | <https://github.com/mpv-player/mpv> · build recipe and binaries: <https://github.com/shinchiro/mpv-winbuild-cmake/releases/tag/20261021> |
| FFmpeg (`ffmpeg.exe`, `ffprobe.exe`) | N-128122-g215351c1c3 (build 2026-10-27, LGPL variant) | LGPL-2.1-or-later | <https://github.com/FFmpeg/FFmpeg> · builds: <https://github.com/BtbN/FFmpeg-Builds> |
| Deno (`deno.exe`) | 2.9.8 | MIT | <https://github.com/denoland/deno> |

## Python runtime and libraries (inside `_internal`)

| Component | Version | Licence |
|---|---|---|
| Python | 3.12.10 | PSF-2.0 |
| PyInstaller (bootloader) | 6.22.3 | GPL-2.0 with bootloader exception |
| Textual | 8.2.8 | MIT |
| textual-image | 0.13.2 | LGPL-3.0-or-later |
| ytmusicapi | 1.12.2 | MIT |
| yt-dlp | 2026.8.19 | Unlicense |
| Pillow | 12.3.0 | MIT-CMU |
| Requests | 2.34.2 | Apache-2.0 |
| Rich and other dependencies pulled in by the above | — | see each package |

## About the licences

ytm-tui is GPL-3.0-or-later and its full text is in [LICENSE](LICENSE). mpv is GPL, which is why its source is linked above rather than omitted. FFmpeg is the LGPL build, and the other libraries are permissively licensed. Nothing here is modified except ytm-tui itself.
