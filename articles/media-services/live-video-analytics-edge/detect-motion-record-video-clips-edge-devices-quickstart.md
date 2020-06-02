---
title: Wykrywaj ruch, nagrywaj wideo na urządzeniach brzegowych — Azure
description: W tym przewodniku szybki start przedstawiono sposób korzystania z usługi Analiza filmów wideo na żywo na IoT Edge do analizowania strumieniowego wideo na żywo z (symulowanej) kamery IP, wykrywania ewentualnych ruchów i w razie potrzeby rejestrowania klipu wideo MP4 w lokalnym systemie plików na urządzeniu brzegowym.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: d824870ea95922bbbdbf01cf2c95692522936f85
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262080"
---
# <a name="quickstart-detect-motion-record-video-on-edge-devices"></a>Szybki Start: wykrywanie ruchu, nagrywanie wideo na urządzeniach brzegowych
 
W tym przewodniku szybki start przedstawiono sposób korzystania z usługi Analiza filmów wideo na żywo na IoT Edge do analizowania strumieniowego wideo na żywo z (symulowanej) kamery IP, wykrywania ewentualnych ruchów i w razie potrzeby rejestrowania klipu wideo MP4 w lokalnym systemie plików na urządzeniu brzegowym. Używa ona maszyny wirtualnej platformy Azure jako urządzenia IoT Edge oraz symulowanego strumienia wideo na żywo. Ten artykuł jest oparty na przykładowym kodzie zapisanym w języku C#.

Ten artykuł jest oparty na [tym](detect-motion-emit-events-quickstart.md) przewodniku Szybki Start. 

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) na komputerze z następującymi rozszerzeniami:
    * [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Zestaw .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) zainstalowany w systemie
