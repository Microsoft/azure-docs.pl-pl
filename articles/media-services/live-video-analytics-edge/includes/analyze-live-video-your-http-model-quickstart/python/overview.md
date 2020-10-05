---
ms.openlocfilehash: db0e3bf102feeab6272ac96198d486b51e144d05
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89570136"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs5.svg" alt-text="Przepływ sygnałów":::

Ten diagram przedstawia sposób przepływu sygnałów w tym przewodniku Szybki Start. [Moduł graniczny](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) symuluje kamerę IP obsługującą serwer protokołu przesyłania strumieniowego w czasie rzeczywistym (RTSP). Węzeł [źródłowy RTSP](../../../media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do węzła [procesora filtru szybkości klatek](../../../media-graph-concept.md#frame-rate-filter-processor) . Ten procesor ogranicza szybkość klatek strumienia wideo, który dociera do węzła [procesora rozszerzenia http](../../../media-graph-concept.md#http-extension-processor) . 

Węzeł rozszerzenia HTTP pełni rolę serwera proxy. Konwertuje ramki wideo na określony typ obrazu. Następnie przekazuje obraz za pośrednictwem REST do innego modułu krawędzi, który uruchamia model AI za punktem końcowym HTTP. W tym przykładzie moduł Edge jest zbudowany przy użyciu modelu [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który może wykryć wiele typów obiektów. Węzeł procesora rozszerzenia HTTP zbiera wyniki wykrywania i publikuje zdarzenia w węźle [ujścia IoT Hub](../../../media-graph-concept.md#iot-hub-message-sink) . Następnie węzeł wysyła te zdarzenia do [centrum IoT Edge](../../../../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

W tym przewodniku szybki start:

1. Utwórz i Wdróż Graf multimedialny.
1. Interpretuj wyniki.
1. Wyczyść zasoby.
