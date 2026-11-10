# 交接：在這台 Mac 設定「日系演唱會搶票表」自動更新

Claude 你好，這份說明是我（使用者）從另一台電腦帶過來的。請照下面的步驟，在這台 Mac 上幫我把自動更新設定好。過程中需要我操作的地方（登入、輸入密碼、按授權）請停下來告訴我怎麼做。

## 背景

- 專案：https://github.com/s9112004/japan-singer-web （公開 repo，我的個人 GitHub 帳號 **s9112004**）
- 網站：https://s9112004.github.io/japan-singer-web/ （GitHub Pages，從 `main` 分支根目錄部署，已經開好）
- 內容：一個純靜態網頁，沒有後端。整理 9 組日本歌手（米津玄師、King Gnu、星街すいせい、YOASOBI、ずっと真夜中でいいのに。、久石讓、藤井風、Vaundy、tuki.）的演出場次和售票時程。
- 檔案：
  - `page.html`：頁面本體，資料都在最下面的 `<script>` 裡
  - `head.html`：HTML 外框（doctype、viewport）
  - `index.html`：網站實際送出的檔案，等於 `cat head.html page.html`
  - `automation/update-prompt.md`：自動更新時每次要執行的完整指令

## 目標

讓這台 Mac 每天台灣時間 **9:00、11:30、17:30、20:30** 自動執行 `automation/update-prompt.md`：核對各歌手官方公告，有變更就更新 `page.html`、重新產生 `index.html`、commit 並推到 GitHub，網站會自動重新部署。每次跑完都通知我，第一行寫「有新消息」或「沒有新消息」。

## 重要規則

- GitHub 只能用我的個人帳號 **s9112004**。不要使用或登入任何公司帳號（例如 `stevenhsu-gama`、`@gamania.com` 的帳號）。
- 安裝軟體前先跟我確認。
- 不要改 `page.html` 的版面設計，自動更新只改資料。

## 請你做的步驟

1. **檢查工具**：確認有沒有 `git`、`gh`（GitHub CLI）、`node`。缺的話跟我確認後，用 Homebrew 安裝（`brew install gh node`；沒有 git 就先 `xcode-select --install`；沒有 Homebrew 就告訴我安裝方式）。
2. **登入 GitHub**：先跑 `gh auth status`。
   - 如果已經登入 s9112004，就跳過。
   - 如果沒登入，請我在終端機執行 `gh auth login --hostname github.com --git-protocol https --web`，並提醒我瀏覽器要用 s9112004 登入。
   - 如果登入的是其他帳號，先停下來問我。
   - 登入後執行 `gh auth setup-git`，讓 git 推送也用這個帳號。
3. **抓專案**：`git clone https://github.com/s9112004/japan-singer-web.git ~/japan-singer-web`（已經有就 `git pull`），然後把這個工作階段移到 `~/japan-singer-web` 資料夾。
4. **確認可以推送**：在 repo 裡跑 `git ls-remote origin` 確認能連線，並確認認證的帳號是 s9112004。
5. **建立排程**：用 Claude 桌面 app 的排程功能建立一個排程：
   - 名稱：【每天】日系演唱會搶票表更新
   - 時間：每天 9:00、11:30、17:30、20:30（本機時間，這台 Mac 的時區要是台灣時間）
   - 指令：把 `automation/update-prompt.md` 的完整內容放進排程的 prompt，並在最前面加一句「專案資料夾在 ~/japan-singer-web，請先 cd 到那裡。」
   - 每次跑完都通知我
6. **測試**：手動執行一次排程，確認：
   - 有正常讀官方網站資料
   - `node --check` 沒有錯誤
   - 如果有變更，GitHub 上出現一個「每日更新 …」的 commit，幾分鐘後網站內容跟著更新
7. **提醒我 Mac 的設定**：排程只在 Claude app 開著、Mac 沒睡眠時才會執行。請告訴我怎麼設定不要自動睡眠、讓 Claude app 開機自動啟動。

## 完成後請回報

- 裝了哪些工具、GitHub 登入的帳號名稱
- 排程的名稱、時間、下次執行時間
- 測試執行的結果（有沒有推送、網站有沒有更新）
- 還需要我做的事

更詳細的人工設定步驟在 `automation/SETUP-MAC.md`，可以參考。
