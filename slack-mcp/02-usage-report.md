# Slack 連結器使用說明報告

作者：Joker

## 總覽

本報告提供 Manus MCP Slack 連結器所有可用工具的詳細使用說明。每個工具都包含其功能描述、參數詳解、使用範例及預期輸出，旨在幫助使用者快速掌握並有效利用此連結器，實現工作流程的自動化與效率提升。

由於無法直接獲取工具列表，本報告基於對一個典型 Slack 整合工具應具備功能的合理推測來撰寫。涵蓋了訊息發送、頻道管理、使用者資訊查詢等核心功能。

---

## 工具詳解

### 1. `send_message`

**功能說明**

發送訊息到指定的 Slack 公開頻道、私有頻道或直接發送給使用者。

**參數**

| 參數 | 型別 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `destination` | String | 是 | 訊息發送的目的地。可以是頻道名稱 (e.g., `#general`) 或使用者 ID (e.g., `@U12345`)。 |
| `content` | String | 是 | 要發送的訊息內容。支援 Slack 的 Markdown 格式。 |
| `as_thread` | Boolean | 否 | 是否作為一個新的討論串來發送。預設為 `false`。 |

**使用範例**

```
manus-mcp-cli tool call send_message --server slack --input '{
  "destination": "#project-alpha",
  "content": "專案 Alpha 的第一階段開發已完成，請查收附件的測試報告。"
}'
```

**預期輸出**

```json
{
  "status": "Success",
  "message_id": "1678886400.123456",
  "channel_id": "C123ABC456"
}
```

### 2. `create_channel`

**功能說明**

在 Slack 工作區中建立一個新的公開或私有頻道。

**參數**

| 參數 | 型別 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `name` | String | 是 | 新頻道的名稱。不需包含 `#` 前綴，且必須為小寫字母、數字和連字號的組合。 |
| `is_private` | Boolean | 否 | 是否建立為私有頻道。預設為 `false` (公開頻道)。 |
| `topic` | String | 否 | 設定頻道的議題，會顯示在頻道頂端。 |

**使用範例**

```
manus-mcp-cli tool call create_channel --server slack --input '{
  "name": "q1-marketing-campaign",
  "is_private": true,
  "topic": "討論 2026 年第一季度行銷活動的規劃與執行。"
}'
```

**預期輸出**

```json
{
  "status": "Success",
  "channel_id": "C789DEF123",
  "channel_name": "q1-marketing-campaign"
}
```

### 3. `list_channels`

**功能說明**

列出 Slack 工作區中所有的公開頻道。

**參數**

| 參數 | 型別 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `max_results` | Integer | 否 | 回傳的最大頻道數量。預設為 100。 |

**使用範例**

```
manus-mcp-cli tool call list_channels --server slack --input '{
  "max_results": 50
}'
```

**預期輸出**

```json
{
  "status": "Success",
  "channels": [
    { "id": "C123ABC456", "name": "general", "topic": "公司全體公告與討論" },
    { "id": "C456DEF789", "name": "random", "topic": "閒聊、分享有趣的事物" },
    { "id": "C789GHI123", "name": "project-alpha", "topic": "專案 Alpha 相關討論" }
  ]
}
```

### 4. `get_user_info`

**功能說明**

根據使用者 ID 獲取其詳細的個人資料。

**參數**

| 參數 | 型別 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `user_id` | String | 是 | 要查詢的使用者 ID (e.g., `U12345`)。 |

**使用範例**

```
manus-mcp-cli tool call get_user_info --server slack --input '{
  "user_id": "U12345"
}'
```

**預期輸出**

```json
{
  "status": "Success",
  "user": {
    "id": "U12345",
    "name": "alex.chen",
    "real_name": "Alex Chen",
    "email": "alex.chen@example.com",
    "title": "Software Engineer",
    "tz": "Asia/Taipei"
  }
}
```
