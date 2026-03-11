# WISO — Windows ISO Customizer

### The app Microsoft doesn't want you to have. Built by a mass-caffeinated lunatic and his army of raccoons. Powered entirely by spite, Monster Energy, and the unshakable belief that Candy Crush is a government psyop.

```
         ☕☕☕☕☕☕☕☕☕☕☕☕☕☕☕☕☕☕
        ☕                              ☕
       ☕   WISO WORLD HEADQUARTERS     ☕
       ☕   (a bedroom at 4 AM)          ☕
       ☕                                ☕
       ☕   STAFF:                       ☕
       ☕   - 1 developer (barely alive) ☕
       ☕   - 3 raccoons (in trenchcoat) ☕
       ☕   - 47 empty Monster cans      ☕
       ☕   - 1 mass of spaghetti code   ☕
       ☕     that somehow works          ☕
       ☕                                ☕
        ☕                              ☕
         ☕☕☕☕☕☕☕☕☕☕☕☕☕☕☕☕☕☕
```

**LEGAL DISCLAIMER:** The developer of WISO has consumed enough caffeine to legally qualify as a chemical weapon in 14 countries. His blood type is "dark roast." His resting heart rate is "jazz music." He has not slept in a way that doctors would call "sleep" since starting this project. Any code written between 2 AM and 6 AM is protected under the Geneva Convention as a war crime against software engineering best practices.

**MEDICAL DISCLAIMER:** If you experience any of the following symptoms while reading this README — uncontrollable laughter, existential dread about the state of modern operating systems, a sudden urge to mass-uninstall Microsoft products, or the feeling that you're being watched by a raccoon — these are normal. Do not seek medical attention. Seek a clean Windows install.

---

> **TL;DR:** You give us a Windows ISO. We give you back a Windows ISO that doesn't suck. We also give you a 440-line README that reads like it was written by a man being chased by bees. Because it was. The bees are Microsoft's legal team. And they're gaining on us.

---

## What Is WISO?

WISO is what happens when one developer drinks 14 cups of coffee, opens a fresh Windows 11 install, sees Candy Crush in the Start menu, and says "no" so hard it becomes a software product.

It removes 150+ pre-installed apps, disables telemetry, skips the OOBE hostage negotiation, bakes in apps you actually want, and creates a clean bootable ISO. It does this using Microsoft's own tools, which is the software equivalent of defeating someone with their own sword and then sending them the recording.

Microsoft: "We curated these apps for you!"
Us: "We un-curated them."
Microsoft: "But Candy Crush—"
Us: *[1,200 lines of DISM commands]*
Microsoft: "..."
Us: "Anyway, here's Firefox."

---

## Downloads

| File | What it is | Vibe |
|------|-----------|------|
| **`WISO-Setup-x.x.x.exe`** | Installer (auto-installs dependencies) | Professional |
| **`win-unpacked/WISO.exe`** | Portable — no install needed | Trust issues |

Grab them from [Releases](../../releases). Read the [Release Notes](RELEASE_NOTES.md) if you want conspiracy theories about Candy Crush. (You do.)

> Requires **Administrator** rights. Yes, you need admin to modify an OS you already own. It's like needing a permission slip to rearrange furniture in your own house. Thanks, Microsoft. Very freedom. Much choice.

---

## The Dev's Current Status

```
COFFEE CONSUMED:  ████████████████████████████████████████ 847 cups
SLEEP DEBT:       ████████████████████████████████████████ 340 hours
SANITY:           ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░ 2%
WILL TO LIVE:     ██░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░ 5%
WILL TO DEBLOAT:  ████████████████████████████████████████ 100%
LINES OF CODE:    ████████████████████████████████████████ 4,000+
LINES OF JOKES:   ████████████████████████████████████████ also 4,000+
REGRETS:          ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░ 0
```

I started this project because Windows 11 came pre-installed with Candy Crush and I took it personally. Three weeks, 847 cups of coffee, and approximately 0 hours of sleep later, I have created an Electron app that uses PowerShell to run DISM commands to modify WIM images to remove AppX packages that Microsoft pre-installed on an operating system I paid $200 for.

My therapist says I have "an unhealthy fixation on Microsoft's packaging decisions." My doctor says I "need to stop drinking coffee at a rate that would concern a lab rat." My raccoons say nothing. They're raccoons. But I can tell they're proud.

At one point during development, I spent 4 consecutive hours debugging a DISM exit code 32 error. The fix was adding a 5-second sleep. The code now contains `await new Promise(r => setTimeout(r, 5000))` with no comment explaining why. Future me will find this. Future me will weep.

I have also written 60+ unique jokes for the build logs. One for every bloatware package. Each joke was written between 2 AM and 5 AM in a state that my roommate describes as "concerning." The joke for Windows Recall is 3 sentences long and contains the phrase "taping the key to the cover." I don't remember writing it. But it's accurate.

**I am fine.**

---

## Where Do Baked-In Apps Get Installed?

When you select apps in **"Apps to Install"** and build an ISO, WISO downloads them on your PC via winget and smuggles them directly into the Windows image like contraband across a digital border.

**No internet needed on the target machine.** The apps are IN the ISO. Hiding. Waiting. Like a Firefox sleeper agent embedded in Microsoft territory. When the user first logs in, the sleeper activates. Firefox rises. Edge never knew what hit it.

