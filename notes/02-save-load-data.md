# 02 - Saving and Loading Market Data

This stage builds on the cleaned market data created in Stage 1.

The goal is to understand how to move data between:

```text
Python memory
     ↕
Saved files on the computer
```

The main workflow is:

```text
Clean DataFrame
      ↓
Choose file location
      ↓
Save to CSV
      ↓
CSV exists on computer
      ↓
Load CSV
      ↓
Restore datetime index
      ↓
DataFrame ready for research again
```

---

# 1. DataFrame Memory vs Saved Files

When market data is downloaded using `yfinance`, it is stored inside a Python object such as:

```python
df
```

For example:

```python
df = yf.download(...)
```

At this point, the data exists in the currently running Python session.

It has **not** automatically been saved as a separate dataset on the computer.

If the Python session is closed or restarted, the variable:

```python
df
```

will no longer exist.

Saving the Jupyter notebook saves:

- the notebook cells
- the code
- usually the displayed outputs

but it does not turn `df` into a reusable market-data file.

To keep the actual rows of market data, they need to be saved separately.

For this project, we begin by using:

```text
CSV
```

---

# 2. What is a CSV File?

CSV stands for:

```text
Comma-Separated Values
```

It is a simple text-based format for storing tabular data.

A CSV containing OHLCV data might look roughly like:

```text
time,open,high,low,close,volume
2026-08-31 13:31:00+00:00,715.11,716.11,714.67,716.11,1290381
2026-08-31 13:32:00+00:00,716.13,717.00,716.10,716.79,123098
```

Each line represents one row.

The values are separated by commas.

CSV files are useful because they are:

- simple
- widely supported
- human-readable
- easy for pandas to save
- easy for pandas to load again

---

# 3. The `data/` Folder

The project now contains a folder for saved market data:

```text
market-data-workflow/
│
├── data/
│
├── notebooks/
│   ├── 01-download-clean-yahoo-data.ipynb
│   └── 02-save-load-data.ipynb
│
├── notes/
│   ├── 01-download-clean-data.md
│   └── 02-save-load-data.md
│
├── README.md
└── requirements.txt
```

The `data/` folder is separate from the notebooks because the notebooks contain code while the data folder contains the datasets produced by that code.

For example:

```text
data/
└── qqq_1m.csv
```

---

# 4. Importing `Path`

Stage 2 introduces:

```python
from pathlib import Path
```

`pathlib` is part of Python's standard library.

It does not need to be installed separately.

`Path` is used to work with:

- folders
- filenames
- file locations
- paths on the computer

For example:

```python
data_folder = Path("../data")
```

creates a `Path` object representing a folder location.

---

# 5. What is a File Path?

A file path tells the computer where a file or folder is located.

For example:

```text
market-data-workflow/
└── data/
    └── qqq_1m.csv
```

The path to the file could be represented as:

```text
data/qqq_1m.csv
```

or, depending on the current working directory:

```text
../data/qqq_1m.csv
```

---

# 6. Relative Paths

A relative path describes a location relative to the folder Python is currently working from.

For example:

```text
..
```

means:

> Go up one folder.

Therefore:

```python
Path("../data")
```

means:

> Go up one directory and then enter the `data` folder.

If Python is currently working from:

```text
market-data-workflow/notebooks/
```

then:

```text
../data
```

means:

```text
market-data-workflow/data/
```

---

## Checking the Current Working Directory

The current working directory can be checked using:

```python
Path.cwd()
```

`cwd` stands for:

```text
Current Working Directory
```

For example:

```python
print(Path.cwd())
```

may produce something like:

```text
C:\...\market-data-workflow\notebooks
```

The correct relative path depends on this location.

If the current working directory is the project root:

```text
market-data-workflow/
```

then the data folder would normally be:

```python
Path("data")
```

If the working directory is:

```text
market-data-workflow/notebooks/
```

then it would normally be:

```python
Path("../data")
```

This is important because relative paths are interpreted from the **working directory**, not simply from where the notebook file visually appears in VS Code.

---

