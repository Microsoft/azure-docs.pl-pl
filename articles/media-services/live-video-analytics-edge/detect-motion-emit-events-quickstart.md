---
title: Wykrywaj ruch i Emituj zdarzenia — platforma Azure
description: W tym przewodniku szybki start pokazano, jak za pomocą funkcji analizy filmów wideo na żywo na IoT Edge wykrywać ruch i emitować zdarzenia, programowo wywołując metody bezpośrednie.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: fdc80c4d734902309e8b6dc5a6bfee38514fcdb7
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067804"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Szybki Start: wykrywanie ruchu i emitowanie zdarzeń

Ten przewodnik Szybki Start przeprowadzi Cię przez kroki umożliwiające rozpoczęcie pracy z usługą analiza filmów wideo na żywo na IoT Edge. Używa ona maszyny wirtualnej platformy Azure jako urządzenia usługi IoT Edge oraz symulowanego strumienia wideo na żywo. Po wykonaniu kroków instalacji można uruchomić symulowany strumień wideo na żywo za pomocą grafu multimediów, który wykrywa i raportuje wszystkie ruchy w tym strumieniu. Na poniższym diagramie przedstawiono graficzną reprezentację tego wykresu multimedialnego.

![Analiza wideo na żywo oparta na wykrywaniu ruchu](./media/analyze-live-video/motion-detection.png) 

