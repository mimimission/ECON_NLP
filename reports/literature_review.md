# Literature Review

## Economic forecasting with text data

### Core motivation

The thesis that news media reflects and anticipates economic conditions draws on a long tradition in macroeconomics. Keynes (1936) observed that investor psychology — captured in part by media sentiment — shapes aggregate demand. The empirical challenge is operationalizing this observation at scale.

### Nowcasting and real-time data

**Giannone, Reichlin & Small (2008)** — "Nowcasting: The real-time informational content of macroeconomic data." *Journal of Monetary Economics*. Establishes the dynamic factor model framework for nowcasting with mixed-frequency data. Key insight: information arrives continuously and models should update accordingly.

**Bańbura, Giannone, Modugno & Reichlin (2013)** — "Now-Casting and the Real-Time Data Flow." *Handbook of Economic Forecasting*. Comprehensive survey of real-time forecasting methods. Documents the importance of using vintages rather than final revised data.

**Diebold & Mariano (1995)** — "Comparing Predictive Accuracy." *Journal of Business & Economic Statistics*. Standard test for forecast comparison. We use MAE/RMSE comparison rather than formal DM tests given our sample size.

### Text data and economic forecasting

**Bholat, Hansen, Santos & Schonhardt-Bailey (2015)** — "Text mining for central banks." Bank of England Staff Working Paper. Reviews NLP approaches applied to central bank communications.

**Tetlock (2007)** — "Giving Content to Investor Sentiment: The Role of Media in the Stock Market." *Journal of Finance*. Demonstrates that pessimistic words in Wall Street Journal columns predict market downturns. One of the first papers to quantify media sentiment's predictive power.

**Baker, Bloom & Davis (2016)** — "Measuring Economic Policy Uncertainty." *Quarterly Journal of Economics*. Constructs EPU index from newspaper count of terms related to economic uncertainty. Directly relevant to our `uncertainty_news_count` feature.

**Bybee, Kelly, Manela & Xiu (2021)** — "The Structure of Economic News." NBER Working Paper. Applies topic models to WSJ articles to extract economic themes. Shows that news-derived topics contain predictive information about macro variables.

**Kalamara, Turrell, Redl, Kapetanios & Kapetanios (2022)** — "Making text count: Economic forecasting using newspaper text." *Journal of Applied Econometrics*. Direct precedent for our approach. Uses newspaper text to forecast UK GDP. Key finding: simple word counts often compete with sophisticated NLP approaches for macro forecasting.

### GDELT as a data source

**Leetaru & Schrodt (2013)** — "GDELT: Global data on events, location, and tone." Introduces GDELT. Comprehensive coverage of global news media from 1979 to present.

**Bollen, Mao & Zeng (2011)** — "Twitter mood predicts the stock market." *Journal of Computational Science*. Early demonstration that high-frequency sentiment from media predicts financial outcomes.

### Employment forecasting specifically

**Loungani (2001)** — "How accurate are private sector forecasts? Cross-country evidence from consensus forecasts of output growth." *International Journal of Forecasting*. Documents systematic optimism bias in employment forecasts — relevant context for our evaluation.

**Modugno, Pistelli & Soybilgen (2020)** — "Nowcasting Turkish GDP with FRED-QD." Demonstrates the value of high-frequency indicators in real-time employment forecasting.

**Boehm & Smets (2024)** — Related work on using alternative data for nowcasting labor markets.

### Retrieval-Augmented Generation (RAG)

**Lewis, Perez, Piktus et al. (2020)** — "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks." *NeurIPS*. Foundational RAG paper. Our approach adapts RAG for economic explanation rather than knowledge retrieval: we use it to surface contemporaneous evidence that contextualizes an existing forecast.

**Guu, Lee, Tung & Chang (2020)** — "REALM: Retrieval-Augmented Language Model Pre-Training." Companion architecture to RAG.

### LLM-generated economic explanations

**Lopez-Lira & Tang (2023)** — "Can ChatGPT Forecast Stock Price Movements? Return Predictability and Large Language Models." SSRN. Tests whether LLM-generated summaries of news contain predictive power. Finding is mixed, which motivates our approach of using LLMs for *explanation* rather than prediction.

**Korinek (2023)** — "Language Models and Cognitive Automation for Economic Research." NBER Working Paper. Reviews LLM applications in economics research, including explanation generation.

## Gaps this project addresses

1. **Real-time constraint**: Most text-forecasting papers use data as-of now, not as-of the forecast date. We use ALFRED vintages and strict time filters.

2. **Transparency**: RAG makes evidence visible, allowing a human to verify whether the explanation is grounded.

3. **Honest comparison**: We compare macro-only vs. macro+news explicitly and report results even when news does not help.

4. **Explainability**: Rather than treating the model as a black box, the RAG layer produces auditable evidence chains.

## Open questions

- Do simple news count features outperform sentiment scores?
- Does news provide information orthogonal to macro indicators, or does it just correlate with ICSA/UNRATE?
- Are COVID-period observations helpful or harmful for the model?
- Does the DeepSeek explanation faithfully reflect the retrieved evidence?
