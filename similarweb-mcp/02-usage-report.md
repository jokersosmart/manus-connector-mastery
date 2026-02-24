_# 「Similarweb」連結器使用說明報告

**作者：Joker**

---

本文旨在提供一份關於「Similarweb」連結器的詳細使用說明。此連結器透過 `similarweb-analytics` 技能，提供了一系列工具來獲取網站流量和參與度的數據。以下將詳細介紹每個工具的功能、參數、使用範例及預期輸出。

## 通用參數

所有工具共享一些通用參數，這些參數用於定義查詢的範圍。

| 參數 | 類型 | 說明 | 預設值 |
| :--- | :--- | :--- | :--- |
| `domain` | String | 目標網站的域名，例如 `google.com`。 | (無) |
| `start_date` | String | 查詢的開始日期，格式為 `YYYY-MM`。最遠可追溯至 12 個月前。 | 依工具而定 |
| `end_date` | String | 查詢的結束日期，格式為 `YYYY-MM`。預設為最近一個完整的月份。 | 最近一個完整的月份 |
| `country` | String | ISO 2 國家代碼，用於篩選特定國家的數據。預設為全球數據。 | `world` |
| `main_domain_only` | Boolean | 若為 `True`，則只計算主域名的流量，排除子域名。 | `False` |

---
## 工具一：獲取全球排名 (get_global_rank)

- **功能說明**：查詢指定網站在特定時間範圍內的全球排名變化。
- **預設時間範圍**：過去 6 個月。

### 參數

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `domain` | String | 目標網站的域名。 |

### 使用範例

```python
import sys
sys.path.append('/opt/.manus/.sandbox-runtime')
from data_api import ApiClient

client = ApiClient()
result = client.call_api('SimilarWeb/get_global_rank', path_params={'domain': 'amazon.com'})
```

### 預期輸出

回傳一個包含日期和對應排名的列表。

```json
{
  "global_rank": [
    {
      "date": "2025-07-01",
      "global_rank": 12
    },
    {
      "date": "2025-08-01",
      "global_rank": 11
    }
  ]
}
```

---
## 工具二：獲取總訪問量 (get_visits_total)

- **功能說明**：查詢指定網站在特定時間範圍內、特定國家/地區的總訪問量。
- **預設時間範圍**：過去 6 個月。

### 參數

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `domain` | String | 目標網站的域名。 |
| `country` | String | ISO 2 國家代碼。 |
| `start_date` | String | 開始日期 (YYYY-MM)。 |
| `end_date` | String | 結束日期 (YYYY-MM)。 |
| `granularity` | String | 時間粒度，可選值為 `monthly`。 |

### 使用範例

```python
import sys
sys.path.append("/opt/.manus/.sandbox-runtime")
from data_api import ApiClient

client = ApiClient()
result = client.call_api(
    "SimilarWeb/get_visits_total",
    path_params={"domain": "amazon.com"},
    query={'country': 'world', 'granularity': 'monthly', 'start_date': '2025-07', 'end_date': '2025-12'}
)
```

### 預期輸出

回傳一個包含日期和對應訪問量的列表。

```json
{
  "visits": [
    {
      "date": "2025-07-01",
      "visits": 2500000000
    },
    {
      "date": "2025-08-01",
      "visits": 2600000000
    }
  ]
}
```

---
## 工具三：獲取獨立訪客數 (get_unique_visit)

- **功能說明**：查詢指定網站在特定時間範圍內的獨立訪客數量。
- **預設時間範圍**：過去 6 個月。

### 參數

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `domain` | String | 目標網站的域名。 |
| `start_date` | String | 開始日期 (YYYY-MM)。 |
| `end_date` | String | 結束日期 (YYYY-MM)。 |

### 使用範例

```python
import sys
sys.path.append("/opt/.manus/.sandbox-runtime")
from data_api import ApiClient

client = ApiClient()
result = client.call_api(
    "SimilarWeb/get_unique_visit",
    path_params={"domain": "amazon.com"},
    query={
        "start_date": "2025-07",
        "end_date": "2025-12",
    },
)
```

### 預期輸出

回傳一個包含日期和對應獨立訪客數的列表。

```json
{
  "unique_visitors": [
    {
      "date": "2025-07-01",
      "unique_visitors": 1800000000
    },
    {
      "date": "2025-08-01",
      "unique_visitors": 1900000000
    }
  ]
}
```

---
## 工具四：獲取跳出率 (get_bounce_rate)

- **功能說明**：查詢指定網站在特定時間範圍內、特定國家/地區的跳出率。
- **預設時間範圍**：過去 6 個月。

### 參數

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `domain` | String | 目標網站的域名。 |
| `country` | String | ISO 2 國家代碼。 |
| `start_date` | String | 開始日期 (YYYY-MM)。 |
| `end_date` | String | 結束日期 (YYYY-MM)。 |
| `granularity` | String | 時間粒度，可選值為 `monthly`。 |

### 使用範例

