# Investor Attention and Lead–Lag Networks in U.S. Equities

**Author:** Seif Zogheibi
**Context:** Individual capstone project, Data Structures and Algorithms, MSc Financial Technology, Imperial College London

## Overview

This project tests whether investor attention can improve trading strategies built on lead–lag relationships between stocks in the same industry. The idea is that news affecting an industry is reflected first in heavily followed stocks and only later in less-followed ones, so a large move in a high-attention stock might predict next-day moves in lower-attention stocks.

Two strategies are compared on 50 large U.S. stocks across five industries (Banks, Technology, Consumer, Healthcare, Energy), using daily Bloomberg data from January 2020 to September 2026:

- **Price-only network:** keeps the strongest historical one-day lagged correlations within each industry, rebuilt every 20 trading days from the previous 252 days.
- **Attention-informed network:** keeps the strongest relationships where the leading stock has more Bloomberg news coverage than the follower.

The strategies are developed on 2020–2023 and evaluated out of sample on January 2024 – September 2026, with all parameters fixed in advance.

## Key findings

- **Attention did not reliably improve the network.** The attention-informed strategy had a lower Sharpe ratio than the price-only strategy in both the development period (−0.194 vs −0.165) and the out-of-sample period (0.815 vs 0.848). It came out ahead in only 4 of 9 parameter combinations, and a block bootstrap showed the difference between the two is not statistically significant.
- **Gross profits did not survive trading costs.** Out of sample, both strategies were profitable before costs (+29.4% and +24.0% cumulative) but lost money after 10 bps per trade, because the portfolio turned over about 240 times a year. Break-even cost was roughly 4 bps per side.
- **The profits came from a different mechanism.** Supplementary tests showed most out-of-sample gains came from reversal relationships and industry-wide moves, not the delayed same-direction responses the attention hypothesis predicts.

The full methodology, results and discussion are in `report.pdf`.

## Methods included

- Data pipeline from a raw Bloomberg Excel export: parsing, holiday removal, missing-data handling, split and dividend checks
- Rolling directed lead–lag networks within industries
- Signal generation from return z-score shocks, with position sizing and an exposure cap
- Development and out-of-sample backtests (cumulative return, Sharpe ratio, maximum drawdown)
- 100-run industry-matched random-network benchmark
- Transaction-cost sensitivity (5, 10, 20 bps) and break-even cost
- Parameter robustness grid and attention-window sensitivity
- Moving-block bootstrap significance tests
- Next-open execution test
- Risk and concentration analysis
- Mechanism tests (continuation-only and industry-adjusted networks)
- Network caching, with runtime comparison

## Repository contents

```
├── notebook.ipynb   Full pipeline, strategies and backtests (with saved outputs)
├── report.pdf       Written report
└── README.md
```

## Data

The Bloomberg data are **not included**, because Bloomberg's terms do not permit raw Terminal data to be redistributed. The notebook's saved outputs show every result without re-running it.

To reproduce the analysis, export the following daily fields for the 50 tickers listed in Section 2 of the notebook, from 1 January 2020 to 30 September 2026, using the Bloomberg Excel Add-in:

- `data/bloomberg_data.xlsx`, sheet `Final_Data`: `PX_LAST`, `NEWS_PUBLICATION_COUNT`, `PX_VOLUME`, `TOT_RETURN_INDEX_GROSS_DVDS`
- `data/open_prices.xlsx`, sheet `Open_Prices`: `PX_OPEN`

## Running the notebook

With the data files in a `data/` folder next to the notebook:

```bash
python3 -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
pip install pandas numpy matplotlib openpyxl jupyter
jupyter notebook notebook.ipynb
```

Then run all cells. All random elements use fixed seeds, so results reproduce exactly. Only the runtime measurements vary between computers.
