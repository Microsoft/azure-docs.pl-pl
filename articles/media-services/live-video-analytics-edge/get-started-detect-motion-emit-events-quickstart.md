---
title: Wprowadzenie do analizy filmów wideo na żywo na IoT Edge platformy Azure
description: W tym przewodniku szybki start pokazano, jak rozpocząć pracę z funkcją analizy filmów wideo na żywo na IoT Edge i wykryć ruch w strumieniu wideo na żywo.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 307a81938be3e25b8a6a07bb3696ca3b7647c0aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262014"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Szybki Start: Rozpoczynanie pracy — Analiza filmów wideo na żywo na IoT Edge

Ten przewodnik Szybki Start przeprowadzi Cię przez kroki umożliwiające rozpoczęcie pracy z usługą analiza filmów wideo na żywo na IoT Edge. Używa ona maszyny wirtualnej platformy Azure jako urządzenia IoT Edge oraz symulowanego strumienia wideo na żywo. Po wykonaniu kroków instalacji można uruchomić symulowany strumień wideo na żywo za pomocą grafu multimediów, który wykrywa i raportuje wszystkie ruchy w tym strumieniu. Na poniższym diagramie przedstawiono graficzną reprezentację tego wykresu multimedialnego.

![Analiza wideo na żywo oparta na wykrywaniu ruchu](./media/analyze-live-video/motion-detection.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) na komputerze deweloperskim przy użyciu [rozszerzenia narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Sieć, z którą jest połączony komputer deweloperski, powinna zezwalać na protokół AMQP przez port 5671 (dzięki czemu narzędzia Azure IoT Tools mogą komunikować się z usługą Azure IoT Hub).

> [!TIP]
> Może zostać wyświetlony monit o zainstalowanie platformy Docker podczas instalowania rozszerzenia narzędzi Azure IoT Tools. Możesz go zignorować.

## <a name="set-up-azure-resources"></a>Konfigurowanie zasobów platformy Azure

Dla tego samouczka wymagane są następujące zasoby platformy Azure.

* Usługa IoT Hub
* Konto magazynu
* Konto Azure Media Services
* Maszyna wirtualna z systemem Linux na platformie Azure z zainstalowanym [IoT Edge środowiska uruchomieniowego](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

W tym przewodniku szybki start zalecamy użycie [skryptu konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) w celu wdrożenia zasobów platformy Azure wymienionych powyżej w ramach subskrypcji platformy Azure. W tym celu wykonaj poniższe kroki:

1. Przejdź do https://shell.azure.com.
1. Jeśli używasz Cloud Shell po raz pierwszy, zostanie wyświetlony monit o wybranie subskrypcji w celu utworzenia konta magazynu i udziału plików Microsoft Azure. Wybierz pozycję "Utwórz magazyn", aby utworzyć konto magazynu do przechowywania informacji o sesji Cloud Shell. To konto magazynu jest niezależne od tego, który skrypt utworzy do użycia z Twoim kontem Azure Media Services.
1. Wybierz pozycję "bash" jako środowisko na liście rozwijanej po lewej stronie okna powłoki.

    ![Selektor środowiska](./media/quickstarts/env-selector.png)

1. Uruchom następujące polecenie

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
Jeśli skrypt zakończy się pomyślnie, zobaczysz wszystkie zasoby wymienione powyżej w subskrypcji. W ramach danych wyjściowych skryptu zostanie wygenerowana tabela zasobów, która będzie zawierać nazwę Centrum IoT. Poszukaj typu zasobu **"Microsoft. Devices/IotHubs"** i zanotuj nazwę. Będzie ona potrzebna w następnym kroku. Skrypt generuje również kilka plików konfiguracji w katalogu ~/CloudDrive/LVA-Sample/— będą one potrzebne później w tym przewodniku Szybki Start.

## <a name="deploy-modules-on-your-edge-device"></a>Wdrażanie modułów na urządzeniu brzegowym

Uruchom następujące polecenie w Cloud Shell

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Powyższe polecenie spowoduje wdrożenie następujących modułów na urządzeniu brzegowym (maszyna wirtualna z systemem Linux):

* Analiza wideo na żywo na IoT Edge (Nazwa modułu: "lvaEdge")
* Symulator RTSP (Nazwa modułu: "rtspsim")

Moduł symulatora RTSP symuluje strumień wideo na żywo przy użyciu zapisanego pliku wideo, który został skopiowany do urządzenia brzegowego po uruchomieniu [skryptu konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). Na tym etapie są wdrożone moduły, ale nie są aktywne żadne wykresy multimedialne.

## <a name="configure-azure-iot-tools-extension-in-visual-studio-code"></a>Skonfiguruj rozszerzenie narzędzi Azure IoT Tools w Visual Studio Code

Rozpocznij Visual Studio Code i postępuj zgodnie z poniższymi instrukcjami, aby nawiązać połączenie z usługą Azure IoT Hub przy użyciu rozszerzenia Azure IoT Tools.

1. Przejdź do karty Eksplorator w Visual Studio Code za pomocą **View**  >  **Eksploratora** widoków lub po prostu naciśnij klawisz (Ctrl + Shift + E).
1. Na karcie Eksplorator kliknij pozycję "Azure IoT Hub" w lewym dolnym rogu.
1. Kliknij ikonę Więcej opcji, aby wyświetlić menu kontekstowe i wybierz opcję "Ustaw IoT Hub parametry połączenia".
1. Zostanie wyświetlona ramka wejściowa, a następnie wprowadź parametry połączenia IoT Hub. Parametry połączenia dla IoT Hub można uzyskać z ~/CloudDrive/LVA-Sample/appSettings.JSON w Cloud Shell.
1. Jeśli połączenie zakończy się pomyślnie, zostanie wyświetlona lista urządzeń brzegowych. Powinien być co najmniej jedno urządzenie o nazwie "LVA-Sample-Device".
1. Teraz możesz zarządzać urządzeniami IoT Edge i korzystać z usługi Azure IoT Hub za pomocą menu kontekstowego.
1. Można wyświetlić moduły wdrożone na urządzeniu brzegowym, rozszerzając węzeł modułów w obszarze "LVA-Sample-Device".

    ![LVA — przykład — węzeł urządzenia](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-methods"></a>Korzystanie z metod bezpośrednich

Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie metod bezpośrednich. Przeczytaj [bezpośrednie metody analizy wideo na żywo na IoT Edge](direct-methods.md) , aby zrozumieć wszystkie metody bezpośrednie dostarczone przez moduł. 

### <a name="invoke-graphtopologylist"></a>Wywołaj GraphTopologyList
Spowoduje to wyliczenie wszystkich [topologii grafu](media-graph-concept.md#media-graph-topologies-and-instances) w module.

1. Kliknij prawym przyciskiem myszy moduł "lvaEdge" i wybierz polecenie "Wywołaj metodę bezpośrednią modułu" z menu kontekstowego.
1. W górnym rogu okna Visual Studio Code zobaczysz pole edycji. Wprowadź wartość "GraphTopologyList" w polu edycji i naciśnij klawisz ENTER.
1. Następnie skopiuj i wklej poniższy ładunek JSON w polu edycji i naciśnij klawisz ENTER.

    ```
    {
        "@apiVersion" : "1.0"
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

Korzystając z tych samych kroków, które opisano w wywołaniu GraphTopologyList, można wywołać GraphTopologySet, aby ustawić [topologię wykresu](media-graph-concept.md#media-graph-topologies-and-instances) przy użyciu następującego kodu JSON jako ładunku.

```
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
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
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
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


Powyższy ładunek JSON powoduje utworzenie topologii wykresu, która definiuje trzy parametry (dwa z wartości domyślnych). Topologia ma jeden węzeł źródłowy (RTSP Source), jeden węzeł procesora (procesor wykrywania ruchu) i jeden węzeł ujścia (IoT Hub ujścia).

W ciągu kilku sekund w oknie danych wyjściowych zostanie wyświetlona następująca odpowiedź:

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
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
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
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

Zwróconym stanem jest 201, co oznacza, że została utworzona nowa topologia. Spróbuj wykonać następujące czynności w następujących krokach:

* Wywołaj GraphTopologySet ponownie i zwróć uwagę, że zwrócony kod stanu to 200. Kod stanu 200 wskazuje, że istniejąca topologia została pomyślnie zaktualizowana.
* Wywołaj GraphTopologySet ponownie, ale Zmień ciąg opisu. Zwróć uwagę, że kod stanu w odpowiedzi to 200, a opis zostanie zaktualizowany do nowej wartości.
* Wywołaj GraphTopologyList zgodnie z opisem w poprzedniej sekcji i pamiętaj, że teraz można zobaczyć topologię "MotionDetection" w zwracanym ładunku.

### <a name="invoke-graphtopologyget"></a>Wywołaj GraphTopologyGet

Teraz Wywołaj GraphTopologyGet z następującym ładunkiem

```

{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

W ciągu kilku sekund powinna zostać wyświetlona następująca odpowiedź w oknie danych wyjściowych:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
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
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
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

Zwróć uwagę na następujące kwestie w ładunku odpowiedzi:

* Kod stanu to 200, co wskazuje na powodzenie.
* Ładunek ma sygnaturę czasową "created" i "lastModified".

### <a name="invoke-graphinstanceset"></a>Wywołaj GraphInstanceSet

Następnie Utwórz wystąpienie grafu odwołujące się do powyższej topologii wykresu. Jak wyjaśniono [tutaj](media-graph-concept.md#media-graph-topologies-and-instances), wystąpienia grafów umożliwiają analizowanie strumieni wideo na żywo z wielu kamer z tą samą topologią grafu.

Wywołaj metodę bezpośrednią GraphInstanceSet z następującym ładunkiem.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

. Weź pod uwagę następujące kwestie:

* Powyższy ładunek określa nazwę topologii (MotionDetection), dla której należy utworzyć wystąpienie.
* Ładunek zawiera wartość parametru dla elementu "rtspUrl", który nie ma wartości domyślnej w ładunku topologii wykresu.

W ciągu kilku sekund w oknie danych wyjściowych zostanie wyświetlona następująca odpowiedź:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Zwróć uwagę na następujące kwestie w ładunku odpowiedzi:

* Kod stanu to 201, co oznacza, że zostało utworzone nowe wystąpienie.
* Stan ma wartość "Inactive", co oznacza, że wystąpienie grafu zostało utworzone, ale nie uaktywnione. Aby uzyskać więcej informacji, zobacz [Stany wykresu multimediów](media-graph-concept.md).

Spróbuj wykonać następujące czynności w następujących krokach:

* Wywołaj GraphInstanceSet ponownie z tym samym ładunkiem i zwróć uwagę, że zwrócony kod stanu to teraz 200.
* Wywołaj GraphInstanceSet ponownie, ale z innym opisem i pamiętaj, że zaktualizowany opis w ładunku odpowiedzi wskazujący, że wystąpienie grafu zostało pomyślnie zaktualizowane.
* Wywołaj GraphInstanceSet, ale Zmień nazwę na "Sample-Graph-2" i obserwuj ładunek odpowiedzi. Należy pamiętać, że tworzone jest nowe wystąpienie grafu (oznacza to, że kod stanu to 201).

### <a name="invoke-graphinstanceactivate"></a>Wywołaj GraphInstanceActivate

Teraz Aktywuj wystąpienie grafu, które uruchamia przepływ wideo na żywo za pomocą modułu. Wywołaj metodę bezpośrednią GraphInstanceActivate z następującym ładunkiem.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

W ciągu kilku sekund powinna zostać wyświetlona następująca odpowiedź w oknie danych wyjściowych:

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

Teraz Wywołaj metodę bezpośrednią GraphInstanceGet z następującym ładunkiem:

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

W ciągu kilku sekund powinna zostać wyświetlona następująca odpowiedź w oknie danych wyjściowych:

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Zwróć uwagę na następujące kwestie w ładunku odpowiedzi:

* Kod stanu to 200, co wskazuje na powodzenie.
* Stanem jest "aktywny" wskazujący, że wystąpienie grafu jest teraz w stanie "aktywny".

## <a name="observe-results"></a>Obserwuj wyniki

Utworzone i aktywowane wystąpienie grafu używa węzła procesora wykrywania ruchu w celu wykrywania ruchu przychodzącego strumienia wideo na żywo i wysyłania zdarzeń do węzła ujścia IoT Hub. Te zdarzenia są następnie przekazywane do centrum IoT Edge, które można obsłużyć. W tym celu wykonaj poniższe kroki.

1. Otwórz okienko Eksploratora w Visual Studio Code i Znajdź IoT Hub platformy Azure w lewym dolnym rogu.
2. Rozwiń węzeł urządzenia.
3. Right-klinkieru na LVA-Sample-Device i wybierz opcję "Rozpocznij monitorowanie wbudowanego monitorowania zdarzeń".

![Rozpocznij monitorowanie zdarzeń centrum IoT Hub](./media/quickstarts/start-monitoring-iothub-events.png)

W oknie danych wyjściowych zostaną wyświetlone następujące komunikaty:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

Zwróć uwagę na następujące dane w powyższym komunikacie

* Komunikat zawiera sekcję "treść" i sekcję "applicationProperties". Aby zrozumieć, co reprezentuje Ta sekcja, przeczytaj artykuł [Tworzenie i odczytywanie wiadomości IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).
* "podmiot" w applicationProperties odwołuje się do węzła w MediaGraph, z poziomu którego wiadomość została wygenerowana. W takim przypadku komunikat pochodzi z procesora wykrywania ruchu.
* "eventType" w applicationProperties wskazuje, że jest to zdarzenie analizy.
* "eventTime" wskazuje czas wystąpienia zdarzenia.
* "treść" zawiera dane dotyczące zdarzenia analizy. W takim przypadku zdarzenie jest zdarzeniem wnioskowania, w związku z czym treść zawiera dane o sygnaturach czasowych i "wnioskach".
* sekcja "wnioskowanie" wskazuje, że "typ" jest "ruch" i zawiera dodatkowe dane dotyczące zdarzenia "Motion".

Jeśli MediaGraph zostanie uruchomiony przez jakiś czas, w oknie danych wyjściowych zostanie wyświetlony następujący komunikat:

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

Zwróć uwagę na następujące dane w powyższym komunikacie

* "podmiot" w applicationProperties wskazuje, że wiadomość została wygenerowana z węzła źródłowego RTSP na grafie multimediów.
* "eventType" w applicationProperties wskazuje, że jest to zdarzenie diagnostyczne.
* "treść" zawiera dane dotyczące zdarzenia diagnostycznego. W takim przypadku zdarzenie to MediaSessionEstablished, a tym samym treść.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Wywołaj dodatkowe metody bezpośrednie do oczyszczenia

Teraz wywołaj metody bezpośrednie, aby dezaktywować i usunąć wystąpienie grafu (w tej kolejności).

### <a name="invoke-graphinstancedeactivate"></a>Wywołaj GraphInstanceDeactivate

Wywołaj metodę bezpośrednią GraphInstanceDeactivate z następującym ładunkiem.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

W ciągu kilku sekund powinna zostać wyświetlona następująca odpowiedź w oknie danych wyjściowych:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Kod stanu 200 wskazuje, że wystąpienie grafu zostało pomyślnie zdezaktywowane.

Spróbuj wykonać poniższe czynności, postępując zgodnie z kolejnymi krokami.

* Wywołaj GraphInstanceGet zgodnie ze wskazanymi wcześniej sekcjami i obserwuj wartość "State" (stan).

### <a name="invoke-graphinstancedelete"></a>Wywołaj GraphInstanceDelete

Wywołaj metodę bezpośrednią GraphInstanceDelete z następującym ładunkiem

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
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

Wywołaj metodę bezpośrednią GraphTopologyDelete z następującym ładunkiem:

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

W ciągu kilku sekund powinna zostać wyświetlona następująca odpowiedź w oknie danych wyjściowych:

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Kod stanu 200 wskazuje, że topologia grafu została pomyślnie usunięta.

Spróbuj wykonać poniższe czynności.

* Wywołaj GraphTopologyList i sprawdź, czy w module nie ma topologii grafu.
* Wywołaj GraphInstanceList z tym samym ładunkiem co GraphTopologyList i obserwuj, że nie są wyliczane wystąpienia grafów.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń zasoby utworzone w tym przewodniku Szybki Start.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak zarejestrować wideo przy użyciu usługi Live Video Analytics na IoT Edge
* Dowiedz się więcej o komunikatach diagnostycznych.
