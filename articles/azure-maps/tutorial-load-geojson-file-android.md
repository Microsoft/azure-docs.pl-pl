---
title: 'Samouczek: ładowanie danych GEOJSON do Azure Maps Android SDK | Mapy Microsoft Azure'
description: Samouczek dotyczący ładowania pliku danych GEOJSON do Azure Maps zestawu SDK mapy systemu Android.
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 8300a7c120ce816c8068a88fa69f4f978fa664ca
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034510"
---
# <a name="tutorial-load-geojson-data-into-azure-maps-android-sdk"></a>Samouczek: ładowanie danych GEOJSON do Azure Maps Android SDK

Ten samouczek przeprowadzi Cię przez proces importowania pliku GEOJSON danych lokalizacji do Android SDK Azure Maps. Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Dodaj Azure Maps do aplikacji systemu Android.
> * Utwórz źródło danych i załaduj je w pliku GEOJSON z pliku lokalnego lub sieci Web.
> * Wyświetl dane na mapie.

## <a name="prerequisites"></a>Wymagania wstępne

1. Ukończ [Przewodnik Szybki Start: Tworzenie aplikacji dla systemu Android](quick-android-map.md). Ten samouczek spowoduje zwiększenie kodu używanego w tym przewodniku Szybki Start.
2. Pobierz [przykładowe punkty z pliku o znaczeniu](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) GEOJSON.

### <a name="import-geojson-data-from-web-or-assets-folder"></a>Importuj dane GEOJSON z folderu sieci Web lub zasobów

Większość plików GeoJSON otacza wszystkie dane w `FeatureCollection` . Z tego względu, jeśli pliki GEOJSON są ładowane do aplikacji jako ciąg, można je przesłać do metody statycznej kolekcji funkcji `fromJson` , która spowoduje deserializacja ciągu do obiektu GEOjson, `FeatureCollection` który można dodać do mapy.

Poniższe kroki pokazują, jak zaimportować plik GEOJSON do aplikacji i zdeserializować go jako obiekt GEOJSON `FeatureCollection` .

1. Ukończ [Przewodnik Szybki Start: Tworzenie aplikacji dla systemu Android](quick-android-map.md) , wykonując następujące kroki kompilacji na tej aplikacji.
2. W panelu projektu programu Android Studio kliknij prawym przyciskiem myszy folder **aplikacja** i przejdź do `New > Folder > Assets Folder` .
3. Przeciągnij i upuść [przykładowe punkty pliku o znaczeniu](https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/data/geojson/SamplePoiDataSet.json) GEOJSON do folderu Assets.

::: zone pivot="programming-language-java-android"

