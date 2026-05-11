# 🧹 World Layoffs — SQL Data Cleaning Project

A structured SQL data cleaning project that prepares a real-world global layoffs dataset for exploratory data analysis (EDA). The project focuses on standardising inconsistent values, correcting data types, and removing unusable records to produce a clean, analysis-ready table.

---

## 📁 Dataset

The dataset (`world_layoffs`) tracks tech and corporate layoffs globally, including fields such as:

- `company` — Name of the company
- `country` — Country where layoffs occurred
- `total_laid_off` — Number of employees laid off
- `percentage_laid_off` — Percentage of workforce affected
- `funds_raised_millions` — Funding raised by the company
- `date` — Date of the layoff event

The raw data is loaded into a staging table (`layoffs_staging2`) to preserve the original dataset throughout cleaning.

---

## 🛠️ Data Cleaning Steps

### 1. Standardising Country Names

Identified and removed trailing periods from country values (e.g. `"United States."` → `"United States"`).

```sql
UPDATE layoffs_staging2
SET country = TRIM(TRAILING '.' FROM country);
```

### 2. Fixing Date Format & Data Type

The `date` column was stored as a string. Converted it to a proper `DATE` type using `STR_TO_DATE`.

```sql
UPDATE layoffs_staging2
SET `date` = STR_TO_DATE(`date`, '%m/%d/%Y');

ALTER TABLE layoffs_staging2
MODIFY COLUMN `date` DATE;
```

### 3. Handling NULL Values

Reviewed NULL values in `total_laid_off`, `percentage_laid_off`, and `funds_raised_millions`. These were intentionally left as `NULL` to avoid distorting aggregate calculations during the EDA phase.

### 4. Removing Unusable Rows

Deleted records where both `total_laid_off` and `percentage_laid_off` are `NULL`, as these rows provide no analytical value.

```sql
DELETE FROM world_layoffs.layoffs_staging2
WHERE total_laid_off IS NULL
AND percentage_laid_off IS NULL;
```

### 5. Dropping Helper Columns

Removed the `row_num` column that was used as a temporary identifier during earlier deduplication steps.

```sql
ALTER TABLE layoffs_staging2
DROP COLUMN row_num;
```

---

## 📂 File Structure

```
├── WORLD_LAYOFFS_data_cleaning.sql   # Main data cleaning script
└── README.md
```

---

## 🚀 How to Use

1. Import the raw layoffs dataset into your MySQL database under the schema `world_layoffs`.
2. Ensure the staging table `layoffs_staging2` exists (created in a prior step).
3. Run `WORLD_LAYOFFS_data_cleaning.sql` against your database.
4. The cleaned table is ready for EDA.

---

## 🔧 Tools & Technologies

- **MySQL** — All queries written in standard MySQL SQL
- **MySQL Workbench** (or any compatible client)

---

## 📌 Notes

- This script operates on the **staging table** (`layoffs_staging2`), not the original raw table, ensuring the source data is never modified.
- NULL values in numeric columns are preserved deliberately — they are handled appropriately during the EDA phase rather than imputed or dropped blindly.
- This cleaning script is part of a larger project; a separate script handles **deduplication** (creation of `layoffs_staging2` and the `row_num` column referenced here).

---

## 📊 Next Steps

With the data now cleaned, the next phase is **Exploratory Data Analysis (EDA)**, which will explore:

- Industries and countries most affected by layoffs
- Trends over time
- Correlation between funding raised and layoff size
- Companies with multiple rounds of layoffs

