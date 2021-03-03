---
ms.openlocfilehash: 597a617f892ba00af13b4f88cad1eddf00f0d11b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750240"
---
### <a name="examine-and-edit-the-sample-files"></a>Sprawdzanie i edytowanie plików przykładowych

W ramach wymagań wstępnych pobrano przykładowy kod do folderu. Wykonaj następujące kroki, aby przejrzeć i edytować przykładowe pliki.

1. W Visual Studio Code przejdź do pozycji *src/Edge*. Zobaczysz plik *ENV* i kilka plików szablonów wdrożenia.

    Szablon wdrożenia odwołuje się do manifestu wdrożenia urządzenia brzegowego. Zawiera ona niektóre wartości zastępcze. Plik *ENV* zawiera wartości dla tych zmiennych.
1. Przejdź do folderu *src/Cloud-to-Device-Console-App* . Tutaj zobaczysz *appsettings.jsw* pliku i kilku innych plikach:

    * ***C2D-Console-App. csproj*** — plik projektu dla Visual Studio Code.
    * ***operations.js*** listę operacji, które program ma uruchomić.
    * ***Program.cs*** — przykładowy kod programu. Ten kod:

        * Ładuje ustawienia aplikacji.
        * Wywołuje bezpośrednie metody, które są ujawniane w module IoT Edge na żywo. Za pomocą modułu można analizować strumienie wideo na żywo poprzez wywoływanie [metod bezpośrednich](../../../direct-methods.md).
        * Wstrzymuje działanie, aby można było przeanalizować dane wyjściowe programu w oknie **terminalu** i przeanalizować zdarzenia, które zostały wygenerowane przez moduł w oknie **danych wyjściowych** .
        * Wywołuje bezpośrednie metody czyszczenia zasobów.
1. Edytuj *operations.jsw* pliku:
    * Zmień łącze do topologii grafu:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`

    * W obszarze `GraphInstanceSet` Zmień nazwę topologii wykresu, aby odpowiadała wartości w poprzednim łączu:

      `"topologyName" : "InferencingWithHttpExtension"`

    * W obszarze `GraphTopologyDelete` Edytuj nazwę:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generowanie i wdrażanie manifestu wdrażania IoT Edge

1. Kliknij prawym przyciskiem myszy plik *src/Edge/deployment.yolov3.template.jsw* pliku, a następnie wybierz polecenie **Generuj IoT Edge manifest wdrożenia**.

    ![Generowanie manifestu wdrażania IoT Edge](../../../media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    *deployment.yolov3.amd64.jsw* pliku manifestu jest tworzony w folderze *src/Edge/config* .
1. Jeśli zakończono [wykrywanie ruchu i emitowanie zdarzeń](../../../detect-motion-emit-events-quickstart.md) przewodnika Szybki Start, Pomiń ten krok. 

    W przeciwnym razie obok okienka **Azure IoT Hub** w lewym dolnym rogu wybierz ikonę **więcej akcji** , a następnie wybierz pozycję **Ustaw IoT Hub parametry połączenia**. Można skopiować ciąg z *appsettings.js* pliku. Lub, aby upewnić się, że w ramach Visual Studio Code zostało skonfigurowane odpowiednie Centrum IoT Hub, użyj [polecenia wybierz Centrum IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Określanie parametrów połączenia centrum IoT Hub](../../../media/quickstarts/set-iotconnection-string.png)

    > [!NOTE]
    > Może zostać wyświetlony monit o podanie wbudowanych informacji o punkcie końcowym dla IoT Hub. Aby uzyskać te informacje, w Azure Portal przejdź do IoT Hub i poszukaj opcji **wbudowane punkty końcowe** w lewym okienku nawigacji. Kliknij tam i Wyszukaj **punkt końcowy zgodny z centrum zdarzeń** w sekcji **punkt końcowy zgodny z centrum zdarzeń** . Skopiuj i Użyj tekstu w polu. Punkt końcowy będzie wyglądać następująco:  
        ```
        Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
        ```

1. Kliknij prawym przyciskiem myszy pozycję *src/Edge/config/deployment.yolov3.amd64.js* , a następnie wybierz pozycję **Utwórz wdrożenie dla jednego urządzenia**. 

    ![Utwórz wdrożenie dla pojedynczego urządzenia](../../../media/quickstarts/create-deployment-single-device.png)
1. Po wyświetleniu monitu o wybranie urządzenia IoT Hub wybierz pozycję **LVA-Sample-Device**.
1. Po około 30 sekundach w lewym dolnym rogu okna Odśwież IoT Hub platformy Azure. Na urządzeniu brzegowym są teraz wyświetlane następujące wdrożone moduły:

    * Moduł analizy wideo na żywo o nazwie `lvaEdge` .
    * `rtspsim`Moduł, który symuluje serwer RTSP i działa jako źródło kanału informacyjnego wideo na żywo.

        > [!NOTE]
        > W powyższych krokach przyjęto założenie, że używana jest maszyna wirtualna utworzona przez skrypt Instalatora. Jeśli używasz własnego urządzenia brzegowego, przejdź do urządzenia brzegowego i uruchom następujące polecenia z **uprawnieniami administratora**, aby ściągnąć i przechować przykładowy plik wideo używany do tego przewodnika Szybki Start:  
        
        ```
        mkdir /home/lvaedgeuser/samples
        mkdir /home/lvaedgeuser/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
        chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
        ```
        * `yolov3`Moduł, który jest modelem wykrywania obiektów YoloV3, który stosuje na potrzeby obrazów i zwraca wiele klas typów obiektów
 
      ![Moduły wdrożone na urządzeniu brzegowym](../../../media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Przygotowanie do monitorowania zdarzeń

1. W Visual Studio Code Otwórz kartę **rozszerzenia** (lub naciśnij klawisze CTRL + SHIFT + X) i Wyszukaj pozycję Azure IoT Hub.
1. Kliknij prawym przyciskiem myszy i wybierz pozycję **Ustawienia rozszerzenia**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Ustawienia rozszerzenia":::
1. Wyszukaj i Włącz opcję "Pokaż pełny komunikat".

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Pokaż pełny komunikat":::
1. Kliknij prawym przyciskiem myszy urządzenie analizy wideo na żywo, a następnie wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**. Ten krok jest wymagany do monitorowania zdarzeń IoT Hub w oknie **danych wyjściowych** Visual Studio Code. 

   ![Rozpocznij monitorowanie](../../../media/quickstarts/start-monitoring-iothub-events.png) 

> [!NOTE]
> Może zostać wyświetlony monit o podanie wbudowanych informacji o punkcie końcowym dla IoT Hub. Aby uzyskać te informacje, w Azure Portal przejdź do IoT Hub i poszukaj opcji **wbudowane punkty końcowe** w lewym okienku nawigacji. Kliknij tam i Wyszukaj **punkt końcowy zgodny z centrum zdarzeń** w sekcji **punkt końcowy zgodny z centrum zdarzeń** . Skopiuj i Użyj tekstu w polu. Punkt końcowy będzie wyglądać następująco:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```
### <a name="run-the-sample-program"></a>Uruchamianie przykładowego programu