* Jeśli nie wykonano wcześniej [tego](detect-motion-emit-events-quickstart.md) przewodnika Szybki Start, wykonaj następujące czynności:
     * [Konfigurowanie zasobów platformy Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     * [Konfigurowanie środowiska projektowego](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     * [Generowanie i wdrażanie manifestu wdrażania IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest)
     * [Przygotowanie do monitorowania zdarzeń](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

> [!TIP]
> Podczas instalowania narzędzi platformy Azure IoT może zostać wyświetlony monit o zainstalowanie platformy Docker. Możesz go zignorować.

## <a name="review-the-sample-video"></a>Zapoznaj się z przykładowym wideo
Zgodnie z powyższymi krokami w celu skonfigurowania zasobów platformy Azure, (krótkie) wideo z partii parkingowej zostanie skopiowane na maszynę wirtualną z systemem Linux na platformie Azure używaną jako urządzenie IoT Edge. Ten plik wideo będzie używany do symulowania strumienia na żywo dla tego samouczka.

Możesz użyć aplikacji, takiej jak [VLC Player](https://www.videolan.org/vlc/), uruchomić ją, nacisnąć klawisz Control + N i wkleić [ten](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) link do wideo parkingowej partii, aby rozpocząć odtwarzanie. W przypadku 5-sekundowego znaku biały samochód porusza się w ramach parkingów.

Po wykonaniu poniższych kroków zostanie użyta usługa analizy wideo na żywo na IoT Edge, aby wykryć ten ruch samochodu i nagrać klip wideo, zaczynając od 5-sekundowego znacznika.

## <a name="overview"></a>Omówienie

![overview](./media/quickstarts/overview-qs4.png)

Na powyższym diagramie przedstawiono sposób przepływu sygnałów w tym przewodniku Szybki Start. Moduł krawędzi (szczegółowy [tutaj](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) symuluje kamerę IP obsługującą serwer RTSP. Węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do węzła [procesora wykrywania ruchu](media-graph-concept.md#motion-detection-processor) . Źródło RTSP wysyła te same ramki wideo do węzła [procesora bramy sygnalizującej](media-graph-concept.md#signal-gate-processor) , który pozostanie zamknięty do momentu wyzwolenia przez zdarzenie.

Gdy procesor wykrywania ruchu określa, że ruch jest obecny w filmie wideo, wysyła zdarzenie do węzła procesora bramy sygnału, wyzwalając go. Zostanie otwarta brama dla skonfigurowanego czasu trwania, czyli wysłanie klatek wideo do węzła [ujścia pliku](media-graph-concept.md#file-sink) . Ten węzeł ujścia rejestruje wideo jako plik MP4 w lokalnym systemie plików urządzenia brzegowego w skonfigurowanej lokalizacji.

W tym przewodniku szybki start:

1. Tworzenie i wdrażanie grafu multimediów
1. Interpretacja wyników
1. Oczyszczanie zasobów

## <a name="examine-and-edit-the-sample-files"></a>Sprawdzanie i edytowanie plików przykładowych
W ramach wymagań wstępnych pobrano przykładowy kod do folderu. Uruchom Visual Studio Code i Otwórz folder.

1. W Visual Studio Code przejdź do "src/Edge". Zobaczysz plik ENV, który został utworzony wraz z kilkoma plikami szablonu wdrożenia
    * Szablon wdrożenia odwołuje się do manifestu wdrożenia urządzenia brzegowego z niektórymi wartościami zastępczymi. Plik ENV ma wartości dla tych zmiennych.
1. Następnie przejdź do folderu "src/Cloud-to-Device-App". W tym miejscu zostanie wyświetlony plik appSettings. JSON, który został utworzony wraz z kilkoma innymi plikami:
    * C2D-Console-App. csproj — jest to plik projektu dla Visual Studio Code
    * Operations. JSON — w tym pliku zostaną wystawione różne operacje, które mają być uruchamiane przez program
    * Program.cs — jest to przykładowy kod programu, który wykonuje następujące czynności:

        * Ładuje ustawienia aplikacji
        * Wywołuje bezpośrednie metody udostępniane przez usługę Live Video Analytics w module IoT Edge. Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie [metod bezpośrednich](direct-methods.md) 
        * Wstrzymuje pracę, aby przeanalizować dane wyjściowe z programu w oknie terminalu i zdarzenia wygenerowane przez moduł w oknie danych wyjściowych.
        * Wywołuje bezpośrednie metody czyszczenia zasobów   

1. Wprowadź następujące zmiany do pliku Operations. JSON
    * Zmień łącze do topologii grafu:`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * W obszarze GraphInstanceSet Edytuj nazwę topologii wykresu, aby dopasować ją do wartości podanej powyżej.`"topologyName" : "EVRToFilesOnMotionDetection"`
    * Edytuj również adres URL RTSP, aby wskazywał żądany plik wideo`"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * W obszarze GraphTopologyDelete Edytuj nazwę`"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-status-of-the-modules"></a>Przegląd — sprawdzanie stanu modułów
W kroku [generowanie i wdrażanie manifestu wdrażania IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest) w Visual Studio Code, jeśli rozszerzasz węzeł "LVA-Sample-Device" w obszarze Azure IoT Hub (w lewej dolnej części) powinny zostać wyświetlone następujące wdrożone moduły

    1. Moduł Live Video Analytics o nazwie "lvaEdge"
    1. Moduł o nazwie "rtspsim" symuluje serwer RTSP, działając jako źródło kanału informacyjnego wideo na żywo

        ![Moduły](./media/quickstarts/lva-sample-device-node.png)


## <a name="review---prepare-for-monitoring-events"></a>Przegląd — przygotowanie do monitorowania zdarzeń
Upewnij się, że wykonano kroki [przygotowania do monitorowania zdarzeń](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

![Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Uruchamianie przykładowego programu

1. Rozpocznij sesję debugowania (naciśnij klawisz F5). Zobaczysz, że niektóre komunikaty są wyświetlane w oknie terminalu.
1. Operacja. JSON jest uruchamiana z wywołaniami metod bezpośrednich GraphTopologyList i GraphInstanceList. Jeśli wyczyszczono zasoby po poprzednich przewodnikach Szybki Start, spowoduje to zwrócenie pustych list, a następnie pauzę w celu naciśnięcia klawisza ENTER
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
     * Wywołanie GraphTopologySet przy użyciu topologyUrl powyżej
     * Wywołanie GraphInstanceSet przy użyciu następującej treści
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
     * Wywołanie GraphInstanceActivate do uruchomienia wystąpienia grafu i rozpoczęcie przepływu wideo
     * Drugie wywołanie GraphInstanceList, aby pokazać, że wystąpienie grafu jest rzeczywiście w stanie uruchomienia
1. Dane wyjściowe w oknie terminalu zostaną wstrzymane teraz w wierszu "naciśnij klawisz ENTER, aby kontynuować". W tej chwili nie należy trafiać "Enter". Można przewijać w górę, aby zobaczyć ładunki odpowiedzi JSON dla wywoływanych metod bezpośrednich
1. Jeśli przełączysz się teraz do okna danych wyjściowych w Visual Studio Code, zobaczysz komunikaty wysyłane do IoT Hub za pomocą usługi Live Video Analytics w IoT Edge module.
     * Te komunikaty zostały omówione w poniższej sekcji
1. Graf multimedialny będzie nadal uruchamiany i wyniki drukowania — symulator wideo będzie utrzymywać zapętlenie źródłowego filmu. Aby zatrzymać Graf multimedialny, Wróć do okna terminalu i naciśnij klawisz ENTER. Kolejna seria wywołań jest wykonywana w celu oczyszczenia zasobów:
     * Wywołanie GraphInstanceDeactivate do dezaktywowania wystąpienia grafu
     * Wywołanie GraphInstanceDelete do usunięcia wystąpienia
     * Wywołanie GraphTopologyDelete do usunięcia topologii
     * Końcowe wywołanie GraphTopologyList, aby pokazać, że lista jest teraz pusta

## <a name="interpret-results"></a>Interpretowanie wyników 
Po uruchomieniu grafu multimediów wyniki z węzła procesora czujnika ruchu są wysyłane za pośrednictwem IoT Hub węzła ujścia do IoT Hub. Komunikaty wyświetlane w oknie dane wyjściowe Visual Studio Code zawierają sekcję "treść" i sekcję "applicationProperties". Aby zrozumieć, co reprezentuje Ta sekcja, Przeczytaj [ten](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artykuł.

W poniższych komunikatach właściwości aplikacji i zawartość treści są zdefiniowane przez moduł analizy wideo na żywo.

## <a name="mediasession-established-event"></a>MediaSession ustalone zdarzenie

Po utworzeniu wystąpienia grafu multimedialnego węzeł źródłowy RTSP próbuje połączyć się z serwerem RTSP uruchomionym w kontenerze rtspsim-live555. Jeśli to się powiedzie, spowoduje to wydrukowanie tego zdarzenia:

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

* Komunikat jest zdarzeniem diagnostycznym, MediaSessionEstablished, wskazuje, że węzeł źródłowy RTSP (podmiot) mógł nawiązać połączenie z symulatorem RTSP i zaczyna odbierać (symulowane) kanały informacyjne na żywo.
* "Podmiot" w applicationProperties odwołuje się do węzła w topologii grafu, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła źródłowego RTSP.
* "eventType" w applicationProperties wskazuje, że jest to zdarzenie diagnostyczne.
* "eventTime" wskazuje czas wystąpienia zdarzenia.
* "treść" zawiera dane o zdarzeniu diagnostycznym, które w tym przypadku są szczegółami [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .


## <a name="recording-started-event"></a>Rejestrowanie uruchomionego zdarzenia

Jak wyjaśniono [tutaj](#overview), gdy zostanie wykryte ruchy, węzeł procesora bramy sygnałów jest uaktywniany, a węzeł ujścia plików na grafie multimediów zaczyna pisać plik MP4. Węzeł ujścia plików wysyła zdarzenie operacyjne. Typ jest ustawiany na "Motion", aby wskazać, że jest wynikiem procesora wykrywania ruchu, a eventTime informuje o tym, co nastąpiło przekroczenie czasu (UTC). Poniżej znajduje się przykład:

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

* "podmiot" w applicationProperties odwołuje się do węzła na grafie nośnika, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła ujścia plików.
* "eventType" w applicationProperties wskazuje, że jest to zdarzenie operacyjne.
* "eventTime" wskazuje czas wystąpienia zdarzenia. Należy zauważyć, że ten 5-6 sekund od momentu rozpoczęcia przepływu MediaSessionEstablished i wideo. Odnosi się to do 5-6 drugiego znaku, gdy [samochód rozpoczął przechodzenie](#review-the-sample-video) do partii parkingowej
* "treść" zawiera dane dotyczące zdarzenia operacyjnego, które w tym przypadku to dane "OutputType" i "outputLocation".
* wartość "OutputType" wskazuje, że te informacje dotyczą ścieżki pliku
* "outputLocation" zawiera lokalizację pliku MP4, z poziomu modułu krawędzi

## <a name="recording-stopped-and-available-events"></a>Rejestrowanie zatrzymanych i dostępnych zdarzeń

Jeśli przebadasz właściwości węzła procesor bramy sygnałów w [topologii wykresu](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), zobaczysz, że czasy aktywacji zostały ustawione na 5 sekund. Po otrzymaniu zdarzenia RecordingStarted około 5 sekund spowoduje to wyświetlenie
* Zdarzenie RecordingStopped, wskazujące, że nagranie zostało zatrzymane
* Zdarzenie RecordingAvailable wskazujące, że plik MP4 może być teraz używany do wyświetlania

Te dwa zdarzenia są zwykle emitowane z sekund.

### <a name="playing-back-the-mp4-clip"></a>Odtwarzanie klipu MP4

1. Pliki MP4 są zapisywane w katalogu na urządzeniu brzegowym skonfigurowanym w pliku ENV za pośrednictwem tego klucza — OUTPUT_VIDEO_FOLDER_ON_DEVICE. Jeśli zostanie pozostawiona wartość domyślna, wyniki powinny być w/Home/lvaadmin/Samples/Output/
1. Przejdź do grupy zasobów, Znajdź maszynę wirtualną i Połącz się za pomocą bastionu

    ![Grupa zasobów](./media/quickstarts/resource-group.png)
 
    ![VM](./media/quickstarts/virtual-machine.png)
1. Po zalogowaniu się (przy użyciu poświadczeń, które zostały wygenerowane w [tym](detect-motion-emit-events-quickstart.md#set-up-azure-resources) kroku), w wierszu polecenia przejdź do odpowiedniego katalogu (domyślnie:/Home/lvaadmin/Samples/Output), a następnie Zobacz pliki MP4. Można podłączać [pliki](https://docs.microsoft.com/azure/virtual-machines/linux/copy-files-to-linux-vm-using-scp) do komputera lokalnego i odtwarzać je z powrotem za pośrednictwem programu [VLC Player](https://www.videolan.org/vlc/) lub dowolnego innego odtwarzacza MP4.

    ![Dane wyjściowe](./media/quickstarts/samples-output.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz wypróbować inne Przewodniki Szybki Start, należy zamieścić do utworzonych zasobów. W przeciwnym razie przejdź do obszaru Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten przewodnik Szybki Start, i Usuń wszystkie zasoby.

## <a name="next-steps"></a>Następne kroki

* Uruchom usługę [Analiza filmów wideo na żywo przy użyciu własnego modelu](use-your-model-quickstart.md) — Szybki Start, który pokazuje, jak zastosować AI do kanałów wideo na żywo.
* Zapoznaj się z dodatkowymi wyzwaniami dla zaawansowanych użytkowników:

    * Użyj [aparatu IP](https://en.wikipedia.org/wiki/IP_camera) z obsługą protokołu RTSP, zamiast korzystać z symulatora RTSP. W celu wyszukania kamer IP z obsługą protokołu RTSP na stronie [ONVIF zgodne produkty](https://en.wikipedia.org/wiki/IP_camera) można wyszukać urządzenia zgodne z profilami G, S lub T.
    * Użyj urządzenia z systemem AMD64 lub x64 (a nie z maszyną wirtualną systemu Linux). To urządzenie musi znajdować się w tej samej sieci co kamera IP. Aby zarejestrować urządzenie w usłudze Azure IoT Hub, można wykonać instrukcje zawarte w temacie [Install Azure IoT Edge Runtime w systemie Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) , a następnie wykonać instrukcje przedstawione w [module Wdróż pierwszy IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) .
