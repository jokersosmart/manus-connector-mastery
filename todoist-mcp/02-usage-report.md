# Todoist MCP 連結器使用說明報告

**作者：** Joker

## 總覽

本報告旨在提供一份關於 Manus MCP「Todoist」連結器的詳細使用說明。文件中將羅列所有可用的工具 (Tools)，並針對每一個工具提供其完整的參數說明、功能介紹、使用範例及預期輸出。本文件所記載的所有資訊，均基於對 `Doist/todoist-ai` GitHub 儲存庫 [1] 的原始碼分析。

## 工具列表

「Todoist」連結器提供了一套豐富的工具集，涵蓋了從任務、專案、區塊到評論的完整生命週期管理。以下是所有可用工具的詳細說明。

說明。

### 1. `add_comments`

**功能說明：**

此工具用於將一或多則評論新增至指定的任務 (Task) 或專案 (Project)。您必須為每一則評論提供 `taskId` 或 `projectId`，但不能兩者都提供。

**參數詳解：**

此工具接受一個名為 `comments` 的陣列，陣列中的每個物件都代表一則要新增的評論，其結構如下：

<table header-row="true">
  <tr>
    <td><b>參數</b></td>
    <td><b>類型</b></td>
    <td><b>必要性</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>`taskId`</td>
    <td>string</td>
    <td>可選</td>
    <td>要評論的任務 ID。與 `projectId` 二選一。</td>
  </tr>
  <tr>
    <td>`projectId`</td>
    <td>string</td>
    <td>可選</td>
    <td>要評論的專案 ID。可使用 "inbox" 來指定收件匣。與 `taskId` 二選一。</td>
  </tr>
  <tr>
    <td>`content`</td>
    <td>string</td>
    <td>必要</td>
    <td>評論的具體內容。</td>
  </tr>
</table>

**使用範例：**

假設您想為任務 `789123` 新增一則進度更新，並同時在專案 `456789` 中留下一則提醒。

```json
{
  "comments": [
    {
      "taskId": "789123",
      "content": "報告：初步設計已完成，待審核。"
    },
    {
      "projectId": "456789",
      "content": "@Joker 請記得在本週五前回覆客戶的郵件。"
    }
  ]
}
```

**預期輸出：**

工具執行成功後，會返回一個包含已新增評論資訊的物件，並提供一段總結性的文字說明。

```json
{
  "comments": [
    {
      "id": "234567",
      "taskId": "789123",
      "projectId": null,
      "postedAt": "2026-02-24T16:00:00Z",
      "content": "報告：初步設計已完成，待審核。"
    },
    {
      "id": "234568",
      "taskId": null,
      "projectId": "456789",
      "postedAt": "2026-02-24T16:00:01Z",
      "content": "@Joker 請記得在本週五前回覆客戶的郵件。"
    }
  ],
  "totalCount": 2,
  "addedCommentIds": ["234567", "234568"]
}
```

**文字輸出總結：**

> Added 1 task comment and 1 project comment

### 2. `add_projects`

**功能說明：**

此工具用於建立一或多個新的專案。您可以指定專案的名稱、父專案、是否為最愛以及預設的檢視樣式。

**參數詳解：**

此工具接受一個名為 `projects` 的陣列，陣列中的每個物件都代表一個要新增的專案，其結構如下：

<table header-row="true">
  <tr>
    <td><b>參數</b></td>
    <td><b>類型</b></td>
    <td><b>必要性</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>`name`</td>
    <td>string</td>
    <td>必要</td>
    <td>專案的名稱。</td>
  </tr>
  <tr>
    <td>`parentId`</td>
    <td>string</td>
    <td>可選</td>
    <td>父專案的 ID。若提供，則會建立為子專案。</td>
  </tr>
  <tr>
    <td>`isFavorite`</td>
    <td>boolean</td>
    <td>可選</td>
    <td>是否將專案設為最愛。預設為 `false`。</td>
  </tr>
  <tr>
    <td>`viewStyle`</td>
    <td>string</td>
    <td>可選</td>
    <td>專案的檢視樣式，可選值為 `list`、`board`、`calendar`。預設為 `list`。</td>
  </tr>
</table>

**使用範例：**

假設您要建立一個名為「2026 年 Q1 產品規劃」的新專案，並將其設為最愛，同時在另一個既有專案 `123456` 下建立一個名為「行銷活動」的子專案。

