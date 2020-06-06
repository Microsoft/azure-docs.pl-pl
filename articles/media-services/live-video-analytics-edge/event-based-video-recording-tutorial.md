---
title: Rejestrowanie wideo oparte na zdarzeniach w chmurze i odtwarzanie z samouczka dotyczącego chmury — Azure
description: W ramach tego samouczka nauczysz się korzystać z usługi Analiza filmów wideo na żywo w IoT Edge, aby wykonać rejestrowanie wideo na podstawie zdarzeń w chmurze i odtwarzanie z chmury.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 7ff8502688e2b507b8a576c177948f29c2248be4
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456639"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Samouczek: Rejestrowanie wideo oparte na zdarzeniach w chmurze i odtwarzanie z chmury

W tym samouczku dowiesz się, jak za pomocą usługi Analiza wideo na żywo na IoT Edge selektywnie rejestrować fragmenty wideo na żywo w celu Media Services w chmurze. Ten przypadek użycia jest określany mianem [nagrania wideo opartego na zdarzeniach](event-based-video-recording-concept.md) (EVR) w tym samouczku. Aby to osiągnąć, należy użyć modelu wykrywania obiektów AI do wyszukiwania obiektów w wideo i rejestrowania klipów wideo tylko w przypadku wykrycia określonego typu obiektu. Dowiesz się również, jak odtworzyć zarejestrowane klipy wideo przy użyciu Media Services. Jest to przydatne w różnych scenariuszach, w których istnieje potrzeba zachowania archiwum interesujących klipów wideo.

> [!div class="checklist"]
> * Skonfiguruj odpowiednie zasoby
> * Badaj kod, który wykonuje EVR
> * Uruchamianie przykładowego kodu
> * Sprawdź wyniki i Obejrzyj wideo

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem  

Zaleca się zapoznanie się z następującymi stronami dokumentacji

