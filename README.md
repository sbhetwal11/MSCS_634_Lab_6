# MSCS_634_Lab_6 : Association Rule Mining with Apriori and FP-Growth

**Name:** Sagar Bhetwal  
**Course:** Advanced Big Data and Data Mining  
**Instructor:** Dr. Satish Penmatsa  
**Lab Title:** Lab 6: Association Rule Mining with Apriori and FP-Growth

---

## Overview

This lab explores **association rule mining** on transactional retail data using two well-known algorithms:

- **Apriori**
- **FP-Growth**

The notebook uses the **Online Retail Dataset** from the UCI Machine Learning Repository and walks through the full process of:

1. loading and cleaning transaction-level retail data,
2. exploring item frequency and item co-occurrence patterns,
3. mining frequent itemsets with Apriori,
4. mining frequent itemsets with FP-Growth,
5. generating association rules using support, confidence, and lift,
6. comparing both algorithms in terms of output and runtime.

The goal of the lab is to understand how association rule mining can uncover hidden buying patterns and how those patterns can be interpreted for practical business use cases such as **cross-selling, bundling, recommendation systems, and store layout decisions**.

---

## Repository Contents

This repository should contain at least the following files:

- `Lab6_Association_Rule_Mining.ipynb` — Jupyter Notebook with the full lab work
- `README.md` — explanation of the dataset, workflow, results, and observations

If you want to make the repository cleaner, a good structure is:

```text
MSCS_634_Lab_6/
├── Lab6_Association_Rule_Mining.ipynb
└── Readme.md(report)
```

## Dataset Used

This notebook uses the **Online Retail Dataset** from the **UCI Machine Learning Repository**.

### Dataset characteristics
The dataset contains transactional records from a UK-based online retail store between **December 2010 and December 2011**. Each row represents an item purchased within an invoice.

### Why this dataset fits the lab
This dataset is a good match for association rule mining because it contains:

- invoice-level transaction identifiers,
- item descriptions,
- quantities purchased,
- customer and country fields,
- item-level purchase records that can be converted into a basket matrix.

Association rule mining works best when the data can be represented as a set of transactions containing items, and this dataset naturally supports that structure.

---

## Objective of the Lab

The main objective of this lab is to identify **frequent item combinations** and determine whether the purchase of one item is associated with the purchase of another item.

More specifically, the notebook aims to:

- identify the most common products in the retail dataset,
- visualize frequent items and item co-occurrence,
- extract frequent itemsets using Apriori,
- extract frequent itemsets using FP-Growth,
- generate association rules from frequent itemsets,
- interpret support, confidence, and lift values,
- compare the performance and output of both algorithms.

---

## Step 1 — Data Preparation

The notebook begins by loading the Online Retail Excel file directly from UCI and then performing basic data cleaning.

### Libraries used
The notebook installs and imports:

- `pandas`
- `numpy`
- `matplotlib`
- `seaborn`
- `mlxtend`
- `openpyxl`

### Raw dataset size
After loading, the raw dataset shape shown in the notebook is:

- **541,909 rows**
- **8 columns**

### Cleaning steps performed
The notebook applies several important cleaning operations:

1. **Drops rows with missing values**
   - This is important because missing item descriptions or other critical fields can break the basket transformation step.

2. **Removes canceled transactions**
   - Invoices beginning with `"C"` are excluded.
   - This avoids mixing returned/canceled orders with actual purchase behavior.

3. **Keeps only positive quantities**
   - Negative or zero quantities do not represent normal purchases and can distort the association patterns.

4. **Strips whitespace from product descriptions**
   - This avoids duplicate item names caused by formatting differences.

### Cleaned dataset size
After cleaning, the notebook reports:

- **397,884 rows**
- **18,532 unique transactions**
- **3,866 unique items**

This shows that the dataset is still large after cleaning, which makes it a realistic retail mining example.

---

## Exploratory Data Analysis and Visualizations

The notebook includes visual exploration before applying the mining algorithms.

