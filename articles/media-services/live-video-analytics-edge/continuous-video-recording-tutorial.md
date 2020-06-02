---
title: Ciągłe nagrywanie filmów wideo w chmurze i odtwarzanie z samouczka w chmurze — Azure
description: W ramach tego samouczka nauczysz się korzystać z funkcji analizy wideo na żywo na IoT Edge, aby ciągle rejestrować wideo w chmurze i przesyłać strumieniowo dowolną część tego wideo przy użyciu Azure Media Services.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: a69d3f5db9dd8cbe25bbf79f44921f26258005cc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261858"
---
# <a name="tutorial-continuous-video-recording-to-cloud-and-playback-from-cloud"></a>Samouczek: ciągłe nagrywanie filmów wideo w chmurze i odtwarzanie z chmury  

W ramach tego samouczka nauczysz się korzystać z usługi Analiza filmów wideo na żywo w IoT Edge, aby wykonywać [stałe nagrywanie wideo](continuous-video-recording-concept.md) (CVR) w chmurze, a także przesyłać strumieniowo dowolną część tego wideo przy użyciu Media Services. Jest to przydatne w przypadku scenariuszy, takich jak bezpieczeństwo, zgodność i inne, gdy istnieje potrzeba utrzymania archiwum materiału z aparatu przez wiele dni (lub tygodnie).

> [!div class="checklist"]
> * Skonfiguruj odpowiednie zasoby
> * Badaj kod, który wykonuje CVR
> * Uruchamianie przykładowego kodu
> * Sprawdź wyniki i Obejrzyj wideo

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem  

Zaleca się zapoznanie się z następującymi stronami dokumentacji

