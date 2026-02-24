# Invideo 終端使用者操作手冊

作者：Joker

## 簡介

歡迎使用 Invideo 連結器！本手冊專為終端使用者設計，旨在展示如何透過簡單、直觀的自然語言指令，讓 Manus 為您完成複雜的影片製作任務。您無需了解任何程式碼或複雜的參數，只需像與真人助理對話一樣，描述您想要的影片即可。

以下，我們將透過一系列的任務導向範例，展示您可以用哪些方式下達指令，並解釋 Manus 在背後是如何將您的話語，精準地轉換為對 Invideo 工具的調用。

## 任務一：快速製作一個產品介紹影片

假設您是一位新創公司的創辦人，想要為您的 App「Mindful」製作一個簡短的介紹影片。

### 您的指令

> 「嘿 Manus，幫我做一個 30 秒的影片，介紹我們的冥想 App『Mindful』。我希望影片風格是平靜、放鬆的，旁白用溫柔的女聲。影片要展示一些大自然的美景，像是森林和海邊，然後穿插一些人們閉眼微笑、看起來很平靜的畫面。最後，記得放上我們的 Logo，網址是 `https://example.com/mindful_logo.png`，並加上一句話：『Mindful: 找到你內心的平靜』。」

### Manus 的轉換過程

收到您的指令後，Manus 會開始解析您的意圖，並將其對應到 `invideo.create_video` 工具的各個參數上。

1.  **核心需求分析**：Manus 識別出您的主要目標是「製作影片」，因此鎖定 `create_video` 工具。
2.  **Prompt 組織**：Manus 將您對影片內容的描述，整合成一個結構化的 `prompt`。它會包含影片時長、App 名稱、風格、場景描述、結尾畫面和標語。
    -   `Create a 30-second video for the meditation app 'Mindful'.`
    -   `The style should be calm and relaxing.`
    -   `Show beautiful nature scenes like forests and beaches.`
    -   `Intersperse with shots of people smiling peacefully with their eyes closed.`
    -   `End with the logo and the tagline: 'Mindful: Find your inner peace'.`
3.  **參數提取**：
    -   「溫柔的女聲」被對應到 `voice: 'calm_female'`。
    -   「平靜、放鬆的」風格，除了體現在 Prompt 中，也可能影響 `music_style` 的選擇，例如 `music_style: 'ambient'`。
    -   Logo 網址被直接提取到 `brand_logo_url: 'https://example.com/mindful_logo.png'`。

### 最終的工具調用 (示意)

```json
{
  "tool_name": "invideo.create_video",
  "parameters": {
    "prompt": "Create a 30-second video for the meditation app 'Mindful'. The style should be calm and relaxing. Show beautiful nature scenes like forests and beaches, interspersed with shots of people smiling peacefully with their eyes closed. End with the logo from https://example.com/mindful_logo.png and the tagline: 'Mindful: Find your inner peace'.",
    "voice": "calm_female",
    "music_style": "ambient",
    "output_quality": "1080p"
  }
}
```

## 任務二：將部落格文章轉換為影片摘要

您是一位內容行銷人員，希望將最新發布的一篇關於「遠距工作的好處」的部落格文章，快速轉換成一支可以在社群媒體上分享的短影片。

### 您的指令

> 「Manus，幫我把這篇文章 `https://myblog.com/remote-work-benefits` 做成一支影片摘要。影片長度大概一分半鐘，用聽起來比較專業、有活力的男聲來當旁白。幫我自動加上英文字幕。」

### Manus 的轉換過程

這個任務稍微複雜一些，因為它涉及了外部資訊的處理。

1.  **識別多步驟任務**：Manus 首先會意識到，在製作影片前，需要先存取並理解您提供的網址內容。這會觸發一個前置步驟：使用瀏覽器工具讀取文章內容。
2.  **內容摘要**：在讀取文章後，Manus 會運用其語言模型能力，將文章的核心論點（例如：彈性的工作時間、節省通勤成本、更好的工作生活平衡）提煉出來。
3.  **Prompt 生成**：接著，Manus 會將這些摘要後的論點，編織成一個影片腳本，作為 `create_video` 的 `prompt`。例如：「`Create a 90-second video summarizing the benefits of remote work. Start by showing a stressful commute, then transition to a relaxed home office. Highlight key benefits like flexible schedules, no commuting, and better work-life balance. Use an energetic and professional tone.`」
4.  **參數提取**：
    -   「一分半鐘」被轉換為 Prompt 的一部分。
    -   「專業、有活力的男聲」被對應到 `voice: 'energetic_male'`。
    -   「自動加上英文字幕」被對應到 `subtitle_language: 'en'`。

### 最終的工具調用 (示意)

```json
{
  "tool_name": "invideo.create_video",
  "parameters": {
    "prompt": "Create a 90-second video summarizing the benefits of remote work, based on the article at https://myblog.com/remote-work-benefits. Start by showing a stressful commute, then transition to a relaxed home office. Highlight key benefits like flexible schedules, no commuting, and better work-life balance. Use an energetic and professional tone.",
    "voice": "energetic_male",
    "subtitle_language": "en",
    "output_quality": "1080p"
  }
}
```

透過這些範例，您可以看到，您只需要專注於「您想要什麼」，而將「如何實現」的技術細節交給 Manus。您描述得越清晰、越有畫面感，Manus 就越能產出令您驚豔的成果。
