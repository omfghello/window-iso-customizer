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

## Customization & Options

### Can I customize what gets removed?

Yes. Everything is toggleable. Want to keep Edge? Uncheck it. Want to keep the Xbox app? Use the Gaming preset. Want to keep Candy Crush? We're not judging. We're *disappointed*. But we're not judging. The GUI has checkboxes. Use them. We believe in freedom. Unlike Windows OOBE, which believes in forced Microsoft account sign-in and "suggested" apps that are actually ads.

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

### Why does the first logon take so long?

`SetupComplete.cmd` is executing 400+ lines of batch fury as LOCAL SYSTEM. It's `taskkill`-ing 80+ processes. `sc config`-ing 50+ services to disabled. Running a PowerShell one-liner to remove 150+ Appx packages (3 passes). Uninstalling OneDrive from 6 angles. `robocopy`-ing your app installers to the Desktop. Running MSI/EXE/MSIX installers (7 silent flag combos per EXE). Setting up power plans via `powercfg`. It's a lot. We're not lazy. We're *thorough*. It's like a spa day for your OS. Except instead of massage it's `taskkill /f /im`. Same energy. More violence.

### What about the Windows ADK?

You need it. For oscdimg. The thing that creates bootable ISOs. It's 6GB for one 2MB executable. Typical Microsoft. The installer can set it up for you. Or you can do it manually. We're not picky. We're just needy.

### Why is the installer so big?

Electron. Chromium. Node. React. We know. The tool that removes bloat ships in a 200MB wrapper. The tool that fights JavaScript is written in JavaScript. We are a paradox. We are an ouroboros. We are a snake eating its own tail while complaining about the taste. If you can do better in C, we dare you. We tried. At 4 AM. The raccoons staged an intervention.

### Why did you move everything to a .cmd file?

Because PowerShell has execution policies. And UAC. And sometimes just *doesn't run* during Windows Setup because the universe is personally against us. We had a beautiful 300-line PowerShell script. It was elegant. It was verbose. It used `try/catch` blocks and `-ErrorAction SilentlyContinue` like a civilized language. And then Windows said "nah, execution policy says no." At 3 AM. During a test. After we'd been debugging for 4 hours. So we snapped. We moved EVERYTHING into `SetupComplete.cmd`. 400+ lines of pure batch. `taskkill /f /im`. `sc config start= disabled`. `reg add`. No execution policy. No UAC. LOCAL SYSTEM. Full admin. cmd.exe is the cockroach of Windows. It's been running since 1981. It will be running when the sun explodes. We respect that. Also, `taskkill /f /im` just *hits different* than `Stop-Process -Force`. It's the same thing but angrier.

### What still uses PowerShell?

Three things. Exactly three. We counted. We're not happy about it but cmd literally cannot do these:

1. `Remove-AppxPackage` — Because Microsoft tied Appx package management exclusively to PowerShell cmdlets. No cmd equivalent. No API. Nothing. You MUST use PowerShell to remove Appx packages. We're 99% sure Microsoft did this specifically to annoy us.
2. `Disable-MMAgent` — For disabling memory compression in Ultra Performance mode. No cmd equivalent. One line.
3. `Set-MpPreference` — For re-enabling Defender at the end. No cmd equivalent. One line.

That's it. Three one-liners. Embedded in cmd via `powershell -Command "..."`. The rest is pure cmd. We're not anti-PowerShell. We're pro-reliability. And cmd is reliable the way a brick is reliable. It doesn't do much. But what it does, it does every. single. time.

### What's SetupComplete.cmd?

It's the file Windows was BORN to run. If `%WINDIR%\Setup\Scripts\SetupComplete.cmd` exists, Windows executes it as LOCAL SYSTEM after OOBE completes. Full admin. No UAC prompt. No execution policy. Microsoft built this hook for OEMs like Dell and HP to install their bloatware. The irony is not lost on us. We are now Dell. Three raccoons in a Dell costume. With a .cmd file. And 80 `taskkill` commands. And a dream. The Dell of debloating. The HP of hatred for Candy Crush.

### What's firstlogon-options.cmd?

It's your build settings in cmd format. `set "WISO_REMOVE_BLOAT=1"`. `set "WISO_GAMING_MODE=0"`. `set "WISO_ULTRA_PERF=1"`. Poetry. cmd can't parse JSON. We tried during development. cmd looked at the JSON. cmd looked at us. cmd produced errors that made the raccoons cry. So we told the builder to write a `.cmd` file that just sets environment variables. `SetupComplete.cmd` loads it with `call firstlogon-options.cmd`. Boom. Configuration loaded. No JSON parser needed. No PowerShell needed. Just `set` commands. The way God intended.

