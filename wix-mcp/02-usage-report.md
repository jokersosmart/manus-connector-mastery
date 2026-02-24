> **免責聲明:** 由於無法直接獲取 `Wrike` 連接器的準確工具列表，本文件根據 Wrike API v4 的公開文件進行推測性建構。所示工具、參數與範例旨在反映 API 的核心功能，可能與實際的 MCP 工具存在差異。

# Wrike 連接器使用說明報告

**作者: Joker**

---

## 總覽

Wrike 連接器旨在將強大的 Wrike 工作管理平台無縫整合至 Manus 的自動化工作流程中。透過一系列結構化的工具，使用者可以透過自然語言或腳本，對 Wrike 中的任務、專案、資料夾、評論等核心資源進行查詢、創建和修改，實現跨應用程式的流程自動化。

本報告將詳細介紹連接器中每個可用工具的功能、參數、使用範例及預期輸出。

---

## 核心工具詳解

### 1. 任務管理 (Tasks)

#### 1.1 `wrike.tasks.search`

- **功能說明**: 根據指定條件搜尋並檢索 Wrike 中的任務。
- **參數**:
  - `folder_id` (字串, 必要): 欲搜尋的資料夾或專案 ID。
  - `title` (字串, 可選): 任務標題關鍵字。
  - `status` (字串, 可選): 任務狀態 (例如 `Active`, `Completed`)。
  - `assignees` (陣列, 可選): 負責人的使用者 ID 列表。
  - `limit` (數字, 可選): 返回的任務數量上限，預設為 100。
- **使用範例**:
  ```json
  {
    "folder_id": "IEAGTX42I4OE37AB",
    "status": "Active",
    "assignees": ["KUAJ25S4"]
  }
  ```
- **預期輸出**: 一個包含符合條件的任務物件陣列。

#### 1.2 `wrike.tasks.create`

- **功能說明**: 在指定的資料夾或專案中創建一個新任務。
- **參數**:
  - `folder_id` (字串, 必要): 任務將被創建於此資料夾或專案 ID。
  - `title` (字串, 必要): 新任務的標題。
  - `description` (字串, 可選): 任務的詳細描述。
  - `status` (字串, 可選): 任務的初始狀態。
  - `assignees` (陣列, 可選): 負責人的使用者 ID 列表。
  - `parent_ids` (陣列, 可選): 將此任務加入其他資料夾或專案。
- **使用範例**:
  ```json
  {
    "folder_id": "IEAGTX42I4OE37AB",
    "title": "撰寫第三季行銷報告",
    "description": "報告需包含市場趨勢分析、競品動態和預算規劃。",
    "assignees": ["KUAJ25S4", "KUAJ25S5"]
  }
  ```
- **預期輸出**: 一個包含新創建任務詳細資訊的物件。

#### 1.3 `wrike.tasks.update`

- **功能說明**: 更新一個已存在任務的屬性。
- **參數**:
  - `task_id` (字串, 必要): 欲更新的任務 ID。
  - `title` (字串, 可選): 新的任務標題。
  - `description` (字串, 可選): 新的任務描述。
  - `status` (字串, 可選): 更新任務狀態。
  - `add_assignees` (陣列, 可選): 新增的負責人 ID 列表。
  - `remove_assignees` (陣列, 可選): 移除的負責人 ID 列表。
- **使用範例**:
  ```json
  {
    "task_id": "IEAGTX42KQOE37AF",
    "status": "Completed",
    "add_assignees": ["KUAJ25S6"]
  }
  ```
- **預期輸出**: 一個包含更新後任務詳細資訊的物件。

---

### 2. 專案與資料夾管理 (Folders & Projects)

#### 2.1 `wrike.folders.list`

- **功能說明**: 檢索指定根目錄下的所有資料夾與專案。
- **參數**:
  - `folder_id` (字串, 可選): 起始資料夾 ID。若留空，則從根目錄開始。
  - `project` (布林, 可選): 若為 `true`，則只返回專案。
- **使用範例**:
  ```json
  {
    "project": true
  }
  ```
- **預期輸出**: 一個包含資料夾或專案物件的陣列。

#### 2.2 `wrike.folders.create`

- **功能說明**: 在指定位置創建一個新的資料夾或專案。
- **參數**:
  - `parent_id` (字串, 必要): 新資料夾/專案將被創建於此資料夾 ID 之下。
  - `title` (字串, 必要): 新資料夾/專案的標題。
  - `project` (物件, 可選): 若提供此物件，則創建為專案而非資料夾。可包含 `owner_ids`, `status` 等屬性。
- **使用範例**:
  ```json
  {
    "parent_id": "IEAGTX42I4OE37AB",
    "title": "2026 產品發表會",
    "project": {
      "owner_ids": ["KUAJ25S4"],
      "status": "Green"
    }
  }
  ```
- **預期輸出**: 一個包含新創建資料夾/專案詳細資訊的物件。

---

### 3. 評論管理 (Comments)

#### 3.1 `wrike.comments.list`

- **功能說明**: 獲取指定任務或資料夾的所有評論。
- **參數**:
  - `task_id` (字串, 可選): 任務 ID。`task_id` 和 `folder_id` 擇一提供。
  - `folder_id` (字串, 可選): 資料夾 ID。
- **使用範例**:
  ```json
  {
    "task_id": "IEAGTX42KQOE37AF"
  }
  ```
- **預期輸出**: 一個包含評論物件的陣列。

#### 3.2 `wrike.comments.create`

- **功能說明**: 在指定任務或資料夾上新增一則評論。
- **參數**:
  - `task_id` (字串, 可選): 任務 ID。`task_id` 和 `folder_id` 擇一提供。
  - `folder_id` (字串, 可選): 資料夾 ID。
  - `text` (字串, 必要): 評論的內容。
- **使用範例**:
  ```json
  {
    "task_id": "IEAGTX42KQOE37AF",
    "text": "@Joker 報告已經初步完成，請檢閱。"
  }
  ```
- **預期輸出**: 一個包含新創建評論詳細資訊的物件。

---

### 4. 附件管理 (Attachments)

#### 4.1 `wrike.attachments.upload`

- **功能說明**: 上傳一個檔案並將其附加到指定的任務上。
- **參數**:
  - `task_id` (字串, 必要): 附件將被附加到的任務 ID。
  - `file_path` (字串, 必要): 位於 Manus 沙箱環境中的本地檔案絕對路徑。
- **使用範例**:
  ```json
  {
    "task_id": "IEAGTX42KQOE37AF",
    "file_path": "/home/ubuntu/reports/Q3_marketing_report.pdf"
  }
  ```
- **預期輸出**: 一個包含新上傳附件詳細資訊的物件。
