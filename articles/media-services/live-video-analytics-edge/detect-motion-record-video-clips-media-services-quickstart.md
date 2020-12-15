---
title: Wykrywaj ruch, nagrywaj wideo do Azure Media Services
description: W tym przewodniku szybki start pokazano, jak używać analizy wideo na żywo na IoT Edge w celu wykrywania ruchu w strumieniu wideo na żywo i rejestrowania klipów wideo do Azure Media Services.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 8872c9aefa0ed748cbed93d0f7376586859be9df
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511896"
---
# <a name="quickstart-detect-motion-record-video-to-media-services"></a>Szybki Start: wykrywanie ruchu, nagrywanie wideo do Media Services

W tym artykule przedstawiono kroki umożliwiające korzystanie z usługi Analiza filmów wideo na żywo w IoT Edge na potrzeby [rejestrowania opartego na zdarzeniach](event-based-video-recording-concept.md). Używa maszyn wirtualnych z systemem Linux na platformie Azure jako urządzenie IoT Edge i symulowany strumień wideo na żywo. Ten strumień wideo jest analizowany pod kątem obecności obiektów do przeniesienia. Po wykryciu ruchu są wysyłane zdarzenia do usługi Azure IoT Hub, a odpowiednia część strumienia wideo jest rejestrowana jako zasób w Azure Media Services.

