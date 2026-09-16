# 專案規則 / Project Rules

這份文件記錄維護 `index.html`（SOP 主文件）時應該遵守的規則，是這個專案這段時間累積下來的經驗與慣例，不是 SOP 本身的內容——SOP 的方法論規則請直接看 `index.html` 裡的 Phase 0–7。

## 內容規則（SOP 文件本身）

1. **雙語是硬性要求**：每一段可見文字都要有成對的 `<span class="zh">` / `<span class="en">`，靠根元素的 `data-lang` 屬性切換顯示（CSS `[data-lang="en"] .zh{display:none}` 那組規則），不是即時翻譯。新增任何內容都要兩個語言都寫齊，不能只寫一種先上、之後補。
2. **Phase 編號 vs Section 編號是兩套系統**：Phase 0–7 是流程本身的階段名稱，不會因為文件改版而變動；但左側 TOC／`<span class="num">` 的章節編號（0, 1, 2…）只要中間插入新章節就要整體往後移一位。目前已經因為插入 Phase 0、Phase 7 各做過一次全文重新編號——之後要再插入新章節（含附錄子章節 11.x）都要有心理準備，並記得同步更新兩份 TOC（桌面版 `nav.toc` 和行動版 `details.toc-m`）。
3. **callout 顏色分級要守住**：`callout danger`（紅）只留給「無論統計證據多強都不能被覆蓋」等級的硬性規則或高風險警示（零覆蓋、trim/TC 溫度係數、製程漂移大 loss）；`callout warn`（橘）用於一般常見誤區或執行提醒；`callout`（無色，預設 accent 左框）用於中性參考資訊。不要把 warn 升級成 danger 只是為了引人注意，否則真正的高風險警示會被稀釋。
4. **能在 Phase 0 擋掉的，就不要留到 Phase 3 統計分析才發現**：任何新的「不可移除」類規則（規格強制、零覆蓋、量測系統有效性…）都優先考慮放進 Phase 0 §3.x，因為它代表「不需要資料就能先判斷」的前提；需要資料才能驗證的證據線（相關性、線性、離群偵測）才放進 Phase 3 §6.x。
5. **公式一律用 KaTeX**：`\[ ... \]`／`\( ... \)` 包在 `.formula .eq` 或行內文字裡，不要手打 Unicode 符號充當數學排版；新公式記得同步加進附錄「公式參考卡」（目前是 §11.3）並在延伸參考（§11.4）補上出處。
6. **每張圖三件事都要做到**：(a) 最外層 `<g>` 設 `fill="var(--ink)"`（或逐一設定顏色），避免深色模式下文字變黑字看不見；(b) 一定放在 `<figure>` 裡，讓它自動套用 `fig-zoom` 放大按鈕；(c) `figcaption` 用中英文說明圖在畫什麼、顏色/線條/虛線各代表什麼語意。

## 技術規則（HTML / CSS / JS）

1. **顏色一律用 `var(--token)`**，不要寫死色碼；新增 token 要在 `:root`（淺色預設）、`@media (prefers-color-scheme: dark)`、`:root[data-theme="dark"]` 三處都定義，深色/淺色手動切換才會一致。
2. **Flex 置中容器 + 百分比寬度子元素，容器要給實際 `width`**：只給 `max-width`（沒有 `width`）的話，容器會收縮到內容大小，裡面 `width:100%` 的 SVG／img 就沒有東西可以參照，會縮成瀏覽器預設的小尺寸而不是放大——燈箱放大圖那次的 bug 就是這樣來的。
3. **收合（collapse）預設狀態**：除了 Process Overview（`#overview`）維持展開，其餘所有 `h2` 章節預設收合；`h3`/`h4` 子章節本身預設展開（只是要等父層章節被點開才看得到）。
4. **Find 功能不能邊打字邊跳頁**：輸入時只更新結果面板內容和計數（`current = -1`，不呼叫 `highlightCurrent()`）；只有明確動作（點結果面板的項目、按 Enter、按上下箭頭按鈕）才會真的捲動跳轉並展開對應的收合區塊。
5. **服務於網址的檔案要用 ASCII 檔名**：主文件叫 `index.html`（GitHub Pages 用，避免中文檔名被編碼成一長串 `%E8%B7%A8...` 網址，分享時出問題）；純本地、不會被 URL 存取的檔案才可以用中文檔名。

## 工作流程規則

1. **本地優先，不主動用 Artifact 工具發佈**：預設寫本地檔案 + git commit；只有使用者明確要求分享/上線時才處理發佈，且優先選使用者自己能控制的地方（他自己的 GitHub 帳號），而不是 Claude 代管的 Artifact 連結。牽涉到 repo 可見度（public/private）這類影響分享範圍的決定，要先問清楚再動手。
2. **每次編輯完 `index.html` 都要 commit + push**：GitHub Pages 網站（https://sinliongtoo.github.io/semiconductor-test-coverage-sop/）不會自動反映本機變更，一定要 `git add` + `git commit`（訊息寫清楚改了什麼、為什麼）+ `git push` 到 `origin/master`，線上版本才會跟著更新。
3. **每次實質編輯都要更新版次時間戳**：masthead 的「版次更新」欄位（`<div><span class="zh">版次更新</span>...<b>YYYY-MM-DD HH:MM</b></div>`）和 footer 的同一組時間戳，是判斷「這份文件是不是最新」的唯一依據——commit 前先跑一次 `date` 拿目前時間，`sed -i` 或 Edit 把兩處（masthead + footer）都換成新時間，不要漏掉任何一處，也不要用記憶中的日期硬編。這件事已經漏做過一次（連續好幾次 commit 都忘記更新，時間戳停在舊的），要當成跟結構完整性檢查同等級的「commit 前必做項」。
4. **commit 訊息寫「為什麼」，不是條列「做了什麼」**：解釋這個變更背後解決的問題或工程理由，方便未來（包含未來的自己）回頭看 log 就懂決策脈絡，不用重新讀一次 diff。
5. **大改動後、commit 前先做結構完整性檢查**：用 grep 比對 `<section>`、`<figure>`/`<svg>`、KaTeX 公式 `\[...\]`／`\]</div>`、`<div class="callout...">` 等標籤的開合數量是否一致；再用 Node.js 對每段 inline `<script>` 跑一次 `new Function(scriptText)` 確認語法沒壞掉。三項（結構檢查、時間戳、語法檢查）都過了才 commit。

---

這份規則本身也受版本控制；有新的慣例或踩過的坑，直接更新這個檔案，不用另外開文件。
