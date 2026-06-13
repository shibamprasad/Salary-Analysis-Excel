# Data Science Salary Dashboard

An interactive Excel dashboard to explore median data science salaries — filterable by job title, country, and job type.

![Dashboard Preview](/Assets/Dashboard/dashboard.gif)

---

## 📊 Dashboard Features

Three interactive filters drive the entire dashboard simultaneously:

- **Job Title** — 10 roles from Business Analyst to Senior Data Scientist
- **Country** — 100+ countries from the global dataset
- **Job Type** — Full-time, Part-time, Contractor, Internship, Temp work

Each selection updates three visuals and three KPI cards instantly:

| Visual | What It Shows |
|--------|--------------|
| Horizontal bar chart | Median salary ranked across all job titles |
| Bing Maps chart | Geographic salary distribution by country |
| Column chart | Median salary breakdown by employment type |
| **Median Salary** KPI | Median salary for the selected combination |
| **Top Job Platform** KPI | Most active hiring platform for the selection |
| **Job Count** KPI | Number of postings matching the selection |

---

## ⚙️ How It's Built

- **Data Validation dropdowns** — feed the three filter cells from pre-validated lists
- **Helper sheets** (`title`, `Country`, `job_type`) — pre-calculate medians so the dashboard stays responsive
- **MEDIAN/IF array formulas** — compute conditional medians based on filter selections
- **XLOOKUP** — pulls the top platform and job count into the KPI cards
- **Median over average** — used throughout to avoid distortion from outliers like $525k hedge fund postings

---

## 📂 File

| File | Description |
|------|-------------|
| `salary_dashboard.xlsx` | Full workbook including raw data, helper sheets, and dashboard |