# Seattle vs. St. Louis Precipitation Analysis (2018–2022)

> A comparison of daily precipitation in Seattle, WA and St. Louis, MO over five years, using NOAA GHCN-Daily weather records. The analysis covers mean daily rainfall, how often it rains, and how both change by month.

---

## Project Overview

Seattle has a reputation as one of the rainiest cities in the U.S., while St. Louis has a humid continental climate with hot, stormy summers. This project tests that reputation with data. It asks:

1. Which city gets more precipitation on an average day?
2. Which city has precipitation on a larger share of days?
3. How do the two cities differ month by month, and are those differences statistically significant?

Daily precipitation records from 2018 to 2022 for one weather station in each city are cleaned, merged into a single tidy data set, gap-filled, visualized, and compared with hypothesis tests.

**Key findings** (from `code/Seattle_Weather_Template.ipynb`):

- St. Louis has a slightly higher mean daily precipitation (about 0.130 in/day vs. 0.113 in/day for Seattle) and a more extreme maximum (8.64 in vs. 2.60 in in a single day).
- Seattle has precipitation on a larger share of days in most months. The difference is statistically significant in every month except May, July, and August.
- Seattle is wetter in the winter (January, November, and December mean daily precipitation are significantly higher). St. Louis is wetter in March, May, July, and August.

---

## Repository Contents

```
├── code/
│   ├── Seattle_Weather_Template.ipynb      # Data analysis notebook
│   └── clean_seattle_stlouis_weather.csv   # Cleaned, merged data set (output of the notebook)
├── data/
│   ├── seattle_rain.csv                    # Raw NOAA daily data, Seattle
│   └── stl_rain.csv                        # Raw NOAA daily data, St. Louis area stations
├── reports/                                # Communication document
├── requirements.txt                        # Python dependencies
├── LICENSE                                 # MIT License
└── README.md
```

| File | Description |
|---|---|
| [`code/Seattle_Weather_Template.ipynb`](code/Seattle_Weather_Template.ipynb) | Jupyter notebook with the full analysis: loading, cleaning, imputation, visualization, and statistical tests |
| [`data/seattle_rain.csv`](data/seattle_rain.csv) | Raw Seattle data |
| [`data/stl_rain.csv`](data/stl_rain.csv) | Raw St. Louis data |
| [`code/clean_seattle_stlouis_weather.csv`](code/clean_seattle_stlouis_weather.csv) | Tidy data set written by the notebook |

---

## Data

- **Source:** NOAA National Centers for Environmental Information (NCEI), Climate Data Online, GHCN-Daily dataset: <https://www.ncei.noaa.gov/cdo-web/search?datasetid=GHCND>
- **Period analyzed:** January 2, 2018 – December 31, 2022
- **Seattle:** one station, `US1WAKG0225` (SEATTLE 2.1 ESE, WA US), 1,658 daily records.
- **St. Louis:** the raw file has 54,574 records from 44 stations in the St. Louis area, covering 2017–2022. The analysis uses one station, `USW00013994` (ST LOUIS LAMBERT INTERNATIONAL AIRPORT, MO US), so that each city is represented by a single station.
- **Variable of interest:** `PRCP`, daily precipitation in inches. The raw files also contain `STATION`, `NAME`, `DATE`, `DAPR`, `MDPR`, `SNOW`, and `SNWD`. The Seattle file additionally has `WESD` and `WESF`.
- **Missing data:** Some records have no `PRCP` value, and some dates are absent from the files. After merging on date, 190 Seattle days and 1 St. Louis day had no precipitation value.
- **Data license:** NOAA NCEI data is publicly available. See NOAA's [data access policy](https://www.ncei.noaa.gov/) for terms of use.

---

## Data Processing and Analysis

All steps are in [`code/Seattle_Weather_Template.ipynb`](code/Seattle_Weather_Template.ipynb).

### 1. Load and explore
- Loaded both CSV files with pandas and compared columns, shapes, data types, number of stations, and null counts with `head()`, `info()`, `shape`, and `unique()`.
- Found that the St. Louis file contains 44 stations and dates from 2017, while the Seattle file has one station and starts in 2018.
- Converted `DATE` from text to `datetime` (Seattle used `M/D/YY`, St. Louis used `YYYY-MM-DD`).
- Plotted raw daily precipitation for each city to check that the data could answer the question.

