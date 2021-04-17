---
title: Operacje analizy przestrzennej
titleSuffix: Azure Cognitive Services
description: Operacje analizy przestrzennej.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 37ac7573a1794c97c81fe5364204f85ff14d9fa6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538078"
---
# <a name="spatial-analysis-operations"></a>Operacje analizy przestrzennej

Analiza przestrzenna umożliwia analizę wideo przesyłanego strumieniowo w czasie rzeczywistym z urządzeń aparatów. Dla każdego skonfigurowanego urządzenia aparatu operacje analizy przestrzennej wygeneruje strumień wyjściowy komunikatów JSON wysyłanych do wystąpienia Azure IoT Hub. 

Kontener analizy przestrzennej implementuje następujące operacje:

| Identyfikator operacji| Opis|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount | Zlicza osoby w wyznaczonej strefie w polu widzenia aparatu. Strefa musi być w pełni objęta pojedynczym aparatem, aby element PersonCount rejestrował dokładną sumę. <br> Emituje początkowe zdarzenie _personCountEvent,_ a następnie _zdarzenia personCountEvent_ po zmianie liczby zdarzeń.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline | Śledzi, kiedy osoba przekracza wyznaczoną linię w polu widzenia kamery. <br>Emituje zdarzenie _personLineEvent,_ gdy osoba przekroczy linię i poda informacje kierunkowe. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon | Emituje zdarzenie _personZoneEnterExitEvent,_ gdy osoba wchodzi do strefy lub kończy jej działanie, i udostępnia informacje kierunkowe z numerowana strona strefy, która została skrzyżowana. Emituje _personZoneDwellTimeEvent,_ gdy osoba opuści strefę i udostępnia informacje kierunkowe, a także liczbę milisekund spędzonych w strefie. |
| cognitiveservices.vision.spatialanalysis-persondistance | Śledzi, kiedy ludzie naruszają regułę odległości. <br> Okresowo emituje _personDistanceEvent_ z lokalizacją każdego naruszenia odległości. |
| cognitiveservices.vision.spatialanalysis | Operacja ogólna, która może służyć do uruchamiania wszystkich scenariuszy wymienionych powyżej. Ta opcja jest bardziej przydatna, gdy chcesz uruchomić wiele scenariuszy na tym samym aparacie lub wydajniej korzystać z zasobów systemowych (np. procesora GPU). |

Wszystkie powyższe operacje są również dostępne w wersji , która umożliwia wizualizowanie ramek wideo podczas `.debug` ich przetwarzania. Aby włączyć wizualizację klatek wideo i zdarzeń, należy uruchomić program na `xhost +` komputerze-hoście.

| Identyfikator operacji| Opis|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.debug | Zlicza osoby w wyznaczonej strefie w polu widzenia aparatu. <br> Emituje początkowe zdarzenie _personCountEvent,_ a następnie _zdarzenia personCountEvent_ po zmianie liczby.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.debug | Śledzi, kiedy osoba przekracza wyznaczoną linię w polu widzenia aparatu. <br>Emituje zdarzenie _personLineEvent,_ gdy osoba przekroczy linię i poda informacje kierunkowe. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug | Emituje _zdarzenie personZoneEnterExitEvent,_ gdy osoba wchodzi do strefy lub znika z tej strefy, i udostępnia informacje kierunkowe z numerową stroną strefy, która została skrzyżowana. Emituje _personZoneDwellTimeEvent,_ gdy osoba opuści strefę i udostępnia informacje kierunkowe, a także liczbę milisekund spędzonych w strefie. |
| cognitiveservices.vision.spatialanalysis-persondistance.debug | Śledzi, kiedy ludzie naruszają regułę odległości. <br> Okresowo emituje _personDistanceEvent_ z lokalizacją każdego naruszenia odległości. |
| cognitiveservices.vision.spatialanalysis.debug | Operacja ogólna, która może służyć do uruchamiania wszystkich scenariuszy wymienionych powyżej. Ta opcja jest bardziej przydatna, gdy chcesz uruchomić wiele scenariuszy na tym samym aparacie lub wydajniej korzystać z zasobów systemowych (np. procesora GPU). |

Analizę przestrzenną można również uruchamiać [z Live Video Analytics](../../media-services/live-video-analytics-edge/spatial-analysis-tutorial.md) jako modułem video AI. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Identyfikator operacji| Opis|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics | Zlicza osoby w wyznaczonej strefie w polu widzenia aparatu. <br> Emituje początkowe zdarzenie _personCountEvent,_ a następnie _zdarzenia personCountEvent_ po zmianie liczby zdarzeń.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.livevideoanalytics | Śledzi, kiedy osoba przekracza wyznaczoną linię w polu widzenia kamery. <br>Emituje zdarzenie _personLineEvent,_ gdy osoba przekroczy linię i poda informacje kierunkowe. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics | Emituje zdarzenie _personZoneEnterExitEvent,_ gdy osoba wchodzi do strefy lub kończy jej działanie, i udostępnia informacje kierunkowe z numerowana strona strefy, która została skrzyżowana. Emituje _personZoneDwellTimeEvent,_ gdy osoba opuści strefę i udostępnia informacje kierunkowe, a także liczbę milisekund spędzonych w strefie.  |
| cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics | Śledzi, kiedy ludzie naruszają regułę odległości. <br> Okresowo emituje _personDistanceEvent_ z lokalizacją każdego naruszenia odległości. |
| cognitiveservices.vision.spatialanalysis.livevideoanalytics | Operacja ogólna, która może służyć do uruchamiania wszystkich scenariuszy wymienionych powyżej. Ta opcja jest bardziej przydatna, gdy chcesz uruchomić wiele scenariuszy na tym samym aparacie lub wydajniej korzystać z zasobów systemowych (np. procesora GPU). |

