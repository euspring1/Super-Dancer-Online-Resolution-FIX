# Super Dancer Online Resolution FIX

**A resolution fix for SDO-X (Super Dance Online).**

> **About the game:** SDO-X (Super Dance Online) is an online rhythm game in the
> style of DDR (Dance Dance Revolution): you hit the arrows in time with the music, playing with and against
> other players in online rooms, with customizable avatars, a city lobby and social features.
> Official site: **https://sdo.cibmall.net/home/**

The original client is built for **800x600**: in fullscreen it switches your monitor to 800x600, and the
picture ends up small, blurry and with black bars. This fix makes the game **actually render at your
monitor's resolution** (1080p, 1440p/2K, ultrawide...) while your monitor stays at its **native resolution and
refresh rate** (e.g. 1920x1080 @ 240 Hz). No game files are modified: it is a new launcher (`SDOX.exe`) plus a
DLL (`sdox.dll`) that is only loaded while the game is running.

> **No game data is included.** This project only distributes its own launcher and DLL — you need your own
> SDO-X client installed.

| Original client | With the fix |
|---|---|
| Monitor switched to 800x600 | Monitor keeps its native resolution and refresh rate |
| 800x600 picture upscaled (blurry) | Real rendering at your monitor's resolution (or any from 800x600 up to 2K) |
| 16-bit color | 32-bit color |
| No anti-aliasing | Up to 8x MSAA + 16x anisotropic filtering |
| No FPS control | Configurable FPS limit (240 by default) |

## Installation

> **Note:** your game must be **up to date** before using the fix. Open the **original SDO-X launcher** first,
> let it download all patches, and only then start the game through `SDOX.exe`. (Later on, the
> **Update (original launcher)** button opens the official launcher for new patches.)

1. Download the latest **`SuperDancerOnline-ResolutionFIX-vX.Y.Z.zip`** from the
   [Releases](../../releases) page.
2. **Extract the zip into your game folder.** It is usually called **`Super Dance Online`**, but the name
   may be different on your PC — what matters is that it is the game's own folder, the one that contains
   `SDO_English.exe`. The zip only contains the two files the fix needs: `SDOX.exe` and `sdox.dll`.
3. Right-click **`SDOX.exe` → Run as administrator**.
   (The game requires administrator rights; the launcher also asks for them on its own.)
4. Click **Play**. The defaults are already the recommended setup: fullscreen at your monitor's resolution
   (e.g. 1920x1080), filling the screen. Only change the options if you want something different.

That's it. From now on, always start the game through `SDOX.exe`.

> Requirement: the game folder (`Super Dance Online`) must contain **dgVoodoo** (`D3D9.dll` + `dgVoodoo.conf`), which already
> ships with the SDO-X client. The fix uses dgVoodoo to render at high resolution.

### Recommended settings (defaults)

