# 全域 UI（共用 chrome，寫一次）

> 全站一致的 UI 元素**只在此定義一次**，各頁 SRS 不重寫。
> 導覽選單結構與 breadcrumb 路徑見 [功能地圖.md](功能地圖.md)（導覽權威）；頁標題屬逐頁，寫在各頁。
> 產 SRS 首批由 `SAtoSRS` 填寫本檔骨架。

## topbar（頂部列）

| 區域 | 內容 |
|---|---|
| 左 | 系統名稱：{系統名稱} |
| 右 | 登入者名稱、登出 |

> 登入頁等前置頁不顯示 topbar 與左側選單。

## 左側全域導覽選單

- 選單群組與項目依 [功能地圖.md](功能地圖.md)「選單階層」展開。
- 各帳號可見的選單項目，依其角色功能權限動態顯示。

## 頁尾

- {版權／版本資訊；RFP 未明則待釐清}

## 全站共用行為（承接 iniRFP 全域決策，不重寫）

- 瀏覽器支援、密碼規範、錯誤鎖定、session／自動登出、Token 失效檢查等：承接 [PM/output/iniRFP.md](../PM/output/iniRFP.md)。
- 表單送出／列表／Toast／按鈕詞彙／權限不足／Session 等共用行為：見 [rules/srs_common_rules.md](rules/srs_common_rules.md)。

## 待釐清項目

- topbar／頁尾實際內容、是否有全站搜尋或通知區，RFP 未明文則待釐清。
