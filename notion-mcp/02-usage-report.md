# Supabase 連結器使用說明報告

作者：Joker

## 總覽

本文件旨在為 Manus MCP 的「Supabase」連結器提供一份完整的使用說明。Supabase 作為一個開源的 Firebase 替代品，提供了一整套後端即服務 (BaaS) 的功能，包括資料庫、認證、檔案儲存和邊緣函數。此連結器將這些強大的功能封裝成一系列直觀的 Manus 工具，讓開發者能以自然語言快速建構與管理其 Supabase 後端。

本報告將詳細介紹連結器提供的每一項工具，包含其功能、參數、使用範例及預期輸出，以協助開發者充分利用此連結器的能力。

## 工具詳解

連結器工具主要分為四大模組：`database`、`auth`、`storage` 和 `functions`。

### 1. Database 模組

Database 模組提供了與 Supabase Postgres 資料庫進行互動的核心功能，支援完整的 CRUD (建立、讀取、更新、刪除) 操作以及遠端程序呼叫 (RPC)。

#### `database.select`

此工具用於從指定的資料表中查詢數據。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `from` | string | 是 | 目標資料表的名稱。 |
| `select` | string | 否 | 要查詢的欄位，以逗號分隔。預設為 `*` (所有欄位)。 |
| `filter` | object | 否 | 一個或多個過濾條件，用於精確查找數據。支援 `eq` (等於)、`gt` (大於)、`lt` (小於) 等操作符。 |
| `limit` | integer | 否 | 回傳的資料筆數上限。 |

**使用範例**

> 查詢 `products` 資料表中，所有 `category` 為 `laptops` 且 `price` 大於 1000 的前 5 項商品名稱與價格。

**預期輸出**

一個包含查詢結果的 JSON 陣列，例如：

```json
[
  {"name": "Laptop Pro X", "price": 1200},
  {"name": "UltraBook Air", "price": 1500}
]
```

#### `database.insert`

此工具用於將一筆或多筆新紀錄插入到指定的資料表中。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `into` | string | 是 | 目標資料表的名稱。 |
| `values` | object/array | 是 | 一個 JSON 物件或物件陣列，代表要插入的紀錄。 |

**使用範例**

> 在 `users` 資料表中新增一位名為 Alice、年齡 30 歲的使用者。

**預期輸出**

一個包含新增紀錄的 JSON 陣列。

#### `database.update`

此工具用於更新符合條件的紀錄。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `table` | string | 是 | 目標資料表的名稱。 |
| `values` | object | 是 | 要更新的欄位與新值。 |
| `filter` | object | 是 | 用於指定要更新哪些紀錄的過濾條件。 |

**使用範例**

> 將 `products` 資料表中 `id` 為 `prod_123` 的商品價格更新為 999。

**預期輸出**

一個包含更新後紀錄的 JSON 陣列。

#### `database.delete`

此工具用於刪除符合條件的紀錄。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `from` | string | 是 | 目標資料表的名稱。 |
| `filter` | object | 是 | 用於指定要刪除哪些紀錄的過濾條件。 |

**使用範例**

> 刪除 `orders` 資料表中所有 `status` 為 `cancelled` 的訂單。

**預期輸出**

一個包含被刪除紀錄的 JSON 陣列。

### 2. Auth 模組

Auth 模組封裝了 Supabase 的使用者認證功能，讓應用程式可以輕鬆管理使用者登入、註冊和狀態。

#### `auth.signUp`

此工具用於建立一個新使用者帳號。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `email` | string | 是 | 新使用者的電子郵件。 |
| `password` | string | 是 | 新使用者的密碼。 |
| `metadata` | object | 否 | 附加到使用者上的額外資料。 |

**使用範例**

> 使用 `test@example.com` 和密碼 `password123` 註冊一個新使用者。

**預期輸出**

一個包含新使用者資訊 (如 `id`, `email`, `created_at`) 的 JSON 物件。

#### `auth.signInWithPassword`

此工具用於使用電子郵件和密碼登入現有使用者。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `email` | string | 是 | 使用者的電子郵件。 |
| `password` | string | 是 | 使用者的密碼。 |

**使用範例**

> 使用 `test@example.com` 和密碼 `password123` 登入。

**預期輸出**

一個包含使用者資訊和 session token 的 JSON 物件。

### 3. Storage 模組

Storage 模組讓開發者可以輕鬆地與 Supabase 的檔案儲存服務互動，進行檔案的上傳、下載與管理。

#### `storage.upload`

此工具用於將本地檔案上傳到指定的儲存桶 (bucket)。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `bucket` | string | 是 | 目標儲存桶的名稱。 |
| `local_path` | string | 是 | 要上傳的本地檔案路徑。 |
| `storage_path` | string | 否 | 在儲存桶中存放的路徑。預設為檔案名稱。 |

**使用範例**

> 將本地的 `/home/ubuntu/avatar.png` 檔案上傳到名為 `avatars` 的儲存桶中。

**預期輸出**

一個包含已上傳檔案公開 URL 的 JSON 物件。

### 4. Functions 模組

Functions 模組用於調用部署在 Supabase 平台上的邊緣函數 (Edge Functions)。

#### `functions.invoke`

此工具用於觸發一個指定的邊緣函數，並傳遞參數。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `name` | string | 是 | 要調用的函數名稱。 |
| `payload` | object | 否 | 傳遞給函數的 JSON 物件。 |

**使用範例**

> 調用名為 `send-welcome-email` 的函數，並傳遞 `{ "user_id": "usr_456" }` 作為參數。

**預期輸出**

函數回傳的結果，格式取決於函數本身的設計。
