

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




    index      659
    City       659
    Country    659
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
      <td>lebu</td>
      <td>cl</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>ponta delgada</td>
      <td>pt</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>east london</td>
      <td>za</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>prado</td>
      <td>br</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>barentsburg</td>
      <td>sj</td>
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
        clean_cities_df.set_value(index, "City", city_data["name"])
        clean_cities_df.set_value(index, "Country", city_data["sys"]["country"])
        clean_cities_df.set_value(index, "Temperature (F)", city_data["main"]["temp"])
        clean_cities_df.set_value(index, "Latitude", city_data["coord"]["lat"])
        clean_cities_df.set_value(index, "Longitude", city_data["coord"]["lon"])
        clean_cities_df.set_value(index, "Humidity (%)", city_data["main"]["humidity"])
        clean_cities_df.set_value(index, "Cloudiness (%)", city_data["clouds"]["all"])
        clean_cities_df.set_value(index, "Wind Speed (mph)", city_data["wind"]["speed"])
        
        print("------------------------")
        print("Proceesing: City # " , row_count, ' | ' , city_data["name"], city_data["sys"]["country"])
        print(target_url)
        row_count += 1

```

    Beginning Data Retrieval: 
    ------------------------
    Proceesing: City #  1  |  Lebu ET
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lebu


    /Users/catesoane/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:21: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /Users/catesoane/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:22: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /Users/catesoane/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:23: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /Users/catesoane/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:24: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /Users/catesoane/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:25: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /Users/catesoane/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:26: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /Users/catesoane/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:27: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
    /Users/catesoane/anaconda3/lib/python3.6/site-packages/ipykernel_launcher.py:28: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead


    ------------------------
    Proceesing: City #  2  |  Ponta Delgada PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ponta+delgada
    ------------------------
    Proceesing: City #  3  |  East London ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=east+london
    ------------------------
    Proceesing: City #  4  |  Prado BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=prado
    City not found, skipping...
    ------------------------
    Proceesing: City #  5  |  Ponta do Sol BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ponta+do+sol
    ------------------------
    Proceesing: City #  6  |  Hay River CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hay+river
    ------------------------
    Proceesing: City #  7  |  New Norfolk AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=new+norfolk
    ------------------------
    Proceesing: City #  8  |  Albany US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=albany
    ------------------------
    Proceesing: City #  9  |  Debica PL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=debica
    ------------------------
    Proceesing: City #  10  |  Tiksi RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tiksi
    ------------------------
    Proceesing: City #  11  |  Baykit RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=baykit
    ------------------------
    Proceesing: City #  12  |  Santa Maria BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=santa+maria
    ------------------------
    Proceesing: City #  13  |  Ushuaia AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ushuaia
    ------------------------
    Proceesing: City #  14  |  Derzhavinsk KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=derzhavinsk
    ------------------------
    Proceesing: City #  15  |  Lahij YE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lahij
    ------------------------
    Proceesing: City #  16  |  Robertsport LR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=robertsport
    ------------------------
    Proceesing: City #  17  |  Rikitea PF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=rikitea
    ------------------------
    Proceesing: City #  18  |  Ancud CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ancud
    ------------------------
    Proceesing: City #  19  |  Tasiilaq GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tasiilaq
    ------------------------
    Proceesing: City #  20  |  Morondava MG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=morondava
    City not found, skipping...
    City not found, skipping...
    ------------------------
    Proceesing: City #  21  |  Poum MK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=poum
    ------------------------
    Proceesing: City #  22  |  Busselton AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=busselton
    ------------------------
    Proceesing: City #  23  |  Hovd NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hovd
    ------------------------
    Proceesing: City #  24  |  Ambilobe MG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ambilobe
    City not found, skipping...
    ------------------------
    Proceesing: City #  25  |  Hilo US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hilo
    City not found, skipping...
    ------------------------
    Proceesing: City #  26  |  Aljezur PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=aljezur
    ------------------------
    Proceesing: City #  27  |  Puerto Ayora EC
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=puerto+ayora
    ------------------------
    Proceesing: City #  28  |  Groningen NL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=groningen
    ------------------------
    Proceesing: City #  29  |  Jamestown AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=jamestown
    ------------------------
    Proceesing: City #  30  |  Vaini IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vaini
    ------------------------
    Proceesing: City #  31  |  Bluff AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bluff
    City not found, skipping...
    ------------------------
    Proceesing: City #  32  |  Jamame SO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=jamame
    ------------------------
    Proceesing: City #  33  |  Saint-Philippe CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saint-philippe
    ------------------------
    Proceesing: City #  34  |  Bathsheba BB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bathsheba
    ------------------------
    Proceesing: City #  35  |  Hobart AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hobart
    ------------------------
    Proceesing: City #  36  |  Saskylakh RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saskylakh
    ------------------------
    Proceesing: City #  37  |  Chuy UY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=chuy
    ------------------------
    Proceesing: City #  38  |  Shenjiamen CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=shenjiamen
    ------------------------
    Proceesing: City #  39  |  Barrow AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=barrow
    ------------------------
    Proceesing: City #  40  |  Visani RO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=visani
    ------------------------
    Proceesing: City #  41  |  Severo-Kurilsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=severo-kurilsk
    ------------------------
    Proceesing: City #  42  |  Bukama CD
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bukama
    ------------------------
    Proceesing: City #  43  |  Butaritari KI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=butaritari
    ------------------------
    Proceesing: City #  44  |  Hithadhoo MV
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hithadhoo
    ------------------------
    Proceesing: City #  45  |  Hauge NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hauge
    ------------------------
    Proceesing: City #  46  |  Georgetown GY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=georgetown
    ------------------------
    Proceesing: City #  47  |  Mohelnice CZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mohelnice
    ------------------------
    Proceesing: City #  48  |  La Paz MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=la+paz
    ------------------------
    Proceesing: City #  49  |  Javanrud IR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=javanrud
    ------------------------
    Proceesing: City #  50  |  Udimskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=udimskiy
    ------------------------
    Proceesing: City #  51  |  Elwood US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=elwood
    ------------------------
    Proceesing: City #  52  |  Lively US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lively
    City not found, skipping...
    ------------------------
    Proceesing: City #  53  |  Port Alfred ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port+alfred
    ------------------------
    Proceesing: City #  54  |  Iqaluit CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=iqaluit
    ------------------------
    Proceesing: City #  55  |  Arraial do Cabo BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=arraial+do+cabo
    ------------------------
    Proceesing: City #  56  |  Sioux Lookout CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sioux+lookout
    ------------------------
    Proceesing: City #  57  |  Nikolayevka RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nikolayevka
    ------------------------
    Proceesing: City #  58  |  Mataura NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mataura
    ------------------------
    Proceesing: City #  59  |  Gizo IL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gizo
    ------------------------
    Proceesing: City #  60  |  Cidreira BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cidreira
    ------------------------
    Proceesing: City #  61  |  Cockburn Town TC
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cockburn+town
    ------------------------
    Proceesing: City #  62  |  Souillac FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=souillac
    ------------------------
    Proceesing: City #  63  |  Srednekolymsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=srednekolymsk
    ------------------------
    Proceesing: City #  64  |  Valkeala FI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=valkeala
    ------------------------
    Proceesing: City #  65  |  Saint George GR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saint+george
    ------------------------
    Proceesing: City #  66  |  Castro CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=castro
    ------------------------
    Proceesing: City #  67  |  Huilong CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=huilong
    ------------------------
    Proceesing: City #  68  |  Laguna MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=laguna
    City not found, skipping...
    ------------------------
    Proceesing: City #  69  |  Ust-Omchug RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ust-omchug
    ------------------------
    Proceesing: City #  70  |  Nha Trang VN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nha+trang
    ------------------------
    Proceesing: City #  71  |  Yaan NG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=yaan
    ------------------------
    Proceesing: City #  72  |  Cherkasskoye RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cherkasskoye
    ------------------------
    Proceesing: City #  73  |  Thompson CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=thompson
    ------------------------
    Proceesing: City #  74  |  Sao Jose da Coroa Grande BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sao+jose+da+coroa+grande
    ------------------------
    Proceesing: City #  75  |  Roald NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=roald
    ------------------------
    Proceesing: City #  76  |  Maniitsoq GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=maniitsoq
    ------------------------
    Proceesing: City #  77  |  Takoradi GH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=takoradi
    ------------------------
    Proceesing: City #  78  |  Kerema PG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kerema
    ------------------------
    Proceesing: City #  79  |  Gidole ET
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gidole
    ------------------------
    Proceesing: City #  80  |  Pevek RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pevek
    ------------------------
    Proceesing: City #  81  |  Talara PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=talara
    ------------------------
    Proceesing: City #  82  |  Los Llanos de Aridane ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=los+llanos+de+aridane
    ------------------------
    Proceesing: City #  83  |  Punta Arenas CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=punta+arenas
    ------------------------
    Proceesing: City #  84  |  Abu Kamal SY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=abu+kamal
    ------------------------
    Proceesing: City #  85  |  Torbay CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=torbay
    ------------------------
    Proceesing: City #  86  |  Khatanga RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=khatanga
    ------------------------
    Proceesing: City #  87  |  Carnarvon ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=carnarvon
    ------------------------
    Proceesing: City #  88  |  Balkhash KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=balkhash
    ------------------------
    Proceesing: City #  89  |  Upernavik GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=upernavik
    ------------------------
    Proceesing: City #  90  |  Port Blair IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port+blair
    ------------------------
    Proceesing: City #  91  |  Kropotkin RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kropotkin
    ------------------------
    Proceesing: City #  92  |  Madimba CM
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=madimba
    ------------------------
    Proceesing: City #  93  |  Hermanus ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hermanus
    City not found, skipping...
    ------------------------
    Proceesing: City #  94  |  Bethel US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bethel
    ------------------------
    Proceesing: City #  95  |  Sept-Iles CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sept-iles
    ------------------------
    Proceesing: City #  96  |  Codrington AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=codrington
    ------------------------
    Proceesing: City #  97  |  Cape Town ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cape+town
    ------------------------
    Proceesing: City #  98  |  Avarua CK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=avarua
    ------------------------
    Proceesing: City #  99  |  Lagoa PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lagoa
    ------------------------
    Proceesing: City #  100  |  Klaksvik FO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=klaksvik
    ------------------------
    Proceesing: City #  101  |  Hasaki JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hasaki
    ------------------------
    Proceesing: City #  102  |  Sao Joao da Barra BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sao+joao+da+barra
    ------------------------
    Proceesing: City #  103  |  Atuona PF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=atuona
    ------------------------
    Proceesing: City #  104  |  Rio Grande BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=rio+grande
    ------------------------
    Proceesing: City #  105  |  Manicore BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=manicore
    ------------------------
    Proceesing: City #  106  |  Islampur IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=islampur
    ------------------------
    Proceesing: City #  107  |  Salinas US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=salinas
    City not found, skipping...
    ------------------------
    Proceesing: City #  108  |  Nyurba RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nyurba
    ------------------------
    Proceesing: City #  109  |  Qaanaaq GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=qaanaaq
    ------------------------
    Proceesing: City #  110  |  San Patricio PY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=san+patricio
    ------------------------
    Proceesing: City #  111  |  Nikolskoye RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nikolskoye
    ------------------------
    Proceesing: City #  112  |  Zhigansk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=zhigansk
    ------------------------
    Proceesing: City #  113  |  Selkirk CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=selkirk
    ------------------------
    Proceesing: City #  114  |  Tuatapere NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tuatapere
    City not found, skipping...
    City not found, skipping...
    City not found, skipping...
    ------------------------
    Proceesing: City #  115  |  Pearl US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pearl
    ------------------------
    Proceesing: City #  116  |  Nome US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nome
    ------------------------
    Proceesing: City #  117  |  Ribeira Grande PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ribeira+grande
    ------------------------
    Proceesing: City #  118  |  Mandera ET
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mandera
    ------------------------
    Proceesing: City #  119  |  Vila Franca do Campo PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vila+franca+do+campo
    ------------------------
    Proceesing: City #  120  |  Kalevala FI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kalevala
    ------------------------
    Proceesing: City #  121  |  Harper US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=harper
    ------------------------
    Proceesing: City #  122  |  Lufkin US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lufkin
    ------------------------
    Proceesing: City #  123  |  Abha SA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=abha
    ------------------------
    Proceesing: City #  124  |  Katsuura JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=katsuura
    ------------------------
    Proceesing: City #  125  |  Hami CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hami
    ------------------------
    Proceesing: City #  126  |  Alofi NU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=alofi
    ------------------------
    Proceesing: City #  127  |  Korem ET
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=korem
    City not found, skipping...
    City not found, skipping...
    ------------------------
    Proceesing: City #  128  |  Mahebourg MU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mahebourg
    ------------------------
    Proceesing: City #  129  |  Cayenne GF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cayenne
    ------------------------
    Proceesing: City #  130  |  Pont-a-Mousson FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pont-a-mousson
    ------------------------
    Proceesing: City #  131  |  Ushtobe KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ushtobe
    City not found, skipping...
    ------------------------
    Proceesing: City #  132  |  Essau GM
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=essau
    City not found, skipping...
    ------------------------
    Proceesing: City #  133  |  Kodiak US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kodiak
    ------------------------
    Proceesing: City #  134  |  Chernyshevskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=chernyshevskiy
    ------------------------
    Proceesing: City #  135  |  Dikson RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dikson
    ------------------------
    Proceesing: City #  136  |  Oriximina BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=oriximina
    ------------------------
    Proceesing: City #  137  |  Praia BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=praia
    ------------------------
    Proceesing: City #  138  |  Kapaa US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kapaa
    ------------------------
    Proceesing: City #  139  |  Severobaykalsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=severobaykalsk
    ------------------------
    Proceesing: City #  140  |  Dauphin CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dauphin
    ------------------------
    Proceesing: City #  141  |  Tuktoyaktuk CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tuktoyaktuk
    ------------------------
    Proceesing: City #  142  |  Isangel VU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=isangel
    City not found, skipping...
    ------------------------
    Proceesing: City #  143  |  Bredasdorp ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bredasdorp
    ------------------------
    Proceesing: City #  144  |  Pangnirtung CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pangnirtung
    ------------------------
    Proceesing: City #  145  |  Lastoursville GA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lastoursville
    ------------------------
    Proceesing: City #  146  |  Dalby AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dalby
    ------------------------
    Proceesing: City #  147  |  Kualakapuas ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kualakapuas
    ------------------------
    Proceesing: City #  148  |  Emba CY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=emba
    ------------------------
    Proceesing: City #  149  |  Mbanza-Ngungu CD
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mbanza-ngungu
    ------------------------
    Proceesing: City #  150  |  Bela PK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bela
    ------------------------
    Proceesing: City #  151  |  Coquimbo CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=coquimbo
    ------------------------
    Proceesing: City #  152  |  Yellowknife CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=yellowknife
    ------------------------
    Proceesing: City #  153  |  Udachnyy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=udachnyy
    ------------------------
    Proceesing: City #  154  |  Tarsus TR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tarsus
    ------------------------
    Proceesing: City #  155  |  Kirakira SB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kirakira
    ------------------------
    Proceesing: City #  156  |  Xunchang CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=xunchang
    ------------------------
    Proceesing: City #  157  |  Ibra OM
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ibra
    ------------------------
    Proceesing: City #  158  |  Negombo LK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=negombo
    ------------------------
    Proceesing: City #  159  |  Banjar ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=banjar
    ------------------------
    Proceesing: City #  160  |  Caravelas BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=caravelas
    City not found, skipping...
    ------------------------
    Proceesing: City #  161  |  Matara PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=matara
    ------------------------
    Proceesing: City #  162  |  Umm Kaddadah SD
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=umm+kaddadah
    ------------------------
    Proceesing: City #  163  |  Petropavlovsk-Kamchatskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=petropavlovsk-kamchatskiy
    ------------------------
    Proceesing: City #  164  |  Bubaque GW
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bubaque
    ------------------------
    Proceesing: City #  165  |  Todos Santos MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=todos+santos
    ------------------------
    Proceesing: City #  166  |  Longyearbyen NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=longyearbyen
    ------------------------
    Proceesing: City #  167  |  Provideniya RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=provideniya
    ------------------------
    Proceesing: City #  168  |  Karratha AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=karratha
    ------------------------
    Proceesing: City #  169  |  Naze NG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=naze
    ------------------------
    Proceesing: City #  170  |  Teya MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=teya
    ------------------------
    Proceesing: City #  171  |  Pyaozerskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pyaozerskiy
    ------------------------
    Proceesing: City #  172  |  Seoul KR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=seoul
    ------------------------
    Proceesing: City #  173  |  Portland US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=portland
    ------------------------
    Proceesing: City #  174  |  Airai TL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=airai
    ------------------------
    Proceesing: City #  175  |  Camacha PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=camacha
    ------------------------
    Proceesing: City #  176  |  Freeport BS
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=freeport
    ------------------------
    Proceesing: City #  177  |  Mogadishu SO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mogadishu
    ------------------------
    Proceesing: City #  178  |  Shreveport US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=shreveport
    ------------------------
    Proceesing: City #  179  |  Broken Hill AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=broken+hill
    ------------------------
    Proceesing: City #  180  |  Varnamo SE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=varnamo
    ------------------------
    Proceesing: City #  181  |  Husavik CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=husavik
    ------------------------
    Proceesing: City #  182  |  Cedeno CO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cedeno
    ------------------------
    Proceesing: City #  183  |  Tabuk PH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tabuk
    ------------------------
    Proceesing: City #  184  |  Kruisfontein ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kruisfontein
    ------------------------
    Proceesing: City #  185  |  Mar del Plata AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mar+del+plata
    ------------------------
    Proceesing: City #  186  |  Quelimane MZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=quelimane
    ------------------------
    Proceesing: City #  187  |  Kavaratti IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kavaratti
    ------------------------
    Proceesing: City #  188  |  Mount Gambier AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mount+gambier
    ------------------------
    Proceesing: City #  189  |  Cherskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cherskiy
    ------------------------
    Proceesing: City #  190  |  Tautira PF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tautira
    ------------------------
    Proceesing: City #  191  |  Burriana ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=burriana
    City not found, skipping...
    ------------------------
    Proceesing: City #  192  |  Road Town VG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=road+town
    ------------------------
    Proceesing: City #  193  |  Port Elizabeth US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port+elizabeth
    ------------------------
    Proceesing: City #  194  |  Nanortalik GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nanortalik
    ------------------------
    Proceesing: City #  195  |  Leningradskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=leningradskiy
    City not found, skipping...
    ------------------------
    Proceesing: City #  196  |  Sundsvall SE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sundsvall
    ------------------------
    Proceesing: City #  197  |  Usinsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=usinsk
    ------------------------
    Proceesing: City #  198  |  Oksfjord NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=oksfjord
    City not found, skipping...
    ------------------------
    Proceesing: City #  199  |  Tyukalinsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tyukalinsk
    ------------------------
    Proceesing: City #  200  |  Gobabis NA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gobabis
    ------------------------
    Proceesing: City #  201  |  Ionia US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ionia
    City not found, skipping...
    City not found, skipping...
    ------------------------
    Proceesing: City #  202  |  Lakes Entrance AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lakes+entrance
    ------------------------
    Proceesing: City #  203  |  Smolensk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=smolensk
    ------------------------
    Proceesing: City #  204  |  Russell AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=russell
    ------------------------
    Proceesing: City #  205  |  Luwuk ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=luwuk
    ------------------------
    Proceesing: City #  206  |  Sao Filipe CV
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sao+filipe
    ------------------------
    Proceesing: City #  207  |  Malinovskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=malinovskiy
    City not found, skipping...
    ------------------------
    Proceesing: City #  208  |  Lishui CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lishui
    ------------------------
    Proceesing: City #  209  |  Taltal CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=taltal
    ------------------------
    Proceesing: City #  210  |  Sampit ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sampit
    ------------------------
    Proceesing: City #  211  |  Dingle PH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dingle
    ------------------------
    Proceesing: City #  212  |  Zaozerne UA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=zaozerne
    ------------------------
    Proceesing: City #  213  |  Kangaatsiaq GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kangaatsiaq
    ------------------------
    Proceesing: City #  214  |  Diamantino BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=diamantino
    ------------------------
    Proceesing: City #  215  |  Kieta PG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kieta
    ------------------------
    Proceesing: City #  216  |  Berlevag NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=berlevag
    City not found, skipping...
    ------------------------
    Proceesing: City #  217  |  Aanekoski FI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=aanekoski
    ------------------------
    Proceesing: City #  218  |  Chokurdakh RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=chokurdakh
    ------------------------
    Proceesing: City #  219  |  Tucuman PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tucuman
    ------------------------
    Proceesing: City #  220  |  Corinto BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=corinto
    ------------------------
    Proceesing: City #  221  |  Yar-Sale RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=yar-sale
    ------------------------
    Proceesing: City #  222  |  Grindavik IS
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=grindavik
    ------------------------
    Proceesing: City #  223  |  Dalianwan CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dalianwan
    ------------------------
    Proceesing: City #  224  |  Kaitangata NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kaitangata
    ------------------------
    Proceesing: City #  225  |  Te Anau NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=te+anau
    ------------------------
    Proceesing: City #  226  |  Jalu LY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=jalu
    ------------------------
    Proceesing: City #  227  |  Camargo MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=camargo
    ------------------------
    Proceesing: City #  228  |  Linxia CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=linxia
    ------------------------
    Proceesing: City #  229  |  Tual ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tual
    ------------------------
    Proceesing: City #  230  |  Sankt Johann im Pongau AT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sankt+johann+im+pongau
    ------------------------
    Proceesing: City #  231  |  Zelenoborsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=zelenoborsk
    ------------------------
    Proceesing: City #  232  |  Almaznyy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=almaznyy
    ------------------------
    Proceesing: City #  233  |  Monrovia LR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=monrovia
    ------------------------
    Proceesing: City #  234  |  Namibe AO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=namibe
    City not found, skipping...
    ------------------------
    Proceesing: City #  235  |  Vardo US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vardo
    ------------------------
    Proceesing: City #  236  |  Victoria BN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=victoria
    ------------------------
    Proceesing: City #  237  |  Yura PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=yura
    ------------------------
    Proceesing: City #  238  |  Richards Bay ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=richards+bay
    ------------------------
    Proceesing: City #  239  |  Bambous Virieux MU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bambous+virieux
    ------------------------
    Proceesing: City #  240  |  Jambi ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=jambi
    ------------------------
    Proceesing: City #  241  |  Perelyub RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=perelyub
    ------------------------
    Proceesing: City #  242  |  Ahipara NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ahipara
    ------------------------
    Proceesing: City #  243  |  Comodoro Rivadavia AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=comodoro+rivadavia
    ------------------------
    Proceesing: City #  244  |  Warrington GB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=warrington
    ------------------------
    Proceesing: City #  245  |  Port Lincoln AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port+lincoln
    ------------------------
    Proceesing: City #  246  |  Cabo San Lucas MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cabo+san+lucas
    ------------------------
    Proceesing: City #  247  |  Anandnagar IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=anandnagar
    ------------------------
    Proceesing: City #  248  |  Clyde River CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=clyde+river
    ------------------------
    Proceesing: City #  249  |  Stavrovo RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=stavrovo
    ------------------------
    Proceesing: City #  250  |  Goderich CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=goderich
    ------------------------
    Proceesing: City #  251  |  Lavrentiya RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lavrentiya
    ------------------------
    Proceesing: City #  252  |  Malchevskaya RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=malchevskaya
    ------------------------
    Proceesing: City #  253  |  Kosa RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kosa
    ------------------------
    Proceesing: City #  254  |  Yataity PY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=yataity
    ------------------------
    Proceesing: City #  255  |  Bilibino RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bilibino
    ------------------------
    Proceesing: City #  256  |  Lunglei IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lunglei
    ------------------------
    Proceesing: City #  257  |  Santa Teresa VE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=santa+teresa
    ------------------------
    Proceesing: City #  258  |  Wyndham GB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=wyndham
    City not found, skipping...
    ------------------------
    Proceesing: City #  259  |  Douentza ML
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=douentza
    ------------------------
    Proceesing: City #  260  |  Rawson AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=rawson
    City not found, skipping...
    ------------------------
    Proceesing: City #  261  |  Wajima JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=wajima
    ------------------------
    Proceesing: City #  262  |  Valdivia CL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=valdivia
    ------------------------
    Proceesing: City #  263  |  Port Hedland AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port+hedland
    ------------------------
    Proceesing: City #  264  |  Trincomalee LK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=trincomalee
    ------------------------
    Proceesing: City #  265  |  Fort Nelson CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=fort+nelson
    ------------------------
    Proceesing: City #  266  |  Huelva ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=huelva
    ------------------------
    Proceesing: City #  267  |  Charlestown US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=charlestown
    ------------------------
    Proceesing: City #  268  |  Kota Kinabalu MY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kota+kinabalu
    ------------------------
    Proceesing: City #  269  |  Gwadar PK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gwadar
    ------------------------
    Proceesing: City #  270  |  Miraflores CO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=miraflores
    ------------------------
    Proceesing: City #  271  |  Nevelsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nevelsk
    ------------------------
    Proceesing: City #  272  |  Aklavik CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=aklavik
    ------------------------
    Proceesing: City #  273  |  Samana EC
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=samana
    ------------------------
    Proceesing: City #  274  |  Teguise ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=teguise
    ------------------------
    Proceesing: City #  275  |  Porirua NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=porirua
    ------------------------
    Proceesing: City #  276  |  Ulety RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ulety
    ------------------------
    Proceesing: City #  277  |  Chapais CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=chapais
    ------------------------
    Proceesing: City #  278  |  Oktyabrskoye RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=oktyabrskoye
    ------------------------
    Proceesing: City #  279  |  Creston US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=creston
    ------------------------
    Proceesing: City #  280  |  Rocha UY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=rocha
    ------------------------
    Proceesing: City #  281  |  Lazaro Cardenas MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lazaro+cardenas
    ------------------------
    Proceesing: City #  282  |  Saint Anthony US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saint+anthony
    ------------------------
    Proceesing: City #  283  |  Hindaun IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hindaun
    ------------------------
    Proceesing: City #  284  |  Fortuna ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=fortuna
    City not found, skipping...
    ------------------------
    Proceesing: City #  285  |  Launceston AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=launceston
    ------------------------
    Proceesing: City #  286  |  Constitucion MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=constitucion
    ------------------------
    Proceesing: City #  287  |  Lompoc US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lompoc
    ------------------------
    Proceesing: City #  288  |  Meadow Lake CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=meadow+lake
    ------------------------
    Proceesing: City #  289  |  Kawalu ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kawalu
    ------------------------
    Proceesing: City #  290  |  Port Hawkesbury CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port+hawkesbury
    ------------------------
    Proceesing: City #  291  |  Esperance TT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=esperance
    City not found, skipping...
    ------------------------
    Proceesing: City #  292  |  Neya RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=neya
    ------------------------
    Proceesing: City #  293  |  Magadan RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=magadan
    ------------------------
    Proceesing: City #  294  |  Petropavl KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=petropavl
    ------------------------
    Proceesing: City #  295  |  Constantine DZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=constantine
    ------------------------
    Proceesing: City #  296  |  Arman RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=arman
    ------------------------
    Proceesing: City #  297  |  College US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=college
    ------------------------
    Proceesing: City #  298  |  Bandarbeyla SO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bandarbeyla
    City not found, skipping...
    City not found, skipping...
    ------------------------
    Proceesing: City #  299  |  Belousovka KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=belousovka
    ------------------------
    Proceesing: City #  300  |  La Ronge CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=la+ronge
    ------------------------
    Proceesing: City #  301  |  Buriti Alegre BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=buriti+alegre
    ------------------------
    Proceesing: City #  302  |  Markova RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=markova
    ------------------------
    Proceesing: City #  303  |  Ilulissat GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ilulissat
    ------------------------
    Proceesing: City #  304  |  Concordia AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=concordia
    ------------------------
    Proceesing: City #  305  |  Itanhaem BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=itanhaem
    ------------------------
    Proceesing: City #  306  |  Pasighat IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pasighat
    ------------------------
    Proceesing: City #  307  |  Macklin CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=macklin
    ------------------------
    Proceesing: City #  308  |  Vestmannaeyjar IS
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vestmannaeyjar
    ------------------------
    Proceesing: City #  309  |  Thika KE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=thika
    ------------------------
    Proceesing: City #  310  |  Weinan CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=weinan
    City not found, skipping...
    ------------------------
    Proceesing: City #  311  |  Loreto PY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=loreto
    ------------------------
    Proceesing: City #  312  |  Krasnogorskoye RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=krasnogorskoye
    ------------------------
    Proceesing: City #  313  |  Nenjiang CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nenjiang
    City not found, skipping...
    City not found, skipping...
    ------------------------
    Proceesing: City #  314  |  Luderitz NA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=luderitz
    ------------------------
    Proceesing: City #  315  |  Haines Junction CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=haines+junction
    City not found, skipping...
    ------------------------
    Proceesing: City #  316  |  San Cristobal EC
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=san+cristobal
    ------------------------
    Proceesing: City #  317  |  Farafangana MG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=farafangana
    ------------------------
    Proceesing: City #  318  |  Beringovskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=beringovskiy
    ------------------------
    Proceesing: City #  319  |  Aykhal RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=aykhal
    ------------------------
    Proceesing: City #  320  |  Lappeenranta FI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lappeenranta
    ------------------------
    Proceesing: City #  321  |  Tadine NC
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tadine
    ------------------------
    Proceesing: City #  322  |  Paamiut GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=paamiut
    ------------------------
    Proceesing: City #  323  |  Nyrob RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nyrob
    ------------------------
    Proceesing: City #  324  |  Weiser US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=weiser
    ------------------------
    Proceesing: City #  325  |  Cienfuegos CU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cienfuegos
    ------------------------
    Proceesing: City #  326  |  Rio Gallegos AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=rio+gallegos
    ------------------------
    Proceesing: City #  327  |  Aksu KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=aksu
    ------------------------
    Proceesing: City #  328  |  Kamenka RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kamenka
    ------------------------
    Proceesing: City #  329  |  Saldanha PT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saldanha
    ------------------------
    Proceesing: City #  330  |  Fort Frances CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=fort+frances
    City not found, skipping...
    ------------------------
    Proceesing: City #  331  |  Kagalnitskaya RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kagalnitskaya
    ------------------------
    Proceesing: City #  332  |  Lubango AO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lubango
    City not found, skipping...
    ------------------------
    Proceesing: City #  333  |  Hofn IS
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hofn
    City not found, skipping...
    ------------------------
    Proceesing: City #  334  |  Severnoye RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=severnoye
    ------------------------
    Proceesing: City #  335  |  Faya SA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=faya
    ------------------------
    Proceesing: City #  336  |  Flinders AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=flinders
    ------------------------
    Proceesing: City #  337  |  Mgachi RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mgachi
    ------------------------
    Proceesing: City #  338  |  Miri NG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=miri
    City not found, skipping...
    ------------------------
    Proceesing: City #  339  |  Khandyga RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=khandyga
    ------------------------
    Proceesing: City #  340  |  Natal BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=natal
    ------------------------
    Proceesing: City #  341  |  Boa Vista BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=boa+vista
    ------------------------
    Proceesing: City #  342  |  Acapulco MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=acapulco
    ------------------------
    Proceesing: City #  343  |  Hambantota LK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hambantota
    ------------------------
    Proceesing: City #  344  |  Saint-Prosper CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saint-prosper
    ------------------------
    Proceesing: City #  345  |  Santiago del Estero AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=santiago+del+estero
    ------------------------
    Proceesing: City #  346  |  Umm Lajj SA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=umm+lajj
    ------------------------
    Proceesing: City #  347  |  Bad Sackingen DE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bad+sackingen
    ------------------------
    Proceesing: City #  348  |  Misratah LY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=misratah
    City not found, skipping...
    ------------------------
    Proceesing: City #  349  |  Zarubino RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=zarubino
    ------------------------
    Proceesing: City #  350  |  Hailar CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hailar
    ------------------------
    Proceesing: City #  351  |  Karlstad SE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=karlstad
    ------------------------
    Proceesing: City #  352  |  Kulhudhuffushi MV
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kulhudhuffushi
    ------------------------
    Proceesing: City #  353  |  Swakopmund NA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=swakopmund
    ------------------------
    Proceesing: City #  354  |  Kavieng PG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kavieng
    ------------------------
    Proceesing: City #  355  |  Peleduy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=peleduy
    ------------------------
    Proceesing: City #  356  |  Kahului US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kahului
    ------------------------
    Proceesing: City #  357  |  Harbour Breton CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=harbour+breton
    ------------------------
    Proceesing: City #  358  |  Matay EG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=matay
    ------------------------
    Proceesing: City #  359  |  Mocuba MZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mocuba
    ------------------------
    Proceesing: City #  360  |  Ostrovnoy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ostrovnoy
    ------------------------
    Proceesing: City #  361  |  Covington US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=covington
    ------------------------
    Proceesing: City #  362  |  Vicam MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vicam
    ------------------------
    Proceesing: City #  363  |  Hauterive FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hauterive
    City not found, skipping...
    ------------------------
    Proceesing: City #  364  |  Bratsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bratsk
    ------------------------
    Proceesing: City #  365  |  El Sauzal AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=el+sauzal
    ------------------------
    Proceesing: City #  366  |  Mezhdurechensk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mezhdurechensk
    ------------------------
    Proceesing: City #  367  |  Nijar ES
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nijar
    ------------------------
    Proceesing: City #  368  |  Gonen IL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gonen
    ------------------------
    Proceesing: City #  369  |  Ambovombe MG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ambovombe
    City not found, skipping...
    ------------------------
    Proceesing: City #  370  |  Belyy Yar RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=belyy+yar
    City not found, skipping...
    ------------------------
    Proceesing: City #  371  |  Pisco PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pisco
    ------------------------
    Proceesing: City #  372  |  Sigli ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sigli
    City not found, skipping...
    ------------------------
    Proceesing: City #  373  |  Zapolyarnyy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=zapolyarnyy
    ------------------------
    Proceesing: City #  374  |  Iguape BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=iguape
    ------------------------
    Proceesing: City #  375  |  Margate AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=margate
    ------------------------
    Proceesing: City #  376  |  Gat SN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gat
    ------------------------
    Proceesing: City #  377  |  Poronaysk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=poronaysk
    ------------------------
    Proceesing: City #  378  |  Buraydah SA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=buraydah
    ------------------------
    Proceesing: City #  379  |  De Aar ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=de+aar
    ------------------------
    Proceesing: City #  380  |  Hamilton CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hamilton
    ------------------------
    Proceesing: City #  381  |  Kununurra AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kununurra
    ------------------------
    Proceesing: City #  382  |  Yulara AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=yulara
    City not found, skipping...
    City not found, skipping...
    ------------------------
    Proceesing: City #  383  |  Quepos CR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=quepos
    City not found, skipping...
    ------------------------
    Proceesing: City #  384  |  Manavalakurichi IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=manavalakurichi
    ------------------------
    Proceesing: City #  385  |  Santo Angelo BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=santo+angelo
    ------------------------
    Proceesing: City #  386  |  Mukhen RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mukhen
    City not found, skipping...
    City not found, skipping...
    ------------------------
    Proceesing: City #  387  |  Ventspils LV
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ventspils
    ------------------------
    Proceesing: City #  388  |  Sechura PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sechura
    ------------------------
    Proceesing: City #  389  |  Narsaq GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=narsaq
    ------------------------
    Proceesing: City #  390  |  Quirinopolis BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=quirinopolis
    ------------------------
    Proceesing: City #  391  |  La Palma CO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=la+palma
    ------------------------
    Proceesing: City #  392  |  Erenhot CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=erenhot
    ------------------------
    Proceesing: City #  393  |  Dillon US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dillon
    City not found, skipping...
    ------------------------
    Proceesing: City #  394  |  Samarai PG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=samarai
    ------------------------
    Proceesing: City #  395  |  Aksarka RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=aksarka
    ------------------------
    Proceesing: City #  396  |  Velikiy Ustyug RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=velikiy+ustyug
    ------------------------
    Proceesing: City #  397  |  Rudsar IR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=rudsar
    ------------------------
    Proceesing: City #  398  |  Emerald AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=emerald
    ------------------------
    Proceesing: City #  399  |  Thinadhoo MV
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=thinadhoo
    City not found, skipping...
    ------------------------
    Proceesing: City #  400  |  Saint-Francois FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saint-francois
    ------------------------
    Proceesing: City #  401  |  Mbini GQ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mbini
    ------------------------
    Proceesing: City #  402  |  Hervey Bay AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hervey+bay
    ------------------------
    Proceesing: City #  403  |  Marzuq YE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=marzuq
    ------------------------
    Proceesing: City #  404  |  Colac AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=colac
    ------------------------
    Proceesing: City #  405  |  Turan RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=turan
    ------------------------
    Proceesing: City #  406  |  Sur OM
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sur
    ------------------------
    Proceesing: City #  407  |  Filingue NE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=filingue
    ------------------------
    Proceesing: City #  408  |  Henderson US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=henderson
    ------------------------
    Proceesing: City #  409  |  Safford US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=safford
    City not found, skipping...
    ------------------------
    Proceesing: City #  410  |  Oron NG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=oron
    ------------------------
    Proceesing: City #  411  |  Jian IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=jian
    ------------------------
    Proceesing: City #  412  |  Morehead US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=morehead
    ------------------------
    Proceesing: City #  413  |  Pochutla MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pochutla
    ------------------------
    Proceesing: City #  414  |  Waingapu ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=waingapu
    ------------------------
    Proceesing: City #  415  |  Necochea AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=necochea
    ------------------------
    Proceesing: City #  416  |  Padang ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=padang
    ------------------------
    Proceesing: City #  417  |  High Level CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=high+level
    ------------------------
    Proceesing: City #  418  |  Turukhansk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=turukhansk
    ------------------------
    Proceesing: City #  419  |  Knysna ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=knysna
    ------------------------
    Proceesing: City #  420  |  Gamboma CG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gamboma
    ------------------------
    Proceesing: City #  421  |  Saltpond GH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saltpond
    ------------------------
    Proceesing: City #  422  |  Maues BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=maues
    ------------------------
    Proceesing: City #  423  |  Newport GB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=newport
    ------------------------
    Proceesing: City #  424  |  Dali CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dali
    ------------------------
    Proceesing: City #  425  |  Jumla NP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=jumla
    ------------------------
    Proceesing: City #  426  |  Guerrero Negro MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=guerrero+negro
    ------------------------
    Proceesing: City #  427  |  Puerto Madryn AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=puerto+madryn
    City not found, skipping...
    City not found, skipping...
    ------------------------
    Proceesing: City #  428  |  Nemuro JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nemuro
    ------------------------
    Proceesing: City #  429  |  Pimentel BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pimentel
    City not found, skipping...
    ------------------------
    Proceesing: City #  430  |  Atikokan CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=atikokan
    ------------------------
    Proceesing: City #  431  |  Panzhihua CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=panzhihua
    ------------------------
    Proceesing: City #  432  |  Makat KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=makat
    City not found, skipping...
    ------------------------
    Proceesing: City #  433  |  Marawi PH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=marawi
    ------------------------
    Proceesing: City #  434  |  Kirksville US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kirksville
    ------------------------
    Proceesing: City #  435  |  Terrasini IT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=terrasini
    City not found, skipping...
    ------------------------
    Proceesing: City #  436  |  Saraland US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=saraland
    ------------------------
    Proceesing: City #  437  |  Valleyview CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=valleyview
    ------------------------
    Proceesing: City #  438  |  Sitka US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sitka
    ------------------------
    Proceesing: City #  439  |  Armidale AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=armidale
    ------------------------
    Proceesing: City #  440  |  Cabedelo BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cabedelo
    ------------------------
    Proceesing: City #  441  |  Tougan BF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tougan
    ------------------------
    Proceesing: City #  442  |  Utete TZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=utete
    ------------------------
    Proceesing: City #  443  |  Abay KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=abay
    ------------------------
    Proceesing: City #  444  |  Kichera RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kichera
    ------------------------
    Proceesing: City #  445  |  Touros BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=touros
    ------------------------
    Proceesing: City #  446  |  Sobolevo RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sobolevo
    ------------------------
    Proceesing: City #  447  |  The Valley AI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=the+valley
    ------------------------
    Proceesing: City #  448  |  Oistins BB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=oistins
    ------------------------
    Proceesing: City #  449  |  Zuenoula CI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=zuenoula
    ------------------------
    Proceesing: City #  450  |  Evensk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=evensk
    ------------------------
    Proceesing: City #  451  |  Sinop TR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sinop
    ------------------------
    Proceesing: City #  452  |  Mareeba AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mareeba
    City not found, skipping...
    ------------------------
    Proceesing: City #  453  |  Mangrol IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mangrol
    City not found, skipping...
    ------------------------
    Proceesing: City #  454  |  Shafranovo RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=shafranovo
    ------------------------
    Proceesing: City #  455  |  Kandrian PG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kandrian
    ------------------------
    Proceesing: City #  456  |  Plettenberg Bay ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=plettenberg+bay
    ------------------------
    Proceesing: City #  457  |  Komsomolskiy RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=komsomolskiy
    ------------------------
    Proceesing: City #  458  |  Mahibadhoo MV
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mahibadhoo
    ------------------------
    Proceesing: City #  459  |  Tanete ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tanete
    ------------------------
    Proceesing: City #  460  |  Weligama LK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=weligama
    ------------------------
    Proceesing: City #  461  |  Mariinsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mariinsk
    ------------------------
    Proceesing: City #  462  |  Lata IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lata
    ------------------------
    Proceesing: City #  463  |  Braco do Norte BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=braco+do+norte
    City not found, skipping...
    ------------------------
    Proceesing: City #  464  |  Gumla IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gumla
    ------------------------
    Proceesing: City #  465  |  Yerbogachen RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=yerbogachen
    ------------------------
    Proceesing: City #  466  |  Golden CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=golden
    ------------------------
    Proceesing: City #  467  |  Zastron ZA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=zastron
    ------------------------
    Proceesing: City #  468  |  Bulgan MN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bulgan
    ------------------------
    Proceesing: City #  469  |  Matias Olimpio BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=matias+olimpio
    City not found, skipping...
    ------------------------
    Proceesing: City #  470  |  Makakilo City US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=makakilo+city
    ------------------------
    Proceesing: City #  471  |  Kamaishi JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kamaishi
    ------------------------
    Proceesing: City #  472  |  Pontianak ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pontianak
    ------------------------
    Proceesing: City #  473  |  Lima PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lima
    ------------------------
    Proceesing: City #  474  |  High Rock BS
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=high+rock
    ------------------------
    Proceesing: City #  475  |  Bulawayo ZW
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bulawayo
    ------------------------
    Proceesing: City #  476  |  Norman Wells CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=norman+wells
    ------------------------
    Proceesing: City #  477  |  Oyama JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=oyama
    ------------------------
    Proceesing: City #  478  |  Joshimath IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=joshimath
    ------------------------
    Proceesing: City #  479  |  Olinda EC
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=olinda
    ------------------------
    Proceesing: City #  480  |  Honiara SB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=honiara
    ------------------------
    Proceesing: City #  481  |  Finnsnes NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=finnsnes
    ------------------------
    Proceesing: City #  482  |  Birao CF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=birao
    ------------------------
    Proceesing: City #  483  |  Pacifica US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pacifica
    ------------------------
    Proceesing: City #  484  |  Mildura AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mildura
    ------------------------
    Proceesing: City #  485  |  Viedma AR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=viedma
    ------------------------
    Proceesing: City #  486  |  Youghal IE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=youghal
    ------------------------
    Proceesing: City #  487  |  Itaituba BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=itaituba
    ------------------------
    Proceesing: City #  488  |  Porto Belo BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=porto+belo
    ------------------------
    Proceesing: City #  489  |  Walvis Bay NA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=walvis+bay
    ------------------------
    Proceesing: City #  490  |  Porto Novo BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=porto+novo
    ------------------------
    Proceesing: City #  491  |  Bud NO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bud
    City not found, skipping...
    ------------------------
    Proceesing: City #  492  |  Tura IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tura
    ------------------------
    Proceesing: City #  493  |  Geraldton CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=geraldton
    ------------------------
    Proceesing: City #  494  |  Carauari BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=carauari
    ------------------------
    Proceesing: City #  495  |  Najran SA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=najran
    City not found, skipping...
    ------------------------
    Proceesing: City #  496  |  Mayo CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mayo
    ------------------------
    Proceesing: City #  497  |  Riyadh SA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=riyadh
    ------------------------
    Proceesing: City #  498  |  Manaia NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=manaia
    ------------------------
    Proceesing: City #  499  |  Santa Maria del Oro MX
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=santa+maria+del+oro
    ------------------------
    Proceesing: City #  500  |  Kloulklubed PW
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kloulklubed
    ------------------------
    Proceesing: City #  501  |  Campbellton CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=campbellton
    ------------------------
    Proceesing: City #  502  |  Vagur FO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vagur
    ------------------------
    Proceesing: City #  503  |  Bitkine TD
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bitkine
    ------------------------
    Proceesing: City #  504  |  Jiupu CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=jiupu
    City not found, skipping...
    ------------------------
    Proceesing: City #  505  |  Susanville US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=susanville
    ------------------------
    Proceesing: City #  506  |  Ronne FR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ronne
    ------------------------
    Proceesing: City #  507  |  Kailua US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kailua
    ------------------------
    Proceesing: City #  508  |  Sarh TD
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sarh
    City not found, skipping...
    ------------------------
    Proceesing: City #  509  |  Iberia PE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=iberia
    ------------------------
    Proceesing: City #  510  |  Nushki PK
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nushki
    ------------------------
    Proceesing: City #  511  |  Aligudarz IR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=aligudarz
    ------------------------
    Proceesing: City #  512  |  Smithers CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=smithers
    ------------------------
    Proceesing: City #  513  |  Bedum NL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bedum
    ------------------------
    Proceesing: City #  514  |  Culpeper US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=culpeper
    ------------------------
    Proceesing: City #  515  |  Talnakh RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=talnakh
    ------------------------
    Proceesing: City #  516  |  Kohima IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kohima
    ------------------------
    Proceesing: City #  517  |  Ginir ET
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ginir
    ------------------------
    Proceesing: City #  518  |  Kachug RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kachug
    ------------------------
    Proceesing: City #  519  |  Hoshiarpur IN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=hoshiarpur
    ------------------------
    Proceesing: City #  520  |  Ayagoz KZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ayagoz
    ------------------------
    Proceesing: City #  521  |  Bonoua CI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bonoua
    ------------------------
    Proceesing: City #  522  |  Falun SE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=falun
    ------------------------
    Proceesing: City #  523  |  Christchurch NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=christchurch
    ------------------------
    Proceesing: City #  524  |  Ossora RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ossora
    ------------------------
    Proceesing: City #  525  |  Onguday RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=onguday
    ------------------------
    Proceesing: City #  526  |  Salinopolis BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=salinopolis
    ------------------------
    Proceesing: City #  527  |  Vila Velha BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vila+velha
    ------------------------
    Proceesing: City #  528  |  Vyazma RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vyazma
    ------------------------
    Proceesing: City #  529  |  Mazagao BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mazagao
    ------------------------
    Proceesing: City #  530  |  Pitimbu BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pitimbu
    ------------------------
    Proceesing: City #  531  |  Danilov RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=danilov
    ------------------------
    Proceesing: City #  532  |  Kushiro JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kushiro
    ------------------------
    Proceesing: City #  533  |  Havre-Saint-Pierre CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=havre-saint-pierre
    ------------------------
    Proceesing: City #  534  |  Ankazoabo MG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ankazoabo
    ------------------------
    Proceesing: City #  535  |  Mitu IR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mitu
    ------------------------
    Proceesing: City #  536  |  Vila do Maio CV
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vila+do+maio
    ------------------------
    Proceesing: City #  537  |  Sabang PH
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sabang
    ------------------------
    Proceesing: City #  538  |  Upata VE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=upata
    City not found, skipping...
    ------------------------
    Proceesing: City #  539  |  Masvingo ZW
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=masvingo
    ------------------------
    Proceesing: City #  540  |  Vostok RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vostok
    ------------------------
    Proceesing: City #  541  |  Nadym RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=nadym
    ------------------------
    Proceesing: City #  542  |  Keuruu FI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=keuruu
    City not found, skipping...
    ------------------------
    Proceesing: City #  543  |  Faanui PF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=faanui
    ------------------------
    Proceesing: City #  544  |  Bayan MN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bayan
    ------------------------
    Proceesing: City #  545  |  Atambua ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=atambua
    ------------------------
    Proceesing: City #  546  |  Tamandare BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tamandare
    ------------------------
    Proceesing: City #  547  |  Egvekinot RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=egvekinot
    ------------------------
    Proceesing: City #  548  |  Henties Bay NA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=henties+bay
    ------------------------
    Proceesing: City #  549  |  Kupang ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kupang
    ------------------------
    Proceesing: City #  550  |  Vila IT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=vila
    ------------------------
    Proceesing: City #  551  |  Mount Isa AU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mount+isa
    ------------------------
    Proceesing: City #  552  |  Cananeia BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=cananeia
    ------------------------
    Proceesing: City #  553  |  Bosaso SO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bosaso
    ------------------------
    Proceesing: City #  554  |  Pincher Creek CA
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=pincher+creek
    ------------------------
    Proceesing: City #  555  |  Baldovinesti RO
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=baldovinesti
    ------------------------
    Proceesing: City #  556  |  Green River US
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=green+river
    ------------------------
    Proceesing: City #  557  |  Lethem GY
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=lethem
    ------------------------
    Proceesing: City #  558  |  Naryan-Mar RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=naryan-mar
    ------------------------
    Proceesing: City #  559  |  Palaia Fokaia GR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=palaia+fokaia
    ------------------------
    Proceesing: City #  560  |  Wundanyi KE
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=wundanyi
    ------------------------
    Proceesing: City #  561  |  Gazimurskiy Zavod RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gazimurskiy+zavod
    ------------------------
    Proceesing: City #  562  |  Winslow GB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=winslow
    ------------------------
    Proceesing: City #  563  |  Qasigiannguit GL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=qasigiannguit
    ------------------------
    Proceesing: City #  564  |  Linfen CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=linfen
    ------------------------
    Proceesing: City #  565  |  Fare PF
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=fare
    ------------------------
    Proceesing: City #  566  |  Meulaboh ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=meulaboh
    ------------------------
    Proceesing: City #  567  |  Gornja Radgona SI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=gornja+radgona
    ------------------------
    Proceesing: City #  568  |  Bariri BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=bariri
    ------------------------
    Proceesing: City #  569  |  Broome GB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=broome
    ------------------------
    Proceesing: City #  570  |  Correntina BR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=correntina
    ------------------------
    Proceesing: City #  571  |  Waipawa NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=waipawa
    ------------------------
    Proceesing: City #  572  |  Wangqing CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=wangqing
    City not found, skipping...
    ------------------------
    Proceesing: City #  573  |  Zandvoort NL
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=zandvoort
    ------------------------
    Proceesing: City #  574  |  Mae Sai MM
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=mae+sai
    ------------------------
    Proceesing: City #  575  |  Siva GR
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=siva
    ------------------------
    Proceesing: City #  576  |  Kutoarjo ID
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=kutoarjo
    City not found, skipping...
    City not found, skipping...
    ------------------------
    Proceesing: City #  577  |  Tilichiki RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=tilichiki
    ------------------------
    Proceesing: City #  578  |  Rio Hondo GT
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=rio+hondo
    ------------------------
    Proceesing: City #  579  |  Dunedin NZ
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dunedin
    ------------------------
    Proceesing: City #  580  |  Ust-Kuyga RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=ust-kuyga
    ------------------------
    Proceesing: City #  581  |  Sola FI
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=sola
    ------------------------
    Proceesing: City #  582  |  Buala SB
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=buala
    ------------------------
    Proceesing: City #  583  |  Salalah OM
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=salalah
    ------------------------
    Proceesing: City #  584  |  Okhotsk RU
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=okhotsk
    ------------------------
    Proceesing: City #  585  |  Shimoda JP
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=shimoda
    ------------------------
    Proceesing: City #  586  |  Port Said EG
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=port+said
    ------------------------
    Proceesing: City #  587  |  Dingtao CN
    http://api.openweathermap.org/data/2.5/weather?appid=dadcd19c1e6e63f5f672fc2085e24ef7&units=IMPERIAL&q=dingtao



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
# For Fun
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

