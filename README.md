# 📊 LinkedIn Job Market Analysis
### Exploratory Data Analysis with Pandas

## 📌 Project Overview

This project performs **Exploratory Data Analysis (EDA)** on a real-world **LinkedIn job postings dataset** using Python and Pandas. Through **10 hands-on exercises** — ranging from beginner to advanced — the notebook covers the full EDA workflow: loading, cleaning, filtering, aggregating, string manipulation, pivot tables, and correlation analysis.

> 🎯 **Goal:** Uncover insights from LinkedIn job data — which domains hire the most, which states have the highest demand, what compensation looks like across work types, and how views translate to applications.

---

## 📁 Dataset

| Property | Details |
|---|---|
| **File** | `LinkedIn_RDB_three.csv` |
| **Rows** | 6,328 jobs |
| **Columns** | 15 |
| **Source** | LinkedIn Job Postings |

### 🗂️ Key Columns

| Column | Description |
|---|---|
| `job_id` | Unique identifier for each job posting |
| `title` | Job title |
| `description` | Full job description text |
| `pay_period` | Payment frequency — YEARLY, HOURLY, MONTHLY |
| `work_type` | Full-time, Part-time, Contract, etc. |
| `job_location` | City and state of the job |
| `applies` | Number of applicants |
| `views` | Number of job views |
| `remote_allowed` | Whether remote work is allowed |
| `level` | Seniority level |
| `sponsored` | Whether the listing is sponsored |
| `compensation` | Salary/compensation amount |
| `job_domain` | Industry domain — Healthcare, IT, etc. |
| `company_id` | Company identifier |

---

## 🧰 Tech Stack

```
Python 3.10+
├── pandas          → Data loading, cleaning, analysis
├── numpy           → Numerical operations
├── matplotlib      → Data visualization
├── seaborn         → Statistical plots
└── warnings        → Suppress unnecessary warnings
```

---

## 📓 Exercises Overview

The notebook is structured into **3 difficulty levels** with 10 exercises total.

---

### 🟢 Beginner

#### Q1 — Basic Loading & Inspection
> Load the CSV into a DataFrame, display the first 10 rows, check the shape, and list all column names with data types.

```python
df = pd.read_csv('LinkedIn_RDB_three.csv')
df.head(10)
df.shape      # → (6328, 15)
df.info()
```
✅ **Finding:** Dataset has **6,328 rows** and **15 columns** with a mix of numeric, boolean, and object data types.

---

#### Q2 — Missing Values
> Find missing values per column, identify the column with the most nulls, and drop rows where both `compensation` and `level` are missing.

```python
df.isnull().sum()
df.isnull().sum().idxmax()
df = df.dropna(subset=['compensation', 'level'])
```
✅ **Finding:** The `level` column has the highest number of missing values. After cleaning, the dataset is significantly reduced but more reliable.

---

#### Q3 — Filtering
> Filter the dataset to show only **Full-time** jobs and count them.

```python
df[df['work_type'] == 'Full-time']
df[df['work_type'] == 'Full-time'].value_counts().sum()
```
✅ **Finding:** Full-time roles make up the dominant work type on LinkedIn, reflecting traditional hiring patterns.

---

### 🟡 Intermediate

#### Q4 — Value Counts & Grouping
> Find the top 5 most common `job_domain` values and calculate the average `views` and `applies` for each.

```python
top_5_job_domains = df['job_domain'].value_counts().head().index.tolist()
df_top_domains = df[df['job_domain'].isin(top_5_job_domains)]
avg_values = df_top_domains.groupby('job_domain')[['views', 'applies']].mean()
```
✅ **Finding:** Technology and Healthcare domains dominate LinkedIn postings and attract the most views and applicants.

---

#### Q5 — String Cleaning & Compensation Analysis
> Clean the `compensation` column (remove `$` and `,`), convert to float, and find the **median compensation** per `pay_period`.

```python
df['compensation'] = df['compensation'].str.replace(r'[$,]', '', regex=True).astype(float)
df['compensation'].groupby(df['pay_period']).median()
```
✅ **Finding:** Yearly-paid roles have the highest median compensation. Hourly roles reflect part-time or contract nature.

---

#### Q6 — Sorting & Ranking
> Find the **top 10 job titles** by views and check how many are sponsored vs. non-sponsored.

```python
top_views = df.sort_values(by='views', ascending=False).head(10)
top_views[['title', 'views']]
```
✅ **Finding:** Sponsored listings consistently appear among the most-viewed, confirming that promotion significantly boosts visibility.

---

### 🔴 Advanced

#### Q7 — GroupBy + Aggregation
> Group jobs by `work_type` and compute total job count, mean applies, and max views — sorted by mean applies.

```python
df.groupby('work_type').agg({
    "job_id": "count",
    "applies": "mean",
    "views": "max"
})
```
✅ **Finding:** Full-time jobs dominate in count, while contract and part-time roles show different applicant engagement patterns.

---

#### Q8 — Pivot Table
> Build a pivot table showing **average compensation** for every `work_type` × `pay_period` combination.

```python
df.pivot_table(
    index='work_type',
    columns='pay_period',
    values='compensation',
    aggfunc='mean'
)
```
✅ **Finding:** Full-time + YEARLY yields the highest average compensation. Part-time + HOURLY sits at the lower end.

---

#### Q9 — Apply & Lambda (Feature Engineering)
> Extract the **state abbreviation** from `job_location`, create a `demand` column (High/Low based on applies), and find the **top 3 states** by job postings.

```python
df['state'] = df['job_location'].apply(
    lambda x: x.split(',')[-1].strip()
)
df['demand'] = df['applies'].apply(
    lambda x: "High" if x > 100 else "Low"
)
df['state'].value_counts().head(3)
```
✅ **Finding:** A handful of major US states dominate LinkedIn job postings, reflecting key economic and tech hubs.

---

#### Q10 — Correlation & Conversion Rate
> Calculate the Pearson correlation between `views` and `applies`, create a `conversion_rate` column, and find which `job_domain` converts best.

```python
df[['views', 'applies']].corr()

df['conversion_rate'] = df['applies'] / df['views'].replace(0, float('nan'))
df.groupby('job_domain')['conversion_rate'].mean().sort_values(ascending=False).head(1)
```
✅ **Finding:** Views and applies show a positive correlation. Niche domains often convert viewers to applicants more effectively than high-volume ones.

---

## 💡 Key Insights Summary

| # | Insight |
|---|---|
| 1 | Dataset has **6,328 job postings** across **15 features** |
| 2 | `level` column has the most missing values — critical to handle before analysis |
| 3 | **Full-time** roles dominate LinkedIn postings |
| 4 | **Technology & Healthcare** are the top job domains |
| 5 | **Sponsored listings** attract significantly more views |
| 6 | **Full-time + YEARLY** = highest average compensation |
| 7 | A few US states account for the majority of job postings |
| 8 | Views and applies are **positively correlated** |
| 9 | Conversion rates vary significantly across job domains |
| 10 | Contract roles show higher hourly rates but fewer total applicants |

---
