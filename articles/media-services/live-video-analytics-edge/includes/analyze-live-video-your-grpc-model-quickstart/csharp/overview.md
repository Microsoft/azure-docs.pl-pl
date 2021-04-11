---
ms.openlocfilehash: 8d18b2e72a2a9e787f69d6adaeae8ebc3ca162b8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105582802"
---
![Omówienie](../../../media/quickstarts/gRPC-extension.svg)

Ten diagram przedstawia sposób przepływu sygnałów w tym przewodniku Szybki Start. [Moduł graniczny](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) symuluje kamerę IP obsługującą serwer Real-Time Streaming Protocol (RTSP). Węzeł [źródłowy RTSP](../../../media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do węzła [procesora wykrywania ruchu](../../../media-graph-concept.md#motion-detection-processor) . Ten procesor wykrywa ruch i po wykryciu spowoduje wypychanie klatek wideo do węzła [procesora rozszerzenia gRPC](../../../media-graph-concept.md#grpc-extension-processor) .

Węzeł rozszerzenia gRPC odgrywa rolę serwera proxy. Konwertuje ramki wideo na określony typ obrazu. Następnie przekazuje obraz przez gRPC do innego modułu brzegowego, który uruchamia model AI za punktem końcowym gRPC w [pamięci współdzielonej](https://en.wikipedia.org/wiki/Shared_memory). W tym przykładzie moduł Edge jest zbudowany przy użyciu modelu [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który może wykryć wiele typów obiektów. Węzeł procesora rozszerzenia gRPC zbiera wyniki wykrywania i publikuje zdarzenia w węźle [ujścia IoT Hub](../../../media-graph-concept.md#iot-hub-message-sink) . Następnie węzeł wysyła te zdarzenia do [centrum IoT Edge](../../../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

W tym przewodniku szybki start:

1. Utwórz i Wdróż Graf multimedialny.
1. Interpretuj wyniki.
1. Wyczyść zasoby.