---
title: Wyświetlanie danych o ruchu w usłudze mapy systemu Android | Mapy Microsoft Azure
description: W tym artykule dowiesz się, jak wyświetlać dane o ruchu na mapie przy użyciu Android SDK Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 36b3666f12b48468467e76f4c281d58d8018478c
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098540"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>Pokaż dane ruchu na mapie (Android SDK)

Dane przepływu i dane zdarzeń to dwa typy danych ruchu, które mogą być wyświetlane na mapie. W tym przewodniku pokazano, jak wyświetlić oba typy danych ruchu. Dane zdarzeń obejmują dane dotyczące punktów i danych opartych na wierszach dla elementów, takich jak konstrukcje, zamknięcia dróg i awarie. Dane przepływu przedstawiają metryki dotyczące przepływu ruchu w podróży.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że wykonano kroki opisane w dokumencie [Szybki Start: Tworzenie aplikacji dla systemu Android](quick-android-map.md) . Bloki kodu w tym artykule można wstawiać do `onReady` programu obsługi zdarzeń Maps.

## <a name="show-traffic-on-the-map"></a>Pokaż ruch na mapie

Istnieją dwa typy danych ruchu dostępne w Azure Maps:

- Dane zdarzenia — składa się z punktów i danych opartych na wierszu dla elementów, takich jak konstrukcja, zamknięcie dróg i awarie.
- Dane przepływu — dostarcza metryki przepływu ruchu na drogach. Często dane przepływu ruchu są używane do kolorowania dróg. Kolory są zależne od tego, jaki ruch zmniejsza przepływ w stosunku do limitu szybkości lub innej metryki. Istnieją cztery wartości, które można przekazywać do opcji ruchu na `flow` mapie.

    |Wartość przepływu | Opis|
    | :-- | :-- |
    | TrafficFlow. NONE | Nie wyświetla danych o ruchu na mapie |
    | TrafficFlow. RELATYWN | Pokazuje dane o ruchu odnoszące się do szybkości swobodnego przepływu drogi |
    | TrafficFlow.RELATIVE_DELAY | Wyświetla obszary, które są wolniejsze niż średnie oczekiwane opóźnienie |
    | TrafficFlow. ABSOLUTN | Pokazuje absolutną prędkość wszystkich pojazdów w podróży |

Poniższy kod przedstawia sposób wyświetlania danych o ruchu na mapie.

::: zone pivot="programming-language-java-android"

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)
```

::: zone-end

Poniższy zrzut ekranu przedstawia powyższy kod rending informacje o ruchu w czasie rzeczywistym na mapie.

![Mapa pokazująca informacje o ruchu w czasie rzeczywistym](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>Pobierz szczegóły zdarzenia dotyczącego ruchu

Szczegółowe informacje o zdarzeniu związanym z ruchem są dostępne we właściwościach funkcji używanej do wyświetlania zdarzenia na mapie. Zdarzenia dotyczące ruchu są dodawane do mapy przy użyciu usługi kafelka wektora zdarzenia Azure Maps. Format danych w tych kafelkach wektorowych, jeśli [opisano tutaj](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles). Poniższy kod dodaje zdarzenie kliknięcia do mapy i pobiera funkcję incydentu o wykorzystaniu ruchu, która została kliknięta i wyświetla komunikat wyskakujący z niektórymi szczegółami.

::: zone pivot="programming-language-java-android"

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)

//Add a click event to the map.
map.events.add(OnFeatureClick { features: List<Feature>? ->
    if (features != null && features.size > 0) {
        val incident = features[0]

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {
            val sb = StringBuilder()
            val incidentType = incident.getStringProperty("incidentType")

            if (incidentType != null) {
                sb.append(incidentType)
            }

            if (sb.length > 0) {
                sb.append("\n")
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed" == incidentType) {
                val from = incident.getStringProperty("from")
                if (from != null) {
                    sb.append(from)
                }
            } else { //Get the description of the traffic incident.
                val description = incident.getStringProperty("description")
                if (description != null) {
                    sb.append(description)
                }
            }

            val message = sb.toString()
            if (message.length > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show()
            }
        }
    }
})
```

::: zone-end

Poniższy zrzut ekranu przedstawia powyższy kod rending informacje o ruchu w czasie rzeczywistym na mapie z wyskakującym komunikatem wyświetlającym szczegóły zdarzenia.

![Mapa pokazująca informacje o ruchu w czasie rzeczywistym z wyskakującym komunikatem wyświetlającym szczegóły zdarzenia](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak dodać więcej danych do mapy, zobacz następujące przewodniki:

> [!div class="nextstepaction"]
> [Dodawanie warstwy kafelków](how-to-add-tile-layer-android-map.md)
