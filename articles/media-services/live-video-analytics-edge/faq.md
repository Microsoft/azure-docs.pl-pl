---
title: Analiza filmów wideo na żywo na IoT Edge często zadawane pytania — Azure
description: Ten temat zawiera odpowiedzi na żywo analizy wideo na IoT Edge często zadawanych pytań.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 521cd0e4f5fc8232a000e10520298a979ba1c14c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401580"
---
# <a name="frequently-asked-questions-faqs"></a>Często zadawane pytania

Ten temat zawiera odpowiedzi na żywo analizy wideo na IoT Edge często zadawanych pytań.

## <a name="general"></a>Ogólne

### <a name="what-are-the-system-variables-that-can-be-used-in-graph-topology-definition"></a>Jakie są zmienne systemowe, których można użyć w definicji topologii wykresu?

|Zmienna   |Opis|
|---|---|
|[System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Reprezentuje chwilę czasu UTC, zwykle wyrażoną jako datę i godzinę dnia (podstawowa reprezentacja yyyyMMddTHHmmssZ).|
|System. PreciseDateTime|Reprezentuje wystąpienie daty UTC w formacie zgodnym z ISO8601 pliku z milisekundami (podstawowa reprezentacja yyyyMMddTHHmmss. fffZ).|
|System. GraphTopologyName   |Reprezentuje topologię grafu multimediów, która zawiera plan grafu.|
|System. GraphInstanceName|  Reprezentuje wystąpienie grafu nośnika, przechowuje wartości parametrów i odwołuje się do topologii.|

## <a name="configuration-and-deployment"></a>Konfiguracja i wdrażanie

### <a name="can-i-deploy-the-media-edge-module-to-a-windows-10-device"></a>Czy mogę wdrożyć moduł Media Edge na urządzeniu z systemem Windows 10?

Tak. Zapoznaj się z artykułem na temat [kontenerów systemu Linux w systemie Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Przechwyć z ustawień aparatu IP i RTSP

### <a name="do-i-need-to-use-a-special-sdk-on-my-device-to-send-in-a-video-stream"></a>Czy muszę użyć specjalnego zestawu SDK na urządzeniu, aby wysyłać strumień wideo?

Nie. Analiza filmów wideo na żywo na IoT Edge obsługuje przechwytywanie multimediów przy użyciu protokołu przesyłania strumieniowego RTSP wideo (który jest obsługiwany przez większość kamer IP).

### <a name="can-i-push-media-to-live-video-analytics-on-iot-edge-using-rtmp-or-smooth-like-a-media-services-live-event"></a>Czy można wypchnąć multimedia do analizy wideo na żywo na IoT Edge przy użyciu protokołu RTMP lub Wygładź (na przykład zdarzenia Media Services na żywo)?

* Nie. Analiza wideo na żywo obsługuje tylko protokół RTSP do przechwytywania wideo z kamer IP.
* Wszystkie aparaty obsługujące przesyłanie strumieniowe RTSP za pośrednictwem protokołu TCP/HTTP powinny funkcjonować. 

### <a name="can-i-reset-or-update-the-rtsp-source-url-on-a-graph-instance"></a>Czy mogę zresetować lub zaktualizować adres URL źródła RTSP w wystąpieniu grafu?

Tak, gdy wystąpienie grafu jest w stanie nieaktywnym.  

### <a name="is-there-a-rtsp-simulator-available-to-use-during-testing-and-development"></a>Czy jest dostępny symulator RTSP do użycia podczas testowania i opracowywania?

Tak. Dostępny jest moduł brzegowy [symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) do użycia w przewodnikach Szybki Start i samouczkach do obsługi procesu nauki. Ten moduł jest udostępniany w miarę możliwości i może nie być zawsze dostępny. Zdecydowanie zaleca się, aby nie używać go przez więcej niż kilka godzin. Przed utworzeniem planów dla wdrożenia produkcyjnego należy zainwestować w testy z rzeczywistym źródłem RTSP.

### <a name="do-you-support-onvif-discovery-of-ip-cameras-at-the-edge"></a>Czy jest obsługiwane odnajdywanie kamer IP zgodne ze standardem ONVIF w sieci brzegowej?

Nie, odnajdywanie urządzeń w sieci brzegowej zgodne ze standardem ONVIF nie jest obsługiwane.

## <a name="streaming-and-playback"></a>Przesyłanie strumieniowe i odtwarzanie

### <a name="can-assets-recorded-to-ams-from-the-edge-be-played-back-using-media-services-streaming-technologies-like-hls-or-dash"></a>Czy zasoby mogą być rejestrowane w ramach AMS z brzegu, przy użyciu Media Services technologii przesyłania strumieniowego, takich jak HLS czy łącznik?

Tak. Zarejestrowane zasoby mogą być przesyłane strumieniowo jak każdy inny zasób w Azure Media Services. Aby przesłać strumieniowo zawartość, musisz mieć utworzony punkt końcowy przesyłania strumieniowego i w stanie uruchomienia. Użycie standardowego procesu tworzenia lokalizatora przesyłania strumieniowego zapewnia dostęp do manifestu HLS lub PAUZy na potrzeby przesyłania strumieniowego do dowolnych platform odtwarzacza. Aby uzyskać szczegółowe informacje na temat tworzenia HLS publikowanych lub manifestów PAUZy, zobacz [dynamiczne pakowanie](../latest/dynamic-packaging-overview.md).

### <a name="can-i-use-the-standard-content-protection-and-drm-features-of-media-services-on-an-archived-asset"></a>Czy można używać standardowych funkcji ochrony zawartości i technologii DRM Media Services na zarchiwizowanym elemencie zawartości?

Tak. Wszystkie standardowe funkcje dynamicznej ochrony zawartości i funkcji DRM są dostępne do użycia w zasobach zarejestrowanych z grafu multimedialnego.

### <a name="what-players-can-i-use-to-view-content-from-the-recorded-assets"></a>Jakich uczestników mogę używać do wyświetlania zawartości z zarejestrowanych zasobów?

Obsługiwane są wszystkie standardowe odtwarzacze obsługujące zgodną HTTP Live Streaming firmy Apple (HLS) w wersji 3 lub 4. Ponadto obsługiwany jest również każdy odtwarzacz, który jest w stanie zgodne z odtwarzaniem za PAUZą MPEG.

Zalecane gracze do testowania obejmują:

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka Player](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Natywna HTTP Live Streaming firmy Apple](https://developer.apple.com/streaming/)
* Przeglądarki Edge, Chrome lub Safari skompilowane w odtwarzaczu wideo HTML5
* Gracze komercyjni obsługujące HLS lub odtwarzanie KRESek

### <a name="what-are-the-limits-on-streaming-a-media-graph-asset"></a>Jakie są limity przesyłania strumieniowego zasobów grafu multimediów?

Przesyłanie strumieniowe zawartości na żywo lub nagranie z grafu multimediów korzysta z tej samej infrastruktury o dużej skali i punktu końcowego przesyłania strumieniowego, który Media Services obsługuje na potrzeby przesyłania strumieniowego na żądanie i na żywo w przypadku multimediów & rozrywki, OTT i emisji. Oznacza to, że można szybko i łatwo włączyć Azure CDN, Verizon lub Akamai, aby dostarczyć zawartość do odbiorców jako mały jako kilku osób przeglądających lub do milionów w zależności od Twojego scenariusza.

Zawartość można dostarczyć przy użyciu zarówno Apple HTTP Live Streaming (HLS), jak i MPEG-KRESKowego.

## <a name="design-your-ai-model"></a>Projektowanie modelu AI 

### <a name="i-have-multiple-ai-models-wrapped-in-a-docker-container-how-should-i-use-them-with-live-video-analytics"></a>Mam wiele modeli AI opakowanych w kontener platformy Docker. Jak używać ich w usłudze Live Video Analytics? 

Rozwiązania różnią się w zależności od protokołu komunikacyjnego używanego przez serwer inferencing do komunikowania się z analizą wideo na żywo. Poniżej przedstawiono kilka sposobów wykonania tej czynności.

#### <a name="http-protocol"></a>Protokół HTTP:

* Pojedynczy kontener (Single lvaExtension):  

   Na serwerze inferencing można użyć jednego portu, ale innych punktów końcowych dla różnych modeli AI. Przykładowo w przypadku przykładu w języku Python można użyć różnych `route` s na model jako: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   A następnie w rozmieszczeniu analizy wideo na żywo podczas tworzenia wystąpienia grafów należy ustawić adres URL serwera wnioskowania dla każdego wystąpienia jako: 

   pierwsze wystąpienie: adres URL serwera wnioskowania =`http://lvaExtension:44000/score/face_detection`<br/>
   drugie wystąpienie: adres URL serwera wnioskowania =`http://lvaExtension:44000/score/vehicle_detection`  
    > [!NOTE]
    > Alternatywnie można również uwidocznić modele AI na różnych portach i wywoływać je podczas tworzenia wystąpienia grafów.  

* Wiele kontenerów: 

   Każdy kontener jest wdrażany z inną nazwą. Obecnie w zestawie dokumentacji usługi Live Video Analytics pokazano, jak wdrożyć rozszerzenie o nazwie: **lvaExtension**. Teraz można opracowywać dwa różne kontenery. Każdy kontener ma ten sam interfejs HTTP (oznacza ten sam `/score` punkt końcowy). Wdróż te dwa kontenery z różnymi nazwami i upewnij się, że oba nasłuchują na **różnych portach**. 

   Na przykład jeden kontener z nazwą `lvaExtension1` nasłuchuje portu `44000` , a inny kontener o nazwie `lvaExtension2` nasłuchuje na porcie `44001` . 

   W topologii analizy wideo na żywo tworzysz dwa wykresy z różnymi adresami URL wnioskowania, takimi jak: 

   Pierwsze wystąpienie: adres URL serwera wnioskowania = `http://lvaExtension1:44001/score`    
   Drugie wystąpienie: adres URL serwera wnioskowania = `http://lvaExtension2:44001/score`
   
#### <a name="grpc-protocol"></a>GRPC: 

W przypadku korzystania z protokołu wideo w trybie Live Video Analytics 1,0, jeśli używany jest protokół gRPC, jedynym sposobem, gdyby serwer gRPC uwidaczniał różne modele AI za pośrednictwem różnych portów. W [tym przykładzie](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json)istnieje pojedynczy port 44000, który uwidacznia wszystkie modele Yolo. Teoretycznie można napisać serwer Yolo gRPC, aby udostępnić niektóre modele w 44000, inne o 45000,... 

W przypadku 2,0 modułu gRPC Video Analytics na żywo Nowa właściwość jest dodawana do węzła rozszerzenia. Ta właściwość jest nazywana **extensionConfiguration** , który jest opcjonalnym ciągiem, który może być używany jako część kontraktu gRPC. Jeśli masz wiele modeli AI spakowanych na pojedynczym serwerze wnioskowania, nie trzeba ujawniać węzła dla każdego modelu AI. Zamiast tego, w przypadku wystąpienia grafu, Dostawca rozszerzeń (ty) można zdefiniować, jak wybrać różne modele AI przy użyciu właściwości **extensionConfiguration** i podczas wykonywania, analiza wideo na żywo przekaże ten ciąg do serwera inferencing, który może użyć tego do wywołania żądanego modelu AI. 

### <a name="i-am-building-a-grpc-server-around-an-ai-model-and-want-to-be-able-to-support-being-used-by-multiple-camerasgraph-instances-how-should-i-build-my-server"></a>Tworzymy serwer gRPC wokół modelu AI i chcesz mieć możliwość obsługi wielu kamer/wystąpień grafów. Jak należy skompilować serwer? 

 Po pierwsze upewnij się, że serwer może obsłużyć więcej niż jedno żądanie jednocześnie. Lub upewnij się, że serwer działa w wątkach równoległych. 

Na przykład w jednym z [przykładów GRPC analizy wideo na żywo](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py)istnieje domyślna liczba ustawionych kanałów równoległych. Zobacz: 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

W powyższej instancji serwera gRPC serwer może otworzyć tylko trzy kanały na kamerę (w przypadku wystąpienia topologii wykresu) naraz. Nie należy próbować łączyć więcej niż trzech wystąpień z serwerem. Jeśli spróbujesz otworzyć więcej niż trzy kanały, żądania będą oczekiwane do momentu porzucania.  

Powyższa implementacja serwera gRPC jest używana w naszych przykładach języka Python. Deweloperzy mogą zaimplementować własne serwery lub w powyższej domyślnej implementacji można zwiększyć numer procesu roboczego dla liczby kamer używanych do pobierania źródła wideo. 

Aby skonfigurować i użyć wielu kamer, deweloperzy mogą utworzyć wystąpienia wielu wystąpień topologii wykresu, gdzie każde wystąpienie wskazuje na ten sam lub inny serwer wnioskowania (na przykład serwer wymieniony w powyższym akapicie). 

### <a name="i-want-to-be-able-to-receive-multiple-frames-from-upstream-before-i-make-an-inferencing-decision-how-can-i-enable-that"></a>Chcę mieć możliwość otrzymywania wielu ramek z nadrzędnego przed podjęciem decyzji inferencing. Jak można ją włączyć? 

Bieżące [domyślne przykłady](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) działają w trybie bezstanowym. Te próbki nie zachowują stanu poprzednich wywołań, a nawet osoby, które wywołały (oznacza to, że wiele wystąpień topologii może wywoływać ten sam serwer wnioskowania, a serwer nie będzie mógł odróżnić, kto jest wywoływany i stan na obiekt wywołujący) 

#### <a name="http-protocol"></a>Protokół HTTP

W przypadku korzystania z protokołu HTTP: 

Aby zachować stan, każdy obiekt wywołujący (wystąpienie topologii grafu) wywoła serwer inferencing z parametrem zapytania HTTP unikatowym dla elementu wywołującego. Na przykład adres URL serwera wnioskowania dla  

pierwsze wystąpienie topologii = `http://lvaExtension:44000/score?id=1`<br/>
drugie wystąpienie topologii = `http://lvaExtension:44000/score?id=2`

… 

Po stronie serwera trasa oceny będzie wiedzieć, kto wywołuje. Jeśli identyfikator = 1, można zachować stan oddzielnie dla tego obiektu wywołującego (wystąpienie topologii grafu). Następnie można zachować odebrane ramki wideo w buforze (na przykład Array lub słowniku z kluczem DateTime, a wartość to ramka), a następnie zdefiniować serwer do przetwarzania (wnioskowania) po odebraniu ramek x. 

#### <a name="grpc-protocol"></a>Protokół GRPC 

W przypadku korzystania z protokołu gRPC: 

W przypadku rozszerzenia gRPC każda sesja dotyczy pojedynczego źródła danych, więc nie ma potrzeby podawania identyfikatora. Teraz z właściwością extensionConfiguration można przechowywać klatki wideo w buforze i definiować serwer do przetwarzania (wnioskowania) po odebraniu ramek x. 

### <a name="do-all-processmediastreams-on-a-particular-container-run-the-same-ai-model"></a>Czy wszystkie ProcessMediaStreams w określonym kontenerze mają ten sam model AI? 

Nie.  

Wywołania Start/Stop z użytkownika końcowego w wystąpieniu grafu stanowią sesję lub prawdopodobnie istnieje połączenie/ponowne połączenie z aparatem. Celem jest utrzymywanie jednej sesji, jeśli aparat fotograficzny przesyła strumieniowo wideo. 

* Dwa aparaty fotograficzne wysyłają wideo do przetwarzania, tworzy dwie sesje. 
* Jeden aparat przechodzenie do grafu, który ma dwa węzły gRPCExtension tworzy dwie sesje. 

Każda sesja jest połączeniem pełnego dupleksu między usługą analizy wideo na żywo a serwerem gRPC, a każda sesja może mieć inny model/potoku. 

> [!NOTE]
> W przypadku odłączenia/ponownego połączenia aparatu (z kamerą przechodzącą w tryb offline przez okres poza limitami tolerancji) Usługa Analiza wideo na żywo otworzy nową sesję z serwerem gRPC. Serwer nie wymaga śledzenia stanu w ramach tych sesji. 

Usługa Live Video Analytics Dodaliśmy również obsługę wielu rozszerzeń gRPC dla jednej kamery w wystąpieniu grafu. Będzie można użyć tych rozszerzeń gRPC, aby przeprowadzić przetwarzanie AI sekwencyjnie lub równolegle, lub nawet mieć kombinację obu tych elementów. 

> [!NOTE]
> Jednoczesne uruchamianie wielu rozszerzeń będzie miało wpływ na zasoby sprzętowe i należy pamiętać o tym, wybierając sprzęt, który będzie odpowiadał Twoim potrzebom obliczeniowym. 

### <a name="what-is-the-max--of-simultaneous-processmediastreams"></a>Jaka jest maksymalna liczba jednoczesnych ProcessMediaStreams? 

Nie ma żadnego limitu, który ma zastosowanie do analizy filmów wideo na żywo.  

### <a name="how-should-i-decide-if-my-inferencing-server-should-use-cpu-or-gpu-or-any-other-hardware-accelerator"></a>Jak należy zdecydować, czy mój serwer inferencing powinien korzystać z procesora CPU, czy procesora GPU czy dowolnego innego akceleratora sprzętowego? 

Jest to w całości zależne od tego, jak złożono model AI, oraz sposób, w jaki deweloper chce korzystać z akceleratorów procesora i sprzętowych. Podczas opracowywania modelu AI deweloperzy mogą określić zasoby, które powinny być używane przez model do wykonywania działań. 

### <a name="how-do-i-store-images-with-bounding-boxes-post-processing"></a>Jak mogę przechowywać obrazy z przetworzonymi polami zawierającymi ograniczenia? 

Dzisiaj udostępniamy współrzędne pola ograniczenia jako tylko komunikaty wnioskowania. Deweloperzy mogą tworzyć niestandardowe pliki strumienia MJPEG, które mogą korzystać z tych komunikatów i nakładać pola ograniczenia na ramki wideo.  

## <a name="grpc-compatibility"></a>zgodność gRPC 

### <a name="how-will-i-know-what-the-mandatory-fields-for-the-media-stream-descriptor-are"></a>Jak wiemy, jakie są obowiązkowe pola dla deskryptora strumienia multimediów? 

Dla każdej wartości pola, która nie została podana, zostanie podana wartość domyślna [określona przez gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

Usługa Analiza filmów wideo na żywo używa wersji **proto3** języka bufora protokołów. Wszystkie dane bufora protokołu używane przez kontrakty analizy wideo na żywo są dostępne w plikach buforu protokołu [zdefiniowanych w tym miejscu](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). 

### <a name="how-should-i-ensure-that-i-am-using-the-latest-protocol-buffer-files"></a>Jak upewnić się, że używam najnowszych plików buforu protokołu? 

Najnowsze pliki buforu protokołu można [uzyskać tutaj](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). Za każdym razem, gdy aktualizujemy pliki kontraktu, pojawią się one w tej lokalizacji. Chociaż nie istnieje natychmiastowe Planowanie aktualizacji plików protokołu, poszukaj nazwy pakietu u góry plików, aby poznać wersję. Powinien on zostać odczytany: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Wszystkie aktualizacje tych plików spowodują zwiększenie wartości "v-Value" na końcu nazwy. 

> [!NOTE]
> Ponieważ usługa Analiza wideo na żywo używa wersji języka proto3, pola są opcjonalne, a to zapewnia zgodność z poprzednią i przekazaniem dalej. 

### <a name="what-grpc-features-are-available-for-me-to-use-with-live-video-analytics-which-features-are-mandatory-and-which-ones-are-optional"></a>Jakie funkcje gRPC są dostępne dla mnie, aby można było korzystać z usługi Live Video Analytics? Które funkcje są obowiązkowe i które są opcjonalne? 

Wszystkie funkcje gRPC po stronie serwera mogą być używane pod warunkiem, że kontrakt protobuf jest spełniony. 

## <a name="monitoring-and-metrics"></a>Monitorowanie i metryki

### <a name="can-i-monitor-the-media-graph-on-the-edge-using-event-grid"></a>Czy mogę monitorować wykres multimedialny na krawędzi za pomocą Event Grid?

Tak. Można używać metryk Prometheus i publikować je w usłudze Event Grid. 

### <a name="can-i-use-azure-monitor-to-view-the-health-metrics-and-performance-of-my-media-graphs-in-the-cloud-or-on-the-edge"></a>Czy mogę używać Azure Monitor do wyświetlania kondycji, metryk i wydajności grafów multimediów w chmurze lub na granicy?

Tak. Jest to obsługiwane. Dowiedz się więcej na [temat używania metryk Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

### <a name="are-there-any-tools-to-make-it-easier-to-monitor-the-media-services-iot-edge-module"></a>Czy istnieją jakieś narzędzia ułatwiające monitorowanie modułu Media Services IoT Edge?

Visual Studio Code obsługuje rozszerzenie "Azure IoT Tools", które pozwala łatwo monitorować punkty końcowe modułu LVAEdge. Za pomocą tego narzędzia można szybko rozpocząć monitorowanie IoT Hub wbudowanego punktu końcowego dla "events" i wyświetlanie komunikatów wnioskowania, które są kierowane z urządzenia brzegowego do chmury. 

Ponadto można użyć tego rozszerzenia do edycji sznurka modułu dla modułu LVAEdge, aby zmodyfikować ustawienia wykresu multimedialnego.

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [monitorowania i rejestrowania](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Rozliczenia i dostępność

### <a name="how-is-live-video-analytics-on-iot-edge-billed"></a>Jak jest rozliczana usługa Live Video Analytics na IoT Edge rozliczeń?

Aby uzyskać szczegółowe informacje, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/media-services/) .

## <a name="next-steps"></a>Następne kroki

[Szybki Start: Rozpoczynanie pracy — Analiza filmów wideo na żywo na IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
