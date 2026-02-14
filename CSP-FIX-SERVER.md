# Content-Security-Policy 警告說明

瀏覽器出現的 CSP 警告例如：

- `Content-Security-Policy: Ignoring "'report-sample'" within script-src: 'strict-dynamic' specified`
- `Ignoring "'unsafe-inline'" within script-src: 'strict-dynamic' specified`
- 等

表示目前送出的 CSP 在 `script-src` 裡同時使用了 `'strict-dynamic'` 與其他會被忽略的項目（如 `'report-sample'`、`'unsafe-inline'`、`https:`、`http:`）。

---

## 能透過改網頁內容（例如 meta 標籤）修掉嗎？

**不行。** CSP 有兩種來源：

1. **HTTP 回應標頭** — 由伺服器（例如 GitHub Pages）送出，**網頁無法修改或覆蓋**。
2. **`<meta http-equiv="Content-Security-Policy" content="...">`** — 可在 HTML 裡設定。

若伺服器已經送了會觸發警告的 CSP 標頭，在頁面裡再加 meta 只會**多一層政策**，瀏覽器會把兩者一起套用（取較嚴的那邊），**無法刪除或取代**伺服器送出的那條。因此無法靠改網頁內容來消除這類警告，只能從伺服器端調整標頭（或接受警告、不影響功能）。  
整條會觸發警告的 CSP（包含 `strict-dynamic`、`report-sample`、`unsafe-inline`、`https:`、`http:` 等）都是**伺服器在 HTTP 標頭裡送的**，本專案沒有在網頁裡設定 CSP，所以我們無法從網頁端拿掉或修改其中任何一項；只有能改伺服器設定時（例如自管主機），才可以改或移除該政策。

---

## 若網站放在 GitHub Pages (github.io)

**GitHub Pages 無法自訂 HTTP 標頭**，因此無法從我們這邊修改 Content-Security-Policy。  
這些警告來自 **GitHub 或瀏覽器的預設行為**，屬於告知性質，**不影響頁面正常顯示或功能**，可以放心忽略。

---

## 若日後改放在自管主機（例如 atva2019.iis.sinica.edu.tw）

屆時若想消除同樣的警告，再在 **伺服器**（Apache / Nginx / 主機控制台）的 Content-Security-Policy 設定中，從 `script-src` 裡**刪除**會被忽略的項目，只保留需要的來源（例如 `'strict-dynamic'`、`'self'` 或必要的 nonce/hash），不要與 `'strict-dynamic'` 或 nonce/hash 同時寫入 `'unsafe-inline'`、`'report-sample'`、`https:`、`http:`。

本專案為靜態 HTML，未在頁面內設定 CSP；若主機有預設 CSP，需在伺服器設定或 .htaccess 中調整。
