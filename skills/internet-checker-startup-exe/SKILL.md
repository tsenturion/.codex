---
name: internet-checker-startup-exe
description: Rebuild and replace the installed Internet Checker Startup executable in C:\Users\user\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup after meaningful functional changes to the internet-checker app or after a git tag push/release-tag push. Use for local installed EXE refresh workflows in C:\Users\user\repos\internet-checker. Do not use for documentation-only, README-only, wording, changelog, metadata, or description-only changes.
---

# Internet Checker Startup EXE

## Overview

Refresh the local Startup copy of `InternetChecker.exe` when the runnable behavior of Internet Checker has changed. This keeps the automatically started local app aligned with the current functional code.

This local replacement does not create, update, or substitute the release EXE artifact. Treat release packaging/upload as a separate task.

## Decision Rule

Run this workflow after:

- Meaningful functional changes in the app, monitor loop, notifications, tray behavior, config behavior, network checks, packaging behavior, or startup installation behavior.
- A successful `git tag push` or release-tag push for this project, unless the user explicitly says not to refresh the local Startup app.

Do not run this workflow after:

- Documentation-only changes.
- README, changelog, comments-only, wording-only, metadata-only, or description-only edits.
- Changes that do not affect the runnable app.

If the change set mixes functional and documentation edits, treat it as functional and run the workflow.

## Workflow

Work from `C:\Users\user\repos\internet-checker`.

1. Inspect scope before building:
   - Run `git status -sb`.
   - Review the relevant diff enough to confirm the change is functional.
   - Skip this skill if the change is documentation-only or description-only.

2. Validate before packaging:
   - Run `python -m py_compile main.py`.
   - Run any focused smoke check that matches the changed behavior when practical.

3. Build the onefile executable:
   - Run `powershell -ExecutionPolicy Bypass -File .\build_exe.ps1`.
   - Confirm `dist\InternetChecker.exe` exists.

4. Stop the currently installed Startup executable before replacement:
   - Stop only the already-running `InternetChecker.exe` whose `ExecutablePath` is `C:\Users\user\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\InternetChecker.exe`.
   - Use an exact-path process check before replacing the file:

```powershell
$startupExe = Join-Path $env:APPDATA "Microsoft\Windows\Start Menu\Programs\Startup\InternetChecker.exe"
Get-CimInstance Win32_Process -Filter "name = 'InternetChecker.exe'" |
    Where-Object { $_.ExecutablePath -eq $startupExe } |
    ForEach-Object { Stop-Process -Id $_.ProcessId -Force -ErrorAction SilentlyContinue }
Start-Sleep -Milliseconds 500
```

5. Replace the Startup executable:
   - Run `powershell -ExecutionPolicy Bypass -File .\install_startup.ps1 -SourceExe .\dist\InternetChecker.exe`.
   - This script also stops existing `InternetChecker` processes, copies the new EXE to Startup, and starts the copied Startup EXE hidden; still perform the exact Startup-path stop above first.

6. Ensure the copied Startup executable is running:
   - Start or verify only `C:\Users\user\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\InternetChecker.exe`.
   - Do not start `dist\InternetChecker.exe` as the installed app.
   - If the process is not running from the Startup path after replacement, start it explicitly:

```powershell
$startupExe = Join-Path $env:APPDATA "Microsoft\Windows\Start Menu\Programs\Startup\InternetChecker.exe"
$running = Get-CimInstance Win32_Process -Filter "name = 'InternetChecker.exe'" |
    Where-Object { $_.ExecutablePath -eq $startupExe }
if (-not $running) {
    Start-Process -FilePath $startupExe -WorkingDirectory (Split-Path $startupExe) -WindowStyle Hidden
}
```

7. Verify installation:
   - Compare SHA-256 hashes of `dist\InternetChecker.exe` and `C:\Users\user\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\InternetChecker.exe`.
   - Confirm at least one `InternetChecker.exe` process is running from the Startup path.
   - A parent/child pair with the same EXE path can be normal for PyInstaller onefile.

## Reporting

In the final response, state whether the Startup EXE was refreshed, whether hashes matched, what validation ran, and whether this was separate from any release EXE work.
