---
title: Tworzenie źródła danych dla map systemu Android | Mapy Microsoft Azure
description: 'Dowiedz się, jak utworzyć źródło danych dla mapy. Dowiedz się więcej na temat źródeł danych, których używa Android SDK Azure Maps: źródła GEOJSON i kafelki wektorowe.'
author: rbrundritt
ms.author: richbrun
ms.date: 12/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: fc68dc25aad3671a55e5c11cbee094b4027e7070
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047676"
---
# <a name="create-a-data-source-android-sdk"></a>Tworzenie źródła danych (Android SDK)

Azure Maps Android SDK przechowuje dane w źródłach danych. Użycie źródeł danych optymalizuje operacje na danych na potrzeby wykonywania zapytań i renderowania. Obecnie istnieją dwa typy źródeł danych:

- **Źródło GEOJSON**: dane lokalizacji pierwotnej w formacie GEOJSON są zarządzane lokalnie. Dobre dla małych i średnich zestawów danych (w górę setek tysięcy kształtów).
- **Źródło kafelka wektorowego**: wczytuje dane sformatowane jako kafelki wektorowe dla bieżącego widoku mapy, na podstawie systemu sąsiadującego z mapowaniem. Idealne rozwiązanie w przypadku dużych i wielkich zestawów danych (miliony lub miliardów kształtów).

## <a name="geojson-data-source"></a>Źródło danych GEOJSON

Azure Maps używa GEOJSON jako jednego z podstawowych modeli danych. GEOJSON jest otwartym standardem geograficznym do reprezentowania danych geoprzestrzennych w formacie JSON. Klasy GEOJSON dostępne w Android SDK Azure Maps mogą łatwo tworzyć i serializować dane GEOJSON. Załaduj i Zapisz dane GEOJSON w `DataSource` klasie i Renderuj je przy użyciu warstw. Poniższy kod pokazuje, jak można tworzyć obiekty GEOJSON w Azure Maps.

```java
/*
    Raw GeoJSON feature
    
    {
         "type": "Feature",
         "geometry": {
             "type": "Point",
             "coordinates": [-100, 45]
         },
         "properties": {
             "custom-property": "value"
         }
    }

*/

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add a property to the feature.
feature.addStringProperty("custom-property", "value");

//Add the feature to the data source.
source.add(feature);
```

Alternatywnie można najpierw załadować właściwości do obiektu JsonObject, a następnie przesłać je do funkcji podczas jej tworzenia, jak pokazano poniżej.

```java
//Create a JsonObject to store properties for the feature.
JsonObject properties = new JsonObject();
properties.addProperty("custom-property", "value");

Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45), properties);
```

Po utworzeniu funkcji GEOJSON źródło danych można dodać do mapy za pomocą `sources` Właściwości mapy. Poniższy kod pokazuje, jak utworzyć `DataSource` , dodać go do mapy i dodać funkcję do źródła danych.

```javascript
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add GeoJSON feature to the data source.
source.add(feature);
```

Poniższy kod przedstawia kilka sposobów tworzenia funkcji GEOJSON, Featurecollection i geometrie.

```java
//GeoJSON Point Geometry
Point point = Point.fromLngLat(LONGITUDE, LATITUDE);

//GeoJSON Point Geometry
LineString linestring = LineString.fromLngLats(PointList);

//GeoJSON Polygon Geometry
Polygon polygon = Polygon.fromLngLats(listOfPointList);
 
Polygon polygonFromOuterInner = Polygon.fromOuterInner(outerLineStringObject,innerLineStringObject);

//GeoJSON MultiPoint Geometry
MultiPoint multiPoint = MultiPoint.fromLngLats(PointList);

//GeoJSON MultiLineString Geometry
MultiLineString multiLineStringFromLngLat = MultiLineString.fromLngLats(listOfPointList);

MultiLineString multiLineString = MultiLineString.fromLineString(singleLineString);

//GeoJSON MultiPolygon Geometry
MultiPolygon multiPolygon = MultiPolygon.fromLngLats(listOflistOfPointList);

MultiPolygon multiPolygonFromPolygon = MultiPolygon.fromPolygon(polygon);

MultiPolygon multiPolygonFromPolygons = MultiPolygon.fromPolygons(PolygonList);

//GeoJSON Feature
Feature pointFeature = Feature.fromGeometry(Point.fromLngLat(LONGITUDE, LATITUDE));
 
//GeoJSON FeatureCollection 
FeatureCollection featureCollectionFromSingleFeature = FeatureCollection.fromFeature(pointFeature);
 
FeatureCollection featureCollection = FeatureCollection.fromFeatures(listOfFeatures);
```

