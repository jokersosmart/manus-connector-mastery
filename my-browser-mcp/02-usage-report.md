
# Cloudflare 連結器使用說明報告

**作者：Joker**

---

本文檔詳細介紹了 Manus MCP Cloudflare 連結器提供的所有可用工具。每個工具都包含其功能說明、詳細參數、使用範例和預期輸出，旨在幫助使用者全面了解並有效利用此連結器的各項功能。

## 1. 帳戶管理 (Account Management)

### 1.1. `accounts_list`

**功能說明：**

列出您 Cloudflare 帳戶中的所有帳戶 (accounts)。這通常是執行任何其他操作之前的第一步，因為許多後續的工具調用都需要指定一個帳戶 ID。

**參數說明：**

此工具不需要任何輸入參數。

**使用範例：**

```bash
manus-mcp-cli tool call accounts_list --server cloudflare --input '{}'
```

**預期輸出：**

```json
{
  "result": [
    {
      "id": "01a7362d577a6c3019a474fd6f485823",
      "name": "Joker's Account",
      "settings": {
        "default_nameservers": "brad.ns.cloudflare.com",
        "enforce_twofactor": false
      }
    }
  ]
}
```

### 1.2. `set_active_account`

**功能說明：**

設定當前操作的活動帳戶 (active account)。在擁有多個 Cloudflare 帳戶的情況下，此工具非常重要。設定後，後續所有需要 `accountId` 的工具調用將會自動使用此處設定的帳戶 ID，無需在每個指令中重複指定。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `activeAccountIdParam` | string | 是 | 您要設定為活動狀態的帳戶 ID。此 ID 可以透過 `accounts_list` 工具獲得。 |

**使用範例：**

```bash
# 將 '01a7362d577a6c3019a474fd6f485823' 設定為活動帳戶
manus-mcp-cli tool call set_active_account --server cloudflare --input '{
  "activeAccountIdParam": "01a7362d577a6c3019a474fd6f485823"
}'
```

**預期輸出：**

```json
{
  "result": {
    "message": "Active account has been set to 01a7362d577a6c3019a474fd6f485823"
  }
}
```

## 2. KV 命名空間管理 (KV Namespace Management)

Cloudflare Workers KV 是一個全域、低延遲、無限制的鍵值資料儲存。它支援在邊緣執行的 Workers，是儲存設定檔、功能標誌或 A/B 測試路由等需要快速讀取但不頻繁寫入的資料的理想選擇。

### 2.1. `kv_namespaces_list`

**功能說明：**

列出指定 Cloudflare 帳戶中的所有 KV 命名空間。命名空間是隔離不同應用程式資料的容器。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `per_page` | integer | 否 | 每頁顯示的命名空間數量，範圍為 1-100。 |
| `page` | integer | 否 | 結果的頁碼，從 1 開始。 |
| `order` | string | 否 | 排序欄位，可選值為 `id` 或 `title`。 |
| `direction` | string | 否 | 排序方向，可選值為 `asc` (升序) 或 `desc` (降序)。 |

**使用範例：**

```bash
# 列出帳戶中所有的 KV 命名空間
manus-mcp-cli tool call kv_namespaces_list --server cloudflare --input 
'{
  "params": {}
}'
```

**預期輸出：**

```json
{
  "result": [
    {
      "id": "0f2ac74b498b48028cb68387c421e279",
      "title": "MY_KV_NAMESPACE"
    },
    {
      "id": "1a3b8c2d5e7f4a01b4c6d8e9f0a1b2c3",
      "title": "staging_config"
    }
  ]
}
```

### 2.2. `kv_namespace_create`

**功能說明：**

在您的 Cloudflare 帳戶中建立一個新的 KV 命名空間。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `title` | string | 是 | KV 命名空間的人類可讀名稱。 |

**使用範例：**

```bash
# 建立一個名為 "production_settings" 的新 KV 命名空間
manus-mcp-cli tool call kv_namespace_create --server cloudflare --input 
'{
  "title": "production_settings"
}'
```

**預期輸出：**

```json
{
  "result": {
    "id": "d8b3d2a087174255a4356f4a2324034a",
    "title": "production_settings"
  }
}
```

### 2.3. `kv_namespace_delete`

**功能說明：**

刪除一個指定的 KV 命名空間。**請注意：此操作不可逆，所有儲存在該命名空間中的資料都將被永久刪除。**

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `namespace_id` | string | 是 | 要刪除的 KV 命名空間的 ID。 |

**使用範例：**

