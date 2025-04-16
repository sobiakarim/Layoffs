#  SQL EDA Project on Tech Layoffs (2020–2022)

This project involves **data cleaning** and **exploratory data analysis (EDA)** using SQL, focusing on **layoffs across various companies between 2020 and 2022**. The objective is to identify trends, outliers, and patterns from a dataset of layoffs during the COVID-19 pandemic and beyond.

##  Data Cleaning Steps

Performed using MySQL in multiple stages:

1. **Duplicate Removal**  
   - Used `ROW_NUMBER()` to identify duplicate rows.
   - Removed entries with the same company, location, layoffs, and date fields.

2. **Standardization**  
   - Trimmed whitespace in fields like `company`, `country`, etc.
   - Standardized entries like `'Crypto & Blockchain'` to `'Crypto'`.

3. **Date Format Conversion**  
   - Converted string-formatted dates to `DATE` data type using `STR_TO_DATE`.

4. **Null Value Handling**  
   - Replaced blank industries by referencing other non-null rows with the same company.
   - Removed rows with both `total_laid_off` and `percentage_laid_off` missing.

5. **Column Cleanup**  
   - Dropped temporary columns like `row_num`.


##  Exploratory Data Analysis (EDA)

Key questions and insights explored:

- 📌 **Which company had the most layoffs?**
  ```sql
  SELECT company, SUM(total_laid_off)
  FROM layoffs_staging2
  GROUP BY company
  ORDER BY 2 DESC;
  ```

- 📌 **Which industry was hit the hardest?**
  ```sql
  SELECT industry, SUM(total_laid_off)
  FROM layoffs_staging2
  GROUP BY industry
  ORDER BY 2 DESC;
  ```

- 📌 **Layoffs by country**
  ```sql
  SELECT country, SUM(total_laid_off)
  FROM layoffs_staging2
  GROUP BY country
  ORDER BY 2 DESC;
  ```

- 📌 **Layoffs by year**
  ```sql
  SELECT YEAR(date), SUM(total_laid_off)
  FROM layoffs_staging2
  GROUP BY YEAR(date)
  ORDER BY 1 DESC;
  ```

- 📌 **Monthly layoffs trend with rolling total**
  ```sql
  WITH rolling_total AS (
    SELECT SUBSTRING(date, 1, 7) AS month, SUM(total_laid_off) AS total_off
    FROM layoffs_staging2
    GROUP BY month
  )
  SELECT month, total_off,
         SUM(total_off) OVER (ORDER BY month) AS rolling_total
  FROM rolling_total;
  ```

- 📌 **Top 5 companies with highest layoffs per year**
  ```sql
  WITH company_year AS (
    SELECT company, YEAR(date) AS years, SUM(total_laid_off) AS total_laid_off
    FROM layoffs_staging2
    GROUP BY company, YEAR(date)
  ),
  company_year_rank AS (
    SELECT *, DENSE_RANK() OVER (PARTITION BY years ORDER BY total_laid_off DESC) AS ranking
    FROM company_year
  )
  SELECT * FROM company_year_rank
  WHERE ranking <= 5;
  ```

---

## 🛠 Tools Used

- **MySQL**: For writing and executing SQL queries  
- **DBMS Client**: (MySQL Workbench)  
- **Spreadsheet Software**: For preliminary dataset review (Excel)

---

## 📈 Key Insights

- Certain startups had 100% layoffs, indicating complete shutdown.   
- Layoffs peaked in mid-2020 and again in late 2022.  
- U.S.-based companies dominated the layoff counts.  
- Fundraising did not always prevent layoffs—some companies with high capital still laid off large portions.

---

## 🚀 How to Use

1. Clone the repo:
   ```bash
   git clone https://github.com/sobiakarim/Layoffs
   ```

2. Import the dataset (`layoffs_dataset.csv`) into your SQL database.

3. Run the SQL scripts:
   - `data_cleaning.sql` to clean the dataset.
   - `eda_queries.sql` to analyze and explore.

---

## 📝 Dataset

https://github.com/sobiakarim/Layoffs/blob/main/layoffs_dataset.csv

