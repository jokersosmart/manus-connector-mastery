'''
# 「Tripo AI」連結器使用說明報告

作者：Joker

## 總覽

本文件提供了「Tripo AI」MCP 連結器中所有可用工具的詳細使用說明。每個工具都包含其功能說明、參數、使用範例和預期輸出。

## 工具列表

### 1. `get_scene_info`

- **功能說明**：取得當前 Blender 場景的詳細資訊。
- **參數**：無
- **使用範例**：
  ```json
  {
    "tool": "get_scene_info"
  }
  ```
- **預期輸出**：一個包含場景資訊的 JSON 字串。

### 2. `get_object_info`

- **功能說明**：取得 Blender 場景中特定物件的詳細資訊。
- **參數**：

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `object_name` | `string` | 要取得資訊的物件名稱。 |

- **使用範例**：
  ```json
  {
    "tool": "get_object_info",
    "object_name": "Cube"
  }
  ```
- **預期輸出**：一個包含物件資訊的 JSON 字串。

### 3. `create_object`

- **功能說明**：在 Blender 場景中建立一個新物件。
- **參數**：

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `type` | `string` | 物件類型 (CUBE, SPHERE, CYLINDER, PLANE, CONE, TORUS, EMPTY, CAMERA, LIGHT)。預設為 "CUBE"。 |
| `name` | `string` | 物件的選用名稱。 |
| `location` | `array` | 選用的 [x, y, z] 位置座標。 |
| `rotation` | `array` | 選用的 [x, y, z] 弧度旋轉。 |
| `scale` | `array` | 選用的 [x, y, z] 縮放因子。 |

- **使用範例**：
  ```json
  {
    "tool": "create_object",
    "type": "SPHERE",
    "name": "MySphere",
    "location": [1, 2, 3]
  }
  ```
- **預期輸出**：一個確認物件已建立的訊息。

### 4. `modify_object`

- **功能說明**：修改 Blender 場景中的現有物件。
- **參數**：

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `name` | `string` | 要修改的物件名稱。 |
| `location` | `array` | 選用的 [x, y, z] 位置座標。 |
| `rotation` | `array` | 選用的 [x, y, z] 弧度旋轉。 |
| `scale` | `array` | 選用的 [x, y, z] 縮放因子。 |
| `visible` | `boolean` | 設定可見性的選用布林值。 |

- **使用範例**：
  ```json
  {
    "tool": "modify_object",
    "name": "MySphere",
    "scale": [2, 2, 2]
  }
  ```
- **預期輸出**：一個確認物件已修改的訊息。

### 5. `delete_object`

- **功能說明**：從 Blender 場景中刪除一個物件。
- **參數**：

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `name` | `string` | 要刪除的物件名稱。 |

- **使用範例**：
  ```json
  {
    "tool": "delete_object",
    "name": "MySphere"
  }
  ```
- **預期輸出**：一個確認物件已刪除的訊息。

### 6. `set_material`

- **功能說明**：為物件設定或建立一個材質。
- **參數**：

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `object_name` | `string` | 要套用材質的物件名稱。 |
| `material_name` | `string` | 要使用或建立的材質的選用名稱。 |
| `color` | `array` | 選用的 [R, G, B] 顏色值 (0.0-1.0)。 |

- **使用範例**：
  ```json
  {
    "tool": "set_material",
    "object_name": "Cube",
    "color": [1, 0, 0]
  }
  ```
- **預期輸出**：一個確認材質已套用的訊息。

### 7. `execute_blender_code`

- **功能說明**：在 Blender 中執行任意 Python 程式碼。
- **參數**：

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `code` | `string` | 要執行的 Python 程式碼。 |

- **使用範例**：
  ```json
  {
    "tool": "execute_blender_code",
    "code": "import bpy; bpy.ops.mesh.primitive_monkey_add()"
  }
  ```
- **預期輸出**：一個確認程式碼已執行的訊息。

### 8. `create_3d_model_from_text`

- **功能說明**：使用 Tripo API 從文字描述建立 3D 模型。
- **參數**：

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `describe_the_look_of_object` | `string` | 要產生的物件的詳細描述。 |
| `face_limit` | `integer` | 模型中的最大面數。 |

- **使用範例**：
  ```json
  {
    "tool": "create_3d_model_from_text",
    "describe_the_look_of_object": "A red sports car"
  }
  ```
- **預期輸出**：一個包含任務 ID 和狀態檢查說明的字典。

### 9. `create_3d_model_from_image`

- **功能說明**：使用 Tripo API 從圖片建立 3D 模型。
- **參數**：

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `image` | `string` | 圖片檔案的本地路徑或 URL。 |
| `face_limit` | `integer` | 模型中的最大面數。 |

- **使用範例**：
  ```json
  {
    "tool": "create_3d_model_from_image",
    "image": "https://example.com/car.jpg"
  }
  ```
- **預期輸出**：一個包含任務 ID 和狀態檢查說明的字典。

### 10. `get_task_status`

- **功能說明**：取得 3D 模型產生任務的狀態。
- **參數**：

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `task_id` | `string` | 要檢查的任務 ID。 |

- **使用範例**：
  ```json
  {
    "tool": "get_task_status",
    "task_id": "some-task-id"
  }
  ```
- **預期輸出**：一個包含任務狀態和其他資訊的字典。

### 11. `import_tripo_glb_model`

- **功能說明**：將 GLB 模型從 URL 匯入 Blender 場景。
- **參數**：

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `glb_url` | `string` | GLB 模型檔案的下載 URL。 |

- **使用範例**：
  ```json
  {
    "tool": "import_tripo_glb_model",
    "glb_url": "https://example.com/model.glb"
  }
  ```
- **預期輸出**：一個確認模型已匯入的訊息。
'''
