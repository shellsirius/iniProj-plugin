# iniProj 開新案 Plugin

一個 Claude Code plugin。團隊安裝一次後，在**任何資料夾**對 Claude 說「**開新專案**」，
它就會把一整包「PM 專案起始範本」複製到你指定的路徑——含 PM／PRD／SA／SRS 流程與自帶 skills。

## 團隊安裝（每位成員做一次）

在 Claude Code 裡輸入：

```
/plugin marketplace add sharon-pmux/iniProj-plugin
/plugin install iniproj
```

> `sharon-pmux/iniProj-plugin` 是 GitHub `owner/repo` 簡寫；推上 GitHub 後改成你實際的帳號／倉庫名。
> 也可用完整 git URL，或私有倉庫的 SSH URL。

安裝後重啟（或 `/exit` 再進）Claude Code，讓 plugin 生效。

## 使用

1. 在任何地方開 Claude Code，說：
   ```
   開新專案
   ```
2. Claude 會問你**目標母目錄**與**專案名稱**，然後把整包複製過去。
3. 接續流程（在新專案資料夾內進行）：
   - 有售前文件 → 丟進 `PM/input/`，說「importRFP」→ 產 gap-analysis＋預填、開新手包網頁。
   - 直接填問卷 → 雙擊 `PM/index.html`。
   - 之後：「產PRD」→「產SA」依序往下。

> ⚠️ 後續 skill（importRFP／iniRFPtoPRD…）綁在**範本包自身**的 `.claude/skills/`，
> 需要 Claude「站進新專案資料夾」才會載入（`cd <新專案>` 後再 `claude`，或 VS Code 開該資料夾）。

## 結構

```
iniProj-plugin/
├── .claude-plugin/marketplace.json     # marketplace 入口
└── plugins/iniproj/
    ├── .claude-plugin/plugin.json      # plugin manifest
    ├── skills/開新專案/SKILL.md         # 啟動器：複製範本到指定路徑
    └── template/                        # 範本快照（會被複製成新專案）
```

## 維護：更新範本

`template/` 是「會被複製成新專案的骨架」。要改範本內容（題庫、規範、skills…），
直接編輯 `plugins/iniproj/template/` 底下的檔案後 commit／push 即可；
團隊下次 `/plugin marketplace update iniproj` 後生效。

> 若你另有一份工作用的 `iniProj/` 當編輯源，記得改完同步覆蓋到 `template/`，避免兩邊走鐘。