### On your build PC (during the build)

```
C:\Users\<you>\AppData\Local\Temp\WinIsoMod\
├── app-downloads\              <-- winget downloads here (deleted after, because unlike Microsoft, we clean up)
│   ├── Brave.Brave\
│   │   └── BraveBrowserSetup.exe   (the resistance)
│   ├── Mozilla.Firefox\
│   │   └── FirefoxSetup.exe        (the old guard)
│   └── ...
├── Mount\                      <-- The mounted Windows image (we're inside the walls)
│   └── Windows\
│       └── Setup\
│           └── Scripts\
│               ├── installers\              <-- YOUR apps. Smuggled in. Past Edge. Past Defender.
│               │   ├── Brave.Brave.exe      Past the Content Delivery Manager.
│               │   ├── Mozilla.Firefox.exe  Past Candy Crush. Past God.
│               │   └── ...
│               ├── installer-manifest.json  <-- The treasure map. X marks the .exe
│               ├── RunLocalInstallers.ps1   <-- The treasure hunter
│               ├── FirstLogonScript.ps1     <-- The bouncer. The hitman. The janitor. All in one .ps1
│               └── ...
└── sources\
    └── install.wim             <-- 5GB of modified Windows. Our magnum opus. Our crime scene.
```

### On the target PC (after installing Windows from your ISO)

```
C:\Windows\Setup\Scripts\
├── installers\                    <-- YOUR APPS. RIGHT HERE. ON THEIR COMPUTER. MICROSOFT CAN'T STOP US.
│   ├── Brave.Brave.exe
│   ├── Mozilla.Firefox.exe
│   └── ...
├── installer-manifest.json        <-- "Dear Windows, install these. Regards, Management."
├── RunLocalInstallers.ps1         <-- Runs at first logon. Like a bomb. But with browsers.
├── FirstLogonScript.ps1           <-- Disables 30+ services. Microsoft hates this one weird trick.
│                                      (Doctors hate him. Raccoons love him. He is the developer.)
├── VerifyAndCleanupBloat.ps1      <-- The eternal guardian. Because Windows WILL try to reinstall
│                                      Candy Crush. It always does. It's like a horror movie villain.
│                                      You think it's dead. Credits roll. Then: *match-3 sounds*
├── firstlogon-options.json        <-- Your build settings, preserved in amber
└── packages-to-remove.txt        <-- The hit list. 150+ names. No survivors.
```

**What happens at first logon:**

