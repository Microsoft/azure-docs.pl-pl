---
title: Analizowanie wideo na żywo bez żadnego rejestrowania — Azure
description: Graf multimedialny może służyć do zaledwie wyodrębnienia analizy z strumienia wideo na żywo bez konieczności rejestrowania go na krawędzi lub w chmurze. W tym artykule omówiono tę koncepcję.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 25a7cadc47603b726542fa391d441e1fbca78908
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97398978"
---
# <a name="analyzing-live-video-without-any-recording"></a>Analizowanie wideo na żywo bez rejestrowania

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem 

* [Koncepcje grafu multimediów](media-graph-concept.md)
* [Nagrywanie wideo oparte na zdarzeniach](event-based-video-recording-concept.md)

## <a name="overview"></a>Omówienie  

Możesz użyć grafu multimediów do analizowania wideo na żywo, bez rejestrowania jakichkolwiek fragmentów wideo do pliku lub zasobu. Przedstawione poniżej wykresy multimedialne są podobne do tych, które znajdują się w artykule na temat [nagrywania filmów wideo opartych na zdarzeniach](event-based-video-recording-concept.md), ale bez węzła ujścia zasobów lub węzła ujścia pliku.

### <a name="motion-detection"></a>Wykrywanie ruchu

Wykres multimedialny przedstawiony poniżej składa się z węzła [źródłowego RTSP](media-graph-concept.md#rtsp-source) , węzła [procesora wykrywania ruchu](media-graph-concept.md#motion-detection-processor) i węzła [ujścia komunikatu IoT Hub](media-graph-concept.md#iot-hub-message-sink) . Reprezentację w formacie JSON topologii wykresu takiego wykresu multimedialnego można znaleźć [tutaj](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json). Ten wykres pozwala wykryć ruch w przychodzącym strumieniu wideo na żywo i przekazywać zdarzenia ruchu do innych aplikacji i usług za pośrednictwem węzła ujścia komunikatów IoT Hub. Zewnętrzne aplikacje lub usługi mogą wyzwalać alert lub wysyłać powiadomienia do odpowiedniego personelu.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Analiza wideo na żywo oparta na wykrywaniu ruchu":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>Analizowanie wideo przy użyciu niestandardowego modelu wzrokowego 

Wykres multimedialny przedstawiony poniżej umożliwia przeanalizowanie strumienia wideo na żywo przy użyciu niestandardowego modelu wzrokowego spakowanego w osobnym module. Reprezentację w formacie JSON topologii wykresu takiego wykresu multimedialnego można znaleźć [tutaj](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json). [Poniżej](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) znajdziesz przykłady dotyczące zawijania modeli do modułów IoT Edge, które są uruchamiane jako usługa wnioskowania.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detected-frames.svg" alt-text="Analiza wideo na żywo oparta na zewnętrznym module inferencing":::

Na tym grafie multimedialnym dane wejściowe wideo ze źródła RTSP są wysyłane do węzła [procesora rozszerzenia http](media-graph-concept.md#http-extension-processor) , który wysyła ramki obrazu (w formatach JPEG, BMP lub PNG) do zewnętrznej usługi wnioskowania za pośrednictwem Rest. Wyniki z zewnętrznej usługi wnioskowania są pobierane przez węzeł rozszerzenia HTTP i przekazywane do centrum IoT Edge za pośrednictwem IoT Hub węzła ujścia komunikatów. Ten typ grafu multimediów może służyć do kompilowania rozwiązań dla różnych scenariuszy, takich jak zrozumienie rozkładu szeregów czasowych pojazdów w punkcie częściowym, zrozumienie wzorca ruchu odbiorców w sklepie detalicznym i tak dalej.
>[!TIP]
> Możesz zarządzać szybkością klatek w węźle procesor rozszerzenia HTTP przy użyciu `samplingOptions` pola przed wysłaniem go.

Udoskonaleniem tego przykładu jest użycie procesora wykrywania ruchu przed węzłem procesora rozszerzenia HTTP. Spowoduje to zmniejszenie obciążenia usługi wnioskowania, ponieważ jest ona używana tylko wtedy, gdy w filmie wideo znajduje się aktywność ruchu.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/custom-model.svg" alt-text="Analiza wideo na żywo oparta na wykrytych klatkach za pośrednictwem zewnętrznego modułu inferencing":::

## <a name="next-steps"></a>Następne kroki

[Ciągłe nagrywanie wideo](continuous-video-recording-concept.md)
