---
name: importRFP
description: 讀取 PM/input 的售前文件（服務建議書／RFP／合約），對照 iniRFP 題庫，產出 gap-analysis.md 與 prefill.js，供新手包自動預填。當使用者說「importRFP」「分析RFP」「分析這份 RFP／合約」「跑售前文件」時使用。
---

# importRFP

把售前文件轉成「iniRFP 的合約依據 + 自動預填」。

## 第 0 步：判斷作業系統
開始前先確認在 macOS 還是 Windows（環境資訊已含平台；不確定就跑 `uname`，回傳 `Darwin`＝macOS，指令失敗或回傳 Windows 字樣＝Windows）。
後續凡有「mac／Windows」雙寫的指令，依此選用。

## 步驟

### 1. 讀文件
列出 `PM/input/` 內所有文件。讀取全文：
- `.docx`
  - macOS → `textutil -convert txt -stdout "<檔>"`
  - Windows → 用 Read 工具直接讀；讀不出純文字時，用 PowerShell 解壓 docx 取 `word/document.xml`（docx 即 zip），或請使用者另存純文字／PDF。
- `.pdf` → 用可讀 PDF 的工具（Read 工具或 pdftotext）
- `.txt / .md` → 直接讀

### 2. 對照題庫
題庫定義在 `PM/index.html` 的 `CONFIG` 陣列。每題有 `id` / `label` / `type`(radio|multi|text) / `opts`。
逐項把文件內容對應到題目 `id`。注意：
- radio 題的值必須是該題 `opts` 既有的字串；若文件值不在選項內，選「其他」並填它的 `reveal` 欄位。
- multi 題的值用**字串陣列**。
- 有 `reveal` 的選項，連同 `reveal.id` 一起填補充說明。

### 3. 分三類
- **已綁定**：文件白紙黑字規定的（例：Session 15 分、MSSQL、資安中級）。記「題目 id、規定值、出處章節」。
- **待決策**：題庫有、文件沒提的（例：無障礙、密碼複雜度、前端框架）。
- **額外發現**：文件有綁、但題庫沒有對應題目的（例：密碼雜湊演算法、效能 SLA、保固年限、前後台分離）。← 也要列出，這是題庫可擴充的線索。

### 4. 產出 `PM/output/gap-analysis.md`
三段式（已綁定／待決策／額外發現），每項附**出處**，人看的、可稽核。檔頭註明來源文件。

### 5. 產出 `PM/prefill.js`
```js
window.PREFILL = {
  project: '<案名>',
  source: '<來源檔相對路徑>',
  answers: {            // 只放「已綁定」項；值需符合該題 opts
    '<題目id>': '<值或陣列>',
    '<reveal的id>': '<補充>',
  },
  sources: {            // 對應 answers 的出處，會顯示在題目下方 📎
    '<題目id>': '<章節：規定摘要>',
  }
};
```
規則：`answers` 只放已綁定項；`sources` 的 key 對齊 `answers` 的主要題目 id（reveal 子欄位不必各自列出處）。

### 6. 自動開啟網頁
產出 `prefill.js` 後，用預設瀏覽器自動開啟新手包：
- macOS → `open PM/index.html`
- Windows → `start "" "PM\index.html"`（cmd）或 `Start-Process PM\index.html`（PowerShell）

（若失敗，再請使用者手動雙擊 `PM/index.html`。）

### 7. 收尾提示使用者
告訴使用者：網頁已開啟 → 已綁定項已帶出並標 📎 → 補完「待決策」題目 → 下載 .md → 說「填好了」我把它放進 `PM/output/iniRFP.md`。

## 原則
- 只填合約**明確綁定**的；不確定或屬設計層的列「待決策」，不硬填。
- 合約沒提到的安全／無障礙等，標「待確認」，不預設。
- 不主動執行 git。
