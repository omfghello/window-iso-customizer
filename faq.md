# FAQ — Frequently Asked Questions (And Some We Made Up Because Gerald Thought It'd Be Funny)

*Look, we know you have questions. We have answers. The answers are jokes. But they're INFORMATIVE jokes. Like Deadpool explaining tax law while juggling chimichangas on a unicorn. You'll learn something. You'll also question your life choices. You'll question OUR life choices. You'll question why three raccoons in a trenchcoat are writing FAQ documents at 4 AM instead of, you know, being raccoons. These are all valid responses. Gerald wrote half of these. Steve typed them. Dave stood there. The trenchcoat held. Welcome to the FAQ. Abandon all sanity, ye who enter here.*

*[Gerald's note: I didn't write half of these. I wrote 73% of them. The developer wrote the rest while I was in the dumpster. His answers are worse. You can tell which ones are his because they lack the subtle sophistication that only a raccoon perched atop two other raccoons can provide.]*

---

## The Basics

### Is this legal?

Yes. We use Microsoft's own tools to modify Microsoft's own operating system. It's their DISM. Their PowerShell. Their answer file format. Their deployment pipeline. We're basically a very opinionated OEM. Dell puts bloat on your PC. We take it off. Same tools. Different vibes. Microsoft hasn't sued Dell. They're not gonna sue three raccoons in a trenchcoat. Probably. Gerald consulted a lawyer. The lawyer was also a raccoon. The legal advice was "just keep doing it and see what happens." This is the most raccoon legal strategy possible. Gerald is satisfied. Dave is standing. The trenchcoat persists.

### Will this break Windows Update?

No. Updates still work. Windows might *try* to reinstall Candy Crush via a feature update. That's when our verify script kicks in. Think of it as a bouncer. Candy Crush shows up with a fake mustache and a "Hello, I am definitely not Candy Crush" name tag. We still recognize it. We've seen this movie 47 times. The war is eternal. So are we. So is the coffee.

### Does this work with Windows 10?

Yes. Windows 10 has less bloat. It's like a house with termites. Windows 11 is a house *made of* termites. Both benefit from an exterminator. We are the exterminator. We have opposable thumbs. The termites do not. Advantage: us.

### Does this work with Windows 11?

That's... literally what we built it for. Windows 11 is where the bloat went from "annoying" to "existential crisis." We saw the Copilot. We saw the Recall. We saw the 47th Bing app. We said no. So hard it became software.

---

## Installation & Usage

### Can I use this on a work computer?

*Technically* yes. *Practically* ask your IT department. They might have a policy about modifying OS images. They might have a policy about raccoons. We don't know your workplace. What we do know: if you're the kind of person who runs custom WIM images on your work machine, you're either the IT department or you're about to have a very interesting conversation. We're rooting for you. From the dumpster.

### What if I already have Windows installed?

WISO is for *building* a custom ISO. You install that ISO on a clean machine. It's not an "upgrade your current Windows" tool. We're not magic. We're not a wizard. We're a very angry DISM wrapper. If you want to debloat an *existing* install, there are other tools. We're not them. We're the "build a clean ISO from scratch" people. We have a lane. We stay in it.

### Can I use this for multiple PCs?

Yes. One build. Infinite installs. You're not paying per license. You're not paying per raccoon. You're paying in coffee. (We're not paying. You're paying. For your own coffee. We're just saying.)

### Do I need to be connected to the internet?

**During the build:** Yes, if you're baking in apps. We use winget to download them. No winget = no Firefox. No internet = no winget. Math.

**On the target machine:** No. The ISO is self-contained. The apps are baked in. You could install Windows in a bunker. No signal. No problem. Just you, a USB, and a clean OS. And the raccoons. The raccoons are always there.

### What if I don't have admin rights?

You need admin. DISM needs admin. Mounting WIM images needs admin. Modifying the registry needs admin. We're sorry. We didn't make the rules. Microsoft did. We're just working within them. It's like needing a permission slip to rearrange furniture in your own house. Thanks, Microsoft. Very freedom. Much choice.

---

## The Custom OOBE (The Heist)

### Wait, you REPLACED the Windows OOBE?

Yes. The ENTIRE thing. Microsoft's "Hi there! Let's get you set up!" — gone. Replaced with our own custom Electron app. Dark-themed. Glassmorphic. Privacy-first. 10 screens. Live debloating. App installation. Raccoon-approved. We didn't skip the OOBE. We didn't bypass it. We REPLACED it. Like swapping the engine in a car while the driver is looking at the GPS. Gerald planned the heist. Steve typed it. Dave provided structural support. The trenchcoat held.

