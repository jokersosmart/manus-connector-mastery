_**Author: Joker**_

# PopHIVE End User Manual

This manual provides task-oriented instructions for end-users. It demonstrates how to use natural language commands to perform tasks, and explains how Manus translates these commands into specific tool calls.

## Task 1: See all the data I can use

**Your Command:** "Show me all the datasets available in PopHIVE."

**How Manus Understands It:** Manus recognizes that you want a list of all available data sources. It translates your request into the following tool call:

```json
{
  "tool": "get_available_datasets"
}
```

## Task 2: Find data about a specific topic

**Your Command:** "Search for data on youth mental health."

**How Manus Understands It:** Manus identifies your intent to search for a specific topic. It uses the `search_health_data` tool and passes your topic as the `query` parameter:

```json
{
  "tool": "search_health_data",
  "query": "youth mental health"
}
```

## Task 3: Focus on a specific dataset and region

**Your Command:** "Filter the immunization data for California from last year."

**How Manus Understands It:** Manus breaks down your request into several components. It identifies the dataset (`immunizations`), the state (`California`), and the time frame (`last year`). This is then converted into a `filter_data` tool call:

```json
{
  "tool": "filter_data",
  "dataset": "dataset://immunizations_nis",
  "state": "California",
  "start_date": "2025-01-01",
  "end_date": "2025-12-31"
}
```

## Task 4: Compare a health issue between states

**Your Command:** "Compare obesity rates between Texas, Florida, and California."

**How Manus Understands It:** Manus understands that you want to perform a comparative analysis. It identifies the metric (`obesity rates`) and the locations (`Texas`, `Florida`, `California`) and uses the `compare_states` tool:

```json
{
  "tool": "compare_states",
  "dataset": "dataset://chronic_obesity",
  "states": ["Texas", "Florida", "California"],
  "metric": "obesity_prevalence"
}
```

## Task 5: Analyze a health trend over time

**Your Command:** "Show me the monthly trend of emergency room visits for respiratory illnesses in New York for the last three years."

**How Manus Understands It:** Manus recognizes the key elements of a time series analysis: the metric (`emergency room visits for respiratory illnesses`), the location (`New York`), the time frame (`last three years`), and the desired aggregation (`monthly`). This leads to the following `time_series_analysis` tool call:

```json
{
  "tool": "time_series_analysis",
  "dataset": "dataset://respiratory_ed",
  "state": "New York",
  "metric": "ed_visits",
  "aggregation": "monthly"
}
```
