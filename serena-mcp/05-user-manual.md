_Author: Joker_

# Serena 終端使用者操作手冊

本手冊旨在為終端使用者展示如何透過自然語言指令與 Manus 互動，以驅動 Serena 連結器完成複雜的軟體開發任務。我們將透過一系列常見的開發場景，解析使用者的一句簡單指令，是如何被 Manus 拆解為一連串精確的 Serena 工具調用。

理解這個轉換過程，將幫助您更有效地與 Manus 協作，並充分發揮 Serena 作為一個 AI 開發夥伴的潛力。

## 核心哲學：從「意圖」到「執行」

您不需要學習 Serena 的每一個具體工具名稱或參數。您的任務是清晰地表達您的「開發意圖」(Intent)，而 Manus 的任務則是將這個意圖「翻譯」成一系列具體的「工具執行」(Execution)。

以下場景將揭示這個「翻譯」過程的內幕。

## 場景一：初探一個新的程式碼庫

當您接手一個新專案時，首要任務是快速建立對程式碼庫的整體認知。您不需要親自深入每一個檔案，只需向 Manus 表達您的探索意圖。

**您的指令 (The Intent):**

> 「嘿 Manus，幫我分析一下這個專案的結構。它的入口點在哪裡？主要的模組和功能有哪些？」

**Manus 的執行過程 (The Execution):**

收到指令後，Manus 會啟動 Serena 連結器，並像一位經驗豐富的開發者一樣，開始它的探索之旅。這個過程可能包含以下一系列工具調用：

1.  **啟動專案:** 首先，它會確認當前的工作目錄，並將其設定為 Serena 的作用範圍。
    ```json
    {
      "tool": "activate_project",
      "project_path": "/path/to/your/project"
    }
    ```

2.  **尋找設定檔:** 它會使用 `list_dir` 和 `read_file` 來尋找並閱讀 `package.json`, `pom.xml` 或 `build.gradle` 等專案設定檔，目的是找到專案的名稱、依賴以及最重要的——啟動腳本。
    ```json
    {
      "tool": "read_file",
      "path": "package.json"
    }
    ```

3.  **定位入口點:** 從啟動腳本中，它能找到主程式的入口檔案，例如 `src/index.js`。接著，它會閱讀這個檔案。
    ```json
    {
      "tool": "read_file",
      "path": "src/index.js"
    }
    ```

4.  **繪製結構圖:** 為了理解這個檔案的結構，它會使用 `get_symbols_overview` 來獲取一個包含所有頂層函式、類別和變數的清單。
    ```json
    {
      "tool": "get_symbols_overview",
      "path": "src/index.js"
    }
    ```

5.  **追蹤依賴關係:** 對於清單中一個看起來很重要的符號，例如 `createApp`，它會使用 `find_referencing_symbols` 來查看專案中還有哪些地方呼叫了它，從而描繪出模組之間的依賴關係圖。
    ```json
    {
      "tool": "find_referencing_symbols",
      "symbol": "createApp",
      "file_path": "src/index.js",
      // ... line and column info
    }
    ```

**最終結果:**

經過這一系列操作後，Manus 會向您回報一份清晰的摘要，內容可能包含：「這個專案是一個 Node.js 應用，入口點在 `src/index.js`。它使用了 Express 框架，並定義了三個主要模組：用戶管理、產品目錄和訂單處理...」

您只用了一句話，就完成了一位開發者可能需要半小時才能完成的初步分析工作。

## 場景二：進行一次安全的重構

假設您在程式碼審查 (Code Review) 中發現，一個名為 `getUser` 的函式命名不夠精確，因為它實際上獲取的是用戶的個人資料 (Profile)。您希望將其重新命名為 `getUserProfile`，並確保所有呼叫它的地方都被同步更新。

**您的指令 (The Intent):**

> 「Manus，請幫我把 `getUser` 這個函式重新命名為 `getUserProfile`。它定義在 `src/services/userService.js` 裡面。請確保整個專案中所有用到它的地方都被更新。」

**Manus 的執行過程 (The Execution):**

Manus 知道這是一個重構任務，而且是一個有風險的操作。它不會天真地在整個專案中進行簡單的文字尋找和取代，因為那樣可能會錯誤地修改掉不相關的程式碼 (例如，一段註解裡的 "getUser")。相反，它會利用 Serena 的語義分析能力來執行一次「手術式」的重構。

