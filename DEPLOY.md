# 部署指南 — 三選一

> 目標：**5-15 分鐘內讓 https://某某網址 打得開**，然後就能送綠界特約審核。
> 三條路都試過、都能用，選你手邊工具最熟悉的即可。

---

## 🚀 選項 A：Netlify Drop（最快 · 2 分鐘 · 不用 CLI）

**適合誰：只想趕快上線試試看、還沒選好長期主機的你。**

1. 開瀏覽器打 **https://app.netlify.com/drop**
2. 把整個 `binsir_studio_website/` 資料夾 **拖進網頁的框框裡**
3. 等 30-60 秒，Netlify 會給你一個網址類似 `https://serene-heliotrope-abc123.netlify.app`
4. 打開檢查看看，確認頁面 OK
5. 想改網址名字或綁自己的域名（例如把 `guestars.tw` 指過來），註冊免費 Netlify 帳號 → Site settings 裡改

**優點：** 零設定、零 CLI、零登入即可測試
**缺點：** 免費網址是 `*.netlify.app`，要換成自己的域名還是要註冊

---

## 🎯 選項 B：Vercel CLI（**推薦，跟你現有工作流一致**）

**適合誰：已經用 Vercel 部署 `mryes-wedding-game`、想維持同一套架構的你。**

打開 Terminal，依序執行：

```bash
# 1. 進入網站資料夾
cd /path/to/binsir_studio_website

# 2. 部署（第一次會問你要建新 project，選 Yes）
npx vercel --prod
```

第一次會問你這些，照直覺選就好：

- **Set up and deploy?** → `Y`
- **Which scope?** → 選你的 Vercel 帳號
- **Link to existing project?** → **N**（重要，這是新專案，別跟 `mryes-wedding-game` 混）
- **What's your project's name?** → `binsir-studio-site`（或你想要的名字）
- **In which directory is your code located?** → `./`
- **Want to override the settings?** → `N`

跑完會給你一個網址 `https://binsir-studio-site-xxx.vercel.app`。之後每次要更新，在同一個資料夾跑 `npx vercel --prod` 就好。

**綁定 `guestars.tw` 網域：**
1. 去 Vercel Dashboard → 你剛建的 project → Settings → Domains
2. Add domain → 輸入 `guestars.tw`
3. Vercel 會告訴你要在你的 DNS 供應商那邊加什麼 A record 或 CNAME
4. **記得先把舊 `guestars.tw` 從 `mryes-wedding-game` 專案移除**（否則 DNS 衝突）——依你的 Google Doc 規劃，舊專案改用 `mryes-wedding-game.vercel.app` 或另外的網域

**優點：** 跟你現有工作流一致、可綁自己網域、免費方案就夠用
**缺點：** 要有 Vercel 帳號、要處理 DNS 切換

---

## 🌐 選項 C：Cloudflare Pages（適合已經用 Cloudflare 管理 DNS 的你）

**適合誰：`guestars.tw` 的 DNS 本來就在 Cloudflare、想省一個服務商的你。**

1. 登入 Cloudflare Dashboard
2. 左邊選單 **Workers & Pages** → **Create** → **Pages** → **Upload assets**
3. Project name：`binsir-studio-site`
4. 上傳整個 `binsir_studio_website/` 資料夾（可直接拖）
5. Deploy 完後給你 `https://binsir-studio-site.pages.dev`
6. 綁 `guestars.tw`：Custom domains → Add domain → 已經在 Cloudflare 的網域直接一鍵綁

**優點：** DNS 和 hosting 在同一家、CDN 全球最快之一、免費額度大
**缺點：** UI 沒 Vercel 直觀

---

## ✅ 部署後的快速檢查清單

網站上線後跑一遍：

- [ ] 首頁 `/` 打開有大 hero + 三張產品卡
- [ ] 點「了解 GUESTARS →」進 `/guestars` 或 `/guestars.html` 看得到影片
- [ ] 「了解 drinkcam →」進 `/drinkcam` 看得到訊號圖與方案
- [ ] 「了解虛擬對話 →」進 `/virtualchar` 看得到 Oreo/Dan 兩個 demo 按鈕
- [ ] `/privacy` 和 `/terms` 都打得開
- [ ] 手機版點開每頁都不會破版
- [ ] 頁尾看得到「斌先生創意工作室｜統編 61426073｜地址｜電話」

**表單目前 action 會 404**（`/api/contact`、`/api/order/*`）——這是**預期的**，因為 API endpoints 還沒建。綠界審核時他們會看你網站有商品清單、價格、隱私、條款，不會實際點下單，所以先上線沒問題。API 端點跟綠界特約通過後一起接。

---

## 🔗 送綠界特約要給的資訊

網站上線後，你填綠界特約表單時：

- **網站網址**：填你新的 `https://guestars.tw`（或部署後的 vercel/netlify 網址）
- **商品項目**：GUESTARS 婚禮 AI 影片（NT$6,000-15,000）、drinkcam 多機直播（NT$1,800-36,000）、虛擬人物對話（NT$3,800-12,000）
- **公司資訊**：斌先生創意工作室、統編 61426073
- **服務條款頁**：`https://[網址]/terms`
- **隱私權政策頁**：`https://[網址]/privacy`
- **實際營業地址照片**：等商務中心那邊回覆

---

## 🆘 部署卡住了？

三個最常見的坑：

**1. Vercel 部署錯的專案**
```bash
# 在資料夾裡先確認
cat .vercel/project.json
# 沒有這檔案 = 全新專案，正常
# 有的話確認 projectName 是不是你要的
```

**2. Netlify Drop 上傳後路由不對（例如 `/guestars` 404）**
Netlify 預設沒有 clean URLs，得加一份 `_redirects` 檔（我沒放，因為只有選 Netlify 才用得到）：
```
/guestars    /guestars.html    200
/drinkcam    /drinkcam.html    200
/virtualchar /virtualchar.html 200
/privacy     /privacy.html     200
/terms       /terms.html       200
```

**3. 影片 hero 顯示不出來**
如果你的 Cloudinary CDN 有變動或被移除，Hero 影片會壞。臨時解法是註解掉 `<video>` tag，或者換成別的 Cloudinary URL。

---

**我的建議走法：**

1. 現在 → **選項 A（Netlify Drop）** 快速上線，2 分鐘拿到網址
2. 網址給綠界送特約審核（這期間 3-5 天）
3. 特約通過前 → 決定要用 Vercel 或 Cloudflare Pages 作為長期主機、切換 `guestars.tw` DNS
4. 特約通過後 → 建 API endpoints、換成正式綠界 key

這樣**綠界審核在跑的時候，你有時間慢慢挑長期主機**，兩件事並進不會卡。
