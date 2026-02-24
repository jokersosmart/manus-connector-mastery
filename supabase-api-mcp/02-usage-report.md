_Author: Joker_

# 「Serena」連結器使用說明報告

本報告提供「Serena」連結器所有可用工具的詳細使用說明，旨在幫助開發者充分利用其強大的程式碼分析與編輯能力。每個工具的說明都包含其功能描述、詳細參數、使用範例及預期輸出，以利於快速上手與整合應用。

## 核心概念

在深入了解各個工具之前，請務必理解 Serena 的幾個核心概念：

- **專案 (Project):** Serena 的所有操作都圍繞「專案」進行。在使用前，必須先透過 `activate_project` 啟用一個專案。專案對應到您本地檔案系統中的一個程式碼庫。
- **符號 (Symbol):** 在程式碼的語義中，「符號」是指代變數、函式、類別、介面等的名稱。Serena 的許多強大功能，如 `find_symbol`、`rename_symbol` 等，都是基於對程式碼符號的精準識別與操作，而非簡單的文字匹配。
- **語言伺服器 (Language Server):** Serena 在後端利用語言伺服器協定 (LSP) 來理解程式碼的結構與語義。這使其能夠提供如 IDE般精準的程式碼導航、重構與分析能力。`restart_language_server` 工具可以在必要時重啟此服務。
- **記憶體 (Memory):** Serena 具備一個專案級的記憶體儲存，允許您透過 `write_memory` 和 `read_memory` 等工具，將臨時性的筆記、程式碼片段或思考過程儲存起來，以供後續的任務參考。

接下來，我們將按照功能類別，逐一介紹 Serena 的所有工具。

## 1. 專案與環境管理 (Project & Environment Management)

這類工具專注於設定和管理您的工作環境，確保 Serena 在正確的程式碼庫中運作。

### `activate_project`

- **功能說明:** 啟用一個專案，讓後續的指令都在該專案的上下文中執行。這是開始使用 Serena 的第一步。
- **參數說明:**
    - `project_name` (字串): 您為專案設定的易記名稱。
    - `project_path` (字串): 專案在您本地檔案系統的絕對路徑。
- **使用範例:**
  ```json
  {
    "tool": "activate_project",
    "project_name": "my-web-app",
    "project_path": "/home/user/dev/my-web-app"
  }
  ```
- **預期輸出:** 成功啟用專案的確認訊息。

### `check_onboarding_performed`

- **功能說明:** 檢查目前專案是否已經執行過 `onboarding` 程序。`onboarding` 會分析專案結構並識別關鍵任務 (如測試、建置)。
- **參數說明:** 無。
- **使用範例:**
  ```json
  {
    "tool": "check_onboarding_performed"
  }
  ```
- **預期輸出:** 一個布林值，`true` 表示已執行，`false` 表示尚未執行。

### `get_current_config`

- **功能說明:** 顯示當前 agent 的完整設定，包括已啟用的專案、可用的工具、上下文和模式。
- **參數說明:** 無。
- **使用範例:**
  ```json
  {
    "tool": "get_current_config"
  }
  ```
- **預期輸出:** 一個 JSON 物件，詳細列出目前的所有設定資訊。

### `onboarding`

- **功能說明:** 對目前專案進行初次設定與分析，識別專案的語言、框架、建置指令、測試指令等關鍵資訊。
- **參數說明:** 無。
- **使用範例:**
  ```json
  {
    "tool": "onboarding"
  }
  ```
- **預期輸出:** 分析過程的日誌，以及成功完成的訊息。

### `remove_project`

- **功能說明:** 從 Serena 的設定中移除一個專案。
- **參數說明:**
    - `project_name` (字串): 要移除的專案名稱。
- **使用範例:**
  ```json
  {
    "tool": "remove_project",
    "project_name": "my-web-app"
  }
  ```
- **預期輸出:** 成功移除專案的確認訊息。

### `restart_language_server`

- **功能說明:** 重新啟動後端的語言伺服器。當您在 Serena 外部對程式碼進行了修改，導致 Serena 的分析結果不準確時，可以使用此工具來強制同步。
- **參數說明:** 無。
- **使用範例:**
  ```json
  {
    "tool": "restart_language_server"
  }
  ```
- **預期輸出:** 成功重啟語言伺服器的確認訊息。

## 2. 檔案系統操作 (File System Operations)

這類工具提供了在專案目錄內讀取、寫入和搜尋檔案的能力。

### `create_text_file`

- **功能說明:** 在專案目錄中建立或覆寫一個文字檔案。
- **參數說明:**
    - `path` (字串): 相對於專案根目錄的檔案路徑。
    - `content` (字串): 要寫入檔案的內容。
