# ROAD SAFETY & ACCIDENT ANALYTICS

[cite_start]Analyzing traffic accident data to identify risk factors, geographic hotspots, and temporal patterns to inform road safety interventions. [cite: 37]

* [cite_start]**Sector:** Public Infrastructure & Safety Analytics [cite: 38]
* [cite_start]**Project Focus:** Spatiotemporal Hotspots & Mechanical Risk Profiling [cite: 39]

## The System Snapshot

| Component | Specification / Detail |
| :--- | :--- |
| [cite_start]System Version | v1.2 (Post-Cleaning Audit) [cite: 41] |
| Data Environment | [cite_start]Google Sheets Enterprise [cite: 41] |
| Total Features | [cite_start]32 Dimensional Columns [cite: 41] |
| Cleaning Coverage | [cite_start]100% (No raw placeholders remaining) [cite: 41] |
| Deduplication | [cite_start]Verified Unique (1 row removed) [cite: 41] |

---

## 1. Document Control

| Version | Date | Description of Changes |
| :--- | :--- | :--- |
| v1.0 | 14-02-2026 | [cite_start]Initial data ingestion and cleaning logic. [cite: 43] |
| v2.0 | 15-02-2026 | [cite_start]Analyzation of Data [cite: 43] |
| v3.0 | 16-02-2026 | [cite_start]Pivot tables [cite: 43] |
| v4.0 | 17-02-2026 | [cite_start]Dashboard [cite: 43] |

---

## 2. Project Architecture & Data Dictionary

### 2.1 Technical Architecture Overview

* [cite_start]**Data Hosting:** Google Sheets [cite: 46]

**Sheet Architecture & Navigation**

* [cite_start]**Tab_1 -> Raw Dataset [RTA_Dataset]** [cite: 48]
    * [cite_start]**The "Golden Record":** This tab contains the original, untouched data exactly as it was first imported. [cite: 49]
    * [cite_start]**Immutable Rule:** No manual edits, formatting, or deletions are allowed here. [cite: 50]
* [cite_start]**Tab_2 -> Cleaned_Dataset [Cleaned_Dataset]** [cite: 51]
    * [cite_start]**The Processing Zone:** This is where the technical "heavy lifting" happens. [cite: 52]
    * [cite_start]**Action Taken:** All the formulas (like converting Gender to numbers, fixing typos, and creating time_category) are applied here. [cite: 53]
    * **Purpose:** It provides a "High-Quality" version of the data. [cite_start]This tab is used as the source for all calculations to ensure the results are accurate and standardized. [cite: 54]
* [cite_start]**Tab_3 -> Analysis_&_Working [Analysis_&_Working]** [cite: 55]
    * [cite_start]This is where Pivot Tables, frequency distributions, and summary statistics are built. [cite: 56]
* [cite_start]**Tab_4 -> Dashboard [Dashboard]** [cite: 57]
    * [cite_start]**Action Taken:** It features high-level Visual Analytics, including charts, heatmaps, and "Slicers" (filters) that allow users to interact with the data. [cite: 58]
    * **Purpose:** To turn complex data into "Actionable Insights." [cite_start]It answers the core project questions (e.g., "Where are the hotspots?") at a single glance. [cite: 59]

### 2.2 Data Dictionary (The Schema)

#### A. Temporal & Geographic Identifiers

| Column Name | Data Type | Description | Constraints/Values |
| :--- | :--- | :--- | :--- |
| time | Time (HH:mm:ss) | Exact time of occurrence | [cite_start]24-hour format [cite: 62] |
| day_of_week | Categorical | Day name | [cite_start]Monday - Sunday [cite: 62] |
| time_category | Derived (String) | [cite_start]Binned time periods | e.g., "Rush Hour", "Night" [cite: 63] |
| area accident occured | String/Geo | Physical location name | [cite_start]Standardized list of districts [cite: 63] |

#### B. Driver & Vehicle Profile

