---
title: Nagrywanie wideo oparte na zdarzeniach — Azure
description: Nagrywanie wideo oparte na zdarzeniach (EVR) odnosi się do procesu nagrywania wideo wyzwalanego przez zdarzenie. Dane zdarzenie może pochodzić z powodu przetwarzania samego sygnału wideo (na przykład wykrywania ruchu) lub z niezależnego źródła (na przykład otwierającego drzwi).  W tym artykule opisano kilka przypadków użycia dotyczących nagrywania wideo opartego na zdarzeniach.
ms.topic: conceptual
ms.date: 05/27/2020
ms.openlocfilehash: f3efd2b9be41928ab4721d6db4aa84c0f1f57e2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89568510"
---
# <a name="event-based-video-recording"></a>Nagrywanie wideo oparte na zdarzeniach  
 
## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem  

* [Ciągłe nagrywanie wideo](continuous-video-recording-concept.md)
* [Odtwarzanie zarejestrowanej zawartości](video-playback-concept.md)
* [Koncepcje grafu multimediów](media-graph-concept.md)

## <a name="overview"></a>Omówienie 

Nagrywanie wideo oparte na zdarzeniach (EVR) odnosi się do procesu nagrywania wideo wyzwalanego przez zdarzenie. Omawiane zdarzenie może pochodzić z powodu przetwarzania samego sygnału wideo (na przykład wykrywania ruchu) lub z niezależnego źródła (na przykład otwierającego drzwi). 

