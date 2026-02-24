# JSONBin.io 連結器使用說明報告

**作者：** Joker

---

本文檔旨在詳細說明 Manus MCP 連結器「JSONBin.io」的所有可用工具、參數、功能、使用範例及預期輸出，幫助使用者充分利用此連結器進行 JSON 資料的儲存與管理。

由於無法直接查詢 MCP 伺服器，本報告基於官方 API 文件推斷連結器的工具集。工具名稱和參數可能與實際情況略有出入，但核心功能保持一致。

## Bins API 工具集

Bins API 是最核心的工具集，用於對單個 JSON 記錄（稱為 Bin）進行增刪改查操作。

### 1. `create_bin`

- **功能說明：** 建立一個新的 JSON 記錄 (Bin)。可以設定為公開或私有，並可選擇性地加入一個集合 (Collection)。
- **參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `json_content` | string | 是 | 要儲存的 JSON 字串。 |
| `bin_name` | string | 否 | 為此 Bin 設定一個人類可讀的名稱。 |
| `is_private` | boolean | 否 | 設定為 `true` 以建立私有 Bin，預設為 `false` (公開)。 |
| `collection_id` | string | 否 | 將此 Bin 加入指定 ID 的集合中。 |

- **使用範例：**

```json
{
  "tool_name": "jsonbin.create_bin",
  "parameters": {
    "json_content": "{\"name\": \"John Doe\", \"age\": 30}",
    "bin_name": "user_profile_john",
    "is_private": true
  }
}
```

- **預期輸出：**

```json
{
  "success": true,
  "bin_id": "63f8b3b3e4f90735e832d3a1",
  "created_at": "2023-02-24T12:00:00.000Z",
  "is_private": true
}
```

### 2. `read_bin`

- **功能說明：** 讀取一個已存在的 JSON 記錄。
- **參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `bin_id` | string | 是 | 要讀取的 Bin 的唯一 ID。 |

- **使用範例：**

```json
{
  "tool_name": "jsonbin.read_bin",
  "parameters": {
    "bin_id": "63f8b3b3e4f90735e832d3a1"
  }
}
```

- **預期輸出：**

```json
{
  "name": "John Doe",
  "age": 30
}
```

### 3. `update_bin`

- **功能說明：** 更新一個已存在的 JSON 記錄。
- **參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `bin_id` | string | 是 | 要更新的 Bin 的唯一 ID。 |
| `json_content` | string | 是 | 新的 JSON 內容，將完全覆蓋舊內容。 |

- **使用範例：**

```json
{
  "tool_name": "jsonbin.update_bin",
  "parameters": {
    "bin_id": "63f8b3b3e4f90735e832d3a1",
    "json_content": "{\"name\": \"John Doe\", \"age\": 31, \"city\": \"New York\"}"
  }
}
```

- **預期輸出：**

```json
{
  "success": true,
  "bin_id": "63f8b3b3e4f90735e832d3a1",
  "updated_at": "2023-02-24T13:00:00.000Z",
  "data": {
    "name": "John Doe",
    "age": 31,
    "city": "New York"
  }
}
```

### 4. `delete_bin`

- **功能說明：** 刪除一個已存在的 JSON 記錄。
- **參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `bin_id` | string | 是 | 要刪除的 Bin 的唯一 ID。 |

- **使用範例：**

```json
{
  "tool_name": "jsonbin.delete_bin",
  "parameters": {
    "bin_id": "63f8b3b3e4f90735e832d3a1"
  }
}
```

- **預期輸出：**

```json
{
  "success": true,
  "message": "Bin deleted successfully."
}
```

## Collections API 工具集

Collections API 允許你將多個 Bins 組織到一個集合中，便於管理和批次操作。

### 1. `create_collection`

- **功能說明：** 建立一個新的集合 (Collection)。
- **參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `collection_name` | string | 是 | 為此集合設定一個人類可讀的名稱。 |

- **使用範例：**

```json
{
  "tool_name": "jsonbin.create_collection",
  "parameters": {
    "collection_name": "User Profiles"
  }
}
```

- **預期輸出：**

```json
{
  "success": true,
  "collection_id": "c1a2b3d4e5f6",
  "created_at": "2023-02-25T10:00:00.000Z"
}
```

### 2. `list_bins_in_collection`

- **功能說明：** 列出一個集合中所有的 Bins。
- **參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `collection_id` | string | 是 | 要查詢的集合的唯一 ID。 |

- **使用範例：**

```json
{
  "tool_name": "jsonbin.list_bins_in_collection",
  "parameters": {
    "collection_id": "c1a2b3d4e5f6"
  }
}
```

- **預期輸出：**

```json
[
  {
    "bin_id": "63f8b3b3e4f90735e832d3a1",
    "bin_name": "user_profile_john",
    "created_at": "2023-02-24T12:00:00.000Z"
  },
  {
    "bin_id": "63f8b4cde4f90735e832d3b2",
    "bin_name": "user_profile_jane",
    "created_at": "2023-02-24T12:05:00.000Z"
  }
]
```
