# 「Cloudflare API」連結器使用說明報告

**作者：Joker**

---

本報告提供 Manus MCP「Cloudflare API」連結器所有可用工具的詳細使用說明，旨在幫助使用者全面了解其功能並快速應用於實際工作中。

## 總覽

此連結器提供了一系列工具，讓使用者可以透過自然語言指令，直接管理與操作其 Cloudflare 帳戶下的各種資源，包含帳戶管理、KV 儲存、Workers、R2 儲存、D1 資料庫、Hyperdrive 以及文件搜尋等。

## 通用設定

在使用大部分需要指定帳戶資源的工具前，建議先設定當前要操作的 Cloudflare 帳戶。

### `set_active_account`

設定後續 API 調用所使用的有效 Cloudflare 帳戶 ID。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `activeAccountIdParam` | string | 是 | 您 Cloudflare 帳戶中的帳戶 ID。 |

**使用範例：**

```json
{
  "activeAccountIdParam": "YOUR_CLOUDFLARE_ACCOUNT_ID"
}
```

**預期輸出：**

成功設定有效帳戶的確認訊息。

---

## 帳戶管理 (Accounts)

### `accounts_list`

列出您 Cloudflare 帳戶下所有可用的帳戶資訊。

*本工具無需任何參數。*

**使用範例：**

```json
{}
```

**預期輸出：**

一個包含您所有 Cloudflare 帳戶列表的 JSON 物件，每個物件包含帳戶 ID 和名稱。

---

## Workers KV (Key-Value Storage)

Workers KV 是一個全域、低延遲的鍵值資料儲存服務。

### `kv_namespaces_list`

列出帳戶中所有的 KV 命名空間。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `page` | integer | 否 | 結果的頁碼 (從 1 開始)。 |
| `per_page` | integer | 否 | 每頁的命名空間數量 (1-100)。 |
| `order` | string | 否 | 排序欄位 (id/title)。 |
| `direction` | string | 否 | 排序方向 (asc/desc)。 |

**使用範例：**

```json
{
  "per_page": 5,
  "order": "title"
}
```

**預期輸出：**

一個包含 KV 命名空間列表的 JSON 物件，包含其 ID 和標題。

### `kv_namespace_create`

建立一個新的 KV 命名空間。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `title` | string | 是 | 人類可讀的命名空間名稱/標題。 |

**使用範例：**

```json
{
  "title": "my-new-kv-namespace"
}
```

**預期輸出：**

成功建立的 KV 命名空間的詳細資訊。

### `kv_namespace_delete`

刪除一個 KV 命名空間。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `namespace_id` | string | 是 | 要刪除的 KV 命名空間的 ID。 |

**使用範例：**

```json
{
  "namespace_id": "NAMESPACE_ID_TO_DELETE"
}
```

**預期輸出：**

操作成功的確認訊息。

---

## Cloudflare Workers

Cloudflare Workers 允許您在 Cloudflare 的全球網路上執行 JavaScript、Rust、C 和 C++ 程式碼。

### `workers_list`

列出帳戶中所有的 Workers。

*本工具無需任何參數。*

**使用範例：**

```json
{}
```

**預期輸出：**

一個包含您所有 Worker 列表的 JSON 物件。

### `workers_get_worker`

獲取特定 Cloudflare Worker 的詳細資訊。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `scriptName` | string | 是 | 要檢索的 Worker 腳本名稱。 |

**使用範例：**

```json
{
  "scriptName": "my-worker"
}
```

**預期輸出：**

指定 Worker 的詳細設定與中繼資料。

### `workers_get_worker_code`

獲取特定 Cloudflare Worker 的原始碼。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `scriptName` | string | 是 | 要檢索的 Worker 腳本名稱。 |

**使用範例：**

```json
{
  "scriptName": "my-worker"
}
```

**預期輸出：**

指定 Worker 的原始程式碼字串。

---

## R2 (Object Storage)

R2 提供與 S3 相容的物件儲存服務，主要優勢是零出口費用。

### `r2_buckets_list`

列出帳戶中所有的 R2 儲存桶。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `name_contains` | string | 否 | 儲存桶名稱過濾條件。 |
| `start_after` | string | 否 | 從指定的儲存桶名稱之後開始列出。 |
| `cursor` | string | 否 | 上次 API 回傳的分頁游標。 |

