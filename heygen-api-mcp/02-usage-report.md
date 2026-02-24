# HeyGen API 連接器使用說明報告

作者：Joker

## 總覽

HeyGen API 提供了一套強大的工具，讓開發者能夠以程式化的方式，大規模地生成高品質的 AI 頭像影片。這份報告將詳細介紹 HeyGen 連接器中包含的所有工具、其參數、功能、使用範例及預期輸出。

**基礎 URL:** `https://api.heygen.com`

**認證方式:** 所有 API 請求都必須在 HTTP 標頭 (Header) 中包含 `X-Api-Key` 欄位，並填入您的專屬 API 金鑰。

## 核心工具 (Endpoints)

HeyGen API 的核心功能主要透過以下幾個端點 (Endpoint) 來實現。這些工具涵蓋了從快速生成影片到精細化控制影片的各個層面。

### 1. 一鍵式影片生成 (Video Agent)

這個工具適合需要快速將文字腳本轉換為影片的場景，系統會自動處理場景、頭像和聲音的選擇。

*   **Endpoint:** `POST /v1/video_agent/generate`
*   **功能說明:** 傳入一個結構化的腳本，API 將自動產生一個完整的影片。這是最簡單的影片生成方式。
*   **詳細參數:**
    *   `script`: (必要) 一個包含場景 (scenes) 的陣列，每個場景可以定義要說的文字、頭像、背景等。
    *   `title`: (選用) 影片的標題。
*   **使用範例:**

    ```json
    {
      "script": [
        {
          "actor": "Luna",
          "text": "Welcome to our first scene!"
        },
        {
          "actor": "Mark",
          "text": "And this is the second scene, with a different avatar."
        }
      ],
      "title": "My First Agent Video"
    }
    ```

*   **預期輸出:** 一個 JSON 物件，其中包含 `video_id`，可用於後續查詢影片狀態。

### 2. 精準多場景影片生成

當您需要對影片的每一個細節（如頭像、聲音、背景、運鏡）進行精確控制時，應使用此工具。

*   **Endpoint:** `POST /v2/video/generate`
*   **功能說明:** 提供更細緻的參數，允許開發者為每個場景獨立設定頭像、聲音、背景、配樂等元素，實現高度客製化的影片內容。
*   **詳細參數:**
    *   `video_inputs`: (必要) 一個場景物件的陣列，每個物件都可精細定義。
    *   `dimension`: (選用) 影片的解析度，例如 `1920x1080`。
    *   `title`: (選用) 影片標題。
*   **使用範例:**

    ```json
    {
      "video_inputs": [
        {
          "avatar": {
            "avatar_id": "Mark-costume-1234",
            "avatar_style": "normal"
          },
          "voice": {
            "voice_id": "en-US-JennyNeural",
            "text": "This is a highly customized video scene."
          },
          "background": {
            "type": "color",
            "value": "#FFFFFF"
          }
        }
      ],
      "title": "Advanced Custom Video",
      "dimension": {
        "width": 1920,
        "height": 1080
      }
    }
    ```

*   **預期輸出:** 同樣返回包含 `video_id` 的 JSON 物件。

### 3. 查詢影片生成狀態

影片生成是一個非同步過程。提交生成請求後，您需要使用此工具來輪詢 (polling) 影片的處理進度。

*   **Endpoint:** `GET /v1/video_status.get`
*   **功能說明:** 根據 `video_id` 查詢影片的目前狀態，例如「處理中」、「成功」或「失敗」。
*   **詳細參數:**
    *   `video_id`: (必要) 從生成請求中獲得的影片 ID。
*   **使用範例:**

    `https://api.heygen.com/v1/video_status.get?video_id=your_video_id_here`

*   **預期輸出:** 一個 JSON 物件，包含影片的 `status`。如果狀態為 `completed`，則會額外提供 `video_url` (影片下載連結) 和 `thumbnail_url` (縮圖連結)。

### 4. 獲取可用頭像列表

在生成影片前，您可能需要知道有哪些頭像可供選擇。

*   **Endpoint:** `GET /v2/avatars`
*   **功能說明:** 返回所有公開可用的頭像 (avatar) 列表及其相關資訊。
*   **詳細參數:** 無。
*   **使用範例:**

    `https://api.heygen.com/v2/avatars`

*   **預期輸出:** 一個 JSON 陣列，每個物件代表一個頭像，包含 `avatar_id`、名稱、預覽圖等資訊。

### 5. 獲取可用聲音列表

與頭像類似，您也可以獲取所有可用的 AI 聲音。

*   **Endpoint:** `GET /v2/voices`
*   **功能說明:** 返回所有可用的 AI 聲音 (voice) 列表。
*   **詳細參數:** 無。
*   **使用範例:**

    `https://api.heygen.com/v2/voices`

*   **預期輸出:** 一個 JSON 陣列，每個物件代表一個聲音，包含 `voice_id`、語言、性別等資訊。

## 參考資料

[1] HeyGen API Documentation. (2026). *API Reference*. Retrieved from https://docs.heygen.com/docs/api-reference