### 2. Select relevant subsets
- Kept only the St. Louis Lambert International Airport station.
- Limited the St. Louis data to 2018 and later so both cities cover the same period.

### 3. Merge and tidy
- Outer-joined the two data sets on `DATE`, keeping only `DATE` and `PRCP`, so that every date in either file is kept.
- Renamed the precipitation columns to `SEAT` and `STL` and reshaped to a tidy (long) format with `pd.melt`, giving the columns `DATE`, `CITY`, and `PRCP`.

### 4. Impute missing values
- Counted missing precipitation values overall and for each city.
- Added a `day_of_year` column (1–366).
- Computed the mean precipitation for each day of the year, averaged across the five years, and replaced each missing value with the mean for its day.
- Confirmed no missing values remained and exported the result to `clean_seattle_stlouis_weather.csv` (saved in the `code/` folder).

### 5. Explore and visualize
- Line plots of daily precipitation for both cities.
- Summary statistics (`describe`) and mean daily precipitation by city.
- Added `month` and `any_precipitation` (precipitation > 0) columns.
- Box plots and bar plots of precipitation by month, and bar plots of the proportion of days with precipitation, overall and by month.

### 6. Statistical tests
- **Mean precipitation by month:** Welch's two-sample t-test (`scipy.stats.ttest_ind`, `equal_var=False`) for each month, Seattle vs. St. Louis, at a 0.05 significance level.
- **Proportion of days with precipitation by month:** two-sided two-proportion z-test (`statsmodels.stats.proportion.proportions_ztest`) for each month, at a 0.05 significance level.
- Months with a significant difference are marked with `*` on the plots.

---

## Results

| Question | Seattle | St. Louis |
|---|---|---|
| Mean daily precipitation | 0.113 in | 0.130 in |
| Median daily precipitation | 0.01 in | 0.00 in |
| Maximum single-day precipitation | 2.60 in | 8.64 in |

| Test | Months with a significant difference (p < 0.05) | Direction |
|---|---|---|
| Mean precipitation (t-test) | Jan, Mar, May, Jul, Aug, Nov, Dec | Seattle higher in Jan, Nov, Dec. St. Louis higher in Mar, May, Jul, Aug |
| Proportion of days with precipitation (z-test) | Jan, Feb, Mar, Apr, Jun, Sep, Oct, Nov, Dec | Seattle higher in every significant month |

In short, St. Louis gets heavier rain on the days it rains, concentrated in spring and summer storms. Seattle gets lighter but more frequent precipitation, concentrated in the cool months.

### Limitations
- Each city is represented by a single station, which may not represent the whole metro area.
- About 10% of Seattle's days (190 of 1,826) were missing and filled with the day-of-year average, which smooths out real day-to-day variability.
- The t-tests assume roughly normal data, but daily precipitation is heavily right-skewed with many zero days. The z-tests treat days as independent, although consecutive rainy days are correlated. Results should be read as indicative.



---

## Requirements

- Python 3.9+
- [Jupyter Notebook](https://jupyter.org/) or JupyterLab
- [pandas](https://pandas.pydata.org/)
- [NumPy](https://numpy.org/)
- [Matplotlib](https://matplotlib.org/)
- [seaborn](https://seaborn.pydata.org/)
- [SciPy](https://scipy.org/) (t-tests)
- [statsmodels](https://www.statsmodels.org/) (two-proportion z-tests)

Install and run:

```bash
pip install -r requirements.txt
jupyter notebook code/Seattle_Weather_Template.ipynb
```

---

## Author

- **Anush Kallugadde** – [@anush901](https://github.com/anush901)

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

## Acknowledgements

- **Data provider:** National Oceanic and Atmospheric Administration (NOAA), National Centers for Environmental Information.
- **Tools:** Python, pandas, NumPy, Matplotlib, seaborn, SciPy, statsmodels, Jupyter.
