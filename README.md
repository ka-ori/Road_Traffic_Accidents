# ROAD SAFETY & ACCIDENT ANALYTICS

Analyzing traffic accident data to identify risk factors, geographic hotspots, and temporal patterns to inform road safety interventions.

* **Sector:** Public Infrastructure & Safety Analytics
* **Project Focus:** Spatiotemporal Hotspots & Mechanical Risk Profiling

## The System Snapshot

| Component | Specification / Detail |
| --- | --- |
| System Version | v1.2 (Post-Cleaning Audit) |
| Data Environment | Google Sheets Enterprise |
| Total Features | 32 Dimensional Columns |
| Cleaning Coverage | 100% (No raw placeholders remaining) |
| Deduplication | Verified Unique (1 row removed) |

---

## 1. Document Control

| Version | Date | Description of Changes |
| --- | --- | --- |
| v1.0 | 14-02-2026 | Initial data ingestion and cleaning logic. |
| v2.0 | 15-02-2026 | Analyzation of Data |
| v3.0 | 16-02-2026 | Pivot tables |
| v4.0 | 17-02-2026 | Dashboard |

---

## 2. Project Architecture & Data Dictionary

### 2.1 Technical Architecture Overview

* **Data Hosting:** Google Sheets

**Sheet Architecture & Navigation**

* **Tab_1 -> Raw Dataset [RTA_Dataset]** * **The "Golden Record":** This tab contains the original, untouched data exactly as it was first imported.
* **Immutable Rule:** No manual edits, formatting, or deletions are allowed here.


* **Tab_2 -> Cleaned_Dataset [Cleaned_Dataset]** * **The Processing Zone:** This is where the technical "heavy lifting" happens.
* **Action Taken:** All the formulas (like converting Gender to numbers, fixing typos, and creating time_category) are applied here.
* **Purpose:** It provides a "High-Quality" version of the data. This tab is used as the source for all calculations to ensure the results are accurate and standardized.


* **Tab_3 -> Analysis_&*Working [Analysis*&_Working]** * This is where Pivot Tables, frequency distributions, and summary statistics are built.
* **Tab_4 -> Dashboard [Dashboard]** * **Action Taken:** It features high-level Visual Analytics, including charts, heatmaps, and "Slicers" (filters) that allow users to interact with the data.
* **Purpose:** To turn complex data into "Actionable Insights." It answers the core project questions (e.g., "Where are the hotspots?") at a single glance.



### 2.2 Data Dictionary (The Schema)

#### A. Temporal & Geographic Identifiers

| Column Name | Data Type | Description | Constraints/Values |
| --- | --- | --- | --- |
| time | Time (HH:mm:ss) | Exact time of occurrence | 24-hour format |
| day_of_week | Categorical | Day name | Monday - Sunday |
| time_category | Derived (String) | Binned time periods | e.g., "Rush Hour", "Night" |
| area accident occured | String/Geo | Physical location name | Standardized list of districts |

#### B. Driver & Vehicle Profile

| Column Name | Data Type | Description | Notes |
| --- | --- | --- | --- |
| age band of driver | Ordinal | Age range of the driver | e.g., 18-30, 31-50 |
| driving_experience | Ordinal | Years of licensed driving | Used to calculate risk vs. skill |
| type_of_vehicle | Categorical | Classification of vehicle | e.g., Lorry, Public (>30 seats) |
| service_year_of_vehicle | Integer | Age of the vehicle | Check for "Unknown" values |
| vehicle_condition_risk | Boolean/Cat | Safety status of the car | Derived from Defect_of_vehicle |

#### C. Environmental & Road Conditions

| Column Name | Data Type | Description | Expected Values |
| --- | --- | --- | --- |
| road_surface_type | Categorical | Material of the road | Asphalt, Gravel, etc. |
| light_conditions | Categorical | Visibility levels | Daylight, Dark-Lighted, etc. |
| weather conditions | Categorical | Atmospheric state | Raining, Foggy, Clear |

#### D. Incident & Casualty Details

| Column Name | Data Type | Description | Metrics |
| --- | --- | --- | --- |
| type_of_collision | Categorical | Manner of impact | e.g., Head-on, Side-swipe |
| number of casualties | Integer | Total people injured/killed | Minimum: 1 |
| accident_severity | Ordinal/Target | Primary KPI | Slight, Serious, Fatal |

