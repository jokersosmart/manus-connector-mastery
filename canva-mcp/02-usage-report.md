# 「Canva」連結器使用說明報告

作者：Joker

---

本文將詳細介紹 Manus MCP「Canva」連結器的所有可用工具、其參數、功能、使用範例及預期輸出，旨在幫助使用者充分利用此連結器，將 Canva 的強大設計能力無縫整合到其自動化工作流程中。

## 總覽

Canva 連結器提供了一系列工具，讓使用者可以透過 Manus 的自然語言指令，以程式化方式操作 Canva 的核心功能。這包括建立設計、管理資產、添加評論、匯出成品等，從而實現設計流程的自動化與效率提升。

---

## 工具詳解

### 1. `create_design`

**功能說明**

此工具用於在 Canva 中建立一個新的設計。使用者可以選擇從一個現有的 Canva 範本開始，也可以建立一個指定尺寸的空白設計。

**參數**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `template_id` | `string` | 否 | 要使用的 Canva 範本 ID。如果未提供，則會建立空白設計。 |
| `width` | `integer` | 否 | 設計的寬度（像素）。如果未提供 `template_id`，則此為必填項。 |
| `height` | `integer` | 否 | 設計的高度（像素）。如果未提供 `template_id`，則此為必填項。 |
| `title` | `string` | 否 | 新設計的標題。 |

**使用範例**

> 「用 Canva 建立一個 1080x1080 像素的新設計，標題是『社群媒體貼文』。」

**預期輸出**

一個 JSON 物件，包含新建立設計的 ID 和預覽連結。

```json
{
  "design_id": "dae-abc-123",
  "preview_url": "https://www.canva.com/design/dae-abc-123/view"
}
```

### 2. `upload_asset`

**功能說明**

將本地的圖片或影片檔案上傳到使用者的 Canva 媒體庫中，以便在設計中使用。

**參數**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `file_path` | `string` | 是 | 要上傳的本地檔案路徑。 |
| `folder_id` | `string` | 否 | 要將資產上傳到的 Canva 資料夾 ID。 |

**使用範例**

> 「把 `/path/to/my-logo.png` 這個檔案上傳到我的 Canva 媒體庫。」

**預期輸出**

一個 JSON 物件，包含新上傳資產的 ID。

```json
{
  "asset_id": "asset-xyz-789"
}
```

### 3. `export_design`

**功能說明**

將指定的 Canva 設計匯出為特定檔案格式（如 PNG, JPG, PDF）。

**參數**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `design_id` | `string` | 是 | 要匯出的 Canva 設計 ID。 |
| `format` | `string` | 是 | 匯出的檔案格式。支援 `PNG`, `JPG`, `PDF`。 |
| `quality` | `integer` | 否 | 匯出圖片的品質 (1-100)，僅適用於 JPG。 |

**使用範例**

> 「將 Canva 設計 `dae-abc-123` 匯出為 PNG 檔案。」

**預期輸出**

一個 JSON 物件，包含匯出工作的 ID。使用者可以後續使用此 ID 來查詢匯出狀態並下載檔案。

```json
{
  "export_job_id": "export-job-456"
}
```

### 4. `autofill_design`

**功能說明**

使用提供的資料自動填入 Canva 設計範本中的特定元素。這對於批次產生客製化設計（如名片、證書）非常有用。

**參數**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `template_id` | `string` | 是 | 要填入資料的 Canva 範本 ID。 |
| `data` | `object` | 是 | 一個 JSON 物件，其鍵值對應範本中的變數名稱和要填入的內容。 |

**使用範例**

> 「使用範本 `template-business-card`，並用以下資料填入設計：姓名是『Joker』，職位是『AI Engineer』。」

**預期輸出**

一個 JSON 物件，包含新產生設計的 ID 和預覽連結。

```json
{
  "design_id": "dae-def-456",
  "preview_url": "https://www.canva.com/design/dae-def-456/view"
}
```

### 5. `add_comment`

**功能說明**

在指定的 Canva 設計中新增一則評論。

**參數**

| 參數 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `design_id` | `string` | 是 | 要新增評論的 Canva 設計 ID。 |
| `comment_text` | `string` | 是 | 評論的內容。 |

**使用範例**

> 「在 Canva 設計 `dae-abc-123` 上新增一則評論：『請確認這個顏色是否符合品牌規範』。」

**預期輸出**

一個 JSON 物件，包含新評論的 ID。

```json
{
  "comment_id": "comment-uvw-123"
}
```

---

## 結論

Canva 連結器旨在將設計流程自動化，並將其與其他工具和系統整合。透過上述工具，使用者可以大幅減少手動操作，專注於更有創造性的任務。隨著 Canva API 的不斷發展，此連結器也將持續更新，提供更多強大的功能。
