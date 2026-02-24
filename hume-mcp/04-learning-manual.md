# Supabase API 學習手冊

**作者：Joker**

本學習手冊旨在幫助您快速上手 Manus Supabase API 連接器，並掌握其關鍵技能。我們將透過清晰的學習目標和實踐練習，引導您在最短的時間內發揮 Supabase 的強大潛力。

## 模組一：初探 Supabase 與連接器

**學習目標：**
- 了解 Supabase 的核心價值與三大基本服務：資料庫、身份驗證、儲存。
- 成功設定 Manus Supabase API 連接器，並理解 API 金鑰的作用。

**關鍵技能：**
1.  **專案建立**：能夠獨立在 Supabase 平台上建立一個新專案。
2.  **金鑰配置**：能夠在 Supabase 儀表板中找到 API URL 和金鑰，並在 Manus 中正確配置連接器。

**實踐練習：**
1.  建立一個名為 `my-first-app` 的 Supabase 專案。
2.  在 Manus 中新增一個 Supabase API 連接器，並使用您專案的 `anon` 金鑰完成設定。
3.  嘗試第一個指令：「列出我資料庫裡所有的資料表」。雖然目前沒有任何資料表，但這個指令能驗證您的連接是否成功。

## 模組二：資料庫核心操作 (CRUD)

**學習目標：**
- 掌握對 Supabase 資料庫進行建立 (Create)、讀取 (Read)、更新 (Update)、刪除 (Delete) 的基本操作。

**關鍵技能：**
1.  **資料檢索 (`get_rows`)**：能夠使用篩選、排序和欄位選擇等參數，精準地從資料表中獲取所需資料。
2.  **資料修改 (`insert_rows`, `update_rows`, `delete_rows`)**：能夠新增、更新和刪除資料表中的紀錄。

**實踐練習：**
1.  在 Supabase 儀表板的 SQL 編輯器中，建立一個 `contacts` 資料表，包含 `name` (姓名) 和 `email` (信箱) 兩個欄位。
2.  **新增聯絡人**：使用 Manus 指令新增至少三位聯絡人。
    > 「在 `contacts` 資料表新增一筆資料，姓名是『陳大文』，信箱是 'david.chen@example.com'。」
3.  **查詢聯絡人**：查詢所有姓「陳」的聯絡人。
    > 「從 `contacts` 資料表中，找出所有姓名以『陳』開頭的聯-絡人。」
4.  **更新聯絡人**：將其中一位聯絡人的信箱更新為新的地址。
    > 「在 `contacts` 資料表中，將姓名為『陳大文』的紀錄，其信箱更新為 'david.c@newdomain.com'。」
5.  **刪除聯絡人**：刪除一位指定的聯絡人。
    > 「從 `contacts` 資料表中，刪除姓名為『陳大文』的紀錄。」

## 模組三：進階資料庫與儲存功能

**學習目標：**
- 學習如何呼叫資料庫函式 (RPC) 以執行伺服器端邏輯。
- 掌握使用連接器管理 Supabase 儲存 (Storage) 中的檔案。

**關鍵技能：**
1.  **函式呼叫 (`call_function`)**：能夠在 Manus 中成功呼叫一個在 Supabase 資料庫中定義的 RPC 函式。
2.  **檔案管理 (`list_files`, `upload_file`, `download_file`)**：能夠列出、上傳和下載儲存桶中的檔案。

**實踐練習：**
1.  **建立 RPC 函式**：在 Supabase SQL 編輯器中，建立一個名為 `hello_world` 的簡單函式，讓它回傳文字「Hello from Supabase!」。
    ```sql
    create function hello_world() 
    returns text as $$
      select 'Hello from Supabase!';
    $$ language sql;
    ```
2.  **呼叫函式**：在 Manus 中呼叫您剛剛建立的函式。
    > 「呼叫 `hello_world` 函式。」
3.  **建立儲存桶**：在 Supabase 儀表板的 Storage 頁面，建立一個名為 `public-media` 的公開儲存桶。
4.  **上傳檔案**：在您的 Manus 沙箱環境中建立一個測試檔案 (`/home/ubuntu/test.txt`)，然後將其上傳到儲存桶。
    > 「將本地的 `/home/ubuntu/test.txt` 檔案上傳到 `public-media` 儲存桶，並命名為 `welcome.txt`。」
5.  **列出與下載**：確認檔案已上傳，並嘗試將其下載回來。
    > 「列出 `public-media` 儲存桶中的所有檔案。」
    > 「從 `public-media` 儲存桶下載 `welcome.txt` 檔案，並儲存到 `/home/ubuntu/downloads/`。」

## 結語

恭喜您完成本學習手冊！透過這三個模組的學習和實踐，您已經掌握了使用 Manus Supabase API 連接器的核心技能。現在，您可以開始將這些能力應用到您的實際專案中，探索更多可能性，並享受 Supabase 帶來的開發便利性。