```bash
# 刪除 ID 為 "d8b3d2a087174255a4356f4a2324034a" 的 KV 命名空間
manus-mcp-cli tool call kv_namespace_delete --server cloudflare --input 
'{
  "namespace_id": "d8b3d2a087174255a4356f4a2324034a"
}'
```

**預期輸出：**

一個空的成功回應，表示刪除操作已成功執行。

```json
{
  "result": null
}
```

## 3. R2 儲存桶管理 (R2 Bucket Management)

Cloudflare R2 是一個與 Amazon S3 API 相容的物件儲存服務，它最大的特點是 **零出口費用 (zero egress fees)**。這意味著您可以自由地存取和分發您的資料，而無需擔心高昂的網路傳輸成本。R2 非常適合儲存大量非結構化資料，如圖片、影片、日誌檔案、備份等。

### 3.1. `r2_buckets_list`

**功能說明：**

列出您 Cloudflare 帳戶中的所有 R2 儲存桶 (buckets)。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `name_contains` | string | 否 | 篩選儲存桶名稱，只返回名稱包含此字串的儲存桶。 |
| `start_after` | string | 否 | 從指定的儲存桶名稱之後開始按字典順序列出。 |
| `per_page` | integer | 否 | 每頁顯示的儲存桶數量。 |
| `cursor` | string | 否 | 用於分頁的游標，從上一次 `r2_buckets_list` 調用的回應中獲取。 |
| `direction` | string | 否 | 排序方向，可選值為 `asc` (升序) 或 `desc` (降序)。 |

**使用範例：**

```bash
# 列出所有 R2 儲存桶
manus-mcp-cli tool call r2_buckets_list --server cloudflare --input 
'{
  "params": {}
}'
```

**預期輸出：**

```json
{
  "result": {
    "buckets": [
      {
        "name": "my-awesome-bucket",
        "creation_date": "2023-10-26T14:00:00.000Z"
      },
      {
        "name": "backup-logs",
        "creation_date": "2023-09-15T10:30:00.000Z"
      }
    ],
    "truncated": false,
    "cursor": ""
  }
}
```

### 3.2. `r2_bucket_create`

**功能說明：**

在您的 Cloudflare 帳戶中建立一個新的 R2 儲存桶。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `name` | string | 是 | 新儲存桶的名稱。必須是全域唯一的。 |

**使用範例：**

```bash
# 建立一個名為 "my-project-assets" 的新 R2 儲存桶
manus-mcp-cli tool call r2_bucket_create --server cloudflare --input 
'{
  "name": "my-project-assets"
}'
```

**預期輸出：**

```json
{
  "result": {
    "name": "my-project-assets",
    "creation_date": "2024-01-01T12:00:00.000Z"
  }
}
```

### 3.3. `r2_bucket_delete`

**功能說明：**

刪除一個指定的 R2 儲存桶。**請注意：此操作不可逆，儲存桶中的所有物件都將被永久刪除。**

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `name` | string | 是 | 要刪除的儲存桶的名稱。 |

**使用範例：**

```bash
# 刪除名為 "my-project-assets" 的 R2 儲存桶
manus-mcp-cli tool call r2_bucket_delete --server cloudflare --input 
'{
  "name": "my-project-assets"
}'
```

**預期輸出：**

一個空的成功回應，表示刪除操作已成功執行。

```json
{
  "result": null
}
```

## 4. D1 資料庫管理 (D1 Database Management)

Cloudflare D1 是 Cloudflare 的原生無伺服器資料庫。它基於 SQLite 技術，提供了簡單、可靠的 SQL 資料庫功能，並與 Workers 緊密整合，讓您可以在邊緣輕鬆地儲存和查詢結構化資料。

### 4.1. `d1_databases_list`

**功能說明：**

列出您 Cloudflare 帳戶中的所有 D1 資料庫。

**參數說明：**

此工具接受可選的分頁和篩選參數，如 `name`, `page`, `per_page`。

**使用範例：**

```bash
# 列出所有 D1 資料庫
manus-mcp-cli tool call d1_databases_list --server cloudflare --input 
'{
  "params": {}
}'
```

**預期輸出：**

```json
{
  "result": [
    {
      "uuid": "a1b2c3d4-e5f6-7890-1234-567890abcdef",
      "name": "my_app_db",
      "version": "beta"
    }
  ]
}
```

### 4.2. `d1_database_create`

**功能說明：**

在您的 Cloudflare 帳戶中建立一個新的 D1 資料庫。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `name` | string | 是 | 新資料庫的名稱。 |

**使用範例：**

