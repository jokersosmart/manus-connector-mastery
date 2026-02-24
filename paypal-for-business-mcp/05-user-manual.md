# Flux 終端使用者操作手冊

作者：Joker

## 簡介

歡迎使用 Flux！這是一本為終端使用者設計的操作手冊。在這裡，你不需要學習複雜的程式碼或參數。你只需要用最自然的語言告訴 Manus 你想要什麼，剩下的交給我們。本手冊將透過一系列任務導向的範例，展示你如何與 Manus 協作，釋放 Flux 的強大圖像能力。

--- 

### **任務一：我想創造一張不存在的風景畫**

這是最基礎也最神奇的任務。你只需要在腦中構思一幅畫面，然後用文字描述出來。

**你的指令**：

> 「幫我畫一張圖：一隻巨大的、由水晶構成的狐狸，正沉睡在一片發光的魔法森林裡。風格要像日本動畫一樣，要橫的，適合當電腦桌布。」

**Manus 如何理解與執行**：

Manus 會分析你的需求，並將其轉換為對 Flux 連結器的精確調用。

1.  **識別核心內容**：Manus 抓取到主體「水晶狐狸」、場景「發光魔法森林」和風格「日本動畫」。
2.  **組合 Prompt**：它會將這些元素組合成一個強大的 Prompt: `A giant fox made of crystal, sleeping in a glowing magical forest, in the style of Japanese anime`。
3.  **確定技術參數**：你提到「橫的」、「電腦桌布」，Manus 會將其解讀為 `aspect_ratio: "16:9"`。
4.  **調用工具**：最終，Manus 會執行以下的 Flux 命令：

    ```json
    {
      "tool": "generate",
      "arguments": {
        "prompt": "A giant fox made of crystal, sleeping in a glowing magical forest, in the style of Japanese anime",
        "aspect_ratio": "16:9"
      }
    }
    ```

--- 

### **任務二：我想把我的自拍照變成太空人**

這個任務需要你提供一張現有的圖片，並告訴 Manus 你想如何改造它。

**你的指令**：

> 「這是我的一張自拍照 (`my_face.jpg`)。你能不能用這張照片，把我變成一個穿著未來感太空衣的太空人？讓我看起來像是站在火星上。風格可以多一點科幻感。」

**Manus 如何理解與執行**：

1.  **識別任務類型**：Manus 知道這是一個「圖生圖」的任務，因為你提供了一張輸入圖片。
2.  **定位輸入檔案**：它會找到你提到的 `my_face.jpg`。
3.  **組合 Prompt**：它將你的描述「穿著未來感太空衣的太空人」、「站在火星上」、「科幻感」組合成 Prompt: `A portrait of an astronaut in a futuristic spacesuit, standing on Mars, science fiction style`。
4.  **調用工具**：Manus 會選擇 `img2img` 工具，並將你的照片作為參考。

    ```json
    {
      "tool": "img2img",
      "arguments": {
        "image": "/path/to/my_face.jpg",
        "prompt": "A portrait of an astronaut in a futuristic spacesuit, standing on Mars, science fiction style",
        "name": "astronaut_me",
        "strength": 0.8 
      }
    }
    ```

--- 

### **任務三：我想移除照片裡的路人**

這是一個非常實用的修圖任務。你只需要告訴 Manus 你想讓什麼東西「消失」。

**你的指令**：

> 「我有一張在海灘拍的照片 (`beach_day.jpg`)，但背景裡有幾個我不認識的人，很礙眼。你能不能幫我把他們都弄掉，讓背景只剩下沙灘和大海？」

**Manus 如何理解與執行**：

1.  **識別任務類型**：Manus 識別出這是「局部修復」或「物件移除」的需求。
2.  **理解修改內容**：你明確指出要移除「背景裡的人」，並希望留下的內容是「沙灘和大海」。
3.  **制定策略**：Manus 會使用 `inpaint` 工具。它會（在內部）標記出有人物出現的區域作為遮罩 (mask)。
4.  **組合 Prompt**：關鍵的一步來了！`inpaint` 的 Prompt 應該描述**你希望遮罩區域被什麼填滿**。所以，Manus 會使用 `a clean beach and ocean waves` 作為 Prompt。
5.  **調用工具**：

    ```json
    {
      "tool": "inpaint",
      "arguments": {
        "image": "/path/to/beach_day.jpg",
        "prompt": "a clean beach and ocean waves" 
      }
    }
    ```

--- 

### **任務四：我想讓這個火柴人變成一個真的騎士**

這是一個進階任務，你可以提供一個非常簡單的結構圖，讓 AI 為其填充細節和真實感。

**你的指令**：

> 「我畫了一個火柴人正在騎馬的簡筆畫 (`knight_pose.jpg`)。你能不能根據這個姿勢，畫一個全身穿著銀色盔甲、騎著一匹白色戰馬的中世紀騎士？背景要是一片廣闊的草原。」

**Manus 如何理解與執行**：

1.  **識別任務類型**：Manus 看到你提供的是一張姿勢圖，並要求根據它來創作，因此判斷這是一個「結構控制」任務。
2.  **確定控制類型**：因為是火柴人姿勢圖，Manus 會選擇 `pose` 作為控制類型。
3.  **組合 Prompt**：它會將你的詳細描述組合成 Prompt: `A medieval knight in full silver armor, riding a white warhorse, in a vast grassland`。
4.  **調用工具**：Manus 會選擇最強大的 `control` 工具。

    ```json
    {
      "tool": "control",
      "arguments": {
        "type": "pose",
        "image": "/path/to/knight_pose.jpg",
        "prompt": "A medieval knight in full silver armor, riding a white warhorse, in a vast grassland"
      }
    }
    ```

## 結語

正如你所見，你不需要成為 Flux 專家。你只需要成為一個**好的溝通者**。清楚地告訴 Manus 你的目標、你擁有的材料（圖片）以及你期望的結果，Manus 和 Flux 就會成為你最強大的創意實現工具。
