<h1 align="center">pziOS</h1>

<p align="center"><b>Project Zomboid Build 42 on your iPhone and iPad.</b></p>

<p align="center">
  <a href="https://github.com/onlyknt/pziOS/releases"><img src="https://img.shields.io/github/downloads/onlyknt/pziOS/total?label=Downloads&style=for-the-badge&color=5865F2&labelColor=111111" alt="Total downloads (live)"></a>
  &nbsp;
  <a href="https://github.com/onlyknt/pziOS/releases"><img src="https://img.shields.io/github/v/release/onlyknt/pziOS?include_prereleases&label=Release&style=for-the-badge&color=brightgreen&labelColor=111111" alt="Latest release"></a>
  &nbsp;
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-GPL3-yellow?style=for-the-badge&labelColor=111111" alt="GPL3 licence"></a>
  &nbsp;
  <img src="https://img.shields.io/badge/iOS-17%2B-blue?style=for-the-badge&labelColor=111111" alt="iOS 26 or later">
</p>

pziOS runs Project Zomboid (Build 42) on an iPhone or iPad. It is a launcher
plus the runtime the game needs: a Java virtual machine built for iOS, an OpenGL-to-Metal graphics
stack, and audio and input layers. It runs **your own copy** of the game; no game assets are
included or downloaded.

> [!NOTE]
> **Free and non-commercial.** pziOS is not for sale, not for profit, and not for distribution on
> any app store. You need to own Project Zomboid. Project Zomboid is a work of The Indie Stone;
> this project is not affiliated with or endorsed by them.

> [!WARNING]
> **Beta (0.9.0).** Tested on an iPhone 16 Pro Max (A18 Pro, iOS 27) and an iPad Pro 11-inch
> 2nd generation (A12Z, iPadOS 26.6.2). Everything else is untested. Expect rough edges.

## How it works

