# Data Science Salary & Skills Analysis (Excel)

An end-to-end Excel analytics project exploring **999+ real-world data science job postings** — built entirely with **Power Query**, **Power Pivot**, and **DAX** to answer the questions every job seeker actually cares about: *where does the money go, and what skills get me there?*

---

## 🎯 Project Goals

Most "what skills should I learn" advice is anecdotal. This project answers it with data, by digging into four questions:

1. How much more do US-based data roles pay compared to the rest of the world?
2. Which skills are most in-demand for each job title and country?
3. Which individual skills are associated with the highest pay — and is demand the same thing as value?
4. Does knowing more skills actually translate into a bigger paycheck?

---

## 🛠️ Tools & Techniques

- **Power Query (M)** — ETL pipeline: cleaning, reshaping, and unpivoting raw job posting data
- **Power Pivot** — relational data model linking job postings to individual skills
- **DAX** — custom measures for medians, ratios, and cross-table calculations
- **CROSSFILTER** — bidirectional filtering to solve a real data-modeling problem (see below)
- **PivotTables & PivotCharts** — interactive, filterable dashboards

---

## ⚙️ Data Model

![Power Pivot Model](/Assets/Analysis/power_pivot.gif)

Two tables, connected via a **one-to-many relationship on `job_id`**:

### `data_jobs_salary` — 999+ rows × 19 columns
One row per job posting. Built with Power Query:

- Stripped the `"via "` prefix from job platform names for cleaner labels
- Extracted the posting month from the full date
- Converted hourly rates to annual equivalents (`salary_hour_avg × 2080`) so hourly and salaried roles are comparable
- Added a unique `job_id` (starting at 10001) to act as the relationship key
- Reordered columns for readability

```
M Code excerpt:
#"adjusted hourly salary to yearly" = Table.AddColumn(#"Inserted Month",
    "adjusted_hourly_salary", each [salary_hour_avg] * 2080, Currency.Type)
```

### `data_jobs_skills` — exploded skill-level table
Sourced from `data_jobs_salary`, transformed into **one row per skill per job**:

- Stripped Python-style list formatting (`[`, `]`, `'`) left over from the source data
- Split the comma-delimited skill list into separate columns
- **Unpivoted** into a long, one-skill-per-row format — the key step that makes skill-level analysis possible
- Trimmed whitespace and standardized casing
- Fixed acronym capitalization (SQL, AWS, Power BI, NoSQL, GCP, SAS) so "sql" and "SQL" aren't treated as different skills

---

## 🧮 DAX Measures

**On `data_jobs_salary`:**
```dax
Job Count            = DISTINCTCOUNT(data_jobs_salary[job_id])
Median Salary        = MEDIAN(data_jobs_salary[salary_year_avg])
Median Salary US     = CALCULATE([Median Salary], data_jobs_salary[job_country] = "United States")
Median Salary Non US = CALCULATE([Median Salary], data_jobs_salary[job_country] <> "United States")
```

**On `data_jobs_skills`:**
```dax
Skills Count           = COUNT(data_jobs_skills[job_skills])
Skills Per Job         = DIVIDE([Skills Count], [Job Count])
Skill Percentage       = DIVIDE([Skills Count], [Job Count])
Median Salary - Skills = CALCULATE([Median Salary], CROSSFILTER(data_jobs_salary[job_id], data_jobs_skills[job_id], Both))
```

> 💡 **The CROSSFILTER problem:** Power Pivot relationships are one-directional by default, so filtering by a *skill* (in the skills table) couldn't naturally filter the *salary* (in the salary table). I solved this by wrapping the median salary calculation in `CROSSFILTER(..., Both)`, enabling bidirectional filtering on demand without permanently changing the relationship — a small detail that made every skill-level salary chart possible.

---

## 📊 Findings

### 1. US vs. Non-US Salary Gap

![Salary Analysis](/Assets/Analysis/salary_analysis.gif)

US-based roles pay significantly more across the board — but the gap **varies a lot by role**. The premium is widest for **Machine Learning Engineers**, who earn **$150K in the US vs. $101K elsewhere — a 49% gap**. Interestingly, **senior roles show the smallest US/non-US divergence**, suggesting that for top-end talent, the market is already global and pay tends to equalize.

### 2. Top Skills by Job Title & Country

![Skill Job Analysis](/Assets/Analysis/skill_job_analysis.gif)

Built a fully filterable dashboard (by job title and country) showing skill demand as a % of postings. For **Data Engineers**, **SQL appears in 70% of postings**, followed by **Python (65%)** and **AWS (43%)** — a clear signal of the core toolkit expected for that role, and a useful filter to check demand in any specific market.

### 3. Pay vs. Demand for Top Skills

![Skill Salary Analysis](/Assets/Analysis/skill_salary_analysis.png)

This is where it gets interesting: **demand and pay aren't the same thing.**

- **Python** has the highest median salary (**$98,500**) among top skills — despite not being the *most* requested.
- **Excel** shows up in **40% of postings** (one of the highest demand rates) but correlates with the **second-lowest median salary ($84,500)** — largely because it's so heavily tied to lower-paying analyst roles.

**Takeaway:** chasing the *most demanded* skill isn't the same as chasing the *highest-paying* one. Python, Oracle, and Tableau reward depth; Excel and Word reflect breadth but cap out lower.

### 4. Does Skill Count Drive Salary?

![Salary vs Skills](/Assets/Analysis/salary_skills.png)

Yes — and the trend holds across almost every role. **Senior Data Engineers** average **8.1 skills per posting** and earn a median of **$147,500**, while **Business Analysts** average just **3.3 skills** for **$85,000**. The relationship is roughly linear: more required skills generally tracks with higher pay, reinforcing the value of building a broad, technical skill stack.

---

## 🚀 Key Takeaways

- **Location still matters** — but mostly for mid-level roles, not senior ones.
- **SQL and Python are non-negotiable** for Data Engineers and Data Scientists.
- **Demand ≠ pay.** A skill being common (like Excel) doesn't mean it's the most valuable.
- **Stacking skills pays off** — the data shows a clear, near-linear relationship between skills-per-job and median salary.

---

## 📂 Project Files

| File | Description |
|------|-------------|
| `analysis.xlsx` | Full workbook — Power Query queries, Power Pivot data model, DAX measures, and all four analysis dashboards |
| `salary_dashboard.xlsx` | Standalone interactive dashboard view |

---

*Built with Excel, Power Query, Power Pivot, and DAX — no external tools required.*