1.  **精準定位:** 首先，它會使用 `find_symbol` 來精準地找到您所說的那個 `getUser` 函式的定義位置，獲取其準確的檔案路徑、行號和列號。
    ```json
    {
      "tool": "find_symbol",
      "symbol": "getUser",
      "file_path": "src/services/userService.js"
    }
    ```

2.  **執行語義重命名:** 接著，它會調用 `rename_symbol` 這個強大的工具。這個工具會請求語言伺服器來執行重命名操作，語言伺服器擁有整個專案的語義索引，因此它知道哪些 `getUser` 是您想改的那個函式，哪些不是。
    ```json
    {
      "tool": "rename_symbol",
      "file_path": "src/services/userService.js",
      "line": 42, // The line number from the previous step
      "column": 9, // The column number from the previous step
      "new_name": "getUserProfile"
    }
    ```

3.  **(可選) 運行測試進行驗證:** 如果您的專案配置了測試指令，Manus 還可能會自動執行測試，以確保這次重構沒有破壞任何現有功能。
    ```json
    {
      "tool": "execute_shell_command",
      "command": "npm test"
    }
    ```

**最終結果:**

Manaus 會回報：「重構完成。函式 `getUser` 已被安全地重命名為 `getUserProfile`，並已更新了專案中所有 17 處對它的引用。所有測試均已通過。」

您避免了一次繁瑣且容易出錯的手動修改，並對變更的正確性充滿信心。

## 場景三：實現一個新功能

這是最能體現 Serena 作為 AI 開發夥伴價值的場景。假設您需要為您的電商應用添加一個「獲取用戶訂單歷史」的 API 端點。

**您的指令 (The Intent):**

> 「Manus，請為我實作一個新的 API 端點 `/users/:userId/orders`。它應該是一個 GET 請求，能夠根據用戶 ID 回傳該用戶的所有訂單列表。你需要建立一個新的 controller 和 service，並在 `routes/index.js` 中註冊這個路由。」

**Manus 的執行過程 (The Execution):**

這是一個複雜的、需要多步驟協調的任務。Manus 會像一位真正的開發者一樣，進行思考、規劃和執行。

1.  **規劃任務:** 在開始編寫程式碼之前，Manus (或其內部的 LLM) 可能會使用 `write_memory` 工具來記錄下它的行動計畫。
    ```json
    {
      "tool": "write_memory",
      "name": "feature_plan_get_orders",
      "content": "1. Create `controllers/orderController.js`. 2. Create `services/orderService.js`. 3. Add `getOrdersByUserId` to `orderController`. 4. Add `findOrdersByUserId` to `orderService`. 5. Register route in `routes/index.js`."
    }
    ```

2.  **建立新檔案:** 它會使用 `create_text_file` 來建立計畫中需要的新檔案，並填入基礎的模板程式碼。
    ```json
    {
      "tool": "create_text_file",
      "path": "src/controllers/orderController.js",
      "content": "// Order controller logic..."
    }
    // ... similar call for orderService.js
    ```

3.  **實作商業邏輯:** 它會開始編寫核心的商業邏輯。它可能會先用 `read_file` 參考一下其他 service 是如何與資料庫互動的，然後在 `orderService.js` 中使用 `insert_at_line` 或 `replace_lines` 來添加 `findOrdersByUserId` 函式。

4.  **實作控制器邏輯:** 接著，它會以同樣的方式在 `orderController.js` 中添加 `getOrdersByUserId` 函式，並在其中呼叫 `orderService.findOrdersByUserId`。

5.  **註冊路由:** 最後，它會讀取 `routes/index.js`，並使用 `insert_at_line` 在適當的位置添加新的路由定義，例如 `router.get("/users/:userId/orders", orderController.getOrdersByUserId);`。

6.  **最終驗證:** 完成程式碼編寫後，它可能會嘗試運行一個 `curl` 指令或相關測試來驗證新端點的功能是否如預期般運作。

**最終結果:**

Manaus 會向您報告：「新功能已完成。API 端點 `GET /users/:userId/orders` 現已可用。相關程式碼已添加到 `orderController.js` 和 `orderService.js` 中。」

您僅僅透過一段清晰的自然語言描述，就完成了一個完整功能的開發、整合與初步驗證。這就是 Serena 與 Manus 結合所帶來的強大力量：將您的開發意圖，無縫轉化為高效率的程式碼實現。
