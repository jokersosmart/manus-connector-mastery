'''
# HeyGen MCP 連結器使用說明報告

**作者：Joker**

---

本文檔詳細介紹了 HeyGen MCP 連結器的所有可用工具、參數、功能、使用範例及預期輸出。基於「漸進式揭示」的設計哲學，工具集被劃分為三個層級，以適應從新手到專家的不同使用者需求。

## 設計理念：漸進式揭示

HeyGen 連結器的設計核心是讓使用者能夠平滑地從簡單的任務開始，逐步探索更複雜的功能。我們不希望使用者在初次接觸時就被繁雜的參數所淹沒。因此，我們提供了三個層級的工具：

*   **層級一：新手工具** - 只需一個指令，就能完成核心任務。
*   **層級二：進階工具** - 提供更多客製化選項，如選擇虛擬人與聲音。
*   **層級三：專家工具** - 允許對影片的每一個場景和元素進行精細控制。

## 層級一：新手工具 (Quick Start)

此層級專為希望立即看到結果的使用者設計。

### 1. `heygen.generate_simple_video`

快速從一段文字腳本生成一個完整的影片。系統會自動選擇預設的虛擬人、聲音和背景。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `script` | `string` | 是 | 影片的文字腳本。建議長度不超過 300 字元以獲得最佳效果。 |
| `title` | `string` | 否 | 影片的標題。將用於影片元數據。 |

**使用範例：**

```json
{
  "tool": "heygen.generate_simple_video",
  "script": "歡迎使用 Manus 與 HeyGen 打造的 AI 影片生成功能！只需幾秒鐘，您的文字就能變成生動的影片。",
  "title": "我的第一個 AI 影片"
}
```

**預期輸出：**

一個包含生成影片 URL 的 JSON 物件。

```json
{
  "status": "success",
  "video_url": "https://heygen.com/videos/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.mp4"
}
```

## 層級二：進階工具 (Customization)

此層級為希望對影片有更多控制的使用者提供客製化選項。

### 1. `heygen.list_avatars`

列出所有可用的虛擬人 (Avatar) 及其 ID。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `gender` | `string` | 否 | 篩選虛擬人的性別。可選值：`male`, `female`。 |
| `ethnicity` | `string` | 否 | 篩選虛擬人的種族。可選值：`asian`, `white`, `black`, `hispanic`。 |

**使用範例：**

```json
{
  "tool": "heygen.list_avatars",
  "gender": "female",
  "ethnicity": "asian"
}
```

**預期輸出：**

一個包含虛擬人列表的 JSON 陣列。

```json
{
  "avatars": [
    { "id": "avatar-f-asian-01", "name": "Anna" },
    { "id": "avatar-f-asian-02", "name": "Yuki" }
  ]
}
```

### 2. `heygen.list_voices`

列出所有可用的 AI 聲音及其 ID。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `language` | `string` | 否 | 篩選聲音的語言。例如：`en-US`, `zh-CN`。 |
| `gender` | `string` | 否 | 篩選聲音的性別。可選值：`male`, `female`。 |

**使用範例：**

```json
{
  "tool": "heygen.list_voices",
  "language": "en-US",
  "gender": "male"
}
```

**預期輸出：**

一個包含聲音列表的 JSON 陣列。

```json
{
  "voices": [
    { "id": "voice-en-us-male-01", "name": "David" },
    { "id": "voice-en-us-male-02", "name": "John" }
  ]
}
```

### 3. `heygen.generate_custom_video`

使用指定的虛擬人、聲音和背景顏色生成影片。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `script` | `string` | 是 | 影片的文字腳本。 |
| `avatar_id` | `string` | 是 | 虛擬人的 ID，可從 `list_avatars` 取得。 |
| `voice_id` | `string` | 是 | 聲音的 ID，可從 `list_voices` 取得。 |
| `background_color` | `string` | 否 | 影片的背景顏色，使用十六進位色碼 (例如 `#FFFFFF`)。預設為藍色。 |

**使用範例：**

```json
{
  "tool": "heygen.generate_custom_video",
  "script": "現在，我可以自訂我的虛擬主持人和聲音了！",
  "avatar_id": "avatar-f-asian-01",
  "voice_id": "voice-en-us-male-01",
  "background_color": "#1E88E5"
}
```

**預期輸出：**

與 `generate_simple_video` 相同的影片 URL 輸出。

## 層級三：專家工具 (Scene-based Control)

此層級為需要電影級精細控制的專業使用者設計，允許透過場景 (Scene) 的組合來建構複雜影片。

### 1. `heygen.create_scene`

創建一個新的空白場景，並返回其 ID。

**使用範例：**

```json
{
  "tool": "heygen.create_scene"
}
```

**預期輸出：**

```json
{
  "scene_id": "scene-xxxxxxxx-xxxx"
}
```

### 2. `heygen.add_to_scene`

向指定場景中添加元素，如虛擬人、文字或圖片。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `scene_id` | `string` | 是 | 目標場景的 ID。 |
| `element_type` | `string` | 是 | 要添加的元素類型。可選值：`avatar`, `text`, `image`。 |
| `...` | `any` | 是 | 根據 `element_type` 而定的其他參數。例如，`avatar` 需要 `avatar_id` 和 `script`；`text` 需要 `content` 和 `position`。 |

**使用範例：**

```json
// 添加虛擬人
{
  "tool": "heygen.add_to_scene",
  "scene_id": "scene-xxxxxxxx-xxxx",
  "element_type": "avatar",
  "avatar_id": "avatar-f-asian-01",
  "script": "這是第一個場景。"
}

// 添加文字
{
  "tool": "heygen.add_to_scene",
  "scene_id": "scene-xxxxxxxx-xxxx",
  "element_type": "text",
  "content": "場景一標題",
  "position": { "x": 100, "y": 50 }
}
```

**預期輸出：**

```json
{
  "status": "success",
  "scene_id": "scene-xxxxxxxx-xxxx"
}
```

### 3. `heygen.render_video`

將多個場景按指定順序渲染成最終影片。

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `scene_ids` | `array` | 是 | 一個包含場景 ID 的陣列，按播放順序排列。 |
| `title` | `string` | 否 | 影片的標題。 |

**使用範例：**

```json
{
  "tool": "heygen.render_video",
  "scene_ids": ["scene-xxxx-0001", "scene-xxxx-0002", "scene-xxxx-0003"],
  "title": "我的多場景電影"
}
```

**預期輸出：**

與 `generate_simple_video` 相同的影片 URL 輸出。

---

**附註**：所有工具在調用前，若涉及潛在的 API 費用，Manus 將會提供成本預估並請求使用者確認，以確保成本透明度。
'''
