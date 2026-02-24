# Jotform 連結器學習手冊

作者：Joker

本手冊旨在幫助您快速上手 Jotform 連結器，掌握關鍵技能，並透過實踐練習鞏固學習成果。

## 核心學習目標

完成本手冊後，您將能夠：

1.  **獨立完成帳戶連接**：安全地授權 Manus 存取您的 Jotform 數據。
2.  **掌握 5 個核心工具**：熟練使用 `form_list`, `create_form`, `edit_form`, `get_submissions`, `create_submission`。
3.  **使用自然語言完成 80% 的常見任務**：包括建立表單、查詢回覆、提交數據等。
4.  **建立一個簡單的自動化任務**：將手動操作轉換為自動化流程。

## 快速入門：三步走

### 第一步：連接帳戶 (5 分鐘)

- **目標**：完成 Jotform 帳戶與 Manus 的連接。
- **練習**：在 Manus 中，輸入指令 `"List my Jotform forms"`，並按照彈出的引導完成 OAuth 授權流程。
- **驗證**：當您看到一個（可能是空的）表單列表時，代表連接成功。

### 第二步：建立與查詢 (15 分鐘)

- **目標**：學會建立新表單和查詢現有回覆。
- **練習 1 (建立)**：
  > `"Create a Jotform named 'My First Form' with a single text field for 'Name'."`
- **練習 2 (查詢)**：
  > `"List all submissions for the form 'My First Form'."` (此時應為空)
- **驗證**：成功建立表單並能查詢其回覆（即使為空）。

### 第三步：提交與驗證 (10 分鐘)

- **目標**：學會以編程方式向表單提交數據。
- **練習**：
  > `"Submit to 'My First Form' with the name 'Test User'."`
- **驗證**：再次執行 `"List all submissions for the form 'My First Form'."`，這次您應該能看到剛剛提交的「Test User」記錄。

--- 

## 關鍵技能培養

### 技能一：精準的數據檢索

僅僅獲取所有數據是不夠的，高效工作的關鍵在於精準。練習使用篩選條件來縮小結果範圍。

- **場景**：您只想查看最近三天內提交的，且狀態為「已批准」的回覆。
- **練習指令**：
  > `"Show me submissions from the 'Vacation Request' form from the last 3 days that are marked as 'Approved'."`
- **核心概念**：學習在您的自然語言指令中包含時間、日期、狀態等限定詞。

### 技能二：動態的表單管理

表單不是一成不變的。學會動態修改它們，以適應不斷變化的需求。

- **場景**：您需要在「客戶滿意度調查」表單中，臨時增加一個關於新功能的開放式問題。
- **練習指令**：
  > `"Add a paragraph text field with the label 'Any feedback on our new feature?' to the 'Customer Satisfaction Survey' form."`
- **核心概念**：掌握 `edit_form` 的能力，了解您可以透過指令增、刪、改表單的問題。

### 技能三：自動化的思維

將重複性工作交給 Manus。開始思考哪些日常操作可以被自動化。

- **場景**：您每天都需要手動將 Jotform 的新訂單同步到一個 Notion 資料庫。
- **練習思考**：
    1.  這個任務的觸發條件是什麼？（例如：每小時一次）
    2.  需要從 Jotform 獲取什麼數據？（`get_submissions`，並篩選出新的）
    3.  需要將數據寫入 Notion 的哪個資料庫？（調用 Notion 連結器的 `create_page`）
    4.  如何將兩者串連起來？（在 Manus 中建立一個包含這兩個步驟的自動化任務）
- **核心概念**：將一個手動流程分解為「觸發-操作-結果」的步驟，並將其轉換為 Manus 的指令序列。

## 結語

恭喜您完成了 Jotform 連結器的快速學習！真正的掌握來自於持續的實踐。現在就開始，將今天學到的技能應用到您的實際工作中，探索更多可能性，讓自動化為您創造價值。
