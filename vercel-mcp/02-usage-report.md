# Notion MCP 連結器使用說明報告

**作者：** Joker

---

本報告提供 Manus MCP Notion 連結器的詳細使用說明，涵蓋所有可用工具的功能、參數、使用範例及預期輸出。

## 1. `notion-search`

### 功能說明

`notion-search` 工具提供了一個強大的搜尋功能，允許使用者在整個 Notion 工作區及已連接的第三方應用（如 Slack, Google Drive, GitHub 等）中進行語意搜尋。它還可以專門用於搜尋工作區內的使用者。此工具能根據使用者的權限自動選擇使用 Notion AI 進行更廣泛的搜尋，或進行僅限於工作區的快速搜尋。

為了獲得最佳的搜尋結果，建議在獲取搜尋結果後，使用 `notion-fetch` 工具來抓取頁面或資料庫的完整內容。若要在特定的資料庫內進行搜尋，應先用 `notion-fetch` 獲取該資料庫的 `data_source_url`。

### 參數詳解

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `query` | string | 是 | 搜尋的關鍵字或語意化問題。也可以是使用者姓名或 email 的一部分。 |
| `query_type` | string | 否 | 搜尋類型，可選值為 `internal`（內容搜尋）或 `user`（使用者搜尋）。預設為 `internal`。 |
| `content_search_mode` | string | 否 | 覆寫自動的 AI 搜尋選擇。 |
| `data_source_url` | string | 否 | 指定一個資料來源 (Data Source) 的 URL，將搜尋範圍限制在該資料來源內。 |
| `page_url` | string | 否 | 指定一個頁面的 URL 或 ID，將搜尋範圍限制在該頁面及其子頁面內。 |
| `teamspace_id` | string | 否 | 指定一個團隊空間 (Teamspace) 的 ID，將搜尋結果限制在該空間內。 |
| `filters` | object | 否 | 一個包含過濾條件的物件，僅在 `query_type` 為 `internal` 時有效。 |

#### `filters` 物件結構

| 屬性 | 類型 | 說明 |
| :--- | :--- | :--- |
| `created_date_range` | object | 按建立日期進行過濾。包含 `start_date` 和 `end_date` 兩個屬性。 |
| `created_by_user_ids` | array | 一個包含使用者 ID 的陣列，用於過濾由特定使用者建立的內容。 |

### 使用範例

#### 範例 1：在整個工作區搜尋文件

```json
{
  "query": "2024年第一季度的營收報告",
  "query_type": "internal",
  "filters": {
    "created_date_range": {
      "start_date": "2024-01-01",
      "end_date": "2024-04-01"
    }
  }
}
```

**預期輸出：**
返回在 2024 年第一季度建立且內容與「營收報告」相關的頁面列表。

#### 範例 2：在特定資料庫中搜尋由特定成員建立的任務

```json
{
  "query": "修復登入頁面 Bug",
  "data_source_url": "collection://f336d0bc-b841-465b-8045-024475c079dd",
  "filters": {
    "created_by_user_ids": ["a1b2c3d4-e5f6-7890-abcd-ef1234567890"]
  }
}
```

**預期輸出：**
在指定的資料來源中，返回由使用者 `a1b2c3d4-...` 建立且與「修復登入頁面 Bug」相關的頁面。

#### 範例 3：搜尋使用者

```json
{
  "query": "joker@manus.im",
  "query_type": "user"
}
```

**預期輸出：**
返回名稱或 email 中包含 "joker@manus.im" 的使用者列表。

## 2. `notion-fetch`

### 功能說明

`notion-fetch` 工具用於根據指定的 URL 或 ID 來檢索 Notion 實體（包括頁面、資料庫或資料來源）的詳細資訊。

當抓取一個頁面時，其內容會以 Notion 增強型 Markdown 格式返回。要獲取完整的格式規範，可以抓取 `notion://docs/enhanced-markdown-spec` 資源。

