---
name: 開新專案
description: 在使用者指定的路徑長出一整包「專案起始範本」（PM/PRD/SA/SRS 流程＋自帶 skills），並可選配把使用者指定的售前文件複製進新專案的 PM/input/。當使用者說「開新專案」「開一個新專案」「幫我建一個新專案」「scaffold 新案」「用範本開案」時使用。此 skill 負責「複製整包＋搬入售前文件」；分析售前文件的「importRFP」流程仍在新專案資料夾內由另一顆 skill 進行（勿在此處執行）。
---

# 開新專案（啟動器）

把本 plugin 內建的範本包複製到使用者指定的路徑，產生一個自給自足的新專案資料夾。

## 第 0 步：判斷作業系統
環境資訊已含平台；不確定就跑 `uname`（`Darwin`＝macOS，其餘視為 Windows）。後續雙寫指令依此選用。

## 步驟

### 1. 一次問齊（目標路徑、專案名、售前文件）
向使用者確認以下（缺哪個就問哪個，別自己猜）：
- **目標母目錄**：新專案要建在哪個資料夾底下（給絕對路徑）。
- **專案名稱**：建議 `proj-某某案` 形式。
- **售前文件（選配）**：「你手邊有 RFP／服務建議書／合約嗎？有的話給我**檔案或資料夾的路徑**（可多個），我複製進新專案的 `PM/input/`，省得你手動拖；沒有就略過。」

最終專案路徑 = `<母目錄>/<專案名>`。若該路徑已存在且非空，**停下來問**使用者要換名還是覆蓋，不要直接蓋掉。

### 2. 定位範本來源（plugin 內的 template/）
範本在本 plugin 目錄的 `template/` 子資料夾。先解析 plugin 根目錄：

```bash
# 優先用環境變數
echo "PLUGIN_ROOT=$CLAUDE_PLUGIN_ROOT"
```

- 若 `$CLAUDE_PLUGIN_ROOT` 有值且 `"$CLAUDE_PLUGIN_ROOT/template"` 存在 → 來源 = `$CLAUDE_PLUGIN_ROOT/template`。
- 若為空或不存在，改用搜尋（plugin 安裝在快取目錄，實際路徑形如 `~/.claude/plugins/cache/iniproj/iniproj/<版本>/template`，版本層數不固定，故用遞迴 find）：
  - macOS/Linux：
    ```bash
    find ~/.claude/plugins -type d -name template -path '*iniproj*' 2>/dev/null | head -1
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

### 4. 搬入售前文件（選配）
若步驟 1 使用者**有提供**售前文件路徑，把它們複製進 `<目標路徑>/PM/input/`。
**用「複製」不是「搬移」——保留使用者原檔，不刪不動原始位置。**

- macOS/Linux：
  ```bash
  # 來源是單一檔案（可多個）
  cp "<來源檔1>" "<來源檔2>" "<目標路徑>/PM/input/"
  # 來源是資料夾 → 複製裡面的文件（避免帶入無關子資料夾時，可只挑 .docx/.pdf/.txt/.md）
  cp -R "<來源夾>/." "<目標路徑>/PM/input/"
  ```
- Windows（PowerShell）：
  ```powershell
  Copy-Item -Path "<來源檔或夾>" -Destination "<目標路徑>\PM\input\" -Recurse -Force
  ```

規則：
- 來源路徑**找不到／拼錯** → 回報實際情形請使用者確認，**不臆測、不亂猜檔名**。
- 複製後**列出 `PM/input/` 內容**給使用者確認搬了哪些。
- 使用者沒提供文件 → 跳過此步。

### 5. 回報並指引後續
告訴使用者整包已就緒（若有搬入售前文件，一併說明 `PM/input/` 已備妥哪些檔），並給接續路線：

- **已有售前文件在 `PM/input/`**（剛搬入或使用者自行放）：**在新專案資料夾**對 Claude 說「importRFP」→ 會跑分析、產 `gap-analysis.md`＋`prefill.js`、開啟新手包網頁。
- **若直接從問卷開始**：雙擊新專案的 `PM/index.html` 填問卷。

提醒：後續流程要 Claude「站進新專案資料夾」才會載入該包自帶的 skills——
- macOS 終端機：`cd "<目標路徑>"` 後再 `claude`；
- 或 VS Code：開啟新專案資料夾後用 Claude Code 擴充。

（也可主動詢問是否現在就幫他開新專案的工作目錄／開啟網頁，但**不要自動執行 git**。）

## 原則
- 這步只做「複製範本骨架」與「把售前文件搬進 `PM/input/`」；**不分析、不填問卷、不產任何文件**——那是 `importRFP` 等專案內 skill 的事。
- 搬售前文件一律**用複製保留原檔**；來源找不到就問，不亂猜。
- 目標路徑已存在且非空時，先問再做，絕不靜默覆蓋。
- 不主動執行 git。
