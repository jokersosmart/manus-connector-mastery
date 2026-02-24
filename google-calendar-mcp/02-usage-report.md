# Google Calendar 連結器使用說明報告

**作者：Joker**

---

## 總覽

Google Calendar 連結器提供了一套強大的工具，讓使用者可以透過自然語言無縫地管理他們的日曆。基於第一階段的遞迴分析，我們設計了一組以使用者心智模型為中心的、最小化且功能正交的工具集。這組工具旨在最大化意圖識別的準確率，提供真正流暢的自然語言操作體驗。

本報告將詳細介紹每個可用工具的功能、參數、使用範例及預期輸出。

---

## 工具集詳解

### 1. `create_event`

此工具用於在 Google Calendar 中建立新事件。

#### 功能說明

`create_event` 工具能理解多樣化的自然語言指令，從中提取事件的核心要素（主旨、時間、參與者、地點），並在日曆上建立一個新的活動。它支援指定精確的時間點、時間段，以及邀請參與者和設定地點。

#### 參數

| 參數 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `summary` | String | 事件的標題或主旨。 | 是 |
| `start_time` | String | 事件的開始時間 (ISO 8601 格式)。 | 是 |
| `end_time` | String | 事件的結束時間 (ISO 8601 格式)。 | 是 |
| `attendees` | Array of Strings | 參與者的電子郵件地址列表。 | 否 |
| `location` | String | 事件的地點或會議連結。 | 否 |
| `description` | String | 事件的詳細描述。 | 否 |

#### 使用範例

**自然語言指令**：
> 「幫我安排一個會議，主題是『第四季規劃』，時間是明天下午 2 點到 3 點，邀請 `joker@manus.im`，地點在會議室 A。」

**對應的工具調用 (JSON)**：
```json
{
  "tool": "google_calendar.create_event",
  "parameters": {
    "summary": "第四季規劃",
    "start_time": "2026-02-25T14:00:00Z",
    "end_time": "2026-02-25T15:00:00Z",
    "attendees": ["joker@manus.im"],
    "location": "會議室 A"
  }
}
```

#### 預期輸出

成功建立事件後，工具將回傳一個確認訊息，包含事件的標題、時間和連結。

```json
{
  "status": "success",
  "message": "成功建立事件：第四季規劃",
  "event_details": {
    "id": "evt_123456789",
    "summary": "第四季規劃",
    "start_time": "2026-02-25T14:00:00Z",
    "end_time": "2026-02-25T15:00:00Z",
    "url": "https://calendar.google.com/event?eid=..."
  }
}
```

---

### 2. `find_events`

此工具用於搜尋 Google Calendar 中的事件。

#### 功能說明

`find_events` 讓使用者可以根據時間範圍、關鍵字、參與者或地點來查詢日曆上的活動。它能幫助使用者快速找到特定會議或了解某段時間內的行程安排。

#### 參數

| 參數 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `start_time` | String | 搜尋範圍的開始時間 (ISO 8601 格式)。 | 是 |
| `end_time` | String | 搜尋範圍的結束時間 (ISO 8601 格式)。 | 是 |
| `query` | String | 用於搜尋事件標題或描述的關鍵字。 | 否 |
| `attendee` | String | 根據參與者電子郵件地址進行篩選。 | 否 |

#### 使用範例

**自然語言指令**：
> 「幫我看看下週有哪些跟『專案 Alpha』相關的會議。」

**對應的工具調用 (JSON)**：
```json
{
  "tool": "google_calendar.find_events",
  "parameters": {
    "start_time": "2026-03-02T00:00:00Z",
    "end_time": "2026-03-08T23:59:59Z",
    "query": "專案 Alpha"
  }
}
```

#### 預期輸出

工具將回傳一個符合條件的事件列表。

```json
{
  "status": "success",
  "count": 2,
  "events": [
    {
      "id": "evt_abcdef123",
      "summary": "專案 Alpha 啟動會議",
      "start_time": "2026-03-03T10:00:00Z",
      "end_time": "2026-03-03T11:00:00Z"
    },
    {
      "id": "evt_ghijk456",
      "summary": "專案 Alpha 進度同步",
      "start_time": "2026-03-05T15:00:00Z",
      "end_time": "2026-03-05T15:30:00Z"
    }
  ]
}
```

---

### 3. `update_event`

此工具用於修改 Google Calendar 中的現有事件。

#### 功能說明

