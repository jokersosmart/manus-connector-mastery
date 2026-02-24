
# Cloudflare 連結器終端使用者操作手冊

**作者：Joker**

---

## 歡迎來到指令中心

本手冊是您的實戰指南。在這裡，我們不談理論，只專注於「任務」。您將看到如何用最簡單的自然語言指令，讓 Manus Agent 為您完成具體的 Cloudflare 管理工作。

每一節都會展示一個常見的任務場景，包含「**您需要說什麼**」和「**Manus 會做什麼**」的對比，讓您直觀地理解意圖驅動的魔力。

---

### 任務一：初始化您的工作環境

**目標：** 在開始任何操作前，確保 Manus 在正確的 Cloudflare 帳戶下工作。

**您需要說什麼：**
> 「嘿 Manus，幫我看一下我的 Cloudflare 有哪些帳戶，然後幫我把『Joker's Account』設為預設操作帳戶。」

**Manus 會做什麼：**

1.  **分析意圖：** 理解到您有兩個連續的請求：首先「查詢」，然後「設定」。
2.  **執行第一步：** 調用 `accounts_list` 工具來獲取所有帳戶的列表。
    ```bash
    manus-mcp-cli tool call accounts_list --server cloudflare --input 
    '{}'
    ```
3.  **執行第二步：** 從上一步的結果中找到名為「Joker's Account」的帳戶 ID，然後調用 `set_active_account` 工具將其設定為活動帳戶。
    ```bash
    manus-mcp-cli tool call set_active_account --server cloudflare --input 
    '{
      "activeAccountIdParam": "01a7362d577a6c3019a474fd6f485823"
    }'
    ```

---

### 任務二：為新專案啟動後端基礎設施

**目標：** 一次性為一個名為「Zenith」的新專案建立所有必需的後端資源。

**您需要說什麼：**
> 「我要啟動一個叫『Zenith』的新專案。幫我建立一個叫 `zenith-config` 的 KV 命名空間，一個叫 `zenith-media` 的 R2 儲存桶，還有一個叫 `zenith-db` 的 D1 資料庫。」

**Manus 會做什麼：**

1.  **分析意圖：** 識別出這是一個包含三個獨立資源建立請求的「組合任務」。
2.  **並行或串行執行：** Manus 會依次（或在可能的情況下並行）執行以下三個工具調用：
    *   建立 KV 命名空間：
        ```bash
        manus-mcp-cli tool call kv_namespace_create --server cloudflare --input 
        '{
          "title": "zenith-config"
        }'
        ```
    *   建立 R2 儲存桶：
        ```bash
        manus-mcp-cli tool call r2_bucket_create --server cloudflare --input 
        '{
          "name": "zenith-media"
        }'
        ```
    *   建立 D1 資料庫：
        ```bash
        manus-mcp-cli tool call d1_database_create --server cloudflare --input 
        '{
          "name": "zenith-db"
        }'
        ```

---

### 任務三：初始化資料庫結構

**目標：** 在新建立的 D1 資料庫中，建立應用程式所需的資料表。

**您需要說什麼：**
> 「在 `zenith-db` 資料庫裡，幫我建立一個 `users` 資料表。它需要有 `id`（整數，主鍵）、`username`（文字）和 `email`（文字）這幾個欄位。」

**Manus 會做什麼：**

1.  **分析意圖：** 理解到這是一個資料庫結構定義 (DDL) 操作。
2.  **生成 SQL：** 根據您的描述，自動生成對應的 `CREATE TABLE` SQL 語句。
3.  **執行查詢：** 調用 `d1_database_query` 工具，在指定的資料庫上執行該 SQL。
    ```bash
    manus-mcp-cli tool call d1_database_query --server cloudflare --input 
    '{
      "database_id": "<zenith-db 的 UUID>",
      "sql": "CREATE TABLE users (id INTEGER PRIMARY KEY, username TEXT, email TEXT);"
    }'
    ```

---

### 任務四：填充初始資料

**目標：** 向資料表中插入第一條資料，例如一個管理員帳戶。

**您需要說什麼：**
> 「在 `zenith-db` 的 `users` 表裡新增一個使用者，使用者名稱是 `admin`，電子郵件是 `admin@zenith.app`。」

**Manus 會做什麼：**

1.  **分析意圖：** 理解到這是一個資料插入 (DML) 操作。
2.  **生成 SQL：** 生成帶有參數化查詢的 `INSERT INTO` 語句，以防止 SQL 注入。
3.  **執行查詢：** 調用 `d1_database_query` 工具，並傳入 SQL 語句和對應的參數。
    ```bash
    manus-mcp-cli tool call d1_database_query --server cloudflare --input 
    '{
      "database_id": "<zenith-db 的 UUID>",
      "sql": "INSERT INTO users (username, email) VALUES (?, ?);",
      "params": ["admin", "admin@zenith.app"]
    }'
    ```

---

### 任務五：清理測試資源

**目標：** 刪除不再需要的測試用資源，避免產生不必要的費用或混亂。

**您需要說什麼：**
> 「我之前的測試專案 `alpha-test` 可以清掉了。幫我刪除 `alpha-test-db` 這個 D1 資料庫和 `alpha-test-assets` 這個 R2 儲存桶。」

**Manus 會做什麼：**

1.  **分析意圖：** 識別出刪除兩個不同類型資源的請求。
2.  **執行刪除操作：** 分別調用對應的刪除工具。
    *   刪除 D1 資料庫：
        ```bash
        manus-mcp-cli tool call d1_database_delete --server cloudflare --input 
        '{
          "database_id": "<alpha-test-db 的 UUID>"
        }'
        ```
    *   刪除 R2 儲存桶：
        ```bash
        manus-mcp-cli tool call r2_bucket_delete --server cloudflare --input 
        '{
          "name": "alpha-test-assets"
        }'
        ```

---

### 任務六：尋求知識與協助

**目標：** 當您對 Cloudflare 的某個功能或限制有疑問時，直接向 Manus 尋求答案。

**您需要說什麼：**
> 「Cloudflare D1 資料庫的儲存上限是多少？」

**Manus 會做什麼：**

1.  **分析意圖：** 判斷這是一個知識查詢類請求。
2.  **選擇最佳工具：** 選擇 `search_cloudflare_documentation` 工具，因為它是獲取官方資訊的最佳途徑。
3.  **執行搜尋：** 將您的問題轉換為搜尋關鍵字，並調用該工具。
    ```bash
    manus-mcp-cli tool call search_cloudflare_documentation --server cloudflare --input 
    '{
      "query": "D1 database storage limit"
    }'
    ```
4.  **呈現結果：** 將從文件中找到的相關段落呈現給您。

---

**像這樣與 Manus 對話，您就可以將精力完全集中在您的業務目標上，而不是繁瑣的技術細節。**
