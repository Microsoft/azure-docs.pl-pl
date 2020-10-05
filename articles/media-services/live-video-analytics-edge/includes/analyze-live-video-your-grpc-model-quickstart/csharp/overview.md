---
ms.openlocfilehash: fa0c2f5bb00122b40fb4f4ea06b7cf55c0248904
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91025035"
---

![Omówienie](../../../media/quickstarts/gRPC-extension.svg)

Ten diagram przedstawia sposób przepływu sygnałów w tym przewodniku Szybki Start. [Moduł graniczny](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) symuluje kamerę IP obsługującą serwer protokołu przesyłania strumieniowego w czasie rzeczywistym (RTSP). Węzeł [źródłowy RTSP](../../../media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do węzła [procesora wykrywania ruchu](../../../media-graph-concept.md#motion-detection-processor) . Ten procesor wykrywa ruch i po wykryciu spowoduje wypychanie klatek wideo do węzła [procesora rozszerzenia gRPC](../../../media-graph-concept.md#grpc-extension-processor) .

Węzeł rozszerzenia gRPC odgrywa rolę serwera proxy. Konwertuje ramki wideo na określony typ obrazu. Następnie przekazuje obraz przez gRPC do innego modułu brzegowego, który uruchamia model AI za punktem końcowym gRPC w [pamięci współdzielonej](https://en.wikipedia.org/wiki/Shared_memory). W tym przykładzie moduł Edge jest zbudowany przy użyciu modelu [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) , który może wykryć wiele typów obiektów. Węzeł procesora rozszerzenia gRPC zbiera wyniki wykrywania i publikuje zdarzenia w węźle [ujścia IoT Hub](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/media-graph-concept#iot-hub-message-sink) . Następnie węzeł wysyła te zdarzenia do [centrum IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

W tym przewodniku szybki start:

1. Utwórz i Wdróż Graf multimedialny.
1. Interpretuj wyniki.
1. Wyczyść zasoby.
