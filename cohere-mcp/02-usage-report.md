# 「Cohere」連結器使用說明報告

**作者：** Joker

---

## 1. 總覽

本報告旨在詳細說明 Manus MCP 平台上的「Cohere」連結器所提供的各項工具。Cohere 連結器將 Cohere 平台先進的語言模型能力無縫整合至 Manus Agent，讓使用者能透過自然語言指令，執行複雜的文本生成、理解與分析任務。

由於無法直接查詢到連結器的具體工具清單，本報告將基於 Cohere 官方 API 的核心功能，推斷並模擬連結器可能提供的工具集。我們假設連結器將 Cohere 的主要端點（如 Generate, Embed, Rerank, Chat, Summarize）封裝為 Manus 中的可用工具。下文將對每一個推斷的工具進行詳細的功能、參數、使用範例及預期輸出說明。

**參考資料：**

- [Cohere API Documentation](https://docs.cohere.com/)

## 2. 可用工具詳解

### 2.1. `cohere.generate` - 文本生成

**功能說明：**

此工具根據使用者提供的提示詞 (prompt) 來生成文本。它適用於多種創意與寫作任務，例如撰寫文章草稿、產生產品描述、編寫程式碼或回答問題。此工具對應 Cohere API 的 `Generate` 端點，提供強大的單次文本生成能力。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `prompt` | string | 是 | 用於指導模型生成文本的輸入提示詞。 |
| `model` | string | 否 | 指定使用的模型 ID，預設為 `command-r`。可選其他模型如 `command-light`。 |
| `max_tokens` | integer | 否 | 設定生成文本的最大長度 (token 數量)。預設值較低，建議根據需求調整。 |
| `temperature` | float | 否 | 控制生成結果的隨機性。數值越高 (如 0.8)，結果越具創意；數值越低 (如 0.2)，結果越具確定性。預設為 0.75。 |
| `k` | integer | 否 | 從機率最高的 K 個詞中進行抽樣，有助於減少低機率詞的出現。 |
| `p` | float | 否 | 核心抽樣 (Nucleus sampling)，從累積機率超過 p 的詞彙中抽樣。 |
| `stop_sequences` | array | 否 | 一個字串陣列，當模型生成其中任何一個字串時，將停止生成。 |

**使用範例：**

```bash
manus-mcp-cli tool call cohere.generate --server cohere --input '{
  "prompt": "撰寫一首關於星際旅行的五言絕句",
  "model": "command-r",
  "max_tokens": 50,
  "temperature": 0.8
}'
```

**預期輸出：**

模型將返回一個 JSON 物件，其中包含生成的文本內容。

```json
{
  "id": "gen-a1b2c3d4",
  "text": "銀河渡遠舟，\n暗夜探星流。\n光年一瞬過，\n宇宙任我遊。",
  "finish_reason": "COMPLETE"
}
```

### 2.2. `cohere.chat` - 對話

**功能說明：**

此工具用於進行多輪對話。與 `generate` 不同，`chat` 能夠記憶對話的上下文，讓使用者可以進行追問、澄清或逐步完善指令。它非常適合用於聊天機器人、虛擬助理、互動式問題解答等場景。此工具對應 Cohere API 的 `Chat` 端點。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `message` | string | 是 | 使用者在當前回合輸入的訊息。 |
| `chat_history` | array | 否 | 一個包含過去對話紀錄的物件陣列，每個物件包含 `role` (`USER` 或 `CHATBOT`) 和 `message`。 |
| `model` | string | 否 | 指定使用的模型 ID，預設為 `command-r`。 |
| `temperature` | float | 否 | 控制生成結果的隨機性，數值範圍 0.0 至 1.0。 |
| `connectors` | array | 否 | 整合外部資料來源 (如企業知識庫) 進行 RAG (檢索增強生成)，以提供更精確、有來源依據的回答。 |

**使用範例：**

```bash
manus-mcp-cli tool call cohere.chat --server cohere --input '{
  "message": "那如果改成七言律詩呢？",
  "chat_history": [
    {"role": "USER", "message": "撰寫一首關於星際旅行的五言絕句"},
    {"role": "CHATBOT", "message": "銀河渡遠舟，\n暗夜探星流。\n光年一瞬過，\n宇宙任我遊。"}
  ]
}'
```

**預期輸出：**

模型將返回包含最新回覆及完整對話歷史的 JSON 物件。

```json
{
  "text": "浩瀚銀河萬里舟，星辰為伴任遨遊。\n光年一躍三千界，時空扭曲四維愁。\n黑洞引力吞噬夢，白矮餘暉映眼眸。\n未知彼岸何處是，歸途已忘在星流。",
  "generation_id": "chat-e5f6g7h8",
  "chat_history": [...],
  "finish_reason": "COMPLETE"
}
```
### 2.3. `cohere.embed` - 文本嵌入

**功能說明：**

此工具將文本轉換為高維度的浮點數向量 (embeddings)。這些向量能夠捕捉文本的語義資訊，是實現語義搜尋、文本分類、聚類分析和推薦系統等應用的基礎。此工具對應 Cohere API 的 `Embed` 端點。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `texts` | array | 是 | 一個包含多個待處理文本字串的陣列。 |
| `model` | string | 否 | 指定使用的嵌入模型 ID，如 `embed-english-v3.0` 或 `embed-multilingual-v3.0`。 |
| `input_type` | string | 否 | 指定輸入文本的類型，以優化嵌入品質。可選值包括 `search_document`, `search_query`, `classification`, `clustering`。 |
| `truncate` | string | 否 | 當輸入文本超過模型長度限制時的處理方式。`NONE` (預設) 會報錯，`START` 或 `END` 會從頭部或尾部截斷。 |

**使用範例：**

```bash
manus-mcp-cli tool call cohere.embed --server cohere --input 
'{
  "texts": ["今天天氣真好", "適合出門散步"],
  "model": "embed-multilingual-v3.0",
  "input_type": "search_document"
}'
```

**預期輸出：**

模型將返回一個包含每個輸入文本對應的嵌入向量的 JSON 物件。

```json
{
  "id": "embed-x1y2z3",
  "embeddings": [
    [0.123, -0.456, ..., 0.789],
    [0.987, -0.654, ..., 0.321]
  ],
  "meta": {
    "api_version": {
      "version": "1"
    }
  }
}
```

### 2.4. `cohere.rerank` - 結果重排

**功能說明：**

此工具用於優化搜尋結果的排序。傳統的關鍵字搜尋可能無法準確理解查詢意圖，`rerank` 工具利用語義理解能力，對一組文件根據其與特定查詢的相關性進行重新排序，將最相關的結果排在最前面。此工具對應 Cohere API 的 `Rerank` 端點。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `query` | string | 是 | 用戶的搜尋查詢。 |
| `documents` | array | 是 | 一個文件陣列 (可以是字串或物件)，代表需要重新排序的搜尋結果。 |
| `model` | string | 否 | 指定使用的重排模型 ID，預設為 `rerank-english-v2.0` 或 `rerank-multilingual-v2.0`。 |
| `top_n` | integer | 否 | 返回排序後前 N 個最相關的文件。如果未指定，則返回所有文件。 |

**使用範例：**

```bash
manus-mcp-cli tool call cohere.rerank --server cohere --input 
'{
  "query": "全球最大的經濟體是哪個國家？",
  "documents": [
    "日本是世界第三大經濟體。",
    "美國擁有全球最大的國內生產總值。",
    "中國近年來經濟增長迅速。"
  ],
  "top_n": 2
}'
```

**預期輸出：**

模型將返回一個按相關性分數從高到低排序的文件列表。

```json
{
  "id": "rerank-p9o8i7",
  "results": [
    { "index": 1, "relevance_score": 0.98 },
    { "index": 2, "relevance_score": 0.75 }
  ]
}
```

### 2.5. `cohere.summarize` - 文本摘要

**功能說明：**

此工具用於將長篇文本精煉成簡潔的摘要。使用者可以控制摘要的長度與格式，快速獲取文章、報告或對話的核心內容。此工具對應 Cohere API 的 `Summarize` 端點。

**參數說明：**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `text` | string | 是 | 需要進行摘要的原始文本。 |
| `model` | string | 否 | 指定使用的模型 ID。 |
| `length` | string | 否 | 控制摘要的長度。可選值為 `short`, `medium`, `long`。預設為 `medium`。 |
| `format` | string | 否 | 控制摘要的格式。`paragraph` (段落) 或 `bullets` (項目符號)。預設為 `paragraph`。 |
| `temperature` | float | 否 | 控制摘要的創意程度，數值越高越有創意。 |

**使用範例：**

```bash
manus-mcp-cli tool call cohere.summarize --server cohere --input 
'{
  "text": "(此處省略一段長達 1000 字的關於太陽系的文章)...",
  "length": "short",
  "format": "bullets"
}'
```

**預期輸出：**

模型將返回包含摘要文本的 JSON 物件。

```json
{
  "id": "summarize-k6j5h4",
  "summary": "- 太陽系由太陽及其引力束縛的八大行星組成。\n- 內太陽系包含四顆類地行星：水星、金星、地球和火星。\n- 外太陽系包含四顆巨行星：木星、土星、天王星和海王星。"
}
```
