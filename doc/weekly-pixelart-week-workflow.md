---
name: weekly-pixelart-week-workflow
description: Weekly_PixelartTutorials full workflow—32doc WeChat-pushed tutorials into doc/issue-*.md + README, optional Bilibili space 鸡汤 (UID 11484264, non-pinned text dynamic), strip issue blockquotes on request, git commit+push. Triggers include 更新第N期、检查上一周32doc、今日教程收斂、B站动态鸡汤、去掉備註、提交推送GitHub.
---

> **版控與 Cursor**：本檔在 `doc/` 下，**會被 git 追蹤**。本倉庫 `.gitignore` 忽略整個 `.cursor/`，Skill 無法從 GitHub 下發到 Cursor。若要在本機讓 Agent 自動載入，請將本檔**複製**為 `.cursor/skills/weekly-pixelart-week-workflow/SKILL.md`（目錄若無則自建），或建立符號連結指向本檔。  
> **更名說明**：原 **`weekly-pixelart-issue`** 已全部合併於此；`.cursor/skills/weekly-pixelart-issue/SKILL.md` 僅保留 **stub**，引導讀本檔。

# 週刊像素教程工作流（含 issue + 雞湯 + GitHub）

把「**本倉庫週更** + **`32doc` 已推送公眾號的像素畫教程**收斂進 `doc/issue-<期號>.md` / `README.md`」，並可選：**刪除 issue 內備註**、**從 B 站空間取雞湯**、**提交並推送到 GitHub**。

## 觸發說法（示例）

- 「更新第 362 期」「本週教程寫進最新一期」「同步 README 四月」
- 「檢查上一週 `~/32doc` 發到公眾號的像素畫教程，更新專案 `issue` md」
- 「去掉 `issue-363` 裡那段 `> 備註：…`」
- 「用 B 站動態當雞湯」「訪問 bilibili…最新**非置頂**動態」
- 「用 web access 試試」（與 B 站抓取相關時）
- 「推送和提交到 GitHub」「commit / push 到 origin」

使用者可給定期號（如 `362`）與可選 **32doc 根路徑**（預設 `~/32doc`）。

## 路徑約定

| 用途 | 預設路徑 |
| --- | --- |
| 本倉庫根目錄 | 當前工作區 `Weekly_PixelartTutorials` |
| 期數正文 | `doc/issue-<期號>.md` |
| 目錄 | `README.md`（依 `# 年份` → `## 月份` 小標題掛連結） |
| 教程成稿與原文 URL | 使用者本機 `32doc`：`categories/tech/pixel-art/YYYY-MM-DD-*.md`（路徑不同時以使用者指定為準） |
| 公眾號雞湯 UP 主（可選） | `https://space.bilibili.com/11484264/dynamic`（教你畫像素畫，UID `11484264`） |

## 資料來源：只收「已推送公眾號草稿」的教程

**納入條件**：該篇已在 `32doc` 執行 `wechat_publish_with_keychain.sh`，且對話／終端輸出中有**成功建立草稿**（例如回傳 `media_id`）。僅生成 MD、未推送或推送失敗的檔案**不列入**本期「本周教程」。

**如何核對（優先順序）**

1. 在 Cursor **agent transcripts**（`32doc` 專案）搜尋：`wechat_publish_with_keychain.sh`、`pixel-art/20..-..-`、`media_id`／`草稿`，對應到具體 `.md` 檔名。
2. 若無紀錄：請使用者確認當週實際推送篇目，**不得臆補**。

## 從教程 MD 抽取「原文 URL」

在每篇已推送的 `32doc` 教程檔內找**單一來源**（取第一處即可，與成稿一致）：

- `原文链接：` 之後的 URL  
- 或 `**参考来源**：`／`来源：` 區塊內的連結  

訪談稿用訪談頁 URL；無單一 URL 的輯錄／原創綜寫不納入「教程條目」（除非使用者明確要求）。

## `doc/issue-<期號>.md` 成稿格式（自動部分）

1. **摘要 / 关键词**  
   - 與既有各期一致，關鍵詞含「本周教程」（或與使用者約定用詞）。

2. **`## 本周教程`**（或使用者指定 `## 本週教程`）  
   每一篇**固定四行一組**（組與組之間空一行）：

   ```text
   (N) <教程主题（簡短中文 + 必要時英文括註）>

   <原始 URL，單行；可為純 URL 或 Markdown 連結>
   <一句簡短評論：適合誰、講什麼、技法／訪談／工作流>
   ```

