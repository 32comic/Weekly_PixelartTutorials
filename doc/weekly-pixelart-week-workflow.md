---
name: weekly-pixelart-week-workflow
description: Monday weekly automation for Weekly_PixelartTutorials—create last week's doc/issue-*.md from 32doc WeChat-pushed tutorials, auto-fetch Bilibili 鸡汤 (UID 11484264, non-pinned text), update README, git commit+push. Also ad-hoc triggers for 更新第N期、删備註、只推git.
---

> **版控與 Cursor**：本檔在 `doc/` 下，**會被 git 追蹤**。本倉庫 `.gitignore` 忽略整個 `.cursor/`，Skill 無法從 GitHub 下發到 Cursor。若要在本機讓 Agent 自動載入，請將本檔**複製**為 `.cursor/skills/weekly-pixelart-week-workflow/SKILL.md`（目錄若無則自建），或建立符號連結指向本檔。  
> **更名說明**：原 **`weekly-pixelart-issue`** 已全部合併於此；`.cursor/skills/weekly-pixelart-issue/SKILL.md` 僅保留 **stub**，引導讀本檔。

# 週刊像素教程工作流（每週一例行 + GitHub）

## 例行目標（設計給「每週一」跑）

在**每週一**觸發本流程時（或由使用者／排程在週一啟動 Agent），應**一次做完**下列閉環，無需再問「要不要推 Git」：

1. **新建或更新**當期 `doc/issue-<期號>.md`：寫入摘要、關鍵詞、「本周教程」；雞湯區依階段 B **例行覆寫**（若使用者當輪聲明「保留現有雞湯」則跳過階段 B）。
2. 依 **`32doc` 上一自然週內已推送公眾號**的像素畫教程，填好 **`## 本周教程`**（規則見下）。
3. **自動**從 B 站 UID **`11484264`** 空間動態抓取**非置頂之文字動態**（見階段 B），寫入 **`## 鸡汤摘录`**。
4. 更新 **`README.md`** 對應年／月與期數連結。
5. **`git commit`** + **`git push origin`** 當前分支（非互動；落後則 `pull --rebase` 再推）。

**排程說明**：本 skill 定義的是「**被觸發時 Agent 必須做完的事**」。真正的每週一鬧鐘需由 **Cron、GitHub Actions、或使用者週一固定開對話** 觸發；未觸發則不執行。

## 「上一週」與期號 `<期號>`

- **上一自然週（曆週一～週日）**：以執行當日 `D`（系統或使用者給的「今天」）為準，令 `weekMonday(D)` 為 `D` 所在曆週的週一 00:00（曆法上週一）。則**收斂區間**為 **`weekMonday(D) - 7` 日至 `weekMonday(D) - 1` 日**（含端點）。當 `D` 為週一時，即「剛結束的那一週」。
- **期號**：若使用者未指定，取 `doc/issue-*.md` 檔名中**最大期號 + 1**（僅比對 `issue-<數字>.md`）。若與使用者口頭期號衝突，**以使用者指定為準**。

## 觸發說法（示例）

- **每週一例行**：「跑週刊」「本週一週報」「更新上一週 issue」——依上文**必達五步**執行（含 B 站雞湯與 GitHub）。
- 「更新第 362 期」「本週教程寫進最新一期」「同步 README 四月」
- 「檢查上一週 `~/32doc` 發到公眾號的像素畫教程，更新專案 `issue` md」
- 「去掉 `issue-363` 裡那段 `> 備註：…`」（**臨時**，見階段 A）
- 「只提交推送 GitHub」（**臨時**；仍須 `status`/`diff` 檢查）

使用者可給定期號與可選 **32doc 根路徑**（預設 `~/32doc`）。

## 路徑約定

| 用途 | 預設路徑 |
| --- | --- |
| 本倉庫根目錄 | 當前工作區 `Weekly_PixelartTutorials` |
| 期數正文 | `doc/issue-<期號>.md` |
| 目錄 | `README.md`（依 `# 年份` → `## 月份` 小標題掛連結） |
| 教程成稿與原文 URL | 使用者本機 `32doc`：`categories/tech/pixel-art/YYYY-MM-DD-*.md`（路徑不同時以使用者指定為準） |
| 雞湯來源（例行固定） | B 站 `https://space.bilibili.com/11484264/dynamic`（教你畫像素畫，UID `11484264`） |

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
   - **每週一例行**：必須依**階段 B**自動填入；`README` 該期連結簡稱優先取自雞湯首句或主題（簡短即可）。  
   - **若 B 站抓取失敗**或無符合條件之文字動態：在雞湯區保留標題並加一行 `（本周鸡汤抓取失败，请手动从空间动态粘贴。）`，**仍須**完成 README 與 Git 提交，並在回覆中說明原因。  
   - **若使用者當輪貼出定稿全文**：**以使用者文字為準**覆寫後再 commit。

