# Flux 從新手到精通：完整學習路徑指南

作者：Joker

## 前言：你的 AI 視覺創作夥伴

歡迎來到 Flux 的世界！Flux 不僅僅是一個圖像生成工具，它更是一個強大的 AI 視覺創作夥伴。本指南將帶領你從一位對 AI 繪圖感到好奇的新手，一步步成長為能夠駕馭 Flux、創造出驚人視覺作品的專家。無論你是設計師、藝術家、行銷人員，還是純粹的愛好者，這條路徑都將為你打開一扇通往無限創意的大門。

## 第一章：基礎概念 (Novice)

在這個階段，我們的目標是理解 AI 圖像生成的基本原理，並成功生成你的第一張圖片。

### 1.1 核心概念：什麼是 Prompt？

Prompt（提示詞）是你與 AI 溝通的語言。一個好的 Prompt 就像一個清晰的指令，它告訴 AI 你想要畫什麼。一個基本的 Prompt 包含三個要素：

*   **主體 (Subject)**：你想要畫的核心物件，例如「一隻貓」。
*   **場景 (Setting)**：主體所在的環境，例如「在月光下的屋頂上」。
*   **風格 (Style)**：圖像的藝術風格，例如「像梵谷的畫一樣」。

> **練習 1**：嘗試組合你的第一個 Prompt：「A cat on a rooftop under the moonlight, in the style of Van Gogh」。

### 1.2 初試啼聲：使用 `generate` 工具

`generate` 是你將想法變為現實的第一個工具。它最簡單，也最直接。你只需要提供一個 Prompt，Flux 就會為你創造圖像。

**指令範例**：

```json
{
  "tool": "generate",
  "arguments": {
    "prompt": "A majestic white dragon flying over a cyberpunk city at night, neon lights reflecting on its scales",
    "aspect_ratio": "16:9"
  }
}
```

> **練習 2**：修改上面的 Prompt，生成一張屬於你自己的「賽博龐克城市」圖像。嘗試更換主體，例如「一輛懸浮摩托車」或「一位偵探」。

### 1.3 掌握構圖：`aspect_ratio` 的重要性

長寬比決定了你的畫布形狀。`16:9` 適合製作電影感的橫幅畫面，`9:16` 適合手機桌布，而 `1:1` 則是社群媒體的黃金比例。學會根據你的內容選擇合適的長寬比。

## 第二章：核心功能 (Adept)

恭喜你已入門！現在，我們將學習更進階的工具，讓你對圖像有更多的控制權。

### 2.1 風格遷移：`img2img` 的魔法

`img2img` (Image-to-Image) 可讓你上傳一張參考圖，並將其風格或構圖應用到新的創作中。這是實現風格一致性的利器。

**關鍵參數**：`strength`

`strength` 參數控制了新圖像與參考圖的相似度。值越低（例如 0.3），越忠於原圖的結構；值越高（例如 0.9），AI 的創作自由度越大。

**指令範例**：

```json
{
  "tool": "img2img",
  "arguments": {
    "image": "/path/to/your_portrait.jpg",
    "prompt": "A portrait of an astronaut, futuristic, detailed armor",
    "strength": 0.7
  }
}
```

> **練習 3**：找一張你喜歡的風景照，使用 `img2img` 將它變成不同的季節，例如「in winter, covered in snow」或「in autumn, with golden leaves」。

### 2.2 精準修復：`inpaint` 的妙用

`inpaint` (局部重繪) 就像一支智慧畫筆，可以讓你精準地修改圖像的某個部分，同時保持其他部分不變。你可以用它來移除照片中的路人、為角色更換服裝，或在場景中添加新物件。

**指令範例**：

假設你想移除一張合照中某個人臉上的眼鏡。

```json
{
  "tool": "inpaint",
  "arguments": {
    "image": "/path/to/group_photo.jpg",
    "prompt": "a human face without glasses",
    "mask_shape": "rectangle" // 假設你手動框選了眼鏡區域
  }
}
```

