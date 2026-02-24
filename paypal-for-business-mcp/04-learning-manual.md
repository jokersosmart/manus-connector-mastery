# Flux 快速上手學習手冊

作者：Joker

## 歡迎！

本手冊旨在幫助你快速掌握 Flux 圖像生成的核心技能。我們將透過一系列清晰的學習目標和實踐練習，讓你從零開始，在短時間內學會創造和編輯 AI 圖像。讓我們開始吧！

---

### **模組一：我的第一張 AI 圖像**

**學習目標**：學會使用 `generate` 工具，透過文字提示創造一張全新的圖像。

**關鍵技能**：撰寫有效的 Prompt (提示詞)。

一個好的 Prompt 至少包含**主體**、**場景**和**風格**。這是你與 AI 溝通的基礎。

**實踐練習 1.1：生成你的第一個世界**

1.  **指令**：請 Manus 執行以下 Flux 工具調用。
2.  **工具調用**：

    ```json
    {
      "tool": "generate",
      "arguments": {
        "prompt": "A crystal castle on a floating island, surrounded by waterfalls, in a Ghibli-like anime style",
        "aspect_ratio": "16:9"
      }
    }
    ```

3.  **挑戰**：修改上述 `prompt`，將「crystal castle」替換為「a giant glowing mushroom house」，看看會發生什麼。

---

### **模組二：AI 橡皮擦與畫筆**

**學習目標**：學會使用 `inpaint` 工具，對圖像的局部進行修改或移除。

**關鍵技能**：理解 `prompt` 如何與遮罩 (mask) 區域協同工作。

`inpaint` 的 `prompt` 描述的是你**希望在被選取區域內看到什麼**，而不是你想要移除什麼。

**實踐練習 2.1：讓物件消失**

1.  **準備**：假設你有一張名為 `living_room.jpg` 的客廳照片，中間有一張咖啡桌。
2.  **指令**：請 Manus 使用 Flux 的 `inpaint` 工具，移除照片中央的咖啡桌。
3.  **工具調用**：

    ```json
    {
      "tool": "inpaint",
      "arguments": {
        "image": "/path/to/living_room.jpg",
        "prompt": "a clean wooden floor",
        "mask_shape": "rectangle",
        "position": "center"
      }
    }
    ```

4.  **挑戰**：在同一個客廳照片中，嘗試使用 `inpaint` 在牆上添加一扇「a round window showing a forest outside」。

---

### **模組三：風格的融合**

**學習目標**：學會使用 `img2img` 工具，將一張圖像的風格應用到另一張圖像上。

**關鍵技能**：掌握 `strength` 參數的平衡藝術。

`strength` 決定了 AI 在多大程度上「尊重」你的原始圖像結構。較低的值（如 0.4）會保留更多原始結構，較高的值（如 0.8）則給予 AI 更多創作自由來匹配 `prompt` 和風格。

**實踐練習 3.1：當照片遇上名畫**

1.  **準備**：準備一張你自己的頭像照片 `my_photo.jpg` 和一張梵谷的《星夜》 `starry_night.jpg`。
2.  **指令**：請 Manus 使用 Flux，將《星夜》的風格應用到你的頭像上。
3.  **工具調用**：

    ```json
    {
      "tool": "img2img",
      "arguments": {
        "image": "/path/to/my_photo.jpg",
        "prompt": "A portrait of a person in the style of Van Gogh's Starry Night",
        "name": "van_gogh_portrait",
        "strength": 0.75
      }
    }
    ```

4.  **挑戰**：嘗試將 `strength` 的值調整為 `0.5`，比較兩次生成結果的差異。

---

### **模組四：精準控制你的創作**

**學習目標**：初步了解 `control` 工具如何透過結構來指導圖像生成。

**關鍵技能**：理解「以形控圖」的概念。

`control` 工具讓你可以超越文字，用視覺結構（如人體姿勢、建築線條）來命令 AI。這是通往專業級創作的門戶。

**實踐練習 4.1：擺個姿勢吧！**

1.  **準備**：假設你有一張名為 `dancing_pose.jpg` 的火柴人跳舞姿勢圖。
2.  **指令**：請 Manus 使用 Flux，生成一個穿著芭蕾舞裙的舞者，並讓她擺出和火柴人完全相同的姿勢。
3.  **工具調用**：

    ```json
    {
      "tool": "control",
      "arguments": {
        "type": "pose",
        "image": "/path/to/dancing_pose.jpg",
        "prompt": "A ballerina in a white tutu, dancing gracefully on a stage, dramatic lighting",
        "output": "ballerina.jpg"
      }
    }
    ```

4.  **挑戰**：使用同一個 `dancing_pose.jpg`，將 `prompt` 改為「An NFL player scoring a touchdown」，看看 Flux 如何詮釋同一個姿勢。

---

## 恭喜你！

你已經完成了 Flux 快速上手手冊的所有模組！你現在掌握了生成、修改、風格化和控制 AI 圖像的四大核心技能。不斷練習這些技巧，並嘗試將它們組合使用，你將能創造出無限可能。
