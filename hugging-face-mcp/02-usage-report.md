# 「Zapier」連結器使用說明報告

作者：Joker

---

## 總覽

Zapier 連結器作為 Manus 與 Zapier 生態系統之間的橋樑，賦予使用者透過自然語言指令來建構、管理和執行自動化工作流程的能力。本報告將詳細介紹此連結器提供的所有可用工具，包括其功能、參數、使用範例及預期輸出，旨在幫助使用者充分利用其強大功能。

## 核心概念

在深入了解具體工具之前，我們需要先掌握 Zapier 的兩個核心概念：**觸發 (Triggers)** 和 **動作 (Actions)**。

> **觸發 (Trigger)**：是啟動一個自動化工作流程 (Zap) 的事件。例如，「當我收到一封新的 Gmail 郵件時」就是一個觸發。觸發負責監控應用程式中的新資料或更新。

> **動作 (Action)**：是當觸發事件發生後，所要執行的任務。例如，「在 Google Sheets 中新增一行」就是一個動作。動作負責在應用程式中建立或更新資料。

Manus 的 Zapier 連結器將這些概念封裝成一系列直觀的工具，讓使用者可以專注於業務邏輯，而非技術細節。

## 可用工具詳解

基於對 Zapier 開發者文件的研究，我們將 Zapier 的核心功能抽象為以下兩大類工具。值得注意的是，由於我們無法直接獲取 `zapier` MCP 伺服器的工具列表，此處的工具是根據公開文件所做的合理推斷與設計。

### 1. 觸發管理工具 (Trigger Management)

這類工具專門用於設定和管理 Zaps 的觸發條件。

#### 1.1 `create_polling_trigger`

*   **功能說明**：建立一個「輪詢觸發器」。Manus 會根據設定的頻率，定期檢查指定的應用程式 API，看是否有新的或更新的資料。這是最常見的觸發類型。
*   **參數**：
    *   `app` (string, required): 要監控的應用程式名稱，例如 `gmail`。
    *   `event` (string, required): 要監控的具體事件，例如 `new_email`。
    *   `polling_frequency` (integer, optional): 輪詢的頻率（分鐘），預設為 5 分鐘。受限於使用者的 Zapier 方案等級。
    *   `sort_field` (string, optional): 用於排序回傳資料的欄位，以確保最新的資料在最前面。
*   **使用範例**：
    ```
    > Manus, 幫我建立一個每 10 分鐘檢查一次 Gmail 是否有新郵件的觸發器。
    ```
*   **預期 Manus 調用**：
    ```json
    {
      "tool": "zapier.create_polling_trigger",
      "parameters": {
        "app": "gmail",
        "event": "new_email",
        "polling_frequency": 10
      }
    }
    ```
*   **預期輸出**：成功建立觸發器的確認訊息，並返回一個唯一的觸發器 ID。

#### 1.2 `create_rest_hook_trigger`

*   **功能說明**：建立一個「REST Hook 觸發器」。這是一種更即時的觸發方式。指定的應用程式會在事件發生時，主動發送一個 Webhook 通知給 Manus (Zapier)，立即觸發工作流程。
*   **參數**：
    *   `app` (string, required): 要監控的應用程式名稱。
    *   `event` (string, required): 要監控的具體事件。
*   **使用範例**：
    ```
    > Manus, 當我的 Shopify 商店有新訂單時，立即通知我。
    ```
*   **預期 Manus 調用**：
    ```json
    {
      "tool": "zapier.create_rest_hook_trigger",
      "parameters": {
        "app": "shopify",
        "event": "new_order"
      }
    }
    ```
*   **預期輸出**：成功建立觸發器的確認訊息，並返回一個唯一的觸發器 ID 和一個供應用程式使用的 Webhook URL。

### 2. 動作管理工具 (Action Management)

這類工具用於定義觸發發生後需要執行的具體任務。

#### 2.1 `create_action`

*   **功能說明**：建立一個「創造動作」。當觸發器被觸發時，在目標應用程式中建立一個新的項目（例如一封郵件、一個日曆事件、一筆資料庫紀錄）。
*   **參數**：
    *   `app` (string, required): 要執行動作的應用程式名稱，例如 `google_sheets`。
    *   `action_type` (string, required): 動作的類型，固定為 `create`。
    *   `data` (object, required): 要寫入的資料內容，以鍵值對形式提供。
*   **使用範例**：
    ```
    > Manus, 當 Gmail 有新郵件時，將寄件人和主旨記錄到我的 Google Sheets 'Leads' 工作表中。
    ```
*   **預期 Manus 調用**：
    ```json
    {
      "tool": "zapier.create_action",
      "parameters": {
        "app": "google_sheets",
        "action_type": "create",
        "data": {
          "worksheet_name": "Leads",
          "row_data": {
            "Sender": "{{trigger.sender}}",
            "Subject": "{{trigger.subject}}"
          }
        }
      }
    }
    ```
*   **預期輸出**：成功建立動作的確認訊息，並返回一個唯一的動作 ID。

#### 2.2 `create_search_action`

*   **功能說明**：建立一個「搜尋動作」。在執行主要動作之前，先在目標應用程式中搜尋是否存在某個項目。這常用於避免重複建立資料，或是在更新資料前先找到它。
*   **參數**：
    *   `app` (string, required): 要執行搜尋的應用程式名稱。
    *   `action_type` (string, required): 動作的類型，固定為 `search`。
    *   `query` (string, required): 搜尋的查詢條件。
    *   `create_if_not_found` (boolean, optional): 如果找不到，是否自動建立一個新項目。預設為 `false`。
*   **使用範例**：
    ```
    > Manus, 當有新的 Hubspot 聯絡人時，先檢查 Notion 的『客戶』資料庫裡是否已經有這個 email，如果沒有，就新增一筆紀錄。
    ```
*   **預期 Manus 調用**：
    ```json
    {
      "tool": "zapier.create_search_action",
      "parameters": {
        "app": "notion",
        "action_type": "search",
        "query": "email:{{trigger.email}}",
        "create_if_not_found": true,
        "create_data": {
          "database_id": "YOUR_DATABASE_ID",
          "properties": {
            "Name": "{{trigger.name}}",
            "Email": "{{trigger.email}}"
          }
        }
      }
    }
    ```
*   **預期輸出**：返回搜尋結果。如果找到，則返回該項目的資料；如果沒找到，根據 `create_if_not_found` 的設定，可能會返回空值或新建立項目的資料。

---

**免責聲明**：本報告中的工具列表與參數是基於公開可得的 Zapier 開發者文件所做的推論設計。實際可用的工具集可能因 Manus MCP 伺服器的具體實現而有所不同。
