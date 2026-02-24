# 「Prisma Postgres」連結器學習手冊

**作者：Joker**

本手冊旨在幫助您快速上手「Prisma Postgres」連結器，並掌握其核心技能。我們將透過一系列清晰的學習目標和實踐練習，讓您在最短的時間內將此工具應用到您的日常工作中。

---

## 核心技能矩陣

| 技能領域 | 關鍵技能 | 對應工具 |
| :--- | :--- | :--- |
| **資料庫生命週期管理** | 創建、列出和刪除資料庫實例 | `CreateDatabaseTool`, `ListDatabasesTool`, `DeleteDatabaseTool` |
| **資料庫結構與查詢** | 探測資料庫結構、執行 SQL 查詢 | `IntrospectSchemaTool`, `ExecuteSqlQueryTool` |
| **連線與存取控制** | 創建、列出和刪除連接字串 | `CreateConnectionStringTool`, `ListConnectionStringsTool`, `DeleteConnectionStringTool` |
| **資料安全與災難恢復** | 創建備份、列出備份、從備份中恢復 | `CreateBackupTool`, `ListBackupsTool`, `CreateRecoveryTool` |

---

## 快速上手：15 分鐘挑戰

**目標**：在 15 分鐘內，完成一次完整的「建立資源 -> 使用資源 -> 清理資源」的生命週期操作，體驗連結器的核心價值。

### 第 1 步：查看您現有的戰場 (2 分鐘)

- **學習目標**：學會如何檢視您在 Prisma 平台上的現有資源。
- **練習指令**：「Manus，列出我所有的 Prisma 資料庫。」
- **底層工具**：`ListDatabasesTool`
- **成功標準**：您能看到一個列表，其中包含您帳戶下所有資料庫的名稱和 ID。

### 第 2 步：建立您的第一個靶機 (5 分鐘)

- **學習目標**：學會使用連結器創建一個新的資料庫實例。
- **練習指令**：「幫我建立一個名為 `my-first-db` 的 Prisma Postgres 資料庫。」
- **底層工具**：`CreateDatabaseTool` (示意)
- **成功標準**：您收到一個確認訊息，其中包含新資料庫 `my-first-db` 的唯一 ID。

### 第 3 步：取得進入靶機的鑰匙 (3 分鐘)

- **學習目標**：學會為您的新資料庫產生一個連接字串。
- **練習指令**：「給我 `my-first-db` 的連接字串。」
- **底層工具**：`CreateConnectionStringTool`
- **成功標準**：您得到一個可以用於連接資料庫的 `postgresql://` 格式的字串。

### 第 4 步：摧毀靶機，不留痕跡 (5 分鐘)

- **學習目標**：學會如何安全地刪除一個您不再需要的資料庫實例。
- **練習指令**：「幫我刪除 `my-first-db` 這個資料庫。」
- **底層工具**：`DeleteDatabaseTool`
- **成功標準**：您收到一個確認訊息，告知 `my-first-db` 已被成功刪除。您可以再次執行第 1 步的指令來驗證它是否已從列表中消失。

**恭喜！** 您已經在 15 分鐘內體驗了「Prisma Postgres」連結器最核心的生命週期管理能力。

---

## 關鍵技能培養

### 技能一：資料庫透視眼 (Schema Introspection)

- **學習目標**：能夠快速掌握任何一個現有 Postgres 資料庫的完整結構，並將其轉換為 Prisma Schema。
- **實踐場景**：您被指派維護一個您從未接觸過的舊專案。在開始修改任何程式碼之前，您需要先了解其資料庫的全貌。
- **練習指令**：「幫我分析一下 ID 為 `db_...` 的這個資料庫的結構。」
- **進階挑戰**：嘗試將輸出的 Prisma Schema 存為 `schema.prisma` 檔案，並執行 `npx prisma generate` 來產生 Prisma Client，感受完整的端到端工作流程。

### 技能二：SQL 任意門 (SQL Execution)

- **學習目標**：能夠在不離開 Manus 的情況下，對任何一個資料庫執行臨時的 SQL 查詢。
- **實踐場景**：線上服務出現問題，您懷疑是某張表的資料異常導致。您需要立即查詢該表的幾筆資料來進行驗證。
- **練習指令**：「在 `production-db` 上幫我執行 `SELECT * FROM \"Order\" ORDER BY \"createdAt\" DESC LIMIT 5;`」
- **進階挑戰**：嘗試執行一個帶有 `JOIN` 的複雜查詢，驗證連結器處理複雜 SQL 的能力。

### 技能三：時光回溯機 (Backup & Recovery)

- **學習目標**：掌握資料庫的備份與還原機制，為您的資料安全加上雙重保險。
- **實踐場景**：您即將進行一次重大的資料庫結構變更 (Migration)。在執行前，您需要先建立一個備份，以防萬一需要回滾。
- **練習指令**：
  1. 「為 `production-db` 建立一個備份。」 (使用 `CreateBackupTool`)
  2. 「列出 `production-db` 的所有備份。」 (使用 `ListBackupsTool`)
  3. (假設發生問題) 「從 ID 為 `backup_...` 的這個備份，幫我還原一個名為 `restored-db` 的新資料庫。」 (使用 `CreateRecoveryTool`)
- **進階挑戰**：思考如何將這個流程與您的 CI/CD 工具結合，在每次部署前自動執行資料庫備份任務。
