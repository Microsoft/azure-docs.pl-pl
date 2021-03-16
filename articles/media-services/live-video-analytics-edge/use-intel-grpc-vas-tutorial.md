---
title: Analizowanie wideo na żywo przy użyciu rozwiązania Intel OpenVINO™ DL — rozszerzenie AI, za pośrednictwem gRPC
description: W tym samouczku pokazano, jak za pomocą rozszerzenia Intel OpenVINO™ DL — rozszerzenie AI firmy Intel do analizowania strumieniowego wideo na żywo z (symulowanej) kamery IP.
ms.topic: tutorial
ms.date: 02/04/2021
ms.service: media-services
ms.author: faneerde
author: fvneerden
ms.openlocfilehash: 20a22d11973f5bb01e2c1345538d5d94ce311dc7
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103465763"
---
# <a name="tutorial-analyze-live-video-by-using-intel-openvino-dl-streamer--edge-ai-extension"></a>Samouczek: analizowanie wideo na żywo przy użyciu rozwiązania Intel OpenVINO™ DL — rozszerzenie AI 

W tym samouczku pokazano, jak za pomocą rozszerzenia Intel OpenVINO™ DL — rozszerzenie AI firmy Intel do analizowania strumieniowego wideo na żywo z (symulowanej) kamery IP. Zobaczysz, jak ten serwer wnioskowania zapewnia dostęp do różnych modeli w celu wykrywania obiektów (osoby, pojazdu lub roweru), klasyfikacji obiektów (przypisań pojazdów) i modelu śledzenia obiektów (osoby, pojazdy i rower). Integracja z modułem gRPC umożliwia wysyłanie klatek wideo do serwera wnioskowania AI. Wyniki są następnie wysyłane do centrum IoT Edge. Po uruchomieniu tej usługi wnioskowania w tym samym węźle obliczeniowym, co w przypadku analizy wideo na żywo, można skorzystać z wysyłania danych wideo za pośrednictwem pamięci współdzielonej. Dzięki temu można uruchamiać inferencing z szybkością klatek kanału wideo na żywo (np. 30 klatek/s). 

W tym samouczku zostanie użyta maszyna wirtualna platformy Azure jako urządzenie IoT Edge i zostanie użyty symulowany strumień wideo na żywo. Jest on oparty na przykładowym kodzie zapisanym w języku C# i kompiluje się w przewodniku [wykrywania zdarzeń i emisji](detect-motion-emit-events-quickstart.md) .

> [!NOTE]
> Ten samouczek wymaga użycia maszyny z procesorem x86-64 jako urządzenia brzegowego.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure, które obejmuje aktywną subskrypcję. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , jeśli jeszcze go nie masz.
  > [!NOTE]
  > Potrzebna jest subskrypcja platformy Azure z uprawnieniami do tworzenia jednostek usługi (jest to **rola właściciela** ). Jeśli nie masz odpowiednich uprawnień, skontaktuj się z administratorem konta, aby przyznać Ci odpowiednie uprawnienia. 
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

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

W tym przewodniku szybki start użyjesz usługi Analiza filmów wideo na żywo na IoT Edge wraz z technologią Intel OpenVINO™ DL — rozszerzenie AI z firmy Intel do wykrywania obiektów, takich jak pojazdy, do klasyfikowania pojazdów lub śledzenia pojazdów, osób lub rowerów. Spowoduje to opublikowanie powstających zdarzeń wnioskowania w centrum IoT Edge.

## <a name="overview"></a>Omówienie

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.svg" alt-text="Omówienie LVA MediaGraph":::

