---
name: 開新專案
description: 在使用者指定的路徑長出一整包「專案起始範本」（PM/PRD/SA/SRS 流程＋自帶 skills）。當使用者說「開新專案」「開一個新專案」「幫我建一個新專案」「scaffold 新案」「用範本開案」時使用。此 skill 只負責「複製整包到指定位置」；複製完成後，分析售前文件的「importRFP」流程在新專案資料夾內進行（屬另一顆 skill，勿在此處執行）。
---

# 開新專案（啟動器）

把本 plugin 內建的範本包複製到使用者指定的路徑，產生一個自給自足的新專案資料夾。

## 第 0 步：判斷作業系統
環境資訊已含平台；不確定就跑 `uname`（`Darwin`＝macOS，其餘視為 Windows）。後續雙寫指令依此選用。

## 步驟

### 1. 取得目標路徑與專案名
向使用者確認兩件事（缺哪個就問哪個，別自己猜）：
- **目標母目錄**：新專案要建在哪個資料夾底下（給絕對路徑）。
- **專案名稱**：建議 `proj-某某案` 形式。

最終專案路徑 = `<母目錄>/<專案名>`。若該路徑已存在且非空，**停下來問**使用者要換名還是覆蓋，不要直接蓋掉。

### 2. 定位範本來源（plugin 內的 template/）
範本在本 plugin 目錄的 `template/` 子資料夾。先解析 plugin 根目錄：

```bash
# 優先用環境變數
echo "PLUGIN_ROOT=$CLAUDE_PLUGIN_ROOT"
```

- 若 `$CLAUDE_PLUGIN_ROOT` 有值且 `"$CLAUDE_PLUGIN_ROOT/template"` 存在 → 來源 = `$CLAUDE_PLUGIN_ROOT/template`。
- 若為空或不存在，改用搜尋（plugin 安裝在快取目錄）：
  - macOS/Linux：
    ```bash
    ls -d ~/.claude/plugins/*/iniproj/template ~/.claude/plugins/cache/*/iniproj/template ~/.claude/plugins/**/iniproj*/template 2>/dev/null | head -1
    ```
  - Windows（PowerShell）：
    ```powershell
    Get-ChildItem -Path "$env:USERPROFILE\.claude\plugins" -Recurse -Directory -Filter template -ErrorAction SilentlyContinue | Where-Object { $_.FullName -match 'iniproj' } | Select-Object -First 1 -ExpandProperty FullName
    ```

把解析到的來源路徑記為 `SRC`。確認 `SRC` 底下有 `CLAUDE.md`、`PM/`、`PRD/`、`.claude/skills/` 再往下做。

### 3. 複製整包
建立目標並複製（含隱藏的 `.claude`）：

- macOS/Linux：
  ```bash
  mkdir -p "<目標路徑>"
  cp -R "$SRC/." "<目標路徑>/"
  # 清掉範本骨架不該帶的系統垃圾
  find "<目標路徑>" -name '.DS_Store' -delete 2>/dev/null || true
  ```
- Windows（PowerShell）：
  ```powershell
  New-Item -ItemType Directory -Force -Path "<目標路徑>" | Out-Null
  Copy-Item -Path "<SRC>\*" -Destination "<目標路徑>" -Recurse -Force
  ```

複製後快速驗證：列出目標路徑，確認有 `CLAUDE.md`、`PM/index.html`、`.claude/skills/importRFP`。

### 4. 回報並指引後續
告訴使用者整包已就緒，並給接續路線（兩條，看他的起點）：

- **若有售前文件（RFP／服務建議書／合約）**：把文件丟進新專案的 `PM/input/`，然後**在新專案資料夾**對 Claude 說「importRFP」→ 會跑分析、產 `gap-analysis.md`＋`prefill.js`、開啟新手包網頁。
- **若直接從問卷開始**：雙擊新專案的 `PM/index.html` 填問卷。

提醒：後續流程要 Claude「站進新專案資料夾」才會載入該包自帶的 skills——
- macOS 終端機：`cd "<目標路徑>"` 後再 `claude`；
- 或 VS Code：開啟新專案資料夾後用 Claude Code 擴充。

（也可主動詢問是否現在就幫他開新專案的工作目錄／開啟網頁，但**不要自動執行 git**。）

## 原則
- 只「複製範本骨架」，不在此步填任何專案內容。
- 目標路徑已存在且非空時，先問再做，絕不靜默覆蓋。
- 不主動執行 git。
