---
title: 'Samouczek: kierowanie pojazdów elektrycznych przy użyciu Azure Notebooks (Python) z mapami Microsoft Azure'
description: Samouczek dotyczący sposobu kierowania pojazdami elektrycznymi przy użyciu Microsoft Azure Maps Routing interfejsów API i Azure Notebooks
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 7341d1f07e8814edcad7b84f6b3b46c7bece3159
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98680335"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Samouczek: kierowanie pojazdów elektrycznych przy użyciu Azure Notebooks (Python)

Azure Maps to portfolio interfejsów API usług geoprzestrzennych, które są natywnie zintegrowane z platformą Azure. Te interfejsy API umożliwiają deweloperom, firmom i dostawcom oprogramowania Tworzenie aplikacji obsługujących lokalizacje, rzeczy, mobilności, logistycznych i rozwiązań do śledzenia zasobów. 

Interfejsy API REST Azure Maps mogą być wywoływane z języków takich jak Python i R, aby umożliwić analizę danych geoprzestrzennych i scenariusze uczenia maszynowego. Azure Maps oferuje niezawodny zestaw [interfejsów API routingu](/rest/api/maps/route) , które umożliwiają użytkownikom obliczanie tras między kilkoma punktami danych. Obliczenia są zależne od różnych warunków, takich jak typ pojazdu lub dostępny obszar. 