---

## 4. Data Transformation & Engineering Log

This section details the specific ETL (Extract, Transform, Load) operations performed to convert raw observational data into an analysis-ready dataset.

### 4.1 Structural & Schema Refinement

To ensure programmatic compatibility and ease of use in formulas, the following structural changes were made:

* **Header Standardization:** All column headers were converted to snake_case (e.g., Type of vehicle -> type_of_vehicle).
* **Dimensional Reduction:** Removed high-cardinality or low-relevance columns to optimize performance and focus: owner_of_vehicle, vehicle_driver_relation, work_of_casualty, fitness_of_casualty, sex_of_casualty.

### 4.2 Data Cleansing & Normalization

A rigorous cleaning pass was performed to handle noise and typos in the raw data.

| Task | Operation | Impact/Result |
| --- | --- | --- |
| Deduplication | Record-level scan | 1 duplicate identified and removed. |
| Sanitization | Whitespace Trimming | Applied TRIM() to prevent "Ghost Categories." |
| Typo Correction | Fuzzy matching | "statioUnknownry" to "stationary" (481 rows). |
| Value Mapping | Formatting | Standardized "yrs" and "yrss" to "years" for service year of vehicle. Replaced "RecreatioUnknownl areas" to "Recreational areas" in area accident occured. |
| Placeholder Fix | Character replacement | Replaced "?" with "Unknown" in type_of_vehicle. |

### 4.3 Feature Engineering & Encoding

New attributes were engineered to facilitate numerical analysis and temporal grouping.

**A. Temporal Encoding**
To analyze cyclical patterns, time was binned into categorical shifts:

* **Hour Extraction:** Isolated the integer hour from the HH:mm:ss string.
* **Shift Categorization:** Applied logic to create time category: Night (0-5), Morning (6-11), Afternoon (12-17), Evening (18-23).

**B. Categorical Binning & Grouping**
We reduced the "noise" in the educational_level column by aggregating specific tiers into broader, more statistically significant categories:

* **Low Education:** Illiterate, Reading and Writing.
* **School Education:** Elementary, Junior High, High School.
* **Higher Education:** Above High School.

**C. Boolean & Numerical Encoding**
For future correlation analysis, categorical text was converted into numerical flags:

* **Gender Encoding:** Male 1, Female - 0.
* **Day of Week:** Mapped Monday...Sunday - 1...7.
* **Vehicle Risk:** Created vehicle_condition risk as a risk-assessment flag:
* "No Defect" - Normal (Encoded as 0).
* Defect present - Risky.



### 4.4 Technical Formula Registry

The following logic was implemented within the Google Sheets environment to maintain a dynamic pipeline:

* **Temporal Binning:**
`=IF(HOUR(A2)<6,"Night",IF(HOUR(A2)<12,"Morning",IF(HOUR(A2)<18,"Afternoon", "Evening")))`
* **Gender Logic (Array):**
`=ArrayFormula(IFS(D2:D="Male", 1, D2:D="Female",0, D2:D="Unknown","Unknown"))`
* **Vehicle Risk Logic:**
`=ARRAYFORMULA(IF(J2:J="No defect", "Normal", IF(J2:J="Unknown", "Unknown", "Risky")))`

### 4.5 Data Quality Status (Post-Cleaning)

* **Total Columns:** 33 (Post-removal).
* **Null Handling:** All null, blank, and NA values were identified. Categorical blanks were imputed with "Unknown" or mode values to maintain row integrity.
* **Integrity:** The dataset now supports uniform aggregation across all 33 dimensions.

| Category | Column Name | Count | Impact |
| --- | --- | --- | --- |
| **Blank Values** | defect of vehicle | 4,429 | High: Requires assumption logic (No defect). |
|  | vehicle service | 3,930 | High: Impacts maintenance-risk correlation. |
|  | work of casualty | 3,200 | Moderate: Affects demographic profiling. |
|  | fitness_of_casualty | 2,637 | Moderate: Impacts human factor analysis. |
|  | educational level | 743 | Low: Managed via binning. |
| **"Unknown" Values** | driver_age_band | 1,548 | High: Skews age-based risk assessment. |
|  | vehicle movement | 88 | Low: Minimal impact on collision type. |
|  | cause_of accident | 25 | Low: Critical but small volume. |
| **"na" Values** | casualty_class | 4,443 | Critical: High systemic data entry failure. |
|  | sex_of_casualty | 4,443 | Critical: High systemic data entry failure. |
|  | accident_severity | 4,443 | Critical: Target variable inconsistency. |