Ten artykuł jest oparty na [wprowadzenie szybki start](get-started-detect-motion-emit-events-quickstart.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) na swojej maszynie przy użyciu [rozszerzenia narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Jeśli wcześniej nie ukończono [wprowadzenie szybkiego startu](get-started-detect-motion-emit-events-quickstart.md) , wykonaj następujące czynności:
    * [Konfigurowanie zasobów platformy Azure](get-started-detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [Wdrażanie modułów](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)
    * [Konfigurowanie programu Visual Studio Code](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension)

## <a name="review-the-sample-video"></a>Zapoznaj się z przykładowym wideo

Zgodnie z powyższymi krokami w celu skonfigurowania zasobów platformy Azure, (krótkie) wideo z partii parkingowej zostanie skopiowane na maszynę wirtualną z systemem Linux na platformie Azure używaną jako urządzenie IoT Edge. Ten plik wideo będzie używany do symulowania strumienia na żywo dla tego samouczka.

Możesz użyć aplikacji, takiej jak [VLC Player](https://www.videolan.org/vlc/), uruchomić ją, nacisnąć `Ctrl+N` i wkleić link [przykładowego wideo z przeparką partii](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) , aby rozpocząć odtwarzanie. W przypadku 5-sekundowego znaku biały samochód porusza się w ramach parkingów.

Po wykonaniu poniższych kroków zostanie użyta usługa analizy wideo na żywo na IoT Edge, aby wykryć ten ruch samochodu i nagrać klip wideo, zaczynając od 5-sekundowego znacznika. Poniższy diagram przedstawia wizualną reprezentację ogólnego przepływu.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/topology.svg" alt-text="Nagrywanie filmów wideo na podstawie zdarzeń do zasobów na podstawie zdarzeń ruchu":::

## <a name="use-direct-method-calls"></a>Używanie wywołań metod bezpośrednich

Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie metod bezpośrednich. Przeczytaj [bezpośrednie metody analizy wideo na żywo na IoT Edge](direct-methods.md) , aby zrozumieć wszystkie metody bezpośrednie dostarczone przez moduł. 

1. W Visual Studio Code Otwórz kartę **rozszerzenia** (lub naciśnij klawisze CTRL + SHIFT + X) i Wyszukaj pozycję Azure IoT Hub.
1. Kliknij prawym przyciskiem myszy i wybierz pozycję **Ustawienia rozszerzenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Ustawienia rozszerzenia":::
1. Wyszukaj i Włącz opcję "Pokaż pełny komunikat".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Pokaż pełny komunikat":::

### <a name="invoke-graphtopologylist"></a>Wywołaj GraphTopologyList
Ten krok wylicza wszystkie [topologie grafu](media-graph-concept.md#media-graph-topologies-and-instances) w module.

1. Kliknij prawym przyciskiem myszy moduł "lvaEdge" i wybierz polecenie "Wywołaj metodę bezpośrednią modułu" z menu kontekstowego.
1. W górnym rogu okna Visual Studio Code zobaczysz pole edycji. Wprowadź wartość "GraphTopologyList" w polu edycji i naciśnij klawisz ENTER.
1. Następnie skopiuj i wklej poniższy ładunek JSON w polu edycji i naciśnij klawisz ENTER.
    
```
{
    "@apiVersion" : "2.0"
}
```

W ciągu kilku sekund zobaczysz okno dane wyjściowe w oknie podręcznym Visual Studio Code z następującą odpowiedzią
    
```
[DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```
    
Należy oczekiwać powyższej odpowiedzi, ponieważ nie zostały utworzone żadne topologie grafu.

### <a name="invoke-graphtopologyset"></a>Wywołaj GraphTopologySet

Korzystając z tych samych kroków, które opisano w wywołaniu GraphTopologyList, można wywołać GraphTopologySet, aby ustawić [topologię wykresu](media-graph-concept.md#media-graph-topologies-and-instances) przy użyciu następującego kodu JSON jako ładunku. Zostanie utworzona topologia grafu o nazwie "EVRtoAssetsOnMotionDetection".

```
{
    "@apiVersion": "2.0",
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Assets based on motion events",
      "parameters": [
        {
            "name": "rtspUserName",
            "type": "String",
            "description": "rtsp source user name.",
            "default": "dummyUserName"
        },
        {
            "name": "rtspPassword",
            "type": "String",
            "description": "rtsp source password.",
            "default" : "dummyPassword"
        },
        {
            "name": "rtspUrl",
            "type": "String",
            "description": "rtsp Url"
        },
        {
            "name": "motionSensitivity",
            "type": "String",
            "description": "motion detection sensitivity",
            "default" : "medium"
        },
        {
            "name": "hubSinkOutputName",
            "type": "String",
            "description": "hub sink output name",
            "default" : "iothubsinkoutput"
        }                              
    ],         

      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "name": "motionDetection",
          "sensitivity": "${motionSensitivity}",
          "inputs": [
            {
              "nodeName": "rtspSource"
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection"
            },
            {
              "nodeName": "rtspSource"
            }
          ],
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S"
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "name": "assetSink",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "segmentLength": "PT0M30S",
          "localMediaCacheMaximumSizeMiB": "2048",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "inputs": [
            {
              "nodeName": "signalGateProcessor"
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "name": "hubSink",
          "hubOutputName": "${hubSinkOutputName}",
          "inputs": [
            {
              "nodeName": "motionDetection"
            }
          ]
        }
      ]
    }
}
```

Powyższy ładunek JSON powoduje utworzenie topologii wykresu, która definiuje pięć parametrów (cztery z wartości domyślnych). Topologia ma jeden węzeł źródłowy ([RTSP](media-graph-concept.md#rtsp-source)), dwa węzły procesora ([procesor wykrywania ruchu](media-graph-concept.md#motion-detection-processor) oraz [procesor bramy sygnałów](media-graph-concept.md#signal-gate-processor)i dwa węzły ujścia (IoT Hub ujścia i [ujścia zasobów](media-graph-concept.md#asset-sink)). Wizualna reprezentacja topologii jest pokazana powyżej.

W ciągu kilku sekund zostanie wyświetlona następująca odpowiedź w oknie danych wyjściowych.

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T22:05:31.603Z",
      "lastModifiedAt": "2020-05-12T22:05:31.603Z"
    },
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to assets based on motion events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        },
        {
          "name": "motionSensitivity",
          "type": "String",
          "description": "motion detection sensitivity",
          "default": "medium"
        },
        {
          "name": "hubSinkOutputName",
          "type": "String",
          "description": "hub sink output name",
          "default": "iothubsinkoutput"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "segmentLength": "PT0M30S",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "name": "assetSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Zwróconym stanem jest 201, co oznacza, że została utworzona nowa topologia grafu. Wypróbuj następujące metody bezpośrednie jako następne kroki:

* Wywołaj GraphTopologySet ponownie i sprawdź, czy zwrócony kod stanu to 200. Kod stanu 200 wskazuje, że istniejąca topologia grafu została pomyślnie zaktualizowana.
* Wywołaj GraphTopologySet ponownie, ale Zmień ciąg opisu. Sprawdź, czy kod stanu w odpowiedzi to 200, a opis zostanie zaktualizowany do nowej wartości.
* Wywołaj GraphTopologyList, jak opisano w poprzedniej sekcji, i sprawdź, czy teraz można zobaczyć topologię grafu "EVRtoAssetsOnMotionDetection" w zwracanym ładunku.

### <a name="invoke-graphtopologyget"></a>Wywołaj GraphTopologyGet

Teraz Wywołaj GraphTopologyGet z następującym ładunkiem
```

{
    "@apiVersion" : "2.0",
    "name" : "EVRtoAssetsOnMotionDetection"
}
```

W ciągu kilku sekund powinna zostać wyświetlona następująca odpowiedź w oknie danych wyjściowych.

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T22:05:31.603Z",
      "lastModifiedAt": "2020-05-12T22:05:31.603Z"
    },
    "name": "EVRtoAssetsOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Assets based on motion events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        },
        {
          "name": "motionSensitivity",
          "type": "String",
          "description": "motion detection sensitivity",
          "default": "medium"
        },
        {
          "name": "hubSinkOutputName",
          "type": "String",
          "description": "hub sink output name",
          "default": "iothubsinkoutput"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphAssetSink",
          "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "segmentLength": "PT0M30S",
          "assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}",
          "name": "assetSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Zwróć uwagę na następujące właściwości w ładunku odpowiedzi:

* Kod stanu to 200, co wskazuje na powodzenie.
* Ładunek ma sygnaturę czasową "created" i "lastModified".

### <a name="invoke-graphinstanceset"></a>Wywołaj GraphInstanceSet

Następnie Utwórz wystąpienie grafu odwołujące się do powyższej topologii wykresu. Jak wyjaśniono [tutaj](media-graph-concept.md#media-graph-topologies-and-instances), wystąpienia grafów umożliwiają analizowanie strumieni wideo na żywo z wielu kamer z tą samą topologią grafu.

Teraz Wywołaj metodę GraphInstanceSet Direct z następującym ładunkiem:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2",
    "properties" : {
        "topologyName" : "EVRtoAssetsOnMotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/lots_015.mkv" }
        ]
    }
}
```

. Weź pod uwagę następujące kwestie:

* Powyższy ładunek określa nazwę topologii wykresu (EVRtoAssetsOnMotionDetection), dla której należy utworzyć wystąpienie grafu.
* Ładunek zawiera wartość parametru dla elementu "rtspUrl", który nie ma wartości domyślnej w ładunku topologii.

W ciągu kilku sekund w oknie danych wyjściowych zostanie wyświetlona następująca odpowiedź:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T23:30:20.666Z",
      "lastModifiedAt": "2020-05-12T23:30:20.666Z"
    },
    "name": "Sample-Graph-2",
    "properties": {
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "EVRtoAssetsOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Zwróć uwagę na następujące właściwości w ładunku odpowiedzi:

* Kod stanu to 201, co oznacza, że zostało utworzone nowe wystąpienie.
* Stan ma wartość "Inactive", co oznacza, że wystąpienie grafu zostało utworzone, ale nie uaktywnione. Aby uzyskać więcej informacji, zobacz Stany [wykresu multimediów](media-graph-concept.md) .

Wypróbuj następujące metody bezpośrednie jako następne kroki:

* Wywołaj GraphInstanceSet ponownie z tym samym ładunkiem i zwróć uwagę, że zwrócony kod stanu to teraz 200.
* Wywołaj GraphInstanceSet ponownie, ale z innym opisem i zanotuj zaktualizowany opis w ładunku odpowiedzi, wskazując, że wystąpienie grafu zostało pomyślnie zaktualizowane.
* Wywołaj GraphInstanceSet, ale Zmień nazwę na "Sample-Graph-3" i obserwuj ładunek odpowiedzi. Należy pamiętać, że tworzone jest nowe wystąpienie grafu (oznacza to, że kod stanu to 201). Pamiętaj, aby wyczyścić takie zduplikowane wystąpienia po zakończeniu pracy z przewodnikiem Szybki Start.

### <a name="prepare-for-monitoring-events"></a>Przygotowanie do monitorowania zdarzeń

Utworzony wykres multimedialny używa węzła procesora wykrywania ruchu w celu wykrywania ruchu, a zdarzenia te są przekazywane do IoT Hub. Aby przygotować się do przestrzegania takich zdarzeń, wykonaj następujące kroki

1. Otwórz okienko Eksploratora w Visual Studio Code i Znajdź IoT Hub platformy Azure w lewym dolnym rogu.
1. Rozwiń węzeł urządzenia
1. Right-klinkieru na LVA-Sample-Device i wybierz opcję "Rozpocznij monitorowanie wbudowanego monitorowania zdarzeń"

    ![Rozpocznij monitorowanie wbudowanego monitorowania zdarzeń](./media/quickstarts/start-monitoring-iothub-events.png)
    
    W ciągu kilku sekund w oknie danych wyjściowych zostaną wyświetlone następujące komunikaty:

```
[IoTHubMonitor] Start monitoring message arrived in built-in endpoint for all devices ...
[IoTHubMonitor] Created partition receiver [0] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [1] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [2] for consumerGroup [$Default]
[IoTHubMonitor] Created partition receiver [3] for consumerGroup [$Default]
```

### <a name="invoke-graphinstanceactivate"></a>Wywołaj GraphInstanceActivate

Teraz Aktywuj wystąpienie grafu, które uruchamia przepływ wideo na żywo za pomocą modułu. Wywołaj metodę bezpośrednią GraphInstanceActivate z następującym ładunkiem:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

W ciągu kilku sekund powinna zostać wyświetlona następująca odpowiedź w oknie danych wyjściowych.

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Kod stanu 200 w ładunku odpowiedzi wskazuje, że wystąpienie grafu zostało pomyślnie uaktywnione.

### <a name="invoke-graphinstanceget"></a>Wywołaj GraphInstanceGet

Teraz Wywołaj metodę GraphInstanceGet Direct z następującym ładunkiem:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

W ciągu kilku sekund powinna zostać wyświetlona następująca odpowiedź w oknie danych wyjściowych.

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-12T23:30:20.666Z",
      "lastModifiedAt": "2020-05-12T23:30:20.666Z"
    },
    "name": "Sample-Graph-2",
    "properties": {
      "state": "Active",
      "description": "Sample graph description",
      "topologyName": "EVRtoAssetsOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Zwróć uwagę na następujące właściwości w ładunku odpowiedzi:

* Kod stanu to 200, co wskazuje na powodzenie.
* Stanem jest "aktywny" wskazujący, że wystąpienie grafu jest teraz w stanie "aktywny".

## <a name="observe-results"></a>Obserwuj wyniki

Utworzone i aktywowane wystąpienie grafu używa węzła procesora wykrywania ruchu w celu wykrywania ruchu przychodzącego strumienia wideo na żywo i wysyłania zdarzeń do IoT Hub ujścia. Te zdarzenia są następnie przekazywane do IoT Hub, które można obsłużyć. W oknie danych wyjściowych zostaną wyświetlone następujące komunikaty:

```
[IoTHubMonitor] [4:33:04 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-12T23:33:04.077Z",
    "dataVersion": "1.0"
  }
}
[IoTHubMonitor] [4:33:09 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "timestamp": 143039375044290,
    "inferences": [
      {
        "type": "motion",
        "motion": {
          "box": {
            "l": 0.48954,
            "t": 0.140741,
            "w": 0.075,
            "h": 0.058824
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/processors/md",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-12T23:33:09.381Z",
    "dataVersion": "1.0"
  }
}
```

Zwróć uwagę na następujące właściwości w powyższych komunikatach

* Każdy komunikat zawiera sekcję "treść" i sekcję "applicationProperties". Aby zrozumieć, co reprezentuje Ta sekcja, przeczytaj artykuł [Tworzenie i odczytywanie wiadomości IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).
* Pierwszy komunikat jest zdarzeniem diagnostycznym, MediaSessionEstablished, że węzeł źródłowy RTSP (podmiot) mógł nawiązać połączenie z symulatorem RTSP i zaczyna odbierać (symulowane) kanały informacyjne na żywo.
* "Podmiot" w applicationProperties odwołuje się do węzła w topologii grafu, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła źródłowego RTSP.
* "eventType" w applicationProperties wskazuje, że jest to zdarzenie diagnostyczne.
* "eventTime" wskazuje czas wystąpienia zdarzenia.
* "treść" zawiera dane dotyczące zdarzenia diagnostycznego — jest to komunikat [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .
* Drugi komunikat to zdarzenie analizy. Można sprawdzić, czy jest on wysyłany około 5 sekund po komunikacie MediaSessionEstablished, który odnosi się do opóźnienia między początkiem filmu wideo, a gdy są używane w ramach parkingowej partii.
* "Podmiot" w applicationProperties odwołuje się do węzła procesora wykrywania ruchu na grafie, który wygenerował ten komunikat
* Zdarzenie jest zdarzeniem wnioskowania, w związku z czym treść zawiera dane "timestamp" i "wnioskowania".
* sekcja "wnioskowanie" wskazuje, że "typ" jest "ruch" i zawiera dodatkowe dane dotyczące zdarzenia "Motion".

Zostanie wyświetlony następny komunikat.

```
[IoTHubMonitor] [4:33:10 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-12T23:33:10.392Z",
    "dataVersion": "1.0"
  }
}
```

* Trzeci komunikat to zdarzenie operacyjne. Można sprawdzić, czy jest on wysyłany niemal natychmiast po komunikacie wykrywania ruchu, który działa jak wyzwalacz, aby rozpocząć nagrywanie.
* "Podmiot" w applicationProperties odwołuje się do węzła ujścia zasobów na grafie, który wygenerował tę wiadomość.
* Treść zawiera informacje o lokalizacji wyjściowej, która w tym przypadku jest nazwą zasobu usługi Azure Media, do którego jest nagrywane wideo. Zanotuj tę wartość — będziesz jej używać w dalszej części przewodnika Szybki Start.

W topologii węzeł procesora bramy sygnału został skonfigurowany z czasem aktywacji wynoszącym 30 sekund, co oznacza, że topologia grafu będzie rejestrować około 30-sekundową zawartość wideo w elemencie zawartości. Podczas rejestrowania wideo węzeł procesora wykrywania ruchu będzie kontynuował emitowanie zdarzeń wnioskowania, które będą widoczne w oknie danych wyjściowych. Po pewnym czasie zostanie wyświetlony następujący komunikat.

```
[IoTHubMonitor] [4:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-12T23:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

* Ten komunikat jest również zdarzeniem operacyjnym. Zdarzenie, RecordingAvailable, wskazuje, że do zasobu Zapisano wystarczającą ilość danych, aby gracze/klienci mogli inicjować odtwarzanie wideo
* "Podmiot" w applicationProperties odwołuje się do węzła ujścia zasobów na grafie, który wygenerował ten komunikat
* Treść zawiera informacje o lokalizacji wyjściowej, która w tym przypadku jest nazwą zasobu usługi Azure Media, do którego jest nagrywane wideo.

Jeśli zezwolisz na kontynuowanie działania wystąpienia programu Graph, zobaczysz ten komunikat.

```
[IoTHubMonitor] [4:33:40 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-12T23:33:40.014Z",
    "dataVersion": "1.0"
  }
}
```

* Ten komunikat jest również zdarzeniem operacyjnym. Zdarzenie RecordingStopped wskazuje, że nagrywanie zostało zatrzymane.
* Należy zauważyć, że około 30 sekund upłynął od zdarzenia RecordingStarted, dopasowując wartości czasów aktywacji w węźle procesor bramy sygnałów.
* "Podmiot" w applicationProperties odwołuje się do węzła ujścia zasobów na grafie, który wygenerował tę wiadomość.
* Treść zawiera informacje o lokalizacji wyjściowej, która w tym przypadku jest nazwą zasobu usługi Azure Media, do którego jest nagrywane wideo.

Jeśli zezwolisz na kontynuowanie działania wystąpienia programu Graph, symulator RTSP osiągnie koniec pliku wideo i zatrzyma/Rozłącz. Węzeł źródłowy RTSP będzie ponownie łączył się z symulatorem, a proces zostanie powtórzony.
    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>Wywoływanie dodatkowych wywołań metody bezpośredniej do oczyszczenia

Teraz wywołaj metody bezpośrednie, aby dezaktywować i usunąć wystąpienie grafu (w tej kolejności).

### <a name="invoke-graphinstancedeactivate"></a>Wywołaj GraphInstanceDeactivate

Wywołaj metodę Direct metody GraphInstanceDeactivate z następującym ładunkiem:

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

W ciągu kilku sekund powinna zostać wyświetlona następująca odpowiedź w oknie danych wyjściowych.

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Kod stanu 200 wskazuje, że wystąpienie grafu zostało pomyślnie zdezaktywowane.

Spróbuj wykonać poniższe czynności, wykonując następujące czynności:

* Wywołaj GraphInstanceGet zgodnie ze wskazanymi wcześniej sekcjami i obserwuj wartość "State" (stan).

### <a name="invoke-graphinstancedelete"></a>Wywołaj GraphInstanceDelete

Wywołaj metodę bezpośrednią GraphInstanceDelete z następującym ładunkiem

```
{
    "@apiVersion" : "2.0",
    "name" : "Sample-Graph-2"
}
```

W ciągu kilku sekund powinna zostać wyświetlona następująca odpowiedź w oknie danych wyjściowych:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Kod stanu 200 w odpowiedzi wskazuje, że wystąpienie grafu zostało pomyślnie usunięte.

### <a name="invoke-graphtopologydelete"></a>Wywołaj GraphTopologyDelete

Wywołaj metodę Direct metody GraphTopologyDelete z następującym ładunkiem:

```
{
    "@apiVersion" : "2.0",
    "name" : "EVRtoAssetsOnMotionDetection"
}
```

W ciągu kilku sekund powinna zostać wyświetlona następująca odpowiedź w oknie danych wyjściowych.

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Kod stanu 200 wskazuje, że topologia MediaGraph została pomyślnie usunięta.

Wypróbuj następujące metody bezpośrednie jako następne kroki:

* Wywołaj GraphTopologyList i sprawdź, czy w module nie ma topologii grafu.
* Wywołaj GraphInstanceList z tym samym ładunkiem co GraphTopologyList i obserwuj, że nie są wyliczane wystąpienia grafów.

## <a name="playing-back-the-recorded-video"></a>Odtwarzanie nagrania wideo

Następnie możesz użyć Azure Portal, aby odtworzyć nagrywane wideo.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/), w polu wyszukiwania wpisz ciąg "Media Services".
1. Znajdź konto Azure Media Services i otwórz je.
1. Znajdź i wybierz pozycję zasoby na liście Media Services.

    ![Wpis zasobów na liście Media Services](./media/quickstarts/asset-entity.png)
1. Jeśli ten przewodnik Szybki Start to pierwsze użycie Azure Media Services, zostaną wyświetlone tylko zasoby wygenerowane z tego przewodnika Szybki Start i można wybrać najstarsze.
1. W przeciwnym razie użyj nazwy zasobu, który został podany jako outputLocation w powyższym zdarzeniu operacyjnym.
1. Na stronie Szczegóły, która zostanie otwarta, kliknij link "Utwórz nowy" tuż poniżej pola tekstowego adres URL przesyłania strumieniowego.

    ![Adres URL przesyłania strumieniowego](./media/quickstarts/asset-streaming-url.png)
1. W okienku, które zostanie otwarte dla opcji Dodaj lokalizator przesyłania strumieniowego, zaakceptuj ustawienia domyślne i naciśnij przycisk "Dodaj" u dołu.
1. Na stronie Szczegóły zasobu odtwarzacz wideo powinien teraz ładować do pierwszej ramki filmu wideo i można nacisnąć przycisk Odtwórz. Sprawdź, czy jest wyświetlana część filmu wideo, w której kabina jest przenoszona w partii parkingowej.

    ![Graj.](./media/quickstarts/asset-playback.png)

> [!NOTE]
> Ponieważ symulowane wideo na żywo jest uruchamiane po aktywowaniu wykresu, wartości godzinowe nie są istotne i nie są udostępniane za pośrednictwem tego skrótu odtwarzacza. Samouczek dotyczący ciągłego nagrywania i odtwarzania wideo pokazuje, jak można wyświetlić sygnatury czasowe.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń zasoby utworzone w tym przewodniku Szybki Start.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak programowo wywoływać analizy wideo na żywo na IoT Edge [bezpośrednie metody](direct-methods.md) .
* Dowiedz się więcej o komunikatach diagnostycznych.    
