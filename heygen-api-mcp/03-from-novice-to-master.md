# HeyGen API 從新手到精通指南

作者：Joker

## 前言

歡迎來到 HeyGen API 的世界！本指南將帶您從零開始，逐步掌握利用 AI 生成影片的強大能力。無論您是初次接觸 API 的新手，還是尋求進階功能的資深開發者，這裡都為您規劃了清晰的學習路徑。

## 第一階段：基礎概念與首次呼叫 (The First Step)

**學習目標：** 了解 HeyGen API 的基本運作模式，並成功發出您的第一個 API 請求。

1.  **獲取 API 金鑰：**
    *   前往 HeyGen 官方網站，註冊並登入您的帳戶。
    *   在您的帳戶設定中，找到 API 金鑰 (API Key) 的管理頁面。
    *   產生一組新的 API 金鑰並妥善保管。這是您與 API 溝通的唯一憑證。

2.  **理解核心概念：**
    *   **非同步處理 (Asynchronous Processing):** 影片生成需要時間。您發送請求後，系統會在背景處理。您需要透過另一個請求來查詢進度。
    *   **頭像 (Avatar) 與聲音 (Voice):** 這是構成影片的兩個核心元素。您可以選擇系統提供的公開頭像和聲音，或客製化您自己的。
    *   **腳本 (Script):** 您想讓頭像說的話。

3.  **您的第一個 API 呼叫 (使用 Postman 或 cURL)：**
    *   **目標：** 獲取可用的頭像列表。
    *   **方法：** 使用 `GET /v2/avatars` 端點。
    *   **實作 (cURL):**

        ```bash
        curl --request GET \
             --url https://api.heygen.com/v2/avatars \
             --header 'X-Api-Key: YOUR_API_KEY_HERE'
        ```

    *   **驗證：** 如果您看到一長串 JSON 格式的頭像列表，恭喜您，您已成功邁出第一步！

## 第二階段：核心功能實戰 (Core Functionality)

**學習目標：** 掌握生成影片的核心流程，從簡單到複雜。

1.  **快速生成您的第一支影片：**
    *   **工具：** `POST /v1/video_agent/generate`
    *   **練習：** 參考 `02-usage-report.md` 中的範例，撰寫一個簡單的兩場景腳本，並提交請求。
    *   **關鍵步驟：** 記下返回的 `video_id`。

2.  **查詢影片狀態與下載：**
    *   **工具：** `GET /v1/video_status.get`
    *   **練習：** 使用上一步取得的 `video_id`，每隔 10-15 秒查詢一次影片狀態，直到 `status` 變為 `completed`。
    *   **下載：** 從回傳的結果中找到 `video_url`，並在瀏覽器中打開以下載您的第一支 AI 影片。

3.  **精準控制影片內容：**
    *   **工具：** `POST /v2/video/generate`
    *   **練習：**
        1.  先呼叫 `GET /v2/avatars` 和 `GET /v2/voices`，挑選一個您喜歡的頭像 ID 和聲音 ID。
        2.  使用 `POST /v2/video/generate`，在 `video_inputs` 中明確指定 `avatar_id` 和 `voice_id`，並設定一個純色的背景。
        3.  重複查詢與下載的流程。

## 第三階段：進階技巧與應用 (Advanced Techniques)

**學習目標：** 探索 HeyGen API 的進階功能，以應對更複雜的應用場景。

1.  **影片客製化：**
    *   **背景：** 嘗試將背景從純色改為圖片 (image) 或影片 (video) 連結。
    *   **聲音：** 調整 `voice` 物件中的 `speed` (語速) 和 `pitch` (音高) 參數，感受聲音的變化。
    *   **腳本：** 在 `text` 中使用 SSML (Speech Synthesis Markup Language) 標籤，例如 `<break time="1s"/>` 來控制停頓。

2.  **模板化影片生成 (Template-based Generation)：**
    *   **概念：** 如果您需要大量生成結構相似但內容不同的影片（例如個人化行銷影片），使用模板會非常高效。
    *   **流程：**
        1.  在 HeyGen 網站上建立一個包含變數（例如姓名、公司名稱）的影片模板。
        2.  透過 API 傳入變數的值，快速生成個人化影片。

3.  **即時互動頭像 (Streaming Avatar)：**
    *   **應用場景：** 虛擬客服、線上直播、互動式教學。
    *   **概念：** 這是一個更複雜的功能，允許您與 AI 頭像進行即時的雙向溝通。
    *   **探索：** 仔細閱讀官方文件中的 `Streaming API` 章節，了解其運作原理和技術要求（例如 WebSocket）。

## 第四階段：實戰應用與整合 (Real-world Integration)

**學習目標：** 將 HeyGen API 整合到您自己的應用程式或工作流程中。

1.  **自動化內容流程：**
    *   **範例：** 建立一個簡單的 Python 腳本，每天自動從 RSS feed 讀取新聞標題，並使用 HeyGen API 將其轉換為每日新聞摘要影片。

2.  **與其他服務整合：**
    *   **範例：** 透過 Zapier 或類似的自動化平台，將 HeyGen 與 Google Sheets 連接。當您在 Google Sheets 的新一列表格中填入姓名和一段問候語時，自動觸發 HeyGen API 生成一支個人化的歡迎影片，並將影片連結回填到表格中。

3.  **錯誤處理與成本控制：**
    *   **健壯性：** 在您的程式碼中加入完整的錯誤處理邏輯，例如處理 API 金鑰錯誤、無效的參數、生成失敗等情況。
    *   **成本意識：** 熟悉 HeyGen 的計費方式（通常基於影片時長或生成次數）。在開發和正式上線時，監控您的 API 使用量，避免超出預算。

恭喜您！完成這條學習路徑後，您已具備從零到一，甚至從一到 N，靈活運用 HeyGen API 解決實際問題的能力，成為一名真正的 HeyGen API 大師。