當抓取一個資料庫時，工具會返回其包含的所有資料來源（collections）。每個資料來源都有一個唯一的 ID，顯示在 `<data-source url="collection://...">` 標籤中。這個 ID 可以被直接用於 `notion-fetch` 或其他工具（如 `update-data-source`）來操作特定的資料來源。

此外，可以透過設定 `include_discussions` 為 `true` 來一併獲取頁面的留言與討論資訊。

### 參數詳解

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `id` | string | 是 | 要抓取的 Notion 頁面、資料庫或資料來源的 ID 或 URL。支援多種格式，包括標準 Notion URL、公開站點 URL、UUID 以及資料來源 URL (`collection://...`)。 |
| `include_transcript` | boolean | 否 | 是否包含頁面內容的純文字轉錄。預設為 `false`。 |
| `include_discussions` | boolean | 否 | 是否在返回內容中包含留言與討論的摘要資訊。預設為 `false`。 |

### 使用範例

#### 範例 1：抓取一個 Notion 頁面

```json
{
  "id": "https://notion.so/workspace/Page-Title-a1b2c3d4e5f67890"
}
```

**預期輸出：**
返回指定頁面的完整內容，格式為 Notion 增強型 Markdown。內容將包含頁面的標題、屬性以及所有區塊。

#### 範例 2：抓取一個資料庫的結構資訊

```json
{
  "id": "https://www.notion.so/workspace/f8c7d9e6a5b4c3b2a1d0e9f8a7b6c5d4"
}
```

**預期輸出：**
返回指定資料庫的結構定義，其中包含一個或多個 `<data-source>` 標籤。每個標籤都詳細定義了一個資料來源的綱要（schema）、屬性（properties）以及可用的模板（templates），並提供了一個 `collection://...` 格式的 URL，用於後續的資料操作。

#### 範例 3：抓取一個頁面及其所有留言

```json
{
  "id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "include_discussions": true
}
```

**預期輸出：**
返回頁面的完整 Markdown 內容，並在內容中包含 `<page-discussions>` 標籤，其中匯總了頁面上的所有留言數量和預覽。同時，內文中對應位置會出現留言標記。

## 3. `notion-create-pages`

### 功能說明

`notion-create-pages` 工具用於在 Notion 中建立一個或多個新的頁面。所有透過單次呼叫建立的頁面都會擁有相同的父級，這個父級可以是一個頁面 (`page_id`) 或一個資料來源 (`data_source_id`)。如果省略父級，新頁面將會被建立為獨立的工作區私人頁面。

**核心要點：**

-   **父級選擇：** 在資料庫中建立頁面時，**強烈建議**先用 `notion-fetch` 工具獲取資料庫的 `data_source_id`，而不是直接使用 `database_id`，特別是當資料庫可能包含多個資料來源時。
-   **內容格式：** 頁面內容必須使用 Notion 增強型 Markdown 格式。頁面標題應在 `properties` 中指定，而非直接寫在內容頂部。
-   **屬性設定：** 頁面屬性是一個 JSON 物件。在資料庫中建立頁面時，屬性名稱必須與 `notion-fetch` 獲取的資料來源綱要 (schema) 完全一致。對於特殊類型的屬性（如日期、選框），需要使用特定的擴充格式。
-   **模板應用：** 可以在建立頁面時應用資料庫模板。只需提供 `template_id`，頁面內容便會被自動填充。此過程是異步的。

### 參數詳解

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `parent` | object | 否 | 一個指定父級的物件，可包含 `page_id` 或 `data_source_id`。 |
| `pages` | array | 是 | 一個包含要建立頁面資訊的陣列。每個陣列元素都是一個頁面物件。 |

#### `pages` 陣列元素結構