- **使用範例:**
  ```json
  {
    "tool": "create_text_file",
    "path": "src/components/Button.js",
    "content": "export default function Button() { return <button>Click me</button>; }"
  }
  ```
- **預期輸出:** 成功建立檔案的確認訊息。

### `delete_lines`

- **功能說明:** 刪除檔案中指定範圍的行。
- **參數說明:**
    - `path` (字串): 目標檔案的路徑。
    - `start_line` (整數): 刪除範圍的起始行號 (包含)。
    - `end_line` (整數): 刪除範圍的結束行號 (包含)。
- **使用範例:**
  ```json
  {
    "tool": "delete_lines",
    "path": "src/App.js",
    "start_line": 10,
    "end_line": 15
  }
  ```
- **預期輸出:** 成功刪除行的確認訊息。

### `find_file`

- **功能說明:** 在指定的相對路徑中尋找檔案。
- **參數說明:**
    - `paths` (字串陣列): 要搜尋的檔案路徑列表。
- **使用範例:**
  ```json
  {
    "tool": "find_file",
    "paths": ["src/utils/api.js", "README.md"]
  }
  ```
- **預期輸出:** 一個包含找到的檔案路徑的列表。

### `insert_at_line`

- **功能說明:** 在檔案的指定行號插入內容。
- **參數說明:**
    - `path` (字串): 目標檔案的路徑。
    - `line` (整數): 要插入內容的行號。
    - `content` (字串): 要插入的內容。
- **使用範例:**
  ```json
  {
    "tool": "insert_at_line",
    "path": "src/index.js",
    "line": 1,
    "content": "import React from 'react';"
  }
  ```
- **預期輸出:** 成功插入內容的確認訊息。

### `list_dir`

- **功能說明:** 列出指定目錄中的檔案和子目錄。
- **參數說明:**
    - `path` (字串): 要列出內容的目錄路徑。
    - `recursive` (布林值, 可選): 是否遞迴列出所有子目錄的內容。
- **使用範例:**
  ```json
  {
    "tool": "list_dir",
    "path": "src/components",
    "recursive": true
  }
  ```
- **預期輸出:** 一個包含檔案和目錄名稱的列表。

### `read_file`

- **功能說明:** 讀取專案目錄中一個檔案的完整內容。
- **參數說明:**
    - `path` (字串): 要讀取的檔案路徑。
- **使用範例:**
  ```json
  {
    "tool": "read_file",
    "path": "package.json"
  }
  ```
- **預期輸出:** 包含檔案內容的字串。

### `replace_content`

- **功能說明:** 取代檔案中的內容，可選擇性地使用正規表示式。
- **參數說明:**
    - `path` (字串): 目標檔案的路徑。
    - `find` (字串): 要尋找的內容或正規表示式。
    - `replace` (字串): 用於取代的內容。
    - `is_regex` (布林值, 可選): `find` 參數是否為正規表示式，預設為 `false`。
- **使用範例:**
  ```json
  {
    "tool": "replace_content",
    "path": "src/config.js",
    "find": "API_ENDPOINT = '.*'",
    "replace": "API_ENDPOINT = 'https://api.production.com'",
    "is_regex": true
  }
  ```
- **預期輸出:** 成功取代內容的確認訊息。

### `replace_lines`

- **功能說明:** 將檔案中指定範圍的行取代為新的內容。
- **參數說明:**
    - `path` (字串): 目標檔案的路徑。
    - `start_line` (整數): 取代範圍的起始行號 (包含)。
    - `end_line` (整數): 取代範圍的結束行號 (包含)。
    - `content` (字串): 新的內容。
- **使用範例:**
  ```json
  {
    "tool": "replace_lines",
    "path": "Dockerfile",
    "start_line": 1,
    "end_line": 1,
    "content": "FROM node:18-alpine"
  }
  ```
- **預期輸出:** 成功取代行的確認訊息。

## 3. 符號級程式碼智能 (Symbol-based Code Intelligence)

這是 Serena 最強大的功能所在，它允許您像在 IDE 中一樣，對程式碼的「語義」進行操作，而不僅僅是操作純文字。

### `find_symbol`

- **功能說明:** 在整個專案或指定檔案中，透過語義搜尋來尋找一個符號 (函式、類別、變數等) 的定義。
- **參數說明:**
    - `symbol` (字串): 您要尋找的符號名稱。
    - `file_path` (字串, 可選): 將搜尋範圍限制在單一檔案內。
- **使用範例:**
  ```json
  {
    "tool": "find_symbol",
    "symbol": "getUserProfile"
  }
  ```
- **預期輸出:** 一個包含符號定義位置 (檔案路徑和行號) 的列表。

### `find_referencing_symbols`

