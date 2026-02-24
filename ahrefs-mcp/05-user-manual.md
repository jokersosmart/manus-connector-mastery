# Kling 連結器終端使用者操作手冊

作者：Joker

## 歡迎！

本手冊專為希望透過**自然語言**與 Manus 互動，來使用 Kling 連結器強大功能的您而設計。您不需要了解任何程式碼或複雜的參數，只需要像與一位創意助理對話一樣，告訴 Manus 您想要什麼樣的影片。本手冊將透過一系列任務導向的範例，向您展示如何輕鬆實現您的創意。

## 核心理念：您說，Manus 做

Kling 連結器的設計核心，是將複雜的技術隱藏在簡單的對話之後。您只需要專注於**描述您想要的結果**，Manus 會負責將您的話語轉換為精確的技術指令（即 `tool call`）。

--- 

## 任務一：我的第一個 AI 影片

**您的目標**：創建一個簡單的影片，體驗從無到有的過程。

#### 您可以這樣說：

> "嗨 Manus，能幫我用 Kling 做一個影片嗎？我想要看到『陽光穿過森林樹冠』的畫面。"

#### Manus 如何理解與執行：

聽到這個指令後，Manus 會識別出您的意圖是「創建影片」，並且內容是「陽光穿過森林樹冠」。它會自動進行以下翻譯：

-   **意圖** → 調用 `kling.create_video` 工具。
-   **內容** → 填入 `prompt` 參數。

於是，在幕後，Manus 執行了這樣的工具調用：

```json
{
  "tool_name": "kling.create_video",
  "parameters": {
    "prompt": "Sunlight filtering through a forest canopy."
  }
}
```

您會收到一個任務已提交的通知。稍後，您可以簡單地問「影片好了嗎？」來獲取結果。

--- 

## 任務二：為我的社群媒體創建內容

**您的目標**：創建一個適合在 Instagram 或 TikTok 上發布的垂直短影片。

#### 您可以這樣說：

> "我需要一個 10 秒的**垂直影片**，用在我的 Instagram Story。內容是『一杯正在被拉花的拿鐵咖啡』，風格要**像照片一樣逼真**。"

#### Manus 如何理解與執行：

這次，您的指令包含了更多細節。Manus 會像一位細心的助理一樣，捕捉這些關鍵字：

-   **「10 秒」** → `duration_seconds: 10`
-   **「垂直影片」** → `aspect_ratio: "9:16"`
-   **「像照片一樣逼真」** → `style: "photorealistic"`
-   **「一杯正在被拉花的拿鐵咖啡」** → `prompt: "A cup of latte with latte art being made."`

工具調用會是這樣：

```json
{
  "tool_name": "kling.create_video",
  "parameters": {
    "prompt": "A cup of latte with latte art being made.",
    "duration_seconds": 10,
    "aspect_ratio": "9:16",
    "style": "photorealistic"
  }
}
```

--- 

## 任務三：我想要更有動感的影片！

**您的目標**：創建一個充滿動感的影片，例如追車或體育活動。

#### 您可以這樣說：

> "來點刺激的！幫我做一個**運動感很強**的影片，內容是『一隻老鷹在峽谷中快速飛行』。"

#### Manus 如何理解與執行：

關鍵詞「運動感很強」會被 Manus 解讀為需要較高的動態水平。

-   **「運動感很強」** → `motion_level: 8` (或一個較高的數值)
-   **「一隻老鷹在峽谷中快速飛行」** → `prompt: "An eagle flying swiftly through a canyon."`

工具調用會是這樣：

```json
{
  "tool_name": "kling.create_video",
  "parameters": {
    "prompt": "An eagle flying swiftly through a canyon.",
    "motion_level": 8
  }
}
```

--- 

## 任務四：一次處理多個想法

**您的目標**：您腦中有好幾個點子，希望 AI 一次幫您全部處理，而您先去忙別的事。

#### 您可以這樣說：

> "我有點忙，你先幫我把這幾個影片加到待辦清單裡，**不用等它們完成**。第一個是『復古風格的巴黎街景』，第二個是『動漫風格的魔法少女變身』，第三個是『一個男人在雨中行走，感覺要很孤獨』。"

#### Manus 如何理解與執行：

「不用等它們完成」這個指令至關重要。它告訴 Manus 應該採用非同步模式，立即返回，讓您可以繼續下達其他指令。

-   **「不用等它們完成」** → `wait_for_completion: false`

Manus 會連續執行三次 `kling.create_video` 工具調用，每一次都包含這個參數，並分別回報三個任務的 `job_id` 給您。

--- 

## 任務五：我不想要……

**您的目標**：在生成影片時，明確排除一些不希望出現的元素，以提升影片品質。

#### 您可以這樣說：

> "幫我生成一個寫實風格的『微笑的女人』的影片，但我**不希望**看到**奇怪的牙齒或模糊的臉**。"

#### Manus 如何理解與執行：

「不希望看到」或「避免」等詞語會觸發 Manus 使用 `negative_prompt` 參數。

-   **「微笑的女人」** → `prompt: "A realistic portrait of a smiling woman."`
-   **「奇怪的牙齒或模糊的臉」** → `negative_prompt: "deformed teeth, blurry face"`

工具調用會是這樣：

```json
{
  "tool_name": "kling.create_video",
  "parameters": {
    "prompt": "A realistic portrait of a smiling woman.",
    "style": "photorealistic",
    "negative_prompt": "deformed teeth, blurry face"
  }
}
```

## 結語

正如您所見，您與 Kling 連結器的互動是自然且直觀的。您不需要成為技術專家，只需要成為一個好的溝通者。專注於清晰地表達您的創意，剩下的交給 Manus。現在，就開始您的創作之旅吧！
