---
title: Uaktualnianie analizy filmów wideo na żywo na IoT Edge od 1,0 do 2,0
description: W tym artykule omówiono różnice i różne zagadnienia, które należy wziąć pod uwagę podczas uaktualniania usługi Live Video Analytics (LVA) w module Azure IoT Edge.
author: naiteeks
ms.topic: how-to
ms.author: naiteeks
ms.date: 12/14/2020
ms.openlocfilehash: 49c17946203bc6c3655b1aaf7b04a1ee3ea67388
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955653"
---
# <a name="upgrading-live-video-analytics-on-iot-edge-from-10-to-20"></a>Uaktualnianie analizy filmów wideo na żywo na IoT Edge od 1,0 do 2,0

W tym artykule omówiono różnice i różne zagadnienia, które należy wziąć pod uwagę podczas uaktualniania usługi Live Video Analytics (LVA) w module Azure IoT Edge.

## <a name="change-list"></a>Zmień listę

> [!div class="mx-tdCol4BreakAll"]
> |Tytuł|Analiza wideo na żywo 1,0|Analiza wideo na żywo 2,0|Opis|
> |-------------|----------|---------|---------|
> |Obraz kontenera|mcr.microsoft.com/media/live-video-analytics:1|mcr.microsoft.com/media/live-video-analytics:2|Obrazy platformy Docker opublikowane przez firmę Microsoft na potrzeby analizy filmów wideo na żywo na Azure IoT Edge|
> |**MediaGraph węzły** |    |   |   |
> |Źródła|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Źródło RTSP </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub źródło komunikatu |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Źródło RTSP </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub źródło komunikatu | MediaGraph węzły, które działają jako źródła pozyskiwania multimediów i komunikatów.|
> |Procesory|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor wykrywania ruchu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor filtru szybkości klatek </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor rozszerzeń http </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor rozszerzenia GRPC </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor bramy sygnałów |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor wykrywania ruchu </br>:::image type="icon" source="./././media/upgrading-lva/remove.png":::**Procesor filtru szybkości klatek**</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor rozszerzeń http </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor rozszerzenia GRPC </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Procesor bramy sygnałów | MediaGraph węzły, które umożliwiają formatowanie nośnika przed wysłaniem do serwerów wnioskowania AI.|
> |Ujścia|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Ujścia zasobów </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Ujścia plików </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub ujścia komunikatów|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Ujścia zasobów </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Ujścia plików </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: IoT Hub ujścia komunikatów| MediaGraph węzły, które umożliwiają przechowywanie przetworzonych nośników.|
> |**Obsługiwane MediaGraphs** |    |   |   |
> |Topologie|:::image type="icon" source="./././media/upgrading-lva/check.png"::: Ciągłe nagrywanie wideo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analiza ruchu i ciągłe nagrywanie wideo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analiza zewnętrzna i ciągłe nagrywanie wideo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Rejestrowanie na podstawie zdarzeń na ruch </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Rejestrowanie oparte na zdarzeniach na AI</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Rejestrowanie oparte na zdarzeniach na zewnętrznym zdarzeniu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analiza ruchu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analiza ruchu, a następnie inferencing AI |:::image type="icon" source="./././media/upgrading-lva/check.png"::: Ciągłe nagrywanie wideo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analiza ruchu i ciągłe nagrywanie wideo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analiza zewnętrzna i ciągłe nagrywanie wideo </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Rejestrowanie na podstawie zdarzeń na ruch </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Rejestrowanie oparte na zdarzeniach na AI</br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Rejestrowanie oparte na zdarzeniach na zewnętrznym zdarzeniu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analiza ruchu </br>:::image type="icon" source="./././media/upgrading-lva/check.png"::: Analiza ruchu, a następnie inferencing AI </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**Kompozycja AI** </br>:::image type="icon" source="./././media/upgrading-lva/check.png":::**Nagrywanie dźwięku i wideo** </br>  | Topologie MediaGraph, które umożliwiają definiowanie strategii wykresu, z parametrami jako symbolami zastępczymi wartości.|

