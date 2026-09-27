# Seattle vs. Miami Precipitation Analysis (2018–2022)

> A comparative data analysis evaluating daily precipitation patterns, volume differences, and seasonal rainfall distributions between Seattle, WA, and Miami, FL, using NOAA historical weather data.


---

## Project Overview
This project analyzes and compares historical daily precipitation records between two distinct climate regimes in the United States: Seattle, Washington (Csb - Warm-summer Mediterranean / Pacific Northwest) and Miami, Florida (Am - Tropical Monsoon). 

Using NOAA Global Historical Climatology Network (GHCN) daily summaries from 2018 through 2022, this study handles real-world weather data challenges (such as handling missing observations via day-of-year mean imputation) to uncover structural differences in annual rainfall volume, wet day frequency, and seasonal variability.
- **Objective:** Compare 5-year daily precipitation trends between Seattle and Miami
- **Domain:** Climate
- **Key Techniques:** Data cleaning and analysis

---

## Project Structure

```
├── data/                 # Raw and processed data
├── code/                 # Jupyter notebooks and Python scripts
├── reports/              # Generated reports and visualizations
├── requirements.txt      # Dependencies
└── README.md             # Project documentation
```

---

## Data

- **Source:** https://www.ncei.noaa.gov/cdo-web/search?datasetid=GHCND
- **Description:** Daily weather records containing daily precipitation depth (`PRCP`)
- **License:** (if applicable)

---

## Analysis

The analysis pipeline follows these core steps:
1. **Data Preprocessing & Data Quality Check:**
   - Evaluated missing precipitation values across cities (e.g., identifying missing entries in Seattle and Miami data).
2. **Missing Value Imputation:**
   - Created a `day_of_year` feature ($1 \le \text{day} \le 365/366$) to account for seasonal variation.
   - Computed the multi-year mean precipitation for each day of the year (`.groupby('day_of_year').mean()`).
   - Imputed missing values using the corresponding day-of-year historical mean.

---

## Results

**Imputation Impact:** Grouping by `day_of_year` successfully preserved seasonal weather signatures during missing value imputation without introducing artificial bias into multi-year trends.



---

## Authors

- Your Name - [@anush901](https://github.com/anush901)

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## Acknowledgements

- **Data Provider:** National Oceanic and Atmospheric Administration (NOAA) NCEI.
- **Tools & Libraries:** Python, Pandas, NumPy, Matplotlib, Seaborn, Jupyter Notebooks.