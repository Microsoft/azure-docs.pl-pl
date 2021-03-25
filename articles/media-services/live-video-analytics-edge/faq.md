---
title: Analiza filmów wideo na żywo na IoT Edge często zadawane pytania — Azure
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące analizy filmów wideo na żywo na IoT Edge.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 661b6155ce2d95e2111a1fa338fd5df438e61e7d
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105032804"
---
# <a name="live-video-analytics-on-iot-edge-faq"></a>Analiza filmów wideo na żywo na IoT Edge często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące analizy filmów wideo na żywo na Azure IoT Edge.

## <a name="general"></a>Ogólne

**Jakie zmienne systemowe można użyć w definicji topologii wykresu?**

| Zmienna   |  Opis  | 
| --- | --- | 
| [System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods) | Reprezentuje chwilę czasu UTC, zwykle wyrażoną jako datę i godzinę dnia w następującym formacie:<br>*yyyyMMddTHHmmssZ* | 
| System. PreciseDateTime | Reprezentuje wystąpienie daty i czasu uniwersalnego czasu koordynowanego (UTC) w formacie zgodnym z ISO8601 (milisekundy) w następującym formacie:<br>*yyyyMMddTHHmmss. fffZ* | 
| System. GraphTopologyName   | Reprezentuje topologię grafu multimediów i utrzymuje plan grafu. | 
| System. GraphInstanceName |    Reprezentuje wystąpienie grafu nośnika, zawiera wartości parametrów i odwołuje się do topologii. | 

## <a name="configuration-and-deployment"></a>Konfiguracja i wdrażanie

**Czy mogę wdrożyć moduł Media Edge na urządzeniu z systemem Windows 10?**

