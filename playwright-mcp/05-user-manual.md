# 「Close」連結器使用者操作手冊

作者：Joker

## 簡介

歡迎使用 Manus Close 連結器！本手冊將向您展示如何透過簡單的自然語言指令，來操作強大的 Close 銷售 CRM。您將學習到如何將日常的銷售任務，轉化為給 Manus 的指令，並理解 Manus 是如何將這些指令翻譯成具體的工具調用的。

我們的目標是讓您感覺像在與一位超級助理對話，而不是在操作一個複雜的軟體。

---

### **任務一：管理您的客戶**

這是您最常執行的任務：記錄和查詢客戶資訊。

| 您的指令 (範例) | Manus 的理解與行動 |
| :--- | :--- |
| 「新增一個客戶『Global Exports Ltd.』，他們是潛在客戶。」 | **意圖**：建立新的潛在客戶 (Lead)。<br>**工具**：`close.create_lead`<br>**參數**：`name='Global Exports Ltd.'`, `status='Potential'` |
| 「在『Global Exports Ltd.』底下加個聯絡人，叫『Maria Garcia』，信箱是 maria@globalexports.com。」 | **意圖**：為已存在的 Lead 新增聯絡人 (Contact)。<br>**工具**：`close.create_contact`<br>**參數**：`lead_id='(查詢 Global Exports Ltd. 的 ID)'`, `name='Maria Garcia'`, `emails=['maria@globalexports.com']` |
| 「幫我找一下『Global Exports Ltd.』的所有活動記錄。」 | **意圖**：查詢特定 Lead 的相關活動 (Activity)。<br>**工具**：`close.get_activities`<br>**參數**：`lead_id='(查詢 Global Exports Ltd. 的 ID)'` |
| 「把『Global Exports Ltd.』的狀態改成『已聯繫』。」 | **意圖**：更新 Lead 的資訊。<br>**工具**：`close.update_lead`<br>**參數**：`id='(查詢 Global Exports Ltd. 的 ID)'`, `status='Contacted'` |

---

### **任務二：追蹤您的銷售機會**

管理銷售管道是確保業績達成的關鍵。

| 您的指令 (範例) | Manus 的理解與行動 |
| :--- | :--- |
| 「為『Global Exports Ltd.』開一個新機會，價值 50,000 美元，關於『年度供應合約』。」 | **意圖**：建立新的銷售機會 (Opportunity)。<br>**工具**：`close.create_opportunity`<br>**參數**：`lead_id='...'`, `value=50000`, `note='年度供應合約'` |
| 「我贏得了『Global Exports Ltd.』的『年度供應合約』！」 | **意圖**：更新 Opportunity 的狀態。<br>**工具**：`close.update_opportunity`<br>**參數**：`id='(查詢對應的 Opportunity ID)'`, `status='Won'` |

---

### **任務三：安排您的工作**

確保沒有任何重要事項被遺忘。

| 您的指令 (範例) | Manus 的理解與行動 |
| :--- | :--- |
| 「提醒我後天要打電話給 Maria Garcia。」 | **意圖**：建立一個待辦任務 (Task)。<br>**工具**：`close.create_task`<br>**參數**：`text='打電話給 Maria Garcia'`, `due_date='(計算後天的日期)'` |
| 「記錄一通給『Global Exports Ltd.』的電話，我們討論了合約細節。」 | **意圖**：記錄一次通話活動 (Call Activity)。<br>**工具**：`close.log_call`<br>**參數**：`lead_id='...'`, `note='討論了合約細節'` |

---

### **任務四：自動化您的流程**

當您熟悉了基礎操作後，就可以開始讓 Manus 為您分擔更多重複性工作。

| 您的指令 (範例) | Manus 的理解與行動 |
| :--- | :--- |
| 「把所有超過 30 天沒有互動的『潛在客戶』，狀態都改成『冷卻』。」 | **意圖**：批次更新符合特定條件的 Leads。<br>**工具**：`close.bulk_update_leads`<br>**參數**：`query='last_activity > 30d AND status:"Potential"'`, `status='Cold'` |
| 「幫我產生一份報告，列出上個月所有贏得的機會，按金額排序。」 | **意圖**：查詢、排序並呈現數據。<br>**行動**：<br>1. **工具**：`close.get_opportunity`<br>   **參數**：`status='Won'`, `date_updated_range='(上個月)'`<br>2. **程式邏輯**：對返回的結果列表，按 `value` 欄位進行降序排序。<br>3. **輸出**：以表格形式呈現排序後的報告。 |

## 結論

如您所見，您不需要學習複雜的程式碼或 API 語法。您只需要用清晰的語言，告訴 Manus 您的**目標**是什麼。Manus 會負責將您的目標分解，並選擇最合適的工具來執行。

您越是能將您的工作任務，用「動詞 + 名詞 + 條件」的結構來描述，您與 Manus 的協作就會越順暢。祝您使用愉快！
