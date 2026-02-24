# 「Prisma Postgres」連結器使用說明報告

**作者：Joker**

本報告提供「Prisma Postgres」連結器中所有可用工具的詳細說明，包含其功能、參數、使用範例及預期輸出，旨在幫助開發者快速理解並有效利用此連結器。

---

## 總覽

連結器提供了一系列圍繞 Prisma Postgres 資料庫生命週期管理的工具，涵蓋了從資料庫創建、結構探測、查詢、備份到刪除的全套自動化操作。

| 工具名稱 | 功能簡述 |
| :--- | :--- |
| `ListDatabasesTool` | 取得您工作區內所有可用的 Prisma Postgres 資料庫實例。 |
| `IntrospectSchemaTool` | 探測指定資料庫的結構 (Schema)，並以 Prisma Schema Language (PSL) 格式回傳。 |
| `ExecuteSqlQueryTool` | 在指定的資料庫上執行原生 SQL 查詢。 |
| `CreateConnectionStringTool` | 為指定的資料庫建立一個新的連接字串。 |
| `ListConnectionStringsTool` | 列出指定資料庫所有已存在的連接字串。 |
| `DeleteConnectionStringTool` | 刪除一個指定的連接字串。 |
| `CreateBackupTool` | 為指定的資料庫建立一個即時備份。 |
| `ListBackupsTool` | 列出指定資料庫所有可用的備份。 |
| `CreateRecoveryTool` | 從一個指定的備份中，將資料庫恢復到一個全新的實例。 |
| `DeleteDatabaseTool` | 永久刪除一個指定的資料庫實例。 |

---

## 工具詳解

### 1. `ListDatabasesTool`

- **功能**：取得您工作區內所有可用的 Prisma Postgres 資料庫實例。
- **參數**：無
- **使用範例**：
  ```json
  {
    "tool_name": "ListDatabasesTool",
    "parameters": {}
  }
  ```
- **預期輸出**：
  ```json
  {
    "result": [
      {
        "id": "db_clxm8p4a1000008l9g1j2h3k4",
        "name": "production-database",
        "createdAt": "2024-10-26T10:00:00Z"
      },
      {
        "id": "db_clxna9b21000108l9e7f8g9h0",
        "name": "staging-database",
        "createdAt": "2024-11-15T14:30:00Z"
      }
    ]
  }
  ```

### 2. `IntrospectSchemaTool`

- **功能**：探測指定資料庫的結構 (Schema)，並以 Prisma Schema Language (PSL) 格式回傳。
- **參數**：
  - `database_id` (string, 必要): 目標資料庫的唯一識別碼。
- **使用範例**：
  ```json
  {
    "tool_name": "IntrospectSchemaTool",
    "parameters": {
      "database_id": "db_clxm8p4a1000008l9g1j2h3k4"
    }
  }
  ```
- **預期輸出**：
  ```json
  {
    "result": {
      "schema": "datasource db {\n  provider = \"postgresql\"\n  url      = env(\"DATABASE_URL\")\n}\n\ngenerator client {\n  provider = \"prisma-client-js\"\n}\n\nmodel User {\n  id    Int     @id @default(autoincrement())\n  email String  @unique\n  name  String?\n  posts Post[]\n}\n\nmodel Post {\n  id        Int      @id @default(autoincrement())\n  title     String\n  content   String?\n  published Boolean  @default(false)\n  author    User     @relation(fields: [authorId], references: [id])\n  authorId  Int\n}"
    }
  }
  ```

### 3. `ExecuteSqlQueryTool`

- **功能**：在指定的資料庫上執行原生 SQL 查詢。
- **參數**：
  - `database_id` (string, 必要): 目標資料庫的唯一識別碼。
  - `query` (string, 必要): 要執行的原生 SQL 查詢語句。
- **使用範例**：
  ```json
  {
    "tool_name": "ExecuteSqlQueryTool",
    "parameters": {
      "database_id": "db_clxm8p4a1000008l9g1j2h3k4",
      "query": "SELECT COUNT(*) FROM \"User\";"
    }
  }
  ```
- **預期輸出**：
  ```json
  {
    "result": [
      {
        "count": 1250
      }
    ]
  }
  ```

... (其餘工具的詳細說明將遵循相同格式陸續完成)

### 4. `CreateConnectionStringTool`

- **功能**：為指定的資料庫建立一個新的連接字串。
- **參數**：
  - `database_id` (string, 必要): 目標資料庫的唯一識別碼。
- **使用範例**：
  ```json
  {
    "tool_name": "CreateConnectionStringTool",
    "parameters": {
      "database_id": "db_clxm8p4a1000008l9g1j2h3k4"
    }
  }
  ```
- **預期輸出**：
  ```json
  {
    "result": {
      "connection_string": "postgresql://user:password@host:port/database?sslmode=require"
    }
  }
  ```