| 屬性 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `properties` | object | 否 | 包含頁面屬性的 JSON 物件。屬性名稱與值的格式需嚴格遵守規範。 |
| `content` | string | 否 | 使用 Notion 增強型 Markdown 格式的頁面內容。 |
| `template_id` | string | 否 | 要應用的資料庫模板 ID。使用模板時，不應再提供 `content`。 |

### 使用範例

#### 範例 1：在一個資料庫中，使用模板建立一個新任務頁面

```json
{
  "parent": {
    "data_source_id": "f336d0bc-b841-465b-8045-024475c079dd"
  },
  "pages": [
    {
      "template_id": "a5da15f6-b853-455d-8827-f906fb52db2b",
      "properties": {
        "Task Name": "處理緊急的登入系統 Bug"
      }
    }
  ]
}
```

**預期輸出：**
在指定的資料來源中，應用模板 `a5da15f6-...` 建立一個新頁面，並將其標題屬性「Task Name」設定為「處理緊急的登入系統 Bug」。頁面內容將由模板自動填充。

#### 範例 2：建立一個獨立的、帶有內容的私人頁面

```json
{
  "pages": [
    {
      "properties": {
        "title": "我的個人讀書筆記"
      },
      "content": "# 《原子習慣》\n\n這是一本關於如何建立微小但強大習慣的書。\n\n<details>\n<summary>核心概念</summary>\n提示、渴望、回應、獎賞。\n</details>"
    }
  ]
}
```

**預期輸出：**
在工作區的私人區域建立一個新頁面，標題為「我的個人讀書筆記」，並包含指定的 Markdown 內容，其中還有一個可摺疊的區塊。

#### 範例 3：在資料庫中建立一個包含多個自訂屬性的頁面

```json
{
  "parent": {
    "data_source_id": "f336d0bc-b841-465b-8045-024475c079dd"
  },
  "pages": [
    {
      "properties": {
        "Task Name": "規劃 Q3 產品路線圖",
        "Status": "進行中",
        "Priority": 5,
        "Is Complete": "__NO__",
        "date:Due Date:start": "2024-08-31",
        "date:Due Date:is_datetime": 0
      }
    }
  ]
}
```

**預期輸出：**
在指定的資料來源中建立一個新頁面，並精確設定其標題、狀態、優先級、是否完成以及截止日期等多個屬性。

## 4. `notion-update-page`

### 功能說明

`notion-update-page` 工具用於更新現有 Notion 頁面的屬性或內容。這是一個功能強大且靈活的工具，支援多種更新命令。

**核心要點：**

-   **前置操作：** 在更新任何頁面之前，**強烈建議**先使用 `notion-fetch` 工具獲取頁面的最新內容和屬性綱要，以確保操作的準確性。
-   **屬性更新：** 更新屬性時，只需提供要變更的屬性及其新值。未提及的屬性將保持不變。特殊格式（如日期）需嚴格遵守規範。
-   **內容操作：**
    -   `replace_content`: 完全替換頁面的所有內容。
    -   `replace_content_range`: 替換頁面中被 `selection_with_ellipsis` 選中的特定內容範圍。
    -   `insert_content_after`: 在 `selection_with_ellipsis` 選中的內容之後插入新內容。
-   **安全機制：** 當內容替換操作可能導致子頁面或子資料庫被意外刪除時，操作會失敗並返回錯誤。使用者必須明確設定 `allow_deleting_content: true` 參數以確認刪除。
-   **模板應用：** 可以使用 `apply_template` 命令將一個現有模板的內容附加到頁面中。

