---
title: Koncepcje grafu multimediów — Azure
description: Program Media Graph pozwala określić, gdzie mają być przechwytywane nośniki, jak należy je przetworzyć i gdzie powinny zostać dostarczone wyniki. Ten artykuł zawiera szczegółowy opis koncepcji wykresu multimedialnego.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: ee055c34fd37d2d1cc5e7d0bb5147c945dcbff94
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84260967"
---
# <a name="media-graph-concept"></a>Koncepcje grafu multimediów

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem

* [Analiza filmów wideo na żywo na IoT Edge przegląd](overview.md)
* [Analiza filmów wideo na żywo na IoT Edge terminologii](terminology.md)

## <a name="overview"></a>Omówienie

Program Media Graph pozwala określić, gdzie mają być przechwytywane nośniki, jak należy je przetworzyć i gdzie powinny zostać dostarczone wyniki. Graf multimedialny składa się z węzłów źródłowych, procesora i ujścia. Poniższy diagram przedstawia graficzną reprezentację grafu multimedialnego.   

![Graficzna reprezentacja grafu multimediów](./media/media-graph/overview.png)


* **Węzły źródłowe** umożliwiają przechwytywanie multimediów do grafu multimedialnego. Multimedia w tym kontekście, koncepcyjnie, mogą być strumieniem audio, strumieniem wideo, strumieniem danych lub strumieniem zawierającym dźwięk, wideo i/lub dane połączone razem w jednym strumieniu.
* **Węzły procesora** umożliwiają przetwarzanie multimediów w grafie multimediów.
* **Węzły ujścia** umożliwiają dostarczanie wyników przetwarzania do usług i aplikacji poza wykresem multimedialnym.

## <a name="media-graph-topologies-and-instances"></a>Topologie i wystąpienia grafu multimediów 

Usługa Analiza filmów wideo na żywo na IoT Edge umożliwia zarządzanie wykresami multimedialnymi przez dwie koncepcje — "topologia grafu" i "wystąpienie grafu". Topologia grafu umożliwia definiowanie planu wykresu przy użyciu parametrów jako symboli zastępczych dla wartości. Topologia określa, które węzły są używane na grafie multimediów i jak są połączone w grafie multimediów. Wartości parametrów można określić podczas tworzenia wystąpień grafu odwołujących się do topologii. Dzięki temu można utworzyć wiele wystąpień odwołujących się do tej samej topologii, ale z różnymi wartościami parametrów określonych w topologii. 

## <a name="media-graph-states"></a>Stany grafu multimediów  

Program Media Graph może być w jednym z następujących stanów:

* Nieaktywny — reprezentuje stan, w którym Graf multimedialny jest skonfigurowany, ale nie jest aktywny.
* Aktywowanie — stan podczas tworzenia wystąpienia grafu multimedialnego (czyli stanu przejścia między nieaktywnym i aktywnym).
* Aktywny — stan, w którym jest aktywny Graf multimedialny. 

    > [!NOTE]
    >  Program Media Graph może być aktywny bez przechodzenia przez nią danych (na przykład wejściowe źródło wideo przejdzie w tryb offline).
* Dezaktywowanie — jest to stan, gdy wykres multimedialny przechodzi z aktywnego do nieaktywnego.

Poniższy diagram przedstawia komputer stanu wykresu multimediów.

![Komputer stanu grafu multimediów](./media/media-graph/media-graph-state-machine.png)

## <a name="sources-processors-and-sinks"></a>Źródła, procesory i ujścia  

Usługa Analiza filmów wideo na żywo na IoT Edge obsługuje następujące typy węzłów w grafie multimedialnym:

### <a name="sources"></a>Źródła 

#### <a name="rtsp-source"></a>Źródło RTSP 

