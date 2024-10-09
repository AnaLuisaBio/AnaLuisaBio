# Pandas Timeseries

Want to learn more than you find here? Then take a look at these sources:

 * [Basic Time Series Manipulation with Pandas](https://towardsdatascience.com/basic-time-series-manipulation-with-pandas-4432afee64ea)
 * [Dealing with Datetimes: A tutorial given at PyCon 2019](https://github.com/pganssle-talks/pycon-us-2019-dealing-with-datetimes)


```python
import pandas as pd
```

## Creating Timeseries

[date_range](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.date_range.html) and its [exhaustive list of frequency choices](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#timeseries-offset-aliases) make it very easy to create a list of specific dates.

For example, the list of all last *business* days in a month in 2019:


```python
year = '2019'
ts = pd.date_range(year, periods=12, freq='BM')
ts
```




    DatetimeIndex(['2019-01-31', '2019-02-28', '2019-03-29', '2019-04-30',
                   '2019-05-31', '2019-06-28', '2019-07-31', '2019-08-30',
                   '2019-09-30', '2019-10-31', '2019-11-29', '2019-12-31'],
                  dtype='datetime64[ns]', freq='BM')




```python
df = pd.DataFrame(index=ts)
df['DoW'] = df.index.weekday_name
df['BM≠M'] = (df.index != pd.date_range(year, periods=12, freq='M'))
df.iloc[:, -1] = df.iloc[:, -1].apply(lambda x: '✔' if x else '☓')
print(df.iloc[:6, :].transpose())
print()
print(df.iloc[6:, :].transpose())
```

         2019-01-31 2019-02-28 2019-03-29 2019-04-30 2019-05-31 2019-06-28
    DoW    Thursday   Thursday     Friday    Tuesday     Friday     Friday
    BM≠M          ☓          ☓          ✔          ☓          ☓          ✔
    
         2019-07-31 2019-08-30 2019-09-30 2019-10-31 2019-11-29 2019-12-31
    DoW   Wednesday     Friday     Monday   Thursday     Friday    Tuesday
    BM≠M          ☓          ✔          ☓          ☓          ✔          ☓


Generated sequences like this can be used to filter or bin time series data, or create ‘nice’ (i.e. semantically meaningful) axis labels for a graph.

## Relative Date & Time Offsets
To get a series like “first Thursday in the second week of each month”, you can add [DateOffset Objects](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects) like this:


```python
pd.date_range(year, periods=12, freq='MS') + pd.DateOffset(weeks=1, weekday=3)
```

    /opt/venvs/jupyterhub/lib/python3.6/site-packages/pandas/core/arrays/datetimes.py:743: PerformanceWarning: Non-vectorized DateOffset being applied to Series or DatetimeIndex
      "or DatetimeIndex", PerformanceWarning)





    DatetimeIndex(['2019-01-10', '2019-02-14', '2019-03-14', '2019-04-11',
                   '2019-05-09', '2019-06-13', '2019-07-11', '2019-08-08',
                   '2019-09-12', '2019-10-10', '2019-11-14', '2019-12-12'],
                  dtype='datetime64[ns]', freq='WOM-2THU')



This works, but you'll get an ugly `PerformanceWarning` about non-vectorized application of a `DateOffset`. However, as you can see above, the resulting `DatetimeIndex` tells you the proper `freq` string to use.


```python
pd.date_range(year, periods=12, freq='WOM-2THU')
```




    DatetimeIndex(['2019-01-10', '2019-02-14', '2019-03-14', '2019-04-11',
                   '2019-05-09', '2019-06-13', '2019-07-11', '2019-08-08',
                   '2019-09-12', '2019-10-10', '2019-11-14', '2019-12-12'],
                  dtype='datetime64[ns]', freq='WOM-2THU')