# 7. Create a Path to the Data Folder

In the notebook we created a variable for the data folder:

```python
data_folder = Path("../data")
```

This variable now represents:

```text
../data
```

Instead of repeatedly typing the folder location, it can be stored once and reused.

For example:

```python
print(data_folder)
```

might display:

```text
../data
```

---

# 8. Create the File Path

Next we created the complete path to the CSV:

```python
file_path = data_folder / "qqq_1m.csv"
```

This combines:

```text
../data
```

with:

```text
qqq_1m.csv
```

to produce:

```text
../data/qqq_1m.csv
```

---

## Why is `/` Used?

Normally `/` looks like division:

```python
10 / 2
```

However, when working with `Path` objects, Python overloads `/` so it can be used to join paths together.

Therefore:

```python
data_folder / "qqq_1m.csv"
```

means:

> Add `qqq_1m.csv` to the end of the data-folder path.

It does not mean division in this context.

This is much cleaner than manually joining strings.

---

# 9. Inspect the Path

The path can be inspected before saving:

```python
print(data_folder)
print(file_path)
```

For example:

```text
../data
../data/qqq_1m.csv
```

This is useful when debugging because it confirms where Python intends to save the file.

---

# 10. Saving the DataFrame with `to_csv()`

Once the DataFrame has been cleaned, it can be saved using:

```python
df.to_csv(file_path)
```

Breaking this down:

```python
df
```

is the DataFrame.

```python
.to_csv()
```

is a pandas DataFrame method that writes the DataFrame to a CSV file.

```python
file_path
```

tells pandas where to save it.

Therefore:

```python
df.to_csv(file_path)
```

can be read as:

> Save the DataFrame `df` as a CSV at `file_path`.

After running this code, a real file should exist on the computer:

```text
data/
└── qqq_1m.csv
```

---

# 11. The Index is Saved Too

Our cleaned DataFrame uses time as its index:

```text
time
2026-08-31 13:31:00+00:00
2026-08-31 13:32:00+00:00
...
```

Because we previously named the index:

```python
df.index.name = "time"
```

the CSV contains a field called:

```text
time
```

This becomes important when loading the file again.

---

# 12. Checking Whether the File Exists

A `Path` object has an:

```python
.exists()
```

method.

For example:

```python
file_path.exists()
```

returns:

```python
True
```

if the file exists.

It returns:

```python
False
```

if it does not.

This can be read literally as:

> Does this file path exist?

For example:

```python
print(file_path.exists())
```

---

## Why Will `.exists()` Become Useful?

Later the program may need to decide:

```text
Does a saved dataset already exist?
        │
   ┌────┴────┐
   │         │
  YES        NO
   │         │
 Load      Download
 existing    new
 data        data
```

This will become important when building an automatic update workflow.

---

# 13. Loading a CSV

The opposite of:

```python
df.to_csv()
```

is:

```python
pd.read_csv()
```

For example:

```python
loaded_df = pd.read_csv(file_path)
```

This means:

> Read the CSV at `file_path` and create a new pandas DataFrame.

The new DataFrame is stored inside:

```python
loaded_df
```

---

# 14. First Loading the CSV Without Extra Instructions

We first deliberately loaded the CSV simply:

```python
loaded_df = pd.read_csv(file_path)

loaded_df.head()
```

This allows us to see something important.

The original DataFrame may have looked like:

```text
time                              open      high ...
2026-08-31 13:31:00+00:00         ...
2026-08-31 13:32:00+00:00         ...
```

But after a basic `read_csv()`, it may look more like:

```text
   time                              open      high ...
0  2026-08-31 13:31:00+00:00        ...
1  2026-08-31 13:32:00+00:00        ...
2  ...
```

Notice two differences.

The DataFrame has received a new default index:

```text
0
1
2
3
...
```

and:

```text
time
```

has become a normal column.

---

# 15. Why Does This Happen?

A CSV file is essentially text.

When pandas reads the file, it does not automatically know:

> `time` used to be the special DataFrame index.

It also does not necessarily know:

> These strings are supposed to represent datetime values.

