# Strategy Allocation Review

Research analyst notebook evaluating whether a live trading strategy meets the allocation mandate of **MasterFOF**, a fund of funds considering an allocation to a strategy with roughly ten years of live performance history.

The analysis compares the strategy against two benchmarks:

- **S&P 500**
- **HFRI Macro CTA Index**

It also includes an additional monthly factor-attribution extension using AQR trend and carry factors plus a VIX-based short-volatility proxy.

## Project objective

The core question is whether the strategy is suitable for allocation under the MasterFOF mandate.

The allocation mandate asks whether the strategy has:

1. Positively skewed return characteristics.
2. Low average correlation with the S&P 500.
3. Low beta and high alpha relative to the S&P 500.
4. Moderately high or high correlation, and high alpha, relative to the CTA benchmark.
5. An annualised Sharpe ratio above 0.50.
6. Annualised volatility between 15% and 25%.
7. Stable volatility consistent with good risk management.

The notebook assumes:

- 256 business days per year.
- Zero risk-free rate.
- Daily returns for the main performance analysis.
- Monthly returns for the HFRI benchmark and factor-attribution extension.

## Repository structure

```text
.
├── strategy_analysis.ipynb
├── strategy_analysis.pdf
├── Strategy returns.xlsx
├── SP 500 returns.xls
├── hfri_index.xlsx
├── Century of Factor Premia Monthly.xlsx
├── VIX_History.csv
├── exhibit1_cumulative.png
├── exhibit2_rolling_vol.png
├── exhibit3_hist.png
├── exhibit4_drawdown.png
├── exhibit5_regime_metrics.png
├── exhibit6_sp_scatter.png
├── exhibit7_hfri_scatter.png
├── exhibit8_factor_loadings.png
└── README.md
```

Some data files may not be included in the public repository depending on licensing or size constraints. If they are excluded, place them in the project root using the filenames shown above before running the notebook. Because apparently computers cannot infer that `SP500_final_FINAL_v3.xls` is spiritually the same file. Tragic.

## Main files

| File | Description |
|---|---|
| `strategy_analysis.ipynb` | Main research notebook containing the full allocation review. |
| `strategy_analysis.pdf` | PDF export of the notebook and results. |
| `Strategy returns.xlsx` | Daily strategy return data. |
| `SP 500 returns.xls` | Daily S&P 500 return data. |
| `hfri_index.xlsx` | HFRI Macro CTA benchmark index data. |
| `Century of Factor Premia Monthly.xlsx` | AQR factor workbook used for monthly trend and carry factors. |
| `VIX_History.csv` | VIX history used to construct the short-volatility proxy. |

## Analysis workflow

The notebook performs the following steps:

1. Loads strategy, S&P 500, and HFRI benchmark data.
2. Cleans dates and converts return/index fields into numerical format.
3. Merges strategy returns with S&P 500 daily returns.
4. Converts strategy and S&P 500 returns into monthly returns for comparison with HFRI.
5. Calculates distributional statistics, including skewness, hit rate, average gain/loss, and zero-return days.
6. Estimates correlation with the S&P 500.
7. Runs regression analysis against the S&P 500 to estimate beta, alpha, R-squared, and statistical significance.
8. Compares the strategy with the HFRI Macro CTA benchmark using monthly correlation and regression.
9. Calculates annualised Sharpe ratio.
10. Calculates annualised volatility and rolling 256-day volatility.
11. Tests regime performance across pre-Covid, pandemic, inflation-shock, and recent periods.
12. Evaluates tail risk using drawdown, VaR, expected shortfall, skewness, and kurtosis.
13. Runs an additional four-factor monthly attribution model.

## Key results

### Return distribution

The strategy has a positive average daily return, but its return distribution is not positively skewed.

