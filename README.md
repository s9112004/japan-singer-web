# 日系演唱會搶票表

整理日本歌手的演出場次和售票時程，包含倒數、日本／台灣時間對照、售票頁連結，每位歌手都有自己的頁面。

目前收錄：米津玄師、King Gnu、星街すいせい、YOASOBI、ずっと真夜中でいいのに。、久石讓、藤井風、Vaundy、tuki.

## 怎麼看

- 網站：https://s9112004.github.io/japan-singer-web/
- 本機：用瀏覽器直接打開 `index.html`，不需要安裝任何東西，也沒有後端。

## 檔案

| 檔案 | 用途 |
|---|---|
| `page.html` | 頁面本體（版面、樣式、資料、程式），也是 claude.ai Artifact 版本的原始檔 |
| `head.html` | HTML 外框（doctype、手機版 viewport） |
| `index.html` | 網站實際送出的檔案，等於 `head.html` + `page.html`，不要直接改它 |

改完 `page.html` 後重新產生網站檔：

```bash
cat head.html page.html > index.html
```

## 自動更新

家裡 Mac 上的 Claude 桌面 app 排程，每天台灣時間 9:00、11:30、17:30、20:30 會核對各歌手官方公告。有變更就更新 `page.html`、重新產生 `index.html`，commit 並推上 GitHub，GitHub Pages 會自動重新部署。

- 設定步驟：[automation/SETUP-MAC.md](automation/SETUP-MAC.md)
- 排程指令：[automation/update-prompt.md](automation/update-prompt.md)

- 首頁：歌手海報、下一個要搶的票（倒數）、近五週行事曆、搶票清單、接下來的演出
- 點歌手海報進入個人頁（網址會變成 `index.html#yonezu` 這種形式，可以收藏）
- 搶票清單裡，同一個巡演的售票合併成一張卡；每張卡有「演出日」和「前往售票頁」按鈕

## 資料放在哪裡

全部資料都寫在 `index.html` 最下面的 `<script>` 裡：

| 變數 | 內容 |
|---|---|
| `ARTISTS` | 歌手名稱、代表色、官方網站 |
| `PHASES` | 每一筆售票階段：開始、截止、結果公布時間、備註、售票頁 |
| `TOURS` | 每個巡演的所有場次（日期、城市、會場） |
| `LINK` | 售票階段對應到哪些場次 |
| `TICKET`、`PHASE_URL` | 售票頁連結 |
| `SOURCES` | 每位歌手的資料來源 |

時間一律寫成帶時區的格式：日本 `2026-11-17T20:00+09:00`，台灣 `2026-12-26T18:00+08:00`。

`PHASES` 常用欄位：

- `kind`：`range`（有受付期間）、`point`（單一時間點）、`tba`（尚未公布）
- `unverified: true`：只有第三方來源，頁面會標「待確認」
- `cancelled: true`：確定不舉辦
- `tid` / `city` / `dates`：指定這筆售票對應哪個巡演、哪些城市或日期

## 注意

資料整理自官方網站、官方 FC、新聞與售票整理網站，申請前請到官方網站或售票平台再確認一次。
