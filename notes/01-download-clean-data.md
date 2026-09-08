
# Downloading and Cleaning Yahoo Finance Data with `yfinance`

This guide explains the process of downloading a small financial dataset using `yfinance`, understanding the options inside `yf.download()`, and performing some basic cleanup before using the data for research.

The goal is to understand each step rather than simply copy code.

---

# 1. Import the Libraries

```python
import pandas as pd
import yfinance as yf
```

We are using two libraries:

- `pandas` → used for working with tabular data using DataFrames.
- `yfinance` → used for downloading market data from Yahoo Finance.

We import pandas as:

```python
pd
```

so instead of writing:

```python
pandas.Timedelta()
```

we can write:

```python
pd.Timedelta()
```

---

# 2. Choose the Ticker

```python
TICKER = "QQQ"
```

This creates a variable called:

```text
TICKER
```

and stores:

```text
QQQ
```

inside it.

`QQQ` is the ticker symbol for the Invesco QQQ ETF.

Instead of writing:

```python
yf.download("QQQ")
```

throughout the notebook, we can use:

```python
yf.download(TICKER)
```

This makes changing the instrument later much easier.

For example:

```python
TICKER = "SPY"
```

or:

```python
TICKER = "AAPL"
```

---

# 3. Download the Data

```python
df = yf.download(
    TICKER,
    period="5d",
    interval="1m",
    auto_adjust=True,
    prepost=False,
    multi_level_index=False,
    progress=False,
)
```

This asks Yahoo Finance to download historical data and store the result inside:

```python
df
```

`df` stands for:

```text
DataFrame
```

A DataFrame is essentially a table of data.

---

# Understanding `yf.download()`

Each argument inside `yf.download()` controls how the data is downloaded.

---

## `TICKER`

```python
TICKER
```

This tells Yahoo Finance:

> Which instrument do I want data for?

In this example:

```python
TICKER = "QQQ"
```

so we are downloading QQQ data.

---

## `period="5d"`

```python
period="5d"
```

This tells Yahoo Finance:

> How far back should I download data?

Here:

```text
5d = 5 days
```

Examples include:

```python
period="1d"
period="5d"
period="1mo"
period="3mo"
period="1y"
```

For learning, downloading a small period first is useful because the dataset remains easy to inspect.

---

# `interval="1m"`

```python
interval="1m"
```

This controls the size of each candle or bar.

Here:

```text
1m = 1 minute
```

Therefore:

```python
period="5d"
interval="1m"
```

approximately means:

> Download five days of data with one row for each one-minute candle.

The DataFrame will therefore contain timestamps such as:

```text
09:30
09:31
09:32
09:33
09:34
```

Other intervals include:

```python
interval="5m"
interval="15m"
interval="1h"
interval="1d"
```

Yahoo places restrictions on how much intraday historical data can be downloaded, so longer periods are not always available for very small intervals.

---

# `auto_adjust=True`

```python
auto_adjust=True
```

This tells `yfinance` to adjust historical prices for corporate actions such as:

- stock splits
- dividends

This is particularly useful when analysing longer periods of stock market data.

---

## Example: Stock Split

Imagine a stock is trading around:

```text
$100
```

and experiences a:

```text
2-for-1 stock split
```

After the split, the price may become approximately:

```text
$50
```

Without adjustment, historical data could appear like:

```text
100
101
102
51
52
53
```

This could incorrectly look like the stock suddenly crashed by around 50%.

Adjusted data attempts to remove this artificial jump.

It may instead look approximately like:

```text
50
50.5
51
51
52
53
```

This makes calculations such as:

- returns
- moving averages
- momentum
- indicators

more meaningful.

---

## Do We Need `auto_adjust=True` for 5 Days of QQQ?

For only a few days of minute data, it probably will not make much difference.

However, it is useful to keep because later we may work with much larger historical datasets.

It also makes our research data closer in concept to adjusted equity data used in QuantConnect.

---

# `prepost=False`

```python
prepost=False
```

This controls whether extended trading hours are included.

US equities can trade during:

```text
Pre-market
    ↓
Regular market
    ↓
After-hours
```

