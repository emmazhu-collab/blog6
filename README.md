# Blog 6: Does One Year of Income Tell the Whole Story?

This project compares the earnings link between birth fathers and adult children using one income year and three-year averages. It also checks different income-year choices and the same children at two career stages. Python prepares the files, SQLite matches people and averages earnings, and Python estimates the regressions and saves the results.

This repository contains the analysis process. The article stays in the separate `website-recovered` project. No article or Render step is needed to reproduce these results.

The article is a six-step tutorial: obtain the inputs, prepare the data with Python, match IDs with SQL, calculate average income, compare IGE estimates, and check life-cycle bias. Detailed setup and reproduction instructions stay in this README. The short article examples assume the preparation steps have run; use the complete script for reproduction.

## Folder structure

```text
blog6.Rproj
README.md
requirements.txt
code/analysis.py
sql/build_sample.sql
data/
  raw/README.md             # PSID acquisition instructions
  raw/pid23/               # original PSID files, local only
  raw/ind2023er/            # original PSID files, local only
  raw/J366467.xlsx          # original income extract, local only
  raw/J366467_codebook.pdf  # original extract documentation
  raw/J366467_labels.txt
  source/cpi_monthly.csv    # public inflation input
results/
  elasticities.csv
  year_sensitivity.csv
  life_cycle.csv
  sample_counts.csv
  matching_checks.json
  summary.json
  figures/
    income-measures.png
    year-sensitivity.png
    life-cycle.png
```

`data/raw` is visible on your computer. Its PSID files are excluded from Git; only the acquisition README is eligible for Git. A reader who downloads this repository must obtain their own PSID files. Do not force-add the raw files or extracts.

## Data sources

The inputs are the PSID 2023 Parent Identification File, 1968–2023 Cross-Year Individual File, and a custom family earnings extract for the 2011, 2013, 2015, 2017, 2019, 2021, and 2023 survey waves. These interviews report earnings from 2010, 2012, 2014, 2016, 2018, 2020, and 2022. The downloaded 2017 wave (2016 earnings) is not used in this analysis. See [data/raw/README.md](data/raw/README.md) for the official sources, required variables, and filenames. The original files are unchanged.

