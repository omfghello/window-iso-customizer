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

---

## What to Do About It

### Option 1: Add WISO to Your Exclusions (Recommended)
Add the WISO installation directory and your working directory to your antivirus exclusion list before building.

### Option 2: Temporarily Disable Real-Time Protection
Disable your AV's real-time scanning during the build process, then re-enable it after. WISO does this automatically for Defender, but third-party AVs need manual intervention.

### Option 3: Submit a False Positive Report
If your AV flags the built ISO or the WISO installer itself, you can submit a false positive report:
- **Windows Defender:** https://www.microsoft.com/en-us/wdsi/filesubmission
- **VirusTotal:** Upload the file to https://www.virustotal.com and check which engines flag it
- **Your AV vendor:** Most have a false positive reporting page

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
