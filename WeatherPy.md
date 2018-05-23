

```python
# Import Dependencies 
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from citipy import citipy
from datetime import datetime
from random import uniform
import requests
```


```python
# Google AIP key / Open Weather API key
gkey = "AIzaSyDJLIZeQsZvl9JyfqB6u6FW6-U9n41RY1k"
wkey = "dadcd19c1e6e63f5f672fc2085e24ef7"
```


```python
# Create DataFrame
column_names = ["City", "Country"]
cities_df = pd.DataFrame(columns=column_names)
```


```python
for x in range(1750):
    lat, lng = np.random.uniform(low=-90.000, high=90.000), np.random.uniform(low=-180.000, high=180.000)
    city = citipy.nearest_city(lat, lng)
    cities_df = cities_df.append({"City": city.city_name,"Country": city.country_code,}, ignore_index=True)

#Remove duplicate cities
cities_df = cities_df.drop_duplicates(subset='City').reset_index()
cities_df.count()
```




    index      676
    City       676
    Country    676
    dtype: int64




```python
cities_df.head()
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
      <th>index</th>
      <th>City</th>
      <th>Country</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>victoria</td>
      <td>sc</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>ojinaga</td>
      <td>mx</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>saldanha</td>
      <td>za</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>xai-xai</td>
      <td>mz</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>illoqqortoormiut</td>
      <td>gl</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Add API data to DataFrame
column_names = ["City", "Country","Temperature (F)",
                "Latitude","Longitude","Humidity (%)",
                "Cloudiness (%)", "Wind Speed (mph)"]
clean_cities_df = pd.DataFrame(columns=column_names)

# Loop through the list of cities and add the data to the df
url= "http://api.openweathermap.org/data/2.5/weather?"

row_count = 1

print("Beginning Data Retrieval: ")
for index, row in cities_df.iterrows():
    target_city = row["City"]
    target_url = url + "appid=" + wkey + "&units=IMPERIAL" + "&q=" + target_city.replace(" ","+")
    city_data = requests.get(target_url).json()
    if city_data["cod"] == "404":
        print("City not found, skipping...")

    else:
        clean_cities_df.loc[index, "City"] = city_data["name"]
        clean_cities_df.loc[index, "Country"] = city_data["sys"]["country"]
        clean_cities_df.loc[index, "Temperature (F)"] = city_data["main"]["temp"]
        clean_cities_df.loc[index, "Latitude"] = city_data["coord"]["lat"]
        clean_cities_df.loc[index, "Longitude"] = city_data["coord"]["lon"]
        clean_cities_df.loc[index, "Humidity (%)"] = city_data["main"]["humidity"]
        clean_cities_df.loc[index, "Cloudiness (%)"] = city_data["clouds"]["all"]
        clean_cities_df.loc[index, "Wind Speed (mph)"] = city_data["wind"]["speed"]
        
        print("------------------------")
        print("Proceesing: City # " , row_count, ' | ' , city_data["name"], city_data["sys"]["country"])
        print(target_url)
        row_count += 1

```

    Beginning Data Retrieval: 
    ------------------------
    Proceesing: City #  1  |  Victoria BN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=victoria
    ------------------------
    Proceesing: City #  2  |  Ojinaga US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ojinaga
    ------------------------
    Proceesing: City #  3  |  Saldanha PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saldanha
    ------------------------
    Proceesing: City #  4  |  Xai-Xai MZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=xai-xai
    City not found, skipping...
    ------------------------
    Proceesing: City #  5  |  Adrar DZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=adrar
    ------------------------
    Proceesing: City #  6  |  Georgetown GY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=georgetown
    ------------------------
    Proceesing: City #  7  |  Iberia PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=iberia
    ------------------------
    Proceesing: City #  8  |  Ushuaia AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ushuaia
    ------------------------
    Proceesing: City #  9  |  Busselton AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=busselton
    ------------------------
    Proceesing: City #  10  |  Pudozh RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pudozh
    ------------------------
    Proceesing: City #  11  |  Butaritari KI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=butaritari
    ------------------------
    Proceesing: City #  12  |  Puerto Escondido MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=puerto+escondido
    ------------------------
    Proceesing: City #  13  |  Jiuquan CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=jiuquan
    ------------------------
    Proceesing: City #  14  |  Karratha AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=karratha
    ------------------------
    Proceesing: City #  15  |  Ust-Nera RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ust-nera
    ------------------------
    Proceesing: City #  16  |  Shelburne CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=shelburne
    ------------------------
    Proceesing: City #  17  |  Tuatapere NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tuatapere
    ------------------------
    Proceesing: City #  18  |  Ahipara NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ahipara
    ------------------------
    Proceesing: City #  19  |  Yellowknife CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=yellowknife
    ------------------------
    Proceesing: City #  20  |  Dikson RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dikson
    ------------------------
    Proceesing: City #  21  |  Vaini IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vaini
    City not found, skipping...
    ------------------------
    Proceesing: City #  22  |  Hermanus ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hermanus
    ------------------------
    Proceesing: City #  23  |  Faya SA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=faya
    ------------------------
    Proceesing: City #  24  |  Jamestown AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=jamestown
    ------------------------
    Proceesing: City #  25  |  Chuy UY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=chuy
    ------------------------
    Proceesing: City #  26  |  Cape Town ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cape+town
    ------------------------
    Proceesing: City #  27  |  Dongsheng CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dongsheng
    ------------------------
    Proceesing: City #  28  |  Hobart AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hobart
    ------------------------
    Proceesing: City #  29  |  Mataura NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mataura
    ------------------------
    Proceesing: City #  30  |  Salinopolis BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=salinopolis
    ------------------------
    Proceesing: City #  31  |  Zaraza VE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=zaraza
    ------------------------
    Proceesing: City #  32  |  Codrington AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=codrington
    ------------------------
    Proceesing: City #  33  |  Olinda EC
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=olinda
    ------------------------
    Proceesing: City #  34  |  Fairbanks US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=fairbanks
    ------------------------
    Proceesing: City #  35  |  San Cristobal EC
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=san+cristobal
    ------------------------
    Proceesing: City #  36  |  Obzor BG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=obzor
    ------------------------
    Proceesing: City #  37  |  Hithadhoo MV
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hithadhoo
    ------------------------
    Proceesing: City #  38  |  Klaksvik FO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=klaksvik
    ------------------------
    Proceesing: City #  39  |  Hami CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hami
    ------------------------
    Proceesing: City #  40  |  Apatou SR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=apatou
    ------------------------
    Proceesing: City #  41  |  Rikitea PF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=rikitea
    ------------------------
    Proceesing: City #  42  |  Kotel CZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kotel
    ------------------------
    Proceesing: City #  43  |  Tessalit ML
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tessalit
    ------------------------
    Proceesing: City #  44  |  Saint-Philippe CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saint-philippe
    ------------------------
    Proceesing: City #  45  |  Khatanga RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=khatanga
    ------------------------
    Proceesing: City #  46  |  Vostok RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vostok
    ------------------------
    Proceesing: City #  47  |  Puerto Ayora EC
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=puerto+ayora
    ------------------------
    Proceesing: City #  48  |  Mar del Plata AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mar+del+plata
    City not found, skipping...
    ------------------------
    Proceesing: City #  49  |  Kapaa US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kapaa
    ------------------------
    Proceesing: City #  50  |  Bathsheba BB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bathsheba
    ------------------------
    Proceesing: City #  51  |  Channel-Port aux Basques CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=channel-port+aux+basques
    ------------------------
    Proceesing: City #  52  |  Bonavista CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bonavista
    ------------------------
    Proceesing: City #  53  |  Luau CD
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=luau
    ------------------------
    Proceesing: City #  54  |  Kaitangata NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kaitangata
    ------------------------
    Proceesing: City #  55  |  Qaanaaq GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=qaanaaq
    ------------------------
    Proceesing: City #  56  |  Avarua CK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=avarua
    ------------------------
    Proceesing: City #  57  |  Castro CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=castro
    ------------------------
    Proceesing: City #  58  |  Khani GE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=khani
    ------------------------
    Proceesing: City #  59  |  Namatanai PG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=namatanai
    ------------------------
    Proceesing: City #  60  |  Mopti ML
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mopti
    ------------------------
    Proceesing: City #  61  |  Mountain Home US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mountain+home
    ------------------------
    Proceesing: City #  62  |  Albany US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=albany
    ------------------------
    Proceesing: City #  63  |  Zhongxing CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=zhongxing
    City not found, skipping...
    ------------------------
    Proceesing: City #  64  |  Zaozerne UA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=zaozerne
    ------------------------
    Proceesing: City #  65  |  Punta Arenas CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=punta+arenas
    ------------------------
    Proceesing: City #  66  |  San Patricio PY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=san+patricio
    ------------------------
    Proceesing: City #  67  |  Cidreira BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cidreira
    ------------------------
    Proceesing: City #  68  |  Ribeira Grande PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ribeira+grande
    ------------------------
    Proceesing: City #  69  |  Sitka US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sitka
    ------------------------
    Proceesing: City #  70  |  Ha Tinh VN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ha+tinh
    ------------------------
    Proceesing: City #  71  |  Provideniya RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=provideniya
    ------------------------
    Proceesing: City #  72  |  East London ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=east+london
    ------------------------
    Proceesing: City #  73  |  Pevek RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pevek
    ------------------------
    Proceesing: City #  74  |  Peniche PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=peniche
    ------------------------
    Proceesing: City #  75  |  Tasiilaq GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tasiilaq
    ------------------------
    Proceesing: City #  76  |  Saint-Pierre FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saint-pierre
    ------------------------
    Proceesing: City #  77  |  Virginia Beach US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=virginia+beach
    ------------------------
    Proceesing: City #  78  |  Lasa CY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lasa
    City not found, skipping...
    City not found, skipping...
    ------------------------
    Proceesing: City #  79  |  Mahebourg MU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mahebourg
    ------------------------
    Proceesing: City #  80  |  Dumas IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dumas
    ------------------------
    Proceesing: City #  81  |  Venustiano Carranza MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=venustiano+carranza
    ------------------------
    Proceesing: City #  82  |  Saint-Joseph FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saint-joseph
    ------------------------
    Proceesing: City #  83  |  Tiksi RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tiksi
    ------------------------
    Proceesing: City #  84  |  Upernavik GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=upernavik
    ------------------------
    Proceesing: City #  85  |  Caravelas BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=caravelas
    ------------------------
    Proceesing: City #  86  |  Isangel VU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=isangel
    ------------------------
    Proceesing: City #  87  |  Chifeng CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=chifeng
    ------------------------
    Proceesing: City #  88  |  Kosh-Agach RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kosh-agach
    ------------------------
    Proceesing: City #  89  |  Broome GB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=broome
    ------------------------
    Proceesing: City #  90  |  Barrow AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=barrow
    ------------------------
    Proceesing: City #  91  |  Kutoarjo ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kutoarjo
    ------------------------
    Proceesing: City #  92  |  Nador MA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nador
    ------------------------
    Proceesing: City #  93  |  Pisco PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pisco
    ------------------------
    Proceesing: City #  94  |  Kuybysheve UA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kuybysheve
    ------------------------
    Proceesing: City #  95  |  Bluff AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bluff
    ------------------------
    Proceesing: City #  96  |  Ixtapa MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ixtapa
    ------------------------
    Proceesing: City #  97  |  Los Llanos de Aridane ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=los+llanos+de+aridane
    ------------------------
    Proceesing: City #  98  |  Ancud CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ancud
    ------------------------
    Proceesing: City #  99  |  Saskylakh RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saskylakh
    ------------------------
    Proceesing: City #  100  |  Iqaluit CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=iqaluit
    ------------------------
    Proceesing: City #  101  |  Batagay-Alyta RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=batagay-alyta
    ------------------------
    Proceesing: City #  102  |  Mbandaka CD
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mbandaka
    ------------------------
    Proceesing: City #  103  |  Hilo US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hilo
    ------------------------
    Proceesing: City #  104  |  Praia da Vitoria PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=praia+da+vitoria
    City not found, skipping...
    ------------------------
    Proceesing: City #  105  |  Vao EE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vao
    ------------------------
    Proceesing: City #  106  |  Namibe AO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=namibe
    ------------------------
    Proceesing: City #  107  |  Cardoso BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cardoso
    ------------------------
    Proceesing: City #  108  |  Touros BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=touros
    ------------------------
    Proceesing: City #  109  |  Okhotsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=okhotsk
    ------------------------
    Proceesing: City #  110  |  Haines Junction CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=haines+junction
    City not found, skipping...
    ------------------------
    Proceesing: City #  111  |  Burgeo CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=burgeo
    ------------------------
    Proceesing: City #  112  |  Dingle PH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dingle
    ------------------------
    Proceesing: City #  113  |  Narsaq GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=narsaq
    ------------------------
    Proceesing: City #  114  |  Hit IQ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hit
    ------------------------
    Proceesing: City #  115  |  Afua NG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=afua
    ------------------------
    Proceesing: City #  116  |  Katsuura JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=katsuura
    City not found, skipping...
    ------------------------
    Proceesing: City #  117  |  Batticaloa LK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=batticaloa
    City not found, skipping...
    ------------------------
    Proceesing: City #  118  |  Maceio BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=maceio
    ------------------------
    Proceesing: City #  119  |  Thompson CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=thompson
    ------------------------
    Proceesing: City #  120  |  Christchurch NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=christchurch
    ------------------------
    Proceesing: City #  121  |  Mariinskiy Posad RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mariinskiy+posad
    ------------------------
    Proceesing: City #  122  |  Vardo US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vardo
    ------------------------
    Proceesing: City #  123  |  Benghazi LY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=benghazi
    ------------------------
    Proceesing: City #  124  |  Xinmin CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=xinmin
    ------------------------
    Proceesing: City #  125  |  Bethel US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bethel
    ------------------------
    Proceesing: City #  126  |  Kruisfontein ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kruisfontein
    ------------------------
    Proceesing: City #  127  |  Tecoanapa MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tecoanapa
    ------------------------
    Proceesing: City #  128  |  Cayenne GF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cayenne
    ------------------------
    Proceesing: City #  129  |  Hay River CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hay+river
    ------------------------
    Proceesing: City #  130  |  Hasaki JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hasaki
    ------------------------
    Proceesing: City #  131  |  Lagoa PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lagoa
    ------------------------
    Proceesing: City #  132  |  Luchegorsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=luchegorsk
    ------------------------
    Proceesing: City #  133  |  Matara PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=matara
    ------------------------
    Proceesing: City #  134  |  Severo-Kurilsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=severo-kurilsk
    ------------------------
    Proceesing: City #  135  |  Kulhudhuffushi MV
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kulhudhuffushi
    ------------------------
    Proceesing: City #  136  |  Beringovskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=beringovskiy
    ------------------------
    Proceesing: City #  137  |  Mazatlan MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mazatlan
    ------------------------
    Proceesing: City #  138  |  Erdenet MN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=erdenet
    ------------------------
    Proceesing: City #  139  |  Chhabra IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=chhabra
    ------------------------
    Proceesing: City #  140  |  Brae GB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=brae
    ------------------------
    Proceesing: City #  141  |  Nuristan UZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nuristan
    ------------------------
    Proceesing: City #  142  |  Zyryanka RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=zyryanka
    ------------------------
    Proceesing: City #  143  |  The Pas CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=the+pas
    ------------------------
    Proceesing: City #  144  |  College US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=college
    ------------------------
    Proceesing: City #  145  |  Lompoc US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lompoc
    City not found, skipping...
    ------------------------
    Proceesing: City #  146  |  Grand Gaube MU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=grand+gaube
    ------------------------
    Proceesing: City #  147  |  Atuona PF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=atuona
    ------------------------
    Proceesing: City #  148  |  Champerico MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=champerico
    ------------------------
    Proceesing: City #  149  |  Altagracia de Orituco VE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=altagracia+de+orituco
    ------------------------
    Proceesing: City #  150  |  Kodiak US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kodiak
    ------------------------
    Proceesing: City #  151  |  Kachiry KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kachiry
    ------------------------
    Proceesing: City #  152  |  Mehran IR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mehran
    ------------------------
    Proceesing: City #  153  |  Waingapu ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=waingapu
    ------------------------
    Proceesing: City #  154  |  Aklavik CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=aklavik
    ------------------------
    Proceesing: City #  155  |  New Norfolk AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=new+norfolk
    ------------------------
    Proceesing: City #  156  |  Emerald AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=emerald
    ------------------------
    Proceesing: City #  157  |  Ondangwa NA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ondangwa
    ------------------------
    Proceesing: City #  158  |  Ponta do Sol BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ponta+do+sol
    ------------------------
    Proceesing: City #  159  |  Otjiwarongo NA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=otjiwarongo
    ------------------------
    Proceesing: City #  160  |  Komsomolskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=komsomolskiy
    ------------------------
    Proceesing: City #  161  |  Kungurtug RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kungurtug
    ------------------------
    Proceesing: City #  162  |  Tuktoyaktuk CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tuktoyaktuk
    ------------------------
    Proceesing: City #  163  |  Carnarvon ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=carnarvon
    ------------------------
    Proceesing: City #  164  |  Fortuna ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=fortuna
    ------------------------
    Proceesing: City #  165  |  Byron Bay AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=byron+bay
    ------------------------
    Proceesing: City #  166  |  Norman Wells CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=norman+wells
    ------------------------
    Proceesing: City #  167  |  Nishihara JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nishihara
    ------------------------
    Proceesing: City #  168  |  Bredasdorp ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bredasdorp
    ------------------------
    Proceesing: City #  169  |  Abashiri JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=abashiri
    ------------------------
    Proceesing: City #  170  |  Changanacheri IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=changanacheri
    ------------------------
    Proceesing: City #  171  |  Bilopillya UA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bilopillya
    ------------------------
    Proceesing: City #  172  |  Saint George GR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saint+george
    ------------------------
    Proceesing: City #  173  |  Naze NG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=naze
    ------------------------
    Proceesing: City #  174  |  Avera US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=avera
    ------------------------
    Proceesing: City #  175  |  Sao Filipe CV
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sao+filipe
    ------------------------
    Proceesing: City #  176  |  Vanavara RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vanavara
    ------------------------
    Proceesing: City #  177  |  Port Alfred ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port+alfred
    ------------------------
    Proceesing: City #  178  |  Muros ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=muros
    ------------------------
    Proceesing: City #  179  |  Muyezerskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=muyezerskiy
    ------------------------
    Proceesing: City #  180  |  Arraial do Cabo BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=arraial+do+cabo
    ------------------------
    Proceesing: City #  181  |  Faanui PF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=faanui
    ------------------------
    Proceesing: City #  182  |  Paamiut GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=paamiut
    City not found, skipping...
    ------------------------
    Proceesing: City #  183  |  Mocuba MZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mocuba
    ------------------------
    Proceesing: City #  184  |  Bintulu MY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bintulu
    ------------------------
    Proceesing: City #  185  |  Itarema BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=itarema
    ------------------------
    Proceesing: City #  186  |  General Elizardo Aquino PY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=general+elizardo+aquino
    ------------------------
    Proceesing: City #  187  |  Songjianghe CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=songjianghe
    ------------------------
    Proceesing: City #  188  |  Pozo Colorado PY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pozo+colorado
    ------------------------
    Proceesing: City #  189  |  Kaura Namoda NG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kaura+namoda
    ------------------------
    Proceesing: City #  190  |  Oranjemund ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=oranjemund
    ------------------------
    Proceesing: City #  191  |  Gat SN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gat
    ------------------------
    Proceesing: City #  192  |  Margate AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=margate
    ------------------------
    Proceesing: City #  193  |  Cabo San Lucas MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cabo+san+lucas
    ------------------------
    Proceesing: City #  194  |  Hermiston US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hermiston
    ------------------------
    Proceesing: City #  195  |  Ust-Kalmanka RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ust-kalmanka
    ------------------------
    Proceesing: City #  196  |  Erenhot CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=erenhot
    ------------------------
    Proceesing: City #  197  |  Umm Kaddadah SD
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=umm+kaddadah
    ------------------------
    Proceesing: City #  198  |  Henties Bay NA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=henties+bay
    ------------------------
    Proceesing: City #  199  |  Luderitz NA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=luderitz
    City not found, skipping...
    ------------------------
    Proceesing: City #  200  |  Maumere ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=maumere
    ------------------------
    Proceesing: City #  201  |  Soure PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=soure
    ------------------------
    Proceesing: City #  202  |  Sao Joao da Barra BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sao+joao+da+barra
    ------------------------
    Proceesing: City #  203  |  Matagami NG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=matagami
    ------------------------
    Proceesing: City #  204  |  Lebu ET
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lebu
    ------------------------
    Proceesing: City #  205  |  Kholtoson RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kholtoson
    ------------------------
    Proceesing: City #  206  |  Kuusamo FI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kuusamo
    ------------------------
    Proceesing: City #  207  |  Abu Samrah SY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=abu+samrah
    City not found, skipping...
    ------------------------
    Proceesing: City #  208  |  Konigstein DE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=konigstein
    ------------------------
    Proceesing: City #  209  |  Flagstaff US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=flagstaff
    ------------------------
    Proceesing: City #  210  |  Gizo IL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gizo
    City not found, skipping...
    ------------------------
    Proceesing: City #  211  |  Morehead US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=morehead
    ------------------------
    Proceesing: City #  212  |  Ucluelet CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ucluelet
    ------------------------
    Proceesing: City #  213  |  Ambunti PG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ambunti
    ------------------------
    Proceesing: City #  214  |  Baishishan CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=baishishan
    ------------------------
    Proceesing: City #  215  |  Mineiros BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mineiros
    ------------------------
    Proceesing: City #  216  |  Effingham US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=effingham
    City not found, skipping...
    ------------------------
    Proceesing: City #  217  |  Beira ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=beira
    ------------------------
    Proceesing: City #  218  |  Sassandra CI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sassandra
    ------------------------
    Proceesing: City #  219  |  Xuanzhou CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=xuanzhou
    ------------------------
    Proceesing: City #  220  |  Ketchikan US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ketchikan
    ------------------------
    Proceesing: City #  221  |  Pineville US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pineville
    ------------------------
    Proceesing: City #  222  |  Agropoli IT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=agropoli
    ------------------------
    Proceesing: City #  223  |  Novogurovskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=novogurovskiy
    ------------------------
    Proceesing: City #  224  |  Kearney US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kearney
    ------------------------
    Proceesing: City #  225  |  Port-Gentil GA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port-gentil
    ------------------------
    Proceesing: City #  226  |  Gangtok IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gangtok
    ------------------------
    Proceesing: City #  227  |  Bud NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bud
    ------------------------
    Proceesing: City #  228  |  Nikolskoye RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nikolskoye
    ------------------------
    Proceesing: City #  229  |  Shorkot PK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=shorkot
    ------------------------
    Proceesing: City #  230  |  Ust-Kuyga RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ust-kuyga
    ------------------------
    Proceesing: City #  231  |  Msanga TZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=msanga
    ------------------------
    Proceesing: City #  232  |  Port Augusta AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port+augusta
    ------------------------
    Proceesing: City #  233  |  Jalu LY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=jalu
    ------------------------
    Proceesing: City #  234  |  Moron VE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=moron
    ------------------------
    Proceesing: City #  235  |  Vila Franca do Campo PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vila+franca+do+campo
    ------------------------
    Proceesing: City #  236  |  Pakokku MM
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pakokku
    ------------------------
    Proceesing: City #  237  |  Ulladulla AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ulladulla
    ------------------------
    Proceesing: City #  238  |  Vologda RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vologda
    ------------------------
    Proceesing: City #  239  |  Portland US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=portland
    ------------------------
    Proceesing: City #  240  |  Koutsouras GR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=koutsouras
    ------------------------
    Proceesing: City #  241  |  Elblag PL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=elblag
    ------------------------
    Proceesing: City #  242  |  Chapais CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=chapais
    ------------------------
    Proceesing: City #  243  |  Rodez FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=rodez
    ------------------------
    Proceesing: City #  244  |  Evensk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=evensk
    ------------------------
    Proceesing: City #  245  |  Ilulissat GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ilulissat
    ------------------------
    Proceesing: City #  246  |  Yulara AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=yulara
    City not found, skipping...
    ------------------------
    Proceesing: City #  247  |  Kadom RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kadom
    ------------------------
    Proceesing: City #  248  |  Atar MR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=atar
    ------------------------
    Proceesing: City #  249  |  Bubaque GW
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bubaque
    ------------------------
    Proceesing: City #  250  |  Grindavik IS
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=grindavik
    ------------------------
    Proceesing: City #  251  |  Hazorasp UZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hazorasp
    ------------------------
    Proceesing: City #  252  |  Port-Cartier CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port-cartier
    ------------------------
    Proceesing: City #  253  |  Edd ER
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=edd
    ------------------------
    Proceesing: City #  254  |  Flinders AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=flinders
    ------------------------
    Proceesing: City #  255  |  Longkou CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=longkou
    ------------------------
    Proceesing: City #  256  |  Parvatsar IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=parvatsar
    ------------------------
    Proceesing: City #  257  |  Suihua CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=suihua
    ------------------------
    Proceesing: City #  258  |  Lebanon US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lebanon
    ------------------------
    Proceesing: City #  259  |  Camacha PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=camacha
    City not found, skipping...
    ------------------------
    Proceesing: City #  260  |  Safranbolu TR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=safranbolu
    ------------------------
    Proceesing: City #  261  |  Khuchni RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=khuchni
    ------------------------
    Proceesing: City #  262  |  Lorengau PG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lorengau
    City not found, skipping...
    ------------------------
    Proceesing: City #  263  |  Saint-Augustin FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saint-augustin
    ------------------------
    Proceesing: City #  264  |  Sylva US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sylva
    ------------------------
    Proceesing: City #  265  |  Pauini BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pauini
    ------------------------
    Proceesing: City #  266  |  Alice Springs AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=alice+springs
    ------------------------
    Proceesing: City #  267  |  Catia La Mar VE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=catia+la+mar
    ------------------------
    Proceesing: City #  268  |  Ostrovnoy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ostrovnoy
    ------------------------
    Proceesing: City #  269  |  Kattivakkam IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kattivakkam
    ------------------------
    Proceesing: City #  270  |  Saint-Paul FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saint-paul
    ------------------------
    Proceesing: City #  271  |  Torbay CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=torbay
    ------------------------
    Proceesing: City #  272  |  Vanimo PG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vanimo
    ------------------------
    Proceesing: City #  273  |  Linhares BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=linhares
    ------------------------
    Proceesing: City #  274  |  Planadas CO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=planadas
    City not found, skipping...
    ------------------------
    Proceesing: City #  275  |  Vila do Maio CV
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vila+do+maio
    City not found, skipping...
    ------------------------
    Proceesing: City #  276  |  Lavrentiya RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lavrentiya
    ------------------------
    Proceesing: City #  277  |  Shchors UA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=shchors
    ------------------------
    Proceesing: City #  278  |  Daura NG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=daura
    ------------------------
    Proceesing: City #  279  |  Mehamn NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mehamn
    ------------------------
    Proceesing: City #  280  |  Victor Harbor AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=victor+harbor
    ------------------------
    Proceesing: City #  281  |  Sinnamary GF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sinnamary
    ------------------------
    Proceesing: City #  282  |  Kahului US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kahului
    ------------------------
    Proceesing: City #  283  |  Bambous Virieux MU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bambous+virieux
    ------------------------
    Proceesing: City #  284  |  Shu KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=shu
    ------------------------
    Proceesing: City #  285  |  Doha QA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=doha
    ------------------------
    Proceesing: City #  286  |  Saint-Georges CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saint-georges
    ------------------------
    Proceesing: City #  287  |  San Quintin PH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=san+quintin
    ------------------------
    Proceesing: City #  288  |  Araouane ML
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=araouane
    ------------------------
    Proceesing: City #  289  |  Harer US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=harer
    ------------------------
    Proceesing: City #  290  |  Anchorage US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=anchorage
    ------------------------
    Proceesing: City #  291  |  Shetpe KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=shetpe
    ------------------------
    Proceesing: City #  292  |  Paltinis RO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=paltinis
    ------------------------
    Proceesing: City #  293  |  Aldan RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=aldan
    ------------------------
    Proceesing: City #  294  |  Mayumba GA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mayumba
    ------------------------
    Proceesing: City #  295  |  Comodoro Rivadavia AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=comodoro+rivadavia
    ------------------------
    Proceesing: City #  296  |  Tautira PF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tautira
    ------------------------
    Proceesing: City #  297  |  Bunia CD
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bunia
    ------------------------
    Proceesing: City #  298  |  Ossora RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ossora
    ------------------------
    Proceesing: City #  299  |  Geraldton CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=geraldton
    ------------------------
    Proceesing: City #  300  |  Poum MK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=poum
    ------------------------
    Proceesing: City #  301  |  Nome US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nome
    ------------------------
    Proceesing: City #  302  |  Axim GH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=axim
    ------------------------
    Proceesing: City #  303  |  Beatrice US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=beatrice
    ------------------------
    Proceesing: City #  304  |  Sao Jose da Coroa Grande BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sao+jose+da+coroa+grande
    ------------------------
    Proceesing: City #  305  |  Loralai PK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=loralai
    ------------------------
    Proceesing: City #  306  |  Inirida CO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=inirida
    ------------------------
    Proceesing: City #  307  |  Alamosa US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=alamosa
    ------------------------
    Proceesing: City #  308  |  Soe EE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=soe
    ------------------------
    Proceesing: City #  309  |  Cherskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cherskiy
    ------------------------
    Proceesing: City #  310  |  Pyaozerskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pyaozerskiy
    ------------------------
    Proceesing: City #  311  |  Sobolevo RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sobolevo
    ------------------------
    Proceesing: City #  312  |  Damara CF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=damara
    ------------------------
    Proceesing: City #  313  |  Puntarenas CR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=puntarenas
    ------------------------
    Proceesing: City #  314  |  Bara NG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bara
    ------------------------
    Proceesing: City #  315  |  Dunn US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dunn
    ------------------------
    Proceesing: City #  316  |  Mogadishu SO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mogadishu
    ------------------------
    Proceesing: City #  317  |  Kaabong UG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kaabong
    ------------------------
    Proceesing: City #  318  |  Hamilton BM
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hamilton
    ------------------------
    Proceesing: City #  319  |  Houma CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=houma
    ------------------------
    Proceesing: City #  320  |  Maningrida AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=maningrida
    ------------------------
    Proceesing: City #  321  |  Nanortalik GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nanortalik
    ------------------------
    Proceesing: City #  322  |  Alofi NU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=alofi
    ------------------------
    Proceesing: City #  323  |  Hovd NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hovd
    City not found, skipping...
    ------------------------
    Proceesing: City #  324  |  Parvatipuram IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=parvatipuram
    ------------------------
    Proceesing: City #  325  |  Anadyr RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=anadyr
    City not found, skipping...
    ------------------------
    Proceesing: City #  326  |  Lahij YE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lahij
    ------------------------
    Proceesing: City #  327  |  Vytegra RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vytegra
    ------------------------
    Proceesing: City #  328  |  Aguimes ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=aguimes
    ------------------------
    Proceesing: City #  329  |  Korem ET
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=korem
    ------------------------
    Proceesing: City #  330  |  Norden DE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=norden
    ------------------------
    Proceesing: City #  331  |  Kodinar IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kodinar
    ------------------------
    Proceesing: City #  332  |  Amazar RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=amazar
    ------------------------
    Proceesing: City #  333  |  Makakilo City US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=makakilo+city
    City not found, skipping...
    ------------------------
    Proceesing: City #  334  |  Todos Santos MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=todos+santos
    ------------------------
    Proceesing: City #  335  |  Beloha MG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=beloha
    City not found, skipping...
    ------------------------
    Proceesing: City #  336  |  Nichinan JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nichinan
    ------------------------
    Proceesing: City #  337  |  Nueve de Julio AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nueve+de+julio
    City not found, skipping...
    ------------------------
    Proceesing: City #  338  |  Kargopol RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kargopol
    ------------------------
    Proceesing: City #  339  |  Lodja CD
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lodja
    ------------------------
    Proceesing: City #  340  |  Inta RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=inta
    City not found, skipping...
    ------------------------
    Proceesing: City #  341  |  Souillac FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=souillac
    ------------------------
    Proceesing: City #  342  |  Nata CY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nata
    ------------------------
    Proceesing: City #  343  |  Tromso NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tromso
    ------------------------
    Proceesing: City #  344  |  San Andres PH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=san+andres
    ------------------------
    Proceesing: City #  345  |  Kaeo NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kaeo
    ------------------------
    Proceesing: City #  346  |  Kolokani ML
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kolokani
    ------------------------
    Proceesing: City #  347  |  Banda Aceh ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=banda+aceh
    ------------------------
    Proceesing: City #  348  |  Te Anau NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=te+anau
    City not found, skipping...
    ------------------------
    Proceesing: City #  349  |  Deputatskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=deputatskiy
    ------------------------
    Proceesing: City #  350  |  Carndonagh IE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=carndonagh
    ------------------------
    Proceesing: City #  351  |  Sri Aman MY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sri+aman
    ------------------------
    Proceesing: City #  352  |  Krasnoarmeysk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=krasnoarmeysk
    ------------------------
    Proceesing: City #  353  |  Abalak NE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=abalak
    ------------------------
    Proceesing: City #  354  |  Solak TR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=solak
    ------------------------
    Proceesing: City #  355  |  Kottagudem IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kottagudem
    ------------------------
    Proceesing: City #  356  |  Madras US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=madras
    ------------------------
    Proceesing: City #  357  |  Biak ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=biak
    ------------------------
    Proceesing: City #  358  |  Khorixas NA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=khorixas
    ------------------------
    Proceesing: City #  359  |  Markala ML
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=markala
    ------------------------
    Proceesing: City #  360  |  Longyearbyen NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=longyearbyen
    ------------------------
    Proceesing: City #  361  |  Amol IR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=amol
    ------------------------
    Proceesing: City #  362  |  Vestmanna FO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vestmanna
    ------------------------
    Proceesing: City #  363  |  Chokurdakh RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=chokurdakh
    ------------------------
    Proceesing: City #  364  |  Harper US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=harper
    City not found, skipping...
    ------------------------
    Proceesing: City #  365  |  Dzilam Gonzalez MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dzilam+gonzalez
    ------------------------
    Proceesing: City #  366  |  Waddan LY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=waddan
    ------------------------
    Proceesing: City #  367  |  Paris FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=paris
    ------------------------
    Proceesing: City #  368  |  Golden CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=golden
    ------------------------
    Proceesing: City #  369  |  Rupnagar IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=rupnagar
    ------------------------
    Proceesing: City #  370  |  Dhidhdhoo MV
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dhidhdhoo
    ------------------------
    Proceesing: City #  371  |  Igarka RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=igarka
    ------------------------
    Proceesing: City #  372  |  Parana BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=parana
    ------------------------
    Proceesing: City #  373  |  Kommunisticheskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kommunisticheskiy
    ------------------------
    Proceesing: City #  374  |  Bloomfield US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bloomfield
    ------------------------
    Proceesing: City #  375  |  Kavarna BG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kavarna
    City not found, skipping...
    ------------------------
    Proceesing: City #  376  |  Guerrero Negro MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=guerrero+negro
    ------------------------
    Proceesing: City #  377  |  Eydhafushi MV
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=eydhafushi
    ------------------------
    Proceesing: City #  378  |  San Javier ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=san+javier
    City not found, skipping...
    ------------------------
    Proceesing: City #  379  |  Cilegon ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cilegon
    ------------------------
    Proceesing: City #  380  |  Coquimbo CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=coquimbo
    ------------------------
    Proceesing: City #  381  |  Qaqortoq GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=qaqortoq
    ------------------------
    Proceesing: City #  382  |  Lafia NG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lafia
    ------------------------
    Proceesing: City #  383  |  Ierissos GR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ierissos
    ------------------------
    Proceesing: City #  384  |  Koslan RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=koslan
    ------------------------
    Proceesing: City #  385  |  El Alto BO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=el+alto
    ------------------------
    Proceesing: City #  386  |  Mangrol IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mangrol
    ------------------------
    Proceesing: City #  387  |  Coolum Beach AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=coolum+beach
    ------------------------
    Proceesing: City #  388  |  Purpe RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=purpe
    ------------------------
    Proceesing: City #  389  |  Lobito AO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lobito
    City not found, skipping...
    ------------------------
    Proceesing: City #  390  |  Gao ML
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gao
    ------------------------
    Proceesing: City #  391  |  Miles City US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=miles+city
    ------------------------
    Proceesing: City #  392  |  San Martin US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=san+martin
    ------------------------
    Proceesing: City #  393  |  Kamaishi JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kamaishi
    ------------------------
    Proceesing: City #  394  |  Ravar IR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ravar
    ------------------------
    Proceesing: City #  395  |  Omboue GA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=omboue
    ------------------------
    Proceesing: City #  396  |  Abyek IR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=abyek
    ------------------------
    Proceesing: City #  397  |  Montauban FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=montauban
    ------------------------
    Proceesing: City #  398  |  Velsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=velsk
    ------------------------
    Proceesing: City #  399  |  Bodden Town KY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bodden+town
    City not found, skipping...
    ------------------------
    Proceesing: City #  400  |  Jaguaribe BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=jaguaribe
    ------------------------
    Proceesing: City #  401  |  Gamba CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gamba
    ------------------------
    Proceesing: City #  402  |  Iquique CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=iquique
    ------------------------
    Proceesing: City #  403  |  Port Hedland AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port+hedland
    ------------------------
    Proceesing: City #  404  |  Kloulklubed PW
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kloulklubed
    ------------------------
    Proceesing: City #  405  |  Airai TL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=airai
    City not found, skipping...
    ------------------------
    Proceesing: City #  406  |  Mitsamiouli KM
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mitsamiouli
    ------------------------
    Proceesing: City #  407  |  Corinto BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=corinto
    ------------------------
    Proceesing: City #  408  |  Batemans Bay AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=batemans+bay
    ------------------------
    Proceesing: City #  409  |  Opole PL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=opole
    ------------------------
    Proceesing: City #  410  |  Port Elizabeth US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port+elizabeth
    ------------------------
    Proceesing: City #  411  |  Saint-Francois FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saint-francois
    ------------------------
    Proceesing: City #  412  |  Pangoa PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pangoa
    City not found, skipping...
    ------------------------
    Proceesing: City #  413  |  Baraki Barak AF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=baraki+barak
    ------------------------
    Proceesing: City #  414  |  Balkhash KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=balkhash
    ------------------------
    Proceesing: City #  415  |  Buraydah SA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=buraydah
    ------------------------
    Proceesing: City #  416  |  Honiara SB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=honiara
    ------------------------
    Proceesing: City #  417  |  Borgustanskaya RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=borgustanskaya
    City not found, skipping...
    ------------------------
    Proceesing: City #  418  |  Tahoua NE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tahoua
    ------------------------
    Proceesing: City #  419  |  Vila Velha BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vila+velha
    ------------------------
    Proceesing: City #  420  |  Nivala FI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nivala
    ------------------------
    Proceesing: City #  421  |  Copiapo CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=copiapo
    ------------------------
    Proceesing: City #  422  |  Havre-Saint-Pierre CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=havre-saint-pierre
    ------------------------
    Proceesing: City #  423  |  Huarmey PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=huarmey
    ------------------------
    Proceesing: City #  424  |  Bandrele YT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bandrele
    ------------------------
    Proceesing: City #  425  |  Puerto Colombia CO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=puerto+colombia
    ------------------------
    Proceesing: City #  426  |  Lebedinyy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lebedinyy
    ------------------------
    Proceesing: City #  427  |  Cetate RO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cetate
    ------------------------
    Proceesing: City #  428  |  Longyan CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=longyan
    ------------------------
    Proceesing: City #  429  |  Puerto Santander VE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=puerto+santander
    ------------------------
    Proceesing: City #  430  |  Fort Collins US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=fort+collins
    ------------------------
    Proceesing: City #  431  |  Panlaitan PH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=panlaitan
    ------------------------
    Proceesing: City #  432  |  Port Blair IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port+blair
    ------------------------
    Proceesing: City #  433  |  Meadow Lake CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=meadow+lake
    ------------------------
    Proceesing: City #  434  |  Beyneu KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=beyneu
    ------------------------
    Proceesing: City #  435  |  Tambilil PH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tambilil
    ------------------------
    Proceesing: City #  436  |  Senneterre CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=senneterre
    ------------------------
    Proceesing: City #  437  |  Hofn IS
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hofn
    ------------------------
    Proceesing: City #  438  |  Northam GB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=northam
    ------------------------
    Proceesing: City #  439  |  Manggar ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=manggar
    ------------------------
    Proceesing: City #  440  |  Qingdao CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=qingdao
    ------------------------
    Proceesing: City #  441  |  Cockburn Town TC
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cockburn+town
    ------------------------
    Proceesing: City #  442  |  Turan RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=turan
    ------------------------
    Proceesing: City #  443  |  Ewa Beach US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ewa+beach
    ------------------------
    Proceesing: City #  444  |  Naron ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=naron
    City not found, skipping...
    ------------------------
    Proceesing: City #  445  |  Sertanopolis BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sertanopolis
    ------------------------
    Proceesing: City #  446  |  Kirovskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kirovskiy
    ------------------------
    Proceesing: City #  447  |  Kutum SD
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kutum
    ------------------------
    Proceesing: City #  448  |  Leningradskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=leningradskiy
    City not found, skipping...
    City not found, skipping...
    ------------------------
    Proceesing: City #  449  |  Coihaique CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=coihaique
    ------------------------
    Proceesing: City #  450  |  Winslow GB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=winslow
    ------------------------
    Proceesing: City #  451  |  Grootfontein NA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=grootfontein
    ------------------------
    Proceesing: City #  452  |  Puri ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=puri
    City not found, skipping...
    ------------------------
    Proceesing: City #  453  |  Garoua Boulai CM
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=garoua+boulai
    ------------------------
    Proceesing: City #  454  |  Marsh Harbour BS
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=marsh+harbour
    ------------------------
    Proceesing: City #  455  |  Baiyin CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=baiyin
    ------------------------
    Proceesing: City #  456  |  Nybro SE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nybro
    ------------------------
    Proceesing: City #  457  |  Kununurra AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kununurra
    ------------------------
    Proceesing: City #  458  |  Gharo PK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gharo
    ------------------------
    Proceesing: City #  459  |  Luanda AO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=luanda
    ------------------------
    Proceesing: City #  460  |  Miandrivazo MG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=miandrivazo
    ------------------------
    Proceesing: City #  461  |  San Luis AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=san+luis
    ------------------------
    Proceesing: City #  462  |  Palmer US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=palmer
    ------------------------
    Proceesing: City #  463  |  Bilibino RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bilibino
    ------------------------
    Proceesing: City #  464  |  Ulagan RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ulagan
    City not found, skipping...
    ------------------------
    Proceesing: City #  465  |  Pringsewu ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pringsewu
    ------------------------
    Proceesing: City #  466  |  Daloa CI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=daloa
    City not found, skipping...
    ------------------------
    Proceesing: City #  467  |  Minna NG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=minna
    ------------------------
    Proceesing: City #  468  |  Carutapera BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=carutapera
    ------------------------
    Proceesing: City #  469  |  Arlit NE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=arlit
    ------------------------
    Proceesing: City #  470  |  Paragominas BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=paragominas
    ------------------------
    Proceesing: City #  471  |  Porto Walter BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=porto+walter
    ------------------------
    Proceesing: City #  472  |  Coruripe BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=coruripe
    ------------------------
    Proceesing: City #  473  |  Beitbridge ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=beitbridge
    ------------------------
    Proceesing: City #  474  |  Carauari BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=carauari
    ------------------------
    Proceesing: City #  475  |  La Sierra CO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=la+sierra
    ------------------------
    Proceesing: City #  476  |  Miass RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=miass
    ------------------------
    Proceesing: City #  477  |  Polson US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=polson
    ------------------------
    Proceesing: City #  478  |  Ayabaca PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ayabaca
    ------------------------
    Proceesing: City #  479  |  Clyde River CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=clyde+river
    ------------------------
    Proceesing: City #  480  |  Benjamin Constant BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=benjamin+constant
    City not found, skipping...
    ------------------------
    Proceesing: City #  481  |  Ust-Shonosha RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ust-shonosha
    ------------------------
    Proceesing: City #  482  |  Xining CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=xining
    ------------------------
    Proceesing: City #  483  |  Bandundu CD
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bandundu
    ------------------------
    Proceesing: City #  484  |  Mabaruma GY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mabaruma
    ------------------------
    Proceesing: City #  485  |  Kristianstad SE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kristianstad
    ------------------------
    Proceesing: City #  486  |  Petropavlovsk-Kamchatskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=petropavlovsk-kamchatskiy
    ------------------------
    Proceesing: City #  487  |  Eureka US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=eureka
    ------------------------
    Proceesing: City #  488  |  Esperance TT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=esperance
    City not found, skipping...
    ------------------------
    Proceesing: City #  489  |  Havelock US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=havelock
    ------------------------
    Proceesing: City #  490  |  Kenai US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kenai
    ------------------------
    Proceesing: City #  491  |  Sorland NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sorland
    ------------------------
    Proceesing: City #  492  |  Tieling CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tieling
    ------------------------
    Proceesing: City #  493  |  Myitkyina MM
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=myitkyina
    City not found, skipping...
    ------------------------
    Proceesing: City #  494  |  Ustka PL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ustka
    City not found, skipping...
    ------------------------
    Proceesing: City #  495  |  Tabuk PH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tabuk
    ------------------------
    Proceesing: City #  496  |  Alyangula AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=alyangula
    ------------------------
    Proceesing: City #  497  |  Fort Nelson CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=fort+nelson
    ------------------------
    Proceesing: City #  498  |  Paramonga PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=paramonga
    City not found, skipping...
    ------------------------
    Proceesing: City #  499  |  Karatau KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=karatau
    ------------------------
    Proceesing: City #  500  |  Sunrise Manor US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sunrise+manor
    ------------------------
    Proceesing: City #  501  |  Berdigestyakh RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=berdigestyakh
    ------------------------
    Proceesing: City #  502  |  Kavieng PG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kavieng
    ------------------------
    Proceesing: City #  503  |  Raudeberg NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=raudeberg
    ------------------------
    Proceesing: City #  504  |  Sambava MG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sambava
    ------------------------
    Proceesing: City #  505  |  Ardakan IR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ardakan
    ------------------------
    Proceesing: City #  506  |  Quatre Cocos MU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=quatre+cocos
    ------------------------
    Proceesing: City #  507  |  Marrakesh MA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=marrakesh
    ------------------------
    Proceesing: City #  508  |  Vadso NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vadso
    ------------------------
    Proceesing: City #  509  |  Siguiri GN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=siguiri
    ------------------------
    Proceesing: City #  510  |  Bowen AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bowen
    ------------------------
    Proceesing: City #  511  |  Natal BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=natal
    ------------------------
    Proceesing: City #  512  |  Qrendi MT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=qrendi
    ------------------------
    Proceesing: City #  513  |  Mandalgovi MN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mandalgovi
    ------------------------
    Proceesing: City #  514  |  Westport IE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=westport
    City not found, skipping...
    ------------------------
    Proceesing: City #  515  |  Santa Fe AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=santa+fe
    ------------------------
    Proceesing: City #  516  |  Buala SB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=buala
    ------------------------
    Proceesing: City #  517  |  Padang ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=padang
    ------------------------
    Proceesing: City #  518  |  Auki NG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=auki
    ------------------------
    Proceesing: City #  519  |  Indramayu ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=indramayu
    ------------------------
    Proceesing: City #  520  |  Taltal CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=taltal
    ------------------------
    Proceesing: City #  521  |  Ayer Keroh MY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ayer+keroh
    ------------------------
    Proceesing: City #  522  |  Portoferraio IT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=portoferraio
    ------------------------
    Proceesing: City #  523  |  Port Moresby PG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port+moresby
    ------------------------
    Proceesing: City #  524  |  Hanusovice CZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hanusovice
    ------------------------
    Proceesing: City #  525  |  Oleksandrivka UA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=oleksandrivka
    ------------------------
    Proceesing: City #  526  |  Meulaboh ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=meulaboh
    ------------------------
    Proceesing: City #  527  |  Ayame CI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ayame
    ------------------------
    Proceesing: City #  528  |  Port Shepstone ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port+shepstone
    ------------------------
    Proceesing: City #  529  |  Kamenskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kamenskiy
    ------------------------
    Proceesing: City #  530  |  Pangnirtung CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pangnirtung
    ------------------------
    Proceesing: City #  531  |  Wellington NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=wellington
    ------------------------
    Proceesing: City #  532  |  Boguchany RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=boguchany
    ------------------------
    Proceesing: City #  533  |  Bereda ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bereda
    City not found, skipping...
    ------------------------
    Proceesing: City #  534  |  Dmytrivka UA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dmytrivka
    ------------------------
    Proceesing: City #  535  |  Tura IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tura
    ------------------------
    Proceesing: City #  536  |  Pacific Grove US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pacific+grove
    ------------------------
    Proceesing: City #  537  |  Chiquimulilla GT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=chiquimulilla
    ------------------------
    Proceesing: City #  538  |  Nantucket US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nantucket
    ------------------------
    Proceesing: City #  539  |  Zhytomyr UA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=zhytomyr
    ------------------------
    Proceesing: City #  540  |  Winnemucca US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=winnemucca
    ------------------------
    Proceesing: City #  541  |  Havoysund NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=havoysund
    ------------------------
    Proceesing: City #  542  |  Opuwo NA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=opuwo
    ------------------------
    Proceesing: City #  543  |  Boende CD
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=boende
    ------------------------
    Proceesing: City #  544  |  Mumbwa ZM
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mumbwa
    ------------------------
    Proceesing: City #  545  |  Qasigiannguit GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=qasigiannguit
    ------------------------
    Proceesing: City #  546  |  Tazovskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tazovskiy
    ------------------------
    Proceesing: City #  547  |  Goderich CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=goderich
    ------------------------
    Proceesing: City #  548  |  Dabakala CI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dabakala
    ------------------------
    Proceesing: City #  549  |  Farmington US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=farmington
    ------------------------
    Proceesing: City #  550  |  Mount Isa AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mount+isa
    ------------------------
    Proceesing: City #  551  |  Voznesenye RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=voznesenye
    ------------------------
    Proceesing: City #  552  |  Yenagoa NG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=yenagoa
    ------------------------
    Proceesing: City #  553  |  Jiangyou CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=jiangyou
    ------------------------
    Proceesing: City #  554  |  Bama CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bama
    City not found, skipping...
    ------------------------
    Proceesing: City #  555  |  Ust-Maya RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ust-maya
    ------------------------
    Proceesing: City #  556  |  Sheregesh RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sheregesh
    ------------------------
    Proceesing: City #  557  |  San Pedro AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=san+pedro
    City not found, skipping...
    ------------------------
    Proceesing: City #  558  |  Salalah OM
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=salalah
    City not found, skipping...
    ------------------------
    Proceesing: City #  559  |  Gaozhou CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gaozhou
    ------------------------
    Proceesing: City #  560  |  Marsabit KE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=marsabit
    ------------------------
    Proceesing: City #  561  |  Feicheng CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=feicheng
    ------------------------
    Proceesing: City #  562  |  Buchanan LR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=buchanan
    ------------------------
    Proceesing: City #  563  |  Quelimane MZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=quelimane
    ------------------------
    Proceesing: City #  564  |  Dali CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dali
    ------------------------
    Proceesing: City #  565  |  Rabo de Peixe PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=rabo+de+peixe
    City not found, skipping...
    City not found, skipping...
    ------------------------
    Proceesing: City #  566  |  Kunda EE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kunda
    City not found, skipping...
    ------------------------
    Proceesing: City #  567  |  Porto Novo BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=porto+novo
    ------------------------
    Proceesing: City #  568  |  Laguna MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=laguna
    ------------------------
    Proceesing: City #  569  |  Blackwater GB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=blackwater
    ------------------------
    Proceesing: City #  570  |  Seoul KR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=seoul
    ------------------------
    Proceesing: City #  571  |  Shingu JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=shingu
    ------------------------
    Proceesing: City #  572  |  Sur OM
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sur
    ------------------------
    Proceesing: City #  573  |  Mount Gambier AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mount+gambier
    City not found, skipping...
    ------------------------
    Proceesing: City #  574  |  Saint Anthony US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saint+anthony
    ------------------------
    Proceesing: City #  575  |  Rocha UY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=rocha
    ------------------------
    Proceesing: City #  576  |  Korenovsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=korenovsk
    ------------------------
    Proceesing: City #  577  |  Orchard Homes US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=orchard+homes
    ------------------------
    Proceesing: City #  578  |  Presidente Epitacio BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=presidente+epitacio
    ------------------------
    Proceesing: City #  579  |  Noumea NC
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=noumea
    City not found, skipping...
    ------------------------
    Proceesing: City #  580  |  Paine CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=paine
    ------------------------
    Proceesing: City #  581  |  Erzurum TR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=erzurum
    ------------------------
    Proceesing: City #  582  |  Gazli UZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gazli
    City not found, skipping...
    ------------------------
    Proceesing: City #  583  |  Dromolaxia CY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dromolaxia
    ------------------------
    Proceesing: City #  584  |  Laohekou CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=laohekou
    ------------------------
    Proceesing: City #  585  |  Shima JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=shima
    ------------------------
    Proceesing: City #  586  |  Burnie AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=burnie
    ------------------------
    Proceesing: City #  587  |  Lima PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lima
    ------------------------
    Proceesing: City #  588  |  Camacari BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=camacari
    ------------------------
    Proceesing: City #  589  |  Lata IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lata
    ------------------------
    Proceesing: City #  590  |  Katherine AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=katherine
    ------------------------
    Proceesing: City #  591  |  Duns GB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=duns
    City not found, skipping...
    ------------------------
    Proceesing: City #  592  |  Kavaratti IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kavaratti
    ------------------------
    Proceesing: City #  593  |  Kang BW
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kang
    ------------------------
    Proceesing: City #  594  |  Salinas US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=salinas
    ------------------------
    Proceesing: City #  595  |  Inhambane MZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=inhambane
    ------------------------
    Proceesing: City #  596  |  Arnedo ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=arnedo
    ------------------------
    Proceesing: City #  597  |  La Ronge CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=la+ronge
    ------------------------
    Proceesing: City #  598  |  Magrath CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=magrath
    ------------------------
    Proceesing: City #  599  |  Hauterive FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hauterive
    ------------------------
    Proceesing: City #  600  |  Half Moon Bay US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=half+moon+bay
    ------------------------
    Proceesing: City #  601  |  Wajir KE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=wajir
    ------------------------
    Proceesing: City #  602  |  San Juan PH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=san+juan
    ------------------------
    Proceesing: City #  603  |  Zemio CF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=zemio
    ------------------------
    Proceesing: City #  604  |  Elk River US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=elk+river
    ------------------------
    Proceesing: City #  605  |  Leh IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=leh
    ------------------------
    Proceesing: City #  606  |  Bayir TR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bayir
    ------------------------
    Proceesing: City #  607  |  Besskorbnaya RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=besskorbnaya
    ------------------------
    Proceesing: City #  608  |  Nemuro JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nemuro
    ------------------------
    Proceesing: City #  609  |  Kendari ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kendari
    ------------------------
    Proceesing: City #  610  |  Sabang PH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sabang
    ------------------------
    Proceesing: City #  611  |  Tangwani PK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tangwani
    ------------------------
    Proceesing: City #  612  |  Boa Vista BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=boa+vista
    ------------------------
    Proceesing: City #  613  |  Dawson Creek CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dawson+creek
    ------------------------
    Proceesing: City #  614  |  Alfenas BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=alfenas
    ------------------------
    Proceesing: City #  615  |  Mindelo PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mindelo
    ------------------------
    Proceesing: City #  616  |  Santiago de Cao PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=santiago+de+cao
    ------------------------
    Proceesing: City #  617  |  Romans-sur-Isere FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=romans-sur-isere



