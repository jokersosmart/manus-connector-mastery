# 「Stripe」連結器使用說明報告

**作者：Joker**

## 簡介

本報告提供 Manus MCP「Stripe」連結器所有可用工具的詳細使用說明。每個工具都包含其功能描述、參數詳解、JSON 格式的使用範例以及預期的輸出結果。本文件旨在幫助開發者和進階使用者充分利用 Stripe 連結器的全部功能，將其無縫整合到自動化工作流中。

---

## 資源：Account

### 1. get_stripe_account_info

- **功能說明**：檢索您 Stripe 帳戶的詳細資訊。這是一個唯讀操作，用於確認帳戶狀態、功能和設定。
- **參數**：此工具無需任何參數。
- **使用範例**：

  ```json
  {
    "tool": "get_stripe_account_info",
    "parameters": {}
  }
  ```

- **預期輸出**：一個包含帳戶詳細資訊的 JSON 物件，其中可能包含 `id`, `email`, `country`, `default_currency`, `business_profile`, `capabilities` 等欄位，完整描述了您的 Stripe 帳戶狀態。

---

## 資源：Balance

### 2. retrieve_balance

- **功能說明**：檢索您 Stripe 帳戶的即時餘額，包括可用餘額和待處理餘額。
- **參數**：此工具無需任何參數。
- **使用範例**：

  ```json
  {
    "tool": "retrieve_balance",
    "parameters": {}
  }
  ```

- **預期輸出**：一個包含餘額詳細資訊的 JSON 物件，主要包含 `available` 和 `pending` 兩個陣列，分別列出不同貨幣的可用和待處理金額。

---

## 資源：Coupon

### 3. create_coupon

- **功能說明**：建立一個新的折扣券 (Coupon)。您可以設定折扣的百分比、固定金額、持續時間以及兌換次數等。
- **參數**：
  - `percent_off` (float, 可選): 折扣的百分比，例如 `25.5` 代表 74.5 折。
  - `amount_off` (integer, 可選): 折扣的固定金額，以最小貨幣單位表示 (例如，10 美元應輸入 `1000`)。
  - `currency` (string, 如果 `amount_off` 被設定則為必選): 三個字母的 ISO 貨幣代碼。
  - `duration` (string, 必選): 折扣券的持續時間，可為 `once`, `repeating`, 或 `forever`。
  - `duration_in_months` (integer, 如果 `duration` 是 `repeating` 則為必選): 折扣重複的月數。
  - `name` (string, 可選): 折扣券的內部顯示名稱。
- **使用範例**：

  ```json
  {
    "tool": "create_coupon",
    "parameters": {
      "percent_off": 20,
      "duration": "once",
      "name": "20% Off Welcome Offer"
    }
  }
  ```

- **預期輸出**：一個代表新建立折扣券的 JSON 物件，包含其所有屬性。

### 4. list_coupons

- **功能說明**：列出您帳戶中所有已建立的折扣券。
- **參數**：
  - `limit` (integer, 可選): 回傳的物件數量上限，預設為 10，最大為 100。
  - `starting_after` (string, 可選): 用於分頁，傳入上一次請求回傳的最後一個物件 ID。
- **使用範例**：

  ```json
  {
    "tool": "list_coupons",
    "parameters": {
      "limit": 5
    }
  }
  ```

- **預期輸出**：一個包含折扣券列表的 JSON 物件。

---

## 資源：Customer

### 5. create_customer

- **功能說明**：建立一個新的客戶物件。您可以在後續操作中將其與支付、發票和訂閱關聯。
- **參數**：
  - `email` (string, 可選): 客戶的電子郵件地址。
  - `name` (string, 可選): 客戶的全名或公司名稱。
  - `description` (string, 可選): 對客戶的任意描述。
  - `payment_method` (string, 可選): 客戶的預設支付方式 ID。
- **使用範例**：

  ```json
  {
    "tool": "create_customer",
    "parameters": {
      "email": "customer@example.com",
      "name": "John Doe"
    }
  }
  ```

- **預期輸出**：一個代表新建立客戶的 JSON 物件。

### 6. list_customers

- **功能說明**：列出您帳戶中所有已建立的客戶。
- **參數**：
  - `limit` (integer, 可選): 回傳的物件數量上限，預設為 10，最大為 100。
  - `email` (string, 可選): 根據電子郵件地址篩選客戶。
  - `starting_after` (string, 可選): 用於分頁，傳入上一次請求回傳的最後一個物件 ID。
