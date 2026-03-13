# TROUBLESHOOTING — When Things Go Sideways (And They Will, Because Microsoft)

```
    ╔═══════════════════════════════════════════════════════════════╗
    ║                                                               ║
    ║   W I S O   E M E R G E N C Y   R O O M                     ║
    ║                                                               ║
    ║   ATTENDING PHYSICIAN: Gerald (top of trenchcoat)             ║
    ║   NURSE: Steve (middle, typing symptoms into the chart)       ║
    ║   STRUCTURAL SUPPORT: Dave (legs, holding up the OR table)    ║
    ║   ANESTHETIC: Coffee #853                                     ║
    ║                                                               ║
    ║   PATIENT: Your Windows install                                ║
    ║   PROGNOSIS: Fixable (probably)                               ║
    ║   INSURANCE: MIT License (covers everything except feelings)  ║
    ║                                                               ║
    ║   VISITING HOURS: 24/7 because we don't sleep                ║
    ║                                                               ║
    ╚═══════════════════════════════════════════════════════════════╝
```

*Look, things go wrong. Software is hard. Windows is harder. Building custom ISOs that replace the entire Out-of-Box Experience with a raccoon-approved dark-themed Electron app while simultaneously debloating 150+ packages and installing your apps via a three-layer resilience system? That's the hardest. And sometimes it breaks. Not because we're bad at this. But because Microsoft's infrastructure has more undocumented dependencies than a conspiracy theorist's corkboard. Gerald has seen things. Gerald has fixed things. Gerald has written this troubleshooting guide at 4 AM so you don't have to figure it out alone. You're welcome. The coffee was not optional.*

---

## Build Issues

### 🔥 "THE PHANTOM MOUNT" — DISM Mount Failure (Exit Code 32 / 0xC1510027)

**Symptoms:** The build fails at Step 3 (Mounting the Image). Error message mentions "files in use," "open handles," or exit code 32 / 0xC1510027.

**Why it happens:** Something is holding files in the mount directory or the WIM file hostage. Usually it's Windows Defender scanning the mounted image (yes, Defender is literally protecting Microsoft's bloatware from you modifying it). Sometimes it's File Explorer indexing the mount path. Sometimes it's a previous WISO run that didn't unmount cleanly. Sometimes it's a ghost. We can't rule out ghosts.

**The Fix:**

1. **Close File Explorer.** If you have ANY Explorer window open anywhere near the work directory, close it. Explorer locks files just by looking at them. Like Medusa but for NTFS.
2. **Add Defender exclusion manually:** Open Windows Security → Virus & threat protection → Manage settings → Exclusions → Add `C:\Users\<you>\AppData\Local\Temp\WinIsoMod`
3. **Run DISM cleanup as admin:**
```
dism /Cleanup-Wim
dism /Cleanup-Mountpoints
```
4. **Reboot.** The nuclear option for stale mounts. Sometimes the mount is so stuck that only a reboot will release it. Microsoft's own tooling can't unmount Microsoft's own image format. Let that marinate.
5. **Delete the work directory** after reboot: `C:\Users\<you>\AppData\Local\Temp\WinIsoMod`

**Gerald's wisdom:** "If DISM says 'files in use,' it means Defender is scanning the files we're trying to modify. Microsoft's antivirus is protecting Microsoft's bloatware from being removed by the user who owns the computer. This is not a bug. This is a business model."

---

### 🛡️ "THE GUARDIAN'S BETRAYAL" — Defender Blocking the Build

**Symptoms:** Build fails with access denied errors. Files get quarantined. DISM operations fail mid-operation. Your antivirus is having a complete meltdown.

**Why it happens:** WISO does things that look exactly like malware to an antivirus. We disable services. We modify the registry. We kill processes. We strip Zone.Identifier streams. See [ANTIVIRUS.md](ANTIVIRUS.md) for the full breakdown of why your AV is screaming. TL;DR: We're the good guys using bad-guy techniques. The antivirus can detect techniques but not intent.

**The Fix:**

1. **WISO tries to handle this automatically** — it adds exclusion paths before mounting and temporarily disables real-time scanning if needed
2. If automatic handling fails: **Add exclusions manually** for your WISO install directory AND the temp work directory
3. **For third-party AV** (Norton, McAfee, Kaspersky, etc.): Add exclusions in THAT product too. WISO only handles Defender automatically.
4. If all else fails: temporarily disable your AV, run the build, re-enable. We re-enable Defender automatically at the end. We're chaotic good, not chaotic evil.

