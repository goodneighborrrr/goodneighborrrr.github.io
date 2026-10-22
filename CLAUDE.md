# EQ 單字刷題 — 網站空間

這個公開空間就是 App 本身，網址是 https://goodneighborrrr.github.io 。推送到 `main` 之後，GitHub Pages 大約 1 分鐘內會更新。

主人沒有程式背景，用繁體中文、白話跟她說明，不要用術語。

## 請 Claude 幫忙加單字（收件匣）

主人說「幫我加 xxx」時，把單字寫進 `inbox.json`。App 每次打開（或按左上角重新整理）會讀這個檔，把還沒匯入過的字加進她的雲端單字庫。

每個字一筆，加在 `words` 陣列最後面，不要改動或刪除已經存在的項目（App 用 `id` 判斷有沒有匯入過）：

```json
{
  "id": "20261016-diligent",
  "word": "diligent",
  "pos": "形容詞",
  "meaning": "勤奮的；用功的",
  "sentences": [
    { "en": "Our most diligent employees often stay late to make sure every report is accurate.", "zh": "我們最勤奮的員工常常留晚一點，確保每份報告都正確。" }
  ],
  "distractors": ["delicate", "dominant", "different"],
  "note": "",
  "addedAt": "2026-10-16"
}
```

規則：
- `id`：`日期-單字`，全小寫，同一個 id 不能重複。
- `pos` 只能是：名詞、動詞、形容詞、副詞、介系詞、連接詞、片語動詞、其他。
- `meaning`：台灣繁體中文，簡短，不同意思用「；」隔開。
- `sentences`：寫 5 句多益 Part 5 風格（商務、辦公室情境）的英文句子，每句 15–28 個字，**每句都要包含一模一樣拼法的 `word`，而且只出現一次**（App 會把它挖空出題），附自然的台灣繁體中文翻譯。
- `distractors`：3 個詞性相同、長得或聽起來像、但放進句子裡都不對的英文字。
- `note`（備註，可以空白）：有常見同義詞或容易搞混的字時，用台灣繁體中文寫 1～3 句，說明差別（例如句型不同、意思不同、拼法很像）。答完題目時會顯示在答案最下面。
- **錯的選項要有中文**：App 答完題目時，會在錯的選項旁邊顯示中文意思（先查單字庫，再查 `gloss.json`）。所以每次新增單字，`distractors` 裡不在單字庫的英文字，都要在 `gloss.json` 的 `words` 補上簡短的台灣繁體中文意思（key 全小寫）。
- 選擇題的正確答案位置不要老是放在同一個字母（App 本身會隨機打亂，這條是給對話裡出的小測驗）。
- 主人只考單字，不考文法和片語。
- 單字拼錯或看不懂時，先問主人。

**幫已經加過的字補備註**：不要改舊的那筆（App 已經匯入過，不會再讀）。改成在 `inbox.json` 最上層的 `notes` 陣列加一筆 `{"id": "note-日期-單字", "word": "單字", "note": "備註內容"}`，App 下次打開時會把備註補到單字庫裡那個字（每個 id 只套用一次，要再改就用新的 id）。

做法：修改 `inbox.json` → 檢查是合法 JSON（`python3 -m json.tool inbox.json`）→ commit，說明用繁體中文，例如「新增單字：diligent、postpone」→ push 到 `main`。完成後告訴主人：等 1 分鐘，打開 App（或按左上角重新整理）就會看到「已加入 N 個新字」。

## 英語口說練習

`speaking/` 這個資料夾是另一個小功能：用瀏覽器內建的語音功能陪主人練習英文口說，說完一輪後請 Gemini 糾正、接著聊。細節、重要決定（為什麼沒有用 Firebase Cloud Function）跟已知的 iPhone 限制，都寫在 `speaking/CLAUDE.md`，改這個功能前先看那份。

## 其他

- 不公開的備份空間是 `goodneighborrrr/eq-vocab-app`，App 的檔案兩邊要保持一致。
- 雲端單字庫在 Firebase 專案 `eq-vocab-app`（Firestore，`users/{uid}/words`），只有主人的帳號能讀寫。
- 測試「隔天再考」：網址後面加 `#test`，「隔天」會變成「1 分鐘後」。