With:

```python
prepost=False
```

we are saying:

> Only give me the regular trading session.

With:

```python
prepost=True
```

we would say:

> Include pre-market and after-hours trading too.

For learning and initial research:

```python
prepost=False
```

keeps the dataset simpler.

Later, this can be changed if a strategy specifically requires extended-hours data.

---

# `multi_level_index=False`

```python
multi_level_index=False
```

This controls how the DataFrame columns are created.

`yfinance` can download multiple instruments simultaneously.

For example:

```python
["QQQ", "SPY"]
```

When working with several tickers, it may create a more complicated column structure called a:

```text
MultiIndex
```

Conceptually, it might look something like:

```text
        QQQ                 SPY
       Open Close          Open Close
```

Because we are only working with one ticker:

```text
QQQ
```

we do not need this complexity.

Using:

```python
multi_level_index=False
```

gives us simple columns such as:

```text
Open
High
Low
Close
Volume
```

This is much easier to work with while learning pandas.

---

# `progress=False`

```python
progress=False
```

This controls whether `yfinance` displays a download progress bar.

Without it, you might see something like:

```text
[*********************100%*********************]
```

Using:

```python
progress=False
```

simply hides the progress display.

It does not change the downloaded market data.

---

# The Whole Download in Plain English

This:

```python
df = yf.download(
    TICKER,
    period="5d",
    interval="1m",
    auto_adjust=True,
    prepost=False,
    multi_level_index=False,
    progress=False,
)
```

can be read as:

> Download QQQ data for approximately five days, using one-minute candles, adjust the historical prices, only include the regular trading session, use simple DataFrame columns, and do not display the download progress bar.

---

# 4. Inspect the Data

After downloading the data:

```python
df.head()
```

displays the first five rows.

For example:

```text
Datetime                 Close    High    Low    Open    Volume
2026-08-31 09:30         ...
2026-08-31 09:31         ...
2026-08-31 09:32         ...
```

`head()` is useful because it allows us to quickly check what the DataFrame looks like.

---

# Understanding the DataFrame Index

The timestamps down the left-hand side are not ordinary columns.

They are the:

```text
DataFrame index
```

Conceptually:

```text
INDEX                     COLUMNS
↓                         ↓

Datetime                  Open High Low Close Volume
09:30                     ...
09:31                     ...
09:32                     ...
```

This is why later we use:

```python
df.index
```

`df.index` means:

> Work with the timestamps stored down the left-hand side of the DataFrame.

---

# 5. Make the Column Names Lowercase

```python
df.columns = df.columns.str.lower()
```

Yahoo may return:

```text
Open
High
Low
Close
Volume
```

This changes them to:

```text
open
high
low
close
volume
```

---

## Breaking the Code Down

```python
df.columns
```

means:

> Access the DataFrame's column names.

Then:

```python
.str
```

means:

> Treat those column names like strings.

Then:

```python
.lower()
```

means:

> Convert the strings to lowercase.

Therefore:

```python
df.columns = df.columns.str.lower()
```

means:

> Take all of my column names and replace them with lowercase versions.

---

# 6. Select and Order the Columns

```python
df = df[["open", "high", "low", "close", "volume"]]
```

This performs two useful jobs.

It selects only:

```text
open
high
low
close
volume
```

and also forces them into that order.

For example, Yahoo might originally return:

```text
close
high
low
open
volume
```

We change it to:

```text
open
high
low
close
volume
```

This creates a consistent structure for our research data.

---

# 7. Convert the Timestamps to UTC

Yahoo returns QQQ timestamps using New York market time.

For example:

```text
2026-08-31 09:30:00-04:00
```

The:

```text
-04:00
```

means that timezone is four hours behind UTC.

We can convert the timestamps using:

```python
df.index = df.index.tz_convert("UTC")
```

---

## Before

```text
09:30 -04:00
```

## After

```text
13:30 +00:00
```

These represent the same moment.

We have only changed the timezone used to describe it.

---

# Why Store Financial Data in UTC?

UTC gives us one standard timezone.

This becomes particularly useful when working with:

