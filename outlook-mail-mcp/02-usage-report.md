_**免責聲明：** 由於無法直接獲取「Outlook Mail」連結器的確切工具列表，本報告基於對標準電子郵件自動化功能的合理推斷而產生。報告中描述的工具、參數和範例旨在反映一個功能完備的郵件連結器應具備的能力，但可能與實際的連結器實現存在差異。_

# 「Outlook Mail」連結器使用說明報告

**作者：** Joker

## 1. 總覽

「Outlook Mail」連結器為 Manus 平台提供了一套強大的工具，旨在讓使用者能夠透過自然語言指令，無縫地管理和自動化其 Microsoft Outlook 電子郵件帳戶。從傳送和回覆郵件，到搜尋和組織郵件，此連結器將繁瑣的日常郵件處理任務轉化為簡單、高效的自動化流程。

本報告將詳細介紹連結器提供的各個工具、其參數、功能、使用範例以及預期的輸出結果。

## 2. 可用工具詳解

### 2.1 `send_email`

傳送一封新的電子郵件。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `to_recipients` | `list[str]` | 是 | 收件人的電子郵件地址列表。 |
| `subject` | `str` | 是 | 郵件主旨。 |
| `body` | `str` | 是 | 郵件正文內容。可以是純文字或 HTML 格式。 |
| `cc_recipients` | `list[str]` | 否 | 副本 (CC) 收件人的電子郵件地址列表。 |
| `bcc_recipients` | `list[str]` | 否 | 密件副本 (BCC) 收件人的電子郵件地址列表。 |
| `attachments` | `list[str]` | 否 | 要附加的檔案的路徑列表。 |

**功能說明：**

此工具用於建立並傳送一封新的電子郵件。您可以指定一個或多個收件人、主旨和郵件內文。同時，也支援副本、密件副本以及檔案附件。

**使用範例：**

```json
{
  "tool_name": "send_email",
  "parameters": {
    "to_recipients": ["team@example.com"],
    "subject": "專案進度更新",
    "body": "大家好，\n\n附件為本週的專案進度報告，請查收。\n\n謝謝！\nJoker",
    "attachments": ["/home/ubuntu/reports/progress_week_12.pdf"]
  }
}
```

**預期輸出：**

```json
{
  "status": "success",
  "message_id": "<Sg...>",
  "confirmation": "郵件已成功傳送至 team@example.com。"
}
```

### 2.2 `reply_to_email`

回覆一封指定的電子郵件。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `message_id` | `str` | 是 | 要回覆的原始郵件的唯一識別碼。 |
| `body` | `str` | 是 | 回覆的郵件正文內容。 |
| `reply_all` | `bool` | 否 | 是否回覆給所有原始收件人 (包含 CC)。預設為 `false`。 |
| `attachments` | `list[str]` | 否 | 要附加在回覆中的檔案路徑列表。 |

**功能說明：**

使用此工具可以快速回覆一封收到的郵件。您需要提供原始郵件的 `message_id` 和回覆內容。透過 `reply_all` 參數，您可以選擇是僅回覆給寄件人，還是回覆給所有人。

**使用範例：**

```json
{
  "tool_name": "reply_to_email",
  "parameters": {
    "message_id": "<Sg...>",
    "body": "收到，謝謝你的更新！",
    "reply_all": true
  }
}
```

**預期輸出：**

```json
{
  "status": "success",
  "new_message_id": "<Sg...>",
  "confirmation": "已成功回覆郵件。"
}
```

### 2.3 `get_emails`

從指定的郵件資料夾中檢索郵件列表。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `folder` | `str` | 否 | 要從中檢索郵件的資料夾名稱。預設為「收件匣」。 |
| `limit` | `int` | 否 | 要返回的最大郵件數量。預設為 25。 |
| `query` | `str` | 否 | 用於篩選郵件的搜尋查詢。支援 Outlook 的進階搜尋語法 (例如 `from: "John Doe"` 或 `subject: "報告"`)。 |
| `unread_only` | `bool` | 否 | 是否只返回未讀郵件。預設為 `false`。 |

**功能說明：**

此工具讓您可以搜尋和檢索郵件。您可以指定資料夾、數量限制，並使用強大的查詢語法來精確地找到您需要的郵件。查詢結果是一個郵件摘要列表，包含每封郵件的 `message_id`、寄件人、主旨和接收時間。

**使用範例：**

```json
{
  "tool_name": "get_emails",
  "parameters": {
    "folder": "專案A",
    "query": "is:unread has:attachment",
    "limit": 5
  }
}
```

**預期輸出：**

```json
{
  "status": "success",
  "emails": [
    {
      "message_id": "<Sg...>",
      "from": "Alice <alice@example.com>",
      "subject": "Re: 專案A 設計稿",
      "received_at": "2026-02-24T10:30:00Z",
      "snippet": "這是最新的設計稿，請查收附件..."
    },
    {
      "message_id": "<Sg...>",
      "from": "Bob <bob@example.com>",
      "subject": "專案A 伺服器規格",
      "received_at": "2026-02-24T09:15:00Z",
      "snippet": "附件是我們建議的伺服器規格文件..."
    }
  ]
}
```

### 2.4 `get_email_details`

獲取單一封郵件的完整內容。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `message_id` | `str` | 是 | 要獲取詳細資訊的郵件的唯一識別碼。 |

**功能說明：**

當您需要讀取一封郵件的完整內文、查看所有收件人或下載附件時，可以使用此工具。它會返回郵件的所有詳細資訊。

**使用範例：**

```json
{
  "tool_name": "get_email_details",
  "parameters": {
    "message_id": "<Sg...>",
  }
}
```

**預期輸出：**

```json
{
  "status": "success",
  "details": {
    "message_id": "<Sg...>",
    "from": "Alice <alice@example.com>",
    "to": ["Joker <joker@manus.im>"],
    "cc": ["team@example.com"],
    "subject": "Re: 專案A 設計稿",
    "body": "這是最新的設計稿，請查收附件...",
    "received_at": "2026-02-24T10:30:00Z",
    "attachments": [
      {
        "filename": "design_v3.zip",
        "size": 5242880,
        "content_type": "application/zip"
      }
    ]
  }
}
```

### 2.5 `create_folder`

在您的 Outlook 帳戶中建立一個新的郵件資料夾。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `folder_name` | `str` | 是 | 新資料夾的名稱。可以使用 `/` 來建立巢狀資料夾，例如 `專案/設計`。 |

**功能說明：**

用於組織您的郵件。如果指定的資料夾已經存在，此操作將不會執行任何動作。

**使用範例：**

```json
{
  "tool_name": "create_folder",
  "parameters": {
    "folder_name": "供應商/2026"
  }
}
```

**預期輸出：**

```json
{
  "status": "success",
  "folder_id": "AAMk...",
  "confirmation": "資料夾「供應商/2026」已成功建立。"
}
```

### 2.6 `move_email`

將一封或多封郵件移動到指定的資料夾。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `message_ids` | `list[str]` | 是 | 要移動的郵件的 `message_id` 列表。 |
| `destination_folder` | `str` | 是 | 目標資料夾的名稱。 |

**功能說明：**

這是整理收件匣的核心工具。您可以將一封或一批郵件從一個資料夾移動到另一個資料夾。

**使用範例：**

```json
{
  "tool_name": "move_email",
  "parameters": {
    "message_ids": ["<Sg...>", "<Sg...>"],
    "destination_folder": "專案A"
  }
}
```

**預期輸出：**

```json
{
  "status": "success",
  "moved_count": 2,
  "confirmation": "2 封郵件已成功移動到「專案A」。"
}
```
