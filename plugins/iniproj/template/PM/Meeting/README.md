# PM/Meeting — 會議／需求訪談記錄

存放需求訪談、客戶會議的逐字稿或紀錄，作為 PRD 的來源之一。

## 用途

- 把會議／訪談記錄（`.md`／`.txt`／`.docx`／`.pdf`）放進本資料夾。
- 由 PRD 產出 skill 讀取，轉成 `PRD/` 下的 R檔。

## 怎麼用

- 把會議記錄放進本資料夾後，跟 Claude 說「會議產PRD」（或 `MeetingtoPRD`）。可指定檔案：
  `/MeetingtoPRD PM/Meeting/2026-06-20訪談.md`（不指定則 Claude 會列出本夾檔案請你選一份）。
- `MeetingtoPRD` 會萃取需求並**新增或修訂**既有 R檔（動工前先列清單給你確認）；與 `iniRFPtoPRD` 共用同一份 `PRD/rules/PRD撰寫規範.md`。

> 與 `PM/input/`（售前文件 RFP／合約）區別：`input/` 是開新案問答的依據，產出 `PM/output/iniRFP.md`；`Meeting/` 是 PRD 的另一類來源，直接餵給 PRD 產出 skill。
