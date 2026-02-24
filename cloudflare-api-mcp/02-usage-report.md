# Metabase 連結器使用說明報告

**作者：Joker**

---

## 1. 總覽

Metabase 連結器為 Manus 生態系統帶來了強大的商業智慧 (BI) 與數據視覺化能力。透過此連結器，使用者能夠以自然語言的方式，直接與您的 Metabase 服務進行互動，實現查詢數據、管理儀表板、探索洞察等一系列的自動化操作。這份報告將詳細介紹此連結器提供的所有工具、其參數、功能，並提供具體的使用範例。

## 2. 可用工具詳解

Metabase 連結器主要圍繞四大核心資源進行操作：**警報 (Alerts)**、**資料庫 (Databases)**、**指標 (Metrics)** 和 **問題 (Questions)**。以下將逐一介紹每個類別下的可用工具。

### 2.1. 警報 (Alerts)

警報功能讓您能夠監控您的數據，並在滿足特定條件時收到通知。例如，當銷售額低於某個閾值，或新註冊用戶數超過預期時，Metabase 可以自動發出警報。

#### 2.1.1. 取得單一警報 (Get an alert)

此工具用於根據指定的 ID 檢索單一警報的詳細資訊。

- **工具名稱**：`get_alert`
- **功能描述**：取得特定警報的完整設定與狀態。
- **參數**：

| 參數名稱 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `alert_id` | `integer` | 是 | 您想要檢索的警報的唯一識別碼。 |

- **使用範例**：

> 「幫我看看 ID 為 12345 的 Metabase 警報設定是什麼。」

- **預期輸出**：

一個 JSON 物件，包含該警報的所有詳細資訊，如名稱、觸發條件、通知頻道、建立時間等。

```json
{
  "id": 12345,
  "name": "每小時新用戶註冊數",
  "card": {
    "id": 567,
    "name": "New Users per Hour"
  },
  "alert_condition": "rows > 100",
  "channels": [...],
  "created_at": "2025-10-26T10:00:00Z"
}
```

#### 2.1.2. 取得多個警報 (Get many alerts)

此工具用於檢索與您的 Metabase 帳戶關聯的所有警報的清單。

- **工具名稱**：`get_many_alerts`
- **功能描述**：列出所有可用的警報。
- **參數**：無

- **使用範例**：

> 「列出我所有在 Metabase 上設定的警報。」

- **預期輸出**：

一個 JSON 陣列，其中每個物件都代表一個警報的摘要資訊。

```json
[
  {
    "id": 12345,
    "name": "每小時新用戶註冊數",
    "archived": false
  },
  {
    "id": 12346,
    "name": "每日銷售額低於預期",
    "archived": false
  }
]
```

### 2.2. 資料庫 (Databases)

Metabase 連接器允許您管理和查詢已連接的數據源。您可以列出所有可用的資料庫，或深入檢視特定資料庫的結構。

#### 2.2.1. 新增資料庫 (Add a database)

此工具用於將一個新的資料庫連接到您的 Metabase 實例。

- **工具名稱**：`add_database`
- **功能描述**：註冊一個新的數據源以供 Metabase 查詢。
- **參數**：

| 參數名稱 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `name` | `string` | 是 | 您為此資料庫指定的名稱。 |
| `engine` | `string` | 是 | 資料庫的類型，例如 `postgres`、`mysql`、`bigquery` 等。 |
| `details` | `object` | 是 | 一個包含連接詳細資訊的 JSON 物件，例如主機、端口、用戶名、密碼等。具體欄位取決於 `engine` 類型。 |

- **使用範例**：

> 「幫我在 Metabase 中新增一個名為 'Production Replica' 的 PostgreSQL 資料庫，主機是 'db.example.com'，用戶名是 'readonly'。」

- **預期輸出**：

一個 JSON 物件，包含新建立的資料庫的詳細資訊，包括其在 Metabase 中的唯一 ID。

```json
{
  "id": 23,
  "name": "Production Replica",
  "engine": "postgres",
  "is_sample": false
}
```

#### 2.2.2. 取得多個資料庫 (Get many databases)

此工具用於檢索您的 Metabase 實例中所有已設定的資料庫清單。

- **工具名稱**：`get_many_databases`
- **功能描述**：列出所有已連接的資料庫。
- **參數**：無

- **使用範例**：

> 「列出我所有在 Metabase 中設定的資料庫。」

- **預期輸出**：

一個 JSON 陣列，其中每個物件都代表一個資料庫的摘要資訊。

```json
[
  {
    "id": 1,
    "name": "Sample Database",
    "engine": "h2"
  },
  {
    "id": 23,
    "name": "Production Replica",
    "engine": "postgres"
  }
]
```

#### 2.2.3. 取得資料庫的欄位 (Get Fields of a database)

此工具用於檢索特定資料庫中所有資料表及其欄位的詳細清單。

- **工具名稱**：`get_database_fields`
- **功能描述**：取得指定資料庫的完整結構描述 (schema)。
- **參數**：

| 參數名稱 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `database_id` | `integer` | 是 | 您想要檢索其欄位的資料庫的唯一識別碼。 |

- **使用範例**：

> 「顯示 ID 為 23 的資料庫的所有資料表和欄位。」

