# 發布與更新 SOP（RELEASING）

這份給**維護者**（發布新版）與**團隊成員**（更新已裝的 plugin）照著做。

> 核心觀念：**plugin 不會自動更新**，而且**沒升版號，團隊就抓不到你的修改**。

---

## TL;DR

| 角色 | 要做的事 |
|---|---|
| 維護者 | 改 `version` → `validate` → commit → push |
| 團隊成員 | `marketplace update` → `plugin update` → 重啟 Claude Code |

---

## A. 維護者：發布新版

每次改完 `plugins/iniproj/`（skill、範本、文件…）要讓團隊拿得到，缺一不可：

### 1. 升版號（最容易漏！）
編輯 `plugins/iniproj/.claude-plugin/plugin.json` 的 `version`，採語意化版號：

| 改動性質 | 版號 |
|---|---|
| 修錯字、小修文案、fallback 等不影響用法 | patch：`0.1.2` → `0.1.3` |
| 新增功能、流程（向下相容） | minor：`0.1.x` → `0.2.0` |
| 破壞性改動（觸發詞、結構大改） | major：`0.x` → `1.0.0`（1.0 前可用 minor 代表） |

> ⚠️ **沒改 version，團隊端 `plugin update` 會「找不到更新」**——他們永遠停在舊版。

### 2. 驗證 manifest（push 前自檢）
```
claude plugin validate .                 # marketplace.json
claude plugin validate plugins/iniproj   # plugin.json
```
兩個都要 `✔ Validation passed` 才往下。

### 3. commit + push
```
git add -A
git commit -m "[YYYY/MM/DD] 0.1.3：<這版改了什麼>"
git push origin main
```
（commit 訊息依慣例加 `[YYYY/MM/DD]` 日期前綴。）

### 4.（選配）打版本 tag
```
claude plugin tag plugins/iniproj
```
會建立 `iniproj--v0.1.3` 的 git tag，並檢查 `plugin.json` 與 marketplace 條目版本一致。push tag：`git push --tags`。

---

## B. 團隊成員：更新已安裝的 plugin

兩步，**順序有意義**：先更新 marketplace（重新拉 GitHub 上的版本資訊），再更新 plugin。

### 方式一：在 Claude Code 裡（直覺）
- 開 `/plugin` 選單 → marketplace `iniproj` 更新來源 → 再更新 plugin `iniproj`
- `/exit` 重開 Claude Code 套用

### 方式二：終端機 CLI（可複製貼上）
```
claude plugin marketplace update iniproj    # 先抓 GitHub 最新版本資訊
claude plugin update iniproj@iniproj         # 再升到最新版
```
然後重啟 Claude Code。

> 只跑 `plugin update`、跳過 `marketplace update`，可能抓不到剛 push 的新版。

### 確認版本
```
claude plugin list        # 看 iniproj 的 Version 是不是最新
```

---

## 首次安裝（新成員）

```
claude plugin marketplace add shellsirius/iniProj-plugin
claude plugin install iniproj@iniproj
```
或在 Claude Code 裡用 `/plugin marketplace add shellsirius/iniProj-plugin` → `/plugin install iniproj`。重啟生效。

---

## 雜項

- **本機快取會累積多版**（`~/.claude/plugins/cache/iniproj/iniproj/<版本>/`），無害；要清舊版跑 `claude plugin prune`。
- **改了範本內容（`template/`）也算一次發布**——同樣要升 version 才會傳到團隊新開的專案。
- **解除安裝**：`claude plugin uninstall iniproj@iniproj`；**移除 marketplace**：`claude plugin marketplace remove iniproj`。