| Metric | Value |
|---|---:|
| Mean daily return | 0.0005 |
| Median daily return | 0.0000 |
| Median daily return, non-zero days | 0.0009 |
| Skew daily return | -0.3282 |
| Hit rate, all days | 0.4882 |
| Hit rate, non-zero days | 0.5395 |
| Average positive day | 0.0095 |
| Average negative day | -0.0101 |
| Ratio of average gain to average loss | 0.9493 |
| Zero-return days | 283 |

### S&P 500 relationship

The strategy has near-zero average correlation and beta relative to the S&P 500.

| Metric | Value |
|---|---:|
| Observations | 2,976 |
| Correlation with S&P 500 | -0.0174 |
| Correlation p-value | 0.3416 |
| Beta vs S&P 500 | -0.0227 |
| Alpha vs S&P 500, daily | 0.0005 |
| Alpha vs S&P 500, annualised | 1.4326 |
| R-squared | 0.0003 |
| p-value for alpha | 0.0466 |
| p-value for beta | 0.3416 |

### HFRI Macro CTA relationship

The strategy has a meaningful positive monthly relationship with HFRI Macro CTA, but the alpha relative to HFRI is not statistically significant in the reported regression.

| Metric | Value |
|---|---:|
| Observations | 140 |
| Correlation with HFRI | 0.5716 |
| Correlation p-value | 0.0000 |
| Beta vs HFRI | 1.8794 |
| Alpha vs HFRI, monthly | 0.0034 |
| Alpha vs HFRI, annualised | 0.0408 |
| R-squared | 0.3267 |
| p-value for alpha | 0.4034 |
| p-value for beta | 0.0000 |

### Sharpe ratio and volatility

The strategy meets the headline Sharpe and annualised volatility requirements.

| Metric | Value |
|---|---:|
| Annualised Sharpe ratio | 0.5717 |
| Sharpe t-stat | 1.9487 |
| Sharpe p-value, one-tailed | 0.0257 |
| Annualised volatility | 0.2109 |
| Max drawdown | -0.2756 |

The annualised volatility is within the 15% to 25% mandate range on a full-sample basis. However, the rolling volatility analysis shows that the strategy only spends around **44.4%** of rolling observations inside the mandate range, which weakens the case for stable volatility management.

### Tail risk

The strategy shows material downside tail risk.

| Metric | Value |
|---|---:|
| Skewness | -0.3282 |
| Excess kurtosis | 4.4232 |
| Worst daily return | -0.0931 |
| Max drawdown | -0.2756 |
| Historical VaR, 5% | -0.0214 |
| Historical Expected Shortfall, 5% | -0.0324 |
| Historical VaR, 1% | -0.0382 |
| Historical Expected Shortfall, 1% | -0.0505 |

## Factor-attribution extension

The notebook includes an additional four-factor attribution model using monthly data.

The model is:

```text
Strategy return = α 
                + β1 × S&P 500 return
                + β2 × Trend factor
                + β3 × Carry factor
                + β4 × VIX change
                + ε
```

Where:

- **S&P 500** proxies broad equity market exposure.
- **Trend factor** uses AQR All Macro Momentum.
- **Carry factor** uses AQR All Macro Carry.
- **VIX change** proxies short-volatility exposure.

The factor-attribution sample runs from **April 2014 to December 2024**, ending there because the AQR factor workbook used in the notebook ends at that point.

### Four-factor headline results

| Variable | Coefficient | p-value |
|---|---:|---:|
| Alpha, monthly | 0.0158 | 0.0040 |
| S&P 500 return | -0.3446 | 0.0749 |
| Trend factor | 1.3136 | 0.0068 |
| Carry factor | 1.7651 | 0.0113 |
| VIX change | -0.0433 | 0.0539 |

The four-factor model has an R-squared of **0.1755**. Trend and carry exposures are economically large and statistically significant, while the VIX proxy is negative and borderline significant.

## Exhibits

The notebook generates the following charts:

