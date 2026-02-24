# OpenRouter 連結器使用說明報告

**作者：Joker**

---

## 1. 核心概念：OpenRouter 作為模型閘道

與傳統的 MCP 連結器不同，OpenRouter 本身不提供具體的、有固定功能的「工具」（例如 `notion.create_page` 或 `google.search`）。相反地，OpenRouter 是一個強大的 **大型語言模型（LLM）聚合器與路由器**。

您可以將其理解為一個統一的「模型閘道」。當您在 Manus 中下達一個需要大型語言模型來理解、生成或回答的指令時，Manus 可以透過 OpenRouter 連結器，將您的請求發送給市面上幾乎所有主流的 AI 模型，包括來自 OpenAI、Google、Anthropic、Meta、Mistral 等公司的模型。

**這意味著 OpenRouter 的「工具」就是「選擇和使用任何一個頂尖 AI 模型的能力」。**

其主要優勢包括：

*   **模型選擇的極大豐富**：您可以指定使用數百種模型中的任何一個，不再局限於單一供應商。
*   **統一的介面**：無論背後是哪個模型，您都使用同樣的自然語言與 Manus 互動。
*   **成本與效能最佳化**：可以根據任務需求，選擇最經濟實惠或最高效能的模型。

## 2. 主要「工具」：模型調用 (Model Invocation)

OpenRouter 的核心功能可以被抽象為一個名為 `invoke_model` 的虛擬工具。這個工具負責接收您的指令，並將其路由到指定的語言模型。雖然您不會直接輸入 `invoke_model`，但您下達的每一個自然語言指令，背後都是對這個能力的調用。

### 2.1. 可用參數 (Parameters)

當您使用 Manus 並透過 OpenRouter 調用模型時，您可以直接或間接地影響以下參數：

| 參數名稱 | 資料類型 | 說明 | 如何影響 |
| :--- | :--- | :--- | :--- |
| `model` | `string` | **必要參數**。指定要使用哪一個 AI 模型。這是 OpenRouter 最核心的參數。模型名稱是一個唯一的字串識別碼，例如 `google/gemini-flash-1.5` 或 `openai/gpt-4o`。 | 透過在指令中明確指定模型名稱。 |
| `prompt` | `string` | **必要參數**。您輸入的自然語言指令、問題或要求。 | 您在 Manus 中輸入的完整對話內容。 |
| `temperature` | `float` | 控制模型輸出的「創意程度」或「隨機性」。數值越高（例如 1.0），回答越隨機、越有創意；數值越低（例如 0.1），回答越確定、越保守。預設值通常在 0.7 左右。 | 可以在指令中用自然語言描述，例如「請用更有創意的方式回答」。 Manus 會將其轉換為對應的 `temperature` 參數。 |
| `max_tokens` | `integer` | 限制模型單次生成內容的最大長度（以 token 為單位）。有助於控制成本和避免過長的無意義輸出。 | 可以在指令中設定限制，例如「回答請不要超過 200 字」。 |
| `transforms` | `array` | 一個 OpenRouter 特有的強大參數，允許您在請求中加入額外的轉換層，例如 `"omega-mistral-instruct"` 可以讓一些基礎模型表現得更像指示追蹤模型。 | 這屬於進階功能，通常由 Manus 根據您的任務類型自動選擇最佳轉換。 |
| `route` | `string` | 決定模型的路由策略，可以是 `"fallback"`（如果首選模型失敗，自動嘗試下一個）或 `"any"`（從一組模型中選擇任何一個可用的）。 | 這主要由 Manus 系統在後台管理，以確保服務的穩定性。 |

### 2.2. 使用範例與預期輸出

以下範例展示了您的自然語言指令如何被 Manus 解讀，並轉換為對 OpenRouter 的 API 調用。

#### 範例 1：指定特定模型撰寫郵件

*   **您的指令**：
    > 「幫我用 **Claude 3.5 Sonnet** 模型，草擬一封專業的商業郵件，內容是向客戶說明我們的專案進度延遲了，並提出補救方案。」

*   **Manus 的轉換 (示意)**：
    ```json
    {
      "tool_name": "invoke_model",
      "server": "openrouter",
      "parameters": {
        "model": "anthropic/claude-3.5-sonnet",
        "prompt": "草擬一封專業的商業郵件，內容是向客戶說明我們的專案進度延遲了，並提出補救方案。",
        "temperature": 0.5
      }
    }
    ```

*   **預期輸出**：
    由 Claude 3.5 Sonnet 模型生成的一封符合要求的、語氣專業的商業郵件草稿。

#### 範例 2：使用高效能模型進行程式碼除錯

*   **您的指令**：
    > 「這段 Python 程式碼有 bug，請用最強的程式碼模型幫我看看問題在哪裡。我懷疑是跟非同步處理有關。這是程式碼：`[貼上您的程式碼]`」

*   **Manus 的轉換 (示意)**：
    Manus 會根據「最強的程式碼模型」這個描述，從 OpenRouter 的模型清單中，選擇一個在程式碼能力上評分最高的模型，例如 `openai/gpt-4o`。
    ```json
    {
      "tool_name": "invoke_model",
      "server": "openrouter",
      "parameters": {
        "model": "openai/gpt-4o",
        "prompt": "這段 Python 程式碼有 bug，請幫我看看問題在哪裡。我懷疑是跟非同步處理有關。這是程式碼：...",
        "temperature": 0.1
      }
    }
    ```

*   **預期輸出**：
    由 GPT-4o 模型提供的、針對您程式碼的詳細分析、錯誤定位以及建議的修正方案。

#### 範例 3：要求創意寫作並調整隨機性

*   **您的指令**：
    > 「我想寫一個關於太空探險的短篇故事開頭，請用 **天馬行空、更有創意** 的風格，給我三個不同的版本。使用 **Mistral Large** 模型。」

*   **Manus 的轉換 (示意)**：
    Manus 會解讀「天馬行空、更有創意」為需要較高的 `temperature` 值。
    ```json
    {
      "tool_name": "invoke_model",
      "server": "openrouter",
      "parameters": {
        "model": "mistralai/mistral-large-latest",
        "prompt": "寫一個關於太空探險的短篇故事開頭，給我三個不同的版本。",
        "temperature": 1.2,
        "n": 3
      }
    }
    ```

*   **預期輸出**：
    由 Mistral Large 模型生成的三個風格各異、充滿想像力的故事開頭。

## 3. 可用模型列表

OpenRouter 支援的模型列表是動態更新的，涵蓋了數百種模型。由於列表過長且變動頻繁，在此無法完整列出。您可以在 Manus 中透過以下指令查詢當前熱門或可用的模型：

> 「查詢 OpenRouter 上目前最受歡迎的 10 個模型是什麼？」

> 「我想找一個適合寫作的模型，OpenRouter 上有什麼推薦的？」

Manus 將會查詢 OpenRouter 的 API，並回傳最新的模型資訊給您參考。

---

**結論**：OpenRouter 連結器的強大之處在於其賦予的「無限可能性」。透過簡單的自然語言，您就能駕馭全球頂尖的 AI 模型，讓 Manus 成為您真正的、可隨意客製化的智慧助理。
