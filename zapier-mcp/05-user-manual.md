# Vercel MCP 連結器使用者操作手冊

**作者：Joker**

## 前言

本手冊旨在引導終端使用者如何透過 Manus，以自然的語言與 Vercel 平台進行互動。Manus MCP（模型情境協議）的 Vercel 連結器將 Vercel 的強大功能，從複雜的指令和儀表板點擊中解放出來，讓您能用對話的方式，完成專案的部署、查詢與管理。

> **重要提示：** 目前，名為「vercel」的 MCP 伺服器尚未啟用，因此本手冊中的工具調用範例均為**基於設計理念的示意**。待連結器正式上線後，實際的工具名稱與參數可能會有所不同。

## 核心概念：從自然語言到 Vercel API

您無需學習 Vercel 的 API 或指令列工具。您只需用日常語言向 Manus 提出您的需求，Manus 便會理解您的意圖，並在背景執行對應的 Vercel MCP 工具調用。

**運作流程如下：**

1.  **您下達指令：** 您用中文或英文說出您的需求，例如：「幫我列出所有在 Vercel 上的專案」。
2.  **Manus 理解意圖：** Manus 的自然語言理解 (NLU) 模型會分析您的指令，判斷您想要執行的操作是「列出專案」。
3.  **轉換為工具調用：** Manus 將您的意圖轉換為一個精確的 `manus-mcp-cli` 工具調用指令。
4.  **執行並回傳結果：** 指令被執行，Vercel MCP 連結器與 Vercel API 互動，然後將結果以人類可讀的格式呈現給您。

---

## 任務導向範例

以下將透過一系列常見的開發任務，展示您如何透過自然語言與 Vercel 互動。

### 任務一：列出所有專案

當您想快速概覽您在 Vercel 上託管的所有專案時，您可以這樣說：

*   **您的指令：** 「我在 Vercel 上有哪些專案？」 或 「List all my Vercel projects.」

*   **Manus 的轉換 (示意)：**

    ```bash
    manus-mcp-cli tool call vercel.projects.list --server vercel --input '{}'
    ```

*   **說明：** Manus 會調用 `vercel.projects.list` 工具，該工具不需要任何參數，它會向 Vercel API 請求您的專案列表，並將專案名稱、最新部署狀態、網域等資訊整理後回報給您。

### 任務二：部署一個新專案

假設您在 GitHub 上有一個名為 `my-awesome-app` 的新專案，想要將其部署到 Vercel。

*   **您的指令：** 「幫我把 GitHub 上的 `jokersosmart/my-awesome-app` 這個專案部署到 Vercel。」

*   **Manus 的轉換 (示意)：**

    ```bash
    manus-mcp-cli tool call vercel.deployments.create --server vercel --input '{
      "git_source": {
        "type": "github",
        "owner": "jokersosmart",
        "repo": "my-awesome-app"
      }
    }'
    ```

*   **說明：** Manus 會識別出 Git 倉庫的擁有者 (`jokersosmart`) 和名稱 (`my-awesome-app`)，並將這些資訊作為參數傳遞給 `vercel.deployments.create` 工具。Vercel 連結器會據此啟動一個新的部署流程。

### 任務三：查詢特定專案的部署狀態

您想確認 `my-awesome-app` 這個專案的最新部署情況。

*   **您的指令：** 「查詢 `my-awesome-app` 的最新部署狀態。」

*   **Manus 的轉換 (示意)：**

    ```bash
    manus-mcp-cli tool call vercel.deployments.get --server vercel --input '{
      "project_name": "my-awesome-app",
      "latest": true
    }'
    ```

*   **說明：** Manus 會使用 `vercel.deployments.get` 工具，並傳入 `project_name` 和 `latest: true` 參數，以獲取該專案最新一次部署的詳細資訊，如部署狀態（成功、失敗、建置中）、部署時間、預覽 URL 等。

### 任務四：為專案新增一個環境變數

您需要為您的 `my-awesome-app` 專案新增一個用於正式環境 (Production) 的 API 金鑰。

*   **您的指令：** 「為 `my-awesome-app` 專案新增一個正式環境的環境變數，名稱是 `API_KEY`，值是 `supersecretkey123`。」

*   **Manus 的轉換 (示意)：**

    ```bash
    manus-mcp-cli tool call vercel.env.add --server vercel --input '{
      "project_name": "my-awesome-app",
      "key": "API_KEY",
      "value": "supersecretkey123",
      "target": ["production"]
    }'
    ```

*   **說明：** Manus 會解析出所有必要的資訊——專案名稱、變數鍵、變數值以及目標環境（`production`），並調用 `vercel.env.add` 工具來完成操作。這避免了您手動登入儀表板尋找設定頁面的麻煩。

## 結語

Vercel MCP 連結器的目標是將強大的 Vercel 平台，無縫融入到您的日常對話與工作流程中。透過自然語言，您可以更直觀、更高效地管理您的前端專案，將寶貴的時間和精力，專注於真正重要的程式碼創造上。敬請期待 Vercel MCP 連結器的正式上線！