### 參數詳解

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `page_id` | string | 是 | 要更新的頁面 ID。 |
| `command` | string | 是 | 要執行的命令，例如 `update_properties`, `replace_content`, `apply_template` 等。 |
| `properties` | object | 否 | 僅在 `command` 為 `update_properties` 時需要。一個包含要更新屬性的 JSON 物件。 |
| `new_str` | string | 否 | 在內容操作命令中需要，代表新的內容字串。 |
| `selection_with_ellipsis` | string | 否 | 在 `replace_content_range` 和 `insert_content_after` 命令中需要，用於唯一標識要操作的內容範圍。格式為 `開頭文字...結尾文字`。 |
| `allow_deleting_content` | boolean | 否 | 是否允許在內容替換時刪除子頁面或子資料庫。預設為 `false`。 |
| `template_id` | string | 否 | 僅在 `command` 為 `apply_template` 時需要，代表要應用的模板 ID。 |

### 使用範例

#### 範例 1：更新一個任務頁面的狀態和優先級

```json
{
  "page_id": "f336d0bc-b841-465b-8045-024475c079dd",
  "command": "update_properties",
  "properties": {
    "Status": "已完成",
    "Priority": 1
  }
}
```

**預期輸出：**
目標頁面的「Status」屬性被更新為「已完成」，「Priority」屬性被更新為 1。

#### 範例 2：替換頁面中的某個章節

```json
{
  "page_id": "f336d0bc-b841-465b-8045-024475c079dd",
  "command": "replace_content_range",
  "selection_with_ellipsis": "# 舊的第二章...本章結束。",
  "new_str": "# 全新的第二章\n\n這是更新後的內容。"
}
```

**預期輸出：**
頁面中從「# 舊的第二章」開始到「本章結束。」為止的全部內容，被替換為 `new_str` 提供的新內容。

#### 範例 3：在頁面特定位置後插入一個待辦事項列表

```json
{
  "page_id": "f336d0bc-b841-465b-8045-024475c079dd",
  "command": "insert_content_after",
  "selection_with_ellipsis": "這是會議的總結部分。",
  "new_str": "\n\n## 後續行動\n\n- [ ] @Joker 整理會議記錄\n- [ ] @AI 完成報告初稿"
}
```

**預期輸出：**
在頁面中「這是會議的總結部分。」這段文字之後，插入一個新的二級標題和兩個待辦事項。

## 5. `notion-move-pages`

### 功能說明

`notion-move-pages` 工具用於將一個或多個 Notion 頁面或資料庫移動到一個新的父級底下。這是一個用於整理和重組工作區內容的實用工具。

**核心要點：**

-   **批量操作：** 一次最多可以移動 100 個頁面或資料庫。
-   **目標父級：** 新的父級可以是一個頁面、一個資料庫、資料庫下的特定資料來源，甚至是整個工作區（這會將頁面變為私人頁面，應謹慎使用）。
-   **限制：** 無法單獨移動資料庫下的資料來源（Data Source）。

### 參數詳解

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `page_or_database_ids` | array | 是 | 一個包含要移動的頁面或資料庫 ID 的陣列。ID 可以是從 `search` 或 `fetch` 工具獲取的 UUID 字串。 |
| `new_parent` | object | 是 | 一個指定新父級的物件。其結構與 `create-pages` 工具的 `parent` 參數類似，可包含 `page_id`、`database_id` 或 `data_source_id`。 |

### 使用範例

#### 範例 1：將兩個頁面移動到一個新的專案頁面下

```json
{
  "page_or_database_ids": [
    "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
    "b2c3d4e5-f6a7-8901-bcde-f12345678901"
  ],
  "new_parent": {
    "page_id": "c3d4e5f6-a7b8-9012-cdef-1234567890ab"
  }
}
```

**預期輸出：**
指定的兩個頁面被成功移動到 ID 為 `c3d4e5f6-...` 的頁面之下，成為其子頁面。

#### 範例 2：將一個頁面歸檔到一個資料庫中

```json
{
  "page_or_database_ids": [
    "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
  ],
  "new_parent": {
    "data_source_id": "f336d0bc-b841-465b-8045-024475c079dd"
  }
}
```

**預期輸出：**
指定的頁面被移動到 ID 為 `f336d0bc-...` 的資料來源中，成為該資料庫的一個條目。該頁面原有的內容會被保留，並可能需要後續調整其屬性以符合資料庫的綱要。

