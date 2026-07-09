# 斌先生工作室 · Studio — 官網整合包

> 用途：作為母站，把 GUESTARS、drinkcam 與未來產品整合在同一個網站底下，滿足綠界 ECPay 特約會員「所有商品需在同一網站」的要求。
> 設計語言：Claude 米色暖橘 × 文青職人（低調、有溫度）。
> 建立日期：2026-07-09

---

## 檔案清單

| 檔案 | 用途 | 路由建議 |
|---|---|---|
| `index.html` | 母站首頁（工作室介紹＋產品目錄） | `/` |
| `guestars.html` | GUESTARS 產品頁（含方案、訂購、FAQ） | `/guestars` 或 `/guestars.html` |
| `drinkcam.html` | drinkcam 產品頁（含方案、訂購、FAQ） | `/drinkcam` 或 `/drinkcam.html` |
| `virtualchar.html` | 虛擬人物即時對話產品頁（含方案、訂購、FAQ） | `/virtualchar` 或 `/virtualchar.html` |
| `privacy.html` | 隱私權政策（**綠界特約必要**） | `/privacy` |
| `terms.html` | 服務條款（**綠界特約必要**） | `/terms` |
| `styles.css` | 共用樣式（Claude 設計系統） | `/styles.css` |
| `README.md` | 這份說明 | — |

---

## 快速預覽

檔案是**純靜態 HTML/CSS**，任何一台電腦都能：

```bash
# 用 Python 起一個本地伺服器
cd binsir_studio_website
python3 -m http.server 8080
# 打開 http://localhost:8080
```

或直接雙擊 `index.html` 也能開，只是路徑跳轉會用檔名（`guestars.html`）而不是乾淨路徑。

---

## 網站架構選項（重要決策）

阿斌手上原本已有豐富的產品頁（尤其 GUESTARS 花了不少 credit 生成示範影片），因此有兩種可行的架構，各有優缺點：

### Option 1：全部收在新主站（我目前產出的預設架構）

- 主站首頁 → 產品卡片 →「了解 XX」→ **本站精簡版商品頁**（`guestars.html` / `drinkcam.html` / `virtualchar.html`）
- 好處：一致的視覺、單一維護點、內容更 SEO 友善
- 代價：舊有豐富內容（示範影片、互動 demo）沒直接接上，需要重新整合過來

### Option 2：主站當 Hub，商品頁外連舊有豐富頁（推薦給有現成資產者）

- 主站首頁 → 產品卡片 →「了解 XX」→ **外連現有豐富頁**（例如舊 `guestars.tw/guestars.html`）
- 舊有豐富頁的購買按鈕改成 POST 到主站的 `/api/order/*`
- 好處：**保留現有內容資產**（示範影片不浪費）、銷售時可以直接分享商品頁跳過主站
- 代價：兩套風格並存、跨域名 CORS 需要處理

### Option 3：混合（每個產品各自選最合適）

- **GUESTARS** 已有豐富頁 → 外連舊頁（Option 2）
- **drinkcam / virtualchar** 是新產品，沒有現成豐富頁 → 用本站精簡版（Option 1）
- 這是實務上最順的走法：新舊都用，各取所長

### 共通結單邏輯（無論選哪個 Option）

任何一頁的購買表單 `action` 都指向 **同一個主站** 的 `/api/order/*` 端點，用同一組綠界 MerchantID 結單。這樣：
- 綠界特約審核只需一次
- 客戶不管從哪個頁面下單，最終走同一套流程
- 對帳、發票、客服統一

**跨域名時要處理的**：舊網域的表單 POST 到新網域，會遇到 CORS。開發 AI 要記得在主站的 API endpoint 設定 `Access-Control-Allow-Origin` 允許舊網域，或做成 form action 直接跨域 POST（form POST 不受 CORS 限制、但無法讀 response，可用 redirect URL 回傳結果）。

**推薦走 Option 3**：GUESTARS 卡片外連舊豐富頁、drinkcam 與 virtualchar 用本站精簡版。首頁維持統一 Claude 風格當品牌 hub。

---

## 部署選項（依據你的 Google Doc 技術規劃）

你目前的 Vercel 專案 `mryes-wedding-game` 已被規劃為**只跑 AI 變身系統**，官網要移出去。所以：

### 方案 A：另開一個 Vercel 專案（推薦）