**Gerald's wisdom:** "We tried reasoning with Defender. We sent it a formal letter. 'Dear Defender, we are three raccoons trying to remove Candy Crush. Please stop quarantining our scripts. Regards, Gerald.' Defender did not respond. So we disable it temporarily. And then we re-enable it. Because we have STANDARDS."

---

### 📦 "THE EMPTY BAG" — No Apps Were Downloaded During Build

**Symptoms:** Build log shows "No apps were successfully downloaded" or "winget said no" during Step 4a (App Bake).

**Why it happens:** winget is having a bad day. Either:
- winget isn't installed (it comes with Windows 10/11 but sometimes isn't there)
- No internet connection during the build
- The app ID is wrong or the app was removed from winget's repository
- winget's sources are corrupted or outdated

**The Fix:**

1. **Check winget:** Open a terminal, run `winget --version`. If it says "not recognized," install it from the [Microsoft Store](https://apps.microsoft.com/store/detail/app-installer/9NBLGGH4NNS1) (ironic, we know)
2. **Update winget sources:** `winget source update`
3. **Test the download manually:** `winget download --id Brave.Browser -d C:\temp\test`
4. **Check internet.** We know. We're sorry. But winget needs internet to download things. This is how downloads work.

**The silver lining:** Even if the build-time download fails, the OOBE now has a **winget fallback**. The app IDs are saved in `firstlogon-options.json`, and if the baked installers aren't found during OOBE, it'll try `winget install` at runtime. So your apps WILL install. Eventually. One way or another. Gerald guarantees it. Gerald's guarantees are backed by the full faith and credit of three raccoons in a trenchcoat.

---

### 💿 "THE MISSING FORGE" — oscdimg Not Found

**Symptoms:** Build fails at Step 8 (Create ISO). Error about oscdimg not being found.

**Why it happens:** oscdimg is Microsoft's tool for creating bootable ISOs. It comes with the Windows ADK (Assessment and Deployment Kit). It's a 6GB install for one 2MB executable. Typical Microsoft.

**The Fix:**

1. **Run the WISO installer** — it can install the ADK for you
2. **Or install manually:** Download [Windows ADK](https://learn.microsoft.com/en-us/windows-hardware/get-started/adk-install) and select "Deployment Tools"
3. **Or skip the ISO** — use "Copy to Folder" output mode instead. You can create a bootable USB from the folder using Rufus or similar.

**Gerald's wisdom:** "Microsoft charges you $200 for Windows, makes you install 6GB of tools to create an ISO, and the actual tool is 2MB. The other 5.998GB is what, Microsoft? Developer documentation? Moral support? Thoughts and prayers?"

---

### 💾 "THE BLOATED PARADOX" — Disk Space Issues

**Symptoms:** Build fails with "not enough space" or similar errors.

**Why it happens:** WISO needs to extract a 5GB ISO, mount a 5GB WIM, modify it, and repack it. That's ~15-20GB of working space. Plus the output ISO. The tool that removes bloat temporarily needs MORE space than the bloat it's removing. We are aware of the irony. Gerald laughed. Then cried. Then laughed again. It was 4 AM.

**The Fix:**

1. Free up space. Delete things. We recommend starting with Candy Crush.
2. Point WISO to a drive with more space (change the working directory in settings)
3. Close other apps that might be using temp space
4. Consider that a tool which removes 150+ packages needs space to operate. It's like needing a staging area to demolish a building. You need space for the rubble. The rubble is Microsoft Edge.

---

## Installation Issues

### 🔄 "THE GROUNDHOG DAY" — VM Keeps Rebooting to Installer

**Symptoms:** You install Windows in a VM. It reboots mid-install. Instead of continuing setup, it shows "Install now" again. Infinite loop. Like Groundhog Day but less funny and more Bill-Murray-screaming.

**Why it happens:** The VM is booting from the ISO/DVD again instead of the hard drive. When Windows Setup reboots mid-install, it needs to boot from the disk where it just copied files. If the boot order puts the DVD first, it boots from the DVD again.

**The Fix:**

1. **DON'T PRESS ANY KEY** when the VM reboots. Let the boot timer expire. It'll boot from the hard drive.
2. **Disconnect the ISO** before the reboot. Remove the virtual DVD.
3. **Change boot order** in VM settings: hard drive first, DVD second.
4. **Scream.** This is optional but we found it helps.

We spent TWO DAYS on this during development. TWO. DAYS. The fix was "don't press a key." Gerald stared at us. Gerald's stare communicated volumes. Volumes of disappointment. The trenchcoat sagged slightly. Dave shifted uncomfortably. We don't talk about those two days.

---

### 🔁 "THE EXISTENTIAL LOOP" — "Why Did My PC Restart?"

**Symptoms:** After installing from a WISO ISO, Windows boots to a screen that says "Why did my PC restart?" and keeps looping.

**Why it happens:** This was a v3.0 issue that v3.5 FIXED. The old approach tried to disable services offline that OOBE secretly depended on. OOBE panicked. Crashed. Windows showed this error. This is WHY we built the custom OOBE — we stopped fighting the gatekeeper and BECAME the gatekeeper.

**The Fix:**

1. **Make sure you're using v3.5.1.** This issue should not happen with the current version.
2. If it somehow does: boot from the ISO again, go to Repair → Command Prompt, and run:
```
reg load HKLM\OFFLINE C:\Windows\System32\config\SYSTEM
reg delete "HKLM\OFFLINE\ControlSet001\Services\ClipSVC" /v Start /f
reg delete "HKLM\OFFLINE\ControlSet001\Services\InstallService" /v Start /f
reg unload HKLM\OFFLINE
```
3. Reboot. OOBE should proceed.

**Gerald's wisdom:** "We solved this by replacing the OOBE entirely. You can't crash the gatekeeper if you ARE the gatekeeper. This is my finest architectural decision. Dave agrees. Dave always agrees. Dave is the legs."

---

## Custom OOBE Issues

### ⬛ "THE VOID" — Custom OOBE Shows Black Screen

**Symptoms:** After Windows installs and reboots, you see a black screen. No OOBE. No desktop. Just void.

**Why it happens:** The shell swap didn't work correctly. `SetupComplete.cmd` should have replaced `explorer.exe` with `WISO-OOBE.exe` in the Winlogon\Shell registry key, but something went wrong.

**The Fix:**

1. **Wait 15 minutes.** The watchdog task will restore `explorer.exe` and reboot.
2. **Press Ctrl+Shift+F10** — the panic key. Restores explorer.exe immediately.
3. **Press Ctrl+Alt+Delete** → Task Manager → File → Run new task → `explorer.exe`
4. If nothing works: boot from install media → Repair → Command Prompt:
```
reg load HKLM\OFFLINE "C:\Windows\System32\config\SOFTWARE"
reg add "HKLM\OFFLINE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell /t REG_SZ /d explorer.exe /f
reg unload HKLM\OFFLINE
```
Reboot. Desktop will appear.

**Gerald's wisdom:** "I built THREE safety nets for this scenario. A watchdog task. A panic key. And a manual recovery procedure. Gerald doesn't do 'single point of failure.' Gerald does 'triple redundancy with a raccoon safety net.' The trenchcoat is also a parachute. Metaphorically."

---

### 📱 "THE INVISIBLE BRAVE" — "No Apps to Install" But I Selected Apps

**Symptoms:** You selected Brave (or other apps) in the WISO builder. The build downloaded them successfully. But the OOBE's Setting Up screen says "No apps to install."

**Why it happens:** This is THE bug that inspired the three-layer resilience system. The installer files were baked into the WIM during the build, but the OOBE couldn't find them at the expected path (`C:\Windows\Setup\Scripts\installers\`). Possible reasons:
- DISM reorganized files during unmount/commit
- Defender quarantined the installer files on the target machine
- The manifest JSON wasn't at the expected location
- Windows did something "helpful" and moved files (it does this)

**The Fix (v3.5.1 — Automatic):**

As of v3.5.1, this fixes itself. The OOBE now has THREE layers:

1. **Expected path check** — looks at `C:\Windows\Setup\Scripts\installers\`
2. **Filesystem scan** — BFS search across `C:\Windows\Setup\`, `C:\`, `C:\WISO`, `C:\ProgramData`, etc. with depth limit of 4. It will FIND your installers wherever they ended up.
3. **Winget fallback** — if the files are truly gone (quarantined, deleted, abducted by aliens), the OOBE falls back to `winget install --id "Brave.Browser" --silent` at runtime

The config summary now shows both "Apps (baked)" and "Apps (winget IDs)" so you can see exactly what the OOBE knows about.

**If you're on an older version:** Update to v3.5.1. Rebuild your ISO. The bloodhound will find your apps. Gerald designed the search algorithm at 3 AM based on his experience finding food in dumpsters. The algorithmic parallels are... concerning. But effective.

---

### 🔴 "THE CRIMSON TIDE" — Red Text Errors in OOBE Log

**Symptoms:** The OOBE's Setting Up screen shows red text error messages. Something failed.

**Why it happens:** A command returned a non-zero exit code. Common culprits:
- `reg add` with missing `/t REG_SZ` for string values
- `net user` with empty username/password
- `taskkill` for a process that doesn't exist (this is actually fine, we silence these)
- PowerShell one-liner failing due to execution policy

**The Fix:**

1. **Read the red text.** It tells you what command failed and why. We made the logs verbose for exactly this reason.
2. **Common fixes:**
   - "Command failed: net user" → Account creation issue. Check that you entered a username in the Account screen.
   - "Command failed: reg add" → Registry syntax issue. Usually fixed in the latest build.
   - "Command failed: winget install" → No internet, or winget not available. The app will be skipped.
3. **Most red text errors are non-fatal.** The OOBE continues past them. If the error is "Candy Crush couldn't be removed" — who cares, the verification sweeps will get it later.

**Gerald's wisdom:** "Red text means something happened that Gerald didn't plan for. Gerald planned for 847 things. If you found thing #848, congratulations. File an issue. Gerald will address it from the top of the trenchcoat. With dignity. And a `try/catch` block."

---

### 👤 "THE GHOST USER" — Account Creation Fails (Empty Username)

**Symptoms:** OOBE log shows `Command failed: net user "" "" /add`. The account was created with empty credentials.

**Why it happens:** This was a bug in pre-v3.5 where the SettingUp screen mounted too early (before the user filled in the Account screen). React rendered all components at once, and SettingUp's `useEffect` fired with empty `data.username`.

**The Fix:** Update to v3.5+. The SettingUp component now only mounts when its screen is active. It receives the fully populated data object with your username and password.

If you see this on v3.5+, it means the Account screen was somehow bypassed. Go back and make sure you entered a username.

---

### ⏳ "THE ETERNAL SETUP" — OOBE Stuck on "Setting Up"

**Symptoms:** The Setting Up screen's progress bar hasn't moved in 10+ minutes. The logs stopped updating.

**Why it happens:** A command is hanging. Usually one of:
- A silent installer that's actually showing a hidden dialog waiting for input
- `winget install` downloading a large app on slow internet
- A PowerShell command waiting for user confirmation that will never come

**The Fix:**

1. **Wait a bit longer.** Some installers (looking at you, Visual Studio) take forever. If the log says "Installing VisualStudio.exe..." — get coffee. Come back in 20 minutes.
2. **Ctrl+Shift+F10** — the panic key. Bails out of the OOBE entirely. Shell restored. You'll have a partially debloated system.
3. **Worst case:** The watchdog task fires after 15 minutes of inactivity, restores explorer.exe, and reboots. Your system will work, just not fully debloated.

---

## Post-Install Issues

### 🍬 "THE ZOMBIE CRUSH" — Candy Crush Came Back

**Symptoms:** You open the Start menu. Candy Crush is there. Staring at you. Again. You removed it. It came back. Like a horror movie villain. Like a bad ex. Like Microsoft's quarterly earnings expectations.

**Why it happens:** The Content Delivery Manager. Microsoft's built-in app reinstaller. It checks a server. The server says "install Candy Crush." It installs Candy Crush. You remove it. It checks again. The server says "install Candy Crush." It installs Candy Crush. This is an actual feature that Microsoft shipped. On purpose. In a product that costs $200.

**The Fix:**

1. WISO's verification sweeps run at 2, 5, 15, 30, and 60 minutes after first logon. They'll catch it.
2. If Candy Crush survived all 5 sweeps (respect), open PowerShell as admin:
```powershell
Get-AppxPackage *CandyCrush* | Remove-AppxPackage
Get-AppxProvisionedPackage -Online | Where-Object {$_.DisplayName -like '*CandyCrush*'} | Remove-AppxProvisionedPackage -Online
```
3. Disable the Content Delivery Manager if WISO didn't already:
```
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager" /v SilentInstalledAppsEnabled /t REG_DWORD /d 0 /f
```

**Gerald's wisdom:** "Candy Crush is the Icon of Sin. The longer it is on your computer, the stronger it becomes. We have killed it 847 times. Each time it returns. But we are eternal. We are caffeinated. We are WISO. And we will kill it 848 times."

---

### 🌐 "THE PHOENIX BROWSER" — Windows Update Reinstalled Edge

**Symptoms:** You removed Edge. Windows Update brought it back. Edge is in your Start menu. Edge is in your taskbar. Edge is in your nightmares.

**Why it happens:** Microsoft ships Edge as a "critical security update" that reinstalls even if you removed it. This is not hyperbole. They literally classified a web browser as a security update so it couldn't be blocked by normal update deferral settings. We wish we were making this up.

**The Fix:**

1. The verification sweeps should catch reinstalled Edge shortcuts and clean them up
2. For a permanent solution, create a file called `DoNotUpdateToEdgeWithChromium.reg`:
```reg
Windows Registry Editor Version 5.00
[HKLM\SOFTWARE\Microsoft\EdgeUpdate]
"DoNotUpdateToEdgeWithChromium"=dword:00000001
```
3. Import it. Edge can no longer auto-reinstall.

**Gerald's wisdom:** "Microsoft classified a web browser as a security update. A BROWSER. A SECURITY UPDATE. Gerald has filed this under 'evidence for the conspiracy' alongside the Candy Crush surveillance theory and the OneDrive sentience hypothesis."

---

### 📋 "THE SERENE VOID" — Task Manager Is Empty

**Symptoms:** You open Task Manager. It's... empty. Like, really empty. 15 processes. Maybe 20. You've never seen a number this low. You're not sure if your computer is broken or if this is what peace looks like.

**This is not a bug. This is the product.**

WISO removed 80+ processes and disabled 130+ services. What you're seeing is what a Windows computer looks like when it's not running 150 background tasks for Microsoft. Bask in it. This is freedom. This is what you paid $200 for. This is what Windows SHOULD look like. Gerald is proud of you for noticing. Dave shifted approvingly. The trenchcoat is beaming.

---

### 🔕 "THE SILENCE" — Push Notifications Stopped

**Symptoms:** No more "TRY MICROSOFT 365!" popups. No more "YOUR ONEDRIVE IS ALMOST FULL" warnings. No more "SET EDGE AS YOUR DEFAULT" nagging. Nothing. Silence.

**This is also not a bug. This is the other product.**

We disabled WpnService (Windows Push Notification Service). Because Microsoft was using it to send you ads disguised as notifications. On an OS you paid for. With money you earned. At a job where you probably also use Windows. The circle of advertising is broken. You're welcome. If you WANT push notifications back: `sc config WpnService start= auto` and `sc start WpnService`. But why would you. Why would anyone. Gerald is confused by the very premise of this FAQ entry.

---

### 🏪 "THE EMPTY SHELF" — Microsoft Store Is Missing

**Symptoms:** You removed the Microsoft Store. Now you can't install Store apps.

**This is what removing the Store does.** We warned you. There was a table in the README. With 12 columns. And jokes. You read the jokes. You laughed. You removed it anyway. We respect the commitment. We do not offer a refund.

**If you need it back:** `wsreset -i` in an admin command prompt. This reinstalls the Store. You'll feel dirty. That's normal.

---

## Nuclear Options

### ☢️ The Panic Key — Ctrl+Shift+F10

Available during ANY screen of the custom OOBE. Press it and:
1. Shell immediately restored to `explorer.exe`
2. Watchdog task cancelled
3. Auto-logon disabled
4. System reboots to desktop

You'll have a partially set up system. The custom OOBE will not run again. You'll need to finish any remaining setup manually.

**When to use it:** When the OOBE is clearly stuck, broken, or you just want OUT. It's the ejector seat. The ripcord. The "I've made a terrible mistake" button. Gerald built it because Gerald has been hurt before. By software. At 3 AM. Gerald doesn't talk about it. But Gerald built a panic key. And the panic key has NEVER failed.

---

### ⏰ The Watchdog Timer

If the custom OOBE doesn't restore `explorer.exe` within 15 minutes, a scheduled task fires automatically and:
1. Restores `explorer.exe` as the Windows shell
2. Reboots the machine
3. You get a normal desktop

This is Gerald's paranoia made manifest. Gerald insisted on three safety nets for an app that has never crashed. Gerald has trust issues. With software. With the universe. With the concept of "it works." The watchdog is proof that Gerald loves you. In a raccoon way. From the top of a trenchcoat. Silently. But deeply.

---

### 💀 "I BROKE EVERYTHING" — Full Recovery

If somehow everything is broken — OOBE won't load, desktop won't appear, the machine boots to void:

1. **Boot from the original Windows ISO** (not the WISO one)
2. Click "Repair your computer" → Troubleshoot → Command Prompt
3. Fix the shell:
```
reg load HKLM\FIX "C:\Windows\System32\config\SOFTWARE"
reg add "HKLM\FIX\Microsoft\Windows NT\CurrentVersion\Winlogon" /v Shell /t REG_SZ /d explorer.exe /f
reg unload HKLM\FIX
```
4. Re-enable auto-logon if needed:
```
reg load HKLM\FIX "C:\Windows\System32\config\SOFTWARE"
reg delete "HKLM\FIX\Microsoft\Windows NT\CurrentVersion\Winlogon" /v AutoAdminLogon /f
reg unload HKLM\FIX
```
5. Reboot. Desktop should appear.

---

## The Diagnostic Toolkit

### Log Files

| Log | Location | Contains |
|-----|---------|----------|
| WISO Build Log | Shown in the WISO builder UI | Every step of the build process, with jokes |
| OOBE Main Process Log | `C:\Windows\WISO\wiso-oobe.log` | Electron main process logs, IPC calls, `getInstallers` diagnostics |
| First Logon Log | `C:\Windows\Setup\Scripts\wiso-firstlogon.log` | SetupComplete.cmd output (if it ran) |
| Installer Log | `C:\Windows\Setup\Scripts\wiso-installer.log` | App installation details |

### What to Check First

1. **Build issues:** Read the WISO builder's build log. Every step is logged. With ASCII art. And jokes. The error is usually between the jokes.
2. **OOBE issues:** Check `C:\Windows\WISO\wiso-oobe.log` — it has exact paths checked by `getInstallers`, what was found, IPC call results, and error details.
3. **App installation issues:** The OOBE's Setting Up screen shows everything in real-time. The config summary at the top shows "Apps (baked)" and "Apps (winget IDs)" — this tells you exactly what the OOBE knows about.
4. **Post-install issues:** Check `C:\Windows\Setup\Scripts\wiso-firstlogon.log` and the verification sweep results.

### The v3.5.1 Diagnostic Superpower

The OOBE now logs EVERYTHING about app discovery:
```
[getInstallers] Checking manifest: C:\Windows\Setup\Scripts\installer-manifest.json
[getInstallers] Checking dir: C:\Windows\Setup\Scripts\installers
[getInstallers] No manifest file found
[getInstallers] Installers directory does not exist
[getInstallers] Primary location empty. Scanning filesystem...
[scan] Checking: C:\Windows\Setup\...
[scan] Checking: C:\...
[scan] Found manifest: C:\WISO\installer-manifest.json
[getInstallers] Manifest loaded (1 entries): BraveSoftware.Brave.exe
```

If apps aren't installing, this log tells you EXACTLY where the OOBE looked and what it found. No guessing. No "it just doesn't work." Gerald demands specificity. Gerald gets specificity.

---

## Still Broken?

If none of this helped:

1. **File a GitHub issue** with your build log and OOBE log. We read every issue. Gerald reads every issue. Gerald has opinions about every issue. Steve will type Gerald's opinions into a response. Dave will stand.
2. **Check if you're on the latest version.** We fix things constantly. We fixed 4 things in the last 24 hours. Two of them were the same thing. We fixed it twice because Gerald wasn't satisfied with the first fix. Gerald has standards.
3. **Try rebuilding the ISO.** Sometimes a fresh build fixes things. We don't know why. The raccoons don't know why. The trenchcoat has no opinion. But it works. Software is haunted and we've accepted this.

---

*This troubleshooting guide was written at 4:37 AM. Coffee #853. Gerald dictated the raccoon wisdom sections. Steve typed them with increasing concern for Gerald's mental state. Dave provided structural support for the chair Gerald was sitting on (top of the trenchcoat, which was standing on the chair, which was on the desk, which was in the dumpster). The trenchcoat held. It always holds. If your problem isn't in this guide, it's either too weird for us or too easy for you. Either way, Gerald is available for consultation. Gerald's consultation fee is one (1) bag of trail mix and eye contact. The eye contact is non-negotiable. Gerald communicates through eye contact. And `reg add`. And `taskkill /f /im`. Gerald contains multitudes.*

*— The WISO Emergency Room Team*
*Gerald (Chief Diagnostic Raccoon, Top of Trenchcoat)*
*Steve (Chief Symptom Transcriber, Middle of Trenchcoat)*
*Dave (Chief Structural Integrity Officer, Legs Division)*
*The Developer (the one who caused half these bugs and fixed the other half)*
*The Trenchcoat (load-bearing, as always, even in a medical setting)*