## 6. `notion-duplicate-page`

### 功能說明

`notion-duplicate-page` 工具用於複製一個現有的 Notion 頁面。這對於建立重複性結構的內容（如每週會議記錄、專案模板）非常有用。

**核心要點：**

-   **權限要求：** 必須對要複製的頁面有存取權限。
-   **異步操作：** 頁面複製是一個異步過程。工具會立即返回新頁面的 ID 和 URL，但內容的完全填充可能需要一些時間。需要告知使用者複製正在進行中，可以稍後透過 `fetch` 工具或直接訪問 URL 來查看結果。

### 參數詳解

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `page_id` | string | 是 | 要複製的頁面的 ID。這是一個 v4 UUID，可以從 Notion 頁面 URL 中解析得到。 |

### 使用範例

#### 範例 1：複製一個會議記錄模板頁面

```json
{
  "page_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
}
```

**預期輸出：**
工具會立即返回一個包含新頁面 `id` 和 `url` 的物件。例如：
```json
{
  "id": "e5f6a7b8-c9d0-1234-5678-90abcdef1234",
  "url": "https://www.notion.so/workspace/Copy-of-Meeting-Template-e5f6a7b8c9d01234567890abcdef1234"
}
```
同時，系統會開始在背景複製頁面內容。使用者可以稍後訪問返回的 URL 查看完整的複製頁面。

## 7. `notion-create-database`

### 功能說明

`notion-create-database` 工具允許使用者透過類似 SQL DDL (Data Definition Language) 的語法來建立一個全新的 Notion 資料庫。這為程式化地定義複雜資料結構提供了極大的便利。

**核心要點：**

-   **SQL DDL 語法：** 使用 `CREATE TABLE` 語句來定義資料庫的欄位（屬性）。欄位名稱需要用雙引號 `"` 包裹，而型別選項則使用單引號 `'`。
-   **豐富的屬性類型：** 支援包括 `TITLE`, `RICH_TEXT`, `DATE`, `SELECT`, `MULTI_SELECT`, `NUMBER`, `FORMULA`, `RELATION`, `ROLLUP` 等在內的幾乎所有 Notion 屬性類型。
-   **父級指定：** 可以將新資料庫建立在某個特定頁面下，或作為獨立的私人頁面建立在工作區根目錄。
-   **自動標題：** 如果未提供 `TITLE` 類型的屬性，系統會自動新增一個名為 "Name" 的標題屬性。

### 參數詳解

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `schema` | string | 是 | 定義資料庫綱要的 SQL `CREATE TABLE` 語句。 |
| `parent` | object | 否 | 指定父級頁面的物件，包含 `page_id`。若省略，則建立為工作區級別的私人頁面。 |
| `title` | string | 否 | 新資料庫的標題。 |
| `description` | string | 否 | 新資料庫的描述文字。 |

### 使用範例

#### 範例 1：建立一個簡單的任務資料庫

```json
{
  "title": "我的任務列表",
  "parent": {
    "page_id": "f336d0bc-b841-465b-8045-024475c079dd"
  },
  "schema": "CREATE TABLE (\"Task Name\" TITLE, \"Status\" SELECT('To Do':red, 'In Progress':yellow, 'Done':green), \"Due Date\" DATE)"
}
```

**預期輸出：**
在 ID 為 `f336d0bc-...` 的頁面下，建立一個名為「我的任務列表」的新資料庫。該資料庫包含三個屬性：「Task Name」（標題）、「Status」（帶有三個選項的單選框）和「Due Date」（日期）。工具會返回新資料庫的綱要定義和 `data_source_id`。

#### 範例 2：建立一個帶有多種屬性類型的專案資料庫

