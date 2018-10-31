

```python
# Importing dependies for visualizations
%matplotlib inline
from matplotlib import style
style.use('fivethirtyeight')
import matplotlib.pyplot as plt
```


```python
# Importing python libraries
import numpy as np
import pandas as pd
```


```python
# importing datetime module
import datetime as dt
```
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }
.dataframe tbody tr th {
    vertical-align: top;
}

.dataframe thead th {
    text-align: right;
}
</style>
# Reflect Tables into SQLAlchemy ORM


```python
# Python SQL toolkit and object relational mapper
import sqlalchemy
from sqlalchemy.ext.automap import automap_base
from sqlalchemy.orm import Session
from sqlalchemy import create_engine, func
```


```python
engine = create_engine("sqlite:///Resources/hawaii.sqlite")
```


```python
# reflect an existing database into a new model
Base = automap_base()
# reflect the tables
Base.prepare(engine, reflect=True)
```


```python
# View all the classes automap found
Base.classes.keys()
```




    ['measurement', 'station']




```python
# Save references of each table in variables
Measurement = Base.classes.measurement
Station = Base.classes.station
```


```python
# Create session link from Python to Database
session = Session(engine)
```


```python
first_row_measurement = session.query(Measurement).first()
first_row_measurement.__dict__
```




    {'_sa_instance_state': <sqlalchemy.orm.state.InstanceState at 0x2b4ff5187f0>,
     'tobs': 65.0,
     'date': '2010-01-01',
     'id': 1,
     'prcp': 0.08,
     'station': 'USC00519397'}




```python
first_row_station = session.query(Station).first()
first_row_station.__dict__
```




    {'_sa_instance_state': <sqlalchemy.orm.state.InstanceState at 0x2b4ff529470>,
     'longitude': -157.8168,
     'latitude': 21.2716,
     'station': 'USC00519397',
     'elevation': 3.0,
     'name': 'WAIKIKI 717.2, HI US',
     'id': 1}



# Exploratory Climate Analysis


```python
# Design a query to retrieve the last 12 months of precipitation data and plot the results
# last date in the table
last_date = session.query(Measurement.date).order_by(Measurement.date.desc()).first()
print(last_date)
```

    ('2017-08-23',)
    


```python
# Calculate the date 1 year ago from the last data point in the database
year_ago = dt.date(2017,8,23) - dt.timedelta(days=365)
print(year_ago)
```

    2016-08-23
    


```python
# Perform a query to retrieve the data and precipitation scores
prcp_scores = session.query(Measurement.date, Measurement.prcp).filter(Measurement.date > year_ago).order_by(Measurement.date).all()
#for prcp in prcp_scores:
    #print(prcp)
```


```python
# Save the query results as a Pandas DataFrame and set the index to the date column
prcp_df = pd.DataFrame(prcp_scores)
prcp_df.head().set_index('date')
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>prcp</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2016-08-24</th>
      <td>0.08</td>
    </tr>
    <tr>
      <th>2016-08-24</th>
      <td>2.15</td>
    </tr>
    <tr>
      <th>2016-08-24</th>
      <td>2.28</td>
    </tr>
    <tr>
      <th>2016-08-24</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2016-08-24</th>
      <td>1.22</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Sort the dataframe by date
prcp_df.sort_values(by='date').head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>prcp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2016-08-24</td>
      <td>0.08</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2016-08-24</td>
      <td>2.15</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2016-08-24</td>
      <td>2.28</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2016-08-24</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2016-08-24</td>
      <td>1.22</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.rcParams['figure.figsize'] = [15,10]
```


```python
# Use Pandas Plotting with Matplotlib to plot the data
prcp_df.plot('date','prcp')
plt.xlabel("Date")
plt.title("last 12 months of precipitation data analysis")
plt.legend(["Precipitation"])
plt.show()
```


![png](output_19_0.png)



```python
# Use Pandas to calcualte the summary statistics for the precipitation data
prcp_df.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>prcp</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>2015.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>0.176462</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.460288</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>0.020000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>0.130000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>6.700000</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Design a query to show how many stations are available in this dataset?
stations_count = session.query(Measurement).group_by(Measurement.station).count()
print(stations_count)
```

    9
    


