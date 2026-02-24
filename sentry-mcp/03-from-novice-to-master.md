# Sentry 連結器：從新手到精通指南

**作者：Joker**

---

## 前言：您的自動化錯誤監控助理

歡迎來到 Sentry 連結器的世界。這個強大的工具將 Sentry 的錯誤追蹤能力與 Manus 的對話式 AI 操作無縫結合，讓您能夠用自然語言即時查詢、管理和自動化您的錯誤處理工作流。本指南將帶您從基礎概念開始，逐步深入，最終掌握利用此連結器提升團隊生產力的實戰技巧，成為真正的 Sentry on Manus 大師。

## 第一章：基礎入門 (新手)

本章節將為您打下堅實的基礎，了解 Sentry 的核心概念以及如何發出您的第一個指令。

### 1.1 理解 Sentry 核心概念

在開始之前，讓我們先對齊幾個 Sentry 的關鍵術語：

- **Event (事件)**：每一次錯誤的發生都是一個事件。
- **Issue (問題)**：Sentry 會將相似的事件（Events）分組為一個單一的可操作項目，稱為問題。
- **Project (專案)**：您在 Sentry 中監控的每一個應用程式或服務都是一個專案。
- **Organization (組織)**：您的公司或團隊在 Sentry 中的頂層容器，包含多個專案和團隊成員。

### 1.2 準備工作：取得授權金鑰 (Auth Token)

要讓 Manus 操作您的 Sentry 帳戶，您需要一個授權金鑰。

1.  登入您的 Sentry 帳戶。
2.  前往 **User Settings > API > Auth Tokens**。
3.  點擊 **"Create New Token"**。
4.  賦予該 Token 至少 `event:read` 和 `event:write` 的權限。
5.  將產生的 Token 儲存到 Manus 的安全金鑰儲存區。

### 1.3 您的第一個指令：列出未解決的問題

萬事起頭難，讓我們從一個最常見的需求開始：查看專案中還沒解決的問題。

**試著對 Manus 說：**
> "Manus, list the unresolved issues in my 'backend-api' project for the 'my-company' organization on Sentry."

Manus 將會調用 `list_project_issues` 工具，並回傳一個列表，讓您對當前的問題狀況一目了然。

## 第二章：掌握核心功能 (勝任)

您已經學會了如何查詢問題，現在讓我們深入了解如何調查和管理它們。

### 2.1 深入調查：取得問題的詳細資訊

當您看到一個感興趣的問題時，下一步就是獲取更多細節。

**接著說：**
> "Get me the details for issue MY-COMPANY-A1."

Manus 會使用 `get_issue_details` 工具，提供關於這個問題的全部資訊，包括錯誤訊息、堆疊追蹤、影響的使用者數量等。

### 2.2 採取行動：解決與忽略

調查完畢後，您需要更新問題的狀態。

- **如果問題已修復：**
  > "Resolve Sentry issue MY-COMPANY-A1."
- **如果這是個已知的、暫時不處理的問題：**
  > "Ignore issue MY-COMPANY-A1 for now."

Manus 會調用 `update_issue` 工具來更新 Sentry 中的狀態，讓您的團隊成員知道最新進度。

### 2.3 精準搜尋：活用查詢參數

`list_project_issues` 的強大之處在於其 `query` 參數。您可以像在 Sentry 官網搜尋一樣，使用結構化查詢。

- **查詢被分配給您的問題：**
  > "Show me issues assigned to me in the 'frontend-app' project."
- **查詢特定瀏覽器發生的錯誤：**
  > "List issues from the last 24 hours in the 'frontend-app' project where the browser is Chrome."

## 第三章：進階技巧 (精通)

真正的威力來自於將工具組合，建立自動化流程。

### 3.1 自動化工作流：從錯誤到工單

想像一下，當 Sentry 出現一個新的嚴重錯誤時，自動在 Jira 中建立一張票證並指派給對應的工程師，同時在 Slack 中發送通知。

**您可以設計一個 Manus 工作流：**
1.  **觸發器**：設定一個排程任務，每 5 分鐘執行一次。
2.  **動作一**：使用 `sentry.list_project_issues` 查詢過去 5 分鐘內出現的、標記為 `level:fatal` 的新問題。
3.  **條件判斷**：如果列表不為空。
4.  **動作二**：遍歷列表中的每個問題，使用 `jira.create_ticket` 工具，將問題標題和連結填入描述，並根據問題標籤指派給特定人員。
5.  **動作三**：使用 `slack.send_message` 將新建立的 Jira 票證連結發送到 `#dev-alerts` 頻道。

### 3.2 主動式監控報告

您可以讓 Manus 成為您的數據分析師，每天早上定時整理一份 Sentry 報告。

**設計一個每日報告工作流：**
> "Manus, every morning at 9 AM, list the top 5 most frequent issues from the 'production-api' project in the last 24 hours and send the summary to the #daily-standup channel."

## 第四章：實戰應用場景 (大師)

讓我們看看在真實世界中，不同角色如何利用 Sentry 連結器發揮最大價值。

### 4.1 場景一：值班工程師 (On-Call Engineer)

半夜收到告警，值班工程師需要快速判斷問題的嚴重性。他可以立即對 Manus 說：
> "Show me the latest event for issue PROD-API-XYZ. How many users are affected?"

在幾秒鐘內，他就能得到關鍵資訊，判斷是需要立即修復，還是可以等到早上再處理。

### 4.2 場景二：產品經理 (Product Manager)

產品經理正在規劃下個季度的功能。他想知道哪些 Bug 最影響使用者體驗。
> "Manus, list all issues from the 'ios-app' project that have more than 100 user reports in the last 30 days, and sort them by the number of affected users."

這讓他能夠基於數據，決定 Bug 修復的優先級。

### 4.3 場景三：開發維運專家 (DevOps Specialist)

在 CI/CD 流程中，每次部署到預備環境 (Staging) 後，DevOps 專家可以加入一個自動化步驟：
> "Manus, wait 5 minutes, then check the 'api-staging' project for any new unresolved issues. If there are any, halt the deployment to production and send an alert to the dev team."

這建立了一道品質閘門，有效防止有問題的程式碼被部署到生產環境。

## 結語：您的潛力無窮

從簡單的查詢到複雜的跨工具自動化，Sentry 連結器為您提供了無限可能。精通的關鍵在於不斷思考：**「我目前手動在 Sentry 上做的哪些重複性工作，可以讓 Manus 為我代勞？」** 不斷實驗、組合，您將會發現一個前所未有的高效工作模式。