Live Video Analytics są również dostępne w wersji `.debug` (np. cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics.debug), która umożliwia wizualizowanie ramek wideo jako przetwarzanych. Aby włączyć wizualizację klatek wideo i zdarzeń, należy uruchomić program `xhost +` na komputerze-hoście

> [!IMPORTANT]
> Modele AI do obrazów wykrywają i lokalizują obecność człowieka na nagraniu wideo i w danych wyjściowych przy użyciu pola ograniczonego wokół ludzkiego ciała. Modele AI nie próbują odnaleźć tożsamości ani danych demograficznych poszczególnych osób.

Są to parametry wymagane przez każdą z tych operacji analizy przestrzennej.

| Parametry operacji| Opis|
|---------|---------|
| Identyfikator operacji | Identyfikator operacji z powyższej tabeli.|
| enabled | Wartość logiczna: true lub false|
| VIDEO_URL| Adres URL RTSP urządzenia aparatu fotograficznego (przykład: `rtsp://username:password@url` ). Analiza przestrzenna obsługuje strumień zakodowany w formacie H.264 za pośrednictwem protokołu RTSP, http lub mp4. Video_URL można określić jako zaciemnianą wartość ciągu base64 przy użyciu szyfrowania AES, a jeśli adres URL wideo jest zaciemniany, należy go określić jako zmienne `KEY_ENV` `IV_ENV` środowiskowe. Przykładowe narzędzie do generowania kluczy i szyfrowania można [znaleźć tutaj.](/dotnet/api/system.security.cryptography.aesmanaged) |
| VIDEO_SOURCE_ID | Przyjazna nazwa urządzenia aparatu lub strumienia wideo. Zostanie to zwrócone z danych wyjściowych JSON zdarzenia.|
| VIDEO_IS_LIVE| Prawda w przypadku urządzeń z aparatami fotograficznymi; wartość false dla zarejestrowanych filmów wideo.|
| VIDEO_DECODE_GPU_INDEX| Procesor GPU do dekodowania ramki wideo. Domyślnie jest to 0. Powinna być taka sama jak w `gpu_index` innej konfiguracji węzła, takiej jak `VICA_NODE_CONFIG` , `DETECTOR_NODE_CONFIG` .|
| INPUT_VIDEO_WIDTH | Szerokość ramki wejściowego wideo/strumienia (np. 1920). Jest to pole opcjonalne. Jeśli zostanie podane, ramka zostanie przeskalowana do tego wymiaru przy zachowaniu współczynnika proporcji.|
| DETECTOR_NODE_CONFIG | JSON wskazujący, na którym procesorze GPU ma być uruchomiony węzeł detektora. Powinna ona mieć następujący format: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | Konfiguracja JSON dla strefy i linii, jak opisano poniżej.|
| ENABLE_FACE_MASK_CLASSIFIER | `True` aby włączyć wykrywanie osób noszących maski twarzy w strumieniu wideo, `False` aby je wyłączyć. Domyślnie ta opcja jest wyłączona. Wykrywanie maski twarzy wymaga, aby wejściowy parametr szerokości wideo to 1920 `"INPUT_VIDEO_WIDTH": 1920` . Atrybut maski twarzy nie zostanie zwrócony, jeśli wykryte osoby nie są skierowane do aparatu lub znajdują się zbyt daleko od niego. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem](spatial-analysis-camera-placement.md) umieszczania aparatu |

### <a name="detector-node-parameter-settings"></a>Ustawienia parametrów węzła detektora
Jest to przykład parametrów DETECTOR_NODE_CONFIG dla wszystkich operacji analizy przestrzennej.

```json
{
"gpu_index": 0,
"do_calibration": true,
"enable_recalibration": true,
"calibration_quality_check_frequency_seconds":86400,
"calibration_quality_check_sample_collect_frequency_seconds": 300,
"calibration_quality_check_one_round_sample_collect_num":10,
"calibration_quality_check_queue_max_size":1000
}
```