```python
# What are the most active stations? (i.e. what stations have the most rows)?
# List the stations and the counts in descending order.
active_stations = session.query(Measurement.station, func.count(Measurement.id)).group_by(Measurement.station).\
order_by(func.count(Measurement.id).desc()).all()

most_active = active_stations[0][0]
for station, count in active_stations:
    print(station,count)
print(f'Most Active Station:{most_active}')
```

    USC00519281 2772
    USC00519397 2724
    USC00513117 2709
    USC00519523 2669
    USC00516128 2612
    USC00514830 2202
    USC00511918 1979
    USC00517948 1372
    USC00518838 511
    Most Active Station:USC00519281
    


```python
# Using the station id from the previous query, calculate the lowest temperature recorded, 
# highest temperature recorded, and average temperature most active station?
temp = session.query(Measurement.station, func.min(Measurement.tobs), func.max(Measurement.tobs),\
                     func.avg(Measurement.tobs)).filter(Measurement.station == most_active).group_by(Measurement.station)
for station,lowest_temp,highest_temp,avg_temp in temp:
    print(lowest_temp,highest_temp,avg_temp)
```

    54.0 85.0 71.66378066378067
    


```python
# Choose the station with the highest number of temperature observations.
# Query the last 12 months of temperature observation data for this station and plot the results as a histogram
temperature = session.query(Measurement.station, Measurement.date, Measurement.tobs).\
filter(Measurement.station == most_active).\
filter(Measurement.date > year_ago).\
order_by(Measurement.date).all()

temp_df = pd.DataFrame(temperature)
temp_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>station</th>
      <th>date</th>
      <th>tobs</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>USC00519281</td>
      <td>2016-08-24</td>
      <td>77.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>USC00519281</td>
      <td>2016-08-25</td>
      <td>80.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>USC00519281</td>
      <td>2016-08-26</td>
      <td>80.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>USC00519281</td>
      <td>2016-08-27</td>
      <td>75.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>USC00519281</td>
      <td>2016-08-28</td>
      <td>73.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
plt.hist(temp_df['tobs'],12)
plt.xlabel("Temperature")
plt.ylabel("Number of observations")
plt.title("Temperature analysis of station with the highest number of temperature observations")
plt.show()
```


![png](output_25_0.png)



```python
# This function called `calc_temps` will accept start date and end date in the format '%Y-%m-%d' 
# and return the minimum, average, and maximum temperatures for that range of dates
def calc_temps(start_date, end_date):
    """TMIN, TAVG, and TMAX for a list of dates.
    
    Args:
        start_date (string): A date string in the format %Y-%m-%d
        end_date (string): A date string in the format %Y-%m-%d
        
    Returns:
        TMIN, TAVE, and TMAX
    """
    
    return session.query(func.min(Measurement.tobs), func.avg(Measurement.tobs), func.max(Measurement.tobs)).\
        filter(Measurement.date >= start_date).filter(Measurement.date <= end_date).all()

# function usage example
print(calc_temps('2012-02-28', '2012-03-05'))
```

    [(62.0, 69.57142857142857, 74.0)]
    


```python
# Use your previous function `calc_temps` to calculate the tmin, tavg, and tmax 
# for your trip using the previous year's data for those same dates.
temps_2011 = calc_temps('2011-02-28','2011-03-05')
temps_2011
```




    [(61.0, 69.75510204081633, 75.0)]




```python
# Plot the results from your previous query as a bar chart. 
# Use "Trip Avg Temp" as your Title
# Use the average temperature for the y value
# Use the peak-to-peak (tmax-tmin) value as the y error bar (yerr)
calc_temps_df = pd.DataFrame(temps_2011,columns=['tmin','tavg','tmax'])
calc_temps_df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>tmin</th>
      <th>tavg</th>
      <th>tmax</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>61.0</td>
      <td>69.755102</td>
      <td>75.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
from matplotlib.pyplot import figure
```


```python
error = calc_temps_df['tmax'] - calc_temps_df ['tmin']
plt.bar(0.5,calc_temps_df['tavg'], yerr = error, color = 'lightcoral', alpha=0.75)
plt.ylabel("Temp(F)")
plt.title("Trip Avg Temp")
plt.ylim(0,100)
plt.rc('figure', figsize=(10, 6))
plt.show()
```


![png](output_30_0.png)