We therefore need to give pandas more information when loading the CSV.

---

# 16. Loading the CSV Properly

We used:

```python
loaded_df = pd.read_csv(
    file_path,
    parse_dates=["time"],
    index_col="time",
)
```

This introduces two important arguments:

```python
parse_dates
```

and:

```python
index_col
```

---

# 17. Understanding `parse_dates`

```python
parse_dates=["time"]
```

tells pandas:

> Interpret the values in the `time` column as datetime values.

Without this instruction, pandas may treat the values as ordinary strings.

For example:

```text
"2026-08-31 13:31:00+00:00"
```

looks like a timestamp to us, but to Python it can still simply be text unless it is parsed.

Using:

```python
parse_dates=["time"]
```

converts those values back into proper datetime objects.

---

# 18. Understanding `index_col`

```python
index_col="time"
```

tells pandas:

> Use the `time` column as the DataFrame index.

Without it, pandas creates a default integer index:

```text
0
1
2
3
```

and keeps `time` as an ordinary column.

With:

```python
index_col="time"
```

the DataFrame returns to:

```text
time                              open      high ...
2026-08-31 13:31:00+00:00         ...
2026-08-31 13:32:00+00:00         ...
```

This recreates the structure we had before saving.

---

# 19. The Complete Loading Code

The preferred loading code at this stage is:

```python
loaded_df = pd.read_csv(
    file_path,
    parse_dates=["time"],
    index_col="time",
)
```

Then inspect it using:

```python
loaded_df.head()
```

---

# 20. Using `.info()`

A useful way to inspect a DataFrame is:

```python
loaded_df.info()
```

`.info()` displays information such as:

- number of rows
- number of columns
- column names
- data types
- missing values
- memory usage
- index information

This helps confirm that pandas interpreted the CSV correctly.

---

# 21. Using `.shape`

The shape of a DataFrame can be checked using:

```python
df.shape
```

For example:

```text
(1949, 5)
```

means:

```text
1949 rows
5 columns
```

The structure is:

```text
(rows, columns)
```

Therefore:

```python
df.shape
```

and:

```python
loaded_df.shape
```

should normally match.

For example:

```python
print("Original shape:", df.shape)
print("Loaded shape:", loaded_df.shape)
```

---

# 22. Checking the First and Last Timestamp

Because the timestamp is our index, we can inspect its earliest value with:

```python
df.index.min()
```

and its latest value with:

```python
df.index.max()
```

The same checks can be performed on the loaded DataFrame:

```python
loaded_df.index.min()
loaded_df.index.max()
```

For example:

```python
print("Original first time:", df.index.min())
print("Loaded first time:", loaded_df.index.min())

print("Original last time:", df.index.max())
print("Loaded last time:", loaded_df.index.max())
```

---

# 23. Why Compare the Original and Loaded Data?

Saving a file is not enough.

We also want to check that loading it again gives us the dataset we expected.

Useful checks include:

```text
Same number of rows?
Same number of columns?
Same first timestamp?
Same last timestamp?
Same column names?
Datetime index restored correctly?
```

This is an introduction to:

```text
Data validation
```

Later, much stronger validation checks can be added.

---

# 24. Complete Stage 2 Example

## Imports

```python
from pathlib import Path

import pandas as pd
import yfinance as yf
```

---

## Create the Clean DataFrame

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

df.columns = df.columns.str.lower()
df = df[["open", "high", "low", "close", "volume"]]

df.index = df.index.tz_convert("UTC")

bar_length = pd.Timedelta(minutes=1)
df.index = df.index + bar_length

df.index.name = "time"
df = df.sort_index()
```

This part comes from Stage 1.

Its purpose in Stage 2 is simply to create some clean data that can be saved.

---

## Create the File Path

```python
data_folder = Path("../data")
file_path = data_folder / "qqq_1m.csv"

print(data_folder)
print(file_path)
```

---

## Save the Data

```python
df.to_csv(file_path)
```

---

## Check the File Exists

```python
file_path.exists()
```

---

## Load the CSV Without Special Instructions

```python
loaded_df = pd.read_csv(file_path)

