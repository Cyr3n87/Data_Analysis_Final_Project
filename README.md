# ISTAT traffic analysis

Data Analytics project.

## The goal

A company working in road traffic management and risk prevention wants an updated dashboard of the best Italian municipalities to invest in.

The questions we wanted to answer:

- which municipalities have the highest accident rates in the latest years;
- how many accidents, injured people and deaths each municipality has, compared with its population and area;
- which types of municipalities exist, and which kind of intervention suits each of them.

## Data sources

| Source | Content | How we got it |
|---|---|---|
| [ISTAT](https://esploradati.istat.it/SDMXWS/rest/data/41_983) (dataset 41_983) | Accidents, injured and deaths per municipality, 2001-2024 | ISTAT SDMX API, CSV format |
| [SITUAS](https://situas.istat.it/web/#/territorio/body?id=74&dateFrom=2023-12-31) (report 74) | Name, province, region, population and area of each municipality at 31/12/2023 | Web scraping of the JSON data loaded by the page |

Note: the ISTAT API blocks an IP address for 1-2 days if it receives too many requests. During the project this happened, so the repository also contains a saved copy of the ISTAT data in `data/raw`. The notebook downloads the data again when the API is available.

## What we did

1. **Set up the project**: folder structure, Git repository and GitHub.
2. **Downloaded the accident data** from the ISTAT API and checked the meaning of the columns (`DATA_TYPE`, `RESULT`, `OBS_VALUE`).
3. **Scraped population and area** from SITUAS.
4. **Cleaned and merged the data**: kept the municipality codes with their leading zeros, reshaped the ISTAT table (one row per municipality and year), and joined it with SITUAS. 7,900 of 7,901 municipalities matched: the missing one, Albaredo Arnaboldi, was abolished in November 2023.
5. **Checked the data quality**: in 2024 about 1,500 small municipalities are missing, but the national total (173,364 accidents) matches the official ISTAT figure, so they simply had zero accidents.
6. **Created the indicators** on the 2022-2023 average (most recent complete years, after Covid): accidents per 1,000 inhabitants, accidents per km² and deaths per 100 accidents. We kept the 2,180 municipalities with at least 10 accidents per year, because in very small towns one accident changes the rates too much.
7. **Exploratory data analysis**: national trend, distributions, differences between macro-areas and provincial capitals, correlations.
8. **Clustering** with K-means (4 clusters) on the three indicators, after log transform and standardisation.
9. **Hypothesis tests**: Mann-Whitney U to compare provincial capitals with the other municipalities.
10. **Regression** (OLS) to explain the accident rate with population, density, capital status and macro-area, and to find municipalities with many more accidents than expected.
11. **Power BI dashboard** with three pages: overview, where to invest, risk profiles and anomalies.
12. **Presentation** with the main results.

## Main results

- From 2001 to 2023 accidents fell by 37% and deaths by 57%, but since 2013 there has been no further improvement.
- The Centre and the North have more accidents per inhabitant; the South and the Islands have fewer accidents, but more serious ones.
- The clustering found 4 risk profiles: *dense urban areas*, *high accident rate*, *low frequency, high severity* and *low risk, low severity*.
- Provincial capitals have significantly more accidents per inhabitant than the other municipalities (p < 0.0001), but they are less serious.
- The regression explains about 29% of the differences. The municipalities with the most accidents compared with what the model expects are mostly seaside resorts (e.g. Forte dei Marmi, 6.5 times the expected rate) and logistics or shopping hubs (e.g. Bentivoglio, Assago).

## Recommendations

- **Big cities**: 30 km/h zones, safer crossings and junctions, cycle lanes.
- **Municipalities with serious accidents**, mostly in the South: speed checks, roundabouts, barriers and lighting on extra-urban roads.
- **Seaside resorts and commercial hubs**: seasonal controls and traffic plans for visitors.

## Limitations and next steps

- Only two years of data (2022-2023) are used for the indicators, so severity is unstable in medium-sized municipalities.
- Population counts only residents: tourists and commuters are not included.
- A forecast model could be added in the future.
- Adding data on tourist presences and road types would improve the model.

## Repository structure

```
├── data/
│   ├── raw/            original data (ISTAT, SITUAS)
│   └── processed/      cleaned and enriched data
├── notebooks/
│   ├── 01_incidenti_istat.ipynb       ISTAT download and cleaning
│   ├── 02_situas.ipynb                SITUAS scraping, merge and indicators
│   ├── 03_eda.ipynb                   exploratory data analysis
│   ├── 04_clustering.ipynb            K-means clustering
│   └── 05_statistical_analysis.ipynb  hypothesis tests and regression
├── reports/
│   ├── figures/        charts exported from the notebooks
│   ├── dashboard.pbix  Power BI dashboard
│   └── dashboard.pdf   dashboard export
├── requirements.txt
└── README.md
└── Road_Accidents_Analysis_italy.pptx
```

## How to run

1. Create a virtual environment and install the libraries: `pip install -r requirements.txt`
2. Run the notebooks in order, from `01` to `05`.
3. Open `reports/dashboard.pbix` with Power BI Desktop and click **Refresh** to load the updated data.

## Tools

Python (pandas, NumPy, requests, matplotlib, seaborn, scikit-learn, SciPy, statsmodels), Jupyter, Power BI, Git and GitHub.
