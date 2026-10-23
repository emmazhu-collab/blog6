# Blog 6: Does One Year of Income Tell the Whole Story?

This project asks whether one earnings year gives the same picture of the link between fathers' and children's earnings as an average of three years. The main analysis compares both generations near age 40 with SQL in Python.

## Run the main analysis

Use Python 3 and install the packages listed in `requirements.txt` if needed:

```sh
python3 -m pip install -r requirements.txt
python3 code/same_age_analysis.py
```

Run the command from this project folder. The script reads the local PSID files described in [`data/raw/README.md`](data/raw/README.md), uses the public monthly CPI file in `data/source/cpi_same_age.csv`, and writes a new timestamped folder under `results_same_age/`. These run folders are ignored by Git. The script writes aggregate summaries and figures, not person-level output files. It does not render the website.

## Data and matching

The analysis uses the 2023 PSID Parent Identification File to link children to birth fathers, the Cross-Year Individual File to follow people and find their age, role, and family ID in each interview, and the two family earnings extracts `J366581.xlsx` and `J366467.xlsx`. PSID earnings refer to the prior calendar year, so 1993 earnings come from the 1994 interview. The supplied codebooks define the comparable earnings measures used here; extra retrospective measures with different definitions are left out.

Permanent person IDs connect a child and father across years. For each interview, the script joins a person's record to the family earnings record using both interview year and family ID. It assigns Head/Reference Person or spouse earnings only to people whose recorded household role matches. Earnings are adjusted to 2022 dollars with annual-average CPI.

For each person, the analysis keeps positive earnings observations measured at interview ages 35–45. It selects the observation closest to age 40 for the single-year measure and the three closest observations from distinct earnings years for the average. Both father-child age gaps must be at most two years for both measures. The estimates use the same pairs. The SQL query that performs the aggregation and matching is `sql/same_age_sample.sql`.

## Verified main results

The checked-in aggregate snapshot in `results/` records 321 father-child pairs and 249 unique fathers. The single-year IGE is 0.170 (95% father-cluster bootstrap interval: 0.030–0.354). The three-year-average IGE is 0.291 (0.151–0.444). The paired difference is 0.121 (-0.037–0.268). The interval for the difference includes zero.

`results/figures/same-age-ige.png` is the main article figure. The CSV and JSON files in `results/` document the estimates, sample flow, selected ages, and aggregate analysis settings. These files are a snapshot of the verified run; a new local run writes to `results_same_age/` instead of replacing them.

## Published life-cycle comparison

The article also shows a separate exploratory comparison of children's 2014 and 2022 earnings for 140 father-child pairs, holding the father's average fixed. It reports IGEs of 0.186 and 0.244, mean child interview ages of 27.5 and 35.5, and a difference interval of -0.111 to 0.232. Age and calendar year change together, so this comparison cannot isolate an age effect.

This published comparison is not reproduced by the current same-age script. Static inspection was not enough to safely merge its older sample construction and bootstrap sequence while guaranteeing the displayed estimates. The minimum reproducible source is therefore retained: `code/analysis.py` and `sql/build_sample.sql`. Run it locally with the same PSID inputs and the CPI file at `data/source/cpi_monthly.csv`:

```sh
python3 code/analysis.py
```

Its aggregate outputs go to `results/life_cycle_2014_2022/`, separate from the main results. The saved `life_cycle.csv`, `summary.json`, and `figures/life-cycle.png` in that folder are the outputs used for the article. The script also writes aggregate diagnostics from its original workflow to that same folder. This separate source is kept because it is still needed to reproduce the published figure; it is not the main analysis.

## Project files

```text
README.md
requirements.txt
blog6.Rproj
.gitignore
code/
  same_age_analysis.py       # main 1993–2022 same-age analysis
  analysis.py                # source for the published 2014/2022 comparison
sql/
  same_age_sample.sql        # main SQL aggregation and matching
  build_sample.sql           # SQL used by the separate 2014/2022 comparison
data/
  raw/README.md              # PSID download and local setup instructions
  source/cpi_same_age.csv    # public CPI for the main analysis
  source/cpi_monthly.csv     # CPI input for the 2014/2022 reproduction
results/                     # verified aggregate snapshots and article figures
results_same_age/            # outputs from new local main-analysis runs; Git-ignored
```

## Limits and privacy

This is an unweighted analysis of people with supported family roles and positive earnings records. Requiring repeated positive earnings selects the sample, so the results do not represent all PSID families or the whole U.S. population. Three years do not measure lifetime earnings. Interview age is an approximation of the age when the earnings were received, and matching ages does not remove temporary shocks, cohort differences, or other life-cycle bias. IGE is an association, not a causal effect.

PSID files require a registered account and are subject to PSID's Conditions of Use. Keep original files in `data/raw/`; do not put them or person-level extracts in GitHub or send them to an AI/LLM service. `.gitignore` excludes the raw folder except for its acquisition README. The public CPI files and aggregate outputs can be shared.
