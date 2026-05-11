# NYC Residential Property Appreciation Model

## Team
Shreya Karki · Arati Kalor · Ayushman Shrestha · Chenbin Wu · Ali Adnan

---

## Business Question
Can we predict whether a residential property will appreciate above a 
meaningful threshold, based on neighborhood factors such as crime, 
median income, and subway accessibility?

---

## Data Sources

| Dataset | Source | Size |
|---|---|---|
| NYC Citywide Annualized Calendar Sales | NYC Open Data | 760,914 rows |
| NYPD Complaint Data | NYC Open Data | 579,561 rows |
| MTA Subway Stations | MTA Open Data | 496 stations |
| Median Income by Neighborhood | US Census / ACS | 4,685 rows |

> Raw data files are not included in this repo due to size.  
> All datasets are publicly available and free to download from the links above.

---

## Methodology

### Step 1 — Build Repeat-Sale Dataset
- Identified properties sold 2+ times
- Computed annualized return: `(Last Price / First Price)^(1 / Years) - 1`
- Filtered out: prices below $100K, holding periods under 1 year, extreme outliers

### Step 2 — Define the Target Variable
- Labeled each property as **High Return (1)** or **Low Return (0)**
- Threshold = 75th percentile of annualized return **within each building segment**
- This ensures fair comparison — co-ops and one-family homes are not compared against each other

### Step 3 — Attach Neighborhood Features
| Feature | Description |
|---|---|
| Crime count | Total NYPD complaints per borough |
| Subway distance | Haversine distance to nearest MTA station (km) |
| Near subway flag | 1 if within 0.5 km of a station |
| Borough | Encoded as dummy variables |

> Income was evaluated but excluded — ~70% of values were missing after merging with property data.

### Step 4 — Build Separate Models Per Segment
For each building segment, trained and compared 4 models:
- Logistic Regression
- Decision Tree
- Random Forest
- Gradient Boosting

Evaluated using: AUC, Precision, Recall, F1-Score, Lift Analysis

---

## Key Results

### One-Family Dwellings (8,744 properties)
| Model | AUC |
|---|---|
| **Decision Tree** | **0.618** ✅ Best |
| Gradient Boosting | 0.614 |
| Logistic Regression | 0.608 |
| Random Forest | 0.554 |

- High-return threshold: **23% annualized return**
- Top decile lift: **1.14×**

### Two-Family Dwellings (5,675 properties)
- Best model: **Logistic Regression**
- Top feature: **Borough** (Manhattan and Staten Island dominate)
- Surprise: Bronx & Brooklyn averaged 20% returns; Manhattan averaged just 6%

### Co-op Dwellings (4,044 properties)
- Best AUC: **0.69** — strongest of any segment
- Top 10 predicted neighborhoods all in Queens, Staten Island, or the Bronx
- Crime + borough were the strongest predictors

### Condo Dwellings (2,899 properties)
- Weakest segment — best AUC only **0.567**
- Reason: Premium neighborhoods are already priced in — little room for outperformance
- Logistic Regression scored 0.498 — literally worse than random

---

## Cross-Segment Findings

Three patterns held across all building types:

1. **Outer boroughs win** — Bronx (18.7%), Queens (16.1%), Brooklyn (12.9%) all beat Manhattan (1.2%)
2. **Crime matters more than transit** — crime ranked top-3 in every model; subway distance barely registered
3. **Segments are fundamentally different** — a 6% return is elite for a co-op, disappointing for a two-family home

---

## Limitations
- Crime data is borough-level only — all of Brooklyn shares one score regardless of neighborhood
- Income had to be excluded due to missing data coverage
- No property-level features available: building age, square footage, school quality, renovation history
- Results are specific to NYC and the time period in the dataset
- AUC scores of 0.55–0.69 show the model is helpful but not highly predictive on its own

---

## Tools & Libraries

- Python 3
- pandas · numpy · scikit-learn
- matplotlib · seaborn
- Google Colab

---

## Files in This Repo
- NYC-Property-Appreciation-Model.ipynb     # Full analysis notebook
- NYC-Property-Appreciation.pptx  # Final presentation slides
- README.md                       # This file
