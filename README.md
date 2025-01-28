# Login Data Analysis Script

This repository contains a Python script designed to analyze user login data. The script processes raw data and generates actionable insights to help identify patterns in user behavior and flag irregularities. Below is a detailed explanation of the tasks performed and the methods used in this project.

## Features

The script provides the following functionalities:

1. **Convert raw data to a structured summary table:**
   - Input raw data from an Excel file.
   - Process the data into a user-level summary sheet for further analysis.

2. **Key Insights Generated:**
   - Total number of logins for each user.
   - First login time for each user.
   - Last login time for each user.
   - Average time difference between logins for each user.
   - Number of unique devices used by each user.
   - Dates on which each user logged in.

3. **Outputs a Summary Sheet:**
   - Saves the processed data to a new Excel file for easy sharing and reporting.

---

## File Structure

- **`login_data_analysis.py`**: The main script to process the login data.
- **`login_data.xlsx`**: The input Excel file containing raw login data.
- **`login_data_summary.xlsx`**: The output Excel file containing the summary report.

---

## Script Details

### Dependencies

The script uses the following Python libraries:
- `pandas`

Install the dependencies using:
```bash
pip install pandas
```

### Input File Format

The input Excel file (`login_data.xlsx`) should contain the following columns:
- `CIRCLE`: Location of the user.
- `USER_CODE`: A unique identifier for each user.
- `USER_NAME`: Name of the user.
- `LOGIN_DATE`: Date of login.
- `LOGIN_TIME`: The date and time when the user logged in.
- `TIME_DIFF_BETWEEN_PREV_LOGIN`: Time difference since the user's previous login.
- `DEVICE_BRAND`: Name of the device used for login.
- `DEVICE_MODEL`: The device model used for the login (e.g., Redmi, realme).
- `DEVICE_ID`: Device ID of the device used for login.

### Processing Steps

1. **Read and preprocess raw data:**
   - The script reads raw data from the `raw_data` sheet of the input Excel file.
   - Ensures the `LOGIN_DATE` column is in proper datetime format.

2. **Group data by user (`USER_CODE`) and aggregate:**
   - Calculate total logins (`TOTAL_LOGINS`).
   - Find the first and last login dates (`FIRST_LOGIN`, `LAST_LOGIN`).
   - Compute the average time difference between logins (`AVG_TIME_DIFF`).
   - Determine the number of unique devices used (`UNIQUE_DEVICES`).
   - Collect all unique login dates as a concatenated string (`LOGIN_DATES`).

3. **Save the processed data:**
   - Outputs the summary sheet to a new Excel file (`login_data_summary.xlsx`).

### Code Walkthrough

```python
import pandas as pd

def analyze_login_data(file_path):
    """
    Analyzes user login data and generates actionable insights.

    Args:
        file_path (str): Path to the raw data Excel file.

    Returns:
        pd.DataFrame: Summary sheet containing insights.
    """
    # Load the data
    raw_data = pd.read_excel(file_path, sheet_name='raw_data')

    # Ensure LOGIN_DATE is in datetime format
    raw_data['LOGIN_DATE'] = pd.to_datetime(raw_data['LOGIN_DATE'], errors='coerce')

    # Create a summary DataFrame
    summary = raw_data.groupby('USER_CODE').agg(
        TOTAL_LOGINS=('LOGIN_DATE', 'count'),
        FIRST_LOGIN=('LOGIN_DATE', 'min'),
        LAST_LOGIN=('LOGIN_DATE', 'max'),
        AVG_TIME_DIFF=('TIME_DIFF_BETWEEN_PREV_LOGIN', 'mean'),
        UNIQUE_DEVICES=('DEVICE_ID', lambda x: x.nunique()),
        LOGIN_DATES=('LOGIN_DATE', lambda x: ', '.join(pd.Series(x.dropna().unique()).dt.strftime('%Y-%m-%d')))
    ).reset_index()

    # Format results
    summary['AVG_TIME_DIFF'] = summary['AVG_TIME_DIFF'].round(2)

    return summary

if __name__ == "__main__":
    # File path to the Excel file containing raw data
    file_path = "login_data.xlsx"

    # Analyze the data
    summary_sheet = analyze_login_data(file_path)

    # Save the results to a new Excel file
    output_file = "login_data_summary.xlsx"
    summary_sheet.to_excel(output_file, index=False)

    print(f"Analysis complete! Summary saved to {output_file}")
```

---

## Usage Instructions

1. **Prepare the Input File:**
   - Ensure the input file (`login_data.xlsx`) is in the same directory as the script.
   - The file should have a sheet named `raw_data` with the required columns.

2. **Run the Script:**
   Execute the script using:
   ```bash
   python login_data_analysis.py
   ```

3. **Output File:**
   - The script will generate a new file named `login_data_summary.xlsx` in the same directory.
   - This file will contain the processed summary sheet with insights.

---

## Sample Output

| USER_CODE | TOTAL_LOGINS | FIRST_LOGIN | LAST_LOGIN | AVG_TIME_DIFF | UNIQUE_DEVICES | LOGIN_DATES         |
|-----------|--------------|-------------|------------|---------------|----------------|---------------------|
| 1001      | 5            | 2025-01-01  | 2025-01-10 | 24.50         | 3              | 2025-01-01, 2025-01-02 |
| 1002      | 3            | 2025-01-03  | 2025-01-05 | 36.75         | 2              | 2025-01-03, 2025-01-04 |

---

## Future Enhancements

- Add functionality to handle missing or invalid data more robustly.
- Include data visualization to identify login trends.
- Automate email notifications for flagged irregularities.

---

## License

This project is licensed under the MIT License. Feel free to use and modify it as needed!