- different exchanges
- different countries
- Forex
- US equities
- UK markets
- daylight-saving changes
- QuantConnect

For example, UK time changes during the year.

During British Summer Time:

```text
UK = UTC + 1
```

During GMT:

```text
UK = UTC
```

Storing the underlying dataset in UTC avoids unnecessary confusion.

---

# 8. Understanding `Timedelta`

Next we create:

```python
bar_length = pd.Timedelta(minutes=1)
```

A `Timedelta` represents an:

```text
amount of time
```

For example:

```text
1 minute
5 minutes
2 hours
3 days
```

---

# Timestamp vs Timedelta

A:

```text
Timestamp
```

represents a specific point in time.

Example:

```text
13:30 on 31 August 2026
```

A:

```text
Timedelta
```

represents a duration.

Example:

```text
1 minute
```

---

## Normal Number Example

```python
10 + 1
```

becomes:

```text
11
```

With timestamps:

```text
13:30 + 1 minute
```

becomes:

```text
13:31
```

So:

```python
pd.Timedelta(minutes=1)
```

simply creates a value representing one minute.

---

# 9. Why Add One Minute to the Timestamp?

Yahoo labels a one-minute candle using approximately its:

```text
START time
```

For example:

```text
09:30
```

represents the candle covering:

```text
09:30 → 09:31
```

Conceptually:

```text
             ONE-MINUTE CANDLE

        ┌───────────────────────┐
        │                       │
      09:30                   09:31
        ↑                       ↑
    Start Time               End Time
```

QuantConnect/LEAN often works with the bar's end time when historical data is represented inside a DataFrame.

To make our Yahoo data easier to compare with LEAN, we move the timestamp forward by one minute.

First:

```python
bar_length = pd.Timedelta(minutes=1)
```

Then:

```python
df.index = df.index + bar_length
```

---

# Example

Yahoo originally gives:

```text
09:30
```

After converting to UTC:

```text
13:30
```

Then we add one minute:

```text
13:31
```

So the complete change is:

```text
09:30 New York time
        ↓
13:30 UTC
        ↓
Add one minute
        ↓
13:31 UTC
```

The market data itself has not changed.

We are changing how the candle is labelled.

---

# 10. Rename the Index

```python
df.index.name = "time"
```

Originally the index might be called:

```text
Datetime
```

We rename it:

```text
time
```

So instead of:

```text
Datetime
2026-08-31 ...
```

we get:

```text
time
2026-08-31 ...
```

This does not change the timestamps.

It only changes the name of the index.

---

# 11. Sort the Data by Time

```python
df = df.sort_index()
```

Because our index contains the timestamps, this means:

> Sort the rows using their timestamps.

For example:

```text
13:33
13:31
13:34
13:32
```

would become:

```text
13:31
13:32
13:33
13:34
```

For historical market data, we normally want:

```text
oldest
↓
newest
```

---

# 12. A Simpler Version of the Cleaning Code

For now, it is easier to keep the process simple and readable.

```python
# Make the column names lowercase.
df.columns = df.columns.str.lower()

# Select the OHLCV columns and put them in a consistent order.
df = df[["open", "high", "low", "close", "volume"]]

# Convert the timestamps to UTC.
df.index = df.index.tz_convert("UTC")

# A one-minute candle lasts one minute.
bar_length = pd.Timedelta(minutes=1)

# Move Yahoo's start-time label to the candle's end time.
df.index = df.index + bar_length

# Rename the datetime index.
df.index.name = "time"

# Sort from oldest to newest.
df = df.sort_index()

df.head()
```

---

# Why Use a `bar_length` Variable?

Instead of immediately writing:

```python
df.index = df.index + pd.Timedelta(minutes=1)
```

we can write:

```python
bar_length = pd.Timedelta(minutes=1)

df.index = df.index + bar_length
```

This is slightly longer, but easier to understand.

We can read it as:

> Create a bar length of one minute.

Then:

> Add that bar length to every timestamp.

Readable code is more useful while learning than trying to make everything as short as possible.

---

# 13. Extra Cleanup We Will Add Later