Możesz nagrać wideo (wyzwolone przez zdarzenie) z aparatu CCTV do elementu zawartości Media Services przy użyciu grafu nośnika zawierającego węzeł [źródłowy RTSP](media-graph-concept.md#rtsp-source) , węzeł [ujścia zasobów](media-graph-concept.md#asset-sink) i inne węzły, jak opisano w poniższych przypadkach użycia. Węzeł [ujścia zasobów](media-graph-concept.md#asset-sink) można skonfigurować w taki sposób, aby generować nowe zasoby przy każdym wystąpieniu zdarzenia, dzięki czemu wideo odpowiadające każdemu zdarzeniu będzie w jego własnym zawartości. Możesz również użyć jednego zasobu do przechowywania wideo dla wszystkich zdarzeń. 

Jako alternatywę dla węzła ujścia zasobów można użyć węzła [ujścia pliku](media-graph-concept.md#file-sink) do przechwytywania krótkich fragmentów wideo (związanych ze zdarzeniem zainteresowania) do określonej lokalizacji w lokalnym systemie plików na urządzeniu brzegowym. 

W tym artykule opisano kilka przypadków użycia dotyczących nagrywania wideo opartego na zdarzeniach.

### <a name="video-recording-based-on-motion-detection"></a>Nagrywanie filmów wideo na podstawie wykrywania ruchu  

W tym przypadku użycia można rejestrować klipy wideo tylko wtedy, gdy wykryto ruch w filmie wideo i zostanie wygenerowany alert w przypadku wygenerowania takiego klipu wideo. Może to być istotne w scenariuszach zabezpieczeń komercyjnych obejmujących ochronę infrastruktury krytycznej. Generując alerty i nagrywaj wideo w przypadku wykrycia nieoczekiwanego ruchu, można poprawić wydajność operatora ludzkiego, ponieważ akcja jest wymagana tylko w przypadku wygenerowania alertu.

Na poniższym diagramie przedstawiono graficzną reprezentację grafu multimedialnego, który dotyczy tego przypadku użycia. Reprezentację w formacie JSON topologii wykresu takiego wykresu multimedialnego można znaleźć [tutaj](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/motion-detection.svg" alt-text="Nagrywanie filmów wideo na podstawie wykrywania ruchu":::

Na diagramie węzeł źródłowy RTSP przechwytuje dynamiczny kanał wideo z aparatu i dostarcza go do węzła [procesora wykrywania ruchu](media-graph-concept.md#motion-detection-processor) . W przypadku wykrywania ruchu wideo na żywo w węźle procesor wykrywania ruchu generowane jest zdarzenie, które przechodzi do węzła [procesora bramy sygnałów](media-graph-concept.md#signal-gate-processor) , a także do węzła ujścia komunikatów IoT Hub. Drugi węzeł wysyła zdarzenia do centrum IoT Edge, z którego mogą być kierowani do innych miejsc docelowych w celu wyzwalania alertów. 

Zdarzenie z węzła wykrywania ruchu spowoduje wyzwolenie węzła procesora bramy i umożliwi przesyłanie strumieniowe wideo na żywo z węzła źródłowego RTSP do węzła ujścia zasobów. W przypadku braku kolejnych zdarzeń wykrywania ruchu Brama zostanie zamknięta po upływie skonfigurowanego czasu. Określa czas trwania nagrania wideo.

### <a name="video-recording-based-on-events-from-other-sources"></a>Nagrywanie filmów wideo na podstawie zdarzeń z innych źródeł  

W tym przypadku użycia sygnały z innego czujnika IoT mogą służyć do wyzwalania nagrywania wideo. Na poniższym diagramie przedstawiono graficzną reprezentację grafu multimedialnego, który dotyczy tego przypadku użycia. Reprezentację w formacie JSON topologii wykresu takiego wykresu multimedialnego można znaleźć [tutaj](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/other-sources.svg" alt-text="Nagrywanie filmów wideo na podstawie wykrywania ruchu":::

Na diagramie zewnętrzny czujnik wysyła zdarzenia do centrum IoT Edge. Zdarzenia są następnie kierowane do węzła procesora bramy sygnałów za pośrednictwem węzła [źródła wiadomości IoT Hub](media-graph-concept.md#iot-hub-message-source) . Zachowanie węzła procesora bramy sygnałów jest takie samo jak w przypadku poprzedniego przypadku użycia — zostanie otwarte i umożliwi przepływ strumieniowego kanału wideo na żywo z węzła źródłowego RTSP do węzła ujścia plików (lub węzła ujścia zasobów), gdy zostanie wyzwolone przez zdarzenie zewnętrzne. 

Jeśli używasz węzła ujścia plików, wideo zostanie zarejestrowane w lokalnym systemie plików na urządzeniu brzegowym. W przeciwnym razie, jeśli używasz węzła ujścia zasobów, film wideo zostanie zarejestrowany w elemencie zawartości.

### <a name="video-recording-based-on-an-external-inferencing-module"></a>Nagrywanie filmów wideo na podstawie zewnętrznego modułu inferencing 

W tym przypadku użycia można nagrać klipy wideo na podstawie sygnału z zewnętrznego systemu logiki. Przykładem takiego przypadku użycia może być nagrywanie klipu wideo tylko w przypadku wykrycia wózka w strumieniu wideo na autostradie. Na poniższym diagramie przedstawiono graficzną reprezentację grafu multimedialnego, który dotyczy tego przypadku użycia. Reprezentację w formacie JSON topologii wykresu takiego wykresu multimedialnego można znaleźć [tutaj](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/external-inferencing-module.svg" alt-text="Nagrywanie filmów wideo na podstawie wykrywania ruchu":::

Na diagramie węzeł źródłowy RTSP przechwytuje dynamiczny kanał wideo z aparatu i dostarcza go do dwóch gałęzi: jeden ma węzeł [procesora bramy sygnalizującej](media-graph-concept.md#signal-gate-processor) , a drugi używa węzła [rozszerzenia http](media-graph-concept.md) do wysyłania danych do zewnętrznego modułu logiki. Węzeł rozszerzenia HTTP umożliwia grafowi multimediów wysyłanie ramek obrazu (w formatach JPEG, BMP lub PNG) do zewnętrznej usługi wnioskowania za pośrednictwem REST. Ta ścieżka sygnału może zazwyczaj obsługiwać tylko niskie szybkości klatek (<5fps). Można użyć węzła [procesora filtru szybkości klatek](media-graph-concept.md#frame-rate-filter-processor) , aby obniżyć szybkość klatek wideo przechodzących do węzła rozszerzenia http.

Wyniki z zewnętrznej usługi wnioskowania są pobierane przez węzeł rozszerzenia HTTP i przekazywane do centrum IoT Edge za pośrednictwem IoT Hub węzła ujścia komunikatów, gdzie mogą być przetwarzane przez zewnętrzny moduł logiki. Jeśli usługa wnioskowania jest w stanie wykryć pojazdy, na przykład moduł logiki może szukać określonego pojazdu, takiego jak magistrala lub ciężar. Gdy moduł logiki wykrywa interesujący obiekt, może wyzwolić węzeł procesora bramy sygnałów przez wysłanie zdarzenia za pośrednictwem Centrum IoT Edge do węzła źródła komunikatu IoT Hub na wykresie. Dane wyjściowe bramy sygnałów są wyświetlane, aby przejść do węzła ujścia plików lub węzła ujścia zasobów. W poprzednim przypadku wideo zostanie zarejestrowane w lokalnym systemie plików na urządzeniu brzegowym. W tym drugim przypadku wideo zostanie zarejestrowane w elemencie zawartości.

Udoskonaleniem tego przykładu jest użycie procesora wykrywania ruchu przed węzłem procesora filtru szybkości klatek. Spowoduje to zmniejszenie obciążenia usługi wnioskowania, na przykład Nighttime, gdy może być długi okres czasu, gdy nie ma żadnych pojazdów na autostradie. 

## <a name="next-steps"></a>Następne kroki

[Samouczek: nagrywanie filmów wideo opartych na zdarzeniach](event-based-video-recording-tutorial.md)