| Nazwa | Typ| Opis|
|---------|---------|---------|
| `gpu_index` | ciąg| Indeks procesora GPU, na którym będzie uruchamiana ta operacja.|
| `do_calibration` | ciąg | Wskazuje, że została włączona tylko ta włączona. `do_calibration` Aby funkcja **cognitiveservices.vision.spatialanalysis-persondistance działała** prawidłowo, musi mieć wartość true. do_calibration jest domyślnie ustawiona na wartość True. |
| `enable_recalibration` | bool | Wskazuje, czy automatyczna recalibracja jest włączona. Wartość domyślna to `true`.|
| `calibration_quality_check_frequency_seconds` | int | Minimalna liczba sekund między poszczególnymi sprawdzeniami jakości w celu określenia, czy jest wymagana ponowna kontrola. Wartość `86400` domyślna to (24 godziny). Używany tylko wtedy, gdy `enable_recalibration=True` .|
| `calibration_quality_check_sample_collect_frequency_seconds` | int | Minimalna liczba sekund między zebraniem nowych próbek danych w celu ponownej weryfikacji i kontroli jakości. Wartość domyślna `300` to (5 minut). Używany tylko wtedy, gdy `enable_recalibration=True` .|
| `calibration_quality_check_one_round_sample_collect_num` | int | Minimalna liczba nowych próbek danych do zebrania na rundę zbierania próbek. Wartość domyślna to `10`. Używany tylko wtedy, gdy `enable_recalibration=True` .|
| `calibration_quality_check_queue_max_size` | int | Maksymalna liczba próbek danych do przechowywania, gdy model aparatu jest skalibrowany. Wartość domyślna to `1000`. Używany tylko wtedy, gdy `enable_recalibration=True` .|
| `enable_breakpad`| bool | Wskazuje, czy chcesz włączyć wyłącznik, który jest używany do generowania zrzutu awaryjnego na użytek debugowania. Jest `false` on domyślnie. Jeśli ustawisz ją na wartość , musisz również dodać `true` element w części kontenera `"CapAdd": ["SYS_PTRACE"]` `HostConfig` `createOptions` . Domyślnie zrzut awaryjnego jest przekazywany do aplikacji [RealTimePersonTracking](https://appcenter.ms/orgs/Microsoft-Organization/apps/RealTimePersonTracking/crashes/errors?version=&appBuild=&period=last90Days&status=&errorType=all&sortCol=lastError&sortDir=desc) AppCenter. Jeśli chcesz, aby zrzuty awaryjne zostały przekazane do Twojej własnej aplikacji AppCenter, możesz zastąpić zmienną środowiskową kluczem tajnym `RTPT_APPCENTER_APP_SECRET` aplikacji.

## <a name="spatial-analysis-operations-configuration-and-output"></a>Konfiguracja i dane wyjściowe operacji analizy przestrzennej
### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Konfiguracja strefy dla cognitiveservices.vision.spatialanalysis-personcount

 Jest to przykład danych wejściowych JSON dla SPACEANALYTICS_CONFIG, który konfiguruje strefę. Dla tej operacji można skonfigurować wiele stref.

```json
{
"zones":[{
       "name": "lobbycamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
              "type": "count",
              "config":{
                     "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
      }
       }]
}
```

| Nazwa | Typ| Opis|
|---------|---------|---------|
| `zones` | list| Lista stref. |
| `name` | ciąg| Przyjazna nazwa dla tej strefy.|
| `polygon` | list| Każda para wartości reprezentuje x,y dla wierzchołków wielokąta. Wielokąt reprezentuje obszary, w których osoby są śledzone lub liczone, a punkty wielokąta są oparte na znormalizowanych współrzędnych (0–1), gdzie lewy górny róg to (0,0, 0,0), a prawy dolny róg to (1,0, 1,0).   
| `threshold` | float| Zdarzenia są wychodzące, gdy osoba jest większa niż ta liczba pikseli w strefie. |
| `type` | ciąg| W **przypadku cognitiveservices.vision.spatialanalysis-personcount** powinno to być `count` .|
| `trigger` | ciąg| Typ wyzwalacza do wysyłania zdarzenia. Obsługiwane wartości są do wysyłania zdarzeń, gdy liczba zmienia się lub wysyłanie zdarzeń okresowo, niezależnie od `event` `interval` tego, czy liczba została zmieniona, czy nie.
| `output_frequency` | int | Szybkość, z jaką zdarzenia są wychodzące. Gdy `output_frequency` = X, każde zdarzenie X jest wychodzące, np. `output_frequency` = 2 oznacza, że każde inne zdarzenie jest wyjściem. Ma `output_frequency` zastosowanie zarówno do , jak i `event` `interval` . |
| `focus` | ciąg| Lokalizacja punktu w polu granicy osoby używana do obliczania zdarzeń. Wartością fokusu może być (ślad osoby), (dolny środek pola ograniczonego `footprint` `bottom_center` osoby) (środek pola `center` ograniczonego osoby).|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Konfiguracja linii dla cognitiveservices.vision.spatialanalysis-personcrossingline

Jest to przykład danych wejściowych JSON dla parametru SPACEANALYTICS_CONFIG, który konfiguruje wiersz. Dla tej operacji można skonfigurować wiele wierszy przekroczenia.

```json
{
   "lines": [
       {
           "name": "doorcamera",
           "line": {
               "start": {
                   "x": 0,
                   "y": 0.5
               },
               "end": {
                   "x": 1,
                   "y": 0.5
               }
           },
           "events": [
               {
                   "type": "linecrossing",
                   "config": {
                       "trigger": "event",
                       "threshold": 16.00,
                       "focus": "footprint"
                   }
               }
           ]
       }
   ]
}
```

| Nazwa | Typ| Opis|
|---------|---------|---------|
| `lines` | list| Lista wierszy.|
| `name` | ciąg| Przyjazna nazwa dla tego wiersza.|
| `line` | list| Definicja wiersza. Jest to linia kierunkowa umożliwiająca zrozumienie "wejścia" i "wyjścia".|
| `start` | para wartości| Współrzędne x, y dla punktu początkowego linii. Wartości zmiennoprzecinkowa reprezentują położenie wierzchołka względem lewego górnego rogu. Aby obliczyć wartości bezwzględne x, y, należy pomnożyć te wartości z rozmiarem ramki. |
| `end` | para wartości| Współrzędne x, y dla punktu końcowego linii. Wartości zmiennoprzecinkowa reprezentują położenie wierzchołka względem lewego górnego rogu. Aby obliczyć wartości bezwzględne x, y, należy pomnożyć te wartości z rozmiarem ramki. |
| `threshold` | float| Zdarzenia są wychodzące, gdy osoba jest większa niż ta liczba pikseli w strefie. Wartość domyślna to 16. Jest to zalecana wartość w celu osiągnięcia maksymalnej dokładności. |
| `type` | ciąg| W **przypadku cognitiveservices.vision.spatialanalysis-personcrossingline** powinno to być `linecrossing` .|
|`trigger`|ciąg|Typ wyzwalacza do wysyłania zdarzenia.<br>Obsługiwane wartości: "event": są obsługiwane, gdy ktoś przekroczy linię.|
| `focus` | ciąg| Lokalizacja punktu w polu granicy osoby używana do obliczania zdarzeń. Wartość fokusu może być (ślad osoby), (dolny środek pola granicy `footprint` `bottom_center` osoby) (środek pola `center` granicy osoby). Wartość domyślna to zużycie pamięci.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Konfiguracja strefy dla cognitiveservices.vision.spatialanalysis-personcrossingpolygon

Jest to przykład danych wejściowych JSON dla SPACEANALYTICS_CONFIG, który konfiguruje strefę. Dla tej operacji można skonfigurować wiele stref.

 ```json
{
"zones":[
   {
       "name": "queuecamera",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonecrossing",
           "config":{
               "trigger": "event",
               "threshold": 48.00,
               "focus": "footprint"
               }
           }]
   },
   {
       "name": "queuecamera1",
       "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
       "events":[{
           "type": "zonedwelltime",
           "config":{
               "trigger": "event",
               "threshold": 16.00,
               "focus": "footprint"
               }
           }]
   }]
}
```

| Nazwa | Typ| Opis|
|---------|---------|---------|
| `zones` | list| Lista stref. |
| `name` | ciąg| Przyjazna nazwa dla tej strefy.|
| `polygon` | list| Każda para wartości reprezentuje x,y dla wierzchołków wielokąta. Wielokąt reprezentuje obszary, w których są śledzone lub zliczane osoby. Wartości zmiennoprzecinkowa reprezentują położenie wierzchołka względem lewego górnego rogu. Aby obliczyć wartości bezwzględne x, y, należy pomnożyć te wartości przez rozmiar ramki. 
| `threshold` | float| Zdarzenia są wychodzące, gdy osoba jest większa niż ta liczba pikseli w strefie. Wartość domyślna to 48, gdy typem jest zonecrossing, i 16, gdy czas to DwellTime. Są to zalecane wartości w celu osiągnięcia maksymalnej dokładności.  |
| `type` | ciąg| W **przypadku cognitiveservices.vision.spatialanalysis-personcrossingpolygon** powinien to być `zonecrossing` lub `zonedwelltime` .|
| `trigger`|ciąg|Typ wyzwalacza do wysyłania zdarzenia<br>Obsługiwane wartości: "event": są obsługiwane, gdy ktoś wchodzi do strefy lub kończy ją.|
| `focus` | ciąg| Lokalizacja punktu w polu granicy osoby używana do obliczania zdarzeń. Wartością fokusu może być (ślad osoby), (dolny środek pola ograniczonego `footprint` `bottom_center` osoby) (środek pola `center` ograniczonego osoby). Wartość domyślna to zużycie pamięci.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Konfiguracja strefy dla cognitiveservices.vision.spatialanalysis-persondistance

Jest to przykład danych wejściowych JSON dla parametru SPACEANALYTICS_CONFIG, który konfiguruje strefę dla **parametru cognitiveservices.vision.spatialanalysis-persondistance.** Dla tej operacji można skonfigurować wiele stref.

```json
{
"zones":[{
   "name": "lobbycamera",
   "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
   "events":[{
       "type": "persondistance",
       "config":{
           "trigger": "event",
           "output_frequency":1,
           "minimum_distance_threshold":6.0,
           "maximum_distance_threshold":35.0,
        "aggregation_method": "average"
           "threshold": 16.00,
           "focus": "footprint"
                   }
          }]
   }]
}
```

| Nazwa | Typ| Opis|
|---------|---------|---------|
| `zones` | list| Lista stref. |
| `name` | ciąg| Przyjazna nazwa dla tej strefy.|
| `polygon` | list| Każda para wartości reprezentuje x,y dla wierzchołków wielokąta. Wielokąt reprezentuje obszary, w których są liczone osoby, oraz odległość między osobami jest mierzona. Wartości zmiennoprzecinkowa reprezentują położenie wierzchołka względem lewego górnego rogu. Aby obliczyć wartości bezwzględne x, y, należy pomnożyć te wartości przez rozmiar ramki. 
| `threshold` | float| Zdarzenia są wychodzące, gdy osoba jest większa niż ta liczba pikseli w strefie. |
| `type` | ciąg| W **przypadku cognitiveservices.vision.spatialanalysis-persondistance** powinno to być `people_distance` .|
| `trigger` | ciąg| Typ wyzwalacza do wysyłania zdarzenia. Obsługiwane wartości są do wysyłania zdarzeń, gdy liczba zmienia się lub wysyłanie zdarzeń okresowo, niezależnie od `event` `interval` tego, czy liczba została zmieniona, czy nie.
| `output_frequency` | int | Szybkość, z jaką zdarzenia są wychodzące. Gdy `output_frequency` = X, każde zdarzenie X jest wychodzące, np. `output_frequency` = 2 oznacza, że każde inne zdarzenie jest wyjściem. Ma `output_frequency` zastosowanie zarówno do , jak i `event` `interval` .|
| `minimum_distance_threshold` | float| Odległość w stopach, która spowoduje wyzwolenie zdarzenia "TooClose", gdy odległość osób jest mniejsza niż ta.|
| `maximum_distance_threshold` | float| Odległość w stopach, która wyzwoli zdarzenie "TooFar", gdy ludzie będą od siebie od siebie odseć.|
| `aggregation_method` | ciąg| Metoda zagregowanego wyniku persondistance. Ten aggregation_method ma zastosowanie zarówno do , `mode` jak i `average` .|
| `focus` | ciąg| Lokalizacja punktu w polu granicy osoby używana do obliczania zdarzeń. Wartością fokusu może być (ślad osoby), (dolny środek pola ograniczonego `footprint` `bottom_center` osoby) (środek pola `center` ograniczonego osoby).|

### <a name="configuration-for-cognitiveservicesvisionspatialanalysis"></a>Konfiguracja cognitiveservices.vision.spatialanalysis
Jest to przykład danych wejściowych JSON dla parametru SPACEANALYTICS_CONFIG, który konfiguruje linię i strefę dla **cognitiveservices.vision.spatialanalysis.** Dla tej operacji można skonfigurować wiele wierszy/stref, a każdy wiersz/strefa może mieć różne zdarzenia.

 ```
{
  "lines": [
    {
      "name": "doorcamera",
      "line": {
        "start": {
          "x": 0,
          "y": 0.5
        },
        "end": {
          "x": 1,
          "y": 0.5
        }
      },
      "events": [
        {
          "type": "linecrossing",
          "config": {
            "trigger": "event",
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ],
  "zones": [
    {
      "name": "lobbycamera",
      "polygon": [[0.3, 0.3],[0.3, 0.9],[0.6, 0.9],[0.6, 0.3],[0.3, 0.3]],
      "events": [
        {
          "type": "persondistance",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "minimum_distance_threshold": 6.0,
            "maximum_distance_threshold": 35.0,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "count",
          "config": {
            "trigger": "event",
            "output_frequency": 1,
            "threshold": 16.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonecrossing",
          "config": {
            "threshold": 48.00,
            "focus": "footprint"
          }
        },
        {
          "type": "zonedwelltime",
          "config": {
            "threshold": 16.00,
            "focus": "footprint"
          }
        }
      ]
    }
  ]
}
```
## <a name="camera-configuration"></a>Konfiguracja aparatu fotograficznego

Zapoznaj się z [wytycznymi dotyczącymi umieszczania](spatial-analysis-camera-placement.md) aparatu, aby dowiedzieć się więcej na temat konfigurowania stref i linii.

## <a name="spatial-analysis-operation-output"></a>Dane wyjściowe operacji analizy przestrzennej

Zdarzenia z każdej operacji są wychodzące do Azure IoT Hub w formacie JSON.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>Format JSON dla danych cognitiveservices.vision.spatialanalysis-personcount AI Insights

Przykładowy kod JSON dla danych wyjściowych zdarzenia dla tej operacji.

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
            "status": "Calibrated",
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
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
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
            "metadata":{
            "attributes": {
            "face_nomask": 0.99
            }
            }
       }
    ],
    "schemaVersion": "1.0"
}
```

| Nazwa pola zdarzenia | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator zdarzenia|
| `type` | ciąg| Typ zdarzenia|
| `detectionsId` | array| Tablica rozmiaru 1 unikatowego identyfikatora osoby, która wyzwoliła to zdarzenie|
| `properties` |  — kolekcja| Kolekcja wartości|
| `trackinId` | ciąg| Unikatowy identyfikator wykrytej osoby|
| `zone` | ciąg | Pole "name" wielokąta reprezentujące strefę, która została skrzyżowana|
| `trigger` | ciąg| Typ wyzwalacza to "zdarzenie" lub "interwał" w zależności od wartości w `trigger` SPACEANALYTICS_CONFIG|

| Nazwa pola wykrywania | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator wykrywania|
| `type` | ciąg| Typ wykrywania|
| `region` |  — kolekcja| Kolekcja wartości|
| `type` | ciąg| Typ regionu|
| `points` |  — kolekcja| Lewy górny i prawy dolny punkt, gdy typ regionu to RECTANGLE |
| `confidence` | float| Zaufanie algorytmu|
| `face_mask` | float | Wartość ufności atrybutu z zakresem (0–1) wskazuje, że wykryta osoba nosi maskę twarzy |
| `face_nomask` | float | Wartość ufności atrybutu z zakresem (0–1) wskazuje, że wykryta osoba nie ma na sobie maski twarzy  |

| Nazwa pola SourceInfo | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator aparatu|
| `timestamp` | data| Data utc emisji ładunku JSON|
| `width` | int | Szerokość ramki wideo|
| `height` | int | Wysokość ramki wideo|
| `frameId` | int | Identyfikator ramki|
| `cameraCallibrationInfo` |  — kolekcja | Kolekcja wartości|
| `status` | ciąg | Stan iniekcyjną w formacie `state[;progress description]` . Może to być `Calibrating` stan , `Recalibrating` (jeśli włączono recylizację) lub `Calibrated` . Część opisu postępu jest prawidłowa tylko wtedy, gdy jest w stanie i , która służy do pokazywania postępu `Calibrating` `Recalibrating` bieżącego procesu inkasowania.|
| `cameraHeight` | float | Wysokość aparatu nad ziemią w stopach. Jest to wywnioskowane z automatycznego natłoku. |
| `focalLength` | float | Długość odsłoń aparatu w pikselach. Jest to wywnioskowane z automatycznego natłoku. |
| `tiltUpAngle` | float | Kąt nachylenia kamery od pionowej. Jest to wywnioskowane z automatycznego natłoku.|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>Format JSON dla cognitiveservices.vision.spatialanalysis-personcrossingline AI Insights

Przykładowy kod JSON do wykrywania danych wyjściowych przez tę operację.

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
            "metadata": {
            "attributes": {
                "face_mask": 0.99
            }
        }
        }
    ],
    "schemaVersion": "1.0"
}
```
| Nazwa pola zdarzenia | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator zdarzenia|
| `type` | ciąg| Typ zdarzenia|
| `detectionsId` | array| Tablica rozmiaru 1 unikatowego identyfikatora osoby, która wyzwoliła to zdarzenie|
| `properties` |  — kolekcja| Kolekcja wartości|
| `trackinId` | ciąg| Unikatowy identyfikator wykrytej osoby|
| `status` | ciąg| Kierunek przekroczenia linii: "CrossLeft" lub "CrossRight". Kierunek opiera się na wyobrażenie sobie pozycji na "początku" z "końcem" linii. CrossRight jest przekroczenie od lewej do prawej. CrossLeft jest przekroczenie od prawej do lewej.|
| `zone` | ciąg | Pole "name" w wierszu, który został skrzyżowany|

