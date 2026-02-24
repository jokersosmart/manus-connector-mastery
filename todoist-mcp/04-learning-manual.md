# Todoist 連結器學習手冊

**作者：** Joker

## 歡迎來到快速上手中心

本學習手冊專為希望在最短時間內掌握 Todoist 連結器核心功能的您而設計。我們摒棄了冗長的理論，專注於提供清晰的學習目標和立即可上手的實踐練習。透過本手冊，您將在一個小時內，從一位新手，成長為能夠自信地使用自然語言管理 Todoist 任務的使用者。

每個模組都包含三個關鍵部分：

- **學習目標 (Objectives)**：明確告知您在本單元將學會什麼。
- **核心指令 (Core Commands)**：聚焦於一到兩個關鍵工具或概念。
- **實踐練習 (Practice)**：提供具體的指令讓您立即嘗試，並解釋其背後的原理。

準備好開始您的極速學習之旅了嗎？讓我們即刻開始。

## 模組一：10 分鐘任務大師 (10-Minute Task Master)

**時間：** 10 分鐘

### 學習目標

- 學會如何快速將腦中的待辦事項加入 Todoist。
- 學會如何查詢今天需要完成的任務。

### 核心指令

本模組的核心是 `add_tasks` 工具，它是您與 Todoist 互動最頻繁的工具。

### 實踐練習

1.  **練習一：您的第一個任務**

    **下達指令：**
    > "add task to call mom tonight"

    **背後原理：**
    Manus 調用 `add_tasks` 工具，將 "call mom tonight" 作為任務內容 (`content`)。由於您沒有指定專案，任務會被自動放入您的 Todoist「收件匣」(Inbox)。AI 會智能識別 "tonight" 並將其轉換為今天的日期作為截止日期 (`dueString`)。

2.  **練習二：指定專案**

    **下達指令：**
    > "add a task to my 'Shopping' project to buy some bread"

    **背後原理：**
    這次，Manus 會在 `add_tasks` 調用中，額外傳入 `projectId` 參數。它會自動尋找您名為「Shopping」的專案，並將任務放入其中。您無需記憶複雜的專案 ID。

3.  **練習三：檢視成果**

    **下達指令：**
    > "what are my tasks for today?"

    **背後原理：**
    這個指令會觸發 `find_tasks` 工具。Manus 會設定篩選條件，找出所有截止日期為今天的任務，並以清晰的列表呈現給您。

**恭喜！** 您已經掌握了最重要的輸入和輸出循環。您現在可以隨時隨地捕捉您的想法，並快速了解當天的焦點。

## 模組二：20 分鐘結構化您的生活 (20-Minute Life Structuring)

**時間：** 20 分鐘

### 學習目標

- 學會建立專案和區塊來組織您的任務。
- 學會使用截止日期和優先級來管理您的時間和精力。

### 核心指令

本模組圍繞 `add_projects`、`add_sections` 以及 `add_tasks` 的進階參數 `priority` 和 `dueString`。

### 實踐練習

1.  **練習一：建立您的第一個專案**

    **下達指令：**
    > "create a new project called 'Learn Spanish'"

    **背後原理：**
    Manus 調用 `add_projects` 工具，建立一個名為「Learn Spanish」的新專案。這是您組織相關任務的容器。

2.  **練習二：為專案添加結構**

    **下達指令：**
    > "add sections 'Vocabulary', 'Grammar', and 'Practice' to my 'Learn Spanish' project"

    **背後原理：**
    此指令觸發 `add_sections` 工具，在您剛建立的專案中新增了三個區塊。這有助於您將學習計劃分解為更小的部分。

3.  **練習三：新增一個帶有優先級和截止日期的任務**

    **下達指令：**
    > "in my 'Learn Spanish' project, add a p1 task to 'memorize 20 new verbs' in the 'Vocabulary' section, due this Wednesday"

    **背後原理：**
    這是一個更複雜的 `add_tasks` 調用。Manus 會解析出：
    - `content`: "memorize 20 new verbs"
    - `projectId`: 「Learn Spanish」專案的 ID
    - `sectionId`: 「Vocabulary」區塊的 ID
    - `priority`: "p1"
    - `dueString`: "this Wednesday"

**太棒了！** 您現在不僅能建立任務，更能為您的生活和工作建立清晰的結構。您已經從一個簡單的清單使用者，成長為一位懂得規劃的架構師。

## 模組三：30 分鐘自動化您的工作流 (30-Minute Workflow Automation)

**時間：** 30 分鐘

### 學習目標

- 學會使用組合指令來完成多步驟操作。
- 學會如何指派任務給他人，進行團隊協作。
- 理解如何將查詢結果作為下一步行動的輸入。

### 核心指令

本模組是所有基礎工具的綜合應用，特別是 `add_tasks` 的 `responsibleUser` 參數，以及將 `find_tasks` 與其他工具結合的思維模式。

### 實踐練習

1.  **練習一：一句话設定一個會議**

    **下達指令：**
    > "Create a p2 task to 'Schedule a budget meeting with the finance team' for tomorrow morning. Also, add a subtask to 'Prepare the presentation slides'."

    **背後原理：**
    Manus 會執行兩次 `add_tasks` 操作。第一次建立父任務，並獲取其 ID。第二次再次調用 `add_tasks`，這次會傳入 `parentId` 參數來建立子任務，將兩者關聯起來。

2.  **練習二：指派一個任務**

    **前提：** 您需要在一個與同事（例如：`jane@example.com`）共享的專案中。

    **下達指令：**
    > "In our 'Marketing Campaign' project, assign the task 'Design the new banner ad' to Jane."

    **背後原理：**
    Manus 在調用 `add_tasks` 時，會使用 `responsibleUser` 參數。它會先在專案的協作者中尋找名為 "Jane" 或郵件為 `jane@example.com` 的使用者，然後將任務指派給她。

3.  **練習三：清理您的過期任務**

    **下達指令：**
    > "Find all my overdue tasks and reschedule them to tomorrow."

    **背後原理：**
    這是一個動態工作流的絕佳範例。
    1.  Manus 首先調用 `find_tasks`，篩選出所有 `overdue` (已過期) 的任務，並獲取它們的 ID 列表。
    2.  然後，它會遍歷這個 ID 列表，並為每個任務調用 `update_tasks` 工具 (一個我們未在基礎部分詳述，但功能強大的工具)，將它們的 `dueString` 更新為 "tomorrow"。

**祝賀您完成所有模組！**

您已經從一個只會簡單指令的新手，成長為一位能夠設計和自動化自己工作流程的進階使用者。您掌握的不仅是工具，更是一種全新的、高效的生產力方法。現在，請自由地去探索、組合，創造出專屬於您的指令和工作流吧！
