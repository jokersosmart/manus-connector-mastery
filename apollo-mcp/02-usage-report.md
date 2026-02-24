# 「Apollo」連結器使用說明報告

**作者：Joker**

本報告提供 Manus MCP「Apollo」連結器的詳細使用說明，涵蓋其所有可用工具 (tools) 的詳細參數、功能說明、使用範例和預期輸出。由於 Apollo 連結器是基於 GraphQL 的動態工具集，此處以一個常見的「電子商務後端」為例，展示其可能生成的工具。

---

## 核心概念

Apollo 連結器扮演一個橋樑的角色，將您現有的 GraphQL API 無縫轉換為 AI 模型可以理解和使用的 MCP 工具。這代表 AI (例如 Manus) 可以透過自然語言，直接查詢和操作您的後端資料，如同一個真人開發者在操作 API。

工具的生成主要有三種方式：
1.  **Schema Introspection**：AI 會自動分析您的 GraphQL Schema，並將所有的 Queries 和 Mutations 動態轉換為可用的工具。
2.  **Persisted Queries**：您可以預先定義並批准一組特定的 GraphQL 操作，只有這些操作會被暴露為工具，提供最高的安全性與控管能力。
3.  **Operation Files**：將 `.graphql` 檔案放在指定目錄中，Apollo MCP Server 會自動將其轉換為工具。

以下我們將以一個虛構的電商平台 API 為例，展示透過 **Schema Introspection** 可能產生的工具。

## 工具清單

### 查詢 (Queries)

#### 1. `getProduct`

- **功能說明**：根據提供的產品 ID，獲取單一產品的詳細資訊。
- **參數**：

| 參數名 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `id` | `String` | 是 | 產品的唯一識別碼。 |

- **使用範例**：

```json
{
  "tool_name": "apollo.getProduct",
  "parameters": {
    "id": "prod_12345"
  }
}
```

- **預期輸出**：

```json
{
  "product": {
    "id": "prod_12345",
    "name": "無線降噪耳機",
    "description": "提供沉浸式音質體驗，電池續航長達 20 小時。",
    "price": 299.99,
    "currency": "USD",
    "stock": 150
  }
}
```

#### 2. `listProducts`

- **功能說明**：獲取產品列表，可選擇性地進行篩選和分頁。
- **參數**：

| 參數名 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `filter` | `Object` | 否 | 篩選條件，例如 `{"category": "electronics", "inStock": true}`。 |
| `limit` | `Integer` | 否 | 返回的產品數量上限，預設為 10。 |
| `offset` | `Integer` | 否 | 分頁偏移量，用於獲取後續頁面的產品。 |

- **使用範例**：

```json
{
  "tool_name": "apollo.listProducts",
  "parameters": {
    "filter": {
      "category": "audio",
      "minPrice": 100
    },
    "limit": 5
  }
}
```

- **預期輸出**：

```json
{
  "products": [
    {
      "id": "prod_12345",
      "name": "無線降噪耳機",
      "price": 299.99
    },
    {
      "id": "prod_67890",
      "name": "智慧型喇叭",
      "price": 129.00
    }
  ]
}
```

### 操作 (Mutations)

#### 1. `createOrder`

- **功能說明**：建立一筆新的訂單。
- **參數**：

| 參數名 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `input` | `Object` | 是 | 訂單的詳細資訊，包含 `userId` 和 `items` 陣列 (每個 item 包含 `productId` 和 `quantity`)。 |

- **使用範例**：

```json
{
  "tool_name": "apollo.createOrder",
  "parameters": {
    "input": {
      "userId": "user_abc",
      "items": [
        {
          "productId": "prod_12345",
          "quantity": 1
        },
        {
          "productId": "prod_67890",
          "quantity": 2
        }
      ]
    }
  }
}
```

- **預期輸出**：

```json
{
  "order": {
    "id": "order_zyx987",
    "status": "PENDING",
    "totalAmount": 558.99,
    "itemCount": 3
  }
}
```

#### 2. `updateInventory`

- **功能說明**：更新特定產品的庫存數量。
- **參數**：

| 參數名 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `productId` | `String` | 是 | 要更新庫存的產品 ID。 |
| `quantityChange` | `Integer` | 是 | 庫存的變化量，可以是正數 (入庫) 或負數 (出庫)。 |

- **使用範例**：

```json
{
  "tool_name": "apollo.updateInventory",
  "parameters": {
    "productId": "prod_12345",
    "quantityChange": -5
  }
}
```

- **預期輸出**：

```json
{
  "product": {
    "id": "prod_12345",
    "name": "無線降噪耳機",
    "stock": 145
  }
}
```
