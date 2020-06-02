---
title: Wykrywaj ruch i Emituj zdarzenia — platforma Azure
description: W tym przewodniku szybki start pokazano, jak za pomocą funkcji analizy filmów wideo na żywo na IoT Edge wykrywać ruch i emitować zdarzenia, programowo wywołując metody bezpośrednie.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: 4986ea13bec5382a8e0ef791e75442e4333e4356
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262026"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Szybki Start: wykrywanie ruchu i emitowanie zdarzeń

Ten przewodnik Szybki Start przeprowadzi Cię przez kroki umożliwiające rozpoczęcie pracy z usługą analiza filmów wideo na żywo na IoT Edge. Używa ona maszyny wirtualnej platformy Azure jako urządzenia IoT Edge oraz symulowanego strumienia wideo na żywo. Po wykonaniu kroków instalacji można uruchomić symulowany strumień wideo na żywo za pomocą grafu multimediów, który wykrywa i raportuje wszystkie ruchy w tym strumieniu. Na poniższym diagramie przedstawiono graficzną reprezentację tego wykresu multimedialnego.

![Analiza wideo na żywo oparta na wykrywaniu ruchu](./media/analyze-live-video/motion-detection.png) 

Ten artykuł jest oparty na [przykładowym kodzie](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) zapisanym w języku C#.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) na komputerze z następującymi rozszerzeniami:
    1. [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    2. [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Zestaw .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) zainstalowany w systemie

> [!TIP]
> Może zostać wyświetlony monit o zainstalowanie platformy Docker podczas instalowania rozszerzenia narzędzi Azure IoT Tools. Możesz go zignorować.

## <a name="set-up-azure-resources"></a>Konfigurowanie zasobów platformy Azure

Dla tego samouczka wymagane są następujące zasoby platformy Azure.

* Usługa IoT Hub
* Konto magazynu
* Konto Azure Media Services
* Maszyna wirtualna z systemem Linux na platformie Azure z zainstalowanym [IoT Edge środowiska uruchomieniowego](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)

W tym przewodniku szybki start zalecamy użycie [skryptu konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) w celu wdrożenia zasobów platformy Azure wymienionych powyżej w ramach subskrypcji platformy Azure. W tym celu wykonaj poniższe kroki:

1. Przejdź do https://shell.azure.com.
1. Jeśli używasz Cloud Shell po raz pierwszy, zostanie wyświetlony monit o wybranie subskrypcji w celu utworzenia konta magazynu i udziału plików Microsoft Azure. Wybierz pozycję "Utwórz magazyn", aby utworzyć konto magazynu do przechowywania informacji o sesji Cloud Shell. To konto magazynu jest niezależne od tego, który skrypt utworzy do użycia z Twoim kontem Azure Media Services.
1. Wybierz pozycję "bash" jako środowisko na liście rozwijanej po lewej stronie okna powłoki.

    ![Selektor środowiska](./media/quickstarts/env-selector.png)

1. Uruchom następujące polecenie

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```

    Jeśli skrypt zakończy się pomyślnie, zobaczysz wszystkie zasoby wymienione powyżej w subskrypcji.

1. Po zakończeniu działania skryptu kliknij nawiasy klamrowe, aby uwidocznić strukturę folderów. Zobaczysz kilka plików utworzonych w katalogu ~/CloudDrive/LVA-Sample. Są one przydatne w tym przewodniku szybki start:

     * ~/CloudDrive/LVA-Sample/Edge-Deployment/.env-zawiera właściwości, których Visual Studio Code używa do wdrożenia modułów na urządzeniu brzegowym
     * ~/CloudDrive/LVA-Sample/AppSetting.JSON — używany przez Visual Studio Code do uruchamiania przykładowego kodu
     
Te pliki będą potrzebne do zaktualizowania plików w Visual Studio Code dalszej części przewodnika Szybki Start. Możesz chcieć skopiować je do pliku lokalnego.


 ![Ustawienia aplikacji](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

1. Sklonuj repozytorium z tego miejsca https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp .
1. Uruchom Visual Studio Code i Otwórz folder, do którego zostało pobrane repozytorium.
1. W Visual Studio Code przejdź do folderu "src/Cloud-to-Device-App" i Utwórz plik o nazwie "appSettings. JSON". Ten plik będzie zawierać ustawienia, które są konieczne do uruchomienia programu.
1. Skopiuj zawartość z pliku ~/CloudDrive/LVA-Sample/appSettings.JSON wygenerowanego w poprzedniej sekcji (zobacz krok 5)

    Tekst powinien wyglądać następująco:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Następnie przejdź do folderu "src/Edge" i Utwórz plik o nazwie "ENV".
1. Skopiuj zawartość z pliku "/CloudDrive/LVA-Sample/Edge-Deployment/.env". Tekst powinien wyglądać następująco:

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
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>Sprawdzanie plików przykładowych

1. W Visual Studio Code przejdź do "src/Edge". Zostanie wyświetlony plik ENV, który został utworzony wraz z kilkoma plikami szablonu wdrożenia.

    Szablon wdrożenia odwołuje się do manifestu wdrożenia urządzenia brzegowego z niektórymi wartościami zastępczymi. Plik ENV ma wartości dla tych zmiennych.
1. Następnie przejdź do folderu "src/Cloud-to-Device-App". W tym miejscu zostanie wyświetlony plik appSettings. JSON, który został utworzony wraz z kilkoma innymi plikami:

    * C2D-Console-App. csproj — plik projektu dla Visual Studio Code.
    * Operations. JSON — ten plik zawiera listę różnych operacji, które mają być uruchamiane przez program.
    * Program.cs — przykładowy kod programu, który wykonuje następujące czynności:
    
        * Ładuje ustawienia aplikacji
        * Wywołuje bezpośrednie metody udostępniane przez usługę Live Video Analytics w module IoT Edge. Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie [metod bezpośrednich](direct-methods.md) 
        * Wstrzymuje pracę, aby przeanalizować dane wyjściowe z programu w oknie terminalu i zdarzenia wygenerowane przez moduł w oknie danych wyjściowych.
        * Wywołuje bezpośrednie metody czyszczenia zasobów   

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrażania IoT Edge

Manifest wdrożenia definiuje, jakie moduły są wdrażane na urządzeniu brzegowym, oraz ustawienia konfiguracji dla tych modułów. Wykonaj następujące kroki, aby wygenerować taki manifest z pliku szablonu, a następnie wdróż go na urządzeniu brzegowym.

1. Otwórz Visual Studio Code
1. Ustaw parametry połączenia IoTHub, klikając ikonę "więcej akcji" obok okienka AZURE IOT HUB w lewym dolnym rogu. Można skopiować ciąg z pliku SRC/Cloud-to-Device-Console-App/appSettings. JSON. 

    ![Ustaw parametry połączenia IOT](./media/quickstarts/set-iotconnection-string.png)
1. Następnie kliknij prawym przyciskiem myszy plik "src/Edge/Deployment. Template. JSON" i kliknij pozycję "Generuj IoT Edge manifest wdrożenia".
    ![Generowanie manifestu wdrażania IoT Edge](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Należy utworzyć plik manifestu w folderze src/Edge/config o nazwie "Deployment. amd64. JSON".
1. Kliknij prawym przyciskiem myszy pozycję "src/Edge/config/Deployment. amd64. JSON" i kliknij pozycję "Utwórz wdrożenie dla pojedynczego urządzenia" i wybierz nazwę urządzenia brzegowego.

    ![Tworzenie wdrożenia dla pojedynczego urządzenia](./media/quickstarts/create-deployment-single-device.png)
1. Zostanie wyświetlony monit o wybranie opcji "Wybierz urządzenie IoT Hub". Z listy rozwijanej wybierz pozycję LVA-Sample-Device.
1. W ciągu około 30 sekund Odśwież usługę Azure IOT Hub w lewej dolnej części, a urządzenie brzegowe ma wdrożone następujące moduły:

    * Analiza wideo na żywo na IoT Edge (Nazwa modułu: "lvaEdge")
    * Symulator RTSP (Nazwa modułu: "rtspsim")

Moduł symulatora RTSP symuluje strumień wideo na żywo przy użyciu zapisanego pliku wideo, który został skopiowany do urządzenia brzegowego po uruchomieniu [skryptu konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). Na tym etapie są wdrożone moduły, ale nie są aktywne żadne wykresy multimedialne.

## <a name="prepare-for-monitoring-events"></a>Przygotowanie do monitorowania zdarzeń

Będziesz korzystać z usługi Analiza filmów wideo na żywo w IoT Edge module, aby wykrywać ruch w przychodzącej strumieniu wideo na żywo i wysyłać zdarzenia do IoT Hub. Aby wyświetlić te zdarzenia, wykonaj następujące kroki:

1. Otwórz okienko Eksploratora w Visual Studio Code i Znajdź IoT Hub platformy Azure w lewym dolnym rogu.
1. Rozwiń węzeł urządzenia.
1. Right-klinkieru na LVA-Sample-Device i wybierz opcję "Rozpocznij monitorowanie wbudowanego monitorowania zdarzeń".

    ![Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Uruchamianie przykładowego programu

Wykonaj poniższe kroki, aby uruchomić przykładowy kod.
1. W Visual Studio Code przejdź do pliku "src/Cloud-to-Device-Console-App/Operations. JSON".
1. W węźle GraphTopologySet upewnij się, że:

    ` "topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. Następnie w obszarze węzły GraphInstanceSet i GraphTopologyDelete upewnij się, że wartość topologyname pasuje do wartości właściwości "name" w powyższej topologii wykresu:

    `"topologyName" : "MotionDetection"`
    
1. Rozpocznij sesję debugowania (naciśnij klawisz F5). Zobaczysz, że niektóre komunikaty są wyświetlane w oknie terminalu.
1. Operacja. JSON jest uruchamiana z wywołaniami do GraphTopologyList i GraphInstanceList. Jeśli wyczyszczono zasoby po poprzednich przewodnikach Szybki Start, spowoduje to zwrócenie pustych list, a następnie pauzę w celu naciśnięcia klawisza ENTER.

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
     
     * Wywołanie GraphInstanceActivate do uruchomienia wystąpienia grafu i rozpoczęcie przepływu wideo.
     * Drugie wywołanie GraphInstanceList, aby pokazać, że wystąpienie grafu jest rzeczywiście w stanie uruchomienia.
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

* Komunikat jest zdarzeniem diagnostycznym, MediaSessionEstablished, wskazuje, że węzeł źródłowy RTSP (podmiot) mógł nawiązać połączenie z symulatorem RTSP i zaczyna odbierać (symulowane) kanały informacyjne na żywo.
* "Podmiot" w applicationProperties odwołuje się do węzła w topologii grafu, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła źródłowego RTSP.
* "eventType" w applicationProperties wskazuje, że jest to zdarzenie diagnostyczne.
* "eventTime" wskazuje czas wystąpienia zdarzenia.
* "treść" zawiera dane o zdarzeniu diagnostycznym, które w tym przypadku są szczegółami [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) .


## <a name="motion-detection-event"></a>Zdarzenie wykrywania ruchu

Po wykryciu ruchu moduł usługi Live Video Analytics Edge wysyła zdarzenie wnioskowania. Typ jest ustawiany na "Motion", aby wskazać, że jest wynikiem procesora wykrywania ruchu, a eventTime informuje o tym, co nastąpiło przekroczenie czasu (UTC). Poniżej znajduje się przykład:

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

* "podmiot" w applicationProperties odwołuje się do węzła na grafie nośnika, z którego Wygenerowano komunikat. W takim przypadku komunikat pochodzi z węzła procesora wykrywania ruchu.
* "eventType" w applicationProperties wskazuje, że jest to zdarzenie analizy.
* "eventTime" wskazuje czas wystąpienia zdarzenia.
"treść" zawiera dane dotyczące zdarzenia analizy. W takim przypadku zdarzenie jest zdarzeniem wnioskowania, w związku z czym treść zawiera dane o sygnaturach czasowych i "wnioskach".
* dane dotyczące "wnioskowania" wskazują, że "typ" jest "ruch" i zawiera dodatkowe dane dotyczące zdarzenia "Motion".
* sekcja "Box" zawiera współrzędne dla ograniczonego pola wokół przenoszonego obiektu. Wartości są znormalizowane przez szerokość i wysokość wideo w pikselach (np. Szerokość 1920 i wysokość 1080).

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
    
## <a name="cleanup-resources"></a>Oczyszczanie zasobów

Jeśli zamierzasz wypróbować inne Przewodniki Szybki Start, należy zamieścić do utworzonych zasobów. W przeciwnym razie przejdź do obszaru Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten przewodnik Szybki Start, i Usuń wszystkie zasoby.

## <a name="next-steps"></a>Następne kroki

Uruchom inne Przewodniki Szybki Start, takie jak wykrywanie obiektu w strumieniowym wideo na żywo.        
