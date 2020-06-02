---
title: Analizowanie wideo na żywo przy użyciu własnego modelu — Azure
description: W tym przewodniku szybki start nastąpi zastosowanie analizy komputerowej w celu przeanalizowania na żywo kanału informacyjnego wideo z (symulowanej) kamery IP.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 0b502fb4bcfa3a11890167c5ef297463a3c51cdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261972"
---
# <a name="quickstart-analyze-live-video-with-your-own-model"></a>Szybki Start: analizowanie wideo na żywo przy użyciu własnego modelu

W tym przewodniku szybki start pokazano, jak za pomocą usługi Analiza wideo na żywo na IoT Edge przeanalizować dynamiczny kanał informacyjny wideo z (symulowanej) kamery IP przez zastosowanie modelu przetwarzania obrazów w celu wykrycia obiektów. Podzestaw ramek w dynamicznym kanale wideo jest wysyłany do usługi wnioskowania, a jego wyniki są wysyłane do centrum IoT Edge. Używa ona maszyny wirtualnej platformy Azure jako urządzenia IoT Edge oraz symulowanego strumienia wideo na żywo. Ten artykuł jest oparty na przykładowym kodzie zapisanym w języku C#.

Ten artykuł jest oparty na [tym](detect-motion-emit-events-quickstart.md) przewodniku Szybki Start. 

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) na komputerze z następującymi rozszerzeniami:
    * [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Zestaw .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) zainstalowany w systemie
* Jeśli nie wykonano wcześniej [tego](detect-motion-emit-events-quickstart.md) przewodnika Szybki Start, wykonaj następujące czynności:
     * [Konfigurowanie zasobów platformy Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)

> [!TIP]
> Podczas instalowania narzędzi platformy Azure IoT może zostać wyświetlony monit o zainstalowanie platformy Docker. Możesz go zignorować.

## <a name="review-the-sample-video"></a>Zapoznaj się z przykładowym wideo
W ramach powyższych kroków w celu skonfigurowania zasobów platformy Azure do maszyny wirtualnej z systemem Linux na platformie Azure zostanie skopiowany (krótki) film o ruchu drogowym, który jest używany jako urządzenie IoT Edge. Ten plik wideo będzie używany do symulowania strumienia na żywo dla tego samouczka.

