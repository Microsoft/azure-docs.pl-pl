---
title: Samouczek — Migrowanie aplikacji systemu Android | Mapy Microsoft Azure
description: Samouczek dotyczący sposobu migrowania aplikacji systemu Android z usługi Mapy Google do usługi Microsoft Azure Maps
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 3d160649008199233fa0b676d938470569a27853
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102101498"
---
# <a name="tutorial-migrate-an-android-app-from-google-maps"></a>Samouczek: Migrowanie aplikacji systemu Android z usługi Google Maps

Android SDK Azure Maps ma interfejs API, który jest podobny do zestawu SDK sieci Web. Jeśli zostały opracowane z jednego z tych zestawów SDK, mają zastosowanie wiele z tych samych koncepcji, najlepszych rozwiązań i architektur. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Załaduj mapę
> * Lokalizowanie mapy
> * Dodaj znaczniki, linie łamane i wielokąty.
> * Nałóż warstwę kafelków
> * Wyświetlanie danych o ruchu

Wszystkie przykłady są dostępne w języku Java. można jednak używać Kotlin z Android SDK Azure Maps.

Aby uzyskać więcej informacji na temat programowania przy użyciu Android SDK przez Azure Maps, zobacz [przewodniki dotyczące wykonywania Azure Maps Android SDK](how-to-use-android-map-control-library.md).

## <a name="prerequisites"></a>Wymagania wstępne

