```python
import pandas as pd
import numpy as np
```

load the data from the covid dataset to see what countries we need


```python
df_hopkins = pd.read_csv('data/confirmed.csv')
```

Load the data for population from the UN/Worldbank.  
We must skip non csv stuff in the first 4 rows:


```python
df_world_bank = pd.read_csv('data/wpp_population.csv', skiprows=4)
```


```python
# take a look at the data
df_world_bank.head()
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
      <th>Indicator Code</th>
      <th>1960</th>
      <th>1961</th>
      <th>1962</th>
      <th>1963</th>
      <th>1964</th>
      <th>1965</th>
      <th>...</th>
      <th>2010</th>
      <th>2011</th>
      <th>2012</th>
      <th>2013</th>
      <th>2014</th>
      <th>2015</th>
      <th>2016</th>
      <th>2017</th>
      <th>2018</th>
      <th>2019</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Aruba</td>
      <td>ABW</td>
      <td>Population, total</td>
      <td>SP.POP.TOTL</td>
      <td>54211.0</td>
      <td>55438.0</td>
      <td>56225.0</td>
      <td>56695.0</td>
      <td>57032.0</td>
      <td>57360.0</td>
      <td>...</td>
      <td>101669.0</td>
      <td>102046.0</td>
      <td>102560.0</td>
      <td>103159.0</td>
      <td>103774.0</td>
      <td>104341.0</td>
      <td>104872.0</td>
      <td>105366.0</td>
      <td>105845.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Afghanistan</td>
      <td>AFG</td>
      <td>Population, total</td>
      <td>SP.POP.TOTL</td>
      <td>8996973.0</td>
      <td>9169410.0</td>
      <td>9351441.0</td>
      <td>9543205.0</td>
      <td>9744781.0</td>
      <td>9956320.0</td>
      <td>...</td>
      <td>29185507.0</td>
      <td>30117413.0</td>
      <td>31161376.0</td>
      <td>32269589.0</td>
      <td>33370794.0</td>
      <td>34413603.0</td>
      <td>35383128.0</td>
      <td>36296400.0</td>
      <td>37172386.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Angola</td>
      <td>AGO</td>
      <td>Population, total</td>
      <td>SP.POP.TOTL</td>
      <td>5454933.0</td>
      <td>5531472.0</td>
      <td>5608539.0</td>
      <td>5679458.0</td>
      <td>5735044.0</td>
      <td>5770570.0</td>
      <td>...</td>
      <td>23356246.0</td>
      <td>24220661.0</td>
      <td>25107931.0</td>
      <td>26015780.0</td>
      <td>26941779.0</td>
      <td>27884381.0</td>
      <td>28842484.0</td>
      <td>29816748.0</td>
      <td>30809762.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Albania</td>
      <td>ALB</td>
      <td>Population, total</td>
      <td>SP.POP.TOTL</td>
      <td>1608800.0</td>
      <td>1659800.0</td>
      <td>1711319.0</td>
      <td>1762621.0</td>
      <td>1814135.0</td>
      <td>1864791.0</td>
      <td>...</td>
      <td>2913021.0</td>
      <td>2905195.0</td>
      <td>2900401.0</td>
      <td>2895092.0</td>
      <td>2889104.0</td>
      <td>2880703.0</td>
      <td>2876101.0</td>
      <td>2873457.0</td>
      <td>2866376.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Andorra</td>
      <td>AND</td>
      <td>Population, total</td>
      <td>SP.POP.TOTL</td>
      <td>13411.0</td>
      <td>14375.0</td>
      <td>15370.0</td>
      <td>16412.0</td>
      <td>17469.0</td>
      <td>18549.0</td>
      <td>...</td>
      <td>84449.0</td>
      <td>83747.0</td>
      <td>82427.0</td>
      <td>80774.0</td>
      <td>79213.0</td>
      <td>78011.0</td>
      <td>77297.0</td>
      <td>77001.0</td>
      <td>77006.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 64 columns</p>
</div>




```python
# list with columns which contain population data
df_world_bank_pop_columns = df_world_bank.columns[4:]
df_world_bank_pop_columns
```




    Index(['1960', '1961', '1962', '1963', '1964', '1965', '1966', '1967', '1968',
           '1969', '1970', '1971', '1972', '1973', '1974', '1975', '1976', '1977',
           '1978', '1979', '1980', '1981', '1982', '1983', '1984', '1985', '1986',
           '1987', '1988', '1989', '1990', '1991', '1992', '1993', '1994', '1995',
           '1996', '1997', '1998', '1999', '2000', '2001', '2002', '2003', '2004',
           '2005', '2006', '2007', '2008', '2009', '2010', '2011', '2012', '2013',
           '2014', '2015', '2016', '2017', '2018', '2019'],
          dtype='object')



We are interested in unique countries only:


```python
hk_countries = set(df_hopkins['Country/Region'].unique())
wb_countries = set(df_world_bank['Country Name'].unique())
```

With python sets we can subtract from each other to find the missing countries:


```python
hk_countries - wb_countries
```




    {'Bahamas',
     'Brunei',
     'Burma',
     'Congo (Brazzaville)',
     'Congo (Kinshasa)',
     'Czechia',
     'Diamond Princess',
     'Egypt',
     'Gambia',
     'Holy See',
     'Iran',
     'Korea, South',
     'Kyrgyzstan',
     'Laos',
     'MS Zaandam',
     'Russia',
     'Saint Kitts and Nevis',
     'Saint Lucia',
     'Saint Vincent and the Grenadines',
     'Slovakia',
     'Syria',
     'Taiwan*',
     'US',
     'Venezuela',
     'Western Sahara',
     'Yemen'}



Get missing data from wikipedia which are not in the dataframe (not recognized as country). 


```python
pop_other_sources = { 'Diamond Princess' : 3600, 
                        'Holy See' : 825, 
                        'Taiwan*' : 23_780_000 ,
                        'Western Sahara' : 595_060,
                        'MS Zaandam' :  (1243 + 586) }
