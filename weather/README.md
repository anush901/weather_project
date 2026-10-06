# Seattle vs. Miami Precipitation Analysis (2018–2022)

> A comparison of daily precipitation in Seattle, WA and Miami Beach, FL over five years, using NOAA GHCN-Daily weather records. The analysis covers mean daily rainfall, how often it rains, and how both change by month.

---

## Project Overview

Seattle (Pacific Northwest, warm-summer Mediterranean climate) and Miami (tropical monsoon climate) are often assumed to be opposites: one gray and drizzly, the other hot and stormy. This project tests that with data. It asks:

1. Which city gets more precipitation on an average day?
2. Which city has rain on a larger share of days?
3. How do the two cities differ month by month, and are those differences statistically significant?

Daily precipitation records from 2018-01-01 to 2022-12-31 for one weather station in each city are cleaned, merged into a single tidy data set, gap-filled, visualized, and compared with hypothesis tests.

**Key findings** (from `code/weather_project.ipynb`):

- Miami has the higher mean daily precipitation (about 0.154 in/day vs. 0.114 in/day for Seattle), and a much more extreme maximum (11.0 in vs. 2.6 in in a single day).
- Seattle has rain on a larger share of days in most months (January–May and October–December). Miami has the larger share in July and August. June and September show no significant difference.
- Seattle is wetter in winter (December and January mean daily precipitation are significantly higher). Miami is wetter from May through September.

---

## Repository Contents

```
├── code/
│   ├── weather_project.ipynb             # Data analysis notebook
│   └── clean_seattle_miami_weather.csv   # Cleaned, merged data set (output of the notebook)
├── data/
│   ├── seattle_rain.csv                  # Raw NOAA daily data, Seattle
│   └── miami_rain.csv                    # Raw NOAA daily data, Miami Beach
├── reports/                              # Communication document
├── requirements.txt                      # Python dependencies
├── LICENSE                               # MIT License
└── README.md
```

| File | Description |
|---|---|
| [`code/weather_project.ipynb`](code/weather_project.ipynb) | Jupyter notebook with the full analysis: loading, cleaning, imputation, visualization, and statistical tests |
| [`data/seattle_rain.csv`](data/seattle_rain.csv) | Raw Seattle data |
| [`data/miami_rain.csv`](data/miami_rain.csv) | Raw Miami data |
| [`code/clean_seattle_miami_weather.csv`](code/clean_seattle_miami_weather.csv) | Tidy data set written by the notebook |

---

## Data

- **Source:** NOAA National Centers for Environmental Information (NCEI), Climate Data Online, GHCN-Daily dataset: <https://www.ncei.noaa.gov/cdo-web/search?datasetid=GHCND>
- **Period:** January 1, 2018 – December 31, 2022
- **Stations:**
  - Seattle: `US1WAKG0225` (SEATTLE 2.1 ESE, WA US), 1,658 daily records
  - Miami: `USW00092811` (MIAMI BEACH, FL US), 1,598 daily records
- **Variable of interest:** `PRCP`, daily precipitation in inches. The raw files also contain `STATION`, `NAME`, `DATE`, `DAPR`, and `MDPR`. The Seattle file additionally has `SNOW`, `SNWD`, `WESD`, and `WESF`.
- **Missing data:** Neither raw file covers all 1,826 days in the period, and some records have no `PRCP` value. After merging on date, 175 Seattle and 215 Miami days had no precipitation value.
- **Data license:** NOAA NCEI data is publicly available. See NOAA's [data access policy](https://www.ncei.noaa.gov/) for terms of use.

---

## Data Processing and Analysis

All steps are in [`code/weather_project.ipynb`](code/weather_project.ipynb).

### 1. Load and explore
- Loaded both CSV files with pandas and compared columns, shapes, data types, number of stations, and null counts with `head()`, `info()`, `shape`, and `nunique()`.
- Converted `DATE` from text to `datetime` (Seattle used `M/D/YY`, Miami used `YYYY-MM-DD`) and confirmed both cover 2018-01-01 to 2022-12-31.
- Plotted raw daily precipitation for each city to check that the data could answer the question.

### 2. Merge and tidy
- Outer-joined the two data sets on `DATE`, keeping only `DATE` and `PRCP`, so that every date in either file is kept.
- Reshaped to a tidy (long) format with `pd.melt` and renamed values and columns to `date`, `city` (`SEA` / `MIA`), and `precipitation`.

### 3. Impute missing values
- Added a `day_of_year` column (1–366).
- Computed the mean precipitation for each day of the year, averaged across the five years, and replaced each missing value with the mean for its day.
- Confirmed no missing values remained and exported the result to `clean_seattle_miami_weather.csv` (saved in the `code/` folder).

### 4. Explore and visualize
- Line plots of daily precipitation for both cities.
- Summary statistics (`describe`) and mean daily precipitation by city.
- Added `month` and `any_precipitation` (precipitation > 0) columns.
- Box plots and bar plots of precipitation by month, and bar plots of the proportion of days with precipitation, overall and by month.

### 5. Statistical tests
- **Mean precipitation by month:** Welch's two-sample t-test (`scipy.stats.ttest_ind`, `equal_var=False`) for each month, Seattle vs. Miami, at a 0.05 significance level.
- **Proportion of rainy days by month:** two-sided two-proportion z-test (`statsmodels.stats.proportion.proportions_ztest`) for each month, at a 0.05 significance level.
- Months with a significant difference are marked with `*` on the plots.

---

## Results

| Question | Result |
|---|---|
| Mean daily precipitation | Miami 0.154 in, Seattle 0.114 in |
| Maximum single-day precipitation | Miami 11.0 in, Seattle 2.6 in |
| Months with significantly different mean precipitation | Jan, May, Jun, Jul, Aug, Sep, Dec |
| Months with significantly different proportion of rainy days | All except Jun and Sep |
| Seattle is wetter | Winter months (Dec–Jan for mean precipitation; Jan–May and Oct–Dec for share of rainy days) |
| Miami is wetter | Late spring and summer (May–Sep for mean precipitation; Jul–Aug for share of rainy days) |

In short, Miami gets heavier rain, concentrated in the warm months. Seattle gets lighter but more frequent rain, concentrated in the cool months.

### Limitations
- Each city is represented by a single station, which may not represent the whole metro area.
- The t-tests assume roughly normal data, but daily precipitation is heavily right-skewed with many zero days. The z-tests treat days as independent, although consecutive rainy days are correlated. Results should be read as indicative.
- Filling gaps with the day-of-year average smooths out real day-to-day variability.


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
jupyter notebook code/weather_project.ipynb
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
