> **注意：** 由於無法直接查詢到已註冊的 Intercom MCP 伺服器，本文件將根據 Intercom 公開的 REST API 文件，定義一套最核心、最符合用戶價值的虛擬工具集。這套工具集旨在作為未來實現 Intercom 連接器時的藍圖與規範。

# 「Intercom」連接器使用說明報告

**作者：Joker**

## 1. 總覽

Intercom 連接器旨在將 Manus 的自然語言指令能力與 Intercom 強大的客戶溝通平台無縫對接。透過本連接器，使用者可以自動化處理客戶互動、管理聯絡人與公司資訊、指派對話以及查詢支援知識庫，從而大幅提升客戶服務團隊的效率與回應品質。

本報告將詳細介紹連接器提供的所有工具、其參數、使用範例及預期輸出，以協助開發者與使用者充分利用其功能。

## 2. 核心工具詳解

### 2.1 聯絡人管理 (Contacts)

#### `create_contact`

**功能說明：** 在 Intercom 中建立一個新的聯絡人（使用者或潛在客戶）。

**參數：**

| 參數名稱 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `role` | String | 聯絡人的角色，可為 `user` 或 `lead`。 | 是 |
| `email` | String | 聯絡人的電子郵件地址，用於唯一識別。 | 是 |
| `name` | String | 聯絡人的全名。 | 否 |
| `phone` | String | 聯絡人的電話號碼。 | 否 |
| `custom_attributes` | Object | 一個包含自訂資料的鍵值對物件。 | 否 |

**使用範例：**

```json
{
  "role": "user",
  "email": "jane.doe@example.com",
  "name": "Jane Doe",
  "custom_attributes": {
    "plan": "premium",
    "signup_date": 1677628800
  }
}
```

**預期輸出：** 返回一個包含新建立聯絡人完整資訊的 JSON 物件。

---

#### `get_contact`

**功能說明：** 根據指定的唯一識別碼，檢索單一聯絡人的詳細資訊。

**參數：**

| 參數名稱 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `id` | String | Intercom 中聯絡人的唯一 ID。 | 擇一 |
| `email` | String | 聯絡人的電子郵件地址。 | 擇一 |

**使用範例：**

```json
{
  "email": "jane.doe@example.com"
}
```

**預期輸出：** 返回一個包含指定聯絡人詳細資訊的 JSON 物件。

---

#### `update_contact`

**功能說明：** 更新現有聯絡人的屬性。

**參數：**

| 參數名稱 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `id` | String | Intercom 中聯絡人的唯一 ID。 | 是 |
| `name` | String | 要更新的聯絡人全名。 | 否 |
| `phone` | String | 要更新的聯絡人電話號碼。 | 否 |
| `custom_attributes` | Object | 一個包含要更新或新增的自訂資料鍵值對物件。 | 否 |

**使用範例：**

```json
{
  "id": "61f0c9a77e4f3d001f2b6b1a",
  "custom_attributes": {
    "plan": "enterprise",
    "last_seen": 1678886400
  }
}
```

**預期輸出：** 返回一個包含更新後聯絡人完整資訊的 JSON 物件。

---

#### `list_contacts`

**功能說明：** 檢索符合特定條件的聯絡人列表。

**參數：**

| 參數名稱 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `segment_id` | String | 根據 Intercom 中的區隔 (Segment) ID 進行篩選。 | 否 |
| `tag_id` | String | 根據標籤 (Tag) ID 進行篩選。 | 否 |
| `email` | String | 根據電子郵件地址進行篩選。 | 否 |

**使用範例：**

```json
{
  "tag_id": "1234567"
}
```

**預期輸出：** 返回一個包含聯絡人列表的 JSON 物件，支援分頁。

### 2.2 對話管理 (Conversations)

#### `create_conversation`

**功能說明：** 以使用者或管理員的身分，發起一則新的對話。

**參數：**

| 參數名稱 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `from_user_id` | String | 發起對話的使用者 ID。 | 是 |
| `body` | String | 對話的第一則訊息內容。 | 是 |
| `message_type` | String | 訊息類型，例如 `comment`。 | 是 |

**使用範例：**

```json
{
  "from_user_id": "61f0c9a77e4f3d001f2b6b1a",
  "body": "Hi there, I have a question about my recent invoice.",
  "message_type": "comment"
}
```

**預期輸出：** 返回一個包含新建立對話資訊的 JSON 物件。

---

#### `reply_to_conversation`

**功能說明：** 在現有的對話中，以管理員或使用者的身分發送回覆。

**參數：**

