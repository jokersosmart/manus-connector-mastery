# Outlook Calendar 連結器：從新手到精通

作者：Joker

---

## 前言：重新定義你的日曆

歡迎來到「Outlook Calendar 連結器」的精通之旅。本指南將帶你從基礎概念出發，一步步進階，最終讓你能夠靈活運用此連結器，將你的 Outlook 日曆從一個被動的記事工具，轉變為一個主動、智慧的工作流程自動化核心。忘掉繁瑣的手動排程和提醒吧，你將學會如何讓日曆「聽懂」你的需求，並為你完成跨應用的複雜任務。

本指南分為四個階段，每個階段都建立在前一個的基礎之上，確保你能夠穩固地建立技能，並在最後達到精通的水平。

- **第一章：基礎奠基 (Foundations)** - 理解連結器的核心價值與基本互動模式。
- **第二章：核心技能 (Core Skills)** - 掌握最常用、最關鍵的日曆管理工具。
- **第三章：進階技巧 (Advanced Techniques)** - 學習處理複雜場景，如協調多人時間、管理重複事件等。
- **第四章：實戰應用 (Practical Applications)** - 將所學融會貫通，建構強大的真實世界自動化工作流。

準備好了嗎？讓我們開始這趟通往自動化大師的旅程。

---

## 第一章：基礎奠基 (Foundations)

### 1.1 核心價值：不只是日曆，更是工作流的觸發器

傳統上，我們將日曆視為記錄約會的工具。但「Outlook Calendar 連結器」的真正威力在於，它將你的每一個日曆事件都轉化為一個潛在的「觸發器」。

> 正如我們在深度分析中得出的結論：「連結器的核心價值在於將日曆數據轉化為跨應用工作流的觸發器」。

這意味著你可以建立這樣的規則：
- **如果** `明天有一個標記為「重要」的會議`，**就** `在今晚自動將會議簡報從 SharePoint 下載到我的 OneDrive，並發送一條 Slack 提醒給我`。
- **如果** `下週三下午三點到五點有空`，**就** `自動接受來自特定客戶的會議邀請`。

這種將日曆事件與其他應用程式（Email, Slack, Todoist, SharePoint 等）連動的能力，是釋放你生產力的關鍵。

### 1.2 首次互動：查詢你的第一個事件

學習任何新工具最好的方式就是親自動手。讓我們從最簡單的操作開始：查詢你今天的行程。

你可以使用 `graph_listCalendarView` 工具。這個工具會抓取指定時間範圍內的所有事件，包括重複事件的實例。

**指令範例**：
「幫我看看今天有什麼行程？」

**Manus 的轉換 (底層工具調用)**：
```json
{
  "tool": "graph_listCalendarView",
  "parameters": {
    "startDateTime": "2026-02-24T00:00:00Z", // 今天開始時間
    "endDateTime": "2026-02-24T23:59:59Z",   // 今天結束時間
    "orderby": "start/dateTime"             // 按開始時間排序
  }
}
```

**練習**：嘗試用自然語言查詢「我明天下午有什麼事？」或「下週一的所有會議」。觀察 Manus 如何為你取得結果。

---

## 第二章：核心技能 (Core Skills)

掌握了基礎後，我們來學習日常工作中最高頻的三個操作：建立 (Create)、讀取 (Read)、更新 (Update)。

### 2.1 建立事件 (Create)

使用 `graph_createEvent` 工具，你可以快速建立一個新的會議或約會。最關鍵的參數是 `subject`, `start`, `end`, 和 `attendees_addresses`。

**指令範例**：
「幫我安排一個會議，主題是『專案啟動會』，時間是明天下午 2 點到 3 點，邀請我和 marketing@example.com。」

**Manus 的轉換**：
```json
{
  "tool": "graph_createEvent",
  "parameters": {
    "subject": "專案啟動會",
    "start": { "dateTime": "2026-02-25T14:00:00", "timeZone": "Your_Time_Zone" },
    "end": { "dateTime": "2026-02-25T15:00:00", "timeZone": "Your_Time_Zone" },
    "attendees_addresses": ["user@example.com", "marketing@example.com"]
  }
}
```

**Pro-Tip**：你可以一次性加入地點、會議內容，甚至直接將其設定為 Teams 線上會議 (`isOnlineMeeting: true`)。

### 2.2 讀取事件 (Read)

除了用 `listCalendarView` 進行範圍查詢，你也可以用 `getEvent` 獲取特定事件的詳細資訊，或用 `listEvents` 搭配 `$filter` 進行精準搜尋。

**指令範例**：
「找一下我所有關於『Q3 財報』的會議。」

**Manus 的轉換**：
```json
{
  "tool": "graph_listEvents",
  "parameters": {
    "filter": "contains(subject, 'Q3 財報')"
  }
}
```

### 2.3 更新與刪除事件 (Update & Delete)

計畫總趕不上變化。使用 `graph_updateEvent` 和 `graph_deleteEvent` 可以輕鬆修改或取消會議。你只需要提供目標事件的 `eventId`。