**使用範例：**

```json
{
  "name_contains": "production"
}
```

**預期輸出：**

一個包含符合條件的 R2 儲存桶列表的 JSON 物件。

### `r2_bucket_create`

建立一個新的 R2 儲存桶。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `name` | string | 是 | 新儲存桶的名稱。 |

**使用範例：**

```json
{
  "name": "my-new-bucket"
}
```

**預期輸出：**

操作成功的確認訊息。

### `r2_bucket_delete`

刪除一個 R2 儲存桶。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `name` | string | 是 | 要刪除的儲存桶名稱。 |

**使用範例：**

```json
{
  "name": "BUCKET_NAME_TO_DELETE"
}
```

**預期輸出：**

操作成功的確認訊息。

---

## D1 (Serverless SQL Database)

D1 是 Cloudflare 的原生無伺服器 SQL 資料庫。

### `d1_databases_list`

列出帳戶中所有的 D1 資料庫。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `name` | string | 否 | 資料庫名稱過濾條件。 |

**使用範例：**

```json
{
  "name": "user-data"
}
```

**預期輸出：**

一個包含 D1 資料庫列表的 JSON 物件。

### `d1_database_create`

建立一個新的 D1 資料庫。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `name` | string | 是 | 新資料庫的名稱。 |

**使用範例：**

```json
{
  "name": "my-new-database"
}
```

**預期輸出：**

成功建立的 D1 資料庫的詳細資訊。

### `d1_database_delete`

刪除一個 D1 資料庫。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `database_id` | string | 是 | 要刪除的 D1 資料庫的 ID。 |

**使用範例：**

```json
{
  "database_id": "DATABASE_ID_TO_DELETE"
}
```

**預期輸出：**

操作成功的確認訊息。

### `d1_database_query`

對指定的 D1 資料庫執行 SQL 查詢。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `database_id` | string | 是 | D1 資料庫的 ID。 |
| `sql` | string | 是 | 要執行的 SQL 查詢語句。 |
| `params` | array | 否 | SQL 查詢的參數化綁定。 |

**使用範例：**

```json
{
  "database_id": "YOUR_DATABASE_ID",
  "sql": "SELECT * FROM users WHERE id = ?;",
  "params": [1]
}
```

**預期輸出：**

SQL 查詢的結果集。

---

## Hyperdrive

Hyperdrive 讓您能夠將現有的區域性資料庫，轉變為全球分佈式的資料庫，加速查詢效能。

### `hyperdrive_configs_list`

列出帳戶中所有的 Hyperdrive 設定。

*本工具無需任何參數。*

**使用範例：**

```json
{}
```

**預期輸出：**

一個包含 Hyperdrive 設定列表的 JSON 物件。

### `hyperdrive_config_get`

獲取特定 Hyperdrive 設定的詳細資訊。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `hyperdrive_id` | string | 是 | Hyperdrive 設定的 ID。 |

**使用範例：**

```json
{
  "hyperdrive_id": "YOUR_HYPERDRIVE_ID"
}
```

**預期輸出：**

指定 Hyperdrive 設定的詳細資訊。

### `hyperdrive_config_delete`

刪除一個 Hyperdrive 設定。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `hyperdrive_id` | string | 是 | 要刪除的 Hyperdrive 設定的 ID。 |

**使用範例：**

```json
{
  "hyperdrive_id": "HYPERDRIVE_ID_TO_DELETE"
}
```

**預期輸出：**

操作成功的確認訊息。

---

## 文件與指南 (Documentation & Guides)

### `search_cloudflare_documentation`

搜尋 Cloudflare 官方文件。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `query` | string | 是 | 您要查詢的關鍵字或問題。 |

**使用範例：**

```json
{
  "query": "How to set up a firewall rule?"
}
```

**預期輸出：**

與查詢語意相關的文件片段列表。

### `migrate_pages_to_workers_guide`

提供將 Cloudflare Pages 專案遷移到 Cloudflare Workers 的官方指南。

*本工具無需任何參數。*

**使用範例：**

```json
{}
```

**預期輸出：**

一份詳細的遷移指南文件內容。
