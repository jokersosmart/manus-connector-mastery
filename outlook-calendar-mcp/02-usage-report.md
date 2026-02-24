# Outlook Calendar 連結器使用說明報告

作者：Joker

---

## 總覽

本文件詳細介紹了 Manus MCP 連結器「Outlook Calendar」的所有可用工具、參數、功能及使用範例。此連結器讓使用者能夠透過自然語言指令，無縫地在 Manus 平台中管理其 Outlook 日曆，執行創建、查詢、更新、刪除事件，以及更複雜的排程與協調任務。

連結器的核心設計理念是將日曆數據轉化為跨應用工作流的強大觸發器，打破應用孤島，實現工作流程的深度自動化。所有時間戳記均使用 UTC/ISO 8601 格式，並需包含時區資訊。

## 可用工具

以下是 `mcp_CalendarTools` 伺服器提供的所有工具及其詳細說明。

### 1. `graph_acceptEvent`

接受使用者日曆中的特定事件邀請。

| 參數 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `eventId` | string | 是 | 要接受的事件的唯一 ID。 |
| `userId` | string | 否 | 使用者的 ID 或 `userPrincipalName`。 |
| `comment` | string | 否 | 回應中包含的選用文字。 |
| `sendResponse` | boolean | 否 | 是否將回應發送給組織者。 |

**使用範例**:

```json
{
  "tool": "graph_acceptEvent",
  "parameters": {
    "eventId": "AAMkAGI3..."
  }
}
```

**預期輸出**: 成功接受事件邀請的確認訊息。

### 2. `graph_cancelEvent`

取消指定使用者日曆中的一個事件，並通知所有參與者。

| 參數 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `eventId` | string | 是 | 要取消的事件的唯一 ID。 |
| `userId` | string | 否 | 使用者的 ID 或 `userPrincipalName`。 |
| `comment` | string | 否 | 附加在取消通知中的選用訊息。 |

**使用範例**:

```json
{
  "tool": "graph_cancelEvent",
  "parameters": {
    "eventId": "AAMkAGI3...",
    "comment": "因專案排程變更，此會議取消。"
  }
}
```

**預期輸出**: 成功取消事件並已通知參與者的確認訊息。

### 3. `graph_createEvent`

在指定的使用者日曆中創建一個新的事件。支援重複性事件和線上會議。

| 參數 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `subject` | string | 是 | 事件的主題或標題 (不可為空)。 |
| `start` | object | 是 | 包含 `dateTime` 和 `timeZone` 的開始時間戳記。 |
| `end` | object | 是 | 包含 `dateTime` 和 `timeZone` 的結束時間戳記。 |
| `attendees_addresses` | array | 是 | 參與者的電子郵件地址陣列。 |
| `userId` | string | 否 | 使用者的 ID 或 `userPrincipalName`。 |
| `body` | object | 否 | 包含 `contentType` (Text/HTML) 和 `content` 的事件內文。 |
| `location` | object | 否 | 包含 `displayName` 的地點資訊。 |
| `attendees_types` | array | 否 | 參與者的角色 (必要、選用、資源)。 |
| `attendees_names` | array | 否 | 參與者的顯示名稱。 |
| `recurrence` | object | 否 | 重複模式與範圍。 |
| `isOnlineMeeting` | boolean | 否 | 設定為 `true` 以建立線上會議。 |
| `onlineMeetingProvider` | string | 否 | 線上會議提供商 (`teamsForBusiness`, `skypeForBusiness`, `skypeForConsumer`)。 |
| `allowNewTimeProposals` | boolean | 否 | 是否允許參與者提出新的會議時間建議 (預設為 `true`)。 |
| `transactionId` | string | 否 | 用於確保冪等性 (Idempotence) 的交易 ID。 |

**使用範例**:

```json
{
  "tool": "graph_createEvent",
  "parameters": {
    "subject": "Q3 產品策略審查會議",
    "start": {
      "dateTime": "2026-08-05T10:00:00",
      "timeZone": "Pacific Standard Time"
    },
    "end": {
      "dateTime": "2026-08-05T11:00:00",
      "timeZone": "Pacific Standard Time"
    },
    "attendees_addresses": ["alex@example.com", "bob@example.com"],
    "isOnlineMeeting": true,
    "onlineMeetingProvider": "teamsForBusiness"
  }
}
```

**預期輸出**: 成功創建的事件物件，包含事件 ID 和其他詳細資訊。

### 4. `graph_declineEvent`

拒絕使用者日曆中的特定事件邀請。

| 參數 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `eventId` | string | 是 | 要拒絕的事件的唯一 ID。 |
| `userId` | string | 否 | 使用者的 ID 或 `userPrincipalName`。 |
| `comment` | string | 否 | 回應中包含的選用文字。 |
| `sendResponse` | boolean | 否 | 是否將回應發送給組織者。 |

**使用範例**:

```json
{
  "tool": "graph_declineEvent",
  "parameters": {
    "eventId": "AAMkAGI3...",
    "comment": "抱歉，當天已有其他安排。"
  }
}
```

**預期輸出**: 成功拒絕事件邀請的確認訊息。

### 5. `graph_deleteEvent`

從指定的使用者日曆中刪除一個事件。

| 參數 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `eventId` | string | 是 | 要刪除的事件的唯一 ID。 |
| `userId` | string | 否 | 使用者的 ID 或 `userPrincipalName`。 |

**使用範例**:

```json
{
  "tool": "graph_deleteEvent",
  "parameters": {
    "eventId": "AAMkAGI3..."
  }
}
```

**預期輸出**: 成功刪除事件的確認訊息。

### 6. `graph_findMeetingTimes`

