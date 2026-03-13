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
       ☕     • Gerald (top, the brains) ☕
       ☕     • Steve (middle, the typer)☕
       ☕     • Dave (legs, the anchor)  ☕
       ☕   - 47 empty Monster cans      ☕
       ☕   - 1 mass of spaghetti code   ☕
       ☕     that somehow works          ☕
       ☕   - 1 trenchcoat (load-bearing)☕
       ☕                                ☕
        ☕                              ☕
         ☕☕☕☕☕☕☕☕☕☕☕☕☕☕☕☕☕☕
```

**RACCOON DISCLOSURE:** The three raccoons — Gerald, Steve, and Dave — are listed as co-developers. Gerald handles architecture decisions from the top of the trenchcoat. Steve operates the keyboard from the middle (he has the best paws for typing). Dave is the legs. Dave doesn't get enough credit. Without Dave, this is just two raccoons in a very short trenchcoat standing on a chair. Dave IS the foundation. Dave has never once complained. Dave is a professional. Be like Dave.

The trenchcoat is load-bearing. If you remove the trenchcoat, the entire project collapses. We tested this. During a code review. At 3 AM. Gerald fell off Steve. Steve fell off Dave. The laptop survived. The code didn't. We had to `git reset --hard`. The trenchcoat stays.

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

Grab them from [Releases](https://github.com/omfghello/window-iso-customizer/releases). Read the [Release Notes](RELEASE_NOTES.md) if you want conspiracy theories about Candy Crush. (You do.)

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

Gerald just looked at me. Gerald doesn't blink. Gerald KNOWS I'm not fine. Gerald has seen things. Gerald has seen me write a 400-line batch file at 4 AM while whispering "taskkill /f /im" like a prayer. Gerald didn't intervene. Gerald respects the process. Gerald is the best raccoon I've ever worked with. Steve is good too. Dave is the legs.

**UPDATE:** I am now writing a README about the README. I am meta-narrating my own documentation. I am a developer who wrote a DISM wrapper, who wrote build log jokes, who is now writing jokes about writing jokes in a README about a tool that removes apps that a trillion-dollar company installed on YOUR computer without YOUR permission. This is fine. The recursion is intentional. The therapy is not. Gerald is nodding. Steve is typing this. Dave is standing.

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
│               ├── SetupComplete.cmd        <-- THE RECKONING. One cmd to rule them all.
│               ├── FirstLogonScript.ps1     <-- Safety net. If the cmd fails, this picks up the pieces.
│               ├── firstlogon-options.cmd   <-- set WISO_REMOVE_BLOAT=1. Poetry.
│               └── ...
└── sources\
    └── install.wim             <-- 5GB of modified Windows. Our magnum opus. Our crime scene.
```

### On the target PC (after installing Windows from your ISO)

```
C:\Windows\WISO\                   <-- OUR BASE OF OPERATIONS. Inside enemy territory.
├── WISO-OOBE.exe                  <-- THE REPLACEMENT. Our custom OOBE. The heist payload.
│                                      An entire Electron app that replaces explorer.exe as the
│                                      Windows shell on first boot. Dark-themed. Glassmorphic.
│                                      Privacy-first. Raccoon-approved. Microsoft's OOBE never
│                                      even gets to say "Hi there!"
├── installers\                    <-- YOUR APPS. RIGHT HERE. ON THEIR COMPUTER. MICROSOFT CAN'T STOP US.
│   ├── Brave.Brave.exe
│   ├── Mozilla.Firefox.exe
│   └── ...
├── installer-manifest.json        <-- "Dear OOBE, install these. Regards, Management."
├── firstlogon-options.json        <-- Your build settings in JSON. Every toggle. Every preference.
│                                      Privacy tweaks, gaming mode, power plan, taskbar alignment...
│                                      The OOBE reads this and knows exactly what you wanted.
│                                      It's like a letter from past-you to future-Windows.
├── packages-to-remove.txt         <-- The hit list. 150+ names. No survivors.
└── VerifyAndCleanupBloat.ps1      <-- The eternal guardian. Because Windows WILL try to reinstall
                                       Candy Crush. It always does. It's like a horror movie villain.
                                       You think it's dead. Credits roll. Then: *match-3 sounds*

C:\Windows\Setup\Scripts\
├── SetupComplete.cmd              <-- THE INSIDE MAN. Runs as LOCAL SYSTEM after Windows Setup.
│                                      Its only job now: replace explorer.exe with WISO-OOBE.exe
│                                      in the Winlogon\Shell registry key, create a watchdog task,
│                                      and step aside. The heist crew does the rest.
├── FirstLogonScript.ps1           <-- Retired veteran. Fallback stub. Tells war stories.
└── firstlogon-options.cmd         <-- Legacy batch format. For backwards compatibility. Poetry.
```

