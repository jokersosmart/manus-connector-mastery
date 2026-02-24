# Typeform 連結器使用說明報告

**作者：Joker**

## 總覽

Typeform 連結器允許 Manus 與您的 Typeform 帳戶進行互動，實現表單的建立、管理以及回應的檢視。本報告將詳細介紹連結器提供的所有工具、其參數、功能、使用範例及預期輸出。

## 可用工具

Typeform 連結器主要透過其 `Create API` 和 `Responses API` 提供服務。以下是基於這些 API 核心功能所設計的工具：

### 1. 建立表單 (create_form)

此工具對應 Typeform 的 `Create API`，讓使用者能以程式化的方式建立新的表單。

| 參數 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `title` | `string` | 新表單的標題。 | 是 |
| `fields` | `array` | 表單中的問題欄位陣列。 | 是 |
| `theme` | `string` | (可選) 要套用的主題 ID。 | 否 |
| `settings` | `object` | (可選) 表單的進階設定。 | 否 |

**功能說明：**

使用者可以透過定義一個包含標題和欄位結構的 JSON 物件來建立一個新的 Typeform 表單。欄位可以是各種 Typeform 支援的類型，例如「Short Text」、「Multiple Choice」等。

**使用範例：**

```json
{
  "title": "客戶滿意度調查",
  "fields": [
    {
      "title": "請問您對我們的服務有多滿意？",
      "type": "rating",
      "properties": {
        "steps": 5,
        "shape": "star"
      }
    },
    {
      "title": "您有任何建議嗎？",
      "type": "long_text"
    }
  ]
}
```

**預期輸出：**

成功執行後，API 將返回一個包含新建立表單 ID 和其他詳細資訊的 JSON 物件。

### 2. 取得表單列表 (list_forms)

此工具讓使用者能獲取其帳戶下所有表單的列表。

| 參數 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `search` | `string` | (可選) 用於搜尋表單標題的關鍵字。 | 否 |
| `page` | `integer` | (可選) 結果的分頁頁碼。 | 否 |
| `page_size` | `integer` | (可選) 每頁顯示的表單數量。 | 否 |

**功能說明：**

使用者可以獲取其 Typeform 帳戶中所有表單的清單，並可選擇性地進行搜尋和分頁。

**使用範例：**

```json
{
  "page_size": 10
}
```

**預期輸出：**

API 將返回一個包含表單列表的 JSON 物件，其中每個表單物件都包含其 ID、標題和連結等資訊。

### 3. 取得表單回應 (get_responses)

此工具對應 Typeform 的 `Responses API`，用於檢視特定表單的已提交回應。

| 參數 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `form_id` | `string` | 要檢視回應的表單 ID。 | 是 |
| `since` | `string` | (可選) 只檢視在此時間戳之後提交的回應。 | 否 |
| `until` | `string` | (可選) 只檢視在此時間戳之前提交的回應。 | 否 |
| `completed` | `boolean` | (可選) 只檢視已完成的回應。 | 否 |

**功能說明：**

使用者可以檢視特定表單收集到的所有回應。可以透過時間範圍或完成狀態來篩選結果。

**使用範例：**

```json
{
  "form_id": "your_form_id",
  "completed": true
}
```

**預期輸出：**

API 將返回一個包含回應列表的 JSON 物件。每個回應物件都包含提交的答案、提交時間和使用者資訊等。

---

**參考資料:**

[1] Typeform. (n.d.). *Create API*. Retrieved from https://www.typeform.com/developers/create/
[2] Typeform. (n.d.). *Responses API*. Retrieved from https://www.typeform.com/developers/responses/