1. Utwórz konto Azure Maps, logując się do [Azure Portal](https://portal.azure.com). Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
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

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

5. W pliku **MAINS. kt** należy zaimportować zestaw SDK usługi Google Maps. Przekazuj wszystkie metody cyklu życiowego z działania zawierającego widok mapy do odpowiednich z nich w klasie mapy. Pobierz `MapView` wystąpienie ze fragmentu mapy przy użyciu `getMapAsync(OnMapReadyCallback)` metody. `MapView`Automatycznie inicjuje system map i widok. Edytuj plik **MAINS. kt** w następujący sposób:

    ```kotlin
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
 
    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle

    class MainActivity : AppCompatActivity() implements OnMapReadyCallback {
    
        var mapView: MapView? = null
    
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
    
            mapView = findViewById(R.id.myMap)
    
            mapView?.onCreate(savedInstanceState)
            mapView?.getMapAsync(this)
        }

        public fun onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        public override fun onStart() {
            super.onStart()
            mapView?.onStart()
        }
    
        public override fun onResume() {
            super.onResume()
            mapView?.onResume()
        }
    
        public override fun onPause() {
            mapView?.onPause()
            super.onPause()
        }
    
        public override fun onStop() {
            mapView?.onStop()
            super.onStop()
        }
    
        override fun onLowMemory() {
            mapView?.onLowMemory()
            super.onLowMemory()
        }
    
        override fun onDestroy() {
            mapView?.onDestroy()
            super.onDestroy()
        }
    
        override fun onSaveInstanceState(outState: Bundle) {
            super.onSaveInstanceState(outState)
            mapView?.onSaveInstanceState(outState)
        }
    }
    ```

::: zone-end

Po uruchomieniu aplikacji formant mapy jest ładowany jak na poniższej ilustracji.

![Proste usługi Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Aby wyświetlić mapę przy użyciu zestawu Azure Maps SDK dla systemu Android, należy wykonać następujące czynności:

1. Otwórz plik **Build. Gradle** najwyższego poziomu i Dodaj następujący kod do sekcji **wszystkie projekty** blokowe:

    ```gradel
    maven {
        url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Zaktualizuj **aplikację/Build. Gradle** i Dodaj do niej następujący kod:

    1. Upewnij się, że **minSdkVersion** projektu ma interfejs API w wersji 21 lub nowszej.

    2. Dodaj następujący kod do sekcji systemu Android:

        ```gradel
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```

    3. Aktualizowanie bloku zależności. Dodaj nową linię zależności implementacji dla najnowszej Azure Maps Android SDK:

        ```gradel
        implementation "com.microsoft.azure.maps:mapcontrol:0.7"
        ```

        > [!Note]
        > Numer wersji można ustawić na wartość "0 +", aby kod zawsze wskazywał najnowszą wersję.

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

::: zone pivot="programming-language-java-android"

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
    
    import androidx.appcompat.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    
    public class MainActivity extends AppCompatActivity {
    
    static {
        AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");

        //Alternatively use Azure Active Directory authenticate.
        //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
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
    }}
    ```

::: zone-end

::: zone pivot="programming-language-kotlin"

4. W pliku **MAINS. kt** należy wykonać następujące:

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

    Edytuj plik **MAINS. kt** w następujący sposób:

    ```kotlin
    package com.example.myapplication;

    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle
    import com.microsoft.azure.maps.mapcontrol.AzureMap
    import com.microsoft.azure.maps.mapcontrol.AzureMaps
    import com.microsoft.azure.maps.mapcontrol.MapControl
    import com.microsoft.azure.maps.mapcontrol.events.OnReady
    
    class MainActivity : AppCompatActivity() {
    
        companion object {
            init {
                AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
    
                //Alternatively use Azure Active Directory authenticate.
                //AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");
            }
        }
    
        var mapControl: MapControl? = null
    
        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)
    
            mapControl = findViewById(R.id.mapcontrol)
    
            mapControl?.onCreate(savedInstanceState)
    
            //Wait until the map resources are ready.
            mapControl?.onReady(OnReady { map: AzureMap -> })
        }
    
        public override fun onStart() {
            super.onStart()
            mapControl?.onStart()
        }
    
        public override fun onResume() {
            super.onResume()
            mapControl?.onResume()
        }
    
        public override fun onPause() {
            mapControl?.onPause()
            super.onPause()
        }
    
        public override fun onStop() {
            mapControl?.onStop()
            super.onStop()
        }
    
        override fun onLowMemory() {
            mapControl?.onLowMemory()
            super.onLowMemory()
        }
    
        override fun onDestroy() {
            mapControl?.onDestroy()
            super.onDestroy()
        }
    
        override fun onSaveInstanceState(outState: Bundle) {
            super.onSaveInstanceState(outState)
            mapControl?.onSaveInstanceState(outState)
        }
    }
    ```

::: zone-end

Po uruchomieniu aplikacji formant mapy zostanie załadowany jak na poniższej ilustracji.

![Prosta Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)

Zauważ, że formant Azure Maps obsługuje bardziej powiększanie i oferuje więcej informacji o widoku światowym.

> [!TIP]
> Jeśli używasz emulatora systemu Android na komputerze z systemem Windows, mapa może nie być renderowana z powodu konfliktów ze sposobem renderowania grafiki OpenGL i oprogramowania. Aby rozwiązać ten problem, w przypadku niektórych osób działały następujące czynności. Otwórz Menedżera AVD i wybierz urządzenie wirtualne do edycji. Przewiń w dół do panelu **Weryfikuj konfigurację** . W sekcji **emulowana wydajność** ustaw opcję **grafika** na **sprzęt**.

## <a name="localizing-the-map"></a>Lokalizowanie mapy

Lokalizacja jest ważna, jeśli odbiorcy są rozproszeni w wielu krajach/regionach lub mówisz w różnych językach.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Dodaj następujący kod do metody, `onCreate` Aby ustawić język mapy. Należy dodać kod przed ustawieniem widoku kontekstu mapy. Kod języka "fr" ogranicza język do języka francuskiego.

::: zone pivot="programming-language-java-android"

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);

Configuration config = new Configuration();
config.locale = locale;

getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val languageToLoad = "fr"
val locale = Locale(languageToLoad)
Locale.setDefault(locale)

val config = Configuration()
config.locale = locale

baseContext.resources.updateConfiguration(
    config,
    baseContext.resources.displayMetrics
)
```

::: zone-end

Oto przykład mapy Google z ustawionym językiem "fr".

![Lokalizacja usługi Google Maps](media/migrate-google-maps-android-app/google-maps-localization.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Azure Maps udostępnia trzy różne sposoby ustawiania języka i regionalnego widoku mapy. Pierwsza opcja polega na przekazaniem informacji o języku i widoku regionalnym do `AzureMaps` klasy. Ta opcja używa metod statycznych `setLanguage` i `setView` globalnie. Oznacza to, że język domyślny i widok regionalny są ustawiane dla wszystkich kontrolek Azure Maps załadowanych w aplikacji. Ten przykład ustawia francuski przy użyciu kodu języka "fr-FR".

::: zone pivot="programming-language-java-android"

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
companion object {
    init {
            //Set your Azure Maps Key.
        AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

Druga opcja polega na przejściu języka i wyświetleniu informacji w kodzie XML formantu mapy.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

Trzecią opcją jest Programowanie języka i widoku mapy regionalnej przy użyciu `setStyle` metody Maps. Ta opcja aktualizuje język i widok regionalny wszędzie tam, gdzie wykonywany jest kod.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
}
```

::: zone-end

Oto przykład Azure Maps z językiem ustawionym na "fr-FR".

![Lokalizacja Azure Maps](media/migrate-google-maps-android-app/azure-maps-localization.png)

Zapoznaj się z pełną listą [obsługiwanych języków](supported-languages.md).

## <a name="setting-the-map-view"></a>Ustawianie widoku mapy

Mapy dynamiczne w obu Azure Maps i Google Maps można programistycznie przenieść do nowych lokalizacji geograficznych, wywołując odpowiednie metody. Przyjrzyjmy się mapie do obrazów satelitarnych satelitów, Wyśrodkuj mapę w lokalizacji ze współrzędnymi i zmień poziom powiększenia. Na potrzeby tego przykładu będziemy używać szerokości geograficznej: 35,0272, długości geograficznej:-111,0225 i poziomu powiększenia 15.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Aparat kontrolki mapy usługi Google Maps można programistycznie przenieść przy użyciu `moveCamera` metody. `moveCamera`Metoda pozwala określić środek mapy i poziom powiększenia. `setMapType`Metoda zmienia typ mapy do wyświetlenia.

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(LatLng(35.0272, -111.0225), 15))
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE)
}
```

::: zone-end

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

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-111.0225, 35.0272)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-111.0225, 35.0272)), zoom(14))

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE))
}
```

