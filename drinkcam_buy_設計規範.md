# guestars.app/drinkcam/buy 購買頁設計規範
> 沿用 guestars.tw 官網「斌先生工作室 · Studio」設計系統（米色暖橘 × 文青職人）。
> 全部內嵌、不外連，確保付款頁不依賴其他站台。

---

## 1. 字體（Google Fonts，放 `<head>`）

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Noto+Serif+TC:wght@500;600;700&family=Noto+Sans+TC:wght@400;500;600&family=Playfair+Display:ital,wght@0,500;0,700;1,500&family=Inter:wght@400;500;600&display=swap" rel="stylesheet">
```

---

## 2. Design Tokens（`:root`，直接內嵌）

```css
:root {
  /* 色票 */
  --bg: #F7F3ED;            /* 頁面背景（米色） */
  --surface: #FDFBF7;       /* 卡片/表單背景（暖白） */
  --text-primary: #2C1F17;  /* 主文字（深棕） */
  --text-secondary: #6D5B4E;/* 次文字 */
  --text-muted: #9B8A7C;    /* 弱化文字 */
  --accent: #C25E3F;        /* 主色（暖橘）— 主按鈕、重點、連結 */
  --accent-hover: #A94A2E;  /* 主色 hover */
  --accent-soft: #F5D9CE;   /* 主色淺（focus 光暈） */
  --border: #E5DDD1;        /* 邊框（淺） */
  --border-strong: #C9BEAE; /* 邊框（強，輸入框用） */
  --success: #6B8B4D;       /* 成功綠 */

  /* GUESTARS 品牌點綴色（子頁可選用） */
  --gs-navy: #1C1725; --gs-coral: #C4819A; --gs-gold: #C9A87C; --gs-cream: #F5F0EB;

  /* 陰影 */
  --shadow-soft: 0 2px 12px rgba(44,31,23,.06);
  --shadow-med:  0 4px 24px rgba(44,31,23,.08);
  --shadow-strong: 0 8px 32px rgba(44,31,23,.12);

  /* 字體 */
  --font-serif: "Noto Serif TC","Playfair Display","EB Garamond",Georgia,serif;
  --font-sans:  "Noto Sans TC","Inter",-apple-system,BlinkMacSystemFont,"Segoe UI",sans-serif;
  --font-mono:  "JetBrains Mono","Fira Code",ui-monospace,monospace;

  /* 圓角 */
  --radius-sm: 8px; --radius: 16px; --radius-lg: 24px;
}
```

**字級**：標題用 `--font-serif`、font-weight 500、`letter-spacing:-0.01em`；內文用 `--font-sans`、`line-height:1.7`。
- h1 `clamp(2.25rem,5vw,3.75rem)`｜h2 `clamp(1.75rem,3.5vw,2.75rem)`｜h3 `1.5rem`｜h4 `1.25rem`
- 內文 1rem、`.lead` 1.125rem（次文字色）

---

## 3. 元件樣式

```css
body { font-family: var(--font-sans); background: var(--bg); color: var(--text-primary);
       line-height: 1.7; -webkit-font-smoothing: antialiased; }
h1,h2,h3,h4,h5 { font-family: var(--font-serif); font-weight: 500; line-height: 1.25;
       color: var(--text-primary); letter-spacing: -0.01em; }

/* 按鈕 */
.btn { display:inline-flex; align-items:center; justify-content:center; gap:6px;
  padding:14px 28px; border-radius:var(--radius-sm); text-decoration:none; font-weight:500;
  transition:all .2s; font-size:.95rem; cursor:pointer; border:1px solid transparent;
  font-family:var(--font-sans); white-space:nowrap; }
.btn-primary { background:var(--accent); color:var(--surface); }
.btn-primary:hover { background:var(--accent-hover); transform:translateY(-1px); }
.btn-ghost { background:transparent; color:var(--text-primary); border-color:var(--border-strong); }
.btn-ghost:hover { border-color:var(--accent); color:var(--accent); }
.btn-lg { padding:16px 32px; font-size:1rem; }

/* 卡片（方案卡可沿用這組質感：暖白底、淺邊、軟陰影、hover 上浮） */
.card { background:var(--surface); padding:40px 32px; border-radius:var(--radius);
  border:1px solid var(--border); box-shadow:var(--shadow-soft);
  transition:transform .25s, box-shadow .25s; }
.card:hover { transform:translateY(-3px); box-shadow:var(--shadow-med); }
.price { font-family:var(--font-serif); font-size:1.2rem; color:var(--text-primary); }

/* 輸入框（結帳表單用） */
.form-row { margin-bottom:24px; }
.form-row label { display:block; margin-bottom:8px; font-size:.9rem;
  color:var(--text-secondary); font-weight:500; }