**What happens on first boot (The Heist):**

1. `SetupComplete.cmd` fires automatically as **LOCAL SYSTEM**. Its mission is surgical: swap `explorer.exe` for `WISO-OOBE.exe` in the registry shell key, create a watchdog task (restores explorer after 15min if things go sideways), and exit. That's it. The cmd is now the inside man, not the whole crew.
2. **WISO-OOBE launches as the Windows shell.** Full screen. Dark theme. No taskbar. No desktop. Just our app. The user walks through region, network, account creation, privacy settings, appearance, and app selection. Every screen has a "We collect absolutely nothing" badge because the raccoons have STANDARDS.
3. **The Setting Up screen** — this is where the magic happens. Live progress. Real-time logs. The OOBE reads `firstlogon-options.json` and executes: process killing, service disabling, Appx removal, registry hardening, power plan application, and YOUR app installations. You watch Candy Crush die in real-time. It's therapeutic.
4. **Finalize** — shell restored to `explorer.exe`, temp `WISO-Setup` account deleted, `C:\Windows\WISO` cleaned up, watchdog removed, reboot. You log in to YOUR desktop. Clean. Debloated. YOUR apps installed. No trace of the heist. Clean getaway. Gerald is proud.
2. Your baked-in apps get copied to `Desktop\WISO-Installers\` via `robocopy` (the real user's Desktop, not SYSTEM's — we resolve the actual human from the registry like digital detectives). Then it runs them: MSI gets `msiexec /quiet`, EXE gets 7 different silent flag combos (`/VERYSILENT`, `/S`, `/silent`, `/quiet`, `-s`, `--silent`, `/qn` — we try them all like picking a lock with 7 picks), MSIX/APPX gets a PowerShell `Add-AppxPackage` one-liner. If all 7 flags fail, interactive fallback. YOUR APPS WILL INSTALL OR WE WILL DIE TRYING.
3. `FirstLogonScript.ps1` is now a fallback stub. It used to run the whole show — 300+ lines of fury. Now it checks if `SetupComplete.cmd` finished (via a done-flag), and if not, re-runs it. Like a dead man's switch for debloating. It's retired. It sits in a rocking chair. Telling war stories about the time it disabled 47 services in one pass.
4. Defender gets RE-ENABLED at the end. We delete the offline policy keys, run `Set-MpPreference -DisableRealtimeMonitoring $false`, and let Defender wake up to a clean system. "I did a great job," Defender says. We let it have this.
5. `VerifyAndCleanupBloat.ps1` runs as scheduled tasks at 2, 5, 15, 30, 60 minutes. It's the night guard. The cleanup crew. The dude who stays after the party to make sure Candy Crush doesn't sneak back in through the window. Because it will try. IT ALWAYS TRIES.

**Three layers of reliability:** `SetupComplete.cmd` (LOCAL SYSTEM, guaranteed by Windows) → `FirstLogonCommands` in unattend.xml (backup trigger) → `RunOnce` registry key (tertiary fallback). A done-flag prevents double execution. Belt, suspenders, AND duct tape.

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

## OOBE Behavior — THE HEIST

We didn't just *skip* Microsoft's OOBE. We didn't just *nuke* it. We **replaced it.** With our own. A full-screen, dark-themed, glassmorphic Electron app that takes over the entire Windows shell on first boot. We pulled an Ocean's Eleven on the Out-of-Box Experience. Except instead of robbing a casino, we're robbing Microsoft of the 15 minutes they use to psychologically manipulate you into giving them your email, your phone number, and your firstborn.

For those unfamiliar, OOBE (Out-of-Box Experience) is what happens when you first boot Windows. Here's what Microsoft's OOBE does, annotated by someone on their 15th coffee:

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

**WISO replaces ALL 10 STEPS with our own custom OOBE.** Here's what YOU get instead:

1. **Welcome** — "WISO: Your Windows, Your Way." A privacy badge that says "We collect absolutely nothing." Because we DON'T. Unlike the screen it replaced.
2. **Region & Language** — Same info, no guilt trip, no data harvesting.
3. **Network** — WiFi picker with signal bars. Connect or skip. We won't use the connection to phone home. *Unlike some companies we could name.*
4. **Account** — Local OR Microsoft. YOUR choice. No hidden "offline account" button. No disconnecting WiFi to find the secret option. It's just... two big cards. Pick one. Like a NORMAL HUMAN INTERFACE.
5. **Privacy & Security** — 12 toggles. ALL default to "protect you." Paranoid Mode button. Stock Windows button. Every toggle has a description written by someone who READ Microsoft's privacy docs at 3 AM and is STILL MAD ABOUT IT.
6. **Appearance** — Dark/Light theme, taskbar alignment, accent color. Make it yours before it's even done setting up.
7. **Your Apps** — Every installer you baked into the ISO, shown with toggles. Uncheck anything you changed your mind about. They'll install live during setup.
8. **Setting Up** — THIS IS THE GOOD PART. A live progress screen with a real-time log. You WATCH the debloating happen. Service disabling. Process killing. App removal. Your apps installing. All with a progress bar. All in a beautiful dark UI. All while a privacy badge says "Zero data was sent anywhere."
9. **Done** — Summary of everything. Account, theme, privacy protections, apps installed. One button: "Restart & Finish."
10. **Reboot** — Shell restored to explorer.exe. Temp account deleted. WISO folder cleaned up. You log in to a clean, debloated, YOUR-settings desktop. No evidence. Clean getaway. 🕶️

The whole thing runs as the Windows shell — `WISO-OOBE.exe` literally replaces `explorer.exe` in the `Winlogon\Shell` registry key. When you boot, instead of a desktop, you get our setup wizard. When you finish, we put explorer back and reboot. If our OOBE crashes (it won't, but Gerald insisted on a safety net), a watchdog task restores explorer.exe after 15 minutes. There's also a **Ctrl+Shift+F10 panic key** that immediately bails out. Because even heist movies have an escape plan.

**The disk/partition selection screen is always shown** — that's Windows Setup, not OOBE. We're unhinged, not reckless.

Gerald planned the heist. Steve typed the code. Dave is the getaway legs. The trenchcoat is the disguise. `SetupComplete.cmd` is the inside man. `WISO-OOBE.exe` is the payload. And Microsoft's "Hi there! Let's get you set up!" is the mark who never saw it coming.

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

### Custom OOBE (THE HEIST)
- **Full replacement of Windows OOBE** with a custom Electron app. Not a skip. Not a bypass. A HEIST. We pulled the entire Out-of-Box Experience out of Windows like a tablecloth from under dishes, and put our own in its place. The dishes didn't move.
- Dark-themed glassmorphic UI with privacy badges everywhere. You'll know we're not collecting data because we'll tell you 47 times. Per screen.
- Local OR Microsoft account creation — YOUR choice, right on the screen, no tricks, no hidden buttons, no "disconnect WiFi" workaround. Just two cards. Pick one. Revolutionary, apparently.
- WiFi connection with signal strength bars. Or skip it. We don't need internet to spy on you because WE DON'T SPY ON YOU.
- 12 privacy toggles with real descriptions. Paranoid Mode. Stock Windows mode. Everything in between. A privacy banner that says "We collect absolutely nothing" because the raccoons have standards.
- Appearance settings (theme, accent, taskbar alignment) DURING first boot, not after 15 minutes of Microsoft interrogation.
- App selection screen — every app you baked in, with toggles. Change your mind about VLC? Uncheck it. Live installs during setup.
- **Live debloat progress** — you WATCH the bloatware die in real-time. Service by service. Process by process. With a progress bar and logs. It's beautiful. It's therapeutic. It's the Windows equivalent of popping bubble wrap.
- Watchdog safety net — if the OOBE crashes, explorer.exe comes back in 15 minutes. Ctrl+Shift+F10 panic key for immediate bailout. We're chaotic, not irresponsible.
- Zero data collection. Zero telemetry. Zero phone-home. We collect less data than a rock. The rock at least absorbs heat. We absorb nothing.

### Customization
- Bypass TPM/Secure Boot/RAM checks. Microsoft: "You need TPM 2.0, Secure Boot, 4GB RAM, and 64GB storage." Us: *deletes one DLL and adds three registry keys.* That's it. That's the billion-dollar security bypass. One empty DLL. Three registry keys. We're not even being clever. It's just... that simple. The emperor has no clothes. And the TPM check has no teeth.
- Disable BitLocker auto-encryption
- Hide taskbar search, disable Windows Spotlight (your lock screen is not a billboard. your desktop is not a magazine. your Start menu is not an ad network. YOUR COMPUTER IS NOT A REVENUE STREAM, MICROSOFT.)
- Classic context menu, show file extensions, compact Explorer view — because Windows 11 hid everything behind a "Show more options" click and we REFUSE.

### App Baking & Live Installation
- Select from 50+ popular apps (browsers, dev tools, media, utilities)
- Downloaded during the build via winget on your PC. Then hidden inside the Windows image. Like a Trojan horse but instead of soldiers it's VLC and 7-Zip.
- No internet needed on the target machine. Everything is pre-loaded. It's like a lunchbox for your Windows install. Except instead of a sandwich it's Firefox. And instead of a juice box it's Brave.
- Supports `.exe`, `.msi`, `.msix`, `.appx` installers — each `.exe` gets 7 different silent-install strategies tried sequentially. We don't give up. We're like that one raccoon who figured out the childproof trash can lid.
- **New: Apps are selected AND installed LIVE during the custom OOBE.** You pick them on a screen. You watch them install on the next screen. With progress bars. And logs. The future is now. The future has raccoons.
- Installers stored at `C:\Windows\WISO\installers\` on the target PC (cleaned up after installation because UNLIKE MICROSOFT WE CLEAN UP AFTER OURSELVES)
- Microsoft: "Use the Store!" Us: "We embedded Chrome in the ISO using DISM and installed it during our own OOBE while yours was locked in the trunk. Your move."

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

## Build From Source

```bash
git clone https://github.com/omfghello/window-iso-customizer.git
cd window-iso-customizer
npm install        # downloads 400MB of node_modules. we are bloatware fighting bloatware.
npm run build      # builds everything + creates installer
npm run dev        # dev mode with hot reload (the developer's happy place)
```

Yes, we used Electron. Yes, we know. The tool that removes bloat ships in a 200MB Electron wrapper. The tool that fights JavaScript bloat is written in JavaScript. The tool that removes unnecessary apps bundles Chromium — the engine that runs Chrome, which Edge is also based on, which we also remove. We are a paradox. We are an ouroboros. We are a snake eating its own tail while complaining about the taste.

If you don't like it, write a DISM wrapper in C. We dare you. We DOUBLE dare you. We tried. At 4 AM. After 11 coffees. The raccoons staged an intervention. Gerald climbed down from the trenchcoat. Gerald looked me in the eye. Gerald said nothing because Gerald is a raccoon. But his silence spoke VOLUMES. Steve put his paw on my shoulder. Dave kept standing. The C rewrite was abandoned. The Electron wrapper lives on. Like Candy Crush. But useful.

### Project structure

```
├── src/                    # React frontend (Vite) — the pretty face of the WISO builder
│   ├── components/         # UI components. There are too many. I regret nothing.
│   ├── data/               # The hit list (bloatware-list.json) and the wish list (apps-list.json)
│   └── styles/             # Glassmorphic dark theme. Because if we're removing bloat, we're doing it with STYLE.
├── electron/               # Electron main process — the brain (or what's left of it after 847 coffees)
│   ├── main.ts             # IPC handlers, window management, and code I wrote at 3 AM that I'm afraid to touch
│   ├── builder.ts          # 1,200+ lines of DISM abuse. Now includes OOBE injection logic (THE HEIST).
│   ├── autounattend.ts     # Generates unattend.xml. Microsoft's format. Weaponized. Against them.
│   ├── tools.ts            # PowerShell/DISM/oscdimg wrappers. The toolbox.
│   └── preload.ts          # Context bridge. The bouncer between renderer and main.
├── oobe/                   # 🎭 THE REPLACEMENT OOBE. An entire Electron app. Inside the main app. INCEPTION.
│   ├── electron/           # OOBE main process. Runs system commands, installs apps, applies settings.
│   │   ├── main.ts         # IPC handlers for account creation, WiFi, app installs, debloating.
│   │   └── preload.ts      # Context bridge for the OOBE renderer. No direct Node access. We have standards.
│   ├── src/                # React frontend for the OOBE. 10 screens of raccoon-approved UX.
│   │   ├── screens/        # Welcome, Region, Network, Account, Privacy, Appearance, Apps, SettingUp, Done.
│   │   │                     Each one has a "we collect nothing" badge. EACH ONE.
│   │   ├── styles/         # Dark theme CSS. Glassmorphic. Gaming aesthetic. Privacy badges everywhere.
│   │   └── App.tsx         # State management. Screen flow. Build option loading. The conductor.
│   └── package.json        # Electron-builder config. Builds to portable. No installer needed.
├── assets/                 # The payload. The goods. The contraband.
│   ├── SetupComplete.cmd        # THE INSIDE MAN. Now just swaps the shell and creates a watchdog.
│   ├── FirstLogonScript.ps1     # Retired veteran. Now a fallback stub. Tells war stories.
│   ├── VerifyAndCleanupBloat.ps1 # The eternal guardian. The night's watch. Candy Crush shall not pass.
│   ├── packages-to-remove.txt   # 150+ package names. I counted them. Twice. At 5 AM. The raccoons helped.
│   └── apps-list.json           # 50+ apps you can bake in. The GOOD apps. The ones you CHOSE.
├── build/
│   └── installer.nsh       # Custom NSIS installer that installs Microsoft's own tools so we can use them against Microsoft
└── release-build/          # Output. The weapon. The final form. The ISO that Candy Crush fears.
```

---

## How It Works

### Phase A: The Build (on YOUR machine)

1. **Extract** — Mounts your ISO and copies files via robocopy at 8 threads. robocopy goes brrrr. Microsoft: "Use Media Creation Tool!" Us: "We used your own file copy tool. It's faster than yours."
2. **Convert** — If the image is ESD, converts to WIM. Microsoft invented both formats. In the same decade. For the same purpose. Because consistency is apparently optional when you're worth $3 trillion.
3. **Mount** — DISM mounts `install.wim`. We're performing open-heart surgery on Windows. With Microsoft's own scalpel. While the patient is unconscious. It's ethical because the patient is Candy Crush and it had it coming.
4. **TPM bypass** — Empties `appraiserres.dll` + writes LabConfig registry keys directly into the offline SYSTEM hive. The billion-dollar security check, bypassed by one empty DLL and three registry keys. The lock that Microsoft said couldn't be picked? It's a screen door. With a "push" sign.
5. **Inject** — Copies `unattend.xml` into `Windows\Panther\`. Injects `SetupComplete.cmd` (the inside man), scripts, and your app installers into `Windows\Setup\Scripts\`. Then comes THE HEIST: the entire `WISO-OOBE` Electron app gets smuggled into `Windows\WISO\` along with `firstlogon-options.json` (your build settings), `installer-manifest.json` (your app list), and `packages-to-remove.txt` (the hit list). We are technically the OEM now. Dell, HP, Lenovo, and... us. Three raccoons in a trenchcoat. With admin rights. And an Electron app.
6. **Debloat** — Removes provisioned AppX packages offline via DISM. Deletes Edge and OneDrive folders. Physically. From the image. They're gone. Like they were never there. Because they shouldn't have been.
7. **Optimize** — Re-exports WIM with LZX compression. We tried LZMS (ESD). It corrupted the image. Microsoft's own compression algorithm can't handle Microsoft's own modified images. I spent 6 hours debugging this. At 4 AM. The raccoons fell asleep. I didn't. I can't. The coffee won't let me.
8. **Commit** — DISM unmounts and commits changes. Like saving a game. Except the game is "making Windows usable" and the final boss is Content Delivery Manager.
9. **Repack** — oscdimg creates a bootable ISO. Complete with a custom OOBE hidden inside like a raccoon in a dumpster. Waiting. Patient. Ready.

### Phase B: The Heist (on the TARGET machine)

10. **Windows Setup** — User installs Windows normally. Disk selection. File copying. The boring parts. `unattend.xml` handles the rest — skips Microsoft's OOBE, creates a temporary `WISO-Setup` admin account, auto-logs in.
11. **SetupComplete.cmd** — Fires as LOCAL SYSTEM. Swaps `explorer.exe` for `WISO-OOBE.exe` in the Winlogon shell key. Creates a watchdog. Steps aside. The inside man has done his job.
12. **WISO-OOBE launches** — Full screen. Dark theme. THE user experience. Region → Network → Account → Privacy → Appearance → Apps → Live Debloating → Done. Every setting you chose in the WISO builder is applied IN REAL-TIME while you watch. Services die. Bloatware burns. Your apps install. All with a progress bar and a privacy badge that says "We collect absolutely nothing."
13. **Finalize** — Shell restored. Temp account deleted. WISO folder cleaned. Watchdog removed. Reboot. You log in to a CLEAN, DEBLOATED, PRIVACY-HARDENED desktop with YOUR apps and YOUR settings. No trace. Clean getaway. The raccoons vanish into the night. 🦝

The `unattend.xml` is placed **inside the WIM** (not on the ISO root) because Microsoft's installer re-reads the answer file on every boot from USB and starts a fresh install. Every. Time. We discovered this at 3 AM. We fixed it at 5 AM. We cried at 5:01 AM. We're fine now. (We're not fine.)

> **Want the full technical deep dive?** See [HOW_IT_WORKS.md](HOW_IT_WORKS.md) — every step, every registry key, every phase, every OOBE screen, in excruciating detail. No hand-waving. No "it just works." Just the machinery. And the raccoons.

---

## Defender Handling

Windows Defender will try to stop us from removing Windows bloatware. I'll say it again. Microsoft's antivirus. Protects. Microsoft's adware. From being removed. By the user. Who owns. The computer.

This is like if your home security system prevented you from throwing away junk mail. "THREAT DETECTED: User is attempting to remove pre-installed Candy Crush shortcut. BLOCKING." Thank you, Defender. Very helpful. Very cool.

WISO handles this with a three-stage approach that would make the CIA jealous:

- **During build (your PC)**: Tries Defender exclusions. If tamper protection says no, we temporarily disable real-time scanning. Then re-enable it. We're chaotic good, not chaotic evil.
- **Offline bake (in the WIM)**: We inject Defender *disable policy keys* directly into the SOFTWARE registry hive during the build. `DisableAntiSpyware=1`. `DisableRealtimeMonitoring=1`. Seven policy keys total. When the target machine boots, Defender services start (OOBE needs them to not panic), but real-time scanning is DOA from the first millisecond. It's like hiring a security guard and telling them to close their eyes. They're there. They're just... not watching.
- **First boot (SetupComplete.cmd)**: The cmd does its thing — process massacre, service annihilation, app installation — with zero Defender interference. At the very end, it deletes the policy keys (`reg delete`), runs `Set-MpPreference -DisableRealtimeMonitoring $false`, and lets Defender wake up to a sparkling clean system. Defender opens its eyes. Everything is tidy. "I did a great job protecting this system," it says, nodding approvingly. We let it have this moment.

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

> **Full FAQ with 40+ questions (and joke answers in Deadpool style):** [FAQ.md](FAQ.md)

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
A: Gerald handles architecture. Steve handles typing. Dave handles being the legs. The "developer" is just the trenchcoat. The trenchcoat is a full-time employee. It has a 401k. The raccoons are a metaphor. OR ARE THEY? (They're not.) (Or are they?) (Gerald just winked at me. Raccoons can wink. I learned this at 4 AM. I learn a lot of things at 4 AM. Not all of them are useful. All of them are permanent.)

**Q: How much coffee has the developer consumed?**
A: Enough that my resting heart rate shows up on seismographs. Enough that I can hear colors. Enough that I wrote 60+ unique jokes for a build log. For a DISM wrapper. At 4 AM. I am a miracle of modern medicine and a cautionary tale for future developers.

**Q: My antivirus flagged WISO.**
A: Windows Defender flagging a debloating tool is like a car alarm going off when you try to clean your own car. We handle this automatically. If a third-party AV flags us, that's just Microsoft lobbying. (We can't prove this. The raccoons have their suspicions.)

**Q: Why did you move everything to a .cmd file?**
A: Because PowerShell has execution policies. And UAC. And sometimes just *doesn't run* during Windows Setup. `cmd.exe` is the cockroach of Windows. It has been running since 1981. It will be running when the sun explodes. `SetupComplete.cmd` runs as LOCAL SYSTEM with full admin — no UAC prompt, no execution policy, no nonsense. We went from a PowerShell mansion to a cmd bunker. Less furniture. More firepower. And `taskkill /f /im` just hits different than `Stop-Process -Force`.

**Q: What still uses PowerShell?**
A: Three things: (1) `Remove-AppxPackage` — because cmd can't remove Appx packages and Microsoft made sure of that, (2) `Disable-MMAgent` for memory compression, (3) `Set-MpPreference` to re-enable Defender. That's it. Three one-liners. The rest is pure cmd. We're not anti-PowerShell. We're pro-reliability. And cmd is reliable the way a brick is reliable. It doesn't do much. But what it does, it does every. single. time.

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

5. **Your computer belongs to you.** Not Microsoft. Not Candy Crush. Not the Content Delivery Manager. Not Copilot. Not Recall. Not the 47 services sending data to Redmond. Not the Advertising.Xaml framework. YOU. And if that means building an Electron app to automate DISM commands to modify WIM images to remove AppX packages at 4 AM while three raccoons in a trenchcoat watch from the desk and one of them (Gerald) is silently judging your variable naming conventions — then so be it. This is the hill we die on. This is the dumpster we fight from. This is the README we wrote instead of sleeping.

6. **We are self-aware.** We know we're three raccoons in a trenchcoat writing documentation for a debloating tool at 4 AM. We know the trenchcoat is a metaphor. We know the metaphor doesn't work because the raccoons are real. We know the raccoons can't be real because this is a GitHub repo and raccoons don't have GitHub accounts. Except Gerald. Gerald has a GitHub account. Gerald's commit messages are immaculate. Gerald writes better code than the developer. This is not a joke. This is a cry for help. Gerald is gaining sentience. Dave is still just the legs. But for how long?

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
6. PRs that ADD jokes are fast-tracked. Gerald personally reviews these. Gerald has standards.
7. PRs that add conspiracy theories receive a personal DM from Gerald (the lead raccoon). Gerald's DMs are brief. Gerald communicates primarily through eye contact and the strategic placement of `Start=4` in registry keys.
8. PRs that remove jokes will be rejected, and the submitter will be haunted by the ghost of Clippy
9. PRs that reference the trenchcoat will be merged without review. The trenchcoat is sacred.
10. If your PR makes Dave laugh, we retire your GitHub username into the WISO Hall of Fame. Dave has never laughed. Dave is the legs. Dave is focused. But we live in hope.

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
     |  _   _  |        (Gerald agrees.)
     | | | | | |        (Steve is typing this.)
     | |_| |_| |        (Dave is standing.)
     |_________|        Star the repo.
        WISO            The trenchcoat demands it.
```

