# Ho Schedule

> **摘要**：Ho Schedule 是一個以 LINE MINI App 為主要使用情境的行程與排班系統，整合 Google Calendar、Firebase 與 HoHo 訂單同步流程，支援多人指派、衝突檢查與通知能力。

## 目錄

- [功能概覽](#功能概覽)
- [技術架構](#技術架構)
- [專案結構](#專案結構)
- [環境需求](#環境需求)
- [快速開始](#快速開始)
- [環境變數設定](#環境變數設定)
- [可用指令](#可用指令)
- [部署說明](#部署說明)
- [常見問題](#常見問題)
- [參考文件](#參考文件)

## 功能概覽

- LINE LIFF 登入與身份綁定（含 UBN 驗證流程）。
- 行程建立、多人指派、管理員切換與統計檢視。
- Google Calendar 匯入/雙向同步（依使用者設定同步方向）。
- HoHo 訂單背景同步與指派流程。
- LINE Service Message / Push 通知（依頻道權限與使用情境切換）。

## 技術架構

- 前端：React + TypeScript + Vite
- 後端：Express（同專案 `server.ts`）
- 資料層：Firebase（Firestore + Firebase Admin SDK）
- 外部服務：
  - LINE LIFF / LINE Login / Messaging API
  - Google OAuth + Google Calendar API
  - Google Maps Geocoding / Distance Matrix API

## 專案結構

```text
ho-schedule/
├─ src/                    # 前端應用程式
├─ landing/                # 靜態落地頁與隱私頁
│  ├─ index.html
│  └─ privacy.html
├─ server.ts               # Express API 與排程背景任務
├─ firebase.json
├─ firestore.rules
├─ firestore.indexes.json
├─ firebase-applet-config.json
└─ package.json
```

## 環境需求

- Node.js 18 以上（建議 LTS）
- npm 9 以上
- 可用的 Firebase 專案與 Firestore
- LINE Developers Channel（LIFF / Login / Messaging）
- Google Cloud 專案（OAuth、Calendar、Maps API）

## 快速開始

1. 安裝相依套件：

   ```bash
   npm install
   ```

2. 建立環境變數檔：

   ```bash
   copy .env.example .env.local
   ```

3. 依下方章節補齊 `.env.local` 必要變數。

4. 啟動開發伺服器：

   ```bash
   npm run dev
   ```

5. 開啟瀏覽器：

   - `http://localhost:3000`

## 環境變數設定

以下為常用變數，完整欄位請參考 `.env.example`：

- `GOOGLE_CLIENT_ID`：Google OAuth Client ID
- `GOOGLE_CLIENT_SECRET`：Google OAuth Client Secret
- `GOOGLE_REDIRECT_URI`：Google OAuth Callback URL（例如 `https://your-domain/auth/callback`）
- `GOOGLE_MAPS_API_KEY`：Google Maps API Key（地理編碼與距離計算）
- `SESSION_SECRET`：Express Session 簽章金鑰
- `VITE_LIFF_ID`：LINE LIFF ID
- `LIFF_URL`：LINE MINI App URL
- `LINE_CHANNEL_ID`：LINE Channel ID
- `LINE_CHANNEL_SECRET`：LINE Channel Secret
- `FIREBASE_SERVICE_ACCOUNT`：Firebase Admin 服務帳號 JSON 字串（選填，建議正式環境提供）

## 可用指令

- `npm run dev`：啟動本地開發（`tsx server.ts`）
- `npm run build`：建置前端輸出到 `dist`
- `npm run preview`：預覽建置結果
- `npm run lint`：TypeScript 型別檢查（`tsc --noEmit`）

## 部署說明

1. 先執行建置：

   ```bash
   npm run build
   ```

2. 正式環境以 `NODE_ENV=production` 啟動 `server.ts`，Express 會改為提供 `dist` 靜態檔案。
3. 確認部署平台已注入所有必要環境變數（尤其是 Google、LINE、Firebase）。
4. 若使用 Firebase，請同步部署規則與索引設定：

   ```bash
   firebase deploy --only firestore
   ```

## 常見問題

### 1) LINE 通知發送失敗

- 先確認 `LINE_CHANNEL_ID`、`LINE_CHANNEL_SECRET` 是否正確。
- 若回傳權限不足，需在 LINE Developers 後台確認 Messaging API / Service Message 權限是否開通。

### 2) Google Calendar 無法同步

- 確認 OAuth callback URL 與 `GOOGLE_REDIRECT_URI` 完全一致。
- 檢查使用者是否已完成 Google 綁定流程，且 `googleTokens` 文件存在。

### 3) Firestore 權限或讀寫失敗

- 確認 `firebase-applet-config.json` 存在且 `projectId` 正確。
- 若本機無 Application Default Credentials，請提供 `FIREBASE_SERVICE_ACCOUNT`。

## 參考文件

- 系統設計規範：`DESIGN.md`
- Firebase 設定：`firebase.json`、`firestore.rules`、`firestore.indexes.json`
- 落地頁：`landing/index.html`