**指令範例**：
「把我下午的『專案啟動會』延後一小時。」

**Manus 的轉換 (兩步驟)**：
1.  先用 `listEvents` 找到「專案啟動會」的 `eventId`。
2.  再用 `graph_updateEvent` 更新其 `start` 和 `end` 時間。

**練習**：嘗試建立一個會議，然後更新它的地點，最後將它刪除。熟悉這個完整的生命週期管理。

---

## 第三章：進階技巧 (Advanced Techniques)

現在你已經是個合格的日曆管理者了。接下來，讓我們挑戰一些更複雜、更能體現自動化價值的場景。

### 3.1 尋找共同空閒時間：終結來回溝通

`graph_findMeetingTimes` 是此連結器的一大「殺手級功能」。它能幫你自動比對所有參與者的行事曆，找出大家都有空的時間，徹底告別「你什麼時候有空？」的無盡循環。

**指令範例**：
「幫我找一個我和 Alex (alex@example.com) 下週都有空的 30 分鐘會議時間。」

**Manus 的轉換**：
```json
{
  "tool": "graph_findMeetingTimes",
  "parameters": {
    "attendees_addresses": ["user@example.com", "alex@example.com"],
    "meetingDuration": "PT30M",
    "timeConstraint": { 
      "timeslots": [{ 
        "start": { "dateTime": "2026-03-02T09:00:00", "timeZone": "UTC" }, 
        "end": { "dateTime": "2026-03-06T17:00:00", "timeZone": "UTC" } 
      }]
    }
  }
}
```

**Pro-Tip**：你可以加入更多限制，例如 `minimumAttendeePercentage` (最低出席率) 來找到最合適的時間，即使不是所有人都有空。

### 3.2 管理邀請：接受、拒絕與回應

你可以透過 `graph_acceptEvent` 和 `graph_declineEvent` 來自動處理會議邀請。

**指令範例**：
「幫我自動拒絕所有在午餐時間 (12-1pm) 的會議邀請。」

**Manus 的轉換 (建立一個規則)**：
1.  定期使用 `listEvents` 檢查是否有新的邀請。
2.  如果邀請的 `start` 時間在 12-1pm 之間，則調用 `graph_declineEvent` 並附上訊息「午餐時間，無法參加」。

### 3.3 處理重複事件

在 `graph_createEvent` 中加入 `recurrence` 參數，即可建立重複事件。你可以定義重複的模式（每天、每週、每月）和範圍（直到某個日期或重複幾次）。

**指令範例**：
「建立一個每週五下午 4 點的團隊周會，直到年底。」

---

## 第四章：實戰應用 (Practical Applications)

達到這個階段，你已經掌握了所有工具。現在是將它們組合起來，創造驚人價值的時候了。實戰應用的核心在於**組合與串聯**。

### 4.1 實戰場景一：智慧會議助理

**目標**：建立一個全自動的會議安排與提醒流程。

**工作流**：
1.  **觸發**：你收到一封包含「會議邀請」字樣的郵件 (使用 Email 連結器)。
2.  **分析**：Manus 從郵件內文中提取出建議的時間、主題和參與者。
3.  **檢查**：調用 `graph_getSchedule` 檢查你在該時段是否有空。
4.  **決策**：
    - 如果有空，調用 `graph_createEvent` 建立事件，並調用 `graph_acceptEvent` 接受邀請。
    - 如果沒空，調用 `graph_findMeetingTimes` 找出新的可用時間，並回覆郵件建議新時間。
5.  **準備**：在會議開始前 1 小時，調用 `graph_getEvent` 獲取會議主題，然後在 SharePoint (使用 SharePoint 連結器) 中搜尋相關文件，並將連結透過 Slack (使用 Slack 連結器) 發送給你。

### 4.2 實戰場景二：個人專注時間守護者

**目標**：自動保護你的「深度工作」時間不被會議打擾。

**工作流**：
1.  **設定**：你先在自己的日曆上手動建立一個名為「深度工作 (Focus Time)」的重複事件，例如每天上午 9-11 點。
2.  **觸發**：Manus 定期 (例如每小時) 檢查新的會議邀請 (`listEvents` with `filter='isNew'`)。
3.  **檢查**：對於每一個新邀請，調用 `graph_getSchedule` 檢查會議時間是否與你的「深度工作」時段重疊。
4.  **行動**：如果重疊，自動調用 `graph_declineEvent` 拒絕邀請，並附上訊息：「抱歉，該時段為我的個人專注工作時間，可否改約其他時段？您可以使用此連結查看我的空閒時間：[你的公開日曆連結]」。

## 結語：你的想像力是唯一的限制

恭喜你完成了從新手到精通的旅程！你現在所擁有的，不僅僅是操作一個工具的技能，而是一種全新的工作思維：**將重複性的手動操作，轉化為自動化、智慧化的工作流**。

Outlook Calendar 連結器是一個強大的平台，而今天所學的只是起點。不斷嘗試、組合、創造，你會發現，唯一的限制就是你的想像力。現在就去打造屬於你自己的第一個自動化工作流吧！
