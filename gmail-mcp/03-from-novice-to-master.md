# 「Stripe」連結器：從新手到精通

**作者：Joker**

## 前言

歡迎來到「Stripe」連結器的世界。本指南將帶您踏上一段從基礎到進階的旅程，讓您無論是初次接觸支付自動化，還是經驗豐富的開發者，都能掌握如何透過自然語言駕馭強大的 Stripe API。

Stripe 連結器是 Manus AI 與 Stripe 之間的橋樑，它將複雜的金融操作，轉化為一系列直觀、易於理解的「工具」。您不需要撰寫任何程式碼，只需透過對話，就能管理您的客戶、產品、訂閱、支付，並自動化您的核心業務流程。本指南將循序漸進，從核心概念講起，逐步深入到進階技巧與實戰應用，最終讓您成為名副其實的 Stripe 自動化大師。

---

## 第一章：基礎入門 (The Basics)

在我們開始執行任何操作之前，最重要的是理解 Stripe 世界中的幾個核心概念。它們是構成所有商業模式的基礎積木。

### 核心概念

| 概念 | 描述 | 類比 |
| :--- | :--- | :--- |
| **Product** | 您銷售的商品或服務。它是一個抽象的表示，不包含價格資訊。 | 一家餐廳菜單上的「總匯三明治」。 |
| **Price** | 一個 Product 的具體定價方案。一個產品可以有多個價格。 | 「總匯三明治」的「內用價：$12」、「外帶價：$10」、「套餐價：$18」。 |
| **Customer** | 您的客戶。這是一個用於追蹤支付和訂閱的物件。 | 餐廳的會員名單，記錄了每位客人的消費習慣。 |
| **PaymentIntent** | 一次支付意圖。它追蹤了從建立支付到完成收款的整個生命週期。 | 客人點完餐後，服務生開出的帳單。這張帳單的狀態會從「待支付」變為「已支付」。 |
| **Subscription** | 一個客戶與一個特定價格方案之間的重複計費關係。 | 您每月自動續費的 Netflix 會員資格。 |

### 您的第一次互動：查詢資訊

學習任何新工具最好的方式就是親自動手。讓我們從最簡單、最安全的操作開始：查詢資訊。這些唯讀操作不會對您的 Stripe 帳戶做任何修改。

#### 1. 檢視您的帳戶資訊

這是最基本的操作，用於確認連結器已成功連接到您的 Stripe 帳戶。

- **您的指令**：「顯示我的 Stripe 帳戶資訊。」
- **Manus 調用**：`get_stripe_account_info()`
- **您會得到**：一個包含您帳戶詳細資料的物件，例如您的國家、預設貨幣和啟用的功能。

#### 2. 查詢您的帳戶餘額

想知道您現在有多少錢是可用的，又有多少還在路上嗎？

- **您的指令**：「我的 Stripe 餘額是多少？」
- **Manus 調用**：`retrieve_balance()`
- **您會得到**：一個清晰列出「可用餘額 (available)」和「待處理餘額 (pending)」的物件，並按不同貨幣分開顯示。

恭喜！您已經完成了與 Stripe 連結器的第一次互動。在下一章，我們將開始進行實際的建立操作，模擬一個完整的電子商務流程。

---

## 第二章：核心功能：您的第一筆交易 (Core Functions: Your First Transaction)

現在您已經熟悉了基本概念和查詢操作，讓我們來模擬一個最常見的場景：銷售一個單次購買的商品。我們將引導您完成從建立商品到生成支付連結的完整流程。

### 步驟 1：建立您的第一個產品 (Product)

假設您要銷售一本電子書，名為「AI 時代的生存指南」。

- **您的指令**：「幫我建立一個新產品，叫做『AI 時代的生存指南』，描述是『一本關於如何在人工智慧時代保持競爭力的電子書』。」
- **Manus 調用**：`create_product(name='AI 時代的生存指南', description='一本關於如何在人工智慧時代保持競爭力的電子書')`
- **您會得到**：一個代表該產品的新物件，其中包含一個獨一無二的產品 ID (例如 `prod_12345`)。請記下這個 ID，我們下一步會用到。

### 步驟 2：為產品定價 (Price)

接下來，我們要為這本電子書定價，假設是 19.99 美元。

- **您的指令**：「我要為產品 `prod_12345` 設定一個價格，是 19.99 美元，單次收費。」
- **Manus 調用**：`create_price(product='prod_12345', unit_amount=1999, currency='usd')`
- **重點**：請注意，API 中的金額需要以最小貨幣單位表示，所以 19.99 美元要寫成 `1999`。
- **您會得到**：一個新的價格物件，其中也包含一個唯一的價格 ID (例如 `price_67890`)。