## `README.md` 更新（自動）

- 在對應 **年份**（如 `# 2026`）下，**月份**小標（如 `## 四月`）增加一條：  
  `- 第<期號>期：[標題或主題簡稱](doc/issue-<期號>.md)`  
- 期號與月份與本期**收斂區間內最後一日**所屬曆月一致（跨月時以週內多數日期或使用者約定為準）；若尚無該年／月區塊則新建。
- **勿**把本期誤塞進錯誤年份／月份。

## 階段 A：依使用者要求調整 issue 正文（臨時／可選）

- 若使用者要求**刪除**某段（例如整段 `> 備註：…` blockquote）：**僅刪除該段**，不擅自改寫「本周教程」條目。
- **不要**在未獲使用者同意下，自行加回長篇「本機無 media_id／請核對後台」類備註。

## 階段 B：B 站空間動態 → `## 鸡汤摘录`（每週一例行必做）

### 目標

- 從 **UID `11484264`** 空間動態取**本期雞湯**文案。
- **非置頂**：略過 `modules.module_tag.text === "置顶"` 的項目。

### 關鍵規則（易錯點）

- **錯誤做法**：在 `polymer` API `data.items` 裡，略過置頂後**直接取第一條**——置頂後第一條非置頂常仍是**新發影片**，不是短文字動態。
- **正確做法**：略過置頂後，**優先選取** `type === "DYNAMIC_TYPE_WORD"` 或 `"DYNAMIC_TYPE_DRAW"` 且 `modules.module_dynamic.desc.text` 有內容之條目；必要時再比對 `opus` 等欄位。
- 使用者**另貼定稿**時以使用者為準（見上文）。

### 工具順序（web-access → 備援）

1. **首選 `web-access` skill**（`~/.claude/skills/web-access/SKILL.md`）：`check-deps.mjs` → 展示 **温馨提示** → `http://127.0.0.1:3456/new` + `/eval` 內 `fetch`：`https://api.bilibili.com/x/polymer/web-dynamic/v1/feed/space?host_mid=11484264&timezone_offset=-480&platform=web&features=itemOpusStyle`。
2. **若 CDP 不可用**：**Playwright** 於 page context 內 `fetch` 同一 API；回覆中誠實說明未使用已登入 Chrome，**不得假稱**已用 CDP。

## 階段 C：GitHub 提交與推送（每週一例行必做）

- **「推送」指 `git push` 到 GitHub**，**不是**微信公眾號；公眾號仍由 `32doc` 的 `wechat_publish_with_keychain.sh` 處理。
1. `git status` / `git diff`：確認變更範圍；誤動其他期（如 `issue-362` 條目數）**先還原**。
2. **具體**的 conventional commit subject（例如 `docs: add issue 364 and weekly 鸡汤 for 2026-05-05`）。
3. `git commit`（非互動）→ `git push origin <當前分支>`；被拒絕則 `git pull --rebase origin <branch>` 再推。

## 每週一例行執行順序（Agent 必達）

| 順序 | 動作 |
| --- | --- |
| 1 | 依「今天」推算**上一自然週**日期區間；決定**期號**（預設 max+1）。 |
| 2 | 核對 `32doc` **該區間內已推送**教程 → 抽取原文 URL 與評論 → 寫入／新建 `doc/issue-<期號>.md` 的「本周教程」。 |
| 3 | **階段 B**：自動寫入「鸡汤摘录」（失敗則占位說明，仍繼續）。 |
| 4 | 更新 `README.md` 年／月與期數連結。 |
| 5 | **階段 C**：`git commit` + `git push`。 |

**臨時指令**（非整輪週一）：僅執行對應階段（例如只刪備註＝階段 A；只推 git＝在確認 diff 後執行階段 C）。

## 注意

- **Context7**：本流程以倉庫與本機檔案為主，不依賴特定 npm 套件文件。  
- 與「今日教程」skill（`32doc` 內建）分工：`get-pixel-art-tutorial` 負責產文與微信草稿；**本工作流**負責把**已推送結果**收斂進本倉、**例行**補 B 站雞湯與 **GitHub**。
