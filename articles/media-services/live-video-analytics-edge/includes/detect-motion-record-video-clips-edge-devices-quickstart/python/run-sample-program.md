---
ms.openlocfilehash: f5e180cb85e65cf832ffe0a3746e25790644e1ba
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91829007"
---
1. W Visual Studio Code Otwórz kartę **rozszerzenia** (lub naciśnij klawisze CTRL + SHIFT + X) i Wyszukaj pozycję Azure IoT Hub.
1. Kliknij prawym przyciskiem myszy i wybierz pozycję **Ustawienia rozszerzenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Ustawienia rozszerzenia":::
1. Wyszukaj i Włącz opcję "Pokaż pełny komunikat".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Ustawienia rozszerzenia"
        }
      ]
    }
  }
  ```
    
  * Wywołanie `GraphInstanceActivate` , które uruchamia wystąpienie grafu i przepływ wideo.
  * Drugie wywołanie `GraphInstanceList` , które pokazuje, że wystąpienie grafu jest w stanie uruchomienia.
1. Dane wyjściowe w oknie **terminalu** są wstrzymywane na `Press Enter to continue` . Nie zaznaczaj jeszcze klawisza ENTER. Przewiń w górę, aby wyświetlić ładunki odpowiedzi JSON dla wywoływanych metod bezpośrednich.
1. Przejdź do okna **dane wyjściowe** w Visual Studio Code. Zobaczysz komunikat informujący o tym, że usługa Live Video Analytics w IoT Edge module wysyła do centrum IoT Hub. W poniższej sekcji tego przewodnika Szybki Start omówiono te komunikaty.
1. Wykres multimedialny będzie nadal uruchamiany i drukować wyniki. Symulator RTSP utrzymuje zapętlenie źródłowego wideo. Aby zatrzymać Graf multimedialny, Wróć do okna **terminalu** i wybierz klawisz ENTER. 

    Kolejna seria wywołań czyści zasoby:

    * Wywołanie `GraphInstanceDeactivate` dezaktywowania wystąpienia grafu.
    * Wywołanie `GraphInstanceDelete` usuwania wystąpienia.
    * Wywołanie `GraphTopologyDelete` usunięcia topologii.
    * Końcowe wywołanie `GraphTopologyList` pokazujące, że lista jest teraz pusta.
