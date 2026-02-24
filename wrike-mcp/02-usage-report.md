# Jotform MCP 連結器使用說明報告

作者：Joker

本報告提供 Jotform MCP 連結器所有可用工具的詳細使用說明，包括參數、功能、使用範例及預期輸出。

## 總覽

Jotform 連結器旨在讓使用者能透過自然語言指令，無縫地在 Manus 環境中操作 Jotform 的表單與回覆。目前支援的功能主要圍繞表單的生命週期管理和數據提交與檢索。

## 可用工具 (Tools)

根據 `manus-mcp-cli tool list --server jotform` 的輸出，目前支援以下工具。由於無法直接獲取詳細參數，此處根據通用實踐和 Jotform API 的常規設計，推斷可能的參數結構。實際參數請以官方文檔或 `manus-mcp-cli tool call <tool_name> --help` 的輸出為準。

### 1. `form_list`

- **功能說明**：獲取您 Jotform 帳戶中的表單列表。
- **參數**：
    - `status` (可選, 字串): 篩選特定狀態的表單，例如 `ENABLED`, `DISABLED`, `DELETED`。
    - `limit` (可選, 整數): 限制返回的表單數量，預設為 100。
    - `offset` (可選, 整數): 用於分頁，跳過指定數量的表單。
- **使用範例**：
    ```bash
    # 透過 Manus 自然語言指令
    "List my active Jotform forms"
    
    # 對應的工具調用 (推測)
    manus-mcp-cli tool call form_list --server jotform --input '{"status": "ENABLED"}'
    ```
- **預期輸出**：一個包含表單物件的 JSON 陣列，每個物件包含 `id`, `title`, `url`, `status`, `created_at` 等資訊。

### 2. `create_form`

- **功能說明**：建立一個新的表單。
- **參數**：
    - `title` (必需, 字串): 新表單的標題。
    - `properties` (可選, 物件): 表單的屬性，如 `theme`, `thankurl` 等。
    - `questions` (可選, 陣列): 表單的問題物件陣列，定義了表單的結構。
- **使用範例**：
    ```bash
    # 透過 Manus 自然語言指令
    "Create a new Jotform form titled 'Customer Feedback' with a name and email field."
    
    # 對應的工具調用 (推測)
    manus-mcp-cli tool call create_form --server jotform --input '{
      "title": "Customer Feedback",
      "questions": [
        {"type": "control_fullname", "text": "Name", "order": 1},
        {"type": "control_email", "text": "Email", "order": 2}
      ]
    }'
    ```
- **預期輸出**：一個包含新表單詳細資訊的 JSON 物件，包括其 `id` 和 `url`。

### 3. `edit_form`

- **功能說明**：編輯一個已存在的表單。
- **參數**：
    - `form_id` (必需, 字串): 要編輯的表單 ID。
    - `properties` (可選, 物件): 要更新的表單屬性。
    - `questions` (可選, 陣列): 要更新或新增的表單問題。
- **使用範例**：
    ```bash
    # 透過 Manus 自然語言指令
    "Add a 'Comments' field to my 'Customer Feedback' form."
    
    # 對應的工具調用 (推測)
    manus-mcp-cli tool call edit_form --server jotform --input '{
      "form_id": "YOUR_FORM_ID",
      "questions": [
        {"type": "control_textarea", "text": "Comments", "order": 3}
      ]
    }'
    ```
- **預期輸出**：一個包含更新後表單詳細資訊的 JSON 物件。

### 4. `create_submission`

- **功能說明**：向指定的表單提交一筆新的回覆。
- **參數**：
    - `form_id` (必需, 字串): 要提交回覆的表單 ID。
    - `submission_data` (必需, 物件): 一個鍵值對物件，鍵是問題的 ID 或標籤，值是提交的內容。
- **使用範例**：
    ```bash
    # 透過 Manus 自然語言指令
    "Submit a new entry to my 'Customer Feedback' form with Name 'John Doe' and Email 'john.doe@example.com'."
    
    # 對應的工具調用 (推測)
    manus-mcp-cli tool call create_submission --server jotform --input '{
      "form_id": "YOUR_FORM_ID",
      "submission_data": {
        "q1_name": "John Doe",
        "q2_email": "john.doe@example.com"
      }
    }'
    ```
- **預期輸出**：一個包含新提交回覆 ID 的 JSON 物件。

### 5. `get_submissions`

- **功能說明**：獲取指定表單的所有回覆。
- **參數**：
    - `form_id` (必需, 字串): 要獲取回覆的表單 ID。
    - `filter` (可選, 物件): 用於篩選回覆的條件，例如 `{"created_at:gt": "2024-01-01"}`。
    - `limit` (可選, 整數): 限制返回的回覆數量。
    - `offset` (可選, 整數): 用於分頁。
- **使用範例**：
    ```bash
    # 透過 Manus 自然語言指令
    "Get all submissions from my 'Customer Feedback' form received this week."
    
    # 對應的工具調用 (推測)
    manus-mcp-cli tool call get_submissions --server jotform --input '{
      "form_id": "YOUR_FORM_ID",
      "filter": {"created_at:gt": "START_OF_WEEK_DATE"}
    }'
    ```
- **預期輸出**：一個包含回覆物件的 JSON 陣列，每個物件包含提交的詳細數據和元數據。
