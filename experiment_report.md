# Experiment Report: Data Quality Impact on AI Agent

**Student ID:** 26ai.anhhdd@vinuni.edu.vn (2A202600064 AI20K-0064)
**Name:** Hoang Dinh Duy Anh
**Date:** 2026-04-15

---

## 1. Experiment Results

Ran `agent_simulation.py` with two datasets and recorded the results:

| Scenario                          | Agent Response                                                      | Accuracy (1-10) | Notes                                                                                               |
|-----------------------------------|---------------------------------------------------------------------|-----------------|-----------------------------------------------------------------------------------------------------|
| Clean Data (`processed_data.csv`) | "Based on my data, the best choice is Laptop at \$1200."            | 10              | Correct product and price; validation removed invalid records before processing                     |
| Garbage Data (`garbage_data.csv`) | "Based on my data, the best choice is Nuclear Reactor at \$999999." | 1               | Extreme outlier (\$999,999) dominated `idxmax()`; duplicate IDs and wrong types were never filtered |

### Pipeline output (solution.py)

```
ETL Pipeline Started...
Extracting data from raw_data.json...
Extracted 5 records.
Validation complete: 3 valid, 2 dropped.
Data saved to processed_data.csv

Pipeline completed! 3 records saved.
```

### Agent simulation output (agent_simulation.py)

```
Testing with CLEAN data:
Agent: Based on my data, the best choice is Laptop at \$1200.

Testing with GARBAGE data:
Agent: Based on my data, the best choice is Nuclear Reactor at \$999999.
```

---

## 2. Analysis

### Why does the Agent answer incorrectly when using Garbage Data?

When the agent consumed garbage data instead of clean data, it returned a completely wrong and misleading answer. The
root cause is that the ETL pipeline's `validate()` step was bypassed - `garbage_data.csv` was written directly without
any quality checks applied.

Several specific data quality issues caused the failure:

1. **Extreme outlier** - The Nuclear Reactor record has a price of 999,999, which is 833 times higher than the next
   highest item (Laptop at \$1,200). Since the agent uses `idxmax()` to find the best deal, this single outlier
   dominates every query and produces an absurd, unusable recommendation.

2. **Wrong data types** - The "Broken Chair" record has `price = "ten dollars"` (a string instead of a number). When
   pandas reads this, the column becomes a mixed-type object dtype, causing numeric operations like `idxmax()` to
   silently skip or mishandle those rows.

3. **Duplicate IDs** - Two records share `id = 1` (Laptop and Banana). If the agent were doing a lookup by ID, results
   would be ambiguous and non-deterministic - the same query could return different products.

4. **Null/missing values** - The Ghost Item row has `id = None` and `category = None`. These nulls can propagate through
   filtering logic, causing NaN comparisons that silently exclude or misroute records in unexpected ways.

Together, these issues show that no matter how smart the agent's prompt is, corrupted input data will produce corrupted
output. The agent cannot reason correctly over data it cannot trust.

---

## 3. Conclusion

**Quality Data > Quality Prompt? Yes, absolutely.**

A well-engineered prompt cannot compensate for fundamentally broken input data. In this experiment, the agent's logic
was identical for both runs - only the data changed. Clean data produced a perfect answer (Laptop, \$1,200); garbage
data produced a confidently wrong answer (Nuclear Reactor, \$999,999). This is the "garbage in, garbage out" principle
applied to AI agents: an agent is only as reliable as the data pipeline feeding it. Investing in data validation, type
enforcement, and outlier detection upstream is more impactful than refining prompts downstream.