- **功能說明:** 找出整個專案中有哪些地方參考 (呼叫或使用) 了指定的符號。這是進行影響分析或重構時的關鍵工具。
- **參數說明:**
    - `symbol` (字串): 您要查詢的符號。
    - `file_path` (字串): 該符號所在的檔案路徑。
    - `line` (整數): 該符號在檔案中的行號。
    - `column` (整數): 該符號在檔案中的列號。
- **使用範例:**
  ```json
  {
    "tool": "find_referencing_symbols",
    "symbol": "API_KEY",
    "file_path": "src/config.js",
    "line": 5,
    "column": 7
  }
  ```
- **預期輸出:** 一個列表，包含所有參考了該符號的位置。

### `get_symbols_overview`

- **功能說明:** 取得一個檔案中所有頂層符號 (top-level symbols) 的概覽，快速了解檔案的結構。
- **參數說明:**
    - `path` (字串): 目標檔案的路徑。
- **使用範例:**
  ```json
  {
    "tool": "get_symbols_overview",
    "path": "src/server.js"
  }
  ```
- **預期輸出:** 一個列表，包含檔案中所有頂層符號的名稱、類型和位置。

### `insert_after_symbol`

- **功能說明:** 在指定符號的定義結束之後，安全地插入新的程式碼片段。
- **參數說明:**
    - `symbol` (字串): 目標符號。
    - `file_path` (字串): 符號所在的檔案路徑。
    - `content` (字串): 要插入的程式碼。
- **使用範例:**
  ```json
  {
    "tool": "insert_after_symbol",
    "symbol": "connectDatabase",
    "file_path": "src/db.js",
    "content": "
function logConnection() {
  console.log('Database connection successful.');
}"
  }
  ```
- **預期輸出:** 成功插入程式碼的確認訊息。

### `insert_before_symbol`

- **功能說明:** 在指定符號的定義開始之前，安全地插入新的程式碼片段。
- **參數說明:**
    - `symbol` (字串): 目標符號。
    - `file_path` (字串): 符號所在的檔案路徑。
    - `content` (字串): 要插入的程式碼。
- **使用範例:**
  ```json
  {
    "tool": "insert_before_symbol",
    "symbol": "App",
    "file_path": "src/App.js",
    "content": "// Main application component"
  }
  ```
- **預期輸出:** 成功插入程式碼的確認訊息。

### `rename_symbol`

- **功能說明:** 在整個程式碼庫中，安全地重命名一個符號及其所有參考。這是一個強大的重構工具，能避免手動尋找和取代時可能發生的錯誤。
- **參數說明:**
    - `file_path` (字串): 符號所在的檔案路徑。
    - `line` (整數): 符號在檔案中的行號。
    - `column` (整數): 符號在檔案中的列號。
    - `new_name` (字串): 新的符號名稱。
- **使用範例:**
  ```json
  {
    "tool": "rename_symbol",
    "file_path": "src/utils.js",
    "line": 10,
    "column": 9,
    "new_name": "fetchUserData"
  }
  ```
- **預期輸出:** 成功重構的確認訊息，並列出所有被修改的檔案。

### `replace_symbol_body`

- **功能說明:** 完全取代一個符號 (例如一個函式) 的主體內容，同時保留其簽名 (signature)。
- **參數說明:**
    - `symbol` (字串): 目標符號。
    - `file_path` (字串): 符號所在的檔案路徑。
    - `content` (字串): 新的符號主體內容。
- **使用範例:**
  ```json
  {
    "tool": "replace_symbol_body",
    "symbol": "calculatePrice",
    "file_path": "src/billing.js",
    "content": "  const tax = amount * 0.2;\n  return amount + tax;"
  }
  ```
- **預期輸出:** 成功取代符號主體的確認訊息。

### JetBrains IDE 整合工具

Serena 也為 JetBrains 系列 IDE (如 IntelliJ IDEA, PyCharm, WebStorm) 的使用者提供了專屬的整合工具。這些工具的功能與上述的標準工具類似，但它們利用 JetBrains IDE 的後端來提供更精準的分析結果。如果您正在使用 Serena 的 JetBrains 插件，建議優先使用這些工具。

- `jet_brains_find_referencing_symbols`
- `jet_brains_find_symbol`
- `jet_brains_get_symbols_overview`
- `jet_brains_type_hierarchy`: 取得一個符號的類型層級結構 (父類型和子類型)。

## 4. 記憶體與狀態管理 (Memory & State Management)

這類工具幫助 Serena 在複雜的任務中記住上下文、中間步驟和關鍵資訊，使其能夠執行需要多步驟推理的長期任務。

### `delete_memory`

- **功能說明:** 從 Serena 的專案級記憶體儲存中刪除一條記憶。
- **參數說明:**
    - `name` (字串): 要刪除的記憶的名稱。
