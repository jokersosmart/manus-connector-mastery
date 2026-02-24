# Zapier 連結器使用者操作手冊

作者：Joker

---

## 歡迎來到您的自動化控制中心

本手冊是為終端使用者設計的實用指南，旨在向您展示如何透過簡單的自然語言指令，利用 Manus 和 Zapier 連結器來自動化您的日常工作。我們將以任務為導向，提供一系列具體的指令範例，並解釋 Manus 是如何將您的話語轉換為強大的自動化工具調用。您不需要了解程式碼，也不需要知道 API 是什麼，您只需要說出您的需求。

## 核心理念：您的話語，就是指令

Manus 的設計哲學是讓技術適應人，而不是讓人去學習複雜的技術。對於 Zapier 連結器，這意味著您不再需要關心「觸發」、「動作」、「API Key」這些術語。您只需專注於您想完成的 **任務**。

當您向 Manus 下達一個關於自動化的指令時，它會像一位聰明的助理一樣，在幕後完成所有繁瑣的設定工作。以下是一些常見的任務場景以及您可以如何向 Manus 下達指令。

--- 

### 任務一：會議管理自動化

**您的目標**：每次 Google 日曆上有新的會議安排時，自動在 Notion 的「會議記錄」資料庫中建立一個對應的頁面，並提前 10 分鐘在 Slack 中提醒您。

#### 您可以這樣說：

> 「Manus，幫我設定一個會議流程。當我的 Google 日曆有新活動時，在我的 Notion『會議記錄』資料庫裡建立一個同名的新頁面，然後在會議開始前 10 分鐘，在 Slack 上提醒我。」

#### Manus 如何理解與執行：

1.  **識別觸發**：Manus 聽到「當我的 Google 日曆有新活動時」，它會將其轉換為一個 Zapier 觸發器。
    *   **工具調用 (推測)**：`zapier.create_polling_trigger(app='google_calendar', event='new_event')`

2.  **識別第一個動作**：接著，它聽到「在我的 Notion『會議記錄』資料庫裡建立一個同名的新頁面」，這被解析為一個建立動作。
    *   **工具調用 (推測)**：`zapier.create_action(app='notion', action_type='create', data={'database_id': 'YOUR_MEETING_DB_ID', 'properties': {'Title': '{{trigger.summary}}'}})`
    *   **智慧之處**：Manus 自動將日曆活動的「標題」(summary) 作為 Notion 頁面的「標題」(Title)。

3.  **識別第二個動作**：最後，它聽到「在會議開始前 10 分鐘，在 Slack 上提醒我」，這是一個帶有延遲的動作。
    *   **工具調用 (推測)**：這會被轉換為一個包含延遲步驟 (Delay by Zapier) 和一個 Slack 動作的多步驟 Zap。
    *   `zapier.create_action(app='delay_by_zapier', action_type='delay_until', data={'timestamp': '{{trigger.start_time_minus_10m}}'})`
    *   `zapier.create_action(app='slack', action_type='send_direct_message', data={'recipient': 'You', 'message': '會議提醒：{{trigger.summary}} 即將在 10 分鐘後開始。'})`

--- 

### 任務二：社群媒體內容同步

**您的目標**：將您發布在 Instagram 上的新照片，自動同步到您的 Pinterest 圖板和 Twitter。

#### 您可以這樣說：

> 「Manus，以後我發布新的 Instagram 照片時，自動幫我釘到 Pinterest 的『我的攝影作品』圖板上，並且也發布一條帶有這張照片的推文。」

#### Manus 如何理解與執行：

1.  **識別觸發**：`instagram.new_media_posted`
    *   **工具調用 (推測)**：`zapier.create_rest_hook_trigger(app='instagram', event='new_media')` (Instagram 通常使用即時的 REST Hooks)

2.  **識別第一個動作 (Pinterest)**：`pinterest.create_pin`
    *   **工具調用 (推測)**：`zapier.create_action(app='pinterest', action_type='create', data={'board': 'YOUR_BOARD_ID', 'image_url': '{{trigger.media_url}}', 'note': '{{trigger.caption}}'})`
    *   **智慧之處**：Manus 自動將 Instagram 的「圖片連結」和「標題」對應到 Pinterest 的 Pin 中。

3.  **識別第二個動作 (Twitter)**：`twitter.create_tweet_with_image`
    *   **工具調用 (推測)**：`zapier.create_action(app='twitter', action_type='create', data={'status': '{{trigger.caption}}', 'image_url': '{{trigger.media_url}}'})`

--- 

### 任務三：客戶關係管理 (CRM) 自動化

**您的目標**：當您在 Typeform 上收到新的客戶諮詢表單時，自動在 Hubspot 中建立或更新一個聯絡人，並指派給銷售團隊的成員。

#### 您可以這樣說：

> 「Manus，當有人提交我的 Typeform『聯絡我們』表單時，去 Hubspot 裡找一下這個客戶的 email。如果找到了，就更新他的資料；如果沒找到，就建立一個新的聯絡人。然後把這個聯絡人指派給銷售部的 Joker。」

#### Manus 如何理解與執行：

1.  **識別觸發**：`typeform.new_entry`
    *   **工具調用 (推測)**：`zapier.create_rest_hook_trigger(app='typeform', event='new_entry', form_id='YOUR_FORM_ID')`

2.  **識別搜尋與建立動作**：聽到「找一下...如果找到了...如果沒找到...」，Manus 知道這是一個「搜尋或建立」(Search or Create) 的複合動作。
    *   **工具調用 (推測)**：`zapier.create_search_action(app='hubspot', action_type='search', query='email:{{trigger.email}}', create_if_not_found=True, create_data={'email': '{{trigger.email}}', 'firstname': '{{trigger.first_name}}', 'lastname': '{{trigger.last_name}}'})`

3.  **識別後續動作**：`hubspot.assign_owner`
    *   **工具調用 (推測)**：`zapier.create_action(app='hubspot', action_type='update', data={'object_id': '{{search_action_result.id}}', 'properties': {'hubspot_owner_id': 'JOKER_USER_ID'}})`
    *   **智慧之處**：Manus 會利用上一步「搜尋或建立」動作返回的聯絡人 ID，來執行這一步的指派操作。

--- 

## 結論：您的想像力是唯一的限制

如您所見，您不需要成為技術專家，也能建構出強大而實用的自動化工作流程。關鍵在於清晰地思考您的目標，並將其分解為一個個以任務為導向的步驟。然後，只需像與真人助理對話一樣，將您的需求告訴 Manus。

現在就開始吧！想一想您工作中最耗時、最重複的環節是什麼，然後試著用一句話告訴 Manus，讓它為您代勞。
