# Codex Migrator

A PowerShell tool for **migrating local OpenAI Codex data between different Windows computers**.

This tool is designed for scenarios such as:

- switching between a **A** PC and a **B** PC while using Codex
- preserving existing conversations, archived sessions, and selected local state
- migrating data through **ZIP backup / restore**
- supporting both **first-time full migration** and **daily lightweight migration**

---

## Features

- Single-file PowerShell script, easy to deploy
- Supports two roles:
  - **Source Computer**
  - **Target Computer**
- Supports two transfer modes:
  - **First Transfer**: package the entire `.codex`
  - **Daily Transfer**: package only essential items
- Uses `manifest.json` to record backup metadata
- Automatically determines restore mode from `manifest.json`
- Automatically backs up existing target-side data before restoring
- Supports ZIP structures in all of the following forms:
  - `payload/...`
  - `payload\...`
  - legacy root-level layout

---

## Requirements

- Windows
- Windows PowerShell 5.1
- Codex Desktop App installed and used at least once
- Default Codex data path:

```text
%USERPROFILE%\.codex
```

---

## Transfer Modes

### 1. First Transfer (`first`)
Recommended when initializing a new computer.

Packages the entire folder:

```text
%USERPROFILE%\.codex
```

### 2. Daily Transfer (`daily`)
Recommended for routine switching.

By default, it packages the following items:

```text
sessions
archived_sessions
session_index.jsonl
memories
```

---

## How to Use

### Source Computer: Create a backup ZIP

1. Close Codex.
2. Run `Codex-Migrator.ps1`.
3. Choose:

```text
1. Source Computer (Create backup ZIP)
```

4. Select a mode:
   - `1` = First Transfer
   - `2` = Daily Transfer
5. Choose the output folder for the backup ZIP.
6. The tool will create the backup ZIP.

---

### Target Computer: Restore from a backup ZIP

1. Close Codex.
2. Run `Codex-Migrator.ps1`.
3. Choose:

```text
2. Target Computer (Restore backup ZIP)
```

4. Select the ZIP file to restore.
5. The tool will read `manifest.json`.
6. A restore summary will be displayed.
7. Confirm to continue.

---

## Automatic Backup Before Restore

Before restoring on the target computer, the tool will back up the current target-side data to:

```text
<folder containing the ZIP>\_restore_backup\
```

Backup filename format:

```text
target_backup_{mode}_{timestamp}.zip
```

Example:

```text
target_backup_first_20260419_194608.zip
```

---

## Backup ZIP Structure

Each backup ZIP contains:

- `manifest.json`
- `payload\\...`

`manifest.json` records:

- `mode`
- `created_at`
- `source_computer`
- `codex_root`
- `payload_root`
- `included_items`
- `tool_version`

---

## Notes

### 1. Please close Codex before running the tool
The tool checks whether any `codex` processes are still running. If Codex is not fully closed, execution will stop immediately.

### 2. First Transfer is more aggressive
The `first` mode restores the entire `.codex` folder. Make sure the pre-restore backup on the target side is available before continuing.

### 3. Daily Transfer is not a full backup
The `daily` mode only handles selected essential items. It is intended for routine migration, not full disaster recovery.

### 4. Conversation continuity may still depend on local paths
Some Codex conversations may be tied to specific local repository paths. Migrating `.codex` alone may not fully restore the original working context.

---

## Running the Script

### Run directly with PowerShell

```powershell
powershell -STA -NoProfile -ExecutionPolicy Bypass -File .\Codex-Migrator.ps1
```

### Or use a `.bat` launcher

You can create a simple launcher such as:

```bat
@echo off
powershell -STA -NoProfile -ExecutionPolicy Bypass -File "%~dp0Codex-Migrator.ps1"
pause
```

---

## Current Design Highlights

- Restore is performed directly from the ZIP into `%USERPROFILE%\\.codex`
- Uses `FolderBrowserDialog` to select output folders
- Uses `OpenFileDialog` to select ZIP files
- Shows a console prompt first, then opens the selection dialog after about 700 ms

---

## Possible Future Enhancements

- GUI version
- File-based logs
- Backup content comparison
- Automatic launcher generation

---

## Disclaimer

This tool directly modifies the user's `.codex` folder.

Before using it, make sure:

- Codex is fully closed
- the backup ZIP is correct
- important target-side data has been properly preserved

Use at your own risk.

---

## License

```text
MIT
```
