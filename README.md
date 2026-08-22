# Tesla & GameStop — Visualizing Stock Data

![Python](https://img.shields.io/badge/Python-3.8%2B-blue?logo=python&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange?logo=jupyter&logoColor=white)
![pandas](https://img.shields.io/badge/pandas-data%20analysis-150458?logo=pandas&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)

A data-analysis project that extracts historical **share price** and **quarterly revenue** data for **Tesla (TSLA)** and **GameStop (GME)**, then visualizes the two side by side to compare how each company's market price tracks — or fails to track — its actual business performance.

Built as the final project for the **IBM Python Project for Data Science** course.

---

## Table of Contents

- [Overview](#overview)
- [Data Sources](#data-sources)
- [Project Workflow](#project-workflow)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Project Structure](#project-structure)
- [Results](#results)
- [Key Learnings](#key-learnings)
- [Acknowledgements](#acknowledgements)
- [License](#license)

---

## Overview

Share price alone tells you what the market *believes* a company is worth. Revenue tells you what it actually earns. This project puts both on the same timeline for two very different companies:

- **Tesla** — a high-growth company whose price and revenue rise together.
- **GameStop** — a declining retailer whose share price spiked dramatically in early 2021 while revenue kept falling.

The result is a two-panel dashboard per company: historical share price on top, historical quarterly revenue below.

---

## Data Sources

| Data | Source | Method |
| --- | --- | --- |
| TSLA / GME share price history | Yahoo Finance | `yfinance` API |
| Tesla quarterly revenue | IBM Skills Network hosted HTML page | Web scraping with BeautifulSoup |
| GameStop quarterly revenue | IBM Skills Network hosted HTML page | Web scraping with BeautifulSoup |

Revenue is reported in **millions of US dollars**.

---

## Project Workflow

**1. Extract stock data**
Create a `Ticker` object for each symbol and pull the complete price history:

```python
tesla = yf.Ticker("TSLA")
tesla_data = tesla.history(period="max")
tesla_data.reset_index(inplace=True)
```

**2. Scrape revenue data**
Download the revenue page with `requests`, parse it with `BeautifulSoup`, and walk the quarterly revenue table row by row into a DataFrame with `Date` and `Revenue` columns.

```python
html_data = requests.get(url).text
soup = BeautifulSoup(html_data, "html.parser")
```

**3. Clean the data**
Strip `$` and `,` characters from the revenue values, drop null and empty rows, and cast the column to a numeric type so it can be plotted.

```python
tesla_revenue["Revenue"] = tesla_revenue["Revenue"].str.replace(r",|\$", "", regex=True)
tesla_revenue.dropna(inplace=True)
tesla_revenue = tesla_revenue[tesla_revenue["Revenue"] != ""]
```

**4. Visualize**
A reusable `make_graph()` function renders a shared-axis Matplotlib figure — price above, revenue below — for any stock/revenue DataFrame pair:

```python
make_graph(tesla_data, tesla_revenue, 'Tesla')
make_graph(gme_data, gme_revenue, 'GameStop')
```

---

## Tech Stack

| Purpose | Library |
| --- | --- |
| Stock market data | `yfinance` |
| HTTP requests | `requests` |
| HTML parsing / web scraping | `beautifulsoup4` |
| Data manipulation | `pandas` |
| Visualization | `matplotlib` |

---

## Getting Started

**Prerequisites:** Python 3.8+ and Jupyter Notebook.

```bash
# 1. Clone the repository
git clone https://github.com/Dalu-Alex/Tesla-Visualizing-Stock-Data.git
cd Tesla-Visualizing-Stock-Data

# 2. Install dependencies
pip install yfinance beautifulsoup4 requests pandas matplotlib

# 3. Launch the notebook
jupyter notebook
```

Open the notebook and run **Kernel → Restart & Run All** so every cell executes in order.

> **Note:** the cells depend on each other — running one out of order will raise a `NameError` because variables like `soup` are defined in earlier cells.

---

## Project Structure

```
Tesla-Visualizing-Stock-Data/
├── Final_Assignment.ipynb    # Main notebook — data extraction, cleaning, and plots
├── images/                   # Exported graph screenshots
└── README.md
```

---

## Results

**Tesla** — revenue grows from roughly \$21M per quarter in early 2010 to well over \$10B, and the share price rises alongside it. Price growth is backed by real business growth.

**GameStop** — revenue trends flat to downward across the decade, yet the share price explodes in early 2021. The gap between the two panels is the entire story of the short squeeze, visible at a glance.

Plotting price against revenue makes that divergence obvious in a way neither series alone would.

---

## Key Learnings

- Retrieving financial time-series data from a public API
- Web scraping structured HTML tables with BeautifulSoup
- Cleaning messy string data (currency symbols, separators, empty rows) into numeric columns
- Combining data from multiple sources into a single analysis
- Building multi-panel visualizations to compare related metrics

---

## Acknowledgements

Course materials and the hosted revenue datasets are provided by **IBM Skills Network** as part of the *Python Project for Data Science* course on Coursera.

---

## License

Released under the MIT License. See [`LICENSE`](LICENSE) for details.