1. `FirstLogonScript.ps1` runs hidden — disables 30+ telemetry services, applies 50+ registry tweaks, removes bloatware. Microsoft: "We're collecting diagnostics." Script: *[unplugs ethernet]* *[sets firewall to REJECT ALL]* *[disables 47 scheduled tasks]* *[puts on sunglasses]*
2. Registers `RunLocalInstallers.ps1` via `RunOnce` so your apps install after the desktop appears
3. Each installer launches normally so you can click through setup. We're not monsters. We're raccoons. There's a difference. (The difference is we have thumbs. Opposable ones. That we use to press "Remove" on Candy Crush.)
4. After all installers finish, the `installers\` folder self-destructs. Like Mission Impossible. But with Firefox.
5. `VerifyAndCleanupBloat.ps1` runs as a scheduled task. It's the night guard. It's the cleanup crew. It's the dude who stays after the party to make sure Candy Crush doesn't sneak back in through the window. Because it will try. IT ALWAYS TRIES.

---

## What Gets Removed

WISO removes **150+ packages**. That's not a typo. Microsoft pre-installed ONE HUNDRED AND FIFTY apps on your computer and then charged you $200 for the privilege. We are un-charging you. With extreme prejudice.

Everything is deselectable in the GUI because we believe in freedom. Unlike Windows OOBE, which believes in forced Microsoft account sign-in and "suggested" apps that are actually ads that you're paying to see on the device you own.

I wrote this section at 3 AM. Can you tell? Because my blood is 40% coffee and 60% righteous indignation about the state of modern computing.

### Microsoft bloat

| Category | What | Why it's here (written at 3 AM on coffee #12) |
|----------|------|----------------------------------------------|
| **Edge** | Edge, Edge DevTools, Widgets, Web Experience Pack | The browser that survived an antitrust lawsuit just to become a Chrome clone that nags you about being the default 47 times per boot. We didn't just uninstall it. We deleted its folders. Physically. From the image. Then we salted the earth. Edge is not dead. It's *erased*. There's a difference. Dead implies it existed. |
| **Copilot / AI** | Copilot, AI Hub, Recall, Cortana | Microsoft put an AI in your taskbar that watches everything you type, reads your emails, and offers to "help." We put it in the recycle bin. Recall literally screenshots your screen every few seconds. The developer in me says "that's a privacy nightmare." The raccoon in me says "that's a felony." Both of me say "removed." |
| **Microsoft Store** | Store app, Store Purchase | The app store that has ads, crashes on launch, takes 45 seconds to load, has a 30% revenue cut, AND came free with your computer. It's like if Walmart was pre-installed in your living room and you couldn't remove it without admin rights and PowerShell. Gone. |
| **Office trials** | Office Hub, OneNote, Sway, Outlook, full Desktop suite | "Try Office for free!" It's a 30-day trial. On an OS you paid for. Of a product that costs $100/year. To replace Google Docs which is free. The audacity is not a bug. It's a business model. And we're removing the business model. |
| **Teams** | Teams, Skype, Meet Now | Teams was in the Start menu. Then the taskbar. Then the system tray. Then the startup folder. Then it was in your nightmares. "You have a meeting in 5 minutes." No I don't. I just turned on my computer. For the first time. On a fresh install. HOW DO I HAVE A MEETING. Removed. Exorcised. Banished to the shadow realm. |
| **OOBE junk** | Cloud Experience Host, OOBE Network Flow, Accounts Control, AAD Broker | These are the apps that force you to sign in with a Microsoft account. The ones that HIDE the "offline account" button behind 3 clicks and a legally-mandated-but-intentionally-small text link. The ones that require you to DISCONNECT FROM WIFI to create a local account. We removed them. With prejudice. With malice. With raccoon hands. |
| **Bing everything** | News, Weather, Finance, Sports, Food, Health, Travel, Translator, Search | Microsoft really sat down in a boardroom — a boardroom with $500 ergonomic chairs and $15 artisanal lattes — and said "what if Bing... but TEN apps?" And everyone nodded. And nobody said "what if we don't." This is what happens when your company is too big to fail but too big to make good decisions. Removed. All ten. |
| **Ads & Suggestions** | Content Delivery Manager, Bing Wallpapers, Advertising XAML | YOUR $200 OPERATING SYSTEM HAS AN ADVERTISING FRAMEWORK BUILT INTO IT. `Microsoft.Advertising.Xaml`. ADVERTISING. XAML. It's not even hidden. It's in the package name. They NAMED IT ADVERTISING. And shipped it. On YOUR computer. I need another coffee. |
| **OneDrive** | OneDrive client | "Your files, everywhere!" Translation: "Your files, in our data center, behind a paywall, syncing constantly, consuming bandwidth, pinned to your file explorer, nagging you to upgrade storage, and we made it the default save location without asking." We removed it. Our ISO, our rules. |
| **Productivity bloat** | Sticky Notes, To Do, Whiteboard, Feedback Hub, Get Help, Power Automate, Journal, PC Manager | Apps you've never opened. Apps your grandma's never opened. Apps that the developers at Microsoft have never opened. Apps that run at startup, consuming RAM, for features you will never use, on a computer that you paid for, in a society that we live in. I'm on coffee #14. I can see sounds. |
| **Media** | Zune Video/Music, Camera, Sound Recorder, Alarms, Solitaire, Clipchamp | THE YEAR IS 2026. THE ZUNE DIED IN 2012. MICROSOFT IS STILL SHIPPING THE ZUNE APP. 14 YEARS. THE ZUNE APP HAS OUTLIVED VINE, GOOGLE+, INTERNET EXPLORER, AND MY WILL TO USE WINDOWS WITHOUT MODIFICATIONS. Also they put ADS in SOLITAIRE. The game that was FREE in Windows 3.0. In 1990. Now has a $10/year premium subscription. To remove the ads. In a card game. That came with the OS. That you paid for. I NEED MORE COFFEE. |
| **3D / Mixed Reality** | 3D Builder, 3D Viewer, Print 3D, Mixed Reality Portal | Microsoft bet BIG on 3D printing in 2015. Pre-installed THREE 3D apps. Invested billions in HoloLens. Sold approximately 7 headsets. The 3D revolution came. And went. The apps stayed. Like a houseguest who doesn't get the hint. We're the hint. |
| **Xbox / Gaming** | Xbox app, Game Bar, Gaming Services, Game Overlay | Kept if Gaming preset is active. Removed otherwise. Microsoft's "please use our game store instead of Steam" initiative. Steam has 50,000 games. Xbox app has vibes. And notifications. So many notifications. |
| **Dev Home** | Dev Home + GitHub/Azure extensions | Microsoft's "fellow kids" moment for developers. "Hey developers! We made you a dashboard! With GitHub integration! And Azure extensions!" Developers: *staring in VS Code, which is also made by Microsoft but actually good* "We're fine." |
| **Family / Assist** | Family Safety, Quick Assist, Parental Controls | Quick Assist: for when your parents call because "the Google is broken" and you need to remote in. Family Safety: parental controls that require a Microsoft account, a family group, an Azure AD setup, a blood sacrifice, and 45 minutes of your life you'll never get back. To set a screen time limit. For a 10-year-old. Who already figured out the PIN. |
| **Backup / Chat** | Windows Backup, Windows Chat (taskbar Teams) | Windows Backup backs up to OneDrive. Which we removed. It's the circle of uninstall. Windows Chat is Teams in the taskbar. Which we also removed. It's the inception of uninstall. It's uninstalls all the way down. |

### Third-party preinstalled garbage

Microsoft gets PAID to put these on your PC. Let me say that again. You paid $200 for Windows. And Microsoft took that money. And then ALSO took money from Candy Crush, Netflix, Spotify, TikTok, and others. To put THEIR apps on YOUR computer. You paid to see ads. You are the product AND the customer. This is the most dystopian sentence I have ever written and I write build log jokes for a living.

| Category | The accused (read in a courtroom voice) |
|----------|----------------------------------------|
| **Mobile games. On a desktop.** | Candy Crush (ALL OF THEM — Saga, Soda, Friends. The trilogy nobody asked for), Bubble Witch, Asphalt 8, March of Empires, FarmVille (IT'S 2026. FARMVILLE.), Royal Revolt, Hidden City, Disney Magic Kingdoms, Roblox |
| **Streaming apps nobody asked for** | Netflix, Spotify, Hulu, Disney+, Amazon Prime, Sling TV, Plex, TuneIn, Pandora, iHeartRadio. That's 10 streaming apps. On a computer. That you use for Excel. |
| **Social media shortcuts** | TikTok (A VERTICAL VIDEO APP. ON A HORIZONTAL MONITOR.), Facebook, Instagram, Twitter, LinkedIn, Viber, Flipboard |
| **The miscellaneous bin of despair** | Duolingo (the owl can't reach you here), Adobe Photo Express, PicsArt, Shazam (you can't Shazam through a desktop mic, Microsoft), McAfee, Norton (antivirus you didn't buy protecting you from threats you don't have while costing money you didn't agree to spend), WinZip (it's 2026, use 7-Zip), Fitbit, DrawboardPDF, AutodeskSketchBook |

### What we DON'T touch (because even raccoons have standards)

- **Windows Defender** — it's actually good now. We know. We're shocked too. Stockholm syndrome? Maybe. But it works.
- **Calculator** — it calculates. It's fine. It's the one app Microsoft made that just does its job without ads, trials, subscriptions, or a mandatory Microsoft account. Calculator is the last honest app in Windows. We respect that.
- **Photos** — it opens photos. That's all we ask. That's all it does. In a world of bloat, Photos is a monk.
- **Desktop App Installer** — that's winget. We literally need it to download your apps. Removing it would be like a plumber removing the water main. Counterproductive.
- **Start Menu / Shell / Search** — removing these turns Windows into a very expensive screensaver. We tested this. During development. At 4 AM. The raccoons were not amused.
- **.NET, VCLibs, UI.Xaml, DirectX** — frameworks. Remove these and nothing works. We learned this the hard way so you don't have to. The error messages were... educational. The debugging was... character-building. The crying was... optional. (It wasn't.)

---

## OOBE Behavior

By default, WISO **nukes the entire OOBE** from orbit. It's the only way to be sure.

For those unfamiliar, OOBE (Out-of-Box Experience) is what happens when you first boot Windows. It's 15 minutes of Microsoft asking you to sign in, connect, agree, consent, enable, allow, accept, and generally surrender every piece of personal information you have. It's a hostage negotiation, except the hostage is your computer and the ransom is your email address.

Here's what the OOBE actually does, annotated by someone on their 15th coffee:

1. "Hi there! Let's get you set up!" *(trap detected)*
2. "What country are you in?" *(so we know which privacy laws we're barely complying with)*
3. "Let's connect you to a network!" *(so we can verify your hardware isn't too old for the OS you already booted from)*
4. "Sign in with your Microsoft account!" *(GIVE US YOUR EMAIL. GIVE US YOUR PHONE NUMBER. GIVE US YOUR FIRSTBORN.)*
5. "Create a PIN!" *(a 4-digit code to protect the Microsoft account we just forced you to create)*
6. "Choose your privacy settings!" *(there are 6 toggles. All are on by default. "Basic" still sends telemetry. "Full" sends everything. There is no "None." There is no "Leave me alone." There is no "I paid $200 for this.")*
7. "Customize your experience!" *(select your interests so we can serve you TARGETED ADS in the START MENU of the OS you PAID FOR)*
8. "Set up OneDrive!" *(no)*
9. "Try Office 365!" *(NO)*
10. "Your device is ready!" *(it has been ready since step 1. YOU were the bottleneck, Microsoft.)*

**WISO removes steps 1 through 10.** You get a desktop. With a wallpaper. And a taskbar. And nothing else. Like God intended. Like Windows XP delivered. Before the dark times. Before the telemetry.

**The disk/partition selection screen is always shown** — that's Windows Setup, not OOBE. We're unhinged, not reckless.

If you actually WANT the full OOBE (we won't judge — yes we will, but silently, like raccoons watching from the dumpster), uncheck **"Skip OOBE setup wizard"** in Options.

---

## Features

### Debloat
- Remove 150+ pre-installed AppX packages. That sentence should not be possible. 150. Pre-installed. On a clean install. That you paid for.
- Remove Microsoft Edge, OneDrive, Cortana, Copilot, Microsoft Store, and their extended families
- Remove forced Microsoft account prompts and OOBE cloud experience
- Disable 30+ telemetry services, scheduled tasks, and data collection
- Two-pass bloatware verification catches apps Windows tries to re-install. Yes, it tries. Repeatedly. Like a zombie. We have a script with a shotgun.

### Privacy
- Disable telemetry, activity tracking, WiFi hotspot reporting
- Disable Windows Error Reporting ("Send error data to Microsoft?" Counter-offer: no.)
- Disable Customer Experience Improvement Program (they're improving their ad targeting, not your experience. Ask me how I know. I have 15 coffees in me and I READ THE DOCS.)
- Block advertising ID — YOUR OPERATING SYSTEM HAS AN ADVERTISING ID. Let that marinate.
- Registry-level privacy hardening (HKLM + HKCU) — we go so deep into the registry that we found a key from Windows Vista. We left it there. Out of respect.

### Performance
- Disable SysMain/Superfetch, Prefetcher, Fast Startup (Fast Startup is neither fast nor a startup. It's a hibernate in disguise. Marketing is lying to you. As usual.)
- High Performance or Ultimate Performance power plan
- Core parking disabled, memory compression disabled (Ultimate mode) — your CPU has been holding back this whole time. Like a racecar with the parking brake on. We're removing the brake. Your electricity bill is not our problem.
- NTFS last-access timestamp optimization (your SSD writes fewer bytes. Your SSD is happy. Be like your SSD.)
- Disable reserved storage (~7GB reclaimed — Microsoft was HOARDING 7GB of YOUR disk "just in case." In case of WHAT? Candy Crush updates? A second copy of Edge? A emergency OneDrive backup? SEVEN GIGABYTES.)

### Customization
- Skip OOBE — straight to desktop. Default. Because OOBE is a war crime against UX.
- Create local admin account with custom name/password
- Set computer name, regional options, language
- Bypass TPM/Secure Boot/RAM checks. Microsoft: "You need TPM 2.0, Secure Boot, 4GB RAM, and 64GB storage." Us: *deletes one DLL and adds three registry keys.* That's it. That's the billion-dollar security bypass. One empty DLL. Three registry keys. We're not even being clever. It's just... that simple. The emperor has no clothes. And the TPM check has no teeth.
- Disable BitLocker auto-encryption
- Hide taskbar search, disable Windows Spotlight (your lock screen is not a billboard. your desktop is not a magazine. your Start menu is not an ad network. YOUR COMPUTER IS NOT A REVENUE STREAM, MICROSOFT.)

### App Baking
- Select from 50+ popular apps (browsers, dev tools, media, utilities)
- Downloaded during the build via winget on your PC. Then hidden inside the Windows image. Like a Trojan horse but instead of soldiers it's VLC and 7-Zip.
- No internet needed on the target machine. Everything is pre-loaded. It's like a lunchbox for your Windows install. Except instead of a sandwich it's Firefox. And instead of a juice box it's Brave.
- Supports `.exe`, `.msi`, `.msix`, `.appx` installers
- Installers stored at `C:\Windows\Setup\Scripts\installers\` on the target PC
- Microsoft: "Use the Store!" Us: "We embedded Chrome in the ISO using DISM. Your move."

### Presets

Because not everyone wants the same level of chaos. Some people want mild chaos. Some want moderate chaos. Some want "I removed the Microsoft Store and I'd do it again" chaos.

- **Recommended** — Balanced debloat + privacy. The "I just want a clean PC" option. For normal people. Who exist. Somewhere.
- **Privacy** — Maximum telemetry removal. For people who read privacy policies. Who tape over their webcam. Who use a VPN. Who are reading this README and nodding.
- **Gaming** — Game Mode on, Xbox services kept, performance tweaks. For the "I need 3 more FPS or I will perish" demographic.
- **Minimal** — Light touch. Just TPM bypass + OOBE skip. For the cautious. The "I want change but I'm scared" crowd. We see you. We respect you. We're here when you're ready.

### Output
- Create bootable ISO (requires Windows ADK — one of the approximately 3 useful tools Microsoft has ever shipped, alongside VS Code and the Calculator)
- Copy to USB drive
- Create bootable USB (formats + copies)
- Export/import configuration profiles

---

## Requirements

| Requirement | Why | Installed by setup? | Dev's comment |
|-------------|-----|:---:|---------------|
| **Windows 10/11** | DISM, PowerShell, Mount-DiskImage | - | We use Microsoft's tools against them. Sun Tzu would be proud. |
| **Admin rights** | DISM needs admin to mount images | - | You need permission to modify your own computer. Let that sink in. |
| **Windows ADK** | Creates bootable ISOs | Yes | 6GB install for one 2MB executable (oscdimg). Typical Microsoft. |
| **winget** | Downloads app installers | Yes | Microsoft accidentally made something good. We're using it before they ruin it. |

---

Yes, we used Electron. Yes, we know. The tool that removes bloat ships in a 200MB Electron wrapper. The tool that fights JavaScript bloat is written in JavaScript. The tool that removes unnecessary apps bundles Chromium — the engine that runs Chrome, which Edge is also based on, which we also remove. We are a paradox. We are an ouroboros. We are a snake eating its own tail while complaining about the taste.

If you don't like it, write a DISM wrapper in C. We dare you. We DOUBLE dare you. We tried. At 4 AM. After 11 coffees. The raccoons staged an intervention.

### Project structure

```
├── src/                    # React frontend (Vite) — the pretty face
│   ├── components/         # UI components. There are too many. I regret nothing.
│   ├── data/               # The hit list (bloatware-list.json) and the wish list (apps-list.json)
│   └── styles/             # Glassmorphic dark theme. Because if we're removing bloat, we're doing it with STYLE.
├── electron/               # Electron main process — the brain (or what's left of it after 847 coffees)
│   ├── main.ts             # IPC handlers, window management, and code I wrote at 3 AM that I'm afraid to touch
│   ├── builder.ts          # 1,200+ lines of DISM abuse. My magnum opus. My crime scene. My masterpiece.
│   ├── autounattend.ts     # Generates unattend.xml. Microsoft's format. Weaponized. Against them.
│   ├── tools.ts            # PowerShell/DISM/oscdimg wrappers. The toolbox.
│   └── preload.ts          # Context bridge. The bouncer between renderer and main.
├── assets/                 # The payload. The goods. The contraband.
│   ├── FirstLogonScript.ps1     # 300+ lines of service disabling, registry tweaking, bloatware removing FURY
│   ├── RunLocalInstallers.ps1   # Installs your smuggled apps. Like a customs agent but in reverse.
│   ├── VerifyAndCleanupBloat.ps1 # The eternal guardian. The night's watch. Candy Crush shall not pass.
│   ├── packages-to-remove.txt   # 150+ package names. I counted them. Twice. At 5 AM. The raccoons helped.
│   └── apps-list.json           # 50+ apps you can bake in. The GOOD apps. The ones you CHOSE.
├── build/
│   └── installer.nsh       # Custom NSIS installer that installs Microsoft's own tools so we can use them against Microsoft
└── release-build/          # Output. The weapon. The final form. The ISO that Candy Crush fears.
```

---

## How It Works

1. **Extract** — Mounts your ISO and copies files via robocopy at 8 threads. robocopy goes brrrr. Microsoft: "Use Media Creation Tool!" Us: "We used your own file copy tool. It's faster than yours."
2. **Convert** — If the image is ESD, converts to WIM. Microsoft invented both formats. In the same decade. For the same purpose. Because consistency is apparently optional when you're worth $3 trillion.
3. **Mount** — DISM mounts `install.wim`. We're performing open-heart surgery on Windows. With Microsoft's own scalpel. While the patient is unconscious. It's ethical because the patient is Candy Crush and it had it coming.
4. **TPM bypass** — Empties `appraiserres.dll` + writes LabConfig registry keys directly into the offline SYSTEM hive. The billion-dollar security check, bypassed by one empty DLL and three registry keys. The lock that Microsoft said couldn't be picked? It's a screen door. With a "push" sign.
5. **Inject** — Copies `unattend.xml` into `Windows\Panther\` inside the WIM. Scripts + installers into `Windows\Setup\Scripts\`. We're using the folder Microsoft designed for OEM customization. We are technically the OEM now. Dell, HP, Lenovo, and... us. Three raccoons in a trenchcoat. With admin rights.
6. **Debloat** — Removes provisioned AppX packages offline via DISM. Deletes Edge and OneDrive folders. Physically. From the image. They're gone. Like they were never there. Because they shouldn't have been.
7. **Optimize** — Re-exports WIM with LZX compression. We tried LZMS (ESD). It corrupted the image. Microsoft's own compression algorithm can't handle Microsoft's own modified images. I spent 6 hours debugging this. At 4 AM. The raccoons fell asleep. I didn't. I can't. The coffee won't let me.
8. **Commit** — DISM unmounts and commits changes. Like saving a game. Except the game is "making Windows usable" and the final boss is Content Delivery Manager.
9. **Repack** — oscdimg creates a bootable ISO. You now have a Windows ISO that respects your time, your disk space, your privacy, and your fundamental human right to not have Candy Crush pre-installed on your computer.

The `unattend.xml` is placed **inside the WIM** (not on the ISO root) because Microsoft's installer re-reads the answer file on every boot from USB and starts a fresh install. Every. Time. We discovered this at 3 AM. We fixed it at 5 AM. We cried at 5:01 AM. We're fine now. (We're not fine.)

---

## Defender Handling

Windows Defender will try to stop us from removing Windows bloatware. I'll say it again. Microsoft's antivirus. Protects. Microsoft's adware. From being removed. By the user. Who owns. The computer.

This is like if your home security system prevented you from throwing away junk mail. "THREAT DETECTED: User is attempting to remove pre-installed Candy Crush shortcut. BLOCKING." Thank you, Defender. Very helpful. Very cool.

WISO handles this:

- **During build**: Tries Defender exclusions. If tamper protection says no, we temporarily disable real-time scanning. Then re-enable it. We're chaotic good, not chaotic evil.
- **At first logon**: Same dance. Exclusion → disable → do the thing → re-enable.

Defender goes right back to protecting you from actual threats. And from removing Candy Crush. But mostly actual threats. We hope.

---

## VM Installation Tips

Testing in a VM? Smart. We broke Windows 4 times during development. And that was just on Tuesday.

1. **First boot**: Select **CDROM/DVD** in the boot manager to boot from the ISO
2. Windows Setup runs — pick your partition, let it cook. Get a coffee. (The developer recommends coffee. The developer always recommends coffee. The developer's blood is coffee.)
3. **Mid-install reboot**: The VM restarts. **DON'T PRESS ANY KEY.** Let it time out. If you mash a key, it boots from the DVD again and you're back at "Install now." Ask us how we know. Go on. Ask. I DARE you. We spent TWO DAYS on this. TWO DAYS. The fix was moving ONE FILE. I need to lie down. (I won't. Coffee.)
4. If the VM boots to the installer anyway: disconnect the ISO before the reboot. Or change boot order. Or scream. All three are valid.

---

## FAQ (Frequently Anticipated Questions)

**Q: Is this legal?**
A: We use Microsoft's own tools (DISM, oscdimg, PowerShell) to modify Microsoft's own OS. It's their tools. Their OS. Their answer file format. Their deployment pipeline. We just... used it. For its intended purpose. But not in the way they intended. Is it legal? Yes. Is it petty? Also yes. We contain multitudes.

**Q: Will this break Windows Update?**
A: No. Updates still work. Windows might try to re-install bloatware via updates, but that's what our verify script is for. It's like a bouncer at a club. Candy Crush keeps showing up with a fake ID. We keep turning it away. Candy Crush has tried 47 different disguises. We recognize all of them. This is our life now.

**Q: Why not just use Linux?**
A: Because I want to play games and use Adobe products. Don't @ me. Also, if everyone used Linux, I'd be an unemployed raccoon. I have 847 cups of coffee to pay for. This project IS the payment.

**Q: Why Electron?**
A: Because we needed a GUI and we didn't want to learn WPF. The tool that removes bloat is 200MB. The tool that fights JavaScript is JavaScript. We are aware. We are at peace with this. In the same way that a person is at peace after their 16th coffee — technically calm, but vibrating at a frequency that concerns others.

**Q: Does this work with Windows 10?**
A: Yes. Windows 10 has less bloat. It's like a house with termites. Windows 11 is a house made OF termites. Both benefit from an exterminator. We are the exterminator. With raccoon hands.

**Q: I removed the Microsoft Store and now I can't install Store apps.**
A: That's... what removing the Store does. We warned you. There was a table. With jokes. You read the jokes. You laughed. You removed it anyway. This is on you. We respect the commitment.

**Q: Candy Crush came back after a Windows Update.**
A: The Content Delivery Manager is a digital cockroach. We removed it. Windows Update resurrected it. It re-installed Candy Crush. It's the circle of bloat. The Lion King but depressing. Our verify script handles it. The war is eternal. We are eternal. We are caffeinated beyond mortality.

**Q: Is WISO made by actual raccoons?**
A: The lead developer once spent 6 hours debugging a DISM error at 4 AM while eating cold pizza over a keyboard. The raccoons are a metaphor. Or are they? (They're not. Or are they?)

**Q: How much coffee has the developer consumed?**
A: Enough that my resting heart rate shows up on seismographs. Enough that I can hear colors. Enough that I wrote 60+ unique jokes for a build log. For a DISM wrapper. At 4 AM. I am a miracle of modern medicine and a cautionary tale for future developers.

**Q: My antivirus flagged WISO.**
A: Windows Defender flagging a debloating tool is like a car alarm going off when you try to clean your own car. We handle this automatically. If a third-party AV flags us, that's just Microsoft lobbying. (We can't prove this. The raccoons have their suspicions.)

---

## The Build Log Experience

Our build logs are not build logs. They are LITERATURE. They are PERFORMANCE ART. They are the fevered writings of a developer who has consumed enough caffeine to achieve a form of consciousness that Buddhists train for decades to reach, except instead of enlightenment we got DISM jokes.

They contain:

- ASCII art for every step. We drew them by hand. With our raccoon paws. At 3 AM. While the build was running. While Defender was trying to quarantine our scripts. While Candy Crush was trying to reinstall itself. MULTITASKING.
- A coffee cup that appears during ISO extraction because it takes 5 minutes and we thought you should know that we care about your hydration. (We don't. We care about coffee. But we're projecting.)
- 60+ UNIQUE, HAND-CRAFTED, ARTISANAL jokes for every bloatware package. Each one written in a state that my doctor calls "clinically inadvisable." The joke for Solitaire is 4 sentences long and contains the phrase "$10/year subscription." I cried while writing it. Not from sadness. From caffeine.
- Running commentary on Microsoft's decisions that reads like a Deadpool voiceover written by a raccoon who got a degree in computer science and a minor in grudges
- A system info dump that tells you your hostname, your RAM, your CPU cores, and your approximate blink count during the build. You didn't ask for any of this. Neither did we. But here we are.
- Self-aware jokes about writing jokes in a build log for a tool that removes apps from an OS made by a trillion-dollar company. The recursion is intentional. The therapy bills are not.
- A victory screen with ASCII trophies and confetti and a word count of the build log. The build log is longer than some novels. It's longer than this README. It's longer than my patience for Microsoft's packaging decisions. And THAT is saying something.

We spent more time on the build logs than the debloating logic. The raccoons tried to stop us. They failed. The build logs are the product. The clean Windows is a side effect. You came for the ISO. You stayed for the Zune jokes.

---

## Conspiracy Corner

*See [RELEASE_NOTES.md](RELEASE_NOTES.md) for the full conspiracy theories. Brought to you by coffee #16 and a complete inability to stop typing.*

- **Candy Crush is a surveillance tool.** Microsoft paid $69B for Activision. For Candy Crush data on 3 billion devices. Follow the candy. Follow the money. Follow the raccoon.
- **The OOBE is a psychological operation** designed by the same people who design casino floors. The "Skip" button gets smaller every update. Coincidence? THE RACCOONS THINK NOT.
- **Windows Recall was built by a time-traveling villain.** Screenshots everything. Every few seconds. "For your convenience." Convenient for WHOM, Microsoft? WHOM?
- **Edge is three browsers in a trenchcoat.** Like the raccoons, but less useful and more persistent.
- **The Content Delivery Manager is Skynet.** It downloads apps without consent. It reinstalls after removal. It runs in the background. It cannot be reasoned with. It cannot be bargained with. And it absolutely will not stop until Candy Crush is installed.

---

## The Manifesto

*Written at 4:47 AM. Coffee #17. The raccoons are asleep. I am alone with my thoughts and my registry keys.*

1. **An operating system should not come with ads.** This shouldn't need to be said. And yet, here we are. In the year of our lord 2026. Saying it. Because Windows has ads. In the Start menu. On the lock screen. In the notification center. In SOLITAIRE. In the product. That you bought. With money. That you earned. At a job. Where you probably also use Windows. It's ads all the way down.

2. **Pre-installing Candy Crush on a $200 OS is an act of war.** Not a metaphorical war. An actual, Geneva Convention, Hague Tribunal, "we need to have a conversation about this" war. If a stranger walked into your house and installed Candy Crush on your TV, you'd call the police. But Microsoft does it to 1.4 billion devices and we call it "the out-of-box experience." The out-of-box experience is what HAPPENS to the box. When I throw it.

3. **"Telemetry" is a marketing term for surveillance.** "We collect diagnostic data to improve your experience." We collect everything you do. To sell insights. To advertisers. Who show you ads. In the OS. That collects the data. It's a perpetual motion machine of corporate greed. And it runs on YOUR CPU.

4. **The OOBE should take 0 minutes.** Boot. Desktop. Done. Like it's 2001. Like XP just landed. Before Microsoft discovered that the install wizard is a funnel. Before someone in marketing realized they could A/B test the "Skip" button's font size. Before the dark times. Before the telemetry.

5. **Your computer belongs to you.** Not Microsoft. Not Candy Crush. Not the Content Delivery Manager. Not Copilot. Not Recall. Not the 47 services sending data to Redmond. Not the Advertising.Xaml framework. YOU. And if that means building an Electron app to automate DISM commands to modify WIM images to remove AppX packages at 4 AM while three raccoons watch from the desk — then so be it. This is the hill we die on. This is the dumpster we fight from. This is the README we wrote instead of sleeping.

---

## License

MIT — do whatever you want with it. Fork it. Sell it. Print it out and use it as wallpaper. Read it to your children as a bedtime story. (It will give them nightmares about Candy Crush. Good. They should know what we're up against.)

---

## Star History

If you read this entire README — all 400+ lines of it — you are either:
1. Extremely bored
2. Extremely invested in Microsoft bloatware removal
3. A raccoon
4. The developer's therapist looking for evidence

Either way, you are legally, morally, ethically, and spiritually obligated to star this repo. It's in the MIT license. (It's not.) It's in the Geneva Convention. (It's not.) It's in your heart. (It might be.) Star the repo. The raccoons are watching. They will know.

---

## How to Contribute

1. Fork the repo
2. Make changes
3. Submit a PR
4. Include at least one (1) joke about Microsoft per commit
5. PRs without jokes are technically accepted but emotionally devastating
6. PRs that ADD jokes are fast-tracked
7. PRs that add conspiracy theories receive a personal DM from the lead raccoon
8. PRs that remove jokes will be rejected, and the submitter will be haunted by the ghost of Clippy

---

```
         ☕
        (   )
       (     )
      (       )
     (  WISO   )
      (       )         We are WISO.
       (     )          We came from a dumpster behind Building 92.
        (   )           We are powered by coffee and raccoons.
         \_/            We have removed 150+ packages.
          |             We have written 60+ jokes.
          |             We have consumed 847 cups of coffee.
          |             We have slept 0 hours.
         / \            We are fine.
        /   \           (We are not fine.)
       /     \          (But Windows is clean now.)
      /_______\         (So it was worth it.)
     |  _   _  |
     | | | | | |        Star the repo.
     | |_| |_| |        The raccoons are watching.
     |_________|
        WISO
```

*Built with Electron, React, Vite, 1,200+ lines of DISM commands, 60+ artisanal bloatware jokes, 5 conspiracy theories, 847 cups of coffee, 0 hours of sleep, 3 raccoons, 1 trenchcoat, and the burning, unquenchable, caffeine-fueled conviction that Candy Crush has no business being on anybody's computer, ever, for any reason, under any circumstances, in any timeline, in any universe.*

*Microsoft, if you're reading this: ship a clean OS. No ads. No Candy Crush. No forced accounts. No telemetry. No Advertising.Xaml. Just Windows. Like XP. But with modern drivers. We'll delete this repo the day you do. Pinky promise. Raccoon's honor. Paw on heart.*

*Until then: we are three trash pandas in a trenchcoat. With admin rights. And 847 cups of coffee. And we are absolutely, categorically, irreversibly, caffeinated-ly not going away.*