1. 新建 GitHub repo，例如 `binsir-studio-site`
2. 把這個資料夾整包 commit 上去
3. Vercel → New Project → 匯入這個 repo
4. 網域設定：
   - `guestars.tw` 從舊專案移過來指向新專案（DNS 或 Vercel Domain 面板操作）
   - `guestars.app` 保留給舊專案（AI 變身系統）繼續使用，或加 301 redirect 到 `guestars.tw`
5. Deploy 完成 → 綠界特約表單填 `https://guestars.tw`

**重要**：舊專案的 `vercel.json` 保留 cron jobs 和 API routes，只刪除官網相關 rewrites。你的 Google Doc 已經寫得很清楚。

### 方案 B：同專案內共存（不推薦）

若堅持放在 `mryes-wedding-game` 專案裡，官網檔案要**避開這些 AI 系統保留路徑**：

- `/api/*`
- `/guest*`、`/guest/*`
- `/host/*`
- `/display/*`
- `/lottery/*`
- `/admin/*`
- `/download/*`
- `/tools/*`

本包所有頁面（`/`、`/guestars.html`、`/drinkcam.html`、`/privacy.html`、`/terms.html`）**都不會撞到**上述保留路徑，可安全放入。

---

## 需要你 / 你的開發 AI 補上的部分

檔案已可直接預覽，但**要真正上線並串綠界，還有幾件事要接手：**

### 1. 靜態資源
- `/assets/bin.webp` — 首頁 About 區塊的阿斌照片。你把現有 GUESTARS 站上那張照片放進來即可。
- 各產品頁的示範圖片／影片（若要更豐富）—可從舊 `guestars.tw` 搬過來，或先留白。

### 2. 表單後端
每個表單的 `action` 目前是佔位路徑：
- 首頁聯絡表：`/api/contact`
- GUESTARS 訂購表：`/api/order/guestars`
- drinkcam 訂購表：`/api/order/drinkcam`
- 虛擬對話訂購表：`/api/order/virtualchar`

**你的開發 AI 需要做的**：
- 建立這四支 API endpoint（Vercel Serverless Function）
- 接綠界 SDK：訂單建立 → 導向綠界付款頁 → 接 webhook 回來 → 自動觸發交付動作
- 交付動作範例：
  - **GUESTARS** 付款成功 → 自動寄 4 個專屬網址到新人 Email
  - **drinkcam** 付款成功 → 開通對應方案、寄送 App 下載與說明
  - **虛擬對話**付款成功 → 自動寄「角色建置問卷」連結（Typeform / Google Form），阿斌 3-7 個工作天內手動建置後寄專屬對話網址
  - 聯絡表單 → 轉寄到 `wubskimo0424@gmail.com`

**虛擬對話產品的技術參考**：這條產品線的實作骨架已經在既有 `mr-yes-chat` Vercel 專案裡驗證過，內部技術手冊為 `REALTIME_AVATAR_PLAYBOOK.md`（Runway Characters API + LiveKit WebRTC + Upstash Redis + Anthropic Haiku）。訂購流程屬於「手動客製」（非自動化），付款後只需寄問卷、後續由阿斌接手建置。

### 3. 綠界整合

```
【參考 SDK】
- Node.js:  https://github.com/simenkid/node-ecpay-aio
- Python:   https://github.com/ECPay/ECPayAIO_Python
- PHP:      https://github.com/ECPay/SDK_PHP

【流程】
1. 用 sandbox 環境（測試 MerchantID / HashKey / HashIV）先寫整合
2. 特約通過後，換 production key 上線
3. 建議同時勾「綠界電子發票代開服務」，付款成功後自動開發票

【關鍵坑】
- CheckMacValue 演算法：用官方 SDK，別自己寫
- Webhook 收到後要回覆字串 "1|OK"，否則綠界會一直重送
- OrderId 要唯一、長度限制（20 chars）
- 金額用整數（TWD 無小數）
```

### 4. Email 寄送

推薦選擇（依複雜度排序）：
- **最簡單**：Gmail App Password + Nodemailer
- **輕量商用**：Postmark、Resend、SendGrid
- **AWS 生態**：SES

寄送場景：
1. 聯絡表單 → 轉寄給你
2. GUESTARS 付款成功 → 寄 4 個網址給新人
3. drinkcam 付款成功 → 寄 App 下載連結與帳號啟用信
4. 每筆訂單 → 綠界電子發票（走綠界代發最省事）

---

## 設計系統速覽

### 主色

```
--bg:            #F7F3ED   /* 主背景，米/象牙白 */
--surface:       #FDFBF7   /* 卡片、浮起表面 */
--text-primary:  #2C1F17   /* 主文字，深咖啡 */
--text-secondary:#6D5B4E   /* 次文字 */
--accent:        #C25E3F   /* Claude 珊瑚橘 */
--accent-hover:  #A94A2E
--border:        #E5DDD1
```

