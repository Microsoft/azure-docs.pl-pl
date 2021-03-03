---
ms.openlocfilehash: 94044e95e83742487a0d4d650814a5324f07011a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751099"
---
Manifest wdrożenia definiuje, jakie moduły są wdrażane na urządzeniu brzegowym. Definiuje również ustawienia konfiguracji dla tych modułów. 

Wykonaj następujące kroki, aby wygenerować manifest z pliku szablonu, a następnie wdrożyć go na urządzeniu brzegowym.

1. Otwórz program Visual Studio Code.
1. Obok okienka **Azure IoT Hub** wybierz ikonę **więcej akcji** , aby ustawić parametry połączenia IoT Hub. Możesz skopiować ten ciąg z pliku *src/Cloud-to-Device-App/appsettings.jsna* plik. 

    ![Ustaw parametry połączenia IOT](../../../media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Może zostać wyświetlony monit o podanie wbudowanych informacji o punkcie końcowym dla IoT Hub. Aby uzyskać te informacje, w Azure Portal przejdź do IoT Hub i poszukaj opcji **wbudowane punkty końcowe** w lewym okienku nawigacji. Kliknij tam i Wyszukaj **punkt końcowy zgodny z centrum zdarzeń** w sekcji **punkt końcowy zgodny z centrum zdarzeń** . Skopiuj i Użyj tekstu w polu. Punkt końcowy będzie wyglądać następująco:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Kliknij prawym przyciskiem myszy pozycję **src/Edge/deployment.template.jsna** i wybierz polecenie **Generuj IoT Edge manifest wdrożenia**.

    ![Generowanie manifestu wdrażania IoT Edge](../../../media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Ta akcja powinna utworzyć plik manifestu o nazwie *deployment.amd64.js* w folderze *src/Edge/config* .
1. Kliknij prawym przyciskiem myszy pozycję **src/Edge/config/deployment.amd64.jsna**, wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**, a następnie wybierz nazwę urządzenia brzegowego.

    ![Tworzenie wdrożenia dla jednego urządzenia](../../../media/quickstarts/create-deployment-single-device.png)

1. Po wyświetleniu monitu o wybranie urządzenia IoT Hub wybierz pozycję **LVA-Sample-Device (urządzenie** ) z menu rozwijanego.
1. Po około 30 sekundach w lewym dolnym rogu okna Odśwież IoT Hub platformy Azure. Na urządzeniu brzegowym są teraz wyświetlane następujące wdrożone moduły:

    * Analiza wideo na żywo na IoT Edge (Nazwa modułu `lvaEdge` )
    * Symulator protokołu przesyłania strumieniowego Real-Time (RTSP) (Nazwa modułu `rtspsim` )

Moduł symulatora RTSP symuluje strumień wideo na żywo przy użyciu pliku wideo, który został skopiowany do urządzenia brzegowego po uruchomieniu [skryptu konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

> [!NOTE]
> Jeśli używasz własnego urządzenia brzegowego zamiast zainicjowanego przez nasz skrypt Instalatora, przejdź do urządzenia brzegowego i uruchom następujące polecenia z **uprawnieniami administratora**, aby ściągnąć i przechować przykładowy plik wideo używany do tego przewodnika Szybki Start:  

```
mkdir /home/lvaedgeuser/samples      
mkdir /home/lvaedgeuser/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaedgeuser/samples/input/camera-300s.mkv  
chown -R lvalvaedgeuser:localusergroup /home/lvaedgeuser/samples/  
```
Na tym etapie moduły są wdrażane, ale nie są aktywne żadne wykresy multimedialne.
