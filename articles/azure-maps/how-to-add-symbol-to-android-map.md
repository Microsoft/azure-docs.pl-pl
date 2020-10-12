---
title: Dodawanie warstwy symboli do map systemu Android | Mapy Microsoft Azure
description: Dowiedz się, jak dodać znacznik do mapy. Zobacz przykład, który używa Android SDK Azure Maps, aby dodać warstwę symboli, która zawiera dane oparte na punktach ze źródła danych.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 10969e20cd7ae71cade230f6643a27d5d940ceaa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91311278"
---
# <a name="add-a-symbol-layer-to-a-map-using-azure-maps-android-sdk"></a>Dodawanie warstwy symboli do mapy przy użyciu Azure Maps Android SDK

W tym artykule pokazano, jak renderować dane punktu ze źródła danych jako warstwę symboli na mapie przy użyciu Android SDK Azure Maps.

## <a name="prerequisites"></a>Wymagania wstępne

Aby całkowicie wykonać kroki opisane w tym artykule, należy zainstalować [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) w celu załadowania mapy.

## <a name="add-a-symbol-layer"></a>Dodawanie warstwy symboli

Aby dodać znacznik na mapie za pomocą warstwy symboli, wykonaj następujące czynności:

1. Edytuj **res**  >  **layout**  >  **activity_main.xml** układu res, aby wyglądał wyglądać jak w poniższym kodzie XML:
    
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
            app:mapcontrol_centerLat="47.64"
            app:mapcontrol_centerLng="-122.33"
            app:mapcontrol_zoom="12"
            />

    </FrameLayout>
    ```

2. Skopiuj poniższy fragment kodu do metody **OnCreate ()** `MainActivity.java` klasy.

    ```Java
    mapControl.onReady(map -> {
    
        //Create a data source and add it to the map.
        DataSource dataSource = new DataSource();
        map.sources.add(dataSource);
    
        //Create a point feature and add it to the data source.
        dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
    
        //Add a custom image icon to the map resources.
        map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
    
        //Create a symbol layer and add it to the map.
        map.layers.add(new SymbolLayer(dataSource,
            iconImage("my-icon")));
        });
    
    ```
    
    Poniższy fragment kodu uzyskuje Azure Maps wystąpienia kontrolki mapy za pomocą metody wywołania zwrotnego **()** . Następnie tworzy obiekt źródła danych przy użyciu klasy **DataSource** i dodaje go do mapy. Następnie dodaje **funkcję** zawierającą geometrię punktu do niej. Obraz czerwonego znacznika jest następnie ustawiany jako ikona symbolu. **Warstwa symboli** używa tekstu lub ikon do renderowania danych opartych na punktach opakowanych w źródle danych jako symbol na mapie. Następnie zostanie utworzona warstwa symboli, a źródło danych zostanie przesłane do niego do renderowania, a następnie dodane do warstw mapy.
    
    Po dodaniu fragmentu kodu powyżej `MainActivity.java` powinien wyglądać tak jak poniżej:
    
    ```Java
    package com.example.myapplication;
    
    import android.app.Activity;
    import android.os.Bundle;
    import com.mapbox.geojson.Feature;
    import com.mapbox.geojson.Point;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;
    import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
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
    
                //Create a data source and add it to the map.
                DataSource dataSource = new DataSource();
                map.sources.add(dataSource);
            
                //Create a point feature and add it to the data source.
                dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));
            
                //Add a custom image icon to the map resources.
                map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            
                //Create a symbol layer and add it to the map.
                map.layers.add(new SymbolLayer(dataSource,
                    iconImage("my-icon")));
            });
        }
    
        @Override
        public void onStart() {
            super.onStart();
            mapControl.onStart();
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
    
W tym momencie, jeśli uruchomisz aplikację, na mapie powinien zostać wyświetlony znacznik, jak pokazano poniżej:

<center>

![Kod PIN mapy systemu Android](./media/how-to-add-symbol-to-android-map/android-map-pin.png)</center>

> [!TIP]
> Domyślnie warstwy symboli optymalizują renderowanie symboli, ukrywając symbole, które nakładają się na siebie. W miarę powiększania, ukryte symbole stają się widoczne. Aby wyłączyć tę funkcję i renderować wszystkie symbole przez cały czas, ustaw `iconAllowOverlap` opcję na `true` .

## <a name="next-steps"></a>Następne kroki

Aby dodać więcej rzeczy do mapy, zobacz:

> [!div class="nextstepaction"]
> [Dodawanie kształtów do mapy systemu Android](https://docs.microsoft.com/azure/azure-maps/how-to-add-shapes-to-android-map)

> [!div class="nextstepaction"]
> [Wyświetlanie informacji o funkcjach](display-feature-information-android.md)