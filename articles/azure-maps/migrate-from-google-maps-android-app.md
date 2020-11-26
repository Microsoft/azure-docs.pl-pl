---
title: Samouczek — Migrowanie aplikacji systemu Android | Mapy Microsoft Azure
description: Samouczek dotyczący sposobu migrowania aplikacji systemu Android z usługi Mapy Google do usługi Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: b096b24acd5cf65f6ad3e9eabb1d536b3aae0168
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187072"
---
# <a name="tutorial---migrate-an-android-app-from-google-maps"></a>Samouczek — Migrowanie aplikacji systemu Android z usługi Google Maps

Android SDK Azure Maps ma interfejs API, który jest podobny do zestawu SDK sieci Web. Jeśli zostały opracowane z jednego z tych zestawów SDK, mają zastosowanie wiele z tych samych koncepcji, najlepszych rozwiązań i architektur. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Załaduj mapę
> * Lokalizowanie mapy
> * Dodaj znaczniki, linie łamane i wielokąty.
> * Nałóż warstwę kafelków
> * Wyświetlanie danych o ruchu

Android SDK Azure Maps obsługuje minimalną wersję systemu Android dla interfejsu API 21: Android 5.0.0 (lizak).

Wszystkie przykłady są dostępne w języku Java. można jednak używać Kotlin z Android SDK Azure Maps.

Aby uzyskać więcej informacji na temat programowania przy użyciu Android SDK przez Azure Maps, zobacz [przewodniki dotyczące wykonywania Azure Maps Android SDK](how-to-use-android-map-control-library.md).

## <a name="prerequisites"></a>Wymagania wstępne 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
2. [Utwórz konto Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account)
3. [Uzyskaj podstawowy klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account), nazywany także kluczem podstawowym lub kluczem subskrypcji. Aby uzyskać więcej informacji na temat uwierzytelniania w Azure Maps, zobacz [Zarządzanie uwierzytelnianiem w programie Azure Maps](how-to-manage-authentication.md).

## <a name="load-a-map"></a>Załaduj mapę

Załadowanie mapy w aplikacji systemu Android przy użyciu usługi Google lub Azure Maps składa się z podobnych kroków. W przypadku korzystania z dowolnego zestawu SDK należy:

* Uzyskaj interfejs API lub klucz subskrypcji, aby uzyskać dostęp do dowolnej platformy.
* Dodaj plik XML do działania, aby określić, gdzie powinna być renderowana Mapa i jak powinna zostać ustanowiona.
* Zastąp wszystkie metody cyklu życiowego z działania zawierającego widok mapy do odpowiednich metod w klasie map. W szczególności należy zastąpić następujące metody:
    * `onCreate(Bundle)`
    * `onStart()`
    * `onResume()`
    * `onPause()`
    * `onStop()`
    * `onDestroy()`
    * `onSaveInstanceState(Bundle)`
    * `onLowMemory()`
* Poczekaj, aż mapa będzie gotowa, zanim spróbuje uzyskać dostęp i program.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Aby wyświetlić mapę przy użyciu zestawu SDK usługi Google Maps dla systemu Android, należy wykonać następujące czynności:

1. Upewnij się, że zainstalowano usługi Google Play Services.
2. Dodaj zależność usługi Google Maps do pliku  **Gradle. Build** modułu:

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

3. Dodaj klucz interfejsu API usługi Google Maps w sekcji aplikacji pliku usługi  **Google \_ Maps \_api.xml** :

    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

4. Dodaj fragment mapy do działania głównego:

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

5. W pliku **MAINS. Java** należy zaimportować zestaw SDK usługi Google Maps. Przekazuj wszystkie metody cyklu życiowego z działania zawierającego widok mapy do odpowiednich z nich w klasie mapy. Pobierz `MapView` wystąpienie ze fragmentu mapy przy użyciu `getMapAsync(OnMapReadyCallback)` metody. `MapView`Automatycznie inicjuje system map i widok. Edytuj plik **MAINS. Java** w następujący sposób:

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

Po uruchomieniu aplikacji formant mapy jest ładowany jak na poniższej ilustracji.