### 步驟 3：生成支付連結 (Payment Link)

現在產品和價格都有了，是時候生成一個可以分享給客戶的支付連結了。

- **您的指令**：「請幫我建立一個支付連結，用來銷售價格為 `price_67890` 的商品，數量是 1。」
- **Manus 調用**：`create_payment_link(line_items=[{'price': 'price_67890', 'quantity': 1}])`
- **您會得到**：一個支付連結物件，最關鍵的資訊是 `url` 欄位。您可以將這個 URL 直接發送給您的客戶，他們點擊後就能看到一個由 Stripe 託管的支付頁面並完成購買。

### 步驟 4：檢視交易狀態 (PaymentIntent)

當有客戶透過您的支付連結完成購買後，Stripe 會自動建立一個 PaymentIntent 來追蹤這筆交易。

- **您的指令**：「列出我最近的 5 筆交易紀錄。」
- **Manus 調用**：`list_payment_intents(limit=5)`
- **您會得到**：一個 PaymentIntents 列表，您可以從中看到每筆交易的金額、狀態 (`succeeded`, `failed` 等)、客戶資訊和時間戳。

透過這四個步驟，您已經掌握了處理一次性銷售的核心流程。在下一章，我們將探討更進階的場景：如何管理客戶和訂閱服務。

---

## 第三章：進階技巧：管理客戶與訂閱 (Advanced Techniques: Managing Customers & Subscriptions)

一次性銷售是基礎，但真正的業務成長來自於建立長期的客戶關係和可預測的經常性收入。本章將帶您進入 Stripe 的核心——客戶管理和訂閱模式。

### 場景：推出您的 SaaS 服務

假設您要推出一個名為「ProWriter」的 AI 寫作助理服務，提供月度訂閱。

#### 步驟 1：建立一個客戶 (Customer)

在建立訂閱之前，您需要先有一個客戶。假設一位新用戶 `jane.doe@example.com` 註冊了您的服務。

- **您的指令**：「幫我新增一個客戶，姓名是 Jane Doe，電子郵件是 `jane.doe@example.com`。」
- **Manus 調用**：`create_customer(name="Jane Doe", email="jane.doe@example.com")`
- **您會得到**：一個新的客戶物件，包含唯一的客戶 ID (例如 `cus_ABCDEF`)。這是後續所有操作的基礎。

#### 步驟 2：建立訂閱制產品與價格

與第二章類似，我們需要先建立產品和價格，但這次的價格是「經常性 (recurring)」的。

1.  **建立產品**：
    *   **您的指令**：「建立一個名為『ProWriter 會員』的產品。」
    *   **Manus 調用**：`create_product(name="ProWriter 會員")`
    *   **記下產品 ID** (例如 `prod_67890`)。

2.  **建立價格**：假設月費為 29 美元。
    *   **您的指令**：「為產品 `prod_67890` 建立一個每月 29 美元的訂閱價格。」
    *   **Manus 調用**：`create_price(product="prod_67890", unit_amount=2900, currency="usd", recurring={"interval": "month"})`
    *   **記下價格 ID** (例如 `price_PQRST`)。

#### 步驟 3：為客戶建立訂閱 (Subscription)

現在，我們可以將客戶 Jane Doe (`cus_ABCDEF`) 與 ProWriter 的月度方案 (`price_PQRST`) 綁定在一起。

- **您的指令**：「為客戶 `cus_ABCDEF` 建立一個新的訂閱，方案是 `price_PQRST`。」
- **Manus 調用**：`create_subscription(customer="cus_ABCDEF", items=[{"price": "price_PQRST"}])`
- **您會得到**：一個新的訂閱物件。Stripe 將從現在開始，自動在每個計費週期向該客戶收費。

#### 步驟 4：管理訂閱

業務運營中，您會經常需要管理這些訂閱。

- **查詢訂閱**：
    *   **您的指令**：「列出客戶 `cus_ABCDEF` 的所有有效訂閱。」
    *   **Manus 調用**：`list_subscriptions(customer="cus_ABCDEF", status="active")`

- **取消訂閱**：假設客戶 Jane Doe 決定取消服務。
    *   **您的指令**：「幫我取消訂閱 `sub_UVWXYZ`。」 (假設 `sub_UVWXYZ` 是您從上一步查詢中得到的訂閱 ID)
    *   **Manus 調用**：`cancel_subscription(subscription_id="sub_UVWXYZ")`
    *   **結果**：Stripe 會在當前計費週期結束時停止向該客戶收費，訂閱狀態會變為 `canceled`。