| Column Name | Data Type | Description | Notes |
| :--- | :--- | :--- | :--- |
| age band of driver | Ordinal | [cite_start]Age range of the driver | e.g., 18-30, 31-50 [cite: 65] |
| driving_experience | Ordinal | Years of licensed driving | [cite_start]Used to calculate risk vs. skill [cite: 65] |
| type_of_vehicle | Categorical | [cite_start]Classification of vehicle | e.g., Lorry, Public (>30 seats) [cite: 65] |
| service_year_of_vehicle | Integer | Age of the vehicle | [cite_start]Check for "Unknown" values [cite: 65] |
| vehicle_condition_risk | Boolean/Cat | Safety status of the car | [cite_start]Derived from Defect_of_vehicle [cite: 65] |

#### C. Environmental & Road Conditions

| Column Name | Data Type | Description | Expected Values |
| :--- | :--- | :--- | :--- |
| road_surface_type | Categorical | Material of the road | [cite_start]Asphalt, Gravel, etc. [cite: 67] |
| light_conditions | Categorical | Visibility levels | [cite_start]Daylight, Dark-Lighted, etc. [cite: 67] |
| weather conditions | Categorical | Atmospheric state | [cite_start]Raining, Foggy, Clear [cite: 67] |

#### D. Incident & Casualty Details

| Column Name | Data Type | Description | Metrics |
| :--- | :--- | :--- | :--- |
| type_of_collision | Categorical | [cite_start]Manner of impact | e.g., Head-on, Side-swipe [cite: 73, 74, 75, 76] |
| number of casualties | Integer | Total people injured/killed | [cite_start]Minimum: 1 [cite: 77] |
| accident_severity | Ordinal/Target | Primary KPI | [cite_start]Slight, Serious, Fatal [cite: 77] |

---

## 4. Data Transformation & Engineering Log

[cite_start]This section details the specific ETL (Extract, Transform, Load) operations performed to convert raw observational data into an analysis-ready dataset. [cite: 79]

### 4.1 Structural & Schema Refinement

[cite_start]To ensure programmatic compatibility and ease of use in formulas, the following structural changes were made: [cite: 81]

* [cite_start]**Header Standardization:** All column headers were converted to snake_case (e.g., Type of vehicle -> type_of_vehicle). [cite: 82]
* [cite_start]**Dimensional Reduction:** Removed high-cardinality or low-relevance columns to optimize performance and focus: owner_of_vehicle, vehicle_driver_relation, work_of_casualty, fitness_of_casualty, sex_of_casualty. [cite: 83, 84]

### 4.2 Data Cleansing & Normalization

[cite_start]A rigorous cleaning pass was performed to handle noise and typos in the raw data. [cite: 86]

| Task | Operation | Impact/Result |
| :--- | :--- | :--- |
| Deduplication | Record-level scan | [cite_start]1 duplicate identified and removed. [cite: 91] |
| Sanitization | Whitespace Trimming | [cite_start]Applied TRIM() to prevent "Ghost Categories." [cite: 92] |
| Typo Correction | Fuzzy matching | [cite_start]"statioUnknownry" to "stationary" (481 rows). [cite: 92] |
| Value Mapping | Formatting | Standardized "yrs" and "yrss" to "years" for service year of vehicle. [cite_start]Replaced "RecreatioUnknownl areas" to "Recreational areas" in area accident occured. [cite: 92] |
| Placeholder Fix | Character replacement | [cite_start]Replaced "?" with "Unknown" in type_of_vehicle. [cite: 92] |

### 4.3 Feature Engineering & Encoding

[cite_start]New attributes were engineered to facilitate numerical analysis and temporal grouping. [cite: 94]

**A. Temporal Encoding**
[cite_start]To analyze cyclical patterns, time was binned into categorical shifts: [cite: 96]
* [cite_start]**Hour Extraction:** Isolated the integer hour from the HH:mm:ss string. [cite: 97]
* [cite_start]**Shift Categorization:** Applied logic to create time category: Night (0-5), Morning (6-11), Afternoon (12-17), Evening (18-23). [cite: 98, 99]

