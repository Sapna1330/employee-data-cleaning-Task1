# 👩‍💼 Employee Data Cleaning & Preparation

## 📌 Project Overview

This project focuses on cleaning and preparing an Employee dataset for reliable data analysis.

The dataset was cleaned using **Excel, Python (Pandas), and PostgreSQL (SQL)**. The project covers common real-world data-quality issues such as missing values, duplicates, inconsistent text values, invalid values, incorrect data types, and unformatted phone numbers.

The goal was to transform raw employee data into a cleaner and more analysis-ready dataset while preserving the original raw data.

---

## 🎯 Objectives

* Identify missing and invalid values
* Detect duplicate employee records
* Standardize inconsistent text values
* Clean and format phone numbers
* Handle missing values appropriately
* Convert columns to suitable data types
* Validate the cleaned dataset
* Create a separate cleaned dataset without modifying the raw data

---

## 📊 Dataset

The Employee dataset contains the following columns:

| Column            | Description                   |
| ----------------- | ----------------------------- |
| Employee_ID       | Unique employee identifier    |
| First_Name        | Employee first name           |
| Last_Name         | Employee last name            |
| Age               | Employee age                  |
| Department_Region | Department or region          |
| Status            | Employee employment status    |
| Join_Date         | Employee joining date         |
| Salary            | Employee salary               |
| Email             | Employee email address        |
| Phone             | Employee phone number         |
| Performance_Score | Employee performance category |
| Remote_Work       | Remote work status            |

---

## 🛠️ Tools & Technologies

* **Microsoft Excel** – Initial data inspection and cleaning
* **Python** – Data cleaning and validation
* **Pandas** – Data manipulation and missing-value analysis
* **PostgreSQL** – SQL-based data cleaning
* **pgAdmin 4** – PostgreSQL database management
* **GitHub** – Project version control and documentation

---

# 🔄 Data Cleaning Workflow

```text
Raw Employee Dataset
        ↓
     Excel
        ↓
Data Inspection & Basic Cleaning
        ↓
     Python
        ↓
Pandas Cleaning & Validation
        ↓
   PostgreSQL
        ↓
SQL Data Cleaning & Transformation
        ↓
 Clean Employee Dataset
        ↓
Ready for Data Analysis
```

---

# 1️⃣ Data Cleaning Using Excel

The raw dataset was first inspected and cleaned using Microsoft Excel.

### Tasks performed:

* Checked for blank cells
* Identified missing values
* Checked duplicate records
* Combined First Name and Last Name where required
* Checked inconsistent values
* Cleaned phone number formatting
* Used formulas for basic data transformation

### Example

Combining first and last names:

```excel
=A2&" "&B2
```

The raw dataset was preserved before making cleaning changes.

---

# 2️⃣ Data Cleaning Using Python & Pandas

Python was used to inspect the dataset and identify data-quality issues.

### Load the dataset

```python
import pandas as pd

df = pd.read_csv("Raw_Employee_dataset.csv")

print(df.head())
```

### Check dataset information

```python
print(df.info())
```

### Check missing values

```python
print(df.isnull().sum())
```

### Check total missing values

```python
print(df.isnull().sum().sum())
```

### Check duplicate records

```python
print(df.duplicated().sum())
```

### Check unique values

```python
print(df["Status"].unique())
print(df["Department_Region"].unique())
print(df["Performance_Score"].unique())
print(df["Remote_Work"].unique())
```

### Standardize text values

```python
df["First_Name"] = df["First_Name"].str.strip().str.title()
df["Last_Name"] = df["Last_Name"].str.strip().str.title()
```

### Clean phone numbers

```python
df["Phone"] = (
    df["Phone"]
    .astype(str)
    .str.replace(r"[^0-9]", "", regex=True)
)
```

### Export cleaned data

```python
df.to_csv("Cleaned_Employee_Dataset.csv", index=False)
```

---

# 3️⃣ Data Cleaning Using PostgreSQL & SQL

The raw dataset was imported into PostgreSQL as a raw table.

### Raw table

```text
employees
```

The raw table was kept unchanged to preserve the original dataset.

A separate cleaned table was created:

```text
employees_cleaned
```

---

## 🔍 Missing Value Analysis

Missing and blank values were checked using SQL.

```sql
SELECT
    COUNT(*) FILTER (
        WHERE employee_id IS NULL OR TRIM(employee_id) = ''
    ) AS employee_id_missing,

    COUNT(*) FILTER (
        WHERE first_name IS NULL OR TRIM(first_name) = ''
    ) AS first_name_missing,

    COUNT(*) FILTER (
        WHERE age IS NULL OR TRIM(age) = ''
    ) AS age_missing,

    COUNT(*) FILTER (
        WHERE salary IS NULL OR TRIM(salary) = ''
    ) AS salary_missing,

    COUNT(*) FILTER (
        WHERE phone IS NULL OR TRIM(phone) = ''
    ) AS phone_missing
FROM employees;
```

