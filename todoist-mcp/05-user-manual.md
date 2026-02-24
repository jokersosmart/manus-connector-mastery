# Todoist 連結器使用者操作手冊

**作者：** Joker

## 歡迎！

本手冊是為終端使用者設計的實用操作指南。與其深入探討複雜的技術細節，我們將聚焦於您——使用者——最關心的部分：如何透過簡單的日常語言，讓 Manus AI 成為您管理 Todoist 的得力助手。

在本手冊中，每一個功能都將以「任務」為導向。我們將展示一個具體的自然語言指令，並揭示 Manus 在幕後是如何將您的話語轉換為精確的「工具調用」(Tool Call)。這將幫助您不僅知其然，更知其所以然，從而更自信、更創造性地使用本連結器。

讓我們開始吧！

## 第一章：基礎任務管理 (Basic Task Management)

本章涵蓋了您每天都會用到的最核心的任務管理操作。

### 1.1 建立一個新任務

這是最基礎的操作，將一個想法或待辦事項快速記錄下來。

**您的指令：**

> "remind me to pick up the laundry tomorrow at 6pm"

**Manus 的幕後工作：**

Manus 聽到這個指令後，會立刻理解您的意圖，並在內部構建一個對 `add_tasks` 工具的調用。它會智能地解析出任務的內容和時間。

```json
{
  "tool_name": "add_tasks",
  "parameters": {
    "tasks": [
      {
        "content": "pick up the laundry",
        "dueString": "tomorrow at 6pm"
      }
    ]
  }
}
```

### 1.2 尋找您的任務

當您想知道今天或某個專案裡有哪些事情需要處理時，可以使用查詢功能。

**您的指令：**

> "show me all my tasks in the 'Work' project that are due this week"

**Manus 的幕後工作：**

Manus 會啟用 `find_tasks` 工具，並將您的自然語言條件轉換為精確的篩選參數。

```json
{
  "tool_name": "find_tasks",
  "parameters": {
    "projectName": "Work",
    "dueDate": "this week"
  }
}
```

### 1.3 完成一個任務

當您完成了一項待辦，告訴 Manus 來劃掉它。

**您的指令：**

> "I've finished the task 'pick up the laundry', please mark it as done."

**Manus 的幕後工作：**

Manus 會先使用 `find_tasks` 找到名為「pick up the laundry」的任務並獲取其 ID（假設為 `123456`），然後調用 `complete_tasks` 工具。

```json
{
  "tool_name": "complete_tasks",
  "parameters": {
    "ids": ["123456"]
  }
}
```

## 第二章：組織您的專案 (Organizing Your Projects)

任務需要被組織在專案和區塊中，才能保持清晰。本章將教您如何用語言來構建您的工作空間。

### 2.1 建立一個新專案

當您有一個新目標或新想法時，為它建立一個專屬的空間。

**您的指令：**

> "I want to start a new project for my 'Home Renovation' plans."

**Manus 的幕後工作：**

Manus 會識別出「專案」這個關鍵詞，並調用 `add_projects` 工具。

```json
{
  "tool_name": "add_projects",
  "parameters": {
    "projects": [
      {
        "name": "Home Renovation"
      }
    ]
  }
}
```

### 2.2 在專案中新增區塊

區塊能幫助您將一個大專案分解成幾個管理階段。

**您的指令：**

> "In my 'Home Renovation' project, create three sections: 'Planning', 'Execution', and 'Review'."

**Manus 的幕後工作：**

Manus 會先找到「Home Renovation」專案的 ID，然後批次調用 `add_sections` 工具。

```json
{
  "tool_name": "add_sections",
  "parameters": {
    "sections": [
      {
        "projectId": "project-id-of-home-renovation",
        "name": "Planning"
      },
      {
        "projectId": "project-id-of-home-renovation",
        "name": "Execution"
      },
      {
        "projectId": "project-id-of-home-renovation",
        "name": "Review"
      }
    ]
  }
}
```

## 第三章：進階任務設定 (Advanced Task Settings)

當您需要對任務進行更精細的控制時，可以使用以下指令。

### 3.1 為任務增加細節

您可以為任務設定優先級、貼上標籤，或添加詳細描述。

**您的指令：**

> "add a task to 'Prepare Q1 financial report' in my 'Work' project, set it to the highest priority, and add the 'finance' and 'report' labels."

**Manus 的幕後工作：**

Manaus 會在 `add_tasks` 的調用中，加入 `priority` 和 `labels` 參數。

```json
{
  "tool_name": "add_tasks",
  "parameters": {
    "tasks": [
      {
        "content": "Prepare Q1 financial report",
        "projectId": "project-id-of-work",
        "priority": "p1",
        "labels": ["finance", "report"]
      }
    ]
  }
}
```

### 3.2 建立子任務

將一個複雜的任務分解成幾個小步驟，使之更易於管理。

**您的指令：**

> "Under the task 'Prepare Q1 financial report', add a subtask to 'Gather all the sales data'."

**Manus 的幕後工作：**

Manus 會先找到父任務「Prepare Q1 financial report」的 ID，然後在 `add_tasks` 調用中使用 `parentId` 參數來建立子任務。

```json
{
  "tool_name": "add_tasks",
  "parameters": {
    "tasks": [
      {
        "content": "Gather all the sales data",
        "parentId": "parent-task-id-of-report"
      }
    ]
  }
}
```

### 3.3 指派任務給協作者

在團隊專案中，您可以將任務直接指派給某位成員。

**您的指令：**

> "Assign the task 'Review the new design mockups' to Joker."

**Manus 的幕後工作：**

Manaus 會在 `add_tasks` 或 `update_tasks` 的調用中，使用 `responsibleUser` 參數。它會根據姓名或 Email 在專案協作者中找到對應的人。

```json
{
  "tool_name": "update_tasks",
  "parameters": {
    "updates": [
      {
        "id": "task-id-of-mockups",
        "responsibleUser": "joker@manus.im"
      }
    ]
  }
}
```

---

您現在已經掌握了與 Manus 溝通以管理 Todoist 的所有核心技巧。請自由地組合這些指令，創造出最適合您的工作流程。當您忘記某個指令時，隨時可以回來查閱本手冊。祝您使用愉快！
