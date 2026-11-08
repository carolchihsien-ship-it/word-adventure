# 單字大冒險專案指示

## 固定架構
- 每份題庫以單一 `index.html` 部署 GitHub Pages，HTTPS 頂層頁面使用麥克風；維持原有 Pages＋GAS 架構。
- 健康飲食舊網址：專案根目錄 index.html，https://carolchihsien-ship-it.github.io/word-adventure/ 。
- 蔬果攝取新網址：fruit-veggie/index.html，https://carolchihsien-ship-it.github.io/word-adventure/fruit-veggie/ 。更新任一題庫不可覆寫另一份題庫頁面。
- 第三份「預算內買蔬果」：budget-shopping/index.html，https://carolchihsien-ship-it.github.io/word-adventure/budget-shopping/ 。
- 使用者已指定：每份新教材必須新增獨立網址及 QR，保留所有舊練習、舊網址、舊 QR 與舊成績，不再以新教材覆蓋原網址。
- content/quiz-manifest.json 是各份題庫的網址、來源 JSON、頁面路徑、成績分頁與 QR 對照；新增教材時增加項目，不移除舊項目。
- gas/QuizCatalog.gs 以白名單 GAME_ID 分流單字表與成績分頁；content/update-catalog.cjs 由 manifest 中所有來源 JSON 重建目錄。不得用單一預設題庫拒絕另一個仍在使用的網址。
- 後端 `gas/Code.gs` 以 GAS Web App 寫 Google Sheets；不改成 GAS iframe 遊戲、代理伺服器或其他託管架構。
- `doGet` 用 HtmlService 導向 PAGES_URL，保留可點擊連結作為自動導頁受阻時的備援。
- POST 使用 `text/plain;charset=utf-8`，免 CORS 預檢。TextOutput 沒有 setHeader；跨域回應由 Google 服務提供，部署後必須從 Pages 實測，不得假造成功或以 no-cors opaque 回應判定成功。

## 題庫與玩法
- 第三份來源：英文書信 p13–14、詞彙 p15–17、中文譯文 p18，共 32 個標示單字／片語＋8 個同篇書信單字，40 項、19 句例句。
- 第三份來源檔 content/budget-shopping-vocabulary.json，建置器 content/build-budget-shopping.cjs；四組為 🎓校園與生活、🧮精打細算、🧊保鮮與當季、🏷️特價尋寶。
- 第三份 GAME_ID=budget-shopping-20261108-v1，成績分頁「預算購物40字_20261108」。不擷取或公開原書信寄件／收件人的電子郵件地址。
- 第三份 Ph.D. 支援明確列出的 PhD 拼字及縮寫／Doctor of Philosophy 語音別名；不得因此放寬其他單字的比對。go well 的例句答案是 goes well，須提示第三人稱單數。
- 第三份聽力用 distractorGroup 排除 advice/suggestion、reduced/discounted、grocery/goods 等相近中文選項，避免多個合理答案。
- 以下保留第二份蔬果攝取題庫的來源紀錄，第一份健康飲食仍持續上線。
- 題庫 WORDS 40 字，GROUPS 4 組，各 10 字；目前是 2026-11-08 附件「增加水果與蔬菜攝取量的五種方法」（英文 p7、詞彙 p10–11、中文 p12）。
- 23 個詞彙頁標示單字／片語，加上同篇文章的 17 項，合計 40 項，搭配 16 句原文；只使用完整可辨識的附件內容。
- `content/fruit-veggie-vocabulary.json` 保存完整對照；`content/build-fruit-veggie.cjs` 可重建題庫資料，不重建介面邏輯。
- 分組：🍳早餐好食材、🥪午餐與點心、🥤自製蔬果飲、🍊蔬果新吃法，各十題。
- GAME_ID=fruit-veggie-intake-20261108-v1；成績寫入同一本試算表的「蔬果攝取40字_20261108」工作表，保留健康飲食40字成績及其他舊工作表。
- 健康飲食題庫也持續上線：來源 content/health-vocabulary.json，建置器 content/build-health.cjs 更新根目錄頁面；蔬果攝取建置器只更新 fruit-veggie/index.html。兩個建置器完成後都更新 QuizCatalog.gs。歷史快照在 Codex/archive/health-food-v1。
- 三關：聽力四選一、中文拼英文、SpeechRecognition 錄音。
- 拼字 400ms 自動朗讀，2600ms 尚未輸入再朗讀，取消舊畫面計時器。
- 拼字比對忽略大小寫、頭尾空白與連續空白；保留詞間空白與標點，不能將 livingroom 視為 living room。
- 聽力朗讀完整例句後以 The word is 指出題目單字；不先顯示英文答案。
- 拼字顯示原句挖空，依 `form` 判斷答案（例如 experts、bacteria、consuming），而非一律使用字典原形；變化字形要顯示複數／不規則複數／-ing 提示。
- 個別詞的 formHint 優先於通用提示；packed with、mentioned 必須提示過去分詞，Experimenting、steaming 必須提示 -ing，不能一律標成複數。
- 錄音關維持單字／片語辨識計分；提供整句朗讀與跟讀練習，不宣稱整句發音已評分。先聽範例時取消預定自動開錄，避免電腦聲音被當成學生答案。
- 拼字輸入必須保留 autocomplete=off、autocorrect=off、autocapitalize=off、spellcheck=false。
- 錄音首次按鍵取得 getUserMedia 並保留串流；之後各題自動錄音。瀏覽器仍掌握權限政策，不得保證所有裝置永不再問。
- 錄音首次加三次重試，共四次；錯誤顯示辨識文字、首字母、字數與慢速跟讀。不支援或拒絕權限可自評，逐題回顧標示自評。
- 合成語音與錄音不能同時進行；不得用寬鬆子字串或編輯距離把其他字判為答對。

