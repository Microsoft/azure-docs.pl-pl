---
title: Wykrywanie ruchu & nagrywanie wideo na urządzeniach brzegowych — Azure
description: W tym przewodniku szybki start przedstawiono sposób korzystania z usługi Analiza filmów wideo na żywo na IoT Edge do analizowania strumieniowego wideo na żywo z (symulowanej) kamery IP, wykrywania ewentualnych ruchów i w razie potrzeby rejestrowania klipu wideo MP4 w lokalnym systemie plików na urządzeniu brzegowym.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 14dcc7b298244a1d53a9b820c641ea87c4f9a016
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091865"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Szybki Start: wykrywanie ruchu i nagrywanie filmów wideo na urządzeniach brzegowych
 
W tym przewodniku szybki start przedstawiono sposób korzystania z usługi Analiza filmów wideo na żywo na IoT Edge do analizowania strumieniowego kanału informacyjnego wideo z (symulowanej) kamery IP. Pokazano, jak wykryć, czy występują jakieś ruchy, a jeśli tak, Zapisz klip wideo MP4 w lokalnym systemie plików na urządzeniu brzegowym. Przewodnik Szybki Start używa maszyny wirtualnej platformy Azure jako urządzenia IoT Edge, a także używa symulowanego strumienia wideo na żywo. 

