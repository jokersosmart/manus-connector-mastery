# ElevenLabs MCP 連結器使用說明報告

**作者：Joker**

---

## 總覽

本報告旨在詳細說明 ElevenLabs MCP 連結器所提供的各種工具，幫助使用者充分利用其強大的文字轉語音（Text-to-Speech, TTS）功能。ElevenLabs 以其極度逼真和低延遲的語音生成技術聞名，透過本連結器，使用者可以在 Manus 平台中無縫整合這些能力。

## 可用工具

目前，ElevenLabs 連結器主要提供一個核心工具：`text_to_speech`。此工具負責將輸入的文字轉換為高品質的語音音訊。

### 1. `text_to_speech`

此工具是將文字轉換為語音的核心端點。它接受文字輸入，並回傳一個音訊檔案。

#### 參數詳解

| 參數 | 類型 | 是否必須 | 預設值 | 說明 |
| :--- | :--- | :--- | :--- | :--- |
| `voice_id` | string | 是 | - | 指定要使用的聲音 ID。您可以透過 `get_voices` 工具（未來將新增）來獲取所有可用的聲音列表。 |
| `text` | string | 是 | - | 您想要轉換為語音的文字內容。 |
| `model_id` | string | 否 | `eleven_multilingual_v2` | 指定使用的模型 ID。可透過查詢模型列表獲得。模型必須支援 TTS 功能。 |
| `language_code` | string | 否 | - | ISO 639-1 語言代碼，用於強制指定模型的語言和文字正規化。 |
| `voice_settings` | object | 否 | - | 一個包含聲音設定的物件，可覆寫指定聲音的預設設定。包含 `stability`、`similarity_boost`、`style` 和 `use_speaker_boost` 等參數。 |
| `pronunciation_dictionary_locators` | list | 否 | - | 發音字典定位器的列表，用於自訂特定詞彙的發音。 |
| `seed` | integer | 否 | - | 一個 0 到 4294967295 之間的整數，用於嘗試實現可預測的、確定性的採樣結果。 |
| `previous_text` | string | 否 | - | 當前請求文字之前的一段文字，有助於改善語音的連續性。 |
| `next_text` | string | 否 | - | 當前請求文字之後的一段文字，同樣有助於改善語音的連續性。 |
| `output_format` | string | 否 | `mp3_44100_128` | 指定輸出音訊的格式，例如 `mp3_44100_128` 或 `pcm_24000`。 |
| `enable_logging` | boolean | 否 | `true` | 設為 `false` 時將啟用零保留模式，歷史記錄功能將不可用（僅限企業用戶）。 |

#### 使用範例

**情境：** 將一段歡迎詞轉換為 Rachel 的聲音。

```json
{
  "tool_name": "text_to_speech",
  "parameters": {
    "voice_id": "21m00Tcm4TlvDq8ikWAM",
    "text": "Welcome to the world of AI-powered voice generation! We hope you enjoy the experience.",
    "model_id": "eleven_multilingual_v2",
    "voice_settings": {
      "stability": 0.75,
      "similarity_boost": 0.75
    }
  }
}
```

#### 預期輸出

一個包含上述歡迎詞語音的 MP3 音訊檔案。音訊將使用 ID 為 `21m00Tcm4TlvDq8ikWAM`（Rachel）的聲音，並套用指定的穩定性與相似度增強設定。

---

## 結論

`text_to_speech` 工具提供了豐富的參數選項，讓使用者能夠精細地控制語音生成的各個方面，從聲音選擇、模型、語言到風格和連續性。熟悉這些參數將極大地提升您利用 ElevenLabs 連結器進行內容創作的效率與品質。
