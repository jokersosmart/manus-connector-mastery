> **操作指南**：本手冊，為 Manus for ZoomInfo 的核心工具，提供了詳細的命令參考和參數說明。請將其視為您進行 B2B 市場開發的「儀表板操作手冊」。每一個工具，都對應著一個強大的情報獲取能力，其核心，在於如何精確地構建「篩選器 (filters)」，以從 ZoomInfo 的億級數據實體中，找到您需要的「真金」。

---

## 1. 核心工具集

### 1.1 `search_companies` (搜索公司)

此工具是您的「理想客戶發現器」。它根據您定義的一系列標準，從 ZoomInfo 的全球公司數據庫中，篩選出符合條件的公司列表。

| 參數 | 類型 | 是否必須 | 描述與範例 |
| :--- | :--- | :--- | :--- |
| `filters` | `object` | 是 | 包含所有篩選條件的 JSON 物件，如 `{"city": ["Austin"], "industry": ["Software"]}` |
| `limit` | `integer` | 否 | 返回的公司數量上限，預設 10-20 |

### 1.2 `search_contacts` (搜索聯絡人)

此工具是您的「決策者定位器」，能在特定公司、部門或地區，找到符合職位要求的人員。

| 參數 | 類型 | 是否必須 | 描述與範例 |
| :--- | :--- | :--- | :--- |
| `filters` | `object` | 是 | 包含篩選條件的 JSON 物件，如 `{"company_id": [98765], "management_level": ["Director"]}` |
| `limit` | `integer` | 否 | 返回的聯絡人數量上限 |

### 1.3 `enrich` (數據豐富)

此工具是「數據清洗與補全大師」，能接收不完整的資訊，返回最匹配的完整數據記錄。

| 參數 | 類型 | 是否必須 | 描述與範例 |
| :--- | :--- | :--- | :--- |
| `match_type` | `string` | 是 | 實體類型：`company` 或 `contact` |
| `input_data` | `object` | 是 | 現有資訊物件，如 `{"email": "jane@example.com"}` |

### 1.4 `search_intent` (搜索意圖信號)

此工具是「市場預警雷達」，發現哪些公司正在積極研究與您業務相關的主題。

| 參數 | 類型 | 是否必須 | 描述與範例 |
| :--- | :--- | :--- | :--- |
| `topics` | `array` | 是 | 意圖主題列表，如 `["AI", "Cybersecurity"]` |
| `filters` | `object` | 否 | 標準公司篩選器，縮小搜索範圍 |

---

## 2. 常用篩選器 (Filters) 參考

| 篩選器名稱 | 描述 | 範例 |
| :--- | :--- | :--- |
| `country`, `state`, `city` | 地理位置 | `"country": ["Canada"]` |
| `industry` | 公司所屬行業 | `"industry": ["Biotechnology"]` |
| `employee_count_min/max` | 員工人數範圍 | `"employee_count_min": 50` |
| `revenue_min/max` | 公司年收入範圍 | `"revenue_max": 10000000` |
| `tech_stack` | 公司使用的技術產品 | `"tech_stack": ["HubSpot"]` |
| `job_title` | 聯絡人職位頭銜 | `"job_title": ["CTO"]` |
| `management_level` | 聯絡人管理級別 | `"management_level": ["VP"]` |
| `department` | 聯絡人所屬部門 | `"department": ["Sales"]` |
| `company_id` | 限定特定公司 ID | `"company_id": [123]` |
| `is_hiring` | 公司是否在招聘 | `"is_hiring": true` |