Źródło RTSP umożliwia przechwytywanie multimediów z serwera [RTSP](https://tools.ietf.org/html/rfc2326) . Protokół RTSP jest używany do ustanawiania i kontrolowania sesji multimedialnych między serwerem a klientem. Węzeł źródłowy RTSP na wykresie multimedialnym pełni rolę klienta i może ustanowić sesję z określonym serwerem RTSP. Wiele urządzeń, takich jak większość [kamer IP](https://en.wikipedia.org/wiki/IP_camera) , ma wbudowany serwer RTSP. [ONVIF](https://www.onvif.org/) zezwala na obsługiwanie RTSP w swojej definicji [profilów G, S & T](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) zgodnych urządzeń. Węzeł źródłowy RTSP w grafie multimediów wymaga określenia adresu URL protokołu RTSP wraz z poświadczeniami w celu włączenia połączenia uwierzytelnionego.

#### <a name="iot-hub-message-source"></a>IoT Hub źródło komunikatu 

Podobnie jak w przypadku innych [modułów IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-module), analiza filmów wideo na żywo w module IoT Edge może odbierać komunikaty za pośrednictwem [Centrum IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). Te komunikaty mogą być wysyłane z innych modułów lub aplikacji uruchamianych na urządzeniu brzegowym lub w chmurze. Takie komunikaty mogą być dostarczane (kierowane) do [nazwanego wejścia](../../iot-edge/module-composition.md#sink) w module. Źródło komunikatu IoT Hub umożliwia kierowanie komunikatów do wystąpienia grafu multimediów. Te komunikaty lub sygnały mogą być następnie używane wewnętrznie na grafie multimediów, zazwyczaj w celu aktywowania bram sygnałów (zobacz [procesor bramy sygnałów](#signal-gate-processor) poniżej). 

Na przykład można mieć moduł IoT Edge, który generuje komunikat, gdy otwiera się drzwi. Komunikat z tego modułu można skierować do centrum IoT Edge, z którego można następnie skierować do źródła komunikatu usługi IoT Hub grafu multimedialnego. Na wykresie multimedialnym źródło komunikatu usługi IoT Hub może przekazać zdarzenie do procesora usługi Signal Gateway, które następnie może włączyć nagrywanie wideo ze źródła RTSP do pliku. 

### <a name="processors"></a>Procesory  

#### <a name="motion-detection-processor"></a>Procesor wykrywania ruchu 

Procesor wykrywania ruchu umożliwia wykrywanie ruchu wideo na żywo. Sprawdza przychodzące wideo i określa, czy ruch wideo jest dostępny. Jeśli wykryto ruch, przejdzie on do wideo w węźle podrzędnym i emituje zdarzenie. Procesor wykrywania ruchu (w połączeniu z innymi węzłami grafu mediów) może służyć do wyzwalania rejestrowania przychodzącego wideo w przypadku wykrycia ruchu.

#### <a name="frame-rate-filter-processor"></a>Procesor filtru szybkości klatek  

Procesor filtru szybkości klatek umożliwia próbkowanie ramek z przychodzącego strumienia wideo z określoną szybkością klatek. Dzięki temu można zmniejszyć liczbę ramek wysyłanych do węzłów w dół-strumienia (takich jak procesor rozszerzeń HTTP) do dalszej obróbki.

#### <a name="http-extension-processor"></a>Procesor rozszerzeń HTTP 

Procesor rozszerzeń HTTP umożliwia podłączenie własnych plików AI do grafu multimedialnego. Procesor rozszerzeń HTTP przyjmuje jako dane wejściowe, zdekodowane ramki wideo i przekazuje te ramki do punktu końcowego HTTP. Procesor ma możliwość uwierzytelniania w punkcie końcowym HTTP, jeśli jest to wymagane. Ponadto procesor ma wbudowany program formatujący obrazy, który umożliwia skalowanie i kodowanie klatek wideo przed ich przekazaniem do przodu. Skalowanie ma opcje dla współczynnika proporcji obrazu, które mają być zachowane, uzupełnione lub rozciągane, podczas gdy kodowanie zapewnia opcje dla różnych kodowań obrazu, takich jak JPEG, PNG lub BMP.

#### <a name="signal-gate-processor"></a>Procesor bramy sygnałów  

Procesor bramy sygnałów umożliwia warunkowe przekazywanie multimediów z jednego węzła do drugiego. Pełni również rolę bufora, co umożliwia synchronizację nośników i zdarzeń. Przykładowy przypadek użycia polega na wstawieniu procesora sygnałów sygnalizacyjnych między źródłem RTSP i ujścia zasobów i użyciem danych wyjściowych procesora wykrywania ruchu do wyzwolenia bramy. Dzięki takiemu grafowi multimedialnemu można wyzwolić nagrywanie multimediów tylko w przypadku wykrycia ruchu w przychodzącym wideo. 

### <a name="sinks"></a>Ujścia  

#### <a name="asset-sink"></a>Ujścia zasobów  

Ujścia zasobów umożliwia grafowi multimedialnemu pisanie danych multimedialnych (wideo i/lub audio) do Azure Media Servicesego elementu zawartości. Zapoznaj się z sekcją [zasobów](terminology.md#asset) , aby uzyskać więcej informacji o zasobach i ich roli podczas rejestrowania i odtwarzania nośnika.  

#### <a name="file-sink"></a>Ujścia plików  

Ujścia plików umożliwia grafowi multimedialnemu zapisywanie danych multimedialnych (wideo i/lub audio) w lokalizacji w lokalnym systemie plików urządzenia IoT Edge. Obiekt sink musi być elementem podrzędnym procesora sygnałów. Powoduje to ograniczenie czasu trwania plików wyjściowych do wartości określonych we właściwościach procesora bramy sygnałów.

#### <a name="iot-hub-message-sink"></a>IoT Hub ujścia komunikatów  

Ujścia komunikatów IoT Hub umożliwia publikowanie zdarzeń w centrum IoT Edge. Centrum brzegowe może następnie kierować dane do innych modułów lub aplikacji na urządzeniu brzegowym lub IoT Hub w chmurze (na trasy określone w manifeście wdrożenia). Obiekt ujścia komunikatów IoT Hub może akceptować zdarzenia z procesorów nadrzędnych, takich jak procesor wykrywania ruchu lub z zewnętrznej usługi wnioskowania za pośrednictwem procesora rozszerzeń HTTP.

## <a name="scenarios"></a>Scenariusze

Korzystając z kombinacji węzłów źródłowych, procesora i ujścia, zdefiniowanych powyżej, można tworzyć wykresy multimedialne dla różnych scenariuszy. Poniżej przedstawiono kilka przykładowych scenariuszy

* [Ciągłe nagrywanie wideo](continuous-video-recording-concept.md)
* [Rejestrowanie wideo oparte na zdarzeniach](event-based-video-recording-concept.md)
* [Analiza wideo na żywo bez nagrania wideo](analyze-live-video-concept.md)

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie z [przewodnikiem Szybki Start: Uruchamianie analizy filmów wideo na żywo z własnym modelem](use-your-model-quickstart.md) , aby dowiedzieć się, jak można uruchomić Wykrywanie ruchu na żywo kanału informacyjnego wideo.
