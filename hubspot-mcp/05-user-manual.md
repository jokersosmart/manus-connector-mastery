# 「Make」連結器終端使用者操作手冊

**作者：** Joker

---

## 1. 簡介

本手冊旨在協助終端使用者了解如何透過自然語言指令，驅動 Manus AI 來操作 Make.com 連結器，實現強大的自動化工作流程。您不需要了解複雜的程式碼或 API 細節，只需用日常語言描述您的需求，Manus 便會將其轉換為精確的 Make 工具調用。

## 2. 核心概念：指令與工具調用

當您向 Manus 下達一個與 Make 相關的指令時，例如「幫我建立一個每天早上九點發送天氣預報到 Slack 的流程」，Manus 的內部機制會進行以下轉換：

1.  **意圖識別**：理解您想要「建立一個新的自動化流程 (Scenario)」。
2.  **實體提取**：找出關鍵參數，如「觸發時間：每天早上九點」、「執行的應用：Slack」、「任務內容：發送天氣預報」。
3.  **工具匹配**：從 Make 連結器的工具集中，找到最符合的工具，在此例中可能是 `create_scenario`。
4.  **參數對應**：將提取出的實體填入工具所需的參數中。
5.  **執行調用**：最終生成並執行一個類似 `make.create_scenario(...)` 的指令。

本手冊將透過具體範例，展示這一過程。

## 3. 任務導向指令範例

以下章節將以常見的自動化任務為導向，提供自然語言指令範例，並揭示其背後對應的 Manus 工具調用。這將幫助您更直觀地理解 Manus 如何為您工作。

*(注意：以下的工具名稱和參數為根據 Make 公開 API 推斷的示意，實際名稱可能有所不同，但其運作邏輯是相同的。)*

### 3.1. 管理自動化流程 (Scenario)

Scenarios 是 Make 的核心，代表一個完整的自動化工作流程。

#### **任務：建立新流程**

- **您的指令：**
  > 「Manus，幫我建立一個新的 Make 流程，當我的 Gmail 收到標籤為『Invoice』的郵件時，自動將附件存到 Google Drive 的『Invoices』資料夾。」

- **Manus 的工具調用：**
  ```json
  {
    "tool": "make.create_scenario",
    "parameters": {
      "name": "Save Gmail Invoice Attachments to Google Drive",
      "trigger": {
        "app": "gmail",
        "event": "new_email",
        "filters": {
          "label": "Invoice"
        }
      },
      "actions": [
        {
          "app": "google_drive",
          "action": "upload_file",
          "parameters": {
            "folder": "/Invoices/",
            "file_data": "${gmail.attachments}"
          }
        }
      ]
    }
  }
  ```

#### **任務：列出現有流程**

- **您的指令：**
  > 「我想看看目前所有正在運作的 Make 流程有哪些。」

- **Manus 的工具調用：**
  ```json
  {
    "tool": "make.list_scenarios",
    "parameters": {
      "status": "active"
    }
  }
  ```

#### **任務：啟動或停止流程**

- **您的指令：**
  > 「把那個叫做『每日銷售報告』的流程暫停一下。」

- **Manus 的工具調用：**
  ```json
  {
    "tool": "make.stop_scenario",
    "parameters": {
      "scenario_name": "每日銷售報告"
    }
  }
  ```

### 3.2. 操作資料庫 (Data Store)

Data Stores 是 Make 內建的簡易資料庫，可用於在流程中暫存或傳遞資料。

#### **任務：建立資料庫**

- **您的指令：**
  > 「幫我在 Make 裡面建立一個新的資料庫，叫做『潛在客戶名單』，需要有『姓名』、『公司』和『Email』這幾個欄位。」

- **Manus 的工具調用：**
  ```json
  {
    "tool": "make.create_data_store",
    "parameters": {
      "name": "潛在客戶名單",
      "columns": [
        {"name": "姓名", "type": "text"},
        {"name": "公司", "type": "text"},
        {"name": "Email", "type": "text"}
      ]
    }
  }
  ```

#### **任務：新增資料**

- **您的指令：**
  > 「在『潛在客戶名單』裡，新增一筆資料：Joker，Manus Inc.，joker@manus.im。」

- **Manus 的工具調用：**
  ```json
  {
    "tool": "make.add_record_to_data_store",
    "parameters": {
      "data_store_name": "潛在客戶名單",
      "data": {
        "姓名": "Joker",
        "公司": "Manus Inc.",
        "Email": "joker@manus.im"
      }
    }
  }
  ```

## 4. 結語

正如您所見，您與 Manus 的互動是基於意圖和任務的，而非技術細節。透過強大的自然語言理解能力，Manus 將您的需求無縫對接到 Make 的功能上，讓您專注於「做什麼」，而不是「怎麼做」。隨著您使用的深入，您會發現更多可以透過自然語言驅動的自動化可能性。