CPI is the BLS Consumer Price Index for All Urban Consumers, all items, not seasonally adjusted, from [FRED](https://fred.stlouisfed.org/series/CPIAUCNS). The saved monthly file covers January 2010–December 2022. The script checks for 12 months per year, calculates annual means, and expresses earnings in 2022 dollars. It needs no internet connection once the local inputs are present.

PSID's current [Conditions of Use](https://simba.isr.umich.edu/U/CondUse.aspx) prohibit third-party transfers of data and extracts and prohibit AI/LLM use with PSID data. Users must register themselves and follow those conditions. Do not upload PSID records to an AI service. This organization pass copied previously generated aggregate results; it did not run the microdata analysis again. Check permitted use with PSID before further AI-assisted data work.

## Replication instructions

### 1. Prepare Python and the inputs

Use Python 3 with `pandas`, `numpy`, and `matplotlib`. SQLite and the other reading tools are part of Python. The package versions in `requirements.txt` match the author’s successful Anaconda environment (Python 3.13.9): pandas 2.3.3, NumPy 2.1.3, and Matplotlib 3.10.0. Use the same Python environment for installing packages and running the script. RStudio’s Python Console, its Terminal, and a Jupyter kernel can use different environments; installing a package in one does not make it available in the others. In RStudio, open `blog6.Rproj`, then select **Terminal**. Check that the working directory is the main `blog6` folder.

If the packages are missing in your chosen Python environment, run:

```sh
python3 -m pip install -r requirements.txt
```

Place the original files in the layout shown in `data/raw/README.md`. Existing local files have already been organized there. You do not need to download them again.

### 2. Run the complete script

In **Terminal**, copy this command without the surrounding backticks:

```sh
python3 code/analysis.py
```

On the author's Mac, the following command successfully reproduced the reported results:

```sh
/opt/anaconda3/bin/python code/analysis.py
```

This is a local example, not a required path for other computers. If using that interpreter, install any missing packages with `/opt/anaconda3/bin/python -m pip install -r requirements.txt`. The general command above works when `python3` points to your prepared environment.

This reads the local originals and rebuilds the tables, checks, and three PNG figures. It prints the estimates and sample sizes. It does not modify the original data, render an article, commit, or publish anything.

For a different raw-data location, use:

```sh
PSID_DATA_DIR="/full/path/to/PSID/files" python3 code/analysis.py
```

That folder must contain `pid23`, `ind2023er`, and `J366467.xlsx` directly. The input and output paths are based on the script location, so the script can also be called by its full path.

**RStudio Python Console alternative:** the prompt must be `>>>`, not the R prompt `>`, and the Python environment must contain the packages above. With the working directory at `blog6`, run both lines below. This executes the full file with its correct filename; do not select only the plotting section.

```python
import runpy
runpy.run_path("code/analysis.py", run_name="__main__")
```

Do not paste `python3 code/analysis.py` into either the R or Python Console; that is a Terminal command.

**Jupyter alternative:** open a notebook in the `blog6` folder and select a Python kernel with the required packages. Run this in one cell with Shift+Enter:

```python
%run code/analysis.py
```

For a different input location, set `os.environ["PSID_DATA_DIR"]` before `%run`. To view a saved figure in a new cell:

```python
from IPython.display import Image, display
display(Image(filename="results/figures/income-measures.png"))
```

### 3. Check the saved outputs

The saved reference results are:

| Comparison | IGE | Pairs |
| --- | ---: | ---: |
| Father 2012 / child 2020 | 0.233908 | 336 |
| Three-year averages | 0.226085 | 336 |
| Younger group, child 2014 | 0.185663 | 140 |
| Same younger group, child 2022 | 0.244324 | 140 |

The main sample contains 238 unique fathers. `elasticities.csv` also reports intercepts and bootstrap intervals. The difference intervals in `summary.json` include zero for both main and career-stage comparisons. `year_sensitivity.csv` contains all nine single-year combinations. The JSON and sample-count files record matching checks. `Individuals` counts distinct people in the Individual File; `Birth-father links` counts identified child–father links before requiring both income summaries. The father and child three-year counts are separate eligible groups, not nested stages of a single selection funnel.

The website analysis ran successfully before this repository was organized. During organization, all 9 saved result files were compared byte-for-byte with the website copy. The author subsequently reported a successful local run with `/opt/anaconda3/bin/python code/analysis.py`, reproducing 336 pairs, 238 fathers, and the reported main and life-cycle estimates. The final review checks saved results, paths, and Python syntax without rerunning PSID microdata.

## How the analysis works

The fixed-width reader uses column positions in the supplied `.do` files and retains only needed columns, rather than loading all 2,771 Individual File variables. The Excel reader reads the original cells directly because this PSID workbook contains a nonstandard worksheet attribute rejected by some Excel libraries.

The permanent person key combines the 1968 family ID and person number. Biological father links come from the Parent Identification File. SQL joins each person's interview year and family interview ID to the income extract. Both fields are needed because a family number may appear in more than one wave. This family interview ID is different from the permanent person key. The short individual-to-family JOIN in the article demonstrates these matching keys; assigning earnings also requires the household role checks below. Only the current Reference Person (role 10, sequence 1) receives Reference Person income. Current members with role 20 or 22 receive Spouse/Partner income when available. Other family members never receive these earnings. The code checks duplicate keys and records join counts, missing income, and zero income. Unknown father identifiers and invalid income codes are excluded.

`SELECT`, `JOIN`, and `WHERE` construct individual earnings records. `GROUP BY`, `AVG`, and `COUNT(DISTINCT year)` build complete three-year summaries. SQL then joins both summaries to each biological link. SQLite runs in memory and is rebuilt each time; no database of individual records is saved or shared.

Fathers must have positive income in 2010, 2012, and 2014 and interview age 35–60 in all corresponding waves. Children must have positive income in 2018, 2020, and 2022 and interview age 30–50 in all corresponding waves. Interviews report the previous year's income. The career-stage check selects 140 of these children with positive 2014 income and age 22–29 at the 2015 interview. They still meet the later main-sample restrictions.

The regression is log child income on log father income with an intercept. Multi-year income is the arithmetic mean of real earnings, followed by its logarithm. Both main estimates use the same pairs. Confidence intervals use 1,000 bootstrap samples of fathers, keeping siblings together, with random seed 6400. Paired bootstrap draws give intervals for differences.

## Interpretation and limits

These unweighted results describe selected positive earners with complete records, not all U.S. families. Income excludes farm and unincorporated business income, and PSID imputes missing components. The first two income waves have only Head income, which limits coverage and motivates the father-only comparison. The 2017 survey fields, which report 2016 earnings, are available but not used.

Averaging can reduce temporary earnings fluctuations but does not remove life-cycle bias. In the career-stage comparison, age and calendar time change together. The pattern is consistent with possible life-cycle bias, not statistical confirmation; the interval for the change includes zero. Neither comparison identifies a causal effect.

PSID citation: Panel Study of Income Dynamics, public use dataset. Produced and distributed by the Survey Research Center, Institute for Social Research, University of Michigan, Ann Arbor, MI (2026).
