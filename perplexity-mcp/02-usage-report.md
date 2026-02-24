> **免責聲明：** 由於目前 Manus 環境中未配置 Perplexity MCP 伺服器，本文件將基於公開的 Perplexity 功能，設計一套合理且具代表性的虛構工具集 (`joker.perplexity`)，以作為未來建構真實連接器時的藍圖與參考。所有工具名稱、參數和範例均為設想，旨在展示其核心價值與用法。

# Perplexity MCP 連結器使用說明報告

**作者：Joker**

## 總覽

Perplexity 連結器 (`joker.perplexity`) 旨在將 Perplexity 強大的 AI 答案引擎無縫整合至 Manus 的自動化工作流中。透過此連結器，使用者可以用自然語言提出問題，並獲得附有清晰引用來源的精確、即時的答案。本報告將詳細介紹此連結器提供的所有工具、參數及使用範例。

目前，`joker.perplexity` 連結器提供以下核心工具：

| 工具名稱 | 功能描述 |
| :--- | :--- |
| `simple_search` | 執行一次標準的 Perplexity 搜尋，快速獲取問題的直接答案。 |
| `pro_search` | 執行一次專業級的深度搜尋，提供更全面、更深入的分析與洞察。 |

---

## 1. 工具：`simple_search`

此工具是與 Perplexity 互動最基礎也最常用的方法，適用於需要快速、準確回答特定問題的場景。

### 1.1 功能說明

`simple_search` 會接收一個自然語言問題，並回傳一個由 AI 生成、整合了多個來源的摘要性答案，同時附上所有參考資料的引用連結，以便使用者進行事實查核。

### 1.2 參數詳解

| 參數名稱 | 型別 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `query` | `string` | 是 | 您想要詢問的具體問題。問題應盡可能清晰、明確。 |
| `focus` | `string` | 否 | 將搜尋範圍限定在特定領域，以提高答案的相關性與專業性。若不指定，則進行全網搜尋。 |

**`focus` 參數可用選項：**

- `academic`: 專注於學術論文、期刊和研究報告。
- `writing`: 專注於寫作輔助，提供文法、風格建議或進行文本生成。
- `wolfram_alpha`: 專注於計算、數學和科學數據查詢。
- `youtube`: 專注於從 YouTube 影片的字幕和內容中尋找答案。
- `reddit`: 專注於從 Reddit 的討論串中獲取多元觀點和真實使用者經驗。

### 1.3 使用範例

**場景：** 一位市場分析師想要快速了解「客戶關係管理 (CRM)」的市場趨勢。

**自然語言指令：**

> 「用 Perplexity 幫我查一下 2026 年 CRM 市場的主要趨勢有哪些？」

**對應的工具調用 (Tool Call)：**

```json
{
  "tool_name": "joker.perplexity.simple_search",
  "parameters": {
    "query": "What are the main trends in the CRM market for 2026?"
  }
}
```

**場景：** 一位學生正在撰寫關於「量子計算」的論文，需要查找相關的學術資料。

**自然語言指令：**

> 「我想在學術領域裡，用 Perplexity 搜尋“量子霸權”的最新研究進展。」

**對應的工具調用 (Tool Call)：**

```json
{
  "tool_name": "joker.perplexity.simple_search",
  "parameters": {
    "query": "latest research progress on quantum supremacy",
    "focus": "academic"
  }
}
```

### 1.4 預期輸出

工具執行成功後，將回傳一個包含以下結構的 JSON 物件：

- `summary`: 一段文字，為對 `query` 的摘要性回答。
- `sources`: 一個陣列，包含所有被引用的資訊來源，每個來源包含標題、URL 和其他元數據。

```json
{
  "summary": "在2026年，CRM市場的主要趨勢預計將圍繞著超個人化、AI驅動的自動化、以及客戶數據平台的整合... [1]。特別是，生成式AI將在客戶服務和銷售預測中扮演更重要的角色... [2]。",
  "sources": [
    {
      "title": "CRM Market Trends 2026 - Gartner",
      "url": "https://www.gartner.com/..."
    },
    {
      "title": "The Future of CRM: AI and Automation - Forrester",
      "url": "https://www.forrester.com/..."
    }
  ]
}
```

---

## 2. 工具：`pro_search`

此工具專為需要進行深度研究、探索複雜主題或需要更全面答案的使用者設計。

### 2.1 功能說明

`pro_search` 會啟用 Perplexity 的專業搜尋模式。它會更深入地分析問題，提出引導性的追問建議，並整合更廣泛的來源，產出結構更完整、內容更豐富的報告式答案。此功能對應於 Perplexity Pro 的部分進階功能。

### 2.2 參數詳解

| 參數名稱 | 型別 | 是否必須 | 描述 |
| :--- | :--- | :--- | :--- |
| `query` | `string` | 是 | 您想要深入研究的核心主題或問題。 |
| `max_sources` | `integer` | 否 | 希望模型在生成答案時最多參考的來源數量。預設為 10。 |
| `generate_follow_up_questions` | `boolean` | 否 | 是否需要 AI 根據初步答案，自動生成建議的追問問題，以引導研究方向。預設為 `true`。 |

### 2.3 使用範例

**場景：** 一位策略顧問正在為客戶研究「去中心化金融 (DeFi) 的監管挑戰」，需要一份詳盡的報告。

**自然語言指令：**

> 「請使用 Perplexity 的專業模式，為我深入研究“DeFi 的全球監管挑戰”，並提供後續的提問建議。」

**對應的工具調用 (Tool Call)：**

```json
{
  "tool_name": "joker.perplexity.pro_search",
  "parameters": {
    "query": "Global regulatory challenges of Decentralized Finance (DeFi)",
    "generate_follow_up_questions": true
  }
}
```

### 2.4 預期輸出

`pro_search` 的輸出結構比 `simple_search` 更豐富，包含深度報告和追問建議。

```json
{
  "report": {
    "title": "全球去中心化金融 (DeFi) 的監管挑戰深度分析",
    "introduction": "去中心化金融 (DeFi) 的快速發展對全球金融監管機構提出了前所未有的挑戰... [1]。",
    "sections": [
      {
        "title": "反洗錢 (AML) 與認識你的客戶 (KYC) 的困境",
        "content": "DeFi 的匿名性使得傳統的 AML/KYC 框架難以實施... [2][3]。"
      },
      {
        "title": "投資者保護與市場穩定風險",
        "content": "智能合約的漏洞、駭客攻擊以及市場操縱是當前 DeFi 生態系統面臨的主要風險... [4]。"
      }
    ],
    "conclusion": "總體而言，DeFi 的監管需要一種全新的、適應性更強的國際合作框架..."
  },
  "sources": [
    {...}, {...}, {...}, {...}
  ],
  "follow_up_questions": [
    "不同國家 (如美國、歐盟、新加坡) 的 DeFi 監管政策有何差異？",
    "有哪些新興的監管科技 (RegTech) 可以應用於 DeFi 領域？",
    "如何平衡 DeFi 的創新潛力與金融穩定之間的關係？"
  ]
}
```
