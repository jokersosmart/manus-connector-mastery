# 「Apollo」連結器學習手冊

**作者：Joker**

本手冊專為希望快速上手 Apollo MCP 連結器的開發者設計，著重於關鍵技能的培養，提供清晰的學習目標和實踐練習，讓您在最短時間內掌握核心功能，賦能您的 AI 應用。

---

## 學習目標

完成本手冊後，您將能夠：

1.  **獨立設定並啟動一個 Apollo MCP Server 實例**，連接至您現有的 GraphQL API。
2.  **使用「持久化查詢 (Persisted Queries)」白名單**，安全地將您的 API 功能暴露給 AI。
3.  **設計並提供 AI 可用的工具**，讓 AI Agent 能夠查詢和操作您的後端資料。

---

## Part 1: 核心概念極速入門 (15 分鐘)

在開始之前，忘掉複雜的技術細節，只需記住三個核心概念：

1.  **您的 GraphQL API 是「引擎」**：它包含了所有業務邏輯和資料存取能力。
2.  **Apollo MCP Server 是「翻譯官」**：它將您的 API 語言（GraphQL）翻譯成 AI 的語言（MCP 工具）。
3.  **AI Agent 是「駕駛員」**：它使用這些工具來完成您用自然語言下達的指令。

您的任務就是設定好這位「翻譯官」，讓他準確、安全地工作。

## Part 2: 關鍵技能一：安全地暴露查詢 (30 分鐘)

這是最重要的技能，也是生產環境的基礎。我們將跳過不安全的內省模式，直接學習最推薦的「持久化查詢」方法。

### 學習目標

- 能夠定義一個查詢，將其註冊到 Apollo GraphOS，並透過 MCP Server 將其作為工具暴露給 AI。

### 實踐練習

1.  **選定一個簡單的查詢**：從您的 GraphQL API 中，選擇一個最簡單的 `Query`，例如 `getProduct(id: ID!)` 或 `listUsers`。

2.  **註冊持久化查詢**：
    - 登入 Apollo GraphOS Studio。
    - 找到您的 Graph，進入「Operations」分頁。
    - 手動新增一個操作，貼上您選擇的查詢，並給它一個有意義的名稱，例如 `GetProductByID`。

3.  **設定 MCP Server**：
    - 建立一個 `mcp.yaml` 設定檔。
    - 在 `tools` 區塊，設定 `persisted_queries`，並指向您在 GraphOS 上的 Graph。
    - 關鍵設定如下：
      ```yaml
      server:
        endpoint: "http://localhost:4000/graphql" # 您 GraphQL API 的位址
      tools:
        persisted_queries:
          graph_ref: "my-graph@current" # 替換為您的 Graph 參照
      ```

4.  **啟動與驗證**：
    - 執行 `rover mcp start --config mcp.yaml`。
    - 在 Manus 或其他 AI 客戶端中，嘗試用自然語言調用這個工具：「幫我找一下 ID 為 'abc' 的產品資訊」。
    - 觀察 AI 是否成功調用了 `GetProductByID` 工具並返回了正確的資料。

> **核心洞察**：透過持久化查詢，您建立了一個明確的「許可清單」。任何不在這個清單上的操作，AI 都無法執行，從而確保了 API 的安全。

## Part 3: 關鍵技能二：賦予 AI 行動能力 (45 分鐘)

查詢只是讓 AI「看見」世界，而操作 (Mutations) 則讓 AI「改變」世界。這一步將賦予您的 AI Agent 真正的行動能力。

### 學習目標

- 能夠安全地暴露一個 `Mutation`，讓 AI 可以執行創建、更新或刪除等操作。

### 實踐練習

1.  **選定一個安全的 Mutation**：選擇一個影響範圍較小、可逆或冪等的 `Mutation` 作為起點。例如，`updateUserPreferences(input: UserPreferencesInput!)`，而不是 `deleteAllUsers`。

2.  **註冊持久化操作**：如同上一步，將這個 `Mutation` 也註冊到 Apollo GraphOS 的 Operations 列表中。例如，命名為 `UpdateUserPrefs`。

3.  **設定授權轉發**：通常，執行 `Mutation` 需要使用者認證。您需要在 `mcp.yaml` 中設定，讓 MCP Server 將來自 AI 客戶端的授權標頭轉發給您的後端 API。
    - 關鍵設定：
      ```yaml
      server:
        endpoint: "http://localhost:4000/graphql"
        headers:
          - name: "Authorization"
            forward: "always"
      # ... 其餘設定
      ```

4.  **啟動與驗證**：
    - 重啟 MCP Server 以載入新設定。
    - 在 AI 客戶端中，確保您的請求包含了必要的 `Authorization` 標頭（例如，一個 JWT Token）。
    - 嘗試用自然語言下達指令：「幫我把使用者的通知偏好設定為 'email'」。
    - 驗證後端資料庫中的資料是否真的被更新了。

> **核心洞察**：賦予 AI 寫入權限是一把雙面刃。永遠從最小權限原則出發，只暴露絕對必要的、且經過充分驗證的 `Mutation`。

---

恭喜！掌握了這兩項關鍵技能，您已經具備了使用 Apollo MCP 連結器解決 80% 常見問題的能力。接下來，您可以根據「從新手到精通指南」繼續探索更進階的主題，例如客製化純量類型、生產環境部署和設計複雜的工作流。
