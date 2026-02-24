# ClickUp 終端使用者操作手冊：用自然語言驅動您的工作流程

**作者：Joker**

---

歡迎使用 Manus MCP ClickUp 連結器！本手冊將向您展示如何透過簡單的自然語言指令，與您的 ClickUp 工作區進行互動。您無需關心複雜的 API 或程式碼，只需像與一位智慧助理對話一樣，告訴 Manus 您想做什麼。

## 核心概念：指令如何轉換為行動

當您向 Manus 發出一個與 ClickUp 相關的指令時，系統會執行以下操作：

1.  **意圖識別**：Manus 的自然語言處理引擎會分析您的指令，理解您的意圖（例如，是想建立任務、查詢資訊，還是更新進度）。
2.  **工具選擇**：根據您的意圖，Manus 會選擇 ClickUp MCP 連結器中對應的工具。
3.  **參數提取**：系統會從您的指令中提取必要的參數，例如任務標題、專案名稱（List）、截止日期、指派對象等。
4.  **工具調用**：最後，Manus 會執行一個類似於 `manus-mcp-cli tool call` 的指令，將您的請求發送給 ClickUp，完成操作。

接下來，我們將透過一系列任務導向的範例，來具體展示這一過程。

## 任務一：快速建立新任務

這是最常見的使用場景。您可以在任何地方，例如在 Slack 或 Manus 聊天介面中，快速捕捉一個想法或待辦事項。

### 您的指令

> 「嘿 Manus，幫我在 ClickUp 的『產品開發』專案裡，建立一個叫做『設計新的登入頁面』的任務，截止日期是下週五，指派給 Joker。」

### Manus 的行動

Manus 會將您的指令轉換為一個結構化的工具調用。

```json
{
  "tool_name": "tasks.create",
  "parameters": {
    "list_name": "產品開發",
    "task_name": "設計新的登入頁面",
    "due_date": "2026-03-06",
    "assignees": ["Joker"]
  }
}
```

這個 JSON 物件會被傳遞給 ClickUp MCP 連結器，從而在您的 ClickUp 工作區中精確地建立一個新任務，並設定好所有屬性。

## 任務二：查詢專案進度

當您需要快速了解某個專案的狀況時，無需打開 ClickUp 層層點擊查找。

### 您的指令

> 「Manus，請告訴我 ClickUp 上『Q1 內容行銷』專案裡，所有『進行中』的任務有哪些？」

### Manus 的行動

Manus 會執行一個查詢任務的工具調用。

```json
{
  "tool_name": "tasks.find",
  "parameters": {
    "list_name": "Q1 內容行銷",
    "filters": [
      {
        "field": "status",
        "operator": "EQUALS",
        "value": "進行中"
      }
    ]
  }
}
```

連結器會查詢 ClickUp API，找到所有符合條件的任務，並將結果以清晰的列表形式回覆給您。

## 任務三：為任務添加評論

當您需要對某個具體任務提供回饋或補充資訊時，可以直接透過 Manus 進行。

### 您的指令

> 「在 ClickUp 任務『完成使用者訪談報告』下面留言：報告初稿已經完成，請查閱附件，並在今天下班前提供回饋。」

### Manus 的行動

首先，Manus 可能會需要先找到這個任務，如果任務標題是唯一的，它可以直接定位。然後，它會調用添加評論的工具。

```json
{
  "tool_name": "comments.add",
  "parameters": {
    "task_name": "完成使用者訪談報告",
    "comment_text": "報告初稿已經完成，請查閱附件，並在今天下班前提供回饋。"
  }
}
```

這條評論會被準確地添加到對應的 ClickUp 任務下方，並通知相關的任務參與者。

## 任務四：獲取每日工作摘要

您可以設定一個定時任務，讓 Manus 每天早上自動為您整理工作摘要。

### 您的指令 (可設定為定時觸發)

> 「Manus，每天早上九點，幫我總結一下今天在 ClickUp 中即將到期的所有任務。」

### Manus 的行動

這個指令會觸發一個包含日期過濾器的查詢。

```json
{
  "tool_name": "tasks.find",
  "parameters": {
    "filters": [
      {
        "field": "due_date",
        "operator": "EQUALS",
        "value": "today"
      }
    ]
  }
}
```

Manus 會將查詢結果整理成一份簡潔的報告，讓您在開始一天的工作前，對今日的重點有清晰的了解。

## 結論

透過 Manus MCP ClickUp 連結器，您與 ClickUp 的互動方式從「手動點擊」升級為「自然語言驅動」。這不僅僅是操作上的便捷，更是一種思維模式的轉變：您不再需要關心「如何做」，只需專注於「做什麼」。將繁瑣的操作交給 Manus，將寶貴的精力投入到真正具有創造性的工作中。

---

## 參考資料

[1] [ClickUp API Documentation](https://developer.clickup.com/)
[2] [What Is ClickUp Used For? Real-World Use Cases Explained](https://clickup.com/blog/what-is-clickup-used-for/)