* [Analiza filmów wideo na żywo na IoT Edge przegląd](overview.md)
* [Analiza filmów wideo na żywo na IoT Edge terminologii](terminology.md)
* [Koncepcje grafu multimediów](media-graph-concept.md) 
* [Scenariusze ciągłego nagrywania wideo](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące tego samouczka są następujące:

* [Visual Studio Code](https://code.visualstudio.com/) na komputerze deweloperskim przy użyciu rozszerzenia [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) oraz rozszerzenia [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .

    > [!TIP]
    > Może zostać wyświetlony monit o zainstalowanie platformy Docker. Możesz zignorować ten monit.
* [Zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na komputerze deweloperskim.
* Ukończ [skrypt konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)

Na końcu powyższych kroków będziesz mieć pewne zasoby platformy Azure wdrożone w ramach subskrypcji platformy Azure, w tym:

* Usługa IoT Hub
* Konto magazynu
* Konto Azure Media Services
* Maszyna wirtualna z systemem Linux na platformie Azure z zainstalowanym [IoT Edge środowiska uruchomieniowego](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

## <a name="concepts"></a>Pojęcia

Jak wyjaśniono [tutaj](media-graph-concept.md), Graf multimedialny pozwala określić, gdzie mają być przechwytywane nośniki, jak należy je przetworzyć, i gdzie mają zostać dostarczone wyniki. Aby wykonać CVR, musisz przechwycić wideo z aparatu z obsługą protokołu RTSP i ciągle zarejestrować go w [Azure Media Services](terminology.md#asset). Na poniższym diagramie przedstawiono graficzną reprezentację tego wykresu multimedialnego.

![Graf multimedialny](./media/continuous-video-recording-tutorial/continuous-video-recording-overview.png)

W tym samouczku użyjesz jednego modułu krawędzi skompilowanego przy użyciu [serwera multimediów Live555](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) , aby symulować aparat RTSP. Wewnątrz grafu multimedialnego do pobrania kanału informacyjnego na żywo i wysłania tego wideo do [węzła ujścia zasobów](media-graph-concept.md#asset-sink) zostanie użyty węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) .

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Przed rozpoczęciem Sprawdź, czy w sekcji [wymagania wstępne](#prerequisites)zostały spełnione 3 trzecie. Po zakończeniu działania skryptu konfiguracji zasobów kliknij nawiasy klamrowe, aby uwidocznić strukturę folderów. Zobaczysz kilka plików utworzonych w katalogu ~/CloudDrive/LVA-Sample.

![Ustawienia aplikacji](./media/quickstarts/clouddrive.png)

W tym samouczku przedstawiono następujące zagadnienia:

     * ~/clouddrive/lva-sample/edge-deployment/.env  - contains properties that Visual Studio Code uses to deploy modules to an edge device
     * ~/clouddrive/lva-sample/appsettings.json - used by Visual Studio Code for running the sample code

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

## <a name="examine-the-sample-files"></a>Sprawdzanie plików przykładowych

W Visual Studio Code Otwórz plik "src/Edge/Deployment. Template. JSON". Ten szablon definiuje, które moduły krawędzi zostaną wdrożone na urządzeniu brzegowym (maszyna wirtualna z systemem Linux Azure). Należy zauważyć, że w sekcji "moduły" znajdują się dwa wpisy z następującymi nazwami:

* lvaEdge — to jest usługa Live Video Analytics na IoT Edge module
* rtspsim — to jest symulator RTSP

Następnie przejdź do folderu "src/Cloud-to-Device-App". W tym miejscu zostanie wyświetlony plik appSettings. JSON, który został utworzony wraz z kilkoma innymi plikami:

* C2D-Console-App. csproj — plik projektu dla Visual Studio Code.
* Operations. JSON — ten plik zawiera listę różnych operacji, które należy uruchomić
* Program.cs — przykładowy kod programu, który wykonuje następujące czynności:
    * Ładuje ustawienia aplikacji
    * Wywołuje bezpośrednie metody udostępniane przez usługę Live Video Analytics w module IoT Edge. Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie [metod bezpośrednich](direct-methods.md)
    * Wstrzymuje pracę, aby przeanalizować dane wyjściowe z programu w oknie terminalu i zdarzenia wygenerowane przez moduł w oknie danych wyjściowych.
    * Wywołuje bezpośrednie metody czyszczenia zasobów

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrażania IoT Edge 

Manifest wdrożenia definiuje, jakie moduły są wdrażane na urządzeniu brzegowym, oraz ustawienia konfiguracji dla tych modułów. Wykonaj następujące kroki, aby wygenerować taki manifest z pliku szablonu, a następnie wdróż go na urządzeniu brzegowym.

1. Uruchom program Visual Studio Code.
1. Ustaw parametry połączenia IoTHub, klikając ikonę "więcej akcji" obok okienka AZURE IOT HUB w lewym dolnym rogu. Można skopiować ciąg z pliku SRC/Cloud-to-Device-Console-App/appSettings. JSON. 

    ![Ustaw parametry połączenia IOT](./media/quickstarts/set-iotconnection-string.png)
1. Następnie kliknij prawym przyciskiem myszy plik "src/Edge/Deployment. Template. JSON" i kliknij pozycję "Generuj IoT Edge manifest wdrożenia". Visual Studio Code używa wartości z pliku ENV w celu zastąpienia zmiennych znalezionych w pliku szablonu wdrożenia. Należy utworzyć plik manifestu w folderze src/Edge/config o nazwie "Deployment. amd64. JSON".

   ![Generowanie manifestu wdrażania IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Kliknij prawym przyciskiem myszy pozycję "src/Edge/config/Deployment. amd64. JSON" i kliknij pozycję "Utwórz wdrożenie dla pojedynczego urządzenia".

   ![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/quickstarts/create-deployment-single-device.png)
1. Zostanie wyświetlony monit o wybranie opcji "Wybierz urządzenie IoT Hub". Z listy rozwijanej wybierz pozycję LVA-Sample-Device.
1. W ciągu około 30 sekund Odśwież IoT Hub platformy Azure w lewej dolnej części i zobaczysz, że urządzenie brzegowe ma wdrożone następujące moduły:
    * Analiza wideo na żywo na IoT Edge (Nazwa modułu: "lvaEdge")
    * Symulator RTSP (Nazwa modułu: "rtspsim")
 
    ![Centrum IoT](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Przygotowanie do monitorowania modułów 

Gdy korzystasz z usługi Analiza filmów wideo na żywo w IoT Edge module, aby nagrać strumień wideo na żywo, wyśle zdarzenia do IoT Hub. Aby wyświetlić te zdarzenia, wykonaj następujące kroki:

1. Otwórz okienko Eksploratora w Visual Studio Code i Znajdź IoT Hub platformy Azure w lewym dolnym rogu.
1. Rozwiń węzeł urządzenia.
1. Kliknij prawym przyciskiem myszy pozycję LVA-Sample-Device i wybierz opcję "Rozpocznij monitorowanie wbudowanego monitorowania zdarzeń".

    ![Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Uruchamianie programu 

1. Visual Studio Code, przejdź do pliku "src/Cloud-to-Device-Console-App/Operations. JSON"
1. W węźle GraphTopologySet Edytuj następujące elementy:

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json" `
1. Następnie w obszarze węzły GraphInstanceSet i GraphTopologyDelete upewnij się, że wartość topologyname pasuje do wartości właściwości "name" w powyższej topologii wykresu:

    `"topologyName" : "CVRToAMSAsset"`  
1. Otwórz [topologię](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json) w przeglądarce i zapoznaj się z tematem assetNamePattern. Aby upewnić się, że masz zasób o unikatowej nazwie, możesz chcieć zmienić nazwę wystąpienia grafu w pliku Operations. JSON (wartość domyślna to "Sample-Graph-1").

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
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
1. Po naciśnięciu klawisza ENTER w oknie terminalu zostanie wprowadzony następny zestaw wywołań metody bezpośredniej
     * Wywołanie GraphTopologySet przy użyciu topologyUrl powyżej.
     * Wywołanie GraphInstanceSet przy użyciu następującej treści.
     
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
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
1. Dane wyjściowe w oknie terminalu zostaną wstrzymane teraz w wierszu "naciśnij klawisz ENTER, aby kontynuować". W tej chwili nie należy trafiać "Enter". Można przewijać w górę, aby zobaczyć ładunki odpowiedzi JSON dla wywoływanych metod bezpośrednich
1. Jeśli przełączysz się teraz do okna danych wyjściowych w Visual Studio Code, zobaczysz komunikaty wysyłane do IoT Hub za pomocą usługi Live Video Analytics w IoT Edge module.

     * Te komunikaty zostały omówione w poniższej sekcji
1. Wystąpienie grafu będzie nadal działać i nagrać wideo — symulator protokołu RTSP będzie utrzymywać zapętlenie źródłowego wideo. Aby zatrzymać nagrywanie, Wróć do okna terminalu i naciśnij klawisz ENTER. Kolejna seria wywołań jest wykonywana w celu oczyszczenia zasobów:

     * Wywołanie GraphInstanceDeactivate do dezaktywowania wystąpienia grafu
     * Wywołanie GraphInstanceDelete do usunięcia wystąpienia
     * Wywołanie GraphTopologyDelete do usunięcia topologii
     * Końcowe wywołanie GraphTopologyList, aby pokazać, że lista jest teraz pusta

## <a name="interpret-the-results"></a>Interpretacja wyników 

Po uruchomieniu grafu multimediów na żywo analiza filmów wideo w IoT Edge module wysyła do centrum IoT Edge pewne zdarzenia diagnostyczne i operacyjne. Zdarzenia te są wyświetlane w oknie dane wyjściowe Visual Studio Code, które zawierają sekcję "treść" i sekcję "applicationProperties". Aby zrozumieć, co reprezentuje Ta sekcja, Przeczytaj [ten](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artykuł.

W poniższych komunikatach właściwości aplikacji i zawartość treści są zdefiniowane przez moduł analizy wideo na żywo.

## <a name="diagnostic-events"></a>Zdarzenia diagnostyczne 

### <a name="mediasession-established-event"></a>MediaSession ustalone zdarzenie

Po aktywowaniu wystąpienia grafu węzeł źródłowy RTSP próbuje nawiązać połączenie z serwerem RTSP uruchomionym w module rtspsim. Jeśli to się powiedzie, spowoduje to wydrukowanie tego zdarzenia:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* Komunikat jest zdarzeniem diagnostycznym, MediaSessionEstablished, wskazuje, że węzeł źródłowy RTSP (podmiot) mógł nawiązać połączenie z symulatorem RTSP i zaczyna odbierać (symulowane) kanały informacyjne na żywo.
* "Podmiot" w applicationProperties odwołuje się do węzła w topologii grafu, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła źródłowego RTSP.
* "eventType" w applicationProperties wskazuje, że jest to zdarzenie diagnostyczne.
* "eventTime" wskazuje czas wystąpienia zdarzenia.
* "treść" zawiera dane o zdarzeniu diagnostycznym, które w tym przypadku są szczegółami [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

## <a name="operational-events"></a>Zdarzenia operacyjne 

### <a name="recordingstarted-event"></a>Zdarzenie RecordingStarted

Gdy węzeł ujścia zasobów zaczyna rejestrować wideo, emituje to zdarzenie typu Microsoft. Media. Graph. Operations. RecordingStarted

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

"Podmiot" w applicationProperties odwołuje się do węzła ujścia zasobów na grafie, który wygenerował tę wiadomość.

Treść zawiera informacje o lokalizacji wyjściowej, która w tym przypadku jest nazwą zasobu usługi Azure Media, do którego jest nagrywane wideo. Należy zwrócić uwagę na tę wartość.

### <a name="recordingavailable-event"></a>Zdarzenie RecordingAvailable

Jak sugeruje nazwa, zdarzenie RecordingStarted jest wysyłane po rozpoczęciu rejestrowania, ale dane wideo mogły jeszcze nie zostać przekazane do elementu zawartości. Gdy węzeł ujścia zasobów przesłał dane wideo do elementu zawartości, emituje to zdarzenie typu Microsoft. Media. Graph. Operations. RecordingAvailable

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

To zdarzenie wskazuje, że do zasobu Zapisano wystarczającą ilość danych, aby gracze/klienci mogli inicjować odtwarzanie wideo.

"Podmiot" w applicationProperties odwołuje się do węzła AssetSink na grafie, który wygenerował tę wiadomość.

Treść zawiera informacje o lokalizacji wyjściowej, która w tym przypadku jest nazwą zasobu usługi Azure Media, do którego jest nagrywane wideo.

### <a name="recordingstopped-event"></a>Zdarzenie RecordingStopped

Po zdezaktywowaniu wystąpienia grafu węzeł ujścia zasobów przestaje rejestrować wideo do zasobu, emituje to zdarzenie typu Microsoft. Media. Graph. Operations. RecordingStopped.

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

To zdarzenie wskazuje, że nagrywanie zostało zatrzymane.

"Podmiot" w applicationProperties odwołuje się do węzła AssetSink na grafie, który wygenerował tę wiadomość.

Treść zawiera informacje o lokalizacji wyjściowej, która w tym przypadku jest nazwą zasobu usługi Azure Media, do którego jest nagrywane wideo.

## <a name="media-services-asset"></a>Media Services zasób  

Możesz przejrzeć Media Services element zawartości, który został utworzony przez Graf multimedialny, logując się do Azure Portal i wyświetlając wideo.

1. Otwórz przeglądarkę internetową i przejdź do [Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.
1. Znajdź konto Media Services wśród zasobów znajdujących się w Twojej subskrypcji i Otwórz blok konta
1. Kliknij pozycję zasoby na liście Media Services

    ![Elementy zawartości](./media/continuous-video-recording-tutorial/assets.png)
1. Znajdziesz element zawartości o nazwie sampleAsset-CVRToAMSAsset-Sample-Graph-1 — jest to wzorzec nazewnictwa wybrany w pliku topologii grafu.
1. Kliknij element zawartości.
1. Na stronie Szczegóły zasobu kliknij pole tekstowe **Utwórz nowy** pod adresem URL przesyłania strumieniowego.

    ![Nowy element zawartości](./media/continuous-video-recording-tutorial/new-asset.png)

1. W otwartym Kreatorze Zaakceptuj opcje domyślne i kliknij przycisk "Dodaj". Aby uzyskać więcej informacji, zobacz [odtwarzanie wideo](video-playback-concept.md).

    > [!TIP]
    > Upewnij się, że [punkt końcowy przesyłania strumieniowego jest uruchomiony](../latest/streaming-endpoint-concept.md).
1. Gracz powinien załadować wideo i mieć możliwość nawiązywania **odtwarzania**> * *, aby go wyświetlić.

> [!NOTE]
> Ze względu na to, że źródło wideo było kontenerem symulowania kanału informacyjnego aparatu, sygnatury czasowe w filmie wideo są powiązane z po aktywowaniu wystąpienia wykresu i po jego zdezaktywowaniu. [Ten](playback-multi-day-recordings-tutorial.md) samouczek jest widoczny, aby zobaczyć, jak przeglądać nagrywanie wielodniowe i wyświetlić fragmenty tego archiwum. W tym samouczku zobaczysz również sygnatury czasowe w wideo wyświetlanym na ekranie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz wypróbowanie innych samouczków, należy zamieścić do utworzonych zasobów. W przeciwnym razie przejdź do obszaru Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten samouczek, i Usuń grupę zasobów.

## <a name="next-steps"></a>Następne kroki

* Użyj [aparatu IP](https://en.wikipedia.org/wiki/IP_camera) z obsługą protokołu RTSP, zamiast korzystać z symulatora RTSP. W celu wyszukania kamer IP z obsługą protokołu RTSP na [stronie ONVIF zgodne produkty](https://www.onvif.org/conformant-products/) można wyszukać urządzenia zgodne z profilami G, S lub T.
* Użyj urządzenia z systemem AMD64 lub x64 (a nie z maszyną wirtualną systemu Linux). To urządzenie musi znajdować się w tej samej sieci co kamera IP. Aby zarejestrować urządzenie w usłudze Azure IoT Hub, można wykonać instrukcje zawarte w temacie [Install Azure IoT Edge Runtime w systemie Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) , a następnie wykonać instrukcje przedstawione w [module Wdróż pierwszy IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) .
