# 「Mailchimp Marketing」連結器使用說明報告

**作者：Joker**

---

## 簡介

本報告提供 Manus MCP「Mailchimp Marketing」連結器所有可用工具的詳細使用說明。每個工具都包含其功能說明、必要的參數、使用範例及預期的輸出格式。

由於無法直接獲取 MCP 工具列表，本報告中的工具是根據 Mailchimp Marketing API [1] 的公開文件推斷而來，旨在涵蓋核心的郵件行銷自動化功能，包括受眾管理、行銷活動操作與報告檢視。

---

## 受眾管理 (Audiences & Contacts)

管理您的 Mailchimp 受眾（即聯絡人列表）以及其中的聯絡人。

### 1. `list_audiences`

**功能說明**：獲取您 Mailchimp 帳戶中所有受眾（列表）的摘要資訊。

**參數**：無

**使用範例**：

```json
{
  "tool": "mailchimp.list_audiences"
}
```

**預期輸出**：

```json
{
  "audiences": [
    {
      "id": "a1b2c3d4e5",
      "name": "Newsletter Subscribers",
      "contact_count": 1234
    },
    {
      "id": "f6g7h8i9j0",
      "name": "Q4 Event Attendees",
      "contact_count": 567
    }
  ]
}
```

### 2. `add_contact`

**功能說明**：將一個新的聯絡人新增到指定的受眾中。如果聯絡人已存在，可選擇更新其資訊。

**參數**：

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `audience_id` | string | 是 | 要新增聯絡人的受眾 ID。 |
| `email_address` | string | 是 | 聯絡人的電子郵件地址。 |
| `status` | string | 是 | 聯絡人的訂閱狀態，例如 `subscribed`、`unsubscribed`、`cleaned`、`pending`。 |
| `merge_fields` | object | 否 | 一個包含聯絡人額外資訊的物件，例如 `{"FNAME": "John", "LNAME": "Doe"}`。 |
| `tags` | array | 否 | 一個包含要附加到聯絡人的標籤陣列，例如 `["new_customer", "vip"]`。 |

**使用範例**：

```json
{
  "tool": "mailchimp.add_contact",
  "audience_id": "a1b2c3d4e5",
  "email_address": "john.doe@example.com",
  "status": "subscribed",
  "merge_fields": {
    "FNAME": "John",
    "LNAME": "Doe"
  },
  "tags": ["new_customer"]
}
```

**預期輸出**：

```json
{
  "id": "...",
  "email_address": "john.doe@example.com",
  "status": "subscribed",
  ...
}
```

### 3. `get_contact`

**功能說明**：根據電子郵件地址從特定受眾中獲取聯絡人的詳細資訊。

**參數**：

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `audience_id` | string | 是 | 聯絡人所在的受眾 ID。 |
| `email_address` | string | 是 | 要查詢的聯絡人電子郵件地址。 |

**使用範例**：

```json
{
  "tool": "mailchimp.get_contact",
  "audience_id": "a1b2c3d4e5",
  "email_address": "john.doe@example.com"
}
```

**預期輸出**：

```json
{
  "id": "...",
  "email_address": "john.doe@example.com",
  "status": "subscribed",
  "merge_fields": {
    "FNAME": "John",
    "LNAME": "Doe"
  },
  ...
}
```

---

## 行銷活動管理 (Campaigns)

建立、管理和發送您的 Mailchimp 行銷活動。

### 4. `list_campaigns`

**功能說明**：獲取您帳戶中所有行銷活動的列表。

**參數**：

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `status` | string | 否 | 篩選特定狀態的行銷活動，例如 `sent`、`draft`、`schedule`。 |
| `count` | integer | 否 | 返回的行銷活動數量，預設為 10。 |

**使用範例**：

```json
{
  "tool": "mailchimp.list_campaigns",
  "status": "sent",
  "count": 5
}
```

**預期輸出**：

```json
{
  "campaigns": [
    {
      "id": "c1d2e3f4g5",
      "type": "regular",
      "status": "sent",
      "send_time": "2026-02-20T14:30:00+00:00",
      "emails_sent": 5000,
      "report_summary": {
        "opens": 1500,
        "unique_opens": 1200,
        "open_rate": 0.24,
        "clicks": 300,
        "subscriber_clicks": 250,
        "click_rate": 0.06
      }
    }
  ]
}
```

### 5. `create_campaign`

**功能說明**：建立一個新的行銷活動。

**參數**：

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `type` | string | 是 | 行銷活動類型，通常為 `regular`。 |
| `recipients` | object | 是 | 一個包含 `list_id` 的物件，指定目標受眾。 |
| `settings` | object | 是 | 一個包含行銷活動設定的物件，如 `subject_line`、`from_name`、`reply_to`。 |

**使用範例**：

```json
{
  "tool": "mailchimp.create_campaign",
  "type": "regular",
  "recipients": {
    "list_id": "a1b2c3d4e5"
  },
  "settings": {
    "subject_line": "Our Weekly Newsletter",
    "from_name": "My Company",
    "reply_to": "support@mycompany.com"
  }
}
```

**預期輸出**：

```json
{
  "id": "h1i2j3k4l5",
  "type": "regular",
  "status": "draft",
  ...
}
```

### 6. `send_campaign`

**功能說明**：立即發送一個已建立的草稿行銷活動。

**參數**：

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `campaign_id` | string | 是 | 要發送的行銷活動 ID。 |

**使用範例**：

```json
{
  "tool": "mailchimp.send_campaign",
  "campaign_id": "h1i2j3k4l5"
}
```

**預期輸出**：

```json
{
  "status": "sending"
}
```

---

## 報告 (Reports)

獲取已發送行銷活動的成效報告。

### 7. `get_campaign_report`

**功能說明**：獲取特定行銷活動的詳細成效報告。

**參數**：

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `campaign_id` | string | 是 | 要查詢報告的行銷活動 ID。 |

**使用範例**：

```json
{
  "tool": "mailchimp.get_campaign_report",
  "campaign_id": "c1d2e3f4g5"
}
```

**預期輸出**：

```json
{
  "id": "c1d2e3f4g5",
  "campaign_title": "Our Weekly Newsletter",
  "emails_sent": 5000,
  "abuse_reports": 2,
  "unsubscribed": 15,
  "opens": {
    "opens_total": 1500,
    "unique_opens": 1200,
    "open_rate": 0.24
  },
  "clicks": {
    "clicks_total": 300,
    "unique_clicks": 250,
    "click_rate": 0.06
  },
  ...
}
```

---

## 參考資料

[1] Mailchimp. (2026). *Mailchimp Marketing API Reference*. Retrieved from https://mailchimp.com/developer/marketing/api/
