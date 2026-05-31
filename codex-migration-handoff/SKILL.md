---
name: codex-migration-handoff
description: Package, transfer, restore, and verify Codex data when moving from Mac to Windows or between computers. Use when the user wants to migrate Codex conversations, sessions, memories, skills, plugins, automations, generated images, app data, project folders, or reproduce a Codex workspace and previous dialogue on another machine, including Feishu/cloud-drive handoff instructions.
---

# Codex Migration Handoff

Use this skill to make a repeatable migration handoff for Codex itself plus the project folders needed to reopen old conversations on another computer.

## Workflow

1. Identify source and target OS, usernames, and transfer channel.
   - Mac source paths usually include `~/.codex`, `~/Library/Application Support/Codex`, `~/Library/Application Support/com.openai.codex`, and `~/Library/Application Support/OpenAI/Codex`.
   - Windows target paths usually include `%USERPROFILE%\.codex`, `%APPDATA%\Codex`, `%APPDATA%\com.openai.codex`, and `%APPDATA%\OpenAI\Codex`.
   - Project files are separate from Codex data. Ask for, detect, or include project folders such as `~/Documents/New project`.

2. On the source Mac, prefer generating a migration package with `scripts/create_mac_codex_migration_package.sh`.
   - Best practice: install/open Codex once on the Windows computer, then close Codex before restoring.
   - For the cleanest package, run the Mac script from Terminal after closing Codex. If running from inside Codex, tell the user that active SQLite/log files can change while copying; verify package size and rerun if needed.
   - Include optional project folders with repeated `--project /path/to/project` arguments.

3. Transfer the generated `.zip`.
   - Feishu, cloud drive, LAN share, AirDrop-to-phone-to-PC, or external disk are all acceptable.
   - Treat the package as private: it can contain auth tokens, conversation history, memories, generated files, and logs.

4. On Windows, unzip and run the included `Restore-Codex-To-Windows.ps1`.
   - The restore script backs up existing target directories before copying.
   - If execution policy blocks it, run `Set-ExecutionPolicy -Scope Process Bypass` in the same PowerShell session.
   - If Codex fails to start, close Codex and delete stale `SingletonLock`, `SingletonCookie`, and `SingletonSocket` under `%APPDATA%\Codex`.

5. Verify continuity.
   - Open Codex and check recent threads, skills, plugins, memories, generated images, and automations.
   - Reopen the project folder from its new Windows location if old conversations reference Mac paths like `/Users/<name>/...`.
   - If a project was included in the package, move it from `projects/` to the desired Windows folder and update/reopen the workspace in Codex.

## Scripts

- `scripts/create_mac_codex_migration_package.sh`: Run on Mac to build a Windows-oriented migration zip with restore script, README, manifest, checksums, and optional project folders.
- `scripts/restore_codex_to_windows.ps1`: Standalone Windows restore script. The Mac package also embeds a copy named `Restore-Codex-To-Windows.ps1`.
- `scripts/collect_windows_codex_inventory.ps1`: Run on Windows before or after restore to summarize existing Codex data locations, sizes, and project folder candidates.

## Handoff Checklist

When the user wants another Codex instance on the source Mac to help, send a short instruction like:

```text
Use the codex-migration-handoff workflow. Create a full Mac-to-Windows Codex migration package, include ~/.codex, Codex Application Support folders, and these project folders: <paths>. Put the zip on Desktop and tell me the zip path and size.
```

Before finalizing, report:

- Package path and size.
- Whether projects were included or still need separate copying.
- Exact Windows restore steps.
- Any caveats about login state, platform-specific paths, or live-copy consistency.

