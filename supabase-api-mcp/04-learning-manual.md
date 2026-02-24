_Author: Joker_

# Serena 學習手冊：快速上手與關鍵技能培養

本手冊為您提供一條快速掌握 Serena 核心能力的捷徑。我們將複雜的功能拆解為四個獨立的學習模組，每個模組都包含清晰的學習目標和一個可立即上手的實踐練習，讓您在最短的時間內，從「知道」Serena 到「會用」Serena。

## 模組一：專案透視 (Project Inspection)

**學習目標：** 學會如何在不修改任何程式碼的情況下，快速理解一個陌生專案的結構與核心邏輯。

**關鍵技能：**

- **檔案結構探索:** 使用 `list_dir` 來導航專案的目錄樹。
- **程式碼閱讀:** 使用 `read_file` 來查看任何檔案的內容。
- **定義追蹤:** 使用 `find_symbol` 來精準定位任何函式或類別的原始定義位置。

**實踐練習：『繪製你的第一張程式碼地圖』**

1.  **啟動專案:** 選擇一個您熟悉的專案，使用 `activate_project` 將其設定為 Serena 的當前工作區。
2.  **尋找入口:** 使用 `read_file` 查看 `package.json` 或類似的設定檔，找出專案的啟動腳本，並找到主程式入口檔案 (例如 `main.js` 或 `app.py`)。
3.  **追蹤呼叫:** 在入口檔案中，使用 `find_symbol` 尋找一個核心函式 (例如 `app.listen` 或 `createApp`)，然後使用 `find_referencing_symbols` 找出是誰呼叫了它。
4.  **記錄發現:** 將您的追蹤路徑以文字形式記錄下來，例如：`main.js -> createApp() -> server.start()`。

**成果檢驗：** 您現在應該能夠在 5 分鐘內，大致說出一個新專案的入口點在哪裡，以及它的主要模組是如何組織的。

## 模組二：精準編輯 (Surgical Editing)

**學習目標：** 學會在不依賴 IDE 的圖形化介面的情況下，對程式碼進行精準、安全的小範圍修改。

**關鍵技能：**

- **行級操作:** 使用 `insert_at_line` 和 `replace_lines` 在特定行號進行插入和取代。
- **符號邊界操作:** 使用 `insert_before_symbol` 和 `insert_after_symbol` 在一個函式或類別的定義前後添加程式碼。

**實踐練習：『為函式添加日誌』**

1.  **定位目標:** 使用 `find_symbol` 找到一個您想要監控的函式。
2.  **入口日誌:** 使用 `insert_after_symbol` 在該函式定義的 `{` 之後 (或 Python 的 `def` 行之後) 插入一行日誌記錄，例如 `console.log('Entering function X...');`。
3.  **出口日誌:** 再次使用 `insert_before_symbol`，在函式的 `return` 語句之前插入一行日誌，例如 `console.log('Exiting function X...');`。

**成果檢驗：** 您現在應該能夠在不弄亂程式碼格式的情況下，為任何函式添加進入和離開的日誌，以方便您進行偵錯。

## 模組三：安全重構 (Safe Refactoring)

**學習目標：** 學會使用 Serena 的語義感知能力來執行跨檔案的程式碼重構，提升程式碼品質。

**關鍵技能：**

- **符號重命名:** 使用 `rename_symbol` 在整個專案中安全地為變數、函式或類別重新命名。
- **影響力分析:** 結合 `find_referencing_symbols` 來預測一個變更可能影響的範圍。

**實踐練習：『告別魔法數字』**

1.  **尋找目標:** 使用 `search_for_pattern` 在您的程式碼庫中尋找一個硬編碼 (hard-coded) 的數字或字串 (例如，一個超時時間 `3000` 或一個 API 金鑰的字串)。
2.  **建立常數:** 在一個適當的設定檔中，使用 `insert_at_line` 或 `create_text_file` 建立一個新的常數來取代這個魔法數字，例如 `const REQUEST_TIMEOUT = 3000;`。
3.  **執行重構:** 使用 `find_referencing_symbols` 找到所有使用到舊數字的地方，然後逐一使用 `replace_lines` 將其替換為對新常數的引用。
4.  **(進階)** 或者，直接嘗試使用 `rename_symbol` (如果適用)，體驗全自動重構的威力。

**成果檢驗：** 您現在應該能夠自信地對一個在多處被使用的變數進行重命名，而不用擔心會遺漏任何一處參考。

## 模組四：任務自動化 (Task Automation)

**學習目標：** 學會將多個 Serena 工具串連起來，並結合 shell 指令，以自動化完成一個完整的開發任務。

**關鍵技能：**

- **外部指令執行:** 使用 `execute_shell_command` 來運行測試、安裝依賴或與版本控制系統互動。
- **任務記憶:** 使用 `write_memory` 和 `read_memory` 來讓 Serena 在多步驟任務中記住上下文和計畫。

**實踐練習：『自動化的依賴更新』**

1.  **規劃任務:** 使用 `write_memory` 記錄您的計畫：「1. 讀取 `package.json`。 2. 找到 `dependencies` 區塊。 3. 使用 `npm outdated` 檢查過期的套件。 4. 選擇一個套件並使用 `npm install <package>@latest` 來更新它。 5. 運行測試以確保沒有破壞任何東西。」
2.  **執行計畫:** 逐一執行您的計畫。在每一步，您都可以使用 `read_memory` 來查看下一步該做什麼。
    - 使用 `read_file` 讀取 `package.json`。
    - 使用 `execute_shell_command` 運行 `npm outdated`。
    - 使用 `execute_shell_command` 運行 `npm install ...`。
    - 使用 `execute_shell_command` 運行 `npm test`。

**成果檢驗：** 您現在應該能夠設計並執行一個簡單的腳本或一系列指令，讓 Serena 自動為您完成一個包含多個步驟的開發任務。
