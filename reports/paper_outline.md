# Paper Outline

**Working title**: "Labor-Market News and Payroll Forecasting: A Real-Time RAG Approach"

---

## Abstract (draft)

We examine whether labor-market news signals from GDELT improve real-time forecasts of US nonfarm payroll employment growth beyond standard macroeconomic indicators. Using ALFRED vintage data to enforce strict no-look-ahead constraints, we train XGBoost models with macro-only, news-only, and combined feature sets. We compare against naive and ARIMA baselines using MAE, RMSE, and directional accuracy on a held-out 2022–2024 test set. For each forecast, a Retrieval-Augmented Generation layer retrieves contemporaneous news evidence and generates plain-language explanations via the DeepSeek API, enabling human verification of the evidence chain. We evaluate explanation quality on faithfulness, citation accuracy, and usefulness dimensions.

---

## 1. Introduction

- The payroll forecast is the most watched monthly economic release
- Forecasters use macro indicators but rarely systematically incorporate news media
- Real-time constraint: must use only data available at forecast date
- Three contributions:
  1. Systematic news feature construction from GDELT
  2. Strict real-time (ALFRED vintage) evaluation
  3. RAG-based explanation of each forecast

## 2. Related Literature

### 2.1 Nowcasting and real-time forecasting
- Giannone et al. (2008)
- Bańbura et al. (2013)

### 2.2 Text data in macro forecasting
- Tetlock (2007) — media sentiment and markets
- Baker, Bloom & Davis (2016) — EPU index
- Bybee et al. (2021) — news topics and macro
- Kalamara et al. (2022) — newspaper text for GDP forecasting

### 2.3 RAG and LLM explanations
- Lewis et al. (2020) — foundational RAG
- Lopez-Lira & Tang (2023) — LLMs and market prediction

## 3. Data

### 3.1 Macroeconomic indicators (FRED/ALFRED)
- Table: series descriptions
- Figure: PAYEMS level and MoM change
- Figure: UNRATE, ICSA, JTSJOL over time

### 3.2 Labor-market news (GDELT)
- Table: query groups and article counts by group
- Figure: monthly news volume by query group
- Discussion: coverage quality, limitations

### 3.3 Sample construction
- Timeline figure: train/validation/test splits

## 4. Methods

### 4.1 Target variable construction
- Equation: target_payems_next
- Anti-leakage verification

### 4.2 Features
- Table: feature list with descriptions

### 4.3 Models
- Naive baseline
- ARIMA
- XGBoost (macro-only, news-only, macro+news)

### 4.4 RAG evidence retrieval
- Algorithm: time-filtered retrieval
- DeepSeek explanation generation

## 5. Results

### 5.1 Forecast accuracy comparison
- **Table 1**: Model comparison (MAE, RMSE, DA) — all models vs. test set
- Finding: [filled in after training]

### 5.2 Does news help?
- Compare macro-only vs. macro+news
- If yes: characterize which news features contribute most (feature importance)
- If no: document this honestly; RAG explanation analysis remains valuable

### 5.3 Error analysis
- Figure: actual vs. predicted
- Figure: errors over time
- Table: worst 10 forecast errors
- COVID period: crisis vs. non-crisis performance

### 5.4 Feature importance
- Figure: XGBoost feature importance for macro+news model

## 6. RAG Explanation Analysis

### 6.1 Evidence retrieval examples
- Table: example forecasts with top retrieved articles

### 6.2 Explanation quality evaluation
- Table: manual evaluation scores (faithfulness, usefulness, citation support)
- Examples of good vs. poor explanations

### 6.3 Case studies
- 2020-03 (COVID collapse): did news signal the shock?
- 2022-01 (post-COVID recovery): did news signal the surge?
- A typical miss: largest forecast error + retrieved evidence

## 7. Discussion

### 7.1 Real-time data matters
- Comparison: using revised vs. vintage PAYEMS

### 7.2 Limitations
- GDELT coverage biased toward English-language, US-hosted sources
- Simple count features may miss sentiment and intensity
- Small test sample (n≈36 months)
- DeepSeek explanations not independently verified beyond manual sample

### 7.3 Extensions
- Sentiment scoring on titles (e.g., FinBERT)
- Full-text scraping for richer features
- Dynamic factor models incorporating news
- Larger LLM evaluation

## 8. Conclusion

- Summary of findings
- Whether news improves forecasts (honest answer)
- RAG provides auditable evidence chain
- Template for transparent, human-in-the-loop economic forecasting

## Appendices

### A. Data collection details
- GDELT API parameters
- FRED series list

### B. Assertion checks and data quality report

### C. Robustness checks
- Expanding window vs. rolling window
- COVID exclusion

### D. Explanation evaluation rubric

---

## Timeline

| Milestone | Target |
|-----------|--------|
| Data collection complete | NB 01–02 approved |
| Model ready dataset approved | NB 07 approved |
| Main results | NB 08–10 complete |
| RAG results | NB 11–13 complete |
| Paper draft | NB 14 complete |
| Reproducibility verified | NB 15 complete |
