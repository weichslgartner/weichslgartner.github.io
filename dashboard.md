# Creating a Covid-19 Dashboard with bokeh, pandas, numpy, etc.

There is a plethora of Covid-19 dashboards in the depths of the internet.
However, they often let you not play around with the data and the parameters.
So why not just build out own and costumize it to our preferences.
For interactive plots and visualization I love to work with bokeh.
Without having to write javascript you can create everything you need for a dashboard and creates javascript output which can be rendered nicely in your browser.
The final product looks like this:
![gif](img/dashboard/dashboard_demo.gif)

An interactive version is hosted at [Heroku](https://covid-19-bokeh-dashboard.herokuapp.com/dashboard).
Before we dive into the bokeh stuff, we take first a look at the data.
Afterwards, I also explain briefly how you can use the Rest-Api and host a Python app for free at Heroku. 

## The Data

The root of all visualization is always the data.
Thankfully, the Johns Hopkins University offers raw data in csv format at their [github account](https://github.com/CSSEGISandData/COVID-19/tree/master/csse_covid_19_data).
So what is the first thing you do if you have csv files? 
You fire up a jupyter notebook (personally I use jupyter lab) and explore the data with pandas and some numpy.

So let's get started and import these two wonderful libraries:
```python
import pandas as pd
import numpy as np
```

Then we use the the raw urls from Github.
```python
url_confirmed = 'https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_time_series/time_series_covid19_confirmed_global.csv'
url_death = 'https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_time_series/time_series_covid19_deaths_global.csv'
url_recovered = 'https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_time_series/time_series_covid19_recovered_global.csv'
```
These files are updated daily, so we get the latest version.
In pandas you can directly paste the URL into the read_csv function and load the remote file into a dataframe.
```python
df = pd.read_csv(url_confirmed) 
```

With the `head()` we can get the first rows of the dataframe and see how the data is structured.
```python
df.head()
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
      <th>Province/State</th>
      <th>Country/Region</th>
      <th>Lat</th>
      <th>Long</th>
      <th>1/22/20</th>
      <th>1/23/20</th>
      <th>1/24/20</th>
      <th>1/25/20</th>
      <th>1/26/20</th>
      <th>1/27/20</th>
      <th>...</th>
      <th>10/3/20</th>
      <th>10/4/20</th>
      <th>10/5/20</th>
      <th>10/6/20</th>
      <th>10/7/20</th>
      <th>10/8/20</th>
      <th>10/9/20</th>
      <th>10/10/20</th>
      <th>10/11/20</th>
      <th>10/12/20</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>Afghanistan</td>
      <td>33.93911</td>
      <td>67.709953</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>39297</td>
      <td>39341</td>
      <td>39422</td>
      <td>39486</td>
      <td>39548</td>
      <td>39616</td>
      <td>39693</td>
      <td>39703</td>
      <td>39799</td>
      <td>39870</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NaN</td>
      <td>Albania</td>
      <td>41.15330</td>
      <td>20.168300</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>14117</td>
      <td>14266</td>
      <td>14410</td>
      <td>14568</td>
      <td>14730</td>
      <td>14899</td>
      <td>15066</td>
      <td>15231</td>
      <td>15399</td>
      <td>15570</td>
    </tr>
    <tr>
      <th>2</th>
      <td>NaN</td>
      <td>Algeria</td>
      <td>28.03390</td>
      <td>1.659600</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>51995</td>
      <td>52136</td>
      <td>52270</td>
      <td>52399</td>
      <td>52520</td>
      <td>52658</td>
      <td>52804</td>
      <td>52940</td>
      <td>53072</td>
      <td>53325</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NaN</td>
      <td>Andorra</td>
      <td>42.50630</td>
      <td>1.521800</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>2110</td>
      <td>2110</td>
      <td>2370</td>
      <td>2370</td>
      <td>2568</td>
      <td>2568</td>
      <td>2696</td>
      <td>2696</td>
      <td>2696</td>
      <td>2995</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NaN</td>
      <td>Angola</td>
      <td>-11.20270</td>
      <td>17.873900</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>5370</td>
      <td>5402</td>
      <td>5530</td>
      <td>5725</td>
      <td>5725</td>
      <td>5958</td>
      <td>6031</td>
      <td>6246</td>
      <td>6366</td>
      <td>6488</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 269 columns</p>
</div>
So we have columns with the "Province/State", "Country/Region", the GPS coordinates and then the number of absolute confirmed cases.
Each day is one column and JHU will add one column every day.

To get names of the confirmed cases columns we get all column names with `df.columns` and ignore the first four columns.
```python
case_columns=df.columns[4:]
```
Now, lets plot the data of a single country. 
First, we get the index of the wanted row and then select the values with the `loc[index,[columns]]` access.
A little `plot` in the end and we have our first simple visualization.
```python
german_index = df.loc[df['Country/Region']=='Germany'].index[0]
df.loc[german_index,case_columns].plot()
```
![png](img/dashboard/output_5_1.png)
So we see that the data is cumulative and always increasing.
To get the new cases for every day we can just use `diff()` to subtract each suceeding columns.

```python
df.loc[german_index,case_columns].diff().plot()
```

![png](img/dashboard/output_6_1.png)
Looks a bit noisy
If we would zoom in, we would see a pattern every seven days.
The well known seasonality of a week.
We can smoothen the plot by a rolling window (with the windows size of seven) and averaging (we use numpy mean here).


```python
df.loc[german_index,case_columns].diff().rolling(window=7, axis=0).apply(np.mean).plot()
```

![png](img/dashboard/output_7_1.png)
This looks much better now.
Later in the dashboard, we make the window size and the average function interactive parameters.

After we know how the numbers can be plotted, we want to inspect the missing data.
If we drop all rows containing an `nan` and get the ramaining unique countries, we will see the following: 
```python
df.dropna()["Country/Region"].unique()
```




    array(['Australia', 'Canada', 'China', 'Denmark', 'France', 'Netherlands',
           'United Kingdom'], dtype=object)


As all the `nan` values are located in the Province/State colum, we cann also select the rows where this column is not `nan` and see the same result.
```python
df[~pd.isna(df['Province/State'])]['Country/Region'].unique()
```




    array(['Australia', 'Canada', 'China', 'Denmark', 'France', 'Netherlands',
           'United Kingdom'], dtype=object)



A further inspection of this column reveals the following:
```python
df['Province/State'].unique()
```




    array([nan, 'Australian Capital Territory', 'New South Wales',
           'Northern Territory', 'Queensland', 'South Australia', 'Tasmania',
           'Victoria', 'Western Australia', 'Alberta', 'British Columbia',
           'Diamond Princess', 'Grand Princess', 'Manitoba', 'New Brunswick',
           'Newfoundland and Labrador', 'Northwest Territories',
           'Nova Scotia', 'Ontario', 'Prince Edward Island', 'Quebec',
           'Saskatchewan', 'Yukon', 'Anhui', 'Beijing', 'Chongqing', 'Fujian',
           'Gansu', 'Guangdong', 'Guangxi', 'Guizhou', 'Hainan', 'Hebei',
           'Heilongjiang', 'Henan', 'Hong Kong', 'Hubei', 'Hunan',
           'Inner Mongolia', 'Jiangsu', 'Jiangxi', 'Jilin', 'Liaoning',
           'Macau', 'Ningxia', 'Qinghai', 'Shaanxi', 'Shandong', 'Shanghai',
           'Shanxi', 'Sichuan', 'Tianjin', 'Tibet', 'Xinjiang', 'Yunnan',
           'Zhejiang', 'Faroe Islands', 'Greenland', 'French Guiana',
           'French Polynesia', 'Guadeloupe', 'Martinique', 'Mayotte',
           'New Caledonia', 'Reunion', 'Saint Barthelemy',
           'Saint Pierre and Miquelon', 'St Martin', 'Aruba',
           'Bonaire, Sint Eustatius and Saba', 'Curacao', 'Sint Maarten',
           'Anguilla', 'Bermuda', 'British Virgin Islands', 'Cayman Islands',
           'Channel Islands', 'Falkland Islands (Malvinas)', 'Gibraltar',
           'Isle of Man', 'Montserrat', 'Turks and Caicos Islands'],
          dtype=object)



A lot of islands and provinces of China:
```python
df[df['Country/Region']=='China']
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
      <th>Province/State</th>
      <th>Country/Region</th>
      <th>Lat</th>
      <th>Long</th>
      <th>1/22/20</th>
      <th>1/23/20</th>
      <th>1/24/20</th>
      <th>1/25/20</th>
      <th>1/26/20</th>
      <th>1/27/20</th>
      <th>...</th>
      <th>10/3/20</th>
      <th>10/4/20</th>
      <th>10/5/20</th>
      <th>10/6/20</th>
      <th>10/7/20</th>
      <th>10/8/20</th>
      <th>10/9/20</th>
      <th>10/10/20</th>
      <th>10/11/20</th>
      <th>10/12/20</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>56</th>
      <td>Anhui</td>
      <td>China</td>
      <td>31.8257</td>
      <td>117.2264</td>
      <td>1</td>
      <td>9</td>
      <td>15</td>
      <td>39</td>
      <td>60</td>
      <td>70</td>
      <td>...</td>
      <td>991</td>
      <td>991</td>
      <td>991</td>
      <td>991</td>
      <td>991</td>
      <td>991</td>
      <td>991</td>
      <td>991</td>
      <td>991</td>
      <td>991</td>
    </tr>
    <tr>
      <th>57</th>
      <td>Beijing</td>
      <td>China</td>
      <td>40.1824</td>
      <td>116.4142</td>
      <td>14</td>
      <td>22</td>
      <td>36</td>
      <td>41</td>
      <td>68</td>
      <td>80</td>
      <td>...</td>
      <td>936</td>
      <td>936</td>
      <td>936</td>
      <td>936</td>
      <td>936</td>
      <td>936</td>
      <td>936</td>
      <td>936</td>
      <td>937</td>
      <td>937</td>
    </tr>
    <tr>
      <th>58</th>
      <td>Chongqing</td>
      <td>China</td>
      <td>30.0572</td>
      <td>107.8740</td>
      <td>6</td>
      <td>9</td>
      <td>27</td>
      <td>57</td>
      <td>75</td>
      <td>110</td>
      <td>...</td>
      <td>585</td>
      <td>585</td>
      <td>585</td>
      <td>585</td>
      <td>585</td>
      <td>585</td>
      <td>585</td>
      <td>585</td>
      <td>585</td>
      <td>585</td>
    </tr>
    <tr>
      <th>59</th>
      <td>Fujian</td>
      <td>China</td>
      <td>26.0789</td>
      <td>117.9874</td>
      <td>1</td>
      <td>5</td>
      <td>10</td>
      <td>18</td>
      <td>35</td>
      <td>59</td>
      <td>...</td>
      <td>411</td>
      <td>413</td>
      <td>413</td>
      <td>413</td>
      <td>413</td>
      <td>414</td>
      <td>414</td>
      <td>415</td>
      <td>416</td>
      <td>416</td>
    </tr>
    <tr>
      <th>60</th>
      <td>Gansu</td>
      <td>China</td>
      <td>35.7518</td>
      <td>104.2861</td>
      <td>0</td>
      <td>2</td>
      <td>2</td>
      <td>4</td>
      <td>7</td>
      <td>14</td>
      <td>...</td>
      <td>170</td>
      <td>170</td>
      <td>170</td>
      <td>170</td>
      <td>170</td>
      <td>170</td>
      <td>170</td>
      <td>170</td>
      <td>170</td>
      <td>170</td>
    </tr>
    <tr>
      <th>61</th>
      <td>Guangdong</td>
      <td>China</td>
      <td>23.3417</td>
      <td>113.4244</td>
      <td>26</td>
      <td>32</td>
      <td>53</td>
      <td>78</td>
      <td>111</td>
      <td>151</td>
      <td>...</td>
      <td>1840</td>
      <td>1841</td>
      <td>1846</td>
      <td>1848</td>
      <td>1848</td>
      <td>1851</td>
      <td>1852</td>
      <td>1858</td>
      <td>1861</td>
      <td>1863</td>
    </tr>
    <tr>
      <th>62</th>
      <td>Guangxi</td>
      <td>China</td>
      <td>23.8298</td>
      <td>108.7881</td>
      <td>2</td>
      <td>5</td>
      <td>23</td>
      <td>23</td>
      <td>36</td>
      <td>46</td>
      <td>...</td>
      <td>260</td>
      <td>260</td>
      <td>260</td>
      <td>260</td>
      <td>260</td>
      <td>260</td>
      <td>260</td>
      <td>260</td>
      <td>260</td>
      <td>260</td>
    </tr>
    <tr>
      <th>63</th>
      <td>Guizhou</td>
      <td>China</td>
      <td>26.8154</td>
      <td>106.8748</td>
      <td>1</td>
      <td>3</td>
      <td>3</td>
      <td>4</td>
      <td>5</td>
      <td>7</td>
      <td>...</td>
      <td>147</td>
      <td>147</td>
      <td>147</td>
      <td>147</td>
      <td>147</td>
      <td>147</td>
      <td>147</td>
      <td>147</td>
      <td>147</td>
      <td>147</td>
    </tr>
    <tr>
      <th>64</th>
      <td>Hainan</td>
      <td>China</td>
      <td>19.1959</td>
      <td>109.7453</td>
      <td>4</td>
      <td>5</td>
      <td>8</td>
      <td>19</td>
      <td>22</td>
      <td>33</td>
      <td>...</td>
      <td>171</td>
      <td>171</td>
      <td>171</td>
      <td>171</td>
      <td>171</td>
      <td>171</td>
      <td>171</td>
      <td>171</td>
      <td>171</td>
      <td>171</td>
    </tr>
    <tr>
      <th>65</th>
      <td>Hebei</td>
      <td>China</td>
      <td>39.5490</td>
      <td>116.1306</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>8</td>
      <td>13</td>
      <td>18</td>
      <td>...</td>
      <td>365</td>
      <td>365</td>
      <td>365</td>
      <td>365</td>
      <td>365</td>
      <td>365</td>
      <td>365</td>
      <td>365</td>
      <td>367</td>
      <td>367</td>
    </tr>
    <tr>
      <th>66</th>
      <td>Heilongjiang</td>
      <td>China</td>
      <td>47.8620</td>
      <td>127.7615</td>
      <td>0</td>
      <td>2</td>
      <td>4</td>
      <td>9</td>
      <td>15</td>
      <td>21</td>
      <td>...</td>
      <td>948</td>
      <td>948</td>
      <td>948</td>
      <td>948</td>
      <td>948</td>
      <td>948</td>
      <td>948</td>
      <td>948</td>
      <td>948</td>
      <td>948</td>
    </tr>
    <tr>
      <th>67</th>
      <td>Henan</td>
      <td>China</td>
      <td>37.8957</td>
      <td>114.9042</td>
      <td>5</td>
      <td>5</td>
      <td>9</td>
      <td>32</td>
      <td>83</td>
      <td>128</td>
      <td>...</td>
      <td>1281</td>
      <td>1281</td>
      <td>1281</td>
      <td>1281</td>
      <td>1281</td>
      <td>1281</td>
      <td>1281</td>
      <td>1281</td>
      <td>1281</td>
      <td>1281</td>
    </tr>
    <tr>
      <th>68</th>
      <td>Hong Kong</td>
      <td>China</td>
      <td>22.3000</td>
      <td>114.2000</td>
      <td>0</td>
      <td>2</td>
      <td>2</td>
      <td>5</td>
      <td>8</td>
      <td>8</td>
      <td>...</td>
      <td>5108</td>
      <td>5113</td>
      <td>5124</td>
      <td>5132</td>
      <td>5143</td>
      <td>5161</td>
      <td>5169</td>
      <td>5175</td>
      <td>5182</td>
      <td>5193</td>
    </tr>
    <tr>
      <th>69</th>
      <td>Hubei</td>
      <td>China</td>
      <td>30.9756</td>
      <td>112.2707</td>
      <td>444</td>
      <td>444</td>
      <td>549</td>
      <td>761</td>
      <td>1058</td>
      <td>1423</td>
      <td>...</td>
      <td>68139</td>
      <td>68139</td>
      <td>68139</td>
      <td>68139</td>
      <td>68139</td>
      <td>68139</td>
      <td>68139</td>
      <td>68139</td>
      <td>68139</td>
      <td>68139</td>
    </tr>
    <tr>
      <th>70</th>
      <td>Hunan</td>
      <td>China</td>
      <td>27.6104</td>
      <td>111.7088</td>
      <td>4</td>
      <td>9</td>
      <td>24</td>
      <td>43</td>
      <td>69</td>
      <td>100</td>
      <td>...</td>
      <td>1019</td>
      <td>1019</td>
      <td>1019</td>
      <td>1019</td>
      <td>1019</td>
      <td>1019</td>
      <td>1019</td>
      <td>1019</td>
      <td>1019</td>
      <td>1019</td>
    </tr>
    <tr>
      <th>71</th>
      <td>Inner Mongolia</td>
      <td>China</td>
      <td>44.0935</td>
      <td>113.9448</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>7</td>
      <td>7</td>
      <td>11</td>
      <td>...</td>
      <td>266</td>
      <td>268</td>
      <td>268</td>
      <td>268</td>
      <td>268</td>
      <td>268</td>
      <td>268</td>
      <td>268</td>
      <td>269</td>
      <td>270</td>
    </tr>
    <tr>
      <th>72</th>
      <td>Jiangsu</td>
      <td>China</td>
      <td>32.9711</td>
      <td>119.4550</td>
      <td>1</td>
      <td>5</td>
      <td>9</td>
      <td>18</td>
      <td>33</td>
      <td>47</td>
      <td>...</td>
      <td>666</td>
      <td>667</td>
      <td>667</td>
      <td>667</td>
      <td>667</td>
      <td>667</td>
      <td>667</td>
      <td>667</td>
      <td>667</td>
      <td>667</td>
    </tr>
    <tr>
      <th>73</th>
      <td>Jiangxi</td>
      <td>China</td>
      <td>27.6140</td>
      <td>115.7221</td>
      <td>2</td>
      <td>7</td>
      <td>18</td>
      <td>18</td>
      <td>36</td>
      <td>72</td>
      <td>...</td>
      <td>935</td>
      <td>935</td>
      <td>935</td>
      <td>935</td>
      <td>935</td>
      <td>935</td>
      <td>935</td>
      <td>935</td>
      <td>935</td>
      <td>935</td>
    </tr>
    <tr>
      <th>74</th>
      <td>Jilin</td>
      <td>China</td>
      <td>43.6661</td>
      <td>126.1923</td>
      <td>0</td>
      <td>1</td>
      <td>3</td>
      <td>4</td>
      <td>4</td>
      <td>6</td>
      <td>...</td>
      <td>157</td>
      <td>157</td>
      <td>157</td>
      <td>157</td>
      <td>157</td>
      <td>157</td>
      <td>157</td>
      <td>157</td>
      <td>157</td>
      <td>157</td>
    </tr>
    <tr>
      <th>75</th>
      <td>Liaoning</td>
      <td>China</td>
      <td>41.2956</td>
      <td>122.6085</td>
      <td>2</td>
      <td>3</td>
      <td>4</td>
      <td>17</td>
      <td>21</td>
      <td>27</td>
      <td>...</td>
      <td>271</td>
      <td>271</td>
      <td>271</td>
      <td>271</td>
      <td>271</td>
      <td>273</td>
      <td>275</td>
      <td>276</td>
      <td>280</td>
      <td>280</td>
    </tr>
    <tr>
      <th>76</th>
      <td>Macau</td>
      <td>China</td>
      <td>22.1667</td>
      <td>113.5500</td>
      <td>1</td>
      <td>2</td>
      <td>2</td>
      <td>2</td>
      <td>5</td>
      <td>6</td>
      <td>...</td>
      <td>46</td>
      <td>46</td>
      <td>46</td>
      <td>46</td>
      <td>46</td>
      <td>46</td>
      <td>46</td>
      <td>46</td>
      <td>46</td>
      <td>46</td>
    </tr>
    <tr>
      <th>77</th>
      <td>Ningxia</td>
      <td>China</td>
      <td>37.2692</td>
      <td>106.1655</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
      <td>3</td>
      <td>4</td>
      <td>7</td>
      <td>...</td>
      <td>75</td>
      <td>75</td>
      <td>75</td>
      <td>75</td>
      <td>75</td>
      <td>75</td>
      <td>75</td>
      <td>75</td>
      <td>75</td>
      <td>75</td>
    </tr>
    <tr>
      <th>78</th>
      <td>Qinghai</td>
      <td>China</td>
      <td>35.7452</td>
      <td>95.9956</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>6</td>
      <td>...</td>
      <td>18</td>
      <td>18</td>
      <td>18</td>
      <td>18</td>
      <td>18</td>
      <td>18</td>
      <td>18</td>
      <td>18</td>
      <td>18</td>
      <td>18</td>
    </tr>
    <tr>
      <th>79</th>
      <td>Shaanxi</td>
      <td>China</td>
      <td>35.1917</td>
      <td>108.8701</td>
      <td>0</td>
      <td>3</td>
      <td>5</td>
      <td>15</td>
      <td>22</td>
      <td>35</td>
      <td>...</td>
      <td>413</td>
      <td>413</td>
      <td>414</td>
      <td>414</td>
      <td>414</td>
      <td>425</td>
      <td>428</td>
      <td>428</td>
      <td>428</td>
      <td>428</td>
    </tr>
    <tr>
      <th>80</th>
      <td>Shandong</td>
      <td>China</td>
      <td>36.3427</td>
      <td>118.1498</td>
      <td>2</td>
      <td>6</td>
      <td>15</td>
      <td>27</td>
      <td>46</td>
      <td>75</td>
      <td>...</td>
      <td>832</td>
      <td>832</td>
      <td>832</td>
      <td>832</td>
      <td>832</td>
      <td>832</td>
      <td>832</td>
      <td>832</td>
      <td>838</td>
      <td>838</td>
    </tr>
    <tr>
      <th>81</th>
      <td>Shanghai</td>
      <td>China</td>
      <td>31.2020</td>
      <td>121.4491</td>
      <td>9</td>
      <td>16</td>
      <td>20</td>
      <td>33</td>
      <td>40</td>
      <td>53</td>
      <td>...</td>
      <td>1012</td>
      <td>1022</td>
      <td>1024</td>
      <td>1025</td>
      <td>1030</td>
      <td>1036</td>
      <td>1038</td>
      <td>1048</td>
      <td>1053</td>
      <td>1056</td>
    </tr>
    <tr>
      <th>82</th>
      <td>Shanxi</td>
      <td>China</td>
      <td>37.5777</td>
      <td>112.2922</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>6</td>
      <td>9</td>
      <td>13</td>
      <td>...</td>
      <td>204</td>
      <td>205</td>
      <td>205</td>
      <td>206</td>
      <td>206</td>
      <td>206</td>
      <td>206</td>
      <td>206</td>
      <td>208</td>
      <td>208</td>
    </tr>
    <tr>
      <th>83</th>
      <td>Sichuan</td>
      <td>China</td>
      <td>30.6171</td>
      <td>102.7103</td>
      <td>5</td>
      <td>8</td>
      <td>15</td>
      <td>28</td>
      <td>44</td>
      <td>69</td>
      <td>...</td>
      <td>701</td>
      <td>704</td>
      <td>707</td>
      <td>710</td>
      <td>713</td>
      <td>713</td>
      <td>718</td>
      <td>721</td>
      <td>722</td>
      <td>723</td>
    </tr>
    <tr>
      <th>84</th>
      <td>Tianjin</td>
      <td>China</td>
      <td>39.3054</td>
      <td>117.3230</td>
      <td>4</td>
      <td>4</td>
      <td>8</td>
      <td>10</td>
      <td>14</td>
      <td>23</td>
      <td>...</td>
      <td>241</td>
      <td>241</td>
      <td>242</td>
      <td>242</td>
      <td>242</td>
      <td>242</td>
      <td>244</td>
      <td>244</td>
      <td>245</td>
      <td>245</td>
    </tr>
    <tr>
      <th>85</th>
      <td>Tibet</td>
      <td>China</td>
      <td>31.6927</td>
      <td>88.0924</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>86</th>
      <td>Xinjiang</td>
      <td>China</td>
      <td>41.1129</td>
      <td>85.2401</td>
      <td>0</td>
      <td>2</td>
      <td>2</td>
      <td>3</td>
      <td>4</td>
      <td>5</td>
      <td>...</td>
      <td>902</td>
      <td>902</td>
      <td>902</td>
      <td>902</td>
      <td>902</td>
      <td>902</td>
      <td>902</td>
      <td>902</td>
      <td>902</td>
      <td>902</td>
    </tr>
    <tr>
      <th>87</th>
      <td>Yunnan</td>
      <td>China</td>
      <td>24.9740</td>
      <td>101.4870</td>
      <td>1</td>
      <td>2</td>
      <td>5</td>
      <td>11</td>
      <td>16</td>
      <td>26</td>
      <td>...</td>
      <td>211</td>
      <td>211</td>
      <td>211</td>
      <td>211</td>
      <td>211</td>
      <td>211</td>
      <td>211</td>
      <td>211</td>
      <td>211</td>
      <td>211</td>
    </tr>
    <tr>
      <th>88</th>
      <td>Zhejiang</td>
      <td>China</td>
      <td>29.1832</td>
      <td>120.0934</td>
      <td>10</td>
      <td>27</td>
      <td>43</td>
      <td>62</td>
      <td>104</td>
      <td>128</td>
      <td>...</td>
      <td>1282</td>
      <td>1282</td>
      <td>1282</td>
      <td>1282</td>
      <td>1283</td>
      <td>1283</td>
      <td>1283</td>
      <td>1283</td>
      <td>1283</td>
      <td>1283</td>
    </tr>
  </tbody>
</table>
<p>33 rows × 269 columns</p>
</div>


As the listing of states and provinces is very arbitrary (maybe in the beginning of the pandemic 
a more detailed view on China was useful), I decided for a compromise.
I won't display any regions just one number for one country.
For displaying this on the world map, I decided to use the coordinates of the province/state with the most cases.
For Example, for China this will be Hubei, for France it will be mainland France, as all islands and oversea territories have less cases.
Therefore, we group by 'Country/Region' and then select the row where the maximum was recorded on the last day (`case_columns[-1]`): 


```python
idx = df.groupby('Country/Region')[case_columns[-1]].transform(max) == df[case_columns[-1]]
```

We get one row index per country which we use to generate a dataframe with the first for columns (province, country name, gps coordinates):
```python
coord_df = df.loc[idx,df.columns[0:4]]
```
To validate our operation we take a look at China and see as expected, that Hubei was chosen as epicentre of the pandemic in China:

```python
coord_df[coord_df['Country/Region']=='China']
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
      <th>Province/State</th>
      <th>Country/Region</th>
      <th>Lat</th>
      <th>Long</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>69</th>
      <td>Hubei</td>
      <td>China</td>
      <td>30.9756</td>
      <td>112.2707</td>
    </tr>
  </tbody>
</table>
</div>

For the case number, we use the sum over all provinces/states, again using groupby:

```python
df = df.groupby('Country/Region')[case_columns].agg(sum)
df
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
      <th>1/22/20</th>
      <th>1/23/20</th>
      <th>1/24/20</th>
      <th>1/25/20</th>
      <th>1/26/20</th>
      <th>1/27/20</th>
      <th>1/28/20</th>
      <th>1/29/20</th>
      <th>1/30/20</th>
      <th>1/31/20</th>
      <th>...</th>
      <th>10/3/20</th>
      <th>10/4/20</th>
      <th>10/5/20</th>
      <th>10/6/20</th>
      <th>10/7/20</th>
      <th>10/8/20</th>
      <th>10/9/20</th>
      <th>10/10/20</th>
      <th>10/11/20</th>
      <th>10/12/20</th>
    </tr>
    <tr>
      <th>Country/Region</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Afghanistan</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>39297</td>
      <td>39341</td>
      <td>39422</td>
      <td>39486</td>
      <td>39548</td>
      <td>39616</td>
      <td>39693</td>
      <td>39703</td>
      <td>39799</td>
      <td>39870</td>
    </tr>
    <tr>
      <th>Albania</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>14117</td>
      <td>14266</td>
      <td>14410</td>
      <td>14568</td>
      <td>14730</td>
      <td>14899</td>
      <td>15066</td>
      <td>15231</td>
      <td>15399</td>
      <td>15570</td>
    </tr>
    <tr>
      <th>Algeria</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>51995</td>
      <td>52136</td>
      <td>52270</td>
      <td>52399</td>
      <td>52520</td>
      <td>52658</td>
      <td>52804</td>
      <td>52940</td>
      <td>53072</td>
      <td>53325</td>
    </tr>
    <tr>
      <th>Andorra</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>2110</td>
      <td>2110</td>
      <td>2370</td>
      <td>2370</td>
      <td>2568</td>
      <td>2568</td>
      <td>2696</td>
      <td>2696</td>
      <td>2696</td>
      <td>2995</td>
    </tr>
    <tr>
      <th>Angola</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>5370</td>
      <td>5402</td>
      <td>5530</td>
      <td>5725</td>
      <td>5725</td>
      <td>5958</td>
      <td>6031</td>
      <td>6246</td>
      <td>6366</td>
      <td>6488</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>West Bank and Gaza</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>41078</td>
      <td>41498</td>
      <td>41957</td>
      <td>42432</td>
      <td>42840</td>
      <td>43256</td>
      <td>43664</td>
      <td>43945</td>
      <td>44299</td>
      <td>44684</td>
    </tr>
    <tr>
      <th>Western Sahara</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>10</td>
      <td>10</td>
      <td>10</td>
      <td>10</td>
      <td>10</td>
      <td>10</td>
      <td>10</td>
      <td>10</td>
      <td>10</td>
      <td>10</td>
    </tr>
    <tr>
      <th>Yemen</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>2041</td>
      <td>2041</td>
      <td>2041</td>
      <td>2047</td>
      <td>2049</td>
      <td>2050</td>
      <td>2051</td>
      <td>2051</td>
      <td>2052</td>
      <td>2052</td>
    </tr>
    <tr>
      <th>Zambia</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>14974</td>
      <td>15052</td>
      <td>15089</td>
      <td>15170</td>
      <td>15224</td>
      <td>15301</td>
      <td>15339</td>
      <td>15415</td>
      <td>15458</td>
      <td>15549</td>
    </tr>
    <tr>
      <th>Zimbabwe</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>7885</td>
      <td>7888</td>
      <td>7898</td>
      <td>7915</td>
      <td>7919</td>
      <td>7951</td>
      <td>7994</td>
      <td>8010</td>
      <td>8011</td>
      <td>8021</td>
    </tr>
  </tbody>
</table>
<p>189 rows × 265 columns</p>
</div>


To calculate the relative number of cases per persons in a country we need the population numbers.
The UN provides some CSV data for [download](https://population.un.org/wpp/Download/Archive/CSV/).
Again, to transform the [data](https://github.com/weichslgartner/covid_dashboard/blob/master/data/wpp_population.csv) to a format we can use, we have to do some processing.
The procedure can be found in a separate [page/notebook](world_population.md).
To prevent "Unnamed" columns we use `index_col=0`. 
This results in a data frame with the country name as index.
```python
df_population = pd.read_csv('data/population.csv',index_col=0)
df_population
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
      <th>Country/Region</th>
      <th>Population</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Italy</th>
      <td>Italy</td>
      <td>60421760</td>
    </tr>
    <tr>
      <th>Portugal</th>
      <td>Portugal</td>
      <td>10283822</td>
    </tr>
    <tr>
      <th>World</th>
      <td>World</td>
      <td>7594270356</td>
    </tr>
    <tr>
      <th>Rwanda</th>
      <td>Rwanda</td>
      <td>12301939</td>
    </tr>
    <tr>
      <th>Bulgaria</th>
      <td>Bulgaria</td>
      <td>7025037</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>Diamond Princess</th>
      <td>Diamond Princess</td>
      <td>3600</td>
    </tr>
    <tr>
      <th>Holy See</th>
      <td>Holy See</td>
      <td>825</td>
    </tr>
    <tr>
      <th>Taiwan*</th>
      <td>Taiwan*</td>
      <td>23780000</td>
    </tr>
    <tr>
      <th>Western Sahara</th>
      <td>Western Sahara</td>
      <td>595060</td>
    </tr>
    <tr>
      <th>MS Zaandam</th>
      <td>MS Zaandam</td>
      <td>1829</td>
    </tr>
  </tbody>
</table>
<p>268 rows × 2 columns</p>
</div>


As the countries are the same now we can merge on the index:

```python
df_w_pop = df.merge(df_population, left_index=True,right_index=True)
df_w_pop
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
      <th>1/22/20</th>
      <th>1/23/20</th>
      <th>1/24/20</th>
      <th>1/25/20</th>
      <th>1/26/20</th>
      <th>1/27/20</th>
      <th>1/28/20</th>
      <th>1/29/20</th>
      <th>1/30/20</th>
      <th>1/31/20</th>
      <th>...</th>
      <th>10/5/20</th>
      <th>10/6/20</th>
      <th>10/7/20</th>
      <th>10/8/20</th>
      <th>10/9/20</th>
      <th>10/10/20</th>
      <th>10/11/20</th>
      <th>10/12/20</th>
      <th>Country/Region</th>
      <th>Population</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Afghanistan</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>39422</td>
      <td>39486</td>
      <td>39548</td>
      <td>39616</td>
      <td>39693</td>
      <td>39703</td>
      <td>39799</td>
      <td>39870</td>
      <td>Afghanistan</td>
      <td>37172386</td>
    </tr>
    <tr>
      <th>Albania</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>14410</td>
      <td>14568</td>
      <td>14730</td>
      <td>14899</td>
      <td>15066</td>
      <td>15231</td>
      <td>15399</td>
      <td>15570</td>
      <td>Albania</td>
      <td>2866376</td>
    </tr>
    <tr>
      <th>Algeria</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>52270</td>
      <td>52399</td>
      <td>52520</td>
      <td>52658</td>
      <td>52804</td>
      <td>52940</td>
      <td>53072</td>
      <td>53325</td>
      <td>Algeria</td>
      <td>42228429</td>
    </tr>
    <tr>
      <th>Andorra</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>2370</td>
      <td>2370</td>
      <td>2568</td>
      <td>2568</td>
      <td>2696</td>
      <td>2696</td>
      <td>2696</td>
      <td>2995</td>
      <td>Andorra</td>
      <td>77006</td>
    </tr>
    <tr>
      <th>Angola</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>5530</td>
      <td>5725</td>
      <td>5725</td>
      <td>5958</td>
      <td>6031</td>
      <td>6246</td>
      <td>6366</td>
      <td>6488</td>
      <td>Angola</td>
      <td>30809762</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>West Bank and Gaza</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>41957</td>
      <td>42432</td>
      <td>42840</td>
      <td>43256</td>
      <td>43664</td>
      <td>43945</td>
      <td>44299</td>
      <td>44684</td>
      <td>West Bank and Gaza</td>
      <td>4569087</td>
    </tr>
    <tr>
      <th>Western Sahara</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>10</td>
      <td>10</td>
      <td>10</td>
      <td>10</td>
      <td>10</td>
      <td>10</td>
      <td>10</td>
      <td>10</td>
      <td>Western Sahara</td>
      <td>595060</td>
    </tr>
    <tr>
      <th>Yemen</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>2041</td>
      <td>2047</td>
      <td>2049</td>
      <td>2050</td>
      <td>2051</td>
      <td>2051</td>
      <td>2052</td>
      <td>2052</td>
      <td>Yemen</td>
      <td>28498687</td>
    </tr>
    <tr>
      <th>Zambia</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>15089</td>
      <td>15170</td>
      <td>15224</td>
      <td>15301</td>
      <td>15339</td>
      <td>15415</td>
      <td>15458</td>
      <td>15549</td>
      <td>Zambia</td>
      <td>17351822</td>
    </tr>
    <tr>
      <th>Zimbabwe</th>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>...</td>
      <td>7898</td>
      <td>7915</td>
      <td>7919</td>
      <td>7951</td>
      <td>7994</td>
      <td>8010</td>
      <td>8011</td>
      <td>8021</td>
      <td>Zimbabwe</td>
      <td>14439018</td>
    </tr>
  </tbody>
</table>
<p>189 rows × 267 columns</p>
</div>


Validate with Germany:

```python
pop_germany = df_w_pop.loc['Germany',['Population']]
pop_germany
```




    Population    82905782
    Name: Germany, dtype: object

Round about 83 millions sounds right.
With this number we can now plot  the cases divided by population (see the changed y axis):

```python
ax = df_w_pop.loc['Germany',case_columns].apply(lambda x: x/pop_germany).plot()
ax.legend(["Confirmed cases Germany per capita"])
```


![png](img/dashboard/output_22_1.png)

And the cases per one million inhabitants:

```python
ax = df_w_pop.loc['Germany',case_columns].apply(lambda x: x/(pop_germany/1e6)).plot()
ax.legend(["Confirmed cases Germany per million"])
```

![png](img/dashboard/output_23_1.png)

This concludes the inspection and pre-procession of the raw data and we can jump into the dashboard creation.

## Doing the Bokeh

Bokeh enables you to create interactive visualization in your browser.
You can create plots, tables, and other widgets to control appearance of the plots.
In the case of our dashboard, we use two plots on the left (one line plot for the cases, and plotting circles in a world map).
Further, we use some tables, buttons, sliders, multi-select lists etc.

These elements are arranged as follows: 

![png](img/dashboard/layout.png)
In Python code, we use the layout function, which takes a list of further layout elements.
Specifically, we use column and row elements.
The first row consists of three columns, where "column 0" contains the two plots.
"Column 1" consists of two tables with html headings and the last columns has all the buttons and widgets for control.
The bottom row's only element is a footer text line:

```python
self.layout = layout([
            row(column(tab_plot, world_map),
                column(top_top_14_new_header, top_top_14_new, top_top_14_cum_header, top_top_14_cum),
                column(refresh_button, radio_button_group_df, radio_button_group_per_capita, plots_button_group,
                       radio_button_group_scale, slider, radio_button_average,
                       multi_select),
                ),
            row(footer)
            ])
```

This footer for example is a simple Div element with HTML inside:

```python
footer = Div(
            text="""Covid-19 Dashboard created by Andreas Weichslgartner in April 2020 with python, bokeh, pandas, 
            numpy, pyproj, and colorcet. Source Code can be found at 
            <a href="https://github.com/weichslgartner/covid_dashboard/">Github</a>.""",
            width=1600, height=10, align='center')
```

The buttons and selections widgets on the right also quite easy to implement.
Just give a list with labels and a paramter which signals what button is active.
Then add a callback function which will be triggered on clicking on the buttons.

```python
        radio_button_group_per_capita = RadioButtonGroup(
            labels=["Total Cases", "Cases per Million"], active=0 if not self.active_per_capita else 1)
        radio_button_group_per_capita.on_click(self.update_capita)
```


The onclick callback function as one argument, the new value of the active button.
In the case of the per_capita button, it is `0` for total numbers and `1` if the per_capita option is activated.
As you see in the function, the current active status is kept in class member variables.
First, I used global variables (which is fine for small bokeh plots), but gets rather ugly for more states.
So, I decided to create a `class Dashboard` and encapsulate all the state variables as class members.
Coming back to the callback function, once we saved the state, we update the table data (`self.generate_table_new()`and `self.generate_table_cumulative()`).
Finally, we update the line plot in the upper left with  the `self.update_data` function.


```python
    def update_capita(self, new):
        """
        callback to change between total and per capita numbers
        :param new: 0 if total, 1 if per capita
        :return:
        """
        if new == 0:
            self.active_per_capita = False  # 'total'
        else:
            self.active_per_capita = True  # 'per_capita'
        self.generate_table_new()
        self.generate_table_cumulative()
        self.update_data('', self.country_list, self.country_list)
```

The second kind of callbacks are onchange functions, like in the case of the multiselect widget:

```python
        multi_select = MultiSelect(title="Option (Multiselect Ctrl+Click):", value=self.country_list,
                                   options=countries, height=500)
        multi_select.on_change('value', self.update_data)
```
Here, the value is a list with the active countries and the callback function has a attribute value, the old value of the list and the new:
```python
    def update_data(self, attr, old, new):
        """
        repaints the plots with an updated country list
        :param attr:
        :param old:
        :param new:
        :return:
        """
        _ = (attr, old)
        self.country_list = new
        self.source.data = self.get_dict_from_df(self.active_df, self.country_list, self.active_prefix.name)
        self.layout.set_select(dict(name=TAB_PANE), dict(tabs=self.generate_plot(self.source).tabs))
```

However, we only use the new value (currently active countries) and discard the other two parameters.
Afterwards, we update the data source of the plot and redraw the complete plot.
The data source is defined as `ColumnDataSource(data=new_dict)`.
In this dict each key is one plot line type and the values of this keys are the data point.
For example, `germany_confirmed_daily_raw` represents the confirmed cases on a daily basis without average.
Based on the active member variables these key values entries are generates by the `self.get_dict_from_df` function.
Normally, this would be enough to update the plot but we might also influence the axis, scaling etc, that's why just replace the old plot with a new one.
To do this we use `layout.set_select` which searches for the element with the given name "TAB_PANE" and then replaces it with a new `tabs` element.
The `tabs` element in our case is the line plot in the upper left with the two tabs daily and cumulative.
You could also select the layout element by iterating through some children layout elements, e.g. `layout.children[0].children[0]`.
I did this in the beginning, but this is rather ugly and once you update your layout you manually have to update to correct child.
Thankfully I discovered  `set_select` and can now search by a unique name of the element.