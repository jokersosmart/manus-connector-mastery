# JSONBin.io 連結器終端使用者操作手冊

**作者：** Joker

---

本手冊為終端使用者設計，旨在展示如何透過自然的語言指令，讓 Manus Agent 為您操作 JSONBin.io 連結器來完成各種資料儲存與管理的任務。您將看到具體的指令範例，以及 Manus 如何將這些指令轉換為精確的工具調用。

## 核心概念：從指令到行動

您與 Manus 的互動是基於自然語言的。您只需要告訴 Manus 您「想要做什麼」，而不需要關心具體的技術細節。Manus 會分析您的意圖，並自動選擇最合適的工具和參數來執行您的請求。以下範例將揭示這一過程。

--- 

### 任務一：儲存一筆新的使用者資料

當您需要快速儲存一筆新的 JSON 資料時，例如一個新註冊使用者的個人資料。

**您的指令：**

> 「嘿 Manus，幫我在 JSONBin.io 上建立一個私有的 Bin，名稱是 `new_user_profile`，內容是 `{"username": "alex", "plan": "premium"}`。」

**Manus 的行動 (工具調用)：**

```json
{
  "tool_name": "jsonbin.create_bin",
  "parameters": {
    "json_content": "{\"username\": \"alex\", \"plan\": \"premium\"}",
    "bin_name": "new_user_profile",
    "is_private": true
  }
}
```

**解析：**

-   Manus 識別出「建立」和「Bin」等關鍵字，選擇了 `create_bin` 工具。
-   它將您指令中的「內容是...」直接對應到 `json_content` 參數。
-   「名稱是...」對應到 `bin_name` 參數。
-   「私有的」這個詞被轉換為 `is_private: true`，體現了對權限的理解。

--- 

### 任務二：讀取先前儲存的資料

當您需要檢視某個特定 Bin 的內容時。

**您的指令：**

> 「Manus，幫我讀取一下 JSONBin.io 上 ID 為 `63f8b3b3e4f90735e832d3a1` 的 Bin 裡面的內容。」

**Manus 的行動 (工具調用)：**

```json
{
  "tool_name": "jsonbin.read_bin",
  "parameters": {
    "bin_id": "63f8b3b3e4f90735e832d3a1"
  }
}
```

**解析：**

-   「讀取」和「ID 為...」是明確的信號，Manus 因此選擇了 `read_bin` 工具。
-   它精確地提取了您提供的 `bin_id` 並將其填入對應的參數。

--- 

### 任務三：將多筆資料歸類管理

當您希望將相關的 Bins 組織在一起以便管理時。

**您的指令：**

> 「Manus，請在 JSONBin.io 上建立一個叫做 `Project-Alpha-Tasks` 的新集合。然後，建立一個新的 Bin，內容是 `{"task": "Design landing page"}`，並把它放進剛剛那個集合裡。」

**Manus 的行動 (工具調用)：**

這是一個兩步驟的任務，Manus 會依序執行：

1.  **第一步：建立集合**

    ```json
    {
      "tool_name": "jsonbin.create_collection",
      "parameters": {
        "collection_name": "Project-Alpha-Tasks"
      }
    }
    ```

2.  **第二步：建立 Bin 並歸類** (假設上一步返回的 ID 是 `c1a2b3d4e5f6`)

    ```json
    {
      "tool_name": "jsonbin.create_bin",
      "parameters": {
        "json_content": "{\"task\": \"Design landing page\"}",
        "collection_id": "c1a2b3d4e5f6"
      }
    }
    ```

**解析：**

-   Manus 理解了這是一個有先後順序的複合任務。
-   它首先執行 `create_collection` 來滿足您的第一個請求。
-   接著，它會記住上一步操作返回的 `collection_id`，並在執行 `create_bin` 時，將其作為 `collection_id` 參數傳入，完美地實現了「放進剛剛那個集合裡」的指令。

--- 

### 任務四：更新一筆現有資料

當資料發生變化，需要更新時。

**您的指令：**

> 「Manus，把 JSONBin.io 上 ID 為 `63f8b3b3e4f90735e832d3a1` 的 Bin 內容更新為 `{"status": "completed"}`。」

**Manus 的行動 (工具調用)：**

```json
{
  "tool_name": "jsonbin.update_bin",
  "parameters": {
    "bin_id": "63f8b3b3e4f90735e832d3a1",
    "json_content": "{\"status\": \"completed\"}"
  }
}
```

**解析：**

-   「更新」是關鍵動詞，引導 Manus 選擇 `update_bin` 工具。
-   與建立操作類似，它會同時提取 `bin_id` 和新的 `json_content`，並準確地填入參數中。

## 結論

如您所見，您完全可以用日常對話的方式來命令 Manus 操作 JSONBin.io。關鍵在於清晰地表達您的 **意圖** (想做什麼)、**對象** (操作哪個 Bin 或 Collection) 和 **內容** (具體的資料是什麼)。剩下的繁瑣工作，Manus 都會為您自動完成。
