---
title: Analizuj wideo na żywo dzięki usłudze Live Video Analytics na IoT Edge i platformie Azure Custom Vision
description: Dowiedz się, w jaki sposób używać Custom Vision do kompilowania modelu kontenera, który może wykryć wózek i korzystać z rozszerzalności na żywo wideo w usłudze IoT Edge (LVA) w celu wdrożenia modelu na krawędzi na potrzeby wykrywania wózków zabawki z poziomu strumienia wideo na żywo.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 022dc5714e7a2e19446ee57e827a08ef4c56413e
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761434"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Samouczek: analizowanie wideo na żywo za pomocą analizy filmów wideo na żywo na IoT Edge i na platformie Azure Custom Vision

W tym samouczku dowiesz się, jak używać [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) do kompilowania modelu z kontenerem, który może wykrywać wózek i korzystać z [rozszerzalności](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) na żywo wideo w usłudze IoT Edge, aby wdrożyć model na granicy do wykrywania wózków Zabawka z poziomu strumienia wideo na żywo.

Dowiesz się, jak połączyć się z możliwościami Custom Vision — umożliwia to wszystkim użytkownikom Kompilowanie i uczenie modelu przetwarzania obrazów przez przesłanie i etykietowanie kilku obrazków bez znajomości informacji o nauce, ML lub AI z możliwościami korzystania z usługi Live Video Analytics, dzięki czemu można łatwo wdrożyć niestandardowy model jako kontener na krawędzi i analizować symulowane kanały wideo na żywo. Ten samouczek korzysta z maszyny wirtualnej platformy Azure jako urządzenia IoT Edge, opiera się na przykładowym kodzie zapisanym w języku C# i kompiluje się w przewodniku [wykrywania zdarzeń i emisji](detect-motion-emit-events-quickstart.md) .

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj odpowiednie zasoby.
> * Tworzenie modelu Custom Vision w chmurze w celu wykrywania wózków zabawowych i wdrażania ich na brzegach
> * Utwórz i Wdróż Graf multimedialny z rozszerzeniem http na potrzeby niestandardowego modelu wizji
> * Uruchom przykładowy kod.
> * Przeanalizuj i Interpretuj wyniki.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem  

Przed rozpoczęciem zaleca się zapoznanie się z następującymi artykułami: 

