# sql-layoffs-data-cleaning
SQL data cleaning project using a global tech layoffs dataset (MySQL)

This project showcases a complete **data cleaning workflow in SQL** using a global tech layoffs dataset.  
The goal is to take a raw `layoffs` table and transform it into a cleaned version that is ready for analysis.

## 🧱 Dataset & Schema

- Original table: `layoffs`
- Staging tables:  
  - `layoffs_staging` – copy of the raw data used to safely experiment  
  - `layoffs_staging2` – cleaned version with helper column `row_num` (later removed)

The script is written for **MySQL** and was tested in **MySQL Workbench**.

## 🔄 Data Cleaning Steps

The `data_cleaning.sql` script performs the following tasks:

1. **Create staging tables**
   - Copies the original `layoffs` table into `layoffs_staging` and `layoffs_staging2` so the raw data is preserved.

2. **Identify and remove duplicate rows**
   - Uses `ROW_NUMBER()` window function over
     `(company, location, industry, total_laid_off, percentage_laid_off, date, stage, country, funds_raised_millions)`
     to flag duplicates.
   - Deletes rows where `row_num > 1`.

3. **Standardize text fields**
   - Trims extra spaces from `company`.
   - Normalizes `industry` values (e.g., sets all “Crypto%” variations to `"Crypto"`).
   - Cleans up `country` values (e.g., removes trailing `"."` from `"United States."`).

4. **Fix date formats**
   - Converts `date` from text (e.g., `'MM/DD/YYYY'`) to a proper `DATE` type
     using `STR_TO_DATE()` and `ALTER TABLE`.

5. **Handle nulls and blanks**
   - Converts empty strings in `industry` to `NULL`.
   - Fills in missing `industry` values by joining on the same company and location
     where another row has a non-null industry.

6. **Remove useless rows**
   - Deletes rows where both `total_laid_off` and `percentage_laid_off` are `NULL`
     since these records can’t be used in layoff analysis.

7. **Final cleanup**
   - Drops the helper `row_num` column once duplicates are removed.

## 🧠 SQL Concepts Used

This project demonstrates:

- Common Table Expressions (**CTEs**) with `WITH`
- **Window functions** (`ROW_NUMBER() OVER (PARTITION BY ...)`)
- **JOINs** for inferring missing values
- **String functions** (`TRIM`, `LIKE`)
- **Date parsing & type conversion** (`STR_TO_DATE`, `ALTER TABLE ... MODIFY`)
- Best practice of using **staging tables** for safe data cleaning

## ▶️ How to Run This Script

1. Have MySQL (or MySQL Workbench) installed.
2. Create and populate the original `layoffs` table with your raw dataset.
3. Open `data_cleaning.sql` in MySQL Workbench.
4. Make sure you are using the correct schema, for example:

   ```sql
   USE world_layoffs;  -- or your own schema name
