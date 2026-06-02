# 🚢 Port Shipping Data Cleaning Pipeline

A data cleaning and transformation pipeline built with Python to process raw shipping port data from Excel files into structured, ready-to-use datasets.

---

## 📋 Project Overview

This project automates the cleaning and standardization of maritime shipping data across multiple countries. It takes messy, multi-sheet Excel workbooks and transforms them into clean, structured datasets with proper port codes, country IDs, URLs, and pricing information.

---

## 🗂️ Datasets Used

| File | Description |
|------|-------------|
| `Countries IDs.xlsx` | Mapping of country names to their IDs |
| `MyPoert AUG sheet1.xlsx` | Raw shipping data with multiple country sheets |
| `Ports_ceties_codes_and_IDs.xlsx` | Port names, city names, codes, and IDs |

---

## ⚙️ What the Pipeline Does

### 1. 📥 Data Loading
- Reads multiple Excel files using `pandas`
- Loads all sheets from the ports workbook dynamically

### 2. 🔗 Data Merging
- Merges all country sheets into one unified DataFrame
- Extracts three key columns: `trip`, `price offer`, `country`

### 3. 🧹 Data Cleaning
- Removes rows where both `trip` and `price offer` are missing
- Resets index after dropping missing values
- Strips extra whitespace from port names

### 4. ✂️ Feature Extraction
Using regex and string parsing to extract:
- `from` — origin port
- `to` — destination port
- `price` — numeric price
- `type` — shipment type

### 5. 🌍 Country Standardization
- Detects country names not matching the reference dataset
- Corrects mismatched country names (e.g., typos, alternate spellings)
- Merges with country IDs reference table

### 6. 🔍 Fuzzy Port Matching
- Detects port names not matching the reference port list
- Uses `fuzzywuzzy` library to find closest matching port names
- Automatically replaces unrecognized ports with best matches (similarity > 60%)

### 7. 🔁 Port ID & Code Enrichment
- Merges port data twice — once for origin, once for destination
- Adds `from ID`, `from Code`, `to ID`, `to Code`

### 8. 🔗 URL Generation
- Generates unique route URLs in format: `FROMCODE-TOCODE-TYPE`

### 9. 📅 Date Management
- Adds `publish_date` (today's date)
- Adds `expiry_date` (37 days from today)

### 10. 💰 Price Formatting
- Formats price into a multi-language JSON string supporting:
  `en`, `ar`, `gr`, `it`, `cz`, `fr`, `sk`

### 11. 📤 Export
- Exports two clean Excel files:
  - `port_content.xlsx` — route content data
  - `port_templet.xlsx` — route pricing templates

---

## 📦 Requirements

```bash
pip install pandas numpy openpyxl fuzzywuzzy python-Levenshtein matplotlib
```

---

## 🚀 How to Run

1. Clone the repository:
```bash
git clone https://github.com/your-username/port-data-cleaning.git
cd port-data-cleaning
```

2. Place your input Excel files in the `input/` folder

3. Open and run the notebook:
```bash
jupyter notebook
```

4. Find your output files in the `output/` folder:
   - `port_content.xlsx`
   - `port_templet.xlsx`

---

## 📁 Project Structure

```
port-data-cleaning/
│
├── input/
│   ├── Countries IDs.xlsx
│   ├── MyPoert AUG sheet1.xlsx
│   └── Ports_ceties_codes_and_IDs.xlsx
│
├── output/
│   ├── port_content.xlsx
│   └── port_templet.xlsx
│
├── notebook.ipynb       ← Main cleaning pipeline
├── requirements.txt     ← Python dependencies
└── README.md
```

---

## 🛠️ Libraries Used

| Library | Purpose |
|---------|---------|
| `pandas` | Data manipulation and Excel I/O |
| `numpy` | Numerical operations |
| `re` | Regex for string parsing |
| `fuzzywuzzy` | Fuzzy string matching for port names |
| `matplotlib` | Visualization |
| `datetime` | Date generation |

---

## 👤 Author

**Mahmoud Okasha**  
Data Analyst  
📍 Egypt

---

## 📄 License

This project is licensed under the MIT License.
