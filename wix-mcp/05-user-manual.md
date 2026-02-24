# Wrike 連接器使用者操作手冊

**作者: Joker**

---

## 簡介：像與助理對話一樣操作 Wrike

歡迎使用 Wrike 連接器！本手冊旨在幫助您——終端使用者——了解如何透過簡單的自然語言指令，讓 Manus 成為您管理 Wrike 專案的智慧助理。您無需了解複雜的程式碼或 API，只需像平常下達指令一樣，就能完成 Wrike 中的各種操作。

---

## 核心概念：從「指令」到「執行」

當您向 Manus 下達一個關於 Wrike 的指令時，背後會發生一系列的轉換。理解這個過程有助於您更精確地表達您的需求。

**您的指令**: 「幫我在『行銷活動』專案裡，找一下我負責的、還沒完成的任務。」

**Manus 的理解與轉換**:

1.  **意圖識別**: Manus 識別出您想要「搜尋任務」。
2.  **實體提取**: Manus 從您的話中提取出關鍵資訊：
    -   **專案/資料夾**: 「行銷活動」
    -   **負責人**: 「我」 (轉換為您的 Wrike 使用者 ID)
    -   **狀態**: 「還沒完成」 (轉換為 `Active`)
3.  **工具調用**: Manus 將這些資訊組合起來，調用 `wrike.tasks.search` 工具，並傳入對應的參數。

    ```json
    // Manus 在背景執行的操作
    tool: wrike.tasks.search
    input: {
      "folder_id": "IEAGTX42I4OF...", // 「行銷活動」專案的 ID
      "assignees": ["KUAJ25S4"],      // 您的使用者 ID
      "status": "Active"
    }
    ```
4.  **結果呈現**: Manus 將工具返回的結果，以友善的格式呈現給您。

---

## 常用指令範例 (以任務為導向)

以下是一些您在日常工作中可能用到的指令範例，以及 Manus 將如何把它們轉換為工具調用。

### 任務查詢

| 您的指令 | Manus 的工具調用 |
| :--- | :--- |
| 「我在 Wrike 上有哪些待辦事項？」 | `wrike.tasks.search({"assignees": ["me"], "status": "Active"})` |
| 「顯示『Q4 產品發表』專案裡的所有任務。」 | `wrike.tasks.search({"folder_id": "PROJECT_ID"})` |
| 「找一下王小明上週完成的任務。」 | `wrike.tasks.search({"assignees": ["USER_ID"], "status": "Completed", "updated_date_range": "last_week"})` |
| 「有沒有標題包含『合約』的任務？」 | `wrike.tasks.search({"title": "合約"})` |

### 任務創建

| 您的指令 | Manus 的工具調用 |
| :--- | :--- |
| 「幫我建一個任務，標題是『準備週會報告』。」 | `wrike.tasks.create({"title": "準備週會報告"})` |
| 「在『客戶支援』資料夾裡，建一個任務『回覆李先生的郵件』，交給張三處理。」 | `wrike.tasks.create({"folder_id": "FOLDER_ID", "title": "回覆李先生的郵件", "assignees": ["USER_ID"]})` |
| 「創建一個『設計新的 Logo』的任務，把它放到『品牌重塑』專案，並同時加到『設計部』的待辦清單。」 | `wrike.tasks.create({"title": "設計新的 Logo", "parent_ids": ["PROJECT_ID", "FOLDER_ID"]})` |

### 任務更新與溝通

| 您的指令 | Manus 的工具調用 |
| :--- | :--- |
| 「把『準備週會報告』這個任務標示為完成。」 | `wrike.tasks.update({"task_id": "TASK_ID", "status": "Completed"})` |
| 「在『設計新的 Logo』任務下面留言：『初稿已經完成，請大家給點意見。』」 | `wrike.comments.create({"task_id": "TASK_ID", "text": "初稿已經完成，請大家給點意見。"})` |
| 「把『測試新功能』這個任務的負責人改成李四。」 | `wrike.tasks.update({"task_id": "TASK_ID", "assignees": ["USER_ID"]})` |
| 「幫我把這份文件 `report.pdf` 上傳到『完成簡報設計』這個任務裡。」 | `wrike.attachments.upload({"task_id": "TASK_ID", "file_path": "/path/to/report.pdf"})` |

### 專案與資料夾操作

| 您的指令 | Manus 的工具調用 |
| :--- | :--- |
| 「列出我所有的專案。」 | `wrike.folders.list({"project": true})` |
| 「在『行銷活動』空間下，建立一個名為『2026 夏季推廣』的新專案。」 | `wrike.folders.create({"parent_id": "SPACE_ID", "title": "2026 夏季推廣", "project": {}})` |

---

## 提示與技巧

- **明確指定**: 盡量說出完整的專案或資料夾名稱，如果名稱重複，Manus 可能會詢問您是哪一個。
- **使用 ID**: 如果您知道某個任務或專案的 ID，直接使用 ID 是最精確的方式，例如：「把任務 `IEAGTX42KQOE37AF` 的狀態改成已完成」。
- **組合指令**: 您可以將多個指令組合在一起，例如：「在『網站開發』專案中建一個任務『修復登入頁面 Bug』，然後把它指派給陳大文。」
- **循序漸進**: 如果不確定，可以先從查詢開始，確認找到正確的目標後，再下達修改或評論的指令。

透過這些簡單的指令，您就可以將繁瑣的 Wrike 點擊操作，轉變為與智慧助理的高效對話。現在就試試您的第一個指令吧！
