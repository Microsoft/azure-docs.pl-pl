---
title: Dodawanie warstwy kafelków do map systemu Android | Mapy Microsoft Azure
description: Dowiedz się, jak dodać warstwę kafelków do mapy. Zobacz przykład, który używa Android SDK Azure Maps, aby dodać nakładkę radaru do mapy.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 905085d5b0787697d6094bd1337420ee8ae61d90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91311093"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Dodawanie warstwy kafelków do mapy przy użyciu Azure Maps Android SDK

W tym artykule przedstawiono sposób renderowania warstwy kafelków na mapie przy użyciu Android SDK Azure Maps. Warstwy kafelków umożliwiają nakładanie obrazów na kafelkach mapy podstawowej Azure Maps. Więcej informacji o Azure Maps systemie rozmieszczania można znaleźć w dokumentacji [poziomów powiększenia i siatki kafelków](zoom-levels-and-tile-grid.md) .

Warstwa kafelków jest ładowana na kafelkach z serwera. Te obrazy mogą być wstępnie renderowane i przechowywane jak każdy inny obraz na serwerze, przy użyciu konwencji nazewnictwa, która jest rozpoznawana przez warstwę kafelków. Można też renderować te obrazy za pomocą usługi dynamicznej, która generuje obrazy niemal w czasie rzeczywistym. Istnieją trzy różne konwencje nazewnictwa usługi kafelków obsługiwane przez Azure Maps TileLayer klasy:

* X, Y, z notacją powiększenia w oparciu o poziom powiększenia, x to kolumna, a Y to pozycja w wierszu kafelka w siatce kafelków.
* Quadkey-kombinacja x, y, Powiększ informacje w postaci pojedynczej wartości ciągu, która jest unikatowym identyfikatorem dla kafelka.
* Współrzędne pola ograniczenia obwiedni mogą służyć do określania obrazu w formacie, `{west},{south},{east},{north}` który jest często używany przez [usługi mapowania sieci Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> TileLayer to doskonały sposób wizualizacji dużych zestawów danych na mapie. Nie tylko można wygenerować warstwy kafelków z obrazu, ale dane wektorowe mogą być również renderowane jako warstwa kafelków. Przez renderowanie danych wektorowych jako warstwy kafelków, formant mapy musi ładować tylko kafelki, które mogą być znacznie mniejsze w rozmiarze pliku niż dane wektorowe, które reprezentują. Ta technika jest używana przez wiele osób, które muszą renderować miliony wierszy danych na mapie.

Adres URL kafelka przesłany do warstwy kafelków musi być adresem URL protokołu HTTP/HTTPS do zasobu TileJSON lub szablonem adresu URL kafelka, który używa następujących parametrów: 

* `{x}` -X pozycja kafelka. Również wymagają `{y}` i `{z}` .
* `{y}` -Y pozycja kafelka. Również wymagają `{x}` i `{z}` .
* `{z}` — Poziom powiększenia kafelka. Również wymagają `{x}` i `{y}` .
* `{quadkey}` -Kafelek quadkey identyfikator oparty na konwencji nazewnictwa systemu kafelków mapy Bing.
* `{bbox-epsg-3857}` -Ciąg pola granicznego z formatem `{west},{south},{east},{north}` w systemie referencyjnym przestrzennym EPSG 3857.
* `{subdomain}` — Symbol zastępczy wartości poddomeny, jeśli określono wartość poddomeny.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć proces w tym artykule, należy zainstalować [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) w celu załadowania mapy.


## <a name="add-a-tile-layer-to-the-map"></a>Dodawanie warstwy kafelków do mapy

 Ten przykład pokazuje, jak utworzyć warstwę kafelków, która wskazuje zestaw kafelków. Te kafelki używają systemu dzielenia "x, y, zoom". Źródłem tej warstwy kafelków jest nałożenie radaru pogody z [Iowa środowiska Mesonet Iowa University](https://mesonet.agron.iastate.edu/ogc/). 

Do mapy można dodać warstwę kafelków, wykonując poniższe kroki.

1. Edytuj **układ > zasobów > activity_main.xml** tak, aby wyglądał następująco:

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
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. Skopiuj poniższy fragment kodu poniżej do metody **OnCreate ()** `MainActivity.java` klasy.

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    Poniższy fragment kodu uzyskuje Azure Maps wystąpienia kontrolki mapy za pomocą metody wywołania zwrotnego **()** . Następnie tworzy `TileLayer` obiekt i przekazuje sformatowany adres URL kafelka **XYZ** do `tileUrl` opcji. Nieprzezroczystość warstwy jest ustawiona na `0.8` , a ponieważ kafelki z używanej usługi kafelków są 256 pikseli, te informacje są przesyłane do `tileSize` opcji. Warstwa kafelków jest następnie przenoszona do Menedżera warstwy map.

    Po dodaniu fragmentu kodu powyżej `MainActivity.java` powinien wyglądać tak jak poniżej:
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
    public class MainActivity extends AppCompatActivity {
    
        static{
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
    
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
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

Jeśli aplikacja zostanie uruchomiona teraz, na mapie powinna zostać wyświetlona następująca linia:

<center>

![Linia mapy systemu Android](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center>

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z poniższym artykułem, aby dowiedzieć się więcej o sposobach ustawiania stylów mapy

> [!div class="nextstepaction"]
> [Zmień style mapy w usłudze mapy systemu Android](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)