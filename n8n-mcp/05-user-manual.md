# Xero 連結器使用者操作手冊

作者：Joker

## 歡迎！

本手冊專為希望透過自然語言指令簡化日常工作的您而設計。在這裡，您將學會如何像與一位聰明的會計助理對話一樣，輕鬆完成各種 Xero 財務操作。讓我們開始吧！

## 核心概念：從「指令」到「行動」

您只需要用日常語言告訴 Manus 您想做什麼，它會自動理解您的意圖，並將其轉換為對 Xero 連結器的精確工具調用。您無需關心技術細節，只需專注於您的業務目標。

--- 

## 任務一：查詢資訊

這是您最常使用的功能。任何您想從 Xero 知道的事情，都可以直接問。

| 您的指令 (範例) | Manus 如何理解與執行 |
| :--- | :--- |
| "幫我看看 ‘Business Gold’ 信用卡帳戶上個月花了多少錢？" | **意圖：** 查詢帳戶交易<br>**工具調用：** `get_bank_transactions`<br>  `account_name`: "Business Gold"<br>  `from_date`: (上個月初)<br>  `to_date`: (上個月底) |
| "客戶 ‘Stark Industries’ 還欠我們多少錢？" | **意圖：** 查詢聯絡人應收款項<br>**工具調用：** `get_contact_balance`<br>  `contact_name`: "Stark Industries" |
| "INV-0077 這張發票的到期日是什麼時候？" | **意圖：** 查詢特定發票資訊<br>**工具調用：** `get_invoice`<br>  `invoice_number`: "INV-0077" | 

## 任務二：建立發票與帳單

告別繁瑣的表單填寫，一句話就能搞定開票。

| 您的指令 (範例) | Manus 如何理解與執行 |
| :--- | :--- |
| "為 Wayne Enterprises 開一張發票，賣了 100 個 Batarangs，單價 50 元，下週五到期。" | **意圖：** 建立新發票<br>**工具調用：** `create_invoice`<br>  `contact_name`: "Wayne Enterprises"<br>  `line_items`: [{ desc: "Batarangs", qty: 100, amount: 50 }]<br>  `due_date`: (下週五的日期) |
| "我收到一張來自 ‘Cyberdyne Systems’ 的帳單，帳單號 T-800，金額 1000 元，是為了 ‘AI 諮詢’，月底前要付。" | **意圖：** 建立新帳單<br>**工具調用：** `create_bill`<br>  `contact_name`: "Cyberdyne Systems"<br>  `bill_number`: "T-800"<br>  `line_items`: [{ desc: "AI 諮詢", amount: 1000 }]<br>  `due_date`: (月底的日期) |

## 任務三：管理聯絡人

輕鬆新增或更新您的客戶與供應商資料庫。

| 您的指令 (範例) | Manus 如何理解與執行 |
| :--- | :--- |
| "新增一個供應商，叫 ‘Ollivanders Wand Shop’，地址在對角巷。" | **意圖：** 建立新聯絡人<br>**工具調用：** `create_contact`<br>  `name`: "Ollivanders Wand Shop"<br>  `address`: "對角巷"<br>  `is_supplier`: true |
| "更新一下 ‘Spacely Sprockets’ 的 email，改成 `ceo@spacely.com`。" | **意圖：** 更新聯絡人資訊<br>**工具調用：** `update_contact`<br>  `contact_name`: "Spacely Sprockets"<br>  `email`: "ceo@spacely.com" |

## 任務四：產生報告

快速獲取您關心的財務洞察，無需操作複雜的報告產生器。

| 您的指令 (範例) | Manus 如何理解與執行 |
| :--- | :--- |
| "給我一份今年到目前為止的損益表。" | **意圖：** 產生利潤與損失報告<br>**工具調用：** `get_profit_and_loss_report`<br>  `from_date`: (今年初)<br>  `to_date`: (今天) |
| "列出前十大應收帳款客戶以及他們的欠款金額。" | **意圖：** 產生應收帳款帳齡報告並排序<br>**工具調用：** `get_aged_receivables_report`<br>  (Manus 會在內部對結果進行排序和篩選，只呈現前十名) |

## 組合任務：發揮真正的威力

當您熟練後，可以嘗試將多個請求合併為一個指令，體驗流暢的自動化工作流程。

> "幫我查一下客戶 ‘Pied Piper’ 的 email，然後把他們所有未付款的發票清單發送給他們，並提醒他們下週一前付款。"

**Manus 如何理解與執行：**

1.  **意圖 1：** 查詢聯絡人資訊
    *   **工具調用：** `get_contact` (name: "Pied Piper") -> 取得 email
2.  **意圖 2：** 查詢未付發票
    *   **工具調用：** `get_invoices` (contact: "Pied Piper", status: "UNPAID") -> 取得發票清單
3.  **意圖 3：** 發送郵件 (假設有此工具)
    *   **工具調用：** `send_email` (to: (email), subject: "發票付款提醒", body: (發票清單與提醒訊息))

--- 

正如您所見，您只需要專注於 **您想做什麼**，而 Manus 和 Xero 連結器會負責 **如何做**。請大膽嘗試，發掘更多能簡化您工作流程的可能性！