## 成績與保存
- 每組完成自動 POST，只有收到 JSON ok:true 才顯示成功；未設定或離線保留本機進度，恢復網路與每 30 秒自動重試，成績單保留立即同步鍵。
- 每次 POST 帶唯一 request 參數、cache:no-store、credentials:omit，避免 Google 一次性回應網址被快取；GAS 偶發錯誤仍依本機佇列重試，不可將未讀到 JSON 的傳送當作成功。
- 紀錄ID = localStorage 裝置碼 + 全形｜ + 修剪且轉小寫的英文名字。共用裝置換學生不互蓋；同裝置同名字視為同一人。
- 每位學生的本機進度獨立保存；重新開啟直接回主選單顯示 N/12；換學生不清除別人的進度。
- 重新開始只清本機該生進度；老師表格保留最高分，介面須明確說明。
- 各組保留最高分；未傳送的組別保留舊值；後端加鎖防同時新增重複列。
- 總分為 12 組加總，滿分 120；百分比 = 總分/120 四捨五入。尚未測驗顯示未測。
- 錯誤清單由各組最高分對應的逐題明細重新合併、去重、不截斷。明細保存在紀錄ID儲存格備註，不增加指定表格欄位。
- 欄位固定：時間、英文名字、關1-組1…關3-組4、總分、百分比、評語、錯誤單字、紀錄ID。
- 不上傳錄音檔，只傳分數、單字索引與自評標記；分數屬學生自助練習用途，前端不是防作弊考試系統。

## 更新與驗證
1. 編輯 content/build-fruit-veggie.cjs 或 index.html 題庫、GAME_TITLE；更換題庫時 GAME_ID 與後端 GAME_ID、WORDS_JSON 同步更新，另建工作表（SHEET_NAME）以避免版本混用。單字原形 e、句中答案 form、formHint、sentence 與來源頁碼須一起維護。
2. `node tests/check.cjs`：抽出 script 檢查語法並產生 gas/words.json，測試後端合併與最高分。
3. `node tests/browser.cjs`：需 playwright 與 Edge；手機流程測試，辨識服務以模擬驗證，實際手機仍需實測麥克風。
   `node tests/budget-shopping.cjs` 驗證第三份題庫、PhD 別名與字形答案、同義詞干擾排除、舊進度保留；`node tests/check.cjs budget-shopping/index.html` 抽出新頁面語法檢查。
   `node tests/fruit-veggie.cjs`：驗證新網址的 23＋17 題、16 句、字形答案與手機版面。`node tests/health.cjs` 驗證舊網址的健康飲食題庫。`node tests/catalog.cjs` 驗證同一學生跨題庫分數／錯誤清單分開保存。
4. `clasp push -f`；更新時 `clasp deploy --deploymentId <既有ID>`，不可每次建立新 ID。
5. `gh api` contents PUT，攜帶既有 sha 及 base64，使用 `--input` JSON 檔；不可覆寫其他專案。
6. Pages 上線後，測遊戲 HTTP 200、實際跨域 POST ok:true，以及同紀錄ID第二次 msg=已更新。記錄驗證日期、網址與結果。
   雙網址驗證：node tests/live-both.cjs，兩個頁面都須通過，並檢查後端回應 gameId、sheetName 與不同測試分數，確定沒有寫錯分頁。
   現行所有題庫驗證：node tests/live-all.cjs，依 manifest 檢查每個正式網址與各自新增／更新；報告在 Codex/all-quiz-url-check.json。
7. 真正網址確認後才產生 QR.png；未部署不能提供假網址或假 QR。
   QR.png 保持連到根目錄健康飲食題庫；QR-fruit-veggie.png 連到新的蔬果攝取網址，不覆蓋舊 QR。
   第三份 QR-budget-shopping.png 僅連到 budget-shopping/。

## 來源與保護
- 原始成功範本：https://sharlicwu.github.io/word-link-game/ ，保存在 reference.html；不得將其 SAVE_URL 當作老師後端。
- `customize.cjs` 是從原始範本重建的轉換腳本。修改 index.html 後不要隨意重跑覆蓋新增設定。
- 不提交 `.clasprc.json`、OAuth token、個人照片與部署暫存內容；不自動推送工作目錄所有檔案。