> **練習 4**：找一張你的房間照片，使用 `inpaint` 在牆上「掛」上一幅你用 `generate` 生成的畫作。

## 第三章：進階技巧 (Expert)

你已經掌握了 Flux 的核心。現在，讓我們探索最頂尖的功能，讓你成為真正的「控圖大師」。

### 3.1 結構為王：`control` 的精髓

`control` (結構控制) 是專業級圖像生成的關鍵。它允許你使用線稿 (canny)、深度圖 (depth) 或人體姿勢 (pose) 來精確控制生成圖像的結構、佈局和透視。

*   **`canny`**：適用於需要精確線條和輪廓的場景，如建築設計、產品原型。
*   **`depth`**：適用於需要控制場景深度和空間感的場景，如風景畫、室內設計。
*   **`pose`**：適用於需要精準控制角色姿勢的場景，如角色設計、故事漫畫。

**指令範例**：

使用一張骨架圖 `stick_figure_running.jpg` 來生成一個正在奔跑的超級英雄。

```json
{
  "tool": "control",
  "arguments": {
    "type": "pose",
    "image": "/path/to/stick_figure_running.jpg",
    "prompt": "A superhero in a red and gold suit, running at high speed, motion blur, cinematic lighting"
  }
}
```

> **練習 5**：在紙上畫一個簡單的房子線稿，拍下來，然後使用 `control` 的 `canny` 模式，將它變成「a futuristic glass house on a cliff」或「a cozy wooden cabin in the forest」。

### 3.2 建立一致性：角色鎖定的概念

雖然 Flux 目前沒有一個名為「角色鎖定」的直接工具，但你可以透過組合 `img2img` 和 `control` 來實現類似的效果。這是在系列作品中維持角色外觀一致性的核心技巧。

**工作流程**：

1.  **生成基礎角色**：使用 `generate` 創造你的主要角色，得到 `character_base.jpg`。
2.  **提取姿勢**：為你的下一個場景建立一個新的姿勢骨架圖，例如 `new_pose.jpg`。
3.  **結合控制與參考**：
    *   使用 `control` 工具，以 `new_pose.jpg` 作為姿勢控制。
    *   在 Prompt 中詳細描述你的角色特徵（例如，「a man with silver hair, a scar over his left eye, wearing a black leather jacket」）。
    *   （進階）在某些支援的 Flux 版本或介面中，可能允許同時傳入 `img2img` 的參考圖 `character_base.jpg`，以進一步強化風格和特徵的一致性。

## 第四章：實戰應用 (Master)

成為大師意味著不僅懂工具，更懂如何結合工具來解決實際問題。

### 4.1 漫畫與故事板創作

*   **流程**：`generate` (設定角色) → `control` with `pose` (繪製不同分鏡) → `inpaint` (修正細節或添加對話框背景)。
*   **核心**：利用 `control` 的 `pose` 功能確保角色在不同畫格中的動作連貫性。

### 4.2 產品概念設計

*   **流程**：手繪草圖 → `control` with `canny` (生成高清產品渲染圖) → `img2img` (探索不同的材質與配色方案)。
*   **核心**：利用 `canny` 將你的快速草圖想法轉化為專業的視覺呈現。

### 4.3 虛擬攝影與場景設計

*   **流程**：`generate` (創造基礎場景) → `inpaint` (在場景中添加或移除物件，調整光影) → `img2img` (快速改變場景的整體氛圍，如「白天」變「黃昏」)。
*   **核心**：將 `inpaint` 作為你的「虛擬場地佈置工具」，自由地改造你的世界。

## 結語：你的旅程永無止境

恭喜你走完了從新手到精通的完整路徑！但請記住，工具在不斷進化，新的技巧和應用場景也在層出不窮。真正的「大師」永遠保持著一顆「新手」的好奇心。不斷練習，不斷探索，將 Flux 融入你的創意工作流，讓它成為你最強大的視覺表達工具。現在，去創造吧！
