# Market Data Workflow

A Python project for building a reusable local financial market data workflow for quantitative research.

The project focuses on downloading, cleaning, saving, loading, and updating historical market data before using it in research and backtesting environments such as QuantConnect LEAN.

## Current Focus

The workflow is currently being developed around:

```text
Download
   ↓
Clean
   ↓
Save
   ↓
Load
   ↓
Update
   ↓
Research
```

The current data source is Yahoo Finance using `yfinance`, with pandas used for cleaning and processing the data.

## Repository Structure

```text
market-data-workflow/
│
├── notebooks/
│   └── 01-download-clean-yahoo-data.ipynb
│
├── notes/
│   └── 01-download-clean-data.md
│
├── README.md
└── requirements.txt
```

- `notebooks/` — practical Python implementations
- `notes/` — detailed explanations and learning notes
- `requirements.txt` — Python dependencies

## Current Progress

- [x] Download Yahoo Finance data
- [x] Clean and organise OHLCV columns
- [x] Work with datetime indexes and UTC
- [x] Align minute-bar timestamps with bar end times
- [ ] Save cleaned datasets locally
- [ ] Reload saved datasets
- [ ] Update existing datasets
- [ ] Remove duplicates and combine data
- [ ] Build a reusable local research workflow
- [ ] Integrate with QuantConnect LEAN

## Setup

Clone the repository:

```bash
git clone <repository-url>
```

Move into the project:

```bash
cd market-data-workflow
```

Create a virtual environment:

```bash
python -m venv .venv
```

Activate it in Git Bash on Windows:

```bash
source .venv/Scripts/activate
```

Install the dependencies:

```bash
pip install -r requirements.txt
```

## Technologies

- Python
- pandas
- yfinance
- Jupyter Notebook
- VS Code
- Git / GitHub
- QuantConnect LEAN

## Status

Work in progress.

The current stage is:

```text
Download → Clean → Save → Load
```