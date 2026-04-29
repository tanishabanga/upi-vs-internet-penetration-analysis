# 🚀 UPI Growth vs Internet Penetration — India

An end-to-end data science project analyzing whether India's explosive UPI growth is driven by internet penetration or by other multi-factorial forces — using real government datasets, ML models, and an interactive Panel dashboard.

---

## 📌 Project Overview

| Item | Detail |
|---|---|
| **Period** | 2016-17 to 2022-23 |
| **Datasets** | NPCI (RS Session 260), TRAI (Table 3.1), UPI P2P/P2M Monthly Data |
| **Models** | Linear Regression, Random Forest Regressor |
| **Dashboard** | Panel + Plotly (interactive, multi-tab) |
| **Language** | Python 3.14 |

**Research Question:** Is internet penetration the primary driver of UPI's exponential growth, or do time, policy, ecosystem readiness, and behavioral factors dominate?

---

## 📁 Project Structure

```
UpiInternetProject/
│
├── UpiInternetProject_11.ipynb   # Main notebook (latest, all fixes applied)
├── README.md                     # This file
│
├── screenshots/                  # Add your screenshots here
│   ├── dashboard_overview.png
│   ├── dashboard_growth.png
│   ├── dashboard_comparison.png
│   ├── dashboard_ml.png
│   ├── dashboard_conclusion.png
│   └── eda_complete.png
│
└── outputs/
    └── eda_complete.png          # Auto-saved EDA figure (Cell 7)
```

---

## 🗂️ Notebook Structure

The notebook is organized into 13 cells, each mapping to a clear step:

| Cell | Step | Description |
|---|---|---|
| 1 | Setup | Library imports, Panel/Plotly initialization |
| 2 | Data Loading | Load UPI yearly, UPI monthly, and Internet subscriber datasets |
| 3 | Data Integration | Aggregate internet data by year, merge with UPI on calendar year, derive `Internet_Penetration_Pct` |
| 4 | Fix Cell | Pre-compute `Volume_Growth_Pct` and `Value_Per_Txn` before feature engineering |
| 5 | Feature Engineering | Create 11 new features: log transforms, YoY growth rates, interaction terms, subscriber ratios |
| 6 | EDA (Stats) | Descriptive statistics, correlation matrix, key summary metrics |
| 7 | Visualization | 9-panel matplotlib figure saved as `eda_complete.png` |
| 8 | Machine Learning | Train Linear Regression + Random Forest with LOO cross-validation |
| 9 | Model Evaluation | Residual plots, actual vs predicted comparison |
| 10 | Dashboard Helpers | Reusable Panel components + Plotly chart builder functions |
| 11 | Dashboard Assembly | 5-tab interactive dashboard (Overview, Growth, Comparison, ML Models, Conclusion) |
| 12 | Launch | `dashboard.show(port=5007)` |
| 13 | Scratch | Empty/scratch cell |

---

## ⚙️ Feature Engineering

New columns created on `merged_df`:

| Feature | Formula | Purpose |
|---|---|---|
| `Year_Idx` | Sequential 1–7 | Ordinal time trend |
| `Log_Volume` | `log1p(Volume_Million)` | Stabilize exponential growth |
| `Log_Value` | `log1p(Value_Crore)` | Stabilize exponential growth |
| `Log_Subscribers` | `log1p(Total_Subscribers)` | Reduce skew |
| `Subscribers_Sq` | `Total_Subscribers²` | Non-linear term |
| `Volume_Growth_Pct` | `pct_change() × 100` | YoY % change in UPI volume |
| `Value_Growth_Pct` | `pct_change() × 100` | YoY % change in UPI value |
| `Subs_Growth_Pct` | `pct_change() × 100` | YoY % change in internet subscribers |
| `Value_Per_Txn` | `Value_Crore / Volume_Million` | Average ticket size |
| `Rural_Urban_Ratio` | `Rural_Subs / Urban_Subs` | Digital divide proxy |
| `Penetration_x_Volume` | `Penetration_Pct × Volume_Million` | ML interaction term |