`update_event` 允許使用者更新一個已存在事件的屬性，例如更改時間、標題、地點或增刪參與者。使用者需要提供足夠的資訊來唯一確定要修改的事件。

#### 參數

| 參數 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `event_id` | String | 要修改的事件的唯一 ID。如果提供，將優先使用此參數。 | 否 |
| `original_summary`| String | 要修改事件的原始標題 (用於在無 event_id 時查找)。 | 否 |
| `original_start_time`| String | 要修改事件的原始開始時間 (用於在無 event_id 時查找)。 | 否 |
| `new_summary` | String | 新的事件標題。 | 否 |
| `new_start_time`| String | 新的開始時間。 | 否 |
| `new_end_time` | String | 新的結束時間。 | 否 |
| `new_attendees` | Array of Strings | 新的參與者列表 (會覆蓋原有列表)。 | 否 |

#### 使用範例

**自然語言指令**：
> 「把明天下午的『第四季規劃』會議延後一小時。」

**對應的工具調用 (JSON)**：
```json
{
  "tool": "google_calendar.update_event",
  "parameters": {
    "original_summary": "第四季規劃",
    "original_start_time": "2026-02-25T14:00:00Z",
    "new_start_time": "2026-02-25T15:00:00Z",
    "new_end_time": "2026-02-25T16:00:00Z"
  }
}
```

#### 預期輸出

成功更新後，回傳更新後的事件詳情。

```json
{
  "status": "success",
  "message": "成功更新事件：第四季規劃",
  "updated_event": {
    "id": "evt_123456789",
    "summary": "第四季規劃",
    "start_time": "2026-02-25T15:00:00Z",
    "end_time": "2026-02-25T16:00:00Z"
  }
}
```

---

### 4. `delete_event`

此工具用於從 Google Calendar 中刪除事件。

#### 功能說明

`delete_event` 讓使用者可以刪除一個不再需要的日曆事件。同樣地，需要提供足夠資訊來定位要刪除的事件。

#### 參數

| 參數 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `event_id` | String | 要刪除的事件的唯一 ID。 | 否 |
| `summary` | String | 要刪除事件的標題。 | 否 |
| `start_time` | String | 要刪除事件的開始時間。 | 否 |

#### 使用範例

**自然語言指令**：
> 「取消下週一的『專案 Alpha 啟動會議』。」

**對應的工具調用 (JSON)**：
```json
{
  "tool": "google_calendar.delete_event",
  "parameters": {
    "summary": "專案 Alpha 啟動會議",
    "start_time": "2026-03-03T10:00:00Z"
  }
}
```

#### 預期輸出

成功刪除後，回傳確認訊息。

```json
{
  "status": "success",
  "message": "事件『專案 Alpha 啟動會議』已成功刪除。"
}
```

---

### 5. `check_availability`

此工具用於查詢指定時間範圍內的空閒時段。

#### 功能說明

`check_availability` 幫助使用者快速找到自己或他人的空閒時間，以便安排新的會議或活動。它可以考慮多個參與者，並找出所有人都可用的共同時段。

#### 參數

| 參數 | 類型 | 描述 | 是否必須 |
| :--- | :--- | :--- | :--- |
| `start_time` | String | 查詢範圍的開始時間。 | 是 |
| `end_time` | String | 查詢範圍的結束時間。 | 是 |
| `attendees` | Array of Strings | 需要檢查空閒時間的參與者郵件列表。 | 否 |
| `duration_minutes` | Integer | 所需空閒時段的長度（分鐘）。 | 否 |

#### 使用範例

**自然語言指令**：
> 「幫我找一下我和 `joker@manus.im` 明天有空的 30 分鐘時段。」

**對應的工具調用 (JSON)**：
```json
{
  "tool": "google_calendar.check_availability",
  "parameters": {
    "start_time": "2026-02-25T09:00:00Z",
    "end_time": "2026-02-25T18:00:00Z",
    "attendees": ["me", "joker@manus.im"],
    "duration_minutes": 30
  }
}
```

#### 預期輸出

工具將回傳一個或多個建議的空閒時段列表。

```json
{
  "status": "success",
  "available_slots": [
    {
      "start": "2026-02-25T11:00:00Z",
      "end": "2026-02-25T11:30:00Z"
    },
    {
      "start": "2026-02-25T16:00:00Z",
      "end": "2026-02-25T17:30:00Z"
    }
  ]
}
```
