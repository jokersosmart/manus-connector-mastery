# Todoist 連結器：從新手到精通

**作者：** Joker

## 前言：解放你的生產力

歡迎來到「Todoist 連結器：從新手到精通」指南。在現代快節奏的工作與生活中，任務管理工具 Todoist 是您強大的盟友。而當 Todoist 與 Manus 的 AI 能力結合時，它將從一個被動的清單工具，蛻變為一個能聽懂您自然語言指令的智慧生產力夥伴。

本指南將帶領您走過一條精心設計的學習路徑，從最基礎的任務創建，到最進階的工作流自動化。無論您是剛接觸 Todoist 的新手，還是希望將任務管理提升到新境界的資深使用者，都能在這裡找到清晰的指引和實用的範例。我們的目標是讓您不僅學會「如何使用」這些工具，更能理解「為何如此設計」，最終將這套強大的能力內化為您自己的生產力習慣。

準備好告別繁瑣的手動操作，用交談的方式掌控您的所有任務了嗎？讓我們開始這段從新手到精通的旅程吧！

## 第一章：基礎概念入門 (The Basics)

在這一章，我們將建立最核心的認知：您不再需要點擊介面，只需像與助理對話一樣，告訴 Manus 您想做什麼。

### 1.1 核心理念：從「點擊」到「對話」

傳統上，您需要打開 Todoist 應用，找到對應的專案，點擊「+」按鈕，輸入任務，選擇日期... 一系列操作才能完成一個簡單的任務新增。現在，這一切都將被簡化為一句話。

> **舊方式：** 手動操作 App 介面。
> **新方式：** 直接告訴 Manus：「明天下午三點提醒我打電話給客戶」。

Manus 會在幕後解析您的指令，並調用 `add_tasks` 工具，將所有參數（內容、截止日期）設定妥當。這就是 Todoist 連結器的核心價值：將您的自然語言意圖，轉化為精確的 API 操作。

### 1.2 您的第一個指令：建立任務

讓我們從最簡單也最常用的功能開始：建立一個任務。這背後對應的是 `add_tasks` 工具。

**嘗試下達您的第一個指令：**

> "add a task to buy milk tomorrow"

或者更自然地：

> "remind me to buy milk tomorrow"

Manus 會理解您的意圖，並為您在 Todoist 的收件匣 (Inbox) 中建立一個名為「buy milk」的任務，截止日期設定為明天。

### 1.3 指定任務的「歸屬」

任務通常不應漂浮在收件匣中，它們需要歸屬於特定的專案。您可以在指令中直接指定。

**嘗試一個更具體的指令：**

> "add a task to 'draft the project proposal' in my 'Work' project"

Manus 會找到名為「Work」的專案，並在其中建立任務。這背後是 `add_tasks` 工具的 `projectId` 參數在發揮作用。您無需關心專案的 ID 是什麼，只需說出它的名字。

### 1.4 檢視您的成果：查詢任務

建立了任務後，自然需要查詢它們。`find_tasks` 工具是您的得力助手。

**嘗試查詢您今天的任務：**

> "show me all my tasks for today"

Manus 會調用 `find_tasks` 工具，並以清晰的列表向您展示所有今天到期的任務。您也可以用更複雜的條件查詢，例如：

> "find all tasks in the 'Work' project with the 'urgent' label"

恭喜您！僅僅透過幾個簡單的對話，您已經掌握了最核心的「建立」與「查詢」循環。在下一章，我們將深入探索更多核心工具的細節，讓您對任務的掌控力更上一層樓。

## 第二章：核心功能詳解 (Core Functionality)

在掌握了基礎的對話式任務管理後，本章將帶您深入了解 Todoist 連結器的核心工具集。我們將逐一拆解任務的各個屬性，並學會如何用自然語言精確地控制它們。

### 2.1 任務的生命週期：建立、完成與刪除

我們已經學會了使用 `add_tasks` 建立任務。一個任務的完整生命週期還包括「完成」和「刪除」。

- **完成任務 (`complete_tasks`)**：當您完成一項工作後，只需告訴 Manus。

  > "mark the task 'buy milk' as complete"

