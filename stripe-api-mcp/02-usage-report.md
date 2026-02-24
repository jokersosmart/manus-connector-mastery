> **免責聲明：** 由於無法直接透過 `manus-mcp-cli` 工具獲取 `stripe` 伺服器的工具定義，本文件中的工具參數、功能說明和範例，是基於 Stripe 官方 API 文件、公開資訊以及對 MCP 設計模式的推斷整理而成。所有內容僅供參考，實際行為可能與本文件描述有所出入。

# 「Stripe API」連結器使用說明報告

作者：Joker

## 總覽

本報告旨在詳細說明 Manus MCP「Stripe API」連結器所提供的所有可用工具 (tools)。每個工具的介紹都將包含其功能描述、詳細的參數說明、一個或多個使用範例，以及預期的輸出格式。

基於我們在第一階段的遞迴分析，本連結器的核心設計理念是「場景抽象」。它並非旨在提供 Stripe API 的一對一完整對應，而是將開發者在真實世界中最高頻、最繁瑣的任務，封裝成簡單、直觀的工具。這使得開發者可以透過自然語言，快速完成以往需要多步驟、複雜程式碼才能實現的操作。

## 工具清單

根據我們的研究，Stripe API 連結器目前提供以下工具：

| 資源 (Resource) | 工具名稱 (Tool Name) | 功能簡述 |
| :--- | :--- | :--- |
| Account | `get_stripe_account_info` | 獲取 Stripe 帳戶的基本資訊。 |
| Balance | `retrieve_balance` | 查詢帳戶的餘額。 |
| Coupons | `create_coupon` | 建立一個新的折扣券。 |
| | `list_coupons` | 列出現有的折扣券。 |
| Customers | `create_customer` | 建立一個新的客戶。 |
| | `list_customers` | 列出現有的客戶。 |
| Disputes | `list_disputes` | 列出支付爭議。 |
| | `update_dispute` | 更新一個支付爭議的資訊。 |
| Invoices | `create_invoice` | 建立一個新的發票。 |
| | `create_invoice_item` | 為發票新增一個項目。 |
| | `finalize_invoice` | 完成一張草稿發票。 |
| | `list_invoices` | 列出現有的發票。 |
| Payment Links | `create_payment_link` | 建立一個支付連結。 |
| PaymentIntents | `list_payment_intents` | 列出支付意圖。 |
| Prices | `create_price` | 建立一個新的價格。 |
| | `list_prices` | 列出現有的價格。 |
| Products | `create_product` | 建立一個新的產品。 |
| | `list_products` | 列出現有的產品。 |
| Refunds | `create_refund` | 建立一個退款。 |
| Subscriptions | `cancel_subscription` | 取消一個訂閱。 |
| | `list_subscriptions` | 列出現有的訂閱。 |
| | `update_subscription` | 更新一個訂閱。 |
| Search | `search_stripe_resources` | 搜尋 Stripe 資源。 |
| | `fetch_stripe_resources` | 獲取 Stripe 物件。 |
| | `search_stripe_documentation` | 搜尋 Stripe 文件。 |

---

## 工具詳解

### Account (帳戶)

#### get_stripe_account_info

*   **功能說明：**
    此工具用於檢索與您目前經過身份驗證的 Stripe 帳戶相關的詳細資訊。這對於驗證 API 金鑰是否有效，以及獲取帳戶的國家、預設貨幣、公司名稱等基本設定非常有用。

*   **參數：**
    此工具通常不需要任何參數。

*   **使用範例：**
    > "Show me my Stripe account details."
    > "Get my Stripe account information."

*   **預期輸出：**
    一個 JSON 物件，包含 Stripe 帳戶的詳細資訊，例如 `id`, `country`, `default_currency`, `business_name`, `email` 等。

    ```json
    {
      "id": "acct_1234567890",
      "country": "US",
      "default_currency": "usd",
      "business_name": "Manus Inc.",
      "email": "contact@manus.im"
      // ... and other account fields
    }
    ```

### Balance (餘額)

#### retrieve_balance

*   **功能說明：**
    此工具用於查詢您 Stripe 帳戶的目前餘額。餘額分為三個主要部分：`available` (可提現)、`pending` (待處理) 和 `connect_reserved` (為連結帳戶保留)。

*   **參數：**
    此工具通常不需要任何參數。

*   **使用範例：**
    > "What is my current Stripe balance?"
    > "Retrieve my Stripe account balance."

*   **預期輸出：**
    一個 JSON 物件，詳細列出不同類別下的餘額金額和貨幣。

    ```json
    {
      "available": [
        {
          "amount": 100000,
          "currency": "usd",
          "source_types": { ... }
        }
      ],
      "pending": [
        {
          "amount": 25000,
          "currency": "usd",
          "source_types": { ... }
        }
      ],
      "connect_reserved": [ ... ]
    }
    ```

### Coupons (折扣券)

#### create_coupon

*   **功能說明：**
    建立一個可用於訂單、發票或訂閱的折扣券。您可以設定折扣的百分比、固定金額、持續時間等。

*   **參數：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `percent_off` | number | 否 | 折扣的百分比，例如 `25` 代表 25%。`percent_off` 和 `amount_off` 必須提供其一。 |
| `amount_off` | integer | 否 | 折扣的固定金額，以最小貨幣單位表示（例如美分的 1000 代表 $10.00）。 |
| `currency` | string | `amount_off`存在時必須 | `amount_off` 的三字母 ISO 貨幣代碼。 |
| `duration` | string | 是 | 折扣券的持續時間。可為 `once`, `repeating`, 或 `forever`。 |
| `duration_in_months` | integer | `duration`為`repeating`時必須 | 如果 `duration` 是 `repeating`，此參數指定折扣持續的月數。 |
| `name` | string | 否 | 折扣券的內部名稱，方便您在儀表板中識別。 |

