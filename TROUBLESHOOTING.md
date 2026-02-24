# 故障排除日誌 (Troubleshooting Log)

> **作者**：Joker
> **日期**：2026-02-25

---

## 問題一：GitHub 身份驗證失敗 — 舊權杖失效

### 問題描述

在沙箱環境重置後，嘗試重新複製 GitHub 儲存庫時，遇到了持續性的身份驗證失敗。此問題阻礙了將本地完成的連接器文件推送到遠端儲存庫。

### 嘗試的解決方案與結果

| 步驟 | 方法 | 命令 | 結果 |
| :--- | :--- | :--- | :--- |
| 1 | `gh repo clone` | `gh repo clone jokersosmart/... /home/ubuntu/manus-mcp-mastery` | 反覆提示需要 `gh auth login`，CLI 未能自動使用環境中預設的權杖 |
| 2 | `gh auth login` (互動式) | `gh auth login` | 需要開啟瀏覽器輸入一次性驗證碼，在非互動式沙箱環境中超時失敗 |
| 3 | `gh auth login --with-token` | `echo $GH_TOKEN \| gh auth login --with-token` | 同樣生成需要瀏覽器驗證的代碼，超時失敗 |
| 4 | `git clone` (URL 嵌入權杖) | `git clone https://user:$GH_TOKEN@github.com/...` | 返回 `fatal: Authentication failed`，提示「無效的使用者名稱或權杖」 |

### 根本原因

綜合以上測試，問題的根本原因是：**環境中預設的 `GH_TOKEN` 已失效或過期**。第 4 步的測試是關鍵證據——直接使用 `git` 命令繞過了 `gh` CLI 的所有設定層，將權杖直接提交給 GitHub 伺服器驗證，而 GitHub 伺服器明確拒絕了該權杖。這排除了 `gh` CLI 設定檔遺失等本地問題，確認了問題出在權杖本身。

### 解決方案

由使用者提供了一個新的 Fine-grained Personal Access Token，並透過 `export GH_TOKEN="新權杖"` 設定環境變數後，`gh auth status` 成功顯示已登入。

---

## 問題二：Fine-grained Token 無法建立新儲存庫

### 問題描述

使用新權杖成功登入後，嘗試使用 `gh repo create` 建立新的私有儲存庫時，收到錯誤：

```
GraphQL: Resource not accessible by personal access token (createRepository)
```

### 根本原因

新權杖是一個 **Fine-grained Personal Access Token**，其權限是針對特定儲存庫授予的，而非全域性的。該權杖在建立時，並未被授予「Administration (Read and write)」權限，因此無法執行建立儲存庫的操作。

### 解決方案

由使用者手動在 GitHub 網站上建立了空白儲存庫 `manus-connector-mastery`。

---

## 問題三：Fine-grained Token 無法存取新建立的私有儲存庫

### 問題描述

使用者手動建立了私有儲存庫後，嘗試 clone 時收到錯誤：

```
GraphQL: Could not resolve to a Repository with the name 'jokersosmart/manus-connector-mastery'. (repository)
```

使用 `gh repo list --visibility private` 也顯示「No results match your search」。

### 根本原因

Fine-grained Token 的「Repository access」設定，僅包含權杖建立時已存在的、被明確選擇的儲存庫。由於 `manus-connector-mastery` 是在權杖建立**之後**才新增的，因此該權杖沒有被授權存取這個新儲存庫。

### 解決方案

由使用者將儲存庫的可見性從 **Private** 改為 **Public**。公開儲存庫不需要特定的儲存庫層級授權即可被已認證的使用者存取，因此 clone 操作成功。

---

## 經驗總結

1. **Fine-grained Token 的權限是靜態綁定的**：它不會自動繼承新建立的儲存庫。如果需要存取新儲存庫，必須回到 Token 設定頁面手動更新 Repository access。
2. **診斷權杖問題的最佳方法**：使用底層的 `git clone` 命令（在 URL 中嵌入權杖），而非 `gh` CLI。因為 `git` 會直接將 GitHub 伺服器的錯誤訊息回傳，不會被 CLI 的抽象層遮蔽。
3. **沙箱重置後的檢查清單**：重置後應立即執行 `gh auth status` 確認登入狀態，而非等到需要推送時才發現問題。
