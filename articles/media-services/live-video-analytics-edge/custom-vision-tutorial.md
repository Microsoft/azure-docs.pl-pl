---
title: Analizuj wideo na żywo dzięki usłudze Live Video Analytics na IoT Edge i platformie Azure Custom Vision
description: Dowiedz się, jak za pomocą usługi Azure Custom Vision utworzyć model z kontenerem, który może wykrywać wózek i korzystać z rozszerzalności zawartości platformy Azure na żywo na platformie Azure IoT Edge, aby wdrożyć model na brzegu na potrzeby wykrywania wózków Zabawka z poziomu strumienia wideo na żywo.
ms.topic: tutorial
ms.date: 09/08/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: 1abf123883a89bb41909e8aa67aedfadffc3d37e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561206"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Samouczek: analizowanie wideo na żywo za pomocą analizy filmów wideo na żywo na IoT Edge i na platformie Azure Custom Vision

W ramach tego samouczka dowiesz się, jak za pomocą usługi Azure [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) utworzyć model z kontenerem, który może wykrywać wózek i korzystać z [rozszerzalności AI](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) usługi Azure Live Video Analytics w Azure IoT Edge, aby wdrożyć model na granicy do wykrywania wózków Zabawka z poziomu strumienia wideo na żywo.

Pokażemy Ci, jak połączyć się z możliwością Custom Vision, aby kompilować i uczenie modelu przetwarzania obrazów przez przekazywanie i etykietowanie kilku obrazów. Nie potrzebujesz żadnej wiedzy o nauce danych, uczeniu maszynowym ani AI. Zapoznaj się również z możliwościami usługi Analiza filmów wideo na żywo, aby łatwo wdrożyć niestandardowy model jako kontener na krawędzi i analizować symulowane kanały wideo na żywo.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/custom-vision-tutorial/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/custom-vision-tutorial/python/header.md)]
::: zone-end

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj odpowiednie zasoby.
> * Utwórz model Custom Vision w chmurze, aby wykryć wózki zabawki i wdrożyć go na krawędzi.
> * Utwórz i Wdróż Graf multimedialny z rozszerzeniem HTTP w modelu Custom Vision.
> * Uruchom przykładowy kod.
> * Przeanalizuj i Interpretuj wyniki.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem  

Przed rozpoczęciem Przeczytaj następujące artykuły:

* [Analiza filmów wideo na żywo na IoT Edge przegląd](overview.md)
* [Omówienie usługi Azure Custom Vision](../../cognitive-services/custom-vision-service/overview.md)
* [Analiza filmów wideo na żywo na IoT Edge terminologii](terminology.md)
* [Koncepcje grafu multimediów](media-graph-concept.md)
* [Platforma Live Video Analytics bez nagrania wideo](analyze-live-video-concept.md)
* [Uruchamianie analizy filmów wideo na żywo z własnym modelem](use-your-model-quickstart.md)
* [Samouczek: opracowywanie modułu IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Jak edytować wdrożenie. * .template.jsna](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Wymagania wstępne

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/python/prerequisites.md)]
::: zone-end

> [!IMPORTANT]
> Ten moduł Custom Vision obsługuje tylko architektury **Intel x86 i amd64** . Przed kontynuowaniem sprawdź architekturę urządzenia brzegowego.

## <a name="review-the-sample-video"></a>Zapoznaj się z przykładowym wideo