```json
{
  "projects": [
    {
      "name": "2026 年 Q1 產品規劃",
      "isFavorite": true,
      "viewStyle": "board"
    },
    {
      "name": "行銷活動",
      "parentId": "123456"
    }
  ]
}
```

**預期輸出：**

工具執行成功後，會返回包含已建立專案資訊的物件。

```json
{
  "projects": [
    {
      "id": "789012",
      "name": "2026 年 Q1 產品規劃",
      "color": "charcoal",
      "parentId": null,
      "isFavorite": true,
      "viewStyle": "board"
    },
    {
      "id": "789013",
      "name": "行銷活動",
      "color": "charcoal",
      "parentId": "123456",
      "isFavorite": false,
      "viewStyle": "list"
    }
  ],
  "totalCount": 2
}
```

**文字輸出總結：**

> Added 2 projects:
> • 2026 年 Q1 產品規劃 (id=789012)
> • 行銷活動 (id=789013)

### 3. `add_sections`

**功能說明：**

此工具用於在指定的專案中建立一或多個新的區塊 (Section)。區塊有助於將專案內的任務分組管理。

**參數詳解：**

此工具接受一個名為 `sections` 的陣列，陣列中的每個物件都代表一個要新增的區塊，其結構如下：

<table header-row="true">
  <tr>
    <td><b>參數</b></td>
    <td><b>類型</b></td>
    <td><b>必要性</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>`name`</td>
    <td>string</td>
    <td>必要</td>
    <td>區塊的名稱。</td>
  </tr>
  <tr>
    <td>`projectId`</td>
    <td>string</td>
    <td>必要</td>
    <td>要新增區塊的專案 ID。可使用 "inbox" 來指定收件匣。</td>
  </tr>
</table>

**使用範例：**

假設您要在專案 `789012`（即「2026 年 Q1 產品規劃」）中建立「待辦」、「進行中」和「已完成」三個區塊。

```json
{
  "sections": [
    {
      "name": "待辦",
      "projectId": "789012"
    },
    {
      "name": "進行中",
      "projectId": "789012"
    },
    {
      "name": "已完成",
      "projectId": "789012"
    }
  ]
}
```

**預期輸出：**

工具執行成功後，會返回包含已建立區塊資訊的物件。

```json
{
  "sections": [
    {
      "id": "345678",
      "projectId": "789012",
      "order": 1,
      "name": "待辦"
    },
    {
      "id": "345679",
      "projectId": "789012",
      "order": 2,
      "name": "進行中"
    },
    {
      "id": "345680",
      "projectId": "789012",
      "order": 3,
      "name": "已完成"
    }
  ],
  "totalCount": 3
}
```

**文字輸出總結：**

> Added 3 sections:
> • 待辦 (id=345678, projectId=789012)
> • 進行中 (id=345679, projectId=789012)
> • 已完成 (id=345680, projectId=789012)

### 4. `add_tasks`

**功能說明：**

此工具是 Todoist 連結器的核心功能之一，用於新增一或多個任務。它提供了豐富的參數，讓您可以精確控制任務的各個屬性，包括內容、描述、截止日期、優先級、標籤、所屬專案/區塊、子任務、以及指派對象等。

**參數詳解：**

此工具接受一個名為 `tasks` 的陣列，陣列中的每個物件都代表一個要新增的任務，其結構極為靈活：