## <a name="upgrading-the-live-video-analytics-on-iot-edge-module-from-10-to-20"></a>Uaktualnianie analizy filmów wideo na żywo w module IoT Edge z 1,0 do 2,0
Po uaktualnieniu usługi Analiza filmów wideo na żywo w module IoT Edge upewnij się, że zostały zaktualizowane poniższe informacje.
### <a name="container-image"></a>Obraz kontenera
W szablonie wdrożenia poszukaj na żywo analizy filmów wideo na IoT Edge module pod `modules` węzłem i zaktualizuj `image` pole jako:
```
"image": "mcr.microsoft.com/media/live-video-analytics:2"
```
> [!TIP]
> Jeśli nie zmodyfikowano nazwy usługi Live Video Analytics w module IoT Edge, poszukaj w `lvaEdge` węźle module.

### <a name="topology-file-changes"></a>Zmiany pliku topologii
Upewnij się, że w plikach topologii **`apiVersion`** jest ustawiona wartość 2,0

### <a name="mediagraph-node-changes"></a>Zmiany w węźle MediaGraph


* Można teraz przekazać dźwięk ze źródła aparatu przy użyciu wideo. Można przekazać **tylko dźwięk** lub **tylko wideo** lub **audio i wideo** , korzystając z pomocy programu **`outputSelectors`** do dowolnego węzła grafu. Na przykład, jeśli chcesz wybrać wideo tylko ze źródła RTSP i przekazać je w dół, Dodaj następujące polecenie do węzła źródłowego RTSP:
    ```
    "outputSelectors": [
      {
        "property": "mediaType",
        "operator": "is",
        "value": "video"
      }
    ```
>[!NOTE]
>**`outputSelectors`** Jest to właściwość opcjonalna. Jeśli ta wartość nie jest używana, wykres multimedialny przekaże dźwięk (jeśli jest włączony) i wideo z aparatu RTSP. 

* W `MediaGraphHttpExtension` `MediaGraphGrpcExtension` przypadku procesorów i należy pamiętać o następujących zmianach:  
    #### <a name="image-properties"></a>Właściwości obrazu
    * `MediaGraphImageFormatEncoded` nie jest już obsługiwane. 
      * Zamiast tego należy **`MediaGraphImageFormatBmp`** użyć **`MediaGraphImageFormatJpeg`** lub **`MediaGraphImageFormatPng`** . Na przykład
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatJpeg"
                }
            }
        ```
        * Jeśli chcesz używać obrazów RAW, użyj **`MediaGraphImageFormatRaw`** . Aby użyć tego programu, zaktualizuj węzeł obrazu jako:
        ```
        "image": {
                "scale": 
                {
                    "mode": "preserveAspectRatio",
                    "width": "416",
                    "height": "416"
                },
                "format": 
                {
                    "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
                    "pixelFormat": "rgba"
                }
            }
        ```
        >[!NOTE]
        > Możliwe wartości pixelFormat obejmują: `yuv420p` , `rgb565be` ,, `rgb565le` `rgb555be` , `rgb555le` ,, `rgb24` `bgr24` `argb` `rgba` `abgr` ,,,, `bgra`  

    #### <a name="extensionconfiguration-for-grpc-extension-processor"></a>extensionConfiguration dla procesora rozszerzenia GRPC  
    * W `MediaGraphGrpcExtension` procesorze jest dostępna nowa właściwość o nazwie **`extensionConfiguration`** , która jest opcjonalnym ciągiem, który może być używany jako część kontraktu gRPC. To pole może służyć do przekazywania wszelkich danych do serwera wnioskowania i można zdefiniować, w jaki sposób serwer wnioskowania używa tych danych.  
    Jeden przypadek użycia tej właściwości polega na tym, że wiele modeli AI jest spakowanych na pojedynczym serwerze wnioskowania. W przypadku tej właściwości nie trzeba ujawniać węzła dla każdego modelu AI. Zamiast tego w przypadku wystąpienia grafu jako dostawcy rozszerzeń można zdefiniować, jak wybrać różne modele AI przy użyciu **`extensionConfiguration`** właściwości i podczas wykonywania, LVA przekaże ten ciąg do serwera inferencing, który może użyć tego do wywołania żądanego modelu AI.  

    #### <a name="ai-composition"></a>Kompozycja AI
    * Usługa wideo Analytics na żywo 2,0 obsługuje teraz użycie więcej niż jednego procesora rozszerzenia grafu multimediów w ramach topologii. Ramki multimediów można przekazać z aparatu RTSP do różnych modeli AI sekwencyjnie, równolegle lub w połączeniu obu. Zapoznaj się z przykładową topologią pokazującą dwa modele AI używane sekwencyjnie.

### <a name="disk-space-management-with-sink-nodes"></a>Zarządzanie miejscem na dysku z węzłami ujścia
* W węźle **ujścia plików** można teraz określić, jak dużo miejsca na żywo analiza filmów wideo w module IoT Edge może być używana do przechowywania przetworzonych obrazów. Aby to zrobić, Dodaj **`maximumSizeMiB`** pole do węzła FileSink. Przykładowy węzeł ujścia pliku jest następujący:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphFileSink",
        "name": "fileSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "fileNamePattern": "sampleFiles-${System.DateTime}",
        "maximumSizeMiB":"512",
        "baseDirectoryPath":"/var/media"
      }
    ]
    ```
