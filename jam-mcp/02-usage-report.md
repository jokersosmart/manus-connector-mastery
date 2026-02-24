# Jam MCP 連結器使用說明報告

**作者：Joker**

## 簡介

本報告旨在詳細說明 Manus MCP 的「Jam」連結器所提供的所有可用工具。每個工具都包含其功能說明、詳細參數、使用範例以及預期的輸出格式，以幫助開發者和 AI Agent 能有效地利用 Jam 連結器來分析和解決問題。

Jam 連結器的核心是圍繞「Jam 錄製檔」進行操作，這是一個包含了螢幕錄影、使用者操作、主控台日誌、網路請求等豐富上下文的檔案。所有工具的調用都基於一個或多個 Jam 錄製檔的連結。

---

## 工具詳解

### 1. `getSummary`

#### 功能說明

此工具提供一個 Jam 錄製檔的快速總覽，包含是誰錄製的、發生了什麼、以及建議接下來可以嘗試使用哪些其他工具來深入分析。這通常是分析一個 Jam 錄製檔的第一步。

#### 參數

此工具沒有任何參數，它會直接對提供的 Jam 連結進行操作。

#### 使用範例

假設 Manus Agent 獲得了一個 Jam 連結 `https://jam.dev/j/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`，它會發出以下工具調用：

```json
{
  "tool_name": "getSummary",
  "parameters": {}
}
```

#### 預期輸出

工具會返回一段 Markdown 格式的文字，總結該 Jam 的核心資訊。

```markdown
**Jam 摘要**

*   **作者**: 'Joker' (joker@example.com)
*   **錄製於**: 2026-02-24 15:55 UTC
*   **標題**: '結帳頁面載入失敗'
*   **主要活動**: 使用者點擊了「前往結帳」按鈕，但頁面持續顯示載入中，最終出現 500 伺服器錯誤。
*   **建議的下一步**: 
    *   使用 `getConsoleLogs` 檢查瀏覽器錯誤。
    *   使用 `getNetworkRequests` 過濾狀態碼為 `5xx` 的請求以查看伺服器回應。
```

### 2. `getConsoleLogs`

#### 功能說明

此工具用於抓取 Jam 錄製過程中記錄的所有瀏覽器主控台日誌 (console logs)，包含 `log`, `info`, `warn`, `error` 等不同層級的訊息。這對於診斷前端 JavaScript 錯誤至關重要。

#### 參數

| 參數 | 類型 | 說明 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `limit` | integer | 限制返回的日誌數量，預設為返回所有日誌。 | 否 |

#### 使用範例

為了避免過多無關的日誌，AI Agent 決定只查看最新的 10 條日誌。

```json
{
  "tool_name": "getConsoleLogs",
  "parameters": {
    "limit": 10
  }
}
```

#### 預期輸出

工具會返回一個 JSON 陣列，其中每個物件代表一條日誌記錄。

```json
[
  {
    "level": "error",
    "message": "Uncaught TypeError: Cannot read properties of null (reading 'checkout')",
    "timestamp": "2026-02-24T15:55:30.123Z"
  },
  {
    "level": "log",
    "message": "User clicked checkout button",
    "timestamp": "2026-02-24T15:55:28.456Z"
  }
]
```

### 3. `getNetworkRequests`

#### 功能說明

此工具列出 Jam 錄製過程中的所有網路請求，包含其方法、URL、狀態碼、標頭和內文。這對於分析後端 API 錯誤或性能問題非常有幫助。

#### 參數

| 參數 | 類型 | 說明 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `statusCode` | string / integer | 過濾特定狀態碼的請求，例如 `500` 或 `"5xx"` 來尋找伺服器錯誤。 | 否 |
| `contentType` | string | 過濾特定內容類型的請求，例如 `"application/json"`。 | 否 |
| `host` | string | 過濾特定主機的請求，例如 `"api.example.com"`。 | 否 |

#### 使用範例

根據 `getSummary` 的建議，AI Agent 決定尋找所有伺服器錯誤的網路請求。

