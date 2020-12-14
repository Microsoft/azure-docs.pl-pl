---
title: Analizowanie wideo na żywo za pomocą OpenVINO™ model Server — rozszerzenie AI z firmy Intel
description: W tym samouczku użyjesz serwera modelu AI dostarczonego przez firmę Intel do analizowania strumieniowego kanału informacyjnego wideo z (symulowanej) kamery IP.
ms.topic: tutorial
ms.date: 09/08/2020
titleSuffix: Azure
ms.openlocfilehash: 82906111e64bd278d4371d1c3497fefc4510bbbd
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401217"
---
# <a name="tutorial-analyze-live-video-by-using-openvino-model-server--ai-extension-from-intel"></a>Samouczek: analizowanie wideo na żywo za pomocą OpenVINO™ model Server — rozszerzenie AI z firmy Intel 

W tym samouczku pokazano, jak używać rozszerzenia OpenVINO™ model Server — AI z firmy Intel do analizowania strumieniowego kanału informacyjnego wideo z (symulowanej) kamery IP. Zobaczysz, jak ten serwer wnioskowania zapewnia dostęp do modeli do wykrywania obiektów (osoby, pojazdu lub roweru) oraz modelu klasyfikowania pojazdów. Podzestaw ramek w dynamicznym kanale wideo jest wysyłany do tego serwera wnioskowania, a wyniki są wysyłane do centrum IoT Edge.

W tym samouczku zostanie użyta maszyna wirtualna platformy Azure jako urządzenie IoT Edge i zostanie użyty symulowany strumień wideo na żywo. Jest on oparty na przykładowym kodzie zapisanym w języku C# i kompiluje się w przewodniku [wykrywania zdarzeń i emisji](detect-motion-emit-events-quickstart.md) .

