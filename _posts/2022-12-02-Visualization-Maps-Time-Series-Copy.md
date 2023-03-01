# Map Visualization With Plotly

> Map Visualization, Choropleth, Continuous Color Schemes, Divergent Color Schemes, Simultaneous Animated Maps, Undirected Graphs, Subplots For Time Visualization, Line Plots, Scatter Plots and Density Plots.

- toc: true 
- badges: true
- comments: true
- categories: [Map Visualization, Choropleth, Continuous Color Schemes, Divergent Color Schemes, Simultaneous Animated Maps, Undirected Graphs, Subplots For Time Visualization, Line Plots, Scatter Plots and Density Plots]
- image: images/map_visualization.png

## 1) Import libraries


```python
import json
import requests
import numpy as np
import pandas as pd
import plotly.express as px
from plotly.offline import plot   #### We need this in case we need to manually upload the interactive graphs on the blog
import plotly.graph_objects as go
from plotly.subplots import make_subplots
from sklearn.preprocessing import MinMaxScaler

pd.options.mode.chained_assignment = None  # default='warn'
```

    C:\Users\Leonardo Castro\anaconda3\lib\site-packages\scipy\__init__.py:138: UserWarning:
    
    A NumPy version >=1.16.5 and <1.23.0 is required for this version of SciPy (detected version 1.24.2)
    
    

## 2) Data preparation 

Original dataset sources: <br>
- https://www.kaggle.com/code/docxian/visualize-seabird-tracks/data
- https://raw.githubusercontent.com/suchith91/wdi/master/WDI_Data_Selected.csv
- https://www.gov.br/anac/pt-br/assuntos/dados-e-estatisticas/dados-estatisticos/dados-estatisticos

### 2.1) World Indicators Dataset

#### 2.1.1) Load dataset


```python
# This line will disappear in the portfolio page
# Step 1: Import dataset
data = pd.read_csv("https://raw.githubusercontent.com/leonardodecastro/data/main/WDI_Data_Selected.csv", encoding='cp1252').drop(['Indicator Code'], axis= 1)

# Step 2: Change dataset to allow for the use of map libraries
world_data_df = pd.melt(data, id_vars=['Country Name', 'Country Code','Indicator Name'], var_name='Year', value_name='Indicator Value')
world_data_df['Year'] = world_data_df['Year'].astype('int')
world_data_df.head(2)
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
      <th>Country Name</th>
      <th>Country Code</th>
      <th>Indicator Name</th>
      <th>Year</th>
      <th>Indicator Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Arab World</td>
      <td>ARB</td>
      <td>CO2 emissions (metric tons per capita)</td>
      <td>1960</td>
      <td>0.644</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Arab World</td>
      <td>ARB</td>
      <td>Exports of goods and services (% of GDP)</td>
      <td>1960</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



#### 2.1.2) Create datasets for specific indicator


```python
# This line will disappear in the portfolio page
# Create dataset for GDP Growth per year
GDP_growth_df = world_data_df[world_data_df['Indicator Name'] == 'GDP growth (annual %)']

# Create value ranges for certain visualizations
bins= [GDP_growth_df['Indicator Value'].min()-1, -10, -5, 0, 5, 10, GDP_growth_df['Indicator Value'].max()+1]
labels = ['< -10 %' , '-10% to -5%', '-5% to 0%','0% to 5%','5% to 10%','> 10%']
GDP_growth_df.loc[:,'Growth Ranges']= pd.cut(GDP_growth_df['Indicator Value'], bins=bins, labels=labels, right=False).astype('str')
```

### 2.2) Flight Paths Brazil

The following dataset was created using public information. The codes used to represent airports refer to each state in Brazil. The flight information is aggregated so as to determine the amount of people that travel per year from one Brazilian state to another. 


```python
flight_path_df = pd.read_csv('https://raw.githubusercontent.com/leonardodecastro/data/main/airplane_flights_brazil.csv')
```

### 2.3) Bird Migration Dataset

#### 2.3.1) Load dataset


```python
bird_routes_df = pd.read_csv('https://raw.githubusercontent.com/leonardodecastro/data/main/anon_gps_tracks_with_dive.csv')
```

#### 2.3.2) Clean the dataset


```python
# This line will disappear in the portfolio page
# Step 1: Limit the dataset to the features we seek to investigate
final_bird_df = bird_routes_df[['lat','lon','bird','date_time','species','alt']]

# Step 2: Transform the date time variables
final_bird_df['date_time'] = pd.to_datetime(final_bird_df['date_time'])

# Step 3: Set the date time variable as the index do that we can resample the dataset for every 15 minutes
final_bird_df.set_index("date_time",inplace=True)
final_bird_df = final_bird_df.groupby(['bird','species']).resample('15min').mean()

# Step 4: Reset the dataset index
final_bird_df = final_bird_df.reset_index(level=1)
final_bird_df = final_bird_df.drop('bird', axis=1).reset_index()

# Step 5: Make sure the bird identifier is considered a string
final_bird_df['bird'] = final_bird_df['bird'].astype('int').astype('str')

# Step 6: Part of the analysis is focused on the Rathlin Island. Thus, we must create a dataset containing solely the data for this region. 
rathlin_island = final_bird_df[(final_bird_df['lat'] < 55.309) & (final_bird_df['lat'] > 55.254) & (final_bird_df['lon'] > -6.3194) & (final_bird_df['lon'] < -6.1416)]
```

    <ipython-input-6-e8125c7b73a3>:10: FutureWarning:
    
    The default value of numeric_only in DataFrameGroupBy.mean is deprecated. In a future version, numeric_only will default to False. Either specify numeric_only or select only columns which should be valid for the function.
    
    

## 3) Time Series Visualizations (Choropleth)

### 3.1.1) Using continuous color schemes

We need to use ISO codes with 3 letters for plotly.express to work properly


```python
# This line will disappear in the portfolio page
# Step 1: Create visualization
fig = px.choropleth(GDP_growth_df[GDP_growth_df['Year']>=1961],         # Limit the analysis to years for which that is plenty of data
                    locations="Country Code",                           # Column where country code with 3 letters can be found
                    color="Indicator Value",                            # Indicator Value is the numerical value we want to examine
                    hover_name="Country Code",                          # Column to add to hover information
                    animation_frame='Year',                             # Show column that will be used in the animation frame
                    color_continuous_scale=px.colors.diverging.RdYlGn,  # Select the type of divergent color scheme to be used
                    height = 700)                                       # Adjust the size of the figure

# Step 2: Control the speed of the transitions
fig.layout.updatemenus[0].buttons[0].args[1]['frame']['duration'] = 1000
fig.layout.updatemenus[0].buttons[0].args[1]['transition']['duration'] = 10

# Step 3: Add title to the plot
fig.update_layout(title_text='GDP Yealy Growth (%)', title_x=0.5)

# Step 4: Syle the map
fig.update_geos(showocean=True, oceancolor="#99cccc")

# Step 5: Generate an HTML file that includes the graph
plot(fig, filename='graph.html')

# Step 6: Show the map
fig.show()

![html](/images/graph.html)

