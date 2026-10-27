# Windows Laptop Cleanup: Command Prompt Method

Remove personal data from a Windows laptop before transferring ownership, while keeping the system fully functional and bootable.

| | |
|---|---|
| **Applies to** | Windows 10, Windows 11 |
| **Method** | Elevated Command Prompt |
| **Time required** | 15–30 minutes (plus an optional 10–30 minute integrity scan) |
| **Skill level** | Beginner - copy, paste, done |

## Contents

- [Windows Laptop Cleanup: Command Prompt Method](#windows-laptop-cleanup-command-prompt-method)
  - [Contents](#contents)
  - [Before you start](#before-you-start)
  - [Phase 1: System temporary files](#phase-1-system-temporary-files)
  - [Phase 2: Browser data](#phase-2-browser-data)
  - [Phase 3: Usage tracking \& personal traces](#phase-3-usage-tracking--personal-traces)
  - [Phase 4: Final system cleanup](#phase-4-final-system-cleanup)
  - [Optional: integrity check](#optional-integrity-check)
  - [What this does and doesn't do](#what-this-does-and-doesnt-do)
  - [Troubleshooting](#troubleshooting)
  - [Next steps](#next-steps)
  - [Alternative: Windows built-in reset](#alternative-windows-built-in-reset)

## Before you start

> [!IMPORTANT]
> Several commands below permanently delete files. There is no undo. Back up anything you want to keep before you run any of them.

1. **Back up your files** - Documents, Desktop, Downloads, and anywhere else that matters.
2. **Uninstall unwanted applications first**, via *Settings -> Apps*. The commands below clear traces of *use*, not installed *programs*.
3. **Open an elevated Command Prompt:**
   - Press `Win + X`
   - Select **Command Prompt (Admin)**
   - Click **Yes** on the UAC prompt

> [!NOTE]
> Run these in **Command Prompt**, not PowerShell. A couple of the commands below (notably the event log clear in Phase 4) use `cmd.exe` batch syntax and won't behave the same way in PowerShell.

## Phase 1: System temporary files

| # | Command | What it does | Safe? |
|---|---|---|---|
| 1 | `cleanmgr /sagerun:1` | Runs Windows' built-in Disk Cleanup silently - temp files, browser cache, Recycle Bin, thumbnails | ✅ Uses Microsoft's own cleanup logic |
| 2 | `del /s /q /f %TEMP%\*.*`<br>`rmdir /s /q %TEMP%` | Clears your user temp folder and its subfolders | ✅ Windows recreates it automatically at next login |
| 3 | `del /s /q /f C:\Windows\Temp\*.*` | Clears system-wide temp files | ✅ These are disposable by design |
| 4 | `del /s /q /f C:\Windows\Prefetch\*.*` | Removes app-launch optimization files | ✅ Rebuilt automatically as apps run |
| 5 | `del /s /q /f "%APPDATA%\Microsoft\Windows\Recent\*.*"` | Clears recent-file lists | ✅ Convenience data only - File Explorer and taskbar jump lists |

## Phase 2: Browser data

| # | Command | What it does | Safe? |
|---|---|---|---|
| 6 | `RunDll32.exe InetCpl.cpl,ClearMyTracksByProcess 4351` | Clears IE/Edge browsing data | ✅ Standard browser cleanup |
| 7 | `rmdir /s /q "%LOCALAPPDATA%\Google\Chrome\User Data"` | Removes Chrome profiles entirely - history, bookmarks, saved passwords, extensions, settings | ✅ If Chrome data is no longer needed. Skip if Chrome isn't installed. |
| 8 | `rmdir /s /q "%APPDATA%\Mozilla\Firefox"` | Removes Firefox profiles entirely | ✅ If Firefox data is no longer needed. Skip if Firefox isn't installed. |

## Phase 3: Usage tracking & personal traces

| # | Command | What it does | Safe? |
|---|---|---|---|
| 9 | `del /s /q /f "%LOCALAPPDATA%\Microsoft\Windows\Explorer\thumbcache_*.db"` | Removes the thumbnail preview cache | ✅ Windows regenerates it as needed |
| 10 | `reg delete "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\RunMRU" /f` | Clears `Win + R` run-dialog history | ✅ Convenience history only |

## Phase 4: Final system cleanup

| # | Command | What it does | Safe? |
|---|---|---|---|
| 11 | `for /f "tokens=*" %1 in ('wevtutil.exe el') DO wevtutil.exe cl "%1"` | Clears every Windows event log | ✅ Logs are diagnostic only |
| 12 | `reg delete "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Map Network Drive MRU" /f`<br>`reg delete "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\ComDlg32\OpenSavePidlMRU" /f` | Removes network-drive and file-dialog history | ✅ Convenience features only |
| 13 | `net stop wuauserv`<br>`rmdir /s /q C:\Windows\SoftwareDistribution\Download`<br>`net start wuauserv` | Clears the Windows Update download cache | ✅ Windows re-downloads what it needs |
| 14 | `reg delete "HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs" /f` | Removes registry-stored recent-document references | ✅ Tracking data only |

> [!TIP]
> Command 11 uses `%1`, which is only valid typed directly at a `cmd.exe` prompt. If you've pasted it into a `.bat` script instead, double it to `%%1`. In PowerShell, use this equivalent instead:
> ```powershell
> wevtutil el | ForEach-Object { wevtutil cl $_ }
> ```

## Optional: integrity check

```cmd
sfc /scannow
```

Verifies Windows system file integrity and repairs anything corrupted. Takes 10–30 minutes depending on the system - safe to run, and a good final step before handing the machine over.

## What this does and doesn't do

**Removed:**
- Browsing history, cookies, and cached web data
- Recent file and folder access history
- Temporary files and application caches
- Search indexes and thumbnail previews
- Run-dialog and file-dialog history
- Network connection history
- Event logs showing system usage
- Downloaded Windows updates (recovers disk space)

**Not removed:**
- Installed applications - uninstall those separately first
- BIOS/UEFI settings or boot configuration
- Anything requiring secure disk wiping

> [!WARNING]
> This is a **data hygiene** pass, not secure erasure. Deleted files can still be recovered with forensic tools, because the underlying disk sectors aren't overwritten. For a laptop that held highly sensitive data:
> 1. Run this cleanup first.
> 2. Follow with **Settings -> Recovery -> Reset this PC**, using **Remove everything** + **Clean the drive**.
> 3. For classified or regulated environments, use dedicated, approved disk-wiping software instead of file-level deletion.

## Troubleshooting

**"Access is denied"**
Make sure Command Prompt is running as Administrator. If a file is in use, restart and try again.

**"File Not Found" / "Could Not Find"**
Normal - it just means that file or folder doesn't exist on this system. Continue with the remaining commands.

**A browser command doesn't do anything**
Normal if that browser was never installed. Skip it and move on.

## Next steps

- [ ] Restart the computer so all changes take effect
- [ ] Test basic functionality - boot, login, internet, File Explorer
- [ ] Spot-check that personal files are gone (Documents, Downloads, Desktop)
- [ ] Create a new local account for the recipient, if handing the machine over directly
- [ ] Note any software still installed that the new owner should know about
- [ ] Include the Windows product key, if the license is transferring with the device

## Alternative: Windows built-in reset

A GUI-based option if you'd rather not touch a command line, or want a fully fresh install:

1. **Settings -> Update & Security -> Recovery**
2. Select **Reset this PC**
3. Choose **Remove everything**
4. Select **Clean the drive** for thorough removal
5. Complete the reset

| | Command-line method (this guide) | Built-in reset |
|---|---|---|
| Speed | 15–30 min | 1–3 hours |
| Result | Clean install, existing Windows setup preserved | Fresh Windows install |
| Needs install media | No | Sometimes, depending on scenario |

---

*Tested on Windows 10 and Windows 11.*
