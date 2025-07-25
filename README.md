# Automated Field Data Consolidation and Grouping

## Project Overview

This project provides a Python script within a Jupyter Notebook to process, clean, and consolidate time-series data from a portable field data logger. The primary goal is to parse raw `.TXT` log files, transform them into a structured format, and then merge this sensor data with a corresponding logsheet that contains metadata for each measurement session (like location titles and time windows).

The script addresses the common challenge of handling messy, multi-file sensor outputs, transforming them into a clean, analysis-ready dataset. It effectively combines two distinct data sources—raw sensor logs and a manually created field logsheet—into a single, unified Excel file.

-----

## Features

  * **Raw Log Parsing:** Automatically reads all `.TXT` files from a specified folder and parses the timestamped sensor readings.
  * **Data Restructuring:** Transforms the raw, long-format log data into a structured, wide-format DataFrame where each row represents a single timestamp and each column represents a different sensor reading.
  * **Data Cleaning:** Handles irregularities and missing values that are common in raw sensor outputs.
  * **Time-Based Merging:** Intelligently merges the cleaned sensor data with a master logsheet by matching each sensor reading's timestamp to the correct measurement time window (`date created start` to `date created end`).
  * **Data Export:** Saves the intermediate and final processed DataFrames to `.xlsx` files for easy access and further analysis in other tools.

-----

## How It Works

The notebook executes the data processing in two main stages:

### Stage 1: Processing the Raw Logger Data

1.  **Load Logsheet:** The script first loads the `Data Tersortis + logsheet gedong songo.xlsx` file. This file contains the metadata for each field measurement, including a `Title` and the start/end times for each session.
2.  **Handle Time Anomalies:** It identifies and manually corrects any inconsistencies in the logsheet's timestamps to ensure accurate time windows for merging.
3.  **Parse `.TXT` Files:** The script reads all `.TXT` files from the `logger-sd-lapangan` folder. It extracts every line containing a timestamp and a value.
4.  **Pivot Data:** The raw data, which is in a long format (one row per sensor reading), is pivoted into a wide format. This creates a structured table where each row corresponds to a single point in time and includes columns for all the different sensor values recorded at that moment (e.g., `Temp`, `Hum`, `PPM CO2 (MQ135)`, etc.).
5.  **Export Clean Logger Data:** The resulting structured DataFrame is saved as `log_df_pivot.xlsx`.

### Stage 2: Merging Logsheet Metadata with Sensor Data

1.  **Timestamp Alignment:** The script ensures that the timestamps in both the logsheet DataFrame and the sensor data DataFrame are timezone-aware (set to `Asia/Bangkok`) for accurate comparison.
2.  **Merge by Time Window:** It iterates through each row of the cleaned sensor data. For each timestamp, it finds the corresponding measurement session in the logsheet where the timestamp falls between the `date created start` and `date created end`.
3.  **Assign Titles:** When a match is found, the `Title` from the logsheet is added to the sensor data row.
4.  **Export Final Merged Data:** The final, consolidated DataFrame, which now includes both the sensor readings and their corresponding location titles, is saved as `log_df_merged.xlsx`.

-----

## Setup and Usage

### 1\. File Structure

To run this notebook, your project directory should be set up as follows:

```
your-project-folder/
│
├── logger-sd-grouping.ipynb        # The Jupyter Notebook file
│
├── Data Tersortis + logsheet gedong songo.xlsx  # The master logsheet file
│
└── logger-sd-lapangan/             # Folder containing all the raw .TXT log files
    ├── LOG_001.TXT
    ├── LOG_002.TXT
    └── ...
```

### 2\. Requirements

This script requires Python 3 and the following libraries. You can install them using `pip`:

```bash
pip install pandas openpyxl
```

### 3\. How to Use

1.  Ensure your Excel logsheet and the folder of `.TXT` files are in the same directory as the Jupyter Notebook.
2.  Open and run all cells in the `logger-sd-grouping.ipynb` notebook sequentially.
3.  The script will generate two new Excel files in your project directory: `log_df_pivot.xlsx` (cleaned sensor data) and `log_df_merged.xlsx` (final merged data).

-----

## Outcome

The primary output is **`log_df_merged.xlsx`**, a single, comprehensive spreadsheet that contains all the high-resolution sensor data, with each reading correctly attributed to its measurement location and session. This file is ready for detailed analysis, visualization, or reporting.
