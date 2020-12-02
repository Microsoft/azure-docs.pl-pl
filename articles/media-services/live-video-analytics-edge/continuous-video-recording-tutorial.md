---
title: Ciągłe nagrywanie filmów wideo w chmurze i odtwarzanie z samouczka dotyczącego chmury — Azure
description: W tym samouczku dowiesz się, jak używać usługi Azure Live Video Analytics na Azure IoT Edge, aby ciągle rejestrować wideo w chmurze i przesyłać strumieniowo dowolną część tego filmu wideo przy użyciu Azure Media Services.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: c38ab1f32d1ef4e54cd8568ff17d325fabdefc31
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498374"
---
# <a name="tutorial-continuous-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Samouczek: ciągłe nagrywanie filmów wideo w chmurze i odtwarzanie z chmury

W tym samouczku dowiesz się, jak korzystać z usługi Azure Live Video Analytics na Azure IoT Edge w celu wykonywania [ciągłego nagrywania wideo](continuous-video-recording-concept.md) (CVR) w chmurze i przesyłania strumieniowego dowolnej części tego filmu wideo przy użyciu Azure Media Services. Ta funkcja jest przydatna w scenariuszach takich jak bezpieczeństwo i zgodność, w przypadku których istnieje potrzeba zachowania archiwum materiału z aparatu przez dni lub tygodnie. 

W tym samouczku wykonasz następujące instrukcje:

> [!div class="checklist"]
> * Skonfiguruj odpowiednie zasoby.
> * Przejrzyj kod, który wykonuje CVR.
> * Uruchom przykładowy kod.
> * Sprawdź wyniki i Obejrzyj wideo.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem  

Przeczytaj te artykuły przed rozpoczęciem:

