# Typeform 連結器使用者操作手冊

**作者：Joker**

## 簡介

本手冊為終端使用者設計，旨在說明如何透過簡單的自然語言指令，操作 Manus 的 Typeform 連結器來完成常見任務。您將看到具體的指令範例，以及 Manus 在背後如何將這些指令轉換為對應的工具調用 (Tool Call)。

## 核心任務導向範例

### 任務一：檢視我所有的表單

當您想要快速瀏覽您 Typeform 帳戶中有哪些表單時，可以使用這個指令。

- **您的指令：**
  > 「嘿 Manus，幫我列出我所有的 Typeform 表單。」

- **Manus 的理解與執行：**
  Manus 會將您的指令解讀為需要查詢 Typeform 表單列表，並調用 `list_forms` 工具。

  - **工具調用 (Tool Call):**
    ```json
    default_api.typeform.list_forms()
    ```

### 任務二：建立一個新的聯絡表單

當您需要快速建立一個新的表單來收集資訊時，可以直接告訴 Manus 您需要的欄位。

- **您的指令：**
  > 「幫我建立一個 Typeform 表單，標題是『與我聯絡』，需要包含『姓名』、『電子郵件』和『訊息內容』三個欄位。」

- **Manus 的理解與執行：**
  Manus 會辨識出「建立表單」的意圖，並從您的指令中提取標題 (`title`) 和欄位 (`fields`) 資訊，然後調用 `create_form` 工具。

  - **工具調用 (Tool Call):**
    ```json
    default_api.typeform.create_form(
      title="與我聯絡",
      fields=[
        {"title": "姓名", "type": "short_text"},
        {"title": "電子郵件", "type": "email"},
        {"title": "訊息內容", "type": "long_text"}
      ]
    )
    ```

### 任務三：查看特定表單的最新回應

當您關心某個表單的最新動態，想要查看最近收到的提交內容時。

- **您的指令：**
  > 「我想看看我的『客戶滿意度調查』這個表單，最近 10 筆已完成的回應。」

- **Manus 的理解與執行：**
  1.  首先，Manus 需要找到「客戶滿意度調查」這個表單的確切 ID。它會先調用 `list_forms` 並進行搜尋。
  2.  找到對應的 `form_id` 後，Manus 會調用 `get_responses` 工具，並傳入 `form_id` 和您指定的 `limit` (10) 以及 `completed` (true) 參數。

  - **工具調用 (Tool Call):**
    ```json
    // 假設經查詢後，表單 ID 為 "abcdefg"
    default_api.typeform.get_responses(
      form_id="abcdefg",
      limit=10,
      completed=true
    )
    ```

### 任務四：分析特定時間範圍內的回饋

當您需要為週報或月報整理特定時期的資料時，這個指令非常有用。

- **您的指令：**
  > 「幫我整理一下『Q3 產品回饋』這個表單從 7 月 1 號到 9 月 30 號之間的所有回應。」

- **Manus 的理解與執行：**
  Manus 會將您的自然語言日期轉換為標準的時間格式，並在調用 `get_responses` 工具時，使用 `since` 和 `until` 參數來限定時間範圍。

  - **工具調用 (Tool Call):**
    ```json
    // 假設經查詢後，表單 ID 為 "hijklmn"
    default_api.typeform.get_responses(
      form_id="hijklmn",
      since="2025-07-01T00:00:00Z",
      until="2025-09-30T23:59:59Z"
    )
    ```

## 結語

正如您所見，您只需要用最自然的方式說出您的需求，Manus 便會負責背後複雜的工具調用與參數設定。我們鼓勵您多加嘗試，探索更多可能性，讓 Typeform 連結器成為您處理表單與資料的得力助手。
