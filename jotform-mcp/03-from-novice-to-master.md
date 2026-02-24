# Jotform 連結器從新手到精通指南

作者：Joker

歡迎來到 Jotform 連結器的世界！本指南將帶您從基礎概念開始，逐步深入，最終掌握利用 Manus 和 Jotform 進行高效自動化的所有核心技能。

## 學習路徑概覽

| 階段 | 主題 | 學習目標 | 預計時間 |
| :--- | :--- | :--- | :--- |
| **第一階段：新手入門** | 基礎概念與首次連接 | - 了解 MCP 和連結器的價值<br>- 成功連接您的 Jotform 帳戶<br>- 執行第一個自然語言指令 | 30 分鐘 |
| **第二階段：核心功能掌握** | 表單與回覆的基本操作 | - 熟練使用 `form_list`, `create_form`<br>- 掌握 `get_submissions`, `create_submission`<br>- 理解指令與工具調用之間的關係 | 2 小時 |
| **第三階段：進階技巧探索** | 動態查詢與表單編輯 | - 學習使用篩選器 (`filter`) 進行精確查詢<br>- 掌握 `edit_form` 動態修改表單結構<br>- 組合多個指令完成複雜任務 | 4 小時 |
| **第四階段：實戰應用與自動化** | 建立自動化工作流程 | - 設計並實現一個完整的自動化流程<br>- 學習處理錯誤與邊界情況<br>- 將連結器整合到日常工作中 | 8 小時+ |

--- 

## 第一階段：新手入門

### 1. 什麼是 Jotform 連結器？

想像一下，您可以直接用說話或打字的方式，讓 AI 助理幫您完成在 Jotform 上的所有操作——這就是 Jotform 連結器的魔力。它是一個橋樑，將 Manus 的自然語言理解能力與 Jotform 強大的表單功能連接起來。

### 2. 首次連接

在您第一次嘗試使用 Jotform 相關指令時，Manus 會引導您完成一個一次性的授權過程。這是一個基於 OAuth 2.0 的安全標準流程，確保 Manus 只能在您允許的範圍內訪問您的 Jotform 數據。

1.  在 Manus 中輸入您的第一個指令，例如：`"List my Jotform forms"`。
2.  點擊彈出的授權連結，登入您的 Jotform 帳戶。
3.  批准 Manus 的存取請求。
4.  完成！您已成功連接。

### 3. 您的第一個指令

現在，讓我們來點實際的。試試看以下指令：

> `"Create a simple contact form on Jotform"`

Manus 將會調用 `create_form` 工具，為您建立一個包含基本欄位（如姓名、電子郵件）的表單，並返回新表單的連結。

## 第二階段：核心功能掌握

### 1. 管理您的表單

- **列出表單 (`form_list`)**: 隨時盤點您的資產。
  > `"Show me all my disabled forms on Jotform."`
- **建立表單 (`create_form`)**: 快速啟動新的數據收集任務。
  > `"Create a Jotform for event registration with fields for name, company, and dietary restrictions."`

### 2. 操作表單回覆

- **獲取回覆 (`get_submissions`)**: 將數據從 Jotform 中提取出來。
  > `"Get all submissions from the 'Event Registration' form."`
- **提交回覆 (`create_submission`)**: 將數據從其他地方寫入 Jotform。
  > `"Add a new submission to the 'Contact Form': Name is Jane Smith, Email is jane@example.com."`

## 第三階段：進階技巧探索

### 1. 精確查詢

當您的表單和回覆數量增長時，學會篩選至關重要。`get_submissions` 和 `form_list` 工具通常支援 `filter` 參數。

> `"Show me all submissions from the 'Customer Feedback' form received since yesterday where the rating is 5 stars."`

這個指令會被轉換為一個包含 `form_id` 和複雜 `filter` 物件的工具調用，讓您能精準地找到所需數據。

### 2. 動態修改表單

`edit_form` 工具是自動化的利器。您可以根據外部條件動態地調整表單。

> `"Add a multiple-choice question to my 'Survey' form. The options are 'Option A', 'Option B', and 'Option C'."`

### 3. 組合指令

真正的威力在於組合。您可以先建立一個表單，然後立即向其中添加回覆，或者獲取一個表單的數據，然後根據這些數據更新另一個表單。

> `"Create a new form called 'Daily Standup'. Then, add a submission with my name and today's date."`

## 第四階段：實戰應用與自動化

### 1. 設計您的第一個自動化流程

**場景**：您希望每天早上 9 點，自動將前一天收到的所有「新銷售線索」表單回覆，整理並發送到您的郵箱。

**實現步驟**：

1.  **設定觸發器**：使用 Manus 的排程功能，設定一個每天早上 9 點執行的任務。
2.  **獲取數據**：任務的第一步是調用 `get_submissions`，使用 `filter` 參數獲取過去 24 小時內的回覆。
3.  **處理數據**：使用 Manus 的代碼執行能力，將獲取的 JSON 數據格式化為易於閱讀的報告。
4.  **發送通知**：調用郵件服務的連結器（如果有的話），或使用其他通知方式，將報告發送給您。

### 2. 成為大師

精通之路永無止境。持續探索、保持好奇，並嘗試將 Jotform 連結器與您工作流中的其他工具（如 Notion、Google Sheets、Slack）結合起來，您將解鎖前所未有的生產力。