**B. Categorical Binning & Grouping**
[cite_start]We reduced the "noise" in the educational_level column by aggregating specific tiers into broader, more statistically significant categories: [cite: 101]
* [cite_start]**Low Education:** Illiterate, Reading and Writing. [cite: 102]
* [cite_start]**School Education:** Elementary, Junior High, High School. [cite: 103]
* [cite_start]**Higher Education:** Above High School. [cite: 104]

**C. Boolean & Numerical Encoding**
[cite_start]For future correlation analysis, categorical text was converted into numerical flags: [cite: 106]
* [cite_start]**Gender Encoding:** Male 1, Female - 0. [cite: 107]
* [cite_start]**Day of Week:** Mapped Monday...Sunday - 1...7. [cite: 108]
* [cite_start]**Vehicle Risk:** Created vehicle_condition risk as a risk-assessment flag: [cite: 109]
    * [cite_start]"No Defect" - Normal (Encoded as 0). [cite: 110]
    * [cite_start]Defect present - Risky. [cite: 111]

### 4.4 Technical Formula Registry

[cite_start]The following logic was implemented within the Google Sheets environment to maintain a dynamic pipeline: [cite: 113]

* **Temporal Binning:**
    [cite_start]`=IF(HOUR(A2)<6,"Night",IF(HOUR(A2)<12,"Morning",IF(HOUR(A2)<18,"Afternoon", "Evening")))` [cite: 114, 115]
* **Gender Logic (Array):**
    [cite_start]`=ArrayFormula(IFS(D2:D="Male", 1, D2:D="Female",0, D2:D="Unknown","Unknown"))` [cite: 116, 117]
* **Vehicle Risk Logic:**
    [cite_start]`=ARRAYFORMULA(IF(J2:J="No defect", "Normal", IF(J2:J="Unknown", "Unknown", "Risky")))` [cite: 118, 119]

### 4.5 Data Quality Status (Post-Cleaning)

* [cite_start]**Total Columns:** 33 (Post-removal). [cite: 121]
* **Null Handling:** All null, blank, and NA values were identified. [cite_start]Categorical blanks were imputed with "Unknown" or mode values to maintain row integrity. [cite: 122, 124]
* [cite_start]**Integrity:** The dataset now supports uniform aggregation across all 33 dimensions. [cite: 124]

| Category | Column Name | Count | Impact |
| :--- | :--- | :--- | :--- |
| **Blank Values** | defect of vehicle | 4,429 | [cite_start]High: Requires assumption logic (No defect). [cite: 126] |
| | vehicle service | 3,930 | [cite_start]High: Impacts maintenance-risk correlation. [cite: 126] |
| | work of casualty | 3,200 | [cite_start]Moderate: Affects demographic profiling. [cite: 126] |
| | fitness_of_casualty | 2,637 | [cite_start]Moderate: Impacts human factor analysis. [cite: 126] |
| | educational level | 743 | [cite_start]Low: Managed via binning. [cite: 126] |
| **"Unknown" Values** | driver_age_band | 1,548 | [cite_start]High: Skews age-based risk assessment. [cite: 126] |
| | vehicle movement | 88 | [cite_start]Low: Minimal impact on collision type. [cite: 126] |
| | cause_of accident | 25 | [cite_start]Low: Critical but small volume. [cite: 126] |
| **"na" Values** | casualty_class | 4,443 | [cite_start]Critical: High systemic data entry failure. [cite: 126] |
| | sex_of_casualty | 4,443 | [cite_start]Critical: High systemic data entry failure. [cite: 126] |
| | accident_severity | 4,443 | [cite_start]Critical: Target variable inconsistency. [cite: 126, 127] |

---

## 5. KPI & Metric Framework

[cite_start]This section defines the primary indicators used to quantify road safety performance and the severity of incidents. [cite: 129]

| KPI | Value | Business & Safety Significance |
| :--- | :--- | :--- |
| **Total Casualty Volume** | 19,067 | [cite_start]The absolute scale of human impact; serves as the baseline for all safety interventions. [cite: 130] |
| **Injury Severity Breakdown** | 1.28% Fatal | [cite_start]Critical lethality indicator; shifts focus from volume to high-consequence outcomes. [cite: 130] |
| **Avg. Vehicle Involvement** | 2.04 | [cite_start]Measures collision dynamics; values > 2.0 indicate systemic interaction failures. [cite: 130] |
| **Casualty-to-Vehicle Ratio** | 75.86% | [cite_start]Quantifies "Efficiency of Harm"-the likelihood of injury per vehicle involved in a crash. [cite: 130] |
| **Peak Severity Index** | 2.16 | [cite_start]Identifies the most dangerous environmental/temporal conditions for resource allocation. [cite: 130] |