```json
{
  "tool_name": "getNetworkRequests",
  "parameters": {
    "statusCode": "5xx"
  }
}
```

#### 預期輸出

工具會返回一個 JSON 陣列，其中每個物件代表一個符合條件的網路請求。

```json
[
  {
    "method": "POST",
    "url": "https://api.example.com/checkout",
    "statusCode": 500,
    "requestHeaders": { ... },
    "responseHeaders": { ... },
    "responseBody": "{\"error\": \"Internal Server Error\"}"
  }
]
```

### 4. `getScreenshot`

#### 功能說明

此工具用於獲取 Jam 錄製檔中的所有螢幕截圖。對於非影片形式的 Jam（即純截圖標註的 Jam），這是視覺化問題的主要方式。

#### 參數

此工具沒有任何參數。

#### 使用範例

AI Agent 想要查看使用者回報問題時所附的截圖。

```json
{
  "tool_name": "getScreenshot",
  "parameters": {}
}
```

#### 預期輸出

工具會返回一個 JSON 陣列，其中每個物件代表一張截圖的資訊，通常包含圖片的 URL。

```json
[
  {
    "url": "https://jam.dev/screenshots/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/01.png",
    "timestamp": "2026-02-24T15:55:29.000Z"
  }
]
```

### 5. `getUserEvents`

#### 功能說明

此工具以純文字形式，按時間順序列出使用者在錄製過程中的所有互動事件，例如點擊、輸入和頁面導航。這有助於精確地重現使用者的操作路徑。

#### 參數

此工具沒有任何參數。

#### 使用範例

AI Agent 需要了解使用者在遇到錯誤前，具體執行了哪些操作。

```json
{
  "tool_name": "getUserEvents",
  "parameters": {}
}
```

#### 預期輸出

工具會返回一個 JSON 陣列，其中每個物件代表一個使用者事件。

```json
[
  {
    "type": "navigate",
    "url": "https://example.com/products",
    "timestamp": "2026-02-24T15:55:20.000Z"
  },
  {
    "type": "click",
    "selector": "button#add-to-cart",
    "text": "加入購物車",
    "timestamp": "2026-02-24T15:55:25.000Z"
  },
  {
    "type": "click",
    "selector": "a#checkout",
    "text": "前往結帳",
    "timestamp": "2026-02-24T15:55:28.000Z"
  }
]
```

### 6. `getMetadata`

#### 功能說明

此工具用於讀取開發者透過 `jam.metadata()` SDK 主動附加到 Jam 錄製檔中的自訂中繼資料。這些資料通常包含對偵錯非常有用的上下文，例如使用者 ID、應用程式版本、功能旗標 (Feature Flags) 等。

#### 參數

此工具沒有任何參數。

#### 使用範例

AI Agent 想要確認這個 Bug 是在哪個應用程式版本上發生的。

```json
{
  "tool_name": "getMetadata",
  "parameters": {}
}
```

#### 預期輸出

工具會返回一個 JSON 物件，其中包含所有自訂的中繼資料。

```json
{
  "userId": "user-12345",
  "appVersion": "2.5.1",
  "featureFlags": {
    "newCheckoutFlow": true,
    "betaFeatures": false
  }
}
```

### 7. `getVideoTranscript`

#### 功能說明

此工具用於獲取啟用麥克風的影片 Jam 錄製檔中的語音轉錄文字。轉錄的文字以 WebVTT 格式提供，包含時間戳，方便將語音內容與影片畫面進行對應。

#### 參數

此工具沒有任何參數。

#### 使用範例

AI Agent 想要分析使用者在回報問題時的口頭描述。

```json
{
  "tool_name": "getVideoTranscript",
  "parameters": {}
}
```

#### 預期輸出

工具會返回一個包含 WebVTT 格式轉錄稿的字串。

```vtt
WEBVTT

00:00:01.500 --> 00:00:04.000
好的，我現在要試著重現一下那個結帳的問題。

00:00:05.100 --> 00:00:08.200
我點擊「前往結帳」，然後頁面就一直卡在這裡轉圈圈。
```