- **刪除任務 (`delete_object`)**：如果您想徹底移除一個任務（而不僅僅是完成它）。

  > "delete the task with ID 987654"

  **注意：** 刪除是高風險操作。通常建議使用「完成」來代替。您需要提供明確的任務 ID 來執行刪除，這是為了防止誤操作。

### 2.2 精確的時間管理：截止日期與持續時間

`add_tasks` 工具在時間處理上非常強大。您可以非常靈活地設定時間。

- **具體日期和時間**："on Sep 1st at 5pm", "in 2 hours"
- **相對日期**："tomorrow", "next Friday"
- **重複性任務**："every day starting tomorrow", "every other week on Monday"
- **持續時間 (`duration`)**：為任務設定預計耗時，有助於規劃您的一天。

  > "add a task to 'write the report' for this Friday, it should take about 4 hours"

### 2.3 結構化您的工作：專案、區塊與子任務

僅有任務列表是混亂的。Todoist 的強大之處在於其結構化能力。連結器完美地支援了這一點。

- **建立專案 (`add_projects`)**：發起一個新目標時，首先為它建立一個專案。

  > "create a new project called 'Summer Vacation Planning'"

- **在專案中加入區塊 (`add_sections`)**：將專案進一步劃分為不同階段或類別。

  > "add sections 'Flights', 'Hotels', and 'Activities' to my 'Summer Vacation Planning' project"

- **建立子任務**：將一個複雜的任務分解為更小的步驟。

  > "add a subtask 'book the flight tickets' to the task 'arrange travel'"

### 2.4 賦予任務更多情境：優先級、標籤與評論

- **設定優先級 (`priority`)**：讓您專注於最重要的事情。

  > "set the priority of the task 'finish the presentation' to p1"

- **貼上標籤 (`labels`)**：跨專案組織和篩選任務的利器。

  > "add the labels 'work' and 'urgent' to the task 'review the contract'"

- **添加評論 (`add_comments`)**：為任務附加筆記、檔案連結或與協作者的討論。

  > "add a comment to task 123456 saying 'The latest draft is attached.'"

通過本章的學習，您已經能夠像一位經驗豐富的專案經理一樣，精細地管理任務的每一個細節。在下一章，我們將探索如何將這些獨立的工具組合起來，創造出強大的自動化工作流。

## 第三章：進階技巧與工作流 (Advanced Techniques & Workflows)

當您熟練掌握了單個工具的使用後，真正的魔法在於將它們串聯起來，建立自動化的工作流程。這一章將向您展示如何從「執行者」轉變為「系統設計者」。

### 3.1 批次處理：一次操作多個項目

連結器的大多數「新增」和「修改」工具都支援批次處理。您可以在一個指令中建立多個任務、專案或評論，極大地提升了效率。

**範例：一次性規劃您的早晨例行公事**

> "add tasks for my morning routine: 1. Meditate for 10 minutes, 2. Review today's schedule, 3. Process inbox emails. All due by 8am today."

Manus 會解析這段話，並在一次 `add_tasks` 調用中，傳入一個包含三個任務物件的陣列。

### 3.2 組合指令：將多個工具串聯

這是通往精通之路的關鍵一步。您可以將多個獨立的指令組合成一個更宏大的目標。

**範例：建立一個完整的專案結構**

> "Create a new project named 'New Website Launch'. Then, add three sections to it: 'Design', 'Development', and 'Marketing'. Finally, add a task 'Draft initial design mockups' to the 'Design' section, due next Monday."

在這個指令中，Manus 會依序執行：
1.  `add_projects` 來建立專案。
2.  `add_sections` 來建立三個區塊。
3.  `add_tasks` 來建立任務，並正確地將其放入指定的區塊。

### 3.3 查詢與行動：動態工作流

更進階的用法是，先用查詢工具獲取資訊，然後基於查詢結果執行下一步操作。

**範例：每週回顧流程**

> "Find all tasks I completed last week in the 'Work' project, and add a comment to my 'Weekly Review' project with a summary of these tasks."

