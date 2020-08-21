---
ms.openlocfilehash: 93c88f34e32e2057efd3eae25b1f41f58b948575
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682402"
---
1. Rozpocznij sesję debugowania, wybierając klawisz F5. Okno **terminalu** drukuje niektóre komunikaty.
1. *operations.jsw* kodzie wywołuje metody bezpośrednie `GraphTopologyList` i `GraphInstanceList` . W przypadku oczyszczenia zasobów po poprzednich przewodnikach szybki start proces ten spowoduje zwrócenie pustych list, a następnie wstrzymanie. Naciśnij klawisz Enter.
    
    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "1.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
  
  W oknie **terminalu** zostanie wyświetlony następny zestaw wywołań metod bezpośrednich:  
  
  * Wywołanie `GraphTopologySet` , które używa `topologyUrl` 
  * Wywołanie `GraphInstanceSet` , które używa następującej treści:
  
  ```
  {
    "@apiVersion": "1.0",
    "name": "Sample-Graph",
    "properties": {
      "topologyName": "EVRToFilesOnMotionDetection",
      "description": "Sample graph description",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        },
        {
          "name": "rtspPassword",
          "value": "testpassword"
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