### <a name="serialize-and-deserialize-geojson"></a>Serializacja i deserializacja GEOJSON

Wszystkie klasy kolekcji, funkcji i geometrii funkcji mają i są `fromJson()` `toJson()` metodami statycznymi, które pomagają w serializacji. Sformatowany prawidłowy ciąg JSON przeszedł przez `fromJson()` metodę spowoduje utworzenie obiektu Geometry. Ta `fromJson()` Metoda oznacza również, że można użyć Gson lub innych strategii serializacji/deserializacji. Poniższy kod pokazuje, jak wykonać funkcję skonwertowanej GEOJSON i deserializować ją do klasy funkcji, a następnie serializować ją z powrotem do ciągu GEOJSON.

```java
//Take a stringified GeoJSON object.
String GeoJSON_STRING = "{"
    + "      \"type\": \"Feature\","            
    + "      \"geometry\": {"
    + "            \"type\": \"Point\""
    + "            \"coordinates\": [-100, 45]"
    + "      },"
    + "      \"properties\": {"
    + "            \"custom-property\": \"value\""
    + "      },"
    + "}";

//Deserialize the JSON string into a feature.
Feature feature = Feature.fromJson(GeoJSON_STRING);
 
//Serialize a feature collection to a string.
String featureString = feature.toJson();
```

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importuj dane GEOJSON z folderu sieci Web lub zasobów

Większość plików GeoJSON zawiera element Featurecollection. Odczytywanie plików GeoJSON jako ciągów i użycie `FeatureCollection.fromJson` metody do deserializacji.

Poniższy kod jest klasą wielokrotnego użytku do importowania danych z folderu zasobów internetowych lub lokalnych jako ciągu i zwracania go do wątku interfejsu użytkownika za pomocą funkcji wywołania zwrotnego.

```java
import android.content.Context;
import android.os.Handler;
import android.os.Looper;
import android.webkit.URLUtil;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.URL;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import javax.net.ssl.HttpsURLConnection;

public class Utils {

    interface SimpleCallback {
        void notify(String result);
    }

    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback){
        importData(urlOrFileName, context, callback, null);
    }
    
    /**
     * Imports data from a web url or asset file name and returns it to a callback.
     * @param urlOrFileName A web url or asset file name that points to data to load.
     * @param context The context of the app.
     * @param callback The callback function to return the data to.
     * @param error A callback function to return errors to.
     */
    public static void importData(String urlOrFileName, Context context, SimpleCallback callback, SimpleCallback error){
        if(urlOrFileName != null && callback != null) {
            ExecutorService executor = Executors.newSingleThreadExecutor();
            Handler handler = new Handler(Looper.getMainLooper());

            executor.execute(() -> {
                String data = null;

                try {

                    if(URLUtil.isNetworkUrl(urlOrFileName)){
                        data = importFromWeb(urlOrFileName);
                    } else {
                        //Assume file is in assets folder.
                        data = importFromAssets(context, urlOrFileName);
                    }

                    final String result = data;

                    handler.post(() -> {
                        //Ensure the resulting data string is not null or empty.
                        if (result != null && !result.isEmpty()) {
                            callback.notify(result);
                        } else {
                            error.notify("No data imported.");
                        }
                    });
                } catch(Exception e) {
                    if(error != null){
                        error.notify(e.getMessage());
                    }
                }
            });
        }
    }

    /**
     * Imports data from an assets file as a string.
     * @param context The context of the app.
     * @param fileName The asset file name.
     * @return
     * @throws IOException
     */
    private static String importFromAssets(Context context, String fileName) throws IOException {
        InputStream stream = null;

        try {
            stream = context.getAssets().open(fileName);

            if(stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
        }

        return null;
    }

    /**
     * Imports data from the web as a string.
     * @param url URL to the data.
     * @return
     * @throws IOException
     */
    private static String importFromWeb(String url) throws IOException {
        InputStream stream = null;
        HttpsURLConnection connection = null;
        String result = null;

        try {
            connection = (HttpsURLConnection) new URL(url).openConnection();

            //For this use case, set HTTP method to GET.
            connection.setRequestMethod("GET");

            //Open communications link (network traffic occurs here).
            connection.connect();

            int responseCode = connection.getResponseCode();
            if (responseCode != HttpsURLConnection.HTTP_OK) {
                throw new IOException("HTTP error code: " + responseCode);
            }

            //Retrieve the response body as an InputStream.
            stream = connection.getInputStream();

            if (stream != null) {
                return readStreamAsString(stream);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            // Close Stream and disconnect HTTPS connection.
            if (stream != null) {
                stream.close();
            }
            if (connection != null) {
                connection.disconnect();
            }
        }

        return result;
    }

    /**
     * Reads an input stream as a string.
     * @param stream Stream to convert.
     * @return
     * @throws IOException
     */
    private static String readStreamAsString(InputStream stream) throws IOException {
        //Convert the contents of an InputStream to a String.
        BufferedReader in = new BufferedReader(new InputStreamReader(stream, "UTF-8"));

        String inputLine;
        StringBuffer response = new StringBuffer();

        while ((inputLine = in.readLine()) != null) {
            response.append(inputLine);
        }

        in.close();

        return response.toString();
    }
}
```

