# Adobe Hunter — Firewall Nuke Edition

A single self-elevating Windows batch script that finds every Adobe Creative Cloud / Adobe Suite executable on your machine and blocks it at the Windows Firewall — inbound and outbound, no exceptions. No installer, no dependencies beyond what Windows already ships with (`cmd.exe`, `PowerShell`, `netsh`/`NetSecurity`).

Includes an optional, fully reversible **Zombie Mode** that goes a step further and neuters Creative Cloud's own background hub/helper processes directly, without ever touching your real Adobe applications.

> Everything this tool does is local and reversible. It does not delete, uninstall, or modify anything outside of Windows Firewall rules and — only if you explicitly opt in — a handful of Creative Cloud helper `.exe` files, which it always backs up first.

## What it does

- **Finds Adobe, everywhere.** Scans every drive you select (or custom folders you add) for anything Adobe: Photoshop, Illustrator, InDesign, Premiere, After Effects, Audition, Lightroom, Acrobat, Dreamweaver, Media Encoder, Character Animator, Prelude, Substance 3D, Fresco, Dimension, Adobe XD, Adobe Express, Bridge, Animate, InCopy, Adobe Fonts, Camera Raw, Adobe Sign, Creative Cloud's own hub and every background helper (CoreSync, CCXProcess, AAMUpdater, Adobe Genuine Service, the IPC broker, and more) — not just the Creative Cloud app itself.
- **Cross-checks the registry, AppX packages, running processes, Windows services, scheduled tasks/startup items, and existing firewall rules**, so nothing installed outside the obvious `Program Files` folders slips through. A one-line count per category prints to the console; full detail goes into a report file.
- **Blocks every executable it finds**, inbound and outbound, for every Windows Firewall profile.
- **Deduplicates against every existing firewall rule on the system** — including ones you created manually before ever running this tool — so you end up with exactly one inbound and one outbound block per executable, never duplicates.
- **Never sits there in silence.** Every phase — folder scanning, indexing existing rules, creating new rules, zombifying — prints live progress to the console (a running count, a percentage, or a "still working, N checked so far" heartbeat at least every couple of seconds). Windows' own native progress bars are explicitly suppressed so they never cover the console output.
- **Self-elevates.** Launches itself as Administrator automatically; you don't need to right-click → Run as administrator yourself.
- **Never closes on you unexpectedly.** If something goes wrong mid-run, the window stays open so you can read the error. On a normal finish it shows a summary and closes itself; choosing **[Q] Quit** from the menu closes immediately, no confirmation needed.
- **Fully reversible.**
  - **[5] UNDO** removes every firewall rule this tool created, in one click.
  - **[7] UNDO ZOMBIE MODE** restores every executable Zombie Mode touched, byte for byte.

## Zombie Mode

Zombie Mode is separate from — and meant to run after — the firewall block. It targets **only** Creative Cloud's own central hub and background helper executables (CoreSync, CCXProcess, CCLibrary, AAMUpdater, Adobe Genuine Service, the IPC broker, the Elevation Manager, and similar). **It never touches your actual Adobe applications** — Photoshop, Illustrator, Premiere, etc. are explicitly excluded, checked against an internal allow-list before anything is touched.

For each target:
1. The real `.exe` is renamed with `ORIGINAL` appended (e.g. `CoreSync.exe` → `CoreSync.exeORIGINAL`).
2. A brand new, completely empty `.exe` is created using the original file name.

Windows and any installer/launcher still finds the file exactly where it expects it — nothing reports as "missing," nothing errors out — but if anything tries to run it, it does nothing, because the file is empty. If a component is currently running, its process (and matching Windows service, if any) is stopped automatically first so the rename can go through.

Running the scan again afterward will not re-zombify anything: already-zombified files and their backups are recognized and skipped automatically, so re-running the tool never stacks `.exeORIGINALORIGINAL...` copies.

**[7] UNDO ZOMBIE MODE** puts every original executable back and removes the empty stubs, at any time.

## Usage

1. Download `Adobe_Suite_Nuke_Hunter.bat`.
2. Double-click it (or run it from a terminal). It will ask for Administrator rights automatically — accept the UAC prompt.
3. Pick a scan scope with **[D]** if you want to limit it to specific drives or add custom folders (by default it scans every fixed drive).
4. Pick an option from the menu:

   | Option | What it does |
   |---|---|
   | **[1]** | Dump paths only — scan and export the list of every Adobe `.exe` found, no blocking |
   | **[2]** | Firewall rules only — block every Adobe `.exe` found |
   | **[3]** | Do both — dump the lists, then block |
   | **[4]** | View every firewall rule this tool currently has active |
   | **[5]** | UNDO — remove every rule this tool created |
   | **[6]** | ZOMBIE MODE (optional, reversible) |
   | **[7]** | UNDO ZOMBIE MODE |
   | **[D]** | Change scan scope |
   | **[Q]** | Quit |

After blocking with **[2]** or **[3]**, the tool offers to run Zombie Mode right away, without having to go back to the menu.

## Requirements

- Windows 10 or 11 (uses the built-in `NetSecurity` PowerShell module — `New-NetFirewallRule` / `Get-NetFirewallRule`, standard since Windows 8/Server 2012).
- Administrator rights (requested automatically).
- Nothing else. No installation, no external downloads, no third-party tools.

## Disclaimer

This tool blocks Adobe software from reaching the network and, optionally, disables Creative Cloud's background components. That's the entire point of it, and it will very likely break Creative Cloud's license checks, cloud sync, and auto-updates for as long as the rules/Zombie Mode stay active — that is expected, not a bug. Everything it does is scoped to Windows Firewall rules and, only with Zombie Mode, a small set of backed-up executables, and everything is reversible through options **[5]** and **[7]**. Use it at your own risk and discretion.

## Credits

Made by **YoRHa Numberless**

- GitHub: [github.com/YorhaNumberless](https://github.com/YorhaNumberless)
- BlueSky: [@yorhanumberless.bsky.social](https://bsky.app/profile/yorhanumberless.bsky.social)
- YouTube: [youtube.com/@YoRHaNumberless](https://youtube.com/@YoRHaNumberless)
- Twitch: [twitch.tv/yorha_numberless](https://twitch.tv/yorha_numberless)
- Discord: yorhanumberless

Support the project: [tipeeestream.com/yorha-numberless](https://www.tipeeestream.com/yorha-numberless/donation)

## License

[MIT](LICENSE)