```json
{
  "title": "公司專案",
  "schema": "CREATE TABLE (\"專案名稱\" TITLE, \"預算\" NUMBER FORMAT 'dollar', \"標籤\" MULTI_SELECT('工程':blue, '設計':pink), \"專案編號\" UNIQUE_ID PREFIX 'PROJ')"
}
```

**預期輸出：**
在工作區根目錄建立一個名為「公司專案」的私人資料庫。該資料庫包含「專案名稱」、「預算」（美元格式的數字）、「標籤」（多選框）和「專案編號」（帶有 "PROJ" 前綴的唯一 ID）四個屬性。

## 8. `notion-update-data-source`

### 功能說明

`notion-update-data-source` 工具允許使用者透過 SQL DDL 語句來修改現有 Notion 資料來源 (Data Source) 的綱要、標題或描述。這對於在資料庫建立後調整其結構至關重要。

**核心要點：**

-   **目標對象：** 操作的目標是 `data_source_id`，可以從 `fetch` 工具返回的 `<data-source>` 標籤中獲得。對於單一資料來源的資料庫，也可以直接使用資料庫 ID。
-   **SQL DDL 語法：** 支援多種以分號分隔的 DDL 語句：
    -   `ADD COLUMN "名稱" <類型>`: 新增一個屬性。
    -   `DROP COLUMN "名稱"`: 刪除一個屬性。
    -   `RENAME COLUMN "舊名稱" TO "新名稱"`: 重新命名一個屬性。
    -   `ALTER COLUMN "名稱" SET <類型>`: 變更屬性的類型或選項。
-   **其他操作：** 除了修改綱要，還可以更新資料來源的 `title`（標題）、`description`（描述），或將其移入回收站 (`in_trash: true`)。
-   **限制：** 無法刪除或建立標題 (TITLE) 屬性，且一個資料來源最多只能有一個唯一 ID (`UNIQUE_ID`) 屬性。

### 參數詳解

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `data_source_id` | string | 是 | 要更新的資料來源 ID (`collection://...` 中的 UUID) 或單一來源的資料庫 ID。 |
| `statements` | string | 否 | 一串以分號分隔的 SQL DDL 語句，用於修改綱要。 |
| `title` | string | 否 | 資料來源的新標題。 |
| `description` | string | 否 | 資料來源的新描述。 |
| `in_trash` | boolean | 否 | 設定為 `true` 可將資料來源移入回收站。 |

### 使用範例

#### 範例 1：為任務資料庫新增「優先級」和「截止日期」屬性

```json
{
  "data_source_id": "f336d0bc-b841-465b-8045-024475c079dd",
  "statements": "ADD COLUMN \"優先級\" SELECT('高':red, '中':yellow, '低':green); ADD COLUMN \"截止日期\" DATE"
}
```

**預期輸出：**
目標資料來源會增加兩個新的屬性：「優先級」（單選框）和「截止日期」（日期）。工具會返回更新後的資料庫綱要。

#### 範例 2：重新命名一個屬性並刪除另一個

```json
{
  "data_source_id": "f336d0bc-b841-465b-8045-024475c079dd",
  "statements": "RENAME COLUMN \"狀態\" TO \"專案進度\"; DROP COLUMN \"舊的備註欄位\""
}
```

**預期輸出：**
資料來源中的「狀態」屬性被重新命名為「專案進度」，同時「舊的備註欄位」屬性被永久刪除。

#### 範例 3：為資料庫建立一個自我關聯（父/子任務）

此操作通常需要兩步，但 `update-data-source` 讓這一步到位成為可能。假設 `data_source_id` 為 `f336d0bc-...`。

```json
{
  "data_source_id": "f336d0bc-b841-465b-8045-024475c079dd",
  "statements": "ADD COLUMN \"父任務\" RELATION('f336d0bc-b841-465b-8045-024475c079dd', DUAL '子任務' 'children'); ADD COLUMN \"子任務\" RELATION('f336d0bc-b841-465b-8045-024475c079dd', DUAL '父任務' 'parent')"
}
```

