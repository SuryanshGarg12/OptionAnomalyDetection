# Options Anomaly Detection

This repository contains a Python script for detecting anomalies in option chain time-series data. It computes various anomaly metrics (IV spikes, mispricings, price jumps, spread widenings, and gamma spikes), logs results to CSV files, and generates diagnostic plots.

## Repository Structure

```bash
.
├── ad1.parquet              # Input options data in Parquet format
├── sample_plots/            # Generated plots directory
│   └── <INSTRUMENT>.png     # Plot per instrument showing anomalies
├── anomaly_results.csv      # Detailed list of detected anomalies
├── anomaly_summary.csv      # Count of anomalies per instrument and type
├── anomaly_type_summary.csv # Count of anomalies per anomaly type
├── notebook.py              # Main Python script
└── README.md                # This documentation file
```

## Prerequisites

* Python **3.8+**
* Required Python packages:

  ```bash
  pip install pandas numpy scipy matplotlib
  ```

## Data Description

### Instrument Naming Convention

The instrument name follows this pattern: `X_EXPIRY_STRIKE_TYPE`

For example, in `X_25123_21800_CE`:
- `X` represents the underlying asset
- `25123` represents the expiry date (23-Jan-2025)
- `21800` represents the strike price in Rupees
- `CE` indicates a Call option (PE would indicate a Put option)

### Available Data Fields

For each option, the following 14 data fields are available:

| Term           | Meaning                                          |
| -------------- | ------------------------------------------------ |
| best_bid       | Highest price a buyer is willing to pay          |
| best_offer     | Lowest price a seller is willing to accept       |
| ltp            | Last traded price                                |
| traded_volume  | Number of contracts traded                       |
| bid_iv         | IV calculated from best_bid                      |
| ask_iv         | IV calculated from best_offer                    |
| iv             | IV from last traded price                        |
| iv_ema60       | EMA of IV over 60 time units                     |
| iv7            | Short-term IV metric (e.g., 7-period)            |
| iv7_ema60      | EMA of iv7 over 60 time units                    |
| delta          | Sensitivity to price changes of underlying asset |
| gamma          | Sensitivity of delta to price changes            |
| theta          | Time decay of option price                       |
| vega           | Sensitivity to changes in implied volatility     |

## Output Files

After execution, the following files and folders will be created:

| File/Folder | Description |
|-------------|-------------|
| `anomaly_results.csv` | Detailed table of all detected anomalies with columns:<br>- `timestamp`: Datetime of anomaly<br>- `instrument`: Option ticker (e.g., `X_25123_21800_CE`)<br>- `anomaly_type`: One of `IV spike`, `Mispricing`, `Price jump`, `Spread widening`, `Gamma spike`<br>- `value`: Z-score or percentage jump |
| `anomaly_summary.csv` | Aggregated counts of anomalies per instrument and type, sorted by count. |
| `anomaly_type_summary.csv` | Overall counts of each anomaly type across all instruments. |
| `anomaly_plots/<INST>.png` | For each instrument with anomalies, a five-panel plot showing:<br>1. **IV** with red markers for IV spikes<br>2. **Mid vs. BS theoretical price** highlighting mispricings<br>3. **Price jump %** with green markers<br>4. **Spread %** with blue markers<br>5. **Gamma** with magenta markers |

## Anomaly Types Explained

The script detects five types of anomalies:

1. **IV Spike**: Abnormal jumps in implied volatility that may indicate significant changes in market sentiment or expectation.
   
2. **Mispricing**: Substantial deviations between the market mid-price and the theoretical price calculated using Black-Scholes model, potentially indicating arbitrage opportunities.

3. **Price Jump**: Sudden, significant changes in option prices that exceed normal market movements.

4. **Spread Widening**: Unusual expansion of the bid-ask spread, often indicating decreased liquidity or increased uncertainty.

5. **Gamma Spike**: Abnormal increases in gamma, which measures the rate of change in delta with respect to the underlying price, potentially indicating heightened sensitivity to price movements.