::: zone-end

![Widok zestawu Azure Maps](media/migrate-google-maps-android-app/azure-maps-set-view.png)

**Dodatkowe zasoby:**

* [Obsługiwane style mapy](supported-map-styles.md)

## <a name="adding-a-marker"></a>Dodawanie znacznika

Dane punktów są często renderowane przy użyciu obrazu na mapie. Te obrazy są określane jako znaczniki, pinezki, numery PIN lub symbole. Poniższe przykłady renderują dane punktu jako znaczniki na mapie na szerokości geograficznej: 51,5, Długość geograficzna: – 0,2.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Dzięki usłudze Google Maps znaczniki są dodawane za pomocą `addMarker` metody Maps.

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    mapView.addMarker(MarkerOptions().position(LatLng(47.64, -122.33)))
}
```

::: zone-end

![Znacznik Google Maps](media/migrate-google-maps-android-app/google-maps-marker.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

W Azure Maps Renderuj dane punktu na mapie, najpierw dodając dane do źródła danych. Następnie połączenie tego źródła danych z warstwą symboli. Źródło danych optymalizuje zarządzanie danymi przestrzennymi w formancie mapy. Warstwa symboli określa sposób renderowania danych punktu przy użyciu jako obrazu lub tekstu.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(source));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = new DataSource()
    map.sources.add(source)

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)))

    //Create a symbol layer and add it to the map.
    map.layers.add(SymbolLayer(source))
}
```

::: zone-end

![Znacznik Azure Maps](media/migrate-google-maps-android-app/azure-maps-marker.png)

## <a name="adding-a-custom-marker"></a>Dodawanie znacznika niestandardowego

