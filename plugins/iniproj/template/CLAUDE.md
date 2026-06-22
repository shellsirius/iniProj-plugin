# 開新案範本包（iniProj）

這是一個可複製的「專案起始範本包」。每開一個新案，把整個資料夾複製成新專案夾，即可使用。

## 開新案流程

1. 把售前文件（服務建議書 / RFP / 合約）丟進 `PM/input/`
2. 跟我說「importRFP」，我會執行 `importRFP` skill：
   - 讀 `PM/input/` 的文件
   - 對照 iniRFP 題庫（題庫定義在 `PM/index.html` 的 `CONFIG` 陣列）
   - 分出「合約已綁定」與「待 PM 決策」
   - 產出 `PM/output/gap-analysis.md`（人看的，附合約出處）
   - 產出 `PM/prefill.js`（機器讀的，給新手包自動預填）
3. 雙擊 `PM/index.html` → 已綁定項自動帶出並標 📎，補完「待決策」題目
4. 在網頁下載 .md → 跟我說「填好了」，我把 `~/Downloads` 最新那份搬進 `PM/output/iniRFP.md`
5. 產 PRD（依規範 `PRD/rules/PRD撰寫規範.md` 產出 R檔、更新 `PRD/PRD.md`、生成 `PRD/traceability.md`），依來源選 skill：
   - 來源是 iniRFP → 跟我說「產PRD」（或 `iniRFPtoPRD`），讀 `PM/output/iniRFP.md`。
   - 來源是會議／訪談記錄 → 跟我說「會議產PRD」（或 `MeetingtoPRD`），讀 `PM/Meeting/` 指定檔，萃取需求、可新增或修訂既有 R檔（動工前先列清單確認）。
6. 產 SA：跟我說「產SA」（或 `PRDtoSA`），讀 PRD R檔，依 `SA/rules/SA撰寫規範.md` 把需求拆成功能、寫模組層 SA、登錄 `SA/SA.md`、回填 `PRD/traceability.md` 的「SA 對應」欄（動工前先列功能／模組規劃確認）。
7. 產 SRS：跟我說「產SRS」（或 `SAtoSRS`），讀 SA 功能清單，依 `SRS/rules/SRS撰寫規範.md` 為每個功能產出一頁頁面規格（頁碼沿用功能編號、含頁標題／欄位／頁內 UI flow；breadcrumb 由 `SRS/功能地圖.md` 統一定義）、回填 `PRD/traceability.md` 的「SRS 對應」欄（動工前先列頁面清單確認）。
8. 產測試：跟我說「產測試」（或 `SRStoTest`，可指定 R 如「為 R0001 產測試」），讀**指定 R檔**、依追溯找出相關 SA／SRS，依 `Testing/test-cases/rules/測試案例撰寫規範.md` 產測試案例（一 R 一檔、TC 全域流水）、回填 `PRD/traceability.md` 的「測試對應」欄（動工前先列 TC 規劃確認）。缺陷 Issue 為測試執行後的**獨立流程**，見 `Testing/缺陷追蹤/`，與產測試分開。

## 資料夾結構

| 資料夾 | 用途 |
|--------|------|
| `PM/` | iniRFP 決策：新手包 `index.html`、售前文件 `input/`、產出 `output/`、會議／訪談記錄 `Meeting/` |
| `PRD/` | 產品需求文件；撰寫依 `PRD/rules/PRD撰寫規範.md` |
| `SA/` | 系統分析（功能分析層）；撰寫依 `SA/rules/SA撰寫規範.md` |
| `SRS/` | 系統需求規格（頁面 spec 層）；撰寫依 `SRS/rules/SRS撰寫規範.md` |
| `Testing/` | 測試案例＋缺陷追蹤；撰寫依 `Testing/test-cases/rules/測試案例撰寫規範.md` |

`PM/output/iniRFP.md` 是 PRD／SA／SRS 的上游輸入——全域/非功能決策在此彙整（為 RFP 規格初稿，可能不完整，後續文件據此撰寫並補釐清）。

## 原則

- **只填合約白紙黑字綁定的項目**，並標註出處；推測或屬設計層的，列入「待決策」，不要硬填。
- 合約**沒提到**的（例：無障礙、密碼複雜度），明確標為「待確認」，不要預設。
- **版控動作（git add／commit／push）只有使用者明說才執行**，平時只改檔＋預覽。
- **不寫變更紀錄**：任何文件內都不新增「版本修改紀錄／變更紀錄／修訂歷史」區段；版本演進交由 git／版控追蹤，修訂時直接改寫內容。