Tak. Aby uzyskać więcej informacji, zobacz [kontenery systemu Linux w systemie Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Przechwyć z ustawień aparatu IP i RTSP

**Czy muszę użyć specjalnego zestawu SDK na urządzeniu, aby wysyłać strumień wideo?**

Nie, Usługa Analiza filmów wideo na żywo na IoT Edge obsługuje przechwytywanie multimediów przy użyciu protokołu RTSP (protokół przesyłania strumieniowego w czasie rzeczywistym) na potrzeby przesyłania strumieniowego wideo, które jest obsługiwane w większości kamer IP.

**Czy można wypchnąć multimedia do analizy filmów wideo na żywo na IoT Edge przy użyciu protokołu Real-Time Messaging Protocol (RTMP) lub protokołu Smooth Streaming (na przykład zdarzenie Media Services na żywo)?**

Nie. Usługa Analiza filmów wideo na żywo obsługuje tylko protokół RTSP do przechwytywania wideo z kamer IP. Wszystkie aparaty obsługujące przesyłanie strumieniowe RTSP za pośrednictwem protokołu TCP/HTTP powinny funkcjonować. 

**Czy mogę zresetować lub zaktualizować źródłowy adres URL RTSP w wystąpieniu wykresu?**

Tak, gdy wystąpienie grafu jest w stanie *nieaktywnym* .  

**Czy symulator jest dostępny do użycia podczas testowania i opracowywania?**

Tak, w [](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) ramach przewodników Szybki Start i samouczków można korzystać z tego modułu, aby zapewnić obsługę procesu nauki. Ten moduł jest dostępny jako najlepszy nakład pracy i może być niezawsze dostępny. Zalecamy, aby *nie* używać symulatora przez więcej niż kilka godzin. Przed zaplanowaniem wdrożenia produkcyjnego należy zainwestować w testy z rzeczywistym źródłem RTSP.

**Czy jest obsługiwane odnajdywanie kamer IP zgodne ze standardem ONVIF w sieci brzegowej?**

Nie, nie obsługujemy funkcji "Open Network Video Interface forum" (ONVIF) odnajdywania urządzeń na krawędzi.

## <a name="streaming-and-playback"></a>Przesyłanie strumieniowe i odtwarzanie

**Czy mogę odtworzyć zasoby zarejestrowane do Azure Media Services od brzegu przy użyciu technologii przesyłania strumieniowego, takich jak HLS czy łącznik?**

Tak. Można przesyłać strumieniowo zarejestrowane zasoby, takie jak każdy inny zasób w Azure Media Services. Aby przesłać strumieniowo zawartość, musisz mieć utworzony punkt końcowy przesyłania strumieniowego i w stanie uruchomienia. Użycie standardowego procesu tworzenia lokalizatora przesyłania strumieniowego zapewnia dostęp do programu Apple HTTP Live Streaming (HLS) lub dynamicznego adaptacyjnego przesyłania strumieniowego za pośrednictwem protokołu HTTP (PAUZy, znanego również jako MPEG-myślnik) do przesyłania strumieniowego do dowolnej platformy odtwarzacza. Aby uzyskać więcej informacji o tworzeniu i publikowaniu manifestów HLS lub PAUZ, zobacz [dynamiczne pakowanie](../latest/dynamic-packaging-overview.md).

**Czy można używać standardowych funkcji ochrony zawartości i technologii DRM Media Services na zarchiwizowanym elemencie zawartości?**

Tak. Wszystkie standardowe funkcje dynamicznej ochrony zawartości i zarządzania prawami cyfrowymi (DRM) są dostępne do użycia na zasobach, które są rejestrowane z grafu multimedialnego.

**Jakich uczestników mogę używać do wyświetlania zawartości z zarejestrowanych zasobów?**

Obsługiwane są wszystkie standardowe odtwarzacze obsługujące zgodną HLS w wersji 3 lub 4. Ponadto obsługiwany jest również każdy odtwarzacz, który jest w stanie zgodne z odtwarzaniem za PAUZą MPEG.

Zalecane gracze do testowania obejmują:

* [Azure Media Player](../latest/use-azure-media-player.md)
* [HLS.js](https://hls-js.netlify.app/demo/)
* [Video.js](https://videojs.com/)
* [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
* [Shaka Player](https://github.com/google/shaka-player)
* [ExoPlayer](https://github.com/google/ExoPlayer)
* [Natywna HTTP Live Streaming firmy Apple](https://developer.apple.com/streaming/)
* Wbudowany odtwarzacz wideo z systemem Edge, Chrome lub Safari
* Gracze komercyjni obsługujące HLS lub odtwarzanie KRESek

**Jakie są limity przesyłania strumieniowego zasobów grafu multimediów?**

Przesyłanie strumieniowe zawartości na żywo lub nagranie z grafu multimediów korzysta z tej samej infrastruktury o dużej skali i punktu końcowego przesyłania strumieniowego, który Media Services obsługuje na potrzeby przesyłania strumieniowego na żądanie i na żywo w przypadku multimediów & rozrywki, u góry (OTT) i emisji. Oznacza to, że można szybko i łatwo włączyć platformę Azure Content Delivery Network, Verizon lub Akamai, aby dostarczyć zawartość do odbiorców jako niewielką jako kilku osób przeglądających lub do milionów, w zależności od danego scenariusza.

Zawartość można dostarczyć przy użyciu narzędzia Apple HLS lub MPEG-KRESKa.

## <a name="design-your-ai-model"></a>Projektowanie modelu AI 

**Mam wiele modeli AI opakowanych w kontener platformy Docker. Jak używać ich w usłudze Live Video Analytics?** 

Rozwiązania różnią się w zależności od protokołu komunikacyjnego, który jest używany przez serwer inferencing do komunikowania się z analizą wideo na żywo. W poniższych sekcjach opisano sposób działania poszczególnych protokołów.

*Użyj protokołu HTTP*:

* Pojedynczy kontener (Single lvaExtension):  

   Na serwerze inferencing można użyć jednego portu, ale innych punktów końcowych dla różnych modeli AI. Na przykład w przypadku przykładu w języku Python można użyć różnych `route` s na model, jak pokazano poniżej: 

   ```
   @app.route('/score/face_detection', methods=['POST']) 
   … 
   Your code specific to face detection model… 

   @app.route('/score/vehicle_detection', methods=['POST']) 
   … 
   Your code specific to vehicle detection model 
   … 
   ```

   A następnie w rozmieszczeniu analizy wideo na żywo podczas tworzenia wystąpienia grafów Ustaw adres URL serwera wnioskowania dla każdego wystąpienia, jak pokazano poniżej: 

   pierwsze wystąpienie: adres URL serwera wnioskowania =`http://lvaExtension:44000/score/face_detection`<br/>
   drugie wystąpienie: adres URL serwera wnioskowania =`http://lvaExtension:44000/score/vehicle_detection`  
   
    > [!NOTE]
    > Alternatywnie można uwidocznić modele AI na różnych portach i wywoływać je podczas tworzenia wystąpienia grafów.  

* Wiele kontenerów: 

   Każdy kontener jest wdrażany z inną nazwą. Wcześniej w zestawie dokumentacji usługi Live Video Analytics pokazano, jak wdrożyć rozszerzenie o nazwie *lvaExtension*. Teraz można opracowywać dwa różne kontenery, z których każdy ma ten sam interfejs HTTP, co oznacza, że mają ten sam `/score` punkt końcowy. Wdróż te dwa kontenery z różnymi nazwami i upewnij się, że oba nasłuchują na *różnych portach*. 

   Na przykład jeden kontener o nazwie `lvaExtension1` nasłuchuje na porcie `44000` , a drugi kontener o nazwie `lvaExtension2` nasłuchuje na porcie `44001` . 

   W topologii analizy wideo na żywo tworzysz dwa wykresy z różnymi adresami URL wnioskowania, jak pokazano poniżej: 

   Pierwsze wystąpienie: adres URL serwera wnioskowania = `http://lvaExtension1:44001/score`    
   Drugie wystąpienie: adres URL serwera wnioskowania = `http://lvaExtension2:44001/score`
   
*Użyj protokołu gRPC*: 

* W przypadku korzystania z usługi Live Video Analytics 1,0 w przypadku używania protokołu zdalnego wywołania procedury (gRPC), jedynym sposobem, aby to zrobić, jest to, że serwer gRPC uwidacznia różne modele AI za pośrednictwem różnych portów. W [tym przykładzie kodu](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json)pojedynczy port 44000 udostępnia wszystkie modele Yolo. Teoretycznie można napisać serwer Yolo gRPC w celu udostępnienia pewnych modeli na porcie 44000 i innych w porcie 45000. 

* W przypadku 2,0 modułu gRPC Video Analytics na żywo Nowa właściwość jest dodawana do węzła rozszerzenia. Ta właściwość, **extensionConfiguration**, jest opcjonalnym ciągiem, który może być używany jako część kontraktu gRPC. Jeśli masz wiele modeli AI spakowanych na pojedynczym serwerze wnioskowania, nie musisz ujawniać węzła dla każdego modelu AI. Zamiast tego dla wystąpienia grafu, jako dostawcy rozszerzeń, można zdefiniować sposób wybierania różnych modeli AI przy użyciu właściwości **extensionConfiguration** . Podczas wykonywania Analiza wideo na żywo przekazuje ten ciąg do serwera inferencing, który może go użyć do wywołania żądanego modelu AI. 

**Tworzymy serwer gRPC wokół modelu AI i chcę być w stanie obsługiwać jego użycie przez wiele kamer lub wystąpień grafów. Jak należy skompilować serwer?** 

 Najpierw należy upewnić się, że serwer może obsłużyć więcej niż jedno żądanie w czasie lub działać w wątkach równoległych. 

Na przykład domyślna liczba kanałów równoległych została ustawiona w następującym [przykładzie GRPC analizy wideo na żywo](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/notebooks/Yolo/yolov3/yolov3-grpc-icpu-onnx/lvaextension/server/server.py): 

```
server = grpc.server(futures.ThreadPoolExecutor(max_workers=3)) 
```

W poprzednim wystąpieniu serwera gRPC serwer może otworzyć tylko trzy kanały w czasie na kamerę lub na wystąpienie topologii wykresu. Nie próbuj łączyć więcej niż trzech wystąpień z serwerem. Jeśli spróbujesz otworzyć więcej niż trzy kanały, żądania będą oczekiwane do momentu odrzucenia istniejącego kanału.  

Poprzednia implementacja serwera gRPC jest używana w naszych przykładach języka Python. Jako deweloper możesz zaimplementować własny serwer lub użyć poprzedniej domyślnej implementacji, aby zwiększyć numer procesu roboczego, który jest ustawiony na liczbę kamer, które mają być używane na potrzeby źródeł wideo. 

Aby skonfigurować i użyć wielu kamer, można utworzyć wystąpienia wielu wystąpień topologii wykresu, każdy wskazuje na ten sam lub inny serwer wnioskowania (na przykład serwer wymieniony w poprzednim akapicie). 

**Chcę mieć możliwość otrzymywania wielu ramek z nadrzędnego przed podjęciem decyzji inferencing. Jak można ją włączyć?** 

Nasze bieżące [domyślne przykłady](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) działają w trybie *bezstanowym* . Nie przechowują stanu poprzednich wywołań, a nawet o nazwie. Oznacza to, że wiele wystąpień topologii może wywoływać ten sam serwer wnioskowania, ale serwer nie może rozróżnić użytkownika wywołującego lub stanu dla elementu wywołującego. 

*Użyj protokołu HTTP*:

Aby zachować stan, każdy obiekt wywołujący lub wystąpienie topologii wykresu, program wywołuje serwer inferencing przy użyciu parametru zapytania HTTP, który jest unikatowy dla elementu wywołującego. Na przykład adres URL serwera wnioskowania dla każdego wystąpienia jest przedstawiony tutaj:  

pierwsze wystąpienie topologii = `http://lvaExtension:44000/score?id=1`<br/>
drugie wystąpienie topologii = `http://lvaExtension:44000/score?id=2`

… 

Po stronie serwera trasa oceny wie, kto jest wywoływany. Jeśli identyfikator = 1, można zachować stan oddzielnie dla tego obiektu wywołującego lub topologii wykresu. Następnie można pozostawić odebrane ramki wideo w buforze. Na przykład użyj tablicy lub słownika z kluczem DateTime, a wartość jest ramką. Następnie można zdefiniować serwer do przetwarzania (wnioskowania) po odebraniu *x* liczby ramek. 

*Użyj protokołu gRPC*: 

W przypadku rozszerzenia gRPC każda sesja dotyczy pojedynczego źródła danych, więc nie ma potrzeby podawania identyfikatora. Teraz za pomocą właściwości extensionConfiguration można przechowywać klatki wideo w buforze i definiować serwer do przetwarzania (wnioskowanie) po odebraniu *x* liczby ramek. 

**Czy wszystkie ProcessMediaStreams w określonym kontenerze mają ten sam model AI?** 

Nie. Uruchamianie lub zatrzymywanie wywołań od użytkownika końcowego w wystąpieniu grafu tworzy sesję lub może nastąpić odłączenie lub ponowne połączenie aparatu. Celem jest utrzymywanie jednej sesji, jeśli aparat fotograficzny przesyła strumieniowo wideo. 

* Dwa aparaty fotograficzne wysyłające wideo do przetwarzania tworzy dwie sesje. 
* Jeden aparat przechodzenie do grafu, który ma dwa węzły rozszerzenia gRPC tworzy dwie sesje. 

Każda sesja jest połączeniem pełnego dupleksu między usługą Live Video Analytics a serwerem gRPC, a każda sesja może mieć inny model lub potoku. 

> [!NOTE]
> W przypadku odłączenia lub ponownego połączenia aparatu z kamerą w trybie offline przez dłuższy czas niż limity tolerancji usługa Live Video Analytics otworzy nową sesję z serwerem gRPC. Serwer nie wymaga śledzenia stanu w ramach tych sesji. 

Usługa Analiza filmów wideo na żywo dodaje również obsługę wielu rozszerzeń gRPC dla jednej kamery w wystąpieniu grafu. Za pomocą tych rozszerzeń gRPC można wykonywać przetwarzanie AI sekwencyjnie, równolegle lub jako kombinację obu. 

> [!NOTE]
> Jednoczesne uruchamianie wielu rozszerzeń będzie miało wpływ na zasoby sprzętowe. Pamiętaj o tym, jak wybierasz sprzęt spełniający Twoje potrzeby obliczeniowe. 

**Jaka jest maksymalna liczba równoczesnych ProcessMediaStreams?** 

Usługa Analiza filmów wideo na żywo nie ma limitów dla tej liczby.  

**Jak określić, czy serwer inferencing powinien korzystać z procesora CPU, czy procesora GPU czy dowolnego innego akceleratora sprzętowego?** 

Decyzja zależy od złożoności utworzonego modelu AI i sposobu użycia akceleratorów procesora i sprzętu. Podczas opracowywania modelu AI można określić zasoby, które mają być używane przez model i jakie akcje należy wykonać. 

**Jak mogę przechowywać obrazy z ograniczonymi polami przetwarzającymi?** 

Dzisiaj udostępniamy współrzędne pola ograniczenia jako tylko komunikaty wnioskowania. Można utworzyć niestandardowe materiały przerzucające MJPEG, które mogą używać tych komunikatów i nakładać pola ograniczenia na ramki wideo.  

## <a name="grpc-compatibility"></a>zgodność gRPC 

**Jak wiemy, jakie są obowiązkowe pola dla deskryptora strumienia multimediów?** 

Każde pole, do którego nie jest dostarczana wartość, otrzymuje [wartość domyślną określoną przez gRPC](https://developers.google.com/protocol-buffers/docs/proto3#default).  

Usługa Live Video Analytics używa wersji *proto3* języka bufora protokołów. Wszystkie dane buforu protokołu, które są używane przez kontrakty analizy wideo na żywo, są dostępne w [plikach buforu protokołu](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). 

**Jak upewnić się, że używam najnowszych plików buforu protokołu?** 

Najnowsze pliki buforu protokołu można uzyskać w [witrynie pliki kontraktu](https://github.com/Azure/live-video-analytics/tree/master/contracts/grpc). Za każdym razem, gdy aktualizujemy pliki kontraktów, będą one znajdować się w tej lokalizacji. Nie istnieje natychmiastowe Planowanie aktualizacji plików protokołu, dlatego należy poszukać nazwy pakietu u góry plików, aby poznać tę wersję. Powinien on zostać odczytany: 

```
microsoft.azure.media.live_video_analytics.extensibility.grpc.v1 
```

Wszystkie aktualizacje tych plików spowodują zwiększenie wartości "v-Value" na końcu nazwy. 

> [!NOTE]
> Ponieważ analiza filmów wideo na żywo korzysta z wersji proto3 języka, pola są opcjonalne, a wersja jest zgodna z poprzednią i przekazaniem dalej. 

**Jakie funkcje gRPC są dostępne dla mnie, aby można było korzystać z usługi Live Video Analytics? Które funkcje są obowiązkowe i opcjonalne?** 

Można użyć dowolnych funkcji gRPC po stronie serwera, pod warunkiem że spełniony jest kontrakt bufory protokołu (protobuf). 

## <a name="monitoring-and-metrics"></a>Monitorowanie i metryki

**Czy mogę monitorować wykres multimedialny na krawędzi za pomocą Azure Event Grid?**

Tak. Można korzystać z metryk Prometheus i publikować je w usłudze Event Grid. 

**Czy mogę używać Azure Monitor do wyświetlania kondycji, metryk i wydajności grafów multimediów w chmurze lub na granicy?**

Tak, to podejście jest obsługiwane. Aby dowiedzieć się więcej, zobacz [Azure monitor metryki](../../azure-monitor/essentials/data-platform-metrics.md).

**Czy istnieją jakieś narzędzia ułatwiające monitorowanie modułu Media Services IoT Edge?**

Visual Studio Code obsługuje rozszerzenie narzędzi Azure IoT Tools, za pomocą którego można łatwo monitorować punkty końcowe modułu LVAEdge. Za pomocą tego narzędzia można szybko rozpocząć monitorowanie wbudowanego punktu końcowego usługi IoT Hub dla "events" i wyświetlanie komunikatów wnioskowania, które są kierowane z urządzenia brzegowego do chmury. 

Ponadto można użyć tego rozszerzenia do edycji sznurka modułu dla modułu LVAEdge, aby zmodyfikować ustawienia wykresu multimedialnego.

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [monitorowania i rejestrowania](monitoring-logging.md) .

## <a name="billing-and-availability"></a>Rozliczenia i dostępność

**Jak jest rozliczana usługa Live Video Analytics na IoT Edge rozliczeń?**

Aby uzyskać szczegółowe informacje dotyczące rozliczeń, zobacz [Cennik usługi Media Services](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="next-steps"></a>Następne kroki

[Szybki Start: Rozpoczynanie pracy z funkcją analizy filmów wideo na żywo na IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
