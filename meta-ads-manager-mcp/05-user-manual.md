# 「Cloudflare API」連結器使用者操作手冊

**作者：Joker**

---

本手冊為您展示如何透過簡單的自然語言，指揮 Manus 來管理您的 Cloudflare 服務。您將看到您的日常指令是如何被精準地轉換為對應的 API 工具調用，從而實現高效的自動化維運。

我們的目標是讓您忘記複雜的 API 參數和 JSON 結構，專注於您想要完成的「任務」本身。

## 基礎篇：一問一答，精準執行

### 任務一：盤點現有資源

您需要快速了解您的 Cloudflare 帳戶下有哪些資源，例如您有多少個 KV 命名空間或 R2 儲存桶。

| 您的指令 | Manus 的轉換與執行 |
| :--- | :--- |
| 「幫我列出所有的 Cloudflare KV 命名空間。」 | `manus-mcp-cli tool call kv_namespaces_list --server cloudflare --input '{}'` |
| 「列出我帳戶裡全部的 R2 儲存桶。」 | `manus-mcp-cli tool call r2_buckets_list --server cloudflare --input '{}'` |
| 「我有哪些 D1 資料庫？幫我看看。」 | `manus-mcp-cli tool call d1_databases_list --server cloudflare --input '{}'` |
| 「顯示我所有的 Cloudflare Workers。」 | `manus-mcp-cli tool call workers_list --server cloudflare --input '{}'` |

**解讀：** 當您使用「列出」、「顯示」、「有哪些」等詞語時，Manus 會將其對應到 `*_list` 類型的工具，並執行它來獲取列表資訊。

### 任務二：建立新資源

當您開始一個新專案或功能時，需要建立對應的基礎設施。

| 您的指令 | Manus 的轉換與執行 |
| :--- | :--- |
| 「建立一個新的 KV 命名空間，名字叫 `my-app-settings`。」 | `manus-mcp-cli tool call kv_namespace_create --server cloudflare --input '{"title": "my-app-settings"}'` |
| 「我需要一個新的 R2 儲存桶，命名為 `my-static-assets`。」 | `manus-mcp-cli tool call r2_bucket_create --server cloudflare --input '{"name": "my-static-assets"}'` |
| 「幫我建立一個 D1 資料庫，就叫 `user_profiles` 吧。」 | `manus-mcp-cli tool call d1_database_create --server cloudflare --input '{"name": "user_profiles"}'` |

**解讀：** 「建立」、「新增」、「創建」等動詞，會觸發 `*_create` 工具。Manus 會從您的指令中提取您提供的「名稱」或「標題」，並將其作為工具的 `name` 或 `title` 參數傳入。

### 任務三：清理不再需要的資源

專案結束或測試完成後，及時清理資源是一個好習慣。

| 您的指令 | Manus 的轉換與執行 |
| :--- | :--- |
| 「把 `my-app-settings` 這個 R2 儲存桶刪掉。」 | `manus-mcp-cli tool call r2_bucket_delete --server cloudflare --input '{"name": "my-app-settings"}'` |
| 「刪除 ID 為 `[some-namespace-id]` 的 KV 命名空間。」 | `manus-mcp-cli tool call kv_namespace_delete --server cloudflare --input '{"namespace_id": "[some-namespace-id]"}'` |
| 「這個 D1 資料庫 `[some-database-id]` 不用了，幫我刪除它。」 | `manus-mcp-cli tool call d1_database_delete --server cloudflare --input '{"database_id": "[some-database-id]"}'` |

**解讀：** 「刪除」、「刪掉」、「清理」等指令會對應到 `*_delete` 工具。請注意，刪除操作通常需要提供資源的唯一識別碼（ID 或名稱），Manus 會提示您提供或從上下文中尋找。

## 進階篇：組合指令，流程自動化

Manus 的真正威力在於理解並執行由多個步驟組成的複雜任務，將您從重複性工作中解放出來。

### 任務四：一鍵部署開發環境

**場景：** 您正在為一個新功能 `feature-new-login` 建立一個獨立的測試環境。

**您的指令：**
> 「為了 `feature-new-login`，幫我建立一個名為 `login-feature-kv` 的 KV 命名空間，和一個名為 `login-feature-db` 的 D1 資料庫。」

**Manus 的轉換與執行：**

1.  **步驟一：** `manus-mcp-cli tool call kv_namespace_create --server cloudflare --input '{"title": "login-feature-kv"}'`
2.  **步驟二：** `manus-mcp-cli tool call d1_database_create --server cloudflare --input '{"name": "login-feature-db"}'`

**解讀：** Manus 能夠識別出指令中的「和」、「以及」、「然後」等連接詞，將一個長指令拆解為多個連續執行的工具調用。它會按照您說話的順序，依次完成每個步驟，實現簡單的工作流自動化。

### 任務五：查詢並分析資料

**場景：** 您需要從使用者資料庫中，找出所有在今年註冊且來自特定地區的使用者。

**您的指令：**
> 「幫我查詢 D1 資料庫 `[user-db-id]`，執行 SQL `SELECT email FROM users WHERE country = 'TW' AND strftime('%Y', registration_date) = '2024';`」

**Manus 的轉換與執行：**

*   `manus-mcp-cli tool call d1_database_query --server cloudflare --input '{"database_id": "[user-db-id]", "sql": "SELECT email FROM users WHERE country = \'TW\' AND strftime(\'%Y\', registration_date) = \'2024\';"}'`

**解讀：** 對於像 `d1_database_query` 這樣需要複雜輸入（如 SQL 語句）的工具，您可以直接在指令中提供完整的程式碼或語句。Manus 會準確地將它提取出來，作為 `sql` 參數傳遞給工具。這讓您既能享受自然語言的便捷，又不失精確控制的彈性。

### 任務六：尋求協助與知識

**場景：** 您不確定如何在 Cloudflare 中設定 Argo Tunnel。

**您的指令：**
> 「我想知道如何在 Cloudflare 中設定 Argo Tunnel，幫我查一下官方文件。」

**Manus 的轉換與執行：**

*   `manus-mcp-cli tool call search_cloudflare_documentation --server cloudflare --input '{"query": "How to set up Argo Tunnel in Cloudflare"}'`

**解讀：** 當您需要學習或查詢關於 Cloudflare 的任何知識時，只需直接提問。Manus 會利用 `search_cloudflare_documentation` 工具，將您的問題作為查詢條件，在官方文件中尋找最相關的答案，並將結果呈現給您。

## 結語

本手冊僅僅是個開始。隨著您與 Manus 互動的增多，您會發現更多高效、便捷的自動化方式。我們鼓勵您大膽嘗試，用您自己的語言來描述您的任務，探索自然語言自動化的無限可能。
