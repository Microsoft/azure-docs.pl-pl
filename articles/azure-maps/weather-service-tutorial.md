---
title: 'Samouczek: Dołączanie danych z czujników z danymi prognoz pogody przy użyciu Azure Notebooks (Python) | Mapy Microsoft Azure'
description: Samouczek dotyczący sposobu sprzęgania danych czujników z danymi prognoz pogodowych od Microsoft Azure Maps usługi Pogoda przy użyciu Azure Notebooks (Python).
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/29/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 717cb600f1bdae228f45409cb271ab04d1d51a19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91310105"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>Samouczek: Dołączanie danych z czujników z danymi prognoz pogody przy użyciu Azure Notebooks (Python)

Energia wiatru jest jednym Alternatywnym źródłem energii dla paliw kopalnych, aby zwalczać zmiany klimatyczne. Ze względu na to, że wiatr nie jest spójny z natury, operatory siły wiatru muszą kompilować modele uczenia maszynowego (ML), aby przewidzieć moc wiatru. To prognozowanie jest niezbędne do spełnienia wymagań dotyczących energii elektrycznej i zapewnienia stabilności siatki. W tym samouczku opisano sposób, w jaki Azure Maps dane prognozy pogody są łączone z danymi demonstracyjnymi dla odczytów pogodowych. Dane prognozy pogody są żądane przez wywołanie Azure Maps usługi pogodowej.

Ten samouczek obejmuje następujące kroki:

> [!div class="checklist"]
> * Pracuj z plikami danych w [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) w chmurze.
> * Załaduj dane demonstracyjne z pliku.
> * Wywołaj interfejsy API REST Azure Maps w języku Python.
> * Renderuj dane lokalizacji na mapie.
> * Wzbogacaj dane demonstracyjne za pomocą Azure Maps [codziennych](https://aka.ms/AzureMapsWeatherDailyForecast) danych o pogodzie.
> * Kreśl dane prognozy na wykresach.


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, należy najpierw wykonać następujące czynności:

1. Utwórz subskrypcję konta Azure Maps w warstwie cenowej S0, postępując zgodnie z instrukcjami podanymi w temacie [Tworzenie konta](quick-demo-map-app.md#create-an-azure-maps-account).
2. Pobierz podstawowy klucz subskrypcji dla swojego konta, postępuj zgodnie z instrukcjami w temacie [Pobieranie klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account).


Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).

Aby zapoznać się z notesami platformy Azure i wiedzieć, jak rozpocząć pracę, postępuj zgodnie z instrukcjami dotyczącymi [tworzenia notesu platformy Azure](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing#create-an-azure-notebook).

> [!Note]
> Plik notesu Jupyter dla tego projektu można pobrać z [repozytorium Pogoda Jupyter](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data).

## <a name="load-the-required-modules-and-frameworks"></a>Załaduj wymagane moduły i struktury

Aby załadować wszystkie wymagane moduły i struktury, uruchom następujący skrypt:

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>Importuj dane pogodowe

Na potrzeby tego samouczka będziemy używać odczytywania danych pogody z czujników zainstalowanych w czterech różnych turbinach wiatru. Przykładowe dane składają się z 30 dni od odczytu pogody. Te odczyty są zbierane z centrów danych pogody w każdej lokalizacji turbiny. Dane demonstracyjne zawierają odczyty danych dla temperatury, szybkość wiatru i kierunek. Możesz pobrać z tego [miejsca](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data)dane demonstracyjne. Poniższy skrypt importuje dane demonstracyjne do notesu platformy Azure.

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>Żądaj codziennych danych prognoz

W naszym scenariuszu chcemy zażądać codziennej prognozy dla każdej lokalizacji czujnika. Poniższy skrypt wywołuje [dzienny interfejs API prognozowania](https://aka.ms/AzureMapsWeatherDailyForecast) Azure Maps usługi Pogoda. Ten interfejs API zwraca prognozę pogody dla każdego turbiny wiatru w ciągu następnych 15 dni od bieżącej daty.


```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps weather service to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

Poniższy skrypt renderuje lokalizacje turbin na mapie, wywołując Azure Maps [Get Image Service](https://docs.microsoft.com/rest/api/maps/render/getmapimage).

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![Lokalizacje turbin](./media/weather-service-tutorial/location-map.png)


Będziemy grupować dane prognozy z danymi demonstracyjnymi opartymi na IDENTYFIKATORze stacji. Identyfikator stacji dotyczy centrum danych pogody. To grupowanie rozszerza dane demonstracyjne o dane prognozy.

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

W poniższej tabeli przedstawiono połączone historyczne i dane prognozy dla jednej z lokalizacji turbin.

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![Pogrupowane dane](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>Kreśl dane prognozy

Przekreślmy prognozowane wartości w dniach, dla których są prognozowane. Ten wykres pozwala nam zobaczyć prędkość i kierunek zmiany kierunku wiatru przez następne 15 dni.

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

Poniższe wykresy przedstawiają wizualizację danych prognoz. Aby zmienić szybkość wiatru, zobacz lewy wykres. Aby zmienić kierunek wiatru, zobacz odpowiedni wykres. Te dane są przewidywane w ciągu następnych 15 dni od dnia, w którym dane są żądane.

<center>

![Kierunek wiatru — wykres ](./media/weather-service-tutorial/speed-date-plot.png) ![ kierunku wiatru](./media/weather-service-tutorial/direction-date-plot.png)</center>

W tym samouczku przedstawiono sposób wywoływania Azure Maps interfejsów API REST w celu uzyskania danych prognozy pogody. Wiesz również, jak wizualizować dane na wykresach.

Aby dowiedzieć się więcej o sposobie wywoływania Azure Maps interfejsów API REST w programie Azure Notebooks, zobacz [Routing EV przy użyciu Azure Notebooks](https://docs.microsoft.com/azure/azure-maps/tutorial-ev-routing).

Aby poznać Azure Maps interfejsów API, które są używane w tym samouczku, zobacz:

* [Prognoza dzienna](https://aka.ms/AzureMapsWeatherDailyForecast)
* [Renderowanie — Pobieranie obrazu mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)

Aby uzyskać pełną listę Azure Maps interfejsów API REST, zobacz [Azure Maps interfejsów API REST](https://docs.microsoft.com/azure/azure-maps/consumption-model).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Notebooks, zobacz

> [!div class="nextstepaction"]
> [Azure Notebooks](https://docs.microsoft.com/azure/notebooks)