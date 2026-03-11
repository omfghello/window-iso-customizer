# WISO — Windows ISO Customizer

### The app Microsoft doesn't want you to have.

We're not saying Windows 11 ships with more bloatware than actual features. We're *heavily implying* it.

WISO lets you rip out Candy Crush, Copilot, Edge, the entire OOBE guilt trip, and about 150 other things Microsoft pre-installed "for your convenience" — then bake in the apps you actually want, all wrapped in a shiny bootable ISO. No internet needed on the target machine. No Microsoft account required. No Cortana asking if you need help. (You don't. She doesn't help.)

> **TL;DR:** You give us a Windows ISO. We give you back a Windows ISO that doesn't suck.

---

## Downloads

| File | What it is |
|------|-----------|
| **`WISO-Setup-x.x.x.exe`** | Installer (auto-installs Windows ADK + winget because Microsoft can't make up their mind on packaging) |
| **`win-unpacked/WISO.exe`** | Portable — for people who don't trust installers. We respect that. We made an installer. The irony is not lost on us. |

Grab them from [Releases](../../releases).

> Requires **Administrator** rights. Yes, you need admin to fix an OS you already own. Thanks, Microsoft.

---

## Where do baked-in apps get installed?

When you select apps in **"Apps to Install"** and build an ISO, WISO downloads them on your PC via winget and shoves them directly into the Windows image. Microsoft: "Use the Store!" Us: "We're smuggling Firefox into your ISO like contraband."

**No internet needed on the target machine.** The apps are IN the ISO. Like a surprise, except it's a surprise you actually wanted.

### On your build PC (during the build)

```
C:\Users\<you>\AppData\Local\Temp\WinIsoMod\
├── app-downloads\              <-- winget downloads here (deleted after, we clean up after ourselves unlike Microsoft)
│   ├── Brave.Brave\
│   │   └── BraveBrowserSetup.exe
│   ├── Mozilla.Firefox\
│   │   └── FirefoxSetup.exe
│   └── ...
├── Mount\                      <-- The mounted Windows image
│   └── Windows\
│       └── Setup\
│           └── Scripts\
│               ├── installers\              <-- Your apps get stashed here
│               │   ├── Brave.Brave.exe
│               │   ├── Mozilla.Firefox.exe
│               │   └── ...
│               ├── installer-manifest.json  <-- The treasure map
│               ├── RunLocalInstallers.ps1   <-- The treasure hunter
│               ├── FirstLogonScript.ps1     <-- The bouncer (kicks out bloatware)
│               └── ...
└── sources\
    └── install.wim             <-- Everything gets committed into this 5GB chonker
```

### On the target PC (after installing Windows from your ISO)

```
C:\Windows\Setup\Scripts\
├── installers\                    <-- YOUR apps. Right here. You're welcome.
│   ├── Brave.Brave.exe
│   ├── Mozilla.Firefox.exe
│   └── ...
├── installer-manifest.json        <-- "Dear Windows, install these. Love, WISO"
├── RunLocalInstallers.ps1         <-- Runs at first logon via RunOnce
├── FirstLogonScript.ps1           <-- The big one. Disables 30+ services. Microsoft hates this one weird trick.
├── VerifyAndCleanupBloat.ps1      <-- Because Windows will TRY to reinstall Candy Crush. We'll be waiting.
├── firstlogon-options.json        <-- Your build settings
└── packages-to-remove.txt        <-- The hit list
```

**What happens at first logon:**

1. `FirstLogonScript.ps1` runs hidden — disables telemetry services, applies registry tweaks, removes bloatware. Microsoft: "We're collecting diagnostics." Script: *pulls ethernet cable*
2. Registers `RunLocalInstallers.ps1` via `RunOnce` so your apps install after the desktop appears
3. Each installer launches normally so you can click through setup (we're not animals, we let you choose your install path)
4. After all installers finish, the `installers\` folder self-destructs to free disk space
5. `VerifyAndCleanupBloat.ps1` runs as a scheduled task because Windows will absolutely try to re-install stuff. It's like whack-a-mole but with Candy Crush.

---

## What gets removed

WISO removes **150+ packages**. Microsoft pre-installed them. We un-pre-install them. Everything is deselectable in the GUI because we believe in freedom. Unlike Windows OOBE.

### Microsoft bloat

| Category | What | Why it's here |
|----------|------|---------------|
| **Edge** | Edge, Edge DevTools, Widgets, Web Experience Pack | The browser that exists to download other browsers. We're skipping the middleman. |
| **Copilot / AI** | Copilot, AI Hub, Recall, Cortana | Microsoft put an AI in your taskbar. We put it in the recycle bin. Recall literally screenshots everything you do. No thanks. |
| **Microsoft Store** | Store app, Store Purchase | The OS works fine without it. Like a car works fine without a dealership bolted to the hood. |
| **Office trials** | Office Hub, OneNote, Sway, Outlook, full Desktop suite | "Try Office for free!" It's a 30-day trial. On an OS you paid for. Think about that. |
| **Teams** | Teams, Skype, Meet Now | Teams was in the taskbar. Then the system tray. Then the startup. It's like a clingy ex. We're helping you move on. |
| **OOBE junk** | Cloud Experience Host, Network Flow, Accounts Control, AAD Broker | The apps that force you to sign in with a Microsoft account. We disabled them. You're welcome. |
| **Bing everything** | News, Weather, Finance, Sports, Food, Health, Travel, Translator, Search | Microsoft really said "what if Bing, but 10 apps?" Nobody asked for this. |
| **Ads & Suggestions** | Content Delivery Manager, Bing Wallpapers, Advertising XAML | Your $200 OS comes with ads. Let that sink in. We're removing the sink. |
| **OneDrive** | OneDrive client | "Your files, everywhere!" Our ISO, our rules. You're not in it. |
| **Productivity bloat** | Sticky Notes, To Do, Whiteboard, Feedback Hub, Get Help, Power Automate, Journal, PC Manager | Apps you've never opened. Apps your grandma's never opened. Apps that don't even know what they do. |
| **Media** | Zune Video/Music, Camera, Sound Recorder, Alarms, Solitaire, Clipchamp | Zune. Microsoft named their media app after Zune. In 2024. We can't even. |
| **3D / Mixed Reality** | 3D Builder, 3D Viewer, Print 3D, Mixed Reality Portal | Did you buy a 3D printer? No? Then why are there three 3D apps on your PC? |
| **Xbox / Gaming** | Xbox app, Game Bar, Gaming Services, Game Overlay | Kept if you use the Gaming preset. Removed otherwise. We're gamers too — we'll install what WE want. |
| **Dev Home** | Dev Home + GitHub/Azure extensions | Microsoft's "we're cool with developers" app. Developers: "we already have VS Code." |
| **Family / Assist** | Family Safety, Quick Assist, Parental Controls | Quick Assist: for when your parents call and you need to remote in to remove Candy Crush. Oh wait, we already did that. |
| **Backup / Chat** | Windows Backup, Windows Chat (taskbar Teams) | Windows Backup backs up to... OneDrive. Which we also removed. It's the circle of uninstall. |

### Third-party preinstalled garbage

Microsoft gets PAID to put these on your PC. You paid for Windows. And then Windows serves you... ads. For apps. That you didn't ask for. On the computer. That you own.

| Category | The accused |
|----------|-----------|
| **Mobile games on a PC** | Candy Crush (ALL of them — Saga, Soda, Friends), Bubble Witch, Asphalt 8, March of Empires, FarmVille, Royal Revolt, Hidden City, Disney Magic Kingdoms, Roblox |
| **Streaming apps nobody asked for** | Netflix, Spotify, Hulu, Disney+, Amazon Prime, Sling TV, Plex, TuneIn, Pandora, iHeartRadio |
| **Social media shortcuts** | TikTok, Facebook, Instagram, Twitter, LinkedIn, Viber, Flipboard |
| **The rest** | Duolingo (the owl can't reach you here), Adobe Photo Express, PicsArt, Shazam, McAfee, Norton (antivirus you didn't buy), WinZip (it's 2026, use 7-Zip), Fitbit, DrawboardPDF, AutodeskSketchBook |

### What we DON'T touch (because we're not monsters)

- **Windows Defender** — it's actually good now. We know. We're shocked too.
- **Calculator** — it calculates. It's fine. Leave it alone.
- **Photos** — it opens photos. That's all we ask of it.
- **Desktop App Installer** — that's winget. We literally need it.
- **Start Menu / Shell / Search** — removing these turns Windows into a very expensive screensaver.
- **.NET, VCLibs, UI.Xaml, DirectX** — frameworks. Remove these and nothing works. We learned this the hard way so you don't have to.

---

## OOBE behavior

By default, WISO **nukes the entire OOBE** (Out-of-Box Experience). You know the one — where Microsoft spends 15 minutes asking you to sign in, connect to Wi-Fi, enable location services, choose a privacy level (there are no good options), set up Cortana (no), configure OneDrive (NO), and agree to share your firstborn with the Microsoft Advertising Network.

**Gone. All of it.**

- No Microsoft account prompts ("But you need a Microsoft account for—" No.)
- No Cortana setup (she's been deprecated anyway, even Microsoft gave up)
- No privacy questions (the answer was "no" to all of them)
- No "Let's connect you to a network" (aka "let us check if your hardware is good enough for Windows 11 before we let you install the thing you already booted from")
- Creates a local admin account automatically (name + password configurable in the GUI)

**The disk/partition selection screen is always shown** — that's part of Windows Setup, not OOBE. We're not THAT aggressive.

If you actually want the full OOBE experience (we won't judge, but we will silently wonder why), uncheck **"Skip OOBE setup wizard"** in the Options step.

---

## Features

### Debloat
- Remove 150+ pre-installed AppX packages (Microsoft: "We curated these!" Us: "We un-curated them.")
- Remove Microsoft Edge, OneDrive, Cortana, Copilot, Microsoft Store
- Remove forced Microsoft account prompts and OOBE cloud experience
- Disable 30+ telemetry services, scheduled tasks, and data collection
- Two-pass bloatware verification catches apps Windows tries to re-install (yes, it tries. repeatedly.)

### Privacy
- Disable telemetry, activity tracking, WiFi hotspot reporting
- Disable Windows Error Reporting ("Send error data to Microsoft?" How about no.)
- Disable Customer Experience Improvement Program (they're not improving our experience, they're improving their ad targeting)
- Block advertising ID — yes, your OS has an advertising ID. No, we're not making that up.
- Registry-level privacy hardening (HKLM + HKCU) — we go deep

### Performance
- Disable SysMain/Superfetch, Prefetcher, Fast Startup (Fast Startup is neither fast nor a startup. Discuss.)
- High Performance or Ultimate Performance power plan
- Core parking disabled, memory compression disabled (Ultimate mode) — your CPU was holding back. We're removing the governors.
- NTFS last-access timestamp optimization
- Disable reserved storage (~7GB reclaimed — Microsoft was hoarding 7GB of your disk "just in case." In case of what? Candy Crush updates?)

### Customization
- Skip OOBE — straight to desktop (default, because OOBE is a hostage negotiation)
- Create local admin account with custom name/password
- Set computer name, regional options, language
- Bypass TPM/Secure Boot/RAM checks (Microsoft: "You need TPM 2.0." Us: *deletes one DLL.* Microsoft: "Wait, that's illegal.")
- Disable BitLocker auto-encryption
- Hide taskbar search, disable Windows Spotlight (your lock screen is not a billboard)

### App Baking
- Select from 50+ popular apps (browsers, dev tools, media, utilities)
- Installers are **downloaded during the build** via winget on your PC
- Baked directly into the ISO — installs at first logon with no internet needed
- Supports `.exe`, `.msi`, `.msix`, `.appx` installers
- Installers stored at `C:\Windows\Setup\Scripts\installers\` on the target PC
- Microsoft: "Use the Store!" Us: "We embedded Firefox in the ISO. Try to stop us."

### Presets

Because not everyone wants the same level of chaos.

- **Recommended** — Balanced debloat + privacy. The "I just want a clean PC" option.
- **Privacy** — Maximum telemetry removal. For people who read privacy policies.
- **Gaming** — Game Mode on, Xbox services kept, performance tweaks. For when you need those extra 3 FPS.
- **Minimal** — Light touch. Just TPM bypass + OOBE skip. For the cautious.

### Output
- Create bootable ISO (requires Windows ADK — one of the few Microsoft tools that actually works)
- Copy to USB drive
- Create bootable USB (formats + copies)
- Export/import configuration profiles

---

## Requirements

| Requirement | Why | Installed by setup? |
|-------------|-----|:---:|
| **Windows 10/11** | DISM, PowerShell, Mount-DiskImage — Microsoft's own tools, used against them | - |
| **Administrator rights** | You need admin to modify an OS you own. Thanks for the permission, Microsoft. | - |
| **Windows ADK** (oscdimg.exe) | Creates bootable ISOs. One of approximately 3 useful things in the ADK. | Yes |
| **winget** (App Installer) | Downloads app installers. Microsoft accidentally made something good. | Yes |

The WISO installer auto-installs ADK and winget during setup. The portable version works too — you just can't create ISOs (without ADK) or bake apps (without winget). You can still debloat though. Priorities.

---

## Build from source

```bash
git clone https://github.com/<your-repo>/wiso.git
cd wiso
npm install
npm run build        # builds everything + creates installer
npm run build:app    # builds app only (no installer)
npm run dev          # dev mode with hot reload
```

Yes, we used Electron. Yes, we know. The tool that removes bloat is technically bloat. The self-awareness is part of the experience.

### Project structure

```
├── src/                    # React frontend (Vite) — the pretty face
│   ├── components/         # UI components (OptionsStep, BuildStep, etc.)
│   ├── data/               # apps-list.json, bloatware-list.json (the hit list)
│   └── styles/             # CSS (glassmorphic dark theme because we have taste)
├── electron/               # Electron main process — the brains
│   ├── main.ts             # IPC handlers, window management
│   ├── builder.ts          # Core build logic (1200+ lines of DISM abuse)
│   ├── autounattend.ts     # Generates unattend.xml (Microsoft's own config format, weaponized)
│   ├── tools.ts            # PowerShell/DISM/oscdimg wrappers
│   └── preload.ts          # Context bridge
├── assets/                 # Scripts injected into the Windows image (the payload)
│   ├── FirstLogonScript.ps1     # The big one. Disables everything.
│   ├── RunLocalInstallers.ps1   # Installs your apps
│   ├── VerifyAndCleanupBloat.ps1 # The cleanup crew
│   ├── packages-to-remove.txt   # 150+ package names. We counted.
│   └── apps-list.json           # 50+ apps you can bake in
├── build/
│   └── installer.nsh       # Custom NSIS installer (installs our dependencies because lol)
└── release-build/          # Output — the weapon
```

---

## How it works

1. **Extract** — Mounts your ISO and copies files via robocopy. Microsoft: "Use Media Creation Tool!" Us: "robocopy goes brrrr."
2. **Convert** — If the image is ESD, converts to WIM. Microsoft invented both formats. They couldn't pick one.
3. **Mount** — DISM mounts `install.wim`. We're basically performing surgery on Windows. With Microsoft's own scalpel.
4. **TPM bypass** — Empties `appraiserres.dll` + writes LabConfig registry keys into the offline SYSTEM hive. Microsoft: "You need TPM 2.0." Us: *deletes one DLL and adds three registry keys.* That's it. That's the bypass.
5. **Inject** — Copies `unattend.xml` into `Windows\Panther\` inside the WIM, scripts + installers into `Windows\Setup\Scripts\`. We're using the folder Microsoft designed for OEM customization. Technically we're the OEM now.
6. **Debloat** — Removes provisioned AppX packages offline via DISM. Deletes Edge and OneDrive folders. Physically. From the image. They're not coming back.
7. **Optimize** — Re-exports WIM with LZX compression. We tried ESD (LZMS) compression. It corrupted the image. Because of course it did.
8. **Commit** — DISM unmounts and commits changes. Like saving a game. Except the game is "making Windows usable."
9. **Repack** — oscdimg creates a bootable ISO. Congratulations, you now have a Windows ISO that respects your time and disk space.

The `unattend.xml` is placed **inside the WIM** (not on the ISO root) because putting it on the root causes a reboot install loop. Microsoft's installer re-reads the file on every boot from USB and starts a fresh install. Every. Time. We learned this the hard way. You're welcome.

---

## Defender handling

Windows Defender will try to stop us from removing Windows bloatware. Let that sentence sink in. Microsoft's antivirus protects Microsoft's adware from being removed by the user who owns the computer. Incredible.

WISO handles this:

- **During build**: Tries to add Defender exclusions. If Defender says no (tamper protection), we temporarily disable real-time scanning. We re-enable it after the build. We're not savages.
- **At first logon**: Same dance. Exclusion first, temporary disable if needed, re-enable after.

No permanent changes to Defender. It goes right back to protecting you from actual threats (and apparently, from removing Candy Crush).

---

## VM installation tips

Testing in a VM? (Smart. We broke Windows 4 times during development.)

1. **First boot**: Select **CDROM/DVD** in the boot manager
2. Windows Setup runs — pick your partition, let it cook
3. **Mid-install reboot**: **Don't press any key.** Let it time out and fall through to the hard drive. This is the critical moment. If you mash a key, it boots from the DVD again and you're back at "Install now." Ask us how we know.
4. If the VM boots to the installer anyway: disconnect the ISO from the VM before the reboot

---

## FAQ (Frequently Anticipated Questions)

**Q: Is this legal?**
A: We're using Microsoft's own tools (DISM, oscdimg, PowerShell) to modify Microsoft's own OS that you licensed. It's like rearranging furniture in an apartment you're renting. The landlord might not love it, but it's your living room.

**Q: Will this break Windows Update?**
A: No. Updates still work. We remove apps, not the update mechanism. Windows might try to re-install some bloatware via updates, but that's what `VerifyAndCleanupBloat.ps1` is for.

**Q: Why not just use Linux?**
A: Because we want to play games and use Adobe products. Don't @ us.

**Q: Why Electron?**
A: Because we needed a GUI toolkit that works on Windows and we didn't want to learn WPF. Fight us. Also the tool that removes bloat ships in a 200MB Electron wrapper. We contain multitudes.

**Q: Does this work with Windows 10?**
A: Yes! Windows 10 has less bloat, so there's less to remove. But we'll still find things to yeet.

**Q: I removed the Microsoft Store and now I can't install Store apps.**
A: ...yes. That's what removing the Store does. We warned you. It was in the table. There was a whole column.

**Q: Candy Crush came back after a Windows Update.**
A: We know. Run the verify script again. Or just accept that Candy Crush is eternal. It was here before us. It will be here after us. We merely delay the inevitable.

---

## The build log experience

WISO's build logs are not normal build logs. They're a journey. They contain:

- ASCII art headers for every step
- A coffee cup that suggests you get one while we extract the ISO
- Running commentary on Microsoft's design decisions
- A joke for every single bloatware package we remove
- System info dumps that nobody asked for but everyone gets
- A progress bar made of Unicode blocks
- A victory screen with confetti
- Self-aware jokes about the fact that we're writing jokes in a build log

We spent more time on the build logs than on the actual debloating logic. We're not sorry.

---

## License

MIT — do whatever you want with it. Microsoft can't stop you. We tried, and look what happened: we built a whole app.

---

## Star history

If you made it this far in the README, you legally have to star the repo. It's in the MIT license. (It's not. But you should anyway.)

---

*Built with Electron, React, Vite, approximately 1,200 lines of DISM commands, and an unreasonable vendetta against Candy Crush.*

*Microsoft, if you're reading this: just ship a clean OS. We'll happily retire this project. You won't. But we'd happily do it.*
