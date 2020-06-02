---
title: Rejestrowanie wideo oparte na zdarzeniach w chmurze i odtwarzanie z samouczka dotyczącego chmury — Azure
description: W ramach tego samouczka nauczysz się korzystać z usługi Analiza filmów wideo na żywo w IoT Edge, aby wykonać rejestrowanie wideo na podstawie zdarzeń w chmurze i odtwarzanie z chmury.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: daab1f06d8950aa7710c7e808ea6362ee3bfd626
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261930"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Samouczek: Rejestrowanie wideo oparte na zdarzeniach w chmurze i odtwarzanie z chmury

W tym samouczku dowiesz się, jak za pomocą usługi Analiza wideo na żywo na IoT Edge selektywnie rejestrować fragmenty wideo na żywo w celu Media Services w chmurze. Ten przypadek użycia jest określany mianem [nagrania wideo opartego na zdarzeniach](event-based-video-recording-concept.md) (EVR) w tym samouczku. Aby to osiągnąć, należy użyć modelu wykrywania obiektów AI do wyszukiwania obiektów w wideo i rejestrowania klipów wideo tylko w przypadku wykrycia określonego typu obiektu. Dowiesz się również, jak odtworzyć zarejestrowane klipy wideo przy użyciu Media Services. Jest to przydatne w przypadku różnych scenariuszy, w których istnieje potrzeba zachowania archiwum interesujących klipów wideo.

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
* [Rejestrowanie wideo oparte na zdarzeniach](event-based-video-recording-concept.md)
<!--* [Quickstart: Event-based recording based on motion events]()-->
* [Samouczek: opracowywanie modułu IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Jak edytować wdrożenie. *. Template. JSON](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Sekcja [Zadeklaruj trasy w IoT Edge manifeście wdrożenia](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące tego samouczka są następujące:

* Zainstaluj [platformę Docker](https://docs.docker.com/desktop/) na komputerze deweloperskim
* [Visual Studio Code](https://code.visualstudio.com/) na komputerze deweloperskim przy użyciu rozszerzenia [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) oraz rozszerzenia [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .

    > [!TIP]
    > Może zostać wyświetlony monit o zainstalowanie platformy Docker. Możesz zignorować ten monit.
* [Zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na komputerze deweloperskim.
* Ukończ [skrypt konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) i [Skonfiguruj środowisko](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

Na końcu powyższych kroków będziesz mieć pewne zasoby platformy Azure wdrożone w ramach subskrypcji platformy Azure, w tym:

* Usługa IoT Hub
* Konto magazynu
* Konto usługi Media Services
* Maszyna wirtualna z systemem Linux

## <a name="concepts"></a>Pojęcia

![Graf multimedialny](./media/event-based-video-recording-tutorial/overview.png)

Nagrywanie wideo oparte na zdarzeniach (EVR) odnosi się do procesu nagrywania wideo wyzwalanego przez zdarzenie. Omawiane zdarzenie może pochodzić z powodu przetwarzania samego sygnału wideo (na przykład w przypadku wykrywania przenoszonego obiektu w filmie wideo) lub z niezależnego źródła (na przykład otwarcie drzwi). Alternatywnie można wyzwolić nagrywanie tylko wtedy, gdy zewnętrzna usługa inferencing wykryje, że wystąpiło określone zdarzenie.  W tym samouczku użyjesz wideo o pojazdach poruszających się na autostradach i nagrywaj klipy wideo po wykryciu wózka.

Na powyższym diagramie znajduje się obrazkowa reprezentacja [grafu multimedialnego](media-graph-concept.md) i dodatkowych modułów, które spełniają żądany scenariusz. Istnieją cztery IoT Edge moduły:

* Analiza wideo na żywo na IoT Edge module
* Moduł AI utworzony przy użyciu [modelu Yolo v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx)
* Niestandardowy moduł do zliczania i filtrowania obiektów (nazywanych licznikiem obiektów na powyższym diagramie), które zostaną skompilowane i wdrożone w tym samouczku
* [Moduł symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) do symulowania aparatu RTSP
    
Jak widać na diagramie, w grafie multimedialnym zostanie użyty węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) do przechwycenia wideo na żywo i wysłania tego wideo do dwóch ścieżek.

* Pierwsza ścieżka jest węzłem [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) , który wyprowadza ramki wideo z określoną szybkością klatek. Te ramki wideo są takie same jak dane wejściowe do węzła rozszerzenia HTTP. Węzeł rozszerzenia HTTP wysyła ramki (jako obrazy) do modułu AI (YOLO v3 — który jest detektorem obiektów) i odbiera wyniki, które będą obiektami wykrytymi przez model. Węzeł rozszerzenia HTTP publikuje następnie wyniki za pośrednictwem ujścia komunikatu IoT Hub do centrum IoT Edge
* Moduł licznika obiektów jest skonfigurowany do odbierania komunikatów z Centrum IoT Edge, które obejmują wyniki wykrywania obiektów (pojazdy w ruchu). Sprawdza komunikaty szukające obiektów określonego typu (skonfigurowanych za pomocą właściwości przędzy) i wyświetla komunikat do IoT Edge centrum. Te komunikaty są następnie przesyłane z powrotem do węzła źródła IoT Hub wykresu multimediów. Po odebraniu komunikatu węzeł źródła IoT Hub na grafie jest wyzwalany przez węzeł [procesora bramy sygnałów](media-graph-concept.md#signal-gate-processor) , aby otworzyć bramę przez skonfigurowany czas. Przepływy wideo przez bramę do węzła ujścia zasobów dla tego czasu trwania. Ta część strumienia na żywo jest następnie rejestrowana za pośrednictwem węzła [ujścia zasobów](media-graph-concept.md#asset-sink) do [zasobu](terminology.md#asset) na koncie usługi Azure Media.

## <a name="set-up-the-environment"></a>Konfigurowanie środowiska

1. Sklonuj repozytorium z tego miejsca https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
2. Uruchom Visual Studio Code (programu vscode) i Otwórz folder, do którego zostanie pobrane repozytorium.
3. W programie programu vscode przejdź do folderu "src/Cloud-to-Device-App" i Utwórz plik o nazwie "appSettings. JSON". Ten plik będzie zawierać ustawienia, które są konieczne do uruchomienia programu.
3. Skopiuj zawartość z pliku CloudDrive/LVA-Sample/appSettings. JSON po wykonaniu skryptu konfiguracji zasobów. Zobacz [#4 wymagań wstępnych](event-based-video-recording-tutorial.md#prerequisites). Po zakończeniu działania skryptu konfiguracji zasobów kliknij nawiasy klamrowe, aby uwidocznić strukturę folderów. Zobaczysz trzy pliki utworzone w ramach przykładu CloudDrive/LVA. Zainteresowani są obecnie plikami. env i element appSetting. JSON. Te pliki będą potrzebne do zaktualizowania plików w Visual Studio Code dalszej części przewodnika Szybki Start. Możesz chcieć skopiować je do pliku lokalnego.

    ![Ustawienia aplikacji](./media/quickstarts/clouddrive.png)

    Tekst z pliku CloudDrive/LVA-Sample/appSettings. JSON powinien wyglądać następująco:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Następnie przejdź do folderu "src/Edge" i Utwórz plik o nazwie "ENV".
1. Skopiuj zawartość z pliku CloudDrive/LVA-Sample/. env. Tekst powinien wyglądać następująco:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```
## <a name="examine-the-template-file"></a>Sprawdzanie pliku szablonu 

Podczas instalacji środowiska zostanie uruchomiony Visual Studio Code i otwarty folder zawierający przykładowy kod.

W Visual Studio Code przejdź do "src/Edge". Zobaczysz plik. env, który został utworzony, a także kilka plików szablonów wdrożenia. Te szablony definiują, które moduły krawędzi zostaną wdrożone na maszynie wirtualnej z systemem Linux. Plik ENV zawiera wartości zmiennych używanych w tych szablonach, takich jak IoT Hub parametry połączenia, które umożliwiają wysyłanie poleceń do modułów brzegowych za pośrednictwem usługi Azure IoT Hub.

Otwórz plik "src/Edge/Deployment. objectCounter. Template. JSON". Należy zauważyć, że w sekcji "moduły" znajdują się cztery wpisy odpowiadające elementom wymienionym powyżej (w sekcji pojęcia):

* lvaEdge — to jest usługa Live Video Analytics na IoT Edge module
* yolov3 — to jest usługa wnioskowania skompilowana przy użyciu modelu YOLO v3
* rtspsim — to jest symulator RTSP
* objectCounter — jest to moduł, który szuka określonych obiektów w komunikatach zwracanych przez yolov3

W przypadku modułu objectCounter Zobacz ciąg używany dla wartości "Image" — jest to zależne od samouczka dotyczącego tworzenia modułu IoT Edge. Visual Studio Code automatycznie rozpozna, że kod modułu licznika obiektów znajduje się w obszarze "src/Edge/moduły/objectCounter". Zapoznaj się z sekcją jak deklarować trasy w IoT Edge manifeście wdrażania, a następnie przeanalizować trasy w pliku JSON szablonu. Zwróć uwagę na to, jak:

* LVAToObjectCounter służy do wysyłania określonych zdarzeń do określonego punktu końcowego w module objectCounter
* ObjectCounterToLVA jest używany do wysyłania zdarzenia wyzwalacza do określonego punktu końcowego (który powinien być IoT Hub węzeł źródłowy) w module lvaEdge
* objectCounterToIoTHub jest używany jako narzędzie do debugowania — Aby ułatwić wyświetlanie danych wyjściowych z objectCounter podczas uruchamiania tego samouczka

> [!NOTE]
> Żądane właściwości modułu objectCounter — są skonfigurowane do wyszukiwania obiektów, które są oznaczone jako "Ciężarówka", o poziomie ufności równym co najmniej 50%.

## <a name="deploy-the-edge-modules"></a>Wdrażanie modułów brzegowych

Korzystając z Visual Studio Code, postępuj zgodnie z instrukcjami, aby zalogować się do platformy Docker i "build and push IoT Edge rozwiązanie", ale Użyj src/Edge/Deployment. objectCounter. Template. JSON dla tego kroku.

![Rozwiązanie do kompilowania i wypychania IoT Edge](./media/event-based-video-recording-tutorial/build-push.png)

Spowoduje to skompilowanie modułu objectCounter do inwentaryzacji obiektów i wypchnięcie obrazu do Azure Container Registry (ACR)

* Sprawdź, czy istnieją zmienne środowiskowe CONTAINER_REGISTRY_USERNAME_myacr i CONTAINER_REGISTRY_PASSWORD_myacr zdefiniowane w pliku ENV

Powyższy krok spowoduje utworzenie manifestu wdrażania IoT Edge w pliku SRC/Edge/config/Deployment. objectCounter. amd64. JSON.

W Visual Studio Code przejdź do pliku SRC/Edge/config/Deployment. objectCounter. amd64. JSON, kliknij prawym przyciskiem myszy plik i wybierz polecenie "Utwórz wdrożenie dla pojedynczego urządzenia". 

Jeśli jest to Twój pierwszy samouczek z funkcją analizy filmów wideo na żywo na IoT Edge, Visual Studio Code wyświetli monit o wprowadzenie parametrów połączenia IoTHub. Można skopiować go z pliku appSettings. JSON.

Następnie Visual Studio Code zostanie zaproszony o wybranie urządzenia IoT Hub. Wybierz urządzenie IoT Edge (powinno być "LVA-Sample-Device").

Na tym etapie wdrożenie modułów brzegowych na urządzeniu IoT Edge zostało rozpoczęte.
W ciągu około 30 sekund Odśwież usługę Azure IOT Hub w lewej dolnej części Visual Studio Code i zobaczysz, że wdrożono 4 moduły (Uwaga: lvaEdge, rtspsim, yolov3 i objectCounter)

![wdrożone 4 moduły](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Przygotowanie do monitorowania zdarzeń

Kliknij prawym przyciskiem myszy urządzenie brzegowe ("LVA-Sample-Device"), a następnie kliknij pozycję "Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia". Usługa Analiza filmów wideo na żywo w module IoT Edge będzie emitować zdarzenia [operacyjne](#operational-events) i [diagnostyczne](#diagnostic-events) do centrum IoT Edge, a zdarzenia te można zobaczyć w oknie "dane wyjściowe" w Visual Studio Code.

## <a name="run-the-program"></a>Uruchamianie programu

1. Visual Studio Code, przejdź do pliku "src/Cloud-to-Device-Console-App/Operations. JSON"
1. W węźle GraphTopologySet ustaw następujące polecenie: "topologyUrl": " https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json " 
1. Następnie w węźle GraphInstanceSet Edytuj "topologyname": "EVRtoAssetsOnObjDetect"
1. Naciśnij klawisz F5. Spowoduje to rozpoczęcie sesji debugowania.
1. W oknie terminalu zobaczysz odpowiedzi na wywołania [metody bezpośredniej](direct-methods.md) wykonywane przez program do analizy filmów wideo na żywo w IoT Edge module, czyli:

    1. GraphTopologyList — pobiera listę topologii grafów, które zostały dodane do modułu, jeśli istnieją

        Naciśnij klawisz ENTER, aby kontynuować
    1. GraphInstanceList — pobiera listę utworzonych wystąpień grafu, jeśli istnieją

        Naciśnij klawisz ENTER, aby kontynuować
    1. GraphTopologySet — dodaje powyższą topologię o nazwie "EVRtoAssetsOnObjDetect" do modułu
    1. GraphInstanceSet — tworzy wystąpienie powyższej topologii, podstawiając parametry
    1. Jest to parametr rtspUrl. Wskazuje plik MKV, który został pobrany na maszynę wirtualną z systemem Linux, do lokalizacji, z której jest odczytywany przez symulator RTSP
    1. GraphInstanceActivate — uruchamia Graf multimedialny, co powoduje przepływ wideo przez
    1. GraphInstanceList — aby pokazać, że masz już wystąpienie w module, który jest uruchomiony

        W tym momencie należy wstrzymywać i *nie* trafiać ENTER
1. W oknie dane wyjściowe zostaną wyświetlone komunikaty operacyjne i diagnostyczne wysyłane do IoT Hub przez usługę Live Video Analytics w IoT Edge module
1. Na wykresie multimedialnym będzie nadal uruchamiany program, a zdarzenia drukowania — symulator, który będzie utrzymywać zapętlenie źródłowego wideo. Aby zatrzymać Graf multimedialny, możesz ponownie nacisnąć klawisz ENTER w oknie terminalu. Program wyśle:

    1. GraphInstanceDeactivate — aby zatrzymać wystąpienie grafu i zatrzymać nagranie wideo
    1. GraphInstanceDelete — aby usunąć wystąpienie z modułu
    1. GraphInstanceList — aby pokazać, że w module nie ma teraz żadnych wystąpień

> [!NOTE]
> Topologia grafu nie została usunięta. Jeśli chcesz to zrobić, Uruchom ten krok z następującą treścią JSON:

```
{
    "@apiVersion" : "1.0",
    "name" : "EVRtoAssetsOnObjDetect"
}
```

## <a name="examine-the-output"></a>Sprawdzanie danych wyjściowych
 
Usługa Analiza filmów wideo na żywo w IoT Edge module emituje zdarzenia [operacyjne](#operational-events) i [diagnostyczne](#diagnostic-events) do centrum IoT Edge, który jest tekstem widocznym w oknie danych wyjściowych Visual Studio Code postępować zgodnie z formatem komunikatów przesyłania strumieniowego dla komunikacji między urządzeniami a chmurą przez IoT Hub:

* Zestaw właściwości aplikacji. Słownik właściwości ciągów, które aplikacja może definiować i uzyskać do nich dostęp, bez konieczności deserializacji treści komunikatu. IoT Hub nigdy nie modyfikuje tych właściwości
* Nieprzezroczysta treść binarna

W poniższych komunikatach właściwości aplikacji i zawartość treści są zdefiniowane przez analizę wideo na żywo w module IoT Edge. Aby uzyskać więcej informacji, zobacz [monitorowanie i rejestrowanie](monitoring-logging.md). 

## <a name="diagnostic-events"></a>Zdarzenia diagnostyczne

### <a name="mediasessionestablished-event"></a>Zdarzenie MediaSessionEstablished 

Po utworzeniu wystąpienia grafu multimedialnego węzeł źródłowy RTSP próbuje połączyć się z serwerem RTSP uruchomionym w kontenerze symulatora RTSP. Jeśli to się powiedzie, spowoduje to wydrukowanie tego zdarzenia. Należy pamiętać, że typ zdarzenia to Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

```
[IoTHubMonitor] [2:02:54 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1589749373980489 1 IN IP4 172.18.0.4\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T21:02:53.981Z",
    "dataVersion": "1.0"
  }
}
```

. Weź pod uwagę następujące kwestie:

* "Podmiot" w applicationProperties odwołuje się do węzła w MediaGraph, z poziomu którego wiadomość została wygenerowana. W takim przypadku komunikat pochodzi z węzła źródłowego RTSP.
* "eventType" w applicationProperties wskazuje, że jest to zdarzenie diagnostyczne
* "eventTime" wskazuje czas wystąpienia zdarzenia.
* "treść" zawiera dane dotyczące zdarzenia diagnostycznego — jest to komunikat SDP

Napisz eventTime — to jest czas, w którym ruch wideo (plik MKV) został uruchomiony do modułu jako strumień na żywo.

## <a name="operational-events"></a>Zdarzenia operacyjne

Gdy wykres multimedialny zostanie uruchomiony przez pewien czas, ostatecznie uzyskasz zdarzenie z modułu licznika obiektów. 

```
[IoTHubMonitor] [2:03:21 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T21:03:21.062Z"
  }
}
```

ApplicationProperties zawiera tylko eventTime, czyli czas, w którym moduł zaobserwowano, że wyniki z modułu YOLO v3 zawierają obiekty zainteresowania (ciężarówki).

Więcej z tych zdarzeń może pojawić się w przypadku wykrycia innych wagonów w filmie wideo.

### <a name="recordingstarted-event"></a>Zdarzenie RecordingStarted

Niemal natychmiast po wysłaniu zdarzenia przez licznik obiektów zobaczysz zdarzenie typu Microsoft. Media. Graph. Operations. RecordingStarted

```
[IoTHubMonitor] [2:03:22 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T21:03:22.532Z",
    "dataVersion": "1.0"
  }
}
```

"Podmiot" w applicationProperties odwołuje się do węzła ujścia zasobów na grafie, który wygenerował tę wiadomość.

Treść zawiera informacje o lokalizacji wyjściowej, która w tym przypadku jest nazwą zasobu usługi Azure Media, do którego jest nagrywane wideo. Należy zwrócić uwagę na tę wartość.

### <a name="recordingavailable-event"></a>Zdarzenie RecordingAvailable

Gdy węzeł ujścia zasobów przekazał wideo do elementu zawartości, emituje to zdarzenie typu Microsoft. Media. Graph. Operations. RecordingAvailable

```
[IoTHubMonitor] [2:03:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T21:03:31.808Z",
    "dataVersion": "1.0"
  }
}
```

To zdarzenie wskazuje, że do zasobu Zapisano wystarczającą ilość danych, aby gracze/klienci mogli inicjować odtwarzanie wideo.

"Podmiot" w applicationProperties odwołuje się do węzła AssetSink na grafie, który wygenerował tę wiadomość.

Treść zawiera informacje o lokalizacji wyjściowej, która w tym przypadku jest nazwą zasobu usługi Azure Media, do którego jest nagrywane wideo.

### <a name="recordingstopped-event"></a>Zdarzenie RecordingStopped

Jeśli sprawdzisz ustawienia aktywacji (maximumActivationTime) dla węzła procesora bramy usługi Signal w topologii), zobaczysz, że brama jest zamykana po upływie 30 sekund od wideo. W ciągu 30 sekund po zdarzeniu RecordingStarted powinno zostać wyświetlone zdarzenie typu Microsoft. Media. Graph. Operations. RecordingStopped, wskazujące, że węzeł ujścia zasobów zatrzymał nagrywanie wideo dla elementu zawartości.

```
[IoTHubMonitor] [2:03:52 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T210321Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T21:03:52.040Z",
    "dataVersion": "1.0"
  }
}
```

To zdarzenie wskazuje, że nagrywanie zostało zatrzymane.

"Podmiot" w applicationProperties odwołuje się do węzła AssetSink na grafie, który wygenerował tę wiadomość.

Treść zawiera informacje o lokalizacji wyjściowej, która w tym przypadku jest nazwą zasobu usługi Azure Media, do którego jest nagrywane wideo.

## <a name="media-services-asset"></a>Media Services zasób  

Możesz przeanalizować Media Services element zawartości, który został utworzony przez Graf, logując się do Azure Portal i wyświetlając wideo.

1. Otwórz przeglądarkę internetową i przejdź do [Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.
1. Znajdź konto Media Services wśród zasobów znajdujących się w Twojej subskrypcji i Otwórz blok konta
1. Kliknij pozycję zasoby na liście Media Services

    ![Elementy zawartości](./media/continuous-video-recording-tutorial/assets.png)
1. Znajdziesz element zawartości o nazwie sampleAssetFromCVR-LVAEdge-{DateTime} — jest to wzorzec nazewnictwa wybrany w pliku topologii programu Media Graph.
1. Kliknij element zawartości.
1. Na stronie Szczegóły zasobu kliknij pole tekstowe **Utwórz nowy** pod adresem URL przesyłania strumieniowego.

    ![Nowy element zawartości](./media/continuous-video-recording-tutorial/new-asset.png)

1. W otwartym Kreatorze Zaakceptuj opcje domyślne i kliknij przycisk "Dodaj". Aby uzyskać więcej informacji, zobacz [odtwarzanie wideo](video-playback-concept.md).

    > [!TIP]
    > Upewnij się, że [punkt końcowy przesyłania strumieniowego jest uruchomiony](../latest/streaming-endpoint-concept.md).
1. Gracz powinien załadować wideo i mieć możliwość nawiązywania **odtwarzania**> * *, aby go wyświetlić.

> [!NOTE]
> Ze względu na to, że źródło wideo było kontenerem symulowania kanału informacyjnego aparatu, sygnatury czasowe w filmie wideo są powiązane z po aktywowaniu wystąpienia wykresu i po jego zdezaktywowaniu. Aby uzyskać więcej informacji, zobacz [odtwarzanie wielu dni](playback-multi-day-recordings-tutorial.md) na temat przeglądania rejestrowania wielodniowego i wyświetlania części tego archiwum. W tym samouczku widoczne są również sygnatury czasowe w wideo wyświetlanym na ekranie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz wypróbowanie innych samouczków, należy zamieścić do utworzonych zasobów. W przeciwnym razie przejdź do obszaru Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten samouczek, i Usuń grupę zasobów.

## <a name="next-steps"></a>Następne kroki

* Użyj [aparatu IP](https://en.wikipedia.org/wiki/IP_camera) z obsługą protokołu RTSP, zamiast korzystać z symulatora RTSP. W celu wyszukania kamer IP z obsługą protokołu RTSP na [stronie ONVIF zgodne produkty](https://www.onvif.org/conformant-products/) można wyszukać urządzenia zgodne z profilami G, S lub T.
* Użyj urządzenia z systemem AMD64 lub x64 (a nie z maszyną wirtualną systemu Linux). To urządzenie musi znajdować się w tej samej sieci co kamera IP. Aby zarejestrować urządzenie w usłudze Azure IoT Hub, można wykonać instrukcje zawarte w temacie [Install Azure IoT Edge Runtime w systemie Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) , a następnie wykonać instrukcje przedstawione w [module Wdróż pierwszy IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) .
