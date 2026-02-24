_注意：本手冊旨在為終端使用者展示如何透過自然語言與一個理想化的「Neon」MCP 連結器互動。它著重於「做什麼」而非「如何做」，展示了 Manus Agent 如何將使用者意圖轉換為具體的工具調用。作者：Joker_

# 「Neon」連結器終端使用者操作手冊

歡迎使用「Neon」連結器！本手冊將向您展示如何透過簡單的自然語言指令，來管理和操作您強大的 Neon Serverless PostgreSQL 資料庫。您無需關心複雜的 API 或技術細節，只需告訴 Manus 您想完成什麼任務即可。

## 核心理念：從「指令」到「意圖」

傳統上，操作雲端服務需要學習特定的指令或點擊複雜的介面。而 Manus 的理念是理解您的「意圖」。您只需描述您的目標，Agent 就會自動選擇並執行正確的工具來實現它。

下面，我們將透過一系列常見的開發場景，來展示您如何與 Neon 連結器進行互動。

---

## 場景一：開始一個新專案

您即將啟動一個全新的專案，需要一個乾淨、獨立的資料庫環境。

**您的指令：**

> 「我正在開始一個叫 `new-ecommerce-platform` 的專案，幫我建立一個初始的開發資料庫。」

**Manus 的執行過程：**

1.  **意圖識別**：Manus Agent 識別出您需要一個全新的開發環境。關鍵詞是「開始專案」、「建立」、「初始開發資料庫」。
2.  **工具選擇**：Agent 判斷 `setup_dev_environment` 是最符合此意圖的工具。
3.  **參數提取**：
    *   `project_name` 被設定為 `new-ecommerce-platform`。
    *   由於您沒有指定分支名稱，Agent 會智慧地使用一個預設名稱，例如 `main` 或 `development`。
4.  **工具調用**：Agent 在背景執行類似以下的調用：
    ```json
    default_api.neon.setup_dev_environment(
      project_name="new-ecommerce-platform", 
      branch_name="main"
    )
    ```
5.  **結果呈現**：Agent 會將執行結果以友好的方式呈現給您，並提供最重要的資訊——資料庫連線字串。

---

## 場景二：開發一個新功能

您需要在不影響主開發分支的情況下，為您的應用增加一個「使用者評論」功能。

**您的指令：**

> 「在 `new-ecommerce-platform` 專案中，我需要一個獨立的環境來開發『使用者評論』功能。請從 `main` 分支建立一個新的資料庫分支。」

**Manus 的執行過程：**

1.  **意圖識別**：關鍵詞是「獨立環境」、「開發功能」、「從 main 建立分支」。這清晰地指向了建立一個 feature 分支的意圖。
2.  **工具選擇**：`setup_dev_environment` 再次被選中，因為它正是用於此類場景。
3.  **參數提取**：
    *   `project_name` 被設定為 `new-ecommerce-platform`。
    *   Agent 會根據您的描述「使用者評論功能」，為新分支生成一個合適的名稱，例如 `feature/user-comments`。
    *   `from_branch` 被設定為 `main`。
4.  **工具調用**：
    ```json
    default_api.neon.setup_dev_environment(
      project_name="new-ecommerce-platform",
      branch_name="feature/user-comments",
      from_branch="main"
    )
    ```
5.  **結果呈現**：您會得到一個專為「使用者評論」功能準備的、隔離的資料庫連線字串。

---

## 場景三：修復一個緊急的線上 Bug

您的線上應用出現了一個緊急 Bug，但這個 Bug 只在生產資料下才能重現。您需要在不影響線上服務的情況下，複製一份生產資料來進行除錯。

**您的指令：**

> 「線上出問題了！快幫我在 `new-ecommerce-platform` 專案裡，把 `production` 資料庫複製一份，建立一個叫 `hotfix/payment-gateway-bug` 的除錯環境。」

**Manus 的執行過程：**

1.  **意圖識別**：「線上出問題」、「複製 production 資料庫」、「除錯環境」這些強烈的信號，讓 Agent 明白這是一個需要精確複製生產資料的緊急任務。
2.  **工具選擇**：Agent 選擇了專為此場景設計的 `create_test_branch_from_prod` 工具。
3.  **參數提取**：
    *   `project_name` 被設定為 `new-ecommerce-platform`。
    *   `prod_branch` 被設定為 `production`。
    *   `test_branch_name` 被設定為 `hotfix/payment-gateway-bug`。
4.  **工具調用**：
    ```json
    default_api.neon.create_test_branch_from_prod(
      project_name="new-ecommerce-platform",
      prod_branch="production",
      test_branch_name="hotfix/payment-gateway-bug"
    )
    ```
5.  **結果呈現**：您會立即獲得一個包含完整生產資料副本的、但完全隔離的資料庫環境。現在您可以安全地進行各種測試，而不用擔心對線上使用者造成任何影響。

---

## 場景四：例行清理與成本控制

隨著專案的進展，您建立了許多用於開發和測試的分支。為了保持工作區的整潔並節省成本，您希望清理掉那些早已不再使用的分支。

**您的指令：**

> 「幫我整理一下 `new-ecommerce-platform` 專案，把所有超過一個月沒人碰的開發分支都刪掉。但是 `main`、`staging` 和 `production` 這幾個要保留。」

**Manus 的執行過程：**

1.  **意圖識別**：「整理」、「清理」、「超過一個月沒人碰」、「刪掉」、「保留」——這些詞彙共同指向了清理過期資源的意圖。
2.  **工具選擇**：Agent 選擇了 `cleanup_stale_branches` 工具。
3.  **參數提取**：
    *   `project_name` 被設定為 `new-ecommerce-platform`。
    *   `days_inactive` 被設定為 `30` (從「一個月」換算而來)。
    *   `exclude_branches` 被設定為 `["main", "staging", "production"]`。
4.  **工具調用**：
    ```json
    default_api.neon.cleanup_stale_branches(
      project_name="new-ecommerce-platform",
      days_inactive=30,
      exclude_branches=["main", "staging", "production"]
    )
    ```
5.  **結果呈現**：Agent 會向您報告哪些分支被成功刪除，哪些被保護，讓您對清理操作的結果一目了然。

## 結論

如您所見，您與 Neon 連結器的互動是**對話式**和**目標導向**的。您只需專注於您的開發任務，而將繁瑣的資料庫管理工作交給 Manus。這就是下一代的開發體驗。
