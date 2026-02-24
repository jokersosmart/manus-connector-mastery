# Perplexity 終端使用者操作手冊

**作者：Joker**

## 前言：您的自然語言就是最強大的指令

歡迎使用 Perplexity 連接器！本手冊將向您展示如何透過簡單、直觀的自然語言指令，釋放 Perplexity 強大的 AI 研究能力。您不需要學習任何複雜的程式碼或語法，您只需要像平常說話一樣，告訴 Manus 您想知道什麼、想完成什麼任務。

本手冊以「任務」為導向。針對每一個常見任務，我們都會提供：

-   **您說的話：** 您可以對 Manus 下達的自然語言指令範例。
-   **Manus 的理解：** 解釋 Manus 如何將您的話語「翻譯」成精確的工具調用 (Tool Call)。

理解這個「翻譯」過程，將幫助您更有效地與 Manus 及 Perplexity 協作。

---

### **任務 1：我想要快速了解一個新概念**

當您遇到一個陌生的術語、理論或歷史事件時，您需要一個快速、準確的定義和解釋。

#### **您說的話：**

> 「嘿 Manus，用 Perplexity 幫我解釋一下什麼是『區塊鏈的不可能三角』？」

或者更簡單地：

> 「Perplexity，『墨菲定律』是什麼意思？」

#### **Manus 的理解：**

當 Manus 聽到您想要「解釋」、「了解」或「是什麼」時，它會判斷這是一個需要快速、直接答案的任務。因此，它會選擇 `joker.perplexity.simple_search` 工具，並將您的問題核心內容作為 `query` 參數。

```json
// Manus 聽到的指令: "Perplexity，『墨菲定律』是什麼意思？"
{
  "tool_name": "joker.perplexity.simple_search",
  "parameters": {
    "query": "What is Murphy's Law?"
  }
}
```

**💡 提示：** 對於這類問題，您不需要提供太多上下文，直接問核心問題即可。

---

### **任務 2：我需要針對特定領域查找資料**

當您的研究需要限定在特定範圍時，例如學術界、新聞報導或特定的網站，您可以明確地告訴 Manus。

#### **您說的話：**

> 「我想在**學術論文**裡，用 Perplexity 找一下關於『CRISPR 基因編輯技術的倫理爭議』的資料。」

> 「幫我看看 **YouTube** 上，有沒有關於『用 Notion 打造個人儀表板』的教學影片。」

#### **Manus 的理解：**

Manus 會識別出您指令中的「領域限制詞」（如「學術論文」、「YouTube」）。它會將這些詞語映射到 `simple_search` 工具的 `focus` 參數，從而將搜尋範圍縮小到您指定的領域。

```json
// Manus 聽到的指令: "...在學術論文裡...CRISPR...倫理爭議..."
{
  "tool_name": "joker.perplexity.simple_search",
  "parameters": {
    "query": "Ethical controversies of CRISPR gene editing technology",
    "focus": "academic" // <--- 關鍵參數
  }
}
```

**💡 提示：** 嘗試使用 `學術`、`寫作`、`計算`、`YouTube`、`Reddit` 等關鍵詞來觸發 `focus` 功能。

---

### **任務 3：我需要對一個複雜主題進行深度研究**

當您面對的是一個開放式、複雜的議題，需要一份全面、結構化的報告，而不僅僅是幾句話的答案時，您可以使用「深度研究」或「專業模式」等詞語來觸發進階功能。

#### **您說的話：**

> 「Manus，請使用 Perplexity 的**專業模式**，為我**深度研究**一下『全球供應鏈在後疫情時代的重組趨勢』。」

> 「幫我做一份關於『冥想對大腦神經可塑性影響』的**詳細報告**，並告訴我下一步可以研究什麼。」

#### **Manus 的理解：**

關鍵詞「專業模式」、「深度研究」、「詳細報告」會讓 Manus 判斷 `simple_search` 已不足以滿足您的需求。它會轉而調用更強大的 `joker.perplexity.pro_search` 工具。如果您還提到了「下一步研究什麼」，Manus 會確保 `generate_follow_up_questions` 參數被設置為 `true`。

```json
// Manus 聽到的指令: "...深度研究...全球供應鏈...重組趨勢..."
{
  "tool_name": "joker.perplexity.pro_search",
  "parameters": {
    "query": "Restructuring trends of the global supply chain in the post-pandemic era",
    "generate_follow_up_questions": true // 如果您問了下一步
  }
}
```

**💡 提示：** 當您的問題沒有簡單答案，或者您希望得到一個研究的起點時，請大膽使用「深度研究」這個指令。

---

### **任務 4：我想要比較不同事物的優缺點**

比較是做決策的基礎。您可以直接要求 Perplexity 為您進行比較分析。

#### **您說的話：**

> 「用 Perplexity 幫我比較一下 `React` 和 `Vue` 這兩個前端框架的優缺點。」

> 「我想知道投資 `股票` 和 `債券` 的風險與回報對比。」

#### **Manus 的理解：**

「比較」、「優缺點」、「對比」這類詞語會被 Manus 理解為一個結構化的查詢。它可能會使用 `simple_search`，但會精心設計 `query` 參數，以確保 Perplexity 理解比較的意圖。在更進階的實現中，Manus 甚至可能多次調用 Perplexity，分別查詢各個項目，然後自行進行總結比較。

```json
// Manus 聽到的指令: "...比較 React 和 Vue..."
{
  "tool_name": "joker.perplexity.simple_search",
  "parameters": {
    "query": "Comparison of pros and cons: React vs. Vue frontend frameworks"
  }
}
```

**💡 提示：** 在提出比較問題時，請明確指出您想要比較的「對象」和「維度」（如果有的話，如「性能」、「學習曲線」等）。

---

## 結語：您的好奇心是唯一的限制

本手冊只涵蓋了最常見的幾種任務場景。Perplexity 的能力遠不止於此。請記住，Manus 的目標是理解您的「意圖」。不要害怕嘗試新的、更複雜的指令。您可以組合不同的任務，例如：

> 「嘿 Manus，幫我用 Perplexity 的專業模式，深度研究一下『SaaS 產業的定價策略』，然後將生成的報告總結成一份要點，存到我 Notion 的“商業模式”頁面裡。」

這是一個結合了 `pro_search` 和 `notion.pages.create` 的複雜任務。而這，正是 Manus 存在的意義——成為您與強大 AI 工具之間的智慧橋樑。

現在，開始提問吧！