W tym samouczku do symulowania strumienia na żywo jest wykorzystywany plik wideo dotyczący [wywnioskowania samochodu](https://lvamedia.blob.core.windows.net/public/t2.mkv) . Film wideo można przeanalizować za pomocą aplikacji, takiej jak [VLC Media Player](https://www.videolan.org/vlc/). Wybierz **kombinację klawiszy CTRL + N**, a następnie wklej link do [filmu wideo dotyczącego wnioskowania samochodu](https://lvamedia.blob.core.windows.net/public/t2.mkv) , aby rozpocząć odtwarzanie. Gdy oglądasz film wideo, pamiętaj, że w filmie o 36-sekundowym znakiem wózka zabawki. Model niestandardowy został przeszkolony w celu wykrywania określonego wózka zabawki. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LPwK]

W tym samouczku użyjesz usługi Analiza filmów wideo na żywo na IoT Edge, aby wykryć takie wózki i publikować powiązane zdarzenia wnioskowania do centrum IoT Edge.

## <a name="overview"></a>Omówienie

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Diagram przedstawiający Custom Vision przegląd.":::

Ten diagram przedstawia sposób przepływu sygnałów w tym samouczku. [Moduł graniczny](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) symuluje kamerę IP obsługującą serwer Real-Time Streaming Protocol (RTSP). Węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do węzła [procesora rozszerzenia http](media-graph-concept.md#http-extension-processor) .

Węzeł rozszerzenia HTTP pełni rolę serwera proxy.  Próbki przychodzącej ramki wideo ustawionej przy użyciu pola, `samplingOptions` a także konwertuje ramki wideo na określony typ obrazu. Następnie przekazuje obraz za pośrednictwem REST do innego modułu krawędzi, który uruchamia model AI za punktem końcowym HTTP. W tym przykładzie moduł Edge jest modelem wykrywacza samochodów, który został utworzony przy użyciu Custom Vision. Węzeł procesora rozszerzenia HTTP zbiera wyniki wykrywania i publikuje zdarzenia w węźle [ujścia usługi Azure IoT Hub](media-graph-concept.md#iot-hub-message-sink) . Następnie węzeł wysyła te zdarzenia do [centrum IoT Edge](../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Kompilowanie i wdrażanie modelu wykrywania zabawki Custom Vision 

Jak nazwa Custom Vision sugeruje, możesz użyć jej do utworzenia własnego niestandardowego detektora obiektów lub klasyfikatora w chmurze. Zapewnia prosty, łatwy w użyciu i intuicyjny interfejs do kompilowania modeli Custom Vision, które można wdrożyć w chmurze lub na brzegu za pośrednictwem kontenerów.

Aby skompilować wykrywacz samochodów, wykonaj kroki opisane w [przewodniku szybki start: Tworzenie detektora obiektów przy użyciu witryny sieci web Custom Vision](../../cognitive-services/custom-vision-service/get-started-build-detector.md).

> [!IMPORTANT]
> Ten moduł Custom Vision obsługuje tylko architektury **Intel x86 i amd64** . Przed kontynuowaniem sprawdź architekturę urządzenia brzegowego.

Dodatkowe uwagi:
 
* W tym samouczku nie należy używać przykładowych obrazów dostarczonych w [sekcji wymagania wstępne](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites)dotyczące przewodnika Szybki Start. Zamiast tego używamy pewnego zestawu obrazów, aby kompilować Custom Vision modelem wykrywania zabawki. Użyj [tych obrazów](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) , gdy zostanie wyświetlony monit o [wybranie obrazów szkoleniowych](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images) w przewodniku Szybki Start.
* W sekcji obraz Tagowany przewodnika Szybki Start upewnij się, że jest to znakowanie wózka zabawki widocznego na zdjęciu ze znacznikiem "wózek dostawczy".

Po zakończeniu możesz wyeksportować model do kontenera Docker za pomocą przycisku **Eksportuj** na karcie **wydajność** . Upewnij się, że jako typ platformy kontenera wybrano system Linux. Jest to platforma, na której zostanie uruchomiony kontener. Komputer, na którym pobierany jest kontener, może być w systemie Windows lub Linux. Poniższe instrukcje zostały oparte na pliku kontenera pobranym na komputer z systemem Windows.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Zostanie wyświetlony ekran z wybraną pozycją pliku dockerfile.":::
 
1. Plik zip powinien zostać pobrany na komputer lokalny o nazwie `<projectname>.DockerFile.Linux.zip` . 
1. Sprawdź, czy zainstalowano platformę Docker. Jeśli nie, zainstaluj [platformę Docker](https://docs.docker.com/get-docker/) na pulpicie systemu Windows.
1. Rozpakuj pobrany plik w wybranej lokalizacji. Użyj wiersza polecenia, aby przejść do katalogu niespakowanego folderu.
    
    Uruchom następujące polecenia:
    
    1. `docker build -t cvtruck` 
    
        To polecenie pobiera wiele pakietów, kompiluje obraz platformy Docker i Tagi jako `cvtruck:latest` .
    
        > [!NOTE]
        > Jeśli to się powiedzie, powinny pojawić się następujące komunikaty: `Successfully built <docker image id>` i `Successfully tagged cvtruck:latest` . Jeśli polecenie kompilacji nie powiedzie się, spróbuj ponownie. Czasami pakiety zależności nie są pobierane po raz pierwszy.
    1. `docker  image ls`

        To polecenie sprawdza, czy nowy obraz znajduje się w rejestrze lokalnym.
    1. `docker run -p 127.0.0.1:80:80 -d cvtruck`
    
        To polecenie powinno opublikować port uwidoczniony platformy Docker (80) na porcie komputera lokalnego (80).
    1. `docker container ls`
    
        To polecenie sprawdza mapowania portów i jeśli kontener platformy Docker działa prawidłowo na komputerze. Dane wyjściowe powinny wyglądać następująco:

        ```
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
        8b7505398367        cvtruck             "/bin/sh -c 'python …"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            To polecenie testuje kontener na komputerze lokalnym. Jeśli obraz ma taki sam ciężar dostawy, jak w przypadku przeszkolonego modelu, dane wyjściowe powinny wyglądać podobnie jak w poniższym przykładzie. Sugeruje, że ciężar dostawy został wykryty z prawdopodobieństwem 90,12%.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>Sprawdzanie plików przykładowych

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrożenia

1. W Visual Studio Code przejdź do pozycji src/Cloud-to-Device-App/operations.json.

1. W obszarze `GraphTopologySet` upewnij się, że spełnione są następujące kwestie:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`
1. W obszarze `GraphInstanceSet` upewnij się, że:
    1. `"topologyName" : "InferencingWithHttpExtension"`
    1. Dodaj następujący na górze tablicy Parameters: `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Zmień `rtspUrl` wartość parametru na `"rtsp://rtspsim:554/media/t2.mkv"` .
1. W obszarze `GraphTopologyDelete` upewnij się, że `"name": "InferencingWithHttpExtension"` .
1. Kliknij prawym przyciskiem myszy plik SRC/Edge/deployment.customvision.template.jsw pliku, a następnie wybierz polecenie **generuj IoT Edge manifest wdrożenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Zrzut ekranu przedstawiający generowanie manifestu wdrażania IoT Edge.":::
  
    Ta akcja powinna utworzyć plik manifestu w folderze src/Edge/config o nazwie deployment.customvision.amd64.json.
1. Otwórz plik SRC/Edge/deployment.customvision.template.jsw pliku i Znajdź `registryCredentials` blok JSON. W tym bloku znajdziesz adres usługi Azure Container Registry wraz z nazwą użytkownika i hasłem.
1. Wypchnięcie lokalnego kontenera Custom Vision do wystąpienia Azure Container Registry, wykonując następujące kroki w wierszu polecenia:

    1. Zaloguj się do rejestru, wykonując następujące polecenie:
    
        `docker login <address>`
    
        Wprowadź nazwę użytkownika i hasło, gdy zostanie wyświetlony monit o uwierzytelnienie.
        
        > [!NOTE]
        > Hasło nie jest widoczne w wierszu polecenia.
    1. Oznacz obraz za pomocą tego polecenia: <br/>`docker tag cvtruck   <address>/cvtruck`.
    1. Wypchnij obraz za pomocą tego polecenia: <br/>`docker push <address>/cvtruck`.

        Jeśli to się powiedzie, powinno być widoczne `Pushed` w wierszu polecenia wraz z algorytmem SHA dla obrazu.
    1. Możesz również potwierdzić, sprawdzając wystąpienie Azure Container Registry w Azure Portal. W tym miejscu zobaczysz nazwę repozytorium wraz ze znacznikiem.
1. Ustaw parametry połączenia IoT Hub, wybierając ikonę **więcej akcji** obok okienka **Azure IoT Hub** w lewym dolnym rogu. Można skopiować ciąg z appsettings.jspliku. (Oto inne zalecane podejście, aby upewnić się, że skonfigurowano odpowiednie Centrum IoT Hub w ramach Visual Studio Code za pomocą [polecenia wybierz IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub)).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Zrzut ekranu przedstawiający Ustawianie parametrów połączenia IoT Hub.":::
1. Następnie kliknij prawym przyciskiem myszy pozycję src/Edge/config/deployment.customvision.amd64.jsna, a następnie wybierz pozycję **Utwórz wdrożenie dla jednego urządzenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Zrzut ekranu przedstawiający tworzenie wdrożenia dla jednego urządzenia.":::
1. Zostanie wyświetlony monit o wybranie urządzenia IoT Hub. Z listy rozwijanej wybierz pozycję **LVA-Sample-Device** .
1. W ciągu około 30 sekund Odśwież usługę Azure IoT Hub w lewej dolnej części. Urządzenie brzegowe powinno być wdrożone przy użyciu następujących modułów:

    * Analiza filmów wideo na żywo w IoT Edge module o nazwie `lvaEdge` .
    * Moduł o nazwie `rtspsim` , który symuluje serwer RTSP, który działa jako źródło kanału informacyjnego wideo na żywo.
    * Moduł o nazwie `cv` , który jest sugerowany przez nazwę, jest modelem wykrywania samochodów Custom Vision Zabawka, który ma zastosowanie Custom Vision do obrazów i zwraca wiele typów tagów. (Nasz model był szkolony tylko w jednym znaczniku, wózku dostarczającym).

## <a name="prepare-for-monitoring-events"></a>Przygotowanie do monitorowania zdarzeń

Kliknij prawym przyciskiem myszy urządzenie analizy wideo na żywo, a następnie wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**. Ten krok jest wymagany do monitorowania zdarzeń IoT Hub w oknie **danych wyjściowych** Visual Studio Code.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Zrzut ekranu pokazujący Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia.":::

## <a name="run-the-sample-program"></a>Uruchamianie przykładowego programu

Jeśli otworzysz topologię grafu dla tego samouczka w przeglądarce, zobaczysz, że wartość `inferencingUrl` została ustawiona na `http://cv:80/image` . To ustawienie oznacza, że serwer wnioskowania zwróci wyniki po rozpoczęciu wykrywania wózków zabawka (jeśli istnieją) w wideo na żywo.

1. W Visual Studio Code Otwórz kartę **rozszerzenia** (lub wybierz **kombinację klawiszy Ctrl + Shift + X**) i Wyszukaj pozycję Azure IoT Hub.
1. Kliknij prawym przyciskiem myszy i wybierz pozycję **Ustawienia rozszerzenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Zrzut ekranu pokazujący ustawienia rozszerzenia.":::
1. Wyszukaj i Włącz opcję **Pokaż pełny komunikat**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Zrzut ekranu pokazujący Wyświetlanie pełnego komunikatu.":::
1. Aby rozpocząć sesję debugowania, wybierz klawisz **F5** . W oknie **terminalu** są wyświetlane komunikaty.
1. operations.jsw kodzie zaczyna się od wywołania metod bezpośrednich `GraphTopologyList` i `GraphInstanceList` . Jeśli wyczyszczono zasoby po ukończeniu poprzednich przewodników Szybki Start, proces ten spowoduje zwrócenie pustych list, a następnie wstrzymanie. Aby kontynuować, wybierz klawisz **Enter** .
    
   W oknie **terminalu** zostanie wyświetlony następny zestaw wywołań metod bezpośrednich:
    
   * Wywołanie `GraphTopologySet` , które używa poprzedniego `topologyUrl` .
   * Wywołanie `GraphInstanceSet` , które używa następującej treści:
        
   ```
        {
          "@apiVersion": "2.0",
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
    
   * Wywołanie `GraphInstanceActivate` , które uruchamia wystąpienie grafu i przepływ wideo.
   * Drugie wywołanie `GraphInstanceList` , które pokazuje, że wystąpienie grafu jest w stanie uruchomienia.
    
1. Dane wyjściowe w oknie **terminalu** są wstrzymywane po **naciśnięciu klawisza ENTER w celu kontynuacji** monitu. Nie zaznaczaj jeszcze **klawisza ENTER** . Przewiń w górę, aby wyświetlić ładunki odpowiedzi JSON dla wywoływanych metod bezpośrednich.
1. Przejdź do okna **dane wyjściowe** w Visual Studio Code. Zobaczysz komunikat informujący o tym, że usługa Live Video Analytics w IoT Edge module wysyła do centrum IoT Hub. W poniższej sekcji tego samouczka omówiono te komunikaty.
1. Wykres multimedialny będzie nadal uruchamiany i drukować wyniki. Symulator RTSP utrzymuje zapętlenie źródłowego wideo. Aby zatrzymać Graf multimedialny, Wróć do okna **terminalu** i wybierz **klawisz ENTER**.
Kolejna seria wywołań czyści zasoby:
    
   * Wywołanie `GraphInstanceDeactivate` dezaktywowania wystąpienia grafu.
   * Wywołanie `GraphInstanceDelete` usuwania wystąpienia.
   * Wywołanie `GraphTopologyDelete` usunięcia topologii.
   * Końcowe wywołanie `GraphTopologyList` pokazujące, że lista jest pusta.
    
## <a name="interpret-the-results"></a>Interpretacja wyników

Po uruchomieniu grafu multimediów wyniki z węzła procesora rozszerzenia HTTP przechodzą przez węzeł ujścia IoT Hub do centrum IoT Hub. Komunikaty widoczne w oknie **danych wyjściowych** zawierają sekcję treść i `applicationProperties` sekcję. Aby uzyskać więcej informacji, zobacz [Tworzenie i odczytywanie wiadomości IoT Hub](../../iot-hub/iot-hub-devguide-messages-construct.md).

W poniższych komunikatach moduł analizy wideo na żywo definiuje właściwości aplikacji i zawartość treści.

### <a name="mediasessionestablished-event"></a>Zdarzenie MediaSessionEstablished

Po utworzeniu wystąpienia grafu nośnika węzeł źródłowy RTSP próbuje połączyć się z serwerem RTSP, który działa w kontenerze rtspsim-live555. Po pomyślnym nawiązaniu połączenia zostanie wydrukowane następujące zdarzenie. Typem zdarzenia jest **Microsoft. Media. MediaGraph. Diagnostics. MediaSessionEstablished**.

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

* Komunikat jest zdarzeniem diagnostycznym. `MediaSessionEstablished` wskazuje, że węzeł źródłowy RTSP (podmiot) połączony z symulatorem RTSP i zaczął otrzymywać symulowane kanały informacyjne na żywo.
* W `applicationProperties` programie `subject` wskazuje, że komunikat został wygenerowany z węzła źródła RTSP na grafie nośnika.
* W programie `applicationProperties` Typ zdarzenia wskazuje, że to zdarzenie jest zdarzeniem diagnostycznym.
* Czas zdarzenia wskazuje czas wystąpienia zdarzenia.
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

Zwróć uwagę na następujące informacje w poprzednich komunikatach:

* Podmiot w programie `applicationProperties` odwołuje się do węzła w MediaGraph, z którego wiadomość została wygenerowana. W takim przypadku komunikat pochodzi z procesora rozszerzenia HTTP.
* Typ zdarzenia w programie `applicationProperties` wskazuje, że jest to zdarzenie wnioskowania o analizie.
* Czas zdarzenia wskazuje czas wystąpienia zdarzenia.
* Treść zawiera dane dotyczące zdarzenia analizy. W takim przypadku zdarzenie jest zdarzeniem wnioskowania, dlatego treść zawiera tablicę wniosków o nazwie przewidywania.
* Sekcja przewidywania zawiera listę prognoz, w których w ramce zostanie znaleziony ciężar dostawy zabawki (tag "wózek dostawczy"). W miarę odwoływania "wózek dostawczy" jest niestandardowym tagiem dostarczonym do niestandardowego, przeszkolonego modelu dla wózka zabawki. Model wnioskuje i identyfikuje wózka zabawki w wejściowym wideo z różnymi wynikami pewności dopasowania prawdopodobieństwa.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zamierzasz wypróbować inne samouczki lub Przewodniki Szybki Start, zaczekaj na utworzone zasoby. W przeciwnym razie przejdź do obszaru Azure Portal, przejdź do grup zasobów, wybierz grupę zasobów, w której uruchomiono ten samouczek, i Usuń wszystkie zasoby.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dodatkowymi wyzwaniami dla zaawansowanych użytkowników:

* Użyj [kamery IP](https://en.wikipedia.org/wiki/IP_camera) , która obsługuje protokół RTSP, zamiast korzystać z symulatora RTSP. Można wyszukać kamery IP obsługujące protokół RTSP na stronie [ONVIF zgodnych](https://www.onvif.org/conformant-products/) produktów. Wyszukaj urządzenia zgodne z profilami G, S lub T.
* Użyj urządzenia z systemem AMD64 lub x64 zamiast z maszyną wirtualną z systemem Linux systemu Azure. To urządzenie musi znajdować się w tej samej sieci co kamera IP. Można postępować zgodnie z instrukcjami w temacie [Install Azure IoT Edge Runtime on Linux](../../iot-edge/how-to-install-iot-edge.md).

Następnie Zarejestruj urządzenie w usłudze Azure IoT Hub, postępując zgodnie z instrukcjami podanymi w temacie [Wdróż swój pierwszy IoT Edge module na wirtualnym urządzeniu z systemem Linux](../../iot-edge/quickstart-linux.md).