### 1. Top 20 most frequently purchased items
A Seaborn barplot is used to show the most commonly purchased products in the cleaned data.

This visualization helps answer a basic but important question:

> Which products appear most often in customer purchases?

This is useful because frequent singleton items often dominate the support distribution and can shape the association rule results.

### 2. Item co-occurrence heatmap
The notebook also builds a **co-occurrence heatmap** for the **top 15 items**.

How it works:

- the notebook selects the top 15 item descriptions,
- builds a transaction-by-item basket representation,
- computes an item co-occurrence matrix,
- uses a Seaborn heatmap to show how often pairs of top items appear together.

This is a strong visualization choice because it makes pairwise item relationships easier to see than just reading raw counts.

---

## Basket Construction for Mining

Before running Apriori and FP-Growth, the notebook narrows the data to make computation more manageable.

### Filtering used
The mining portion uses:

- only **United Kingdom** transactions,
- only the **top 100 items** by frequency in the UK subset.

This is a practical step because full retail basket mining on thousands of items can become expensive and noisy.

### Basket matrix
The notebook converts transactions into a binary basket matrix where:

- rows = invoices,
- columns = item descriptions,
- values = whether the item appears in the invoice (`True`/`False`).

The reported basket matrix shape is:

- **13,408 transactions**
- **100 items**

This binary matrix is the direct input for both Apriori and FP-Growth.

---

## Step 2 — Frequent Itemset Mining with Apriori

The notebook applies the **Apriori** algorithm using:

- **minimum support = 0.05**
- **maximum itemset length = 4**

### Why support = 0.05 matters
A minimum support threshold of 5% means that an itemset must appear in at least 5% of all transactions in the basket matrix to be considered frequent.

This is a relatively strict threshold, which has two practical effects:

- it filters out weak or rare combinations,
- it keeps the number of resulting itemsets manageable.

### Apriori results
The notebook reports:

- **Execution time:** `0.0243 seconds`
- **Frequent itemsets found:** `43`

### Highest-support Apriori itemsets shown in the notebook
Some of the top frequent singleton itemsets include:

- `WHITE HANGING HEART T-LIGHT HOLDER` — support `0.140513`
- `JUMBO BAG RED RETROSPOT` — support `0.107921`
- `REGENCY CAKESTAND 3 TIER` — support `0.105161`
- `ASSORTED COLOUR BIRD ORNAMENT` — support `0.096957`
- `PARTY BUNTING` — support `0.096211`

These results indicate that certain home décor, gift, and bag-related items appear repeatedly across many orders.

### Apriori visualizations
The notebook includes:

1. **Top 20 frequent itemsets barplot**
   - shows support values for the most common itemsets.

2. **Itemset length distribution**
   - shows how many frequent itemsets are of size 1, 2, 3, and so on.

These plots help make the Apriori output more interpretable than a raw table alone.

---

## Step 3 — Frequent Itemset Mining with FP-Growth

The notebook applies **FP-Growth** using the same settings:

- **minimum support = 0.05**
- **maximum itemset length = 4**

Using the same threshold makes the comparison with Apriori fair.

### FP-Growth results
The notebook reports:

- **Execution time:** `0.1025 seconds`
- **Frequent itemsets found:** `43`

### Output comparison with Apriori
The notebook’s actual output shows that FP-Growth and Apriori produced:

- the **same number of frequent itemsets**,
- effectively the **same frequent itemsets**, just possibly in a different order.

That consistency is expected because both algorithms solve the same frequent itemset mining problem under the same support threshold.

### FP-Growth visualization
A Seaborn barplot is used to display the top frequent itemsets from FP-Growth, matching the same presentation style used for Apriori.

The notebook also includes a bar chart comparing the runtime of the two algorithms.

---

## Important Observation from the Actual Notebook Output

One thing that stood out when reading the notebook carefully is that the **written discussion and the actual runtime output do not fully match**.

### What the code output shows
From the executed notebook:

