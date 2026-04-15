[![Open in Visual Studio Code](https://classroom.github.com/assets/open-in-vscode-2e0aaae1b6195c2367325f4f02e2d04e9abb55f0b24a779b69b11b9e10269abc.svg)](https://classroom.github.com/online_ide?assignment_repo_id=23572387&assignment_repo_type=AssignmentRepo)

# Day 10 Lab: Data Pipeline & Data Observability

**Student ID:** 26ai.anhhdd@vinuni.edu.vn (2A202600064 AI20K-0064)
**Student Email:** 26ai.anhhdd@vinuni.edu.vn / dduyanhhoang@gmail.com
**Name:** Hoang Dinh Duy Anh

---

## Description

Day 10 Lab - build a complete ETL pipeline from scratch: read JSON data, validate data quality, apply business logic
transformations, and save results to CSV. Then run a stress test to observe how bad data quality breaks an AI agent's
responses.

Steps completed:

- Implemented 4 ETL functions in `solution.py`: `extract`, `validate`, `transform`, `load`
- Ran the pipeline on `raw_data.json` (5 records, 2 invalid dropped)
- Generated `garbage_data.csv` with intentional data quality issues
- Compared agent responses on clean data vs garbage data
- Wrote analysis in `experiment_report.md`

---

## How to Run

### Prerequisites

```bash
pip install pandas
```

Or with uv:

```bash
uv sync
```

### Step 1: Run the ETL Pipeline

```bash
python solution.py
```

Output:

```
ETL Pipeline Started...
Extracting data from raw_data.json...
Extracted 5 records.
Validation complete: 3 valid, 2 dropped.
Data saved to processed_data.csv

Pipeline completed! 3 records saved.
```

### Step 2: Generate Garbage Data

```bash
python generate_garbage.py
```

Output: `garbage_data.csv` with duplicate IDs, wrong types, extreme outliers, and null values.

### Step 3: Run Agent Simulation (Stress Test)

```bash
python agent_simulation.py
```

Output:

```
Testing with CLEAN data:
Agent: Based on my data, the best choice is Laptop at $1200.

Testing with GARBAGE data:
Agent: Based on my data, the best choice is Nuclear Reactor at $999999.
```

### Run Autograder Tests

```bash
pytest tests/test_autograder.py -v
```

---

## Directory Structure

```
├── solution.py              # ETL pipeline (extract, validate, transform, load)
├── agent_simulation.py      # RAG-like agent simulation
├── generate_garbage.py      # Generates garbage_data.csv for stress testing
├── raw_data.json            # Source data (5 records)
├── processed_data.csv       # Pipeline output (3 valid records)
├── garbage_data.csv         # Intentionally corrupted data
├── experiment_report.md     # Stress test analysis (clean vs garbage)
├── architecture.md          # Code flow and architecture documentation
└── README.md                # This file
```

---

## Results

- **Records extracted:** 5
- **Records dropped (invalid):** 2 - Mystery Box (price = -10), Phone (empty category)
- **Records saved to CSV:** 3 - Laptop, Chair, Monitor
- **Discount applied:** 10% (`discounted_price = price * 0.9`)
- **Categories normalized:** Title Case (`electronics` $\rightarrow$ `Electronics`)
- **Timestamp added:** `processed_at` column in ISO format

### Agent Stress Test

| Data         | Agent Answer               | Correct?                              |
|--------------|----------------------------|---------------------------------------|
| Clean data   | Laptop at $1200            | Yes                                   |
| Garbage data | Nuclear Reactor at $999999 | No - extreme outlier dominated result |