- **使用範例**：

  ```json
  {
    "tool": "list_customers",
    "parameters": {
      "limit": 3,
      "email": "customer@example.com"
    }
  }
  ```

- **預期輸出**：一個包含客戶列表的 JSON 物件。

---

## 資源：Dispute

### 7. list_disputes

- **功能說明**：列出您帳戶中所有的爭議款項。
- **參數**：
  - `limit` (integer, 可選): 回傳的物件數量上限，預設為 10，最大為 100。
  - `payment_intent` (string, 可選): 只回傳與特定 PaymentIntent 相關的爭議。
  - `starting_after` (string, 可選): 用於分頁。
- **使用範例**：

  ```json
  {
    "tool": "list_disputes",
    "parameters": {
      "limit": 5
    }
  }
  ```

- **預期輸出**：一個包含爭議款項列表的 JSON 物件。

### 8. update_dispute

- **功能說明**：更新一個爭議款項，通常用於提交證據以回應爭議。
- **參數**：
  - `dispute_id` (string, 必選): 要更新的爭議 ID。
  - `evidence` (dict, 可選): 包含反駁證據的物件。
  - `metadata` (dict, 可選): 用於儲存自訂鍵值對。
- **使用範例**：

  ```json
  {
    "tool": "update_dispute",
    "parameters": {
      "dispute_id": "dp_123456789",
      "evidence": {
        "customer_name": "John Doe"
      }
    }
  }
  ```

- **預期輸出**：一個代表更新後爭議的 JSON 物件。

---


## 資源：Invoice

### 9. create_invoice

- **功能說明**：建立一個新的發票草稿。建立後，您可以向其中添加發票項目 (Invoice Item)，然後再將其最終確定並發送給客戶。
- **參數**：
  - `customer` (string, 必選): 要為其開立發票的客戶 ID。
  - `description` (string, 可選): 發票的描述。
  - `collection_method` (string, 可選): `charge_automatically` 或 `send_invoice`，決定如何向客戶收款。
- **使用範例**：

  ```json
  {
    "tool": "create_invoice",
    "parameters": {
      "customer": "cus_123456789",
      "description": "Invoice for consulting services"
    }
  }
  ```

- **預期輸出**：一個代表新建立發票草稿的 JSON 物件。

### 10. create_invoice_item

- **功能說明**：為現有的發票草稿添加一個新的行項目。
- **參數**：
  - `customer` (string, 必選): 客戶 ID。
  - `invoice` (string, 可選): 要將此項目添加到的發票 ID。如果未提供，則會添加到該客戶的下一個待開發票中。
  - `amount` (integer, 必選): 項目的金額，以最小貨幣單位表示。
  - `currency` (string, 必選): 三個字母的 ISO 貨幣代碼。
  - `description` (string, 可選): 項目描述。
- **使用範例**：

  ```json
  {
    "tool": "create_invoice_item",
    "parameters": {
      "customer": "cus_123456789",
      "invoice": "in_123456789",
      "amount": 50000,
      "currency": "usd",
      "description": "5 hours of consulting"
    }
  }
  ```

- **預期輸出**：一個代表新建立發票項目的 JSON 物件。

### 11. finalize_invoice

- **功能說明**：最終確定一個發票草稿。Stripe 在此之後會凍結該發票，並根據其 `collection_method` 進行收款。
- **參數**：
  - `invoice_id` (string, 必選): 要最終確定的發票 ID。
- **使用範例**：

  ```json
  {
    "tool": "finalize_invoice",
    "parameters": {
      "invoice_id": "in_123456789"
    }
  }
  ```

- **預期輸出**：一個代表已最終確定發票的 JSON 物件。

### 12. list_invoices

- **功能說明**：列出您帳戶中的發票。
- **參數**：
  - `limit` (integer, 可選): 回傳的物件數量上限，預設為 10，最大為 100。
  - `customer` (string, 可選): 只回傳特定客戶的發票。
  - `status` (string, 可選): 只回傳特定狀態的發票 (`draft`, `open`, `paid`, `uncollectible`, `void`)。
- **使用範例**：

  ```json
  {
    "tool": "list_invoices",
    "parameters": {
      "limit": 10,
      "status": "paid"
    }
  }
  ```

- **預期輸出**：一個包含發票列表的 JSON 物件。

---

## 資源：Payment Link

### 13. create_payment_link

