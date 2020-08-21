---
ms.openlocfilehash: 768e79c6a2471715b336f90748ad97ecfcc4bbc2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682268"
---

![overview](../../../media/quickstarts/overview-qs4.png)

Na powyższym diagramie przedstawiono sposób przepływu sygnałów w tym przewodniku Szybki Start. [Moduł graniczny](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) symuluje kamerę IP, która hostuje serwer protokołu przesyłania strumieniowego (RTSP) w czasie rzeczywistym. Węzeł [źródłowy RTSP](../../../media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do węzła [procesora wykrywania ruchu](../../../media-graph-concept.md#motion-detection-processor) . Źródło RTSP wysyła te same ramki wideo do węzła [procesora bramy sygnalizującej](../../../media-graph-concept.md#signal-gate-processor) , który pozostanie zamknięty do momentu wyzwolenia przez zdarzenie.

Gdy procesor wykrywania ruchu wykryje ruch wideo, wysyła zdarzenie do węzła procesora bramy sygnału, wyzwalając go. Zostanie otwarta brama dla skonfigurowanego czasu trwania, czyli wysłanie klatek wideo do węzła [ujścia pliku](../../../media-graph-concept.md#file-sink) . Ten węzeł ujścia rejestruje wideo jako plik MP4 w lokalnym systemie plików urządzenia brzegowego. Plik zostanie zapisany w skonfigurowanej lokalizacji.

W tym przewodniku szybki start:

1. Utwórz i Wdróż Graf multimedialny.
1. Interpretuj wyniki.
1. Wyczyść zasoby.