- **Apriori:** `0.0243 s`
- **FP-Growth:** `0.1025 s`

So in this particular run, **Apriori was faster** than FP-Growth.

### What the markdown discussion says
Some later markdown cells say that FP-Growth was faster, which is the common theoretical expectation on larger or denser datasets.

### Likely reason for this difference
That mismatch most likely happened because the notebook reduced the problem size significantly by:

- filtering to UK transactions only,
- keeping only the top 100 items,
- using a fairly high support threshold of 5%.

On a smaller and simpler basket matrix, Apriori can sometimes run very quickly and even beat FP-Growth in practice. So the theory is still fine, but the **measured runtime in this notebook run** says Apriori was faster.

That is worth acknowledging in the report because it shows careful reading rather than blindly repeating theory.

---

## Step 4 — Association Rule Generation

The notebook attempts to generate association rules from both frequent itemset outputs using:

- **minimum confidence = 0.5**

The rules are evaluated with:

- **support**
- **confidence**
- **lift**

### Meaning of the rule metrics

#### Support
Support measures how often an itemset or rule appears in the dataset.

#### Confidence
Confidence measures how often the consequent appears when the antecedent appears.

#### Lift
Lift compares the observed co-occurrence against what would be expected if the items were independent.

- `lift > 1` suggests a positive association,
- `lift = 1` suggests no meaningful association,
- `lift < 1` suggests a negative association.

---

## Actual Association Rule Results

After reading the notebook outputs carefully, the actual result is:

- **Rules from Apriori:** `0`
- **Rules from FP-Growth:** `0`

So the rule-generation stage produced **no association rules** under the chosen thresholds.

### Why no rules were generated
This likely happened because the thresholds and filtering were too restrictive together:

- only the top 100 UK items were used,
- support was set at `0.05`,
- confidence was set at `0.5`.

That combination can easily leave only strong singleton itemsets and a limited number of multi-item frequent itemsets, which means no rule may satisfy the confidence threshold.

### Why this matters
This is an important result, not a failure.

It shows that association rule mining is sensitive to parameter choices. A dataset may still yield frequent single items and even some frequent itemsets, but not necessarily enough strong directional rules at a strict confidence threshold.

### Visualizations still present in the notebook
The notebook includes scatter plots for:

- confidence vs. lift,
- support vs. confidence.

However, because no rules were generated, those plots do not provide meaningful rule insight in this executed version of the notebook.

---

## Step 5 — Comparative Analysis

The notebook compares Apriori and FP-Growth using runtime, itemset counts, and rule counts.

### Comparison summary from the executed notebook

| Metric | Apriori | FP-Growth |
|---|---:|---:|
| Execution Time (s) | 0.0243 | 0.1025 |
| Frequent Itemsets Found | 43 | 43 |
| Association Rules Generated | 0 | 0 |
| Min Support Used | 0.05 | 0.05 |
| Min Confidence Used | 0.50 | 0.50 |

### Interpretation
From the actual run:

- both algorithms produced the same mining output,
- Apriori ran faster in this specific reduced dataset,
- neither approach produced association rules with the current rule threshold.

---

## Main Findings

### 1. The data was appropriate for basket analysis
The Online Retail dataset has transaction-level item records and is a strong example of real-world retail data for association mining.

### 2. Data cleaning mattered a lot
Removing canceled invoices, dropping missing values, and keeping only positive quantities helped ensure that the model focused on actual purchase behavior.

### 3. Frequent individual items were identified clearly
The notebook successfully found high-support products that repeatedly appear across orders.

### 4. Co-occurrence exploration added value
The heatmap made it easier to spot which popular products tend to appear together.

### 5. Both algorithms found the same frequent itemsets
This confirms that both Apriori and FP-Growth were implemented correctly under the chosen support threshold.

### 6. Apriori was faster in this actual notebook run
Although FP-Growth is often faster in theory for larger or denser problems, the executed notebook showed Apriori with the lower runtime.