```python
clean_cities_df.count()
```




    City                587
    Country             587
    Temperature (F)     587
    Latitude            587
    Longitude           587
    Humidity (%)        587
    Cloudiness (%)      587
    Wind Speed (mph)    587
    dtype: int64




```python
# Reset Index
clean_cities_df = clean_cities_df.reset_index()
del clean_cities_df['index']
clean_cities_df.head(10)
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
      <th>City</th>
      <th>Country</th>
      <th>Temperature (F)</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Humidity (%)</th>
      <th>Cloudiness (%)</th>
      <th>Wind Speed (mph)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Lebu</td>
      <td>ET</td>
      <td>60.88</td>
      <td>8.96</td>
      <td>38.73</td>
      <td>59</td>
      <td>12</td>
      <td>7.38</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Ponta Delgada</td>
      <td>PT</td>
      <td>62.6</td>
      <td>37.73</td>
      <td>-25.67</td>
      <td>55</td>
      <td>20</td>
      <td>16.11</td>
    </tr>
    <tr>
      <th>2</th>
      <td>East London</td>
      <td>ZA</td>
      <td>59</td>
      <td>-33.02</td>
      <td>27.91</td>
      <td>77</td>
      <td>0</td>
      <td>8.05</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Prado</td>
      <td>BR</td>
      <td>79.15</td>
      <td>-17.34</td>
      <td>-39.22</td>
      <td>97</td>
      <td>0</td>
      <td>14.2</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Ponta do Sol</td>
      <td>BR</td>
      <td>76.9</td>
      <td>-20.63</td>
      <td>-46</td>
      <td>35</td>
      <td>0</td>
      <td>4.36</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hay River</td>
      <td>CA</td>
      <td>71.6</td>
      <td>60.82</td>
      <td>-115.79</td>
      <td>30</td>
      <td>75</td>
      <td>5.82</td>
    </tr>
    <tr>
      <th>6</th>
      <td>New Norfolk</td>
      <td>AU</td>
      <td>50</td>
      <td>-42.78</td>
      <td>147.06</td>
      <td>71</td>
      <td>75</td>
      <td>10.29</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Albany</td>
      <td>US</td>
      <td>62.64</td>
      <td>42.65</td>
      <td>-73.75</td>
      <td>77</td>
      <td>90</td>
      <td>5.82</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Debica</td>
      <td>PL</td>
      <td>71.6</td>
      <td>50.05</td>
      <td>21.41</td>
      <td>53</td>
      <td>0</td>
      <td>13.87</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Tiksi</td>
      <td>RU</td>
      <td>32.44</td>
      <td>71.64</td>
      <td>128.87</td>
      <td>83</td>
      <td>36</td>
      <td>3.36</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Save data to CSV file
clean_cities_df.to_csv("City_Data.csv")
```


