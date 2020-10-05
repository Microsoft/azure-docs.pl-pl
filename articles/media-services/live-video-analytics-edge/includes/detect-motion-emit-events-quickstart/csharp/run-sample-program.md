---
ms.openlocfilehash: ed7be82146f38cc7ae57fd863bb0c1b8e6910fd2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88691070"
---
Wykonaj następujące kroki, aby uruchomić przykładowy kod:

1. W Visual Studio Code przejdź do pozycji *src/Cloud-to-Device-App/operations.json*.
1. W węźle **GraphTopologySet** upewnij się, że została wyświetlona następująca wartość:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
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
