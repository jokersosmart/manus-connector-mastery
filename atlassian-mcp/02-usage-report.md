# Atlassian 連接器使用說明報告

作者：Joker

## 前言

本文件詳細介紹 Manus MCP Atlassian 連接器提供的所有工具。由於無法直接查詢 `atlassian` 伺服器，此處的工具列表與參數是基於對 Atlassian 核心產品（Jira, Confluence）功能的分析以及第一階段遞迴分析的洞察而建構的，旨在反映最核心與高價值的使用場景。這些工具的設計核心是為了解決使用者在跨產品（特別是 Jira 與 Confluence）操作時的繁瑣與重複性問題。

## 工具總覽

Atlassian 連接器主要分為兩大模組：**Jira 工具集**和 **Confluence 工具集**。這些工具讓使用者能以自然語言無縫地在 Manus 環境中操作這兩個平台。

--- 

## Jira 工具集

### 1. `jira_create_issue`

此工具用於在指定的 Jira 專案中建立一個新的議題 (Issue)。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `project_key` | String | 是 | Jira 專案的鍵值 (例如："PROJ") |
| `summary` | String | 是 | 議題的標題或摘要 |
| `description` | String | 否 | 議題的詳細描述，支援 Markdown 格式 |
| `issue_type` | String | 是 | 議題的類型 (例如："Task", "Bug", "Story") |
| `assignee` | String | 否 | 議題的指派對象的使用者名稱 |
| `priority` | String | 否 | 議題的優先級 (例如："High", "Medium", "Low") |

**使用範例：**

> 在 PROJ 專案中建立一個新的任務，標題是「設計新的登入頁面」，描述為「需要包含 OAuth 和兩步驟驗證」，指派給 Joker。

**預期輸出：**

成功建立後，將返回新議題的 ID、鍵值和可直接在瀏覽器中開啟的 URL。

```json
{
  "status": "Success",
  "issue_id": "1001",
  "issue_key": "PROJ-124",
  "issue_url": "https://your-atlassian-site.atlassian.net/browse/PROJ-124"
}
```

### 2. `jira_search_issues`

使用 Jira 查詢語言 (JQL) 來搜尋議題。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `jql` | String | 是 | 一段有效的 JQL 查詢語句 |
| `max_results` | Integer | 否 | 返回的最大結果數量，預設為 50 |

**使用範例：**

> 搜尋在 PROJ 專案中，所有指派給我且狀態為「進行中」的議題。

**預期輸出：**

返回一個包含符合條件的議題列表，每個議題包含其關鍵資訊。

```json
{
  "status": "Success",
  "count": 2,
  "issues": [
    {
      "key": "PROJ-110",
      "summary": "開發後端 API",
      "status": "In Progress"
    },
    {
      "key": "PROJ-115",
      "summary": "撰寫單元測試",
      "status": "In Progress"
    }
  ]
}
```

--- 

## Confluence 工具集

### 1. `confluence_create_page`

在指定的 Confluence 空間中建立一個新頁面。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `space_key` | String | 是 | Confluence 空間的鍵值 (例如："DOCS") |
| `title` | String | 是 | 頁面的標題 |
| `content` | String | 是 | 頁面的內容，支援 Confluence Wiki Markup 和 Markdown |
| `parent_page_id` | String | 否 | 父頁面的 ID，若指定，則新頁面會成為其子頁面 |

**使用範例：**

> 在 DOCS 空間建立一個名為「2025 Q4 產品藍圖」的頁面，內容是「這是 Q4 產品藍圖的草稿」。

**預期輸出：**

成功建立後，將返回新頁面的 ID 和 URL。

```json
{
  "status": "Success",
  "page_id": "2003",
  "page_url": "https://your-atlassian-site.atlassian.net/wiki/spaces/DOCS/pages/2003"
}
```

### 2. `confluence_update_page`

更新一個既有的 Confluence 頁面內容。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `page_id` | String | 是 | 要更新的頁面的 ID |
| `content` | String | 是 | 新的頁面內容 |
| `version_comment` | String | 否 | 此次更新的版本註解 |

**使用範例：**

> 將 ID 為 2003 的頁面內容更新為「這是 Q4 產品藍圖的最終版本」。

**預期輸出：**

成功更新後，將返回頁面 ID 和新版本號。

```json
{
  "status": "Success",
  "page_id": "2003",
  "new_version": 2
}
```