Możesz użyć aplikacji, takiej jak [VLC Player](https://www.videolan.org/vlc/), uruchomić ją, nacisnąć klawisz Control + N i wkleić [ten](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) link do filmu wideo, aby rozpocząć odtwarzanie. Zobaczysz, że film jest ruchem na autostradach, z wieloma pojazdami.

Po wykonaniu poniższych kroków zostanie użyta usługa analizy wideo na żywo na IoT Edge do wykrywania obiektów, takich jak pojazdy, osoby itd., i publikowania skojarzonych zdarzeń wnioskowania w centrum IoT Edge.

## <a name="overview"></a>Omówienie

![Omówienie](./media/quickstarts/overview-qs5.png)

Na powyższym diagramie przedstawiono sposób przepływu sygnałów w tym przewodniku Szybki Start. Moduł krawędzi (szczegółowy [tutaj](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) symuluje kamerę IP obsługującą serwer RTSP. Węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do węzła [procesora filtru losy ramek](media-graph-concept.md#frame-rate-filter-processor) . Ten procesor ogranicza szybkość klatek strumienia wideo docierających do węzła [procesora rozszerzenia http](media-graph-concept.md#http-extension-processor) . 

Węzeł rozszerzenia HTTP pełni rolę serwera proxy, konwertując ramki wideo na określony typ obrazu i przekazując obraz przez przejście do innego modułu brzegowego, na którym działa model AI za punktem końcowym HTTP. W tym przykładzie moduł Edge jest tworzony przy użyciu modelu [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który jest w stanie wykryć wiele typów obiektów. Węzeł procesora rozszerzenia HTTP zbiera wyniki wykrywania i publikuje zdarzenia w węźle [ujścia IoT Hub](media-graph-concept.md#iot-hub-message-sink ) , który następnie wysyła te zdarzenia do [Centrum IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

W tym przewodniku szybki start:

1. Utwórz i Wdróż Graf multimedialny.
1. Interpretuj wyniki.
1. Oczyszczenie zasobów.



## <a name="create-and-deploy-the-media-graph"></a>Tworzenie i wdrażanie grafu multimediów
    
### <a name="examine-and-edit-the-sample-files"></a>Sprawdzanie i edytowanie plików przykładowych

W ramach wymagań wstępnych pobrano przykładowy kod do folderu. Uruchom Visual Studio Code i Otwórz folder.

1. W Visual Studio Code przejdź do "src/Edge". Zostanie wyświetlony plik ENV, który został utworzony wraz z kilkoma plikami szablonu wdrożenia.

    * Szablon wdrożenia odwołuje się do manifestu wdrożenia urządzenia brzegowego z niektórymi wartościami zastępczymi. Plik ENV ma wartości dla tych zmiennych.
1. Następnie przejdź do folderu "src/Cloud-to-Device-App". W tym miejscu zostanie wyświetlony plik appSettings. JSON, który został utworzony wraz z kilkoma innymi plikami:

    * C2D-Console-App. csproj — jest to plik projektu dla Visual Studio Code.
    * Operations. JSON — ten plik zawiera listę różnych operacji, które mają być uruchamiane przez program.
    * Program.cs — jest to przykładowy kod programu, który wykonuje następujące czynności:

        * Ładuje ustawienia aplikacji.
        *  Wywołuje bezpośrednie metody udostępniane przez usługę Live Video Analytics w module IoT Edge. Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie [metod bezpośrednich](direct-methods.md) 
        * Wstrzymuje pracę, aby przeanalizować dane wyjściowe z programu w oknie terminalu i zdarzenia wygenerowane przez moduł w oknie danych wyjściowych.
        * Wywołuje bezpośrednie metody czyszczenia zasobów   


1. Wprowadź następujące zmiany do pliku Operations. JSON
    * Zmień łącze do topologii grafu:`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
    * W obszarze GraphInstanceSet Edytuj nazwę topologii wykresu, aby dopasować ją do wartości podanej powyżej.`"topologyName" : "InferencingWithHttpExtension"`
    * W obszarze GraphTopologyDelete Edytuj nazwę`"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrażania IoT Edge

1. Kliknij prawym przyciskiem myszy plik "src/Edge/Deployment. yolov3. Template. JSON" i kliknij polecenie Generuj IoT Edge manifest wdrożenia.

    ![Generowanie manifestu wdrażania IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  
1. Należy utworzyć plik manifestu w folderze src/Edge/config o nazwie "Deployment. yolov3. amd64. JSON".
1. Jeśli [Przewodnik Szybki Start](detect-motion-emit-events-quickstart.md)został wcześniej ukończony, Pomiń ten krok. W przeciwnym razie ustaw parametry połączenia IoTHub, klikając ikonę "więcej akcji" obok okienka AZURE IOT HUB w lewym dolnym rogu. Można skopiować ciąg z pliku appSettings. JSON. (Oto inne zalecane podejście, aby upewnić się, że IoT Hub skonfigurowany w ramach Visual Studio Code za pomocą [polecenia Wybierz IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).
    
    ![IoTHub parametry połączenia](./media/quickstarts/set-iotconnection-string.png)
1. Następnie kliknij prawym przyciskiem myszy pozycję "src/Edge/config/Deployment. yolov3. amd64. JSON" i kliknij pozycję "Utwórz wdrożenie dla pojedynczego urządzenia". 

    ![Utwórz wdrożenie dla pojedynczego urządzenia](./media/quickstarts/create-deployment-single-device.png)
1. Zostanie wyświetlony monit o wybranie urządzenia IoT Hub. Z listy rozwijanej wybierz pozycję LVA-Sample-Device.
1. W ciągu około 30 sekund Odśwież usługę Azure IOT Hub w lewym dolnym rogu, a urządzenie brzegowe powinno być wdrożone przy użyciu następujących modułów:

    1. Moduł Live Video Analytics o nazwie "lvaEdge".
    1. Moduł o nazwie "rtspsim" symuluje serwer RTSP, działając jako źródło kanału informacyjnego wideo na żywo.
    1. Moduł o nazwie "yolov3", który sugeruje nazwę, jest modelem wykrywania obiektów YOLOv3, który ma zastosowanie do obrazów i zwraca wiele klas typów obiektów.
 
        ![Model wykrywania obiektów YOLOv3](./media/quickstarts/yolov3.png)

### <a name="prepare-for-monitoring-events"></a>Przygotowanie do monitorowania zdarzeń

Kliknij prawym przyciskiem myszy urządzenie analizy wideo na żywo, a następnie kliknij pozycję "Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia". Ten krok jest niezbędny do monitorowania zdarzeń IoT Hub i wyświetlania ich w oknie danych wyjściowych Visual Studio Code. 

![Rozpocznij monitorowanie](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Uruchamianie przykładowego programu

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
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "InferencingWithHttpExtension",
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
1. Graf multimedialny będzie nadal uruchamiany i wyniki drukowania — symulator wideo będzie utrzymywać zapętlenie źródłowego filmu. Aby zatrzymać Graf multimedialny, Wróć do okna terminalu i naciśnij klawisz ENTER. Kolejna seria wywołań jest wykonywana w celu oczyszczenia zasobów:
     * Wywołanie GraphInstanceDeactivate do dezaktywowania wystąpienia grafu
     * Wywołanie GraphInstanceDelete do usunięcia wystąpienia
     * Wywołanie GraphTopologyDelete do usunięcia topologii
     * Końcowe wywołanie GraphTopologyList, aby pokazać, że lista jest teraz pusta

## <a name="interpret-results"></a>Interpretowanie wyników

Po uruchomieniu grafu multimediów wyniki z węzła procesora rozszerzenia http są wysyłane za pośrednictwem IoT Hub węzła ujścia do IoT Hub. Komunikaty wyświetlane w oknie dane wyjściowe Visual Studio Code zawierają sekcję "treść" i sekcję "applicationProperties". Aby zrozumieć, co reprezentuje Ta sekcja, Przeczytaj [ten](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) artykuł.

W poniższych komunikatach właściwości aplikacji i zawartość treści są zdefiniowane przez moduł analizy wideo na żywo. 



### <a name="mediasession-established-event"></a>MediaSession ustalone zdarzenie

Po utworzeniu wystąpienia grafu multimedialnego węzeł źródłowy RTSP próbuje połączyć się z serwerem RTSP uruchomionym w kontenerze rtspsim-live55. Jeśli to się powiedzie, spowoduje to wydrukowanie tego zdarzenia. Typem zdarzenia jest Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

W powyższym komunikacie należy zwrócić uwagę na następujące kwestie:
* Komunikat jest zdarzeniem diagnostycznym, MediaSessionEstablished, wskazuje, że węzeł źródłowy RTSP (podmiot) mógł nawiązać połączenie z symulatorem RTSP i zaczyna odbierać (symulowane) kanały informacyjne na żywo.
* "podmiot" w applicationProperties wskazuje, że wiadomość została wygenerowana z węzła źródłowego RTSP na grafie multimediów.
* "eventType" w applicationProperties wskazuje, że jest to zdarzenie diagnostyczne.
* "eventTime" wskazuje czas wystąpienia zdarzenia.
* "treść" zawiera dane o zdarzeniu diagnostycznym, które w tym przypadku są szczegółami [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Zdarzenie wnioskowania

Węzeł procesora rozszerzenia HTTP odbiera wyniki wnioskowania z modułu yolov3 i emituje je za pośrednictwem IoT Hub węzła ujścia jako zdarzenia wnioskowania. W tych zdarzeniach typ jest ustawiany na "jednostka", aby wskazać, że jest to jednostka, taka jak samochód lub ciężarówka, a eventTime informuje o tym, w jakim czasie (UTC) wykryto obiekt. Poniżej znajduje się przykład, w którym wykryto dwa samochody z różnymi poziomami zaufania w tej samej klatce wideo.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-04-23T06:37:16.097Z"
  }
}
```

W powyższych komunikatach należy zwrócić uwagę na następujące kwestie:

* "podmiot" w applicationProperties odwołuje się do węzła w topologii grafu, z którego Wygenerowano komunikat. 
* "eventType" w applicationProperties wskazuje, że jest to zdarzenie analizy.
* "eventTime" wskazuje czas wystąpienia zdarzenia.
* "treść" zawiera dane dotyczące zdarzenia analizy. W tym przypadku zdarzenie jest zdarzeniem wnioskowania, w związku z czym treść zawiera dane dotyczące wnioskowania.
* sekcja "wnioskowanie" wskazuje, że "typ" jest "jednostka" i zawiera dodatkowe dane dotyczące "Entity".

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz wypróbować inne Przewodniki Szybki Start, należy zamieścić do utworzonych zasobów. W przeciwnym razie przejdź do obszaru Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten przewodnik Szybki Start, i Usuń wszystkie zasoby.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dodatkowymi wyzwaniami dla zaawansowanych użytkowników:

* Użyj [aparatu IP](https://en.wikipedia.org/wiki/IP_camera) z obsługą protokołu RTSP, zamiast korzystać z symulatora RTSP. W celu wyszukania kamer IP z obsługą protokołu RTSP na stronie [ONVIF zgodne](https://www.onvif.org/conformant-products/) produkty można wyszukać urządzenia zgodne z profilami G, S lub T.
* Użyj urządzenia z systemem AMD64 lub x64 (a nie z maszyną wirtualną systemu Linux). To urządzenie musi znajdować się w tej samej sieci co kamera IP. Możesz wykonać instrukcje w temacie [Install Azure IoT Edge Runtime w systemie Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) , a następnie postępować zgodnie z instrukcjami podanymi w tym [wdrożeniu pierwszego modułu IoT Edge w ramach](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) szybkiego startu urządzenia z systemem Linux w celu zarejestrowania urządzenia w usłudze Azure IoT Hub.