Obrazy niestandardowe mogą służyć do reprezentowania punktów na mapie. Mapa w poniższych przykładach używa obrazu niestandardowego do wyświetlania punktu na mapie. Punkt ma wartość Latitude: 51,5 i Długość geograficzna: – 0,2. Kotwica przesunięcia położenia znacznika, tak aby punkt ikony pinezki wyrównany do poprawnego położenia na mapie.

![żółty obraz pinezki](media/migrate-google-maps-web-app/yellow-pushpin.png)<br/>
yellow-pushpin.png

W obu przykładach Powyższy obraz jest dodawany do folderu do rysowania zasobów aplikacji.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Za pomocą usługi Google Maps można używać obrazów niestandardowych dla znaczników. Załaduj obrazy niestandardowe przy użyciu `icon` opcji znacznika. Aby wyrównać punkt obrazu do współrzędnej, użyj `anchor` opcji. Zakotwiczenie jest względem wymiarów obrazu. W tym przypadku zakotwiczeniem jest 0,2 jednostek szerokości i 1 jednostka wysoka.

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f));
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap;

    mapView.addMarker(MarkerOptions().position(LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.yellow-pushpin))
    .anchor(0.2f, 1f))
}
```

::: zone-end

![Znacznik niestandardowy usługi Google Maps](media/migrate-google-maps-android-app/google-maps-custom-marker.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Warstwy symboli w Azure Maps obsługują obrazy niestandardowe, ale najpierw muszą zostać załadowane do zasobów mapy i przypisany unikatowy identyfikator. Następnie warstwa symboli musi odwoływać się do tego identyfikatora. Przesuń symbol, aby wyrównać go do poprawnego punktu w obrazie przy użyciu `iconOffset` opcji. Przesunięcie ikony jest w pikselach. Domyślnie przesunięcie jest względem środkowego środka obrazu, ale tę wartość przesunięcia można dostosować przy użyciu `iconAnchor` opcji. Ten przykład ustawia `iconAnchor` opcję na `"center"` . Za pomocą przesunięcia ikony można przenieść obraz o pięć pikseli do prawej i 15 pikseli, aby wyrównać punkt obrazu pinezki.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(source,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER),
        iconOffset(new Float[]{5f, -15f})));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = DataSource()
    map.sources.add(source)

    //Create a point feature and add it to the data source.
    source.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)))

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.yellow_pushpin)

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(SymbolLayer(source,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER),
        iconOffset(arrayOf(0f, -1.5f))))
}
```

::: zone-end

![Azure Maps znacznika niestandardowego](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)

## <a name="adding-a-polyline"></a>Dodawanie linii łamanej

Linie łamane są używane do reprezentowania linii lub ścieżki na mapie. W poniższych przykładach pokazano, jak utworzyć kreskowaną linię łamaną na mapie.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Dzięki usłudze Google Maps Renderuj linię łamaną przy użyciu `PolylineOptions` klasy. Dodaj linię łamaną do mapy za pomocą `addPolyline` metody. Ustaw kolor pociągnięcia przy użyciu `color` opcji. Ustaw szerokość obrysu przy użyciu `width` opcji. Dodaj tablicę kreskowaną pędzla przy użyciu `pattern` opcji.

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    //Create the options for the polyline.
    val lineOptions = new PolylineOptions()
        .add(new LatLng(46, -123))
        .add(new LatLng(49, -122))
        .add(new LatLng(46, -121))
        .color(Color.RED)
        .width(10f)
        .pattern(Arrays.<PatternItem>asList(
                new Dash(30f), new Gap(30f)))

    //Add the polyline to the map.
    val polyline = mapView.addPolyline(lineOptions)
}
```

::: zone-end

![Google Maps — łamana](media/migrate-google-maps-android-app/google-maps-polyline.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

W Azure Maps linie łamane są nazywane `LineString` lub `MultiLineString` obiektami. Dodaj te obiekty do źródła danych i Renderuj je przy użyciu warstwy liniowej. Ustaw szerokość obrysu przy użyciu `strokeWidth` opcji. Dodaj tablicę kreskowaną pędzla przy użyciu `strokeDashArray` opcji.

Szerokość obrysu i tablica kreskowana "pikseli" w Azure Maps Web SDK są takie same, jak w usłudze Google Maps. Oba te same wartości są akceptowane w celu uzyskania tych samych wyników.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    source.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(source,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = DataSource()
    map.sources.add(source)

    //Create an array of points.
    val points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46))

    //Create a LineString feature and add it to the data source.
    source.add(Feature.fromGeometry(LineString.fromLngLats(points)))

    //Create a line layer and add it to the map.
    map.layers.add(LineLayer(source,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})))
}
```

