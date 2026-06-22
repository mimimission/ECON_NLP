# economic-news-labor-rag

**Do labor-market news signals improve real-time forecasts of US payroll growth beyond macroeconomic variables, and can retrieved news evidence explain each forecast?**

## What this project does

This project predicts **next-month US nonfarm payroll employment growth** (`PAYEMS` month-on-month change at `t+1`) using a combination of macroeconomic indicators and labor-market news signals from GDELT.

A Retrieval-Augmented Generation (RAG) layer then retrieves contemporaneous news evidence for each forecast and generates plain-language explanations using the DeepSeek API.

## Research question

> Do labor-market news signals improve real-time forecasts of US payroll growth beyond macroeconomic variables, and can retrieved news evidence explain each forecast?

## How to run

**All work happens in Google Colab.** Run notebooks in order:

| Notebook | Purpose |
|----------|---------|
| `00_setup_colab.ipynb` | Mount Drive, install packages, create folder structure |
| `01_collect_raw_fred_alfred.ipynb` | Download FRED current + ALFRED vintage macro data |
| `02_collect_raw_gdelt.ipynb` | Collect GDELT labor-market news metadata |
| `03_audit_macro_vintage.ipynb` | Inspect and approve macro/vintage data |
| `04_audit_and_clean_news.ipynb` | Inspect and clean GDELT news |
| `05_build_news_features.ipynb` | Build monthly news count features |
| `06_build_macro_features_and_target.ipynb` | Build macro lag features and forecast target |
| `07_validate_model_ready_dataset.ipynb` | Merge, validate, gate before training |
| `08_train_baseline_models.ipynb` | Naive + ARIMA baselines |
| `09_train_xgboost_models.ipynb` | Macro-only, news-only, macro+news XGBoost |
| `10_model_audit_and_error_analysis.ipynb` | Inspect model trustworthiness |
| `11_build_rag_evidence_retrieval.ipynb` | Retrieve news evidence per forecast |
| `12_generate_deepseek_explanations.ipynb` | Generate explanations via DeepSeek API |
| `13_evaluate_rag_explanations.ipynb` | Manual evaluation template and scoring |
| `14_create_paper_tables_and_figures.ipynb` | Final paper-ready outputs |
| `15_final_reproducibility_check.ipynb` | Audit all artifacts before writing paper |

### Step 0: Open `00_setup_colab.ipynb` in Colab

```python
from google.colab import drive
drive.mount("/content/drive")
```

```bash
!git clone https://github.com/USER/economic-news-labor-rag.git
%cd economic-news-labor-rag
!pip install -r requirements.txt
```

## Data storage

**Google Drive** stores all data and artifacts at:

```
/content/drive/MyDrive/labor_news_rag_project/
```

**GitHub** stores only:
- Notebooks (`.ipynb`)
- Configs (`configs/`)
- Requirements (`requirements.txt`)
- Reports (`reports/`)
- This README

## API keys required

Set these in Colab before running:

```python
import os
os.environ["FRED_API_KEY"] = "your_fred_api_key"      # Required
os.environ["DEEPSEEK_API_KEY"] = "your_deepseek_key"  # Optional; uses template fallback if missing
```

Get a free FRED API key at: https://fred.stlouisfed.org/docs/api/api_key.html

## Target variable

```
target_payems_next = PAYEMS(t+1) - PAYEMS(t)
```

Forecasted one month ahead using only information available at the forecast date (real-time, no look-ahead bias).

## Main model comparison

| Model | Features |
|-------|---------|
| Naive baseline | Last observed change |
| ARIMA | PAYEMS time series only |
| XGBoost (macro only) | 8 macro lag features |
| XGBoost (news only) | 7 news count features |
| XGBoost (macro + news) | All 15 features |

## Human approval gates

Every notebook requires manual inspection and explicit approval before saving artifacts:

```python
APPROVE_THIS_STEP = False  # Change to True after inspection

if not APPROVE_THIS_STEP:
    raise RuntimeError("STOP: Inspect diagnostics above. Set APPROVE_THIS_STEP=True and rerun.")
```

## Project structure

```
economic-news-labor-rag/
  README.md
  .gitignore
  requirements.txt
  configs/          ← YAML configs for data, models, evidence
  notebooks/        ← All 16 Colab notebooks (the main product)
  reports/          ← Literature review, methods, paper outline
```

## Citation

If you use this project, please cite the data sources:
- **FRED/ALFRED**: Federal Reserve Bank of St. Louis
- **GDELT**: The GDELT Project (https://www.gdeltproject.org)
- **DeepSeek**: DeepSeek AI