Ten artykuł jest oparty na [przykładowym kodzie](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) zapisanym w języku C#.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , jeśli jeszcze go nie masz.
* [Visual Studio Code](https://code.visualstudio.com/) z następującymi rozszerzeniami:
    * [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1). 

> [!TIP]
> Może zostać wyświetlony monit o zainstalowanie platformy Docker podczas instalowania rozszerzenia narzędzi Azure IoT Tools. Możesz zignorować monit.

## <a name="set-up-azure-resources"></a>Konfigurowanie zasobów platformy Azure

Ten samouczek wymaga następujących zasobów platformy Azure:

* Usługa IoT Hub
* Konto magazynu
* Konto Azure Media Services
* Maszyna wirtualna z systemem Linux na platformie Azure z zainstalowanym [IoT Edge środowiska uruchomieniowego](../../iot-edge/how-to-install-iot-edge-linux.md)

W tym przewodniku szybki start zalecamy używanie [skryptu konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) w celu wdrożenia wymaganych zasobów w ramach subskrypcji platformy Azure. Aby to zrobić, wykonaj następujące kroki:

1. Otwórz usługę [Azure Cloud Shell](https://shell.azure.com).
1. Jeśli używasz Cloud Shell po raz pierwszy, zostanie wyświetlony monit o wybranie subskrypcji w celu utworzenia konta magazynu i udziału plików Microsoft Azure. Wybierz pozycję **Utwórz magazyn** , aby utworzyć konto magazynu dla informacji sesji Cloud Shell. To konto magazynu jest niezależne od konta tworzonego przez skrypt do użycia z Twoim kontem Azure Media Services.
1. W menu rozwijanym po lewej stronie okna Cloud Shell wybierz pozycję **bash** jako swoje środowisko.

    ![Selektor środowiska](./media/quickstarts/env-selector.png)

1. Uruchom następujące polecenie.

```
bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
```

Jeśli skrypt zakończy się pomyślnie, wszystkie wymagane zasoby powinny zostać wyświetlone w ramach subskrypcji.

1. Po zakończeniu działania skryptu wybierz nawiasy klamrowe, aby uwidocznić strukturę folderów. Zobaczysz kilka plików w katalogu *~/CloudDrive/LVA-Sample* . Są one przydatne w tym przewodniku szybki start:

     * ***~/CloudDrive/LVA-Sample/Edge-Deployment/.env*** — ten plik zawiera właściwości, których Visual Studio Code używa do wdrożenia modułów na urządzeniu brzegowym.
     * ***~/clouddrive/lva-sample/appsetting.json*** -Visual Studio Code używa tego pliku do uruchomienia przykładowego kodu.
     
Te pliki będą potrzebne podczas konfigurowania środowiska deweloperskiego w Visual Studio Code w następnej sekcji. Możesz chcieć skopiować je do pliku lokalnego.

 ![Ustawienia aplikacji](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

1. Klonuj repozytorium z tej lokalizacji: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. W Visual Studio Code Otwórz folder, w którym repozytorium zostało pobrane.
1. W obszarze Visual Studio Code przejdź do folderu *src/Cloud-to-Device-App* . W tym miejscu Utwórz plik i nadaj mu nazwę *appsettings.js*. Ten plik będzie zawierać ustawienia, które są konieczne do uruchomienia programu.
1. Skopiuj zawartość z pliku *~/clouddrive/lva-sample/appsettings.js* , która została wygenerowana wcześniej w tym przewodniku Szybki Start.

    Tekst powinien wyglądać podobnie do poniższego wyniku.

```
{  
    "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
    "deviceId" : "lva-sample-device",  
    "moduleId" : "lvaEdge"  
}
```
5. Przejdź do folderu *src/Edge* i Utwórz plik o nazwie *. env*.
1. Skopiuj zawartość pliku */CloudDrive/LVA-Sample/Edge-Deployment/.env* . Tekst powinien wyglądać podobnie do poniższego kodu.

```
SUBSCRIPTION_ID="<Subscription ID>"  
RESOURCE_GROUP="<Resource Group>"  
AMS_ACCOUNT="<AMS Account ID>"  
IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
AAD_TENANT_ID="<AAD Tenant ID>"  
AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
```

## <a name="examine-the-sample-files"></a>Sprawdzanie plików przykładowych

1. W Visual Studio Code przejdź do pozycji *src/Edge*. Zobaczysz plik *. env* i kilka plików szablonów wdrożenia.

    Szablon wdrożenia odwołuje się do manifestu wdrożenia urządzenia brzegowego, gdzie zmienne są używane dla niektórych właściwości. Plik *ENV* zawiera wartości dla tych zmiennych.
1. Przejdź do folderu *src/Cloud-to-Device-Console-App* . Tutaj zobaczysz *appsettings.js* pliku i kilku innych plików:

    * ***C2D-Console-App. csproj*** — plik projektu dla Visual Studio Code.
    * ***operations.js*** listę operacji, które program ma uruchomić.
    * ***Program.cs*** — przykładowy kod programu. Ten kod:
    
      * Ładuje ustawienia aplikacji.
      * Wywołuje metody bezpośrednie, które są udostępniane przez usługę Live Video Analytics w module IoT Edge. Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie [metod bezpośrednich](direct-methods.md).
      * Wstrzymuje działanie, aby można było przeanalizować dane wyjściowe programu w oknie **terminalu** i przeanalizować zdarzenia wygenerowane przez moduł w oknie **danych wyjściowych** .
      * Wywołuje bezpośrednie metody czyszczenia zasobów.   

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrożenia

Manifest wdrożenia definiuje, jakie moduły są wdrażane na urządzeniu brzegowym. Definiuje również ustawienia konfiguracji dla tych modułów. 

Wykonaj następujące kroki, aby wygenerować manifest z pliku szablonu, a następnie wdrożyć go na urządzeniu brzegowym.

1. Otwórz program Visual Studio Code.
1. Obok okienka **Azure IoT Hub** wybierz ikonę **więcej akcji** , aby ustawić parametry połączenia IoT Hub. Możesz skopiować ten ciąg z pliku *src/Cloud-to-Device-App/appsettings.jsna* plik. 

    ![Ustaw parametry połączenia IOT](./media/quickstarts/set-iotconnection-string.png)

1. Kliknij prawym przyciskiem myszy pozycję **src/Edge/deployment.template.jsna** i wybierz polecenie **Generuj IoT Edge manifest wdrożenia**.

    ![Generowanie manifestu wdrażania IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Ta akcja powinna utworzyć plik manifestu o nazwie *deployment.amd64.js* w folderze *src/Edge/config* .
1. Kliknij prawym przyciskiem myszy pozycję **src/Edge/config/deployment.amd64.jsna**, wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**, a następnie wybierz nazwę urządzenia brzegowego.

    ![Tworzenie wdrożenia dla jednego urządzenia](./media/quickstarts/create-deployment-single-device.png)

1. Po wyświetleniu monitu o wybranie urządzenia IoT Hub wybierz pozycję **LVA-Sample-Device (urządzenie** ) z menu rozwijanego.
1. Po około 30 sekundach w lewym dolnym rogu okna Odśwież IoT Hub platformy Azure. Na urządzeniu brzegowym są teraz wyświetlane następujące wdrożone moduły:

    * Analiza wideo na żywo na IoT Edge (Nazwa modułu `lvaEdge` )
    * Symulator protokołu przesyłania strumieniowego (RTSP) w czasie rzeczywistym (Nazwa modułu `rtspsim` )

Moduł symulatora RTSP symuluje strumień wideo na żywo przy użyciu pliku wideo, który został skopiowany do urządzenia brzegowego po uruchomieniu [skryptu konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

> [!NOTE]
> Jeśli używasz własnego urządzenia brzegowego zamiast zainicjowanego przez nasz skrypt Instalatora, przejdź do urządzenia brzegowego i uruchom następujące polecenia z **uprawnieniami administratora**, aby ściągnąć i przechować przykładowy plik wideo używany do tego przewodnika Szybki Start:  

```
mkdir /home/lvaadmin/samples      
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
Na tym etapie moduły są wdrażane, ale nie są aktywne żadne wykresy multimedialne.

## <a name="prepare-to-monitor-events"></a>Przygotowanie do monitorowania zdarzeń

Użyjesz usługi Analiza filmów wideo na żywo w IoT Edge module, aby wykryć ruch w przychodzącym strumieniu wideo na żywo i wysyłać zdarzenia do IoT Hub. Aby wyświetlić te zdarzenia, wykonaj następujące kroki:

1. Otwórz okienko Eksploratora w Visual Studio Code i Wyszukaj usługę Azure IoT Hub w lewym dolnym rogu.
1. Rozwiń węzeł **urządzenia** .
1. Kliknij prawym przyciskiem myszy pozycję **LVA-Sample-Device** i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

    ![Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Uruchamianie przykładowego programu

Wykonaj następujące kroki, aby uruchomić przykładowy kod:

1. W Visual Studio Code przejdź do pozycji *src/Cloud-to-Device-App/operations.json*.
1. W węźle **GraphTopologySet** upewnij się, że została wyświetlona następująca wartość:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. W węzłach **GraphInstanceSet** i **GraphTopologyDelete** upewnij się, że wartość jest `topologyName` zgodna z wartością `name` właściwości w topologii grafu:

    `"topologyName" : "MotionDetection"`
    
1. Rozpocznij sesję debugowania, wybierając klawisz F5. W oknie **terminalu** zostaną wyświetlone pewne komunikaty.
1. *operations.jsw* pliku rozpoczyna się z wywołaniami do `GraphTopologyList` i `GraphInstanceList` . Jeśli wyczyszczono zasoby po zakończeniu poprzednich przewodników Szybki Start, proces ten spowoduje zwrócenie pustych list, a następnie wstrzymanie. Aby kontynuować, wybierz klawisz ENTER.

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
  "name": "Sample-Graph",
  "properties": {
    "topologyName": "MotionDetection",
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
6. Dane wyjściowe w oknie **terminalu** są wstrzymywane na `Press Enter to continue` . Nie zaznaczaj jeszcze klawisza ENTER. Przewiń w górę, aby wyświetlić ładunki odpowiedzi JSON dla wywoływanych metod bezpośrednich.
1. Przejdź do okna **dane wyjściowe** w Visual Studio Code. Zobaczysz komunikat informujący o tym, że usługa Live Video Analytics w IoT Edge module wysyła do centrum IoT Hub. W poniższej sekcji tego przewodnika Szybki Start omówiono te komunikaty.
1. Wykres multimedialny będzie nadal uruchamiany i drukować wyniki. Symulator RTSP utrzymuje zapętlenie źródłowego wideo. Aby zatrzymać Graf multimedialny, Wróć do okna **terminalu** i wybierz klawisz ENTER. 

    Kolejna seria wywołań czyści zasoby:
     * Wywołanie `GraphInstanceDeactivate` dezaktywowania wystąpienia grafu.
     * Wywołanie `GraphInstanceDelete` usuwania wystąpienia.
     * Wywołanie `GraphTopologyDelete` usunięcia topologii.
     * Końcowe wywołanie `GraphTopologyList` pokazujące, że lista jest pusta.

## <a name="interpret-results"></a>Interpretowanie wyników

Po uruchomieniu grafu multimediów wyniki z węzła procesora wykrywania ruchu przechodzą przez węzeł ujścia IoT Hub do centrum IoT Hub. Komunikaty wyświetlane w oknie **dane wyjściowe** Visual Studio Code zawierają `body` sekcję i `applicationProperties` sekcję. Aby uzyskać więcej informacji, zobacz [Tworzenie i odczytywanie wiadomości IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

W poniższych komunikatach moduł analizy wideo na żywo definiuje właściwości aplikacji i zawartość treści.

### <a name="mediasessionestablished-event"></a>Zdarzenie MediaSessionEstablished

Po utworzeniu wystąpienia grafu nośnika węzeł źródłowy RTSP próbuje połączyć się z serwerem RTSP, który działa w kontenerze rtspsim-live555. Jeśli połączenie zakończy się pomyślnie, zostanie wydrukowane następujące zdarzenie.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
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

W poprzednich danych wyjściowych: 
* Komunikat jest zdarzeniem diagnostycznym `MediaSessionEstablished` . Wskazuje on, że węzeł źródłowy RTSP (podmiot) połączony z symulatorem RTSP i rozpoczął odbieranie strumieniowego kanału na żywo (symulowanego).
* W `applicationProperties` programie `subject` , odwołuje się do węzła w topologii grafu, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła źródłowego RTSP.
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie jest zdarzeniem diagnostycznym.
* `eventTime`Wartość wskazuje godzinę wystąpienia zdarzenia.
* `body`Sekcja zawiera dane dotyczące zdarzenia diagnostyki. W takim przypadku dane obejmują szczegóły [protokołu Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .


### <a name="motiondetection-event"></a>Zdarzenie MotionDetection

Po wykryciu ruchu usługa analizy filmów wideo na żywo w IoT Edge module wysyła zdarzenie wnioskowania. `type`Jest ustawiona na `motion` , aby wskazać, że jest to wynik z procesora wykrywania ruchu. `eventTime`Wartość informuje o tym, kiedy nastąpiło przeruch (w formacie UTC). 

Oto przykład tego komunikatu:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

W tym przykładzie: 

* W `applicationProperties` programie `subject` odwołuje się do węzła na grafie nośnika, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła procesora wykrywania ruchu.
* W `applicationProperties` programie `eventType` wskazuje, że to zdarzenie jest zdarzeniem analizy.
* `eventTime`Wartość to godzina wystąpienia zdarzenia.
* `body`Wartość to dane dotyczące zdarzenia analizy. W takim przypadku zdarzenie jest zdarzeniem wnioskowania, dlatego treść zawiera `timestamp` i `inferences` dane.
* `inferences`Dane wskazują, że `type` jest to `motion` . Zawiera ona dodatkowe dane dotyczące tego `motion` zdarzenia.
* `box`Sekcja zawiera współrzędne dla ograniczonego pola wokół przenoszonego obiektu. Wartości są znormalizowane przez szerokość i wysokość wideo w pikselach. Na przykład szerokość to 1920, a wysokość to 1080.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz wypróbować inne Przewodniki Szybki Start, należy pozostawić utworzone zasoby. W przeciwnym razie w Azure Portal przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten przewodnik Szybki Start, a następnie usuń wszystkie zasoby.

## <a name="next-steps"></a>Następne kroki

Uruchom inne Przewodniki Szybki Start, takie jak wykrywanie obiektu w strumieniowym wideo na żywo.        