### 沿用的 GUESTARS 品牌色（在需要與舊品牌延續時使用）

```
--gs-navy:  #1C1725
--gs-coral: #C4819A
--gs-gold:  #C9A87C
--gs-cream: #F5F0EB
```

### 字型

- 中文標題：`Noto Serif TC`（透過 Google Fonts）
- 中文內文：`Noto Sans TC`
- 英文標題：`Playfair Display`
- 英文內文：`Inter`

### 響應式斷點

- Desktop ≥ 900px：三欄 grid
- Tablet 600-900px：兩欄／單欄混排
- Mobile ≤ 600px：全單欄

---

## 綠界特約申請提示

以下是你送特約時可能被問／要提供的資料，這個網站已經幫你準備好：

- ✅ 商品清單（首頁產品目錄有兩個產品，未來加卡片即可）
- ✅ 定價明確（各產品頁都有 pricing table）
- ✅ 隱私權政策（`/privacy.html`）
- ✅ 服務條款（`/terms.html`）
- ✅ 公司資訊（頁尾都有：斌先生創意工作室 / 統編 61426073 / 地址 / Tel / Email）
- ⏳ 商業抄本（你已有）
- ⏳ 負責人身分證正反面（你已有）
- ⏳ 商號銀行帳戶存摺影本（去銀行開戶時搞定）
- ⏳ 門面 / 招牌 / 室內照片（跟登記地址方要中）
- ⏳ 印章（大小章已有）

---

## 交接 Prompt 範例（給你的開發 AI）

若要把這包交給另一個 AI 助手接手實作後端整合，可以貼下列 prompt：

> 我有一份 `binsir_studio_website/` 的靜態網站（index.html + guestars.html + drinkcam.html + privacy.html + terms.html + styles.css），是「斌先生工作室」的母站，要部署到 Vercel。
>
> 請幫我：
>
> 1. 建立 Vercel Serverless Functions：
>    - `POST /api/contact` — 接聯絡表單，寄信到 wubskimo0424@gmail.com
>    - `POST /api/order/guestars` — 接 GUESTARS 訂單，建立綠界訂單並導向付款頁
>    - `POST /api/order/drinkcam` — 接 drinkcam 訂單，同上
>    - `POST /api/ecpay/webhook` — 接綠界付款成功通知，自動寄交付信與觸發後續動作
>
> 2. 使用綠界 AIO Node.js SDK（github.com/simenkid/node-ecpay-aio），先寫 sandbox 版本，MerchantID/HashKey/HashIV 從環境變數讀取。
>
> 3. Email 用 Resend 或 Nodemailer + Gmail App Password 寄發。
>
> 4. 訂單資料先存 Vercel KV 或 Supabase（你選一個推薦）。
>
> 5. Webhook 收到後：
>    - GUESTARS：呼叫 `mryes-wedding-game` 專案的 `/admin/super.html` 開房間 API（或提供讓我手動觸發的後台）
>    - drinkcam：發啟用碼與 App 下載連結到 Email
>    - 兩者都觸發綠界電子發票代開
>
> AI 變身系統本體不動，只接 webhook 事件。

---

## 未來加新產品的流程

當你有新產品要上（例如 3D 列印、機器人、其他 AI 工具）：

1. 複製 `drinkcam.html` 當範本，改成 `new-product.html`
2. 首頁 `index.html` 的 `.product-grid` 加一張卡片（找 `product-placeholder` 那張改）
3. 頁尾 `.footer-products` 加一個連結
4. 若涉及新商品類別，告知綠界即可（不用重新特約）

---

## 檔案 diff / 部署 checklist

- [ ] 建立新 Vercel 專案並匯入這個 repo
- [ ] 上傳阿斌照片到 `/assets/bin.webp`
- [ ] `guestars.tw` 網域從舊專案切到新專案
- [ ] 舊專案 `vercel.json` 移除官網 rewrites，保留 cron
- [ ] 建立 `/api/*` endpoints
- [ ] 綠界 sandbox 環境測完整流程
- [ ] 商業抄本、印章、開戶完成
- [ ] 商務中心照片到齊
- [ ] 送綠界特約 + 電子發票代發
- [ ] 換 production key，正式上線

---

**這個包直接可用、可預覽、可交接。**
有任何問題或要調整（顏色、文案、多加一個產品頁）都隨時說。

— 斌先生工作室 · Studio
