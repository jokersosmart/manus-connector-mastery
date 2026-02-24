# Stripe API 連結器使用者操作手冊

作者：Joker

## 歡迎！

歡迎使用 Stripe API 連結器！本手冊將向您展示如何透過簡單的自然語言指令，完成強大的支付相關任務。您不需要成為一名工程師，也不需要了解複雜的 API，您只需要知道您想**完成什麼**。

本連結器的設計核心是**「任務導向」**。我們將向您展示一系列常見的業務場景，並提供您可以直接複製、貼上或修改的指令範例。對於每個範例，我們還會解釋 Manus 是如何理解您的指令，並將其轉換為對應的工具調用的，讓您「知其然，也知其所以然」。

---

## Part 1: 基礎收款與客戶管理

### 任務 1：我需要為我的新書收款

這是最常見的需求：您有一個產品或服務，需要一個簡單的方式讓客戶付錢。

*   **場景：** 您寫了一本電子書《我的第一桶金》，售價 39 美元。
*   **您的指令：** `"我需要為我的新書《我的第一桶金》建立一個收款頁面，價格是 39 美元。"`

#### Manus 如何理解與執行

1.  **意圖識別：** Manus 識別出關鍵字「收款頁面」、「價格」，理解您的核心意圖是「建立一個支付連結」。
2.  **實體提取：** 它會從您的指令中提取出兩個關鍵資訊：
    *   產品名稱 (product_name): `我的第一桶金`
    *   價格 (amount & currency): `39 USD`
3.  **工具選擇：** 基於意圖和提取出的實體，Manus 選擇了最高度抽象化的工具 `create_payment_link`。
4.  **工具調用：** Manus 執行工具調用，大致等同於：
    `stripe.create_payment_link(product_name='我的第一桶金', amount=3900, currency='usd')`
5.  **結果呈現：** 最後，它會將 `create_payment_link` 工具回傳的 URL 呈現給您，這就是您可以分享給客戶的收款頁面。

### 任務 2：我想把一位重要客戶加入我的系統

在銷售過程中，您可能會先於客戶付款前就與他們建立聯繫。將他們預先加入您的客戶系統，有助於後續的管理與跟進。

*   **場景：** 您在一次展會上認識了來自 `Future Corp` 的 `Emily White`，她的郵箱是 `emily@future.com`。
*   **您的指令：** `"新增一個客戶，姓名是 Emily White，公司是 Future Corp，郵箱是 emily@future.com。"`

#### Manus 如何理解與執行

1.  **意圖識別：** 關鍵字「新增」、「客戶」讓 Manus 明白您要建立一個新的客戶記錄。
2.  **實體提取：**
    *   姓名 (name): `Emily White`
    *   郵箱 (email): `emily@future.com`
    *   描述 (description): `Future Corp` (Manus 可能會將公司名放入描述欄位)
3.  **工具選擇：** 選擇 `create_customer` 工具。
4.  **工具調用：**
    `stripe.create_customer(name='Emily White', email='emily@future.com', description='Future Corp')`
5.  **結果呈現：** Manus 會告訴您客戶已成功建立，並可能回傳該客戶的唯一 ID (`cus_xxxxxxxx`) 以供後續操作使用。