| Component | What it does |
|---|---|
| **pziOS launcher** (SwiftUI) | Imports your game, sets options, starts everything. |
| **JVM** **[OpenJDK 25](https://github.com/openjdk/jdk)** | Runs the game's Java code natively on iOS. |
| **JIT** ([StikJIT](https://github.com/StikDebug/StikJIT)) | Attaches a debugger so iOS permits JIT. |
| **[MetalANGLE](https://github.com/kakashidinho/metalangle)** | Translates OpenGL ES calls into Apple's Metal. |
| **[gl4es](https://github.com/ptitSeb/gl4es)** | Turns desktop OpenGL 2 into OpenGL ES. |
| **GL shim** (`native/glshim`) | Routes LWJGL's GL calls and fixes shaders. |
| **[ASTC encoder](https://github.com/ARM-software/astc-encoder)** | Compresses textures on first run, then caches. |
| **GLFW layer** (`native/glfw-ios`) | Provides the window, touch, controller and keyboard input. |
| **Audio shim** (`native/fmodhal`) | Maps macOS audio calls onto iOS audio. |
| **EGL proxy** (`native/eglproxy`) | Bridges gl4es and MetalANGLE's EGL calls. |
| **Library converter** (`scripts/`) | Converts the game's Mac libraries for iOS. |

iOS only lets an app run freshly compiled code while a debugger is attached, which is why the JIT
step exists.

## Features

- Solo play, mods, saves, and direct-connect multiplayer.
- **Controls**: editable touch layout with an aim stick, game controllers, keyboard and mouse. On-screen controls hide when a controller or keyboard and mouse are connected.
- **Display**: render resolution presets, UI margins sized to your screen (Auto, or set your own),
  up to 120 Hz, iPad layouts.
- **Performance options**: memory limit tiers chosen from what iOS lets the app use, garbage
  collector choices (Parallel, pzGC, SGC, pzSGC), JIT compiler choices, ASTC texture compression
  with a disk cache.
- **Setup guide** that imports the game and walks through JIT.

## Supported Devices

- **iPhone 12 Pro/iPhone 13 Pro or later.**
- **iPad Pro A12z or later.**
- **iPad Air M1 or later.**
- **iPad Mini A17 Pro or later.**
- **iPad A16 or later.** 
- Devices outside these bounds may run with **Increased Memory Limit** but results may vary.

## Requirements

- Supported iPhone or iPad on **iOS or iPadOS 17 or later** with **Developer Mode** turned on
  (Settings, Privacy & Security, Developer Mode).
- **Project Zomboid Build 42.21** for Mac. You need the game; pziOS only needs a copy of its files.
- About **5 GB** of free space for the game, plus about 250 MB for the app.
- **JIT**, set up in the app: either the built-in method (a pairing file for your device and a
  loopback VPN such as LocalDevVPN) or the included script in StikDebug,this can also be acquired by idevice\_pair.
- An **Apple ID** to sideload the app. A free account works, but its signing expires after 7 days,
  so the app has to be re-signed weekly. Your game files and saves are kept.
- A sideloading tool: Sideloadly, AltStore, Impactor or Xcode.

## Installing

1. Download **`pziOS-<version>-b42.21.ipa`** from the [latest release](https://github.com/OWNER/REPO/releases/latest)
   and sideload it with your own Apple ID.
2. On your Mac, **prepare the game files** (below), then AirDrop the zip to your device.
3. Open pziOS and follow the **Setup guide**: choose the zip, then set up JIT.
4. Press **Play**. The first start takes a while: the game loads and textures are compressed and cached.

### Prepare the game files

Find **Project Zomboid** on your Mac (Steam: Show in Finder), right-click it, choose
**Show Package Contents**, then right-click the **Contents** folder and choose **Compress**.
AirDrop the resulting `Contents.zip` to your iPhone or iPad. Compressing the whole app or the plain
game folder works too; pziOS searches the zip for `projectzomboid.jar`.

<p align="center">
  <img src="docs/assets/compress-for-airdrop.gif" alt="Compressing the Contents folder of Project Zomboid for AirDrop" width="720">
</p>

In pziOS tap **Choose zip or folder…** and pick the zip. Only the files the game needs are copied;
Mac executables and libraries are ignored.

### No Mac? Get the Mac version of the game with SteamCMD

pziOS needs the **macOS** copy of Project Zomboid: the Windows files do not match, and the game's
native libraries are matched to the Mac build. On Windows or Linux you can download the Mac depot
with [SteamCMD](https://developer.valvesoftware.com/wiki/SteamCMD), using a Steam account that owns the game:

```
steamcmd +@sSteamCmdForcePlatformType macos +force_install_dir ./pz-mac +login <your steam user> +app_update 108600 validate +quit
```

Then zip the downloaded `Project Zomboid.app` folder (or its `Contents` folder) and move the zip to
your device.

### Manual JIT with StikDebug

If you do not use the built-in JIT, set **JIT Mode** to **Manual** in pziOS and use the script included in release.

1. Add the script to StikDebug's scripts.
2. Launch using StikDebug
3. In pziOS press **Play**.
4. The script attaches, hands the game its executable memory when it asks, and detaches.


## If something goes wrong

- **Black screen after Play**: make sure you installed the IPA that includes the game's libraries,
  and that JIT is on.
- **The game does not start or the app closes**: Settings, **Developer Tools**, Tools tab, then
  reset the launch arguments and try again.
- **Import fails**: the source has to be fully downloaded (not an iCloud placeholder) and there
  must be enough free space. You can cancel an import and start it again; finished files are kept.

**Bug reports**: open an issue with your device name and iOS version, the pziOS version shown at the bottom of the launcher, what you did, and `jvm.log` (Files app, your device, pziOS folder). Turn on** Log Memory Use** (Tools tab) first for memory problems.

## Building from source

The source tree has no binaries. [`docs/BUILDING.md`](docs/BUILDING.md) lists what to build and in
which order (the JRE, gl4es, MetalANGLE, StikJIT, our native libraries, then the app).
[`docs/DISTRIBUTION.md`](docs/DISTRIBUTION.md) describes the IPAs and what must stay out of them.

```sh
native/build-natives.sh      # our native libraries
scripts/make-ipa.sh          # an IPA without the game's libraries
scripts/build-release.sh "/path/to/Project Zomboid.app"   # both IPAs
```

**Your own identity**: bundle id, team id and display name come from
`HostApp/Config/Signing.xcconfig` (put your own values in `HostApp/Config/Signing.local.xcconfig`,
which git ignores). For an IPA, edit `release.config` in the repository root.

### Repository layout

| Path | Contents |
|---|---|
| `HostApp/` | The iOS app: SwiftUI launcher and settings, game importer, controls editor, setup guide, built-in JIT helper (`xcodegen` project in `project.yml`) |
| `native/glfw-ios/` | A GLFW 3.4 implementation for iOS (Metal layer view, touch, controller, keyboard and mouse input, on-screen controls) |
| `native/glshim/` | The OpenGL entry points LWJGL loads: routes to gl4es, ES-level shader fixes, ASTC texture compression with a disk cache |
| `native/eglproxy/` | A tiny library between gl4es and MetalANGLE; re-exports MetalANGLE and logs who destroys the display, context or surface |
| `native/fmodhal/` | Lets the game's macOS FMOD libraries run on iOS |
| `native/astc/` | C wrapper around ARM's astc-encoder |
| `patches/` | Patches for the iOS OpenJDK build |
| `scripts/` | Library conversion, IPA building and signing, the manual JIT script |
| `docs/` | Build, distribution, game file and JIT guides |
| `licenses/` | Licence texts of the components we ship |

## Licensing and credits

The pziOS source code is under the [GPL-3 License](LICENSE). Third-party components keep their own
licences: they are listed in [`THIRD_PARTY.md`](THIRD_PARTY.md), their texts are in `licenses/`, and
the same texts are inside the app (`Licenses/`). The bundled JRE is OpenJDK (GPLv2 with the
Classpath Exception); each release attaches its complete source change set.

Thanks to the OpenJDK project, MetalANGLE, gl4es, ARM's astc-encoder, StikJIT,
Baconium's iOS JVM work, and to the LWJGL, FMOD and Project Zomboid teams whose libraries the game
runs on.
Thanks to **Claude** as a major contributor to the project.
**Project Zomboid is a trademark of The Indie Stone & not affiliated with pziOS.**