```python
# LATITUDE VS. MAX TEMPERATURE
# Build Scatter Plot
plt.scatter(clean_cities_df["Latitude"], 
            clean_cities_df["Temperature (F)"], c=clean_cities_df["Temperature (F)"],
            edgecolor="black", linewidths=1, marker="o", 
            cmap='plasma', alpha=0.8, label="City")

# Other Graph Properties
plt.style.use('seaborn')
plt.title(f"City Latitude vs. Max Temperature {datetime.now().strftime('%m/%d/%Y')}")
plt.ylabel("Max Temperature (F)")
plt.xlabel("Latitude")
plt.grid(True)
plt.xlim([-80, 100])
plt.ylim([-60, 120])

# Save the figure
plt.savefig("Latitude_Temperature.png")

# Show plot
plt.show()
```


![png](WeatherPy_files/WeatherPy_9_0.png)



```python
# Scatter plot for Latitude vs. Humidity (%)
plt.scatter(clean_cities_df["Latitude"], 
            clean_cities_df["Humidity (%)"], c=clean_cities_df["Humidity (%)"],
            edgecolor="black", linewidths=1, marker="o", 
            cmap='GnBu', alpha=0.8, label="City")

# Other Graph Properties
plt.style.use('seaborn')
plt.title(f"City Latitude vs. Humidity (%) {datetime.now().strftime('%m/%d/%Y')}")
plt.ylabel("Humidity (%)")
plt.xlabel("Latitude")
plt.grid(True)
plt.xlim([-80, 100])
plt.ylim([-5, 125])

# Save the figure
plt.savefig("Latitude_Humidity.png")

# Show plot
plt.show()
```


