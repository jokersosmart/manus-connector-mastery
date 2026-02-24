# Kling MCP 連結器使用說明報告

作者：Joker

## 1. 總覽

Kling MCP 連結器將最先進的文字到影片 (Text-to-Video) AI 模型 Kling 整合至 Manus 的工作流程中。使用者可以透過自然語言，以任務導向的方式，指揮 Manus 調用此連結器，實現從創意到影片的無縫生成。本報告詳細介紹了 Kling 連結器提供的所有可用工具、其參數、功能及使用範例。

**核心設計理念**：

- **非同步執行**：考量到影片生成需要較長時間，本連結器核心的 `create_video` 工具採用非同步設計。使用者可選擇提交任務後立即返回一個 `job_id`，讓 Manus Agent 繼續處理其他工作，稍後再用 `get_job_status` 工具查詢結果。這確保了複雜工作流的流暢性。
- **賦能使用者**：工具的參數設計旨在將 Kling 強大的能力，轉化為使用者易於理解和控制的選項，如 `style`, `motion_level` 等，以降低提示工程的門檻。

## 2. 可用工具 (Tools)

### 2.1. `create_video`

此為核心工具，根據文字提示生成一段影片。

#### 參數說明

<table header-row="true">
<tr>
<td>參數名稱</td>
<td>類型</td>
<td>是否必須</td>
<td>描述</td>
<td>預設值</td>
</tr>
<tr>
<td>`prompt`</td>
<td>String</td>
<td>是</td>
<td>影片內容的詳細文字描述。這是決定影片內容最關鍵的輸入。</td>
<td>無</td>
</tr>
<tr>
<td>`negative_prompt`</td>
<td>String</td>
<td>否</td>
<td>希望在影片中**避免**出現的元素、風格或動作。</td>
<td>`deformed, blurry, low quality`</td>
</tr>
<tr>
<td>`style`</td>
<td>String</td>
<td>否</td>
<td>影片的藝術風格。可用的風格可透過 `list_styles` 工具查詢。</td>
<td>`cinematic`</td>
</tr>
<tr>
<td>`aspect_ratio`</td>
<td>String</td>
<td>否</td>
<td>影片的寬高比。支援 `16:9` (橫向), `9:16` (縱向), `1:1` (方形)。</td>
<td>`16:9`</td>
</tr>
<tr>
<td>`duration_seconds`</td>
<td>Integer</td>
<td>否</td>
<td>影片的期望長度（秒）。最大值為 60。</td>
<td>`5`</td>
</tr>
<tr>
<td>`motion_level`</td>
<td>Integer</td>
<td>否</td>
<td>影片中鏡頭或主體的運動幅度，範圍從 1 (靜態) 到 10 (劇烈)。</td>
<td>`4`</td>
</tr>
<tr>
<td>`wait_for_completion`</td>
<td>Boolean</td>
<td>否</td>
<td>是否等待影片生成完成後再返回。`false` 會立即返回一個 `job_id`。</td>
<td>`false`</td>
</tr>
</table>

#### 使用範例

**範例 1：快速生成一個電影感的短片，並立即取得任務 ID**

> **使用者指令**："用 Kling 做一個影片，內容是一個太空人走在火星上，電影風格。"

```json
{
  "tool_name": "kling.create_video",
  "parameters": {
    "prompt": "An astronaut walking on the surface of Mars, cinematic style, high detail.",
    "style": "cinematic",
    "wait_for_completion": false
  }
}
```

#### 預期輸出 (wait_for_completion = false)

```json
{
  "status": "pending",
  "job_id": "kling-job-1a2b3c4d5e",
  "message": "Video generation task has been submitted. Use get_job_status with the job_id to check the result."
}
```

**範例 2：生成一個用於社群媒體的垂直影片，並等待其完成**

> **使用者指令**："幫我生成一段 10 秒的垂直影片，用於 Instagram Reels。內容是一隻貓在彈鋼琴，像素藝術風格。這次等它做完。"

```json
{
  "tool_name": "kling.create_video",
  "parameters": {
    "prompt": "A cat playing the piano, pixel art style.",
    "style": "pixel_art",
    "aspect_ratio": "9:16",
    "duration_seconds": 10,
    "wait_for_completion": true
  }
}
```

#### 預期輸出 (wait_for_completion = true)

```json
{
  "status": "completed",
  "job_id": "kling-job-6f7g8h9i0j",
  "result": {
    "file_path": "/home/ubuntu/Downloads/kling_output_6f7g8h9i0j.mp4",
    "url": "https://example.com/kling_output_6f7g8h9i0j.mp4"
  }
}
```

### 2.2. `get_job_status`

查詢一個非同步影片生成任務的當前狀態與結果。

#### 參數說明

<table header-row="true">
<tr>
<td>參數名稱</td>
<td>類型</td>
<td>是否必須</td>
<td>描述</td>
</tr>
<tr>
<td>`job_id`</td>
<td>String</td>
<td>是</td>
<td>由 `create_video` 工具返回的唯一任務 ID。</td>
</tr>
</table>

#### 使用範例

> **使用者指令**："檢查一下剛剛那個火星太空人影片的進度。"

```json
{
  "tool_name": "kling.get_job_status",
  "parameters": {
    "job_id": "kling-job-1a2b3c4d5e"
  }
}
```

#### 預期輸出

**輸出 1：處理中**
```json
{
  "status": "processing",
  "job_id": "kling-job-1a2b3c4d5e",
  "progress_percent": 65,
  "message": "Video generation is in progress."
}
```

**輸出 2：已完成**
```json
{
  "status": "completed",
  "job_id": "kling-job-1a2b3c4d5e",
  "result": {
    "file_path": "/home/ubuntu/Downloads/kling_output_1a2b3c4d5e.mp4",
    "url": "https://example.com/kling_output_1a2b3c4d5e.mp4"
  }
}
```

**輸出 3：失敗**
```json
{
  "status": "failed",
  "job_id": "kling-job-1a2b3c4d5e",
  "error_message": "Failed to generate video: The prompt was flagged for violating safety policies."
}
```

### 2.3. `list_styles`

返回目前支援的所有影片藝術風格列表。

#### 參數說明

此工具沒有參數。

#### 使用範例

> **使用者指令**："Kling 支援哪些影片風格？"

```json
{
  "tool_name": "kling.list_styles",
  "parameters": {}
}
```

#### 預期輸出

```json
{
  "styles": [
    "cinematic",
    "photorealistic",
    "anime",
    "pixel_art",
    "fantasy",
    "documentary",
    "3d_animation",
    "watercolor"
  ]
}
```
