# 「Flux」連結器使用說明報告

作者：Joker

## 1. 總覽

Flux 連結器是一個強大的 MCP (Model Context Protocol) 伺服器，它將 Flux 的高級圖像生成能力帶給 Manus AI。透過此連結器，使用者可以直接在 Manus 的工作流程中，使用自然語言或結構化指令來生成、修改和控制高品質的圖像。

本報告將詳細說明 Flux 連結器提供的所有可用工具、其參數、功能、使用範例及預期輸出。

## 2. 可用工具 (Tools)

Flux 連結器目前提供以下四種核心工具：

1.  **`generate`**: 根據文字提示生成全新的圖像。
2.  **`img2img`**: 以一張現有圖像為參考，結合文字提示生成新的圖像。
3.  **`inpaint`**: 對圖像的特定區域（由遮罩定義）進行重新繪製。
4.  **`control`**: 使用結構性控制訊號（如邊緣、深度圖、姿勢）來指導圖像生成。

接下來將對每個工具進行詳細介紹。

### 2.1 `generate`

此工具是 Flux 的核心功能，能夠將簡單的文字描述轉換為豐富的視覺圖像。

#### 參數說明

| 參數 | 類型 | 描述 | 是否必須 | 預設值 |
| :--- | :--- | :--- | :--- | :--- |
| `prompt` | string | 用於圖像生成的文字提示。 | **是** | N/A |
| `model` | string | 用於生成的模型。可選值：`flux.1.1-pro`, `flux.1-pro`, `flux.1-dev`, `flux.1.1-ultra`。 | 否 | `flux.1.1-pro` |
| `aspect_ratio` | string | 輸出圖像的長寬比。可選值：`1:1`, `4:3`, `3:4`, `16:9`, `9:16`。 | 否 | N/A |
| `width` | number | 圖像寬度（像素）。若設定了 `aspect_ratio`，此參數會被忽略。 | 否 | N/A |
| `height` | number | 圖像高度（像素）。若設定了 `aspect_ratio`，此參數會被忽略。 | 否 | N/A |
| `output` | string | 輸出圖像的檔案名稱。 | 否 | `generated.jpg` |

#### 使用範例

```json
{
  "tool": "generate",
  "arguments": {
    "prompt": "A majestic white dragon flying over a cyberpunk city at night, neon lights reflecting on its scales",
    "model": "flux.1.1-ultra",
    "aspect_ratio": "16:9",
    "output": "dragon_city.jpg"
  }
}
```

#### 預期輸出

執行成功後，系統將返回一段文字，確認圖像已生成並提供儲存路徑。

```text
Image generated successfully and saved to: /path/to/outputs/dragon_city.jpg
```

同時，一張符合描述的 16:9 圖像將被儲存到指定的輸出路徑。

### 2.2 `img2img`

此工具允許使用者提供一張輸入圖像作為風格或結構的參考，並結合新的文字提示來創造變體。

#### 參數說明

| 參數 | 類型 | 描述 | 是否必須 | 預設值 |
| :--- | :--- | :--- | :--- | :--- |
| `image` | string | 輸入參考圖像的檔案路徑。 | **是** | N/A |
| `prompt` | string | 用於圖像生成的文字提示。 | **是** | N/A |
| `name` | string | 為此次生成任務命名，方便追蹤。 | **是** | N/A |
| `model` | string | 用於生成的模型。 | 否 | `flux.1.1-pro` |
| `strength` | number | 生成強度，控制結果與原圖的相似度。值域為 0.0 到 1.0，值越小越像原圖。 | 否 | 0.85 |
| `width` | number | 輸出圖像的寬度（像素）。 | 否 | N/A |
| `height` | number | 輸出圖像的高度（像素）。 | 否 | N/A |
| `output` | string | 輸出圖像的檔案名稱。 | 否 | `outputs/generated.jpg` |

#### 使用範例

假設我們有一張 `van_gogh_starry_night.jpg` 的圖像。

```json
{
  "tool": "img2img",
  "arguments": {
    "image": "/path/to/van_gogh_starry_night.jpg",
    "prompt": "A modern city skyline in the style of Van Gogh",
    "name": "city_in_van_gogh_style",
    "strength": 0.75
  }
}
```

#### 預期輸出

系統將返回成功訊息，並生成一張融合了現代城市天際線內容與梵谷《星夜》風格的新圖像。

### 2.3 `inpaint`

此工具用於對圖像的特定區域進行智慧修復或替換，常用於移除不需要的物件或添加新元素。

#### 參數說明

| 參數 | 類型 | 描述 | 是否必須 | 預設值 |
| :--- | :--- | :--- | :--- | :--- |
| `image` | string | 輸入圖像的檔案路徑。 | **是** | N/A |
| `prompt` | string | 描述要在遮罩區域內生成什麼內容的文字提示。 | **是** | N/A |
| `mask_shape` | string | 遮罩的形狀。可選值：`circle`, `rectangle`。 | 否 | `circle` |
| `position` | string | 遮罩的位置。可選值：`center`, `ground`。 | 否 | `center` |
| `output` | string | 輸出圖像的檔案名稱。 | 否 | `inpainted.jpg` |

#### 使用範例

假設我們有一張 `beach_photo.jpg`，照片中央有一個不想要的沙灘球。

```json
{
  "tool": "inpaint",
  "arguments": {
    "image": "/path/to/beach_photo.jpg",
    "prompt": "empty sand and waves",
    "mask_shape": "circle",
    "position": "center",
    "output": "beach_fixed.jpg"
  }
}
```

#### 預期輸出

系統將返回成功訊息。在 `beach_fixed.jpg` 中，原本位於中央的沙灘球將被符合周圍環境的「空沙灘和海浪」所取代。

### 2.4 `control`

此工具提供了對圖像生成過程更精細的控制，它使用額外的控制圖像（如 Canny 邊緣圖、深度圖或人體姿勢圖）來指導 AI 生成的內容結構。

#### 參數說明

| 參數 | 類型 | 描述 | 是否必須 | 預設值 |
| :--- | :--- | :--- | :--- | :--- |
| `type` | string | 使用的控制類型。可選值：`canny`, `depth`, `pose`。 | **是** | N/A |
| `image` | string | 輸入的控制圖像路徑（例如，一張 Canny 邊緣圖）。 | **是** | N/A |
| `prompt` | string | 用於圖像生成的文字提示。 | **是** | N/A |
| `steps` | number | 推理步數，影響生成品質與時間。 | 否 | 50 |
| `guidance` | number | 引導權重，控制生成結果與提示的符合程度。 | 否 | N/A |
| `output` | string | 輸出圖像的檔案名稱。 | 否 | N/A |

#### 使用範例

假設我們有一張名為 `person_pose.jpg` 的人體骨架姿勢圖。

```json
{
  "tool": "control",
  "arguments": {
    "type": "pose",
    "image": "/path/to/person_pose.jpg",
    "prompt": "An astronaut in a futuristic suit, standing in the same pose",
    "guidance": 7.5,
    "output": "astronaut_pose.jpg"
  }
}
```

#### 預期輸出

系統將返回成功訊息。生成的 `astronaut_pose.jpg` 圖像中，會有一位穿著未來感太空服的太空人，其站立姿勢與輸入的 `person_pose.jpg` 骨架圖完全一致。

## 3. 總結

Flux 連結器為 Manus 提供了從簡單到複雜的全面圖像生成能力。透過這四個核心工具，使用者可以實現從快速創意生成到精細可控的專業內容創作。建議開發者與使用者詳細閱讀本報告，以充分利用 Flux 連結器的強大功能。