::: zone-end

![Azure Maps łamana](media/migrate-google-maps-android-app/azure-maps-polyline.png)

## <a name="adding-a-polygon"></a>Dodawanie wielokątu

Wielokąty są używane do reprezentowania obszaru na mapie. W następnych przykładach pokazano, jak utworzyć wielokąt. Ten Wielokąt tworzy Trójkąt w oparciu o współrzędne środkowe mapy.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Za pomocą usługi Google Maps Renderuj Wielokąt przy użyciu `PolygonOptions` klasy. Dodaj wielokąt do mapy za pomocą `addPolygon` metody. Ustaw kolor wypełnienia i pociągnięcia odpowiednio przy `fillColor` użyciu `strokeColor` opcji i. Ustaw szerokość obrysu przy użyciu `strokeWidth` opcji.

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    val polygonOptions = PolygonOptions()
        .add(new LatLng(46, -123))
        .add(new LatLng(49, -122))
        .add(new LatLng(46, -121))
        .add(new LatLng(46, -123))  //Close the polygon.
        .fillColor(Color.argb(128, 0, 128, 0))
        .strokeColor(Color.RED)
        .strokeWidth(5f)

    //valAdd the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions)
}
```

::: zone-end

![Wielokąt usługi Google Maps](media/migrate-google-maps-android-app/google-maps-polygon.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

W Azure Maps, Dodaj `Polygon` `MultiPolygon` obiekty i do źródła danych i Renderuj je na mapie przy użyciu warstw. Renderowanie obszaru wielokąta w warstwie wielokąta. Renderowanie konspektu wielokąta przy użyciu warstwy liniowej. Ustaw kolor obrysu i szerokość przy użyciu `strokeColor` `strokeWidth` opcji i.

Jednostki z szerokością i tablicą łącznika "piksele" w Azure Maps zestawie Web SDK są wyrównane z odpowiednimi jednostkami w usłudze mapy Google. Obie akceptują te same wartości i generują te same wyniki.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource source = new DataSource();
    map.sources.add(source);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    source.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(source,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(source,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Create a data source and add it to the map.
    val source = DataSource()
    map.sources.add(source)

    //Create an array of point arrays to create polygon rings.
    val rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)))

    //Create a Polygon feature and add it to the data source.
    source.add(Feature.fromGeometry(Polygon.fromLngLats(rings)))

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(PolygonLayer(source,
        fillColor("green"),
        fillOpacity(0.5f)))

    //Add a line layer for rendering the polygon outline.
    map.layers.add(LineLayer(source,
        strokeColor("red"),
        strokeWidth(2f)))
}
```

::: zone-end

![Azure Maps Wielokąt](media/migrate-google-maps-android-app/azure-maps-polygon.png)

## <a name="overlay-a-tile-layer"></a>Nałóż warstwę kafelków

 Użyj warstw kafelków, aby nałożyć na siebie obrazy warstwowe, które zostały podzielone na mniejsze obrazy z podziałem na fragmenty. To podejście jest typowym sposobem nakładania obrazów warstwowych lub dużych zestawów danych. Warstwy kafelków są znane jako nakładki obrazów w usłudze mapy Google.

