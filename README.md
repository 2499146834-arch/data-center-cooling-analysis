# Data Center Cooling System Analysis

Association rule mining and decision tree classification for optimizing cooling strategies in a data center environment, built with **Weka** and visualized with **Python**.

## Overview

This project analyzes 700 operational records from a data center cold-source control system using two complementary data mining approaches:

- **Apriori Algorithm** — discover association rules between cooling parameters and system behavior (confidence 93–99%)
- **J48 Decision Tree** — classify cooling strategy actions and compare two evaluation methods (hold-out vs. 10-fold cross-validation)

### Key Insight

The Apriori analysis revealed that ambient temperature in the range **22.73–25.24°C** yields optimal system stability, with temperature deviation staying below **1.255°C** at **99% confidence** (Lift = 2.97). This provides a data-driven basis for setting operational temperature thresholds.

## Dataset

| Attribute | Description | Unit |
|---|---|---|
| Server_Workload | Server load | % |
| Inlet_Temperature | Inlet air temperature | °C |
| Outlet_Temperature | Outlet air temperature | °C |
| Ambient_Temperature | Ambient temperature | °C |
| Cooling_Unit_Power_Consumption | Cooling unit power draw | kW |
| Chiller_Usage | Chiller utilization rate | % |
| AHU_Usage | Air handling unit utilization | % |
| Temperature_Deviation | Temperature deviation | °C |
| Cooling_Strategy_Action | Cooling strategy (target) | class |

- **Instances**: 700
- **Target classes** (5): `Reduce AHU`, `Eco Mode`, `Boost All`, `Maintain`, `Increase Chiller`
- **Preprocessing**: 3-bin discretization (precision 6), 3 irrelevant attributes removed, 80/20 train-test split

## Project Structure

```
├── code/                          # Source code
│   └── analysis.ipynb             # Jupyter notebook with 16 visualizations
├── models/                        # Trained models
│   └── 决策树模型结果.model         # J48 pruned tree (127 nodes, 85 leaves)
├── results/                       # Raw output
│   ├── Apriori算法结果.txt          # Apriori full output (10 best rules)
│   └── 决策树两种方法的运行结果.txt   # Decision tree evaluation output
├── docs/                          # Documentation
│   ├── report.doc                 # Full experiment report (Chinese)
│   ├── Apriori算法分析参考.doc
│   ├── 决策树分析参考.doc
│   └── 模型选定.doc
├── images/                        # Figures organized by experiment stage
│   ├── apriori/                   # Apriori parameter config & results
│   ├── decision-tree/             # J48 config & data distribution
│   ├── cross-validation/          # 10-fold CV results
│   ├── data-split/                # Train/test split screenshots
│   ├── evaluation/                # Independent test set evaluation
│   └── tables/                    # Output data tables
└── archive/                       # Archived files
    └── report.zip
```

## Results

### Apriori Association Rules

Top 10 rules discovered (min support = 0.25, min confidence = 0.90):

| Rank | Antecedent | Consequent | Confidence | Lift |
|------|-----------|------------|------------|------|
| 1 | Ambient Temp 22.73–25.24°C | Temp Deviation < 1.255°C | **0.99** | 2.97 |
| 2 | Temp Deviation < 1.255°C | Ambient Temp 22.73–25.24°C | **0.99** | 2.97 |
| 3 | Inlet Temp > 22.50°C ∧ Power > 0.835kW | Chiller Usage > 74.86% | **0.98** | 2.91 |
| 4 | Load < 56.37% ∧ Power < 0.685kW | Chiller Usage < 56.32% | **0.97** | 2.94 |
| 5 | Load > 74.47% ∧ Chiller > 74.86% | Power > 0.835kW | **0.96** | 2.81 |
| 6 | Inlet Temp > 22.50°C ∧ Chiller > 74.86% | Power > 0.835kW | **0.95** | 2.79 |
| 7 | Load > 74.47% ∧ Power > 0.835kW | Chiller Usage > 74.86% | **0.95** | 2.83 |
| 8 | Power < 0.685kW | Chiller Usage < 56.32% | **0.94** | 2.83 |
| 9 | Load < 56.37% ∧ Chiller < 56.32% | Power < 0.685kW | **0.94** | 2.88 |
| 10 | Chiller Usage > 74.86% | Power > 0.835kW | **0.93** | 2.72 |

**Three key patterns identified**:
1. **Optimal temperature window** (Rules 1–2): Bidirectional strong association between ambient 22.73–25.24°C and deviation < 1.255°C
2. **High-load cooling chain** (Rules 3, 5–7, 10): Elevated load/temperature → high power → high chiller usage
3. **Low-load energy-saving chain** (Rules 4, 8–9): Sub-56% load → low power → reduced chiller demand

### J48 Decision Tree

| Evaluation Method | Accuracy | Kappa | F-Measure (weighted) |
|---|---|---|---|
| Train/Test split (80/20) | 21.29% | 0.016 | 0.208 |
| 10-fold Cross-Validation | 24.57% | 0.053 | 0.242 |

Tree size: **127 nodes, 85 leaves**. Root split on `Chiller_Usage(%)`.

The low classification accuracy indicates strong non-linear relationships between numeric parameters and discrete cooling strategies — in this domain, association rule mining provides significantly higher business value than classification.

### Model Comparison

| Dimension | Apriori | J48 Decision Tree |
|---|---|---|
| Prediction Accuracy | N/A (unsupervised) | 21–25% |
| Interpretability | **High** (explicit rules) | Medium (85-leaf tree) |
| Business Insight Value | **High** (actionable) | Low |
| Computational Cost | 15 iterations | 0.01–0.02 sec |

## Visualizations

The Jupyter notebook (`code/analysis.ipynb`) generates 16 figures across 4 chart panels:

- **Model comparison**: radar charts, bar charts of accuracy vs. business value
- **Rule quality**: confidence/lift/support metrics, rule ranking by business importance
- **Parameter correlation**: scatter plots of load vs. power, temperature-stability relationships, chiller-power correlation heatmap
- **Threshold analysis**: sensitivity curves for temperature thresholds, load-range energy comparison, pre/post rule implementation comparison

## Tools & Environment

| Tool | Purpose |
|---|---|
| Weka 3.x | Apriori & J48 modeling |
| Python 3.13 | Visualization & analysis |
| Jupyter Notebook | Interactive development |
| matplotlib, seaborn | Charting & heatmaps |
| pandas, numpy | Data manipulation |

## References

- Agrawal, R. & Srikant, R. (1994). *Fast Algorithms for Mining Association Rules*. VLDB.
- Quinlan, J. R. (1993). *C4.5: Programs for Machine Learning*. Morgan Kaufmann.
- Witten, I. H., Frank, E., & Hall, M. A. (2011). *Data Mining: Practical Machine Learning Tools and Techniques*. Morgan Kaufmann.
