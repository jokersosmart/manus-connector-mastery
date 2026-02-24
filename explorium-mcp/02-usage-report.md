> **核心摘要**：Manus for Explorium 的使用，圍繞著三大核心 API 工具：`enrich_entity`、`train_model` 和 `get_predictions`。這三者，構成了一個從「數據增強」到「模型訓練」再到「預測應用」的完整閉環。其架構，是將使用者的自然語言請求，轉化為對 Explorium API 的精確調用，並將複雜的、非同步的任務流程（如模型訓練），進行了無縫的封裝和管理，最終將洞察，以人類可讀或機器可用的格式，返回給使用者或下游系統。

---

## 1. 架構概覽 (Architecture Overview)

Manus for Explorium 的互動架構，可以被理解為一個「智慧轉譯與執行層」。它位於使用者與 Explorium 強大的數據科學平台之間，扮演著關鍵的橋樑角色。

1.  **輸入層 (Input Layer)**: 使用者，可以透過多種介面（如 Slack、Teams、或 Manus 的原生聊天介面），以自然語言，或結構化的指令，發出請求。例如：「`幫我豐富這家公司「Innovate Inc.」的資料`」或「`使用這個客戶列表，建立一個流失預測模型`」。

2.  **理解與轉譯層 (Understanding & Translation Layer)**: Manus 的自然語言理解 (NLU) 引擎，會解析使用者的意圖。它會識別出關鍵的「實體」(如公司名稱 `Innovate Inc.`)、「動作」(如 `豐富資料`、`建立模型`)，以及「參數」(如數據來源 `客戶列表.csv`、預測目標 `是否流失`)。

3.  **工具選擇與參數對應 (Tool Selection & Parameter Mapping)**: 基於解析出的意圖，Manus 會選擇最合適的 Explorium MCP 工具。例如，「豐富資料」的意圖，會被對應到 `enrich_entity` 工具。Manus 會將使用者提供的資訊，打包成該工具所需的 JSON 輸入格式。

4.  **執行與非同步管理層 (Execution & Asynchronous Management Layer)**: Manus 調用 Explorium MCP 工具，向 Explorium API 發出請求。
    *   對於**同步任務** (如 `enrich_entity`)，Explorium 會在短時間內，返回擴充後的數據，Manus 接收後，直接進行下一步處理。
    *   對於**非同步任務** (如 `train_model`)，Explorium API 會立即返回一個 `task_id`。Manus 會自動進入一個「輪詢 (Polling)」模式，在背景中，定期使用這個 `task_id`，去查詢任務的狀態。這個過程，對使用者是完全透明的。使用者不需要關心任務是否在運行，或者何時完成。

5.  **結果處理與輸出層 (Result Processing & Output Layer)**: 一旦任務完成，Manus 會從 Explorium 獲取結果。
    *   對於 `enrich_entity`，結果是豐富的數據欄位。
    *   對於 `train_model`，結果是一個 `model_id`。
    *   對於 `get_predictions`，結果是預測分數。
    Manus 會將這些原始的 API 輸出，進行格式化，轉化為易於理解的報告、表格，或直接將數據，寫入到使用者指定的其他系統中（如 HubSpot 的自定義欄位）。

這個架構的核心優勢，在於**將複雜的、多步驟的、包含非同步等待的數據科學流程，抽象化為一個簡單的、單次的、同步的使用者互動**。

## 2. 核心工具使用詳解 (Core Tools Deep Dive)

以下表格，詳細說明了 Manus for Explorium 的核心工具、其用途，以及關鍵的輸入與輸出。

| 工具名稱 (Tool Name) | 核心用途 (Core Purpose) | 關鍵輸入參數 (Key Input Parameters) | 輸出結果 (Output) | 使用範例 (Example Scenario) |
| :--- | :--- | :--- | :--- | :--- |
| `enrich_entity` | **實體數據擴充** | `entity_type`: `company` 或 `person`<br>`entity_data`: 包含實體標識符的字典，如 `{"name": "Innovate Inc."}` 或 `{"email": "john.doe@example.com"}` | 一個包含數十個甚至上百個外部數據欄位的 JSON 物件，如行業、員工人數、技術棧、社交媒體資料等。 | 在與新客戶會面前，銷售代表，希望快速了解對方的公司背景。 |
| `train_model` | **訓練預測模型** | `data_source`: 包含訓練數據的檔案路徑 (CSV) 或數據源 ID<br>`target_variable`: 您希望預測的目標欄位名稱，如 `has_churned`<br>`model_name`: 為模型命名 | 一個獨一無二的 `model_id`，代表了在 Explorium 平台中，已經訓練完成並可供使用的模型。 | 市場團隊，希望根據歷史客戶數據，建立一個模型，來預測新客戶的終身價值 (LTV)。 |
| `get_predictions` | **獲取模型預測** | `model_id`: `train_model` 步驟返回的模型 ID<br>`data_to_predict`: 需要進行預測的數據，可以是單筆記錄，也可以是一個包含多筆記錄的檔案路徑 (CSV) | 一個 JSON 物件或檔案，其中包含了每一筆輸入記錄的預測結果（如預測分數、標籤）以及解釋性資訊。 | 將新註冊的用戶列表，傳遞給一個預先訓練好的「升級可能性」模型，以識別出高潛力的用戶，進行精準行銷。 |

