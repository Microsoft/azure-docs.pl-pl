---
ms.openlocfilehash: 9c1b521a0f10da77295fd2457793566d787cb2cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89570125"
---
> [!div class="mx-imgBorder"]
> :::image type="content" source="../../../media/quickstarts/overview-qs4.svg" alt-text="Przepływ sygnałów":::

Na powyższym diagramie przedstawiono sposób przepływu sygnałów w tym przewodniku Szybki Start. [Moduł graniczny](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) symuluje kamerę IP, która hostuje serwer Real-Time Streaming. Węzeł [źródłowy RTSP](../../../media-graph-concept.md#rtsp-source) pobiera kanał wideo z tego serwera i wysyła ramki wideo do węzła [procesora wykrywania ruchu](../../../media-graph-concept.md#motion-detection-processor) . Źródło RTSP wysyła te same ramki wideo do węzła [procesora bramy sygnalizującej](../../../media-graph-concept.md#signal-gate-processor) , który pozostanie zamknięty do momentu wyzwolenia przez zdarzenie.

Gdy procesor wykrywania ruchu wykryje ruch wideo, wysyła zdarzenie do węzła procesora bramy sygnału, wyzwalając go. Zostanie otwarta brama dla skonfigurowanego czasu trwania, czyli wysłanie klatek wideo do węzła [ujścia pliku](../../../media-graph-concept.md#file-sink) . Ten węzeł ujścia rejestruje wideo jako plik MP4 w lokalnym systemie plików urządzenia brzegowego. Plik zostanie zapisany w skonfigurowanej lokalizacji.

W tym przewodniku szybki start:

1. Utwórz i Wdróż Graf multimedialny.
1. Interpretuj wyniki.
1. Wyczyść zasoby.