4. Utwórz nowy plik o nazwie narzędzia **. Java** i Dodaj do niego następujący kod. Ten kod zawiera metodę statyczną o nazwie `importData` , która asynchronicznie importuje plik z `assets` folderu aplikacji lub z sieci Web przy użyciu adresu URL jako ciągu i zwraca go z powrotem do wątku interfejsu użytkownika przy użyciu prostej metody wywołania zwrotnego.

    ```java
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;
    
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

5. Przejdź do pliku **MAINS. Java** i Dodaj następujący kod wewnątrz wywołania zwrotnego dla `mapControl.onReady` zdarzenia, który znajduje się wewnątrz `onCreate` metody. Ten kod używa narzędzia Import do odczytu w **SamplePoiDataSet.js** pliku jako ciąg, a następnie deserializacji go jako kolekcję funkcji przy użyciu statycznej `fromJson` metody `FeatureCollection` klasy. Ten kod oblicza również obszar pola ograniczenia dla wszystkich danych w kolekcji funkcji i używa tego do ustawienia aparatu mapy, aby skoncentrować się na danych.

    ```java
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json",
        this,
        (String result) -> {
            //Parse the data as a GeoJSON Feature Collection.
            FeatureCollection fc = FeatureCollection.fromJson(result);
    
            //Add the feature collection to the data source.
            source.add(fc);
    
            //Optionally, update the maps camera to focus in on the data.
    
            //Calculate the bounding box of all the data in the Feature Collection.
            BoundingBox bbox = MapMath.fromData(fc);
    
            //Update the maps camera so it is focused on the data.
            map.setCamera(
                bounds(bbox),

                //Padding added to account for pixel size of rendered points.
                padding(20)
            );
        });
    ```

6. Używając kodu do załadowania danych GEOJSON źródła danych, teraz musimy określić sposób wyświetlania danych na mapie. Istnieje kilka różnych warstw renderowania dla danych punktów; Warstwa [bąbelków](map-add-bubble-layer-android.md), warstwa [symboli](how-to-add-symbol-to-android-map.md)i [warstwa mapy cieplnej](map-add-heat-map-layer-android.md) są najczęściej używanymi warstwami. Dodaj następujący kod, aby renderować dane w warstwie bąbelkowej w wywołaniu zwrotnym `mapControl.onReady` zdarzenia po kodzie do importowania danych.

    ```java
    //Create a layer and add it to the map.
    BubbleLayer layer = new BubbleLayer(source);
    map.layers.add(layer);
    ```

::: zone-end

::: zone pivot="programming-language-kotlin"

4. Utwórz nowy plik o nazwie narzędzia **. kt** i Dodaj do niego następujący kod. Ten kod zawiera metodę statyczną o nazwie `importData` , która asynchronicznie importuje plik z `assets` folderu aplikacji lub z sieci Web przy użyciu adresu URL jako ciągu i zwraca go z powrotem do wątku interfejsu użytkownika przy użyciu prostej metody wywołania zwrotnego.

    ```kotlin
    //Modify the package name as needed to align with your application.
    package com.example.myapplication;

    import android.content.Context
    import android.os.Handler
    import android.os.Looper
    import android.webkit.URLUtil
    import java.net.URL
    import java.util.concurrent.ExecutorService
    import java.util.concurrent.Executors
    
    class Utils {
        companion object {
    
            /**
             * Imports data from a web url or asset file name and returns it to a callback.
             * @param urlOrFileName A web url or asset file name that points to data to load.
             * @param context The context of the app.
             * @param callback The callback function to return the data to.
             */
            fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit) {
                importData(urlOrFileName, context, callback, null)
            }
    
            /**
             * Imports data from a web url or asset file name and returns it to a callback.
             * @param urlOrFileName A web url or asset file name that points to data to load.
             * @param context The context of the app.
             * @param callback The callback function to return the data to.
             * @param error A callback function to return errors to.
             */
            public fun importData(urlOrFileName: String?, context: Context, callback: (String?) -> Unit, error: ((String?) -> Unit)?) {
                if (urlOrFileName != null && callback != null) {
                    val executor: ExecutorService = Executors.newSingleThreadExecutor()
                    val handler = Handler(Looper.getMainLooper())
                    executor.execute {
                        var data: String? = null
                        
                        try {
                            data = if (URLUtil.isNetworkUrl(urlOrFileName)) {
                                URL(urlOrFileName).readText()
                            } else { //Assume file is in assets folder.
                                context.assets.open(urlOrFileName).bufferedReader().use{
                                    it.readText()
                                }
                            }
    
                            handler.post {
                                //Ensure the resulting data string is not null or empty.
                                if (data != null && !data.isEmpty()) {
                                    callback(data)
                                } else {
                                    error!!("No data imported.")
                                }
                            }
                        } catch (e: Exception) {
                            error!!(e.message)
                        }
                    }
                }
            }
        }
    }
    ```

5. Przejdź do pliku **MAINS. kt** i Dodaj następujący kod wewnątrz wywołania zwrotnego dla `mapControl.onReady` zdarzenia, który znajduje się wewnątrz `onCreate` metody. Ten kod używa narzędzia Import do odczytu w **SamplePoiDataSet.js** pliku jako ciąg, a następnie deserializacji go jako kolekcję funkcji przy użyciu statycznej `fromJson` metody `FeatureCollection` klasy. Ten kod oblicza również obszar pola ograniczenia dla wszystkich danych w kolekcji funkcji i używa tego do ustawienia aparatu mapy, aby skoncentrować się na danych.

    ```kotlin
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);
    
    //Import the GeoJSON data and add it to the data source.
    Utils.importData("SamplePoiDataSet.json", this) { 
        result: String? ->
            //Parse the data as a GeoJSON Feature Collection.
             val fc = FeatureCollection.fromJson(result!!)
    
            //Add the feature collection to the data source.
            source.add(fc)
    
            //Optionally, update the maps camera to focus in on the data.
    
            //Calculate the bounding box of all the data in the Feature Collection.
            val bbox = MapMath.fromData(fc);

            //Update the maps camera so it is focused on the data.
            map.setCamera(
                bounds(bbox),

                //Padding added to account for pixel size of rendered points.
                padding(20)
            )
        }
    ```

6. Używając kodu do załadowania danych GEOJSON źródła danych, teraz musimy określić sposób wyświetlania danych na mapie. Istnieje kilka różnych warstw renderowania dla danych punktów; Warstwa [bąbelków](map-add-bubble-layer-android.md), warstwa [symboli](how-to-add-symbol-to-android-map.md)i [warstwa mapy cieplnej](map-add-heat-map-layer-android.md) są najczęściej używanymi warstwami. Dodaj następujący kod, aby renderować dane w warstwie bąbelkowej w wywołaniu zwrotnym `mapControl.onReady` zdarzenia po kodzie do importowania danych.

    ```kotlin
    //Create a layer and add it to the map.
    val layer = new BubbleLayer(source)
    map.layers.add(layer)
    ```

::: zone-end

7. Uruchom aplikację. Mapa zostanie wyświetlona w Stanach Zjednoczonych, z kołami nałożonymi dla każdej lokalizacji w pliku GEOJSON.

    ![Mapa USA z wyświetlanymi danymi z pliku GEOJSON](media/tutorial-load-geojson-file-android/android-import-geojson.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Wykonaj następujące kroki, aby wyczyścić zasoby z tego samouczka:

1. Zamknij Android Studio i Usuń utworzoną aplikację.
2. Jeśli aplikacja została przetestowana na urządzeniu zewnętrznym, odinstaluj ją z tego urządzenia.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe przykłady kodu i zapoznać się z interaktywnym środowiskiem kodowania:

> [!div class="nextstepaction"]
> [Korzystanie z wyrażeń stylu opartych na danych](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Wyświetlanie informacji o funkcjach](display-feature-information-android.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](how-to-add-shapes-to-android-map.md)
