---
title: Analizowanie wideo na żywo przy użyciu własnego modelu — Azure
description: W tym przewodniku szybki start nastąpi zastosowanie wizji komputerowej w celu przeanalizowania na żywo kanału informacyjnego wideo z (symulowanej) kamery IP.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: dc8c2d1f0620a92a13cb1f4c0b83c2452f964fd6
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170620"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-model"></a>Szybki Start: analizowanie wideo na żywo przy użyciu własnego modelu

W tym przewodniku szybki start przedstawiono sposób korzystania z usługi Analiza filmów wideo na żywo na IoT Edge do analizowania strumieniowego kanału informacyjnego wideo z (symulowanej) kamery IP. Zobaczysz, jak zastosować model przetwarzania obrazów w celu wykrycia obiektów. Podzestaw ramek w dynamicznym kanale wideo jest wysyłany do usługi wnioskowania. Wyniki są wysyłane do centrum IoT Edge. 

Ten przewodnik Szybki Start używa maszyny wirtualnej platformy Azure jako urządzenia IoT Edge i używa symulowanego strumienia wideo na żywo. Jest on oparty na przykładowym kodzie zapisanym w języku C# i kompiluje się w przewodniku [wykrywania zdarzeń i emisji](detect-motion-emit-events-quickstart.md) . 

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure, które obejmuje aktywną subskrypcję. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , jeśli jeszcze go nie masz.
* [Visual Studio Code](https://code.visualstudio.com/), z następującymi rozszerzeniami:
    * [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Jeśli nie doszło do przewodnika Szybki Start dotyczącego [wykrywania ruchu i emisji](detect-motion-emit-events-quickstart.md) , pamiętaj o [skonfigurowaniu zasobów platformy Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> W przypadku instalowania narzędzi Azure IoT Tools może zostać wyświetlony monit o zainstalowanie platformy Docker. Możesz zignorować monit.

## <a name="review-the-sample-video"></a>Zapoznaj się z przykładowym wideo
Po skonfigurowaniu zasobów platformy Azure do maszyny wirtualnej z systemem Linux na platformie Azure jest kopiowany krótki film wideo dotyczący ruchu drogowego, który jest używany jako urządzenie IoT Edge. Ten przewodnik Szybki Start używa pliku wideo do symulowania strumienia na żywo.

Otwórz aplikację, taką jak [VLC Media Player](https://www.videolan.org/vlc/). Wybierz kombinację klawiszy CTRL + N, a następnie wklej link do [filmu wideo](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) , aby rozpocząć odtwarzanie. Zobaczysz film o wielu pojazdach poruszających się w ruchu drogowym.

W tym przewodniku szybki start użyjesz usługi Analiza filmów wideo na żywo na IoT Edge do wykrywania obiektów, takich jak pojazdy i osoby. Spowoduje to opublikowanie skojarzonych zdarzeń wnioskowania w centrum IoT Edge.

## <a name="overview"></a>Omówienie

![Omówienie](./media/quickstarts/overview-qs5.png)

Ten diagram przedstawia sposób przepływu sygnałów w tym przewodniku Szybki Start. [Moduł graniczny](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) symuluje kamerę IP obsługującą serwer protokołu przesyłania strumieniowego w czasie rzeczywistym (RTSP). Węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do węzła [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) . Ten procesor ogranicza szybkość klatek strumienia wideo, który dociera do węzła [procesora rozszerzenia http](media-graph-concept.md#http-extension-processor) . 

Węzeł rozszerzenia HTTP pełni rolę serwera proxy. Konwertuje ramki wideo na określony typ obrazu. Następnie przekazuje obraz za pośrednictwem REST do innego modułu krawędzi, który uruchamia model AI za punktem końcowym HTTP. W tym przykładzie moduł Edge jest zbudowany przy użyciu modelu [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który może wykryć wiele typów obiektów. Węzeł procesora rozszerzenia HTTP zbiera wyniki wykrywania i publikuje zdarzenia w węźle [ujścia IoT Hub](media-graph-concept.md#iot-hub-message-sink) . Następnie węzeł wysyła te zdarzenia do [centrum IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

W tym przewodniku szybki start:

1. Utwórz i Wdróż Graf multimedialny.
1. Interpretuj wyniki.
1. Wyczyść zasoby.



## <a name="create-and-deploy-the-media-graph"></a>Tworzenie i wdrażanie grafu multimediów
    
### <a name="examine-and-edit-the-sample-files"></a>Sprawdzanie i edytowanie plików przykładowych

W ramach wymagań wstępnych pobrano przykładowy kod do folderu. Wykonaj następujące kroki, aby przejrzeć i edytować przykładowe pliki.

1. W Visual Studio Code przejdź do pozycji *src/Edge*. Zobaczysz plik *ENV* i kilka plików szablonów wdrożenia.

    Szablon wdrożenia odwołuje się do manifestu wdrożenia urządzenia brzegowego. Zawiera ona niektóre wartości zastępcze. Plik *ENV* zawiera wartości dla tych zmiennych.

1. Przejdź do folderu *src/Cloud-to-Device-Console-App* . Tutaj zobaczysz *appsettings.jsw* pliku i kilku innych plikach:

    * ***C2D-Console-App. csproj*** — plik projektu dla Visual Studio Code.
    * ***operations.js*** listę operacji, które program ma uruchomić.
    * ***Program.cs*** — przykładowy kod programu. Ten kod:

        * Ładuje ustawienia aplikacji.
        * Wywołuje bezpośrednie metody, które są ujawniane w module IoT Edge na żywo. Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie [metod bezpośrednich](direct-methods.md).
        * Wstrzymuje działanie, aby można było przeanalizować dane wyjściowe programu w oknie **terminalu** i przeanalizować zdarzenia, które zostały wygenerowane przez moduł w oknie **danych wyjściowych** .
        * Wywołuje bezpośrednie metody czyszczenia zasobów.


1. Edytuj *operations.jsw* pliku:
    * Zmień łącze do topologii grafu:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`

    * W obszarze `GraphInstanceSet` Zmień nazwę topologii wykresu, aby odpowiadała wartości w poprzednim łączu:

      `"topologyName" : "InferencingWithHttpExtension"`

    * W obszarze `GraphTopologyDelete` Edytuj nazwę:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrażania IoT Edge

1. Kliknij prawym przyciskiem myszy plik *src/Edge/deployment.yolov3.template.jsw* pliku, a następnie wybierz polecenie **Generuj IoT Edge manifest wdrożenia**.

    ![Generowanie manifestu wdrażania IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    *deployment.yolov3.amd64.jsw* pliku manifestu jest tworzony w folderze *src/Edge/config* .

1. Jeśli zakończono [wykrywanie ruchu i emitowanie zdarzeń](detect-motion-emit-events-quickstart.md) przewodnika Szybki Start, Pomiń ten krok. 

    W przeciwnym razie obok okienka **Azure IoT Hub** w lewym dolnym rogu wybierz ikonę **więcej akcji** , a następnie wybierz pozycję **Ustaw IoT Hub parametry połączenia**. Można skopiować ciąg z *appsettings.js* pliku. Lub, aby upewnić się, że w ramach Visual Studio Code zostało skonfigurowane odpowiednie Centrum IoT Hub, użyj [polecenia wybierz Centrum IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Określanie parametrów połączenia centrum IoT Hub](./media/quickstarts/set-iotconnection-string.png)

1. Kliknij prawym przyciskiem myszy pozycję *src/Edge/config/deployment.yolov3.amd64.js* , a następnie wybierz pozycję **Utwórz wdrożenie dla jednego urządzenia**. 

    ![Utwórz wdrożenie dla pojedynczego urządzenia](./media/quickstarts/create-deployment-single-device.png)

1. Po wyświetleniu monitu o wybranie urządzenia IoT Hub wybierz pozycję **LVA-Sample-Device**.
1. Po około 30 sekundach w lewym dolnym rogu okna Odśwież IoT Hub platformy Azure. Na urządzeniu brzegowym są teraz wyświetlane następujące wdrożone moduły:

    * Moduł Live Video Analytics o nazwie **lvaEdge**
    * Moduł **rtspsim** , który symuluje serwer RTSP i działa jako źródło kanału informacyjnego wideo na żywo
    * Moduł **yolov3** , który jest modelem wykrywania obiektów yolov3, który ma zastosowanie do obrazów i zwraca wiele klas typów obiektów
 
      ![Moduły wdrożone na urządzeniu brzegowym](./media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Przygotowanie do monitorowania zdarzeń

Kliknij prawym przyciskiem myszy urządzenie analizy wideo na żywo, a następnie wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**. Ten krok jest wymagany do monitorowania zdarzeń IoT Hub w oknie **danych wyjściowych** Visual Studio Code. 

![Rozpocznij monitorowanie](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Uruchamianie przykładowego programu

1. Aby rozpocząć sesję debugowania, wybierz klawisz F5. W oknie **terminalu** są wyświetlane komunikaty.
1. *operations.jsw* kodzie zaczyna się od wywołania metod bezpośrednich `GraphTopologyList` i `GraphInstanceList` . Jeśli wyczyszczono zasoby po ukończeniu poprzednich przewodników Szybki Start, proces ten spowoduje zwrócenie pustych list, a następnie wstrzymanie. Aby kontynuować, wybierz klawisz ENTER.

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

     * Wywołanie `GraphTopologySet` , które używa poprzedniej`topologyUrl`
     * Wywołanie `GraphInstanceSet` , które używa następującej treści:

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

     * Wywołanie `GraphInstanceActivate` , które uruchamia wystąpienie grafu i przepływ wideo
     * Drugie wywołanie `GraphInstanceList` , które pokazuje, że wystąpienie grafu jest w stanie uruchomienia
1. Dane wyjściowe w oknie **terminalu** są wstrzymywane w `Press Enter to continue` wierszu polecenia. Nie zaznaczaj jeszcze klawisza ENTER. Przewiń w górę, aby wyświetlić ładunki odpowiedzi JSON dla wywoływanych metod bezpośrednich.
1. Przejdź do okna **dane wyjściowe** w Visual Studio Code. Zobaczysz komunikat informujący o tym, że usługa Live Video Analytics w IoT Edge module wysyła do centrum IoT Hub. W poniższej sekcji tego przewodnika Szybki Start omówiono te komunikaty.
1. Wykres multimedialny będzie nadal uruchamiany i drukować wyniki. Symulator RTSP utrzymuje zapętlenie źródłowego wideo. Aby zatrzymać Graf multimedialny, Wróć do okna **terminalu** i wybierz klawisz ENTER. 

    Kolejna seria wywołań czyści zasoby:
      * Wywołanie `GraphInstanceDeactivate` dezaktywowania wystąpienia grafu.
      * Wywołanie `GraphInstanceDelete` usuwania wystąpienia.
      * Wywołanie `GraphTopologyDelete` usunięcia topologii.
      * Końcowe wywołanie `GraphTopologyList` pokazujące, że lista jest pusta.

## <a name="interpret-results"></a>Interpretowanie wyników

Po uruchomieniu grafu multimediów wyniki z węzła procesora rozszerzenia HTTP przechodzą przez węzeł ujścia IoT Hub do centrum IoT Hub. Komunikaty widoczne w oknie **danych wyjściowych** zawierają `body` sekcję i `applicationProperties` sekcję. Aby uzyskać więcej informacji, zobacz [Tworzenie i odczytywanie wiadomości IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

W poniższych komunikatach moduł analizy wideo na żywo definiuje właściwości aplikacji i zawartość treści. 

### <a name="mediasessionestablished-event"></a>Zdarzenie MediaSessionEstablished

Po utworzeniu wystąpienia grafu nośnika węzeł źródłowy RTSP próbuje połączyć się z serwerem RTSP, który działa w kontenerze rtspsim-live555. Jeśli połączenie zakończy się pomyślnie, zostanie wydrukowane następujące zdarzenie. Typ zdarzenia to `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished` .

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

W tym komunikacie Zwróć uwagę na następujące informacje:

* Komunikat jest zdarzeniem diagnostycznym. `MediaSessionEstablished`wskazuje, że węzeł źródłowy RTSP (podmiot) połączony z symulatorem RTSP i rozpoczął odbieranie strumieniowego kanału informacyjnego (symulowanego).
* W `applicationProperties` programie `subject` wskazuje, że komunikat został wygenerowany z węzła źródła RTSP na grafie nośnika.
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie jest zdarzeniem diagnostycznym.
* `eventTime`Wskazuje godzinę wystąpienia zdarzenia.
* `body`Zawiera dane dotyczące zdarzenia diagnostyki. W takim przypadku dane obejmują szczegóły [protokołu Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Zdarzenie wnioskowania

Węzeł procesora rozszerzenia HTTP odbiera wyniki wnioskowania z modułu yolov3. Następnie emituje wyniki przez węzeł ujścia IoT Hub jako zdarzenia wnioskowania. 

W tych zdarzeniach typ jest ustawiany na, aby `entity` wskazać, że jest jednostką, np. samochodem lub ciężarówką. `eventTime`Wartość jest czasem UTC, gdy obiekt został wykryty. 

W poniższym przykładzie wykryto dwa samochody w tej samej klatce wideo z różnymi poziomami zaufania.

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

W komunikatach Zwróć uwagę na następujące informacje:

* W `applicationProperties` programie `subject` odwołuje się do węzła w topologii grafu, z którego Wygenerowano komunikat. 
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie jest zdarzeniem analizy.
* `eventTime`Wartość to godzina wystąpienia zdarzenia.
* `body`Sekcja zawiera dane dotyczące zdarzenia analizy. W takim przypadku zdarzenie jest zdarzeniem wnioskowania, dlatego treść zawiera `inferences` dane.
* `inferences`Sekcja wskazuje, że `type` jest `entity` . Ta sekcja zawiera dodatkowe dane dotyczące jednostki.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz wypróbować inne Przewodniki Szybki Start, Zachowaj utworzone zasoby. W przeciwnym razie przejdź do Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten przewodnik Szybki Start, i Usuń wszystkie zasoby.

## <a name="next-steps"></a>Następne kroki

* Wypróbuj [bezpieczną wersję modelu YOLOv3](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/tls-yolov3-onnx/readme.md) i Wdróż ją na urządzeniu IoT Edge. 

Zapoznaj się z dodatkowymi wyzwaniami dla zaawansowanych użytkowników:

* Użyj [kamery IP](https://en.wikipedia.org/wiki/IP_camera) , która obsługuje protokół RTSP, zamiast korzystać z symulatora RTSP. Można wyszukać kamery IP obsługujące protokół RTSP na stronie [ONVIF zgodnych](https://www.onvif.org/conformant-products/) produktów. Wyszukaj urządzenia zgodne z profilami G, S lub T.
* Użyj urządzenia z systemem AMD64 lub x64 zamiast z maszyną wirtualną z systemem Linux systemu Azure. To urządzenie musi znajdować się w tej samej sieci co kamera IP. Można postępować zgodnie z instrukcjami w temacie [Install Azure IoT Edge Runtime on Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Następnie Zarejestruj urządzenie w usłudze Azure IoT Hub, postępując zgodnie z instrukcjami podanymi w temacie [Wdróż swój pierwszy IoT Edge module na wirtualnym urządzeniu z systemem Linux](../../iot-edge/quickstart-linux.md).