### 7. No association rules were generated at the chosen thresholds
This suggests that the support/confidence settings were strict enough to filter out all qualifying rules.

---

## Challenges Faced in the Lab

The notebook reflects several practical challenges that often come up in real association mining work.

### 1. Large dataset size
The raw retail dataset is large enough to make frequent itemset mining expensive if used without filtering.

**How it was handled:**  
The notebook restricted the mining step to UK transactions and the top 100 items.

### 2. Noisy transaction records
Retail data often includes canceled orders, returns, and formatting inconsistencies in product names.

**How it was handled:**  
The notebook removed canceled transactions, filtered positive quantities, and cleaned item descriptions.

### 3. Threshold sensitivity
Support and confidence thresholds have a huge effect on output size and interpretability.

**What happened here:**  
The chosen thresholds were strict enough that no rules were produced.

### 4. Theory versus measured runtime
Theoretical expectations do not always match actual measured results on reduced datasets.

**What happened here:**  
Even though FP-Growth is usually expected to scale better, Apriori finished faster in this notebook run.

---

## How the Lab Could Be Improved

If this notebook is extended later, a few changes could produce stronger association rule results.

### Option 1 — Lower the support threshold
Reducing support from `0.05` to something like `0.02` or `0.01` may allow more multi-item combinations to survive.

### Option 2 — Lower the confidence threshold
Reducing confidence from `0.5` to `0.3` or `0.4` might allow meaningful rules to appear.

### Option 3 — Increase the item pool carefully
Using more than the top 100 items could uncover richer patterns, though that would increase runtime and complexity.

### Option 4 — Focus on a product subset
Instead of using only top-frequency filtering, the analysis could focus on a more coherent product category where cross-purchase behavior is stronger.

### Option 5 — Add business interpretation after tuning
Once rules exist, the notebook could highlight rules suited for:
- recommendations,
- bundling,
- placement strategy,
- promotional targeting.

---

## Business Relevance of Association Rule Mining

Even though this notebook produced zero final rules at the selected threshold, the lab still demonstrates the real business value of the technique.

In real retail or e-commerce settings, association rule mining can help with:

- **product recommendation systems**
- **bundle creation**
- **cross-selling strategies**
- **promotion planning**
- **inventory placement decisions**
- **market basket analysis**

For example, if two or three items are repeatedly purchased together, a business can:

- place them close to each other,
- create bundle discounts,
- recommend them during checkout,
- plan targeted campaigns based on common basket patterns.

---

## How to Run the Notebook

1. Clone the repository.
2. Open the project folder.
3. Install the required packages.
4. Launch Jupyter Notebook or Jupyter Lab.
5. Run the notebook cells in order.

Example setup:

```bash
pip install pandas numpy matplotlib seaborn mlxtend openpyxl
jupyter notebook
```

The notebook downloads the dataset automatically from UCI if the Excel file is not already present locally.

---

## Reproducibility Notes

To reproduce the notebook exactly, keep the following in mind:

- the same data cleaning steps should be used,
- the same UK-only and top-100-item filtering should be applied,
- the support threshold should remain at `0.05`,
- the confidence threshold should remain at `0.5`.

If any of those settings change, the number of itemsets and rules may change significantly.

---

## Final Conclusion

This lab provided a complete hands-on walkthrough of association rule mining using a real retail transaction dataset. The notebook successfully cleaned the dataset, explored item patterns visually, transformed transactions into a basket matrix, and mined frequent itemsets using both Apriori and FP-Growth.

The strongest takeaway from the actual notebook execution is that:

- both algorithms identified the same frequent itemsets,
- Apriori ran faster than FP-Growth in this reduced experiment,
- no association rules were generated at the chosen thresholds.

That final point is still meaningful because it shows that association mining is highly dependent on parameter tuning and dataset structure. Overall, the lab did a solid job demonstrating the end-to-end workflow of market basket analysis and showed how visualization can support interpretation of retail transaction patterns.
