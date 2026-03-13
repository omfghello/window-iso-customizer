# Why Your Antivirus Is Having a Mental Breakdown

## TL;DR

**WISO is not malware.** But it does things that *look* exactly like malware to an antivirus. Here's why your AV is screaming, and why it's wrong (this time).

---

## The Full Explanation (Buckle Up)

### 1. Disabling Windows Defender Programmatically

WISO temporarily disables Windows Defender's real-time monitoring during the build process and during first-logon app installation. It does this because:

- **Defender locks files being scanned.** When DISM mounts a 5GB WIM image, Defender immediately starts scanning every file inside it. This causes `ERROR 0xC1510027` ("files in use") which makes the mount fail. The fix is to add an exclusion path *before* mounting.
- **Downloaded app installers get quarantined.** WISO downloads installers via `winget` and bakes them into the ISO. Defender sees a fresh `.exe` appear in a temp directory and assumes the worst. WISO adds exclusion paths and, if that fails, temporarily disables real-time scanning — then **re-enables it** after the installers finish.

**What antiviruses flag:** `Set-MpPreference -DisableRealtimeMonitoring $true`, registry writes to `HKLM\SOFTWARE\Policies\Microsoft\Windows Defender`, and calls to `Add-MpPreference -ExclusionPath`.

**Why it's flagged:** These are the exact same techniques malware uses to evade detection. The difference is WISO turns protection back on when it's done, and malware doesn't.

### 2. Registry Manipulation at Scale

WISO modifies hundreds of registry keys across three offline hives:

| Hive | What WISO Does | What Malware Does |
|------|---------------|-------------------|
| `SYSTEM` (ControlSet001\Services) | Sets `Start = 4` (disabled) on 80+ services | Sets `Start = 4` on security services specifically |
| `SOFTWARE` (Policies) | Disables telemetry, Cortana, Tips, Content Delivery | Disables security policies and AV |
| `NTUSER.DAT` (Default user) | Privacy settings, UI tweaks, disables Spotlight | Hijacks shell settings, startup entries |

**Why it's flagged:** Bulk registry modification — especially to service start types and policy keys — is a massive red flag. WISO touches the same registry paths that ransomware and rootkits target. The difference is intent: WISO disables telemetry services, while malware disables security services.

### 3. Killing Processes by Name

The first-logon script contains a list of 80+ process names and systematically kills them with `Stop-Process -Force`. This includes:

- `OneDrive.exe`, `msedge.exe`, `Teams.exe`, `Cortana.exe`
- `DiagTrack.exe`, `CompatTelRunner.exe`, `WerFault.exe`
- Various telemetry and update processes

**Why it's flagged:** Process termination lists are a hallmark of ransomware, which kills processes to unlock files for encryption. WISO's list only targets bloatware and telemetry — never `explorer.exe`, `dwm.exe`, `lsass.exe`, `csrss.exe`, or any other core OS process.

### 4. Service Disabling via sc.exe / Set-Service

WISO disables services both offline (via registry) and online (via `Set-Service -StartupType Disabled` and `Stop-Service -Force`). Over 100 services are targeted across both phases.

**Why it's flagged:** Disabling services is what worms do to kill security software. WISO never disables `WinDefend`, `WdNisSvc`, `WdFilter`, or any other Defender service. It also never disables core OS services like `RpcSs`, `EventLog`, `Dhcp`, etc.

### 5. DISM Image Manipulation

WISO mounts Windows installation images (`.wim`/`.esd`), modifies their contents, and saves them. This includes:

- Removing provisioned Appx packages
- Deleting scheduled task XML files
- Injecting scripts into `Windows\Setup\Scripts`
- Loading and modifying offline registry hives

**Why it's flagged:** Modifying Windows installation media is extremely unusual behavior for legitimate software. It looks like a supply chain attack — intercepting and tampering with OS images before deployment.

### 6. `autounattend.xml` and FirstLogonCommands

WISO generates an `autounattend.xml` answer file that:

- Creates a local admin account
- Skips OOBE (the "sign in with Microsoft" screens)
- Runs a script at first logon via `FirstLogonCommands`

**Why it's flagged:** Answer files with auto-created admin accounts and first-logon script execution are used in automated attack chains. The `RequiresUserInput=false` flag tells Windows to run the command without asking the user — exactly what a backdoor would do.

### 7. Scheduled Task Creation

WISO creates 5 scheduled tasks (`WISO_BloatVerify2`, `5`, `15`, `30`, `60`) that run PowerShell scripts at timed intervals after first logon.

**Why it's flagged:** Persistence via scheduled tasks is one of the most common malware techniques (MITRE ATT&CK T1053.005). WISO's tasks only run `VerifyAndCleanupBloat.ps1` to catch re-provisioned bloatware, but the AV doesn't know that.

### 8. PowerShell with `-ExecutionPolicy Bypass -WindowStyle Hidden`

Every PowerShell invocation in WISO uses:
```
powershell.exe -ExecutionPolicy Bypass -NoProfile -WindowStyle Hidden -File "script.ps1"
```

