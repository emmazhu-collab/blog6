# PSID inputs: local files only

Obtain your own data from the [PSID Packaged Data page](https://simba.isr.umich.edu/Zips/ZipMain.aspx) and [Data Center](https://simba.isr.umich.edu/DC/i.aspx). Registration is required. Follow the [Conditions of Use](https://simba.isr.umich.edu/U/CondUse.aspx), including the restrictions on redistribution and AI/LLM use. Do not upload the files to GitHub or an AI service.

Place these files here:

```text
pid23/PID23.txt
pid23/PID23.do
ind2023er/IND2023ER.txt
ind2023er/IND2023ER.do
J366467.xlsx
J366467_codebook.pdf
J366467_labels.txt
```

Keep the rest of the supplied documentation in the two original folders. The existing local originals were moved here without changing their contents. Git excludes everything in this folder except this README.

Download Parent Identification 2023 and Cross-year Individual 1968–2023. This analysis used the December 11, 2025 Parent Identification release and December 2, 2025 Individual File release. Newer releases may change results.

## Permanent IDs and individual fields

The child uses `PID2` + `PID3`; the birth father uses `PID23` + `PID24`. They match `ER30001` + `ER30002` in the Individual File. The script combines each pair as family ID times 1,000 plus person number. Unknown father person number 999 is excluded.

The needed individual fields are interview number, sequence number, family role, and age:

| Interview year | Family ID | Sequence | Role | Age |
| --- | --- | --- | --- | --- |
| 2011 | ER34101 | ER34102 | ER34103 | ER34104 |
| 2013 | ER34201 | ER34202 | ER34203 | ER34204 |
| 2015 | ER34301 | ER34302 | ER34303 | ER34305 |
| 2019 | ER34701 | ER34702 | ER34703 | ER34704 |
| 2021 | ER34901 | ER34902 | ER34903 | ER34904 |
| 2023 | ER35101 | ER35102 | ER35103 | ER35104 |

## Family income extract

Create a family-level extract containing the variables below for all available families, without a custom sample restriction. Income is reported for the year before the interview. Export Excel and keep the original variable names as column headers. The original custom job was `J366467`; your job will have a different number. Rename your Excel file to `J366467.xlsx` for this script. Different releases or extraction settings may change the reference results.

| Interview | Release | Family interview ID | Head / Reference Person income | Spouse/Partner income |
| --- | --- | --- | --- | --- |
| 2011 | ER47301 | ER47302 | ER52237 | Not in this extract |
| 2013 | ER53001 | ER53002 | ER58038 | Not in this extract |
| 2015 | ER60001 | ER60002 | ER65216 | ER65244 |
| 2017 | ER66001 | ER66002 | ER71293 | ER71321 |
| 2019 | ER72001 | ER72002 | ER77315 | ER77343 |
| 2021 | ER78001 | ER78002 | ER81642 | ER81670 |
| 2023 | ER82001 | ER82002 | ER85496 | ER85524 |

These are the 26 columns in the original workbook, in the displayed order. The current reader expects this original layout. The 2017 columns are retained in the original input but not used in the analysis. The income variables measure labor income excluding farm and unincorporated business income. Read the supplied codebook before changing the income definition.

No individual-level processed extract is included in the public repository. The analysis creates person-level tables temporarily in memory and saves only aggregate statistics and figures.