這個流程展示了 AI 的推理能力：
1.  調用 `find_tasks`，篩選出符合條件的已完成任務。
2.  在記憶中對這些任務的標題進行匯總。
3.  調用 `add_comments`，將匯總後的文字作為評論內容，新增到指定的專案中。

### 3.4 團隊協作：指派與溝通

在共享專案中，您可以使用 `add_tasks` 的 `responsibleUser` 參數來指派任務。

**範例：指派一個任務給團隊成員**

> "Assign the task 'Fix the login bug' to Joker and set its priority to p1."

您甚至可以結合評論功能，在指派的同時提供更多上下文。

> "Assign the task 'Design the new logo' to Jane, and add a comment with the link to our design brief document."

掌握了這些進階技巧，您已經不僅僅是在使用一個工具，而是在設計一個屬於您自己的、高度個人化的生產力系統。在最後一章，我們將展望未來，探索如何將 Todoist 與其他工具連結，打造跨平台的超級工作流。

## 第四章：實戰應用與未來展望 (Real-World Applications & Future)

理論最終要服務於實踐。本章將提供一些真實世界的應用場景，並展望 Todoist 連結器在未來與其他工具整合的無限可能，助您成為真正的生產力大師。

### 4.1 場景一：個人 GTD (Getting Things Done) 系統

您可以完全透過對話來實踐 GTD 流程。

- **捕捉 (Capture)**：隨時隨地將腦中閃現的想法丟進收件匣。
  > "add a thought: research about MCP protocol"
- **理清 (Clarify)**：每天固定時間處理收件匣。
  > "show me all tasks in my inbox"
  > "move the task 'research about MCP protocol' to my 'Learning' project and set it due for this weekend"
- **組織 (Organize)**：為任務設定專案、標籤、截止日期。
- **回顧 (Reflect)**：每週回顧已完成和未完成的任務。
  > "show me all my overdue tasks"
- **執行 (Engage)**：專注於當下。
  > "what are the three most important tasks I need to do today?"

### 4.2 場景二：敏捷開發團隊的日常

在一個共享的「Sprint #12」專案中，團隊成員可以協同工作。

- **站會 (Stand-up)**：快速更新進度。
  > "mark task 'API-123' as complete and add a comment: 'The endpoint is deployed and ready for testing.'"
- **任務指派 (Assignment)**：產品經理分配任務。
  > "assign the task 'Write API documentation for API-123' to Alex"
- **Bug 追蹤 (Bug Tracking)**：測試人員發現問題。
  > "create a new p1 task in the 'Bugs' project: 'User cannot log in with Google account.' Add the 'critical' label."

### 4.3 未來展望：跨工具的超級工作流

Todoist 連結器的真正潛力在於 Manus 的平台能力——將多個工具無縫串接。這意味著您的任務管理系統可以與您的郵件、日曆、筆記、程式碼倉庫等完全打通。

**想像一下未來的可能性：**

- **從郵件到任務**：
  > "Find the email from my boss about the Q3 report, create a task in Todoist to 'Draft the Q3 report' with the email content as a description, and set it due for next Friday."
  (涉及 `Gmail` + `Todoist` 連結器)

- **從日曆到任務**：
  > "Look at my Google Calendar for next week, and for every meeting with 'John', create a task in Todoist to 'Prepare agenda for meeting with John' one day before the event."
  (涉及 `Google Calendar` + `Todoist` 連結器)

- **從程式碼到任務**：
  > "Check my GitHub notifications. For every new issue assigned to me in the 'WebApp' repository, create a corresponding task in my 'Work' project in Todoist."
  (涉及 `GitHub` + `Todoist` 連結器)

## 結語：您的生產力，由您定義

我們從一個簡單的「建立任務」指令開始，一路走來，探索了核心功能、進階工作流，並展望了跨工具整合的宏偉藍圖。您現在擁有的，不僅僅是操作一個工具的能力，更是一套全新的、以對話為核心的生產力思維模式。

精通之路永無止境。不斷嘗試、組合、創造，將這些工具融入您獨一無二的工作與生活節奏中。您會發現，當科技真正理解您的意圖時，生產力將不再是關於「管理」任務，而是關於「實現」目標。

現在，去創造屬於您自己的工作流吧！
