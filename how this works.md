# How WISO Works

**WISO** (Windows ISO Customizer) is a desktop application that modifies Windows installation images so you can create customized ISOs or bootable USB drives. All processing happens locally — no data is sent to any server.

---

## Table of Contents

1. [Overview](#1-overview)
2. [User Interface & Flow](#2-user-interface--flow)
3. [All Options & What They Do](#3-all-options--what-they-do)
4. [Presets](#4-presets)
5. [Build Pipeline (8 Steps)](#5-build-pipeline-8-steps)
6. [Scripts & Files Injected](#6-scripts--files-injected)
7. [First-Logon Behavior](#7-first-logon-behavior)
8. [Bloatware Removal](#8-bloatware-removal)
9. [App Installation (Baked vs Offline)](#9-app-installation-baked-vs-offline)
10. [USB & Output](#10-usb--output)
11. [Dependencies & Installer](#11-dependencies--installer)
12. [Architecture](#12-architecture)
13. [Project Structure](#13-project-structure)
14. [Privacy & Security](#14-privacy--security)
15. [Build & Run](#15-build--run)
16. [Error Hints](#16-error-hints)

---

## 1. Overview

WISO takes a Windows ISO (or extracted folder) and:

1. **Extracts** the ISO to a temporary directory (or uses an existing folder)
2. **Modifies** the image: bypasses hardware checks, skips setup wizard, applies privacy/performance tweaks, removes bloatware
3. **Injects** autounattend.xml and PowerShell scripts so Windows installs with your settings automatically
4. **Rebuilds** the ISO using portable oscdimg (or outputs a modified folder)

---

## 2. User Interface & Flow

### Startup

- **Dependency Check** — Shown first. Checks for portable oscdimg and winget. Optional; you can skip or install from here.
- **Dependencies** link in footer — Reopens the dependency check anytime.

### Main Steps

| Step | Name | Purpose |
|------|------|---------|
| 1 | **Source** | Select Windows ISO or extracted folder. Pick Windows edition (Home, Pro, etc.) from the WIM/ESD. |
| 2 | **Customize** | Configure all options: presets, installation, privacy, performance, bloatware, apps. Export/import config as JSON. |
| 3 | **Build** | Start the build. Live logs, cancel support. Choose output path (default: Downloads\Windows-Custom.iso). |
| 4 | **Output** | Open folder, open in Rufus, copy to USB, or create bootable USB. |

### Config Export / Import

- **Export config** — Saves current options as JSON (version, options).
- **Import config** — Loads options from JSON. Merges with defaults.

### Additional UI

- **ISO to USB (any ISO)** — Collapsible card to write any ISO to USB (Linux, other Windows, etc.) without building.
- **Admin warning** — If not running as admin, shows a warning and "Restart as admin" button.
- **Info overlays** — How it works, Terms, Privacy Policy.

---

## 3. All Options & What They Do

### Installation

| Option | Effect |
|--------|--------|
| **Bypass TPM / Secure Boot / RAM check** | Empties `appraiserres.dll` + writes LabConfig registry keys. Allows install on unsupported hardware. |
| **Skip OOBE setup wizard** | Auto-creates local account, hides Microsoft account prompts, EULA, etc. |
| **Show network setup during OOBE** | When skipping OOBE, still allows Wi‑Fi setup before desktop. |
| **Account name** | Local admin username (default: Admin). |
| **Password** | Local admin password (blank = no password). |
| **Computer name** | Optional. Blank = auto-generated. |
| **Use system regional settings** | Match locale/keyboard to your current PC. |
| **Disable BitLocker auto-encryption** | Prevents automatic drive encryption on first boot. |

### Privacy & Telemetry

| Option | Effect |
|--------|--------|
| **Keep Defender enabled (exclusions only)** | Only adds exclusion for scripts. Never disables Defender. Default: on. |
| **Apply privacy tweaks** | Disable telemetry, activity feed, error reporting, WiFi hotspot reporting. |
| **Disable Windows Spotlight** | Remove lock screen suggestions and ads. |
| **Hide taskbar search** | Remove search box/icon from taskbar. |

### Performance

| Option | Effect |
|--------|--------|
| **Game Mode** | Enable Windows Game Mode. |
| **High performance power plan** | Switch to High Performance at first logon. |
| **Ultimate Performance (ReviOS-style)** | Create/activate Ultimate Performance plan, disable memory compression and core parking. |
| **Disable Game Bar (Win+G)** | Remove Xbox Game Bar overlay. |

### System & Bloatware

| Option | Effect |
|--------|--------|
| **Remove pre-installed apps** | Remove Candy Crush, Edge, OneDrive, Xbox, etc. per `packages-to-remove.txt`. Custom list supported. |
| **Disable reserved storage** | DISM `/Set-ReservedStorageState Disabled` — frees ~7 GB. |

---

## 4. Presets

One-click configurations that set multiple options:

| Preset | Description |
|--------|-------------|
| **Stock** | Full setup wizard. Only TPM bypass and BitLocker disable. |
| **Light** | Minimal. Skip OOBE, keep bloatware and telemetry. |
| **Balanced** | Moderate. Skip OOBE, remove bloatware, privacy tweaks, Game Mode. |
| **Gaming** | Max performance, Game Mode, high/ultra power, keep Win+G. |
| **Gaming Lite** | Game Mode + bloat removal, no aggressive power tweaks. |
| **Privacy** | Max telemetry off, aggressive bloat removal, disable Spotlight/search/Game Bar. |
| **Privacy Lite** | Privacy + bloat, keep Spotlight and taskbar search. |
| **Workstation** | Productivity. Privacy, bloat removal, high perf, no gaming. |
| **Laptop** | Balanced, battery-friendly (no ultra performance). |
| **Developer** | Privacy, performance, lean. Good for dev machines. |
| **Minimal** | Leanest. Aggressive removal, reserved storage off. |
| **Extreme** | Maximum debloat, reserved storage off. |

---

## 5. Build Pipeline (8 Steps)

### Step 1: Source Preparation

- **ISO mode:** Mount via `Mount-DiskImage`, copy with `robocopy` to `%TEMP%\WinIsoMod\Extract`, dismount.
- **Folder mode:** Use provided folder directly.
- Clear read-only with `attrib -r -s -h`.

### Step 2: Image Source Preparation

- Validate `sources` folder.
- **TPM bypass:** Replace `sources\appraiserres.dll` with empty file.
- **ESD → WIM:** If `install.esd` exists, DISM export selected edition to temp WIM.

### Step 3: Mount WIM

- DISM mount at `%TEMP%\WinIsoMod\Mount`.
- Mount selected edition index.

### Step 4: Inject & Modify

- **autounattend.xml** → `Windows\Panther\unattend.xml`
- **LabConfig** → Offline SYSTEM hive: `BypassTPMCheck`, `BypassSecureBootCheck`, `BypassRAMCheck`, `BypassStorageCheck`
- **Setup scripts** → `Windows\Setup\Scripts\`: FirstLogonScript.ps1, RunFirstLogon.cmd, registry-tweaks.reg, RunLocalInstallers.ps1, VerifyAndCleanupBloat.ps1
- **firstlogon-options.json** → Your build options for the first-logon script
- **packages-to-remove.txt** → Copied or custom list
- **4a. App bake** — If apps selected: download via winget, copy to `Setup\Scripts\installers\`, write `installer-manifest.json`
- **4b. Bloatware** — DISM remove provisioned packages, delete Edge/OneDrive/Teams/Skype/Widgets folders
- **4c. Offline packages** — DISM add .appx/.msix if you provided any
- **4d. Reserved storage** — DISM `/Set-ReservedStorageState Disabled` if enabled

### Step 5: (Defender handling during mount)

- If `defenderExclusionsOnly` is false and exclusion fails: temporarily disable Defender for mount. Re-enable after unmount.

### Step 6: Unmount & Commit

- DISM unmount with `/Commit`.
- If ESD was used: swap temp WIM for original, delete ESD.

### Step 7: Optimize (optional)

- Export WIM with `/Compress:max` for smaller ISO.

### Step 8: Create ISO

- **oscdimg** packs folder → bootable ISO.
- Uses `boot\efisys.bin` from extract for UEFI boot.
- If oscdimg fails: output modified folder only.

---

## 6. Scripts & Files Injected

| File | Purpose |
|------|---------|
| **autounattend.xml** | Unattended setup: OOBE, local account, AutoLogon, FirstLogonCommands, regional. |
| **FirstLogonScript.ps1** | Main first-logon script. See [§7](#7-first-logon-behavior). |
| **RunFirstLogon.cmd** | Wrapper that launches FirstLogonScript.ps1. |
| **registry-tweaks.reg** | HKCU: ContentDeliveryManager (suggestions off), Privacy (advertising ID, activity feed), Cortana, TaskView, file extensions visible, TurnOffWindowsCopilot. |
| **firstlogon-options.json** | Options passed to FirstLogonScript: disableGameBar, gamingMode, removeBloatware, etc. |
| **packages-to-remove.txt** | Package prefixes for bloatware removal. |
| **RunLocalInstallers.ps1** | Installs baked-in app installers (no internet). |
| **VerifyAndCleanupBloat.ps1** | Scheduled at 2, 5, 15, 30, 60 min to catch re-provisioned bloat. |
| **installer-manifest.json** | List of baked app installers (when apps selected in WISO). |
| **installers/** | Folder with .exe/.msi/.msix copied from winget download. |

---

## 7. First-Logon Behavior

`FirstLogonScript.ps1` runs once at first user logon. Order of operations:

1. **Defender** — Add exclusion for scripts folder. If that fails and `defenderExclusionsOnly` is false, disable real-time protection.
2. **Registry** — Import registry-tweaks.reg. Apply conditional: Spotlight, taskbar search, Game Bar, Game Mode.
3. **Services** — Disable 100+ telemetry/diagnostic/rarely-used services (ReviOS/AtlasOS style). Keeps: Windows Update, BITS, Bluetooth, printing, network.
4. **Privacy** — Disable telemetry, diagnostic data, activity feed, WiFi hotspot reporting (when privacyTweaks).
5. **Performance** — Disable prefetcher, hiberboot. Optionally: Ultimate Performance plan, Core Parking off, Memory Compression off.
6. **Scheduled tasks** — Disable CEIP, compatibility appraiser, feedback, diagnostics, etc.
7. **OneDrive** — Uninstall completely: kill process, run uninstaller, remove Appx, delete folders, disable policy.
8. **Bloatware (online)** — Remove remaining Appx per packages-to-remove.txt. Delete Edge/Teams/Skype/Copilot/Widgets folders and shortcuts.
9. **VerifyAndCleanupBloat** — Schedule at 2, 5, 15, 30, 60 min.
10. **Baked apps** — Set RunOnce for `RunLocalInstallers.ps1` (runs installers from installer-manifest.json).
11. **Done flag** — Write `wiso-firstlogon-done.flag` to prevent re-run.

---

## 8. Bloatware Removal

### Offline (during build)

- DISM `/Remove-ProvisionedAppxPackage` for packages matching `packages-to-remove.txt`.
- Delete folders: Edge, EdgeWebView, EdgeUpdate, OneDrive, Teams, Skype, Widgets, etc.
- **Never removed:** Defender, Calculator, Photos, DesktopAppInstaller (winget), core UI (ShellExperienceHost, SearchApp, etc.).

### Online (first logon)

- Remove provisioned + installed Appx packages.
- Delete Edge/OneDrive/Teams/Skype/Copilot/Widgets folders and shortcuts.
- **VerifyAndCleanupBloat.ps1** — Runs 5 times (2–60 min) to catch Windows re-provisioning.

### Custom list

- You can override the default list in the Bloatware section. Select/deselect packages. Custom list is written to `packages-to-remove.txt` in the image.

---

## 9. App Installation (Baked vs Offline)

### Baked apps (winget download during build)

- Select apps in "Apps to Install" (Brave, Firefox, VS Code, etc.).
- WISO runs `winget download` on your PC during build.
- Copies installers to `Setup\Scripts\installers\`.
- Writes `installer-manifest.json`.
- At first logon, `RunLocalInstallers.ps1` runs each .exe/.msi/.msix — **no internet needed on target**.

### Offline packages (.appx/.msix)

- Add a folder containing .appx, .msix, .appxbundle, .msixbundle.
- DISM `/Add-ProvisionedAppxPackage` adds them to the image.
- Installed for all users at first boot.

---

## 10. USB & Output

### Output options

- **Open folder** — Open output directory.
- **Open in Rufus** — Launch Rufus with the ISO (if installed).
- **Copy to USB** — Copy ISO or folder to selected USB drive (for Ventoy or existing bootable).
- **Create bootable USB** — Format drive NTFS, copy extracted Windows folder. UEFI-bootable.

### ISO to USB (any ISO)

- Separate collapsible card.
- Select any ISO, pick drive, copy. Or open in Rufus.
- For non-WISO ISOs (Linux, etc.).

---

## 11. Dependencies & Installer

### Required

- Windows 10/11 (64-bit)
- Administrator rights
- Valid Windows ISO (e.g. Media Creation Tool)

### Optional

| Dependency | Purpose | How to get |
|------------|---------|------------|
| **Portable oscdimg** | Create bootable ISO | Auto-downloaded when needed. Cached at `%USERPROFILE%\.wiso\oscdimg.exe`. Installer downloads during setup. Dependency Check can install. |
| **winget** | Download apps to bake | Install App Installer from Microsoft Store. WISO does **not** install winget (pulls in UE-V). |

### NSIS installer (customInstall)

- Checks for cached oscdimg at `%USERPROFILE%\.wiso\oscdimg.exe`.
- If missing: download via BITS or Invoke-WebRequest.
- Checks for winget (informational only).

---

## 12. Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        WISO Application                          │
├─────────────────────────────────────────────────────────────────┤
│  Electron (main)                                                 │
│  ├── main.ts       → IPC, window, build trigger                  │
│  ├── builder.ts    → 8-step build pipeline                       │
│  ├── tools.ts      → PowerShell, DISM, oscdimg, USB               │
│  ├── autounattend  → Unattend.xml generator                      │
│  └── preload.ts    → Context bridge                              │
├─────────────────────────────────────────────────────────────────┤
│  React (Vite)                                                    │
│  ├── App.tsx       → Step flow, DependencyCheck gate             │
│  ├── DependencyCheck, SourceStep, OptionsStep                   │
│  ├── BuildStep, OutputStep, IsoToUsbStep                         │
│  └── PresetButtons, PresetTable, BloatwareSection, etc.          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│  Windows APIs                                                    │
│  • PowerShell (Mount-DiskImage, robocopy, winget, reg, services) │
│  • DISM (mount WIM, add/remove packages, export, reserved storage)│
│  • oscdimg (create bootable ISO)                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 13. Project Structure

```
windows-iso-customizer/
├── electron/
│   ├── main.ts
│   ├── builder.ts
│   ├── tools.ts
│   ├── autounattend.ts
│   └── preload.ts
├── src/
│   ├── App.tsx
│   ├── components/
│   ├── content/
│   ├── data/           # presets.ts, apps-list.json, bloatware-list.json
│   └── utils/          # configExport.ts
├── assets/
│   ├── FirstLogonScript.ps1
│   ├── RunFirstLogon.cmd
│   ├── registry-tweaks.reg
│   ├── RunLocalInstallers.ps1
│   ├── VerifyAndCleanupBloat.ps1
│   ├── packages-to-remove.txt
│   ├── InstallApps.ps1
│   ├── WisoAppInstaller.ps1
│   └── apps-list.json
├── build/
│   └── installer.nsh
└── package.json
```

---

## 14. Privacy & Security

- **All processing is local.** No telemetry, no analytics.
- **Defender:** Default is exclusions only. Never disables Defender during mount/unmount unless exclusion fails and `defenderExclusionsOnly` is false.
- **Open source:** Full pipeline and scripts are auditable.

---

## 15. Build & Run

```bash
# Development
npm run dev

# Production build
npm run build
# Output: release-build/WISO-Setup-1.0.0.exe
```

---

## Error Hints

WISO maps common errors to hints:

| Error pattern | Hint |
|---------------|------|
| Access denied, EPERM, lstat, unlink | Run as Administrator. Delete `%TEMP%\WinIsoMod` or reboot. |
| Error 50, offline image | DISM operation failed. Try rebuilding. |
| Invalid data, 0x8007000d | Image may be corrupted. Try different ISO. |
| Path not found, 0x80070003 | Required file missing. Check source ISO/folder. |
| Disk full, 0x80070070 | Free disk space. Build needs several GB. |
| Invalid parameter, 0x80070057 | Check Windows edition index. |
| Mount/dismount | Restart app and retry. |

---

*WISO — Your Windows, Your Way.*