### How does the custom OOBE work?

When you install Windows from a WISO ISO, here's what happens: Windows Setup finishes. `unattend.xml` skips Microsoft's OOBE entirely and creates a temporary admin account (`WISO-Setup`). `SetupComplete.cmd` fires as LOCAL SYSTEM and does ONE thing: replaces `explorer.exe` with `WISO-OOBE.exe` in the `Winlogon\Shell` registry key. When the user logs in, instead of a desktop, they see our setup wizard. Full screen. Dark theme. Privacy badges. The whole heist. When they're done, we restore `explorer.exe`, delete the temp account, clean up, and reboot. Clean getaway. No evidence. Gerald vanishes into the night.

### Can I turn off the custom OOBE?

No. It's always on. Because it's BETTER. Microsoft's OOBE asks for your email, your phone number, your firstborn, and your consent to 47 different telemetry streams. Ours asks you what theme you want and whether you'd like to watch Candy Crush die in real-time. The choice is obvious. Gerald made this decision unilaterally from the top of the trenchcoat and nobody objected because Gerald is RIGHT.

### What if the custom OOBE crashes?

Three safety nets. Because Gerald is paranoid. And his paranoia is JUSTIFIED:

1. **Watchdog task** — fires 15 minutes after logon. If WISO-OOBE is still the shell, it restores `explorer.exe` and reboots. You get a normal desktop.
2. **Ctrl+Shift+F10 panic key** — press it during ANY OOBE screen. Immediate bailout. Shell restored. Desktop appears. No questions asked.
3. **Backup watchdog** — the OOBE app creates its own watchdog on launch, just in case the first one didn't get created.

In our testing (47 builds, 12 VMs, 3 physical machines, 1 dumpster Dell Optiplex), it has never crashed. But Gerald insisted on three safety nets anyway. Gerald has trust issues. With software. With Microsoft. With the concept of stability. Gerald has been hurt before. By `execution policy restriction`. At 3 AM. We respect Gerald's boundaries.

### Does the OOBE collect any data?

**ABSOLUTELY NOT.** Zero. Zilch. Nada. Nothing. We don't have a server. We don't have analytics. We don't have a database. We don't have a cloud. We don't have a backend. We are an Electron app running locally on your machine. Every screen has a "We collect absolutely nothing" privacy badge because the raccoons have STANDARDS. Microsoft's OOBE has 6 privacy toggles all defaulting to ON. Ours has 12 toggles all defaulting to PROTECT YOU. We collect less data than a rock. The rock at least absorbs heat. We absorb nothing.

### What screens does the custom OOBE have?

1. **Welcome** — "Your Windows, Your Way." Privacy badge. Sets the tone.
2. **Region & Language** — Standard setup. No ulterior motives.
3. **Network** — WiFi picker with signal strength bars. Skip if you want. We don't need internet to spy because WE DON'T SPY.
4. **Account** — Local OR Microsoft. Two big cards. YOUR choice. No hidden offline button. No disconnecting WiFi trick.
5. **Privacy** — 12 toggles. Paranoid Mode. Stock Windows mode. Every toggle has a real description.
6. **Appearance** — Dark/Light theme, accent color, taskbar alignment. Your PC, your look.
7. **Apps** — Every app you baked in, with toggles. Change your mind? Uncheck it.
8. **Setting Up** — THE SHOW. Live debloating progress. Services dying. Apps installing. With a progress bar and logs.
9. **Done** — Summary of everything. Account, theme, privacy, apps. One button: "Restart & Finish."
10. **Reboot** — Shell restored, temp account deleted, everything cleaned up. Clean getaway.

### Will this work with future Windows versions?

The shell hijack uses `Winlogon\Shell` — a registry key that has existed since Windows XP. `SetupComplete.cmd` has been a documented Windows hook since Vista. `unattend.xml` is Microsoft's own deployment format. We're using mechanisms that Microsoft has supported for 20 years. If they break these, every enterprise deployment tool in the world breaks too. So yes. It'll work. Unless Microsoft specifically targets raccoons. In which case, Gerald has a contingency plan. The contingency plan involves a second trenchcoat.

---

## Customization & Options

### Can I customize what gets removed?

Yes. Everything is toggleable. Want to keep Edge? Uncheck it. Want to keep the Xbox app? Use the Gaming preset. Want to keep Candy Crush? We're not judging. We're *disappointed*. But we're not judging. The GUI has checkboxes. Use them. We believe in freedom. Unlike Windows OOBE, which — oh wait, you'll never see Windows OOBE again. Because we replaced it. With ours. Which also has checkboxes. Better checkboxes. Dark-themed checkboxes. With privacy badges.

