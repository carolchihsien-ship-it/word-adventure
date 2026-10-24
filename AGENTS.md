# 單字大冒險專案指示

## 固定架構
- 前端只有 `index.html`，部署 GitHub Pages，HTTPS 頂層頁面使用麥克風。
- 後端 `gas/Code.gs` 以 GAS Web App 寫 Google Sheets；不改成 GAS iframe 遊戲、代理伺服器或其他託管架構。
- `doGet` 用 HtmlService 導向 PAGES_URL，保留可點擊連結作為自動導頁受阻時的備援。
- POST 使用 `text/plain;charset=utf-8`，免 CORS 預檢。TextOutput 沒有 setHeader；跨域回應由 Google 服務提供，部署後必須從 Pages 實測，不得假造成功或以 no-cors opaque 回應判定成功。

## 題庫與玩法
- 題庫 WORDS 40 字，GROUPS 4 組，各 10 字；目前使用使用者提供的範本題庫。
- 三關：聽力四選一、中文拼英文、SpeechRecognition 錄音。
- 拼字 400ms 自動朗讀，2600ms 尚未輸入再朗讀，取消舊畫面計時器。
- 拼字比對忽略大小寫、頭尾空白與連續空白；保留詞間空白與標點，不能將 livingroom 視為 living room。
- 拼字輸入必須保留 autocomplete=off、autocorrect=off、autocapitalize=off、spellcheck=false。
- 錄音首次按鍵取得 getUserMedia 並保留串流；之後各題自動錄音。瀏覽器仍掌握權限政策，不得保證所有裝置永不再問。
- 錄音首次加三次重試，共四次；錯誤顯示辨識文字、首字母、字數與慢速跟讀。不支援或拒絕權限可自評，逐題回顧標示自評。
- 合成語音與錄音不能同時進行；不得用寬鬆子字串或編輯距離把其他字判為答對。

## 成績與保存
- 每組完成自動 POST，只有收到 JSON ok:true 才顯示成功；未設定或離線保留本機進度，恢復網路與每 30 秒自動重試，成績單保留立即同步鍵。
- 紀錄ID = localStorage 裝置碼 + 全形｜ + 修剪且轉小寫的英文名字。共用裝置換學生不互蓋；同裝置同名字視為同一人。
- 每位學生的本機進度獨立保存；重新開啟直接回主選單顯示 N/12；換學生不清除別人的進度。
- 重新開始只清本機該生進度；老師表格保留最高分，介面須明確說明。
- 各組保留最高分；未傳送的組別保留舊值；後端加鎖防同時新增重複列。
- 總分為 12 組加總，滿分 120；百分比 = 總分/120 四捨五入。尚未測驗顯示未測。
- 錯誤清單由各組最高分對應的逐題明細重新合併、去重、不截斷。明細保存在紀錄ID儲存格備註，不增加指定表格欄位。
- 欄位固定：時間、英文名字、關1-組1…關3-組4、總分、百分比、評語、錯誤單字、紀錄ID。
- 不上傳錄音檔，只傳分數、單字索引與自評標記；分數屬學生自助練習用途，前端不是防作弊考試系統。

## 更新與驗證
1. 編輯 index.html 題庫、GAME_TITLE；更換題庫時 GAME_ID 與後端 GAME_ID、WORDS_JSON 同步更新，另建成績表以避免版本混用。
2. `node tests/check.cjs`：抽出 script 檢查語法並產生 gas/words.json，測試後端合併與最高分。
3. `node tests/browser.cjs`：需 playwright 與 Edge；手機流程測試，辨識服務以模擬驗證，實際手機仍需實測麥克風。
4. `clasp push -f`；更新時 `clasp deploy --deploymentId <既有ID>`，不可每次建立新 ID。
5. `gh api` contents PUT，攜帶既有 sha 及 base64，使用 `--input` JSON 檔；不可覆寫其他專案。
6. Pages 上線後，測遊戲 HTTP 200、實際跨域 POST ok:true，以及同紀錄ID第二次 msg=已更新。記錄驗證日期、網址與結果。
7. 真正網址確認後才產生 QR.png；未部署不能提供假網址或假 QR。

## 來源與保護
- 原始成功範本：https://sharlicwu.github.io/word-link-game/ ，保存在 reference.html；不得將其 SAVE_URL 當作老師後端。
- `customize.cjs` 是從原始範本重建的轉換腳本。修改 index.html 後不要隨意重跑覆蓋新增設定。
- 不提交 `.clasprc.json`、OAuth token、個人照片與部署暫存內容；不自動推送工作目錄所有檔案。
