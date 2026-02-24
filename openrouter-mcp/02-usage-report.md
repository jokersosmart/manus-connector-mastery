# 「Airtable」連結器使用說明報告

**作者：Joker**

---

## 1. 總覽

本文件詳細介紹了 Manus MCP 中「Airtable」連結器的所有可用工具、參數、功能及使用範例。此連結器旨在讓使用者能透過自然語言，無縫地與 Airtable 的資料庫進行互動，實現資料的查詢、新增、修改和刪除等操作。

**重要提示**：由於目前環境中未直接配置 Airtable MCP 伺服器，本報告基於 Airtable 官方 REST API 文件 [1] 模擬了一套標準的工具集。這些工具代表了在一個完整的 Airtable MCP 連結器中應具備的核心功能。

## 2. 核心工具詳解

以下是模擬的 Airtable 連結器所包含的核心工具及其詳細說明。

### 2.1. `list_records`

此工具用於從指定的 Airtable 表格中檢索多筆紀錄。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `base_id` | string | 是 | Airtable Base 的唯一識別碼。 |
| `table_id` | string | 是 | Airtable Table 的唯一識別碼或名稱。 |
| `fields` | array | 否 | 一個包含欄位名稱的陣列，用於指定要回傳的特定欄位。如果留空，則回傳所有欄位。 |
| `filter_by_formula` | string | 否 | 一個遵循 Airtable 公式語法的過濾條件，用於篩選紀錄。例如：`"({Status} = 'Done')"`。 |
| `max_records` | integer | 否 | 指定回傳的最大紀錄筆數，預設為 100。 |
| `sort` | array | 否 | 一個物件陣列，用於指定排序的欄位和方向。例如：`[{"field": "Name", "direction": "asc"}]`。 |

**使用範例**：

> 「幫我從專案管理 base 的『任務』表格中，找出所有狀態是『進行中』的任務，並按截止日期升序排序。」

**預期 Manus 調用**：

```json
{
  "tool": "list_records",
  "parameters": {
    "base_id": "appXXXXXXXXXXXXXX",
    "table_id": "tblXXXXXXXXXXXXXX",
    "filter_by_formula": "{Status} = '進行中'",
    "sort": [
      {
        "field": "截止日期",
        "direction": "asc"
      }
    ]
  }
}
```

**預期輸出**：

一個包含符合條件的紀錄物件陣列。

### 2.2. `create_record`

此工具用於在指定的 Airtable 表格中建立一筆或多筆新紀錄。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `base_id` | string | 是 | Airtable Base 的唯一識別碼。 |
| `table_id` | string | 是 | Airtable Table 的唯一識別碼或名稱。 |
| `records` | array | 是 | 一個包含要新增紀錄的物件陣列，每個物件的鍵值對應表格的欄位和內容。 |

**使用範例**：

> 「在客戶關係管理 base 的『聯絡人』表格中，新增一筆客戶資料：姓名是『張三』，公司是『ABC 公司』，電話是『0912-345-678』。」

**預期 Manus 調用**：

```json
{
  "tool": "create_record",
  "parameters": {
    "base_id": "appYYYYYYYYYYYYYY",
    "table_id": "tblYYYYYYYYYYYYYY",
    "records": [
      {
        "fields": {
          "姓名": "張三",
          "公司": "ABC 公司",
          "電話": "0912-345-678"
        }
      }
    ]
  }
}
```

**預期輸出**：

一個包含新增紀錄的 ID 和欄位內容的物件。

### 2.3. `get_record`

此工具用於根據紀錄的唯一 ID，從指定的 Airtable 表格中檢索單筆紀錄。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `base_id` | string | 是 | Airtable Base 的唯一識別碼。 |
| `table_id` | string | 是 | Airtable Table 的唯一識別碼或名稱。 |
| `record_id` | string | 是 | 要檢索紀錄的唯一識別碼。 |

**使用範例**：

> 「幫我找一下紀錄 ID 是『recZZZZZZZZZZZZZZ』的任務詳情。」

**預期 Manus 調用**：

```json
{
  "tool": "get_record",
  "parameters": {
    "base_id": "appXXXXXXXXXXXXXX",
    "table_id": "tblXXXXXXXXXXXXXX",
    "record_id": "recZZZZZZZZZZZZZZ"
  }
}
```

**預期輸出**：

一個包含該筆紀錄所有欄位內容的物件。

### 2.4. `update_record`

此工具用於更新指定 Airtable 表格中的一筆或多筆紀錄。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `base_id` | string | 是 | Airtable Base 的唯一識別碼。 |
| `table_id` | string | 是 | Airtable Table 的唯一識別碼或名稱。 |
| `records` | array | 是 | 一個物件陣列，每個物件包含要更新紀錄的 `id` 和要修改的 `fields`。 |

**使用範例**：

> 「將任務 ID 為『recZZZZZZZZZZZZZZ』的任務狀態更新為『已完成』。」

**預期 Manus 調用**：

```json
{
  "tool": "update_record",
  "parameters": {
    "base_id": "appXXXXXXXXXXXXXX",
    "table_id": "tblXXXXXXXXXXXXXX",
    "records": [
      {
        "id": "recZZZZZZZZZZZZZZ",
        "fields": {
          "Status": "已完成"
        }
      }
    ]
  }
}
```

**預期輸出**：

一個包含更新後紀錄內容的物件。

### 2.5. `delete_record`

此工具用於從指定的 Airtable 表格中刪除一筆或多筆紀錄。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `base_id` | string | 是 | Airtable Base 的唯一識別碼。 |
| `table_id` | string | 是 | Airtable Table 的唯一識別碼或名稱。 |
| `record_ids` | array | 是 | 一個包含要刪除紀錄 ID 的字串陣列。 |

**使用範例**：

> 「幫我把 ID 是『recAAAAAAAAAAAAAA』的任務刪掉。」

**預期 Manus 調用**：

```json
{
  "tool": "delete_record",
  "parameters": {
    "base_id": "appXXXXXXXXXXXXXX",
    "table_id": "tblXXXXXXXXXXXXXX",
    "record_ids": ["recAAAAAAAAAAAAAA"]
  }
}
```

**預期輸出**：

一個確認刪除成功的訊息。

---

## 3. 參考資料

[1] Airtable. (n.d.). *Airtable Web API Introduction*. Retrieved from https://www.airtable.com/developers/web/api/introduction
