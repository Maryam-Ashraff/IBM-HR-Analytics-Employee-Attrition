# IBM HR Analytics - Employee Attrition Analysis

An end-to-end data analysis of the IBM HR Analytics Employee Attrition dataset, structured as a full business consulting engagement: understand the retention problem, audit and clean the data, engineer features, test hypotheses, and deliver recommendations an HR leadership team could actually act on.

![Python](https://img.shields.io/badge/Python-3.x-blue) ![Pandas](https://img.shields.io/badge/Pandas-2.x-orange) ![Scikit-learn](https://img.shields.io/badge/scikit--learn-1.x-red) ![License: MIT](https://img.shields.io/badge/License-MIT-green)

---

## Key findings

| Finding | Detail |
| --- | --- |
| **Overtime is the strongest driver** | Employees working overtime churn at **30.4%** vs **10.3%** for those who do not - roughly a **3x increase** in risk |
| **Sales is the most vulnerable role** | Sales Representatives churn at **39.1%**, followed by Laboratory Technicians at **23.2%** |
| **Low satisfaction precedes exit** | Employees rating `JobSatisfaction` as 1 churn at **22.5%**, vs **11.4%** for those rating it 4 |
| **Early-career staff are flight risks** | Employees with under 2 years of total working experience leave at a markedly higher rate |
| **Overall attrition** | **16.0%** across the 1,495-person workforce |

---

## The business problem

Unplanned turnover is a direct cost centre: recruitment spend, onboarding delay, lost productivity, and lost institutional knowledge. The objective here is to identify *why* employees leave, quantify which factors matter most, and translate that into concrete retention policy.

**Target variable:** `Attrition` (`Yes` = left, `No` = stayed)

---

## Dataset

`employee_attrition_course.csv` - 1,495 rows x 37 columns.

| Category | Key features |
| --- | --- |
| Demographics | `Age`, `Gender`, `MaritalStatus`, `DistanceFromHome`, `EducationField` |
| Job & experience | `Department`, `JobRole`, `JobLevel`, `TotalWorkingYears`, `YearsAtCompany` |
| Compensation | `MonthlyIncome`, `PercentSalaryHike`, `StockOptionLevel` |
| Satisfaction | `EnvironmentSatisfaction`, `JobSatisfaction`, `WorkLifeBalance`, `OverTime` |
| Dropped | `EmployeeCount`, `StandardHours`, `Over18` (constant), `RecordID`, `ManagerNotes` (>60% missing) |

---

## Methodology

The notebook runs a nine-step pipeline:

1. **Understand the business problem** - frame the retention question and key questions
2. **Understand the dataset** - dimensions, target variable, feature taxonomy
3. **Assess data quality** - missing values, duplicates, constant features, type errors, outliers
4. **Clean the dataset** - drop, standardize, impute, cast
5. **Prepare features** - target encoding, one-hot encoding, standardization
6. **Exploratory data analysis** - hypothesis-driven analysis with visualizations
7. **Engineer features** - derive five domain-specific features
8. **Executive summary** - findings and strategic recommendations
9. **Final business report** - consolidated, presentation-ready writeup

---

## Data quality findings

The raw dataset was genuinely dirty. Each issue below was identified, then resolved:

| Issue | Affected | Resolution |
| --- | --- | --- |
| Constant columns (zero variance) | `EmployeeCount`, `StandardHours`, `Over18` | Dropped - no predictive signal |
| Non-predictive IDs and redundant rates | `EmployeeNumber`, `RecordID`, `DailyRate`, `HourlyRate`, `MonthlyRate` | Dropped to reduce noise and overfitting |
| Severe missingness | `ManagerNotes` (>60% `NaN`) | Column removed |
| Whitespace and naming noise | `Gender` (`" Male "`), `BusinessTravel` (`Travel Rarely`) | Stripped and unified categories |
| Invalid / unrealistic values | `Age` 16 and 78, `DistanceFromHome` up to 150, `MonthlyIncome` up to 500,000 | Treated as data-entry errors, set to `NaN` for imputation |
| Missing values | `Age`, `MonthlyIncome`, `TotalWorkingYears`, `YearsAtCompany`, `EducationField`, `JobRole` | Median for numeric, mode for categorical |
| Wrong dtypes (`float64` from `NaN`s) | `Age`, `TotalWorkingYears`, `YearsAtCompany`, `DistanceFromHome` | Cast back to `int64` |

**Outliers detected by the IQR method:** `YearsSinceLastPromotion` (109), `MonthlyIncome` (105), `YearsAtCompany` (67), `TotalWorkingYears` (63).

---

## Feature engineering

Five custom features were derived to add business meaning beyond the raw columns:

| Feature | Logic | Why it matters |
| --- | --- | --- |
| `Income_per_Working_Year` | `MonthlyIncome / (TotalWorkingYears + 1)` | Normalises pay against career experience to expose underpaid staff |
| `Experience_Level` | `Junior` / `Mid` / `Senior` / `Expert` | Bands continuous years into career stages |
| `Promotion_Rate` | `YearsSinceLastPromotion / (YearsAtCompany + 1)` | Flags stagnated employees who are overdue for promotion |
| `Tenure_Group` | `0-1yr` / `1-3yr` / `3-5yr` / `5yr+` | New hires and veterans have different attrition drivers |
| `High_Income_Indicator` | `1` if above median `MonthlyIncome` | Fast binary segmentation for reporting and tree models |

Preprocessing also applies one-hot encoding via `pd.get_dummies(drop_first=True)` and z-score scaling with `StandardScaler`.

---

## Recommendations

1. **Govern overtime** - cap consecutive overtime, audit workload distribution, compensate mandatory overtime with time off or retention bonuses
2. **Benchmark junior and high-risk pay** - Sales Representatives and Lab Technicians need market-aligned compensation and clear progression roadmaps
3. **Run quarterly satisfaction pulse surveys** - trigger a stay interview automatically when a satisfaction score drops to 1 or 2
4. **Pilot, then model** - run a 90-day overtime pilot in Sales, then build a Random Forest / XGBoost classifier to score attrition risk

---

## Tech stack

- **Analysis:** Python, Pandas, NumPy
- **Visualization:** Matplotlib, Seaborn
- **Preprocessing:** Scikit-learn (`StandardScaler`)
- **Environment:** Jupyter Notebook

---

## How to run

```bash
git clone https://github.com/Maryam-Ashraff/IBM-HR-Analytics-Employee-Attrition.git
cd IBM-HR-Analytics-Employee-Attrition
pip install -r requirements.txt
jupyter notebook IBM_Employee_Attrition.ipynb
```

The notebook expects `employee_attrition_course.csv` in the repository root.

---

## Repository structure

```text
IBM-HR-Analytics-Employee-Attrition/
├── IBM_Employee_Attrition.ipynb   # Main analysis notebook
├── employee_attrition_course.csv  # Source dataset
├── presentation.html              # Rendered visual report
├── requirements.txt               # Pinned dependencies
└── README.md
```

---

## Author

**Maryam Ashraf** - Computer Science student, University of Sadat City
[GitHub](https://github.com/Maryam-Ashraff)

## License

MIT - see [LICENSE](LICENSE).