Ten artykuł jest oparty na przykładowym kodzie zapisanym w języku C#. Jest ona oparta na [wykryciu zdarzeń wykrywania ruchu i emisji](detect-motion-emit-events-quickstart.md) — Szybki Start. 

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , jeśli jeszcze go nie masz.
* [Visual Studio Code](https://code.visualstudio.com/), z następującymi rozszerzeniami:
    * [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Jeśli nie ukończono [wykrywania ruchu i emitowania zdarzeń](detect-motion-emit-events-quickstart.md) przewodnika Szybki Start, wykonaj następujące kroki:
     1. [Konfigurowanie zasobów platformy Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     1. [Konfigurowanie środowiska projektowego](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     1. [Generowanie i wdrażanie manifestu wdrażania IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)
     1. [Przygotowanie do monitorowania zdarzeń](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

> [!TIP]
> W przypadku instalowania narzędzi Azure IoT Tools może zostać wyświetlony monit o zainstalowanie platformy Docker. Możesz zignorować ten monit.

## <a name="review-the-sample-video"></a>Zapoznaj się z przykładowym wideo
Po skonfigurowaniu zasobów platformy Azure na potrzeby tego przewodnika Szybki Start do maszyny wirtualnej z systemem Linux na platformie Azure, która jest używana jako urządzenie IoT Edge, zostanie skopiowane krótkie wideo o dużej ilości danych. Ten plik wideo będzie używany do symulowania strumienia na żywo dla tego samouczka.

Otwórz aplikację, taką jak [VLC Media Player](https://www.videolan.org/vlc/), wybierz kombinację klawiszy CTRL + N i wklej [ten link](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) do wideo parkingowej partii, aby rozpocząć odtwarzanie. W przypadku 5-sekundowego znaku biały samochód porusza się w ramach parkingów.

Wykonaj następujące kroki, aby użyć usługi Analiza wideo na żywo na IoT Edge, aby wykryć ruch samochodu i nagrać klip wideo, zaczynając od 5-sekundowego znacznika.

## <a name="overview"></a>Omówienie

![overview](./media/quickstarts/overview-qs4.png)

Na powyższym diagramie przedstawiono sposób przepływu sygnałów w tym przewodniku Szybki Start. [Moduł graniczny](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) symuluje kamerę IP, która hostuje serwer protokołu przesyłania strumieniowego (RTSP) w czasie rzeczywistym. Węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do węzła [procesora wykrywania ruchu](media-graph-concept.md#motion-detection-processor) . Źródło RTSP wysyła te same ramki wideo do węzła [procesora bramy sygnalizującej](media-graph-concept.md#signal-gate-processor) , który pozostanie zamknięty do momentu wyzwolenia przez zdarzenie.

Gdy procesor wykrywania ruchu wykryje ruch wideo, wysyła zdarzenie do węzła procesora bramy sygnału, wyzwalając go. Zostanie otwarta brama dla skonfigurowanego czasu trwania, czyli wysłanie klatek wideo do węzła [ujścia pliku](media-graph-concept.md#file-sink) . Ten węzeł ujścia rejestruje wideo jako plik MP4 w lokalnym systemie plików urządzenia brzegowego. Plik zostanie zapisany w skonfigurowanej lokalizacji.

W tym przewodniku szybki start:

1. Utwórz i Wdróż Graf multimedialny.
1. Interpretuj wyniki.
1. Wyczyść zasoby.

## <a name="examine-and-edit-the-sample-files"></a>Sprawdzanie i edytowanie plików przykładowych
W ramach wymagań wstępnych dotyczących tego przewodnika Szybki Start pobrano przykładowy kod do folderu. Wykonaj następujące kroki, aby przeanalizować i edytować przykładowy kod.

1. W Visual Studio Code przejdź do pozycji *src/Edge*. Zobaczysz plik *ENV* i kilka plików szablonów wdrożenia.

    Szablon wdrożenia odwołuje się do manifestu wdrożenia urządzenia brzegowego, gdzie zmienne są używane dla niektórych właściwości. Plik *ENV* zawiera wartości dla tych zmiennych.
1. Przejdź do folderu *src/Cloud-to-Device-Console-App* . Tutaj zobaczysz *appsettings.js* pliku i kilku innych plików:
    * ***C2D-Console-App. csproj*** — plik projektu dla Visual Studio Code.
    * ***operations.jsna*** liście operacji, które program ma uruchomić.
    * ***Program.cs*** — przykładowy kod programu. Ten kod:

        * Ładuje ustawienia aplikacji.
        * Wywołuje bezpośrednie metody, które są ujawniane w module IoT Edge na żywo. Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie [metod bezpośrednich](direct-methods.md). 
        * Wstrzymuje działanie, aby można było przeanalizować dane wyjściowe programu w oknie **terminalu** i przeanalizować zdarzenia wygenerowane przez moduł w oknie **danych wyjściowych** .
        * Wywołuje bezpośrednie metody czyszczenia zasobów.

1. Edytuj *operations.jsw* pliku:
    * Zmień łącze do topologii grafu:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * W obszarze `GraphInstanceSet` Zmień nazwę topologii wykresu, aby odpowiadała wartości w poprzednim łączu:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`

    * Edytuj adres URL RTSP, aby wskazywał na plik wideo:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`

    * W obszarze `GraphTopologyDelete` Edytuj nazwę:

        `"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-the-modules-status"></a>Przegląd — Sprawdź stan modułów

W kroku [generowanie i wdrażanie manifestu wdrażania IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) w Visual Studio Code rozwiń węzeł **LVA-Sample-Device** w obszarze **Azure IoT Hub** (w lewej dolnej sekcji). Powinny zostać wyświetlone następujące wdrożone moduły:

* Moduł Live Video Analytics o nazwie **lvaEdge**
* Moduł **rtspsim** , który symuluje serwer RTSP, który działa jako źródło kanału informacyjnego wideo na żywo

  ![Moduły](./media/quickstarts/lva-sample-device-node.png)


## <a name="review---prepare-for-monitoring-events"></a>Przegląd — przygotowanie do monitorowania zdarzeń
Upewnij się, że wykonano kroki [przygotowywania do monitorowania zdarzeń](detect-motion-emit-events-quickstart.md#prepare-to-monitor-events).

![Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Uruchamianie przykładowego programu

1. Rozpocznij sesję debugowania, wybierając klawisz F5. Okno **terminalu** drukuje niektóre komunikaty.
1. *operations.jsw* kodzie wywołuje metody bezpośrednie `GraphTopologyList` i `GraphInstanceList` . W przypadku oczyszczenia zasobów po poprzednich przewodnikach szybki start proces ten spowoduje zwrócenie pustych list, a następnie wstrzymanie. Wybierz klawisz ENTER.

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

    W oknie **terminalu** zostanie wyświetlony następny zestaw wywołań metod bezpośrednich:

     * Wywołanie `GraphTopologySet` , które używa`topologyUrl` 
     * Wywołanie `GraphInstanceSet` , które używa następującej treści:

         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Graph",
           "properties": {
             "topologyName": "EVRToFilesOnMotionDetection",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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
     * Wywołanie `GraphInstanceActivate` , które uruchamia wystąpienie grafu i przepływ wideo
     * Drugie wywołanie `GraphInstanceList` , które pokazuje, że wystąpienie grafu jest w stanie uruchomienia
1. Dane wyjściowe w oknie **terminalu** są wstrzymywane na `Press Enter to continue` . Nie zaznaczaj jeszcze klawisza ENTER. Przewiń w górę, aby wyświetlić ładunki odpowiedzi JSON dla wywoływanych metod bezpośrednich.
1. Przejdź do okna **dane wyjściowe** w Visual Studio Code. Zobaczysz komunikat informujący o tym, że usługa Live Video Analytics w IoT Edge module wysyła do centrum IoT Hub. W poniższej sekcji tego przewodnika Szybki Start omówiono te komunikaty.

1. Wykres multimedialny będzie nadal uruchamiany i drukować wyniki. Symulator RTSP utrzymuje zapętlenie źródłowego wideo. Aby zatrzymać Graf multimedialny, Wróć do okna **terminalu** i wybierz klawisz ENTER. 

    Kolejna seria wywołań czyści zasoby:
     * Wywołanie `GraphInstanceDeactivate` dezaktywowania wystąpienia grafu.
     * Wywołanie `GraphInstanceDelete` usuwania wystąpienia.
     * Wywołanie `GraphTopologyDelete` usunięcia topologii.
     * Końcowe wywołanie `GraphTopologyList` pokazujące, że lista jest teraz pusta.

## <a name="interpret-results"></a>Interpretowanie wyników 
Po uruchomieniu grafu multimediów wyniki z węzła procesora wykrywania ruchu przechodzą przez węzeł ujścia IoT Hub do centrum IoT Hub. Komunikaty wyświetlane w oknie **dane wyjściowe** Visual Studio Code zawierają `body` sekcję i `applicationProperties` sekcję. Aby uzyskać więcej informacji, zobacz [Tworzenie i odczytywanie wiadomości IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

W poniższych komunikatach moduł analizy wideo na żywo definiuje właściwości aplikacji i zawartość treści.

### <a name="mediasessionestablished-event"></a>Zdarzenie MediaSessionEstablished

Po utworzeniu wystąpienia grafu nośnika węzeł źródłowy RTSP próbuje połączyć się z serwerem RTSP, który działa w kontenerze rtspsim-live555. Po pomyślnym nawiązaniu połączenia zostanie wydrukowane następujące zdarzenie.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

W poprzednich danych wyjściowych: 

* Komunikat jest zdarzeniem diagnostycznym `MediaSessionEstablished` . Wskazuje, że węzeł źródłowy RTSP (podmiot) ustanowił połączenie z symulatorem RTSP i rozpoczął odbieranie strumieniowego kanału informacyjnego (symulowanego).
* W `applicationProperties` programie `subject` odwołuje się do węzła w topologii grafu, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła źródłowego RTSP.
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie jest zdarzeniem diagnostycznym.
* `eventTime`Wartość to godzina wystąpienia zdarzenia.
* `body`Sekcja zawiera dane dotyczące zdarzenia diagnostyki. W takim przypadku dane obejmują szczegóły [protokołu Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="recordingstarted-event"></a>Zdarzenie RecordingStarted

Po wykryciu ruchu jest uaktywniany węzeł procesora bramy sygnalizujący, a węzeł ujścia pliku na grafie multimedialnym zaczyna zapisywać plik MP4. Węzeł ujścia plików wysyła zdarzenie operacyjne. `type`Jest ustawiona na `motion` , aby wskazać, że jest to wynik z procesora wykrywania ruchu. `eventTime`Wartość to czas UTC, w którym wystąpiło ruch. Aby uzyskać więcej informacji o tym procesie, zobacz sekcję [Przegląd](#overview) w tym przewodniku Szybki Start.

Oto przykład tego komunikatu:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

W powyższym komunikacie: 

* W `applicationProperties` programie `subject` odwołuje się do węzła na grafie nośnika, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła ujścia plików.
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie działa.
* `eventTime`Wartość to godzina wystąpienia zdarzenia. Ten czas wynosi od 5 do 6 sekund po przejściu `MediaSessionEstablished` wideo i po nim. Ten czas odpowiada znakowi 5-do-6-sekundowym, gdy [samochód rozpoczął przechodzenie](#review-the-sample-video) do partii parkingowej.
* `body`Sekcja zawiera dane dotyczące zdarzenia operacyjnego. W takim przypadku dane obejmują `outputType` i `outputLocation` .
* `outputType`Zmienna wskazuje, że te informacje dotyczą ścieżki do pliku.
* `outputLocation`Wartość jest lokalizacją pliku MP4 w module Edge.

### <a name="recordingstopped-and-recordingavailable-events"></a>Zdarzenia RecordingStopped i RecordingAvailable

Jeśli przebadasz właściwości węzła procesor bramy sygnałów w [topologii wykresu](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), zobaczysz, że czasy aktywacji są ustawione na 5 sekund. Przez około 5 sekund od momentu `RecordingStarted` odebrania zdarzenia otrzymujesz następujące informacje:

* `RecordingStopped`Zdarzenie wskazujące, że nagranie zostało zatrzymane.
* `RecordingAvailable`Zdarzenie wskazujące, że plik MP4 może być teraz używany do wyświetlania.

Dwa zdarzenia są zwykle emitowane w ciągu sekund od siebie.

## <a name="play-the-mp4-clip"></a>Odtwórz klip MP4

Pliki MP4 są zapisywane w katalogu na urządzeniu brzegowym skonfigurowanym w pliku *ENV* przy użyciu klucza OUTPUT_VIDEO_FOLDER_ON_DEVICE. Jeśli użyto wartości domyślnej, wyniki powinny znajdować się w folderze */Home/lvaadmin/Samples/Output/* .

Aby odtworzyć klip MP4:

1. Przejdź do grupy zasobów, Znajdź maszynę wirtualną, a następnie połącz się za pomocą usługi Azure bastionu.

    ![Grupa zasobów](./media/quickstarts/resource-group.png)
    
    ![VM](./media/quickstarts/virtual-machine.png)

1. Zaloguj się przy użyciu poświadczeń, które zostały wygenerowane podczas [konfigurowania zasobów platformy Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources). 
1. W wierszu polecenia przejdź do odpowiedniego katalogu. Domyślna lokalizacja to */Home/lvaadmin/Samples/Output*. Pliki MP4 powinny być widoczne w katalogu.

    ![Dane wyjściowe](./media/quickstarts/samples-output.png) 

1. Aby skopiować pliki na komputer lokalny, użyj [bezpiecznego kopiowania (SCP)](../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) . 
1. Odtwórz pliki przy użyciu programu [VLC Media Player](https://www.videolan.org/vlc/) lub dowolnego innego odtwarzacza MP4.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz wypróbować inne Przewodniki Szybki Start, Zachowaj utworzone zasoby. W przeciwnym razie w Azure Portal przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten przewodnik Szybki Start, a następnie usuń wszystkie zasoby.

## <a name="next-steps"></a>Następne kroki

* Postępuj zgodnie z instrukcjami [uruchamiania usługi Live Video Analytics przy użyciu własnego modelu](use-your-model-quickstart.md) — Szybki Start, aby zastosować AI do kanałów wideo na żywo.
* Zapoznaj się z dodatkowymi wyzwaniami dla zaawansowanych użytkowników:

    * Użyj [kamery IP](https://en.wikipedia.org/wiki/IP_camera) , która obsługuje protokół RTSP zamiast korzystania z symulatora RTSP. Kamery IP obsługujące protokół RTSP można znaleźć na stronie [ONVIF zgodne produkty](https://www.onvif.org/conformant-products) . Wyszukaj urządzenia zgodne z profilami G, S lub T.
    * Użyj urządzenia z systemem AMD64 lub x64 zamiast korzystać z maszyny wirtualnej z systemem Linux na platformie Azure. To urządzenie musi znajdować się w tej samej sieci co kamera IP. Postępuj zgodnie z instrukcjami w temacie [Install Azure IoT Edge Runtime on Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Następnie postępuj zgodnie z instrukcjami w temacie [wdrażanie pierwszego modułu IoT Edge na wirtualnym urządzeniu z systemem Linux](../../iot-edge/quickstart-linux.md) w celu zarejestrowania urządzenia w usłudze Azure IoT Hub.