### What if I need OneDrive?

Don't enable "Remove OneDrive" in the build options. Or use a different tool. Or install OneDrive manually after. We're not the OneDrive police. We're the "OneDrive is optional" police. And we're very good at our job.

### What if I need Edge?

Same deal. Uncheck. Don't remove. We're not gonna force you. We're gonna *strongly suggest* you use Firefox. But we're not gonna force you. That would make us Microsoft. And we're not.

### What if I want to keep Copilot?

Uncheck. We're not gonna judge. We're gonna *strongly* judge. But we're not gonna stop you. We're chaotic good. Not chaotic dictator.

### What's the difference between the presets?

**Recommended:** Balanced. For normal people. Who exist. Somewhere.

**Privacy:** Maximum telemetry removal. For people who tape over their webcam. Who use a VPN. Who read privacy policies. We see you. We respect you.

**Gaming:** Keeps Xbox stuff. Game Mode on. Performance tweaks. For the "I need 3 more FPS or I will perish" demographic.

**Minimal:** Light touch. Just TPM bypass + OOBE skip. For the cautious. The "I want change but I'm scared" crowd. We're here when you're ready.

---

## Technical Stuff

### Why does the build take so long?

Extracting a 5GB ISO. Mounting a 5GB WIM. Modifying a 5GB WIM. Unmounting a 5GB WIM. Creating a 5GB ISO. It's a lot of GB. Also Defender is trying to scan everything. Also your disk is slow. Also we're not magic. Get a coffee. We have 847. We can share. (We can't. We need them.)

### What if I get a DISM error?

Check the error code. `0xC1510027` = "files in use." Usually Defender. Add exclusion. Or disable. We'll re-enable. Promise. `32` = mount failed. Try again. Sometimes it's a 5-second sleep. We added that at 4 AM. No comment. Future us will weep.

### Why does the first boot show a custom setup screen instead of Windows OOBE?

THAT'S OUR OOBE. We replaced Microsoft's. You're seeing WISO's custom setup wizard — a dark-themed Electron app that handles everything: region, network, account, privacy, appearance, apps, and live debloating. It's not a bug. It's the feature. THE feature. The feature we built an entire Electron app for. The feature Gerald planned from the top of the trenchcoat. Just go through the screens. Pick your settings. Watch the debloat happen. Enjoy the privacy badges. When you click "Restart & Finish," you'll get a clean desktop with YOUR settings and YOUR apps. No Microsoft interrogation. No Cortana. No "Hi there!" Just your computer. As God intended.

### Why does the "Setting Up" screen take a while?

The custom OOBE's "Setting Up" screen is doing ALL the debloating live while you watch. It's `taskkill`-ing 80+ processes. `sc config`-ing 50+ services to disabled. Running PowerShell one-liners to remove 150+ Appx packages (3 passes). Uninstalling OneDrive from 6 angles. Installing YOUR selected apps with 7 silent flag combos per EXE. Setting up power plans via `powercfg`. And showing you ALL of it on a progress bar with real-time logs. It's like watching surgery. But the patient is Candy Crush. And the surgeon is a raccoon. In a trenchcoat. With admin rights. And the anesthetic is `taskkill /f /im`.

### What about the Windows ADK?

You need it. For oscdimg. The thing that creates bootable ISOs. It's 6GB for one 2MB executable. Typical Microsoft. The installer can set it up for you. Or you can do it manually. We're not picky. We're just needy.

### Why is the installer so big?

Electron. Chromium. Node. React. TWICE. Because now we bundle TWO Electron apps — the main WISO builder AND the custom OOBE replacement. The tool that removes bloat ships with 350MB of JavaScript runtime. The tool that debloats Windows contains two copies of Chromium. We are a paradox inside an enigma wrapped in a trenchcoat. If you can do better in C, we dare you. We tried. At 4 AM. The raccoons staged an intervention. Gerald pointed at the OOBE's glassmorphic CSS and said "do THIS in C." We conceded the point. Gerald's paws were crossed. It was very dramatic. The trenchcoat billowed.

### Why did you move everything to a .cmd file?

Because PowerShell has execution policies. And UAC. And sometimes just *doesn't run* during Windows Setup because the universe is personally against us. We had a beautiful 300-line PowerShell script. It was elegant. It was verbose. It used `try/catch` blocks and `-ErrorAction SilentlyContinue` like a civilized language. And then Windows said "nah, execution policy says no." At 3 AM. During a test. After we'd been debugging for 4 hours. So we snapped. We moved EVERYTHING into `SetupComplete.cmd`. 400+ lines of pure batch. `taskkill /f /im`. `sc config start= disabled`. `reg add`. No execution policy. No UAC. LOCAL SYSTEM. Full admin. cmd.exe is the cockroach of Windows. It's been running since 1981. It will be running when the sun explodes. We respect that. Also, `taskkill /f /im` just *hits different* than `Stop-Process -Force`. It's the same thing but angrier.

### What still uses PowerShell?

Three things. Exactly three. We counted. We're not happy about it but cmd literally cannot do these:

1. `Remove-AppxPackage` — Because Microsoft tied Appx package management exclusively to PowerShell cmdlets. No cmd equivalent. No API. Nothing. You MUST use PowerShell to remove Appx packages. We're 99% sure Microsoft did this specifically to annoy us.
2. `Disable-MMAgent` — For disabling memory compression in Ultra Performance mode. No cmd equivalent. One line.
3. `Set-MpPreference` — For re-enabling Defender at the end. No cmd equivalent. One line.

That's it. Three one-liners. Embedded in cmd via `powershell -Command "..."`. The rest is pure cmd. We're not anti-PowerShell. We're pro-reliability. And cmd is reliable the way a brick is reliable. It doesn't do much. But what it does, it does every. single. time.

### What's SetupComplete.cmd?

It's THE INSIDE MAN. If `%WINDIR%\Setup\Scripts\SetupComplete.cmd` exists, Windows executes it as LOCAL SYSTEM after OOBE completes. Full admin. No UAC prompt. No execution policy. Microsoft built this hook for OEMs like Dell and HP to install their bloatware. We're using it for the opposite. But now its job is ELEGANT: it swaps `explorer.exe` for `WISO-OOBE.exe` in the Winlogon shell key, creates a watchdog task, and exits. That's it. The inside man opens the vault door and walks away. The heist crew (WISO-OOBE) does the rest. It used to be 400+ lines of fury. Now it's 20 lines of precision. The cmd evolved. From a blunt instrument to a surgical tool. Gerald approves of the refinement. Steve misses the 400 lines. Dave is indifferent. Dave is always indifferent. Dave is the legs.

### What's firstlogon-options.cmd?

It's your build settings in cmd format. `set "WISO_REMOVE_BLOAT=1"`. `set "WISO_GAMING_MODE=0"`. `set "WISO_ULTRA_PERF=1"`. Poetry. cmd can't parse JSON. We tried during development. cmd looked at the JSON. cmd looked at us. cmd produced errors that made the raccoons cry. So we told the builder to write a `.cmd` file that just sets environment variables. `SetupComplete.cmd` loads it with `call firstlogon-options.cmd`. Boom. Configuration loaded. No JSON parser needed. No PowerShell needed. Just `set` commands. The way God intended.

### How do the EXE installers run silently?

We try 7 different silent flag combos like a lockpick set: `/VERYSILENT /SUPPRESSMSGBOXES /NORESTART /SP-` (Inno Setup), `/S` (NSIS), `/silent /norestart` (generic), `/quiet /norestart` (generic), `-s` (7-Zip style), `--silent` (some Linux-ported apps), `/qn /norestart` (MSI-wrapped EXE). One of the 7 picks usually works. If all 7 fail, we launch it interactive and let you click through. We're not monsters. We're raccoons who tried 7 times to be polite before kicking the door down.

### How does the 3-layer autostart work?

Layer 1: `SetupComplete.cmd` — Windows runs it as LOCAL SYSTEM. Guaranteed. The nuke. Layer 2: `FirstLogonCommands` in unattend.xml — backup trigger, runs `FirstLogonScript.ps1`. The pistol. Layer 3: `RunOnce` registry key — tertiary fallback. The knife. All three check the same `wiso-firstlogon-done.flag`. If the job's done, they all exit. If it's not done, the next layer picks up. Belt, suspenders, duct tape, and a raccoon holding your pants up. We've never had all three fail. If they did, we'd consider it a personal attack by the universe. And we'd add a Layer 4.

### Can I use a custom ISO?

Yes. Point us at your ISO. Or folder. We support both. We're flexible. We're also opinionated. But we're flexible about the *input*.

### What's in firstlogon-options.json now?

EVERYTHING. All your build toggles — `removeBloat`, `ultraPerformance`, `gamingMode`, `removeEdge`, `removeOneDrive`, the works — PLUS a brand new `appsToInstall` array containing the winget package IDs for every app you selected.

It used to just be the build settings. Now it's the build settings AND your shopping list. The OOBE reads this file on first boot and knows EXACTLY what you wanted. Which apps to install. Which features to enable. Which bloat to obliterate. It's your entire build manifest in one JSON file. Gerald calls it "the heist blueprint." Steve calls it "a config file." Dave calls it nothing because Dave is the legs and the legs don't have opinions about serialization formats.

The `appsToInstall` field is what powers the Layer 3 winget fallback — if the baked-in installers can't be found on disk (Layers 1 and 2), the OOBE looks at these winget IDs and says "fine, I'll download them myself." It's the contingency plan's contingency plan. Gerald is proud. Gerald has never met a contingency he didn't want to nest inside another contingency. The trenchcoat contains multitudes. And also JSON.

---

## Apps & Baking

### Can I add my own apps?

Yes. We have 50+ in the list. Browsers. Dev tools. Media. Utilities. Pick what you want. We download via winget. We bake them in. We run them at first logon. Like a sleeper agent. But with Firefox.

### What if winget fails to download?

Check your internet. Check if winget is installed. Check if the app ID is correct. We're not winget. We're winget's biggest fan. We're winget's *only* fan. If winget fails, we fail. We're in this together. Like a bad buddy cop movie.

### Do the baked-in apps install silently?

We try. HARD. And now you can WATCH. The custom OOBE's "Setting Up" screen installs your apps live with a progress bar. MSI gets `msiexec /quiet /norestart /qn`. EXE gets the full 7-flag treatment: `/VERYSILENT` (Inno), `/S` (NSIS), `/silent`, `/quiet`, `-s`, `--silent`, `/qn`. Seven attempts. Seven chances. If all 7 fail, we go interactive and let you click through. MSIX/APPX gets a PowerShell `Add-AppxPackage` one-liner (because cmd can't do Appx and we've accepted this trauma). And you can PICK which apps to install during the OOBE — there's a whole screen for it. Changed your mind about VLC? Uncheck it. Want everything? Leave it all checked. It's YOUR computer. We're just the raccoons making it work. The trenchcoat provides oversight.

### What if my baked-in apps aren't found during setup?

Then BUCKLE UP, because your apps have **THREE entire chances** to be installed. Three. Layers. Of. Resilience. Like a raccoon trenchcoat but for software deployment:

**Layer 1 — Expected Path:** We check `C:\Windows\Setup\Scripts\installers\` where the baked-in installers SHOULD be. This is the front door. The polite approach. The "excuse me, are you home?" knock.

**Layer 2 — Filesystem Scan (The Bloodhound):** If Layer 1 finds nothing, we unleash a BFS (breadth-first search) scanner across your entire filesystem. It sniffs through `C:\Windows\Setup\`, `C:\`, `C:\WISO`, `C:\ProgramData`, and more. With a depth limit of 4. Because even bloodhounds need boundaries. We built a digital truffle pig. A filesystem archaeologist. Indiana Jones but for `.exe` files in weird directories.

**Layer 3 — Winget Fallback:** If BOTH previous layers came up empty — if your installers somehow evaporated into the quantum foam — we fall back to `winget install` at runtime. Live. From the internet. Like ordering pizza because the fridge is empty.

If your apps fail all three layers, they didn't WANT to be installed. They chose this. They chose the void. They looked at three separate rescue attempts and said "no thank you, I prefer nonexistence." We respect that. We don't understand it. But we respect it. Gerald says it's a metaphor for something. Gerald won't say what. Gerald is staring into the middle distance. The trenchcoat is concerned.

### What's the filesystem scanner?

We built a BLOODHOUND. A digital truffle pig. A search-and-rescue dog but for installer files that got lost during the Windows Setup shuffle.

Here's the deal: sometimes Windows, in its infinite wisdom, rearranges your files during installation like a toddler "organizing" a bookshelf. The installer files you baked in might end up somewhere unexpected. So we built `getInstallers` — a breadth-first search scanner that systematically sniffs through every likely location on the filesystem:

- `C:\Windows\Setup\Scripts\` (and subdirectories)
- `C:\Windows\Setup\` (the parent, in case files got ambitious)
- `C:\` (root level, casting a wide net)
- `C:\WISO` (our own staging area)
- `C:\ProgramData` (the junk drawer of Windows)

It scans with a depth limit of 4 directories deep. Because we're thorough, not insane. (Debatable.) Every file it finds gets logged — exact path, exact filename, exact "oh THERE you are" energy. The diagnostics output tells you exactly what was found and where, so if something goes sideways, you know exactly where to look. Gerald designed the search pattern. Gerald has found things in dumpsters that humans couldn't find with GPS. Gerald's search algorithms are instinct-driven. The trenchcoat nods approvingly.

### What if I don't have internet for the winget fallback?

Then it's a good thing we have TWO layers before we even THINK about winget. The baked-in installers are physically ON YOUR DISK. They were put there during the build process. They're IN the ISO. They traveled with you. Like luggage. Like LOYAL luggage.

Layer 1 checks the expected path. Layer 2 scans the filesystem with the intensity of a raccoon who smells pizza at 3 AM. Between these two, your installers WILL be found. Winget is the Layer 3 last resort — the "break glass in case of emergency" option. The nuclear option. The "everything else has somehow failed and honestly how did you even get here" option.

If you're installing Windows in a bunker, a submarine, or a particularly well-shielded dumpster — no internet needed. The apps are already there. The bloodhound will find them. Gerald guarantees it. Gerald's guarantee is backed by the full faith and credit of three raccoons and one load-bearing garment. This is worth more than most warranties.

### Does the OOBE only do things I selected?

**YES.** Every. Single. Action. Is. Gated. Behind. Your. Settings.

The custom OOBE doesn't just blindly execute a script and hope for the best. Every action — EVERY action — checks your settings first. Didn't enable "Remove Bloat"? The bloat lives. Didn't select Firefox in the app list? Firefox stays unbaked. Didn't toggle "Ultra Performance"? Your power plan remains untouched.

It's like a heist crew that checks the blueprint before every single move. "Are we supposed to crack this safe?" *checks plan* "Client didn't circle it. Moving on." Gerald insisted on this architecture. Gerald has OPINIONS about conditional execution. Gerald believes that software should do what the user ASKED and nothing more. This is radical in an industry where every app installs a browser toolbar and three weather widgets. The trenchcoat operates on a strict consent-based framework. Dave is the enforcement mechanism. Dave is the legs, but Dave is also the BRAKES. Nothing moves without Dave's structural approval. And Dave checks the settings.

---

## Antivirus & Security

### My antivirus flagged WISO.

Of course it did. We disable Defender. We modify the registry. We kill processes. We look like malware. We're not. We're the *opposite* of malware. We're anti-malware. We're anti-bloat. We're anti-Candy-Crush. Read [ANTIVIRUS.md](ANTIVIRUS.md) for the full breakdown. TL;DR: add an exclusion. Or trust us. The raccoons are trustworthy. We're not sure about the developer. But the raccoons.

### What about the TPM bypass — is it safe?

We empty one DLL and add three registry keys. The DLL is what checks if your PC meets Windows 11 requirements. Empty = no check = "you're good." The registry keys tell Setup to skip TPM, Secure Boot, RAM checks. We're not breaking security. We're bypassing a *hardware* check. Your PC might not have TPM 2.0. We're saying "install anyway." The rest of Windows security (Defender, BitLocker, etc.) still works. We're not crazy. We're *pragmatic*.

### Is my data safe?

We don't touch your data. We don't send anything. We don't have analytics. We don't have a server. We're an Electron app that runs DISM. Locally. On your machine. Your data stays on your machine. We're not Microsoft. We're not collecting anything. We're not even sure we have a budget for servers.

---

## VM & Testing

### Does this work in a VM?

Yes. We broke Windows 4 times in a VM during development. And that was just Tuesday. So yes. It works. Now. After many tears. And many coffees.

### Why does the VM keep rebooting to the installer?

**DON'T PRESS ANY KEY.** When Windows Setup reboots mid-install, it's booting from the *hard drive* now. If you mash a key, it boots from the DVD again. "Install now." We've been there. We've cried there. The fix: let it boot. Don't touch anything. Or disconnect the ISO before the reboot. Or change boot order. Or scream. All three are valid.

### Can I run WISO from a USB without installing?

The portable build (`win-unpacked/WISO.exe`) doesn't need installation. Copy the folder. Run the exe. You still need admin. You still need the ADK. You still need coffee. But no installer. For the trust-issues crowd. We see you.

---

## Edge Cases & Weird Stuff

### I removed the Microsoft Store and now I can't install Store apps.

That's... what removing the Store does. We warned you. There was a table. With jokes. You read the jokes. You laughed. You removed it anyway. This is on you. We respect the commitment. If you need Store apps, don't remove the Store. Or use a different build. We're not gonna judge. We're gonna *strongly* judge.

### Candy Crush came back after a Windows Update.

The Content Delivery Manager is a digital cockroach. We removed it. Windows Update resurrected it. It re-installed Candy Crush. It's the circle of bloat. The Lion King but depressing. Our verify script runs at 2, 5, 15, 30, 60 minutes. It'll catch it. The war is eternal. We are eternal. We are caffeinated beyond mortality.

### Why does Defender keep blocking?

Defender protects Microsoft's bloatware from being removed. We're not kidding. Microsoft's antivirus. Protects. Microsoft's adware. From you. We add exclusions. If that fails, we temporarily disable. Then we re-enable. We're chaotic good. Not chaotic evil. We're not leaving you unprotected. We're just... moving the guard for a moment. So we can do our job.

### How is Defender handled now? (The Three-Stage Approach)

Three stages. Like a rocket. But instead of going to space, we're going to a clean Windows.

**Stage 1 — BUILD (your PC):** Defender exclusions or temporary disable on your build machine. Standard stuff. We've been doing this since v1.

**Stage 2 — OFFLINE (in the WIM):** NEW. We bake 7 Defender policy keys directly into the WIM's SOFTWARE registry hive. `DisableAntiSpyware=1`. `DisableRealtimeMonitoring=1`. Five more. When the target machine boots, Defender services start (OOBE needs them or it has a full-blown panic attack), but real-time scanning is already dead. It's like hiring a security guard and telling them to close their eyes. They're on the payroll. They're at their post. They're just... not looking.

**Stage 3 — FIRST BOOT (WISO-OOBE):** The custom OOBE does its entire reckoning — process massacre, service annihilation, app installation — with zero Defender interference. You watch it happen live on a progress bar. At the very end, it deletes the policy keys (`reg delete`), runs `Set-MpPreference -DisableRealtimeMonitoring $false` (PowerShell one-liner, the only way to do it), and lets Defender wake up to a sparkling clean system. Defender opens its eyes. Everything is tidy. "I did a great job protecting this system," it says, nodding approvingly. We let it have this moment. It earned nothing. But we're generous. And the user saw the whole thing on a progress bar. With a "We collect absolutely nothing" badge. Chef's kiss.

### Can I undo this?

You can reinstall Windows. That's the undo. We're not a time machine. We're a very angry DISM wrapper. We make ISOs. We don't unmake them. We don't have that power. We wish we did. We'd unmake the Zune app. From 2012.

---

## Existential & Meta (Gerald's Favorite Section)

*[Gerald's note: This section exists because the developer started asking himself questions at 4 AM and couldn't stop. I let it happen. Steve typed it. Dave stood. The trenchcoat bore witness.]*

### Is WISO made by actual raccoons?

Gerald handles architecture and existential dread. Steve handles typing and snack procurement. Dave is the legs. The "developer" is what we tell the IRS. The trenchcoat is a load-bearing garment. Remove the trenchcoat and the project collapses. We tested this hypothesis at 3 AM during a sprint review. Gerald fell. Steve panicked. Dave remained standing because Dave is a PROFESSIONAL. The git log shows a commit at 3:07 AM with the message "fix: reattach Gerald to Steve." We don't talk about that commit. The raccoons are a metaphor. OR ARE THEY? (Gerald just stared into the camera. There is no camera. Gerald doesn't care. Gerald stares anyway.)

### How much coffee has the developer consumed?

Enough that my resting heart rate shows up on seismographs. Enough that I can hear colors. Enough that I wrote 60+ unique jokes for a build log. For a DISM wrapper. At 4 AM. I am a miracle of modern medicine and a cautionary tale for future developers. Gerald has never had coffee. Gerald runs on pure spite and the ambient energy of badly configured Windows services. Gerald is more productive than me. This bothers me.

### Why not just use Linux?

Because I want to play games and use Adobe products. Don't @ me. Also, if everyone used Linux, I'd be an unemployed raccoon. I have 847 cups of coffee to pay for. This project IS the payment. Gerald tried Linux once. Gerald said nothing (raccoon). But Gerald uninstalled it and went back to the dumpster. The dumpster runs Windows. We're not kidding. The dumpster has a Dell Optiplex in it. With our custom ISO. It's beautiful.

### Why Electron?

Because we needed a GUI and we didn't want to learn WPF. The tool that removes bloat is 200MB. The tool that fights JavaScript is JavaScript. And now we built a SECOND Electron app — the custom OOBE — that ALSO runs in JavaScript. On first boot. As the Windows shell. The irony is so thick you could spread it on toast. Two Electron apps. One trenchcoat. Three raccoons. Zero self-awareness. But you know what? The OOBE is BEAUTIFUL. Dark-themed. Glassmorphic. Privacy badges. Live debloating progress. It looks better than anything Microsoft has ever shipped in an OOBE. And it's built in Electron. The bloat-removal tool. Made of bloat. Replacing the bloat. With style. Gerald proposed rewriting it in Rust. Gerald can't write Rust. Gerald is a raccoon. But Gerald has OPINIONS. And those opinions now include CSS transition timing functions. Gerald's CSS opinions are surprisingly valid.

### Will Microsoft ban me?

No. We're not modifying your Microsoft account. We're modifying a local Windows install. Microsoft doesn't care. They have 1.4 billion Windows users. They're not tracking which ones used a custom ISO. They're not gonna show up. Probably. Gerald has a contingency plan. The contingency plan is: if Microsoft shows up, Gerald climbs out of the trenchcoat, Steve follows, Dave runs. The trenchcoat stays behind as a decoy. The trenchcoat has done this before. The trenchcoat is experienced.

### Does this affect Windows activation?

No. Activation is tied to your hardware. Or your Microsoft account. Or your product key. We don't touch any of that. We're not pirates. We're not even sailors. We're raccoons. With a very specific skill set. A skill set that involves `reg add`, `taskkill /f /im`, and fitting three mammals into one garment.

### Are you aware this FAQ is longer than most legal documents?

Yes. Gerald is aware. Steve's paws are tired. Dave's legs are fine. The trenchcoat has no opinion. We are writing a FAQ for a DISM wrapper. The FAQ has 50+ questions. Some of them are real. Some of them are jokes. ALL of them were written between 2 AM and 5 AM. We are aware that normal projects have a 10-question FAQ. We are not a normal project. We are three raccoons. In a trenchcoat. With admin rights. And a .cmd file. And a vendetta against Candy Crush. Normal was never an option.

### Is this README/FAQ the real product?

Yes. The clean Windows ISO is a side effect. The documentation is the main event. You came for the debloating. You stayed for the raccoon lore. Gerald knows this. Gerald planned this. Gerald is playing 4D chess. Steve is typing the chess moves. Dave is the board.

---

## What We Don't Do

### Can I install WISO on a Mac?

No. WISO is a Windows app. It runs DISM. DISM is Windows. We're not cross-platform. We're not even cross-room. We're Windows-only. We're sorry. The raccoons are sorry. The Mac users are... probably fine. They have their own problems.

### Does this work with Windows Server?

We haven't tested it. Server has different images. Different editions. Different bloat. We're built for Desktop. We're not gonna stop you from trying. We're just not gonna promise it. We're honest raccoons.

### What about BitLocker?

We can disable auto-encryption in the autounattend. We don't touch existing BitLocker. We don't encrypt. We don't decrypt. We're not that kind of party. We're the "remove Candy Crush" party.

---

## Still Have Questions?

*If your question wasn't here, we're either: (a) still writing it, (b) it's too weird and we're scared, or (c) the raccoons ate it. Try the [GitHub issues](https://github.com/omfghello/window-iso-customizer/issues). Or just scream into the void. We'll hear you. We're always listening. We're always watching. We're always caffeinated. We now have a .cmd file that runs as LOCAL SYSTEM. We are unstoppable.*

*— The WISO Team*
*Gerald (Chief Architecture Raccoon & Heist Planner, Top of Trenchcoat)*
*Steve (Chief Typing Raccoon & OOBE CSS Consultant, Middle of Trenchcoat)*
*Dave (Chief Standing Raccoon & Getaway Legs, Bottom of Trenchcoat, Legs Division)*
*The Developer (the trenchcoat, now housing TWO Electron apps)*
*SetupComplete.cmd (the inside man, runs as LOCAL SYSTEM, swaps the shell, steps aside)*
*WISO-OOBE.exe (the payload, runs as the Windows shell, replaced Microsoft's entire first-boot experience, zero regrets)*

*[Gerald's final note: I have reviewed this FAQ. It is adequate. The Custom OOBE section is my finest work. The developer wrote the code but I PLANNED the heist. Steve typed the heist code. Dave was the legs during testing. The trenchcoat held through 47 test builds. The OOBE has never crashed. The watchdog has never fired. The panic key has never been pressed. This is because of MY architecture. MY planning. MY raccoon instincts. The developer's code is... acceptable. Steve's CSS is improving. Dave remains structurally sound. The FAQ now has 60+ questions. We are out of control. We are MAGNIFICENTLY out of control. — Gerald, from the top of the trenchcoat, having just replaced Microsoft's entire Out-of-Box Experience with a raccoon-approved alternative]*