Ten diagram przedstawia sposób przepływu sygnałów w tym przewodniku Szybki Start. [Moduł graniczny](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) symuluje kamerę IP obsługującą serwer Real-Time Streaming Protocol (RTSP). Węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do węzła [procesora rozszerzenia gRPC](media-graph-concept.md#grpc-extension-processor) . 

Węzeł procesora rozszerzenia gRPC wykonuje dekodowane ramki wideo jako dane wejściowe i przekazuje te ramki do punktu końcowego [gRPC](terminology.md#grpc) widocznego dla serwera gRPC. Węzeł obsługuje transfer danych przy użyciu [pamięci współdzielonej](https://en.wikipedia.org/wiki/Shared_memory) lub bezpośrednio osadzanie zawartości w treści gRPC komunikatów. Ponadto węzeł ma wbudowany program formatujący obrazy służący do skalowania i kodowania klatek wideo przed ich przekazaniem do punktu końcowego gRPC. W ramach skalowania dostępne są opcje współczynnika proporcji obrazu, które mają być zachowane, uzupełnione lub rozciągnięte. Koder obrazu obsługuje format JPEG, PNG lub BMP. Więcej informacji na temat procesora [znajdziesz tutaj](media-graph-extension-concept.md#grpc-extension-processor).

W tym samouczku wykonasz następujące czynności:

1. Wdróż Graf multimedialny.
1. Interpretuj wyniki.
1. Wyczyść zasoby.

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>Informacje o technologii Intel OpenVINO™ DL — moduł rozszerzeń AI


OpenVINO™ DL — moduł rozszerzenia systemu AI jest mikrousługą opartą na analizie wideo firmy Intel, która obsługuje potoki analizy wideo utworzone za pomocą OpenVINO™ DL. Deweloperzy mogą wysyłać zdekodowane ramki wideo do modułu rozszerzenia AI, który przeprowadza wykrywanie, klasyfikację lub śledzenie i zwraca wyniki. Moduł rozszerzenia AI udostępnia interfejsy API gRPC, które są zgodne z platformami analitycznymi wideo, takimi jak Live Video Analytics na IoT Edge firmy Microsoft. 

Aby można było tworzyć złożone, wysoce wydajne rozwiązania do analizy filmów wideo na żywo, analiza filmów wideo na żywo w module IoT Edge powinna być sparowana z wydajnym aparatem wnioskowania, który może wykorzystać skalę na krawędzi. W tym samouczku żądania wnioskowania są wysyłane do rozwiązania [Intel OpenVINO™ DL — rozszerzenie AI](https://aka.ms/lva-intel-openvino-dl-streamer), czyli moduł graniczny, który został zaprojektowany z myślą o pracy z analizą wideo na żywo na IoT Edge. 

W początkowej wersji tego serwera wnioskowania masz dostęp do następujących [modeli](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options):

- object_detection ![ wykrywania obiektów person_vehicle_bike_detection dla pojazdu](./media/use-intel-openvino-tutorial/object-detection.png)

- object_classification vehicle_attributes_recognition ![ klasyfikacji obiektów dla pojazdu](./media/use-intel-openvino-tutorial/object-classification.png)

- object_tracking person_vehicle_bike_tracking ![ śledzenia obiektów dla pojazdu osoby](./media/use-intel-openvino-tutorial/object-tracking.png)

Używa wstępnie załadowanego wykrywania obiektów, klasyfikacji obiektów i potoków śledzenia obiektów, aby szybko rozpocząć pracę. Oprócz tego jest dostarczany ze wstępnie załadowanymi [osobami-Pojazdy-Rower-Detect-Crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/description/person-vehicle-bike-detection-crossroad-0078.md) i [pojazdy-deatrybut-](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/description/vehicle-attributes-recognition-barrier-0039.md)----Recognition-0039

> [!NOTE]
> Pobierając i używając modułu Edge: OpenVINO™ DL — rozszerzenie AI z firmy Intel i dołączonego oprogramowania wyraża zgodę na warunki i postanowienia w ramach [umowy licencyjnej](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> Firmy Intel są zaangażowane w przestrzeganie praw człowieka i unikanie Complicity w nadużyciach praw człowieka. Zobacz [globalne zasady praw człowieka firmy Intel](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Produkty i oprogramowanie firmy Intel są przeznaczone wyłącznie do użytku w aplikacjach, które nie powodują naruszenia międzynarodowego prawa człowieka lub nie przyczyniają się do niego.

Możesz użyć elastyczności różnych potoków dla określonego przypadku użycia, zmieniając zmienne środowiskowe potoku w szablonie wdrożenia. Dzięki temu można szybko zmienić model potoku i w połączeniu z analizą wideo na żywo jest to istotne w sekundach, aby zmienić model potokowy i wnioskowanie o multimediach.  

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

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json"`

    * W obszarze `GraphInstanceSet` Zmień nazwę topologii wykresu, aby odpowiadała wartości w poprzednim łączu:

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * W obszarze `GraphTopologyDelete` Edytuj nazwę:

      `"name": "InferencingWithOpenVINOgRPC"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrażania IoT Edge

1. Kliknij prawym przyciskiem myszy plik *src/Edge/deployment.openvino.grpc.cpu.template.jsw* pliku, a następnie wybierz polecenie **Generuj IoT Edge manifest wdrożenia**.

    ![Generowanie manifestu wdrażania IoT Edge](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    *deployment.openvino.grpc.cpu.amd64.jsw* pliku manifestu jest tworzony w folderze *src/Edge/config* .

> [!NOTE]
> Dodaliśmy również *deployment.openvino.grpc.gpu.template.js* szablonu, który umożliwia obsługę procesora GPU dla nieOpenVINOego rozszerzenia DL systemu Intel. Te szablony wskazują obraz centrum platformy Docker firmy Intel.

Powyższe szablony wskazują na obraz centrum platformy Docker firmy Intel. Jeśli nie chcesz hostować kopii na swoim Azure Container Registry, możesz wykonać krok 1 i 2 poniżej:
1. Użyj protokołu SSH do urządzenia z zainstalowanymi narzędziami interfejsu wiersza polecenia platformy Docker (tj. urządzeniem brzegowym) i ściągaj/Oznacz/wypchnij kontener z następującymi krokami:
    * Ściągnij obraz firmy Intel z usługi Docker Hub:

        `sudo docker pull intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`
    
    * Oznacz obraz firmy Intel własną nazwą Azure Container Registry. Zastąp {Twoja nazwa ACR} nazwą ACR, którą można znaleźć w pliku ENV:

        `sudo docker image tag intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
    * Wypchnij oznakowany obraz do Azure Container Registry:

        `sudo docker push {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
2. Teraz musisz edytować szablony, aby odwoływać się do nowego obrazu hostowanego na Azure Container Registry.
    * Kliknij prawym przyciskiem myszy *deployment.openvino.grpc.cpu.template.js* i przejdź do części modułu *lavExtension* i Zastąp:

        `intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`

        tym:

        `{YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    * Powtórz krok 2 dla *deployment.openvino.grpc.gpu.template.jsna*


3. Jeśli zakończono [wykrywanie ruchu i emitowanie zdarzeń](detect-motion-emit-events-quickstart.md) przewodnika Szybki Start, Pomiń ten krok. 

    W przeciwnym razie obok okienka **Azure IoT Hub** w lewym dolnym rogu wybierz ikonę **więcej akcji** , a następnie wybierz pozycję **Ustaw IoT Hub parametry połączenia**. Można skopiować ciąg z *appsettings.js* pliku. Lub, aby upewnić się, że w ramach Visual Studio Code zostało skonfigurowane odpowiednie Centrum IoT Hub, użyj [polecenia wybierz Centrum IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Określanie parametrów połączenia centrum IoT Hub](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Może zostać wyświetlony monit o podanie wbudowanych informacji o punkcie końcowym dla IoT Hub. Aby uzyskać te informacje, w Azure Portal przejdź do IoT Hub i poszukaj opcji **wbudowane punkty końcowe** w lewym okienku nawigacji. Kliknij tam i Wyszukaj **punkt końcowy zgodny z centrum zdarzeń** w sekcji **punkt końcowy zgodny z centrum zdarzeń** . Skopiuj i Użyj tekstu w polu. Punkt końcowy będzie wyglądać następująco:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Kliknij prawym przyciskiem myszy pozycję *src/Edge/config/deployment.openvino.grpc.cpu.template.js* , a następnie wybierz pozycję **Utwórz wdrożenie dla jednego urządzenia**. 

    ![Utwórz wdrożenie dla pojedynczego urządzenia](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Po wyświetleniu monitu o wybranie urządzenia IoT Hub wybierz pozycję **LVA-Sample-Device**.
1. Po około 30 sekundach w lewym dolnym rogu okna Odśwież IoT Hub platformy Azure. Na urządzeniu brzegowym są teraz wyświetlane następujące wdrożone moduły:

    * Moduł Live Video Analytics o nazwie **lvaEdge**
    * Moduł **rtspsim** , który symuluje serwer RTSP i działa jako źródło kanału informacyjnego wideo na żywo
    * Moduł **lvaExtension** , który jest Intel OPENVINO™ DL — rozszerzenie AI 

### <a name="prepare-to-monitor-events"></a>Przygotowanie do monitorowania zdarzeń

Kliknij prawym przyciskiem myszy urządzenie analizy wideo na żywo, a następnie wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**. Ten krok jest wymagany do monitorowania zdarzeń IoT Hub w oknie **danych wyjściowych** Visual Studio Code. 

![Rozpocznij monitorowanie](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>Uruchamianie przykładowego programu w celu wykrywania pojazdów, osób lub rowerów
Jeśli otworzysz [topologię grafu](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) w tym samouczku w przeglądarce, zobaczysz, że wartość `grpcExtensionAddress` została ustawiona na `tcp://lvaExtension:5001` , w porównaniu do przykładu *httpExtensionOpenVINO* , nie musisz zmieniać adresu URL serwera gRPC. Zamiast tego poinstruuje moduł, aby uruchamiał określony potok za pomocą zmiennych środowiskowych, jak wspomniano wcześniej. W szablonie domyślnym ustawimy tę wartość na: "object_detection" dla "person_vehicle_bike_detection". Można eksperymentować z innymi obsługiwanymi potokami. 

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
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINOgRPC",
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

Po utworzeniu wystąpienia grafu nośnika węzeł źródłowy RTSP próbuje połączyć się z serwerem RTSP, który działa w kontenerze rtspsim-live555. Jeśli połączenie zakończy się pomyślnie, zostanie wydrukowane następujące zdarzenie. Typem zdarzenia jest **Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished**.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "sdp": "SDP:\nv=0\r\no=- 1612432131600584 1 IN IP4 172.18.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/homes_00425.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-214.166\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/homes_00425.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=64001F;sprop-parameter-sets=Z2QAH6zZQFAFuwFsgAAAAwCAAAAeB4wYyw==,aOvhEsiw\r\na=control:track1\r\n"
}
```

W tym komunikacie Zwróć uwagę na następujące informacje:

* Komunikat jest zdarzeniem diagnostycznym. `MediaSessionEstablished` wskazuje, że węzeł źródłowy RTSP (podmiot) połączony z symulatorem RTSP i rozpoczął odbieranie strumieniowego kanału informacyjnego (symulowanego).
* W `applicationProperties` programie `subject` wskazuje, że komunikat został wygenerowany z węzła źródła RTSP na grafie nośnika.
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie jest zdarzeniem diagnostycznym.
* `eventTime`Wskazuje godzinę wystąpienia zdarzenia.
* `body`Zawiera dane dotyczące zdarzenia diagnostyki. W takim przypadku dane obejmują szczegóły [protokołu Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Zdarzenie wnioskowania

Węzeł procesora rozszerzenia gRPC odbiera wyniki wnioskowania z usługi Intel OpenVINO™ DL — rozszerzenie AI. Następnie emituje wyniki przez węzeł ujścia IoT Hub jako zdarzenia wnioskowania. 

W tych zdarzeniach typ jest ustawiany na, aby `entity` wskazać, że jest jednostką, np. samochodem lub ciężarówką. `eventTime`Wartość jest czasem UTC, gdy obiekt został wykryty. 

W poniższym przykładzie widać, że zidentyfikowano pojazd, typ pojazdu (van) i kolor (biały), wszystko z poziomu pewności powyżej 0,9, ale również przypisuje mu identyfikator do jednostki, gdy używamy modelu śledzenia obiektów.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "timestamp": 145118912223221,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9605301
        },
        "attributes": [
          {
            "name": "color",
            "value": "white",
            "confidence": 0.9605301
          },
          {
            "name": "type",
            "value": "car",
            "confidence": 0.9605301
          }
        ],
        "box": {
          "l": 0.3958135,
          "t": 0.078730375,
          "w": 0.48403296,
          "h": 0.94352424
        },
        "id": "1"
      }
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
Aby użyć innego modelu, należy zmienić szablon wdrożenia. Aby przełączać się między obsługiwanymi modelami, można zmienić zmienne środowiskowe znajdujące się w module lvaExtenstion. Zapoznaj się z tym [dokumentem w witrynie GitHub](https://github.com/intel/video-analytics-serving/tree/master/samples/lva_ai_extension#edge-ai-extension-module-options) , aby poznać obsługiwane wartości i kombinacje dla modeli.

```
"Env":[
"PIPELINE_NAME=object_classification",
"PIPELINE_VERSION=vehicle_attributes_recognition"
],
```
> [!TIP]
> Skopiuj szablon i Zapisz go pod nową nazwą dla każdego możliwego potoku. W ten sposób można przełączać się między modelami, tworząc nowe wdrożenie na podstawie jednego z tych szablonów.

Po zmianie zmiennych można ponownie wdrożyć szablon na urządzeniu. Możesz teraz powtórzyć powyższe kroki, aby ponownie uruchomić przykładowy program z nowym potokiem. Wyniki wnioskowania będą podobne (w schemacie), ale wyświetlają więcej lub mniej informacji w zależności od wybranego modelu potoku.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz wypróbować inne Przewodniki Szybki start lub samouczków, Zachowaj utworzone zasoby. W przeciwnym razie przejdź do Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten samouczek, i Usuń wszystkie zasoby.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dodatkowymi wyzwaniami dla zaawansowanych użytkowników:

* Użyj [kamery IP](https://en.wikipedia.org/wiki/IP_camera) , która obsługuje protokół RTSP, zamiast korzystać z symulatora RTSP. Można wyszukać kamery IP obsługujące protokół RTSP na stronie [ONVIF zgodnych](https://www.onvif.org/conformant-products/) produktów. Wyszukaj urządzenia zgodne z profilami G, S lub T.
* Użyj urządzenia Intel x64 Linux zamiast maszyny wirtualnej z systemem Linux systemu Azure. To urządzenie musi znajdować się w tej samej sieci co kamera IP. Można postępować zgodnie z instrukcjami w temacie [Install Azure IoT Edge Runtime on Linux](../../iot-edge/how-to-install-iot-edge.md). Następnie Zarejestruj urządzenie w usłudze Azure IoT Hub, postępując zgodnie z instrukcjami podanymi w temacie [Wdróż swój pierwszy IoT Edge module na wirtualnym urządzeniu z systemem Linux](../../iot-edge/quickstart-linux.md).