* [Analiza filmów wideo na żywo na IoT Edge przegląd](overview.md)
* [Analiza filmów wideo na żywo na IoT Edge terminologii](terminology.md)
* [Koncepcje grafu multimediów](media-graph-concept.md) 
* [Nagrywanie wideo oparte na zdarzeniach](event-based-video-recording-concept.md)
* [Samouczek: opracowywanie modułu IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Jak edytować wdrożenie. *. Template. JSON](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Sekcja [Zadeklaruj trasy w IoT Edge manifeście wdrożenia](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące tego samouczka są następujące:

* [Visual Studio Code](https://code.visualstudio.com/) na komputerze deweloperskim przy użyciu rozszerzenia [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) oraz rozszerzenia [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .

    > [!TIP]
    > Może zostać wyświetlony monit o zainstalowanie platformy Docker. Możesz zignorować ten monit.
* [Zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na komputerze deweloperskim.
* Ukończ [skrypt konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) i [Skonfiguruj środowisko](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

Na końcu powyższych kroków będziesz mieć pewne zasoby platformy Azure wdrożone w ramach subskrypcji platformy Azure, w tym:

* Usługa IoT Hub
* Konto magazynu
* Konto Azure Media Services
* Maszyna wirtualna z systemem Linux na platformie Azure z zainstalowanym [IoT Edge środowiska uruchomieniowego](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

## <a name="concepts"></a>Pojęcia

Nagrywanie wideo oparte na zdarzeniach (EVR) odnosi się do procesu nagrywania wideo wyzwalanego przez zdarzenie. To zdarzenie może zostać wygenerowane na podstawie przetwarzania samego sygnału wideo (na przykład w przypadku wykrywania przenoszonego obiektu w filmie wideo) lub z niezależnego źródła (na przykład otwierającego drzwi). Alternatywnie można wyzwolić nagrywanie tylko wtedy, gdy usługa inferencing wykryje, że wystąpiło określone zdarzenie.  W tym samouczku użyjesz wideo o pojazdach poruszających się na autostradach i nagrywaj klipy wideo po wykryciu wózka.

![Graf multimedialny](./media/event-based-video-recording-tutorial/overview.png)

Na powyższym diagramie znajduje się obrazkowa reprezentacja [grafu multimedialnego](media-graph-concept.md) i dodatkowych modułów, które spełniają żądany scenariusz. Istnieją cztery IoT Edge moduły:

* Analiza filmów wideo na żywo na IoT Edge module.
* Moduł graniczny uruchamiający model AI za punktem końcowym HTTP. Ten moduł AI korzysta z modelu [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który jest w stanie wykryć wiele typów obiektów.
* Niestandardowy moduł do zliczania i filtrowania obiektów (nazywanych licznikiem obiektów na powyższym diagramie), które zostaną skompilowane i wdrożone w tym samouczku.
* [Moduł symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) do symulowania aparatu RTSP.
    
    Jak widać na diagramie, w grafie multimedialnym zostanie użyty węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) do przechwytywania symulowanego wideo na żywo (ruchu na autostradach) i wysłania tego wideo do dwóch ścieżek.

* Pierwsza ścieżka jest węzłem [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) , który wyprowadza ramki wideo z określoną (zmniejszoną) szybkością klatek. Te ramki wideo są wysyłane do węzła rozszerzenia HTTP, który następnie przekazuje ramki (jako obrazy) do modułu AI (YOLO v3 — czyli detektor obiektów) i odbiera wyniki — czyli obiekty (pojazdy w ruchu) wykryte przez model. Węzeł rozszerzenia HTTP publikuje następnie wyniki za pośrednictwem węzła ujścia komunikatów IoT Hub do centrum IoT Edge.
* Moduł licznika obiektów jest skonfigurowany do odbierania komunikatów z Centrum IoT Edge, które obejmują wyniki wykrywania obiektów (pojazdy w ruchu). Sprawdza te komunikaty, szukając obiektów określonego typu (skonfigurowanych za pomocą ustawienia). Po znalezieniu takiego obiektu ten moduł wysyła komunikat do centrum IoT Edge. Te komunikaty "znalezione obiekty" są następnie kierowane do węzła źródła IoT Hub wykresu multimediów. Po otrzymaniu takiego komunikatu węzeł IoT Hub Source na grafie multimedialnym wyzwala węzeł [procesora bramy sygnalizującej](media-graph-concept.md#signal-gate-processor) , co powoduje, że ten ostatni zostanie otwarty przez skonfigurowany czas. Przepływy wideo przez bramę do węzła ujścia zasobów dla tego czasu trwania. Ta część strumienia na żywo jest następnie rejestrowana za pośrednictwem węzła [ujścia zasobów](media-graph-concept.md#asset-sink) do [zasobu](terminology.md#asset) na koncie usługi Azure Media.

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Przed rozpoczęciem Sprawdź, czy w sekcji [wymagania wstępne](#prerequisites)zostały spełnione 3 trzecie. Po zakończeniu działania skryptu konfiguracji zasobów kliknij nawiasy klamrowe, aby uwidocznić strukturę folderów. Zobaczysz kilka plików utworzonych w katalogu ~/CloudDrive/LVA-Sample.

![Ustawienia aplikacji](./media/quickstarts/clouddrive.png)

W tym samouczku przedstawiono następujące zagadnienia:

* ~/CloudDrive/LVA-Sample/Edge-Deployment/.env-zawiera właściwości, których Visual Studio Code używa do wdrożenia modułów na urządzeniu brzegowym.
* ~/CloudDrive/LVA-Sample/AppSetting.JSON — używany przez Visual Studio Code do uruchamiania przykładowego kodu.

Te pliki będą potrzebne do wykonania poniższych czynności.

1. Sklonuj repozytorium z tego miejsca https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Uruchom Visual Studio Code i Otwórz folder, do którego zostało pobrane repozytorium.
1. W Visual Studio Code przejdź do folderu "src/Cloud-to-Device-App" i Utwórz plik o nazwie "appSettings. JSON". Ten plik będzie zawierać ustawienia, które są konieczne do uruchomienia programu.
1. Skopiuj zawartość z pliku ~/CloudDrive/LVA-Sample/appSettings.JSON. Tekst powinien wyglądać następująco:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    Parametry połączenia IoT Hub umożliwiają używanie Visual Studio Code do wysyłania poleceń do modułów brzegowych za pośrednictwem usługi Azure IoT Hub.
    
1. Następnie przejdź do folderu "src/Edge" i Utwórz plik o nazwie "ENV".
1. Skopiuj zawartość z pliku ~/CloudDrive/LVA-Sample/.env. Tekst powinien wyglądać następująco:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>Sprawdzanie pliku szablonu 

W poprzednim kroku zostanie uruchomiony Visual Studio Code i otwarto folder zawierający przykładowy kod.

W Visual Studio Code przejdź do "src/Edge". Zobaczysz plik. env, który został utworzony, a także kilka plików szablonów wdrożenia. Ten szablon definiuje, które moduły krawędzi zostaną wdrożone na urządzeniu brzegowym (maszyna wirtualna z systemem Linux Azure). Plik ENV zawiera wartości dla zmiennych używanych w tych szablonach, takich jak poświadczenia usługi Media Service.

Otwórz plik "src/Edge/Deployment. objectCounter. Template. JSON". Należy zauważyć, że w sekcji "moduły" znajdują się cztery wpisy odpowiadające elementom wymienionym powyżej (w sekcji pojęcia):

* lvaEdge — to jest usługa Live Video Analytics na IoT Edge module
* yolov3 — jest to moduł AI utworzony przy użyciu modelu YOLO v3
* rtspsim — to jest symulator RTSP
* objectCounter — to jest moduł, który szuka określonych obiektów w wynikach z yolov3

W przypadku modułu objectCounter Zobacz ciąg ($ {MODULEs. objectCounter}) użyty dla wartości "Image" — jest to zależne od [samouczka](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux) dotyczącego tworzenia modułu IoT Edge. Visual Studio Code automatycznie rozpozna, że kod modułu licznika obiektów znajduje się w obszarze "src/Edge/moduły/objectCounter". 

Zapoznaj się z [tą](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) sekcją, jak zadeklarować trasy w manifeście wdrażania IoT Edge, a następnie przeanalizować trasy w pliku JSON szablonu. Zwróć uwagę na to, jak:

* LVAToObjectCounter służy do wysyłania określonych zdarzeń do określonego punktu końcowego w module objectCounter.
* ObjectCounterToLVA jest używany do wysyłania zdarzenia wyzwalacza do określonego punktu końcowego (który powinien być IoT Hub węzeł źródłowy) w module lvaEdge.
* objectCounterToIoTHub jest używany jako narzędzie do debugowania, aby ułatwić wyświetlanie danych wyjściowych z objectCounter podczas uruchamiania tego samouczka.

> [!NOTE]
> Sprawdź żądane właściwości modułu objectCounter, które są skonfigurowane do wyszukiwania obiektów oznaczonych jako "Ciężarówka", o poziomie pewności równym co najmniej 50%.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrażania IoT Edge 

Manifest wdrożenia definiuje, jakie moduły są wdrażane na urządzeniu brzegowym, oraz ustawienia konfiguracji dla tych modułów. Wykonaj następujące kroki, aby wygenerować taki manifest z pliku szablonu, a następnie wdróż go na urządzeniu brzegowym.

Korzystając z Visual Studio Code, postępuj zgodnie z [tymi](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux#build-and-push-your-solution) instrukcjami, aby zalogować się do platformy Docker i "Kompilacja i wypychanie IoT Edge rozwiązanie", ale Użyj src/Edge/Deployment. objectCounter. Template. JSON dla tego kroku.

![Rozwiązanie do kompilowania i wypychania IoT Edge](./media/event-based-video-recording-tutorial/build-push.png)

Spowoduje to skompilowanie modułu objectCounter do inwentaryzacji obiektów i wypchnięcie obrazu do Azure Container Registry (ACR).

* Sprawdź, czy istnieją zmienne środowiskowe CONTAINER_REGISTRY_USERNAME_myacr i CONTAINER_REGISTRY_PASSWORD_myacr zdefiniowane w pliku ENV.

Powyższy krok spowoduje utworzenie manifestu wdrażania IoT Edge w pliku SRC/Edge/config/Deployment. objectCounter. amd64. JSON. Kliknij prawym przyciskiem myszy ten plik i kliknij pozycję "Utwórz wdrożenie dla pojedynczego urządzenia".

![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/quickstarts/create-deployment-single-device.png)

Jeśli jest to Twój pierwszy samouczek z funkcją analizy filmów wideo na żywo na IoT Edge, Visual Studio Code wyświetli monit o wprowadzenie parametrów połączenia IoTHub. Można skopiować go z pliku appSettings. JSON.

Następnie Visual Studio Code zostanie zaproszony o wybranie urządzenia IoT Hub. Wybierz urządzenie IoT Edge (powinno być "LVA-Sample-Device").

Na tym etapie wdrożenie modułów brzegowych na urządzeniu IoT Edge zostało rozpoczęte.
W ciągu około 30 sekund Odśwież IoT Hub platformy Azure w lewej dolnej części Visual Studio Code i zobaczysz, że wdrożono 4 moduły (Uwaga: lvaEdge, rtspsim, yolov3 i objectCounter).

![wdrożone 4 moduły](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Przygotowanie do monitorowania zdarzeń

Aby wyświetlić zdarzenia z modułu licznika obiektów i z analizy wideo na żywo w module IoT Edge, wykonaj następujące kroki:

1. Otwórz okienko Eksploratora w Visual Studio Code i Znajdź IoT Hub platformy Azure w lewym dolnym rogu.
1. Rozwiń węzeł urządzenia.
1. Right-klinkieru na LVA-Sample-Device i wybierz opcję **Rozpocznij monitorowanie wbudowanego monitorowania zdarzeń**.

![Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Uruchamianie programu

1. Visual Studio Code, przejdź do pliku "src/Cloud-to-Device-Console-App/Operations. JSON"

1. W węźle GraphTopologySet Edytuj następujące elementy:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. Następnie w węzłach GraphInstanceSet i GraphTopologyDelete, Edit,

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. Rozpocznij sesję debugowania (naciśnij klawisz F5). Zobaczysz, że niektóre komunikaty są wyświetlane w oknie terminalu.

1. Operacja. JSON jest uruchamiana z wywołaniami do GraphTopologyList i GraphInstanceList. Jeśli wyczyszczono zasoby po poprzednich przewodnikach szybki start lub samouczkach, spowoduje to zwrócenie pustych list, a następnie wstrzymanie pracy z wprowadzeniem, jak pokazano poniżej:

    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "1.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
    1. Po naciśnięciu klawisza ENTER w oknie terminalu zostanie wprowadzony następny zestaw wywołań metody bezpośredniej.
     * Wywołanie GraphTopologySet przy użyciu topologyUrl powyżej.
     * Wywołanie GraphInstanceSet przy użyciu następującej treści.
     
        ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
            "description": "Sample graph description",
            "parameters": [
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
              },
              {
                "name": "rtspUserName",
                "value": "testuser"
              },
              {
                "name": "rtspPassword",
                "value": "testpassword"
              }
            ]
          }
        }
        ```
    
     * Wywołanie GraphInstanceActivate do uruchomienia wystąpienia grafu i rozpoczęcie przepływu wideo
     * Drugie wywołanie GraphInstanceList, aby pokazać, że wystąpienie grafu jest rzeczywiście w stanie uruchomienia
     
1. Dane wyjściowe w oknie terminalu zostaną wstrzymane teraz w wierszu "naciśnij klawisz ENTER, aby kontynuować". W tej chwili nie należy trafiać "Enter". Można przewijać w górę, aby wyświetlić ładunki odpowiedzi JSON dla wywoływanych metod bezpośrednich.

1. Jeśli przełączysz się teraz do okna danych wyjściowych w Visual Studio Code, zobaczysz komunikaty wysyłane do IoT Hub za pomocą usługi Live Video Analytics w IoT Edge module.

     * Te komunikaty zostały omówione w poniższej sekcji.
     
1. Wystąpienie grafu będzie nadal działać i nagrać wideo — symulator RTSP będzie utrzymywać zapętlenie źródłowego wideo. Przejrzyj komunikaty zgodnie z opisem w sekcji poniżej, a następnie, aby zatrzymać wystąpienie, Wróć do okna terminalu i naciśnij klawisz ENTER. Kolejna seria wywołań jest wykonywana w celu oczyszczenia zasobów:

     * Wywołanie GraphInstanceDeactivate do dezaktywowania wystąpienia grafu
     * Wywołanie GraphInstanceDelete do usunięcia wystąpienia
     * Wywołanie GraphTopologyDelete do usunięcia topologii
     * Końcowe wywołanie GraphTopologyList, aby pokazać, że lista jest teraz pusta

## <a name="interpret-the-results"></a>Interpretacja wyników 

Po uruchomieniu grafu multimediów na żywo analiza filmów wideo w IoT Edge module wysyła do centrum IoT Edge pewne zdarzenia diagnostyczne i operacyjne. Zdarzenia te są wyświetlane w oknie dane wyjściowe Visual Studio Code, które zawierają sekcję "treść" i sekcję "applicationProperties". Aby zrozumieć, co reprezentuje Ta sekcja, zobacz [Tworzenie i odczytywanie wiadomości IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

W poniższych komunikatach właściwości aplikacji i zawartość treści są zdefiniowane przez moduł analizy wideo na żywo.

## <a name="diagnostic-events"></a>Zdarzenia diagnostyczne

### <a name="mediasessionestablished-event"></a>Zdarzenie MediaSessionEstablished 

Po utworzeniu wystąpienia grafu multimedialnego węzeł źródłowy RTSP próbuje połączyć się z serwerem RTSP uruchomionym w kontenerze symulatora RTSP. Jeśli to się powiedzie, spowoduje to wydrukowanie tego zdarzenia. Należy pamiętać, że typ zdarzenia to Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* Komunikat jest zdarzeniem diagnostycznym, MediaSessionEstablished, wskazuje, że węzeł źródłowy RTSP (podmiot) mógł nawiązać połączenie z symulatorem RTSP i zaczyna odbierać (symulowane) kanały informacyjne na żywo.

* "Podmiot" w applicationProperties odwołuje się do węzła w topologii grafu, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła źródłowego RTSP.

* "eventType" w applicationProperties wskazuje, że jest to zdarzenie diagnostyczne.

* "eventTime" wskazuje czas, w którym wystąpiło zdarzenie, czyli czas, w którym ruch wideo (plik MKV) został uruchomiony do modułu jako strumień na żywo.

* "treść" zawiera dane o zdarzeniu diagnostycznym, które w tym przypadku są szczegółami [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .


## <a name="operational-events"></a>Zdarzenia operacyjne

Gdy wykres multimedialny zostanie uruchomiony przez pewien czas, ostatecznie uzyskasz zdarzenie z modułu licznika obiektów. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

ApplicationProperties zawiera eventTime, czyli czas, w którym zaobserwowano moduł licznika obiektów, że wyniki z modułu YOLO v3 zawierają obiekty zainteresowania (ciężarówki).

Więcej z tych zdarzeń może pojawić się w przypadku wykrycia innych wagonów w filmie wideo.

### <a name="recordingstarted-event"></a>Zdarzenie RecordingStarted

Niemal natychmiast po wysłaniu zdarzenia przez licznik obiektów zobaczysz zdarzenie typu Microsoft. Media. Graph. Operations. RecordingStarted

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

"Podmiot" w applicationProperties odwołuje się do węzła ujścia zasobów na grafie, który wygenerował tę wiadomość. Treść zawiera informacje o lokalizacji wyjściowej, która w tym przypadku jest nazwą zasobu usługi Azure Media, do którego jest nagrywane wideo. Należy zwrócić uwagę na tę wartość.

### <a name="recordingavailable-event"></a>Zdarzenie RecordingAvailable

Gdy węzeł ujścia zasobów przekazał wideo do elementu zawartości, emituje to zdarzenie typu Microsoft. Media. Graph. Operations. RecordingAvailable

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

To zdarzenie wskazuje, że do zasobu Zapisano wystarczającą ilość danych, aby gracze/klienci mogli inicjować odtwarzanie wideo. "Podmiot" w applicationProperties odwołuje się do węzła AssetSink na grafie, który wygenerował tę wiadomość. Treść zawiera informacje o lokalizacji wyjściowej, która w tym przypadku jest nazwą zasobu usługi Azure Media, do którego jest nagrywane wideo.

### <a name="recordingstopped-event"></a>Zdarzenie RecordingStopped

Jeśli sprawdzisz ustawienia aktywacji (maximumActivationTime) dla węzła procesor bramy sygnałów w [topologii](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), zobaczysz, że brama jest zamykana po upływie 30 sekund od wysłania wideo za pomocą programu. W ciągu 30 sekund po zdarzeniu RecordingStarted powinno zostać wyświetlone zdarzenie typu Microsoft. Media. Graph. Operations. RecordingStopped, wskazujące, że węzeł ujścia zasobów zatrzymał nagrywanie wideo dla elementu zawartości.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

To zdarzenie wskazuje, że nagrywanie zostało zatrzymane. "Podmiot" w applicationProperties odwołuje się do węzła AssetSink na grafie, który wygenerował tę wiadomość. Treść zawiera informacje o lokalizacji wyjściowej, która w tym przypadku jest nazwą zasobu usługi Azure Media, do którego jest nagrywane wideo.

## <a name="media-services-asset"></a>Media Services zasób  

Możesz przeanalizować Media Services element zawartości, który został utworzony przez Graf, logując się do Azure Portal i wyświetlając wideo.

1. Otwórz przeglądarkę internetową i przejdź do [Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.
1. Znajdź konto Media Services wśród zasobów znajdujących się w Twojej subskrypcji i Otwórz blok konta
1. Kliknij pozycję zasoby na liście Media Services.

    ![Elementy zawartości](./media/continuous-video-recording-tutorial/assets.png)
1. Znajdziesz element zawartości o nazwie sampleAssetFromEVR-LVAEdge-{DateTime} — jest to nazwa podana we właściwości outputLocation zdarzenia RecordingStarted. AssetNamePattern w topologii określa, jak ta nazwa została wygenerowana.
1. Kliknij element zawartości.
1. Na stronie Szczegóły zasobu kliknij pole tekstowe **Utwórz nowy** pod adresem URL przesyłania strumieniowego.

    ![Nowy element zawartości](./media/continuous-video-recording-tutorial/new-asset.png)

1. W otwartym Kreatorze Zaakceptuj opcje domyślne i kliknij przycisk "Dodaj". Aby uzyskać więcej informacji, zobacz [odtwarzanie wideo](video-playback-concept.md).

    > [!TIP]
    > Upewnij się, że [punkt końcowy przesyłania strumieniowego jest uruchomiony](../latest/streaming-endpoint-concept.md).
1. Gracz powinien załadować wideo i **mieć możliwość wyświetlania go w celu wyświetlenia** .

> [!NOTE]
> Ze względu na to, że źródło wideo było kontenerem symulowania kanału informacyjnego aparatu, sygnatury czasowe w filmie wideo są powiązane z po aktywowaniu wystąpienia wykresu i po jego zdezaktywowaniu. Jeśli używasz kontrolek odtwarzania wbudowanych w samouczku [odtwarzanie wielodniowego](playback-multi-day-recordings-tutorial.md) , możesz zobaczyć sygnatury czasowe w wideo wyświetlanym na ekranie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz wypróbowanie innych samouczków, należy zamieścić do utworzonych zasobów. W przeciwnym razie przejdź do obszaru Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten samouczek, i Usuń grupę zasobów.

## <a name="next-steps"></a>Następne kroki

* Użyj [aparatu IP](https://en.wikipedia.org/wiki/IP_camera) z obsługą protokołu RTSP, zamiast korzystać z symulatora RTSP. W celu wyszukania kamer IP z obsługą protokołu RTSP na [stronie ONVIF zgodne produkty](https://www.onvif.org/conformant-products/) można wyszukać urządzenia zgodne z profilami G, S lub T.
* Użyj urządzenia z systemem AMD64 lub x64 (a nie z maszyną wirtualną systemu Linux). To urządzenie musi znajdować się w tej samej sieci co kamera IP. Aby zarejestrować urządzenie w usłudze Azure IoT Hub, można wykonać instrukcje zawarte w temacie [Install Azure IoT Edge Runtime w systemie Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) , a następnie wykonać instrukcje przedstawione w [module Wdróż pierwszy IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) .