```python
rainfall = session.query(Measurement.station,Measurement.date,Measurement.prcp, Measurement.tobs).\
filter(Measurement.date >= ).\
filter(Measurement.date <= ).\
order_by(Measurement.station).all()
```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-87-3449259b691e> in <module>()
    ----> 1 rainfall = session.query(Measurement.station,Measurement.date,Measurement.prcp, Measurement.tobs).filter(Measurement.date >= dt.date('2012-02-28')).filter(Measurement.date <= dt.date('2012-03-05')).order_by(Measurement.station).all()
    

    TypeError: an integer is required (got type str)



```python
# Calculate the rainfall per weather station for your trip dates using the previous year's matching dates.
# Sort this in descending order by precipitation amount and list the station, name, latitude, longitude, and elevation


```

    [('USC00516128', 'MANOA LYON ARBO 785.2, HI US', 21.3331, -157.8025, 152.4, 0.31), ('USC00519281', 'WAIHEE 837.5, HI US', 21.45167, -157.84888999999998, 32.9, 0.25), ('USC00518838', 'UPPER WAHIAWA 874.3, HI US', 21.4992, -158.0111, 306.6, 0.1), ('USC00513117', 'KANEOHE 838.1, HI US', 21.4234, -157.8015, 14.6, 0.060000000000000005), ('USC00511918', 'HONOLULU OBSERVATORY 702.2, HI US', 21.3152, -157.9992, 0.9, 0.0), ('USC00514830', 'KUALOA RANCH HEADQUARTERS 886.9, HI US', 21.5213, -157.8374, 7.0, 0.0), ('USC00517948', 'PEARL CITY, HI US', 21.3934, -157.9751, 11.9, 0.0), ('USC00519397', 'WAIKIKI 717.2, HI US', 21.2716, -157.8168, 3.0, 0.0), ('USC00519523', 'WAIMANALO EXPERIMENTAL FARM, HI US', 21.33556, -157.71139, 19.5, 0.0)]
    

## Optional Challenge Assignment


```python
# Create a query that will calculate the daily normals 
# (i.e. the averages for tmin, tmax, and tavg for all historic data matching a specific month and day)

def daily_normals(date):
    """Daily Normals.
    
    Args:
        date (str): A date string in the format '%m-%d'
        
    Returns:
        A list of tuples containing the daily normals, tmin, tavg, and tmax
    
    """
    
    sel = [func.min(Measurement.tobs), func.avg(Measurement.tobs), func.max(Measurement.tobs)]
    return session.query(*sel).filter(func.strftime("%m-%d", Measurement.date) == date).all()
    
daily_normals("01-01")
```




    [(62.0, 69.15384615384616, 77.0)]




```python
# calculate the daily normals for your trip
# push each tuple of calculations into a list called `normals`

# Set the start and end date of the trip

# Use the start and end date to create a range of dates

# Stip off the year and save a list of %m-%d strings

# Loop through the list of %m-%d strings and calculate the normals for each date

```




    [(62.0, 69.15384615384616, 77.0),
     (60.0, 69.39622641509433, 77.0),
     (62.0, 68.9090909090909, 77.0),
     (58.0, 70.0, 76.0),
     (56.0, 67.96428571428571, 76.0),
     (61.0, 68.96491228070175, 76.0),
     (57.0, 68.54385964912281, 76.0)]




```python
# Load the previous query results into a Pandas DataFrame and add the `trip_dates` range as the `date` index

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>tmin</th>
      <th>tavg</th>
      <th>tmax</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2018-01-01</th>
      <td>62.0</td>
      <td>69.153846</td>
      <td>77.0</td>
    </tr>
    <tr>
      <th>2018-01-02</th>
      <td>60.0</td>
      <td>69.396226</td>
      <td>77.0</td>
    </tr>
    <tr>
      <th>2018-01-03</th>
      <td>62.0</td>
      <td>68.909091</td>
      <td>77.0</td>
    </tr>
    <tr>
      <th>2018-01-04</th>
      <td>58.0</td>
      <td>70.000000</td>
      <td>76.0</td>
    </tr>
    <tr>
      <th>2018-01-05</th>
      <td>56.0</td>
      <td>67.964286</td>
      <td>76.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Plot the daily normals as an area plot with `stacked=False`

```


    <IPython.core.display.Javascript object>



