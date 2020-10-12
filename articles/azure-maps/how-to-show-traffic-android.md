---
title: Wyświetlanie danych o ruchu na mapie systemu Android | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak wyświetlać dane o ruchu na mapie przy użyciu Android SDK Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 93cabb566db02de7ef991fe9cdd293f8c399c3a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272960"
---
# <a name="show-traffic-data-on-the-map-using-azure-maps-android-sdk"></a>Wyświetlanie danych o ruchu na mapie przy użyciu Azure Maps Android SDK

Dane przepływu i dane zdarzeń to dwa typy danych ruchu, które mogą być wyświetlane na mapie. W tym przewodniku pokazano, jak wyświetlić oba typy danych ruchu. Dane zdarzeń obejmują dane dotyczące punktów i danych opartych na wierszach dla elementów, takich jak konstrukcje, zamknięcia dróg i awarie. Dane przepływu przedstawiają metryki dotyczące przepływu ruchu w podróży.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było wyświetlić ruch na mapie, należy [utworzyć konto platformy Azure](quick-demo-map-app.md#create-an-azure-maps-account)i [uzyskać klucz subskrypcji](quick-demo-map-app.md#get-the-primary-key-for-your-account). Następnie należy zainstalować [Android SDK Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) i załadować mapę.

## <a name="incidents-traffic-data"></a>Zdarzenia danych ruchu

Należy zaimportować następujące biblioteki do wywołania `setTraffic` i `incidents` :

```java
import static com.microsoft.com.azure.maps.mapcontrol.options.TrafficOptions.incidents;
```

 Poniższy fragment kodu przedstawia sposób wyświetlania danych o ruchu na mapie. Przekazujemy wartość logiczną do `incidents` metody i przekazujemy ją do `setTraffic` metody. 

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map - > {
        map.setTraffic(incidents(true));
    }
}
```

## <a name="flow-traffic-data"></a>Dane ruchu przepływu

Najpierw należy zaimportować następujące biblioteki w celu wywołania `setTraffic` i `flow` :

```java
import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
```

Użyj poniższego fragmentu kodu, aby ustawić dane przepływu ruchu. Podobnie jak w przypadku kodu w poprzedniej sekcji, przekazujemy wartość zwracaną `flow` metody do `setTraffic` metody. Istnieją cztery wartości, które mogą być przenoszone do `flow` , a każda wartość będzie wyzwalać `flow` odpowiednią wartość. Zwracana wartość `flow` zostanie następnie przeniesiona jako argument do `setTraffic` . Zapoznaj się z poniższą tabelą dla tych czterech wartości:

|Wartość przepływu | Opis|
| :-- | :-- |
| TrafficFlow. NONE | Nie wyświetla danych o ruchu na mapie |
| TrafficFlow. RELATYWN | Pokazuje dane o ruchu odnoszące się do szybkości swobodnego przepływu drogi |
| TrafficFlow.RELATIVE_DELAY | Wyświetla obszary, które są wolniejsze niż średnie oczekiwane opóźnienie |
| TrafficFlow. ABSOLUTN | Pokazuje absolutną prędkość wszystkich pojazdów w podróży |

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mapControl.getMapAsync(map -> {
        map.setTraffic(flow(TrafficFlow.RELATIVE)));
    }
}
```

## <a name="show-incident-traffic-data-by-clicking-a-feature"></a>Pokaż dane o ruchu zdarzeń, klikając funkcję

Aby uzyskać zdarzenia dotyczące konkretnej funkcji, można użyć poniższego kodu. Po kliknięciu funkcji logika kodu sprawdza zdarzenia i kompiluje komunikat o zdarzeniu. W dolnej części ekranu zostanie wyświetlony komunikat z informacjami.

1. Najpierw należy edytować **> > układu zasobów activity_main.xml**, tak aby wyglądał wyglądać jak poniżej. Licencjobiorca może zastąpić `mapcontrol_centerLat` odpowiednie `mapcontrol_centerLng` `mapcontrol_zoom` wartości. Odwołaj, poziom powiększenia jest wartością z zakresu od 0 do 22. Na poziomie powiększenia 0 cały świat mieści się na jednym kafelku.

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
           app:mapcontrol_centerLat="47.6050"
           app:mapcontrol_centerLng="-122.3344"
           app:mapcontrol_zoom="12"
           />

   </FrameLayout>
   ```

2. Dodaj następujący kod do pliku **Main. Java** . Pakiet jest domyślnie dołączany, więc upewnij się, że pakiet znajduje się u góry.

   ```java
   package <yourpackagename>;
   import androidx.appcompat.app.AppCompatActivity;

   import android.os.Bundle;
   import android.widget.Toast;

   import com.microsoft.azure.maps.mapcontrol.AzureMaps;
   import com.microsoft.azure.maps.mapcontrol.MapControl;
   import com.mapbox.geojson.Feature;
   import com.microsoft.azure.maps.mapcontrol.events.OnFeatureClick;

   import com.microsoft.azure.maps.mapcontrol.options.TrafficFlow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.flow;
   import static com.microsoft.azure.maps.mapcontrol.options.TrafficOptions.incidents;

   public class MainActivity extends AppCompatActivity {

       static {
           AzureMaps.setSubscriptionKey("Your Azure Maps Subscription Key");
       }

       MapControl mapControl;

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);

           mapControl = findViewById(R.id.mapcontrol);

           mapControl.onCreate(savedInstanceState);

           //Wait until the map resources are ready.
           mapControl.getMapAsync(map -> {

               map.setTraffic(flow(TrafficFlow.RELATIVE));
               map.setTraffic(incidents(true));

               map.events.add((OnFeatureClick) (features) -> {

                   if (features != null && features.size() > 0) {
                       Feature incident = features.get(0);
                       if (incident.properties() != null) {


                           StringBuilder sb = new StringBuilder();
                           String incidentType = incident.getStringProperty("incidentType");
                           if (incidentType != null) {
                               sb.append(incidentType);
                           }
                           if (sb.length() > 0) sb.append("\n");
                           if ("Road Closed".equals(incidentType)) {
                               sb.append(incident.getStringProperty("from"));
                           } else {
                               String description = incident.getStringProperty("description");
                               if (description != null) {
                                   for (String word : description.split(" ")) {
                                       if (word.length() > 0) {
                                           sb.append(word.substring(0, 1).toUpperCase());
                                           if (word.length() > 1) {
                                               sb.append(word.substring(1));
                                           }
                                           sb.append(" ");
                                       }
                                   }
                               }
                           }
                           String message = sb.toString();

                           if (message.length() > 0) {
                               Toast.makeText(this,message,Toast.LENGTH_LONG).show();
                           }
                       }
                   }
               });
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

3. Po dodaniu powyższego kodu do aplikacji będzie można kliknąć funkcję i zobaczyć szczegóły zdarzeń dotyczących ruchu sieciowego. W zależności od szerokości geograficznej, długości geograficznej oraz wartości poziomu powiększenia, które były używane w pliku **activity_main.xml** , wyniki wyglądają podobnie jak na poniższym obrazie:

   <center>

   ![Zdarzenia — ruch przychodzący na mapie](./media/how-to-show-traffic-android/android-traffic.png)

   </center>

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak dodać więcej danych do mapy, zobacz następujące przewodniki:

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy kafelków](how-to-add-tile-layer-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie kształtów do mapy systemu Android](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Wyświetlanie informacji o funkcjach](display-feature-information-android.md)
