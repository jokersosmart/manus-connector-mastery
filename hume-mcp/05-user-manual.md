# Supabase API 終端使用者操作手冊

**作者：Joker**

歡迎使用 Manus Supabase API 連接器！本手冊專為終端使用者設計，旨在幫助您了解如何透過簡單的自然語言指令，與您的 Supabase 後端進行互動。您將看到具體的指令範例，以及 Manus 如何聰明地將您的話語轉換為對應的工具調用。

## 核心概念：從指令到行動

Manus 的魔力在於其強大的自然語言理解能力。當您下達一個指令時，Manus 會分析您的意圖，並從 Supabase API 連接器提供的工具箱中，挑選出最適合的工具來完成任務。它會自動填寫必要的參數，並執行 API 請求。您無需關心技術細節，只需專注於您想完成的「事」。

以下，我們將透過一系列任務導向的範例，展示這個過程是如何運作的。

## 任務一：管理您的產品庫存

假設您有一個 `products` 資料表，用來追蹤您的商品庫存。

### 1. 查詢庫存低於特定數量的商品

-   **您的指令**：「幫我找出 `products` 資料表中，庫存 (stock) 少於 10 件的所有商品。」
-   **Manus 的轉換**：
    -   **工具**：`get_rows`
    -   **參數**：
        -   `table_name`: "products"
        -   `filter`: "stock,lt,10" (lt 代表 'less than')

### 2. 新增一項新產品

-   **您的指令**：「在 `products` 資料表新增一項產品，名稱是『智慧保溫瓶』，價格是 1200，庫存有 50 件。」
-   **Manus 的轉換**：
    -   **工具**：`insert_rows`
    -   **參數**：
        -   `table_name`: "products"
        -   `data`: `[{"name": "智慧保溫瓶", "price": 1200, "stock": 50}]`

### 3. 更新特定商品的價格

-   **您的指令**：「在 `products` 資料表中，將 ID 為 'prod-003' 的商品價格更新為 999。」
-   **Manus 的轉換**：
    -   **工具**：`update_rows`
    -   **參數**：
        -   `table_name`: "products"
        -   `filter`: "id,eq,prod-003"
        -   `data`: `{"price": 999}`

## 任務二：管理使用者上傳的檔案

您的應用程式可能允許使用者上傳個人頭像，這些檔案儲存在名為 `avatars` 的儲存桶中。

### 1. 列出某位使用者目錄下的所有檔案

-   **您的指令**：「列出 `avatars` 儲存桶中，路徑為 `user-abc/` 下的所有檔案。」
-   **Manus 的轉換**：
    -   **工具**：`list_files`
    -   **參數**：
        -   `bucket_name`: "avatars"
        -   `path`: "user-abc/"

### 2. 上傳新的使用者頭像

-   **您的指令**：「將我電腦裡 `/home/ubuntu/new_avatar.png` 這個檔案，上傳到 `avatars` 儲存桶，並存放在 `user-abc/` 路徑下，命名為 `profile.png`。」
-   **Manus 的轉換**：
    -   **工具**：`upload_file`
    -   **參數**：
        -   `bucket_name`: "avatars"
        -   `source_path`: "/home/ubuntu/new_avatar.png"
        -   `destination_path`: "user-abc/profile.png"

### 3. 刪除舊的暫存檔案

-   **您的指令**：「幫我從 `avatars` 儲存桶中，刪除 `temp/old_pic1.jpg` 和 `temp/old_pic2.jpg` 這兩個檔案。」
-   **Manus 的轉換**：
    -   **工具**：`delete_files`
    -   **參數**：
        -   `bucket_name`: "avatars"
        -   `file_paths`: `["temp/old_pic1.jpg", "temp/old_pic2.jpg"]`

## 任務三：執行後端自訂邏輯

有時您需要執行一些在伺服器上預先定義好的複雜計算，例如結算每月的會員積分。

### 呼叫一個 RPC 函式

-   **您的指令**：「請呼叫 `calculate_monthly_points` 這個資料庫函式，並傳入參數 `user_id` 為 'user-123'，月份為 '2026-02'。」
-   **Manus 的轉換**：
    -   **工具**：`call_function`
    -   **參數**：
        -   `function_name`: "calculate_monthly_points"
        -   `args`: `{"user_id": "user-123", "month": "2026-02"}`

## 結語

如您所見，您不需要成為一名程式設計師，也能透過 Manus Supabase API 連接器輕鬆地管理您的應用程式後端。關鍵在於清晰地表達您的需求。嘗試用不同的方式描述您的任務，您會發現 Manus 總能聰明地理解並為您執行。祝您使用愉快！
