_**Author: Joker**_

# PopHIVE Learning Manual

This manual is designed for rapid onboarding, focusing on key skills to get you started with the PopHIVE connector quickly. Each section presents a clear learning objective and a practical exercise.

## Section 1: Discovering Available Data

**Objective:** Learn how to find out what datasets are available in PopHIVE.

**Key Skill:** Using the `get_available_datasets` tool.

**Exercise:**
1.  Invoke the `get_available_datasets` tool.
2.  Review the output list of datasets.
3.  Identify at least three datasets that are relevant to your interests or field of study.

## Section 2: Searching for Specific Information

**Objective:** Learn how to search for data related to a specific health topic.

**Key Skill:** Using the `search_health_data` tool.

**Exercise:**
1.  Choose a health topic you are interested in (e.g., "influenza", "youth mental health").
2.  Use the `search_health_data` tool with your chosen topic as the `query`.
3.  Examine the results to see which datasets contain information about your topic.

## Section 3: Filtering a Dataset

**Objective:** Learn how to narrow down a large dataset to a specific subset of data.

**Key Skill:** Using the `filter_data` tool.

**Exercise:**
1.  Select one of the datasets you identified in the previous exercises.
2.  Use the `filter_data` tool to narrow down the data to a specific state and a recent one-year period.
3.  *Challenge:* Add a demographic filter (e.g., a specific age group) to further refine your results.

## Section 4: Performing a Comparative Analysis

**Objective:** Learn how to compare a health metric across different locations.

**Key Skill:** Using the `compare_states` tool.

**Exercise:**
1.  Choose a dataset and a relevant metric (e.g., `dataset://chronic_obesity` and `obesity_prevalence`).
2.  Use the `compare_states` tool to compare the metric across three states of your choice.
3.  Analyze the output to determine which state has the highest and lowest values for the metric.

## Section 5: Analyzing Trends Over Time

**Objective:** Learn how to analyze how a health metric has changed over time.

**Key Skill:** Using the `time_series_analysis` tool.

**Exercise:**
1.  Select a dataset that contains time-series data (e.g., `dataset://respiratory_ed`).
2.  Choose a state and a metric to analyze.
3.  Use the `time_series_analysis` tool with monthly aggregation to observe the trend over the past two years.
4.  Look for any seasonal patterns or significant changes in the trend.
