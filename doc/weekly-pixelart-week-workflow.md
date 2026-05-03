---
name: weekly-pixelart-week-workflow
description: End-to-end weekly issue workflow—sync 32doc→issue+README, optional Bilibili space 鸡汤 (non-pinned text), strip issue disclaimers on request, commit+push GitHub. Use when user asks to check last week 32doc tutorials and update issue, fetch 鸡汤 from Bilibili 11484264, remove 備註 blockquote, or push/commit to GitHub for Weekly_PixelartTutorials.
---

> **版控與 Cursor**：本檔在 `doc/` 下，**會被 git 追蹤**。本倉庫 `.gitignore` 忽略整個 `.cursor/`，故 Skill 無法直接從 GitHub 下發到 Cursor。若要在本機讓 Agent 自動載入，請將本檔**複製**為 `.cursor/skills/weekly-pixelart-week-workflow/SKILL.md`（目錄若無則自建），或建立符號連結指向本檔。

# 週刊像素教程「一週工作流」（Weekly_PixelartTutorials）

整合對話中曾出現的一組**連續指令**，收斂成可重複執行的 Agent 流程。教程條目與 URL 規則仍以 **`weekly-pixelart-issue`**（`.cursor/skills/weekly-pixelart-issue/SKILL.md`）為準；本工作流負責**編排順序、B 站雞湯、GitHub 收尾**。

## 觸發說法（示例）

- 「檢查上一週 `~/32doc` 發到公眾號的像素畫教程，更新專案 `issue` md」
- 「去掉 `issue-363` 裡那段 `> 備註：…`」
- 「用 B 站動態當雞湯」「訪問 bilibili…最新**非置頂**動態」
- 「用 web access 試試」（與 B 站抓取相關時）
- 「推送和提交到 GitHub」「commit / push 到 origin」

## 前置：路徑與專案

| 項目 | 預設 |
| --- | --- |
| 本倉庫 | `Weekly_PixelartTutorials` |
| 期數檔 | `doc/issue-<期號>.md` |
| 目錄 | `README.md`（`# 年份` → `## 月份` → 期數連結） |
| 32doc 教程成稿 | `~/32doc/categories/tech/pixel-art/YYYY-MM-DD-*.md` |
| 公眾號雞湯 UP 主 | `https://space.bilibili.com/11484264/dynamic`（教你畫像素畫） |

## 階段一：32doc → 本期 `issue` + `README`

1. **必讀** `.cursor/skills/weekly-pixelart-issue/SKILL.md`（教程格式、原文 URL 抽取、`README` 月分區規則）。
2. **「已推送公眾號」**之認定：僅收錄有 `wechat_publish_with_keychain.sh` 成功回報（如 `media_id`）之篇目；核對優先序見 `weekly-pixelart-issue`（`32doc` Cursor agent transcripts 等）。**無紀錄則不得臆補篇目。**
3. 寫入 `doc/issue-<期號>.md` 的 `## 本周教程`；**除非使用者明確要自動寫雞湯**，否則 `## 鸡汤摘录` 可留白或保留「請手動貼上」一行（與 `weekly-pixelart-issue` 一致）。
4. 更新 `README.md`：對應年／月新增 `- 第<N>期：[簡稱](doc/issue-<N>.md)`。

## 階段二：依使用者要求調整 issue 正文

- 若使用者要求**刪除**某段（例如整段 `> 備註：…` blockquote）：**僅刪除該段**，不擅自改寫「本周教程」條目。
- **不要**在未獲使用者同意下，自行加回長篇「本機無 media_id／請核對後台」類備註。

## 階段三：B 站空間動態 → `## 鸡汤摘录`

### 目標

- 從 **UID `11484264`** 空間動態取**本期雞湯**文案。
- **非置頂**：略過 `modules.module_tag.text === "置顶"` 的項目。

### 關鍵規則（易錯點）

- **錯誤做法**：在 `polymer` API `data.items` 裡，略過置頂後**直接取第一條**——置頂後第一條非置頂常仍是**新發影片**，不是使用者要的短文字動態。
- **正確做法**：在略過置頂之後，**優先選取**帶可讀正文的動態，例如：
  - `type === "DYNAMIC_TYPE_WORD"` 或 `"DYNAMIC_TYPE_DRAW"`，且 `modules.module_dynamic.desc.text` 有內容；或
  - 依實際 JSON 結構中與「純文案」對應的欄位（`opus` 等）判斷。
- 若使用者**明確貼出**應採用的雞湯全文（權威稿），**以使用者文字為準**覆寫檔案，不必堅持 API 自動結果。

### 工具順序（web-access → 備援）

1. **首選 `web-access` skill**（`~/.claude/skills/web-access/SKILL.md`）：執行 `check-deps.mjs`；通過後在回覆中展示該 skill 要求的 **温馨提示**；以 `http://127.0.0.1:3456/new` 開分頁、`/eval` 內 `fetch` 與網頁同源之 `https://api.bilibili.com/x/polymer/web-dynamic/v1/feed/space?host_mid=11484264&...` 取得 JSON 後解析。
2. **若 CDP Proxy（`3456`）無法連線或逾時**：可改用 **Playwright** 等無頭瀏覽器在 **page context** 內 `fetch` 同一 API；並在回覆中**誠實說明**未走使用者已登入的 Chrome（與 web-access 差異），**不得假稱**已用 CDP。
3. 將選定文案寫入當期 `doc/issue-<期號>.md` 的 `## 鸡汤摘录`；可視需要把 `README` 該期連結文字改為與雞湯主題一致的**短簡稱**。

## 階段四：GitHub 提交與推送

- **此處「推送」指 `git push` 到 GitHub**，**不是**微信公眾號草稿；公眾號仍由 `32doc` 的 `wechat_publish_with_keychain.sh` 處理。
1. `git status` / `git diff`：確認變更範圍；若誤動到其他期（例如 `issue-362` 教程條目數），**先還原再提交**。
2. 使用**具體**的 conventional commit subject（例如 `docs: add issue 363 and May 2026 README entry`），避免僅日期或空泛描述。
3. `git commit`（非互動）→ `git push origin <當前分支>`；若被拒絕則 `git pull --rebase origin <branch>` 後再推。

## 建議執行順序（總表）

| 順序 | 動作 |
| --- | --- |
| 1 | `weekly-pixelart-issue`：32doc 已推送篇目 → `issue` 教程區 + `README` |
| 2 | 使用者若要求：刪 `issue` 備註等 |
| 3 | 若使用者要 B 站雞湯：`web-access` 或 Playwright 備援 + **非置頂文字動態**規則 |
| 4 | 使用者若要求：`git commit` + `git push` |

## 注意

- **Context7**：本流程不依賴特定 npm 函式庫文件。
- 與 **`weekly-pixelart-issue`** 分工：該 skill 定義教程與 `README` 的編輯規範；**本工作流不覆寫其細則**，只整合「B 站雞湯 + 刪備註 + GitHub」步驟。
