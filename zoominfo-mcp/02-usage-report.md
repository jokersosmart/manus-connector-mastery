> **核心摘要**：Manus for ZoomInfo 的架構，是一個以「篩選器 (Filters)」為核心的、高度結構化的查詢與執行系統。無論是搜索公司、聯絡人，還是意圖信號，其本質，都是將使用者的自然語言需求，轉譯為一個精確的、由多個條件組成的 JSON 篩選器物件，然後將其傳遞給 ZoomInfo 的 API。其強大之處，不僅在於單次查詢的精準，更在於能夠將這些查詢，嵌入到持續運行的、跨越多個系統（如 CRM、Slack）的自動化工作流中，實現從「線索發現」到「數據豐富」再到「觸發行動」的端到端市場開發自動化。

---

## 1. 架構概覽 (Architecture Overview)

Manus for ZoomInfo 的互動架構，可以被視為一個「智慧 B2B 查詢生成器與工作流協調器」。

1.  **輸入層 (Input Layer)**: 使用者，以自然語言，提出一個關於市場開發的需求。例如：「`幫我找找紐約地區，員工人數超過 1000 人的軟體公司。`」

2.  **自然語言理解與篩選器構建層 (NLU & Filter Construction Layer)**: 這是 Manus 的核心智慧所在。它會解析使用者的語句，並將其映射為一個結構化的 ZoomInfo API 篩選器物件。以上述請求為例，Manus 會在內部，構建出類似以下的 JSON 結構：
    ```json
    {
      "location": ["New York, USA"],
      "employee_count_min": 1001,
      "industry": ["Software"]
    }
    ```
    這個過程，涉及地理位置的標準化、數字範圍的識別，以及行業關鍵字的映射。

3.  **工具選擇層 (Tool Selection Layer)**: 根據請求的目標（是找「公司」還是找「人」），Manus 會選擇對應的工具，如 `search_companies` 或 `search_contacts`。

4.  **執行與分頁處理層 (Execution & Pagination Handling Layer)**: Manus 調用選定的工具，將構建好的篩選器，作為參數，發送給 ZoomInfo API。由於搜索結果，可能包含成千上萬條記錄，ZoomInfo API 會進行「分頁 (Pagination)」。Manus 在後台，會自動處理這種分頁機制，根據需要，拉取多頁數據，直到滿足使用者的請求（例如，「給我前 20 個結果」）或達到預設的上限。這個過程，對使用者是透明的。

5.  **數據處理與整合層 (Data Processing & Integration Layer)**: Manus 獲取到原始的 JSON 數據後，會進行一系列的後處理，以使其更具可用性：
    *   **數據豐富 (Enrichment)**: 在很多情況下，一個單獨的搜索，可能不足以獲取所有需要的資訊。例如，在 `search_companies` 之後，您可能還需要找到這些公司裡的特定決策者。Manus 可以自動鏈接下一步操作，例如，將第一步獲得的公司 ID，作為第二步 `search_contacts` 的篩選條件。
    *   **格式化與呈現 (Formatting & Presentation)**: 將數據，整理成易於閱讀的表格或卡片，在 Slack 或 Teams 中呈現。
    *   **CRM 整合 (CRM Integration)**: 將獲取的數據，通過調用 Salesforce 或 HubSpot 的 MCP 工具，自動創建或更新對應的 Account 和 Contact 記錄。

這個架構的關鍵，在於**將複雜的、多條件的布林搜索邏輯，抽象化為簡單的自然語言對話，並將一次性的數據拉取，升級為可持續運行的、與核心業務系統深度整合的自動化流程**。

## 2. 核心工具使用詳解 (Core Tools Deep Dive)

以下表格，詳細說明了 Manus for ZoomInfo 的核心工具，及其在市場開發中的具體應用。

| 工具名稱 (Tool Name) | 核心用途 (Core Purpose) | 關鍵輸入參數 (Key Input Parameters) | 輸出結果 (Output) | 使用範例 (Example Scenario) |
| :--- | :--- | :--- | :--- | :--- |
| `search_companies` | **搜索公司** | `filters`: 一個包含多個篩選條件的 JSON 物件，如 `{"industry": ["Manufacturing"], "revenue_min": 50000000}` | 一個公司列表，每家公司包含公司 ID、名稱、網站、地址、員工人數、年收入等欄位。 | 尋找所有符合理想客戶畫像 (ICP) 的目標公司。 |
| `search_contacts` | **搜索聯絡人** | `filters`: 一個包含多個篩選條件的 JSON 物件，如 `{"job_title": ["Chief Technology Officer"], "company_id": [12345]}` | 一個聯絡人列表，每位聯絡人包含姓名、職位、公司、郵箱、電話、LinkedIn URL 等。 | 在一家目標公司中，找到特定的決策者或影響者。 |
| `enrich` | **數據豐富** | `company_data` 或 `contact_data`: 包含公司名稱/域名或聯絡人郵箱/姓名的 JSON 物件。 | 針對輸入的實體，返回其在 ZoomInfo 數據庫中，最匹配的、最完整的記錄。 | 清洗和補全從網站表單或線下活動中，獲得的不完整線索。 |
| `search_intent` | **搜索意圖信號** | `topics`: 您感興趣的關鍵字或主題，如 `["Cybersecurity", "Cloud Migration"]`<br>`filters`: 用於縮小公司範圍的篩選器。 | 一個公司列表，這些公司在近期，表現出對指定主題的強烈興趣（例如，大量員工瀏覽相關主題的文章）。 | 在競爭對手之前，找到那些正在積極研究、評估某類解決方案的「熱門」潛在客戶。 |