---

## 🤖 Machine Learning

**Target:** `Log_Volume` (log-transformed UPI transaction volume)

**Features used:**
- `Total_Subscribers`
- `Internet_Penetration_Pct`
- `Urban_Subs`
- `Rural_Subs`

**Preprocessing:** `StandardScaler` (zero-mean, unit-variance)

**Cross-validation:** `LeaveOneOut` — chosen because the dataset has only 7 annual observations; LOO maximizes training data per fold.

**Models:**

| Model | Train R² | Notes |
|---|---|---|
| Linear Regression | ~0.93 | Overfitted on small sample |
| Random Forest (200 trees) | ~0.86 | Captures non-linear patterns |

**Top feature (RF):** `Rural_Subs` — rural subscriber growth is the strongest predictor of UPI volume.

---

## 📊 Dashboard Tabs

The interactive Panel dashboard (launched on port 5007) has 5 tabs:

| Tab | Content |
|---|---|
| 🏠 Overview | Hero banner, 6 KPI cards, UPI Volume & Value trend charts |
| 📈 Growth | YoY growth comparison, monthly P2P vs P2M trends, internet subscriber breakdown |
| 🔍 Comparison | Dual-axis UPI vs Penetration chart, scatter plot, correlation heatmap, insight cards |
| 🤖 ML Models | Actual vs Predicted chart, feature importance bar chart, metrics table |
| 💡 Conclusion | Final verdict, key findings, policy recommendation |

---

## 🔑 Key Findings

- **UPI Volume Growth:** ~4,689× from 2016-17 to 2022-23
- **Internet Penetration Growth:** ~6% over the same period (nearly flat)
- **Correlation (Volume vs Penetration):** −0.143 — weak and negative
- **Conclusion:** Internet penetration is a *necessary enabler* but explains only a small portion of UPI's rise. Time trend, Demonetization (2016), smartphone affordability, QR merchant adoption, and COVID-19 contactless shift are the dominant drivers.

---

## 🛠️ Requirements

```bash
pip install pandas numpy matplotlib seaborn scikit-learn panel plotly
```

Key library versions used:
- `panel == 1.8.10`
- `bokeh == 3.9.0`
- `plotly == 3.1.0`
- `scikit-learn` (any recent version)
- `Python 3.14`

---

## ▶️ How to Run

1. Install dependencies (see above)
2. Open the notebook in JupyterLab or Jupyter Notebook
3. Run all cells in order (Cells 1 → 12)
4. The dashboard will open automatically in your browser at `http://localhost:5007`

To serve as a standalone app:
```bash
panel serve UpiInternetProject_11.ipynb --port 5007 --show
```

---

## 📝 Commit History (Bug Fixes)

| Commit | File | Fix |
|---|---|---|
| Commit 2 | v4 | Added missing `Volume_Growth_Pct` and `Value_Per_Txn` pre-computation cell |
| Commit 4 | v9 | Defined `corr_val` before downstream use to prevent `NameError` |
| Commit 5 | v10 | Fixed wrong `CELL 4` comment header in Cell 5; removed triple redefinition of `Volume_Growth_Pct` and `Value_Per_Txn` |
| Commit 6 | v11 | Removed duplicate LOO CV R² print in ML cell; fixed `pen_growth` ratio→percentage; fixed `fig_feat_imp` label mismatch (7 labels → 4); renamed `vol_growth` to `vol_growth_ratio` in dashboard to eliminate naming collision |

---

## 📄 Data Sources

| Dataset | Source | Description |
|---|---|---|
| UPI Yearly | NPCI — Rajya Sabha Session 260 | Annual UPI transaction volume & value (FY 2016-17 to 2022-23) |
| UPI Monthly | NPCI / RBI | Monthly P2P and P2M breakdown (41 records) |
| Internet Subscribers | TRAI — Table 3.1 | State-wise wireline/wireless subscribers by year |

---

*Built as a data science portfolio project demonstrating data cleaning, integration, EDA, machine learning, and interactive dashboard development on a real-world policy-relevant question.*
