---
ms.openlocfilehash: 055448eacf1cc12c6d021c6571a0478cb35442ba
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89566919"
---
Manifest wdrożenia definiuje, jakie moduły są wdrażane na urządzeniu brzegowym. Definiuje również ustawienia konfiguracji dla tych modułów. 

Wykonaj następujące kroki, aby wygenerować manifest z pliku szablonu, a następnie wdrożyć go na urządzeniu brzegowym.

1. Otwórz program Visual Studio Code.
1. Obok okienka **Azure IoT Hub** wybierz ikonę **więcej akcji** , aby ustawić parametry połączenia IoT Hub. Możesz skopiować ten ciąg z pliku *src/Cloud-to-Device-App/appsettings.jsna* plik. 

    ![Ustaw parametry połączenia IOT](../../../media/quickstarts/set-iotconnection-string.png)
1. Kliknij prawym przyciskiem myszy pozycję **src/Edge/deployment.template.jsna** i wybierz polecenie **Generuj IoT Edge manifest wdrożenia**.

    ![Generowanie manifestu wdrażania IoT Edge](../../../media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Ta akcja powinna utworzyć plik manifestu o nazwie *deployment.amd64.js* w folderze *src/Edge/config* .
1. Kliknij prawym przyciskiem myszy pozycję **src/Edge/config/deployment.amd64.jsna**, wybierz pozycję **Utwórz wdrożenie dla pojedynczego urządzenia**, a następnie wybierz nazwę urządzenia brzegowego.

    ![Tworzenie wdrożenia dla jednego urządzenia](../../../media/quickstarts/create-deployment-single-device.png)

1. Po wyświetleniu monitu o wybranie urządzenia IoT Hub wybierz pozycję **LVA-Sample-Device (urządzenie** ) z menu rozwijanego.
1. Po około 30 sekundach w lewym dolnym rogu okna Odśwież IoT Hub platformy Azure. Na urządzeniu brzegowym są teraz wyświetlane następujące wdrożone moduły:

    * Analiza wideo na żywo na IoT Edge (Nazwa modułu `lvaEdge` )
    * Symulator protokołu przesyłania strumieniowego (RTSP) w czasie rzeczywistym (Nazwa modułu `rtspsim` )

Moduł symulatora RTSP symuluje strumień wideo na żywo przy użyciu pliku wideo, który został skopiowany do urządzenia brzegowego po uruchomieniu [skryptu konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

> [!NOTE]
> Jeśli używasz własnego urządzenia brzegowego zamiast zainicjowanego przez nasz skrypt Instalatora, przejdź do urządzenia brzegowego i uruchom następujące polecenia z **uprawnieniami administratora**, aby ściągnąć i przechować przykładowy plik wideo używany do tego przewodnika Szybki Start:  

```
mkdir /home/lvaadmin/samples      
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
Na tym etapie moduły są wdrażane, ale nie są aktywne żadne wykresy multimedialne.
