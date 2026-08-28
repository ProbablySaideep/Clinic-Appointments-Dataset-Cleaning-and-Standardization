# Clinic Appointments Dataset Cleaning & Standardization
An another end-to-end data cleaning and standardization project just like my previous Heathcare Dateset project, similar yet a bit tricky and different in terms of dealing with the cleaning of columns, workflow built using **Python** and **Pandas** on a messy clinic appointments dataset that I sourced from Kaggle (`messy_clinic_appointments.csv`). This project focuses on handling corrupted primary keys, multi-currency values, mixed datetime formats, categorical noise, and dataset standardization for downstream analytics.

---

## Project Highlights & Key Cleaning Workflow

All cleaning, factorizing, string manipulation, and type conversions were performed in the Jupyter Notebook (`Clinic Appointments Data Cleaning .ipynb`). Below are the specific techniques and logic implemented:

### 1. Advanced ID Architecture & Collision Prevention (`patient_id` -> `new_id`)
* **Problem**: The raw dataset reused non-unique `patient_id` values across different patients, creating data collision errors.
* **Solution**: 
  * Standardized `patient_name` using `.str.strip().str.title()` to ensure clean matching across records.
  * Used `pd.factorize(df['patient_name'])[0]` to generate unique integer IDs mapped to distinct patient names.
  * Applied an offset (`+ 1000`) so new IDs started from `1001` instead of `1`, preventing primary key collisions with legacy database records.
  * Dropped the old `patient_id` column and relocated `new_id` to index position `0`.

### 2. Categorical Standardization (`gender` & `follow_up_required`)
* **Problem**: Categorical fields contained inconsistent entries (`Male`, `Female`, `M`, `F`, `0`, `1`, `Yes`, `No`, `NaN`).
* **Solution**:
  * For `gender`: Stripped whitespaces, standardized casing, and applied direct replacement via `.replace({'Male':'M', 'Female':'F', '0':'', '1':'', 'Nan':''})`.
  * For `follow_up_required`: Stripped spaces and normalized binary indicators using `.replace({'0':'N', '1':'Y', 'Yes':'Y', 'No':'N'})`.

### 3. Complex Datetime Standardization (`appointment_date` & `booking_date`)
* **Problem**: Dates were heavily mixed (`YYYY/MM/DD`, `MM/DD/YYYY`, text formats like `September 26, 25`, `DD-Mon-YYYY`).
* **Solution**: Utilized `pd.to_datetime(..., format='mixed', dayfirst=True)` on both date columns to dynamically parse ambiguous strings while preserving European/Indian standard date structure.

### 4. Text Case & Whitespace Standardization (`doctor` & `department`)
* Standardized text columns (`doctor`, `department`) using chained string methods (`.str.strip().str.title()`) to eliminate boundary whitespaces and enforce uniform title casing across medical records.

### 5. Multi-Currency Stripping & Numeric Conversion (`billing_amount`)
* **Problem**: Billing records contained mixed international currency symbols (`$`, `€`, `£`, `Rs`, `rs`), space padding, and mixed string formats.
* **Solution**:
  * Lowercased the string values to catch all casing variations cleanly.
  * Applied boundary trimming via `.str.strip('rs$£€ ')` to clear currency markers and spaces.
  * Cast the column to numeric using `pd.to_numeric(..., errors='coerce')` to handle invalid or missing values gracefully.

### 6. Strategic Null Handling & Checkpoint Staging
* **Preserving Patient Records**: Replaced remaining missing values with standardized empty strings (`df.fillna('')`) prior to export, avoiding aggressive row deletion so valid appointment details remained intact.
* **Checkpointing**: Preserved intermediate dataframe states (`df2`, `df3`) at key transformation milestones to maintain historical checkpoints during testing.

---

## Key Technical Insights Learned

1. **`.replace()` vs `.map()` for Categorical Cleaning**:
   * **`.replace()`**: Targeted transformation that modifies specified keys while leaving untouched values intact in the container.
   * **`.map()`**: Whitelist approach that maps matching targets but converts everything else to `NaN`. Requires explicit `.fillna('')` handling to prevent accidental data loss.
2. **Avoiding Positional Parameter Errors in Pandas**:
   * Direct dictionary mappings inside `.str.replace()` raise a `TypeError`. Executed series-level replacements via `df['col'].replace(dict)` instead.
3. **Primary Key Offset Logic**:
   * Combining factorized arrays with scalar additions (`pd.factorize(...)[0] + 1000`) guarantees zero primary key overlap with legacy database records.

---

## Summary Statistics

| Feature | Raw Dataset (`messy_clinic_appointments.csv`) | Cleaned Dataset (`Clinic_Appointments_Cleaned_Data.csv`) |
| :--- | :--- | :--- |
| **Row Count** | 1,000 | 999 |
| **Column Count** | 10 | 10 |
| **Primary Key** | Reused / Non-unique `patient_id` | Collision-proof `new_id` starting from `1001` |
| **Date Format** | Mixed / Unparsed | Standardized datetime objects |
| **Billing Amount** | Mixed currency strings (`$`, `€`, `£`, `Rs`) | Standardized numeric float |
| **Follow-up Status** | Mixed (`Yes`, `No`, `1`, `0`) | Normalized (`Y`, `N`) |

---

## Repository Structure

* `Clinic Appointments Data Cleaning .ipynb`: Main Jupyter notebook containing step-by-step cleaning logic and code.
* `Clinic_Appointments_Cleaned_Data.csv`: Final processed, standardized dataset ready for analytics.
* `README.md`: Project documentation and workflow breakdown.
* `messy_clinic_appointments.csv`: Original raw messy clinic dataset sourced from Kaggle.

---

## Connect with me!
* If you're interested in my work, feel free to reach out!
* [<img src="https://cdn-icons-png.flaticon.com/512/174/174857.png" width="20" height="20" style="vertical-align:middle"> saideepmacherla](https://www.linkedin.com/in/saideepmacherla)
* [<img src="https://cdn.worldvectorlogo.com/logos/tableau-software.svg" width="20" height="20" style="vertical-align:middle"> Saideep Macherla - Vizzes](https://public.tableau.com/app/profile/saideep.macherla/vizzes)
* [<img src="https://cdn-icons-png.flaticon.com/512/732/732200.png" width="20" height="20" style="vertical-align:middle"> saideepmacherla@gmail.com](mailto:saideepmacherla@gmail.com)