### KPI Definitions & Formulas

* **Total Casualty Volume (19,067):** The baseline human impact. [cite_start]This metric established the sheer scale of the safety problem. [cite: 143]
    * [cite_start]**Formula:** `=SUM(number_of_casualties)` [cite: 136]
* **Injury Severity Breakdown:** The distribution of harm: 84.56% Slight, 14.15% Serious, and 1.28% Fatal. [cite_start]This allows authorities to prioritize the 1.28% high-lethality incidents. [cite: 145, 146]
* [cite_start]**Average Vehicle Involvement (2.04):** A value $>2.0$ proves accidents are predominantly multi-vehicle "interaction errors" rather than isolated mechanical failures. [cite: 148]
    * [cite_start]**Formula:** `=AVERAGE(number_of_vehicles_involved)` [cite: 148]
* [cite_start]**Casualty-to-Vehicle Ratio (75.86%):** This measures "Efficiency of Harm" or occupant vulnerability per crash. [cite: 149]
    * [cite_start]**Formula:** `=SUM(number_of_casualties) / SUM(number_of_vehicles_involved)` [cite: 137, 138]
* **Peak Severity Index (2.16):** The maximum calculated risk within a specific segment (identified during Night hours). [cite_start]This identifies exactly where and when an accident is most likely to be fatal. [cite: 150, 151]
    * [cite_start]**Formula:** `MAX (SUM(number_of_casualties) / COUNTA(accident_severity))` grouped by dimension. [cite: 141, 142]

---

## 6. Exploratory Data Analysis (EDA) & Pivot Summary

[cite_start]The following pivot table structures were utilized to isolate risk factors and temporal patterns. [cite: 153]

### 6.1 Temporal Risk Analysis (The Night Surge)

* **Pivot Structure:**
    * [cite_start]**Rows:** `time_category` [cite: 155]
    * [cite_start]**Values:** Average of Severity Index [cite: 156]
* **Key Insight:** While traffic volume is highest in the Morning/Afternoon, the Severity Index peaks at 2.16 during Night hours. [cite_start]This indicates that darkness is a "Severity Multiplier," making incidents significantly more lethal during low-light conditions. [cite: 157, 158]

### 6.2 Infrastructure & Hotspot Profiling

* **Pivot Structure:**
    * [cite_start]**Rows:** `area_accident_occured` [cite: 162]
    * [cite_start]**Values:** `COUNTA` of `accident_severity` [cite: 163]
* **Key Insight:** "Office Areas" represent the highest geographic clustering for weekday incidents. [cite_start]This points to a failure in transit design during peak rush-hour windows (8 AM - 10 AM). [cite: 164, 165]

### 6.3 Behavioral & Demographic Analysis

* **Pivot Structure:**
    * [cite_start]**Rows:** `driving_experience`, `age_band_of_driver` [cite: 168]
    * [cite_start]**Values:** `%` of Total Casualties [cite: 169]
* **Key Insight:** A "complacency gap" was identified among experienced drivers. [cite_start]While junior drivers (<2 years) have a higher frequency of minor accidents, drivers with >10 years of experience are disproportionately represented in Serious/Fatal categories. [cite: 170, 171]

### 6.4 Mechanical vs. Human Factors

* **Pivot Structure:**
    * [cite_start]**Rows:** `vehicle_condition_risk` [cite: 176]
    * [cite_start]**Values:** `number_of_casualties` [cite: 177]
* [cite_start]**Key Insight:** Despite a high volume of missing data in vehicle maintenance columns, the cleaned data shows that over 75% of casualties are linked to behavioral errors (lane violations) rather than "Risky" vehicle conditions. [cite: 178]
