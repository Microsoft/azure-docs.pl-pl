---
ms.openlocfilehash: 8319de85cdd8b3527fc6bfb13dd9ed1e8f6fc0fa
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486840"
---
Wykonaj następujące kroki, aby uruchomić przykładowy kod:

1. W Visual Studio Code Otwórz kartę **rozszerzenia** (lub naciśnij klawisze CTRL + SHIFT + X) i Wyszukaj pozycję Azure IoT Hub.
1. Kliknij prawym przyciskiem myszy i wybierz pozycję **Ustawienia rozszerzenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Ustawienia rozszerzenia":::
1. Wyszukaj i Włącz opcję "Pokaż pełny komunikat".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Pokaż pełny komunikat":::
1. W Visual Studio Code przejdź do pozycji *src/Cloud-to-Device-App/operations.json*.
1. W węźle **GraphTopologySet** upewnij się, że została wyświetlona następująca wartość:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/2.0/topology.json"`
1. W węzłach **GraphInstanceSet** i **GraphTopologyDelete**  upewnij się, że wartość jest `topologyName` zgodna z wartością `name` właściwości w topologii grafu:

    `"topologyName" : "MotionDetection"`
    
1. Rozpocznij sesję debugowania, wybierając klawisz F5. W oknie **terminalu** zostaną wyświetlone pewne komunikaty.
1. *operations.jsw* pliku rozpoczyna się z wywołaniami do `GraphTopologyList` i `GraphInstanceList` . Jeśli wyczyszczono zasoby po zakończeniu poprzednich przewodników Szybki Start, proces ten spowoduje zwrócenie pustych list, a następnie wstrzymanie. Aby kontynuować, wybierz klawisz ENTER.

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
     * Wywołanie `GraphTopologySet` , które używa poprzedniej `topologyUrl`
     * Wywołanie `GraphInstanceSet` , które używa następującej treści:
         
    ```
    {
      "@apiVersion": "1.0",
      "name": "Sample-Graph",
      "properties": {
        "topologyName": "MotionDetection",
        "description": "Sample graph description",
        "parameters": [
          {
            "name": "rtspUrl",
            "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
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
    * Końcowe wywołanie `GraphTopologyList` pokazujące, że lista jest pusta.
