# Fatigue in the Cockpit, the Tower and the Hangar
### Investigating fatigue-related aviation safety reports with statistics and natural language processing

**Springboard Data Science Career Track · Capstone Three · Brittany**

Fatigue-related reports to NASA's Aviation Safety Reporting System (ASRS) fall into **eight recurring themes**. Which theme a report tells depends most on **who wrote it** and **what phase of flight** it happened in. This project analyzes **2,191 fatigue-related reports (2009–2025)** by combining the coded report fields with topic modeling of the reporters' own narratives.

📄 **[Final report (PDF)](Capstone_Final_Report.pdf)** · 📊 **[Model metrics](Capstone_Model_Metrics.txt)**

---

## The question
> What statistical patterns and recurring narrative themes characterize fatigue-related aviation safety reports submitted to ASRS between 2009 and 2025?

ASRS's coded fields say *that* fatigue was present. The free-text narratives say *how* it showed up and *why*. This project reads those narratives at scale.

**Intended users:** airline safety teams, aviation safety researchers, and maintenance and ATC safety managers.

## Data
- **Source:** [NASA ASRS Database Online](https://asrs.arc.nasa.gov/search/database.html), exported with fatigue-related search criteria (2,246 reports, 126 columns)
- **Final sample:** 2,191 reports after a 2009–2025 date filter and a documented fatigue-inclusion check
- ASRS reports are voluntary and de-identified. Findings describe patterns **within reports**, not rates across aviation.

## Approach
| Step | Notebook | Highlights |
|---|---|---|
| 1. Data wrangling | [`01_Data_Wrangling.ipynb`](notebooks/01_Data_Wrangling.ipynb) | Two-row header fix, fatigue validation, multi-value fields → indicators, "Unknown" for unreported fields, text cleaning and lemmatization |
| 2. EDA | [`02_EDA.ipynb`](notebooks/02_EDA.ipynb) | Trends, chi-square and Cramér's V, Pearson heat map, n-grams, fatigue vocabulary, feature selection |
| 3. Pre-processing | [`03_Preprocessing.ipynb`](notebooks/03_Preprocessing.ipynb) | Dummy features, standardization, 80/20 stratified split, TF-IDF (unigrams + bigrams) |
| 4. Modeling | [`04_Modeling.ipynb`](notebooks/04_Modeling.ipynb) | NMF vs. LDA vs. K-Means, tuned over 4–14 topics; final model; themes vs. report characteristics |

## Model results
| Model | Best test coherence (NPMI) | Diversity | Stability |
|---|---|---|---|
| **NMF (final, k = 8)** | **0.221** | **0.875** | **1.00** |
| LDA (k = 8) | 0.165 | 0.685 | 0.45 |
| K-Means (k = 10) | 0.211 | 0.684 | 0.67 |

## Key findings
1. **Eight themes:** rest & crew scheduling · gate & cabin operations · altitude & clearance deviations · visual approach · approach configuration & go-arounds · taxi & runway · maintenance tasks · ATC workload & staffing.
2. **Role matters most** (Cramér's V = 0.69): controllers write about workload and staffing, technicians about overnight task errors, and flight attendants about schedules and rest.

   ![Themes by reporter role](figures/model_06_themes_by_role.png)

3. **The rest & scheduling theme fell** from 20–32% of report content (2009–2012) to 6–7% (2023–2025), while approach, landing and taxi themes grew. The timing matches the FAA's 2014 pilot rest rules, but that link needs testing.

   ![Themes by year](figures/model_08_themes_by_year.png)

4. **Narratives add information the codes miss:** coded fields predict a report's theme with macro F1 0.58 (vs. 0.13 by chance). They're strong for maintenance and ATC themes, but weak for pilot flight-deck events.

## Recommendations
1. **Tag and route** incoming fatigue reports with the saved topic model.
2. **Target fatigue-risk efforts by occupation and time of day:** flight attendant scheduling, overnight maintenance shift turnovers, ATC staffing, and fatigue scenarios in approach and landing training.
3. **Test the Part 117 question** with exposure data, comparing pilots with groups the rule doesn't cover.

## Repository structure
```
├── Capstone_Final_Report.pdf
├── Capstone_Model_Metrics.txt
├── README.md
├── notebooks/            01–04 Jupyter notebooks (run in order)
├── data/
│   ├── raw/              ASRS_DBOnline.csv
│   ├── interim/          data dictionaries, exclusion and selection logs
│   └── processed/        cleaned data, train/test matrices, report_topics.csv
├── models/               final_nmf_model.joblib, integration_logreg.joblib, metrics CSVs
└── figures/              all saved charts
```

## How to run
Run the notebooks in order in Google Colab or Jupyter. Notebook 01 asks for `ASRS_DBOnline.csv`. Each later notebook reads the previous notebook's output.
Requirements: Python 3, pandas, numpy, matplotlib, seaborn, scipy, scikit-learn **1.6.1** (to reload the saved models), nltk, joblib.

## Limitations
Voluntary reporting (selection bias) · no flight-hour denominator · possible coding changes over time · topic labels require human judgment · small groups (dispatchers, drones, military) not analyzed separately.