```python
import sys
sys.path.append("/opt/.manus/.sandbox-runtime")
from data_api import ApiClient

client = ApiClient()
result = client.call_api(
    "SimilarWeb/get_bounce_rate",
    path_params={"domain": "amazon.com"},
    query={
        "country": "world",
        "granularity": "monthly",
        "start_date": "2025-07",
        "end_date": "2025-12",
    },
)
```

### 預期輸出

回傳一個包含日期和對應跳出率的列表。

```json
{
  "bounce_rate": [
    {
      "date": "2025-07-01",
      "bounce_rate": 0.45
    },
    {
      "date": "2025-08-01",
      "bounce_rate": 0.44
    }
  ]
}
```

---
## 工具五：獲取桌面流量來源 (get_traffic_sources_desktop)

- **功能說明**：查詢指定網站在特定時間範圍內、特定國家/地區的桌面流量來源分佈。流量來源包括：自然搜尋、付費搜尋、直接流量、展示廣告、電子郵件、推薦流量和社群媒體。
- **預設時間範圍**：過去 3 個月。

### 參數

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `domain` | String | 目標網站的域名。 |
| `country` | String | ISO 2 國家代碼。 |
| `start_date` | String | 開始日期 (YYYY-MM)。 |
| `end_date` | String | 結束日期 (YYYY-MM)。 |

### 使用範例

```python
import sys
sys.path.append("/opt/.manus/.sandbox-runtime")
from data_api import ApiClient

client = ApiClient()
result = client.call_api(
    "SimilarWeb/get_traffic_sources_desktop",
    path_params={"domain": "amazon.com"},
    query={
        "country": "world",
        "start_date": "2025-10",
        "end_date": "2025-12",
    },
)
```

### 預期輸出

回傳一個包含各個流量來源及其佔比的物件。

```json
{
  "traffic_sources": {
    "search_organic": 0.4,
    "search_paid": 0.1,
    "direct": 0.3,
    "display_ads": 0.05,
    "email": 0.05,
    "referrals": 0.05,
    "social": 0.05
  }
}
```

---
## 工具六：獲取行動流量來源 (get_traffic_sources_mobile)

- **功能說明**：查詢指定網站在特定時間範圍內、特定國家/地區的行動流量來源分佈。流量來源與桌面版相同。
- **預設時間範圍**：過去 3 個月。

### 參數

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `domain` | String | 目標網站的域名。 |
| `country` | String | ISO 2 國家代碼。 |
| `start_date` | String | 開始日期 (YYYY-MM)。 |
| `end_date` | String | 結束日期 (YYYY-MM)。 |

### 使用範例

```python
import sys
sys.path.append("/opt/.manus/.sandbox-runtime")
from data_api import ApiClient

client = ApiClient()
result = client.call_api(
    "SimilarWeb/get_traffic_sources_mobile",
    path_params={"domain": "amazon.com"},
    query={
        "country": "world",
        "start_date": "2025-10",
        "end_date": "2025-12",
    },
)
```

### 預期輸出

回傳一個包含各個流量來源及其佔比的物件，結構與桌面版相同。

```json
{
  "traffic_sources": {
    "search_organic": 0.35,
    "search_paid": 0.15,
    "direct": 0.25,
    "display_ads": 0.05,
    "email": 0.05,
    "referrals": 0.05,
    "social": 0.1
  }
}
```

---
## 工具七：按國家/地區獲取總流量 (get_total_traffic_by_country)

- **功能說明**：查詢指定網站在特定時間範圍內，流量最高的國家/地區及其相關指標，包括流量佔比、訪問量、單次訪問頁面數、平均訪問時長、跳出率和國家排名。
- **預設時間範圍**：過去 3 個月。
- **時間範圍限制**：最多 3 個月。

### 參數

| 參數 | 類型 | 說明 |
| :--- | :--- | :--- |
| `domain` | String | 目標網站的域名。 |
| `start_date` | String | 開始日期 (YYYY-MM)。 |
| `end_date` | String | 結束日期 (YYYY-MM)。 |
| `limit` | Integer | 回傳的國家數量，預設為 1，最多為 10。 |

### 使用範例

```python
import sys
sys.path.append("/opt/.manus/.sandbox-runtime")
from data_api import ApiClient

client = ApiClient()
result = client.call_api(
    "SimilarWeb/get_total_traffic_by_country",
    path_params={"domain": "amazon.com"},
    query={
        "start_date": "2025-10",
        "end_date": "2025-12",
        "limit": "10",
    },
)
```

### 預期輸出

回傳一個國家/地區列表，每個物件包含該國家/地區的詳細流量數據。

```json
{
  "top_countries": [
    {
      "country": "US",
      "traffic_share": 0.6,
      "visits": 1500000000,
      "pages_per_visit": 8.5,
      "avg_time_on_site": 540,
      "bounce_rate": 0.35,
      "rank": 5
    },
    {
      "country": "IN",
      "traffic_share": 0.1,
      "visits": 250000000,
      "pages_per_visit": 7.2,
      "avg_time_on_site": 480,
      "bounce_rate": 0.4,
      "rank": 20
    }
  ]
}
```