Poniższe przykłady nakładają warstwę kafelków radarowych z Iowa środowiska Mesonet of Iowa State University. Rozmiary kafelków są 256 pikseli.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Dzięki usłudze Google Maps warstwa kafelków może zostać przełożone na wierzchu mapy. Użyj `TileOverlayOptions` klasy. Dodaj warstwę kafelków do mapy za pomocą `addTileLayer` metody. Aby sprawić, że kafelki są częściowo przezroczyste, `transparency` opcja jest ustawiona na 0,2 lub 20% przezroczyste.

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap
    //Create the options for the tile layer.
    val tileLayer: TileOverlayOptions = TileOverlayOptions()
        .tileProvider(object : UrlTileProvider(256, 256) {
            fun getTileUrl(x: Int, y: Int, zoom: Int): URL? {
                return try { //Define the URL pattern for the tile images.
                    URL(
                        String.format(
                            "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png",
                            zoom,
                            x,
                            y
                        )
                    )
                } catch (e: MalformedURLException) {
                    throw AssertionError(e)
                }
            }
        }).transparency(0.2f)
    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer)
}
```

::: zone-end

![Warstwa kafelków usługi Google Maps](media/migrate-google-maps-android-app/google-maps-tile-layer.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Warstwę kafelków można dodać do mapy w podobny sposób, jak każda inna warstwa. Sformatowany adres URL, który ma symbole zastępcze x, y i zoom; `{x}`, `{y}` `{z}` odpowiednio jest używany do określenia warstwy, w której mają być dostępne kafelki. Ponadto warstwy kafelków w Azure Maps obsługują `{quadkey}` , `{bbox-epsg-3857}` i `{subdomain}` symboli zastępczych. Aby warstwa kafelków została częściowo przezroczysta, używana jest wartość nieprzezroczystości 0,8. Nieprzezroczystość i przezroczystość, chociaż podobne, używaj odwróconych wartości. Aby przeprowadzić konwersję między obiema opcjami, Odejmij ich wartość od liczby.

> [!TIP]
> W Azure Maps jest wygodne renderowanie warstw poniżej innych warstw, w tym warstw mapy podstawowej. Ponadto często pożądane jest renderowanie warstw kafelków poniżej etykiet mapy, dzięki czemu można je łatwo odczytać. `map.layers.add`Metoda przyjmuje drugi parametr, który jest identyfikatorem warstwy, w której ma zostać wstawiona Nowa warstwa poniżej. Aby wstawić warstwę kafelków pod etykietami mapy, można użyć następującego kodu: `map.layers.add(myTileLayer, "labels");`

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Add a tile layer to the map, below the map labels.
    map.layers.add(TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels")
}
```

::: zone-end

![Warstwa kafelków Azure Maps](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)

## <a name="show-traffic"></a>Wyświetlanie ruchu

Zarówno Azure Maps, jak i usługi Google Maps zawierają opcje umożliwiające nakładanie danych ruchu.

### <a name="before-google-maps"></a>Wcześniej: Google Maps

Dzięki usłudze Google Maps dane przepływu ruchu mogą zostać zastąpione na podstawie mapy, przekazując do metody mapy wartość true `setTrafficEnabled` .

::: zone pivot="programming-language-java-android"

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
public override fun onMapReady(googleMap: GoogleMap) {
    mapView = googleMap

    mapView.setTrafficEnabled(true)
}
```

::: zone-end

![Ruch usługi Google Maps](media/migrate-google-maps-android-app/google-maps-traffic.png)

### <a name="after-azure-maps"></a>Po: Azure Maps

Azure Maps oferuje kilka różnych opcji wyświetlania ruchu. Zdarzenia dotyczące ruchu, takie jak zamknięcie dróg i wypadki, mogą być wyświetlane jako ikony na mapie. Przepływ ruchu i kodowane kolorami drogi można przemieścić na mapie. Kolory można zmodyfikować tak, aby były wyświetlane względem wartości granicznej Limit szybkości względem normalnego oczekiwanego opóźnienia lub bezwzględnego opóźnienia. Dane zdarzenia w Azure Maps są aktualizowane co minutę, a dane przepływu są aktualizowane co dwie minuty.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE))
}
```

::: zone-end

![Ruch Azure Maps](media/migrate-google-maps-android-app/azure-maps-traffic.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Brak zasobów do wyczyszczenia.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat migrowania Azure Maps:

> [!div class="nextstepaction"]
> [Migracja aplikacji dla systemu Android](migrate-from-google-maps-android-app.md)
