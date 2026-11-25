# Crop Yield EDA — Study Notes
Student: Zeeshan Khan | ERP: 32133

Files: `solution.py` (full code), `output.txt` (every printed result), `crop_yield_cleaned.csv`, `yield_pivot_by_date.csv`, `total_yield_by_crop.png`, `crop_yield_trend.png`, `crop_yield_trend_monthly.png` (extra).

## What each step does and why

| # | Task | Code | Idea to remember |
|---|------|------|------------------|
| 1 | Load | `pd.read_csv("crop_yield.csv")` | A DataFrame is a table. Shape was 3000 rows x 9 columns. |
| 2 | Preview | `crop_df.head(10)` | Always look at real rows before analysing. |
| 3 | Missing values | `crop_df.isnull().sum()` | `isnull()` gives True/False per cell; `sum()` counts the Trues per column. Found: YIELD_KG 330, AREA_ACRES 144, DISTRICT 126, FERTILIZER_USED 318. |
| 4 | Types | `crop_df.dtypes` | Dates and IDs came in as text. YIELD_KG is float because NaN cannot live in an integer column. |
| 5 | Numeric summary | `crop_df.describe()` | count/mean/std/min/quartiles/max. `count` is lower than 3000 where data is missing. |
| 6 | Text summary | `crop_df.describe(include="object")` | count, unique, top (most common), freq. |
| 7 | Drop missing yield | `dropna(subset=["YIELD_KG"])` | Yield is the main measure, so rows without it are useless. 3000 -> 2670 rows. |
| 8 | Fill area | `fillna(mean)` | Mean imputation keeps the row. Mean used = 4.4127 (computed after step 7). |
| 9 | Fill district | `fillna("Unknown")` | Text columns cannot use a mean, so use a label. |
| 10 | Fill fertilizer | `fillna("No")` | Assumes missing means not used. |
| 11 | Fill yield value | `fillna(mean)` | YIELD_VALUE had no missing values, so nothing changed (still valid to run). |
| 12 | Dates | `pd.to_datetime(..., format="%d/%m/%Y", errors="coerce")` | `%d/%m/%Y` = day/month/year. `coerce` turns bad dates into NaT instead of crashing. None were bad. |
| 13 | FARM_ID | `.str.replace("F","").astype(int)` | `F001` -> `"001"` -> `1`. Leading zeros vanish when it becomes an int. |
| 14 | Re-check missing | `isnull().sum()` | All zeros now. |
| 15 | Re-check types | `dtypes` | FARM_ID int64, HARVEST_DATE datetime64. |
| 16 | Total by crop | `groupby("CROP_TYPE").agg({"YIELD_KG":"sum"})` | Split by group, then sum each group. |
| 17 | Bar chart | `.plot(kind="bar")` | Set title, y-label. `plt.savefig` saves the image. |
| 18 | Pivot + line chart | `pivot_table(values, index, aggfunc="sum")` | A pivot table is a groupby shown as a table. |

## Results
Total yield (KG) by crop: Wheat 2,035,500 > Rice 1,638,000 > Maize 1,112,500 > Sugarcane 592,000.

## Things worth noticing (good for viva questions)
- Order matters: the means in steps 8 and 11 are computed after step 7 removed rows, so they differ from the means in the original describe().
- The assignment's step list and the hints list are numbered differently (the PDF has an extra "object summary" step). The code follows the main question list.
- Harvest dates run daily from 2023-01-01 to around 2031, one farm per day, so each pivot row is basically one farm. That is why the required line chart looks like a dense block. It is correct for the task, and the extra monthly chart shows the real pattern better.
- Pandas 3 note: use `df["col"] = df["col"].fillna(x)` rather than `inplace=True` on a column, which no longer works reliably.