1. Aby rozpocząć sesję debugowania, wybierz klawisz F5. W oknie **terminalu** są wyświetlane komunikaty.
1. *operations.jsw* kodzie zaczyna się od wywołania metod bezpośrednich `GraphTopologyList` i `GraphInstanceList` . Jeśli wyczyszczono zasoby po ukończeniu poprzednich przewodników Szybki Start, proces ten spowoduje zwrócenie pustych list, a następnie wstrzymanie. Aby kontynuować, wybierz klawisz ENTER.

   ```
   --------------------------------------------------------------------------
   Executing operation GraphTopologyList
   -----------------------  Request: GraphTopologyList  --------------------------------------------------
   {
   "@apiVersion": "2.0"
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
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithHttpExtension",
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

     * Wywołanie `GraphInstanceActivate` , które uruchamia wystąpienie grafu i przepływ wideo
     * Drugie wywołanie `GraphInstanceList` , które pokazuje, że wystąpienie grafu jest w stanie uruchomienia
1. Dane wyjściowe w oknie **terminalu** są wstrzymywane w `Press Enter to continue` wierszu polecenia. Nie zaznaczaj jeszcze klawisza ENTER. Przewiń w górę, aby wyświetlić ładunki odpowiedzi JSON dla wywoływanych metod bezpośrednich.
1. Przejdź do okna **dane wyjściowe** w Visual Studio Code. Zobaczysz komunikat informujący o tym, że usługa Live Video Analytics w IoT Edge module wysyła do centrum IoT Hub. W poniższej sekcji tego przewodnika Szybki Start omówiono te komunikaty.
1. Wykres multimedialny będzie nadal uruchamiany i drukować wyniki. Symulator RTSP utrzymuje zapętlenie źródłowego wideo. Aby zatrzymać Graf multimedialny, Wróć do okna **terminalu** i wybierz klawisz ENTER. 

    Kolejna seria wywołań czyści zasoby:
      * Wywołanie `GraphInstanceDeactivate` dezaktywowania wystąpienia grafu.
      * Wywołanie `GraphInstanceDelete` usuwania wystąpienia.
      * Wywołanie `GraphTopologyDelete` usunięcia topologii.
      * Końcowe wywołanie `GraphTopologyList` pokazujące, że lista jest pusta.
