# How WISO Works — The Complete Technical Deep Dive

This document explains *exactly* what WISO does, in what order, and why. Every step, every file, every registry key. No hand-waving. No "it just works." If you want to understand the machinery, read on.

---

## Table of Contents

1. [High-Level Architecture](#1-high-level-architecture)
2. [Phase A: Build (On Your PC)](#2-phase-a-build-on-your-pc)
3. [Phase B: Installation (On Target Machine)](#3-phase-b-installation-on-target-machine)
4. [Phase C: First Logon (The Reckoning)](#4-phase-c-first-logon-the-reckoning)
5. [Key Technical Concepts](#5-key-technical-concepts)
6. [File Locations Reference](#6-file-locations-reference)

---

## 1. High-Level Architecture

WISO operates in three distinct phases:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  PHASE A: BUILD (your PC, Node.js/Electron)                                 │
│  • Extract or use folder with Windows installer                             │
│  • Mount WIM via DISM                                                       │
│  • Modify registry hives, remove Appx, disable services, inject scripts    │
│  • Unmount, create ISO                                                      │
│  Output: Custom Windows ISO                                                 │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  PHASE B: INSTALLATION (target machine, Windows Setup)                      │
│  • Boot from USB/ISO (WinPE)                                                 │
│  • Copy files to disk                                                       │
│  • Reboot into full Windows                                                 │
│  • OOBE runs (or is skipped via autounattend)                              │
│  • First user logon                                                          │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│  PHASE C: FIRST LOGON (target machine, our scripts)                          │
│  • FirstLogonCommands triggers RunFirstLogon.cmd                            │
│  • RunFirstLogon.cmd invokes FirstLogonScript.ps1                            │
│  • Process massacre, service nuke, OneDrive kill, Appx purge, app install   │
│  • Schedule verification sweeps                                             │
│  Output: Clean, debloated Windows                                           │
└─────────────────────────────────────────────────────────────────────────────┘
```

**Critical insight:** Most of the "heavy lifting" happens in Phase A (offline, in the WIM). Phase C only handles what *cannot* be done offline: killing running processes, uninstalling OneDrive via its setup executable, running `.exe`/`.msi` installers (which need a live OS), and configuring power plans. Everything else — registry, services, scheduled tasks, Appx deprovisioning — is baked into the image during the build.

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
- `FirstLogonCommands`: `cmd /min /c "%SystemRoot%\Setup\Scripts\RunFirstLogon.cmd"` (and optionally a BitLocker reg command)
- `International` (input locale, UI language, etc.)

**Script injection:** We create `Mount\Windows\Setup\Scripts\` and copy:

- `RunFirstLogon.cmd` — Batch launcher
- `FirstLogonScript.ps1` — Main first-logon logic
- `RunLocalInstallers.ps1` — Runs baked-in app installers
- `VerifyAndCleanupBloat.ps1` — Used by scheduled tasks
- `registry-tweaks.reg` — Optional; some setups import this

**firstlogon-options.json:** We write a JSON file with the user's build options: `removeBloatware`, `privacyTweaks`, `gamingMode`, `disableGameBar`, `ultraPerformance`, `highPerformancePower`, `disableWindowsSpotlight`, `hideTaskbarSearch`, `defenderExclusionsOnly`. FirstLogonScript.ps1 reads this at runtime to decide what to do.

**packages-to-remove.txt:** If `removeBloatware` is on, we copy our default list (or the user's custom list) to `Setup\Scripts\packages-to-remove.txt`. The first-logon script and VerifyAndCleanupBloat use this for Appx removal.

### Step 4a: App Download and Bake

If the user selected apps to install (`appsToInstall`), we run `winget download --id <appId> -d <dir>` for each. `winget download` fetches the installer (e.g. Firefox.exe, 7zip.msi) to a temp directory. We then:

1. Copy each installer to `Mount\Windows\Setup\Scripts\installers\`
2. Write `installer-manifest.json` with `[{ "appId": "...", "filename": "..." }, ...]`
3. Run `Unblock-File` on all files in the installers dir (strips Zone.Identifier NTFS streams so SmartScreen won't block them on the target)

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

## 4. Phase C: First Logon (The Reckoning)

### 4.1 RunFirstLogon.cmd

The batch file:

1. **Registers a Run key** so that if the script doesn't complete (e.g. reboot mid-run), it will run again at next logon: `HKCU\...\Run` = `cmd /min /c "C:\Windows\Setup\Scripts\RunFirstLogon.cmd"`

2. **Waits for FirstLogonScript.ps1 to exist** — first boot can be slow, files might not be fully written. It loops up to 15 times (2 sec each = 30 sec).

3. **Invokes PowerShell:**
   ```
   C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Bypass -NoProfile -WindowStyle Hidden -File "C:\Windows\Setup\Scripts\FirstLogonScript.ps1" >> "C:\Windows\Setup\Scripts\wiso-firstlogon.log" 2>&1
   ```

4. **If script not found after 30s:** Writes to RunOnce `WISO_FirstLogon_Retry` with the same PowerShell command, so it runs at next logon.

### 4.2 FirstLogonScript.ps1 — Phases

**Guard:** If `wiso-firstlogon-done.flag` exists, the script removes itself from Run/RunOnce and exits. It only runs once.

**Options load:** Reads `firstlogon-options.json` to get `removeBloatware`, `privacyTweaks`, `gamingMode`, etc.

**Defender:** Tries `Add-MpPreference -ExclusionPath` for the script dir. If that fails and `defenderExclusionsOnly` is false, calls `Disable-WisoDefender` (same as builder: disables real-time, IOAV, behavior, script scanning, plus registry policies). Writes a flag file. At the end of the script, we re-enable Defender and delete the flag.

**Phase 0 — Process Massacre:** If `removeBloatware` or `privacyTweaks`, we iterate over 80+ process names and `Stop-Process -Force` on each. We never touch explorer, dwm, csrss, lsass, svchost, SearchApp, StartMenuExperienceHost, ShellExperienceHost. We conditionally skip Xbox/GameBar processes if `gamingMode` is on or `disableGameBar` is off.

**Phase 0b — Post-OOBE Service Annihilation:** We disable 50+ services that we had to protect during OOBE: ClipSVC, InstallService, AppReadiness, TokenBroker, NgcCtnrSvc, NgcSvc, UsoSvc, WaaSMedicSvc, etc. We use `Set-Service -StartupType Disabled` and also write `Start=4` directly to the registry. We disable per-user service instances (OneSyncSvc_*, CDPUserSvc_*, etc.). We conditionally skip Xbox services if `gamingMode`, and Game DVR if `disableGameBar` is off. We run `schtasks /change /disable` on 18 telemetry tasks.

**Phase 1a — OneDrive:** If `removeBloatware`, we taskkill OneDrive processes, run OneDriveSetup.exe /uninstall from multiple locations, winget uninstall Microsoft.OneDrive, Remove-AppxPackage *OneDrive*, Remove-AppxProvisionedPackage *OneDrive*, delete OneDrive folders, remove Run keys.

**Phase 1b — Power plan:** If `ultraPerformance`, we duplicate the "Ultimate Performance" scheme, set it active, disable core parking, set PROCTHROTTLEMIN=100, Disable-MMAgent -mc. If `highPerformancePower`, we activate the High Performance scheme.

**Phase 1c — Online Appx removal:** If `removeBloatware`, we run 3 passes of Remove-AppxProvisionedPackage and Remove-AppxPackage for packages matching our list. We also delete Edge folders, start menu shortcuts for bloat, etc.

**Phase 2 — Schedule verification sweeps:** We create 5 scheduled tasks: `WISO_BloatVerify2`, `5`, `15`, `30`, `60` — each runs VerifyAndCleanupBloat.ps1 at that many minutes from now. One-time (`/sc once`).

**Phase 3 — App installers:** We copy installers from `Setup\Scripts\installers\` to `Desktop\WISO-Installers\`, Unblock-File on everything, add Defender exclusions, then spawn a child PowerShell process to run `RunLocalInstallers.ps1`. We wait for it. If it fails to launch, we queue it for RunOnce.

**Phase 4 — Second process massacre:** We kill the same processes again (respawn check).

**Phase 5 — Cleanup:** Remove Run/RunOnce keys, re-enable Defender if we disabled it, write the done flag.

### 4.3 RunLocalInstallers.ps1

Reads `installer-manifest.json`. For each entry:

- **MSI:** `msiexec /i "path" /quiet /norestart /qn` (or interactive fallback)
- **MSIX/APPX:** `Add-AppxPackage -Path path`
- **EXE:** Tries 7 silent flag combinations (`/VERYSILENT`, `/S`, `/silent`, etc.), then interactive. Logs every attempt.

After all installs, we re-enable Defender (if we disabled it), delete the installers dir and manifest to free space. The Desktop copies remain so the user can re-run installers manually.

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

### Why Some Things Must Be Done at First Logon

- **Process killing:** Processes don't exist until Windows is running. We can't kill OneDrive.exe from inside a WIM.
- **OneDrive uninstall:** OneDriveSetup.exe /uninstall must run in a live OS. We can delete the OneDrive *files* from the image, but the Appx package and scheduled tasks may persist. The first-logon script does the full uninstall.
- **EXE/MSI installers:** They require a running OS to execute. We bake them into the image and run them at first logon.
- **Power plan:** powercfg requires a live OS.
- **Appx removal (online):** Windows re-provisions some packages during OOBE or first boot. Our offline removal gets most of them, but we do 3 passes at first logon to catch stragglers.

### Zone.Identifier and Unblock-File

When you download a file from the internet, Windows adds an NTFS alternate data stream called `Zone.Identifier` to the file. It marks the file as "from the internet." SmartScreen and Defender use this to block or warn. `Unblock-File` removes that stream. We run it at build time (on the host, before baking) and at first logon (on the target, before running installers). Without it, SmartScreen would block our baked-in .exe installers.

### The unattend.xml Placement Trick

- **Wrong:** `autounattend.xml` on ISO root → WinPE reads it, applies, reboots, WinPE boots again, reads again, loop.
- **Right:** `unattend.xml` in `Windows\Panther\` inside the WIM → File is copied to C:\Windows\Panther\ during install. After reboot, Windows boots from C:\. Setup reads C:\Windows\Panther\unattend.xml. USB is not involved. No loop.

---

## 6. File Locations Reference

| Location | Contents |
|---------|----------|
| `C:\Windows\Panther\unattend.xml` | Our autounattend (OOBE, FirstLogonCommands) |
| `C:\Windows\Setup\Scripts\RunFirstLogon.cmd` | Batch launcher |
| `C:\Windows\Setup\Scripts\FirstLogonScript.ps1` | Main first-logon logic |
| `C:\Windows\Setup\Scripts\RunLocalInstallers.ps1` | App installer runner |
| `C:\Windows\Setup\Scripts\VerifyAndCleanupBloat.ps1` | Verification sweep script |
| `C:\Windows\Setup\Scripts\installers\` | Baked-in .exe/.msi files (deleted after install) |
| `C:\Windows\Setup\Scripts\installer-manifest.json` | List of apps to install (deleted after install) |
| `C:\Windows\Setup\Scripts\firstlogon-options.json` | Build options for first-logon script |
| `C:\Windows\Setup\Scripts\packages-to-remove.txt` | Appx package prefixes to remove |
| `C:\Windows\Setup\Scripts\wiso-firstlogon.log` | First-logon log |
| `C:\Windows\Setup\Scripts\wiso-installer.log` | App installer log |
| `C:\Windows\Setup\Scripts\wiso-firstlogon-done.flag` | Existence = script already ran, skip |
| `%USERPROFILE%\Desktop\WISO-Installers\` | Copy of baked-in installers (persists for user) |

---

*This document reflects WISO v3.0.0. For older versions, some steps may differ.*
