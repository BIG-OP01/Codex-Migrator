# Codex Migrator

一個用於 **在不同 Windows 電腦間搬移 OpenAI Codex 本機資料** 的 PowerShell 工具。

此工具主要針對以下情境設計：

- **A** 電腦與 **B** 電腦之間切換使用 Codex
- 保留既有對話紀錄、封存紀錄與部分本機狀態
- 透過 **zip 打包 / 還原** 的方式進行移植
- 支援 **首次完整搬移** 與 **日常增量搬移**

---

## 功能特色

- PowerShell 單檔腳本，部署簡單
- 支援 **來源電腦** / **目標電腦** 兩種角色
- 支援兩種模式：
  - **首次移動**：打包整個 `.codex`
  - **日常移動**：只打包必要項目
- 以 `manifest.json` 記錄備份資訊
- 還原時自動依 `manifest.json` 判斷模式
- 目標端還原前，會先自動備份現有資料
- 支援 zip 內以下格式：
  - `payload/...`
  - `payload\...`
  - 舊版根目錄格式

---

## 適用環境

- Windows
- Windows PowerShell 5.1
- 已安裝並使用過 Codex Desktop App
- `.codex` 預設路徑：

```text
%USERPROFILE%\.codex
```

---

## 搬移模式

### 1. 首次移動（first）
適合新電腦初始化時使用。

會打包整個：

```text
%USERPROFILE%\.codex
```

### 2. 日常移動（daily）
適合平常公司 / 家裡切換使用。

預設打包以下項目：

```text
sessions
archived_sessions
session_index.jsonl
memories
```

---

## 使用方式

### 來源電腦：建立備份 zip

1. 關閉 Codex
2. 執行 `Codex-Migrator.ps1`
3. 選擇：

```text
1. 來源電腦（建立備份 zip）
```

4. 選擇模式：
   - `1` = 首次移動
   - `2` = 日常移動
5. 選擇 zip 輸出資料夾
6. 工具會建立備份 zip

---

### 目標電腦：還原備份 zip

1. 關閉 Codex
2. 執行 `Codex-Migrator.ps1`
3. 選擇：

```text
2. 目標電腦（還原備份 zip）
```

4. 選擇要還原的 zip 檔
5. 工具會先讀取 `manifest.json`
6. 顯示還原摘要
7. 確認後執行還原

---

## 還原前的自動備份

在目標電腦還原前，工具會先把目前目標端的既有資料備份到：

```text
<zip所在資料夾>\_restore_backup\
```

備份檔命名格式：

```text
target_backup_{mode}_{timestamp}.zip
```

例如：

```text
target_backup_first_20260419_194608.zip
```

---

## 備份檔結構

備份 zip 內會包含：

- `manifest.json`
- `payload\...`

其中 `manifest.json` 會記錄：

- `mode`
- `created_at`
- `source_computer`
- `codex_root`
- `payload_root`
- `included_items`
- `tool_version`

---

## 注意事項

### 1. 執行前請先關閉 Codex
工具會先檢查是否仍有 `codex` 程序執行中；若未關閉，會直接中止。

### 2. 首次移動風險較高
首次移動模式會覆蓋整個 `.codex`，建議先確認目標端備份是否成功。

### 3. 日常移動只適合平常同步
日常移動只處理部分必要資料，不等同完整備份。

### 4. 對話能否完整延續，仍與本機路徑有關
若某些對話綁定特定 repo 工作路徑，僅搬移 `.codex` 不一定能完全恢復原工作狀態。

---

## 執行方式

### 直接執行 PowerShell

```powershell
powershell -STA -NoProfile -ExecutionPolicy Bypass -File .\Codex-Migrator.ps1
```

### 或搭配 `.bat` 啟動器

可自行建立：

```bat
@echo off
powershell -STA -NoProfile -ExecutionPolicy Bypass -File "%~dp0Codex-Migrator.ps1"
pause
```

---

## 已知設計重點

- 還原時直接從 zip 解壓到 `%USERPROFILE%\.codex`
- 使用 `FolderBrowserDialog` 選資料夾
- 使用 `OpenFileDialog` 選 zip 檔
- 在彈出選取視窗前，會先於主控台顯示提示訊息並延遲約 700 ms

---

## 後續可擴充方向

- GUI 版本
- 日誌輸出檔案
- 備份內容比對
- 自動建立快捷啟動器

---

## 免責聲明

本工具會直接操作使用者的 `.codex` 資料夾。  
請務必先確認：
- Codex 已關閉
- 備份檔正確
- 目標端重要資料已妥善保存

使用前請自行評估風險。

---

## License

可依需求自行補上，例如：

```text
MIT
```
