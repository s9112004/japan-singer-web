# 在家裡的 Mac 設定自動更新

網站由 GitHub Pages 提供，Mac 只負責定時查資料、更新 `page.html` 並推上 GitHub。推上去後約 1 分鐘，網站就會自動更新。

## 1. 安裝工具（只要做一次）

打開「終端機」：

```bash
xcode-select --install
```

裝 Homebrew（已經有就跳過，指令見 https://brew.sh ），然後：

```bash
brew install gh node
```

再到 https://claude.ai/download 下載安裝 Claude 桌面 app，用你的 Claude 帳號登入。

## 2. 用個人 GitHub 帳號登入

```bash
gh auth login --hostname github.com --git-protocol https --web
```

瀏覽器要登入 **s9112004**。完成後讓 git 也用這個帳號：

```bash
gh auth setup-git
```

確認：

```bash
gh auth status
```

畫面要顯示 `Logged in to github.com account s9112004`。

## 3. 把專案抓下來

```bash
git clone https://github.com/s9112004/japan-singer-web.git ~/japan-singer-web
```

## 4. 在 Claude app 建立排程

1. 打開 Claude 桌面 app，切到 **Code**，資料夾選 `~/japan-singer-web`。
2. 輸入：

   > 請照 automation/update-prompt.md 的內容建立一個排程，每天台灣時間 9:00、11:30、17:30、20:30 執行，每次跑完都通知我。

3. 建好後，到側邊欄「Scheduled」找到這個排程，按一次「Run now」測試。看到 GitHub 上多一個「每日更新」的 commit，就代表設定成功。

## 5. 讓 Mac 保持可以執行

- 排程只在 Claude app 開著、Mac 沒有睡眠時執行。錯過的時段會在下次打開 app 時補跑。
- 建議到「系統設定 → 電池（或能源）」把「顯示器關閉時防止自動睡眠」打開，並讓 Claude app 登入時自動開啟。

## 時段的理由

日本官方公告常在日本時間 12:00、18:00、21:00 發布，換成台灣時間是 11:00、17:00、20:00，所以排程設在這之後的 11:30、17:30、20:30，加上早上 9:00 的例行檢查。