Poniższy kod pokazuje, jak używać tego narzędzia do importowania danych GEOJSON jako ciągu i zwracania go do wątku interfejsu użytkownika za pośrednictwem wywołania zwrotnego. W wywołaniu zwrotnym dane ciągu mogą być serializowane do kolekcji funkcji GEOJSON i dodawane do źródła danych. Opcjonalnie zaktualizuj aparat mapy, aby skoncentrować się na danych.

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

## <a name="vector-tile-source"></a>Źródło kafelków wektora

Źródło kafelka wektorowego opisuje, jak uzyskać dostęp do warstwy kafelków wektorowych. Użyj `VectorTileSource` klasy, aby utworzyć wystąpienie źródła kafelka wektora. Warstwy kafelków wektorowych są podobne do warstw kafelków, ale nie są one takie same. Warstwa kafelków jest obrazem rastrowym. Warstwy kafelków wektorowych są skompresowanym plikiem w formacie **PBF** . Ten skompresowany plik zawiera dane mapy wektorowej i co najmniej jedną warstwę. Plik może być renderowany i ustalany na podstawie stylu każdej warstwy. Dane w kafelku wektorowym zawierają funkcje geograficzne w postaci punktów, linii i wielokątów. Istnieje kilka zalet używania warstw kafelków wektorowych zamiast warstw kafelków rastrowych:

- Rozmiar pliku kafelka wektora jest zwykle znacznie mniejszy niż odpowiednik kafelka rastrowego. W związku z tym jest używana mniejsza przepustowość. Oznacza to małe opóźnienia, szybsze mapowanie i lepszy komfort pracy użytkowników.
- Ponieważ kafelki wektorowe są renderowane na kliencie, dostosowują się do rozdzielczości urządzenia, na którym są one wyświetlane. W związku z tym renderowane mapy pojawiają się dokładniej i są bardziej zdefiniowane przy użyciu funkcji Crystal Clear labels.
- Zmiana stylu danych w mapach wektorów nie wymaga ponownego pobierania danych, ponieważ nowy styl można zastosować na kliencie. Natomiast zmiana stylu warstwy kafelków rastrowych zazwyczaj wymaga załadowania kafelków z serwera, a następnie zastosowania nowego stylu.
- Ponieważ dane są dostarczane w formie wektorowej, do przygotowania danych nie jest wymagane przetwarzanie po stronie serwera. W związku z tym nowsze dane można szybciej udostępnić.