* W węźle **ujścia zasobów** można określić, ile miejsca na dysku można użyć do przechowywania przetworzonych obrazów na żywo w module IoT Edge modułu. Aby to zrobić, Dodaj **`localMediaCacheMaximumSizeMiB`** pole do węzła ujścia zasobów. Przykładowy węzeł ujścia zasobów jest następujący:
    ```
    "sinks": [
      {
        "@type": "#Microsoft.Media.MediaGraphAssetSink",
        "name": "AssetSink",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value": "video"
              }
            ]
          }
        ],
        "assetNamePattern": "sampleAsset-${System.GraphInstanceName}",
        "segmentLength": "PT30S",
        "localMediaCacheMaximumSizeMiB":"200",
        "localMediaCachePath":"/var/lib/azuremediaservices/tmp/"
      }
    ]
    ```
    >[!NOTE]
    >  Ścieżka **ujścia plików** jest dzielona na ścieżkę katalogu podstawowego i wzorzec nazwy pliku, natomiast ścieżka **ujścia zasobów** zawiera ścieżkę katalogu podstawowego.  

### <a name="frame-rate-management"></a>Zarządzanie szybkością klatek
* **`MediaGraphFrameRateFilterProcessor`** jest przestarzałe w **usłudze Live Video Analytics w module IoT Edge 2,0** .
    * Aby próbkować przychodzące wideo w celu przetworzenia, Dodaj **`samplingOptions`** Właściwość do procesorów rozszerzeń MediaGraph ( `MediaGraphHttpExtension` lub `MediaGraphGrpcExtension` )  
     ```
          "samplingOptions": 
          {
            "skipSamplesWithoutAnnotation": "false",  // If true, limits the samples submitted to the extension to only samples which have associated inference(s) 
            "maximumSamplesPerSecond": "20"   // Maximum rate of samples submitted to the extension
          },
    ```
### <a name="module-metrics-in-the-prometheus-format-using-telegraf"></a>Metryki modułu w formacie Prometheus za pomocą telegraf
W tej wersji telegraf może służyć do wysyłania metryk do Azure Monitor. Z tego powodu metryki mogą być kierowane do Log Analytics lub centrum zdarzeń.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/telegraf.png" alt-text="Taksonomia zdarzeń":::

Można utworzyć obraz telegraf z konfiguracją niestandardową z łatwością przy użyciu platformy Docker. Dowiedz się więcej na stronie [monitorowanie i rejestrowanie](monitoring-logging.md#azure-monitor-collection-via-telegraf) .

## <a name="next-steps"></a>Następne kroki

[Rozpocznij pracę z funkcją analizy filmów wideo na żywo na IoT Edge](get-started-detect-motion-emit-events-quickstart.md)