```


```python
pop_other_sources
```




    {'Diamond Princess': 3600,
     'Holy See': 825,
     'Taiwan*': 23780000,
     'Western Sahara': 595060,
     'MS Zaandam': 1829}



For the other countries we generate a dict with the mapping:


```python
hopkins_2_worldbank = { 'Bahamas': 'Bahamas, The', 
                 'Brunei' :  'Brunei Darussalam',  
                 'Burma' : 'Myanmar' ,  
                 'Congo (Brazzaville)': 'Congo, Dem. Rep.' ,
                 'Congo (Kinshasa)':  'Congo, Rep.',
                 'Czechia' :  'Czech Republic', 
                 'Egypt' : 'Egypt, Arab Rep.',
                 'Gambia' :  'Gambia, The',
                 'Iran' : 'Iran, Islamic Rep.',
                 'Korea, South' :'Korea, Rep.',
                 'Kyrgyzstan' : 'Kyrgyz Republic',
                 'Laos' : 'Lao PDR',
                 'Russia' : 'Russian Federation',
                 'Saint Kitts and Nevis' : 'St. Kitts and Nevis',
                 'Saint Lucia' : 'St. Lucia',
                 'Saint Vincent and the Grenadines' :  'St. Vincent and the Grenadines',
                 'Slovakia': 'Slovak Republic',
                 'Syria' :  'Syrian Arab Republic',
                 'US' : 'United States',
                 'Venezuela' :  'Venezuela, RB',
                 'Yemen' : 'Yemen, Rep.'
                } 
```

And create the inverse mapping with a dict comprehension:


```python
worldbank_2_hopkins = {v: k for k, v in hopkins_2_worldbank.items()}
```


```python
# create new stripped down dataframe which onyl contains the country name and the population
df_pop = pd.DataFrame(columns=['Country/Region', 'Population'])
```

Now we iterate over all countries in the UN/Worldbank data and replace the names with the one from the John Hopkins dataset


```python
for c in wb_countries:
    pop_list = df_world_bank[df_world_bank['Country Name'] == c][df_world_bank_pop_columns].dropna(axis = 1).values
    # pop_list is array([]) with pop_list[-1] we get the inner list
    if len(pop_list[-1] > 0):
        if c in worldbank_2_hopkins:
            c = worldbank_2_hopkins[c]
        df_pop.loc[c,'Country/Region'] = c
        df_pop.loc[c,'Population'] = int(pop_list[-1][-1])
```

Finally, we pop in the data for cruise ships and other countries not recognized by the UN:


```python
for k, v in pop_other_sources.items():
    df_pop.loc[k,'Country/Region'] = k
    df_pop.loc[k,'Population'] = v
    
```


```python
df_pop.head()
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
      <th>Curacao</th>
      <td>Curacao</td>
      <td>159800</td>
    </tr>
    <tr>
      <th>Algeria</th>
      <td>Algeria</td>
      <td>42228429</td>
    </tr>
    <tr>
      <th>OECD members</th>
      <td>OECD members</td>
      <td>1303529456</td>
    </tr>
    <tr>
      <th>Denmark</th>
      <td>Denmark</td>
      <td>5793636</td>
    </tr>
    <tr>
      <th>Liechtenstein</th>
      <td>Liechtenstein</td>
      <td>37910</td>
    </tr>
  </tbody>
</table>
</div>



Write it to a csv file and read back to see if the data types are fine.


```python
df_pop.to_csv('data/population.csv')
```


```python
df = pd.read_csv('data/population.csv', index_col=False)
```


```python
df.dtypes
```




    Unnamed: 0        object
    Country/Region    object
    Population         int64
    dtype: object




```python

```
