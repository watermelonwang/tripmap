# 旅圖・共用地點地圖

一張可以多人一起編輯的旅行地圖：OpenStreetMap 底圖（放大後有街道與店家名稱）、自訂清單、自訂顏色分類、Google 登入、只有受邀成員能使用。

資料存放在你自己的 Firebase 專案（免費方案即可），網頁放在 GitHub Pages。

整個設定大約 15 分鐘。

---

## 第 1 步：建立 Firebase 專案

1. 打開 <https://console.firebase.google.com>，用你的 Google 帳號登入。
2. 按「建立專案」（或「新增專案」），取個名字，例如 `trip-map`。Google Analytics 可以關掉。
3. 進到專案首頁後，按 `</>`（網頁）圖示新增一個網頁應用程式，名稱隨意，**不用**勾選 Firebase Hosting。
4. 畫面會出現一段 `const firebaseConfig = { ... }`，先把它複製起來，第 4 步會用到。

## 第 2 步：開啟 Google 登入

1. 左側選單「建構」→「Authentication」→「開始使用」。
2. 「登入方式」分頁 →「Google」→ 啟用 → 選一個支援 email → 儲存。
3. 「設定」分頁 →「授權網域」→「新增網域」，加入 `你的GitHub帳號.github.io`（不用加 https:// 和後面的路徑）。

## 第 3 步：建立 Firestore 資料庫並設定權限

1. 左側選單「建構」→「Firestore Database」→「建立資料庫」。
2. 位置建議選 `asia-east1`（台灣），之後不能改。
3. 選「以正式版模式啟動」（production mode）。
4. 建好之後，到「規則」分頁，把裡面的內容全部換成本專案的 `firestore.rules`。
5. **把規則第 6 行的 `you@gmail.com` 改成你自己的 Gmail（小寫）**，然後按「發布」。

## 第 4 步：修改 index.html

用任何文字編輯器打開 `index.html`，找到最上方的設定區（搜尋 `FIREBASE_CONFIG`）：

```js
const FIREBASE_CONFIG = {
  apiKey: "貼上你的 apiKey",
  ...
};
const OWNER_EMAIL = "you@gmail.com";
```

1. 把 `FIREBASE_CONFIG` 換成第 1 步複製的內容（大括號裡的部分）。
2. 把 `OWNER_EMAIL` 改成你的 Gmail，要和 `firestore.rules` 裡的一樣。

> `apiKey` 放在公開網頁上是正常的，它只用來識別專案，不是密碼。真正保護資料的是第 3 步的規則。

## 第 5 步：放到 GitHub Pages

1. 在 GitHub 建立一個新的 repository，例如 `trip-map`（Public 即可；免費帳號的 Pages 需要 Public）。
2. 上傳 `index.html`（`firestore.rules` 和 `README.md` 也可以一起放）。
3. repository 的「Settings」→「Pages」→ Source 選「Deploy from a branch」，Branch 選 `main`、資料夾 `/ (root)` → Save。
4. 等一兩分鐘，網址會是 `https://你的帳號.github.io/trip-map/`。

## 第 6 步：第一次使用與邀請朋友

1. 打開網址，用**擁有者的 Google 帳號**登入。系統會自動建立預設分類（住宿、賣場、餐廳、扭蛋、景點）和「我的清單」。
2. 到「成員」分頁，輸入朋友的 Gmail，按「加入成員」。
3. 把網址傳給朋友，對方用那個 Gmail 登入就能一起新增和編輯。不在名單上的人登入也看不到任何資料。

---

## 使用方式

- **新增地點**：按「新增地點」，在「位置」欄搜尋店名或地址；或在地圖上按右鍵（手機長按），會自動帶入附近的地名。
- **貼座標**：可以貼「35.6987, 139.7714」或完整的 Google 地圖網址（短網址 `maps.app.goo.gl` 讀不到座標）。
- **地點名稱標籤**：放大到街區等級（縮放 15 以上）時，地圖標籤會同時顯示描述和地點名稱。
- **分類顏色**：在「清單與分類」分頁點色塊換顏色。

## 常見問題

**登入時出現「unauthorized-domain」**：第 2 步的授權網域沒有加，或拼錯了。

**登入後顯示「還不能進入」**：該帳號不在成員名單，請擁有者到「成員」分頁加入。擁有者自己遇到的話，請確認 `index.html`、`firestore.rules` 兩邊的 email 一樣且是小寫。

**想在自己電腦上先測試**：直接雙擊打開 HTML 檔無法登入。請在檔案所在資料夾執行 `python3 -m http.server 8000`，再打開 `http://localhost:8000`（`localhost` 預設就是授權網域）。

**費用**：Firebase 免費方案（Spark）每天有數萬次讀寫額度，一群朋友規劃旅行用不完，也不需要綁信用卡。詳細額度以 Firebase 官網為準。

**地圖與搜尋服務**：底圖來自 OpenStreetMap，搜尋使用 OpenStreetMap 的 Nominatim。兩者都是免費的公共服務，適合小量使用；店名通常以當地語言顯示（例如在日本是日文）。