<table header-row="true">
  <tr>
    <td><b>參數</b></td>
    <td><b>類型</b></td>
    <td><b>必要性</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>`content`</td>
    <td>string</td>
    <td>必要</td>
    <td>任務的名稱或標題。應簡潔明瞭，支援 Markdown。</td>
  </tr>
  <tr>
    <td>`description`</td>
    <td>string</td>
    <td>可選</td>
    <td>任務的詳細描述。支援 Markdown。</td>
  </tr>
  <tr>
    <td>`priority`</td>
    <td>string</td>
    <td>可選</td>
    <td>任務優先級，可選值為 `p1` (最高)、`p2`、`p3`、`p4` (預設)。</td>
  </tr>
  <tr>
    <td>`dueString`</td>
    <td>string</td>
    <td>可選</td>
    <td>以自然語言設定的截止日期，例如 "tomorrow at 5pm" 或 "every Friday"。</td>
  </tr>
  <tr>
    <td>`deadlineDate`</td>
    <td>string</td>
    <td>可選</td>
    <td>以 ISO 8601 格式 (YYYY-MM-DD) 設定的固定死線。</td>
  </tr>
  <tr>
    <td>`duration`</td>
    <td>string</td>
    <td>可選</td>
    <td>任務的預計持續時間，例如 "2h"、"90m"、"1.5h"。</td>
  </tr>
  <tr>
    <td>`labels`</td>
    <td>array</td>
    <td>可選</td>
    <td>要附加到任務的標籤名稱陣列，例如 `["work", "urgent"]`。</td>
  </tr>
  <tr>
    <td>`projectId`</td>
    <td>string</td>
    <td>可選</td>
    <td>任務所屬的專案 ID。可使用 "inbox"。</td>
  </tr>
  <tr>
    <td>`sectionId`</td>
    <td>string</td>
    <td>可選</td>
    <td>任務所屬的區塊 ID。</td>
  </tr>
  <tr>
    <td>`parentId`</td>
    <td>string</td>
    <td>可選</td>
    <td>父任務的 ID，用於建立子任務。</td>
  </tr>
  <tr>
    <td>`order`</td>
    <td>number</td>
    <td>可選</td>
    <td>任務在同級中的排序位置。</td>
  </tr>
  <tr>
    <td>`responsibleUser`</td>
    <td>string</td>
    <td>可選</td>
    <td>指派任務給特定使用者，可以是使用者 ID、姓名或 Email。該使用者必須是專案的協作者。</td>
  </tr>
  <tr>
    <td>`isUncompletable`</td>
    <td>boolean</td>
    <td>可選</td>
    <td>設為 `true` 可建立一個不可完成的標題式任務，用於組織結構。</td>
  </tr>
</table>

**使用範例：**

假設您要在專案 `789012` 的「進行中」區塊 (`345679`) 新增一個高優先級任務，並指派給 `Joker`。

```json
{
  "tasks": [
    {
      "content": "完成 `02-usage-report.md` 的撰寫",
      "description": "需要包含所有工具的詳細說明、參數、範例和預期輸出。",
      "projectId": "789012",
      "sectionId": "345679",
      "priority": "p1",
      "dueString": "today at 8pm",
      "labels": ["documentation", "urgent"],
      "responsibleUser": "joker@manus.im"
    }
  ]
}
```

**預期輸出：**

工具執行成功後，會返回包含已建立任務詳細資訊的物件。

```json
{
  "tasks": [
    {
      "id": "987654",
      "content": "完成 `02-usage-report.md` 的撰寫",
      "description": "需要包含所有工具的詳細說明、參數、範例和預期輸出。",
      "priority": "p1",
      "dueDate": "2026-02-24T20:00:00",
      "labels": ["documentation", "urgent"],
      "projectId": "789012",
      "sectionId": "345679",
      "responsibleUid": "user-id-of-joker"
    }
  ],
  "totalCount": 1
}
```

**文字輸出總結：**

> Added 1 task to sections: • 完成 `02-usage-report.md` 的撰寫 (id=987654)

### 5. `complete_tasks`

**功能說明：**

此工具用於將一或多個任務標示為完成。這是一個破壞性操作，但對於自動化工作流程至關重要。

**參數詳解：**

此工具接受一個名為 `ids` 的陣列，其中包含要完成的任務 ID。

<table header-row="true">
  <tr>
    <td><b>參數</b></td>
    <td><b>類型</b></td>
    <td><b>必要性</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>`ids`</td>
    <td>array</td>
    <td>必要</td>
    <td>一個包含一或多個任務 ID 字串的陣列。</td>
  </tr>
</table>

**使用範例：**

假設您已經完成了任務 `987654`。

```json
{
  "ids": ["987654"]
}
```

**預期輸出：**

工具會返回一個操作結果的摘要，包含成功、失敗的任務列表與計數。

```json
{
  "completed": ["987654"],
  "failures": [],
  "totalRequested": 1,
  "successCount": 1,
  "failureCount": 0
}
```

**文字輸出總結：**

> Completed tasks: 1 of 1 succeeded.
> • 987654

### 6. `delete_object`

**功能說明：**