* [Analiza filmów wideo na żywo na IoT Edge przegląd](overview.md)
* [Omówienie usługi Azure Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)
* [Analiza filmów wideo na żywo na IoT Edge terminologii](terminology.md)
* [Koncepcje grafu multimediów](media-graph-concept.md)
* [Platforma Live Video Analytics bez nagrania wideo](analyze-live-video-concept.md)
* [Uruchamianie analizy filmów wideo na żywo z własnym modelem](use-your-model-quickstart.md)
* [Samouczek: opracowywanie modułu IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Jak edytować wdrożenie. * .template.jsna](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące tego samouczka:

* [Visual Studio Code](https://code.visualstudio.com/) na swoim komputerze deweloperskim przy użyciu narzędzi i rozszerzeń [języka C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) [usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) .

    > [!TIP]
    > Może zostać wyświetlony monit o zainstalowanie platformy Docker. Zignoruj ten monit.
* [Zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) na komputerze deweloperskim.
* Upewnij się, że masz:
    
    * [Konfigurowanie zasobów platformy Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
    * [Konfigurowanie środowiska projektowego](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)

## <a name="review-the-sample-video"></a>Zapoznaj się z przykładowym wideo


W tym samouczku do symulowania strumienia na żywo jest wykorzystywany plik wideo dotyczący [wywnioskowania samochodu](https://lvamedia.blob.core.windows.net/public/t2.mkv) . Film wideo można przeanalizować za pomocą aplikacji, takiej jak [VLC Media Player](https://www.videolan.org/vlc/). Wybierz kombinację klawiszy CTRL + N, a następnie wklej link do [filmu wideo dotyczącego wnioskowania samochodu](https://lvamedia.blob.core.windows.net/public/t2.mkv) , aby rozpocząć odtwarzanie. Obejrzyj film wideo, który w przypadku znacznika 36-sekundowego występuje w filmie. Model niestandardowy został przeszkolony w celu wykrywania określonego wózka zabawki. W tym samouczku użyjesz usługi Analiza filmów wideo na żywo na IoT Edge, aby wykryć takie wózki i publikować powiązane zdarzenia wnioskowania do centrum IoT Edge.

## <a name="overview"></a>Omówienie

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Przegląd Custom Vision":::

Ten diagram przedstawia sposób przepływu sygnałów w tym samouczku. [Moduł graniczny](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) symuluje kamerę IP obsługującą serwer protokołu przesyłania strumieniowego w czasie rzeczywistym (RTSP). Węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do węzła [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) . Ten procesor ogranicza szybkość klatek strumienia wideo, który dociera do węzła [procesora rozszerzenia http](media-graph-concept.md#http-extension-processor) .
Węzeł rozszerzenia HTTP pełni rolę serwera proxy. Konwertuje ramki wideo na określony typ obrazu. Następnie przekazuje obraz za pośrednictwem REST do innego modułu krawędzi, który uruchamia model AI za punktem końcowym HTTP. W tym przykładzie moduł Edge jest modelem wykrywacza samochodów, który został utworzony przy użyciu Custom Vision. Węzeł procesora rozszerzenia HTTP zbiera wyniki wykrywania i publikuje zdarzenia w węźle [ujścia IoT Hub](media-graph-concept.md#iot-hub-message-sink) . Następnie węzeł wysyła te zdarzenia do [centrum IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Kompilowanie i wdrażanie modelu wykrywania zabawki Custom Vision 

Jak nazwa Custom Vision sugeruje, możesz użyć jej do utworzenia własnego niestandardowego detektora obiektów lub klasyfikatora w chmurze. Zapewnia prosty, łatwy w użyciu i intuicyjny interfejs do tworzenia niestandardowych modeli wzrokowych, które można wdrożyć w chmurze lub na brzegu za pośrednictwem kontenerów. 

Aby można było utworzyć wykrywacz samochodów, zalecamy obserwowanie tego niestandardowego modułu wykrywania obiektów za pośrednictwem artykułu sieci Web [Szybki Start](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector) .

Dodatkowe uwagi:
 
* W tym samouczku nie należy używać przykładowych obrazów podanych w [sekcji wymagań wstępnych](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#prerequisites)artykułu Szybki Start. Zamiast tego korzystamy z pewnego zestawu obrazów do kompilowania niestandardowego modelu wzrokowego wykrywania zabawki, zalecamy korzystanie z [tych obrazów](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) , gdy zostanie wyświetlony monit o [wybranie obrazów szkoleniowych](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector#choose-training-images) w przewodniku Szybki Start.
* W sekcji obraz Tagowany przewodnika Szybki Start upewnij się, że jest to znakowanie wózka zabawki widocznego na zdjęciu ze znacznikiem — "wózek dostawcze".

Po zakończeniu, jeśli model jest gotowy zgodnie z oczekiwaniami, można go wyeksportować do kontenera Docker za pomocą przycisku Eksportuj na karcie wydajność. Upewnij się, że jako typ platformy kontenera wybrano system Linux. Jest to platforma, na której zostanie uruchomiony kontener. Komputer, na którym pobierany jest kontener, może być w systemie Windows lub Linux. Poniższe instrukcje zostały oparte na pliku kontenera pobranym na komputer z systemem Windows.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Przegląd Custom Vision"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            To polecenie testuje kontener na maszynie lokalnej i jeśli obraz ma taki sam ciężar dostawy, jak w przypadku przeszkolonego modelu, dane wyjściowe powinny wyglądać podobnie jak poniżej, sugerując, że ciężar dostawy został wykryty z prawdopodobieństwem 90,12%.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>Sprawdzanie plików przykładowych

1. W programu vscode, przejdź do "src/Edge". Zostanie wyświetlony plik ENV, który został utworzony wraz z kilkoma plikami szablonu wdrożenia.

    Szablon wdrożenia odwołuje się do manifestu wdrożenia urządzenia brzegowego z niektórymi wartościami zastępczymi. Plik ENV ma wartości dla tych zmiennych.
1. Następnie przejdź do folderu "src/Cloud-to-Device-App". W tym miejscu zostanie wyświetlony appsettings.jspliku, który został utworzony wraz z kilkoma innymi plikami:

    * C2D-Console-App. csproj — jest to plik projektu dla programu vscode.
    * operations.jsw tym pliku zostaną wystawione różne operacje, które mają być uruchamiane przez program.
    * Program.cs — jest to przykładowy kod programu, który wykonuje następujące czynności:

        * Ładuje ustawienia aplikacji.
        * Wywołaj analizę filmów wideo na żywo na metody bezpośrednie modułu IoT Edge module, aby utworzyć topologię, utworzyć wystąpienie grafu i aktywować Graf.
        * Wstrzymuje pracę, aby przeanalizować dane wyjściowe grafu w oknie terminalu oraz zdarzenia wysyłane do usługi IoT Hub w oknie danych wyjściowych.
        * Dezaktywuj wystąpienie grafu, Usuń wystąpienie grafu i Usuń topologię grafu.
        
## <a name="generate-and-deploy-the-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrożenia

1. W programu vscode przejdź do "src/Cloud-to-Device-Console-App/operations.json"

1. W obszarze GraphTopologySet upewnij się, że są spełnione następujące kwestie:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. W obszarze GraphInstanceSet upewnij się, że: 
    1. "topologyname": "InferencingWithHttpExtension"
    1. Dodaj następujący wiersz na początku tablicy parametrów- `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Zmień wartość parametru rtspUrl na – "rtsp://rtspsim:554/media/t2.mkv"    
1. W obszarze GraphTopologyDelete upewnij się, że jest "name": "InferencingWithHttpExtension"
1. Kliknij prawym przyciskiem myszy plik "src/Edge/deployment.customvision.template.json", a następnie kliknij pozycję **generuj IoT Edge manifest wdrożenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Przegląd Custom Vision" obok okienka AZURE IOT HUB w lewym dolnym rogu. Można skopiować ciąg z appsettings.jspliku. (Oto inne zalecane podejście, aby upewnić się, że w programu vscode są skonfigurowane odpowiednie IoT Hub za pomocą [polecenia wybierz Centrum IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Przegląd Custom Vision":::
1. Następnie kliknij prawym przyciskiem myszy pozycję "src/Edge/config/deployment.customvision.amd64.json", a następnie kliknij pozycję **Utwórz wdrożenie dla jednego urządzenia**. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Przegląd Custom Vision":::
1. Zostanie wyświetlony monit o wybranie urządzenia IoT Hub. Z listy rozwijanej wybierz pozycję LVA-Sample-Device.
1. W ciągu około 30 sekund Odśwież usługę Azure IOT Hub w lewym dolnym rogu, a urządzenie brzegowe powinno być wdrożone przy użyciu następujących modułów:

    * Na żywo analiza filmów wideo w IoT Edge module o nazwie "lvaEdge".
    * Moduł o nazwie `rtspsim` , który symuluje serwer RTSP, działa jako źródło kanału informacyjnego wideo na żywo.
    * Moduł o nazwie `cv` , który jest sugerowany przez nazwę, jest modelem wykrywania samochodów Custom Vision Zabawka, który stosuje niestandardową wizję do obrazów i zwraca wiele typów tagów. (Nasz model był szkolony tylko w jednym tagu — "wózek dostawcze").

## <a name="prepare-for-monitoring-events"></a>Przygotowanie do monitorowania zdarzeń

Kliknij prawym przyciskiem myszy urządzenie analizy wideo na żywo, a następnie wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**. Ten krok jest wymagany do monitorowania zdarzeń IoT Hub w oknie danych wyjściowych Visual Studio Code.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Przegląd Custom Vision":::

## <a name="run-the-sample-program"></a>Uruchamianie przykładowego programu

Jeśli otworzysz topologię grafu w tym samouczku w przeglądarce, zobaczysz, że wartość inferencingUrl została ustawiona na http://cv:80/image , co oznacza, że serwer wnioskowania zwróci wyniki po wykryciu wózków z zabawkami (jeśli istnieją) w wideo na żywo.

1. Aby rozpocząć sesję debugowania, wybierz klawisz F5. W oknie terminalu są wyświetlane komunikaty.
1. operations.jsw kodzie zaczyna się od wywołania metod bezpośrednich GraphTopologyList i GraphInstanceList. Jeśli wyczyszczono zasoby po ukończeniu poprzednich przewodników Szybki Start, proces ten spowoduje zwrócenie pustych list, a następnie wstrzymanie. Aby kontynuować, wybierz klawisz ENTER.
    
   W oknie terminalu zostanie wyświetlony następny zestaw wywołań metod bezpośrednich:
    
   * Wywołanie GraphTopologySet z poprzednią topologyUrl.
   * Wywołanie GraphInstanceSet, które korzysta z następującej treści:
        
   ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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
    
   * Wywołanie GraphInstanceActivate, które uruchamia wystąpienie grafu i przepływ wideo.
   * Drugie wywołanie GraphInstanceList, które pokazuje, że wystąpienie grafu jest w stanie uruchomienia.
    
1. Dane wyjściowe w oknie terminalu są wstrzymywane po naciśnięciu klawisza ENTER w celu kontynuacji monitu. Nie zaznaczaj jeszcze klawisza ENTER. Przewiń w górę, aby wyświetlić ładunki odpowiedzi JSON dla wywoływanych metod bezpośrednich.
1. Przejdź do okna dane wyjściowe w Visual Studio Code. Zobaczysz komunikat informujący o tym, że usługa Live Video Analytics w IoT Edge module wysyła do centrum IoT Hub. W poniższej sekcji tego samouczka omówiono te komunikaty.
1. Wykres multimedialny będzie nadal uruchamiany i drukować wyniki. Symulator RTSP utrzymuje zapętlenie źródłowego wideo. Aby zatrzymać Graf multimedialny, Wróć do okna terminalu i wybierz klawisz ENTER.
Kolejna seria wywołań czyści zasoby:
    
   * Wywołanie GraphInstanceDeactivate dezaktywuje wystąpienie grafu.
   * Wywołanie GraphInstanceDelete usuwa wystąpienie.
   * Wywołanie GraphTopologyDelete powoduje usunięcie topologii.
   * Końcowe wywołanie GraphTopologyList pokazuje, że lista jest pusta.
    
## <a name="interpret-the-results"></a>Interpretacja wyników

Po uruchomieniu grafu multimediów wyniki z węzła procesora rozszerzenia HTTP przechodzą przez węzeł ujścia IoT Hub do centrum IoT Hub. Komunikaty widoczne w oknie danych wyjściowych zawierają sekcję treść i sekcję applicationProperties. Aby uzyskać więcej informacji, zobacz [Tworzenie i odczytywanie wiadomości IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

W poniższych komunikatach moduł analizy wideo na żywo definiuje właściwości aplikacji i zawartość treści.

### <a name="mediasessionestablished-event"></a>Zdarzenie MediaSessionEstablished

Po utworzeniu wystąpienia grafu nośnika węzeł źródłowy RTSP próbuje połączyć się z serwerem RTSP, który działa w kontenerze rtspsim-live555. Jeśli połączenie zakończy się pomyślnie, zostanie wydrukowane następujące zdarzenie. Typem zdarzenia jest Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

W tym komunikacie Zwróć uwagę na następujące informacje:

* Komunikat jest zdarzeniem diagnostycznym. MediaSessionEstablished wskazuje, że węzeł źródłowy RTSP (podmiot) połączony z symulatorem RTSP i rozpoczął odbieranie strumieniowego kanału na żywo (symulowane).
* W applicationProperties, temat wskazuje, że komunikat został wygenerowany z węzła źródłowego RTSP na grafie multimediów.
* W applicationProperties, typ zdarzenia wskazuje, że to zdarzenie jest zdarzeniem diagnostycznym.
* EventTime wskazuje godzinę wystąpienia zdarzenia.
* Treść zawiera dane dotyczące zdarzenia diagnostyki. W takim przypadku dane obejmują szczegóły [protokołu Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol) .

### <a name="inference-event"></a>Zdarzenie wnioskowania

Węzeł procesora rozszerzenia HTTP odbiera wyniki wnioskowania z kontenera Custom Vision i emituje wyniki za pośrednictwem węzła ujścia IoT Hub jako zdarzenia wnioskowania.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

W powyższych komunikatach należy zwrócić uwagę na następujące kwestie:

* Podmiot w applicationProperties odwołuje się do węzła w MediaGraph, z którego wiadomość została wygenerowana. W takim przypadku komunikat pochodzi z procesora rozszerzenia http.
* Element eventType w applicationProperties wskazuje, że jest to zdarzenie wnioskowania o analizie.
* EventTime wskazuje godzinę wystąpienia zdarzenia.
* "treść" zawiera dane dotyczące zdarzenia analizy. W tym przypadku zdarzenie jest zdarzeniem wnioskowania, w związku z czym treść zawiera tablicę wniosków o nazwie "przewidywania".
* sekcja "przewidywania" zawiera listę prognoz, w których w ramce znajduje się wózek do dostarczania zabawka (tag = wózek dostawczy). Jak można się odwołać, ciężarówka dostarczająca jest tagiem niestandardowym, który został dostarczony do niestandardowego modelu przeszkolonego dla wózka zabawki, a model jest inferencing i identyfikujący ciężarówkę zabawki w wejściowym wideo z różnymi wynikami zaufania prawdopodobieństwa.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz wypróbować inne samouczki lub Przewodniki Szybki Start, należy zamieścić do utworzonych zasobów. W przeciwnym razie przejdź do obszaru Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten samouczek, i Usuń wszystkie zasoby.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dodatkowymi wyzwaniami dla zaawansowanych użytkowników:

* Użyj [kamery IP](https://en.wikipedia.org/wiki/IP_camera) , która obsługuje protokół RTSP, zamiast korzystać z symulatora RTSP. Można wyszukać kamery IP obsługujące protokół RTSP na stronie [ONVIF zgodnych](https://www.onvif.org/conformant-products/) produktów. Wyszukaj urządzenia zgodne z profilami G, S lub T.
* Użyj urządzenia z systemem AMD64 lub x64 zamiast z maszyną wirtualną z systemem Linux systemu Azure. To urządzenie musi znajdować się w tej samej sieci co kamera IP. Można postępować zgodnie z instrukcjami w temacie [Install Azure IoT Edge Runtime on Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux). 

Następnie Zarejestruj urządzenie w usłudze Azure IoT Hub, postępując zgodnie z instrukcjami podanymi w temacie [Wdróż swój pierwszy IoT Edge module na wirtualnym urządzeniu z systemem Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).