掌握了客戶和訂閱管理，您就掌握了 SaaS 和所有經常性收入模式的核心。在最後一章，我們將探討如何利用連結器處理更複雜的實戰場景。

---
## 第四章：實戰應用：解決真實世界的業務問題 (Real-World Applications)

您已經從基礎一路走到了進階，現在是時候將所學知識應用到真實、多變的業務場景中了。本章將展示如何組合使用多個工具，以解決日常運營中遇到的具體問題，真正發揮自動化的威力。

### 場景一：客戶服務 —「客戶要求退款」

一位客戶 `customer@example.com` 聲稱他上週的一筆 50 美元的交易是錯誤的，要求退款。

1.  **查找交易**：首先，您需要找到這筆交易。使用 `search_stripe_resources` 是最快的方式。
    *   **您的指令**：「幫我搜尋客戶 `customer@example.com` 的交易紀錄。」
    *   **Manus 調用**：`search_stripe_resources(query="customer:'customer@example.com'")`
    *   **分析結果**：您會得到一個包含該客戶所有相關資源的列表。從中找到對應的 `PaymentIntent` 物件，並記下其 ID (例如 `pi_ABC123`)。

2.  **執行退款**：有了 `PaymentIntent` ID，您就可以執行退款了。
    *   **您的指令**：「為交易 `pi_ABC123` 辦理退款，金額為 50 美元。」
    *   **Manus 調用**：`create_refund(payment_intent="pi_ABC123", amount=5000)`
    *   **結果**：Stripe 會處理這筆退款，並將款項退還給客戶的原始支付方式。您會收到一個退款物件作為確認。

### 場景二：市場行銷 —「推出黑色星期五促銷活動」

您希望建立一個全站商品八折的折扣券，有效期為一個月。

1.  **建立折扣券**：
    *   **您的指令**：「建立一個 20% off 的折扣券，名稱是『Black Friday Sale』，持續時間設定為一次性使用。」
    *   **Manus 調用**：`create_coupon(percent_off=20, duration="once", name="Black Friday Sale")`
    *   **記下折扣券 ID** (例如 `coupon_XYZ`)。

2.  **應用折扣券**：您可以在建立訂閱或支付連結時應用此折扣券。例如，將其應用到一個新的訂閱上。
    *   **您的指令**：「為客戶 `cus_ABCDEF` 建立一個 `price_PQRST` 的訂閱，並套用折扣券 `coupon_XYZ`。」
    *   **Manus 調用**：`create_subscription(customer="cus_ABCDEF", items=[{"price": "price_PQRST"}], coupon="coupon_XYZ")`
    *   **結果**：客戶的第一筆帳單將會自動享有八折優惠。

### 場景三：商業智慧 —「分析上個月的營收」

您想知道上個月的總收入是多少。

- **您的指令**：「列出我上個月所有成功的交易。」
- **Manus 調用**：`list_payment_intents(created={'gte': <timestamp_of_first_day_of_last_month>, 'lte': <timestamp_of_last_day_of_last_month>}, status='succeeded')`
- **分析**：雖然 Manus 會幫您處理時間戳的轉換，但這個例子展示了更複雜的查詢。您會得到上個月所有成功交易的列表。您可以接著提出後續問題，如「將這些交易的總金額加總」，Manus 會為您完成計算。

## 結語：您的自動化之旅

恭喜您完成了本指南！從理解核心概念，到處理第一筆交易，再到管理訂閱和應對複雜的真實場景，您已經掌握了透過自然語言與 Stripe 互動的關鍵技能。

但這僅僅是個開始。Stripe 連結器的真正力量在於其組合性——將這些工具融入您獨特的工作流程中，創造出前所未有的自動化解決方案。嘗試組合不同的工具，挑戰更複雜的任務，例如：

-   當一個爭議 (`Dispute`) 發生時，自動從相關的 `Customer` 物件中提取聯絡資訊。
-   每月初自動執行一次 `list_subscriptions`，找出所有狀態為 `past_due` 的訂閱，並觸發後續的催款流程。
-   使用 `search_stripe_documentation` 來學習您尚不熟悉的 API 功能，並嘗試直接透過 `fetch_stripe_resources` 來與它們互動。

您的想像力是唯一的限制。現在，就開始您的 Stripe 自動化之旅吧！

---