![png](WeatherPy_files/WeatherPy_10_0.png)



```python
# Scatter plot for Latitude vs. Cloudiness (%)
plt.scatter(clean_cities_df["Latitude"], 
            clean_cities_df["Cloudiness (%)"], c=clean_cities_df["Cloudiness (%)"],
            edgecolor="black", linewidths=1, marker="o", 
            cmap='bone_r', alpha=0.8, label="City")

# Other Graph Properties
plt.style.use('seaborn')
plt.title(f"City Latitude vs. Cloudiness (%) {datetime.now().strftime('%m/%d/%Y')}")
plt.ylabel("Cloudiness (%)")
plt.xlabel("Latitude")
plt.grid(True)
plt.xlim([-80, 100])
plt.ylim([-10, 125])

# Save the figure
plt.savefig("Latitude_Cloudiness.png")

# Show plot
plt.show()
```


![png](WeatherPy_files/WeatherPy_11_0.png)



```python
# Scatter plot for Latitude vs. Wind Speed (mph)
plt.scatter(clean_cities_df["Latitude"], 
            clean_cities_df["Wind Speed (mph)"], c=clean_cities_df["Wind Speed (mph)"],
            edgecolor="black", linewidths=1, marker="o", 
            cmap='Spectral_r', alpha=0.8, label="City")

# Other Graph Properties
plt.style.use('seaborn')
plt.title(f"City Latitude vs. Wind Speed (mph) {datetime.now().strftime('%m/%d/%Y')}")
plt.ylabel("Wind Speed (mph)")
plt.xlabel("Latitude")
plt.grid(True)
plt.xlim([-80, 100])
plt.ylim([-10, 50])

# Save the figure
plt.savefig("Latitude_WindSpeed.png")

# Show plot
plt.show()
```


![png](WeatherPy_files/WeatherPy_12_0.png)



```python
# Scatter plot for Latitude vs. Wind Speed (mph)
# To Confirm Lat/Lng's Have a Good Spread (Are Not Concentrated in One Area)
plt.scatter(clean_cities_df["Longitude"], 
            clean_cities_df["Latitude"], c=clean_cities_df["Latitude"],
            edgecolor="black", linewidths=1, marker="o", 
            cmap='RdYlBu', alpha=0.8, label="City")

# Other Graph Properties
plt.style.use('seaborn')
plt.title(f"Longitude vs. Latitude {datetime.now().strftime('%m/%d/%Y')}")
plt.ylabel("Latitude")
plt.xlabel("Longitude")
plt.grid(True)
plt.xlim([-200, 200])
plt.ylim([-80, 100])

# Save the figure
plt.savefig("Latitude_Longitude.png")

# Show plot
plt.show()
```


![png](WeatherPy_files/WeatherPy_13_0.png)

