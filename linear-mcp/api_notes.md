根據瀏覽 Linear GraphQL API Schema 的結果，可以推斷出 Linear MCP 連接器可能包含的工具 (tool) 及其功能。主要分為兩大類：查詢 (Query) 和變更 (Mutation)。

### 查詢 (Query) 工具

*   `get_teams`: 獲取所有可管理的團隊資訊。
*   `get_team_members`: 獲取指定團隊的所有成員。
*   `get_issues`: 根據篩選條件獲取議題列表。
*   `get_issue`: 獲取單一議題的詳細資訊。
*   `get_projects`: 獲取專案列表。
*   `get_project`: 獲取單一專案的詳細資訊。
*   `get_cycles`: 獲取週期列表。
*   `get_cycle`: 獲取單一週期的詳細資訊。
*   `get_comments`: 獲取議題的評論。
*   `get_attachments`: 獲取議題的附件。

### 變更 (Mutation) 工具

*   `create_issue`: 建立一個新的議題。
*   `update_issue`: 更新現有議題的資訊 (例如：標題、描述、狀態、指派對象等)。
*   `delete_issue`: 刪除一個議題。
*   `create_project`: 建立一個新的專案。
*   `update_project`: 更新現有專案的資訊。
*   `delete_project`: 刪除一個專案。
*   `create_cycle`: 建立一個新的週期。
*   `update_cycle`: 更新現有週期的資訊。
*   `delete_cycle`: 刪除一個週期。
*   `create_comment`: 在議題上新增一則評論。
*   `update_comment`: 更新現有的評論。
*   `delete_comment`: 刪除一則評論。
*   `create_attachment`: 為議題新增一個附件。
*   `delete_attachment`: 刪除一個附件。
*   `link_github_pr`: 將 GitHub Pull Request 連結到議題。
*   `link_gitlab_mr`: 將 GitLab Merge Request 連結到議題。