**預期輸出：**
資料來源中新增了「父任務」和「子任務」兩個屬性，它們互相形成雙向關聯，從而可以在資料庫內部建立層級關係。

## 9. `notion-create-comment`

### 功能說明

`notion-create-comment` 工具用於在 Notion 頁面或特定內容區塊上新增留言。這對於團隊協作、提供反饋或進行非同步討論非常有用。

**核心要點：**

-   **目標模式：** 必須提供 `page_id` 來指定頁面，然後選擇以下三種目標模式之一：
    1.  **頁面級留言：** 僅提供 `page_id`，留言會附加到整個頁面。
    2.  **內容級留言：** 提供 `page_id` 和 `selection_with_ellipsis`，留言會附加到頁面中被選中的特定內容區塊上。
    3.  **回覆討論：** 提供 `page_id` 和 `discussion_id`，以回覆一個現有的討論串。
-   **內容格式：** 留言內容 `rich_text` 是一個複雜的陣列物件，用於表示格式化的文字。

### 參數詳解

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `page_id` | string | 是 | 要留言的頁面 ID。 |
| `rich_text` | array | 是 | 一個代表留言內容的富文本物件陣列。 |
| `discussion_id` | string | 否 | 要回覆的現有討論串 ID。 |
| `selection_with_ellipsis` | string | 否 | 用於選中特定內容區塊的字串，格式為 `開頭文字...結尾文字`。 |

### 使用範例

#### 範例 1：在整個頁面發表一條通用評論

```json
{
  "page_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "rich_text": [
    {
      "text": {
        "content": "這份文件寫得很棒！"
      }
    }
  ]
}
```

**預期輸出：**
在指定頁面的頂部評論區，新增一條內容為「這份文件寫得很棒！」的留言。

#### 範例 2：針對頁面中的某個標題進行評論

```json
{
  "page_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "selection_with_ellipsis": "# 第三章：市場分析...的數據來源？",
  "rich_text": [
    {
      "text": {
        "content": "這裡的數據來源是哪裡？可以補充一下嗎？"
      }
    }
  ]
}
```

**預期輸出：**
在頁面中，「# 第三章：市場分析...的數據來源？」這段文字會被高亮，並附加一條內容為「這裡的數據來源是哪裡？可以補充一下嗎？」的留言。

#### 範例 3：回覆一條現有的留言

```json
{
  "page_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "discussion_id": "discussion://a1b2c3d4.../blockId.../discussionId...",
  "rich_text": [
    {
      "text": {
        "content": "同意你的看法！"
      }
    }
  ]
}
```

**預期輸出：**
在指定的討論串中，新增一條內容為「同意你的看法！」的回覆。

## 10. `notion-get-comments`

### 功能說明

`notion-get-comments` 工具用於從一個 Notion 頁面中獲取留言和討論串。預設情況下，它只會返回頁面級別的討論。

**核心要點：**

-   **搭配 `fetch` 使用：** 建議先使用 `fetch` 工具並設定 `include_discussions: true`，這樣可以在頁面內容中看到討論的錨點位置 (`discussion://...`)。然後再使用 `get-comments` 來獲取完整的討論串內容。
-   **過濾選項：**
    -   `include_all_blocks`: 設為 `true` 可以包含所有子區塊上的討論。
    -   `include_resolved`: 設為 `true` 可以包含已經被標記為「已解決」的討論。
-   **指定討論：** 可以提供 `discussion_id` 來直接獲取某個特定的討論串。

### 參數詳解

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `page_id` | string | 是 | 要從中獲取留言的頁面 ID。 |
| `include_all_blocks` | boolean | 否 | 是否包含所有子區塊上的討論。預設為 `false`。 |
| `include_resolved` | boolean | 否 | 是否包含已解決的討論。預設為 `false`。 |
| `discussion_id` | string | 否 | 要獲取的特定討論串的 ID 或 URL。 |

