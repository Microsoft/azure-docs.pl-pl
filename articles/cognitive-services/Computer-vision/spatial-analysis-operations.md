---
title: Operacje analizy przestrzennej
titleSuffix: Azure Cognitive Services
description: Operacje analityczne przestrzenne.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 80f0d29de6b3013ad02ed1a5d34bebdf81a8766b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628217"
---
# <a name="spatial-analysis-operations"></a>Operacje analizy przestrzennej

Analiza przestrzenna umożliwia analizę wideo przesyłania strumieniowego w czasie rzeczywistym z urządzeń kamer. Dla każdego skonfigurowanego urządzenia z kamerą operacje związane z analizą przestrzenną spowodują wygenerowanie strumienia wyjściowego komunikatów JSON wysyłanych do wystąpienia usługi Azure IoT Hub. 

Kontener analizy przestrzennej implementuje następujące operacje:

| Identyfikator operacji| Opis|
|---------|---------|
| cognitiveservices. Vision. spatialanalysis-personcount | Zlicza osoby znajdujące się w określonej strefie w polu widoku aparatu. <br> Emituje początkowe zdarzenie _personCountEvent_ , a następnie _personCountEvent_ zdarzenia w przypadku zmiany liczby.  |
| cognitiveservices. Vision. spatialanalysis-personcrossingline | Śledzi, gdy osoba przecina wydzielony wiersz w polu widoku aparatu. <br>Emituje zdarzenie _personLineEvent_ , gdy osoba przecina wiersz i udostępnia informacje kierunkowe. 
| cognitiveservices. Vision. spatialanalysis-personcrossingpolygon | Śledzi, gdy osoba przecina wydzielony wiersz w polu widoku aparatu. <br> Emituje zdarzenie _personLineEvent_ , gdy osoba przecina strefę i udostępnia informacje kierunkowe. |
| cognitiveservices. Vision. spatialanalysis-persondistance | Śledzi, gdy ludzie naruszają regułę odległości. <br> Emituje _personDistanceEvent_ okresowo przy użyciu lokalizacji każdego naruszenia odległości. |

Wszystkie powyższe operacje są również dostępne w `.debug` wersji, która ma możliwość wizualizacji ramek wideo w miarę ich przetwarzania. `xhost +`Aby umożliwić wizualizację ramek wideo i zdarzeń, należy uruchomić program na komputerze hosta.

| Identyfikator operacji| Opis|
|---------|---------|
| cognitiveservices. Vision. spatialanalysis-personcount. Debug | Zlicza osoby znajdujące się w określonej strefie w polu widoku aparatu. <br> Emituje początkowe zdarzenie _personCountEvent_ , a następnie _personCountEvent_ zdarzenia w przypadku zmiany liczby.  |
| cognitiveservices. Vision. spatialanalysis-personcrossingline. Debug | Śledzi, gdy osoba przecina wydzielony wiersz w polu widoku aparatu. <br>Emituje zdarzenie _personLineEvent_ , gdy osoba przecina wiersz i udostępnia informacje kierunkowe. 
| cognitiveservices. Vision. spatialanalysis-personcrossingpolygon. Debug | Śledzi, gdy osoba przecina wydzielony wiersz w polu widoku aparatu. <br> Emituje zdarzenie _personLineEvent_ , gdy osoba przecina strefę i udostępnia informacje kierunkowe. |
| cognitiveservices. Vision. spatialanalysis-persondistance. Debug | Śledzi, gdy ludzie naruszają regułę odległości. <br> Emituje _personDistanceEvent_ okresowo przy użyciu lokalizacji każdego naruszenia odległości. |