| 參數名稱 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `conversation_id` | String | 要回覆的對話 ID。 | 是 |
| `admin_id` | String | 代表回覆的管理員 ID。 | 是 |
| `body` | String | 回覆的訊息內容。 | 是 |
| `message_type` | String | 訊息類型，可為 `comment` (公開回覆) 或 `note` (內部註記)。 | 是 |

**使用範例：**

```json
{
  "conversation_id": "123456789",
  "admin_id": "98765",
  "body": "Hi Jane, I'd be happy to help with that. Can you please provide the invoice number?",
  "message_type": "comment"
}
```

**預期輸出：** 返回一個包含新發送訊息的對話部分 JSON 物件。

---

#### `get_conversation`

**功能說明：** 檢索單一對話的詳細資訊，包含其中的所有訊息。

**參數：**

| 參數名稱 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `id` | String | 要檢索的對話 ID。 | 是 |

**使用範例：**

```json
{
  "id": "123456789"
}
```

**預期輸出：** 返回一個包含完整對話歷史與狀態的 JSON 物件。

---

#### `list_conversations`

**功能說明：** 檢索符合特定條件的對話列表。

**參數：**

| 參數名稱 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `admin_id` | String | 篩選指派給特定管理員的對話。 | 否 |
| `state` | String | 篩選對話狀態，可為 `open`, `closed`, `snoozed`。 | 否 |

**使用範例：**

```json
{
  "admin_id": "98765",
  "state": "open"
}
```

**預期輸出：** 返回一個包含對話列表的 JSON 物件，支援分頁。

---

#### `add_note_to_conversation`

**功能說明：** 為現有的對話添加一則內部註記，僅團隊成員可見。

**參數：**

| 參數名稱 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `conversation_id` | String | 要添加註記的對話 ID。 | 是 |
| `admin_id` | String | 添加註記的管理員 ID。 | 是 |
| `body` | String | 註記的內容。 | 是 |

**使用範例：**

```json
{
  "conversation_id": "123456789",
  "admin_id": "98765",
  "body": "This user seems to be a high-value customer. Prioritize their issue."
}
```

**預期輸出：** 返回一個包含新建立註記的 JSON 物件。

---

#### `tag_conversation`

**功能說明：** 為一個對話添加一個或多個標籤，以便分類和建立報表。

**參數：**

| 參數名稱 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `conversation_id` | String | 要標記的對話 ID。 | 是 |
| `tag_name` | String | 要添加的標籤名稱。 | 是 |
| `admin_id` | String | 執行標記操作的管理員 ID。 | 是 |

**使用範例：**

```json
{
  "conversation_id": "123456789",
  "tag_name": "billing-issue",
  "admin_id": "98765"
}
```

**預期輸出：** 返回一個包含更新後標籤列表的對話物件。

### 2.3 公司管理 (Companies)

#### `create_company`

**功能說明：** 在 Intercom 中建立一個新的公司。

**參數：**

| 參數名稱 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `name` | String | 公司的名稱。 | 是 |
| `company_id` | String | 公司在您系統中的唯一 ID。 | 是 |
| `monthly_spend` | Number | 公司的月度支出。 | 否 |
| `custom_attributes` | Object | 一個包含自訂資料的鍵值對物件。 | 否 |

**使用範例：**

```json
{
  "name": "Example Corp",
  "company_id": "xyz-789",
  "monthly_spend": 5000
}
```

**預期輸出：** 返回一個包含新建立公司完整資訊的 JSON 物件。

---

#### `get_company`

**功能說明：** 根據指定的唯一識別碼，檢索單一公司的詳細資訊。

**參數：**

| 參數名稱 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `id` | String | Intercom 中公司的唯一 ID。 | 擇一 |
| `company_id` | String | 公司在您系統中的唯一 ID。 | 擇一 |

**使用範例：**

```json
{
  "company_id": "xyz-789"
}
```

**預期輸出：** 返回一個包含指定公司詳細資訊的 JSON 物件。

---

#### `list_companies`

**功能說明：** 檢索所有公司的列表。

**參數：** 無

**使用範例：**

```json
{}
```

**預期輸出：** 返回一個包含公司列表的 JSON 物件，支援分頁。

### 2.4 知識庫 (Help Center)

#### `search_articles`

**功能說明：** 在您的 Intercom 知識庫 (Help Center) 中搜尋文章。

**參數：**

| 參數名稱 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `query` | String | 用於搜尋的關鍵字或問題。 | 是 |

**使用範例：**

```json
{
  "query": "how to reset password"
}
```

**預期輸出：** 返回一個包含最相關的文章列表的 JSON 物件，每篇文章包含標題、摘要和連結。