loaded_df.head()
```

This demonstrates that the timestamp initially returns as a normal column.

---

## Load the CSV Properly

```python
loaded_df = pd.read_csv(
    file_path,
    parse_dates=["time"],
    index_col="time",
)

loaded_df.head()
```

---

## Inspect the Loaded Data

```python
loaded_df.info()
```

---

## Compare Original and Loaded Data

```python
print("Original shape:", df.shape)
print("Loaded shape:", loaded_df.shape)

print("Original first time:", df.index.min())
print("Loaded first time:", loaded_df.index.min())

print("Original last time:", df.index.max())
print("Loaded last time:", loaded_df.index.max())
```

---

# 25. Stage 2 Workflow

The complete process can now be thought of as:

```text
Yahoo Finance
      ↓
Download
      ↓
Clean DataFrame
      ↓
df
      ↓
df.to_csv()
      ↓
qqq_1m.csv
      ↓
pd.read_csv()
      ↓
Restore datetime index
      ↓
loaded_df
      ↓
Ready for research
```

---

# 26. Save vs Load

A useful way to remember the two operations is:

## Save

```python
df.to_csv(file_path)
```

```text
DataFrame
    ↓
CSV file
```

## Load

```python
pd.read_csv(file_path)
```

```text
CSV file
    ↓
DataFrame
```

They are opposite operations.

---

# 27. Main New Concepts from Stage 2

The most important concepts introduced in this stage are:

### `Path`

```python
Path("../data")
```

Represents a location on the computer.

---

### `/` with `Path`

```python
data_folder / "qqq_1m.csv"
```

Combines folder and filename paths.

---

### `df.to_csv()`

```python
df.to_csv(file_path)
```

Writes a DataFrame to a CSV file.

---

### `.exists()`

```python
file_path.exists()
```

Checks whether a file exists.

---

### `pd.read_csv()`

```python
pd.read_csv(file_path)
```

Reads a CSV into a DataFrame.

---

### `parse_dates`

```python
parse_dates=["time"]
```

Converts the saved timestamp text back into datetime values.

---

### `index_col`

```python
index_col="time"
```

Restores the `time` column as the DataFrame index.

---

### `.shape`

```python
df.shape
```

Returns:

```text
(rows, columns)
```

---

### `.info()`

```python
df.info()
```

Provides information about the structure and data types of a DataFrame.

---

### `.index.min()` and `.index.max()`

```python
df.index.min()
df.index.max()
```

Find the earliest and latest timestamp in the dataset.

---

# 28. Why This Stage Matters

Before Stage 2, the workflow was:

```text
Download data
      ↓
Use it
      ↓
Python closes
      ↓
DataFrame disappears
```

Now it is:

```text
Download
      ↓
Clean
      ↓
Save
      ↓
Close Python
      ↓
Open Python later
      ↓
Load
      ↓
Continue research
```

This means the market data no longer needs to be downloaded every time a research session begins.

It also creates the foundation for building larger historical datasets.

---

# 29. What We Have Not Done Yet

At this stage, the workflow still downloads a new small dataset and replaces the CSV.

We have not yet covered:

- checking whether a dataset already exists automatically
- determining the last saved timestamp
- downloading only newer market data
- joining old and new data
- detecting duplicate timestamps
- removing duplicates
- handling gaps
- updating the CSV automatically

Those belong to the next stage.

---

# Next Stage

The next stage will be:

```text
03-update-existing-data
```

The goal will be to move from:

```text
Download everything
      ↓
Save
```

to:

```text
Does a dataset already exist?
          │
     ┌────┴────┐
     │         │
    YES        NO
     │         │
   Load      Download
     │         │
     ↓         │
Find last      │
timestamp      │
     ↓         │
Download       │
newer data     │
     │         │
     └────┬────┘
          ↓
      Clean data
          ↓
      Combine data
          ↓
   Remove duplicates
          ↓
         Save
```

This will begin turning the project from a simple download script into a reusable local market-data workflow.