### 8. `analyzeVideo`

#### 功能說明

此工具是一個強大的 AI 功能，它會分析 Jam 的影片錄製檔，自動提取洞察、偵測潛在問題，並提供結構化的回饋。這能極大地加速對複雜問題的理解。

#### 參數

此工具沒有任何參數。

#### 使用範例

AI Agent 希望 AI 對影片內容進行一次全面的分析，以找出可能被忽略的細節。

```json
{
  "tool_name": "analyzeVideo",
  "parameters": {}
}
```

#### 預期輸出

工具會返回一段 Markdown 格式的分析報告。

```markdown
**影片分析報告**

*   **偵測到的問題**: 在影片 00:07 處，瀏覽器主控台出現了一個短暫閃過的紅色錯誤訊息，隨後被新的日誌覆蓋。錯誤內容似乎與 `payment_gateway_timeout` 有關。
*   **使用者行為洞察**: 使用者在點擊「前往結帳」按鈕前，曾快速來回切換了兩個不同的送貨地址，這可能觸發了某個邊界條件 (edge case)。
*   **建議**: 深入檢查 `getConsoleLogs` 中時間戳在 00:07 附近的錯誤日誌，並使用 `getUserEvents` 來驗證地址切換的操作順序。
```

### 9. `searchJams`

#### 功能說明

此工具允許在整個 Jam 工作區中搜尋和過濾錄製檔，方便一次性分析多個相關的 Bug 報告。

#### 參數

| 參數 | 類型 | 說明 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `folder` | string | 指定要搜尋的資料夾 ID 或名稱。 | 否 |
| `author` | string | 指定要搜尋的作者使用者 ID、名稱或電子郵件。 | 否 |
| `url` | string | 過濾錄製自特定 URL 的 Jam，例如 `"example.com/checkout"`。 | 否 |

#### 使用範例

AI Agent 想要找出所有由使用者 'Joker' 回報的，且與結帳頁面相關的 Jam。

```json
{
  "tool_name": "searchJams",
  "parameters": {
    "author": "Joker",
    "url": "example.com/checkout"
  }
}
```

#### 預期輸出

工具會返回一個 JSON 陣列，其中每個物件代表一個符合條件的 Jam 錄製檔。

```json
[
  {
    "jamId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "title": "結帳頁面載入失敗",
    "author": "Joker",
    "url": "https://example.com/checkout",
    "createdAt": "2026-02-24T15:55:29.000Z"
  }
]
```

### 10. `addComment`

#### 功能說明

此工具允許以程式化的方式為 Jam 錄製檔添加 Markdown 格式的註解。這對於記錄分析過程、留下結論或與團隊成員溝通非常有用。

#### 參數

| 參數 | 類型 | 說明 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `comment` | string | 要添加的 Markdown 格式的註解內容。 | 是 |

#### 使用範例

AI Agent 在分析完畢後，想要將結論記錄在 Jam 上。

```json
{
  "tool_name": "addComment",
  "parameters": {
    "comment": "**分析結論:** 問題根源是後端支付閘道 API 超時。已通知後端團隊處理。"
  }
}
```

#### 預期輸出

工具調用成功後，不會返回具體內容，但該註解會出現在 Jam 的評論區。

### 11. `moveJam`

#### 功能說明

此工具用於將一個或多個 Jam 錄製檔移動到指定的資料夾中，方便對 Bug 報告進行分類和管理。

#### 參數

| 參數 | 類型 | 說明 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `folder` | string | 目標資料夾的 ID 或名稱。 | 是 |

#### 使用範例

AI Agent 在確認問題已解決後，想要將該 Jam 移動到「已解決」資料夾。

```json
{
  "tool_name": "moveJam",
  "parameters": {
    "folder": "已解決"
  }
}
```

#### 預期輸出

工具調用成功後，不會返回具體內容，但該 Jam 會在指定的資料夾中找到。
