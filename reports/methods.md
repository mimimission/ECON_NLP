# Methods

## 1. Data

### 1.1 Macroeconomic indicators

We download eight monthly time series from FRED (Federal Reserve Economic Data) and their real-time vintages from ALFRED (Archival Federal Reserve Economic Data):

| Series | Description | Role |
|--------|-------------|------|
| PAYEMS | Total Nonfarm Payrolls | Target (MoM change) |
| UNRATE | Civilian Unemployment Rate | Feature |
| ICSA | Initial Jobless Claims (weekly, aggregated) | Feature |
| JTSJOL | JOLTS Job Openings | Feature |
| CES0500000003 | Average Hourly Earnings | Feature |
| CPIAUCSL | Consumer Price Index | Feature |
| FEDFUNDS | Federal Funds Rate | Feature |
| INDPRO | Industrial Production Index | Feature |

**Real-time constraint**: We use ALFRED vintage data to ensure that, at each forecast date `t`, we use only the value of each series that was *publicly available* at time `t`, not the subsequently revised value. This prevents look-ahead bias from data revisions.

### 1.2 Labor-market news (GDELT)

We collect news article metadata (title, URL, date, domain, source) from the GDELT Document 2.0 API for six query groups:

1. **Layoffs**: articles about job cuts, mass layoffs, redundancies
2. **Hiring**: articles about job growth, strong hiring, labor demand
3. **Unemployment**: articles about unemployment claims, jobless rate
4. **Job openings**: articles about JOLTS, vacancies, help wanted
5. **Wages**: articles about wage growth, hourly earnings
6. **Uncertainty**: articles about economic uncertainty, recession fears

We collect article metadata only — titles, URLs, dates, domains. We do not scrape full article text. All collection uses public GDELT APIs without authentication.

### 1.3 Sample period

- Full sample: January 2000 — December 2024
- Training: January 2000 — December 2018
- Validation: January 2019 — December 2021 (includes COVID)
- Test: January 2022 — December 2024

## 2. Target variable

```
payems_mom_change_t = PAYEMS_t - PAYEMS_{t-1}
target_payems_next  = payems_mom_change_{t+1}
```

The target is the **one-month-ahead change in nonfarm payrolls** (in thousands of persons). A positive value means employment grew; a negative value means employment shrank.

**Anti-leakage rule**: at each row, `forecast_date = t` and `target_month = t+1`. The model only sees information available at time `t`.

## 3. Features

### 3.1 Macro lag features

All macro features use a **one-month lag** relative to the forecast date, reflecting the typical publication schedule:

```python
payems_lag1 = PAYEMS_{t-1}   # i.e., the just-released value
payems_lag2 = PAYEMS_{t-2}
unrate_lag1 = UNRATE_{t-1}
icsa_lag1   = ICSA_mean_{t-1}
jtsjol_lag1 = JTSJOL_{t-1}   # note: JOLTS has ~2 month lag in practice
cpi_lag1    = CPIAUCSL_{t-1}
fedfunds_lag1 = FEDFUNDS_{t-1}
indpro_lag1 = INDPRO_{t-1}
```

### 3.2 News count features

For each month `t`, we count the number of articles collected in query group `g` with `article_date` in month `t`:

```python
news_volume                = total articles in month t
layoff_news_count          = articles in "layoffs" group
hiring_news_count          = articles in "hiring" group
unemployment_news_count    = articles in "unemployment" group
job_openings_news_count    = articles in "job_openings" group
wage_news_count            = articles in "wages" group
uncertainty_news_count     = articles in "uncertainty" group
```

These are simple counts. We do not compute sentiment scores or topic models.

**Anti-leakage rule**: News features for forecast date `t` use only articles where `article_date` is in month `t` or earlier. The news is known at forecast time.

## 4. Models

### 4.1 Naive baseline

Predicts next month's change = last observed MoM change:

```
ŷ_{t+1} = Δpayems_t
```

### 4.2 ARIMA

ARIMA(2,0,2) on the MoM change series, estimated on the training set and recursively refitted for each test observation.

### 4.3 XGBoost models

Three XGBoost regressors:

1. **Macro-only**: trained on 8 macro lag features
2. **News-only**: trained on 7 news count features
3. **Macro + news**: trained on all 15 features

Hyperparameters selected by visual inspection of validation-set performance (small sample size makes cross-validation unstable for time series).

## 5. Evaluation

All models evaluated on the held-out test set (2022–2024):

```
MAE  = (1/n) Σ |ŷ_t - y_t|
RMSE = sqrt((1/n) Σ (ŷ_t - y_t)²)
DA   = (1/n) Σ 1[sign(ŷ_t) == sign(y_t)]   # directional accuracy
```

**Honest reporting rule**: If macro+news does not beat macro-only on the test set, we do not claim news improves forecasting. We report the result as-is.

## 6. Retrieval-Augmented Generation (RAG)

### 6.1 Purpose

RAG does **not** predict payroll growth. It retrieves news evidence that existed at the time of each forecast and uses it to explain the forecast direction in plain language.

### 6.2 Evidence retrieval

For each forecast at time `t`:
1. Filter all collected news to `article_date <= t` (strict no-future rule)
2. Prefer articles from months `t-1` and `t`
3. Rank by recency and query group relevance
4. Select top 10 articles

### 6.3 Explanation generation

Retrieved evidence is passed to the DeepSeek API (`deepseek-reasoner`, temperature=0) with a prompt that instructs the model to:

- Explain whether evidence supports the forecast direction
- Cite specific article titles and URLs
- Acknowledge if evidence is weak
- Not invent facts beyond the retrieved evidence

If the DeepSeek API key is unavailable, a deterministic template explanation is used instead.

### 6.4 Explanation evaluation

A manual evaluation template is created with fields:
- `correct_time_window_0_1`: no future articles cited
- `labor_relevance_0_1`: articles are actually about labor markets
- `citation_support_0_1`: articles support the claims made
- `faithfulness_1_5`: no invented facts
- `usefulness_1_5`: explanation helps interpret the forecast

## 7. Reproducibility

Every data transformation requires explicit human approval before the artifact is saved. The approval mechanism is a boolean variable in each notebook that must be set to `True` by the user after visual inspection.

All artifacts are stored in Google Drive. All code is stored in GitHub. Notebooks are the single source of truth for all data transformations.