Analiza przestrzenna może być również uruchamiana z użyciem [analizy wideo na żywo](https://aka.ms/lva-spatial-analysis) jako ich modułu wideo AI. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Identyfikator operacji| Opis|
|---------|---------|
| cognitiveservices. Vision. spatialanalysis-personcount. livevideoanalytics | Zlicza osoby znajdujące się w określonej strefie w polu widoku aparatu. <br> Emituje początkowe zdarzenie _personCountEvent_ , a następnie _personCountEvent_ zdarzenia w przypadku zmiany liczby.  |
| cognitiveservices. Vision. spatialanalysis-personcrossingline. livevideoanalytics | Śledzi, gdy osoba przecina wydzielony wiersz w polu widoku aparatu. <br>Emituje zdarzenie _personLineEvent_ , gdy osoba przecina wiersz i udostępnia informacje kierunkowe. 
| cognitiveservices. Vision. spatialanalysis-personcrossingpolygon. livevideoanalytics | Śledzi, gdy osoba przecina wydzielony wiersz w polu widoku aparatu. <br> Emituje zdarzenie _personLineEvent_ , gdy osoba przecina strefę i udostępnia informacje kierunkowe. |
| cognitiveservices. Vision. spatialanalysis-persondistance. livevideoanalytics | Śledzi, gdy ludzie naruszają regułę odległości. <br> Emituje _personDistanceEvent_ okresowo przy użyciu lokalizacji każdego naruszenia odległości. |

Operacje analizy wideo na żywo są również dostępne w `.debug` wersji (np. cognitiveservices. Vision. spatialanalysis-personcount. livevideoanalytics. Debug), która oferuje możliwość wizualizacji ramek wideo w miarę ich przetwarzania. `xhost +`Aby włączyć wizualizację klatek i zdarzeń wideo, należy uruchomić program na komputerze hosta

> [!IMPORTANT]
> Modele AI wykrywają i lokalizują obecność człowieka w filmach wideo i danych wyjściowych przy użyciu obwiedni wokół ciała ludzkiego. Modele AI nie próbują wykrywać twarzy ani odkrywać tożsamości ani demograficznych osób.

Są to parametry wymagane przez każdą z tych operacji analizy przestrzennej.

| Parametry operacji| Opis|
|---------|---------|
| Identyfikator operacji | Identyfikator operacji z tabeli powyżej.|
| enabled | Wartość logiczna: true lub false|
| VIDEO_URL| Adres URL protokołu RTSP dla urządzenia aparatu fotograficznego (przykład: `rtsp://username:password@url` ). Analiza przestrzenna obsługuje strumień w formacie H. 264 przy użyciu protokołu RTSP, http lub MP4 |
| VIDEO_SOURCE_ID | Przyjazna nazwa urządzenia aparatu lub strumienia wideo. Zostanie on zwrócony z danymi wyjściowymi zdarzenia JSON.|
| VIDEO_IS_LIVE| Wartość true w przypadku urządzeń z kamerą; wartość false dla zarejestrowanych filmów wideo.|
| VIDEO_DECODE_GPU_INDEX| Procesor GPU do zdekodowania ramki wideo. Domyślnie jest to 0. Powinna być taka sama jak `gpu_index` w przypadku innych konfiguracji węzła, takich jak `VICA_NODE_CONFIG` , `DETECTOR_NODE_CONFIG` .|
| DETECTOR_NODE_CONFIG | KOD JSON wskazujący procesor GPU, na którym ma być uruchamiany węzeł czujnika. Powinien mieć następujący format: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | Konfiguracja JSON dla strefy i linii, jak pokazano poniżej.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Konfiguracja strefy dla cognitiveservices. Vision. spatialanalysis-personcount

 Jest to przykład danych wejściowych JSON dla parametru SPACEANALYTICS_CONFIG, który konfiguruje strefę. Dla tej operacji można skonfigurować wiele stref.

```json
{
"zones":[{
    "name": "lobbycamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "output_frequency": 1
      }
    }]
}
```

| Nazwa | Typ| Opis|
|---------|---------|---------|
| `zones` | list| Lista stref. |
| `name` | ciąg| Przyjazna nazwa dla tej strefy.|
| `polygon` | list| Każda para wartości reprezentuje symbol x, y dla wierzchołków wielokąta. Wielokąt reprezentuje obszary, w których są śledzone lub zliczane i punkty wielokąta opierają się na znormalizowanych współrzędnych (0-1), gdzie lewy górny róg to (0,0, 0,0) i prawy dolny róg (1,0, 1,0).   
| `threshold` | float| Zdarzenia są egressed, gdy poziom zaufania modeli AI jest większy lub równy tej wartości. |
| `type` | ciąg| Dla **cognitiveservices. Vision. spatialanalysis-personcount** powinno to być `count` .|
| `trigger` | ciąg| Typ wyzwalacza do wysyłania zdarzenia. Obsługiwane wartości są używane `event` do wysyłania zdarzeń, gdy liczba zmienia się lub `interval` okresowo wysyła zdarzenia niezależnie od tego, czy liczba uległa zmianie, czy nie.
| `interval` | ciąg| Czas w sekundach, przez który liczba osób zostanie zagregowana, zanim zdarzenie zostanie wyzwolone. Operacja będzie kontynuować analizowanie sceny z stałą szybkością i zwraca najbardziej typową liczbę w tym interwale. Interwał agregacji ma zastosowanie do obu `event` i `interval` .|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Konfiguracja linii dla cognitiveservices. Vision. spatialanalysis-personcrossingline

Jest to przykład danych wejściowych JSON dla parametru SPACEANALYTICS_CONFIG, który konfiguruje wiersz. Można skonfigurować wiele skrzyżowanych linii dla tej operacji.

```json
{
"lines":[{
    "name": "doorcamera" 
    "line": {
        "start": {"x": 0, "y": 0.5},
        "end": {"x": 1, "y": 0.5}
            },
    "threshold": 50.00,
    "events":[{
        "type": "linecrossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Nazwa | Typ| Opis|
|---------|---------|---------|
| `lines` | list| Lista wierszy.|
| `name` | ciąg| Przyjazna nazwa dla tego wiersza.|
| `line` | list| Definicja wiersza. Jest to linia kierunkowa umożliwiająca zrozumienie "wpisu" i "Exit".|
| `start` | para wartości| Współrzędne x, y punktu początkowego wiersza. Wartości zmiennoprzecinkowe reprezentują pozycję wierzchołka względem górnego, lewego rogu. Aby obliczyć bezwzględne wartości x, y, należy pomnożyć te wartości z rozmiarem ramki. |
| `end` | para wartości| Współrzędne x, y punktu końcowego wiersza. Wartości zmiennoprzecinkowe reprezentują pozycję wierzchołka względem górnego, lewego rogu. Aby obliczyć bezwzględne wartości x, y, należy pomnożyć te wartości z rozmiarem ramki. |
| `threshold` | float| Zdarzenia są egressed, gdy poziom zaufania modeli AI jest większy lub równy tej wartości. |
| `type` | ciąg| Dla **cognitiveservices. Vision. spatialanalysis-personcrossingline** powinno to być `linecrossing` .|
|`trigger`|ciąg|Typ wyzwalacza do wysyłania zdarzenia.<br>Obsługiwane wartości: "Event": Fire, gdy ktoś przecina linię.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Konfiguracja strefy dla cognitiveservices. Vision. spatialanalysis-personcrossingpolygon

Jest to przykład danych wejściowych JSON dla parametru SPACEANALYTICS_CONFIG, który konfiguruje strefę. Dla tej operacji można skonfigurować wiele stref.

 ```json
{
"zones":[{
    "name": "queuecamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "zone_crossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Nazwa | Typ| Opis|
|---------|---------|---------|
| `zones` | list| Lista stref. |
| `name` | ciąg| Przyjazna nazwa dla tej strefy.|
| `polygon` | list| Każda para wartości reprezentuje symbol x, y dla wierzchołków wielokąta. Wielokąt reprezentuje obszary, w których są śledzone lub zliczane ludzie. Wartości zmiennoprzecinkowe reprezentują pozycję wierzchołka względem górnego, lewego rogu. Aby obliczyć bezwzględne wartości x, y, należy pomnożyć te wartości z rozmiarem ramki. 
| `threshold` | float| Zdarzenia są egressed, gdy poziom zaufania modeli AI jest większy lub równy tej wartości. |
| `type` | ciąg| Dla **cognitiveservices. Vision. spatialanalysis-personcrossingpolygon** powinno to być `enter` lub `exit` .|
| `trigger`|ciąg|Typ wyzwalacza do wysyłania zdarzenia<br>Obsługiwane wartości: "Event": Fire, gdy ktoś wprowadzi lub wyjdzie z strefy.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Konfiguracja strefy dla cognitiveservices. Vision. spatialanalysis-persondistance

Jest to przykład danych wejściowych JSON dla parametru SPACEANALYTICS_CONFIG, który konfiguruje strefę dla **cognitiveservices. Vision. spatialanalysis-persondistance**. Dla tej operacji można skonfigurować wiele stref.

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 35.00,
    "events":[{
        "type": "persondistance",
        "config":{
            "trigger": "event",
            "output_frequency":1,
            "minimum_distance_threshold":6.0,
            "maximum_distance_threshold":35.0
            }
        }]
    }]
}
```

| Nazwa | Typ| Opis|
|---------|---------|---------|
| `zones` | list| Lista stref. |
| `name` | ciąg| Przyjazna nazwa dla tej strefy.|
| `polygon` | list| Każda para wartości reprezentuje symbol x, y dla wierzchołków wielokąta. Wielokąt reprezentuje obszary, w których zliczane są osoby, a odległość między osobami jest mierzona. Wartości zmiennoprzecinkowe reprezentują pozycję wierzchołka względem górnego, lewego rogu. Aby obliczyć bezwzględne wartości x, y, należy pomnożyć te wartości z rozmiarem ramki. 
| `threshold` | float| Zdarzenia są egressed, gdy poziom zaufania modeli AI jest większy lub równy tej wartości. |
| `type` | ciąg| Dla **cognitiveservices. Vision. spatialanalysis-persondistance** powinno to być `people_distance` .|
| `trigger` | ciąg| Typ wyzwalacza do wysyłania zdarzenia. Obsługiwane wartości są używane `event` do wysyłania zdarzeń, gdy liczba zmienia się lub `interval` okresowo wysyła zdarzenia niezależnie od tego, czy liczba uległa zmianie, czy nie.
| `interval` | ciąg | Czas (w sekundach) agregowania naruszeń, zanim zdarzenie zostanie wyzwolone. Interwał agregacji ma zastosowanie do obu `event` i `interval` .|
| `output_frequency` | int | Częstotliwość, z jaką zdarzenia są egressed. Gdy `output_frequency` = x, każde zdarzenie X to egressed, np. `output_frequency` = 2 oznacza każde inne zdarzenie w danych wyjściowych. Output_frequency ma zastosowanie do obu `event` i `interval` .|
| `minimum_distance_threshold` | float| Odległość, która spowoduje wyzwolenie zdarzenia "TooClose", gdy ludzie są mniej niż odległość od siebie.|
| `maximum_distance_threshold` | float| Odległość, która spowoduje wyzwolenie zdarzenia "TooFar", gdy ludzie są większe niż odległość od siebie.|

Jest to przykład danych wejściowych JSON dla parametru DETECTOR_NODE_CONFIG, który służy do konfigurowania strefy **cognitiveservices. Vision. spatialanalysis-persondistance** .

```json
{ 
"gpu_index": 0, 
"do_calibration": true
}
```

| Nazwa | Typ| Opis|
|---------|---------|---------|
| `gpu_index` | ciąg| Indeks procesora GPU, na którym zostanie uruchomiona ta operacja.|
| `do_calibration` | ciąg | Wskazuje, że kalibracja jest włączona. `do_calibration` Aby funkcja **cognitiveservices. Vision. spatialanalysis-persondistance** działała prawidłowo, musi mieć wartość true.|

Zobacz wskazówki dotyczące [umieszczania w aparacie](spatial-analysis-camera-placement.md)  , aby poznać konfiguracje stref i linii.

## <a name="spatial-analysis-operation-output"></a>Dane wyjściowe operacji analizy przestrzennej

Zdarzenia z każdej operacji są egressed na platformie Azure IoT Hub w formacie JSON.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>Format JSON dla cognitiveservices. Vision. spatialanalysis-personcount AI Insights

Przykładowy kod JSON dla zdarzenia danych wyjściowych przez tę operację.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nazwa pola zdarzenia | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator zdarzenia|
| `type` | ciąg| Typ zdarzenia|
| `detectionsId` | array| Tablica o rozmiarze 1 unikatowego identyfikatora wykrywania osoby, która wyzwoliła to zdarzenie|
| `properties` |  — kolekcja| Kolekcja wartości|
| `trackinId` | ciąg| Wykryto unikatowy identyfikator osoby|
| `status` | ciąg| "Enter" lub "Exit"|
| `side` | int| Liczba krawędzi wielokąta, który przekroczy osoba|
| `zone` | ciąg | Pole "name" wielokąta reprezentujące przekreśloną strefę|
| `trigger` | ciąg| Typ wyzwalacza to "Event" lub "Interval" w zależności od wartości `trigger` w SPACEANALYTICS_CONFIG|

| Nazwa pola wykrywania | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator wykrywania|
| `type` | ciąg| Typ wykrywania|
| `region` |  — kolekcja| Kolekcja wartości|
| `type` | ciąg| Typ regionu|
| `points` |  — kolekcja| Górny lewy i prawy dolny punkt, gdy typ regionu to prostokąt |
| `confidence` | float| Zaufanie algorytmu|

| Nazwa pola SourceInfo | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator aparatu|
| `timestamp` | data| Data UTC, kiedy ładunek JSON został emitowany|
| `width` | int | Szerokość ramki wideo|
| `height` | int | Wysokość ramki wideo|
| `frameId` | int | Identyfikator ramki|
| `cameraCallibrationInfo` |  — kolekcja | Kolekcja wartości|
| `status` | ciąg | Wskazuje, czy Kalibracja aparatu do płaszczyzny uziemienia jest "kompletna"|
| `cameraHeight` | float | Wysokość kamery nad ziemią w stóp. Ta funkcja jest wywnioskowana z autokalibracji. |
| `focalLength` | float | Długość Ogniskowa aparatu (w pikselach). Ta funkcja jest wywnioskowana z autokalibracji. |
| `tiltUpAngle` | float | Kąt pochylenia aparatu w pionie. Ta funkcja jest wywnioskowana z autokalibracji.|

| Nazwa pola SourceInfo | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator aparatu|
| `timestamp` | data| Data UTC, kiedy ładunek JSON został emitowany|
| `width` | int | Szerokość ramki wideo|
| `height` | int | Wysokość ramki wideo|
| `frameId` | int | Identyfikator ramki|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>Format JSON dla cognitiveservices. Vision. spatialanalysis-personcrossingline AI Insights

Przykładowy kod JSON dla wykrywania danych wyjściowych przez tę operację.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```
| Nazwa pola zdarzenia | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator zdarzenia|
| `type` | ciąg| Typ zdarzenia|
| `detectionsId` | array| Tablica o rozmiarze 1 unikatowego identyfikatora wykrywania osoby, która wyzwoliła to zdarzenie|
| `properties` |  — kolekcja| Kolekcja wartości|
| `trackinId` | ciąg| Wykryto unikatowy identyfikator osoby|
| `status` | ciąg| Kierunek skrzyżowań linii, "CrossLeft" lub "CrossRight"|
| `zone` | ciąg | Pole "name" w wierszu, który został przekreślony|

| Nazwa pola wykrywania | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator wykrywania|
| `type` | ciąg| Typ wykrywania|
| `region` |  — kolekcja| Kolekcja wartości|
| `type` | ciąg| Typ regionu|
| `points` |  — kolekcja| Górny lewy i prawy dolny punkt, gdy typ regionu to prostokąt |
| `confidence` | float| Zaufanie algorytmu|

| Nazwa pola SourceInfo | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator aparatu|
| `timestamp` | data| Data UTC, kiedy ładunek JSON został emitowany|
| `width` | int | Szerokość ramki wideo|
| `height` | int | Wysokość ramki wideo|
| `frameId` | int | Identyfikator ramki|


> [!IMPORTANT]
> Model AI wykrywa osobę niezależnie od tego, czy osoba jest dołączona do aparatu, czy nie. Model AI nie uruchamia wykrywania ani rozpoznawania lub nie emituje żadnych informacji biometrycznych. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>Format JSON dla cognitiveservices. Vision. spatialanalysis-personcrossingpolygon AI Insights

Przykładowy kod JSON dla wykrywania danych wyjściowych przez tę operację.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": ""
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nazwa pola zdarzenia | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator zdarzenia|
| `type` | ciąg| Typ zdarzenia|
| `detectionsId` | array| Tablica o rozmiarze 1 unikatowego identyfikatora wykrywania osoby, która wyzwoliła to zdarzenie|
| `properties` |  — kolekcja| Kolekcja wartości|
| `trackinId` | ciąg| Wykryto unikatowy identyfikator osoby|
| `status` | ciąg| Kierunek skrzyżowania wielokąta, "Enter" lub "Exit"|
| `zone` | ciąg | Pole "name" wielokąta reprezentujące przekreśloną strefę|

| Nazwa pola wykrywania | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator wykrywania|
| `type` | ciąg| Typ wykrywania|
| `region` |  — kolekcja| Kolekcja wartości|
| `type` | ciąg| Typ regionu|
| `points` |  — kolekcja| Górny lewy i prawy dolny punkt, gdy typ regionu to prostokąt |
| `confidence` | float| Zaufanie algorytmu|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>Format JSON dla cognitiveservices. Vision. spatialanalysis-persondistance AI Insights

Przykładowy kod JSON dla wykrywania danych wyjściowych przez tę operację.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nazwa pola zdarzenia | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator zdarzenia|
| `type` | ciąg| Typ zdarzenia|
| `detectionsId` | array| Tablica o rozmiarze 1 unikatowego identyfikatora wykrywania osoby, która wyzwoliła to zdarzenie|
| `properties` |  — kolekcja| Kolekcja wartości|
| `personCount` | int| Liczba osób wykrytych podczas emisji zdarzenia|
| `averageDistance` | float| Średnia odległość między wszystkimi wykrytymi osobami w stóp|
| `minimumDistanceThreshold` | float| Odległość, która spowoduje wyzwolenie zdarzenia "TooClose", gdy ludzie są mniej niż odległość od siebie.|
| `maximumDistanceThreshold` | float| Odległość w stopniu, która wywoła zdarzenie "TooFar", gdy ludzie są większe niż odległość od siebie.|
| `eventName` | ciąg| Nazwa zdarzenia jest `TooClose` `minimumDistanceThreshold` naruszana, `TooFar` gdy `maximumDistanceThreshold` jest naruszona lub `unknown` gdy nie ukończono autokalibracji|
| `distanceViolationPersonCount` | int| Liczba osób wykrytych w przypadku naruszenia `minimumDistanceThreshold` lub `maximumDistanceThreshold`|
| `zone` | ciąg | Pole "name" wielokąta reprezentujące strefę monitorowaną dla distancing między osobami|
| `trigger` | ciąg| Typ wyzwalacza to "Event" lub "Interval" w zależności od wartości `trigger` w SPACEANALYTICS_CONFIG|

| Nazwa pola wykrywania | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator wykrywania|
| `type` | ciąg| Typ wykrywania|
| `region` |  — kolekcja| Kolekcja wartości|
| `type` | ciąg| Typ regionu|
| `points` |  — kolekcja| Górny lewy i prawy dolny punkt, gdy typ regionu to prostokąt |
| `confidence` | float| Zaufanie algorytmu|
| `centerGroundPoint` | 2 wartości zmiennoprzecinkowe| `x`, `y` wartości ze współrzędnymi odwołującej się osoby na ziemi w stawce. `x` jest to odległość od aparatu prostopadle do płaszczyzny obrazu aparatu, która jest rzutowana na ziemię w stawce. `y` jest to odległość od aparatu do warstwy obrazu, która jest rzutowana na ziemię w stawce.|

| Nazwa pola SourceInfo | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator aparatu|
| `timestamp` | data| Data UTC, kiedy ładunek JSON został emitowany|
| `width` | int | Szerokość ramki wideo|
| `height` | int | Wysokość ramki wideo|
| `frameId` | int | Identyfikator ramki|
| `cameraCallibrationInfo` |  — kolekcja | Kolekcja wartości|
| `status` | ciąg | Wskazuje, czy Kalibracja aparatu do płaszczyzny uziemienia jest "kompletna"|
| `cameraHeight` | float | Wysokość kamery nad ziemią w stóp. Ta funkcja jest wywnioskowana z autokalibracji. |
| `focalLength` | float | Długość Ogniskowa aparatu (w pikselach). Ta funkcja jest wywnioskowana z autokalibracji. |
| `tiltUpAngle` | float | Kąt pochylenia aparatu w pionie. Ta funkcja jest wywnioskowana z autokalibracji.|


## <a name="use-the-output-generated-by-the-container"></a>Użyj danych wyjściowych wygenerowanych przez kontener

Możesz chcieć zintegrować wykrywanie przestrzenne lub zdarzenia w aplikacji. Oto kilka metod, które należy wziąć pod uwagę: 

* Użyj zestawu SDK usługi Azure Event Hub dla wybranego języka programowania, aby nawiązać połączenie z punktem końcowym usługi Azure IoT Hub i odebrać zdarzenia. Aby uzyskać więcej informacji [, zobacz odczytywanie komunikatów z urządzenia do chmury z wbudowanego punktu końcowego](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) . 
* Skonfiguruj **Routing komunikatów** na IoT Hub platformy Azure, aby wysyłać zdarzenia do innych punktów końcowych, lub Zapisz zdarzenia w magazynie danych. Aby uzyskać więcej informacji, zobacz [IoT Hub Routing komunikatów](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) . 
* Skonfiguruj zadanie Azure Stream Analytics, aby przetwarzać zdarzenia w czasie rzeczywistym w miarę ich odbierania i tworzenia wizualizacji. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Wdrażanie operacji analizy przestrzennej na dużą skalę (wiele kamer)

Aby uzyskać najlepszą wydajność i wykorzystanie procesorów GPU, można wdrożyć wszystkie operacje analizy przestrzennej dla wielu kamer przy użyciu wystąpień grafu. Poniżej znajduje się przykład służący do uruchamiania `cognitiveservices.vision.spatialanalysis-personcount` operacji w pięciu aparatach.

```json
 "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
          "personcount": {
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
              "version": 1,
              "enabled": true,
              "sharedNodes": {
                  "shared_detector1": {
                      "node": "PersonCountGraph.detector",
                      "parameters": {
                          "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 5}",
                      }
                  }
              },
              "parameters": {
                  "VIDEO_DECODE_GPU_INDEX": 0,
                  "VIDEO_IS_LIVE": true
              },
              "instances": {
                  "1": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                          "VIDEO_SOURCE_ID": "camera 1",
                          "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"zone5\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]],\"threshold\":50.0, \"events\":[{\"type\":\"count\", \"output_frequency\": 1}]}]}"
                      }
                  },
                  "2": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                          "VIDEO_SOURCE_ID": "camera 2",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "3": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                          "VIDEO_SOURCE_ID": "camera 3",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "4": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                          "VIDEO_SOURCE_ID": "camera 4",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "5": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                          "VIDEO_SOURCE_ID": "camera 5",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  }
              }
          }
      }
  }
  ```
| Nazwa | Typ| Opis|
|---------|---------|---------|
| `batch_size` | int | Wskazuje liczbę kamer, które będą używane w operacji. |

## <a name="next-steps"></a>Następne kroki

* [Wdróż aplikację sieci Web zliczania osób](spatial-analysis-web-app.md)
* [Rejestrowanie i rozwiązywanie problemów](spatial-analysis-logging.md)
* [Przewodnik umieszczania w aparacie](spatial-analysis-camera-placement.md)
* [Przewodnik umieszczania strefy i linii](spatial-analysis-zone-line-placement.md)
