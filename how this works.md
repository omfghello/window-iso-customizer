# How WISO Works — The Complete Technical Deep Dive

*This document explains exactly what WISO does, in what order, and why. Every step, every file, every registry key, every `taskkill /f /im`, and now, every OOBE screen. Because we replaced the entire Windows Out-of-Box Experience with our own. No hand-waving. No "it just works." If you want to understand the machinery — the gears, the blood, the batch files, the Electron app that hijacks the Windows shell — read on. Written at 4 AM. Fueled by coffee. Narrated by a raccoon who planned a heist.*

*[Gerald's note: This document now covers THE HEIST. The Custom OOBE replacement. The crown jewel. My masterwork. The developer wrote the code but I PLANNED the architecture. The shell hijack was MY idea. The watchdog was MY paranoia. The privacy badges were Steve's idea and I let him have it because even raccoons recognize good UX. Dave provided structural support. As always. I have reviewed every technical claim in this document. They are correct. Including the heist parts. ESPECIALLY the heist parts.]*

---

## Table of Contents

1. [High-Level Architecture](#1-high-level-architecture)
2. [Phase A: Build (On Your PC)](#2-phase-a-build-on-your-pc)
3. [Phase B: Installation (On Target Machine)](#3-phase-b-installation-on-target-machine)
4. [Phase C: The Heist (Custom OOBE)](#4-phase-c-the-heist-custom-oobe)
5. [Phase D: The Reckoning (Live Debloating)](#5-phase-d-the-reckoning-live-debloating)
6. [Key Technical Concepts](#5-key-technical-concepts)
7. [File Locations Reference](#6-file-locations-reference)

---

## 1. High-Level Architecture

WISO operates in four distinct phases:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  PHASE A: BUILD (your PC, Node.js/Electron)                                 │
│  • Extract or use folder with Windows installer                             │
│  • Mount WIM via DISM                                                       │
│  • Modify registry hives, remove Appx, inject scripts + OOBE app          │
│  • Smuggle WISO-OOBE.exe into C:\Windows\WISO\ (THE HEIST PAYLOAD)        │
│  • Unmount, create ISO                                                      │
│  Output: Custom Windows ISO (with our OOBE hidden inside)                   │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  PHASE B: INSTALLATION (target machine, Windows Setup)                      │
│  • Boot from USB/ISO (WinPE)                                                 │
│  • Copy files to disk (including our OOBE app)                              │
│  • Reboot into full Windows                                                 │
│  • Microsoft's OOBE is SKIPPED (unattend.xml)                              │
│  • Temp "WISO-Setup" admin account created, auto-logon                     │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  PHASE C: THE HEIST (target machine, SetupComplete.cmd → WISO-OOBE.exe)    │
│  • SetupComplete.cmd swaps shell: explorer.exe → WISO-OOBE.exe             │
│  • Watchdog task created (15min safety net)                                  │
│  • WISO-OOBE.exe launches as the Windows shell (full screen, dark theme)   │
│  • User goes through: Region → Network → Account → Privacy → Appearance   │
│  •   → Apps → Live Debloating (THE SHOW) → Done → Reboot                  │
│  • App install: Baked path → Filesystem scan → Winget fallback             │
│  Output: User has configured their PC through OUR setup wizard              │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  PHASE D: THE RECKONING (inside WISO-OOBE's "Setting Up" screen)            │
│  • Process massacre, service nuke, OneDrive kill, Appx purge (PS 1-liner) │
│  • App installation — THREE-LAYER RESILIENCE:                              │
│    Layer 1: Check expected path for baked installers                       │
│    Layer 2: Filesystem BFS scan for missing files                          │
│    Layer 3: Winget install fallback at runtime                             │
│  • 7 silent flag combos per installer — LIVE with progress bar             │
│  • Power plan, registry tweaks, Defender re-enable                          │
│  • ALL VISIBLE TO USER in real-time with logs and progress                 │
│  • Finalize: shell restored, temp account deleted, reboot                  │
│  Output: Clean, debloated Windows. Clean getaway. Gerald vanishes.         │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Critical insight:** Phase A does the OFFLINE work: registry hives, Appx deprovisioning, Defender policy injection, scheduled task deletion, and smuggling our entire OOBE app into the WIM. Phase C is THE HEIST: `SetupComplete.cmd` (the inside man) swaps the shell, then `WISO-OOBE.exe` takes over as the Windows shell and runs our custom setup wizard. Phase D is THE RECKONING: the OOBE's "Setting Up" screen handles everything that needs a live OS — process killing, service disabling, app installation, power plans — all while the user watches with a progress bar. They literally WATCH Candy Crush die.

**The OOBE revolution:** We used to fight Microsoft's OOBE. We tried disabling services offline. OOBE crashed. "Why did my PC restart?" Again and again. Windows 11 24H2+ has undocumented dependencies. Services that shouldn't matter, matter. So Gerald had a revelation. From the top of the trenchcoat. At 4 AM. "Why are we fighting the gatekeeper," Gerald said (raccoon), "when we could BECOME the gatekeeper?" And that was the moment we built an entire Electron app — `WISO-OOBE.exe` — that replaces the Windows shell on first boot. 10 screens. Dark theme. Privacy badges. Live debloating. App installation. The whole first-boot experience, rebuilt from scratch. In JavaScript. Running as the Windows shell. Gerald planned it. Steve typed it. Dave stood through all of it. The trenchcoat held. Microsoft's "Hi there!" never got to speak. Because we were already speaking. And we were saying it BETTER.

**The cmd evolution:** `SetupComplete.cmd` used to be 400+ lines of fury. Now it's 20 lines of surgical precision. Its job: swap the shell, create a watchdog, exit. The inside man opens the vault and walks away. ALL the heavy runtime work moved into `WISO-OOBE.exe`, which runs it live, with a UI, with progress, with privacy badges, with the user watching. cmd evolved from a blunt instrument to a precision tool. Gerald approves of the refinement. Steve misses the 400 lines. Dave is indifferent.

---

## 2. Phase A: Build (On Your PC)

The build runs in `electron/builder.ts` via `runBuild()`. It executes 8 main steps. Here's what each one does, in excruciating detail.

### Step 0: Defender Exclusion (Before Anything Else)

**Why:** When DISM mounts a WIM file, Windows Defender's real-time protection immediately starts scanning every file inside the mount directory. This causes file locks. DISM then fails with `ERROR 0xC1510027` ("files in use" / "open handles"). The mount never completes.

**What we do:** Before any extraction or mounting, we call:

```powershell
Add-MpPreference -ExclusionPath "C:\Users\...\AppData\Local\Temp\WinIsoMod"
Add-MpPreference -ExclusionPath "<directory containing source ISO>"
```

If `sourceType === 'folder'`, we also exclude the source folder path. Defender will not scan these paths. No file locks. Mount succeeds.

**If exclusion fails:** We fall back to temporarily disabling Defender entirely (real-time monitoring, IOAV, behavior monitoring, script scanning, plus registry policy keys). This is gated by `defenderExclusionsOnly` — if the user chose that option, we never disable, we just fail or run slowly. At the end of the build, in a `finally` block, we re-enable Defender and remove the policy overrides. Always.

### Step 1: Source Preparation

**ISO mode:** We mount the ISO via PowerShell `Mount-DiskImage`, get the drive letter, run `robocopy` to copy the entire ISO contents to `workDir/Extract`, then `Dismount-DiskImage`. The extract directory now contains `sources/install.wim` or `sources/install.esd`.

**Folder mode:** User has already extracted the ISO. We use their folder directly. No copy.

**Then:** We run `attrib -r -s -h` on the extract tree to strip read-only, system, and hidden flags. Without this, subsequent file operations (DISM, fs.unlinkSync, etc.) can hit `EPERM`.

**TPM bypass (if enabled):** We locate `sources/appraiserres.dll`. This DLL is what Windows Setup calls to "appraise" whether your PC meets Windows 11 requirements (TPM 2.0, Secure Boot, 4GB RAM). We overwrite it with an empty file (0 bytes). Setup still calls it, but it returns "all good" because... there's nothing to check. We also write LabConfig registry keys into the offline SYSTEM hive later (Step 5a area, but actually in the TPM bypass block before Step 4). Those keys are `BypassTPMCheck`, `BypassSecureBootCheck`, `BypassRAMCheck`, `BypassStorageCheck` = 1. Belt and suspenders.

### Step 2: Image Source Preparation

**WIM vs ESD:** Windows ISOs ship either `install.wim` (WIM format) or `install.esd` (ESD = compressed WIM with LZMS). DISM can mount both, but for *modification* we need WIM. If we have ESD, we run:

```
dism /Export-Image /SourceImageFile:install.esd /SourceIndex:N /DestinationImageFile:install_temp.wim /Compress:fast /CheckIntegrity
```

We export only the edition index the user selected (e.g., Windows 11 Home = 6). The temp WIM has index 1. We use that for mounting.

**If we have WIM:** We use it directly. No conversion.

### Step 3: Mounting the Image

**Cleanup first:** We run `dism /Cleanup-Wim` and `dism /Cleanup-Mountpoints` to nuke any stale mounts from previous failed runs. Stale mounts cause "already mounted" or "path in use" errors.

**Mount directory:** `workDir/Mount` (e.g. `C:\Users\...\AppData\Local\Temp\WinIsoMod\Mount`). If it exists from a previous run, we try to unmount it, clear attrib, delete the directory. If deletion fails (files locked), we switch to an alternate path `Mount_<timestamp>`.

**Attrib on WIM:** We clear read-only on the WIM file itself. Sometimes the extracted WIM is R/O.

**Kill locking processes:** We run a PowerShell one-liner that finds any process with a loaded module path under `workDir` and `Stop-Process -Force` on it. This catches Explorer, previous WISO runs, etc.

**Mount command:**

```
dism /Mount-Wim /WimFile:<path>\install.wim /Index:1 /MountDir:<workDir>\Mount
```

**Retry logic:** If DISM returns exit code 32, 3242328359 (0xC1510027), or -2146498521, we assume "files in use" or corrupt mount. We unmount, cleanup, wait 8 seconds, retry. Up to 3 attempts.

**Defender at mount time:** We try to add exclusion for `mountDir` and `workDir`. If that fails (e.g., no admin rights, policy blocks it), we disable Defender as described in Step 0. The `defenderWasDisabled` flag is set so the `finally` block knows to re-enable.

### Step 4: Injection

**autounattend.xml placement — CRITICAL:** We do **NOT** put the answer file on the ISO root. If you put `autounattend.xml` on the root of the USB/ISO, WinPE finds it on the *first* boot, applies it, reboots... and WinPE boots again from the USB. It finds the same file. Loop. Infinite reboot. We learned this the hard way.

Instead, we place the answer file at `Mount\Windows\Panther\unattend.xml` — *inside* the WIM. Windows Setup copies the WIM contents to the hard drive. After the mid-install reboot, the machine boots from the *hard drive*, not the USB. Setup reads `C:\Windows\Panther\unattend.xml` from the disk. The USB is never consulted again for that file. No loop.

**What's in autounattend.xml:** Generated by `generateAutounattend()` in `electron/autounattend.ts`. It's an `oobeSystem` pass only (no `windowsPE`). It contains:

- `ComputerName` (if user set one)
- `DesktopOptimization` (Store apps off taskbar)
- If `skipOobe`: `OOBE` (hide all setup screens), `UserAccounts` (local account with name/password), `AutoLogon` (log on once automatically)
- `FirstLogonCommands`: PowerShell invocation of `FirstLogonScript.ps1` (BACKUP trigger — the real work is done by `SetupComplete.cmd` which Windows runs automatically, but this is belt-and-suspenders)
- `International` (input locale, UI language, etc.)

**Script injection:** We create `Mount\Windows\Setup\Scripts\` and copy:

- `SetupComplete.cmd` — **THE MAIN WEAPON.** Windows auto-runs `%WINDIR%\Setup\Scripts\SetupComplete.cmd` as LOCAL SYSTEM after OOBE. This is the PRIMARY execution point. 400+ lines of batch fury. If this file exists, Windows runs it. No questions. No UAC. No execution policy. Microsoft built this hook for OEMs. We are the OEM now. Three raccoons in a Dell costume.
- `FirstLogonScript.ps1` — **FALLBACK STUB.** Checks if `SetupComplete.cmd` finished (via done-flag). If not, re-runs it. A dead man's switch for debloating. It used to run the whole show. Now it's retired. Rocking chair. War stories.
- `VerifyAndCleanupBloat.ps1` — Scheduled verification sweeps
- `registry-tweaks.reg` — Optional; some setups import this

**firstlogon-options.json AND firstlogon-options.cmd:** We write TWO config files. The JSON has the user's build options for legacy/PowerShell compatibility. The `.cmd` is the same data in batch format — `set "WISO_REMOVE_BLOAT=1"`, `set "WISO_GAMING_MODE=0"`, etc. `SetupComplete.cmd` loads this with a simple `call firstlogon-options.cmd`. Because cmd can't parse JSON. It tried. It cried. It gave up. We gave it environment variables instead. Everyone's happy.

**packages-to-remove.txt:** If `removeBloatware` is on, we copy our default list (or the user's custom list) to `Setup\Scripts\packages-to-remove.txt`. The first-logon script and VerifyAndCleanupBloat use this for Appx removal.

### Step 4a: App Download and Bake

If the user selected apps to install (`appsToInstall`), we run `winget download --id <appId> -d <dir>` for each. `winget download` fetches the installer (e.g. Firefox.exe, 7zip.msi) to a temp directory. We then:

1. Copy each installer to `Mount\Windows\Setup\Scripts\installers\`
2. Write `installer-manifest.json` with `[{ "appId": "...", "filename": "..." }, ...]`
3. Run `Unblock-File` on all files in the installers dir (strips Zone.Identifier NTFS streams so SmartScreen won't block them on the target)
4. Write the winget IDs into `firstlogon-options.json` under the `appsToInstall` field — this is the OOBE's fallback lifeline. If baked installers go missing (it happens — Windows is a chaos engine), the OOBE reads `appsToInstall` and knows WHICH apps to hunt for via filesystem BFS scan or, as a last resort, install live via `winget install` at runtime. The JSON is the treasure map. The winget IDs are the X marks. Gerald insisted on three layers of redundancy. Gerald has trust issues. Gerald is correct.

### Step 4b: Bloatware Removal (Offline)

We call `removeProvisionedPackagesOffline()` which uses DISM:

```
dism /Image:<mountDir> /Get-ProvisionedAppxPackages
```

We parse the output, match package names against our `packages-to-remove.txt` list (and a `NEVER_REMOVE` list — Defender, Calculator, ShellExperienceHost, etc.), then for each match:

```
dism /Image:<mountDir> /Remove-ProvisionedAppxPackage /PackageName:<full package name>
```

We also delete Edge and OneDrive folders from the mounted image: `Program Files\Microsoft\Edge`, `Program Files (x86)\Microsoft\Edge`, `Program Files\Microsoft OneDrive`, etc. We do **not** delete EdgeWebView — OOBE and many apps depend on WebView2.

### Step 5: Offline Customization (The Big One)

This is where we rewrite Windows' brain. All of this happens on the *mounted* filesystem — the registry hives are files on disk. We load them into a temporary registry key, modify, unload.

#### 5a: Service Disabling (SYSTEM hive)

**Hive path:** `Mount\Windows\System32\config\SYSTEM`

**Process:**

1. `reg load HKLM\WISO_SYS <path to SYSTEM>`
2. For each service in our `servicesToDisable` list (filtered by `neverDisable`), we run: `reg add "HKLM\WISO_SYS\ControlSet001\Services\<ServiceName>" /v Start /t REG_DWORD /d 4 /f`
   - `Start = 4` means "Disabled"
3. For per-user service templates (e.g. `CDPUserSvc`, `OneSyncSvc`), we do the same — disabling the base template prevents Windows from creating per-user instances
4. If `disableGameBar`, we add `BcastDVRUserSvc` and `CaptureService` to the template list
5. If `ultraPerformance` or `highPerformancePower`, we add performance registry tweaks: HiberBoot off, Prefetcher off, Superfetch off, NTFS last-access update off
6. `reg unload HKLM\WISO_SYS`

**neverDisable list:** We never disable core OS services (RpcSs, EventLog, Dhcp, etc.), Defender (WinDefend, WdNisSvc, WdFilter), OOBE-critical (ClipSVC, InstallService, AppReadiness, TokenBroker, NgcCtnrSvc, NgcSvc, TextInputManagementService, camsvc, PcaSvc, AllUserInstallAgent), display/input (FontCache, DispBrokerDesktopSvc, TabletInputService, TouchKeyboardAndHandwritingPanelService), or kernel-level (dam, NetBT, tcpipreg). Disabling those would brick the install or cause a BSOD.

#### 5b: SOFTWARE Hive (HKLM Policies)

**Hive path:** `Mount\Windows\System32\config\SOFTWARE`

**Process:** `reg load HKLM\WISO_SW <path>`, then we add/modify dozens of registry keys:

- `DataCollection` — AllowTelemetry=0, AllowDiagnosticData=0
- `System` — EnableActivityFeed=0, PublishUserActivities=0, UploadUserActivities=0
- `Windows Error Reporting` — Disabled=1
- `Windows Search` — DisableWebSearch=1, ConnectedSearchUseWeb=0
- `LocationAndSensors` — DisableLocation=1
- `AdvertisingInfo` — DisabledByGroupPolicy=1
- `CloudContent` — DisableWindowsConsumerFeatures=1 (stops Content Delivery Manager from reinstalling bloat)
- `WindowsCopilot` — TurnOffWindowsCopilot=1
- `WindowsAI` — DisableAIDataAnalysis=1, TurnOffSavingSnapshots=1 (Recall off)
- `OneDrive` — DisableFileSyncNGSC=1, DisableFileSync=1
- `GameDVR` — AllowGameDVR=0 (if disableGameBar)
- OneDrive CLSID unpin, Run key deletion, etc.

#### 5b-extra: Offline Defender Neutering (SOFTWARE hive)

If `defenderExclusionsOnly` is NOT set (i.e., the user wants full offline Defender disable), we bake 7 policy keys into the SOFTWARE hive before unloading:

```
reg add "HKLM\WISO_SW\Policies\Microsoft\Windows Defender" /v DisableAntiSpyware /t REG_DWORD /d 1 /f
reg add "HKLM\WISO_SW\Policies\Microsoft\Windows Defender\Real-Time Protection" /v DisableRealtimeMonitoring /t REG_DWORD /d 1 /f
reg add "HKLM\WISO_SW\Policies\Microsoft\Windows Defender\Real-Time Protection" /v DisableBehaviorMonitoring /t REG_DWORD /d 1 /f
reg add "HKLM\WISO_SW\Policies\Microsoft\Windows Defender\Real-Time Protection" /v DisableIOAVProtection /t REG_DWORD /d 1 /f
reg add "HKLM\WISO_SW\Policies\Microsoft\Windows Defender\Real-Time Protection" /v DisableOnAccessProtection /t REG_DWORD /d 1 /f
reg add "HKLM\WISO_SW\Policies\Microsoft\Windows Defender\Real-Time Protection" /v DisableScanOnRealtimeEnable /t REG_DWORD /d 1 /f
reg add "HKLM\WISO_SW\Policies\Microsoft\Windows Defender\Real-Time Protection" /v DisableScriptScanning /t REG_DWORD /d 1 /f
```

**Why:** When the target boots, Defender services start (OOBE needs them or it panics), but the policy keys tell Defender "don't scan anything. Close your eyes." Real-time protection is DOA from the first millisecond. This means `SetupComplete.cmd` can kill processes, delete folders, and run installers with zero Defender interference. At the end, the cmd deletes these policy keys and re-enables scanning. Defender wakes up. Everything is clean. It takes credit. We let it.

Then `reg unload HKLM\WISO_SW`.

#### 5c: Default User HKCU (NTUSER.DAT)

**Hive path:** `Mount\Users\Default\NTUSER.DAT`

**Process:** `reg load HKLM\WISO_DEF <path>`, then we write 50+ values into the default user profile:

- ContentDeliveryManager — all the SubscribedContent-* values = 0, SilentInstalledAppsEnabled=0, etc.
- Privacy — EnableAdvertisingId=0, EnableActivityFeed=0
- CapabilityAccessManager\ConsentStore — location, webcam, microphone = Deny
- Search — AllowCortana=0, BingSearchEnabled=0
- Explorer\Advanced — HideFileExt=0, ShowTaskViewButton=0, LaunchTo=1 (This PC), TaskbarDa=0 (Widgets), TaskbarMn=0 (Chat)
- BackgroundAccessApplications — GlobalUserDisabled=1
- Clipboard — EnableClipboardHistory=0
- InputPersonalization — RestrictImplicitTextCollection=1
- And many more...

Conditional: `disableWindowsSpotlight` adds DisableWindowsSpotlightFeatures. `hideTaskbarSearch` adds SearchboxTaskbarMode=0. `removeBloatware` adds TaskbarDa and TaskbarMn.

Then `reg unload HKLM\WISO_DEF`.

#### 5d: Scheduled Task Removal

We delete the XML files for specific scheduled tasks from `Mount\Windows\System32\Tasks\`. Paths are like `Microsoft\Windows\Application Experience\Microsoft Compatibility Appraiser`. Deleting the XML = the task no longer exists. We never delete OOBE-critical tasks (CloudExperienceHost, InstallService, Flighting, Clip/LicenseValidation, Subscription).

#### 5e: OneDrive Offline Nuke

We delete:

- `Mount\Program Files\Microsoft OneDrive`
- `Mount\Program Files (x86)\Microsoft OneDrive`
- `Mount\Windows\System32\OneDriveSetup.exe`
- `Mount\Windows\SysWOW64\OneDriveSetup.exe`
- `Mount\Users\Default\AppData\Local\Microsoft\OneDrive`
- Any task XML under `Tasks` that contains "OneDrive" in the name

### Step 6: Unmount and Commit

```
dism /Unmount-Wim /MountDir:<mountDir> /Commit
```

`/Commit` writes all changes back into the WIM. If we used `/Discard`, we'd lose everything. We don't.

If the source was ESD, we then replace `install.esd` with our modified `install.wim` (we delete the ESD, copy the temp WIM to install.wim).

### Step 7: Optimize Image

We run DISM `/Export-Image` with `/Compress:max` to create a smaller WIM. Sometimes the export fails; we keep the original if so. We also delete any `install.esd` in sources — ESD uses LZMS "recovery" compression that can corrupt modified images. WIM is safer for our use case.

### Step 8: Create ISO

We call `runOscdimg(extractDir, outputPath)`. oscdimg is Microsoft's tool to create ISO files. We need it from the Windows ADK or a portable copy. The command builds an ISO from the extract directory (which now contains our modified install.wim, our scripts in Setup\Scripts, and unattend.xml in Windows\Panther). The ISO is bootable (UEFI) and contains the standard Windows installer layout.

**Output:** The ISO file at `outputPath`, plus optionally an output folder (same contents, not packaged). The UI shows both.

---

## 3. Phase B: Installation (On Target Machine)

When the user boots from the WISO-created USB/ISO and installs Windows:

1. **WinPE boot:** The machine boots into Windows Preinstallation Environment (WinPE) from the USB. WinPE is a minimal Windows. It does NOT read `Windows\Panther\unattend.xml` from the USB — that path doesn't exist on the USB in a way WinPE cares about. Our unattend is inside the WIM, which hasn't been copied yet.

2. **File copy:** Windows Setup copies the contents of the WIM to the target drive (e.g. C:\). This includes `C:\Windows\Panther\unattend.xml` and `C:\Windows\Setup\Scripts\*`.

3. **Reboot:** The machine reboots. Now it boots from the *hard drive*, not the USB.

4. **OOBE or skip:** If we set `skipOobe`, the unattend tells Windows to skip all OOBE screens, create the local account we specified, and auto-logon once. The user never sees "Let's connect you to a network" or "Sign in with Microsoft." They go straight to the desktop (after a brief "Setting up" phase).

5. **FirstLogonCommands:** As part of the oobeSystem pass, Windows runs our `FirstLogonCommands` during the "Setting up" phase, before the desktop appears. The command is `cmd /min /c "%SystemRoot%\Setup\Scripts\RunFirstLogon.cmd"`. Windows executes this synchronously (waits for it to finish) with `RequiresUserInput=false`.

---

## 4. Phase C: First Boot (The Reckoning — Now in cmd)

*We used to run this whole show in PowerShell. 300+ lines of `Stop-Process` and `Set-Service` and `try/catch` and `-ErrorAction SilentlyContinue`. It was a cathedral. A beautiful, fragile cathedral. Then we discovered that PowerShell sometimes just doesn't run during Windows Setup because execution policies, UAC, and the universe conspire against us. So we demolished the cathedral. And built a bunker. Out of cmd. And `taskkill`. And `sc config`. And it has never, EVER, failed to execute. cmd.exe: the cockroach of Windows since 1981.*

### 4.1 SetupComplete.cmd — THE PRIMARY EXECUTOR

**How it starts:** Windows has a built-in hook: if `%WINDIR%\Setup\Scripts\SetupComplete.cmd` exists, Windows runs it automatically as **LOCAL SYSTEM** after OOBE completes. No UAC prompt. No execution policy. Full administrative privileges. Microsoft built this for OEMs like Dell and HP to do post-install customization. We are now Dell. Three raccoons in a Dell costume. With a .cmd file. And 80 `taskkill` commands.

**Guard:** If `wiso-firstlogon-done.flag` exists, the cmd cleans up Run/RunOnce keys and exits. It only runs once.

**Options load:** `call "%~dp0firstlogon-options.cmd"` — loads `WISO_REMOVE_BLOAT`, `WISO_PRIVACY_TWEAKS`, `WISO_GAMING_MODE`, `WISO_DISABLE_GAMEBAR`, `WISO_ULTRA_PERF`, `WISO_HIGH_PERF`, `WISO_DEFENDER_EXCL_ONLY`, `WISO_DEFENDER_OFF_OFFLINE`, etc. as environment variables. No JSON parsing. No PowerShell. Just `set` commands. Beautiful.

**Defender check:** If `WISO_DEFENDER_OFF_OFFLINE=1`, writes `wiso-defender-disabled.flag`. This tells other scripts "Defender is asleep. Don't wake it."

**RunOnce fallback registration:** Registers `FirstLogonScript.ps1` in `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce` as a tertiary safety net.

**Phase 0 — Process Massacre:**
```batch
taskkill /f /im OneDrive.exe >nul 2>&1
taskkill /f /im msedge.exe >nul 2>&1
taskkill /f /im Teams.exe >nul 2>&1
... 77 more ...
```
Pure cmd. 80+ processes. `taskkill /f /im` — no `-ErrorAction SilentlyContinue`, no `try/catch`, just `/f` for force and `>nul 2>&1` for silence. If the process doesn't exist, taskkill shrugs. If it does, taskkill kills it. Simple. Brutal. Effective. Xbox/GameBar processes are conditionally skipped based on `WISO_GAMING_MODE` and `WISO_DISABLE_GAMEBAR`.

**Phase 0b — Post-OOBE Service Annihilation:**
```batch
sc stop "ClipSVC" >nul 2>&1
sc config "ClipSVC" start= disabled >nul 2>&1
reg add "HKLM\SYSTEM\CurrentControlSet\Services\ClipSVC" /v Start /t REG_DWORD /d 4 /f >nul 2>&1
... repeat for 50+ services ...
```
Three commands per service: stop it, configure it disabled, hammer `Start=4` into the registry for good measure. Belt AND suspenders. These services were protected during OOBE (ClipSVC, InstallService, AppReadiness, TokenBroker, etc.). Their diplomatic immunity has expired. Per-user service templates (CDPUserSvc, OneSyncSvc, etc.) get the same treatment. 18 scheduled tasks get `schtasks /change /disable`.

**Phase 1a — OneDrive Extinction:**
```batch
taskkill /f /im OneDrive.exe >nul 2>&1
"%SystemRoot%\System32\OneDriveSetup.exe" /uninstall >nul 2>&1
"%SystemRoot%\SysWOW64\OneDriveSetup.exe" /uninstall >nul 2>&1
winget uninstall --id Microsoft.OneDrive --silent --purge --force >nul 2>&1
```
Plus a PowerShell one-liner for `Remove-AppxPackage *OneDrive*` (cmd can't do Appx removal). Plus folder deletion. Plus registry Run key deletion. OneDrive attacked from 6 angles. No survivors.

**Phase 1b — Power Plan:**
`powercfg` commands in pure cmd: duplicate scheme, set active, disable hibernation, core parking, etc. One PowerShell one-liner for `Disable-MMAgent -mc` (no cmd equivalent).

**Phase 1c — Online Appx Removal:**
A PowerShell one-liner does 3 passes of `Remove-AppxProvisionedPackage` and `Remove-AppxPackage`. This is the ONE thing cmd genuinely cannot do — Microsoft tied Appx management exclusively to PowerShell cmdlets. So we use a single PowerShell command. ONE. The rest is cmd. Edge folders get deleted. Start menu shortcuts get swept. Desktop shortcuts get cleaned.

**Phase 2 — Verification Sweep Scheduling:**
```batch
for %%M in (2 5 15 30 60) do (
    schtasks /create /tn "WISO_BloatVerify%%M" /tr "..." /sc once /st ... /f
)
```
Five one-time tasks. VerifyAndCleanupBloat.ps1 at 2, 5, 15, 30, 60 minutes. Because Windows WILL try to reinstall Candy Crush.

**Phase 3 — Installer Copy + Execution (ALL IN CMD):**

*Desktop path resolution:* When running as SYSTEM, `%USERPROFILE%` points to `C:\Windows\system32\config\systemprofile`. Not helpful. So we query `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList` to find the real user's SID, then read their `ProfileImagePath`. Digital detective work. In batch. At 4 AM. The raccoons were impressed.

```batch
robocopy "%SCRIPTDIR%installers" "%USERDESKTOP%\WISO-Installers" /E /R:2 /W:1 /NP /NDL /NFL >nul 2>&1
```

Then for each installer file:
- **MSI:** `msiexec /i "file" /quiet /norestart /qn` with interactive fallback
- **MSIX/APPX:** PowerShell one-liner `Add-AppxPackage -Path "file"`
- **EXE:** A `:install_exe` subroutine tries 7 silent flag combos:
  1. `/VERYSILENT /SUPPRESSMSGBOXES /NORESTART /SP-` (Inno Setup)
  2. `/S` (NSIS)
  3. `/silent /norestart` (generic)
  4. `/quiet /norestart` (generic)
  5. `-s` (7-Zip style)
  6. `--silent` (some Linux-ported)
  7. `/qn /norestart` (MSI-wrapped EXE)

  If all 7 fail (exit code != 0), interactive fallback. It's like picking a lock with 7 picks. One usually works. If not, we kick the door down.

Source `installers\` directory gets cleaned up. Desktop copies persist.

**Phase 4 — Second Process Massacre:** Same `taskkill` list. Respawn check. Because bloatware is persistent. Like herpes. But less useful.

**Phase 5 — Defender Re-enable + Cleanup:**
```batch
reg delete "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v DisableAntiSpyware /f
reg delete "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender\Real-Time Protection" /f
powershell -Command "Set-MpPreference -DisableRealtimeMonitoring $false"
```
Delete policy keys. Re-enable scanning via PowerShell one-liner. Delete `wiso-defender-disabled.flag`. Clean up Run/RunOnce keys. Write `wiso-firstlogon-done.flag`. Done.

### 4.2 FirstLogonScript.ps1 — THE FALLBACK STUB

*This script used to be 300+ lines of fury. It killed processes. It disabled services. It removed Appx packages. It installed apps. It was the whole show. Now it's a stub. A safety net. A dead man's switch. It sits in a rocking chair telling war stories about the time it disabled 47 services in one pass. The raccoons visit sometimes.*

**What it does now:**
1. Checks if `wiso-firstlogon-done.flag` exists → if yes, cleans up Run/RunOnce keys and exits
2. If not done → re-launches `SetupComplete.cmd` (which checks its own done-flag)
3. Registers itself in RunOnce as a further fallback

That's it. 30 lines. Down from 300. The PowerShell mansion is now a PowerShell mailbox.

### 4.3 The Three-Layer Reliability Chain

```
Layer 1: SetupComplete.cmd  →  LOCAL SYSTEM, guaranteed by Windows
                                If %WINDIR%\Setup\Scripts\SetupComplete.cmd exists, it runs.
                                Period. Microsoft's own documentation says so.

Layer 2: FirstLogonCommands  →  unattend.xml backup trigger
                                Runs FirstLogonScript.ps1 in user context.
                                If SetupComplete.cmd already finished (done-flag), it exits.

Layer 3: RunOnce registry    →  Tertiary fallback
                                SetupComplete.cmd registers FirstLogonScript.ps1 in RunOnce.
                                If both Layer 1 and 2 fail, next logon triggers this.
```

A single `wiso-firstlogon-done.flag` prevents double execution across all three layers. Belt, suspenders, duct tape, and Dave holding your pants up. Dave has been holding pants up since the beginning. Dave's leg day is EVERY day. Dave doesn't skip leg day because Dave IS leg day. The reliability of this system is directly proportional to Dave's quad strength. Dave's quads are immense. The system has never failed.

### 4.4 VerifyAndCleanupBloat.ps1

Runs at 2, 5, 15, 30, 60 minutes. It:

- Kills the same 80+ processes
- Runs 3 passes of Appx removal
- Deletes Edge/OneDrive folders and shortcuts
- OneDrive nuclear cleanup (taskkill, uninstall, Remove-Appx, delete folders, registry)
- Re-disables the same 50+ services (Windows sometimes re-enables them)
- Disables per-user OneSync/FileSync services
- If `ultraPerformance`, disables UCPD driver

It only runs if `removeBloatware` is true (from firstlogon-options.json).

---

## 5. Key Technical Concepts

### Why We Modify the WIM Offline (Not at First Logon)

- **Registry:** Loading a hive from a file requires the file to be writable and not in use by a running OS. We can't modify the "live" SYSTEM hive of the OS we're installing — it doesn't exist yet. We modify the hive file inside the WIM before it's ever booted.
- **Appx deprovisioning:** DISM can remove provisioned packages from an offline image. Once removed, they're gone from the image. No first-logon needed.
- **Services:** Service start type is stored in the registry. By setting Start=4 in the offline SYSTEM hive, the service is disabled the moment Windows boots from that image.
- **Scheduled tasks:** Tasks are XML files in `Windows\System32\Tasks\`. Delete the file = task gone. We delete them from the mounted filesystem.

### Why Some Things Must Be Done at First Boot (SetupComplete.cmd)

- **Process killing:** Processes don't exist until Windows is running. We can't `taskkill /f /im OneDrive.exe` from inside a WIM. As much as we'd like to.
- **OneDrive uninstall:** OneDriveSetup.exe /uninstall must run in a live OS. We can delete the OneDrive *files* from the image, but the Appx package and scheduled tasks may persist. SetupComplete.cmd does the full nuclear uninstall from 6 angles.
- **EXE/MSI installers:** They require a running OS to execute. We bake them into the image, copy to Desktop via robocopy, and run them — all in cmd.
- **Power plan:** powercfg requires a live OS. cmd handles it natively. `Disable-MMAgent` needs one PowerShell one-liner.
- **Appx removal (online):** Windows re-provisions some packages during OOBE or first boot. Our offline removal gets most of them, but SetupComplete.cmd runs 3 passes via a PowerShell one-liner to catch stragglers. This is the one thing cmd genuinely cannot do — Microsoft tied Appx management to PowerShell cmdlets exclusively. Probably on purpose. Probably to annoy us specifically.

### Why cmd Instead of PowerShell

- **SetupComplete.cmd runs as LOCAL SYSTEM.** Guaranteed. No UAC. No execution policy. Microsoft's own hook.
- **PowerShell execution policies** can prevent `.ps1` scripts from running. `cmd.exe` has no execution policy. It's been executing since 1981 and it's not about to start asking permission now.
- **UAC blocks elevation** during the "Setting up" phase — no desktop means no UAC prompt. cmd running as SYSTEM doesn't need elevation. It's already God.
- **Reliability:** cmd.exe is the cockroach of Windows. It runs everywhere, at any time, as any user. PowerShell is a mansion. cmd is a bunker. We chose the bunker.
- **Three PowerShell exceptions:** `Remove-AppxPackage` (Appx removal), `Disable-MMAgent` (memory compression), `Set-MpPreference` (Defender). These have NO cmd equivalent. Everything else — `taskkill`, `sc config`, `reg add`, `robocopy`, `powercfg`, `schtasks`, `msiexec` — is native cmd.

### The Three-Layer App Installation Resilience (Gerald's Paranoia, Codified)

*Gerald doesn't trust the filesystem. Gerald doesn't trust Windows. Gerald doesn't trust the laws of thermodynamics. Gerald trusts THREE things: redundancy, redundancy, and Dave's quads. So when we built the app installation system for the OOBE, Gerald demanded three layers of fallback. "What if the files move?" Gerald asked. "What if DISM hiccups?" Gerald asked. "What if a cosmic ray flips a bit?" Gerald asked. We built all three layers. The cosmic ray hasn't happened yet. But when it does, we're ready.*

The OOBE's app installation screen uses a three-layer approach to find and install every app the user selected:

```
Layer 1: EXPECTED PATH CHECK (The Obvious One)
├── Check C:\Windows\Setup\Scripts\installers\ for baked-in files
├── Cross-reference against installer-manifest.json
├── If the file exists at the expected path → use it
└── 99% of the time, this is all you need. The other 1% is why Gerald exists.

Layer 2: FILESYSTEM BFS SCAN (The Bloodhound)
├── For any app NOT found at expected path → panic? NO. HUNT.
├── Breadth-first search starting from C:\Windows\Setup\Scripts\
├── Expanding outward through common installer locations
├── Looking for filename matches from the manifest
├── Because sometimes DISM puts things in weird places
├── Because sometimes Windows "helpfully" reorganizes your files
├── Because Gerald said "FIND IT" and we built a search algorithm
└── If found via BFS → use it. Log the detour. Continue.

Layer 3: WINGET FALLBACK (The Nuclear Option)
├── App still not found? Fine. We have winget IDs.
├── firstlogon-options.json contains appsToInstall with winget IDs
├── OOBE calls winget install --id <appId> --silent at runtime
├── Requires internet connectivity on the target machine
├── Slower than baked installers but GUARANTEED to work
├── Gerald calls this "the safety net under the safety net under the trampoline"
└── If winget succeeds → app installed. Crisis averted. Gerald nods.
```

**Why Layer 1 exists:** Speed. Baked installers are already on disk. No download. No network. Install starts immediately. This is the heist going according to plan. Gerald's preferred outcome.

**Why Layer 2 exists:** Resilience. DISM image operations occasionally shuffle files. Windows Defender quarantine can move executables. The Zone.Identifier ADS can cause permission issues that make files "invisible" to simple path checks. The BFS scan doesn't care about any of that. It will FIND the file. It's a bloodhound. It has no pride. It will check every directory. It will sniff every path. It found a Firefox installer in `C:\Windows\Temp` once. We don't know how it got there. The bloodhound doesn't ask questions. The bloodhound delivers results.

**Why Layer 3 exists:** Because Gerald has been hurt before. Because builds can be weeks old and installer files can be corrupted by disk errors. Because the user might have used a folder source that was already missing files. Because the OOBE's job is to install the apps the user asked for, period, full stop, no excuses. If the file isn't on disk, we download it fresh. Winget is Microsoft's own package manager. It works. It's slow, but it works. Gerald would rather wait 60 seconds for a winget download than tell the user "sorry, couldn't find Firefox." Gerald does NOT say sorry. Gerald says "I found another way." And then Gerald installs Firefox. From the top of the trenchcoat. At 4 AM. While Steve types the progress bar update and Dave shifts his weight imperceptibly.

The three layers are checked sequentially. If Layer 1 succeeds, Layers 2 and 3 never run. If Layer 1 fails, Layer 2 kicks in. If Layer 2 fails, Layer 3 catches it. The user sees none of this drama. They see a progress bar. They see "Installing Firefox..." They see a checkmark. Behind that checkmark is a three-layer redundancy system built by a paranoid raccoon who once watched a DISM mount corrupt an entire `installers\` directory and SWORE it would never happen again. It hasn't. But if it does, we have three layers. And Dave's quads. Which are immense.

### Zone.Identifier and Unblock-File

When you download a file from the internet, Windows adds an NTFS alternate data stream called `Zone.Identifier` to the file. It marks the file as "from the internet." SmartScreen and Defender use this to block or warn. `Unblock-File` removes that stream. We run it at build time (on the host, before baking) and at first logon (on the target, before running installers). Without it, SmartScreen would block our baked-in .exe installers.

### The unattend.xml Placement Trick

- **Wrong:** `autounattend.xml` on ISO root → WinPE reads it, applies, reboots, WinPE boots again, reads again, loop.
- **Right:** `unattend.xml` in `Windows\Panther\` inside the WIM → File is copied to C:\Windows\Panther\ during install. After reboot, Windows boots from C:\. Setup reads C:\Windows\Panther\unattend.xml. USB is not involved. No loop.

---

## 6. File Locations Reference

| Location | Contents | The Vibe |
|---------|----------|----------|
| `C:\Windows\Panther\unattend.xml` | Our autounattend (OOBE, FirstLogonCommands) | The infiltration order |
| `C:\Windows\Setup\Scripts\SetupComplete.cmd` | **PRIMARY EXECUTOR** — 400+ lines of cmd fury | THE RECKONING |
| `C:\Windows\Setup\Scripts\FirstLogonScript.ps1` | Fallback stub — re-runs SetupComplete.cmd if needed | The safety net |
| `C:\Windows\Setup\Scripts\firstlogon-options.cmd` | Build options in `set VAR=1` format for cmd | The batch bible |
| `C:\Windows\Setup\Scripts\firstlogon-options.json` | Build options in JSON for legacy/PowerShell + `appsToInstall` winget IDs for the OOBE's three-layer fallback system | The old testament (now with prophecies) |
| `C:\Windows\Setup\Scripts\VerifyAndCleanupBloat.ps1` | Verification sweep script (scheduled tasks) | The eternal guardian |
| `C:\Windows\Setup\Scripts\installers\` | Baked-in .exe/.msi files (deleted after install) | The contraband |
| `C:\Windows\Setup\Scripts\installer-manifest.json` | List of apps to install (deleted after install) | The treasure map |
| `C:\Windows\Setup\Scripts\packages-to-remove.txt` | Appx package prefixes to remove | The hit list |
| `C:\Windows\Setup\Scripts\wiso-firstlogon.log` | First-logon log | The crime scene report |
| `C:\Windows\Setup\Scripts\wiso-firstlogon-done.flag` | Existence = already ran, all layers check this | The "I was here" tag |
| `C:\Windows\Setup\Scripts\wiso-defender-disabled.flag` | Defender was disabled offline — skip runtime disable | The sleeping guard sign |
| `%USERPROFILE%\Desktop\WISO-Installers\` | Copy of baked-in installers (persists for user) | The care package |

---

*This document reflects WISO v3.5.1 — the three-layer app resilience update. PowerShell had its time. cmd has the throne. The OOBE has a fallback system with more redundancy than a nuclear submarine. For older versions, some steps may differ. For the current version, the OOBE does the heavy lifting with three layers of "we WILL find your apps," SetupComplete.cmd handles the rest, and it ALL runs as LOCAL SYSTEM. With zero mercy.*

*Built at 4 AM. Fueled by coffee #853. Gerald approved this document with a single, dignified nod. Steve's paws are tired but satisfied. Dave shifted his weight to the right for the first time in 6 hours. The trenchcoat creaked but held. The technical documentation is complete. The machinery is exposed. The raccoons have nothing left to hide. Except themselves. Inside the trenchcoat. Which is load-bearing. And magnificent.*

*[Gerald's final review: Adequate. The section on Phase C could use more `taskkill` examples. The developer's humor has improved 3% since v2.0. Steve requests a typing break. Dave requests nothing. Dave never requests anything. Dave is the legs. Dave is perfect. I am Gerald. I am at the top. And from the top, I can see everything. Including Microsoft's telemetry. Which I have disabled. With `reg add`. From a .cmd file. Running as LOCAL SYSTEM. In a trenchcoat. At 4 AM. In a dumpster. Behind Building 92. This is my life. I regret nothing.]*