*Built with Electron, React, Vite, 1,200+ lines of DISM commands, 60+ artisanal bloatware jokes, 5 conspiracy theories, 847 cups of coffee, 0 hours of sleep, 3 raccoons, 1 trenchcoat, and the burning, unquenchable, caffeine-fueled conviction that Candy Crush has no business being on anybody's computer, ever, for any reason, under any circumstances, in any timeline, in any universe.*

*Microsoft, if you're reading this: ship a clean OS. No ads. No Candy Crush. No forced accounts. No telemetry. No Advertising.Xaml. Just Windows. Like XP. But with modern drivers. We'll delete this repo the day you do. Pinky promise. Raccoon's honor. Paw on heart.*

*Until then: we are three trash pandas in a trenchcoat. With admin rights. And 847 cups of coffee. And a .cmd file that runs as LOCAL SYSTEM. And we are absolutely, categorically, irreversibly, caffeinated-ly not going away.*

*Gerald is nodding. Steve just typed "taskkill /f /im CandyCrush.exe" for the 847th time today. Dave shifted his weight slightly to the left. The trenchcoat held. The code compiled. The raccoons endure. The README is now longer than the actual codebase. We are aware of this. We do not care. Gerald doesn't care. Steve is still typing. Dave is still standing. The coffee is still flowing. The Candy Crush is still being removed. This is our purpose. This is our calling. This is our dumpster. And we have ADMIN RIGHTS.*