```bash
# 建立一個名為 "analytics_db" 的新 D1 資料庫
manus-mcp-cli tool call d1_database_create --server cloudflare --input 
'{
  "name": "analytics_db"
}'
```

**預期輸出：**

```json
{
  "result": {
    "uuid": "b2c3d4e5-f6a7-8901-2345-67890abcdef1",
    "name": "analytics_db",
    "version": "beta"
  }
}
```

### 4.3. `d1_database_delete`

**功能說明：**

刪除一個指定的 D1 資料庫。**請注意：此操作不可逆，資料庫中的所有資料表和資料都將被永久刪除。**

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `database_id` | string | 是 | 要刪除的 D1 資料庫的 UUID。 |

**使用範例：**

```bash
# 刪除 ID 為 "b2c3d4e5-f6a7-8901-2345-67890abcdef1" 的 D1 資料庫
manus-mcp-cli tool call d1_database_delete --server cloudflare --input 
'{
  "database_id": "b2c3d4e5-f6a7-8901-2345-67890abcdef1"
}'
```

**預期輸出：**

一個空的成功回應。

### 4.4. `d1_database_query`

**功能說明：**

對指定的 D1 資料庫執行一條 SQL 查詢。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `database_id` | string | 是 | D1 資料庫的 UUID。 |
| `sql` | string | 是 | 要執行的 SQL 查詢語句。 |
| `params` | array | 否 | 用於 SQL 查詢的參數化綁定。 |

**使用範例：**

```bash
# 在資料庫中建立一個 users 資料表
manus-mcp-cli tool call d1_database_query --server cloudflare --input 
'{
  "database_id": "a1b2c3d4-e5f6-7890-1234-567890abcdef",
  "sql": "CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT, email TEXT);"
}'

# 插入一筆資料
manus-mcp-cli tool call d1_database_query --server cloudflare --input 
'{
  "database_id": "a1b2c3d4-e5f6-7890-1234-567890abcdef",
  "sql": "INSERT INTO users (name, email) VALUES (?, ?);",
  "params": ["Joker", "joker@manus.im"]
}'
```

**預期輸出：**

```json
{
  "result": {
    "results": [],
    "success": true,
    "meta": {
      "served_by": "d1-execute",
      "duration": 0.01,
      "changes": 1,
      "last_row_id": 1,
      "changed_db": true,
      "size_after": 1024,
      "rows_read": 0,
      "rows_written": 1
    }
  }
}
```

## 5. Workers 管理 (Workers Management)

Cloudflare Workers 提供了一個無伺服器執行環境，讓您可以在 Cloudflare 的全球網路上部署和執行 JavaScript/Wasm 程式碼，極大地減少了延遲並提升了性能。

### 5.1. `workers_list`

**功能說明：**

列出您 Cloudflare 帳戶中的所有 Workers 腳本。

**使用範例：**

```bash
manus-mcp-cli tool call workers_list --server cloudflare --input 
'{}'
```

### 5.2. `workers_get_worker`

**功能說明：**

獲取特定 Worker 腳本的詳細資訊。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `scriptName` | string | 是 | Worker 腳本的名稱。 |

**使用範例：**

```bash
manus-mcp-cli tool call workers_get_worker --server cloudflare --input 
'{
  "scriptName": "my-worker"
}'
```

### 5.3. `workers_get_worker_code`

**功能說明：**

獲取特定 Worker 腳本的原始碼。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `scriptName` | string | 是 | Worker 腳本的名稱。 |

**使用範例：**

```bash
manus-mcp-cli tool call workers_get_worker_code --server cloudflare --input 
'{
  "scriptName": "my-worker"
}'
```

## 6. 文件與指南 (Documentation & Guides)

### 6.1. `search_cloudflare_documentation`

**功能說明：**

在 Cloudflare 的官方文件中進行關鍵字搜尋。這是解決特定問題或學習新功能的最佳途徑。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `query` | string | 是 | 您要搜尋的關鍵字或問題。 |

**使用範例：**

```bash
# 搜尋關於 "Workers KV limits" 的文件
manus-mcp-cli tool call search_cloudflare_documentation --server cloudflare --input 
'{
  "query": "Workers KV limits"
}'
```

### 6.2. `migrate_pages_to_workers_guide`

**功能說明：**

提供一份將 Cloudflare Pages 專案遷移到 Cloudflare Workers 的官方指南。在進行此類遷移前，強烈建議閱讀此文件。

**使用範例：**

```bash
manus-mcp-cli tool call migrate_pages_to_workers_guide --server cloudflare --input 
'{}'
```
