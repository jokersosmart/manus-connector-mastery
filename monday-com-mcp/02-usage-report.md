_注意：由於無法直接存取「Neon」MCP 連結器，本使用說明報告是基於公開技術文件和遞迴分析所推斷出的工具集。所有工具、參數和範例均為設想，旨在模擬一個以使用者意圖為中心的理想連結器。作者：Joker_

# 「Neon」連結器使用說明報告

本報告詳細介紹了 Manus MCP「Neon」連結器的所有可用工具、參數、功能及使用範例。此連結器旨在將 Neon 強大的 Serverless PostgreSQL 功能（特別是資料庫分支）與 Manus Agent 的自然語言理解能力相結合，提供一個以開發者工作流為中心的自動化資料庫管理體驗。

## 核心設計理念

此連結器的工具設計遵循「意圖大於功能」的原則，將多個底層 API 操作封裝成單一、高層次的指令，以匹配開發者的真實需求，從而降低心智負擔，提升開發效率。

---

## 可用工具詳解

### 1. `setup_dev_environment`

**功能說明**

此工具為開發者一鍵式建立一個全新的、隔離的開發環境。它會自動處理專案建立（如果需要）、從主幹分支建立一個新的開發分支，並生成對應的資料庫連線端點。這是開始新功能開發或錯誤修復時最常用的指令。

**參數**

| 參數名 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `project_name` | string | 是 | 指定要操作的 Neon 專案名稱。 |
| `branch_name` | string | 是 | 要建立的新開發分支的名稱，建議與 Git 分支對應。 |
| `from_branch` | string | 否 | 指定從哪個父分支建立新分支。如果未提供，預設從專案的主分支建立。 |

**使用範例**

> 「幫我在 `my-app` 專案下，從 `main` 分支建立一個名為 `feature/new-auth` 的開發環境。」

**預期輸出**

工具執行成功後，將返回一個包含新環境詳細資訊的 JSON 物件，其中最重要的是可以直接在應用程式中使用的資料庫連線字串。

```json
{
  "status": "success",
  "project_name": "my-app",
  "branch_name": "feature/new-auth",
  "branch_id": "br-sparse-brook-123456",
  "connection_string": "postgres://joker:AbC_123@ep-tight-limit-456789.us-east-2.aws.neon.tech/neondb?sslmode=require"
}
```

### 2. `create_test_branch_from_prod`

**功能說明**

在需要對生產資料進行測試或除錯的場景下，此工具能安全地從生產資料庫分支建立一個包含完整資料副本的隔離測試分支。這對於重現特定錯誤或進行效能測試至關重要，且完全不影響生產環境。

**參數**

| 參數名 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `project_name` | string | 是 | 指定要操作的 Neon 專案名稱。 |
| `prod_branch` | string | 是 | 生產環境所在的分支名稱，例如 `main` 或 `production`。 |
| `test_branch_name` | string | 是 | 要建立的測試分支的名稱，例如 `hotfix/bug-123`。 |

**使用範例**

> 「為了除錯，請幫我在 `my-app` 專案裡，從 `production` 分支複製一份資料，建立一個叫 `hotfix/user-login-issue` 的測試分支。」

**預期輸出**

返回新建立的測試分支的資訊，包含其獨立的連線字串。

```json
{
  "status": "success",
  "project_name": "my-app",
  "test_branch_name": "hotfix/user-login-issue",
  "parent_branch": "production",
  "connection_string": "postgres://joker:XyZ_789@ep-wandering-leaf-987654.us-east-2.aws.neon.tech/neondb?sslmode=require"
}
```

### 3. `get_connection_string`

**功能說明**

一個簡單的輔助工具，用於快速查詢指定資料庫分支的連線字串。當開發者需要在不同的分支環境之間切換時，無需再去 Neon 控制台尋找，可以直接透過自然語言獲取。

**參數**

| 參數名 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `project_name` | string | 是 | 指定要操作的 Neon 專案名稱。 |
| `branch_name` | string | 是 | 要查詢其連線字串的分支名稱。 |

**使用範例**

> 「我需要 `my-app` 專案下 `staging` 分支的資料庫連線字串。」

**預期輸出**

直接返回一個包含所請求連線字串的 JSON 物件。

```json
{
  "project_name": "my-app",
  "branch_name": "staging",
  "connection_string": "postgres://joker:Def_456@ep-restless-shape-112233.us-east-2.aws.neon.tech/neondb?sslmode=require"
}
```

### 4. `cleanup_stale_branches`

**功能說明**

此工具旨在幫助使用者管理和控制成本。它能找出並刪除超過指定天數未被使用的非主幹開發分支。分支雖然建立成本極低，但長期累積仍會佔用儲存空間。定期清理是維持專案整潔和成本效益的最佳實踐。

**參數**

| 參數名 | 類型 | 是否必須 | 說明 |
| :--- | :--- | :--- | :--- |
| `project_name` | string | 是 | 指定要操作的 Neon 專案名稱。 |
| `days_inactive` | integer | 是 | 定義「過期」的標準，即分支處於非活躍狀態的天數。 |
| `exclude_branches` | array | 否 | 一個包含分支名稱的陣列，用於保護特定分支（如 `main`, `staging`, `production`）不被自動刪除。 |

**使用範例**

> 「清理一下 `my-app` 專案，把所有超過 14 天沒用的分支都刪掉，但是不要動 `main` 和 `staging` 分支。」

**預期輸出**

返回一個報告，列出被成功刪除的分支以及在刪除過程中遇到的任何錯誤。

```json
{
  "status": "success",
  "project_name": "my-app",
  "deleted_branches": [
    "feature/old-feature-1",
    "dev/test-xyz",
    "feature/abandoned-idea"
  ],
  "protected_branches": [
    "main",
    "staging"
  ],
  "message": "Successfully deleted 3 stale branches."
}
```
