# 🚢 Port Shipping Data Cleaning Pipeline

> A complete **end-to-end data cleaning and transformation pipeline** for maritime shipping data.  
> Transforms raw, messy multi-sheet Excel workbooks into clean, structured datasets ready for publishing.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Input vs Output](#input-vs-output)
- [Reference Data](#reference-data)
- [Pipeline Steps](#pipeline-steps)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [How to Run](#how-to-run)
- [Output Files](#output-files)

---

## Overview

This project automates the cleaning and enrichment of raw maritime shipping route data that arrives as messy, inconsistently formatted Excel workbooks with multiple country sheets.

The pipeline handles:
- **Inconsistent formatting** — mixed casing, extra spaces, split cells
- **Typos in port names** — fixed automatically using fuzzy string matching
- **City names instead of port names** — replaced using a reference lookup table
- **Mismatched country names** — detected and corrected
- **Multi-language price formatting** — prices output in 7 languages including Arabic-Indic numerals
- **Route URL generation** — unique identifiers built from port codes
- **Publish & expiry date assignment** — automated date stamping

---

## Input vs Output

### 📥 Input — Raw Excel Workbook (Multi-Sheet)

The raw data comes as a multi-sheet Excel file. Each sheet represents a country, with trip routes and price offers written in unstructured text format.

**Raw trip column example:**
```
FROM Frederikshavn TO Liepaja
from Emden TO Fos-sur-Mer
FROM Zeebrugge  TO Bensersiel
```

**Raw price offer column example:**
```
Trip: ONE WAY\nPrice starts from:EUR  3306\n
Trip:ONE WAY\nprice start from :EUR  6363
```

![Input Raw Excel](images/input_multi_sheet.png)

*The raw input: unstructured trip/price text across multiple country sheets*

---

### 📤 Output 1 — Full Clean Dataset (`full_clean_dataset.xlsx`)

All sheets merged and enriched into one clean, structured table with properly parsed columns.

![Output Clean Dataset](images/input_raw_excel.png)

| Column | Description |
|--------|-------------|
| `from` | Origin port name (cleaned) |
| `to` | Destination port name (cleaned) |
| `price` | Numeric price extracted |
| `type` | Trip type (`O` = one-way, `R` = round) |
| `country id` | Country numeric ID |
| `from Code` | Origin port 3-letter code |
| `from ID` | Origin port numeric ID |
| `to Code` | Destination port 3-letter code |
| `to ID` | Destination port numeric ID |

---

### 📤 Output 2 — Port Content (`port_content.xlsx`)

Deduplicated route table with IDs, unique URLs, and publish/expiry dates — ready for database import.

![Output Port Content](images/output_port_content.png)
![Output Port Content Extended](images/output_port_content2.png)

| Column | Description |
|--------|-------------|
| `from ID` | Origin port numeric ID |
| `type` | Trip type |
| `to ID` | Destination port numeric ID |
| `url` | Route URL slug, e.g. `CAC-ZZK-O` |
| `puplish_date` | Today's date |
| `expir_date` | Expiry date (37 days from today) |

---

### 📤 Output 3 — Pricing Template (`Port_templet.xlsx`)

Per-route pricing formatted as a multilingual JSON string, covering 7 languages.

![Output Port Template](images/output_port_template.png)

**Price format example:**
```json
{"en":"3306","ar":٣٣٠٦,"gr":3306,"it":3306,"cz":3306,"fr":3306,"sk":3306}
```

Arabic prices use **Arabic-Indic numerals** (٠١٢٣٤٥٦٧٨٩) for proper localization.

---

## Reference Data

The pipeline uses two reference Excel files to enrich and validate the data:

### 🌍 Countries Reference (`Countries IDs.xlsx`)

Maps country names to their numeric IDs.

![Countries Reference](images/ref_countries.png)

### 🏙️ Ports Reference (`Ports_ceties_codes_and_IDs.xlsx`)

Maps port names, city names, 3-letter codes, and numeric IDs.

![Ports Reference](images/ref_ports.png)

---

## Pipeline Steps

```
Raw Excel (multi-sheet)
        │
        ▼
1. Load all sheets & merge into one DataFrame
        │
        ▼
2. Drop fully empty rows
        │
        ▼
3. Fix split price-offer cells (merge across rows)
        │
        ▼
4. Extract: from / to / price / type using regex & string parsing
        │
        ▼
5. Detect & fix mismatched country names → merge with Countries IDs
        │
        ▼
6. Detect & fix typos in port names using fuzzy matching (fuzzywuzzy)
        │
        ▼
7. Replace city names with official port names
        │
        ▼
8. Merge with Ports reference → get from/to codes & IDs
        │
        ▼
9. Generate route URL slug (FROMCODE-TOCODE-TYPE)
        │
        ▼
10. Add publish date & expiry date (today + 37 days)
        │
        ▼
11. Format prices in 7 languages (EN, AR, GR, IT, CZ, FR, SK)
        │
        ▼
Output: full_clean_dataset.xlsx
        port_content.xlsx
        Port_templet.xlsx
```

---

## Project Structure

```
port-cleaning-pipeline/
│
├── Port_Cleaning_Pipeline.ipynb   # Main Jupyter Notebook
│
├── input/
│   └── My port/
│       ├── Countries IDs.xlsx             # Country name → ID reference
│       ├── MyPoert AUG sheet1.xlsx        # Raw multi-sheet shipping data
│       └── Ports_ceties_codes_and_IDs.xlsx # Port name/code/ID reference
│
├── output/
│   ├── full_clean_dataset.xlsx    # Full enriched dataset
│   ├── port_content.xlsx          # Route content with URLs & dates
│   └── Port_templet.xlsx          # Multilingual pricing template
│
└── README.md
```

---

## Requirements

```bash
pip install pandas numpy openpyxl fuzzywuzzy
```

| Library | Purpose |
|---------|---------|
| `pandas` | Data loading, cleaning, merging |
| `numpy` | Numerical operations |
| `openpyxl` | Reading/writing Excel files |
| `fuzzywuzzy` | Fuzzy string matching for port name correction |
| `re` | Regex for price and route extraction |
| `datetime` | Date stamping routes |

> **Tip:** Install `python-Levenshtein` to speed up `fuzzywuzzy`:
> ```bash
> pip install python-Levenshtein
> ```

---

## How to Run

1. Clone the repository and place your input Excel files in `input/My port/My port/`
2. Open `Port_Cleaning_Pipeline.ipynb` in Jupyter Notebook or JupyterLab
3. Run all cells (`Kernel → Restart & Run All`)
4. Find the cleaned output files in the `output/` folder

```bash
git clone https://github.com/your-username/port-cleaning-pipeline.git
cd port-cleaning-pipeline
jupyter notebook Port_Cleaning_Pipeline.ipynb
```

---

## Output Files

| File | Rows | Description |
|------|------|-------------|
| `full_clean_dataset.xlsx` | 180 | Complete enriched route dataset |
| `port_content.xlsx` | 52 | Deduplicated routes with dates & URLs |
| `Port_templet.xlsx` | 180 | Multilingual pricing per route |

---

*Built with Python · pandas · fuzzywuzzy*
