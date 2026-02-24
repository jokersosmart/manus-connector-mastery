_**Author: Joker**_

# PopHIVE Connector: Usage Report

This report provides a detailed usage guide for the PopHIVE MCP connector's available tools. Each section covers a specific tool, its parameters, functionality, usage examples, and expected output.

## 1. `filter_data`

### Functionality

This tool allows users to filter datasets based on various criteria such as state, date range, demographics, and specific health conditions. It is the primary tool for narrowing down the vast amount of data to a specific area of interest.

### Parameters

| Parameter     | Type   | Description                                                                 |
| :------------ | :----- | :-------------------------------------------------------------------------- |
| `dataset`     | string | The name of the dataset to filter (e.g., `dataset://immunizations_nis`).    |
| `state`       | string | The state to filter the data by (e.g., "California").                       |
| `start_date`  | string | The start date for the filter range (e.g., "2023-01-01").                   |
| `end_date`    | string | The end date for the filter range (e.g., "2023-12-31").                     |
| `demographics`| object | An object specifying demographic filters (e.g., `{"age_group": "18-24"}`). |
| `conditions`  | array  | An array of health conditions to filter by (e.g., `["diabetes", "obesity"]`). |

### Example

```json
{
  "tool": "filter_data",
  "dataset": "dataset://chronic_diabetes",
  "state": "New York",
  "start_date": "2022-01-01",
  "end_date": "2022-12-31",
  "demographics": {
    "age_group": "45-64"
  }
}
```

### Expected Output

A filtered subset of the original dataset in JSON format, containing only the data points that match the specified criteria.

## 2. `compare_states`

### Functionality

This tool enables the comparison of health metrics across multiple states. It also provides statistical analysis to determine if the differences between states are statistically significant.

### Parameters

| Parameter | Type  | Description                                                                  |
| :-------- | :---- | :--------------------------------------------------------------------------- |
| `dataset` | string| The name of the dataset to use for comparison.                               |
| `states`  | array | An array of state names to compare (e.g., `["California", "Texas", "Florida"]`). |
| `metric`  | string| The specific health metric to compare (e.g., "obesity_rate").                |

### Example

```json
{
  "tool": "compare_states",
  "dataset": "dataset://chronic_obesity",
  "states": ["California", "Texas", "Florida"],
  "metric": "obesity_prevalence"
}
```

### Expected Output

A JSON object containing a comparison table of the specified metric for the selected states, along with a statistical analysis of the differences.

## 3. `time_series_analysis`

### Functionality

This tool is used to analyze trends in health data over time. It offers options for aggregating data by different time intervals (e.g., weekly, monthly, yearly).

### Parameters

| Parameter   | Type   | Description                                                                    |
| :---------- | :----- | :----------------------------------------------------------------------------- |
| `dataset`   | string | The name of the dataset to analyze.                                            |
| `state`     | string | The state to analyze the data for.                                             |
| `metric`    | string | The specific health metric to analyze over time.                               |
| `aggregation`| string | The time interval for aggregation (e.g., "monthly", "yearly").                 |

### Example

```json
{
  "tool": "time_series_analysis",
  "dataset": "dataset://respiratory_ed",
  "state": "California",
  "metric": "ed_visits",
  "aggregation": "monthly"
}
```

### Expected Output

A JSON object containing the time series data, including timestamps and corresponding metric values, ready for plotting or further analysis.

## 4. `get_available_datasets`

### Functionality

This tool provides a comprehensive catalog of all datasets available through the PopHIVE MCP server.

### Parameters

None.

### Example

```json
{
  "tool": "get_available_datasets"
}
```

### Expected Output

A JSON array listing all available datasets with their names and a brief description of each.

## 5. `search_health_data`

### Functionality

This tool allows users to search for specific conditions or keywords across all available datasets. It is useful for discovering relevant datasets for a particular research question.

### Parameters

| Parameter | Type   | Description                                               |
| :-------- | :----- | :-------------------------------------------------------- |
| `query`   | string | The keyword or condition to search for (e.g., "cancer"). |

### Example

```json
{
  "tool": "search_health_data",
  "query": "mental health"
}
```

### Expected Output

A JSON object containing a list of datasets and specific data points that match the search query.