| Option | Value |
|---|---|
| Display mode | Native fullscreen (recommended) |
| Aspect ratio | Stretch to full screen (or Keep 4:3, no distortion) |
| Render resolution | Your monitor's (e.g. 1920x1080) |
| Anti-aliasing | 8x MSAA |
| Texture filtering | Anisotropic 16x |
| FPS limit | 240 (or your monitor's refresh rate) |

The launcher is available in **English**, **Português (Brasil)** and **简体中文** ("Launcher language").

### Render resolution

The default is **your monitor's resolution** — the game is sharp, pixel for pixel. You can pick another one:

- **Lower** (down to 800x600): lighter for weaker PCs, softer/blurrier picture.
- **Higher than your monitor** (up to 2560x1440, marked as *supersampling*): the game renders bigger and is
  scaled down to the screen — even smoother edges, at the cost of more GPU.

The list depends on the aspect ratio: "Keep 4:3" only shows 4:3 resolutions (800x600 … 1920x1440);
"Stretch" shows widescreen ones (1280x720 … 2560x1440). This option only applies to **Native fullscreen**.

### Widescreen and ultrawide monitors

**Native fullscreen** works on any monitor — the output is always the monitor's real resolution.
The game itself, however, is still drawn in 4:3:

- **Keep 4:3**: correct picture with side bars. Works the same on 16:9, 16:10 or 21:9.
- **Stretch to full screen**: fills the screen but widens everything — on 16:9 characters are ~33% wider and
  the note lane looks wider/closer; on 21:9 ultrawide, ~75%.

**True native 16:9** (fixed camera and UI, no stretching and no bars) is the next step of this project.

### Updating the game

Use the **Update (original launcher)** button — it opens the official launcher to download patches.
Afterwards, start the game through `SDOX.exe` again.

### Uninstalling

1. Delete `SDOX.exe`, `sdox.dll`, `sdox.ini` and (if present) `sdox.log` from the game folder.
2. On first run the launcher backed up the files it changes. To restore the originals, rename them removing
   the `.sdox.bak` ending:
   - `config.dat.sdox.bak` → `config.dat`
   - `SDO_Launcher.CFG.sdox.bak` → `SDO_Launcher.CFG`
   - `dgVoodoo.conf.sdox.bak` → `dgVoodoo.conf`

### Diagnostic log (to understand a problem)

The log is **off** by default. To turn it on:

1. Open `SDOX.exe` once and click **Save** (this creates `sdox.ini` in the game folder).
2. Open `sdox.ini` in Notepad and, in the `[Debug]` section, set:
   ```ini
   [Debug]
   Log=1
   ```
3. Play normally and close the game. **`sdox.log`** shows up in the game folder (it is recreated every time
   the game starts).

What the log tells you:

| Line | Meaning |
|---|---|
| `game build recognized: yes` | the client is compatible with native fullscreen |
| `CreateDevice (SDOX): ... windowed=0 ... fmt=X8R8G8B8` | the game runs in fullscreen with 32-bit color |
| `FPS 455 \| 1% low 364 FPS (p99 2.75 ms) ...` (every 2 s) | average FPS, FPS of the worst 1% of frames, and the worst frame time — a low 1% low means stutter |
| `Present avg / max` | time to hand a frame to the screen (should stay below ~1 ms) |
| `focus: ... DI GetDeviceState ok=... fail=...` | mouse/keyboard reads; a high `fail` count means input is not reaching the game |
| `Reset -> 0x00000000` | the game came back from Alt+Tab correctly |
| `Reset -> 0x88760868 (device lost...)` | the game is still waiting to come back from Alt+Tab |

With `Log=1` the timing profile (`Sleep` / `timeGetTime` / `GetTickCount`) is included as well. To log only
the basics, add `Profile=0`. Set `Log=0` again when you are done — the log writes to disk every 2 seconds.

## Known issues

- **Fonts are still pixelated**: the game renders text as 800x600 images; this has not been improved yet.
- **"Stretch to full screen" distorts the picture**: wider characters and a note lane that looks closer,
  because the game's camera and UI are 4:3. Use "Keep 4:3" if you prefer no distortion
  (see [Widescreen and ultrawide monitors](#widescreen-and-ultrawide-monitors)).
- **Mouse feels more sensitive**: the game cursor works in 800x600 space and is scaled up to the screen;
  lower your mouse DPI/sensitivity if it bothers you.
- Built and tested on the SDO-X **`SDO_English.exe`** client. On other clients, native fullscreen turns
  itself off (the launcher keeps working in the original mode).

## What the launcher changes

`SDOX.exe` saves your options to `config.dat`, `SDO_Launcher.CFG` and `dgVoodoo.conf` in the game folder
(the originals are backed up as `*.sdox.bak` on first run — see [Uninstalling](#uninstalling)) and starts
the game with `sdox.dll` loaded. Nothing else on your PC is changed.

## License

**This is not open-source software.** Copyright © 2026 euspring1. All rights reserved.
You may download and use the released files for personal use. Redistribution, modification, decompiling or
reverse engineering of `SDOX.exe` / `sdox.dll` is not allowed without permission. See [`LICENSE`](LICENSE).