---

## 5. KPI & Metric Framework

This section defines the primary indicators used to quantify road safety performance and the severity of incidents.

| KPI | Value | Business & Safety Significance |
| --- | --- | --- |
| **Total Casualty Volume** | 19,067 | The absolute scale of human impact; serves as the baseline for all safety interventions. |
| **Injury Severity Breakdown** | 1.28% Fatal | Critical lethality indicator; shifts focus from volume to high-consequence outcomes. |
| **Avg. Vehicle Involvement** | 2.04 | Measures collision dynamics; values > 2.0 indicate systemic interaction failures. |
| **Casualty-to-Vehicle Ratio** | 75.86% | Quantifies "Efficiency of Harm"-the likelihood of injury per vehicle involved in a crash. |
| **Peak Severity Index** | 2.16 | Identifies the most dangerous environmental/temporal conditions for resource allocation. |

### KPI Definitions & Formulas

* **Total Casualty Volume (19,067):** The baseline human impact. This metric established the sheer scale of the safety problem.
* **Formula:** `=SUM(number_of_casualties)`


* **Injury Severity Breakdown:** The distribution of harm: 84.56% Slight, 14.15% Serious, and 1.28% Fatal. This allows authorities to prioritize the 1.28% high-lethality incidents.
* **Average Vehicle Involvement (2.04):** A value > 2.0 proves accidents are predominantly multi-vehicle "interaction errors" rather than isolated mechanical failures.
* **Formula:** `=AVERAGE(number_of_vehicles_involved)`


* **Casualty-to-Vehicle Ratio (75.86%):** This measures "Efficiency of Harm" or occupant vulnerability per crash.
* **Formula:** `=SUM(number_of_casualties) / SUM(number_of_vehicles_involved)`


* **Peak Severity Index (2.16):** The maximum calculated risk within a specific segment (identified during Night hours). This identifies exactly where and when an accident is most likely to be fatal.
* **Formula:** `MAX (SUM(number_of_casualties) / COUNTA(accident_severity))` grouped by dimension.



---

## 6. Exploratory Data Analysis (EDA) & Pivot Summary

The following pivot table structures were utilized to isolate risk factors and temporal patterns.

### 6.1 Temporal Risk Analysis (The Night Surge)

* **Pivot Structure:**
* **Rows:** `time_category`
* **Values:** Average of Severity Index


* **Key Insight:** While traffic volume is highest in the Morning/Afternoon, the Severity Index peaks at 2.16 during Night hours. This indicates that darkness is a "Severity Multiplier," making incidents significantly more lethal during low-light conditions.

### 6.2 Infrastructure & Hotspot Profiling

* **Pivot Structure:**
* **Rows:** `area_accident_occured`
* **Values:** `COUNTA` of `accident_severity`


* **Key Insight:** "Office Areas" represent the highest geographic clustering for weekday incidents. This points to a failure in transit design during peak rush-hour windows (8 AM - 10 AM).

### 6.3 Behavioral & Demographic Analysis

* **Pivot Structure:**
* **Rows:** `driving_experience`, `age_band_of_driver`
* **Values:** `%` of Total Casualties


* **Key Insight:** A "complacency gap" was identified among experienced drivers. While junior drivers (<2 years) have a higher frequency of minor accidents, drivers with >10 years of experience are disproportionately represented in Serious/Fatal categories.

### 6.4 Mechanical vs. Human Factors

* **Pivot Structure:**
* **Rows:** `vehicle_condition_risk`
* **Values:** `number_of_casualties`


* **Key Insight:** Despite a high volume of missing data in vehicle maintenance columns, the cleaned data shows that over 75% of casualties are linked to behavioral errors (lane violations) rather than "Risky" vehicle conditions.

---

Would you like me to format this into a downloadable code block so you can easily copy and paste it into a fresh `.md` file?