* [Analiza filmów wideo na żywo na IoT Edge przegląd](overview.md)
* [Analiza filmów wideo na żywo na IoT Edge terminologii](terminology.md)
* [Koncepcje grafu multimediów](media-graph-concept.md) 
* [Scenariusze ciągłego nagrywania wideo](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące tego samouczka:

* [Visual Studio Code](https://code.visualstudio.com/) na swoim komputerze deweloperskim przy użyciu [narzędzi usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) i rozszerzeń [języka C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) .

    > [!TIP]
    > Może zostać wyświetlony monit o zainstalowanie platformy Docker. Zignoruj ten monit.
* [Zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na komputerze deweloperskim.
* Ukończ [skrypt instalacyjny zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup).

Po zakończeniu tych kroków będziesz mieć odpowiednie zasoby platformy Azure wdrożone w ramach subskrypcji platformy Azure:

* Azure IoT Hub
* Konto usługi Azure Storage
* Konto Azure Media Services
* Maszyna wirtualna z systemem Linux na platformie Azure, w której zainstalowano [środowisko uruchomieniowe IoT Edge](../../iot-edge/how-to-install-iot-edge.md)

## <a name="concepts"></a>Pojęcia

Jak wyjaśniono w artykule [koncepcja grafu multimediów](media-graph-concept.md) , Graf multimedialny umożliwia definiowanie:

- Gdzie należy przechwytywać multimedia z programu.
- Jak powinien być przetwarzany.
- Miejsce, w którym powinny być dostarczone wyniki. 
 
 Aby wykonać CVR, musisz przechwycić wideo z aparatu z obsługą protokołu RTSP i ciągle zarejestrować go w [Azure Media Services](terminology.md#asset). Ten diagram przedstawia graficzną reprezentację tego wykresu multimedialnego.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg" alt-text="Graf multimedialny":::

W tym samouczku użyjesz jednego modułu krawędzi skompilowanego przy użyciu [serwera multimediów Live555](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) , aby symulować aparat RTSP. Wewnątrz grafu multimediów będziesz używać węzła [źródłowego RTSP](media-graph-concept.md#rtsp-source) , aby uzyskać dynamiczny kanał informacyjny i wysłać go do [węzła ujścia zasobów](media-graph-concept.md#asset-sink), który rejestruje wideo w elemencie zawartości.

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Przed rozpoczęciem upewnij się, że zostały spełnione trzeci punktor w sekcji [wymagania wstępne](#prerequisites). Po zakończeniu działania skryptu konfiguracji zasobów wybierz nawiasy klamrowe, aby uwidocznić strukturę folderów. Zobaczysz kilka plików utworzonych w katalogu ~/CloudDrive/LVA-Sample.

![Ustawienia aplikacji](./media/quickstarts/clouddrive.png)

W tym samouczku znajdują się następujące pliki:

* **~/CloudDrive/LVA-Sample/Edge-Deployment/.env**: zawiera właściwości, których Visual Studio Code używa do wdrożenia modułów na urządzeniu brzegowym.
* **~/clouddrive/lva-sample/appsettings.json**: używany przez Visual Studio Code do uruchamiania przykładowego kodu.

Te pliki będą potrzebne do wykonania następujących czynności:

1. Sklonuj repozytorium z linku w witrynie GitHub https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Uruchom Visual Studio Code i Otwórz folder, do którego zostało pobrane repozytorium.
1. W Visual Studio Code przejdź do folderu src/Cloud-to-Device-Console-App i Utwórz plik o nazwie **appsettings.json**. Ten plik zawiera ustawienia, które są konieczne do uruchomienia programu.
1. Skopiuj zawartość z pliku ~/CloudDrive/LVA-Sample/appsettings.jsna plik. Tekst powinien wyglądać następująco:
    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
    Parametry połączenia IoT Hub umożliwiają używanie Visual Studio Code do wysyłania poleceń do modułów brzegowych za pośrednictwem usługi Azure IoT Hub.
    
1. Następnie przejdź do folderu src/Edge i Utwórz plik o nazwie **. env**.
1. Skopiuj zawartość z pliku ~/CloudDrive/LVA-Sample/Edge-Deployment/.env. Tekst powinien wyglądać następująco:

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

W Visual Studio Code Otwórz pozycję src/Edge/deployment.template.jsna. Ten szablon definiuje, które moduły krawędzi zostaną wdrożone na urządzeniu brzegowym (maszyna wirtualna z systemem Linux Azure). W sekcji **modułów** znajdują się dwa wpisy o następujących nazwach:

* **lvaEdge**: to jest analiza filmów wideo na żywo w IoT Edge module.
* **rtspsim**: to jest symulator RTSP.

Następnie przejdź do folderu src/Cloud-to-Device-Console-App. Tutaj zobaczysz appsettings.jsw utworzonym pliku wraz z kilkoma innymi plikami:

* **C2D-Console-App. csproj**: plik projektu dla Visual Studio Code.
* **operations.js**: ten plik zawiera listę różnych operacji, które mają zostać uruchomione.
* **Program.cs**: przykładowy kod programu, który:
    * Ładuje ustawienia aplikacji.
    * Wywołuje bezpośrednie metody udostępniane przez usługę Live Video Analytics w module IoT Edge. Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie [metod bezpośrednich](direct-methods.md).
    * Wstrzymuje pracę, aby przeanalizować dane wyjściowe z programu w oknie **terminalu** oraz zdarzenia wygenerowane przez moduł w oknie **danych wyjściowych** .
    * Wywołuje bezpośrednie metody czyszczenia zasobów.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrażania IoT Edge 

Manifest wdrożenia definiuje, jakie moduły są wdrażane na urządzeniu brzegowym, oraz ustawienia konfiguracji dla tych modułów. Wykonaj następujące kroki, aby wygenerować manifest z pliku szablonu, a następnie wdróż go na urządzeniu brzegowym.

1. Uruchom program Visual Studio Code.
1. Ustaw parametry połączenia IoT Hub, wybierając ikonę **więcej akcji** obok okienka **Azure IoT Hub** w lewym dolnym rogu. Skopiuj ciąg z pliku SRC/Cloud-to-Device-App/appsettings.jsna plik. 

    ![Ustaw parametry połączenia IoT Hub](./media/quickstarts/set-iotconnection-string.png)
1. Kliknij prawym przyciskiem myszy plik SRC/Edge/deployment.template.jsw pliku, a następnie wybierz polecenie **generuj IoT Edge manifest wdrożenia**. Visual Studio Code używa wartości z pliku ENV do zastępowania zmiennych znalezionych w pliku szablonu wdrożenia. Ta akcja tworzy plik manifestu w folderze src/Edge/config o nazwie **deployment.amd64.json**.

   ![Generowanie manifestu wdrażania IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Kliknij prawym przyciskiem myszy plik SRC/Edge/config/deployment.amd64.jsw pliku, a następnie wybierz pozycję **Utwórz wdrożenie dla jednego urządzenia**.

   ![Utwórz wdrożenie dla pojedynczego urządzenia](./media/quickstarts/create-deployment-single-device.png)
1. Zostanie wyświetlony monit o **wybranie urządzenia IoT Hub**. Z listy rozwijanej wybierz pozycję LVA-Sample-Device.
1. W ciągu około 30 sekund Odśwież IoT Hub platformy Azure w lewej dolnej części. Na urządzeniu brzegowym powinny zostać wdrożone następujące moduły:
    * Analiza wideo na żywo na IoT Edge (Nazwa modułu: **lvaEdge**)
    * Symulator RTSP (Nazwa modułu **rtspsim**)
 
    ![Usługa IoT Hub](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Przygotowanie do monitorowania modułów 

Gdy korzystasz z modułu IoT Edge analizy filmów wideo na żywo, aby nagrać strumień wideo na żywo, wysyła on zdarzenia do IoT Hub. Aby wyświetlić te zdarzenia, wykonaj następujące kroki:

1. Otwórz okienko Eksploratora w Visual Studio Code i Wyszukaj pozycję **Azure IoT Hub** w lewym dolnym rogu.
1. Rozwiń węzeł **urządzenia** .
1. Kliknij prawym przyciskiem myszy plik LVA-Sample-Device i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

    ![Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Uruchamianie programu 

1. W Visual Studio Code Otwórz kartę **rozszerzenia** (lub naciśnij klawisze CTRL + SHIFT + X) i Wyszukaj pozycję Azure IoT Hub.
1. Kliknij prawym przyciskiem myszy i wybierz pozycję **Ustawienia rozszerzenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Ustawienia rozszerzenia":::
1. Wyszukaj i Włącz opcję "Pokaż pełny komunikat".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Pokaż pełny komunikat":::
1. <!--In Visual Studio Code, go-->Przejdź do pozycji src/Cloud-to-Device-App/operations.jsw systemie.
1. W węźle **GraphTopologySet** Edytuj następujące elementy:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json" `
1. Następnie w węzłach **GraphInstanceSet** i **GraphTopologyDelete** upewnij się, że wartość **topologyname** pasuje do wartości właściwości **name** w poprzedniej topologii grafu:

    `"topologyName" : "CVRToAMSAsset"`  
1. Otwórz [topologię](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json) w przeglądarce i zapoznaj się z tematem assetNamePattern. Aby upewnić się, że masz element zawartości o unikatowej nazwie, możesz chcieć zmienić nazwę wystąpienia grafu w operations.jsw pliku (z wartości domyślnej przykład-Graph-1).

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
1. Rozpocznij sesję debugowania, wybierając klawisz F5. Zobaczysz kilka komunikatów wydrukowanych w oknie **terminalu** .
1. operations.jsw pliku rozpoczyna się z wywołaniami do GraphTopologyList i GraphInstanceList. Jeśli wyczyszczono zasoby po poprzednich przewodnikach szybki start lub samouczkach, ta akcja zwróci puste listy, a następnie Wstrzymuje wybieranie **klawisza ENTER**, jak pokazano poniżej:

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

1. Po wybraniu **klawisza ENTER** w oknie **terminalu** zostanie wykonany kolejny zestaw wywołań metody bezpośredniej:
   * Wywołanie GraphTopologySet przy użyciu poprzedniej topologyUrl
   * Wywołanie GraphInstanceSet przy użyciu następującej treści
     
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
   * Wywołanie GraphInstanceActivate do uruchomienia wystąpienia grafu i uruchomienia przepływu wideo
   * Drugie wywołanie GraphInstanceList, aby pokazać, że wystąpienie grafu jest w stanie uruchomienia 
1. Dane wyjściowe w oknie **terminalu** są wstrzymywane teraz po **naciśnięciu klawisza ENTER w celu kontynuowania** monitu. W tej chwili nie zaznaczaj **klawisza ENTER** . Przewiń w górę, aby wyświetlić ładunki odpowiedzi JSON dla wywoływanych metod bezpośrednich.
1. Jeśli teraz przełączysz się do okna **danych wyjściowych** w Visual Studio Code, zobaczysz komunikaty wysyłane do IoT Hub przez analizę wideo na żywo w module IoT Edge.

   Te komunikaty zostały omówione w poniższej sekcji.
1. Wystąpienie grafu będzie nadal działać i nagrać wideo. Symulator RTSP utrzymuje zapętlenie źródłowego wideo. Aby zatrzymać nagrywanie, Wróć do okna **terminalu** i wybierz pozycję **wprowadź**. Kolejna seria wywołań jest wykonywana w celu oczyszczenia zasobów przy użyciu:

   * Wywołanie GraphInstanceDeactivate, aby dezaktywować wystąpienie grafu.
   * Wywołanie GraphInstanceDelete do usunięcia wystąpienia.
   * Wywołanie GraphTopologyDelete do usunięcia topologii.
   * Końcowe wywołanie GraphTopologyList, aby pokazać, że lista jest teraz pusta.

## <a name="interpret-the-results"></a>Interpretacja wyników 

Po uruchomieniu grafu multimediów na żywo analiza filmów wideo w IoT Edge module wysyła do centrum IoT Edge pewne zdarzenia diagnostyczne i operacyjne. Zdarzenia te są wyświetlane w oknie **dane wyjściowe** Visual Studio Code. Zawierają one sekcję treści i sekcję applicationProperties. Aby zrozumieć, co reprezentuje Ta sekcja, zobacz [Tworzenie i odczytywanie wiadomości IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

W poniższych komunikatach właściwości aplikacji i zawartość treści są zdefiniowane przez moduł analizy wideo na żywo.

## <a name="diagnostics-events"></a>Zdarzenia diagnostyki 

### <a name="mediasession-established-event"></a>MediaSession ustalone zdarzenie

Po aktywowaniu wystąpienia grafu węzeł źródłowy RTSP próbuje nawiązać połączenie z serwerem RTSP uruchomionym w module rtspsim. Jeśli to się powiedzie, drukuje to zdarzenie:

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

* Komunikat jest zdarzeniem diagnostycznym (MediaSessionEstablished). Wskazuje, że węzeł źródłowy RTSP (podmiot) ustanowił połączenie z symulatorem RTSP i zaczął odbierać (symulowane) kanały informacyjne na żywo.
* Sekcja subject w applicationProperties odwołuje się do węzła w topologii grafu, z którego został wygenerowany komunikat. W takim przypadku komunikat pochodzi z węzła źródłowego RTSP.
* Sekcja eventType w applicationProperties wskazuje, że jest to zdarzenie diagnostyczne.
* Sekcja eventTime wskazuje czas wystąpienia zdarzenia.
* Sekcja treść zawiera dane dotyczące zdarzenia diagnostycznego, w tym przypadku szczegóły dotyczące [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

## <a name="operational-events"></a>Zdarzenia operacyjne 

### <a name="recordingstarted-event"></a>Zdarzenie RecordingStarted

Gdy węzeł ujścia zasobów zaczyna rejestrować wideo, emituje to zdarzenie typu Microsoft. Media. Graph. Operations. RecordingStarted:

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

Sekcja subject w applicationProperties odwołuje się do węzła ujścia zasobów na grafie, który wygenerował tę wiadomość.

Sekcja treść zawiera informacje o lokalizacji wyjściowej. W tym przypadku jest to nazwa elementu Azure Media Services, do którego jest nagrywane wideo. Zanotuj tę wartość.

### <a name="recordingavailable-event"></a>Zdarzenie RecordingAvailable

Jak sugeruje nazwa, zdarzenie RecordingStarted jest wysyłane po rozpoczęciu rejestrowania, ale dane wideo mogły jeszcze nie zostać przekazane do elementu zawartości. Gdy węzeł ujścia zasobów przesłał dane wideo do elementu zawartości, emituje to zdarzenie typu Microsoft. Media. Graph. Operations. RecordingAvailable:

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

To zdarzenie oznacza, że na potrzeby odtwarzaczy lub klientów zapisało wystarczającą ilość danych, aby rozpocząć odtwarzanie filmu wideo.

Sekcja subject w applicationProperties odwołuje się do węzła AssetSink na grafie, który wygenerował tę wiadomość.

Sekcja treść zawiera informacje o lokalizacji wyjściowej. W tym przypadku jest to nazwa elementu Azure Media Services, do którego jest nagrywane wideo.

### <a name="recordingstopped-event"></a>Zdarzenie RecordingStopped

Po zdezaktywowaniu wystąpienia wykresu węzeł ujścia zasobów przestaje rejestrować wideo do elementu zawartości. Emituje to zdarzenie typu Microsoft. Media. Graph. Operations. RecordingStopped:

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

Sekcja subject w applicationProperties odwołuje się do węzła AssetSink na grafie, który wygenerował tę wiadomość.

Sekcja treść zawiera informacje o lokalizacji wyjściowej, która w tym przypadku jest nazwą elementu Azure Media Services, do którego jest nagrywane wideo.

## <a name="media-services-asset"></a>Media Services zasób  

Możesz przejrzeć Media Services element zawartości, który został utworzony przez Graf multimedialny, logując się do Azure Portal i wyświetlając wideo.

1. Otwórz przeglądarkę internetową i przejdź do [Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.
1. Znajdź konto Media Services wśród zasobów znajdujących się w Twojej subskrypcji i Otwórz okienko konto.
1. Wybierz pozycję **elementy zawartości** z listy **Media Services** .

    ![Zasoby Media Services](./media/continuous-video-recording-tutorial/assets.png)
1. Znajdziesz element zawartości o nazwie sampleAsset-CVRToAMSAsset-Sample-Graph-1. Jest to wzorzec nazewnictwa wybrany w pliku topologii wykresu.
1. Wybierz element zawartości. 
1. Na stronie Szczegóły zasobu wybierz pozycję **Utwórz nowy** w polu tekstowym **adres URL przesyłania strumieniowego** .

    ![Nowy element zawartości](./media/continuous-video-recording-tutorial/new-asset.png)

1. W otwartym Kreatorze Zaakceptuj opcje domyślne i wybierz pozycję **Dodaj**. Aby uzyskać więcej informacji, zobacz [odtwarzanie wideo](video-playback-concept.md).

    > [!TIP]
    > Upewnij się, że [punkt końcowy przesyłania strumieniowego jest uruchomiony](../latest/streaming-endpoint-concept.md).
1. Gracz powinien załadować wideo. Wybierz opcję **Odtwórz** , aby ją wyświetlić.

> [!NOTE]
> Ponieważ źródłem wideo był kontener symulowania kanału informacyjnego aparatu, sygnatury czasowe w filmie wideo są powiązane z momentem aktywowania wystąpienia grafu i po jego zdezaktywowaniu. Aby dowiedzieć się, jak przeglądać multiday nagrywanie i wyświetlić fragmenty tego archiwum, zobacz Odtwarzanie samouczka [dotyczącego wielodniowych nagrań](playback-multi-day-recordings-tutorial.md) . W tym samouczku widoczne są również sygnatury czasowe w wideo wyświetlanym na ekranie.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz wypróbować inne samouczki, zaczekaj na utworzone zasoby. W przeciwnym razie przejdź do obszaru Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten samouczek, i Usuń grupę zasobów.

## <a name="next-steps"></a>Następne kroki

* Użyj [aparatu IP](https://en.wikipedia.org/wiki/IP_camera) z obsługą protokołu RTSP, zamiast korzystać z symulatora RTSP. W celu wyszukania kamer IP z obsługą protokołu RTSP na [stronie ONVIF zgodne produkty](https://www.onvif.org/conformant-products/) można wyszukać urządzenia zgodne z profilami G, S lub T.
* Użyj urządzenia z systemem AMD64 lub x64 (a nie z maszyną wirtualną systemu Linux). To urządzenie musi znajdować się w tej samej sieci co kamera IP. Postępuj zgodnie z instrukcjami w temacie [Install Azure IoT Edge Runtime on Linux](../../iot-edge/how-to-install-iot-edge.md). Następnie postępuj zgodnie z instrukcjami podanymi w obszarze [Wdróż pierwszy IoT Edge module w ramach](../../iot-edge/quickstart-linux.md) szybkiego startu urządzenia z systemem Linux, aby zarejestrować urządzenie w usłudze Azure IoT Hub.