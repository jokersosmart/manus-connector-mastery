> **操作指南**：本手冊，提供了 Manus for Explorium 核心工具的詳細操作指令和參數說明。請將本手冊，視為您與 Explorium 互動的「命令參考大全」。每個工具的設計，都旨在將一個複雜的數據科學任務，簡化為一個清晰、具體的指令。請仔細閱讀每個參數的涵義，以確保您能精準地，向 Manus 表達您的數據需求。

---

## 1. 工具一：`enrich_entity` (實體數據擴充)

此工具，是您探索外部世界的「望遠鏡」。它能針對一個具體的公司或個人，即時從 Explorium 龐大的數據信號庫中，抓取並整合豐富的資訊。

### 1.1 用途

*   在銷售會議前，快速了解潛在客戶的公司背景。
*   在市場活動中，豐富用戶畫像，以便進行更精準的客戶分群。
*   在風險評估中，調查一個公司的關鍵營運數據。

### 1.2 命令結構

您需要透過 Manus，調用名為 `enrich_entity` 的工具，並提供一個 JSON 格式的輸入。

```json
{
  "entity_type": "company | person",
  "entity_data": {
    "primary_identifier": "value",
    "secondary_identifier": "value",
    ...
  }
}
```

### 1.3 參數詳解

| 參數 | 類型 | 是否必須 | 描述與範例 |
| :--- | :--- | :--- | :--- |
| `entity_type` | `string` | 是 | 指定您要擴充的實體類型。必須是 `company` (公司) 或 `person` (個人) 其中之一。 |
| `entity_data` | `object` | 是 | 一個包含實體標識符的物件。您提供的標識符越豐富，Explorium 匹配的準確度就越高。 |

**`entity_data` 的可用標識符：**

*   **當 `entity_type` 為 `company` 時：**
    *   `name`: 公司全名 (e.g., `"Microsoft Corp"`)。
    *   `domain`: 公司網站域名 (e.g., `"microsoft.com"`)。**強烈建議提供**。
    *   `location`: 公司地址或所在城市 (e.g., `"Redmond, WA"`)。
*   **當 `entity_type` 為 `person` 時：**
    *   `email`: 個人郵箱 (e.g., `"bill.gates@microsoft.com"`)。**強烈建議提供**。
    *   `name`: 個人姓名 (e.g., `"Bill Gates"`)。
    *   `phone`: 電話號碼。

### 1.4 操作範例

**範例 1：擴充一個公司的資料**

*   **您的指令**：「`Manus, can you enrich the company with the domain 'google.com'?`」
*   **Manus 內部執行的 JSON 輸入**：
    ```json
    {
      "entity_type": "company",
      "entity_data": {
        "domain": "google.com"
      }
    }
    ```

**範例 2：擴充一個商業聯繫人的資料**

*   **您的指令**：「`請幫我查找關於郵箱為 `satya.nadella@microsoft.com` 的聯繫人的公開商業資訊。`」
*   **Manus 內部執行的 JSON 輸入**：
    ```json
    {
      "entity_type": "person",
      "entity_data": {
        "email": "satya.nadella@microsoft.com"
      }
    }
    ```

---

## 2. 工具二：`train_model` (訓練預測模型)

此工具，是您的「自動化數據科學家」。它能接收您的原始數據，結合 Explorium 的外部數據，自動化地，完成特徵工程和模型訓練。

### 2.1 用途

*   預測哪些客戶，最有可能流失。
*   預測哪些潛在客戶，最有可能轉化為付費客戶。
*   預測哪些用戶，最有可能從免費版升級到付費版。

### 2.2 命令結構

```json
{
  "data_source": "path/to/your/file.csv",
  "target_variable": "column_name_to_predict",
  "model_name": "Your-Descriptive-Model-Name"
}
```

### 2.3 參數詳解

| 參數 | 類型 | 是否必須 | 描述與範例 |
| :--- | :--- | :--- | :--- |
| `data_source` | `string` | 是 | 指向您訓練數據檔案的路徑。**必須是 CSV 格式**。檔案中，應包含您希望預測的目標變數欄位，以及用於預測的基礎特徵（如客戶 ID、公司名稱等）。 |
| `target_variable` | `string` | 是 | 您希望模型預測的目標欄位的**確切名稱**。例如，`HasChurned`、`IsConverted`。該欄位的值，應該是二元的（如 1/0, true/false）或數值的。 |
| `model_name` | `string` | 是 | 為您訓練的模型，指定一個有意義的、易於識別的名稱。例如 `Q3-Customer-Churn-Predictor`。 |

### 2.4 操作範例

1.  **準備數據**：您有一個名為 `customer_data.csv` 的檔案，內容如下：
    ```csv
    CustomerID,CompanyName,LastLoginDays,HasChurned
    1,Innovate Inc.,10,0
    2,Cyberdyne Systems,95,1
    3,Blue Sun Corp.,5,0
    ...
    ```
2.  **您的指令**：「`Manus，請使用檔案 `customer_data.csv`，幫我建立一個預測客戶流失的模型。目標是 `HasChurned` 欄位，模型請命名為 `ChurnModel-V1`。`」
3.  **Manus 內部執行的 JSON 輸入**：
    ```json
    {
      "data_source": "/path/to/customer_data.csv",
      "target_variable": "HasChurned",
      "model_name": "ChurnModel-V1"
    }
    ```
4.  **結果**：Manus 會啟動一個非同步的訓練任務，並在完成後，返回一個 `model_id` 給您，例如 `model-a1b2c3d4`。

---

## 3. 工具三：`get_predictions` (獲取模型預測)

此工具，是您的「水晶球」。它使用一個已經訓練好的模型，來對全新的數據，進行預測。

### 3.1 用途

*   對新註冊的用戶列表，進行批量評分，以識別高潛力用戶。
*   在 CRM 系統中，對新創建的銷售線索，進行即時的轉化可能性評分。

### 3.2 命令結構

```json
{
  "model_id": "model-a1b2c3d4",
  "data_to_predict": "path/to/new_data.csv"
}
```

### 3.3 參數詳解

| 參數 | 類型 | 是否必須 | 描述與範例 |
| :--- | :--- | :--- | :--- |
| `model_id` | `string` | 是 | 您在 `train_model` 步驟中，獲得的那個獨一無二的模型 ID。 |
| `data_to_predict` | `string` | 是 | 指向您需要進行預測的新數據檔案的路徑。**必須是 CSV 格式**。該檔案的結構，應與訓練模型時使用的檔案類似，但**不應包含**目標變數欄位。 |

### 3.4 操作範例

1.  **準備數據**：您有一個名為 `new_leads.csv` 的檔案，內容如下：
    ```csv
    LeadID,CompanyName,Website
    101,Weyland-Yutani Corp.,weyland-yutani.com
    102,Tyrell Corporation,tyrell-corp.com
    ...
    ```
2.  **您的指令**：「`Manus，請使用模型 `model-a1b2c3d4`，對檔案 `new_leads.csv` 中的線索，進行預測。`」
3.  **Manus 內部執行的 JSON 輸入**：
    ```json
    {
      "model_id": "model-a1b2c3d4",
      "data_to_predict": "/path/to/new_leads.csv"
    }
    ```
4.  **結果**：Manus 會返回一個檔案或一個表格，其中包含了 `new_leads.csv` 的所有原始數據，並為每一行，附加了一個新的「預測分數」欄位，以及可能的解釋性資訊。