---

## 🔎 Duplicate Check

Duplicate Employee IDs were identified using:

```sql
SELECT
    employee_id,
    COUNT(*) AS duplicate_count
FROM employees
GROUP BY employee_id
HAVING COUNT(*) > 1;
```

---

## 🔤 Standardizing Text

Text fields were standardized using `TRIM()` and `INITCAP()`.

Example:

```sql
INITCAP(TRIM(first_name)) AS first_name
```

This helps convert inconsistent values such as:

```text
john
JOHN
 john
```

into:

```text
John
```

---

## 📱 Phone Number Cleaning

Non-numeric characters were removed using PostgreSQL regular expressions:

```sql
REGEXP_REPLACE(phone, '[^0-9]', '', 'g')
```

This keeps only numeric digits.

---

## 💰 Handling Invalid Salary Values

The raw dataset contained values such as:

```text
N/A
```

These were converted to `NULL` before converting Salary to a numeric data type.

```sql
NULLIF(NULLIF(TRIM(salary), ''), 'N/A')::NUMERIC
```

This prevents errors during type conversion while preserving the record.

---

# 🧹 Creating the Cleaned SQL Table

```sql
DROP TABLE IF EXISTS employees_cleaned;

CREATE TABLE employees_cleaned AS
SELECT
    NULLIF(TRIM(employee_id), '') AS employee_id,

    INITCAP(TRIM(first_name)) AS first_name,

    INITCAP(TRIM(last_name)) AS last_name,

    NULLIF(NULLIF(TRIM(age), ''), 'N/A')::INTEGER AS age,

    INITCAP(TRIM(department_region)) AS department_region,

    INITCAP(TRIM(status)) AS status,

    NULLIF(NULLIF(TRIM(join_date), ''), 'N/A')::DATE AS join_date,

    NULLIF(NULLIF(TRIM(salary), ''), 'N/A')::NUMERIC AS salary,

    LOWER(TRIM(email)) AS email,

    NULLIF(
        REGEXP_REPLACE(phone, '[^0-9]', '', 'g'),
        ''
    ) AS phone,

    INITCAP(TRIM(performance_score)) AS performance_score,

    INITCAP(TRIM(remote_work)) AS remote_work

FROM employees;
```

---

# ✅ Data Validation

After cleaning, the cleaned table was validated.

### Check cleaned data

```sql
SELECT *
FROM employees_cleaned
LIMIT 10;
```

### Check total records

```sql
SELECT COUNT(*) AS total_rows
FROM employees_cleaned;
```

### Check performance categories

```sql
SELECT
    performance_score,
    COUNT(*) AS employee_count
FROM employees_cleaned
GROUP BY performance_score
ORDER BY employee_count DESC;
```

---

# 📁 Project Structure

```text
employee-data-cleaning/
│
├── data/
│   ├── Raw_Employee_dataset.csv
│   └── Cleaned_Employee_Dataset.csv
│
├── excel/
│   └── Employee_Data_Cleaning.xlsx
│
├── python/
│   └── employee_data_cleaning.ipynb
│
├── sql/
│   └── employee_data_cleaning.sql
│
├── screenshots/
│   ├── raw_data.png
│   ├── excel_cleaning.png
│   ├── pandas_cleaning.png
│   ├── sql_cleaning.png
│   └── cleaned_data.png
│
└── README.md
```

---

# 📈 Key Data Cleaning Techniques

| Technique               | Excel | Pandas | SQL |
| ----------------------- | :---: | :----: | :-: |
| Missing Value Detection |   ✅   |    ✅   |  ✅  |
| Duplicate Detection     |   ✅   |    ✅   |  ✅  |
| Text Standardization    |   ✅   |    ✅   |  ✅  |
| Phone Cleaning          |   ✅   |    ✅   |  ✅  |
| Data Type Conversion    |   ✅   |    ✅   |  ✅  |
| Data Validation         |   ✅   |    ✅   |  ✅  |
| Data Transformation     |   ✅   |    ✅   |  ✅  |

---

# 💡 Key Learnings

Through this project, I practiced:

* Real-world data cleaning techniques
* Excel-based data preparation
* Python and Pandas for data preprocessing
* SQL data cleaning using PostgreSQL
* Handling missing and invalid values
* Identifying duplicate records
* Standardizing inconsistent data
* Converting raw text data into appropriate data types
* Preserving raw data before transformation
* Validating cleaned data before analysis

---

# 🚀 Future Improvements

The cleaned dataset can be used for further analysis such as:

* Employee salary analysis
* Department-wise employee analysis
* Performance analysis
* Remote work analysis
* Employee joining trends
* Age distribution
* Employee status analysis
* Interactive dashboards using Power BI or Excel

---

## 👩‍💻 Author

**Sapna Gupta**

Aspiring Data Analyst | Excel | SQL | Python | Pandas | Power BI

---

⭐ If you find this project useful, feel free to explore the repository and connect with me on LinkedIn.