*   **使用範例：**
    > "Create a 20% off coupon that lasts forever."
    > "Generate a one-time $50 off coupon named 'SPRING2026'."

*   **預期輸出：**
    一個 JSON 物件，代表新建立的折扣券。

#### list_coupons

*   **功能說明：**
    列出您帳戶中已建立的折扣券。

*   **參數：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `limit` | integer | 否 | 回傳物件的數量限制，預設為 10。 |

*   **使用範例：**
    > "List my last 5 coupons."

*   **預期輸出：**
    一個包含折扣券物件列表的 JSON 物件。

### Customers (客戶)

#### create_customer

*   **功能說明：**
    在 Stripe 中建立一個新的客戶物件。您可以儲存客戶的 email、名稱、電話等資訊，以便後續建立收費或訂閱。

*   **參數：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `email` | string | 否 | 客戶的電子郵件地址。 |
| `name` | string | 否 | 客戶的全名或公司名稱。 |
| `phone` | string | 否 | 客戶的電話號碼。 |
| `description` | string | 否 | 對客戶的任意描述。 |

*   **使用範例：**
    > "Create a new customer with the email 'test@example.com' and name 'John Doe'."

*   **預期輸出：**
    一個 JSON 物件，代表新建立的客戶。

#### list_customers

*   **功能說明：**
    列出您帳戶中已建立的客戶。

*   **參數：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `limit` | integer | 否 | 回傳物件的數量限制，預設為 10。 |
| `email` | string | 否 | 根據電子郵件地址篩選客戶。 |

*   **使用範例：**
    > "List all customers."
    > "Find the customer with the email 'test@example.com'."

*   **預期輸出：**
    一個包含客戶物件列表的 JSON 物件。

### Payment Links (支付連結)

#### create_payment_link

*   **功能說明：**
    這是此連結器的核心「場景抽象」工具之一。它將建立 `Product`、`Price` 和 `PaymentLink` 的多個步驟，簡化為單一指令。您只需提供產品名稱和金額，即可得到一個可分享的支付連結，讓客戶直接付款。

*   **參數：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `product_name` | string | 是 | 您要銷售的產品或服務的名稱。 |
| `amount` | integer | 是 | 價格金額，以最小貨幣單位表示。 |
| `currency` | string | 是 | 三字母 ISO 貨幣代碼。 |
| `quantity_adjustable` | boolean | 否 | 是否允許客戶在支付頁面調整購買數量，預設為 `false`。 |

*   **使用範例：**
    > "Create a Stripe payment link for a product called 'My Awesome E-book' at $49."
    > "I need a payment link for a 'Consulting Session' at 150 USD, and allow customers to change the quantity."

*   **預期輸出：**
    一個 JSON 物件，代表新建立的支付連結，其中最重要的欄位是 `url`。

    ```json
    {
      "id": "plink_1234567890",
      "object": "payment_link",
      "active": true,
      "url": "https://buy.stripe.com/abcdef123456",
      // ... and other payment link fields
    }
    ```

### Products (產品)

#### create_product

*   **功能說明：**
    建立一個 `Product` 物件，代表您銷售的實體商品或服務。通常與 `create_price` 一起使用。

*   **參數：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `name` | string | 是 | 產品的名稱。 |
| `description` | string | 否 | 產品的描述。 |

*   **使用範例：**
    > "Create a new product called 'Premium Subscription'."

*   **預期輸出：**
    一個 JSON 物件，代表新建立的產品。

#### list_products

*   **功能說明：**
    列出您帳戶中已建立的產品。

*   **參數：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `limit` | integer | 否 | 回傳物件的數量限制，預設為 10。 |
| `active` | boolean | 否 | 只回傳啟用或禁用的產品。 |

*   **使用範例：**
    > "List all active products."

*   **預期輸出：**
    一個包含產品物件列表的 JSON 物件。

### Search (搜尋)

#### search_stripe_resources

*   **功能說明：**
    使用 [Stripe 搜尋查詢語言](https://stripe.com/docs/search#search-query-language) 在您的 Stripe 帳戶中搜尋各種資源，如客戶、收費、訂閱等。這是一個非常強大的工具，用於進階的資料查詢。

*   **參數：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `query` | string | 是 | 符合 Stripe 搜尋查詢語言的查詢字串。 |
| `limit` | integer | 否 | 回傳物件的數量限制，預設為 10。 |

*   **使用範例：**
    > "Search for all customers in the US with the name 'John'."
    > "Find all successful charges over $100 in the last 30 days."
    > (對應的 `query` 可能為：`"name~'John' AND metadata['country']:'US'"` 或 `"amount > 10000 AND status:'succeeded' AND created > 1672531200"`)

*   **預期輸出：**
    一個包含符合條件的資源物件列表的 JSON 物件。

#### search_stripe_documentation

*   **功能說明：**
    在 Stripe 的官方文件、指南和 API 參考中進行關鍵字搜尋。當您不確定如何使用某個 Stripe 功能或 API 時，這是一個非常有用的工具。

*   **參數：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `query` | string | 是 | 您要搜尋的關鍵字或問題。 |

*   **使用範例：**
    > "Search the Stripe docs for 'how to handle 3D Secure'."
    > "Find documentation on creating subscription schedules."

*   **預期輸出：**
    一個包含相關文件頁面標題、摘要和連結的列表。