- **功能說明**：建立一個可重複使用的支付連結，您可以將此連結分享給多個客戶，讓他們透過一個簡單的頁面完成支付。
- **參數**：
  - `line_items` (list, 必選): 一個包含行項目的列表，每個行項目都必須有 `price` 和 `quantity`。
  - `metadata` (dict, 可選): 用於儲存自訂鍵值對。
- **使用範例**：

  ```json
  {
    "tool": "create_payment_link",
    "parameters": {
      "line_items": [
        {
          "price": "price_123456789",
          "quantity": 1
        }
      ]
    }
  }
  ```

- **預期輸出**：一個代表新建立支付連結的 JSON 物件，其中包含 `url` 欄位。

---

## 資源：PaymentIntent

### 14. list_payment_intents

- **功能說明**：列出您帳戶中的 PaymentIntents。PaymentIntent 物件代表了您向客戶收款的意圖，並追蹤了支付流程的生命週期。
- **參數**：
  - `limit` (integer, 可選): 回傳的物件數量上限，預設為 10，最大為 100。
  - `customer` (string, 可選): 只回傳特定客戶的 PaymentIntents。
  - `starting_after` (string, 可選): 用於分頁。
- **使用範例**：

  ```json
  {
    "tool": "list_payment_intents",
    "parameters": {
      "limit": 5,
      "customer": "cus_123456789"
    }
  }
  ```

- **預期輸出**：一個包含 PaymentIntent 列表的 JSON 物件。

---

## 資源：Price & Product

### 15. create_price

- **功能說明**：為一個產品建立一個新的價格方案。價格可以是固定的，也可以是按用量計費的。
- **參數**：
  - `unit_amount` (integer, 必選): 價格的單位金額，以最小貨幣單位表示。
  - `currency` (string, 必選): 三個字母的 ISO 貨幣代碼。
  - `product` (string, 可選): 此價格關聯的產品 ID。您也可以在建立價格時直接提供 `product_data` 來建立新產品。
  - `recurring` (dict, 可選): 如果是訂閱價格，則設定重複計費的規則，如 `{"interval": "month"}`。
- **使用範例**：

  ```json
  {
    "tool": "create_price",
    "parameters": {
      "unit_amount": 2000,
      "currency": "usd",
      "recurring": {
        "interval": "month"
      },
      "product_data": {
        "name": "Gold Plan"
      }
    }
  }
  ```

- **預期輸出**：一個代表新建立價格的 JSON 物件。

### 16. list_prices

- **功能說明**：列出您帳戶中的價格。
- **參數**：
  - `limit` (integer, 可選): 回傳的物件數量上限，預設為 10，最大為 100。
  - `product` (string, 可選): 只回傳特定產品的價格。
  - `type` (string, 可選): `one_time` 或 `recurring`，篩選價格類型。
- **使用範例**：

  ```json
  {
    "tool": "list_prices",
    "parameters": {
      "limit": 5,
      "type": "recurring"
    }
  }
  ```

- **預期輸出**：一個包含價格列表的 JSON 物件。

### 17. create_product

- **功能說明**：建立一個新的產品。產品是您銷售的商品或服務的表示。
- **參數**：
  - `name` (string, 必選): 產品的名稱。
  - `description` (string, 可選): 產品的描述。
  - `default_price_data` (dict, 可選): 在建立產品的同時為其建立一個預設價格。
- **使用範例**：

  ```json
  {
    "tool": "create_product",
    "parameters": {
      "name": "Premium Subscription",
      "description": "Monthly access to premium content"
    }
  }
  ```

- **預期輸出**：一個代表新建立產品的 JSON 物件。

### 18. list_products

- **功能說明**：列出您帳戶中的產品。
- **參數**：
  - `limit` (integer, 可選): 回傳的物件數量上限，預設為 10，最大為 100。
  - `active` (boolean, 可選): 只回傳啟用或未啟用的產品。
- **使用範例**：

  ```json
  {
    "tool": "list_products",
    "parameters": {
      "limit": 10,
      "active": true
    }
  }
  ```

- **預期輸出**：一個包含產品列表的 JSON 物件。

---

## 資源：Refund

### 19. create_refund

- **功能說明**：為已成功的一筆費用 (Charge) 建立退款。您可以選擇全額退款或部分退款。
- **參數**：
  - `charge` (string, 可選): 要退款的費用 ID。`charge` 或 `payment_intent` 必須提供其一。
  - `payment_intent` (string, 可選): 要退款的 PaymentIntent ID。
  - `amount` (integer, 可選): 退款金額，以最小貨幣單位表示。如果未提供，則為全額退款。