W tym samouczku poznasz sterownik, którego bateria elektryczności jest niska. Sterownik musi znaleźć najbliższą możliwą stację ładowania z lokalizacji pojazdu.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Utwórz i uruchom Jupyter Notebook plik na [Azure Notebooks](https://notebooks.azure.com) w chmurze.
> * Wywołaj interfejsy API REST Azure Maps w języku Python.
> * Wyszukaj osiągalny zakres oparty na modelu zużycia pojazdu elektrycznego.
> * Wyszukaj stacje opłat za pojazdy elektryczne w dostępnym zakresie lub isochrone.
> * Renderuj dostępną granicę zakresu i stacje ładowania na mapie.
> * Znajdź i Wizualizuj trasę do najbliższej stacji naładowania pojazdu elektrycznego na podstawie czasu dysku.


## <a name="prerequisites"></a>Wymagania wstępne 

Aby ukończyć ten samouczek, musisz najpierw utworzyć konto Azure Maps i uzyskać klucz podstawowy (klucz subskrypcji). 

Aby utworzyć subskrypcję konta Azure Maps, postępuj zgodnie z instrukcjami podanymi w temacie [Tworzenie konta](quick-demo-map-app.md#create-an-azure-maps-account). Potrzebna jest subskrypcja konta Azure Maps z warstwą cenową S1. 

Aby uzyskać podstawowy klucz subskrypcji dla konta, postępuj zgodnie z instrukcjami podanymi w temacie [Pobieranie klucza podstawowego](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](./how-to-manage-authentication.md).

## <a name="create-an-azure-notebooks-project"></a>Tworzenie projektu Azure Notebooks

Aby wykonać czynności opisane w tym samouczku, należy utworzyć projekt Azure Notebooks i pobrać i uruchomić plik Jupyter Notebook. Plik Jupyter Notebook zawiera kod języka Python, który implementuje scenariusz w tym samouczku. Aby utworzyć projekt Azure Notebooks i przekazać do niego dokument Jupyter Notebook, wykonaj następujące czynności:

1. Przejdź do [Azure Notebooks](https://notebooks.azure.com) i zaloguj się. Aby uzyskać więcej informacji, zobacz [Szybki Start: Logowanie i Ustawianie identyfikatora użytkownika](https://notebooks.azure.com).
1. W górnej części strony profilu publicznego wybierz pozycję **Moje projekty**.

    ![Przycisk moje projekty](./media/tutorial-ev-routing/myproject.png)

1. Na stronie **Moje projekty** wybierz pozycję **Nowy projekt**.
 
   ![Przycisk Nowy projekt](./media/tutorial-ev-routing/create-project.png)

1. W okienku **Utwórz nowy projekt** wprowadź nazwę projektu i identyfikator projektu.
 
    ![Okienko Utwórz nowy projekt](./media/tutorial-ev-routing/create-project-window.png)

1. Wybierz przycisk **Utwórz**.

1. Po utworzeniu projektu Pobierz ten [Jupyter notebook plik dokumentu](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) z [repozytorium Azure Maps Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook).

1. Na liście projekty na stronie **Moje projekty** wybierz projekt, a następnie wybierz pozycję **Przekaż** , aby przekazać plik dokumentu Jupyter Notebook. 

    ![Przekaż Jupyter Notebook](./media/tutorial-ev-routing/upload-notebook.png)

1. Przekaż plik z komputera, a następnie wybierz pozycję **gotowe**.

1. Po pomyślnym zakończeniu przekazywania plik zostanie wyświetlony na stronie Twojego projektu. Kliknij dwukrotnie plik, aby otworzyć go jako Jupyter Notebook.

Spróbuj zrozumieć funkcjonalność zaimplementowaną w pliku Jupyter Notebook. Uruchom kod, w pliku Jupyter Notebook, po jednej komórce w danym momencie. Kod można uruchomić w każdej komórce, wybierając przycisk **Uruchom** w górnej części aplikacji Jupyter Notebook.

  ![Przycisk Uruchom](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Zainstaluj pakiety na poziomie projektu

Aby uruchomić kod w Jupyter Notebook, zainstaluj pakiety na poziomie projektu, wykonując następujące czynności:

1. Pobierz plik [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) z [repozytorium Azure Maps Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook), a następnie Przekaż go do projektu.
1. Na pulpicie nawigacyjnym projektu wybierz pozycję **Ustawienia projektu**. 
1. W okienku **Ustawienia projektu** wybierz kartę **środowisko** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **czynności konfiguracyjne środowiska** wykonaj następujące czynności:   
    a. Z pierwszej listy rozwijanej wybierz pozycję **Requirements.txt**.  
    b. Z drugiej listy rozwijanej wybierz plik *requirements.txt* .  
    c. Z trzeciej listy rozwijanej wybierz **wersję 3,6 języka Python** jako wersję.
1. Wybierz pozycję **Zapisz**.

    ![Zainstaluj pakiety](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Załaduj wymagane moduły i struktury

Aby załadować wszystkie wymagane moduły i platformy, uruchom poniższy skrypt.

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Żądaj granicy dostępnego zakresu

Firma dostarczająca pakiet ma pojazdy elektryczne w swojej flotie. W ciągu dnia należy ponownie naliczać pojazdy elektryczne bez konieczności powrotu do hurtowni danych. Za każdym razem, gdy pozostała opłata spadnie do mniej niż godziny, Wyszukaj zestaw stacji ładowania, które znajdują się w dostępnym zakresie. Zasadniczo należy wyszukać stacji ładowania, gdy bateria jest niska. I uzyskasz informacje o granicach tego zakresu stacji ładowania. 

Ze względu na to, że firma preferuje używanie tras wymagających zrównoważenia ekonomicznego i szybkości, żądany element routetype jest *ekologiczny*. Następujący skrypt wywołuje [interfejs API pobierania zakresu tras](/rest/api/maps/route/getrouterange) usługi routingu Azure Maps. Używa parametrów dla modelu zużycia pojazdu. Skrypt następnie analizuje odpowiedź w celu utworzenia obiektu wielokąta w formacie GEOJSON, który reprezentuje maksymalny osiągalny zakres.

Aby określić granice dostępnego zakresu dla pojazdu elektrycznego, uruchom skrypt w następującej komórce:

```python
subscriptionKey = "Your Azure Maps key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Wyszukaj stacje opłat za pojazdy elektryczne w dostępnym zakresie

Po ustaleniu dostępnego zakresu (isochrone) dla pojazdu elektrycznego można wyszukać stacje ładowania w tym zakresie. 

Poniższy skrypt wywołuje Azure Maps [post Search w interfejsie API geometrii](/rest/api/maps/search/postsearchinsidegeometry). Wyszukuje on stacje opłat dla pojazdu elektrycznego, w granicach maksymalnego osiągalnego zakresu samochodu. Następnie skrypt analizuje odpowiedź do tablicy osiągalnych lokalizacji.

Aby wyszukać stacje opłat za pojazdy elektryczne w dostępnym zakresie, uruchom następujący skrypt:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service-preview"></a>Przekaż osiągalny zakres i punkty ładowania do usługi danych Azure Maps (wersja zapoznawcza)

Na mapie należy wizualizować stacje ładowania i granicę maksymalnego osiągalnego zakresu pojazdu elektrycznego. W tym celu Przekaż dane graniczne i Nalicz stacje dane jako obiekty GEOJSON do Azure Maps usługi danych (wersja zapoznawcza). Użyj [interfejsu API przekazywania danych](/rest/api/maps/data/uploadpreview). 

Aby przekazać dane dotyczące granicy i ładowania do usługi danych Azure Maps, uruchom następujące dwie komórki:

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data service (Preview).
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data service (Preview).
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>Renderowanie stacji ładowania i dostępnego zakresu na mapie

Po przekazaniu danych do usługi danych Wywołaj Azure Maps [Get Image Service](/rest/api/maps/render/getmapimage). Ta usługa służy do renderowania punktów ładowania i maksymalnej dostępnej granicy na obrazie mapy statycznej przez uruchomienie następującego skryptu:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![Mapa pokazująca zakres lokalizacji](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Znajdowanie optymalnej stacji ładowania

Najpierw chcesz określić wszystkie potencjalne stacje naliczania opłat w dostępnym zakresie. Następnie chcesz wiedzieć, które z nich można osiągnąć w minimalnym czasie. 

Poniższy skrypt wywołuje [interfejs API routingu Azure Maps Matrix](/rest/api/maps/route/postroutematrix). Zwraca określoną lokalizację pojazdu, czas podróży i odległość do każdej stacji ładowania. Skrypt w następnej komórce analizuje odpowiedź w celu zlokalizowania najbliższej dostępnej stacji ładowania w odniesieniu do czasu.

Aby znaleźć najbliższą osiągalną stację ładowania, którą można osiągnąć w czasie najmniejszej ilości czasu, uruchom skrypt w następującej komórce:

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Oblicz trasę do najbliższej stacji ładowania

Po znalezieniu najbliższej stacji ładowania można wywołać [interfejs API wskazówek dotyczących trasy](/rest/api/maps/route/getroutedirections) , aby zażądać szczegółowej trasy z bieżącej lokalizacji pojazdu elektrycznego do stacji ładowania.

Aby uzyskać trasę do stacji ładowania i przeanalizować odpowiedź w celu utworzenia obiektu GEOJSON, który reprezentuje trasę, uruchom skrypt w następującej komórce:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Wizualizacja trasy

Aby ułatwić wizualizację trasy, należy najpierw przekazać dane trasy jako obiekt GEOJSON do Azure Maps usługi danych (wersja zapoznawcza). Aby to zrobić, użyj [interfejsu API przekazywania danych](/rest/api/maps/data/uploadpreview)Azure Maps. Następnie Wywołaj usługę renderowania, [Pobierz interfejs API obrazu mapy](/rest/api/maps/render/getmapimage), aby renderować trasę na mapie, i Wizualizuj ją.

Aby uzyskać obraz dla renderowanej trasy na mapie, uruchom następujący skrypt:

```python
# Upload the route data to Azure Maps Data service (Preview).
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Mapa pokazująca trasę](./media/tutorial-ev-routing/route.png)

W tym samouczku przedstawiono sposób bezpośredniego Azure Maps wywoływania interfejsów API REST i wizualizacji danych Azure Maps przy użyciu języka Python.

Aby poznać Azure Maps interfejsów API, które są używane w tym samouczku, zobacz:

* [Pobierz zakres tras](/rest/api/maps/route/getrouterange)
* [Opublikuj wyszukiwanie w geometrii](/rest/api/maps/search/postsearchinsidegeometry)
* [Przekazywanie danych](/rest/api/maps/data/uploadpreview)
* [Renderowanie — Pobieranie obrazu mapy](/rest/api/maps/render/getmapimage)
* [Macierz po trasie](/rest/api/maps/route/postroutematrix)
* [Pobierz wskazówki dotyczące trasy](/rest/api/maps/route/getroutedirections)
* [Interfejsy API REST Azure Maps](./consumption-model.md)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Brak zasobów, które wymagają oczyszczenia.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Notebooks, zobacz

> [!div class="nextstepaction"]
> [Azure Notebooks](https://notebooks.azure.com)