.form-row input, .form-row select, .form-row textarea {
  width:100%; padding:12px 16px; border:1px solid var(--border-strong);
  border-radius:var(--radius-sm); background:var(--surface); font-family:var(--font-sans);
  font-size:1rem; color:var(--text-primary); transition:border-color .2s, box-shadow .2s; }
.form-row input:focus, .form-row select:focus, .form-row textarea:focus {
  outline:none; border-color:var(--accent); box-shadow:0 0 0 3px var(--accent-soft); }

/* 標籤/eyebrow */
.eyebrow { display:inline-block; font-size:.8rem; font-weight:500; letter-spacing:.15em;
  text-transform:uppercase; color:var(--accent); }
```

---

## 4. Header / Footer（HTML + CSS）

> 購買頁在 guestars.app，nav 連結請用 **guestars.tw 的絕對網址**指回官網。

```html
<header class="site-header">
  <nav>
    <a href="https://guestars.tw/" class="brand">
      <span class="brand-name">斌先生工作室</span>
      <span class="brand-sub">· Studio</span>
    </a>
    <div class="nav-links">
      <a href="https://guestars.tw/guestars">GUESTARS</a>
      <a href="https://guestars.tw/drinkcam">drinkcam</a>
      <a href="https://guestars.tw/virtualchar">虛擬對話</a>
    </div>
  </nav>
</header>

<footer class="site-footer">
  <div class="footer-grid">
    <div>
      <p class="brand-name">斌先生工作室 · Studio</p>
      <p class="tagline">用 AI 做有溫度的現場體驗</p>
    </div>
    <div>
      <p class="footer-heading">法務與聯絡</p>
      <a href="https://guestars.tw/privacy">隱私權政策</a>
      <a href="https://guestars.tw/terms">服務條款</a>
      <address class="contact-info">斌先生創意工作室<br>統編 61426073<br>
        桃園市桃園區介壽路 495 巷 67 號 14 樓<br>Tel 0909-080-233<br>
        Email wubskimo0424@gmail.com</address>
    </div>
  </div>
  <p class="footer-bottom">© 2026 斌先生創意工作室. All rights reserved.</p>
</footer>
```

```css
.site-header { background:rgba(247,243,237,.85); backdrop-filter:saturate(180%) blur(12px);
  border-bottom:1px solid var(--border); position:sticky; top:0; z-index:100; }
.site-header nav { display:flex; justify-content:space-between; align-items:center;
  padding:18px 24px; max-width:1200px; margin:0 auto; }
.brand { text-decoration:none; color:var(--text-primary); display:inline-flex; align-items:baseline; gap:6px; }
.brand-name { font-family:var(--font-serif); font-size:1.15rem; font-weight:600; letter-spacing:.02em; }
.brand-sub { font-family:var(--font-serif); font-style:italic; font-size:.85rem; color:var(--text-secondary); }
.nav-links { display:flex; gap:32px; align-items:center; }
.nav-links a { color:var(--text-primary); text-decoration:none; font-size:.95rem; transition:color .2s; }
.nav-links a:hover { color:var(--accent); }

.site-footer { background:var(--surface); border-top:1px solid var(--border);
  padding:72px 24px 32px; margin-top:96px; }
.footer-grid { display:grid; grid-template-columns:1.5fr 1fr; gap:48px;
  max-width:1200px; margin:0 auto 48px; }
.footer-heading { font-family:var(--font-serif); font-weight:500; font-size:1rem; margin-bottom:18px; }
.site-footer a { color:var(--text-secondary); text-decoration:none; display:block;
  margin-bottom:10px; font-size:.9rem; }
.site-footer a:hover { color:var(--accent); }
.contact-info { color:var(--text-secondary); font-size:.85rem; margin-top:16px; line-height:1.8; font-style:normal; }
.footer-bottom { text-align:center; color:var(--text-muted); font-size:.85rem; max-width:1200px; margin:0 auto; }
@media (max-width:640px){ .footer-grid{ grid-template-columns:1fr; gap:32px; } }
```

---

## 5. Logo

官網目前的 logo 是**文字型 wordmark**（沒有圖檔）：「斌先生工作室 · Studio」，用上方 `.brand` 的 HTML/CSS 即可（serif 字體）。若之後需要圖檔 logo 再另外提供。

---

## 6. 官網按鈕已接好的方案參數

guestars.tw/drinkcam 三個方案按鈕已連到購買頁，並帶參數，請 buy 頁對應預選、並對到後端 plans.js：

| 方案 | 連結 |
|---|---|
| 單日 · 基本版（NT$1,800/日） | `guestars.app/drinkcam/buy?plan=day` |
| 年費 · 基本訂閱（NT$18,000/年） | `guestars.app/drinkcam/buy?plan=year-basic` |
| 年費 · 進階訂閱（NT$36,000/年） | `guestars.app/drinkcam/buy?plan=year-pro` |
