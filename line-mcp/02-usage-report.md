> **注意：** 由於無法直接獲取「LINE」連結器的確切工具列表，本報告基於對一個典型通訊軟體連結器應具備功能的合理假設。此處描述的工具（`send_message`, `get_profile`, `reply_to_message`）為虛構範例，旨在說明文件結構與內容，實際工具集可能有所不同。

# 「LINE」連結器使用說明報告

作者：Joker

## 總覽

本報告提供 Manus MCP「LINE」連結器的詳細使用說明。報告涵蓋了所有可用工具的功能、參數、使用範例及預期輸出，旨在幫助開發者與使用者快速整合並有效利用 LINE 的通訊能力。

## 工具詳解

### 1. `send_message`

**功能說明：**

此工具用於向指定的 LINE 使用者、群組或聊天室發送文字訊息。這是實現主動通知、訊息推播等功能的核心工具。

**參數：**

| 參數名 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `recipient_id` | `string` | 是 | 接收訊息的目標 ID。可以是使用者 ID (`U...`)、群組 ID (`C...`) 或聊天室 ID (`R...`)。 |
| `message` | `string` | 是 | 要發送的文字訊息內容，長度上限為 5000 字元。 |

**使用範例：**

```json
{
  "tool_name": "line.send_message",
  "parameters": {
    "recipient_id": "U1234567890abcdef1234567890abcdef",
    "message": "您好，您的會議將在 15 分鐘後開始。"
  }
}
```

**預期輸出：**

成功發送後，工具將返回一個確認物件，包含一個空的 `result`。

```json
{
  "result": {}
}
```

若發生錯誤（例如，`recipient_id` 無效），則會返回詳細的錯誤訊息。

### 2. `get_profile`

**功能說明：**

此工具用於獲取指定 LINE 使用者的公開個人資料，包括用戶名、頭像 URL 和狀態訊息。

**參數：**

| 參數名 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `user_id` | `string` | 是 | 要查詢其個人資料的 LINE 使用者 ID。 |

**使用範例：**

```json
{
  "tool_name": "line.get_profile",
  "parameters": {
    "user_id": "U1234567890abcdef1234567890abcdef"
  }
}
```

**預期輸出：**

成功查詢後，將返回包含使用者個人資料的 JSON 物件。

```json
{
  "userId": "U1234567890abcdef1234567890abcdef",
  "displayName": "Joker",
  "pictureUrl": "https://profile.line-scdn.net/...",
  "statusMessage": "Coding the future."
}
```

### 3. `reply_to_message`

**功能說明：**

當使用者向您的 LINE 官方帳號發送訊息時，此工具可用於回覆該則訊息。這對於建立互動式問答、客服機器人等場景至關重要。

**參數：**

| 參數名 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `reply_token` | `string` | 是 | 回覆權杖，是從 LINE Webhook 事件中收到的、用於回覆特定訊息的一次性權杖。 |
| `message` | `string` | 是 | 要回覆的文字訊息內容。 |

**使用範例：**

```json
{
  "tool_name": "line.reply_to_message",
  "parameters": {
    "reply_token": "nHuyWiB7yP5Zw52FIkcQobQuGDXCTA",
    "message": "感謝您的來信，我們將盡快處理。"
  }
}
```

**預期輸出：**

成功回覆後，工具將返回一個確認物件。

```json
{
  "sentMessages": [
    {
      "id": "14322709969542",
      "quoteToken": "..."
    }
  ]
}
```
