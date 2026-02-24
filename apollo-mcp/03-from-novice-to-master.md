# 「Apollo」連結器從新手到精通指南

**作者：Joker**

本指南旨在為開發者提供一條清晰的學習路徑，從掌握 Apollo MCP 連結器的基礎概念開始，逐步深入其核心功能與進階技巧，最終達到能夠在真實世界專案中靈活應用的精通水準。

---

## 學習路徑概覽

| 階段 | 主題 | 學習目標 | 預計時間 |
| :--- | :--- | :--- | :--- |
| **第一階段：基礎入門** | **概念與初體驗** | - 了解 MCP 與 GraphQL 的核心價值<br>- 成功運行第一個 Apollo MCP Server 實例<br>- 透過 AI 客戶端發出第一個成功的工具調用 | 2 小時 |
| **第二階段：核心功能** | **工具定義與安全** | - 掌握三種工具定義方式 (Introspection, Persisted Queries, Files)<br>- 實作基於 Persisted Queries 的安全 API 閘道<br>- 設定並理解授權 (Authorization) 機制 | 1 天 |
| **第三階段：進階技巧** | **客製化與部署** | - 學習定義 Custom Scalars 以處理特殊資料類型<br>- 掌握在容器化環境 (如 Docker) 中的部署策略<br>- 設定遙測 (Telemetry) 以監控 Server 運行狀態 | 2 天 |
| **第四階段：實戰應用** | **複雜工作流與優化** | - 設計能讓 AI 完成多步驟任務的工具鏈<br>- 處理錯誤與異常，提升 AI Agent 的穩健性<br>- 探索 GraphQL 訂閱 (Subscriptions) 與即時 AI 互動的可能性 | 3-5 天 |

---

## 第一階段：基礎入門 (The Novice)

### 1. 核心概念：為什麼是 GraphQL + MCP？

在開始撰寫任何程式碼之前，您必須先理解兩件事：

- **GraphQL 的優勢**：相較於傳統 REST API，GraphQL 允許客戶端精確地請求其所需要的資料，不多也不少。這避免了多次 API 請求的網路延遲和資料的過度/不足獲取問題。對於需要高效執行任務的 AI Agent 來說，這項優勢至關重要。

- **MCP 的角色**：Model Context Protocol (MCP) 是一個開放標準，它定義了 AI 模型如何與外部工具互動。Apollo MCP Server 正是這個標準的實作，它將您的 GraphQL API「翻譯」成 AI 能夠理解和使用的「工具」。

> **心法**：將 Apollo MCP Server 想像成一位「GraphQL 翻譯官」，他讓 AI 模型這位「總經理」能夠用自然語言，指揮後端 API 這個「專業部門」完成工作。

### 2. 快速上手：5 分鐘 Hello World

此階段的唯一目標，是成功地讓一個 AI 客戶端 (如 Manus) 透過 Apollo MCP Server 調用您的一個 GraphQL 查詢。

1.  **準備一個 GraphQL API**：如果您沒有現成的，可以使用 Apollo 官方提供的範例 API。
2.  **安裝 Rover CLI**：Rover 是 Apollo 的命令列工具，也是啟動 MCP Server 的最快方式。
3.  **撰寫設定檔**：建立一個簡單的 `mcp.yaml` 檔案，指向您的 GraphQL API 端點。
4.  **啟動 Server**：執行 `rover mcp start --config mcp.yaml`。
5.  **連接 AI**：在您的 AI 客戶端設定中，指向本地運行的 MCP Server。
6.  **發出指令**：嘗試用自然語言發出一個對應到您 GraphQL 查詢的指令，例如「告訴我目前在太空中的所有太空人」。

如果一切順利，您將看到 AI 成功地回傳了從您 API 中獲取的資料。這個「啊哈！」時刻是您踏上精通之路的起點。

## 第二階段：核心功能 (The Practitioner)

### 1. 工具的藝術：三種定義方式

您現在需要掌握如何精準地控制要將哪些 API 功能暴露給 AI。

- **Schema Introspection (內省)**：最簡單暴力的方式，直接將整個 GraphQL Schema 轉換為工具。適合內部開發或信任度高的環境。
- **Operation Files (操作檔案)**：將 `.graphql` 檔案放在指定目錄，Server 會自動載入。適合團隊協作，將工具定義與應用程式碼放在一起。
- **Persisted Queries (持久化查詢)**：最強大且安全的方式。您在 Apollo GraphOS 平台上預先註冊並批准一組查詢。MCP Server 只會執行這個「白名單」上的操作。這是生產環境的首選。

> **實踐**：嘗試將您的 API 從 Introspection 模式重構為 Persisted Queries 模式。感受一下這種轉變如何提升了您對 API 暴露程度的信心。

### 2. 建立護城河：授權與安全

當 AI 能夠操作您的 API 時，安全就成了頭等大事。

- **轉發授權標頭 (Forwarding Auth Headers)**：MCP Server 可以設定將來自 AI 客戶端的特定 HTTP 標頭 (如 `Authorization: Bearer ...`)轉發給您的後端 GraphQL API。這樣，您現有的 API 認證和授權邏輯可以完全重用。
- **CORS 設定**：如果您的 AI 客戶端是從瀏覽器環境運行的，您需要正確設定跨來源資源共用 (CORS) 策略，以允許來自特定來源的請求。

## 第三階段：進階技巧 (The Expert)

### 1. 超越基礎：客製化與部署

- **Custom Scalars**：GraphQL 允許定義自訂的純量類型 (Scalar)，例如 `Date` 或 `JSON`。您需要在 MCP Server 的設定中進行映射，以便 AI 正確理解和使用這些特殊類型。
- **生產環境部署**：學習如何將 MCP Server 打包成一個 Docker 映像檔，並在您選擇的雲端平台 (如 AWS, Google Cloud) 上進行部署。重點是設定健康檢查 (Health Checks) 端點，以確保服務的穩定運行。
- **可觀測性 (Observability)**：設定遙測功能，將 Server 的日誌、指標 (Metrics) 和追蹤 (Traces) 數據發送到您選擇的監控平台 (如 Datadog, OpenTelemetry)。這對於在生產環境中除錯和優化至關重要。

## 第四階段：實戰應用 (The Master)

### 1. 編排的藝術：設計複雜工作流

精通的標誌是，您不再將工具視為單一的操作，而是將其視為可以被 AI 自由編排的「樂高積木」。

- **思考任務，而非 API**：設計您的 GraphQL API 時，要更多地從「使用者想完成什麼任務」出發，而不僅僅是「資料庫裡有什麼表」。一個好的 Mutation 應該對應一個有意義的業務操作。
- **處理副作用與狀態**：當 AI 執行一個 Mutation 後，系統的狀態發生了改變。您需要設計相應的 Query 工具，讓 AI 能夠驗證其操作的結果。例如，`createOrder` 之後，應該有一個 `getOrderStatus` 的工具。

### 2. 探索前沿：即時互動

- **GraphQL Subscriptions**：雖然目前 MCP 協定主要針對請求/回應模式，但您可以探索如何結合 GraphQL Subscriptions 來實現即時的 AI 互動。例如，當後端資料發生變化時，可以主動推送更新給 AI，觸發其進行下一步的分析或行動。

恭喜您！完成這條學習路徑後，您將不僅僅是一個 Apollo MCP Server 的使用者，而是一個能夠駕馭 AI 與 API 協同工作的架構師，為您的產品和團隊創造前所未有的可能性。