<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAyAAAAJYCAYAAACadoJwAAAgAElEQVR4XuydCZgU5bX+T3fPIgMiIIuCCIlEvSaicUEhMW4JxhhRI2qCwS0qGiMSMOYmV40JMRoM0et2E6Pe+08gKnEBjRA1RnBBRUXEBDSurKKADMswDDPT/X/OV1U91T0909W19DlT/dbzDDN01/fVqfdX3XXe+rZEfX19hrBBASgABaAAFIACUAAKQAEoAAXKoEACBqQMKuMQUAAKQAEoAAWgABSAAlAAChgFYEBwIUABKAAFoAAUgAJQAApAAShQNgVgQMomNQ4EBaAAFIACUAAKQAEoAAWgAAwIrgEoAAWgABSAAlAACkABKAAFyqYADEjZpMaBoAAUgAJQAApAASgABaAAFIABwTUABaAAFIACUAAKQAEoAAWgQNkUgAEpm9Q4EBSAAlAACkABKAAFoAAUgAIwILgGoAAUgAJQAApAASgABaAAFCibAjAgZZMaB4ICUAAKQAEoAAWgABSAAlAABgTXABSAAlAACkABKAAFoAAUgAJlUwAGpGxS40BQAApAASgABaAAFIACUAAKwIDgGoACUAAKQAEoAAWgABSAAlCgbArAgJRNahwICkABKAAFoAAUgAJQAApAARgQXANQAApAASgABaAAFIACUAAKlE0BGJCySY0DQQEoAAWgABSAAlAACkABKAADgmsACkABKAAFoAAUgAJQAApAgbIpAANSNqlxICgABaAAFIACUAAKQAEoAAVgQHANQAEoAAWgABSAAlAACkABKFA2BWBAyiY1DgQFoAAUgAJQAApAASgABaAADAiuASgABaAAFIACUAAKQAEoAAXKpgAMSNmkxoGgABSAAlAACkABKAAFoAAUgAHBNQAFoAAUgAJQAApAASgABaBA2RSAASmb1DgQFIACUAAKQAEoAAWgABSAAjAguAagABSAAlAACkABKAAFoAAUKJsCMCBlkxoHggJQAApAASgABaAAFIACUAAGBNcAFIACUAAKQAEoAAWgABSAAmVTAAakbFK3P1A6naZZs2bRAw88QG+++SbV19dTt27daOjQofS1r32NLr30UurXr1/BCDOZDM2YMYNmzpxJy5cvp+bmZhoyZAiNGTOGJk6cSN27dxc8MxwaCkABKAAFoAAUgAJQAAoUVgAGROjKaGxspHHjxtEzzzyT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QAIkQAIkQAIkQAIkQAIRI0ADEjHUvBAJkAAJkAAJkAAJkAAJkAANCJ8BEiABEiABEiABEiABEiCBiBGgAYkYal6IBEiABEiABEiABEiABEiABoTPAAmQAAmQAAmQAAmQAAmQQMQI0IBEDDUvRAIkQAIkQAIkQAIkQAIkQAPCZ4AESIAESIAESIAESIAESCBiBGhAIoaaFyIBEiABEiABEiABEiABEqAB4TNAAiRAAiRAAiRAAiRAAiQQMQI0IBFDzQuRAAmQAAmQAAmQAAmQAAnQgPAZIAESIAESIAESIAESIAESiBgBGpCIoeaFSIAESIAESIAESIAESIAEaED4DJAACZAACZAACZAACZAACUSMAA1IxFDzQiRAAiRAAiRAAiRAAiRAAjQgfAZIgARIgARIgARIgARIgAQiRoAGJGKoeSESIAESIAESIAESIAESIAEaED4DJEACJEACJEACJEACJEACESNAAxIx1LwQCZAACZAACZAACZAACZAADQifARIgARIgARIgARIgARIggYgRoAGJGGpeiARIgARIgARIgARIgARIgAaEzwAJkAAJkAAJkAAJkAAJkEDECNCARAw1L0QCJEACJEACJEACJEACJEADwmeABEiABEiABEiABEiABEggYgRoQCKGmhciARIgARIgARIgARIgARKgAeEzQAIkQAIkQAIkQAIkQAIkEDECNCARQ80LkQAJkAAJkAAJkAAJkAAJ0IDwGSABEiABEiABEiABEiABEogYARqQiKHmhUiABEiABEiABEiABEiABGhA+AyQAAmQAAmQAAmQAAmQAAlEjAANSMRQ80IkQAIkQAIkQAIkQAIkQAL/DwgYbv+MV8FcAAAAAElFTkSuQmCC" width="640">