**Why it's flagged:** This is literally the #1 most-flagged PowerShell command line in every AV engine on earth. `-ExecutionPolicy Bypass` disables script signing requirements. `-WindowStyle Hidden` makes the window invisible. This is the default for every PowerShell-based dropper, loader, and backdoor in existence.

**Why WISO uses it:** Because showing a PowerShell window during first logon is ugly and confusing to the user. And because Microsoft's default execution policy blocks scripts that aren't signed, and WISO's scripts aren't signed (we're not paying Microsoft $300/year for a code signing cert to debloat their own OS).

### 9. File Deletion at System Paths

WISO deletes files from:
- `C:\Program Files\Microsoft\Edge\`
- `C:\Program Files\Microsoft OneDrive\`
- `C:\Windows\SystemApps\` (specific bloat apps only)
- Start Menu shortcuts
- Scheduled task XML files in `C:\Windows\System32\Tasks\`

**Why it's flagged:** Deleting system files is what wipers and destructive malware do. WISO only deletes known bloatware — never system-critical files.

### 10. `Unblock-File` and Zone.Identifier Stripping

WISO calls `Unblock-File` on all baked-in installer files to remove the NTFS Zone.Identifier alternate data stream (the "this file came from the internet" flag).

**Why it's flagged:** Stripping Zone.Identifier is a known technique to bypass SmartScreen and Mark-of-the-Web (MotW) protections. WISO does it because the files were already trusted by the user when they selected them for installation.

### 11. Filesystem Scanning (Breadth-First Search Across Directories)

The WISO OOBE now includes a `getInstallers` function that performs a breadth-first search across multiple directories to locate baked-in installer files. It uses `fs.readdirSync` to enumerate files and subdirectories across:

- `C:\Windows\Setup\Scripts\` (and subdirectories)
- `C:\Windows\Setup\`
- `C:\` (root)
- `C:\WISO`
- `C:\ProgramData`

The scan has a depth limit of 4 directories. It reads directory listings, checks file extensions, and logs every match it finds.

**What antiviruses flag:** Rapid, recursive directory enumeration via `fs.readdirSync` across multiple system paths. Some AV engines classify this as "suspicious filesystem enumeration" or "reconnaissance behavior."

**Why it's flagged:** Malware often scans the filesystem to locate sensitive files, configuration data, or other targets. A process systematically enumerating multiple directories — especially system directories like `C:\Windows\` and `C:\ProgramData` — looks like the discovery phase of an attack (MITRE ATT&CK T1083 — File and Directory Discovery). WISO is doing this to find its OWN installer files that were baked into the ISO during build. It's looking for Firefox.exe, not passwords.txt.

### 12. Winget Execution at Runtime

If baked-in installers can't be found on disk (Layer 1 expected path check and Layer 2 filesystem scan both fail), the OOBE falls back to executing `winget install` commands at runtime. This spawns PowerShell processes that invoke winget to download and install software packages from the internet.

**What antiviruses flag:** Child processes spawning `winget.exe` or `powershell.exe` to download and execute software from external sources. The process chain looks like: `WISO-OOBE.exe` → `powershell.exe` → `winget.exe` → download → install.

**Why it's flagged:** Software that spawns child processes to download and install additional executables from the internet is a textbook dropper/loader pattern. AV engines see "process downloads .exe from internet and runs it" and rightfully get nervous. The difference: WISO is installing Firefox, VLC, and 7-Zip — apps the user explicitly selected during setup — using Microsoft's own package manager (`winget`). Every winget ID comes from `firstlogon-options.json`, which was generated from the user's build choices. Nothing is installed that the user didn't choose. Winget itself verifies package integrity. But your AV doesn't know any of that. Your AV sees "unauthorized software installation" and starts sweating.

---

## Exact Commands Run (Full Transparency)

So you know *exactly* what executes on the target machine at first logon, here are the full command lines. You can verify these against the source code.

### 1. Autounattend FirstLogonCommands (what Windows Setup runs)

```
cmd /min /c "%SystemRoot%\Setup\Scripts\RunFirstLogon.cmd"
```

- `cmd` — Windows Command Processor
- `/min` — (Note: `/min` is not a valid cmd.exe flag; it's ignored. Window hiding is done by the child process.)
- `/c` — Execute the following command string, then exit
- `%SystemRoot%` — Typically `C:\Windows`, so the full path is `C:\Windows\Setup\Scripts\RunFirstLogon.cmd`

### 2. RunFirstLogon.cmd (what the batch file does)

The batch file sets up a Run key for retries, waits for the script to exist, then invokes PowerShell:

```
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Bypass -NoProfile -WindowStyle Hidden -File "C:\Windows\Setup\Scripts\FirstLogonScript.ps1" >> "C:\Windows\Setup\Scripts\wiso-firstlogon.log" 2>&1
```

- `-ExecutionPolicy Bypass` — Allows unsigned scripts to run
- `-NoProfile` — Skips loading the user's PowerShell profile
- `-WindowStyle Hidden` — No visible PowerShell window
- `-File "..."` — Runs `FirstLogonScript.ps1` (the main first-logon logic)
- `>> "...log" 2>&1` — Appends stdout and stderr to the log file

### 3. Run key (if script not found yet, retry at next logon)

```
"C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -ExecutionPolicy Bypass -NoProfile -WindowStyle Hidden -File "C:\Windows\Setup\Scripts\FirstLogonScript.ps1"
```

This is stored in `HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce` as `WISO_FirstLogon_Retry` only if the script file doesn't exist within 30 seconds (first boot can be slow).

### 4. Child process for app installers (from FirstLogonScript.ps1)

When running baked-in app installers, FirstLogonScript.ps1 spawns:

```
C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -ExecutionPolicy Bypass -NoProfile -File "C:\Windows\Setup\Scripts\RunLocalInstallers.ps1"
```

### 5. Scheduled verification sweeps (2, 5, 15, 30, 60 min after first logon)

```
"C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -ExecutionPolicy Bypass -NoProfile -WindowStyle Hidden -File "C:\Windows\Setup\Scripts\VerifyAndCleanupBloat.ps1"
```

### 6. Winget fallback installs (if baked-in installers not found on disk)

```
winget install --id <PackageId> --silent --accept-source-agreements --accept-package-agreements
```

- `winget` — Windows Package Manager (Microsoft's own package manager)
- `install --id <PackageId>` — Installs the specific package by its winget ID (e.g., `Mozilla.Firefox`, `VideoLAN.VLC`)
- `--silent` — No UI, no prompts
- `--accept-source-agreements` — Automatically accepts source repository agreements
- `--accept-package-agreements` — Automatically accepts package license agreements
- Only runs if the baked-in installer could not be found via expected path check or filesystem scan (Layer 3 fallback)
- Package IDs come from the `appsToInstall` array in `firstlogon-options.json`, generated from the user's build selections

All scripts are plaintext and live in `C:\Windows\Setup\Scripts\`. Logs are written to `wiso-firstlogon.log` and `wiso-installer.log` in the same folder.

---

## What to Do About It

### Option 1: Add WISO to Your Exclusions (Recommended)
Add the WISO installation directory and your working directory to your antivirus exclusion list before building. On the **target machine**, you may also need to whitelist `C:\Windows\Setup\Scripts\` — this is where the OOBE's filesystem scanner runs from and where baked-in installers live. If your AV flags the OOBE's directory enumeration (Section 11) or winget installs (Section 12), adding this path to exclusions will prevent false positives during first boot.

### Option 2: Temporarily Disable Real-Time Protection
Disable your AV's real-time scanning during the build process, then re-enable it after. WISO does this automatically for Defender, but third-party AVs need manual intervention. **Note:** On the target machine, the OOBE may trigger AV alerts when it scans the filesystem for installers (rapid `fs.readdirSync` calls across system directories) or when it spawns `winget install` processes as a fallback. The baked-in Defender policy keys (Stage 2) suppress Defender during this phase, but third-party AVs on the target machine may still react.

### Option 3: Submit a False Positive Report
If your AV flags the built ISO or the WISO installer itself, you can submit a false positive report:
- **Windows Defender:** https://www.microsoft.com/en-us/wdsi/filesubmission
- **VirusTotal:** Upload the file to https://www.virustotal.com and check which engines flag it
- **Your AV vendor:** Most have a false positive reporting page

### New in this version: Behaviors that may trigger additional alerts
The OOBE now performs two additional operations that some AV engines may flag:
1. **Filesystem enumeration** — The `getInstallers` function uses `fs.readdirSync` to recursively scan multiple directories (up to depth 4) looking for baked-in installer files. This rapid directory enumeration across `C:\Windows\`, `C:\`, `C:\WISO`, and `C:\ProgramData` may be classified as reconnaissance/discovery behavior.
2. **Runtime software installation via winget** — If baked-in installers aren't found on disk, the OOBE executes `winget install` commands to download and install apps from the internet. The process chain (`WISO-OOBE.exe` → `powershell.exe` → `winget.exe` → download → install) resembles a dropper/loader pattern to AV heuristics. Both behaviors are legitimate, user-initiated, and logged. If your AV flags them, the exclusion path `C:\Windows\Setup\Scripts\` and whitelisting `winget.exe` should resolve it.

---

## Why We Don't Sign the Binaries

Code signing certificates cost $200-$400/year. WISO is a free, open-source project. We're not paying Microsoft (the company whose bloatware we're removing) for the privilege of being "trusted" by their OS. The irony would be too much.

If you want to verify the build yourself: clone the repo, read the source code, and run `npm run build`. What you see is what you get.

---

## The Bottom Line

WISO uses the same tools and techniques as malware — but for opposite reasons. Malware disables security to *attack* your system. WISO disables bloatware to *free* your system. Unfortunately, antivirus engines can't read intent. They see the technique and assume the worst.

Every single thing WISO does is:
1. **Reversible** — services can be re-enabled, registry keys can be restored
2. **Transparent** — all scripts are plaintext PowerShell, all logs are saved to `C:\Windows\Setup\Scripts\`
3. **User-initiated** — nothing runs without the user explicitly choosing to build a custom ISO
4. **Open source** — read every line yourself at the project repository
