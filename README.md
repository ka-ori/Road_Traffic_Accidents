# Road_Traffic_Accidents

# Data Cleaning & Transformation Documentation: RTA Dataset


## 1. Text Standardization & Typo Correction
To satisfy the "Dictionary Check" requirements, text values must be uniform to prevent fragmented charts.

### A. The "Stationary" Typo
A common error in the dataset is the replacement of the word "stationary" with a fragmented string.
* **Target Column:** `Pedestrian_movement`
* **Change:** Replace all instances of `statioNot a Pedestrianry` with `stationary`.

### B. Missing Value Consolidation
The dataset uses multiple ways to represent missing data (`na`, `unknown`, `Unknown`, and blank cells).
* **Action:** Perform a global "Find and Replace" or use a logic-based fill.
* **Standard:** Map all variations to a single, capitalized string: `Unknown`.

### C. Case Sensitivity
Google Sheets pivot tables are case-sensitive. 
* **Action:** Convert all categorical columns to "Proper Case" or ensure no strings like `other` and `Other` coexist in the same column.

## 2. Feature Engineering (For Advanced Analysis)
Static data should be transformed into dynamic categories to provide better insights.

### A. Temporal Extraction
The `Time` column is stored as a string (HH:MM:SS), which is difficult to plot.
* **Transformation:** Create a new column named `Hour`. 
* **Logic:** Extract the first two characters of the `Time` string and convert to an integer (e.g., "17:02:00" -> `17`). This allows for hourly accident distribution charts.

### B. Categorical Bucketing (Experience Level)
The `Driving_experience` column contains too many granular categories (1-2yr, 2-5yr, etc.).
* **Transformation:** Group these into broader bins:
    * **Novice:** <1yr / No Licence
    * **Junior:** 1-2yr / 2-5yr
    * **Senior:** 5-10yr
    * **Expert:** Above 10yr

## 3. Data Type Validation
Ensure Google Sheets recognizes the data correctly for mathematical operations.
* **Numerical Check:** Ensure `Number_of_vehicles_involved` and `Number_of_casualties` are formatted as **Numbers**, not **Plain Text**. 
* **Severity Labels:** While the Kaggle version converted `Accident_severity` to numbers (1, 2), retain the text labels (`Slight Injury`, `Serious Injury`, `Fatal injury`). Text labels are more intuitive for dashboard filters and stakeholder readability.

## 4. Final Dictionary Check (Post-Cleaning)
Before finalizing the "Master Sheet," verify the unique counts of categories.
* **Validation Step:** Use `=UNIQUE(A:A)` in a separate sheet for every categorical column. If you see both `Unknown` and `na`, or `Asphalt` and `asphalt`, the cleaning is incomplete.
