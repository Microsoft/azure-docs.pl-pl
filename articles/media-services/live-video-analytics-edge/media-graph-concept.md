---
title: Koncepcje grafu multimediów — Azure
description: Graf multimedialny pozwala określić, gdzie mają być przechwytywane nośniki, jak należy je przetworzyć i gdzie mają zostać dostarczone wyniki. Ten artykuł zawiera szczegółowy opis koncepcji wykresu multimedialnego.
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 6f23e7db8cecb46106a63fdecdb6ba04dbd99682
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401104"
---
# <a name="media-graph"></a>Graf multimedialny

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem

* [Analiza filmów wideo na żywo na IoT Edge przegląd](overview.md)
* [Analiza filmów wideo na żywo na IoT Edge terminologii](terminology.md)

## <a name="overview"></a>Omówienie

Graf multimedialny pozwala określić, gdzie mają być przechwytywane nośniki, jak należy je przetworzyć i gdzie mają zostać dostarczone wyniki. W tym celu należy połączyć składniki lub węzły w odpowiedni sposób. Poniższy diagram przedstawia graficzną reprezentację grafu multimedialnego.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/media-graph.svg" alt-text="Graf multimedialny":::

Analiza filmów wideo na żywo na IoT Edge obsługuje różne typy źródeł, procesorów i ujścia.

* **Węzły źródłowe** umożliwiają przechwytywanie multimediów do grafu multimedialnego. Multimedia w tym kontekście, koncepcyjnie, mogą być strumieniem audio, strumieniem wideo, strumieniem danych lub strumieniem zawierającym dźwięk, wideo i/lub dane połączone razem w jednym strumieniu.
* **Węzły procesora** umożliwiają przetwarzanie multimediów w grafie multimediów.
* **Węzły ujścia** umożliwiają dostarczanie wyników przetwarzania do usług i aplikacji poza wykresem multimedialnym.

## <a name="media-graph-topologies-and-instances"></a>Topologie i wystąpienia grafu multimediów 

Usługa Analiza filmów wideo na żywo na IoT Edge umożliwia zarządzanie wykresami multimedialnymi przez dwie koncepcje — "topologia grafu" i "wystąpienie grafu". Topologia grafu umożliwia definiowanie planu wykresu przy użyciu parametrów jako symboli zastępczych dla wartości. Topologia określa, które węzły są używane na grafie multimediów i jak są połączone w grafie multimediów. Na przykład jeśli chcesz zarejestrować źródło danych z aparatu, potrzebujesz grafu z węzłem źródłowym, który odbiera wideo, i węzeł ujścia, który zapisuje wideo.

Wartości parametrów w topologii są określane podczas tworzenia wystąpień grafów odwołujących się do topologii. Dzięki temu można utworzyć wiele wystąpień odwołujących się do tej samej topologii, ale z różnymi wartościami parametrów określonych w topologii. W powyższym przykładzie można było użyć parametrów do reprezentowania adresu IP aparatu i nazwy zapisanego wideo. Można utworzyć wiele wystąpień grafu z tą topologią — jedno wystąpienie dla każdego aparatu w budynku, prawdopodobnie każdy z określonym adresem IP i określoną nazwą.

## <a name="media-graph-states"></a>Stany grafu multimediów  

Cykl życia topologii wykresu i wystąpienia grafu są wyświetlane na poniższym diagramie stanu.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/media-graph/graph-topology-lifecycle.svg" alt-text="Cykl życia topologii wykresu i wystąpienia grafu":::

