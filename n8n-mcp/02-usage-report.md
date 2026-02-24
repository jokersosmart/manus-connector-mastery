# Xero MCP 連結器使用說明報告

作者：Joker

## 總覽

Xero MCP 連結器提供了一套強大的工具，讓使用者能夠透過自然語言指令，與其 Xero 帳戶進行互動，實現會計流程的自動化。本報告將詳細介紹每個可用工具的功能、參數、使用範例及預期輸出。

**注意：** 由於無法直接查詢 MCP 伺服器，本文件根據公開的 Xero API 文件模擬了可能的工具集。實際可用工具可能有所不同。

## 核心工具

### 1. `find_account`

**功能說明：** 根據名稱、類型或代碼，查詢一個或多個會計科目。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `name` | string | 否 | 會計科目的完整或部分名稱。 |
| `type` | string | 否 | 會計科目的類型 (例如 `BANK`, `SALES`, `EXPENSE`)。 |
| `code` | string | 否 | 會計科目的代碼。 |

**使用範例：**

> "幫我找一下所有名稱包含 'Sales' 的帳戶"

**預期輸出：**

一個包含符合條件的會計科目清單的表格，包含科目名稱、代碼、類型和目前餘額。

### 2. `create_invoice`

**功能說明：** 建立一張新的銷售發票。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `contact_name` | string | 是 | 客戶的名稱。 |
| `line_items` | array | 是 | 發票的項目列表，每個項目包含 `description`, `quantity`, `unit_amount`。 |
| `due_date` | string | 否 | 發票的到期日 (格式: YYYY-MM-DD)。 |
| `status` | string | 否 | 發票狀態，預設為 `DRAFT`。可設為 `SUBMITTED` 或 `AUTHORISED`。 |

**使用範例：**

> "為 'ACME Inc.' 建立一張發票，內容是 10 小時的諮詢服務，每小時 150 元，到期日是下個月底。"

**預期輸出：**

成功訊息，包含新建立的發票號碼和一個指向 Xero 中該發票的連結。

### 3. `get_invoice_status`

**功能說明：** 查詢特定發票的狀態。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `invoice_number` | string | 是 | 要查詢的發票號碼。 |

**使用範例：**

> "查詢發票 INV-0042 的狀態"

**預期輸出：**

發票的目前狀態 (例如 `DRAFT`, `SENT`, `PAID`)、總金額和到期日。

### 4. `create_contact`

**功能說明：** 在 Xero 中建立一個新的聯絡人（客戶或供應商）。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `name` | string | 是 | 聯絡人的姓名或公司名稱。 |
| `email` | string | 否 | 聯絡人的電子郵件地址。 |
| `is_supplier` | boolean | 否 | 標記此聯絡人是否為供應商。 |

**使用範例：**

> "新增一個客戶，名稱是 'Global Corp'，信箱是 contact@globalcorp.com"

**預期輸出：**

成功訊息，確認聯絡人已建立。

### 5. `get_bank_transactions`

**功能說明：** 獲取指定銀行帳戶的交易紀錄。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `account_name` | string | 是 | 銀行帳戶在 Xero 中的名稱。 |
| `from_date` | string | 否 | 查詢的開始日期 (YYYY-MM-DD)。 |
| `to_date` | string | 否 | 查詢的結束日期 (YYYY-MM-DD)。 |

**使用範例：**

> "顯示 'Business Bank Account' 上個月的所有交易紀錄"

**預期輸出：**

一個交易列表，包含日期、描述、存入金額、提出金額和餘額。

--- 

## 參考資料

[1] Xero Developer API Documentation. [https://developer.xero.com/documentation/api/accounting/overview](https://developer.xero.com/documentation/api/accounting/overview)