## 3. 典型工作流 (Typical Workflows)

### 工作流一：單次的公司背景研究 (One-off Company Research)

這是一個最直接、最簡單的工作流，只涉及單一工具的使用。

1.  **使用者**: 「`幫我查一下「Cyberdyne Systems」這家公司的資料，我需要知道他們的員工人數、預估年收入和最近的新聞。`」
2.  **Manus**: (內部執行)
    *   **意圖識別**: 豐富公司資料。
    *   **工具選擇**: `enrich_entity`。
    *   **參數構建**: `{"entity_type": "company", "entity_data": {"name": "Cyberdyne Systems"}}`。
3.  **Manus**: (返回結果) 「`「Cyberdyne Systems」是一家位於加州的科技公司，預計員工人數在 500-1000 人之間，預估年收入約為 1.2 億美元。最近的新聞顯示，他們發布了一款新的 AI 晶片...`」

### 工作流二：自動化的潛在客戶評分 (Automated Lead Scoring)

這個工作流，展示了 Manus 如何將 Explorium 的能力，嵌入到一個持續運行的業務流程中，並與其他工具（如 HubSpot）進行整合。

1.  **設定 (Setup)**: 使用者，預先在 Explorium 中，訓練好一個「線索轉化預測模型」，並獲得 `model_id` (例如 `model-xyz-123`)。
2.  **觸發 (Trigger)**: Manus 監聽到，在 HubSpot 中，創建了一條新的線索 (公司: `Blue Sun Corp`)。
3.  **執行 (Execution)**: Manus 自動執行一個包含三個步驟的序列：
    1.  **調用 `enrich_entity`**: `{"entity_type": "company", "entity_data": {"name": "Blue Sun Corp"}}`，獲取該公司的外部數據。
    2.  **準備預測數據**: 將第一步獲取的擴充數據，格式化為模型所需的輸入格式。
    3.  **調用 `get_predictions`**: `{"model_id": "model-xyz-123", "data_to_predict": [enriched_data]}`，獲取預測分數 (例如 `0.87`)。
4.  **行動 (Action)**: Manus 調用 HubSpot MCP 工具，將分數 `87`，以及關鍵的擴充數據（如行業、規模），更新到 `Blue Sun Corp` 在 HubSpot 中的對應欄位。

### 工作流三：自助式預測建模 (Self-Service Predictive Modeling)

這個最複雜的工作流，展示了 Manus 如何讓非技術背景的使用者，也能完成端到端的建模任務。

1.  **使用者**: 「`Manus，這是我上個季度的所有用戶活動記錄 (`users.csv`)，裡面有一個 `has_upgraded` 欄位。請用 Explorium 的外部數據，幫我建立一個模型，來預測用戶是否會升級。`」
2.  **Manus**: (內部執行)
    1.  **調用 `train_model`**: `{"data_source": "/path/to/users.csv", "target_variable": "has_upgraded", "model_name": "user_upgrade_prediction_q3"}`。
    2.  **等待任務完成**: Manus 進入非同步等待模式，輪詢 Explorium API，直到模型訓練完成。
    3.  **獲取 `model_id`**: 從 Explorium 獲取新生成的模型 ID (例如 `model-abc-456`)。
3.  **Manus**: (返回結果) 「`好的，我已經使用您的數據和 Explorium 的外部信號，成功建立了一個名為「user_upgrade_prediction_q3」的用戶升級預測模型。模型 ID 是 `model-abc-456`。您現在可以使用這個模型，來對新的用戶進行預測了。`」

這些工作流，清晰地展示了 Manus for Explorium 如何將一個專業的數據科學工具，轉化為一個靈活、易用、且能深度融入業務流程的智慧自動化服務。