| Nazwa pola wykrywania | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator wykrywania|
| `type` | ciąg| Typ wykrywania|
| `region` |  — kolekcja| Kolekcja wartości|
| `type` | ciąg| Typ regionu|
| `points` |  — kolekcja| Lewy górny i prawy dolny punkt, gdy typ regionu to RECTANGLE |
| `confidence` | float| Zaufanie algorytmu|
| `face_mask` | float | Wartość ufności atrybutu z zakresem (0–1) wskazuje, że wykryta osoba nosi maskę twarzy |
| `face_nomask` | float | Wartość ufności atrybutu z zakresem (0–1) wskazuje, że wykryta osoba nie nosi maski twarzy  |

| Nazwa pola SourceInfo | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator aparatu fotograficznego|
| `timestamp` | data| Data utc emisji ładunku JSON|
| `width` | int | Szerokość ramki wideo|
| `height` | int | Wysokość ramki wideo|
| `frameId` | int | Identyfikator ramki|


> [!IMPORTANT]
> Model AI wykrywa osobę niezależnie od tego, czy jest skierowana do kamery, czy z dala od aparatu. Model AI nie obsługuje rozpoznawania twarzy i nie emituje żadnych informacji biometrycznych. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>Format JSON dla cognitiveservices.vision.spatialanalysis-personcrossingpolygon AI Insights

