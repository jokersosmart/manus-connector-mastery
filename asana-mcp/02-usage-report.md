_# Anthropic 連結器使用說明報告

**作者：Joker**

## 1. 總覽

Anthropic 連結器為 Manus 平台提供了一流的、由大型語言模型 (LLM) 驅動的文字生成與理解能力。此連結器直接整合了 Anthropic 公司領先的 Claude 系列模型，讓使用者能夠在 Manus 的工作流程中無縫利用其強大的自然語言處理能力。

與傳統的、擁有多個離散工具的 MCP 連結器不同，Anthropic 連結器的核心功能集中在一個統一的、功能強大的工具上：`create_message`。這個工具是與 Claude 模型互動的主要入口，透過調整其參數，可以實現從簡單的問答、文本摘要到複雜的程式碼生成、創意寫作等多種任務。

本報告將詳細介紹 `create_message` 工具的各項參數、功能、使用範例及預期輸出，幫助您充分發揮 Anthropic 連結器的潛力。

## 2. 核心工具：`create_message`

此工具用於向指定的 Anthropic 模型傳送訊息，並接收模型生成的回應。

### 2.1. 功能說明

`create_message` 是與 Anthropic Claude 模型進行對話和互動的基礎。它支援多輪對話，能夠理解上下文，並根據提供的系統提示 (System Prompt) 來調整其行為、風格和角色。無論是需要分析一份冗長的財務報告、草擬一封專業的電子郵件，還是進行一場頭腦風暴，`create_message` 都是您驅動這一切的核心指令。

### 2.2. 參數詳解

<table header-row="true">
<tr>
<td>參數</td>
<td>類型</td>
<td>是否必須</td>
<td>描述</td>
<td>預設值</td>
</tr>
<tr>
<td>`model`</td>
<td>String</td>
<td>是</td>
<td>指定要使用的 Anthropic 模型 ID。目前支援的模型包括 `claude-3-opus-20240229`, `claude-3-sonnet-20240229`, `claude-3-haiku-20240307` 等。</td>
<td>`claude-3.7-sonnet` (假設)</td>
</tr>
<tr>
<td>`messages`</td>
<td>Array</td>
<td>是</td>
<td>一個包含對話歷史的物件陣列。每個物件應包含 `role` (`user` 或 `assistant`) 和 `content`。</td>
<td>N/A</td>
</tr>
<tr>
<td>`system`</td>
<td>String</td>
<td>否</td>
<td>系統提示，用於設定 AI 的角色、背景、指令或輸出格式。能有效引導模型產生更符合預期的回應。</td>
<td>N/A</td>
</tr>
<tr>
<td>`max_tokens`</td>
<td>Integer</td>
<td>是</td>
<td>指定模型在回應中可以產生的最大 token 數量。這有助於控制輸出的長度和成本。</td>
<td>`1024`</td>
</tr>
<tr>
<td>`temperature`</td>
<td>Float</td>
<td>否</td>
<td>控制輸出的隨機性，範圍從 0.0 到 1.0。較高的值 (如 0.8) 會使輸出更隨機、更有創意；較低的值 (如 0.2) 會使其更具確定性和專注性。</td>
<td>`0.7`</td>
</tr>
<tr>
<td>`stop_sequences`</td>
<td>Array</td>
<td>否</td>
<td>一個字串陣列，當模型產生這些字串時會停止輸出。可用於控制輸出格式，例如在產生列表時，將換行符作為停止序列。</td>
<td>N/A</td>
</tr>
</table>

### 2.3. 使用範例

#### 範例 1：基本問答

此範例展示如何提出一個簡單的問題，並從模型獲得答案。

**工具調用 (JSON 輸入):**
```json
{
  "model": "claude-3-sonnet-20240229",
  "messages": [
    {
      "role": "user",
      "content": "什麼是光合作用？"
    }
  ],
  "max_tokens": 200
}
```

**預期輸出:**

模型將會提供一段關於光合作用的詳細解釋，說明其定義、過程、地點以及對地球生態系統的重要性。

#### 範例 2：使用系統提示進行角色扮演

此範例展示如何使用 `system` 參數讓模型扮演一個特定的角色，並以該角色的口吻和風格進行回應。

**工具調用 (JSON 輸入):**
```json
{
  "model": "claude-3-opus-20240229",
  "system": "你是一位經驗豐富的莎士比亞劇作家。請用充滿戲劇性和詩意的語言回答問題。",
  "messages": [
    {
      "role": "user",
      "content": "請描述一下愛情。"
    }
  ],
  "max_tokens": 250,
  "temperature": 0.8
}
```

**預期輸出:**

模型將會以莎士比亞的風格，用華麗的詞藻和比喻來描繪愛情，可能是一段十四行詩或一段充滿激情的獨白。

#### 範例 3：長文本摘要

此範例展示 Anthropic 模型強大的長文本處理能力。使用者可以輸入一篇長文，並要求模型進行摘要。

**工具調用 (JSON 輸入):**
```json
{
  "model": "claude-3-opus-20240229",
  "system": "你是一位專業的分析師，請將以下文章總結為 5 個關鍵要點。",
  "messages": [
    {
      "role": "user",
      "content": "（此處貼上數千字的長篇文章）... 這篇文章的核心論點是什麼？"
    }
  ],
  "max_tokens": 500
}
```

**預期輸出:**

模型將會閱讀並理解整篇文章的內容，然後輸出五個條理清晰、準確捕捉文章核心思想的關鍵要點。

## 3. 結論

Anthropic 連結器透過其統一的 `create_message` 工具，提供了一個既簡單又極其靈活的介面，讓使用者能夠充分利用 Claude 系列模型的先進能力。通過熟練掌握本報告中介紹的各項參數和使用技巧，開發者和使用者可以在 Manus 平台上建構出功能強大、智慧化的自動化工作流程，解決從日常辦公到複雜分析的各種挑戰。