### How do the EXE installers run silently?

We try 7 different silent flag combos like a lockpick set: `/VERYSILENT /SUPPRESSMSGBOXES /NORESTART /SP-` (Inno Setup), `/S` (NSIS), `/silent /norestart` (generic), `/quiet /norestart` (generic), `-s` (7-Zip style), `--silent` (some Linux-ported apps), `/qn /norestart` (MSI-wrapped EXE). One of the 7 picks usually works. If all 7 fail, we launch it interactive and let you click through. We're not monsters. We're raccoons who tried 7 times to be polite before kicking the door down.

### How does the 3-layer autostart work?

Layer 1: `SetupComplete.cmd` — Windows runs it as LOCAL SYSTEM. Guaranteed. The nuke. Layer 2: `FirstLogonCommands` in unattend.xml — backup trigger, runs `FirstLogonScript.ps1`. The pistol. Layer 3: `RunOnce` registry key — tertiary fallback. The knife. All three check the same `wiso-firstlogon-done.flag`. If the job's done, they all exit. If it's not done, the next layer picks up. Belt, suspenders, duct tape, and a raccoon holding your pants up. We've never had all three fail. If they did, we'd consider it a personal attack by the universe. And we'd add a Layer 4.

### Can I use a custom ISO?

Yes. Point us at your ISO. Or folder. We support both. We're flexible. We're also opinionated. But we're flexible about the *input*.

---

## Apps & Baking

### Can I add my own apps?

Yes. We have 50+ in the list. Browsers. Dev tools. Media. Utilities. Pick what you want. We download via winget. We bake them in. We run them at first logon. Like a sleeper agent. But with Firefox.

### What if winget fails to download?

Check your internet. Check if winget is installed. Check if the app ID is correct. We're not winget. We're winget's biggest fan. We're winget's *only* fan. If winget fails, we fail. We're in this together. Like a bad buddy cop movie.

### Do the baked-in apps install silently?

We try. HARD. MSI gets `msiexec /quiet /norestart /qn` — pure cmd, no PowerShell needed. EXE gets the full 7-flag treatment: `/VERYSILENT` (Inno), `/S` (NSIS), `/silent`, `/quiet`, `-s`, `--silent`, `/qn`. Seven attempts. Seven chances. If all 7 fail, we go interactive and let you click through. MSIX/APPX gets a PowerShell `Add-AppxPackage` one-liner (because cmd can't do Appx and we've accepted this trauma). All of this runs in `SetupComplete.cmd` as LOCAL SYSTEM. No UAC. No SmartScreen. No Zone.Identifier warnings (we stripped those). Think of it as a bonding experience. You and Firefox. Against the world. But mostly Firefox just installs silently and you never even notice.

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

**Stage 3 — FIRST BOOT (SetupComplete.cmd):** The cmd does its entire reckoning — process massacre, service annihilation, app installation — with zero Defender interference. At the very end, it deletes the policy keys (`reg delete`), runs `Set-MpPreference -DisableRealtimeMonitoring $false` (PowerShell one-liner, the only way to do it), and lets Defender wake up to a sparkling clean system. Defender opens its eyes. Everything is tidy. "I did a great job protecting this system," it says, nodding approvingly. We let it have this moment. It earned nothing. But we're generous.

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

Because we needed a GUI and we didn't want to learn WPF. The tool that removes bloat is 200MB. The tool that fights JavaScript is JavaScript. We are aware. We are at peace with this. In the same way that a person is at peace after their 16th coffee — technically calm, but vibrating at a frequency that concerns others. Gerald proposed rewriting it in Rust. Gerald can't write Rust. Gerald is a raccoon. But Gerald has OPINIONS. And those opinions are LOUD. (They are silent. Gerald communicates through eyebrow movements. Gerald has very expressive eyebrows for a raccoon.)

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
*Gerald (Chief Architecture Raccoon, Top of Trenchcoat)*
*Steve (Chief Typing Raccoon, Middle of Trenchcoat)*
*Dave (Chief Standing Raccoon, Bottom of Trenchcoat, Legs Division)*
*The Developer (the trenchcoat)*
*SetupComplete.cmd (honorary team member, runs as LOCAL SYSTEM, never complains, never sleeps, perfect employee)*

*[Gerald's final note: I have reviewed this FAQ. It is adequate. The jokes could be funnier. The developer is trying his best. Steve's typing has improved 12% since v2.0. Dave continues to stand. The trenchcoat continues to hold. Candy Crush continues to be removed. All is as it should be. — Gerald, from the top of the trenchcoat, surveying his domain]*