Zaczynasz od [tworzenia topologii wykresu](direct-methods.md#graphtopologyset). Następnie dla każdego na żywo kanału informacyjnego wideo, który chcesz przetworzyć z tą topologią, [utworzysz wystąpienie grafu](direct-methods.md#graphinstanceset). 

Wystąpienie grafu będzie w `Inactive` stanie (bezczynnym).

Gdy wszystko jest gotowe do wysłania strumieniowego wideo na żywo do wystąpienia grafu, należy je [aktywować](direct-methods.md#graphinstanceactivate) . Wystąpienie grafu zostanie krótko przenoszone przez stan przejściowy `Activating` , a jeśli to się powiedzie, przejdź do `Active` stanu. W `Active` stanie nośnik zostanie przetworzony (Jeśli wystąpienie grafu odbiera dane wejściowe).

> [!NOTE]
>  Wystąpienie grafu może być aktywne bez przechodzenia przez nią danych (na przykład aparat przejdzie w tryb offline).
> Twoja subskrypcja platformy Azure zostanie rozliczona, gdy wystąpienie programu Graph jest w stanie aktywnym.

Możesz powtórzyć proces tworzenia i aktywowania innych wystąpień grafu dla tej samej topologii, jeśli masz inne kanały wideo na żywo do przetworzenia.

Po zakończeniu przetwarzania kanału wideo na żywo można [dezaktywować](direct-methods.md#graphinstancedeactivate) wystąpienie grafu. Wystąpienie grafu krótko przejdzie przez stan przejściowy `Deactivating` , opróżni wszystkie dane, które ma, a następnie powróci do `Inactive` stanu.

Wystąpienie wykresu można [usunąć](direct-methods.md#graphinstancedelete) tylko wtedy, gdy jest w `Inactive` stanie.

Po usunięciu wszystkich wystąpień grafów odwołujących się do określonej topologii grafu można [usunąć topologię grafu](direct-methods.md#graphtopologydelete).


## <a name="sources-processors-and-sinks"></a>Źródła, procesory i ujścia  

Usługa Analiza filmów wideo na żywo na IoT Edge obsługuje następujące typy węzłów w grafie multimedialnym:

### <a name="sources"></a>Źródła 

#### <a name="rtsp-source"></a>Źródło RTSP 

Węzeł źródłowy RTSP umożliwia pozyskiwanie multimediów z serwera [RTSP](https://tools.ietf.org/html/rfc2326) . Aparaty nadzoru i kamery IP przesyłają swoje dane do protokołu o nazwie RTSP (przesyłania strumieniowego w czasie rzeczywistym), który różni się od innych typów urządzeń, takich jak telefony i kamery wideo. Ten protokół służy do ustanawiania i kontrolowania sesji multimedialnych między serwerem (kamerą) a klientem. Węzeł źródłowy RTSP na wykresie multimedialnym pełni rolę klienta i może ustanowić sesję z serwerem RTSP. Wiele urządzeń, takich jak większość [kamer IP](https://en.wikipedia.org/wiki/IP_camera) , ma wbudowany serwer RTSP. [ONVIF](https://www.onvif.org/) zezwala na obsługiwanie RTSP w swojej definicji [profilów G, S & T](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) zgodnych urządzeń. Węzeł źródłowy RTSP wymaga określenia adresu URL protokołu RTSP wraz z poświadczeniami w celu włączenia połączenia uwierzytelnionego.

#### <a name="iot-hub-message-source"></a>IoT Hub źródło komunikatu 

Podobnie jak w przypadku innych [modułów IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-module), analiza filmów wideo na żywo w module IoT Edge może odbierać komunikaty za pośrednictwem [Centrum IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). Te komunikaty mogą być wysyłane z innych modułów lub aplikacji uruchamianych na urządzeniu brzegowym lub w chmurze. Takie komunikaty są dostarczane (kierowane) do [nazwanego wejścia](../../iot-edge/module-composition.md#sink) w module. Węzeł źródła komunikatu IoT Hub pozwala na uzyskiwanie dostępu do grafu multimedialnego. Te komunikaty lub sygnały mogą być następnie używane wewnętrznie na grafie multimediów, zazwyczaj w celu aktywowania bram sygnałów (zobacz [bramy sygnałów](#signal-gate-processor) poniżej). 

Na przykład można mieć moduł IoT Edge, który generuje komunikat, gdy otwiera się drzwi. Komunikat z tego modułu można skierować do centrum IoT Edge, z którego można następnie skierować do źródła komunikatu usługi IoT Hub grafu multimedialnego. Na wykresie multimedialnym źródło komunikatu usługi IoT Hub może przekazać zdarzenie do procesora usługi Signal Gateway, które następnie może włączyć nagrywanie wideo ze źródła RTSP do pliku. 

### <a name="processors"></a>Procesory  

#### <a name="motion-detection-processor"></a>Procesor wykrywania ruchu 

Węzeł procesora wykrywania ruchu umożliwia wykrywanie ruchu wideo na żywo. Sprawdza przychodzące ramki wideo i określa, czy w wideo znajduje się ruch. Jeśli ruch jest wykryty, przechodzi do klatek wideo do składnika podrzędnego i emituje zdarzenie. Węzeł procesora wykrywania ruchu (w połączeniu z innymi węzłami) może służyć do wyzwalania rejestrowania przychodzącego wideo, gdy wykryto ruch.

#### <a name="frame-rate-filter-processor"></a>Procesor filtru szybkości klatek  

Węzeł procesora filtru szybkości klatek umożliwia próbkowanie ramek z przychodzącego strumienia wideo z określoną szybkością. Dzięki temu można zmniejszyć liczbę ramek wysyłanych do składników strumienia w dół (takich jak węzeł procesora rozszerzenia HTTP) do dalszej obróbki.
>[!WARNING]
> Ten procesor jest **przestarzały** w najnowszej wersji analizy filmów wideo na żywo w module IoT Edge. Zarządzanie szybkością klatek jest teraz obsługiwane w ramach procesorów rozszerzeń grafu.

#### <a name="http-extension-processor"></a>Procesor rozszerzeń HTTP

Węzeł procesora rozszerzenia HTTP umożliwia łączenie własnych modułów IoT Edge z wykresem multimedialnym. Ten węzeł wykonuje zdekodowaną ramkę wideo jako dane wejściowe i przekazuje te ramki do punktu końcowego REST protokołu HTTP uwidocznionego przez moduł. Ten węzeł ma możliwość uwierzytelniania przy użyciu punktu końcowego REST, jeśli jest to wymagane. Ponadto węzeł ma wbudowany program formatujący obrazy służący do skalowania i kodowania klatek wideo przed ich przekazaniem do punktu końcowego REST. W ramach skalowania dostępne są opcje współczynnika proporcji obrazu, które mają być zachowane, uzupełnione lub rozciągnięte. Koder obrazu obsługuje format JPEG, PNG lub BMP. Więcej informacji na temat procesora [znajdziesz tutaj](media-graph-extension-concept.md#http-extension-processor).

#### <a name="grpc-extension-processor"></a>Procesor rozszerzenia gRPC

Węzeł procesora rozszerzenia gRPC wykonuje dekodowane ramki wideo jako dane wejściowe i przekazuje te ramki do punktu końcowego [gRPC](terminology.md#grpc) uwidocznionego przez moduł. Węzeł obsługuje transfer danych przy użyciu [pamięci współdzielonej](https://en.wikipedia.org/wiki/Shared_memory) lub bezpośrednio osadzanie zawartości w treści gRPC komunikatów. Ponadto węzeł ma wbudowany program formatujący obrazy służący do skalowania i kodowania klatek wideo przed ich przekazaniem do punktu końcowego gRPC. W ramach skalowania dostępne są opcje współczynnika proporcji obrazu, które mają być zachowane, uzupełnione lub rozciągnięte. Koder obrazu obsługuje format JPEG, PNG lub BMP. Więcej informacji na temat procesora [znajdziesz tutaj](media-graph-extension-concept.md#grpc-extension-processor).

#### <a name="signal-gate-processor"></a>Procesor bramy sygnałów  

Węzeł procesora bramy sygnałów umożliwia warunkowe przekazywanie nośników z jednego węzła do drugiego. Pełni również rolę bufora, co umożliwia synchronizację nośników i zdarzeń. Typowy przypadek użycia polega na wstawieniu węzła procesora bramy sygnałów między węzłem źródłowym RTSP i węzłem ujścia zasobów i użyciem danych wyjściowych węzła procesora wykrywania ruchu w celu wyzwolenia bramy. Dzięki takiemu grafowi multimedialnemu będziesz rejestrować wideo tylko wtedy, gdy wykryto ruch.

### <a name="sinks"></a>Ujścia  

#### <a name="asset-sink"></a>Ujścia zasobów  

Węzeł ujścia zasobów pozwala pisać dane multimedialne (wideo i/lub audio) do Azure Media Servicesgo elementu zawartości. Na wykresie multimedialnym może istnieć tylko jeden węzeł ujścia zasobów. Zapoznaj się z sekcją [zasobów](terminology.md#asset) , aby uzyskać więcej informacji o zasobach i ich roli podczas rejestrowania i odtwarzania nośnika. Możesz również zapoznać się z artykułem dotyczącym [ciągłego rejestrowania filmów wideo](continuous-video-recording-concept.md) , aby uzyskać szczegółowe informacje na temat sposobu używania właściwości tego węzła.

#### <a name="file-sink"></a>Ujścia plików  

Węzeł ujścia plików umożliwia pisanie danych multimedialnych (wideo i/lub audio) w lokalizacji w lokalnym systemie plików urządzenia IoT Edge. Na wykresie multimedialnym może istnieć tylko jeden węzeł ujścia plików, który musi być niższy niż węzeł procesora bramy sygnału. Ogranicza to czas trwania plików wyjściowych do wartości określonych we właściwościach węzła procesora bramy sygnałów. Aby upewnić się, że na urządzeniu brzegowym nie zabraknie miejsca na dysku, można również ustawić maksymalny rozmiar, jaki może być używany przez moduł IoT Edge na żywo w module do przechowywania danych.  
> [!NOTE]
Jeśli ujścia plików zostanie zapełnione, analiza filmów wideo na żywo w IoT Edge module rozpocznie usuwanie najstarszych danych i zastąpi je nowym.
#### <a name="iot-hub-message-sink"></a>IoT Hub ujścia komunikatów  

Węzeł ujścia komunikatów IoT Hub umożliwia publikowanie zdarzeń w centrum IoT Edge. Centrum IoT Edge może następnie kierować dane do innych modułów lub aplikacji na urządzeniu brzegowym lub IoT Hub w chmurze (na trasy określone w manifeście wdrożenia). Węzeł ujścia komunikatów IoT Hub może akceptować zdarzenia z procesorów nadrzędnych, takich jak węzeł procesora wykrywania ruchu lub z zewnętrznej usługi wnioskowania za pośrednictwem węzła procesora rozszerzenia HTTP.

## <a name="rules-on-the-use-of-nodes"></a>Reguły dotyczące używania węzłów

Zobacz [ograniczenia dotyczące topologii grafu](quotas-limitations.md#limitations-on-graph-topologies-at-preview) , aby uzyskać dodatkowe reguły dotyczące używania różnych węzłów w grafie multimediów.

## <a name="scenarios"></a>Scenariusze

Korzystając z kombinacji źródeł, procesorów i ujścia zdefiniowane powyżej, można tworzyć wykresy multimedialne dla różnych scenariuszy obejmujących analizę wideo na żywo. Przykładowe scenariusze:

* [Ciągłe nagrywanie wideo](continuous-video-recording-concept.md)
* [Nagrywanie wideo oparte na zdarzeniach](event-based-video-recording-concept.md)
* [Platforma Live Video Analytics bez nagrania wideo](analyze-live-video-concept.md)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak można uruchomić Wykrywanie ruchu na żywo kanału informacyjnego wideo, zobacz [Szybki Start: Uruchamianie analizy filmów wideo na żywo przy użyciu własnego modelu](use-your-model-quickstart.md).