| Exhibit | Output file | Description |
|---|---|---|
| Exhibit 1 | `exhibit1_cumulative.png` | Monthly compounded cumulative performance versus S&P 500 and HFRI Macro CTA. |
| Exhibit 2 | `exhibit2_rolling_vol.png` | Rolling 256-day annualised volatility against the 15% to 25% mandate band. |
| Exhibit 3 | `exhibit3_hist.png` | Histogram of daily strategy returns. |
| Exhibit 4 | `exhibit4_drawdown.png` | Strategy drawdown profile. |
| Exhibit 5 | `exhibit5_regime_metrics.png` | Regime-level return, volatility, and Sharpe comparison. |
| Exhibit 6 | `exhibit6_sp_scatter.png` | Strategy daily returns versus S&P 500 daily returns. |
| Exhibit 7 | `exhibit7_hfri_scatter.png` | Strategy monthly returns versus HFRI Macro CTA monthly returns. |
| Exhibit 8 | `exhibit8_factor_loadings.png` | Four-factor regression coefficients with 95% confidence intervals. |

## Installation

Clone the repository:

```bash
git clone <your-repository-url>
cd <your-repository-name>
```

Create a virtual environment:

```bash
python -m venv .venv
```

Activate it.

On Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

On macOS/Linux:

```bash
source .venv/bin/activate
```

Install dependencies:

```bash
pip install numpy pandas scipy statsmodels matplotlib openpyxl xlrd jupyter
```

The notebook reads both `.xlsx` and legacy `.xls` files, so `openpyxl` and `xlrd` are both included. Yes, Excel formats multiply like gremlins after midnight.

## Running the notebook

Start Jupyter:

```bash
jupyter notebook
```

Open:

```text
strategy_analysis.ipynb
```

Make sure the required data files are in the project root with the exact filenames used in the notebook:

```text
Strategy returns.xlsx
SP 500 returns.xls
hfri_index.xlsx
Century of Factor Premia Monthly.xlsx
VIX_History.csv
```

Then run all cells.

## Interpretation

The strategy has several attractive allocation features:

- Low correlation with the S&P 500.
- Near-zero S&P 500 beta.
- Positive and statistically significant S&P 500 alpha in the daily regression.
- Sharpe ratio above the mandate threshold.
- Full-sample annualised volatility inside the 15% to 25% range.
- Meaningful relationship with the HFRI Macro CTA benchmark.

However, it also has important weaknesses:

- Daily skewness is negative, not positive.
- Tail risk is material, with a max drawdown around -27.6%.
- Rolling volatility is not consistently inside the mandate band.
- HFRI-relative alpha is positive but not statistically significant.
- Recent-period performance is much weaker than earlier periods.
- The factor model suggests returns are meaningfully exposed to trend and carry premia, so the strategy may not be pure idiosyncratic alpha.

## Suggested allocation view

A reasonable conclusion is that the strategy is **interesting but not an automatic full allocation**.

It appears to offer diversification versus equities and has macro/CTA-like characteristics, but it does not fully satisfy the positive-skew and stable-volatility requirements. A cautious allocation process would involve:

- Further due diligence on drawdown drivers.
- Confirmation of live track record integrity.
- Review of capacity, liquidity, and implementation costs.
- Stress testing under crisis scenarios.
- Monitoring recent performance decay.
- Understanding whether factor exposures are intended, persistent, and compensated.

## Limitations

This project is a research notebook rather than a full institutional due-diligence report. Limitations include:

- No transaction cost or fee adjustment unless already embedded in the strategy return series.
- No separate gross-versus-net return analysis.
- HFRI comparison is monthly, while S&P 500 analysis is daily.
- Factor attribution uses monthly data and ends in December 2024 due to AQR factor availability.
- VIX change is only a proxy for short-volatility exposure.
- Results may be sensitive to benchmark choice, sample period, and data quality.

## Author

Lewis Hikari Kawase Kennedy

MSc Finance and Machine Learning  
Queen Mary University of London
