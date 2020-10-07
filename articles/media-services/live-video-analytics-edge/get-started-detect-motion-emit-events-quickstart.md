---
title: Wprowadzenie do analizy filmów wideo na żywo na IoT Edge platformy Azure
description: Ten przewodnik Szybki Start przedstawia sposób rozpoczynania pracy z usługą analiza filmów wideo na żywo na IoT Edge. Dowiedz się, jak wykrywać ruch w strumieniu wideo na żywo.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 9cf574cba023c9eb5a44999b3aa04f6c1e626ed1
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91773408"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Szybki Start: Rozpoczynanie pracy — Analiza filmów wideo na żywo na IoT Edge

Ten przewodnik Szybki Start przeprowadzi Cię przez kroki umożliwiające rozpoczęcie pracy z usługą analiza filmów wideo na żywo na IoT Edge. Używa maszyny wirtualnej platformy Azure jako urządzenia IoT Edge. Używa również symulowanego strumienia wideo na żywo. 

Po wykonaniu kroków instalacji można uruchomić symulowany strumień wideo na żywo za pomocą grafu multimediów, który wykrywa i raportuje wszystkie ruchy w tym strumieniu. Poniższy diagram przedstawia graficznie wykres multimedialny.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Analiza wideo na żywo oparta na wykrywaniu ruchu":::

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , jeśli jeszcze go nie masz.
* [Visual Studio Code](https://code.visualstudio.com/) na komputerze deweloperskim. Upewnij się, że masz [rozszerzenie narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Upewnij się, że sieć, do której jest podłączony komputer deweloperski, zezwala na protokół AMQP (Advanced Message Queueing Protocol) przez port 5671. To ustawienie umożliwia usłudze Azure IoT Tools komunikowanie się z usługą Azure IoT Hub.

> [!TIP]
> Może zostać wyświetlony monit o zainstalowanie platformy Docker podczas instalowania rozszerzenia narzędzi Azure IoT Tools. Możesz zignorować ten monit.

## <a name="set-up-azure-resources"></a>Konfigurowanie zasobów platformy Azure

Ten samouczek wymaga następujących zasobów platformy Azure:

* Usługa IoT Hub
* Konto magazynu
* Konto Azure Media Services
* Maszyna wirtualna z systemem Linux na platformie Azure, w której zainstalowano [środowisko uruchomieniowe IoT Edge](../../iot-edge/how-to-install-iot-edge-linux.md)

W tym przewodniku szybki start zalecamy używanie [skryptu konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) w celu wdrożenia wymaganych zasobów w ramach subskrypcji platformy Azure. Aby to zrobić, wykonaj następujące kroki:

1. Przejdź do [Azure Cloud Shell](https://shell.azure.com).
1. Jeśli używasz Cloud Shell po raz pierwszy, zostanie wyświetlony monit o wybranie subskrypcji w celu utworzenia konta magazynu i udziału plików Microsoft Azure. Wybierz pozycję **Utwórz magazyn** , aby utworzyć konto magazynu dla informacji sesji Cloud Shell. To konto magazynu jest niezależne od konta, które zostanie utworzone przez skrypt do użycia z Twoim kontem Azure Media Services.
1. W menu rozwijanym po lewej stronie okna Cloud Shell wybierz pozycję **bash** jako swoje środowisko.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/env-selector.png" alt-text="Analiza wideo na żywo oparta na wykrywaniu ruchu"
    ```
    
Jeśli skrypt zakończy się pomyślnie, wszystkie wymagane zasoby powinny zostać wyświetlone w ramach subskrypcji. W danych wyjściowych skryptu tabela zasobów zawiera nazwę Centrum IoT Hub. Poszukaj typu zasobu `Microsoft.Devices/IotHubs` i zanotuj nazwę. Ta nazwa będzie potrzebna w następnym kroku. 

Skrypt generuje również kilka plików konfiguracji w katalogu *~/CloudDrive/LVA-Sample/* . Te pliki będą potrzebne w dalszej części przewodnika Szybki Start.

## <a name="deploy-modules-on-your-edge-device"></a>Wdrażanie modułów na urządzeniu brzegowym

Uruchom następujące polecenie w Cloud Shell.

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

To polecenie wdraża następujące moduły na urządzeniu brzegowym, które jest w tym przypadku maszyną wirtualną z systemem Linux.

* Analiza wideo na żywo na IoT Edge (Nazwa modułu `lvaEdge` )
* Symulator protokołu przesyłania strumieniowego (RTSP) w czasie rzeczywistym (Nazwa modułu `rtspsim` )

Moduł symulatora RTSP symuluje strumień wideo na żywo przy użyciu pliku wideo, który został skopiowany do urządzenia brzegowego po uruchomieniu [skryptu konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

Teraz moduły są wdrażane, ale nie są aktywne żadne wykresy multimedialne.

## <a name="configure-the-azure-iot-tools-extension"></a>Konfigurowanie rozszerzenia narzędzi Azure IoT Tools

Postępuj zgodnie z tymi instrukcjami, aby nawiązać połączenie z Centrum IoT Hub przy użyciu rozszerzenia narzędzi Azure IoT Tools.

1. W Visual Studio Code Otwórz kartę **rozszerzenia** (lub naciśnij klawisze CTRL + SHIFT + X) i Wyszukaj pozycję Azure IoT Hub.
1. Kliknij prawym przyciskiem myszy i wybierz pozycję **Ustawienia rozszerzenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Analiza wideo na żywo oparta na wykrywaniu ruchu":::
1. Wyszukaj i Włącz opcję "Pokaż pełny komunikat".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Analiza wideo na żywo oparta na wykrywaniu ruchu":::
1. <!--In Visual Studio Code-->Wybierz pozycję **Widok**  >  **Eksplorator**. Lub wybierz kombinację klawiszy Ctrl + Shift + E.
1. W lewym dolnym rogu karty **Eksplorator** wybierz pozycję **Azure IoT Hub**.
1. Wybierz ikonę **więcej opcji** , aby wyświetlić menu kontekstowe. Następnie wybierz pozycję **ustaw IoT Hub parametry połączenia**.
1. Po wyświetleniu pola wejściowego wprowadź parametry połączenia IoT Hub. W Cloud Shell można uzyskać parametry połączenia z *~/clouddrive/lva-sample/appsettings.jswłączone*.

Jeśli połączenie zakończy się pomyślnie, zostanie wyświetlona lista urządzeń brzegowych. Powinna zostać wyświetlona co najmniej jedno urządzenie o nazwie **LVA-Sample-Device**. Teraz możesz zarządzać urządzeniami IoT Edge i korzystać z usługi Azure IoT Hub za pomocą menu kontekstowego. Aby wyświetlić moduły wdrożone na urządzeniu brzegowym, w obszarze **LVA-Sample-Device**rozwiń węzeł **moduły** .

![LVA — przykład — węzeł urządzenia](./media/quickstarts/lva-sample-device-node.png)

> [!TIP]
> Po [ręcznym wdrożeniu usługi Azure Video Analytics na IoT Edge](deploy-iot-edge-device.md) yourselves na urządzeniu brzegowym (takim jak urządzenie arm64) zobaczysz, że moduł zostanie wyświetlony na tym urządzeniu w obszarze IoT Hub platformy Azure. Możesz wybrać ten moduł i wykonać pozostałe kroki opisane poniżej.

## <a name="use-direct-method-calls"></a>Używanie wywołań metod bezpośrednich

Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie metod bezpośrednich. Aby uzyskać więcej informacji, zobacz [bezpośrednie metody analizy wideo na żywo na IoT Edge](direct-methods.md). 

### <a name="invoke-graphtopologylist"></a>Wywołaj GraphTopologyList

Aby wyliczyć wszystkie [topologie grafu](media-graph-concept.md#media-graph-topologies-and-instances) w module:

1. W Visual Studio Code kliknij prawym przyciskiem myszy moduł **lvaEdge** i wybierz polecenie **Wywołaj metodę bezpośrednią modułu**.
1. W wyświetlonym polu wprowadź *GraphTopologyList*.
1. Skopiuj następujący ładunek JSON, a następnie wklej go w polu. Następnie wybierz klawisz ENTER.

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    W ciągu kilku sekund w oknie **danych wyjściowych** zostanie wyświetlona następująca odpowiedź.

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
    
    Ta odpowiedź jest oczekiwana, ponieważ nie utworzono żadnych topologii grafu.
    

### <a name="invoke-graphtopologyset"></a>Wywołaj GraphTopologySet

Korzystając z kroków do wywoływania, można `GraphTopologyList` wywołać, `GraphTopologySet` Aby ustawić [topologię grafu](media-graph-concept.md#media-graph-topologies-and-instances). Użyj poniższego kodu JSON jako ładunku.

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

Ten ładunek JSON tworzy topologię grafu, która definiuje trzy parametry. Dwa z tych parametrów mają wartości domyślne. Topologia ma jeden węzeł źródłowy (RTSP Source), jeden węzeł procesora (procesor wykrywania ruchu) i jeden węzeł ujścia (IoT Hub ujścia).

W ciągu kilku sekund zostanie wyświetlona następująca odpowiedź w oknie **danych wyjściowych** .

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

Zwróconym stanem jest 201. Ten stan wskazuje na utworzenie nowej topologii. 

Spróbuj wykonać następujące czynności:

1. Wywołaj `GraphTopologySet` ponownie. Zwrócony kod stanu to 200. Ten kod wskazuje, że istniejąca topologia została pomyślnie zaktualizowana.
1. Wywołaj `GraphTopologySet` ponownie, ale Zmień ciąg opisu. Zwróconym kodem stanu jest 200, a opis zostanie zaktualizowany do nowej wartości.
1. Wywołaj `GraphTopologyList` , jak opisano w poprzedniej sekcji. Teraz można zobaczyć `MotionDetection` topologię w zwracanym ładunku.

### <a name="invoke-graphtopologyget"></a>Wywołaj GraphTopologyGet

Wywołaj `GraphTopologyGet` przy użyciu następującego ładunku.

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

W ciągu kilku sekund w oknie **danych wyjściowych** zostanie wyświetlona następująca odpowiedź:

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

W ładunku odpowiedzi Zwróć uwagę na następujące informacje:

* Kod stanu to 200, co wskazuje na powodzenie.
* Ładunek zawiera `created` sygnaturę czasową i `lastModified` sygnaturę czasową.

### <a name="invoke-graphinstanceset"></a>Wywołaj GraphInstanceSet

Utwórz wystąpienie grafu odwołujące się do poprzedniej topologii wykresu. Wystąpienia grafów umożliwiają analizowanie strumieni wideo na żywo z wielu kamer przy użyciu tej samej topologii wykresu. Aby uzyskać więcej informacji, zobacz [Multimedia i wystąpienia programu Graph](media-graph-concept.md#media-graph-topologies-and-instances).

Wywoływanie metody bezpośredniej przy `GraphInstanceSet` użyciu następującego ładunku.

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

Należy zauważyć, że ten ładunek:

* Określa nazwę topologii ( `MotionDetection` ), dla której należy utworzyć wystąpienie.
* Zawiera wartość parametru dla `rtspUrl` , która nie ma wartości domyślnej w ładunku topologii wykresu.

W ciągu kilku sekund w oknie **danych wyjściowych** zostanie wyświetlona następująca odpowiedź:

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

Zwróć uwagę, że w ładunku odpowiedzi:

* Kod stanu to 201, co oznacza, że zostało utworzone nowe wystąpienie.
* Stan to `Inactive` , wskazujący, że wystąpienie grafu zostało utworzone, ale nie uaktywnione. Aby uzyskać więcej informacji, zobacz [Stany wykresu multimediów](media-graph-concept.md).

Spróbuj wykonać następujące czynności:

1. Wywołaj `GraphInstanceSet` ponownie przy użyciu tego samego ładunku. Zwróć uwagę, że zwrócony kod stanu to 200.
1. Wywołaj `GraphInstanceSet` ponownie, ale Użyj innego opisu. Zwróć uwagę na zaktualizowany opis w ładunku odpowiedzi wskazujący, że wystąpienie grafu zostało pomyślnie zaktualizowane.
1. Wywołaj `GraphInstanceSet` , ale Zmień nazwę na `Sample-Graph-2` . W ładunku odpowiedzi Zwróć uwagę na nowo utworzone wystąpienie grafu (oznacza to, że kod stanu 201).

### <a name="invoke-graphinstanceactivate"></a>Wywołaj GraphInstanceActivate

Teraz Aktywuj wystąpienie grafu, aby uruchomić przepływ wideo na żywo za pomocą modułu. Wywoływanie metody bezpośredniej przy `GraphInstanceActivate` użyciu następującego ładunku.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

W ciągu kilku sekund zostanie wyświetlona następująca odpowiedź w oknie **danych wyjściowych** .

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Kod stanu 200 wskazuje, że wystąpienie grafu zostało pomyślnie uaktywnione.

### <a name="invoke-graphinstanceget"></a>Wywołaj GraphInstanceGet

Teraz wywoływanie metody bezpośredniej przy `GraphInstanceGet` użyciu poniższego ładunku.

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

W ciągu kilku sekund zostanie wyświetlona następująca odpowiedź w oknie **danych wyjściowych** .

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

W ładunku odpowiedzi Zwróć uwagę na następujące informacje:

* Kod stanu to 200, co wskazuje na powodzenie.
* Stan to `Active` , wskazujący, że wystąpienie grafu jest teraz aktywne.

## <a name="observe-results"></a>Obserwuj wyniki

Utworzone i aktywowane wystąpienie grafu używa węzła procesora wykrywania ruchu w celu wykrywania ruchu w przychodzącym strumieniu wideo na żywo. Wysyła zdarzenia do węzła ujścia IoT Hub. Te zdarzenia są przekazywane do centrum IoT Edge. 

Aby obserwować wyniki, wykonaj następujące kroki.

1. W Visual Studio Code Otwórz okienko **Eksploratora** . W lewym dolnym rogu Znajdź pozycję **Azure IoT Hub**.
2. Rozwiń węzeł **urządzenia** .
3. Kliknij prawym przyciskiem myszy pozycję **LVA-Sample-Device** , a następnie wybierz pozycję **Rozpocznij monitorowanie wbudowanego monitorowania zdarzeń**.

    ![Rozpocznij monitorowanie zdarzeń centrum IoT Hub](./media/quickstarts/start-monitoring-iothub-events.png)
    
W oknie **dane wyjściowe** zostanie wyświetlony następujący komunikat:

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

Zwróć uwagę na następujące szczegóły:

* Komunikat zawiera `body` sekcję i `applicationProperties` sekcję. Aby uzyskać więcej informacji, zobacz [Tworzenie i odczytywanie wiadomości IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).
* W `applicationProperties` programie `subject` odwołuje się do węzła, `MediaGraph` z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z procesora wykrywania ruchu.
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie jest zdarzeniem analizy.
* `eventTime`Wartość to godzina wystąpienia zdarzenia.
* `body`Sekcja zawiera dane dotyczące zdarzenia analizy. W takim przypadku zdarzenie jest zdarzeniem wnioskowania, dlatego treść zawiera `timestamp` i `inferences` dane.
* `inferences`Sekcja wskazuje, że `type` jest `motion` . Zawiera dodatkowe dane dotyczące `motion` zdarzenia.

Jeśli zezwolisz na uruchamianie grafu multimediów przez pewien czas, zobaczysz następujący komunikat w oknie **danych wyjściowych** .

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

W tym komunikacie Zwróć uwagę na następujące informacje:

* W `applicationProperties` programie `subject` wskazuje, że komunikat został wygenerowany z węzła źródła RTSP na grafie nośnika.
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie jest diagnostyczne.
* `body`Zawiera dane dotyczące zdarzenia diagnostycznego. W takim przypadku komunikat zawiera treść, ponieważ jest to zdarzenie `MediaSessionEstablished` .

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Wywołaj dodatkowe metody bezpośrednie do oczyszczenia

Wywołaj metody bezpośrednie, aby najpierw dezaktywować wystąpienie grafu, a następnie usunąć je.

### <a name="invoke-graphinstancedeactivate"></a>Wywołaj GraphInstanceDeactivate

Wywoływanie metody bezpośredniej przy `GraphInstanceDeactivate` użyciu następującego ładunku.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

W ciągu kilku sekund w oknie **danych wyjściowych** zostanie wyświetlona następująca odpowiedź:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Kod stanu 200 wskazuje, że wystąpienie grafu zostało pomyślnie zdezaktywowane.

Następnie spróbuj wywołać `GraphInstanceGet` w sposób opisany wcześniej w tym artykule. Obserwuj `state` wartość.

### <a name="invoke-graphinstancedelete"></a>Wywołaj GraphInstanceDelete

Wywoływanie metody bezpośredniej przy `GraphInstanceDelete` użyciu następującego ładunku.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

W ciągu kilku sekund w oknie **danych wyjściowych** zostanie wyświetlona następująca odpowiedź:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Kod stanu 200 wskazuje, że wystąpienie grafu zostało pomyślnie usunięte.

### <a name="invoke-graphtopologydelete"></a>Wywołaj GraphTopologyDelete

Wywoływanie metody bezpośredniej przy `GraphTopologyDelete` użyciu następującego ładunku.

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

W ciągu kilku sekund zostanie wyświetlona następująca odpowiedź w oknie **danych wyjściowych** .

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Kod stanu 200 wskazuje, że topologia grafu została pomyślnie usunięta.

Spróbuj wykonać następujące czynności:

1. Wywołaj `GraphTopologyList` i sprawdź, czy moduł nie zawiera topologii grafu.
1. Wywołaj przy `GraphInstanceList` użyciu tego samego ładunku co `GraphTopologyList` . Zwróć uwagę, że żadne wystąpienia grafu nie są wyliczane.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń zasoby utworzone w tym przewodniku Szybki Start.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak nagrać wideo przy użyciu usługi Live Video Analytics na IoT Edge](continuous-video-recording-tutorial.md).
* Dowiedz się więcej o [komunikatach diagnostycznych](monitoring-logging.md).