Przykładowy kod JSON do wykrywania danych wyjściowych przez tę operację z `zonecrossing` typem SPACEANALYTICS_CONFIG.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEnterExitEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": "1"
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
        "metadata": {
        "attributes": {
        "face_mask": 0.99
        }
        }
           
        }
    ],
    "schemaVersion": "1.0"
}
```

Przykładowy kod JSON do wykrywania danych wyjściowych przez tę operację z `zonedwelltime` typem SPACEANALYTICS_CONFIG.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneDwellTimeEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Exit",
                "side": "1",
              "durationMs": 7132.0
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
| `type` | ciąg| Typ zdarzenia. Wartość może być _personZoneDwellTimeEvent_ lub _personZoneEnterExitEvent_|
| `detectionsId` | array| Tablica rozmiaru 1 unikatowego identyfikatora wykrywania osoby, która wyzwoliła to zdarzenie|
| `properties` |  — kolekcja| Kolekcja wartości|
| `trackinId` | ciąg| Unikatowy identyfikator wykrytej osoby|
| `status` | ciąg| Kierunek wielokątów, "Enter" lub "Exit"|
| `side` | int| Liczba strony wielokąta, która została skrzyżowana przez osobę. Każda strona to po numerowana krawędź między dwoma wierzchołkami wielokąta, które reprezentują twoją strefę. Krawędź między dwoma pierwszymi wierzchołkami wielokąta reprezentuje pierwszą stronę. Pole "Side" jest puste, gdy zdarzenie nie jest skojarzone z określoną stroną z powodu oklulacji. Na przykład wystąpiło wyjście, gdy osoba zniknęła, ale nie widziała przejścia przez stronę strefy lub wejdę, gdy osoba pojawiła się w strefie, ale nie widziała przejścia przez stronę.|
| `durationMs` | float | Liczba milisekund reprezentująca czas spędzony przez osobę w strefie. To pole jest dostępne, gdy typ zdarzenia to _personZoneDwellTimeEvent_|
| `zone` | ciąg | Pole "name" wielokąta reprezentujące strefę, która została skrzyżowana|

| Nazwa pola wykrywania | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator wykrywania|
| `type` | ciąg| Typ wykrywania|
| `region` |  — kolekcja| Kolekcja wartości|
| `type` | ciąg| Typ regionu|
| `points` |  — kolekcja| Lewy górny i prawy dolny punkt, gdy typ regionu to RECTANGLE |
| `confidence` | float| Zaufanie algorytmu|
| `face_mask` | float | Wartość ufności atrybutu z zakresem (0–1) wskazuje, że wykryta osoba nosi maskę twarzy |
| `face_nomask` | float | Wartość ufności atrybutu z zakresem (0–1) wskazuje, że wykryta osoba nie nosi maski twarzy  |

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>Format JSON dla funkcji cognitiveservices.vision.spatialanalysis-persondistance AI Insights

Przykładowy kod JSON do wykrywania danych wyjściowych przez tę operację.

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
            "status": "Calibrated",
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
| `detectionsId` | array| Tablica rozmiaru 1 unikatowego identyfikatora osoby, która wyzwoliła to zdarzenie|
| `properties` |  — kolekcja| Kolekcja wartości|
| `personCount` | int| Liczba osób wykrytych w momencie emisji zdarzenia|
| `averageDistance` | float| Średnia odległość między wszystkimi wykrytymi osobami w stopach|
| `minimumDistanceThreshold` | float| Odległość w stopach, która spowoduje wyzwolenie zdarzenia "TooClose", gdy odległość między osobami jest mniejsza.|
| `maximumDistanceThreshold` | float| Odległość w stopach, która spowoduje wyzwolenie zdarzenia "TooFar", gdy odległość osób jest większa niż odległość.|
| `eventName` | ciąg| Nazwa zdarzenia ma naruszone wartości , naruszenie lub nieuzupełnione `TooClose` `minimumDistanceThreshold` `TooFar` `maximumDistanceThreshold` `unknown` autouzupełnianie|
| `distanceViolationPersonCount` | int| Liczba osób wykrytych z naruszeniem `minimumDistanceThreshold` zasad lub `maximumDistanceThreshold`|
| `zone` | ciąg | Pole "name" wielokąta reprezentujące strefę, która była monitorowana pod kątem rozdyskutowania między osobami|
| `trigger` | ciąg| Typ wyzwalacza to "zdarzenie" lub "interwał" w zależności od wartości w `trigger` SPACEANALYTICS_CONFIG|

| Nazwa pola wykrywania | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator wykrywania|
| `type` | ciąg| Typ wykrywania|
| `region` |  — kolekcja| Kolekcja wartości|
| `type` | ciąg| Typ regionu|
| `points` |  — kolekcja| Lewy górny i prawy dolny punkt, gdy typ regionu to RECTANGLE |
| `confidence` | float| Zaufanie algorytmu|
| `centerGroundPoint` | 2 wartości zmiennoprzecinkowa| `x`, wartości o współrzędnych wywnioskować lokalizację osoby `y` na powierzchni w stopach. `x` i `y` są współrzędnymi na płaszczyźnie podłogi przy założeniu, że podłoga jest pozioma. Lokalizacja aparatu to źródło. |

Podczas obliczania jest to odległość od kamery do osoby `centerGroundPoint` `x` wzdłuż linii nachylić do płaszczyzny obrazu aparatu. `y` to odległość od kamery do osoby wzdłuż linii równoległej do płaszczyzny obrazu aparatu. 

![Przykładowy punkt środkowy parteru](./media/spatial-analysis/x-y-chart.png) 

W tym przykładzie `centerGroundPoint` jest to `{x: 4, y: 5}` . Oznacza to, że osoba znajduje się 4 metry od kamery i 5 metrów po prawej stronie, patrząc na pomieszczenie od góry do dołu.


| Nazwa pola SourceInfo | Typ| Opis|
|---------|---------|---------|
| `id` | ciąg| Identyfikator aparatu fotograficznego|
| `timestamp` | data| Data utc emisji ładunku JSON|
| `width` | int | Szerokość ramki wideo|
| `height` | int | Wysokość ramki wideo|
| `frameId` | int | Identyfikator ramki|
| `cameraCallibrationInfo` |  — kolekcja | Kolekcja wartości|
| `status` | ciąg | Stan drukarki w formacie `state[;progress description]` . Może to być `Calibrating` stan , `Recalibrating` (jeśli włączono recalibrację) lub `Calibrated` . Opis postępu jest prawidłowy tylko wtedy, gdy jest w stanie i , który jest używany do pokazania `Calibrating` `Recalibrating` postępu bieżącego procesu procesów.|
| `cameraHeight` | float | Wysokość aparatu nad ziemią w stopach. Wynika to z automatycznego ustawienia. |
| `focalLength` | float | Długość centralny aparatu w pikselach. Wynika to z automatycznego ustawienia. |
| `tiltUpAngle` | float | Kąt nachylenia kamery od pionowej. Wynika to z automatycznego ustawienia.|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-ai-insights"></a>Format JSON dla cognitiveservices.vision.spatialanalysis AI Insights

Dane wyjściowe tej operacji zależą od skonfigurowanego zdarzenia , na przykład jeśli dla tej operacji skonfigurowano zdarzenie , dane wyjściowe `events` będą takie same jak `zonecrossing` `cognitiveservices.vision.spatialanalysis-personcrossingpolygon` .

## <a name="use-the-output-generated-by-the-container"></a>Używanie danych wyjściowych wygenerowanych przez kontener

Możesz zintegrować wykrywanie analizy przestrzennej lub zdarzenia z aplikacją. Oto kilka podejść, które należy wziąć pod uwagę: 

* Użyj zestawu SDK usługi Azure Event Hub dla wybranego języka programowania, aby nawiązać połączenie z Azure IoT Hub końcowym i odebrać zdarzenia. Aby [uzyskać więcej informacji,](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) zobacz Odczytywanie komunikatów z urządzenia do chmury z wbudowanego punktu końcowego. 
* Skonfiguruj routing **komunikatów** na Azure IoT Hub, aby wysyłać zdarzenia do innych punktów końcowych lub zapisywać zdarzenia w magazynie danych. Zobacz [IoT Hub Routing komunikatów,](../../iot-hub/iot-hub-devguide-messages-d2c.md) aby uzyskać więcej informacji. 
* Skonfiguruj zadanie Azure Stream Analytics przetwarzania zdarzeń w czasie rzeczywistym podczas ich dotarć i tworzenia wizualizacji. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Wdrażanie operacji analizy przestrzennej na dużą skalę (wiele aparatów)

Aby uzyskać najlepszą wydajność i wykorzystanie procesorów GPU, można wdrożyć dowolne operacje analizy przestrzennej na wielu aparatach przy użyciu wystąpień grafu. Poniżej znajduje się przykład uruchamiania operacji na `cognitiveservices.vision.spatialanalysis-personcrossingline` 15 aparatach.

```json
  "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
        "personzonelinecrossing": {
        "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingline",
        "version": 1,
        "enabled": true,
        "sharedNodes": {
            "shared_detector0": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 7, \"do_calibration\": true}",
                }
            },
            "shared_detector1": {
                "node": "PersonCrossingLineGraph.detector",
                "parameters": {
                    "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 8, \"do_calibration\": true}",
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
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                    "VIDEO_SOURCE_ID": "camera 1",
                    "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]]}]}"
                }
            },
            "2": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                    "VIDEO_SOURCE_ID": "camera 2",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "3": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                    "VIDEO_SOURCE_ID": "camera 3",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "4": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                    "VIDEO_SOURCE_ID": "camera 4",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "5": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                    "VIDEO_SOURCE_ID": "camera 5",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "6": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 6>",
                    "VIDEO_SOURCE_ID": "camera 6",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "7": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector0",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 7>",
                    "VIDEO_SOURCE_ID": "camera 7",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "8": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 8>",
                    "VIDEO_SOURCE_ID": "camera 8",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "9": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 9>",
                    "VIDEO_SOURCE_ID": "camera 9",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "10": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 10>",
                    "VIDEO_SOURCE_ID": "camera 10",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "11": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 11>",
                    "VIDEO_SOURCE_ID": "camera 11",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "12": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 12>",
                    "VIDEO_SOURCE_ID": "camera 12",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "13": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 13>",
                    "VIDEO_SOURCE_ID": "camera 13",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "14": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 14>",
                    "VIDEO_SOURCE_ID": "camera 14",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            },
            "15": {
                "sharedNodeMap": {
                    "PersonCrossingLineGraph/detector": "shared_detector1",
                },
                "parameters": {
                    "VIDEO_URL": "<Replace RTSP URL for camera 15>",
                    "VIDEO_SOURCE_ID": "camera 15",
                    "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                }
            }
          }
        },
      }
  }
  ```
| Nazwa | Typ| Opis|
|---------|---------|---------|
| `batch_size` | int | Jeśli wszystkie kamery mają taką samą rozdzielczość, ustaw na liczbę aparatów, które będą używane w tej operacji. W przeciwnym razie ustaw wartość 1 lub pozostaw wartość `batch_size` domyślną (1), co oznacza, że żadna partia nie jest `batch_size` obsługiwana. |

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie aplikacji internetowej zliczanych przez osoby](spatial-analysis-web-app.md)
* [Rejestrowanie i rozwiązywanie problemów](spatial-analysis-logging.md)
* [Przewodnik po umieszczaniu kamery](spatial-analysis-camera-placement.md)
* [Przewodnik umieszczania strefy i linii](spatial-analysis-zone-line-placement.md)
