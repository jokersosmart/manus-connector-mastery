# HubSpot MCP 連接器使用說明報告

本報告詳細介紹了 HubSpot MCP 連接器提供的所有可用工具，包括其功能、參數、使用範例及預期輸出，旨在幫助使用者充分利用此連接器，透過自然語言與 HubSpot CRM 數據進行高效互動。

作者：Joker

---

## 總覽

HubSpot MCP 連接器目前提供對 HubSpot CRM 數據的 **唯讀** 存取。其核心是圍繞 HubSpot 的 CRM 搜尋 API 構建的，讓使用者能夠以靈活的方式查詢各類 CRM 物件。所有工具的調用都遵循 Manus 的自然語言處理邏輯，將使用者的指令轉換為結構化的 API 請求。

目前，連接器主要提供兩大核心工具，涵蓋了從廣泛搜尋到精確查找的全部功能。

## 工具詳解

### 1. `search_crm_objects`

此工具是連接器的主要入口，允許使用者根據特定條件在一個或多個 CRM 物件類型中進行搜尋。

#### 功能說明

`search_crm_objects` 工具能夠根據使用者提供的篩選條件、排序規則和查詢字串，對指定的 HubSpot CRM 物件執行搜尋操作。這是一個功能強大的工具，可以滿足大部分的數據查詢需求。

#### 參數詳解

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `object_types` | Array of Strings | 是 | 指定要查詢的 CRM 物件類型。有效值包括：`contacts`, `companies`, `deals`, `tickets`, `carts`, `products`, `orders`, `line_items`, `invoices`, `quotes`, `subscriptions`。 |
| `query` | String | 否 | 自然語言查詢字串，用於在物件的預設屬性中進行全文搜尋。例如 "active deal in Boston"。 |
| `filters` | Array of Objects | 否 | 一組精確的篩選條件，用於過濾結果。每個物件包含 `property_name`, `operator`, `value`。 |
| `sorts` | Array of Objects | 否 | 指定結果的排序方式。每個物件包含 `property_name` 和 `direction` (`ascending` 或 `descending`)。 |
| `limit` | Integer | 否 | 返回結果的最大數量，預設為 10。 |

#### 使用範例

**自然語言指令：**

> 「幫我找一下在波士頓地區，過去一個月內建立的，金額超過 5000 美元的進行中交易，並按金額從高到低排序。」

**Manus 轉換後的工具調用 (示意)：**

```json
{
  "tool_name": "search_crm_objects",
  "parameters": {
    "object_types": ["deals"],
    "query": "in Boston",
    "filters": [
      {
        "property_name": "createdate",
        "operator": "GTE",
        "value": "2026-01-24T00:00:00Z" 
      },
      {
        "property_name": "amount",
        "operator": "GT",
        "value": "5000"
      },
      {
        "property_name": "dealstage",
        "operator": "EQ",
        "value": "presentationscheduled"
      }
    ],
    "sorts": [
      {
        "property_name": "amount",
        "direction": "descending"
      }
    ],
    "limit": 10
  }
}
```

#### 預期輸出

工具將返回一個包含符合條件的交易物件陣列，每個物件包含其詳細屬性。

```json
{
  "status": "success",
  "data": [
    {
      "object_id": "deal-12345",
      "properties": {
        "dealname": "大型企業網站重構專案",
        "amount": "15000",
        "dealstage": "presentationscheduled",
        "createdate": "2026-02-15T10:00:00Z",
        "hs_lastmodifieddate": "2026-02-20T14:30:00Z"
      }
    },
    {
      "object_id": "deal-67890",
      "properties": {
        "dealname": "年度市場行銷顧問合約",
        "amount": "12000",
        "dealstage": "presentationscheduled",
        "createdate": "2026-02-10T11:00:00Z",
        "hs_lastmodifieddate": "2026-02-18T09:00:00Z"
      }
    }
  ]
}
```

### 2. `get_object_details`

當使用者需要獲取某個特定 CRM 物件的詳細資訊時，可以使用此工具。

#### 功能說明

`get_object_details` 工具根據提供的物件 ID 和物件類型，精確地抓取單一 CRM 物件的全部屬性資料。

#### 參數詳解

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `object_type` | String | 是 | 指定要查詢的 CRM 物件類型。 |
| `object_id` | String | 是 | 要查詢的物件的唯一識別碼。 |
| `properties` | Array of Strings | 否 | 指定要返回的特定屬性列表。如果留空，則返回所有預設屬性。 |

#### 使用範例

**自然語言指令：**

> 「顯示一下 ID 為 `contact-98765` 的聯絡人的詳細資料，我需要他的姓名、電子郵件和電話號碼。」

**Manus 轉換後的工具調用 (示意)：**

```json
{
  "tool_name": "get_object_details",
  "parameters": {
    "object_type": "contacts",
    "object_id": "contact-98765",
    "properties": ["firstname", "lastname", "email", "phone"]
  }
}
```

#### 預期輸出

工具將返回一個包含指定聯絡人詳細資訊的物件。

```json
{
  "status": "success",
  "data": {
    "object_id": "contact-98765",
    "properties": {
      "firstname": "Alice",
      "lastname": "Williams",
      "email": "alice.williams@example.com",
      "phone": "(555) 123-4567"
    }
  }
}
```