The original version also contained some additional cleanup.

For example:

```python
df = df.loc[~df.index.duplicated(keep="last")]
```

This removes duplicate timestamps.

It is useful, but introduces several pandas concepts at once:

```text
.loc
~
.index
.duplicated()
keep="last"
```

We do not need to learn all of these immediately.

---

## Why Remove Duplicates Later?

Imagine we download two datasets.

### File 1

```text
Monday
Tuesday
Wednesday
Thursday
Friday
```

### File 2

```text
Friday
Monday
Tuesday
```

When we join them together, Friday may appear twice.

We eventually want:

```text
Friday
```

to appear only once.

That is where duplicate-removal code becomes useful.

---

# 14. Removing an Unfinished Candle

The original code also included something like:

```python
df = df.loc[
    df.index <= pd.Timestamp.now(tz="UTC").floor("min")
]
```

This is intended to stop us keeping a candle that may not have finished forming yet.

For example, if the current time is:

```text
13:30:25
```

the:

```text
13:30 → 13:31
```

candle has not finished.

This type of safety check is useful later, but it introduces several new pandas ideas at once:

```text
Timestamp
now()
tz
floor()
.loc
<=
```

For now, we can leave this out and add it once the simpler pipeline makes sense.

---

# 15. The Workflow So Far

The process can be thought of like this:

```text
Yahoo Finance
     │
     │ yf.download()
     ▼
Raw DataFrame
     │
     ├── lowercase column names
     ├── select OHLCV
     │
     ▼
Clean Price Data
     │
     ├── convert timestamps to UTC
     ├── start time → end time
     ├── rename index
     ├── sort by time
     │
     ▼
Research-Ready DataFrame
```

Later we will extend this to:

```text
Research-Ready DataFrame
     │
     ├── remove duplicate timestamps
     ├── remove unfinished candles
     │
     ▼
Clean Dataset
     │
     ├── save to CSV
     ▼
Saved File
```

Then:

```text
Saved CSV
     │
     ├── pd.read_csv()
     ▼
Load Dataset Again
     │
     ▼
Continue Research
```

---

# 16. Current Recommended Notebook

For now, the notebook can stay relatively small.

## Imports

```python
import pandas as pd
import yfinance as yf
```

---

## Download the Data

```python
TICKER = "QQQ"

df = yf.download(
    TICKER,
    period="5d",
    interval="1m",
    auto_adjust=True,
    prepost=False,
    multi_level_index=False,
    progress=False,
)

df.head()
```

---

## Clean the Columns

```python
# Make the column names lowercase.
df.columns = df.columns.str.lower()

# Keep OHLCV and use a consistent order.
df = df[["open", "high", "low", "close", "volume"]]

df.head()
```

---

## Clean the Time Index

```python
# Convert timestamps to UTC.
df.index = df.index.tz_convert("UTC")

# Define the length of one candle.
bar_length = pd.Timedelta(minutes=1)

# Change Yahoo's start-time label to an end-time label.
df.index = df.index + bar_length

# Rename the index.
df.index.name = "time"

# Sort chronologically.
df = df.sort_index()

df.head()
```

---

# 17. What I Should Understand Before Moving On

Before adding more complicated cleanup code, I should understand the following concepts:

- What a DataFrame is.
- What the DataFrame index is.
- What `df.columns` refers to.
- What `.str.lower()` does.
- How to select DataFrame columns.
- What UTC is.
- What `.tz_convert("UTC")` does.
- The difference between a `Timestamp` and a `Timedelta`.
- Why one minute is being added to Yahoo's timestamp.
- What `.sort_index()` does.

I do **not** need to understand every advanced pandas command immediately.

The aim is to gradually build the data pipeline while understanding why each step exists.

---

# Next Step

The next stage is learning how to save the cleaned DataFrame.

This will introduce:

```python
from pathlib import Path
```

and:

```python
df.to_csv()
```

The workflow will then become:

```text
Download
    ↓
Clean
    ↓
Save
    ↓
Close Python
    ↓
Load the file later
    ↓
Continue researching
```

This is the foundation for building a reusable local financial-data workflow.