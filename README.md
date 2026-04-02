# 📈 Page View Time Series Visualizer

A Python data analysis project that visualizes freeCodeCamp.org forum page views from **May 2016 to December 2019** using line charts, bar charts, and box plots.

> Part of the **freeCodeCamp – Data Analysis with Python** certification — Project 4.

***

## 📁 Project Files

| File | Purpose |
|------|---------|
| `time_series_visualizer.py` | Main script — all data processing and chart generation |
| `fcc-forum-pageviews.csv` | Dataset — daily forum page views 2016–2019 |
| `main.py` | Development runner and test file |
| `test_module.py` | Unit tests provided by freeCodeCamp |
| `line_plot.png` | Output — daily page views line chart |
| `bar_plot.png` | Output — average monthly page views bar chart |
| `box_plot.png` | Output — year-wise and month-wise box plots |

***

## 📋 Dataset

| Column | Description |
|--------|-------------|
| `date` | Date of the page view record (used as index) |
| `value` | Number of page views on that day |

***

## 🔧 How It Works

### Step 1 — Load Data
```python
df = pd.read_csv('fcc-forum-pageviews.csv', parse_dates=['date'], index_col='date')
```
`parse_dates=['date']` converts the date column from string → real datetime object.
`index_col='date'` sets the date as the row index for easy time-based operations.

### Step 2 — Clean Data
```python
df = df[
    (df['value'] >= df['value'].quantile(0.025)) &
    (df['value'] <= df['value'].quantile(0.975))
]
```
Removes the top 2.5% and bottom 2.5% of page view values — these are outlier days (e.g., viral spikes or server downtime) that would distort the charts.

***

## 📊 Chart 1 — Line Plot (`line_plot.png`)

**What it shows:** Daily page view trend from May 2016 to December 2019.

```python
fig, ax = plt.subplots(figsize=(15, 5))
ax.plot(df.index, df['value'], color='red', linewidth=1)
ax.set_title('Daily freeCodeCamp Forum Page Views 5/2016-12/2019')
ax.set_xlabel('Date')
ax.set_ylabel('Page Views')
```

| Code | Why |
|------|-----|
| `plt.subplots()` | Creates a figure + axes object together |
| `df.index` | Uses the date index as x-axis |
| `linewidth=1` | Thin line since ~1300 data points are plotted |

***

## 📊 Chart 2 — Bar Plot (`bar_plot.png`)

**What it shows:** Average daily page views per month, grouped by year — shows seasonal patterns and year-over-year growth.

```python
df_bar['year'] = df_bar.index.year
df_bar['month'] = df_bar.index.month
df_bar = df_bar.groupby(['year', 'month'])['value'].mean().unstack()
df_bar.columns = ['January', 'February', ... 'December']
fig = df_bar.plot(kind='bar', figsize=(15, 7)).get_figure()
```

| Code | Why |
|------|-----|
| `df.index.year` / `.month` | Extracts year/month number from datetime index |
| `groupby().mean()` | Averages page views per year-month group |
| `.unstack()` | Pivots months from rows → columns so pandas draws grouped bars |
| `.get_figure()` | Extracts the matplotlib figure from a pandas plot |

***

## 📊 Chart 3 — Box Plots (`box_plot.png`)

**What it shows:** Two side-by-side box plots — one showing yearly distribution trend, one showing monthly seasonality.

```python
fig, axes = plt.subplots(1, 2, figsize=(28, 10))

# Year-wise
sns.boxplot(data=df_box, x='year', y='value', ax=axes[0])

# Month-wise
month_order = ['Jan','Feb','Mar','Apr','May','Jun',
               'Jul','Aug','Sep','Oct','Nov','Dec']
sns.boxplot(data=df_box, x='month', y='value', order=month_order, ax=axes[1])
```

| Code | Why |
|------|-----|
| `plt.subplots(1, 2)` | Creates 2 side-by-side chart panels |
| `axes[0]` / `axes[1]` | Controls each panel independently |
| `d.strftime('%b')` | Formats datetime → short month name (Jan, Feb...) |
| `order=[...]` | Forces Jan→Dec order instead of alphabetical sorting |

***

## 🧠 Key Concepts (Quick Revision)

| Concept | Explanation |
|---------|-------------|
| `parse_dates=['col']` | Converts string column to real datetime objects |
| `index_col='date'` | Sets date as row index for time-based operations |
| `df.index.year` / `.month` | Extract year/month from a datetime index |
| `quantile(0.025/0.975)` | 2.5th/97.5th percentile — used for outlier removal |
| `groupby().mean().unstack()` | Aggregate + pivot: long format → wide format for grouped bars |
| `plt.subplots(1, 2)` | Create multi-panel figures side by side |
| `axes[0]` / `axes[1]` | Target individual panels in a multi-plot figure |
| `order=[...]` in seaborn | Force custom sort order on categorical x-axis |
| `strftime('%b')` | Format date to abbreviated month name |
| `sns.boxplot()` | Show data distribution, median, and outliers per group |

***

## ▶️ How to Run

```bash
pip install -r requirements.txt
python main.py
```

Expected output: `Ran 11 tests — OK` ✅

***

## ✅ Tests

All 11 unit tests pass (`test_module.py` provided by freeCodeCamp).

***

## 🏅 Certification

[freeCodeCamp – Data Analysis with Python](https://www.freecodecamp.org/learn/data-analysis-with-python/)