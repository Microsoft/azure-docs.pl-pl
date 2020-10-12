---
title: Wdróż bezstanową aplikację Kubernetes na Azure Stack brzegowej procesora GPU przy użyciu modułu IoT Edge | Microsoft Docs
description: Opisuje sposób wdrażania aplikacji bezstanowej Kubernetes na urządzeniu GPU z systemem Azure Stack Edge przy użyciu modułu IoT Edge, do którego dostęp jest uzyskiwany za pośrednictwem zewnętrznego adresu IP.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 4bc598080b96886e6734ac3709761465a1a28d49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90899524"
---
# <a name="use-iot-edge-module-to-run-a-kubernetes-stateless-application-on-your-azure-stack-edge-pro-gpu-device"></a>Używanie modułu IoT Edge do uruchamiania aplikacji bezstanowej Kubernetes na urządzeniu z systemem Azure Stack EDGE Pro GPU

W tym artykule opisano, jak można użyć modułu IoT Edge do wdrożenia aplikacji bezstanowej na urządzeniu Azure Stack EDGE Pro.

Aby wdrożyć bezstanową aplikację, wykonaj następujące czynności:

- Przed wdrożeniem modułu IoT Edge upewnij się, że wymagania wstępne zostały spełnione.
- Dodaj moduł IoT Edge, aby uzyskać dostęp do sieci obliczeniowej w Azure Stack EDGE Pro.
- Sprawdź, czy moduł ma dostęp do włączonego interfejsu sieciowego.

W tym artykule z tego artykułu opisano użycie modułu aplikacji sieci WebServer do zademonstrowania tego scenariusza.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy:

- Urządzenie Azure Stack EDGE Pro. Upewnij się, że:

    - Ustawienia sieci obliczeniowej są konfigurowane na urządzeniu.
    - Urządzenie jest aktywowane zgodnie z krokami opisanymi w [samouczku: Aktywuj urządzenie](azure-stack-edge-gpu-deploy-activate.md).
- Ukończono Konfigurowanie kroków **obliczeniowych** zgodnie z [samouczkiem: Konfigurowanie obliczeń na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-configure-compute.md) na urządzeniu. Urządzenie powinno mieć skojarzony zasób IoT Hub, urządzenie IoT i urządzenie IoT Edge.


## <a name="add-webserver-app-module"></a>Dodaj moduł aplikacji WebServer

Wykonaj następujące kroki, aby dodać moduł aplikacji sieci WebServer na urządzeniu Azure Stack EDGE Pro.

1. W zasobie IoT Hub skojarzonym z urządzeniem przejdź do pozycji **Automatyczne zarządzanie urządzeniami > IoT Edge**.
1. Wybierz i kliknij urządzenie IoT Edge skojarzone z urządzeniem Azure Stack brzeg Pro. 

    ![Wybierz urządzenie IoT Edge](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-device-1.png)  

1. Wybierz pozycję **Ustaw moduły**. W obszarze **Ustaw moduły na urządzeniu**wybierz pozycję **+ Dodaj** , a następnie wybierz pozycję **moduł IoT Edge**.

    ![Wybierz moduł IoT Edge](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/select-iot-edge-module-1.png)

1. W **module dodawanie IoT Edge**:

    1. Określ **nazwę** modułu aplikacji WebServer, który chcesz wdrożyć.
    2. W obszarze **Ustawienia modułu** kartę Podaj **Identyfikator URI obrazu** dla Twojego obrazu modułu. Pobierany jest moduł pasujący do podanej nazwy i tagów. W takim przypadku program `nginx:stable` będzie ściągał stabilny obraz Nginx (oznaczony jako stabilny) z publicznego [repozytorium platformy Docker](https://hub.docker.com/_/nginx/).

        ![Dodaj moduł IoT Edge](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/set-module-settings-1.png)    

    3. Na karcie **Opcje tworzenia kontenera** wklej następujący przykładowy kod:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Ta konfiguracja pozwala uzyskać dostęp do modułu przy użyciu protokołu IP sieci obliczeniowej za pośrednictwem *protokołu HTTP* na porcie TCP 8080 (z domyślnym portem serwera web: 80). Wybierz pozycję **Dodaj**.

        ![Określ informacje o porcie w bloku modułu IoT Edge niestandardowego](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

    4. Wybierz pozycję **Przeglądanie + tworzenie**. Przejrzyj szczegóły modułu i wybierz pozycję **Utwórz**.

## <a name="verify-module-access"></a>Weryfikuj dostęp do modułu

1. Sprawdź, czy moduł został pomyślnie wdrożony i jest uruchomiony. Na karcie **moduły** musi być **uruchomiony**stan środowiska uruchomieniowego modułu.  

    ![Sprawdź, czy stan modułu jest uruchomiony](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-module-status-1.png)

1. Aby uzyskać zewnętrzny punkt końcowy aplikacji WebServer, [uzyskaj dostęp do pulpitu nawigacyjnego Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 
1. W lewym okienku pulpitu nawigacyjnego Filtruj według przestrzeni nazw **iotedge** . Przejdź do obszaru **odnajdywanie i równoważenie obciążenia > Services**. Na liście usług Znajdź zewnętrzny punkt końcowy dla modułu aplikacji WebServer. 

    ![Nawiązywanie połączenia z aplikacją WebServer w zewnętrznym punkcie końcowym](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/connect-external-endpoint-1.png)

1. Wybierz zewnętrzny punkt końcowy, aby otworzyć nowe okno przeglądarki.

    Powinieneś zobaczyć, że aplikacja WebServer jest uruchomiona.

    ![Sprawdź połączenie z modułem przez określony port](media/azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module/verify-webserver-app-1.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak ujawniać stanowe aplikacje za pośrednictwem modułu IoT Edge<!--insert link-->.