![Proste usługi Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Aby wyświetlić mapę przy użyciu zestawu Azure Maps SDK dla systemu Android, należy wykonać następujące czynności:

1. Otwórz plik **Build. Gradle** najwyższego poziomu i Dodaj następujący kod do sekcji **wszystkie projekty** blokowe:

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Zaktualizuj **aplikację/Build. Gradle** i Dodaj do niej następujący kod:

    1. Upewnij się, że **minSdkVersion** projektu ma interfejs API w wersji 21 lub nowszej.

    2. Dodaj następujący kod do sekcji systemu Android:

        ```java
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. Aktualizowanie bloku zależności. Dodaj nową linię zależności implementacji dla najnowszej Azure Maps Android SDK:

        ```java
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Android SDK Azure Maps są regularnie uaktualniane i rozszerzane. Aby uzyskać najnowszy Azure Maps numer wersji, można zobaczyć [formant wprowadzenie do mapy systemu Android](how-to-use-android-map-control-library.md) . Ponadto można ustawić numer wersji z "0,2" na "0 +", aby kod zawsze wskazywał najnowszą wersję.

    4. Przejdź do **pliku** na pasku narzędzi, a następnie kliknij pozycję **Synchronizuj projekt z plikami Gradle**.

3. Dodawanie fragmentu mapy do działania głównego (zasoby w \> układzie pwd \> \_main.xml):

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        >

        <com.microsoft.azure.maps.mapcontrol.MapControl
            android:id="@+id/mapcontrol"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            />
    </FrameLayout>
    ```

4. W pliku **MAINS. Java** należy:

    * Importuje zestaw Azure Maps SDK
    * Ustawianie informacji o uwierzytelnianiu Azure Maps
    * Pobieranie wystąpienia kontrolki mapy w metodzie **OnCreate**

     Ustaw informacje o uwierzytelnianiu w `AzureMaps` klasie przy użyciu `setSubscriptionKey` `setAadProperties` metod lub. Ta globalna aktualizacja, należy się upewnić, że informacje o uwierzytelnianiu są dodawane do każdego widoku.

    Kontrolka mapy zawiera własne metody cyklu życia do zarządzania cyklem życia OpenGL dla systemu Android. Te metody muszą być wywoływane bezpośrednio z zawartego działania. Aby poprawnie wywołać metody cyklu życia kontrolki mapy, należy zastąpić następujące metody cyklu życia w działaniu, które zawiera formant mapy. Wywoływanie odpowiedniej metody sterującej mapy.

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    Edytuj plik **MAINS. Java** w następujący sposób:

    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
     
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }
    }
    ```

Po uruchomieniu aplikacji formant mapy zostanie załadowany jak na poniższej ilustracji.


![Prosta Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)

Zauważ, że formant Azure Maps obsługuje bardziej powiększanie i oferuje więcej informacji o widoku światowym.

> [!TIP]
> Jeśli używasz emulatora systemu Android na komputerze z systemem Windows, mapa może nie być renderowana z powodu konfliktów ze sposobem renderowania grafiki OpenGL i oprogramowania. Aby rozwiązać ten problem, w przypadku niektórych osób działały następujące czynności. Otwórz Menedżera AVD i wybierz urządzenie wirtualne do edycji. Przewiń w dół do panelu **Weryfikuj konfigurację** . W sekcji **emulowana wydajność** ustaw opcję **grafika** na **sprzęt**.

## <a name="localizing-the-map"></a>Lokalizowanie mapy

Lokalizacja jest ważna, jeśli odbiorcy są rozproszeni w wielu krajach/regionach lub mówisz w różnych językach.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Dodaj następujący kod do metody, `onCreate` Aby ustawić język mapy. Należy dodać kod przed ustawieniem widoku kontekstu mapy. Kod języka "fr" ogranicza język do języka francuskiego.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

Oto przykład mapy Google z ustawionym językiem "fr".

![Lokalizacja usługi Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Azure Maps udostępnia trzy różne sposoby ustawiania języka i regionalnego widoku mapy. Pierwsza opcja polega na przekazaniem informacji o języku i widoku regionalnym do `AzureMaps` klasy. Ta opcja używa metod statycznych `setLanguage` i `setView` globalnie. Oznacza to, że język domyślny i widok regionalny są ustawiane dla wszystkich kontrolek Azure Maps załadowanych w aplikacji. Ten przykład ustawia francuski przy użyciu kodu języka "fr-FR".

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

Druga opcja polega na przejściu języka i wyświetleniu informacji w kodzie XML formantu mapy.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

Trzecią opcją jest Programowanie języka i widoku mapy regionalnej przy użyciu `setStyle` metody Maps. Ta opcja aktualizuje język i widok regionalny wszędzie tam, gdzie wykonywany jest kod.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

Oto przykład Azure Maps z językiem ustawionym na "fr-FR".

![Lokalizacja Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)

Zapoznaj się z pełną listą [obsługiwanych języków](supported-languages.md).

## <a name="setting-the-map-view"></a>Ustawianie widoku mapy

Mapy dynamiczne w obu Azure Maps i Google Maps można programistycznie przenieść do nowych lokalizacji geograficznych, wywołując odpowiednie metody. Przyjrzyjmy się mapie do obrazów satelitarnych satelitów, Wyśrodkuj mapę w lokalizacji ze współrzędnymi i zmień poziom powiększenia. Na potrzeby tego przykładu będziemy używać szerokości geograficznej: 35,0272, długości geograficznej:-111,0225 i poziomu powiększenia 15.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Aparat kontrolki mapy usługi Google Maps można programistycznie przenieść przy użyciu `moveCamera` metody. `moveCamera`Metoda pozwala określić środek mapy i poziom powiększenia. `setMapType`Metoda zmienia typ mapy do wyświetlenia.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

![Widok zestawu usługi Google Maps](media/migrate-google-maps-android-app/google-maps-set-view.png)

> [!NOTE]
> Usługa mapy Google używa kafelków, które są 256 pikseli w wymiarach, podczas gdy Azure Maps używa większego rozmiaru 512 pikseli. Zmniejsza to liczbę żądań sieci wymaganych przez Azure Maps do załadowania tego samego obszaru mapy co Google Maps. Aby osiągnąć ten sam obszar widoczny jako mapa w usłudze Google Maps, należy odjąć poziom powiększenia używany w usłudze Google Maps przy użyciu Azure Maps.

### <a name="after-azure-maps"></a>Po: Azure Maps

Jak wspomniano wcześniej, aby osiągnąć ten sam obszar widoczny w Azure Maps Odejmij poziom powiększenia używany w usłudze Google Maps według jednego. W takim przypadku należy użyć poziomu powiększenia o wartości 14.

Początkowy widok mapy można ustawić w atrybutach XML w kontrolce mapy.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

Widok mapy może być zaprogramowany przy użyciu map `setCamera` i `setStyle` metod.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

![Widok zestawu Azure Maps](media/migrate-google-maps-android-app/azure-maps-set-view.png)

**Dodatkowe zasoby:**

* [Obsługiwane style mapy](supported-map-styles.md)

## <a name="adding-a-marker"></a>Dodawanie znacznika

Dane punktów są często renderowane przy użyciu obrazu na mapie. Te obrazy są określane jako znaczniki, pinezki, numery PIN lub symbole. Poniższe przykłady renderują dane punktu jako znaczniki na mapie na szerokości geograficznej: 51,5, Długość geograficzna: – 0,2.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Dzięki usłudze Google Maps znaczniki są dodawane za pomocą `addMarker` metody Maps.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

![Znacznik Google Maps](media/migrate-google-maps-android-app/google-maps-marker.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

W Azure Maps Renderuj dane punktu na mapie, najpierw dodając dane do źródła danych. Następnie połączenie tego źródła danych z warstwą symboli. Źródło danych optymalizuje zarządzanie danymi przestrzennymi w formancie mapy. Warstwa symboli określa sposób renderowania danych punktu przy użyciu jako obrazu lub tekstu.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

![Znacznik Azure Maps](media/migrate-google-maps-android-app/azure-maps-marker.png)

## <a name="adding-a-custom-marker"></a>Dodawanie znacznika niestandardowego

Obrazy niestandardowe mogą służyć do reprezentowania punktów na mapie. Mapa w poniższych przykładach używa obrazu niestandardowego do wyświetlania punktu na mapie. Punkt ma wartość Latitude: 51,5 i Długość geograficzna: – 0,2. Kotwica przesunięcia położenia znacznika, tak aby punkt ikony pinezki wyrównany do poprawnego położenia na mapie.

<center>

![żółty obraz pinezki](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png</center>

W obu przykładach Powyższy obraz jest dodawany do folderu do rysowania zasobów aplikacji.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Za pomocą usługi Google Maps można używać obrazów niestandardowych dla znaczników. Załaduj obrazy niestandardowe przy użyciu `icon` opcji znacznika. Aby wyrównać punkt obrazu do współrzędnej, użyj `anchor` opcji. Zakotwiczenie jest względem wymiarów obrazu. W tym przypadku zakotwiczeniem jest 0,2 jednostek szerokości i 1 jednostka wysoka.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f));
}
```

![Znacznik niestandardowy usługi Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Warstwy symboli w Azure Maps obsługują obrazy niestandardowe, ale najpierw muszą zostać załadowane do zasobów mapy i przypisany unikatowy identyfikator. Następnie warstwa symboli musi odwoływać się do tego identyfikatora. Przesuń symbol, aby wyrównać go do poprawnego punktu w obrazie przy użyciu `iconOffset` opcji. Przesunięcie ikony jest w pikselach. Domyślnie przesunięcie jest względem środkowego środka obrazu, ale tę wartość przesunięcia można dostosować przy użyciu `iconAnchor` opcji. Ten przykład ustawia `iconAnchor` opcję na `"center"` . Za pomocą przesunięcia ikony można przenieść obraz o pięć pikseli do prawej i 15 pikseli, aby wyrównać punkt obrazu pinezki.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

![Azure Maps znacznika niestandardowego](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)

## <a name="adding-a-polyline"></a>Dodawanie linii łamanej

Linie łamane są używane do reprezentowania linii lub ścieżki na mapie. W poniższych przykładach pokazano, jak utworzyć kreskowaną linię łamaną na mapie.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Dzięki usłudze Google Maps Renderuj linię łamaną przy użyciu `PolylineOptions` klasy. Dodaj linię łamaną do mapy za pomocą `addPolyline` metody. Ustaw kolor pociągnięcia przy użyciu `color` opcji. Ustaw szerokość obrysu przy użyciu `width` opcji. Dodaj tablicę kreskowaną pędzla przy użyciu `pattern` opcji.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

![Google Maps — łamana](media/migrate-google-maps-android-app/google-maps-polyline.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

W Azure Maps linie łamane są nazywane `LineString` lub `MultiLineString` obiektami. Dodaj te obiekty do źródła danych i Renderuj je przy użyciu warstwy liniowej. Ustaw szerokość obrysu przy użyciu `strokeWidth` opcji. Dodaj tablicę kreskowaną pędzla przy użyciu `strokeDashArray` opcji.

Szerokość obrysu i tablica kreskowana "pikseli" w Azure Maps Web SDK są takie same, jak w usłudze Google Maps. Oba te same wartości są akceptowane w celu uzyskania tych samych wyników.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

![Azure Maps łamana](media/migrate-google-maps-android-app/azure-maps-polyline.png)

## <a name="adding-a-polygon"></a>Dodawanie wielokątu

Wielokąty są używane do reprezentowania obszaru na mapie. W następnych przykładach pokazano, jak utworzyć wielokąt. Ten Wielokąt tworzy Trójkąt w oparciu o współrzędne środkowe mapy.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Za pomocą usługi Google Maps Renderuj Wielokąt przy użyciu `PolygonOptions` klasy. Dodaj wielokąt do mapy za pomocą `addPolygon` metody. Ustaw kolor wypełnienia i pociągnięcia odpowiednio przy `fillColor` użyciu `strokeColor` opcji i. Ustaw szerokość obrysu przy użyciu `strokeWidth` opcji.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

![Wielokąt usługi Google Maps](media/migrate-google-maps-android-app/google-maps-polygon.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

W Azure Maps, Dodaj `Polygon` `MultiPolygon` obiekty i do źródła danych i Renderuj je na mapie przy użyciu warstw. Renderowanie obszaru wielokąta w warstwie wielokąta. Renderowanie konspektu wielokąta przy użyciu warstwy liniowej. Ustaw kolor obrysu i szerokość przy użyciu `strokeColor` `strokeWidth` opcji i.

Jednostki z szerokością i tablicą łącznika "piksele" w Azure Maps zestawie Web SDK są wyrównane z odpowiednimi jednostkami w usłudze mapy Google. Obie akceptują te same wartości i generują te same wyniki.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```
![Azure Maps Wielokąt](media/migrate-google-maps-android-app/azure-maps-polygon.png)

## <a name="overlay-a-tile-layer"></a>Nałóż warstwę kafelków

 Użyj warstw kafelków, aby nałożyć na siebie obrazy warstwowe, które zostały podzielone na mniejsze obrazy z podziałem na fragmenty. To podejście jest typowym sposobem nakładania obrazów warstwowych lub dużych zestawów danych. Warstwy kafelków są znane jako nakładki obrazów w usłudze mapy Google.

Poniższe przykłady nakładają warstwę kafelków radarowych z Iowa środowiska Mesonet of Iowa State University. Rozmiary kafelków są 256 pikseli.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Dzięki usłudze Google Maps warstwa kafelków może zostać przełożone na wierzchu mapy. Użyj `TileOverlayOptions` klasy. Dodaj warstwę kafelków do mapy za pomocą `addTileLauer` metody. Aby sprawić, że kafelki są częściowo przezroczyste, `transparency` opcja jest ustawiona na 0,2 lub 20% przezroczyste.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

![Warstwa kafelków usługi Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Warstwę kafelków można dodać do mapy w podobny sposób, jak każda inna warstwa. Sformatowany adres URL, który ma symbole zastępcze x, y i zoom; `{x}`, `{y}` `{z}` odpowiednio jest używany do określenia warstwy, w której mają być dostępne kafelki. Ponadto warstwy kafelków w Azure Maps obsługują `{quadkey}` , `{bbox-epsg-3857}` i `{subdomain}` symboli zastępczych. Aby warstwa kafelków została częściowo przezroczysta, używana jest wartość nieprzezroczystości 0,8. Nieprzezroczystość i przezroczystość, chociaż podobne, używaj odwróconych wartości. Aby przeprowadzić konwersję między obiema opcjami, Odejmij ich wartość od liczby.

> [!TIP]
> W Azure Maps jest wygodne renderowanie warstw poniżej innych warstw, w tym warstw mapy podstawowej. Ponadto często pożądane jest renderowanie warstw kafelków poniżej etykiet mapy, dzięki czemu można je łatwo odczytać. `map.layers.add`Metoda przyjmuje drugi parametr, który jest identyfikatorem warstwy, w której ma zostać wstawiona Nowa warstwa poniżej. Aby wstawić warstwę kafelków pod etykietami mapy, można użyć następującego kodu: `map.layers.add(myTileLayer, "labels");`

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

![Warstwa kafelków Azure Maps](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)

## <a name="show-traffic"></a>Wyświetlanie ruchu

Zarówno Azure Maps, jak i usługi Google Maps zawierają opcje umożliwiające nakładanie danych ruchu.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Dzięki usłudze Google Maps dane przepływu ruchu mogą zostać zastąpione na podstawie mapy, przekazując do metody mapy wartość true `setTrafficEnabled` .

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

![Ruch usługi Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Azure Maps oferuje kilka różnych opcji wyświetlania ruchu. Zdarzenia dotyczące ruchu, takie jak zamknięcie dróg i wypadki, mogą być wyświetlane jako ikony na mapie. Przepływ ruchu i kodowane kolorami drogi można przemieścić na mapie. Kolory można zmodyfikować tak, aby były wyświetlane względem wartości granicznej Limit szybkości względem normalnego oczekiwanego opóźnienia lub bezwzględnego opóźnienia. Dane zdarzenia w Azure Maps są aktualizowane co minutę, a dane przepływu są aktualizowane co dwie minuty.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

![Ruch Azure Maps](media/migrate-google-maps-android-app/azure-maps-traffic.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat Android SDK Azure Maps:

> [!div class="nextstepaction"]
> [Jak używać kontrolki mapy systemu Android](how-to-use-android-map-control-library.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli do mapy systemu Android](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie kształtów do mapy systemu Android](./how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Zmień style mapy w usłudze mapy systemu Android](./set-android-map-styles.md)