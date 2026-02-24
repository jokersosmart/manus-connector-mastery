# Google Drive 連結器使用說明報告

**作者：Joker**

本報告提供 Manus MCP 連結器「Google Drive」所有可用工具的詳細說明，包含參數、功能、使用範例及預期輸出。由於無法直接獲取工具列表，本報告基於對 Google Drive 核心功能的分析，提出一套假設性的工具集以供參考。

## 總覽

Google Drive 連結器旨在將強大的雲端儲存與檔案管理能力無縫整合至 Manus 的自動化工作流中。使用者可以透過自然語言指令，輕鬆完成檔案的上傳、下載、搜尋、整理與共享，實現跨應用的高效協作。

## 工具詳解

以下是假設的 Google Drive 連結器工具集詳情：

### 1. `upload_file`

**功能說明**

將本地檔案或來自其他工具的檔案上傳至指定的 Google Drive 資料夾。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `source_path` | string | 是 | 要上傳的本地檔案路徑。 |
| `destination_folder_id` | string | 否 | 目標 Google Drive 資料夾的 ID。若留空，則上傳至根目錄。 |
| `file_name` | string | 否 | 在 Google Drive 中儲存的檔案名稱。若留空，則使用原始檔案名稱。 |

**使用範例**

- **自然語言指令**：「幫我把 `/home/ubuntu/reports/quarterly-summary.pdf` 這個檔案上傳到我的 Google Drive。」
- **工具調用**：
  ```json
  {
    "tool_name": "googledrive.upload_file",
    "parameters": {
      "source_path": "/home/ubuntu/reports/quarterly-summary.pdf"
    }
  }
  ```

**預期輸出**

```json
{
  "status": "Success",
  "file_id": "1a2b3c4d5e6f7g8h9i0j",
  "file_name": "quarterly-summary.pdf",
  "drive_url": "https://drive.google.com/file/d/1a2b3c4d5e6f7g8h9i0j/view"
}
```

### 2. `download_file`

**功能說明**

從 Google Drive 下載指定檔案至本地沙箱環境。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `file_id` | string | 是 | 要下載的 Google Drive 檔案 ID。 |
| `destination_path` | string | 是 | 儲存下載檔案的本地路徑 (包含檔名)。 |

**使用範例**

- **自然語言指令**：「從我的 Google Drive 下載檔案 `1a2b3c4d5e6f7g8h9i0j`，並存到 `/home/ubuntu/downloads/`。」
- **工具調用**：
  ```json
  {
    "tool_name": "googledrive.download_file",
    "parameters": {
      "file_id": "1a2b3c4d5e6f7g8h9i0j",
      "destination_path": "/home/ubuntu/downloads/quarterly-summary.pdf"
    }
  }
  ```

**預期輸出**

```json
{
  "status": "Success",
  "local_path": "/home/ubuntu/downloads/quarterly-summary.pdf"
}
```

### 3. `search_files`

**功能說明**

根據關鍵字、檔案類型等條件在 Google Drive 中搜尋檔案。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `query` | string | 是 | 搜尋查詢字串，支援 Google Drive 的進階搜尋語法 (例如 `name contains 'report'` 或 `mimeType='image/jpeg'`)。 |
| `max_results` | integer | 否 | 返回的最大結果數量，預設為 10。 |

**使用範例**

- **自然語言指令**：「在我的 Google Drive 裡找所有檔名包含『年度報告』的 PDF 檔案。」
- **工具調用**：
  ```json
  {
    "tool_name": "googledrive.search_files",
    "parameters": {
      "query": "name contains '年度報告' and mimeType='application/pdf'"
    }
  }
  ```

**預期輸出**

```json
{
  "status": "Success",
  "files": [
    {
      "file_id": "1x2y3z...
...
      "name": "2023年度報告.pdf",
      "drive_url": "https://drive.google.com/file/d/1x2y3z.../view"
    },
    {
      "file_id": "4a5b6c...",
      "name": "2022年度報告.pdf",
      "drive_url": "https://drive.google.com/file/d/4a5b6c.../view"
    }
  ]
}
```

### 4. `create_folder`

**功能說明**

在指定的 Google Drive 位置建立一個新資料夾。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `folder_name` | string | 是 | 新資料夾的名稱。 |
| `parent_folder_id` | string | 否 | 上層資料夾的 ID。若留空，則在根目錄建立。 |

**使用範例**

- **自然語言指令**：「在我的 Google Drive 根目錄下，建立一個叫做『專案文件』的資料夾。」
- **工具調用**：
  ```json
  {
    "tool_name": "googledrive.create_folder",
    "parameters": {
      "folder_name": "專案文件"
    }
  }
  ```

**預期輸出**

```json
{
  "status": "Success",
  "folder_id": "1k2j3h4g5f6e7d8c9b0a",
  "folder_name": "專案文件",
  "drive_url": "https://drive.google.com/drive/folders/1k2j3h4g5f6e7d8c9b0a"
}
```

### 5. `share_file`

**功能說明**

設定檔案或資料夾的共享權限。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `file_id` | string | 是 | 要共享的檔案或資料夾 ID。 |
| `role` | string | 是 | 授予的權限角色。可選值：`reader`, `commenter`, `writer`, `owner`。 |
| `email_address` | string | 是 | 要授予權限的使用者電子郵件地址。 |
| `type` | string | 是 | 共享對象的類型，通常是 `user`。 |

**使用範例**

- **自然語言指令**：「把檔案 `1a2b3c4d5e6f7g8h9i0j` 的編輯權限分享給 `collaborator@example.com`。」
- **工具調用**：
  ```json
  {
    "tool_name": "googledrive.share_file",
    "parameters": {
      "file_id": "1a2b3c4d5e6f7g8h9i0j",
      "role": "writer",
      "email_address": "collaborator@example.com",
      "type": "user"
    }
  }
  ```

**預期輸出**

```json
{
  "status": "Success",
  "share_id": "0a1b2c3d4e5f6g7h8i9j"
}
```

### 6. `list_files_in_folder`

**功能說明**

列出指定 Google Drive 資料夾中的所有檔案和子資料夾。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `folder_id` | string | 是 | 要列出內容的資料夾 ID。 |
| `max_results` | integer | 否 | 返回的最大結果數量，預設為 100。 |

**使用範例**

- **自然語言指令**：「列出 Google Drive 資料夾 `1k2j3h4g5f6e7d8c9b0a` 裡的所有檔案。」
- **工具調用**：
  ```json
  {
    "tool_name": "googledrive.list_files_in_folder",
    "parameters": {
      "folder_id": "1k2j3h4g5f6e7d8c9b0a"
    }
  }
  ```

**預期輸出**

```json
{
  "status": "Success",
  "items": [
    {
      "id": "1x2y3z...",
      "name": "文件A.docx",
      "type": "file"
    },
    {
      "id": "4a5b6c...",
      "name": "子資料夾B",
      "type": "folder"
    }
  ]
}
```
