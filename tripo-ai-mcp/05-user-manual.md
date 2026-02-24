## 「Tripo AI」終端使用者操作手冊

作者：Joker

### 簡介

本手冊旨在引導終端使用者如何透過自然語言指令與 Manus 互動，以使用「Tripo AI」連結器的強大功能。您將學習如何下達指令，以及 Manus 如何將這些指令轉換為具體的工具調用，輕鬆實現從文字或圖片生成 3D 模型的魔法。

### 核心概念：從指令到行動

您與 Manus 的互動是基於自然語言的。您只需要用日常語言描述您想做什麼，Manus 就會分析您的意圖，並選擇最適當的工具來完成任務。以下是一些範例，展示了這個過程是如何運作的。

--- 

### 任務一：從文字描述建立一個 3D 模型

假設您想建立一個「科幻風格的無人機」模型。

**您的指令**：
> 「幫我用文字生成一個 3D 模型，我想要一個充滿未來感的科幻風格無人機。」

**Manus 的行動**：
Manus 理解到您想從文字描述建立一個 3D 模型，因此它會調用 `create_3d_model_from_text` 工具。同時，它會從您的描述中提取關鍵詞「科幻風格的無人機」作為模型的描述。

**工具調用**：
```json
{
  "tool": "create_3d_model_from_text",
  "describe_the_look_of_object": "A futuristic sci-fi drone"
}
```

**後續步驟**：
模型生成需要時間。在啟動任務後，Manus 會自動使用 `get_task_status` 工具來追蹤進度。一旦模型準備就緒，Manus 會使用 `import_tripo_glb_model` 工具將其匯入到您的 Blender 場景中。

--- 

### 任務二：從圖片建立一個 3D 模型

如果您有一張現成的圖片，例如一張椅子的照片，並想將其轉換為 3D 模型。

**您的指令**：
> 「我想從這張圖片建立一個 3D 模型：[https://example.com/my-chair-photo.jpg]」

**Manus 的行動**：
Manus 識別出您想從圖片建立模型，並找到了您提供的圖片 URL。因此，它會選擇 `create_3d_model_from_image` 工具。

**工具調用**：
```json
{
  "tool": "create_3d_model_from_image",
  "image": "https://example.com/my-chair-photo.jpg"
}
```

--- 

### 任務三：在場景中新增一個簡單物件

有時候您可能只需要一個簡單的形狀，例如一個紅色的球體。

**您的指令**：
> 「在場景中新增一個紅色的球體。」

**Manus 的行動**：
Manus 將這個任務分解為兩個步驟：
1.  建立一個球體。
2.  將其顏色設定為紅色。

因此，它會先後調用 `create_object` 和 `set_material` 工具。

**工具調用**：

1.  建立球體：
    ```json
    {
      "tool": "create_object",
      "type": "SPHERE"
    }
    ```
2.  設定顏色：
    ```json
    {
      "tool": "set_material",
      "object_name": "Sphere", // Manus 會使用上一步驟中建立的物件名稱
      "color": [1, 0, 0] // RGB 值中的紅色
    }
    ```

### 結論

正如您所見，您無需了解複雜的工具名稱或參數。只需用自然語言清晰地表達您的需求，Manus 就會為您處理剩下的技術細節。現在，就開始發揮您的想像力，用簡單的指令創造出令人驚嘆的 3D 世界吧！