根據組織者和參與者的有空時間，建議會議時間和地點。

| 參數 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `meetingDuration` | string | 是 | 會議持續時間 (例如, 'PT1H' 表示 1 小時)。 |
| `userId` | string | 否 | 組織者的 ID 或 `userPrincipalName`。 |
| `attendees_addresses` | array | 否 | 參與者的電子郵件地址陣列。 |
| `timeConstraint` | object | 否 | 包含時間段和 `activityDomain` 的時間限制。 |
| `locationConstraint` | object | 否 | 地點選項。 |
| `maxCandidates` | integer | 否 | 最大建議數量。 |
| `isOrganizerOptional` | boolean | 否 | 組織者是否為選用。 |
| `minimumAttendeePercentage` | float | 否 | 最小出席率閾值。 |

**使用範例**:

```json
{
  "tool": "graph_findMeetingTimes",
  "parameters": {
    "attendees_addresses": ["alex@example.com", "bob@example.com"],
    "meetingDuration": "PT30M",
    "maxCandidates": 3
  }
}
```

**預期輸出**: 一個包含多個建議會議時間的列表，每個建議都包含參與者的空閒狀態。

### 7. `graph_getEvent`

從指定的使用者日曆中獲取單一事件的詳細資訊。

| 參數 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `eventId` | string | 是 | 事件的唯一 ID。 |
| `userId` | string | 否 | 使用者的 ID 或 `userPrincipalName`。 |
| `select` | string | 否 | OData `$select` 查詢參數，用於指定回傳的欄位。 |

**使用範例**:

```json
{
  "tool": "graph_getEvent",
  "parameters": {
    "eventId": "AAMkAGI3...",
    "select": "subject,start,end,location"
  }
}
```

**預期輸出**: 一個包含指定事件詳細資訊的物件。

### 8. `graph_getSchedule`

獲取一個或多個使用者、通訊群組或資源的空閒/忙碌時間表。

| 參數 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `schedules` | array | 是 | 一個包含使用者或資源 SMTP 地址的陣列。 |
| `startTime` | object | 是 | 包含 `dateTime` 和 `timeZone` 的開始時間。 |
| `endTime` | object | 是 | 包含 `dateTime` 和 `timeZone` 的結束時間。 |
| `availabilityViewInterval` | integer | 否 | 時間間隔的長度 (分鐘)。 |

**使用範例**:

```json
{
  "tool": "graph_getSchedule",
  "parameters": {
    "schedules": ["alex@example.com", "bob@example.com"],
    "startTime": {
      "dateTime": "2026-08-06T09:00:00",
      "timeZone": "Pacific Standard Time"
    },
    "endTime": {
      "dateTime": "2026-08-06T18:00:00",
      "timeZone": "Pacific Standard Time"
    },
    "availabilityViewInterval": 60
  }
}
```

**預期輸出**: 一個包含每個使用者在指定時間範圍內詳細空閒/忙碌時段的列表。

### 9. `graph_listCalendarView`

檢索使用者在指定時間範圍內的日曆事件實例 (包含重複事件的展開)。

| 參數 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `startDateTime` | string | 是 | 時間範圍的開始時間 (ISO 8601)。 |
| `endDateTime` | string | 是 | 時間範圍的結束時間 (ISO 8601)。 |
| `userId` | string | 否 | 使用者的 ID 或 `userPrincipalName`。 |
| `top` | integer | 否 | 回傳的最大事件數量。 |
| `orderby` | string | 否 | OData `orderby` 排序語句。 |

**使用範例**:

```json
{
  "tool": "graph_listCalendarView",
  "parameters": {
    "startDateTime": "2026-08-01T00:00:00Z",
    "endDateTime": "2026-08-07T23:59:59Z",
    "orderby": "start/dateTime"
  }
}
```

**預期輸出**: 一個在指定時間範圍內發生的事件列表。

### 10. `graph_listEvents`

從指定的使用者日曆中檢索事件列表。

| 參數 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `userId` | string | 否 | 使用者的 ID 或 `userPrincipalName`。 |
| `startDateTime` | string | 否 | 時間範圍的開始時間 (ISO 8601)。 |
| `endDateTime` | string | 否 | 時間範圍的結束時間 (ISO 8601)。 |
| `top` | integer | 否 | 回傳的最大事件數量。 |
| `filter` | string | 否 | OData `$filter` 查詢語句。 |
| `orderby` | string | 否 | OData `orderby` 排序語句。 |

**使用範例**:

```json
{
  "tool": "graph_listEvents",
  "parameters": {
    "filter": "contains(subject, 'Q3')",
    "top": 5
  }
}
```

**預期輸出**: 一個符合篩選條件的事件列表。

### 11. `graph_updateEvent`

更新指定使用者日曆中的現有事件。

| 參數 | 類型 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `eventId` | string | 是 | 要更新的事件的唯一 ID。 |
| `userId` | string | 否 | 使用者的 ID 或 `userPrincipalName`。 |
| `subject` | string | 否 | 更新後的事件主題。 |
| `body` | object | 否 | 更新後的事件內文。 |
| `start` | object | 否 | 更新後的開始時間。 |
| `end` | object | 否 | 更新後的結束時間。 |
| `location` | object | 否 | 更新後的地點。 |
| `attendees_addresses` | array | 否 | 更新後的參與者郵件列表。 |
| `isCancelled` | boolean | 否 | 設定為 `true` 以取消事件。 |

**使用範例**:

```json
{
  "tool": "graph_updateEvent",
  "parameters": {
    "eventId": "AAMkAGI3...",
    "location": {
      "displayName": "會議室 501"
    }
  }
}
```

**預期輸出**: 成功更新後的事件物件。
