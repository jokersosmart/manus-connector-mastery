# Invideo 連結器使用說明報告

作者：Joker

## 總覽

Invideo MCP 連結器提供了一個強大的工具，讓使用者能夠透過簡單的文字指令，將創意點子轉換為高品質的影片。其核心功能是利用先進的 AI 模型，自動化處理腳本生成、影像匹配、聲音合成、背景音樂與字幕等繁瑣的影片製作流程。本報告將詳細說明其主要工具、參數及使用方式。

由於無法直接偵測到 `invideo` MCP 伺服器，本報告將基於對 Invideo 公開功能的分析，定義一個核心工具 `create_video` 作為其主要功能的代表。此工具的設計旨在反映 Invideo 的核心價值：**將文字提示 (Prompt) 轉換為影片**。

## 核心工具：`create_video`

`create_video` 是 Invideo 連結器的核心工具，它封裝了從文字到影片的完整生成流程。

### 參數詳解

<table header-row="true">
  <tr>
    <td><b>參數名稱</b></td>
    <td><b>類型</b></td>
    <td><b>是否必要</b></td>
    <td><b>說明</b></td>
    <td><b>預設值</b></td>
  </tr>
  <tr>
    <td><code>prompt</code></td>
    <td>String</td>
    <td>是</td>
    <td>描述影片主題、內容、風格和目標的詳細文字提示。這是整個影片生成的基礎，Prompt 的品質直接影響最終產出的品質。</td>
    <td>無</td>
  </tr>
  <tr>
    <td><code>voice</code></td>
    <td>String</td>
    <td>否</td>
    <td>指定旁白的聲音風格。可選值可能包含：'american_male', 'british_female', 'calm_male', 'energetic_female' 等。</td>
    <td>'american_male'</td>
  </tr>
  <tr>
    <td><code>music_style</code></td>
    <td>String</td>
    <td>否</td>
    <td>指定背景音樂的風格。可選值可能包含：'cinematic', 'corporate', 'upbeat', 'ambient' 等。</td>
    <td>'cinematic'</td>
  </tr>
  <tr>
    <td><code>subtitle_language</code></td>
    <td>String</td>
    <td>否</td>
    <td>指定字幕的語言。可選值為 ISO 639-1 語言代碼，如 'en', 'es', 'zh'。</td>
    <td>'en'</td>
  </tr>
  <tr>
    <td><code>output_quality</code></td>
    <td>String</td>
    <td>否</td>
    <td>指定輸出影片的解析度。可選值可能包含：'720p', '1080p', '4k'。</td>
    <td>'1080p'</td>
  </tr>
  <tr>
    <td><code>brand_logo_url</code></td>
    <td>String</td>
    <td>否</td>
    <td>提供一個公開可存取的圖片 URL，用於在影片中加入品牌 Logo。</td>
    <td>無</td>
  </tr>
</table>

### 使用範例

以下範例展示了如何使用 `create_video` 工具來生成一支關於「永續時尚」的簡短廣告影片。

**情境**: 一家推廣環保服飾的電商，希望製作一支 30 秒的社群媒體廣告，強調其產品的環保理念與時尚感。

**工具調用 (Tool Call)**:

```json
{
  "tool_name": "create_video",
  "parameters": {
    "prompt": "Create a 30-second video ad for a sustainable fashion brand. Start with beautiful shots of nature, then transition to stylish people wearing our eco-friendly clothes in urban settings. The tone should be inspiring and modern. Highlight that our clothes are made from recycled materials. End with the brand's logo and a call to action: 'Shop Sustainably, Live Beautifully'.",
    "voice": "calm_female",
    "music_style": "upbeat",
    "subtitle_language": "en",
    "output_quality": "1080p",
    "brand_logo_url": "https://example.com/logo.png"
  }
}
```

### 預期輸出

執行上述工具調用後，系統將會回傳一個包含影片資訊的 JSON 物件。該物件中會包含一個公開的影片 URL，使用者可以直接存取或下載該影片。

**輸出結果 (Output)**:

```json
{
  "status": "success",
  "video_id": "vid_12345abcde",
  "video_url": "https://invideo.io/generated/vid_12345abcde.mp4",
  "message": "Video generation completed successfully."
}
```

使用者可以透過 `video_url` 連結，在瀏覽器中觀看、分享或下載這支由 AI 全自動生成的影片。影片內容將會緊密圍繞 `prompt` 的描述，包含自然與都市的場景切換、穿著環保服飾的人物、由 `calm_female` 聲音錄製的旁白、`upbeat` 風格的背景音樂，以及結尾的品牌 Logo 和行動呼籲。
