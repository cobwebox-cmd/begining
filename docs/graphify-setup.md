# graphify 安裝與設定

[graphify](https://github.com/Graphify-Labs/graphify) 能把整個專案（程式碼、文件、PDF、圖片、影片）轉成可查詢的知識圖譜，讓 AI 助理用查詢取代逐檔搜尋。安裝後，在 AI 助理中輸入 `/graphify` 即可使用。

- 官方原始碼：https://github.com/Graphify-Labs/graphify
- 官方文件：https://docs.graphify.com
- 授權：Apache-2.0 與 MIT

## 重點特性

- **程式碼在本機解析：** 使用 tree-sitter 語法樹，不經過 LLM，資料不會離開你的電腦。
- **每條關係都有標記：** `EXTRACTED` 表示原始碼中明確寫出，`INFERRED` 表示由 graphify 推導。
- **不是向量資料庫：** 沒有 embedding，是一張可以走訪的真實圖譜。

## 前置需求

| 項目 | 最低版本 | 檢查指令 |
| --- | --- | --- |
| Python | 3.10 以上 | `python --version` |
| uv（建議）或 pipx | 任意 | `uv --version` / `pipx --version` |

各系統安裝 Python 與 uv：

```
# macOS (Homebrew)
brew install python@3.12 uv

# Windows
winget install astral-sh.uv

# Ubuntu / Debian
sudo apt install python3.12 python3-pip pipx
# 或安裝 uv
curl -LsSf https://astral.sh/uv/install.sh | sh
```

## 安裝步驟

> **注意：** PyPI 上的官方套件名稱是 `graphifyy`（兩個 y）。其他名稱類似的 `graphify*` 套件不是官方的。安裝後的指令仍是 `graphify`。

**步驟 1：安裝套件**

```
uv tool install graphifyy      # 建議做法
# 或
pipx install graphifyy
```

若安裝後找不到 `graphify` 指令，執行 `uv tool update-shell` 後重新開啟終端機。

**步驟 2：把 Skill 註冊到 AI 助理**

```
graphify install
```

預設會註冊到 Claude Code。若只想安裝在目前專案內（可一起提交到 Git），加上 `--project`：

```
graphify install --project
```

專案內安裝會寫入 `.claude/skills/graphify/SKILL.md`。

## 其他平台

| 平台 | 指令 |
| --- | --- |
| Claude Code | `graphify install` |
| Codex | `graphify install --platform codex` |
| Gemini CLI | `graphify install --platform gemini` |
| GitHub Copilot CLI | `graphify install --platform copilot` |
| Cursor | `graphify cursor install` |
| 通用 Agent Skills | `graphify install --platform agents` |

完整平台清單請見官方 README。Codex 使用 `$graphify`，其餘使用 `/graphify`。

## 基本使用

在 AI 助理中輸入：

```
/graphify .                                  # 為目前資料夾建立圖譜
/graphify ./docs --update                    # 只重新解析有變動的檔案
/graphify query "認證和資料庫之間如何連接？"      # 用自然語言查詢
/graphify path "UserService" "DatabasePool"  # 找出兩個概念之間的路徑
/graphify explain "RateLimiter"              # 說明單一概念
```

執行後會產生 `graphify-out/` 目錄：

```
graphify-out/
├── graph.html        用瀏覽器開啟，可點選、篩選、搜尋
├── GRAPH_REPORT.md   重點摘要：核心概念、意外的關聯、建議問題
└── graph.json        完整圖譜，可隨時查詢而不必重讀檔案
```

本專案的 `.gitignore` 已忽略 `graphify-out/`。如果想與團隊共用圖譜，可以把這一行移除後再提交。

## 讓助理預設使用圖譜

在專案內執行一次：

```
graphify claude install
```

這會寫入設定，讓助理在回答程式碼問題時優先查詢圖譜。Claude Code 與 Gemini CLI 會用 hook 自動提醒；Codex、Cursor 等則使用 `AGENTS.md` 或規則檔。

## 選用擴充

只安裝需要的部分，例如：

```
uv tool install "graphifyy[pdf]"       # PDF 擷取
uv tool install "graphifyy[office]"    # .docx / .xlsx
uv tool install "graphifyy[video]"     # 影音轉錄
uv tool install "graphifyy[mcp]"       # MCP stdio 伺服器
uv tool install "graphifyy[all]"       # 全部
```

## 隱私

- 程式碼與影音：在本機處理，不外傳。
- 文件、PDF、圖片：會交給你的 AI 助理（使用其目前的模型）做語意擷取。
- 無遙測、無使用追蹤。
- 每次 `graphify query`、`path`、`explain` 會記錄到 `~/.cache/graphify-queries.log`（只記錄時間、問題、命中節點數與耗時）。設定 `GRAPHIFY_QUERY_LOG_DISABLE=1` 可關閉。

## 移除

```
graphify uninstall             # 從所有平台移除
graphify uninstall --purge     # 同時刪除 graphify-out/
```

## 疑難排解

- **`command not found`：** 執行 `uv tool update-shell`，並重新開啟終端機。
- **無法連到 PyPI：** 這通常是網路或企業防火牆限制（例如某些雲端沙盒會封鎖 `pypi.org`）。請改在可以連外的本機環境安裝。
- 其他問題請見 https://docs.graphify.com/troubleshooting
