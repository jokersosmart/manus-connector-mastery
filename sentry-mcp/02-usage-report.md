# Sentry MCP 連結器使用說明報告

**作者：Joker**

---

## 總覽

Sentry MCP 連結器提供了一系列工具，讓使用者可以透過自然語言指令與 Sentry 平台進行互動，主要專注於問題 (Issue) 與事件 (Event) 的查詢與管理。本報告將詳細說明每個工具的功能、參數、使用範例及預期輸出。

## 授權

所有對 Sentry 連結器的請求都必須經過授權。使用者需要在 Manus 中設定 Sentry 的 `Auth Token`，並確保該 Token 擁有至少 `event:read` 的權限範圍 (Scope)。

## 可用工具

根據對 Sentry API 文件的研究，特別是「Events & Issues」相關的端點，我們規劃出以下核心工具。這些工具旨在提供一個符合邏輯且實用的介面，來處理日常的錯誤監控與追蹤任務。

### 1. `list_project_issues`

此工具用於列出特定 Sentry 專案中的所有問題 (Issues)。

#### 功能說明

返回一個與指定專案相關聯的問題列表。預設情況下，它會應用 `is:unresolved` 的查詢過濾，只顯示未解決的問題。使用者可以提供自訂的查詢語句來獲取不同狀態的問題，或獲取所有問題。

#### 參數

| 參數 | 型別 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `organization_slug` | `string` | 是 | Sentry 中組織的唯一識別符 (slug)。 |
| `project_slug` | `string` | 是 | Sentry 中專案的唯一識別符 (slug)。 |
| `query` | `string` | 否 | 一個 Sentry 結構化查詢語句。例如 `is:resolved` 或 `""` (留空以表示查詢所有問題)。如果未提供，預設為 `is:unresolved`。 |
| `stats_period` | `string` | 否 | 統計數據的時間範圍。可選值為 `"24h"` (預設), `"14d"` 或 `""` (禁用統計數據)。 |
| `limit` | `integer` | 否 | 返回結果的數量限制，預設為 50。 |
| `cursor` | `string` | 否 | 用於分頁的游標，指向前一次查詢的最後一個物件。 |

#### 使用範例

**自然語言指令：**
> "Hey Manus, show me the latest unresolved issues in the 'pump-station' project under the 'the-interstellar-jurisdiction' organization in Sentry."

**工具調用 (JSON)：**
```json
{
  "tool_name": "sentry.list_project_issues",
  "parameters": {
    "organization_slug": "the-interstellar-jurisdiction",
    "project_slug": "pump-station",
    "query": "is:unresolved",
    "limit": 10
  }
}
```

#### 預期輸出

工具將返回一個 JSON 陣列，其中包含符合條件的問題物件列表。每個物件都包含了問題的詳細資訊，如 ID、標題、狀態、首次出現時間、最後出現時間、事件計數等。

```json
[
  {
    "id": "1",
    "shortId": "PUMP-STATION-1",
    "title": "This is an example Python exception",
    "culprit": "raven.scripts.runner in main",
    "status": "unresolved",
    "level": "error",
    "firstSeen": "2018-11-06T21:19:55Z",
    "lastSeen": "2018-11-06T21:19:55Z",
    "count": "1",
    "userCount": 0,
    "permalink": "https://sentry.io/the-interstellar-jurisdiction/pump-station/issues/1/"
  }
]
```

### 2. `get_issue_details`

此工具用於獲取單一問題的詳細資訊。

#### 功能說明

根據提供的問題 ID，返回該問題的完整詳細資訊。

#### 參數

| 參數 | 型別 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `issue_id` | `string` | 是 | 要查詢的問題的唯一 ID。 |

#### 使用範例

**自然語言指令：**
> "Manus, get me the details for Sentry issue PUMP-STATION-1."

**工具調用 (JSON)：**
```json
{
  "tool_name": "sentry.get_issue_details",
  "parameters": {
    "issue_id": "1"
  }
}
```

#### 預期輸出

返回一個包含該問題所有屬性的 JSON 物件，與 `list_project_issues` 中的單一物件結構類似，但可能包含更詳細的資訊。

### 3. `update_issue`

此工具用於更新一個現有問題的狀態。

#### 功能說明

允許使用者修改問題的屬性，最常見的用途是改變問題的狀態，例如將其標記為「已解決」(resolved)、「已忽略」(ignored) 或重新指派給某個團隊成員。

#### 參數

| 參數 | 型別 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `issue_id` | `string` | 是 | 要更新的問題的唯一 ID。 |
| `status` | `string` | 否 | 問題的新狀態。可選值為 `"resolved"`, `"unresolved"`, `"ignored"`。 |
| `assigned_to` | `string` | 否 | 將問題指派給的使用者或團隊的識別符。 |

#### 使用範例

**自然語言指令：**
> "Okay Manus, resolve the Sentry issue PUMP-STATION-1."

**工具調用 (JSON)：**
```json
{
  "tool_name": "sentry.update_issue",
  "parameters": {
    "issue_id": "1",
    "status": "resolved"
  }
}
```

#### 預期輸出

返回更新後的問題物件，其 `status` 欄位應為 `"resolved"`。

---

## 參考資料

- [1] Sentry API Documentation. (n.d.). Retrieved February 24, 2026, from https://docs.sentry.io/api/
- [2] Sentry API: Events & Issues. (n.d.). Retrieved February 24, 2026, from https://docs.sentry.io/api/events/
- [3] Sentry API: List a Project's Issues. (n.d.). Retrieved February 24, 2026, from https://docs.sentry.io/api/events/list-a-projects-issues/