**關於 `filters` 的說明**：`filters` 是所有搜索工具中，最強大、最核心的參數。它幾乎可以對應 ZoomInfo 網站上的所有篩選條件，包括：
*   **地理位置**: `country`, `state`, `city`, `zip_code`
*   **公司規模**: `employee_count_min`, `employee_count_max`, `revenue_min`, `revenue_max`
*   **行業與技術**: `industry`, `tech_stack` (公司使用的技術)
*   **人員資訊**: `job_title`, `management_level`, `department`
*   **其他**: `funding_min` (融資額), `is_hiring` (是否在招聘) 等。

## 3. 典型工作流 (Typical Workflows)

### 工作流一：建立目標客戶開發列表 (Building a Target Account List)

這是最基礎，也是最核心的工作流。

1.  **使用者**: 「`Manus，幫我建立一個新的潛在客戶列表。我需要尋找所有在美國的、員工人數在 200 到 1000 人之間、屬於「金融服務」行業、並且正在使用 Salesforce 的公司。請給我前 20 個結果。`」
2.  **Manus**: (內部執行)
    *   **構建篩選器**: 
        ```json
        {
          "country": ["United States"],
          "employee_count_min": 200,
          "employee_count_max": 1000,
          "industry": ["Financial Services"],
          "tech_stack": ["Salesforce"]
        }
        ```
    *   **調用工具**: `search_companies(filters=..., limit=20)`
3.  **Manus**: (返回一個格式化的表格，包含 20 家公司的名稱、網站、地址和員工人數)。

### 工作流二：帳戶映射與關鍵決策者發現 (Account Mapping & Key Persona Discovery)

在確定了目標公司後，下一步是找到「對的人」。

1.  **使用者**: (基於上一步的結果) 「`很好。現在，請幫我在列表中的第一家公司「Future Finance Inc.」裡面，找出所有「副總裁 (VP)」級別以上的、在「市場」或「銷售」部門的聯絡人。`」
2.  **Manus**: (內部執行)
    *   **獲取公司 ID**: 首先，從上一步的結果中，獲取「Future Finance Inc.」的 ZoomInfo 公司 ID (例如 `34567`)。
    *   **構建篩選器**: 
        ```json
        {
          "company_id": [34567],
          "management_level": ["VP", "C-Suite"],
          "department": ["Marketing", "Sales"]
        }
        ```
    *   **調用工具**: `search_contacts(filters=...)`
3.  **Manus**: (返回一個包含幾位高管的聯絡人卡片列表，每張卡片上，都有姓名、職位、郵箱和 LinkedIn 連結)。

### 工作流三：基於意圖信號的主動出擊 (Proactive Outreach Based on Intent Signals)

這個高級工作流，展示了如何從「大海撈針」轉變為「精準釣魚」。

1.  **設定 (Setup)**: 一家網路安全公司的銷售總監，設定了一個每週運行的監控任務：「`Manus，請每週一早上，幫我檢查一次 ZoomInfo 的意圖數據。找出所有在過去 7 天內，對「零信任安全 (Zero Trust Security)」表現出強烈意圖信號、但目前不是我們客戶的公司，並將這個列表，發送到我的郵箱。`」
2.  **執行 (Execution)**: Manus 在每週一，自動執行：
    1.  **調用 `search_intent`**: `search_intent(topics=["Zero Trust Security"], filters={"last_7_days": true})`。
    2.  **數據比對**: 將返回的公司列表，與 CRM 中的現有客戶列表，進行比對，篩選出非客戶的公司。
3.  **行動 (Action)**: Manus 調用 Outlook/Gmail MCP，將最終的「高意向潛在客戶」列表，以報告的形式，發送給銷售總監。

這些工作流，清晰地展示了 Manus for ZoomInfo 如何將傳統的、手動的 B2B 市場開發流程，轉變為一個智慧的、自動化的、且極其高效的系統。