### 使用範例

#### 範例 1：獲取頁面級別的所有未解決留言

```json
{
  "page_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890"
}
```

**預期輸出：**
以 XML 格式返回指定頁面頂層的所有未解決的討論串及其完整留言內容。

#### 範例 2：獲取頁面及其所有子區塊的全部留言（包括已解決的）

```json
{
  "page_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "include_all_blocks": true,
  "include_resolved": true
}
```

**預期輸出：**
以 XML 格式返回該頁面從上到下所有（包括已解決的）的討論串。

#### 範例 3：獲取一個特定的討論串

```json
{
  "page_id": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "discussion_id": "discussion://a1b2c3d4.../blockId.../discussionId..."
}
```

**預期輸出：**
以 XML 格式僅返回與所提供 `discussion_id` 相符的那個討論串的完整內容。

## 11. `notion-get-teams`

### 功能說明

`notion-get-teams` 工具用於檢索當前工作區中的團隊（Teamspaces）列表。它會顯示團隊的名稱、ID、以及當前使用者的成員身份和角色。

**核心要點：**

-   **分組返回：** 結果會根據使用者的成員身份（是否為成員）進行分組。
-   **結果限制：** 每種成員身份狀態最多返回 10 個結果。
-   **名稱過濾：** 可以提供一個查詢字串來按名稱（不區分大小寫）篩選團隊。

### 參數詳解

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `query` | string | 否 | 用於按名稱篩選團隊的查詢字串。 |

### 使用範例

#### 範例 1：列出我所在的所有團隊

```json
{}
```

**預期輸出：**
返回一個列表，其中包含使用者已加入的團隊和未加入的團隊，每組最多顯示 10 個，並包含各團隊的 ID 和名稱。

#### 範例 2：搜尋名為「工程」的團隊

```json
{
  "query": "工程"
}
```

**預期輸出：**
返回所有名稱中包含「工程」字樣的團隊列表。

## 12. `notion-get-users`

### 功能說明

`notion-get-users` 工具用於檢索當前工作區中的使用者列表，包括成員和訪客。它會返回使用者的 ID、姓名、Email（如果可用）以及類型（真人或機器人）。

**核心要點：**

-   **分頁支援：** 支援基於游標 (`cursor`) 的分頁，可以遍歷工作區中的所有使用者。
-   **過濾與搜尋：** 可以按姓名或 Email 進行篩選，也可以直接透過 `user_id` 獲取特定使用者，或使用 `self` 關鍵字獲取當前使用者。
-   **頁面大小：** 可以自訂每頁返回的使用者數量，最大為 100。

### 參數詳解

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `query` | string | 否 | 用於按姓名或 Email 篩選使用者的查詢字串。 |
| `user_id` | string | 否 | 提供特定使用者的 ID 以僅返回該使用者。傳入 `self` 可獲取當前使用者資訊。 |
| `page_size` | integer | 否 | 每頁返回的使用者數量，預設為 100。 |
| `start_cursor` | string | 否 | 用於分頁的游標，從上一次回應的 `next_cursor` 值中獲取。 |

### 使用範例

#### 範例 1：列出工作區中的第一頁使用者

```json
{}
```

**預期輸出：**
返回工作區中前 100 個使用者的列表，包含他們的 ID、姓名、Email 等資訊。如果還有更多使用者，結果中會包含一個 `next_cursor` 值用於獲取下一頁。

#### 範例 2：搜尋姓名或 Email 中包含 "joker" 的使用者

```json
{
  "query": "joker"
}
```

**預期輸出：**
返回所有姓名或 Email 中包含 "joker" (不區分大小寫) 的使用者列表。

#### 範例 3：獲取我自己的使用者資訊

```json
{
  "user_id": "self"
}
```

**預期輸出：**
返回當前進行操作的使用者自己的詳細資訊。
