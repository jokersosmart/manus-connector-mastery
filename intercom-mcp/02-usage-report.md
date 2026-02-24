# 「Linear」連結器使用說明報告

**作者：Joker**

---

本報告提供 Manus MCP「Linear」連結器所有可用工具的詳細使用說明，旨在幫助使用者充分利用此連結器，在 Manus Agent 中無縫地與 Linear 專案管理平台進行互動。

## 工具總覽

「Linear」連結器提供的工具主要分為兩大類：

*   **查詢工具 (Query Tools):** 用於從 Linear 獲取資訊，例如查詢議題、專案或團隊成員。
*   **變更工具 (Mutation Tools):** 用於修改 Linear 中的資料，例如建立議題、更新專案狀態或新增評論。

以下將針對每個工具的詳細參數、功能、使用範例及預期輸出進行說明。

---

## 查詢工具 (Query Tools)

### 1. `get_teams`

此工具用於獲取使用者所有可存取的 Linear 團隊列表。

**參數:** 無

**使用範例:**

```json
{
  "tool": "linear.get_teams"
}
```

**預期輸出:**

```json
{
  "result": [
    {
      "id": "TEAM-123",
      "name": "Frontend Team",
      "key": "FE"
    },
    {
      "id": "TEAM-456",
      "name": "Backend Team",
      "key": "BE"
    }
  ]
}
```

### 2. `get_team_members`

此工具用於獲取指定團隊的所有成員列表。

**參數:**

*   `team_id` (string, required): 團隊的唯一識別碼。

**使用範例:**

```json
{
  "tool": "linear.get_team_members",
  "team_id": "TEAM-123"
}
```

**預期輸出:**

```json
{
  "result": [
    {
      "id": "USER-001",
      "name": "Alice",
      "email": "alice@example.com"
    },
    {
      "id": "USER-002",
      "name": "Bob",
      "email": "bob@example.com"
    }
  ]
}
```

### 3. `get_issues`

此工具用於根據篩選條件獲取議題列表。

**參數:**

*   `team_id` (string, optional): 篩選特定團隊的議題。
*   `project_id` (string, optional): 篩選特定專案的議題。
*   `cycle_id` (string, optional): 篩選特定週期的議題。
*   `assignee_id` (string, optional): 篩選指派給特定成員的議題。
*   `status` (string, optional): 篩選特定狀態的議題 (例如：'Todo', 'In Progress', 'Done')。
*   `priority` (string, optional): 篩選特定優先層級的議題 (例如：'High', 'Medium', 'Low')。

**使用範例:**

```json
{
  "tool": "linear.get_issues",
  "team_id": "TEAM-123",
  "status": "In Progress"
}
```

**預期輸出:**

```json
{
  "result": [
    {
      "id": "ISSUE-789",
      "title": "Fix login button styling",
      "status": "In Progress",
      "assignee": "Alice"
    }
  ]
}
```

### 4. `get_issue`

此工具用於獲取單一議題的詳細資訊。

**參數:**

*   `issue_id` (string, required): 議題的唯一識別碼 (例如 'FE-123') 或 UUID。

**使用範例:**

```json
{
  "tool": "linear.get_issue",
  "issue_id": "ISSUE-789"
}
```

**預期輸出:**

```json
{
  "result": {
    "id": "ISSUE-789",
    "title": "Fix login button styling",
    "description": "The login button on the main page is not aligned correctly.",
    "status": "In Progress",
    "assignee": "Alice",
    "project": "Website Redesign",
    "cycle": "Sprint 3"
  }
}
```
