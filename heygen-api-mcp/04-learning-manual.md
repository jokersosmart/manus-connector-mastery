# HeyGen API 學習手冊

作者：Joker

## 簡介

本手冊旨在幫助您快速上手 HeyGen API，並掌握其核心技能。我們將透過一系列清晰的學習目標和實踐練習，讓您在最短的時間內，從入門到能夠獨立完成專案。

## 模組一：基礎入門 (1-2 小時)

**學習目標：**

*   [ ] 成功獲取並設定您的 API 金鑰。
*   [ ] 理解 API 的非同步運作模式。
*   [ ] 能夠使用 cURL 或 Postman 等工具，成功呼叫 `GET /v2/avatars` 端點並看到回傳結果。

**實踐練習：**

1.  **環境設定：**
    *   安裝 [Postman](https://www.postman.com/downloads/)。
    *   在 Postman 中建立一個新的請求 (Request)。
    *   在 `Headers` 標籤頁中，新增一個鍵值對，其中 `Key` 為 `X-Api-Key`，`Value` 為您從 HeyGen 網站獲取的 API 金鑰。

2.  **第一次接觸：**
    *   將請求的 URL 設定為 `https://api.heygen.com/v2/voices`，方法設定為 `GET`。
    *   點擊「Send」按鈕。
    *   **檢查點：** 在下方的回應 (Response) 區域，您是否看到了一個 JSON 格式的列表，其中包含了各種聲音的資訊？如果看到了，請在上面的學習目標旁打勾。

## 模組二：核心影片生成 (2-3 小時)

**學習目標：**

*   [ ] 能夠使用「一鍵式」工具 (`/v1/video_agent/generate`) 產生一支簡單的影片。
*   [ ] 掌握查詢影片狀態 (`/v1/video_status.get`) 並獲取影片下載連結的完整流程。
*   [ ] 能夠使用「精準控制」工具 (`/v2/video/generate`) 產生一支指定頭像和聲音的影片。

**實踐練習：**

1.  **您的第一支影片：**
    *   在 Postman 中建立一個 `POST` 請求，URL 為 `https://api.heygen.com/v1/video_agent/generate`。
    *   進入 `Body` 標籤頁，選擇 `raw` 和 `JSON` 格式。
    *   貼上以下內容：
        ```json
        {
          "script": [ {"text": "Hello, this is my first AI video!"} ],
          "title": "My Test Video"
        }
        ```
    *   發送請求，並從回傳結果中複製 `video_id` 的值。

2.  **等待與收穫：**
    *   建立一個 `GET` 請求，URL 為 `https://api.heygen.com/v1/video_status.get?video_id=在此貼上您的video_id`。
    *   重複發送此請求，直到回傳的 `status` 欄位值為 `completed`。
    *   從最終的回傳結果中，複製 `video_url` 的值，並在瀏覽器中打開它，觀看您的成果！

3.  **精準挑戰：**
    *   綜合模組一和模組二的知識，挑戰使用 `/v2/video/generate` 端點。
    *   您需要先呼叫 `/v2/avatars` 和 `/v2/voices`，從中挑選出您想要的 `avatar_id` 和 `voice_id`。
    *   在 `/v2/video/generate` 的請求 Body 中，模仿 `02-usage-report.md` 的範例，填入您選擇的 ID。
    *   完成影片的生成與下載。

## 模組三：進階客製化與應用 (3-5 小時)

**學習目標：**

*   [ ] 能夠為影片設定圖片或影片背景。
*   [ ] 能夠調整聲音的語速或音高。
*   [ ] 理解模板化影片生成的概念與優勢。

**實踐練習：**

1.  **美化您的影片：**
    *   尋找一個公開的圖片 URL (例如，從 Unsplash)。
    *   修改您在模組二中使用的 `/v2/video/generate` 請求。
    *   在 `video_inputs` 的場景物件中，加入 `background` 欄位，並設定 `type` 為 `image`，`value` 為您的圖片 URL。
    *   生成影片，看看效果如何。

2.  **變聲挑戰：**
    *   在您的 `/v2/video/generate` 請求中，找到 `voice` 物件。
    *   加入 `speed` 參數，並將其值設定為 `1.2` (加快 20%)。
    *   生成影片，聽聽看語速是否有變化。
    *   嘗試將 `speed` 改為 `0.8`，或加入 `pitch` 參數，探索不同的聲音效果。

3.  **概念研究：**
    *   閱讀 HeyGen 官方文件中關於「Templates」和「Streaming API」的部分。
    *   思考並寫下：
        *   在您的專案中，什麼樣的場景適合使用模板化生成？
        *   您可以想像出哪些應用適合使用即時互動頭像？

完成本手冊的所有練習後，您將具備堅實的 HeyGen API 基礎，足以應對大部分常見的 AI 影片生成需求。持續探索官方文件，是您通往更高階應用的不二法門。