- **預期輸出**：

一個 JSON 物件，詳細描述了該資料庫的結構，包括所有資料表和每個資料表中的欄位。

```json
{
  "db_id": 23,
  "tables": [
    {
      "id": 101,
      "name": "orders",
      "fields": [
        { "id": 1001, "name": "id", "base_type": "type/Integer" },
        { "id": 1002, "name": "user_id", "base_type": "type/Integer" },
        { "id": 1003, "name": "total", "base_type": "type/Float" }
      ]
    }
  ]
}
```

### 2.3. 指標 (Metrics)

指標是您在 Metabase 中定義的可重複使用的計算，通常代表關鍵績效指標 (KPI)。

#### 2.3.1. 取得單一指標 (Get a metric)

此工具用於根據指定的 ID 檢索單一指標的詳細資訊。

- **工具名稱**：`get_metric`
- **功能描述**：取得特定指標的定義與詳細資訊。
- **參數**：

| 參數名稱 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `metric_id` | `integer` | 是 | 您想要檢索的指標的唯一識別碼。 |

- **使用範例**：

> 「幫我看看 ID 為 201 的 Metabase 指標是什麼。」

- **預期輸出**：

一個 JSON 物件，包含該指標的所有詳細資訊。

```json
{
  "id": 201,
  "name": "Revenue per User",
  "description": "Total revenue divided by total number of users.",
  "table_id": 101,
  "definition": {
    "source-table": 101,
    "aggregation": [["/", ["sum", ["field", 1003, null]], ["count", ["field", 1002, null]]]],
  },
  "created_at": "2025-10-20T11:00:00Z"
}
```

#### 2.3.2. 取得多個指標 (Get many metrics)

此工具用於檢索您的 Metabase 實例中所有已定義的指標清單。

- **工具名稱**：`get_many_metrics`
- **功能描述**：列出所有可用的指標。
- **參數**：無

- **使用範例**：

> 「列出我所有在 Metabase 上建立的指標。」

- **預期輸出**：

一個 JSON 陣列，其中每個物件都代表一個指標的摘要資訊。

```json
[
  {
    "id": 201,
    "name": "Revenue per User"
  },
  {
    "id": 202,
    "name": "Average Order Value"
  }
]
```

### 2.4. 問題 (Questions)

「問題」是 Metabase 的核心，代表您對數據的查詢。它可以是一個簡單的表格、一個趨勢圖或是一個複雜的多步驟查詢。

#### 2.4.1. 取得單一問題 (Get a question)

此工具用於根據指定的 ID 檢索單一問題的詳細資訊。

- **工具名稱**：`get_question`
- **功能描述**：取得特定問題的結構與設定。
- **參數**：

| 參數名稱 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `question_id` | `integer` | 是 | 您想要檢索的問題的唯一識別碼 (在 API 中也稱為 `card_id`)。 |

- **使用範例**：

> 「顯示 ID 為 567 的 Metabase 問題的詳細設定。」

- **預期輸出**：

一個 JSON 物件，包含該問題的完整定義，包括其查詢語句、視覺化設定等。

```json
{
  "id": 567,
  "name": "New Users per Hour",
  "display": "line",
  "dataset_query": { ... },
  "visualization_settings": { ... }
}
```

#### 2.4.2. 取得多個問題 (Get many questions)

此工具用於檢索您的 Metabase 實例中所有已儲存的問題清單。

- **工具名稱**：`get_many_questions`
- **功能描述**：列出所有已儲存的問題。
- **參數**：無

- **使用範例**：

> 「列出我在 Metabase 上的所有問題。」

- **預期輸出**：

一個 JSON 陣列，其中每個物件都代表一個問題的摘要資訊。

```json
[
  {
    "id": 567,
    "name": "New Users per Hour",
    "collection_id": 5
  },
  {
    "id": 568,
    "name": "Sales by Product Category",
    "collection_id": 5
  }
]
```

#### 2.4.3. 取得問題的結果 (Get the results from a question)

此工具用於執行一個已儲存的問題，並檢索其查詢結果。

- **工具名稱**：`get_question_results`
- **功能描述**：執行一個問題並取得其數據結果。
- **參數**：

| 參數名稱 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `question_id` | `integer` | 是 | 您想要執行並取得其結果的問題的唯一識別碼。 |
| `parameters` | `array` | 否 | 如果問題包含參數，您可以在此提供參數值。這是一個物件陣列，每個物件包含 `type` 和 `value`。 |
| `export_format` | `string` | 否 | 指定匯出格式，例如 `csv` 或 `json`。預設為 `json`。 |

- **使用範例**：

> 「執行 ID 為 568 的 Metabase 問題，並告訴我結果。」
> 「把 ID 為 568 的 Metabase 問題的結果匯出成 CSV 格式。」

- **預期輸出**：

根據指定的格式，回傳查詢的數據結果。預設情況下，這是一個 JSON 物件，其中包含欄位資訊和數據列。

```json
{
  "data": {
    "cols": [
      { "display_name": "Category", "base_type": "type/Text" },
      { "display_name": "Total Sales", "base_type": "type/Float" }
    ],
    "rows": [
      ["Gadgets", 15000.50],
      ["Doohickeys", 25000.75]
    ]
  }
}
```
