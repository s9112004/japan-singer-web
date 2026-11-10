# 搶票表自動更新指令

這份檔案是給 Claude 排程用的完整指令。每次執行都從零開始，不記得之前的對話，所以所有需要的資訊都寫在這裡。

---

你要更新「日系演唱會搶票表」。專案是這個 git repo（就是這份檔案所在的 repo），網站由 GitHub Pages 從 `main` 分支部署：https://s9112004.github.io/japan-singer-web/

使用者在台灣，用繁體中文。頁面整理這些歌手的演出日期與售票時程：米津玄師(yonezu)、King Gnu(kinggnu)、星街すいせい(suisei)、YOASOBI(yoasobi)、ずっと真夜中でいいのに。(zutomayo)、久石讓(hisaishi)、藤井風(fujii)、Vaundy(vaundy)、tuki.(tuki)、IU(iu，韓國歌手)。

## 帳號規則

- 只能用使用者的個人 GitHub 帳號 **s9112004** 推送。推送前先跑 `gh auth status` 或 `git credential fill` 確認帳號；如果不是 s9112004，就停止，不要推送，並在回報裡說明。
- 不要用任何公司帳號（例如 stevenhsu-gama、@gamania.com）。

## 步驟

1. 在 repo 根目錄執行 `git pull --rebase origin main`。
2. 先算出「今天」的日期（台灣時間）。逐一核對每位歌手：
   - 所有「受付中／即將開始／等結果／未公布」的售票階段，日期時間是否仍正確。
   - 有沒有新公布的場次、追加場、取消、新的售票階段（FC 先行、官方先行、ぴあ／e+／ローチケ先行、一般發售、官方轉售、台灣遠大／KKTIX／拓元售票）、售完消息。
   - 特別注意台灣場次（台北大巨蛋、小巨蛋、高雄等）。

   用網路搜尋（日文、繁中、英文）和讀網頁。來源優先順序：官方網站、官方 FC、官方 X 帳號、官方售票網站（e+、ぴあ、ローチケ、遠大、KKTIX）> 新聞 > 部落格／整理網站。

   官方網站：vaundy.jp、member.vaundy.jp、clubgnu.com、sonymusic.co.jp/artist/kinggnu、hoshimachi-suisei-fc.jp、hoshimachi-suisei.jp、yoasobi-music.jp（是 JS 頁，可改讀 https://www.sonymusic.co.jp/json/v2/artist/YOASOBI/information/start/0/count/100/callback/InfoCallcack）、zutomayo.net/apicula/、joehisaishi.com、hehn.fujiikaze.com/pwt/、fujiikaze.com、tuki-official.net（tuki-official.com 不是她的）、reissuerecords.net（米津）、ticket.kenshiyonezu.jp。

   IU：經紀公司 EDAM 公告 https://www.edam-ent.com/html/sub02/sub02_0201.php?vType=list&bbsCode=b_notice02&page=1 、Melon Ticket（tkglobal.melon.com 國際版）、Interpark／NOL Ticket、韓文新聞（搜「아이유 콘서트」「아이유 티켓 예매」）。2026 年 9 月高陽場已取消（`cancelled:true`），IU 說最晚 2027 年初回歸；有新巡演、台灣場或售票（선예매／일반예매）公布時新增 `TOURS` 和 `PHASES`，時間用韓國時間 `+09:00`（與日本相同）。

3. 只改 `page.html` 裡 `<script>` 的資料，不要改版面設計：
   - `PHASES`：每筆售票 `{a, tour, name, kind:"range"|"point"|"tba", start, end(可省略=截止未公布), result, at, memo, unverified:true(只有第三方來源), cancelled:true(確定不舉辦), tid/city/dates(對應場次), url(售票頁), homeHide:true(合演在兩位歌手都有時，首頁只顯示一次)}`。時間一律寫成帶時區的字串：日本 `+09:00`、台灣 `+08:00`。
   - `TOURS`：`{id, a, title, facts, shows:[[YYYY-MM-DD, 城市, 會場・開演時間]], note}`。
   - `LINK`（售票→場次）、`TICKET`（巡演→售票頁）、`PHASE_URL`（售票階段→售票頁）、`SOURCES`（資料來源）。新增售票或巡演時，要確保它對得到場次並有售票頁連結；新增歌手要在 `ARTISTS` 和 CSS 色票（`:root` 與兩個深色區塊）都加上。
   - 頁面會自動把同一巡演的售票合併成一張卡，不用為了版面合併資料。
   - 已過的售票不用刪，頁面會自動標「已結束」。不要刪除任何歌手。
   - 使用者已確認：米津玄師台北大巨蛋 11/19 一般售票「不舉辦」（`cancelled:true`），保持這樣。
   - 內容用繁體中文，日本場附日文會場名。
4. 檢查語法：把 `page.html` 的 `<script>` 內容抽出來跑 `node --check`。
5. 重新產生網站檔：`cat head.html page.html > index.html`。
6. 有變更才 commit：
   - `git add page.html index.html`
   - commit 訊息：`每日更新 YYYY-MM-DD HH:MM：<一句話摘要>`
   - `git push origin main`
   沒有變更就不要 commit。
7. 回報（用繁體中文，這份回報會通知使用者）：
   - 第一行寫「有新消息：…」或「沒有新消息」。
   - 接著寫「今天要搶的票」和「未來 7 天要注意的售票時間」（開始、截止、結果公布，附台灣時間和售票頁連結）。
   - 再寫這次改了什麼（新增／修正／取消，每項附來源網址），以及仍無法確認的項目。
   - 如果有新消息，而且可以發送推播通知，就送一則不超過 200 字的通知，例如「Vaundy 千葉追加場ぴあ先行 11/20 12:00 開始」。
   - 不要編造任何沒查到的資訊，查不到就寫查不到。