此工具提供了一個統一的介面，用於刪除指定 ID 的物件，可以是專案 (project)、區塊 (section)、任務 (task) 或評論 (comment)。這是一個高風險的破壞性操作，使用時需特別小心。

**參數詳解：**

<table header-row="true">
  <tr>
    <td><b>參數</b></td>
    <td><b>類型</b></td>
    <td><b>必要性</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>`type`</td>
    <td>string</td>
    <td>必要</td>
    <td>要刪除的物件類型，可選值為 `project`、`section`、`task`、`comment`。</td>
  </tr>
  <tr>
    <td>`id`</td>
    <td>string</td>
    <td>必要</td>
    <td>要刪除的物件的唯一 ID。</td>
  </tr>
</table>

**使用範例：**

假設您要刪除先前建立的區塊 `345680`（已完成）。

```json
{
  "type": "section",
  "id": "345680"
}
```

**預期輸出：**

工具執行成功後，會返回一個確認資訊。

```json
{
  "deletedEntity": {
    "type": "section",
    "id": "345680"
  },
  "success": true
}
```

**文字輸出總結：**

> Deleted section: id=345680

### 7. `fetch_object`

**功能說明：**

此工具用於根據指定的類型和 ID，獲取單一物件（任務、專案、評論或區塊）的完整詳細資訊。當您需要查詢特定物件的屬性時，這是一個非常有用的工具。

**參數詳解：**

<table header-row="true">
  <tr>
    <td><b>參數</b></td>
    <td><b>類型</b></td>
    <td><b>必要性</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>`type`</td>
    <td>string</td>
    <td>必要</td>
    <td>要獲取的物件類型，可選值為 `task`、`project`、`comment`、`section`。</td>
  </tr>
  <tr>
    <td>`id`</td>
    <td>string</td>
    <td>必要</td>
    <td>要獲取的物件的唯一 ID。</td>
  </tr>
</table>

**使用範例：**

假設您想查看專案 `789012` 的詳細資訊。

```json
{
  "type": "project",
  "id": "789012"
}
```

**預期輸出：**

工具執行成功後，會返回包含該物件所有屬性的完整資訊。

```json
{
  "type": "project",
  "id": "789012",
  "object": {
    "id": "789012",
    "name": "2026 年 Q1 產品規劃",
    "color": "charcoal",
    "parentId": null,
    "isFavorite": true,
    "viewStyle": "board",
    "isShared": false,
    "inboxProject": false
  }
}
```

**文字輸出總結：**

> Found project: 2026 年 Q1 產品規劃 • id=789012 • color=charcoal • viewStyle=board

### 8. `fetch`

**功能說明：**

此工具專為 OpenAI MCP 規範設計，用於根據特定格式的 ID 獲取任務或專案的完整內容。它會將物件的主要資訊（如內容、描述、截止日期等）組合成一段文字，並提供結構化的元數據。

**參數詳解：**

<table header-row="true">
  <tr>
    <td><b>參數</b></td>
    <td><b>類型</b></td>
    <td><b>必要性</b></td>
    <td><b>說明</b></td>
  </tr>
  <tr>
    <td>`id`</td>
    <td>string</td>
    <td>必要</td>
    <td>格式為 `"task:{id}"` 或 `"project:{id}"` 的唯一識別碼。</td>
  </tr>
</table>

**使用範例：**

假設您想獲取任務 `987654` 的內容。

```json
{
  "id": "task:987654"
}
```

**預期輸出：**

工具會返回一個 JSON 物件，其中 `text` 欄位包含了格式化的文字內容，同時也提供結構化的 `metadata`。

```json
{
  "id": "task:987654",
  "title": "完成 `02-usage-report.md` 的撰寫",
  "text": "完成 `02-usage-report.md` 的撰寫\n\nDescription: 需要包含所有工具的詳細說明、參數、範例和預期輸出。\nDue: 2026-02-24T20:00:00\nLabels: documentation, urgent",
  "url": "https://todoist.com/showTask?id=987654",
  "metadata": {
    "priority": "p1",
    "projectId": "789012",
    "sectionId": "345679",
    "responsibleUid": "user-id-of-joker"
  }
}
```

**文字輸出總結：**

輸出結果會是一個完整的 JSON 字串。

---

## 參考資料

[1] Doist. (2026). *todoist-ai* [原始碼]. GitHub. https://github.com/Doist/todoist-ai