Azure Maps jest zgodna ze [specyfikacją kafelka wektora MapBox](https://github.com/mapbox/vector-tile-spec), otwartym standardem. Azure Maps udostępnia następujące usługi kafelków wektorowych jako część platformy:

- [](/rest/api/maps/renderv2/getmaptilepreview)  |  [Szczegóły formatu danych dokumentacji dotyczącej](https://developer.tomtom.com/maps-api/maps-api-documentation-vector/tile) kafelków dróg
- [](/rest/api/maps/traffic/gettrafficincidenttile)  |  [Szczegóły dotyczące formatu danych dokumentacji dotyczącej](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles) zdarzeń ruchu
- [](/rest/api/maps/traffic/gettrafficflowtile)  |  [Szczegóły formatu danych](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-flow/vector-flow-tiles) dokumentacji przepływu ruchu
- Kreator Azure Maps umożliwia również tworzenie niestandardowych kafelków wektorowych i uzyskiwanie do nich dostępu za pomocą funkcji [renderowania kafelków w wersji 2](/rest/api/maps/renderv2/getmaptilepreview)

> [!TIP]
> W przypadku używania kafelków obrazów wektorowych lub rastrowych z poziomu usługi Azure Maps Render z zestawem SDK sieci Web, można zastąpić `atlas.microsoft.com` symbolem zastępczym `azmapsdomain.invalid` . Ten symbol zastępczy zostanie zastąpiony tą samą domeną używaną przez mapę i automatycznie dołączać te same dane uwierzytelniania. Znacznie upraszcza to uwierzytelnianie za pomocą usługi renderowania przy użyciu uwierzytelniania Azure Active Directory.

Aby wyświetlić dane ze źródła kafelka wektorowego na mapie, Podłącz źródło do jednej z warstw renderowania danych. Wszystkie warstwy używające źródła wektora muszą określać `sourceLayer` wartość w opcjach. Poniższy kod ładuje usługę kafelków wektora przepływu ruchu Azure Maps jako źródło kafelka wektora, a następnie wyświetla ją na mapie przy użyciu warstwy liniowej. To źródło kafelka wektorowego zawiera pojedynczy zestaw danych w warstwie źródłowej o nazwie "przepływ ruchu". Dane wiersza w tym zestawie danych mają właściwość o nazwie `traffic_level` , która jest używana w tym kodzie do wybierania koloru i skalowania rozmiaru wierszy.

```java
//Formatted URL to the traffic flow vector tiles, with the maps subscription key appended to it.
String trafficFlowUrl = "https://azmapsdomain.invalid/traffic/flow/tile/pbf?api-version=1.0&style=relative&zoom={z}&x={x}&y={y}";

//Create a vector tile source and add it to the map.
VectorTileSource source = new VectorTileSource(
    tiles(new String[] { trafficFlowUrl }),
    maxSourceZoom(22)
);
map.sources.add(source);

//Create a layer for traffic flow lines.
LineLayer layer = new LineLayer(source,
    //The name of the data layer within the data source to pass into this rendering layer.
    sourceLayer("Traffic flow"),

    //Color the roads based on the traffic_level property.
    strokeColor(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, color(Color.RED)),
            stop(0.33, color(Color.YELLOW)),
            stop(0.66, color(Color.GREEN))
        )
    ),

    //Scale the width of roads based on the traffic_level property.
    strokeWidth(
        interpolate(
            linear(),
            get("traffic_level"),
            stop(0, 6),
            stop(1,1)
        )
    )
);

//Add the traffic flow layer below the labels to make the map clearer.
map.layers.add(layer, "labels");
```

![Mapuj z kolorowymi liniami drogowymi pokazującymi poziomy przepływu ruchu](media/create-data-source-android-sdk/android-vector-tile-source-line-layer.png)

## <a name="connecting-a-data-source-to-a-layer"></a>Łączenie źródła danych z warstwą

Dane są renderowane na mapie przy użyciu warstw renderowania. Pojedynczemu źródle danych może być przywoływana jedna lub więcej warstw renderowania. Następujące warstwy renderowania wymagają źródła danych:

- [Bąbelkowa warstwa](map-add-bubble-layer-android.md) renderuje dane punktowe jako skalowane kółka na mapie.
- [Warstwa symboli](how-to-add-symbol-to-android-map.md) — renderuje dane punktu jako ikony lub tekst.
- [Warstwa mapy cieplnej](map-add-heat-map-layer-android.md) — renderuje dane punktu jako mapę cieplną gęstości.
- [Warstwa linii](android-map-add-line-layer.md) — Renderuj linię i Renderuj konspekt wielokątów.
- [Warstwa wielokątów](how-to-add-shapes-to-android-map.md) — wypełnia obszar wielokąta przy użyciu pełnego koloru lub wzorca obrazu.

Poniższy kod przedstawia sposób tworzenia źródła danych, dodawania go do mapy i łączenia go z warstwą bąbelkową. A następnie zaimportuj dane punktu GEOJSON z lokalizacji zdalnej do źródła danych.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a layer that defines how to render points in the data source and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Import the geojson data and add it to the data source.
Utils.importData("URL_or_FilePath_to_GeoJSON_data",
    this,
    (String result) -> {
        //Parse the data as a GeoJSON Feature Collection.
        FeatureCollection fc = FeatureCollection.fromJson(result);

        //Add the feature collection to the data source.
        dataSource.add(fc);

        //Optionally, update the maps camera to focus in on the data.

        //Calculate the bounding box of all the data in the Feature Collection.
        BoundingBox bbox = MapMath.fromData(fc);

        //Update the maps camera so it is focused on the data.
        map.setCamera(
            bounds(bbox),
            padding(20));
    });
```

Istnieją dodatkowe warstwy renderowania, które nie łączą się z tymi źródłami danych, ale bezpośrednio ładują dane do renderowania.

- [Warstwa kafelków](how-to-add-tile-layer-android-map.md) nakłada warstwę kafelków rastrowych na podstawie mapy.

## <a name="one-data-source-with-multiple-layers"></a>Jedno źródło danych z wieloma warstwami

Wiele warstw można podłączyć do pojedynczego źródła danych. Istnieje wiele różnych scenariuszy, w których ta opcja jest przydatna. Rozważmy na przykład scenariusz, w którym użytkownik rysuje wielokąt. Powinienmy renderować i wypełnić obszar Wielokąt, gdy użytkownik doda punkty do mapy. Dodanie linii z stylem do konturu Wielokąt sprawia, że krawędzie wielokąta są łatwiej widoczne podczas rysowania przez użytkownika. Aby wygodnie edytować poszczególne położenia w wielokąta, możemy dodać uchwyt, taki jak numer PIN lub znacznik, nad każdą pozycją.

![Mapa pokazująca wiele warstw renderowania danych z jednego źródła danych](media/create-data-source-android-sdk/multiple-layers-one-datasource.png)

W większości platform mapowania potrzebny jest obiekt wielokątny, obiekt linii i numer PIN dla każdej pozycji w wielokąta. Gdy Wielokąt jest modyfikowany, trzeba ręcznie zaktualizować linię i numery PIN, co może szybko stać się skomplikowany.

W przypadku Azure Maps, wystarczy, że jest to pojedynczy Wielokąt w źródle danych, jak pokazano w poniższym kodzie.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon and add it to the data source.
source.add(Polygon.fromLngLats(/* List of points */));

//Create a polygon layer to render the filled in area of the polygon.
PolygonLayer polygonLayer = new PolygonLayer(source,
    fillColor("rgba(255,165,0,0.2)")
);

//Create a line layer for greater control of rendering the outline of the polygon.
LineLayer lineLayer = new LineLayer(source,
    strokeColor("orange"),
    strokeWidth(2f)
);

//Create a bubble layer to render the vertices of the polygon as scaled circles.
BubbleLayer bubbleLayer = new BubbleLayer(source,
    bubbleColor("orange"),
    bubbleRadius(5f),
    bubbleStrokeColor("white"),
    bubbleStrokeWidth(2f)
);

//Add all layers to the map.
map.layers.add(new Layer[] { polygonLayer, lineLayer, bubbleLayer });
```

> [!TIP]
> Podczas dodawania warstw do mapy przy użyciu `map.layers.add` metody, identyfikator lub wystąpienie istniejącej warstwy można przesłać jako drugi parametr. Dzięki temu mapa będzie wstawiać nową warstwę dodawaną poniżej istniejącej warstwy. W elemencie do przekazywania w identyfikatorze warstwy ta metoda obsługuje również następujące wartości.
>
> - `"labels"` — Wstawia nową warstwę poniżej warstw etykiety mapy.
> - `"transit"` — Wstawia nową warstwę poniżej warstwy mapy drogowej i tranzytowej.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby uzyskać więcej przykładów kodu do dodania do Twoich map:

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie mapy cieplnej](map-add-heat-map-layer-android.md)

> [!div class="nextstepaction"]
> [Przykłady kodu zestawu SDK sieci Web](/samples/browse/?products=azure-maps)