3. **序號**  
   - 主題行**勿**單獨使用 `1.` `2.` 行首格式：GitHub／VS Code 預覽會把每段當成「新的一條有序清單」，中間夾空行與 URL 時常**全部顯示為 1**。  
   - 請改用行首 **`(1)` `(2)` … `(7)`**（半形括號 + 數字）或 **`【1】`** 等**不會被解析成 Markdown 有序清單**的寫法。

4. **`## 鸡汤摘录` 或 `## 雞湯`**  
   - **預設**：不自動生成正文；只保留小標題，或加一行 `（以下請手動貼上本周雞湯。）`，由使用者在編輯器內**手動貼上**。  
   - **若使用者明確要求**從 B 站空間取雞湯：依下文「B 站空間動態」規則自動填入；若使用者**另貼定稿全文**，**以使用者文字為準**覆寫。

## `README.md` 更新（自動）

- 在對應 **年份**（如 `# 2026`）下，**月份**小標（如 `## 四月`）增加一條：  
  `- 第<期號>期：[標題或主題簡稱](doc/issue-<期號>.md)`  
- 期號與月份與使用者約定一致；若尚無該年／月區塊則新建。
- **勿**把本期誤塞進錯誤年份／月份。

## 階段 A：依使用者要求調整 issue 正文（可選）

- 若使用者要求**刪除**某段（例如整段 `> 備註：…` blockquote）：**僅刪除該段**，不擅自改寫「本周教程」條目。
- **不要**在未獲使用者同意下，自行加回長篇「本機無 media_id／請核對後台」類備註。

## 階段 B：B 站空間動態 → `## 鸡汤摘录`（可選）

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

1. **首選 `web-access` skill**（`~/.claude/skills/web-access/SKILL.md`）：執行 `check-deps.mjs`；通過後在回覆中展示該 skill 要求的 **温馨提示**；以 `http://127.0.0.1:3456/new` 開分頁、`/eval` 內 `fetch` 與網頁同源之 `https://api.bilibili.com/x/polymer/web-dynamic/v1/feed/space?host_mid=11484264&timezone_offset=-480&platform=web&features=itemOpusStyle` 取得 JSON 後解析。
2. **若 CDP Proxy（`3456`）無法連線或逾時**：可改用 **Playwright** 等無頭瀏覽器在 **page context** 內 `fetch` 同一 API；並在回覆中**誠實說明**未走使用者已登入的 Chrome（與 web-access 差異），**不得假稱**已用 CDP。
3. 將選定文案寫入當期 `doc/issue-<期號>.md` 的 `## 鸡汤摘录`；可視需要把 `README` 該期連結文字改為與雞湯主題一致的**短簡稱**。

## 階段 C：GitHub 提交與推送（可選）

- **此處「推送」指 `git push` 到 GitHub**，**不是**微信公眾號草稿；公眾號仍由 `32doc` 的 `wechat_publish_with_keychain.sh` 處理。
1. `git status` / `git diff`：確認變更範圍；若誤動到其他期（例如 `issue-362` 教程條目數），**先還原再提交**。
2. 使用**具體**的 conventional commit subject（例如 `docs: add issue 363 and May 2026 README entry`），避免僅日期或空泛描述。
3. `git commit`（非互動）→ `git push origin <當前分支>`；若被拒絕則 `git pull --rebase origin <branch>` 後再推。

## 執行順序（Agent 總表）

| 順序 | 動作 |
| --- | --- |
| 1 | 確認期號、`32doc` 路徑、日期範圍（如「上一週」）。 |
| 2 | 列出該區間內**已推送**教程檔名 → 逐篇抽取原文 URL + 讀標題／首段 → 寫**簡短評論**。 |
| 3 | 寫入或更新 `doc/issue-<期號>.md` 的「本周教程」區塊；更新 `README.md` 對應年／月連結。 |
| 4 | 若未做 B 站自動雞湯：告知使用者在「鸡汤摘录／雞湯」**手動補正文**（見上文預設）。 |
| 5 | 若使用者要求：執行**階段 A**（刪備註等）。 |
| 6 | 若使用者要求：執行**階段 B**（B 站雞湯）。 |
| 7 | 若使用者要求：執行**階段 C**（GitHub）。 |

## 注意

- **Context7**：本流程以倉庫與本機檔案為主，不依賴特定 npm 套件文件。  
- 與「今日教程」skill（`32doc` 內建）分工：`get-pixel-art-tutorial` 負責產文與推送；**本工作流只負責把已推送結果（與可選雞湯、Git）收斂進 `Weekly_PixelartTutorials`**。
