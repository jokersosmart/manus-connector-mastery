# Sentry 連結器使用者操作手冊

**作者：Joker**

---

## 簡介

本手冊為終端使用者設計，旨在展示如何透過簡單的自然語言指令，讓 Manus 成為您強大的 Sentry 助理。您不需要了解複雜的 API 或程式碼，只需像與同事對話一樣，告訴 Manus 您想做什麼。

## 核心概念：從「指令」到「行動」

您對 Manus 下達的每一個關於 Sentry 的指令，背後都有一套精密的轉換邏輯。Manus 會解析您的意圖，並將其轉換為對 Sentry 連結器工具的精確調用。本手冊將揭示這一過程。

--- 

## 任務一：快速掌握專案的錯誤狀況

這是最常見的需求：我想知道我的專案現在有多少問題。

### 您的指令

> "Hey Manus, what's the status of the 'frontend-app' project in Sentry? Show me the unresolved issues."

或者更簡潔地說：

> "Manus, list unresolved issues for the 'frontend-app' project."

### Manus 的內心戲 (工具調用)

當 Manus 聽到這個指令時，它會識別出幾個關鍵意圖：

-   **目標服務**: Sentry
-   **動作**: `list_project_issues` (列出問題)
-   **目標專案**: `frontend-app`
-   **過濾條件**: `unresolved` (未解決的)

因此，它會構建並執行如下的工具調用：

```json
{
  "tool_name": "sentry.list_project_issues",
  "parameters": {
    "organization_slug": "[您的組織 SLUG]", // Manus 會從您的設定中獲取
    "project_slug": "frontend-app",
    "query": "is:unresolved"
  }
}
```

--- 

## 任務二：深入調查某個特定問題

在看到問題列表後，您通常會對某個特定的問題感興趣。

### 您的指令

> "Manus, tell me more about issue FRONTEND-APP-123. I want to see the full details."

### Manus 的內心戲 (工具調用)

Manus 識別出您想獲取單一問題的詳細資訊。

-   **目標服務**: Sentry
-   **動作**: `get_issue_details` (獲取問題詳情)
-   **目標問題**: `FRONTEND-APP-123` (這裡的 ID 會被轉換為 Sentry 系統的數字 ID)

工具調用如下：

```json
{
  "tool_name": "sentry.get_issue_details",
  "parameters": {
    "issue_id": "[轉換後的數字 ID]"
  }
}
```

--- 

## 任務三：更新問題狀態

調查清楚後，您需要採取行動，例如解決或忽略一個問題。

### 您的指令

> "Okay, I've fixed this. Manus, please resolve Sentry issue FRONTEND-APP-123."

或者，如果您想暫時忽略它：

> "Manus, ignore issue FRONTEND-APP-123 for now."

### Manus 的內心戲 (工具調用)

Manus 偵測到您想要「更新」一個問題的狀態。

-   **目標服務**: Sentry
-   **動作**: `update_issue` (更新問題)
-   **目標問題**: `FRONTEND-APP-123`
-   **新的狀態**: `resolved` (或 `ignored`)

工具調用如下：

```json
{
  "tool_name": "sentry.update_issue",
  "parameters": {
    "issue_id": "[轉換後的數字 ID]",
    "status": "resolved"
  }
}
```

--- 

## 任務四：進行高級篩選與搜尋

有時候，您需要更精準的搜尋，而不僅僅是查看未解決的問題。

### 您的指令

> "Manus, search for issues in the 'backend-service' project that are assigned to me and have the 'performance' tag."

### Manus 的內心戲 (工具調用)

這個指令比較複雜，但 Manus 能夠解析出多個過濾條件，並將它們組合到 `query` 參數中。

-   **目標服務**: Sentry
-   **動作**: `list_project_issues`
-   **目標專案**: `backend-service`
-   **組合查詢**: `assigned:me` AND `tags:performance`

工具調用如下：

```json
{
  "tool_name": "sentry.list_project_issues",
  "parameters": {
    "organization_slug": "[您的組織 SLUG]",
    "project_slug": "backend-service",
    "query": "is:unresolved assigned:me has:performance"
  }
}
```

## 結論

正如您所見，您無需關心 JSON 格式或具體的工具名稱。您只需用最自然的方式表達您的需求，剩下的交給 Manus 即可。理解這種從「指令」到「行動」的轉換過程，能幫助您更有效地與 Manus 協作，將 Sentry 的錯誤監控能力發揮到極致。
