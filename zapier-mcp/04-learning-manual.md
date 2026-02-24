# Vercel 學習手冊：快速上手與關鍵技能培養

**作者：Joker**

## 前言

本手冊專為希望快速掌握 Vercel 核心功能的開發者設計。我們將摒棄繁雜的理論，透過一系列清晰的學習目標和動手實踐，讓您在最短的時間內，從部署第一個專案到掌握關鍵的開發與優化技能。

---

## 模組一：5 分鐘內完成您的首次部署

**學習目標：**

*   理解 Vercel 以 Git 為中心的自動化部署流程。
*   成功將一個現有的專案部署到 Vercel 的全球網路上。

**核心概念：**

*   **Git 即部署 (Git as a single source of truth):** 您在 Git 儲存庫中的程式碼是部署的唯一來源。Vercel 會監聽您的 `git push` 指令，並自動化後續所有流程。
*   **零配置 (Zero-configuration):** 對於主流的前端框架，Vercel 能自動偵測並套用最佳的建置與部署設定，您無需手動配置。

**實踐練習：**

1.  **準備專案：** 前往 [Next.js 官方入門模板](https://github.com/vercel/next.js/tree/canary/examples/hello-world) 並點擊「Use this template」來建立一個您自己的副本。
2.  **登入 Vercel：** 使用您的 GitHub 帳號登入 [Vercel](https://vercel.com/)。
3.  **匯入專案：** 在 Vercel 儀表板中，點擊「Add New...」>「Project」，選擇您剛剛建立的 `hello-world` 儲存庫，然後點擊「Import」。
4.  **部署：** Vercel 會自動識別這是一個 Next.js 專案。直接點擊「Deploy」按鈕。
5.  **驗收成果：** 幾分鐘後，您會看到慶祝的動畫！點擊「Visit」按鈕，即可看到您成功上線的網站。現在，試著修改您 GitHub 儲存庫中的程式碼並推送 (push)，觀察 Vercel 如何自動為您部署新版本。

---

## 模組二：掌握核心開發工作流

**學習目標：**

*   為您的專案綁定自訂的網域名稱。
*   學會安全地管理專案的環境變數。
*   區分並善用「正式 (Production)」與「預覽 (Preview)」部署環境。

**核心概念：**

*   **環境變數 (Environment Variables):** 用於儲存 API 金鑰、資料庫密碼等敏感或依環境而變的資訊。Vercel 讓您能為不同環境（正式、預覽、本地開發）設定不同的值。
*   **預覽部署 (Preview Deployments):** 每當您推送到一個非預設的分支或建立一個 Pull Request 時，Vercel 都會產生一個獨立的預覽部署。這讓您和團隊能在合併程式碼前，在真實的線上環境中審核變更。

**實踐練習：**

1.  **綁定網域：** 在專案的「Settings」>「Domains」分頁，輸入您想綁定的網域，並依照 Vercel 的指引更新您網域註冊商的 DNS 設定。
2.  **設定環境變數：** 在「Settings」>「Environment Variables」分頁，新增一個名為 `GREETING` 的變數，值設定為 `Hello from Vercel! `。
3.  **在程式碼中使用變數：** 修改您的 Next.js 頁面，讀取並顯示這個環境變數。
    ```jsx
    // pages/index.js
    export default function HomePage() {
      return <h1>{process.env.NEXT_PUBLIC_GREETING || "Hello World"}</h1>;
    }
    // 注意：在 Next.js 中，需要在變數名稱前加上 NEXT_PUBLIC_ 才能在前端讀取。
    ```
4.  **建立預覽部署：** 建立一個新的 Git 分支 (e.g., `feature/greeting`)，修改 `GREETING` 環境變數的值為 `Hello from a Preview! `，然後將分支推送到 GitHub。觀察 Vercel 如何為這個新分支建立一個獨立的預覽 URL。

---

## 模組三：釋放後端能力 (Serverless Functions)

**學習目標：**

*   在您的前端專案中，建立並部署一個簡單的後端 API 端點。

**核心概念：**

*   **Serverless Functions:** 一種無需管理伺服器的後端執行模式。您只需撰寫您的 API 邏輯，Vercel 會負責其執行、擴展與維護。

**實踐練習：**

1.  **建立 API 檔案：** 在您的專案根目錄下，建立一個 `api` 資料夾。在此資料夾中，建立一個名為 `time.js` 的檔案。
2.  **撰寫 API 邏輯：** 在 `api/time.js` 中，貼上以下程式碼。這段程式碼會回傳目前的伺服器時間。
    ```javascript
    export default function handler(req, res) {
      const now = new Date();
      res.status(200).json({ currentTime: now.toISOString() });
    }
    ```
3.  **部署與測試：** 將您的變更 `git push`。部署完成後，在您的網站 URL 後面加上 `/api/time` (例如 `https://your-site.vercel.app/api/time`)，您應該會看到一個包含目前時間的 JSON 回應。

---

## 模組四：網站分析與邊緣優化

**學習目標：**

*   啟用 Vercel 內建的網站分析功能。
*   了解 Edge Functions 的基本概念與用途。

**核心概念：**

*   **Vercel Web Analytics:** 一個注重使用者隱私的輕量級網站分析工具，幫助您了解流量來源與使用者行為，且不會拖慢網站速度。
*   **Edge Functions:** 在 Vercel 全球邊緣網路上執行的程式碼，因為極度靠近使用者，所以反應速度飛快。適合用於需要極低延遲的場景，如身分驗證、A/B 測試等。

**實踐練習：**

1.  **啟用分析：** 在 Vercel 儀表板的「Analytics」分頁，為您的專案啟用 Web Analytics。
2.  **建立 Middleware：** 在專案根目錄下，建立一個 `middleware.js` 檔案。貼上以下程式碼。這段程式碼是一個 Edge Function，它會在所有請求送達您的應用程式前執行，並在回應中加入一個自訂的 HTTP 標頭。
    ```javascript
    import { NextResponse } from 'next/server';

    export function middleware(request) {
      const response = NextResponse.next();
      response.headers.set('x-hello-from-edge', 'Hello from the Edge!');
      return response;
    }
    ```
3.  **部署與驗證：** 部署您的變更。完成後，使用瀏覽器的開發者工具檢查您網站的網路請求，您會在回應標頭 (Response Headers) 中找到 `x-hello-from-edge` 這個欄位。

## 結語

恭喜您完成了 Vercel 的快速上手學習！您現在已經掌握了從部署、管理到優化的核心技能。Vercel 的世界遠不止於此，您可以繼續探索 [Vercel AI SDK](https://sdk.vercel.ai/)、[Edge Middleware](https://vercel.com/docs/functions/edge-middleware) 等更進階的主題，不斷提升您的開發效率與網站效能。