- **使用範例:**
  ```json
  {
    "tool": "delete_memory",
    "name": "refactor_plan"
  }
  ```
- **預期輸出:** 成功刪除記憶的確認訊息。

### `edit_memory`

- **功能說明:** 編輯一條已存在的記憶。
- **參數說明:**
    - `name` (字串): 要編輯的記憶的名稱。
    - `content` (字串): 新的記憶內容。
- **使用範例:**
  ```json
  {
    "tool": "edit_memory",
    "name": "api_endpoints",
    "content": "- GET /users\n- POST /users\n- GET /users/:id"
  }
  ```
- **預期輸出:** 成功編輯記憶的確認訊息。

### `list_memories`

- **功能說明:** 列出目前專案中儲存的所有記憶。
- **參數說明:** 無。
- **使用範例:**
  ```json
  {
    "tool": "list_memories"
  }
  ```
- **預期輸出:** 一個包含所有記憶名稱的列表。

### `read_memory`

- **功能說明:** 讀取一條指定名稱的記憶內容。
- **參數說明:**
    - `name` (字串): 要讀取的記憶的名稱。
- **使用範例:**
  ```json
  {
    "tool": "read_memory",
    "name": "database_schema"
  }
  ```
- **預期輸出:** 包含記憶內容的字串。

### `write_memory`

- **功能說明:** 將一段內容以指定的名稱寫入 Serena 的專案級記憶體，以供未來參考。
- **參數說明:**
    - `name` (字串): 記憶的名稱。
    - `content` (字串): 要儲存的內容。
- **使用範例:**
  ```json
  {
    "tool": "write_memory",
    "name": "refactor_plan",
    "content": "1. Rename service to controller. 2. Extract validation logic. 3. Add unit tests."
  }
  ```
- **預期輸出:** 成功寫入記憶的確認訊息。

## 5. 輔助與思考工具 (Utility & Thinking Tools)

這類工具為 Serena 提供了與外部環境互動、執行通用任務以及進行自我反思的能力，使其行為更像一個真正的開發者。

### `execute_shell_command`

- **功能說明:** 在專案的根目錄下執行一個 shell 指令。這是一個非常強大的工具，可以用來執行測試、安裝依賴、運行腳本等。
- **參數說明:**
    - `command` (字串): 要執行的 shell 指令。
- **使用範例:**
  ```json
  {
    "tool": "execute_shell_command",
    "command": "npm install lodash"
  }
  ```
- **預期輸出:** 指令執行的標準輸出 (stdout) 和標準錯誤 (stderr)。

### `open_dashboard`

- **功能說明:** 在預設的網頁瀏覽器中打開 Serena 的 Web 儀表板，您可以在儀表板上查看日誌、會話資訊和工具使用統計。
- **參數說明:** 無。
- **使用範例:**
  ```json
  {
    "tool": "open_dashboard"
  }
  ```
- **預期輸出:** 成功打開儀表板的確認訊息。

### `search_for_pattern`

- **功能說明:** 在專案中執行類似 `grep` 的模式搜索。
- **參數說明:**
    - `pattern` (字串): 要搜索的文字或正規表示式。
- **使用範例:**
  ```json
  {
    "tool": "search_for_pattern",
    "pattern": "TODO:"
  }
  ```
- **預期輸出:** 一個列表，包含所有匹配項的檔案路徑和行號。

### `switch_modes`

- **功能說明:** 啟用一或多個特定的操作模式，不同的模式下可能會有不同的可用工具集或行為。
- **參數說明:**
    - `modes` (字串陣列): 要啟用的模式名稱列表。
- **使用範例:**
  ```json
  {
    "tool": "switch_modes",
    "modes": ["debug", "performance_test"]
  }
  ```
- **預期輸出:** 成功切換模式的確認訊息。

### Agent 自我引導與反思工具

以下工具主要設計用於讓 LLM Agent 進行自我引導和反思，確保其行為符合預期，並能判斷任務的完成狀態。一般使用者較少直接調用。

- **`initial_instructions`**: 提供關於如何使用 Serena 工具箱的初始指令，主要用於某些無法自動讀取系統提示 (System Prompt) 的 MCP 客戶端。
- **`prepare_for_new_conversation`**: 提供在新對話開始前如何準備上下文的指令。
- **`summarize_changes`**: 指示 agent 總結對程式碼庫所做的變更。
- **`think_about_collected_information`**: 一個「思考工具」，讓 agent 評估目前收集到的資訊是否完整，是否足以進行下一步。
- **`think_about_task_adherence`**: 一個「思考工具」，讓 agent 評估自己是否仍然在正確的任務軌道上。
- **`think_about_whether_you_are_done`**: 一個「思考工具」，讓 agent 在宣告任務完成前，再次確認所有目標是否均已達成。
