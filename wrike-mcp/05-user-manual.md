# Jotform 連結器使用者操作手冊

作者：Joker

本手冊為終端使用者設計，旨在展示如何透過簡單的自然語言指令，讓 Manus 為您執行 Jotform 上的各種任務。您無需關心複雜的技術細節，只需告訴 Manus 您想做什麼。

## 核心理念：從「指令」到「行動」

您用自然語言下達指令，Manus 會理解您的意圖，並自動選擇最合適的工具（Tool）來完成任務。以下是一些常見任務的範例，以及 Manus 在幕後可能的動作解析。

--- 

### 任務一：快速建立一個新的意見回饋表單

當您需要快速收集他人的意見時，無需離開當前對話視窗，直接告訴 Manus。

- **您的指令**：
  > `"Hey Manus, create a Jotform for me to collect feedback. I need fields for their name, email, and the feedback itself."`

- **Manus 的轉換過程**：
  1.  **意圖識別**：使用者想要建立一個 Jotform 表單。
  2.  **工具選擇**：選擇 `create_form` 工具。
  3.  **參數提取**：
      - `title`: 從「collect feedback」推斷出一個合適的標題，如「Feedback Form」。
      - `questions`: 從「name, email, and the feedback itself」識別出需要三個欄位，並匹配到 Jotform 對應的欄位類型（`control_fullname`, `control_email`, `control_textarea`）。
  4.  **工具調用 (幕後)**：
      ```json
      {
        "tool_name": "create_form",
        "parameters": {
          "title": "Feedback Form",
          "questions": [
            { "type": "control_fullname", "text": "Name", "order": 1 },
            { "type": "control_email", "text": "Email", "order": 2 },
            { "type": "control_textarea", "text": "Feedback", "order": 3 }
          ]
        }
      }
      ```
  5.  **結果呈現**：Manus 返回一個訊息，告訴您表單已建立，並附上可點擊的表單連結。

--- 

### 任務二：查詢上週收到了多少產品試用申請

當您需要快速獲取數據洞察時，直接向 Manus 提問。

- **您的指令**：
  > `"How many new trial requests did I get on Jotform last week?"`

- **Manus 的轉換過程**：
  1.  **意圖識別**：使用者想要查詢特定 Jotform 表單在特定時間範圍內的回覆數量。
  2.  **工具選擇**：選擇 `get_submissions` 工具。
  3.  **參數提取**：
      - `form_id`: Manus 可能會根據「trial requests」這個關鍵詞，在您的表單列表中尋找最匹配的表單，或者如果不明確，會反問您是哪個表單。
      - `filter`: 從「last week」計算出具體的開始和結束日期，並轉換為 API 需要的格式，如 `{"created_at:gt": "YYYY-MM-DD", "created_at:lt": "YYYY-MM-DD"}`。
  4.  **工具調用 (幕後)**：
      ```json
      {
        "tool_name": "get_submissions",
        "parameters": {
          "form_id": "1234567890", // 假設找到了對應的表單 ID
          "filter": {
            "created_at:gt": "2024-02-19", // 假設今天是 26 號
            "created_at:lt": "2024-02-26"
          }
        }
      }
      ```
  5.  **結果呈現**：Manus 不會直接展示原始的 JSON 數據，而是會處理這些數據，計算回覆的總數，然後用一句話告訴您：「您上週收到了 [N] 份新的產品試用申請。」

--- 

### 任務三：將一位潛在客戶手動加入到「銷售線索」表單

當您在與客戶溝通時，可以即時將資訊記錄到 Jotform，而無需切換應用。

- **您的指令**：
  > `"Add a new lead to my sales form. Name is Michael Scott, company is Dunder Mifflin, email is michael.scott@dundermifflin.com."`

- **Manus 的轉換過程**：
  1.  **意圖識別**：使用者想要向一個 Jotform 表單提交新的數據。
  2.  **工具選擇**：選擇 `create_submission` 工具。
  3.  **參數提取**：
      - `form_id`: 根據「sales form」找到對應的表單。
      - `submission_data`: 從指令中提取「Michael Scott」、「Dunder Mifflin」、「michael.scott@dundermifflin.com」，並將它們與表單中的「Name」、「Company」、「Email」欄位進行匹配。
  4.  **工具調用 (幕後)**：
      ```json
      {
        "tool_name": "create_submission",
        "parameters": {
          "form_id": "0987654321",
          "submission_data": {
            "q3_name": "Michael Scott",
            "q4_company": "Dunder Mifflin",
            "q5_email": "michael.scott@dundermifflin.com"
          }
        }
      }
      ```
  5.  **結果呈現**：Manus 會確認操作已完成：「好的，已將 Michael Scott 添加到您的銷售線索表單。」

--- 

## 您的可能性是無限的

以上只是幾個簡單的例子。您可以嘗試更複雜的指令，或者將多個指令組合起來，以適應您獨特的工作流程。Manus 的目標是成為您聰明、高效的助手，讓您專注於更有創造力的工作。