> [!NOTE]
> Ten samouczek wymaga użycia maszyny z procesorem x86-64 jako urządzenia brzegowego.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure, które obejmuje aktywną subskrypcję. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , jeśli jeszcze go nie masz.
* [Visual Studio Code](https://code.visualstudio.com/), z następującymi rozszerzeniami:
    * [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Jeśli nie doszło do przewodnika Szybki Start dotyczącego [wykrywania ruchu i emisji](detect-motion-emit-events-quickstart.md) , należy wykonać kroki [konfigurowania zasobów platformy Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> W przypadku instalowania narzędzi Azure IoT Tools może zostać wyświetlony monit o zainstalowanie platformy Docker. Możesz zignorować monit.

## <a name="review-the-sample-video"></a>Zapoznaj się z przykładowym wideo

Po skonfigurowaniu zasobów platformy Azure krótkie wideo z partii parkingowej jest kopiowane na maszynę wirtualną z systemem Linux na platformie Azure, która jest używana jako urządzenie IoT Edge. Ten przewodnik Szybki Start używa pliku wideo do symulowania strumienia na żywo.

Otwórz aplikację, taką jak [VLC Media Player](https://www.videolan.org/vlc/). Wybierz kombinację klawiszy CTRL + N, a następnie wklej link do [filmu wideo](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) , aby rozpocząć odtwarzanie. Zobaczysz film o pojazdach w partii parkingowej, większość z nich zaparkowane i jeden przenoszony.

W tym przewodniku szybki start użyjesz usługi Analiza filmów wideo na żywo na IoT Edge wraz z rozszerzeniem OpenVINO™ model Server — AI firmy Intel do wykrywania obiektów, takich jak pojazdy, lub do klasyfikowania. Spowoduje to opublikowanie powstających zdarzeń wnioskowania w centrum IoT Edge.

## <a name="overview"></a>Omówienie

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.svg" alt-text="Omówienie":::

Ten diagram przedstawia sposób przepływu sygnałów w tym przewodniku Szybki Start. [Moduł graniczny](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) symuluje kamerę IP obsługującą serwer Real-Time Streaming Protocol (RTSP). Węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do węzła [procesora rozszerzenia http](media-graph-concept.md#http-extension-processor) . 

Węzeł rozszerzenia HTTP pełni rolę serwera proxy. Jest to przykładowe przychodzące ramki wideo ustawione przez użytkownika `samplingOptions` , a także konwertuje ramki wideo na określony typ obrazu. Następnie przekazuje obraz za pośrednictwem REST do innego modułu krawędzi, który uruchamia modele AI za punktem końcowym HTTP. W tym przykładzie moduł Edge to OpenVINO™ model Server — rozszerzenie AI od firmy Intel. Węzeł procesora rozszerzenia HTTP zbiera wyniki wykrywania i publikuje zdarzenia w węźle [ujścia IoT Hub](media-graph-concept.md#iot-hub-message-sink) . Następnie węzeł wysyła te zdarzenia do [centrum IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

W tym samouczku wykonasz następujące czynności:

1. Utwórz i Wdróż Graf multimedialny, modyfikując go.
1. Interpretuj wyniki.
1. Wyczyść zasoby.

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>Informacje o rozszerzeniu OpenVINO™ model Server — AI z platformy Intel

Dystrybucja [OpenVINO™ zestawu narzędzi](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) firmy® Intel (Open Visual wnioskowania i neuronowyche optymalizację sieci) to bezpłatny zestaw oprogramowania, który pomaga deweloperom i analitykom danych przyspieszyć tworzenie obciążeń komputerowych, usprawnić przetwarzanie i wdrażanie oraz zapewnić łatwe, heterogeniczne wykonywanie na platformach Intel® od brzegu do chmury. Obejmuje zestaw narzędzi do wdrażania głębokiego uczenia firmy Intel® z technologią optymalizacji modelu i aparatem wnioskowania, a także repozytorium [Open model Zoo](https://github.com/openvinotoolkit/open_model_zoo) , które obejmuje ponad 40 zoptymalizowanych modeli.

Aby można było tworzyć złożone, wysoce wydajne rozwiązania do analizy filmów wideo na żywo, analiza filmów wideo na żywo w module IoT Edge powinna być sparowana z wydajnym aparatem wnioskowania, który może wykorzystać skalę na krawędzi. W tym samouczku żądania wnioskowania są wysyłane do [rozszerzenia OpenVINO™ model Server — AI z firmy Intel](https://aka.ms/lva-intel-ovms), modułu krawędzi, który został zaprojektowany do pracy z analizą wideo na żywo na IoT Edge. Ten moduł serwera wnioskowania zawiera serwer OpenVINO™ Modeling (OVMS), serwer wnioskowania obsługiwany przez zestaw narzędzi OpenVINO™, który jest wysoce zoptymalizowany pod kątem obciążeń dotyczących przetwarzania obrazów i opracowany dla architektur architektury Intel®. Rozszerzenie zostało dodane do OVMS w celu ułatwienia wymiany ramek wideo i wnioskowania między serwerem wnioskowania a analizą filmów wideo na żywo w module IoT Edge, dzięki czemu można w ten sposób uruchomić dowolny model usługi OpenVINO™, który jest obsługiwany przez modyfikację [kodu](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper). Można w dalszej części wybrać różne mechanizmy przyspieszenia zapewniane przez sprzęt firmy Intel®. Obejmują one procesory CPU (Atom, Core, Xeon), FPGA, VPUs.

W początkowej wersji tego serwera wnioskowania masz dostęp do następujących [modeli](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models):

- Wykrywanie pojazdu (adres URL wnioskowania: http://{module-name}: 4000/vehicleDetection)
- Wykrywanie osoby/pojazdu/roweru (adres URL wnioskowania: http://{module-name}: 4000/personVehicleBikeDetection)
- Klasyfikacja pojazdu (adres URL wnioskowania: http://{module-name}: 4000/vehicleClassification)
- Wykrywanie twarzy (adres URL wnioskowania: http://{module-name}: 4000/faceDetection)

> [!NOTE]
> Pobierając i korzystając z modułu Edge: OpenVINO™ model Server — rozszerzenie AI z firmy Intel i dołączonego oprogramowania, wyrażasz zgodę na warunki i postanowienia zawarte w [umowie licencyjnej](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> Firmy Intel są zaangażowane w przestrzeganie praw człowieka i unikanie Complicity w nadużyciach praw człowieka. Zobacz [globalne zasady praw człowieka firmy Intel](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Produkty i oprogramowanie firmy Intel są przeznaczone wyłącznie do użytku w aplikacjach, które nie powodują naruszenia międzynarodowego prawa człowieka lub nie przyczyniają się do niego.

## <a name="create-and-deploy-the-media-graph"></a>Tworzenie i wdrażanie grafu multimediów

### <a name="examine-and-edit-the-sample-files"></a>Sprawdzanie i edytowanie plików przykładowych

W ramach wymagań wstępnych pobrano przykładowy kod do folderu. Wykonaj następujące kroki, aby przejrzeć i edytować przykładowe pliki.

1. W Visual Studio Code przejdź do pozycji *src/Edge*. Zobaczysz plik *ENV* i kilka plików szablonów wdrożenia.

    Szablon wdrożenia odwołuje się do manifestu wdrożenia urządzenia brzegowego. Zawiera ona niektóre wartości zastępcze. Plik *ENV* zawiera wartości dla tych zmiennych.

1. Przejdź do folderu *src/Cloud-to-Device-Console-App* . Tutaj zobaczysz *appsettings.jsw* pliku i kilku innych plikach:

    * ***C2D-Console-App. csproj** _-plik projektu dla Visual Studio Code.
    _ ***operations.jsna** _-listę operacji, które program ma uruchomić.
    _ ***Program.cs** _ — przykładowy kod programu. Ten kod:

        Powoduje załadowanie ustawień aplikacji.
        * Wywołuje bezpośrednie metody, które są ujawniane w module IoT Edge na żywo. Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie [metod bezpośrednich](direct-methods.md).
        * Wstrzymuje działanie, aby można było przeanalizować dane wyjściowe programu w oknie **terminalu** i przeanalizować zdarzenia, które zostały wygenerowane przez moduł w oknie **danych wyjściowych** .
        * Wywołuje bezpośrednie metody czyszczenia zasobów.


1. Edytuj *operations.jsw* pliku:
    * Zmień łącze do topologii grafu:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json"`

    * W obszarze `GraphInstanceSet` Zmień nazwę topologii wykresu, aby odpowiadała wartości w poprzednim łączu:

      `"topologyName" : "InferencingWithOpenVINO"`

    * W obszarze `GraphTopologyDelete` Edytuj nazwę:

      `"name": "InferencingWithOpenVINO"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrażania IoT Edge

1. Kliknij prawym przyciskiem myszy plik *src/Edge/deployment.openvino.template.jsw* pliku, a następnie wybierz polecenie **Generuj IoT Edge manifest wdrożenia**.

    ![Generowanie manifestu wdrażania IoT Edge](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    *deployment.yolov3.amd64.jsw* pliku manifestu jest tworzony w folderze *src/Edge/config* .

1. Jeśli zakończono [wykrywanie ruchu i emitowanie zdarzeń](detect-motion-emit-events-quickstart.md) przewodnika Szybki Start, Pomiń ten krok. 

    W przeciwnym razie obok okienka **Azure IoT Hub** w lewym dolnym rogu wybierz ikonę **więcej akcji** , a następnie wybierz pozycję **Ustaw IoT Hub parametry połączenia**. Można skopiować ciąg z *appsettings.js* pliku. Lub, aby upewnić się, że w ramach Visual Studio Code zostało skonfigurowane odpowiednie Centrum IoT Hub, użyj [polecenia wybierz Centrum IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Określanie parametrów połączenia centrum IoT Hub](./media/quickstarts/set-iotconnection-string.png)

1. Kliknij prawym przyciskiem myszy pozycję *src/Edge/config/deployment.openvino.amd64.js* , a następnie wybierz pozycję **Utwórz wdrożenie dla jednego urządzenia**. 

    ![Utwórz wdrożenie dla pojedynczego urządzenia](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Po wyświetleniu monitu o wybranie urządzenia IoT Hub wybierz pozycję **LVA-Sample-Device**.
1. Po około 30 sekundach w lewym dolnym rogu okna Odśwież IoT Hub platformy Azure. Na urządzeniu brzegowym są teraz wyświetlane następujące wdrożone moduły:

    * Moduł Live Video Analytics o nazwie **lvaEdge**
    * Moduł **rtspsim** , który symuluje serwer RTSP i działa jako źródło kanału informacyjnego wideo na żywo
    * Moduł **openvino** , który jest modułem rozszerzeń Openvino™ model Server — AI z platformy Intel 

### <a name="prepare-to-monitor-events"></a>Przygotowanie do monitorowania zdarzeń

Kliknij prawym przyciskiem myszy urządzenie analizy wideo na żywo, a następnie wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**. Ten krok jest wymagany do monitorowania zdarzeń IoT Hub w oknie **danych wyjściowych** Visual Studio Code. 

![Rozpocznij monitorowanie](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles"></a>Uruchamianie przykładowego programu w celu wykrywania pojazdów
Jeśli zostanie otwarta [topologia grafu](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) w tym samouczku w przeglądarce, zobaczysz, że wartość `inferencingUrl` została ustawiona na `http://openvino:4000/vehicleDetection` , co oznacza, że serwer wnioskowania zwróci wyniki po wykryciu pojazdów (jeśli istnieją) w wideo na żywo.

1. W Visual Studio Code Otwórz kartę **rozszerzenia** (lub naciśnij klawisze CTRL + SHIFT + X) i Wyszukaj pozycję Azure IoT Hub.
1. Kliknij prawym przyciskiem myszy i wybierz pozycję **Ustawienia rozszerzenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Ustawienia rozszerzenia":::
1. Wyszukaj i Włącz opcję "Pokaż pełny komunikat".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Pokaż pełny komunikat":::
1. Aby rozpocząć sesję debugowania, wybierz klawisz F5. W oknie **terminalu** są wyświetlane komunikaty.
1. *operations.jsw* kodzie zaczyna się od wywołania metod bezpośrednich `GraphTopologyList` i `GraphInstanceList` . Jeśli wyczyszczono zasoby po ukończeniu poprzednich przewodników Szybki Start, proces ten spowoduje zwrócenie pustych list, a następnie wstrzymanie. Aby kontynuować, wybierz klawisz ENTER.

    W oknie **terminalu** zostanie wyświetlony następny zestaw wywołań metod bezpośrednich:

     * Wywołanie `GraphTopologySet` , które używa poprzedniej `topologyUrl`
     * Wywołanie `GraphInstanceSet` , które używa następującej treści:

         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINO",
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
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-07-24T16:42:18.1280000Z"
  }
}
```

W tym komunikacie Zwróć uwagę na następujące informacje:

* Komunikat jest zdarzeniem diagnostycznym. `MediaSessionEstablished` wskazuje, że węzeł źródłowy RTSP (podmiot) połączony z symulatorem RTSP i rozpoczął odbieranie strumieniowego kanału informacyjnego (symulowanego).
* W `applicationProperties` programie `subject` wskazuje, że komunikat został wygenerowany z węzła źródła RTSP na grafie nośnika.
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie jest zdarzeniem diagnostycznym.
* `eventTime`Wskazuje godzinę wystąpienia zdarzenia.
* `body`Zawiera dane dotyczące zdarzenia diagnostyki. W takim przypadku dane obejmują szczegóły [protokołu Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Zdarzenie wnioskowania

Węzeł procesora rozszerzenia HTTP odbiera wyniki wnioskowania z OpenVINO™ model Server — AI. Następnie emituje wyniki przez węzeł ujścia IoT Hub jako zdarzenia wnioskowania. 

W tych zdarzeniach typ jest ustawiany na, aby `entity` wskazać, że jest jednostką, np. samochodem lub ciężarówką. `eventTime`Wartość jest czasem UTC, gdy obiekt został wykryty. 

W poniższym przykładzie wykryto dwa pojazdy z wartościami ufności powyżej 0,9.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.9951713681221008
          },
          "box": {
            "l": 0.042635321617126465,
            "t": 0.4004564881324768,
            "w": 0.10961548984050751,
            "h": 0.07942074537277222
          }
        }
      },
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.928486168384552
          },
          "box": {
            "l": 0.2506900727748871,
            "t": 0.07512682676315308,
            "w": 0.05470699071884155,
            "h": 0.07408371567726135
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:43:18.1280000Z"
  }
}
```

W komunikatach Zwróć uwagę na następujące informacje:

* W `applicationProperties` programie `subject` odwołuje się do węzła w topologii grafu, z którego Wygenerowano komunikat. 
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie jest zdarzeniem analizy.
* `eventTime`Wartość to godzina wystąpienia zdarzenia.
* `body`Sekcja zawiera dane dotyczące zdarzenia analizy. W takim przypadku zdarzenie jest zdarzeniem wnioskowania, dlatego treść zawiera `inferences` dane.
* `inferences`Sekcja wskazuje, że `type` jest `entity` . Ta sekcja zawiera dodatkowe dane dotyczące jednostki.

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>Uruchamianie przykładowego programu w celu wykrywania osób lub pojazdów lub rowerów
Aby użyć innego modelu, należy zmodyfikować topologię grafu, a także `operations.json` plik.

Skopiuj [topologię grafu](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) do pliku lokalnego, powiedzmy `C:\TEMP\topology.json` . Otwórz tę kopię i zmień wartość `inferencingUrl` na `http://openvino:4000/personVehicleBikeDetection` .

Następnie w Visual Studio Code przejdź do folderu *src/Cloud-to-Device-App-Console* i Otwórz `operations.json` plik. Edytuj wiersz w programie `topologyUrl` , aby:

```
      "topologyFile" : "C:\\TEMP\\topology.json" 
```
Możesz teraz powtórzyć powyższe kroki, aby ponownie uruchomić przykładowy program z nową topologią. Wyniki wnioskowania będą podobne (w schemacie) do tego modelu wykrywania pojazdów, po prostu `subtype` ustawione na `personVehicleBikeDetection` .

## <a name="run-the-sample-program-to-classify-vehicles"></a>Uruchamianie przykładowego programu w celu klasyfikowania pojazdów
W Visual Studio Code Otwórz kopię lokalną `topology.json` z poprzedniego kroku i zmień wartość `inferencingUrl` na `http://openvino:4000/vehicleClassification` . Jeśli używasz poprzedniego przykładu w celu wykrycia osób lub pojazdów lub rowerów, nie musisz `operations.json` ponownie modyfikować pliku.

Możesz teraz powtórzyć powyższe kroki, aby ponownie uruchomić przykładowy program z nową topologią. Przykładowy wynik klasyfikacji jest następujący.

```
[IoTHubMonitor] [9:44:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "classification",
        "subtype": "color",
        "classification": {
          "tag": {
            "value": "black",
            "confidence": 0.9179772138595581
          }
        }
      },
      {
        "type": "classification",
        "subtype": "type",
        "classification": {
          "tag": {
            "value": "truck",
            "confidence": 1
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:44:18.1280000Z"
  }
}
```

## <a name="run-the-sample-program-to-detect-faces"></a>Uruchamianie przykładowego programu w celu wykrywania twarzy
W Visual Studio Code Otwórz kopię lokalną `topology.json` z poprzedniego kroku i zmień wartość `inferencingUrl` na `http://openvino:4000/faceDetection` . Jeśli używasz poprzedniego przykładu w celu wykrycia osób lub pojazdów lub rowerów, nie musisz `operations.json` ponownie modyfikować pliku.

Możesz teraz powtórzyć powyższe kroki, aby ponownie uruchomić przykładowy program z nową topologią. Wynik wykrywania przykładu jest następujący (Uwaga: w celu wypróbowania tego modelu nie ma żadnych wykrywalnych materiałów wideo o parkingowej partii).

```
[IoTHubMonitor] [9:54:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:54:18.1280000Z"
  }
}
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz wypróbować inne Przewodniki Szybki start lub samouczków, Zachowaj utworzone zasoby. W przeciwnym razie przejdź do Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten samouczek, i Usuń wszystkie zasoby.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dodatkowymi wyzwaniami dla zaawansowanych użytkowników:

* Użyj [kamery IP](https://en.wikipedia.org/wiki/IP_camera) , która obsługuje protokół RTSP, zamiast korzystać z symulatora RTSP. Można wyszukać kamery IP obsługujące protokół RTSP na stronie [ONVIF zgodnych](https://www.onvif.org/conformant-products/) produktów. Wyszukaj urządzenia zgodne z profilami G, S lub T.
* Użyj urządzenia z systemem AMD64 lub x64 zamiast z maszyną wirtualną z systemem Linux systemu Azure. To urządzenie musi znajdować się w tej samej sieci co kamera IP. Można postępować zgodnie z instrukcjami w temacie [Install Azure IoT Edge Runtime on Linux](../../iot-edge/how-to-install-iot-edge.md). Następnie Zarejestruj urządzenie w usłudze Azure IoT Hub, postępując zgodnie z instrukcjami podanymi w temacie [Wdróż swój pierwszy IoT Edge module na wirtualnym urządzeniu z systemem Linux](../../iot-edge/quickstart-linux.md).