- **使用範例**：

  ```json
  {
    "tool": "create_refund",
    "parameters": {
      "payment_intent": "pi_123456789",
      "amount": 1000
    }
  }
  ```

- **預期輸出**：一個代表新建立退款的 JSON 物件。

---

## 資源：Subscription

### 20. cancel_subscription

- **功能說明**：取消一個客戶的訂閱。您可以選擇立即取消或在當前計費週期結束時取消。
- **參數**：
  - `subscription_id` (string, 必選): 要取消的訂閱 ID。
  - `invoice_now` (boolean, 可選): 是否立即為最後一個週期的用量開立發票，預設為 `false`。
- **使用範例**：

  ```json
  {
    "tool": "cancel_subscription",
    "parameters": {
      "subscription_id": "sub_123456789"
    }
  }
  ```

- **預期輸出**：一個代表已取消訂閱的 JSON 物件。

### 21. list_subscriptions

- **功能說明**：列出您帳戶中的訂閱。
- **參數**：
  - `limit` (integer, 可選): 回傳的物件數量上限，預設為 10，最大為 100。
  - `customer` (string, 可選): 只回傳特定客戶的訂閱。
  - `price` (string, 可選): 只回傳包含特定價格方案的訂閱。
  - `status` (string, 可選): `all`, `active`, `canceled`, `incomplete`, `trialing` 等。
- **使用範例**：

  ```json
  {
    "tool": "list_subscriptions",
    "parameters": {
      "limit": 5,
      "status": "active"
    }
  }
  ```

- **預期輸出**：一個包含訂閱列表的 JSON 物件。

### 22. update_subscription

- **功能說明**：更新一個現有的訂閱，例如更換方案、調整數量或修改稅率等。
- **參數**：
  - `subscription_id` (string, 必選): 要更新的訂閱 ID。
  - `items` (list, 可選): 一個包含要更新的訂閱項目列表，可用於更換方案或數量。
  - `cancel_at_period_end` (boolean, 可選): 設定為 `true` 可讓訂閱在當前週期結束時自動取消。
  - `default_payment_method` (string, 可選): 更換訂閱的預設支付方式。
- **使用範例**：

  ```json
  {
    "tool": "update_subscription",
    "parameters": {
      "subscription_id": "sub_123456789",
      "items": [
        {
          "id": "si_123456789",
          "price": "price_987654321"
        }
      ]
    }
  }
  ```

- **預期輸出**：一個代表更新後訂閱的 JSON 物件。

---
## 資源：Others

### 23. search_stripe_resources

- **功能說明**：使用 Stripe 的搜索功能，根據查詢條件查找多種類型的資源，例如客戶、費用或訂閱。
- **參數**：
  - `query` (string, 必選): 搜索查詢字串，遵循 Stripe 的搜索查詢語言規則。
  - `limit` (integer, 可選): 回傳的物件數量上限，預設為 10，最大為 100。
  - `page` (string, 可選): 用於分頁的頁面標識符。
- **使用範例**：

  ```json
  {
    "tool": "search_stripe_resources",
    "parameters": {
      "query": "customer:'customer@example.com'",
      "limit": 1
    }
  }
  ```

- **預期輸出**：一個包含搜索結果的 JSON 物件，結果中可能包含多種類型的 Stripe 資源。

### 24. fetch_stripe_resources

- **功能說明**：根據資源 ID 檢索任何 Stripe 物件的完整詳細資訊。
- **參數**：
  - `resource_id` (string, 必選): 要檢索的 Stripe 資源 ID，例如 `cus_12345` 或 `pi_67890`。
- **使用範例**：

  ```json
  {
    "tool": "fetch_stripe_resources",
    "parameters": {
      "resource_id": "cus_123456789ABC"
    }
  }
  ```

- **預期輸出**：一個代表所請求資源的完整 JSON 物件。

### 25. search_stripe_documentation

- **功能說明**：在 Stripe 的官方文件中搜索相關主題或問題的說明。
- **參數**：
  - `query` (string, 必選): 要在文件中搜索的關鍵字或問題。
- **使用範例**：

  ```json
  {
    "tool": "search_stripe_documentation",
    "parameters": {
      "query": "how to handle disputes"
    }
  }
  ```

- **預期輸出**：一個包含相關文件章節、指南或 API 參考連結的列表。

---
