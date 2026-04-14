# Primetrade.ai – Data Science Intern Assignment
## Trader Performance vs Market Sentiment (Fear/Greed)

---

## Setup

```bash
pip install pandas numpy matplotlib seaborn scikit-learn
```

Place the two CSV files in the same directory as the notebook:
- `historical_data_-_historical_data.csv`
- `fear_greed_index_-_fear_greed_index.csv`

Run:
```bash
jupyter notebook primetrade_analysis.ipynb

```

All charts are saved to a `charts/` folder automatically.

---

## Datasets

| Dataset | Rows | Columns | Date Range |
|---------|------|---------|------------|
| Hyperliquid Trades | 211,224 | 16 | May 2023 – May 2025 |
| Fear/Greed Index | 2,644 | 4 | Feb 2018 – May 2025 |

**Trades columns:** Account, Coin, Execution Price, Size Tokens, Size USD, Side, Timestamp IST, Start Position, Direction, Closed PnL, Transaction Hash, Order ID, Crossed, Fee, Trade ID, Timestamp

**Fear/Greed columns:** timestamp, value (0–100), classification (Extreme Fear / Fear / Neutral / Greed / Extreme Greed), date

---

## Methodology

### Data Preparation
- Parsed `Timestamp IST` (format `DD-MM-YYYY HH:MM`) to extract trade dates.
- Aligned both datasets on `date` via a left merge.
- Created a `broad_sentiment` column collapsing 5 classes into 3 (Fear / Neutral / Greed) for cleaner group comparisons.
- Derived per-account daily aggregates: PnL, trade count, win rate, long ratio, average size USD.

### Analysis Approach
- **B1 (Performance):** Grouped daily account metrics by `classification`; compared avg PnL, median PnL, win rate, and a drawdown proxy (worst single-day PnL per account per sentiment bucket).
- **B2 (Behaviour):** Analysed trade frequency, volume, and long/short bias by sentiment.
- **B3 (Segmentation):** Three orthogonal cuts — Frequent vs Infrequent (median split on avg daily trades), Net Winner vs Net Loser (total PnL sign), Large vs Small Trades (median split on avg trade size USD).
- **Bonus model:** Random Forest predicting next-day PnL bucket (Big Loss / Small Loss / Small Win / Big Win) from same-day sentiment + behaviour features. Achieves ~59% accuracy on held-out 20%.
- **Bonus clustering:** KMeans (k=4) on standardised account-level features to derive behavioural archetypes.

---

## Key Insights

### Insight 1 – Fear Days Drive the Most Trading Activity
During Extreme Fear and Fear days, traders execute **33–73% more trades** and deploy **60–200% more volume** than on Greed or Extreme Greed days. The long ratio on Fear days (~52%) stays mildly bullish — consistent with "buy the dip" behaviour. This elevated activity coincides with **higher average PnL** ($5,329 on Fear vs $3,318 on Greed days), suggesting experienced traders exploit panic-driven mispricings.

### Insight 2 – Worst Drawdowns Occur on Greed Days
The drawdown proxy (average worst single-day PnL) is **−$26,728 on Greed days** vs **−$16,653 on Fear days**. Traders appear overconfident during bullish sentiment, taking on larger positions that reverse sharply. This counterintuitive result highlights that euphoria carries more tail risk than panic for this cohort.

### Insight 3 – Frequent Traders Outperform on Fear; Infrequent Traders Outperform on Extreme Greed
Frequent traders earn **$8,673 avg PnL on Fear days** vs **$2,250 for infrequent** — a 4× gap. On Extreme Greed, the pattern reverses: infrequent traders ($5,800) edge out frequent ones ($4,340). This suggests frequent traders have edge in volatile/fearful markets but over-trade in calm euphoric conditions.

---

## Strategy Recommendations

### Strategy 1 – "Fear = Opportunity for Active Traders"
**Rule:** When the Fear/Greed index reads Fear or Extreme Fear:
- Frequent traders should **maintain or increase trade frequency**.
- Keep a **mild long bias** (long ratio ~50–52%).
- Cap individual position size to control the −$16K tail risk.
- *Evidence:* Frequent traders earned 4× more PnL on Fear days; long ratio above 50% on all Fear days.

### Strategy 2 – "Greed = Trim Size, Trade Selectively"
**Rule:** When the index reads Greed or Extreme Greed:
- **Reduce position size by ≥30%** relative to Fear-day sizing.
- Switch to **selective, lower-frequency trading** (infrequent-style).
- Shift long/short ratio closer to neutral (47–50%).
- *Evidence:* Greed days had the largest average drawdown (−$26K); infrequent traders outperform frequent ones on Extreme Greed days; volume naturally drops ~47% vs Fear days suggesting the market itself is thinner.

---

## File Structure

```
.
├── primetrade_analysis.ipynb  
├── README.md                  
└── charts/
    ├── chart1_performance_by_sentiment.png
    ├── chart2_behaviour_by_sentiment.png
    ├── chart3_pnl_distribution.png
    ├── chart4_freq_segments.png
    ├── chart5_winners_losers.png
    ├── chart6_volume_timeline.png
    ├── chart7_coin_concentration.png
    ├── bonus_feature_importance.png
    └── bonus_kmeans_clusters.png
```
