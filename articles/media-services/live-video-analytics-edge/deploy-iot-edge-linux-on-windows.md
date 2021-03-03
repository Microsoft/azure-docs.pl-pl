---
title: Wdrażanie do IoT Edge dla systemu Linux w systemie Windows — Azure
description: Ten artykuł zawiera wskazówki dotyczące sposobu wdrażania programu w IoT Edge dla systemu Linux na urządzeniu z systemem Windows.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 5cf4184857f12065d808d7b528dbfe8258950cd8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745478"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>Wdrażanie do IoT Edge dla systemu Linux na urządzeniu z systemem Windows (EFLOW)

W tym artykule dowiesz się, jak wdrożyć analizę wideo na żywo na urządzeniu brzegowym, które ma [IoT Edge dla systemu Linux w systemie Windows (EFLOW)](https://docs.microsoft.com/azure/iot-edge/iot-edge-for-linux-on-windows?view=iotedge-2018-06). Po wykonaniu kroków opisanych w tym dokumencie będzie można uruchomić [Graf multimedialny](media-graph-concept.md) , który wykrywa ruch wideo i emituje te zdarzenia do centrum IoT w chmurze. Można następnie przełączać Graf multimedialny w celu zaawansowania scenariuszy i zapewnić możliwości analizy filmów wideo na żywo na urządzeniu IoT Edge z systemem Windows.

## <a name="prerequisites"></a>Wymagania wstępne 

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , jeśli jeszcze go nie masz.

    > [!NOTE]
    > Potrzebna jest subskrypcja platformy Azure z uprawnieniami do tworzenia jednostek usługi (jest to **rola właściciela** ). Jeśli nie masz odpowiednich uprawnień, skontaktuj się z administratorem konta w celu udzielenia odpowiednich uprawnień.
* [Visual Studio Code](https://code.visualstudio.com/) na komputerze deweloperskim. Upewnij się, że masz [rozszerzenie narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Przeczytaj [co to jest EFLOW](https://aka.ms/AzEFLOW-docs).

## <a name="deployment-steps"></a>Kroki wdrażania

Poniżej przedstawiono ogólny przepływ dokumentu oraz 5 prostych kroków, które należy wykonać, aby uruchomić analizę wideo na żywo na urządzeniu z systemem Windows, które ma EFLOW:

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="Diagram IoT Edge dla systemu Linux w systemie Windows (EFLOW)":::

1. [Zainstaluj program EFLOW](https://aka.ms/AzEFLOW-install) na urządzeniu z systemem Windows. 

    1. Jeśli używasz komputera z systemem Windows, na stronie startowej [Centrum administracyjnego systemu Windows](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview) w obszarze listy połączeń zostanie wyświetlone połączenie hosta lokalnego reprezentujące komputer, na którym działa centrum administracyjne systemu Windows. 
    1. Wszystkie dodatkowe serwery, komputery lub klastry, którymi zarządzasz, również zostaną wyświetlone tutaj.
    1. Korzystając z centrum administracyjnego systemu Windows, można zainstalować usługę Azure EFLOW na urządzeniu lokalnym lub urządzeniach zarządzanych zdalnie i zarządzać nimi. W tym przewodniku połączenie hosta lokalnego obsługiwane jako urządzenie docelowe dla wdrożenia Azure IoT Edge dla systemu Linux w systemie Windows. W związku z tym zobaczysz localhost jako urządzenie IoT Edge.

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="Kroki wdrażania — centrum administracyjne systemu Windows":::
1. Kliknij urządzenie IoT Edge, aby nawiązać z nim połączenie. powinna zostać wyświetlona karta przegląd i powłoka poleceń. Karta powłoka poleceń umożliwia wydawanie poleceń na urządzeniu brzegowym.
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="Kroki wdrażania — Azure IoT Edge Manager":::
1. Przejdź do powłoki poleceń i wpisz następujące polecenie:
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    Moduł Analiza wideo na żywo jest uruchamiana na urządzeniu brzegowym z [kontami użytkowników lokalnych](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment)bez uprawnień. Ponadto [potrzebuje pewnych folderów lokalnych](deploy-iot-edge-device.md#granting-permissions-to-device-storage) do przechowywania danych konfiguracji aplikacji. Na koniec tego przewodnika korzystamy z [symulatora RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) , który przekazuje kanał wideo w czasie rzeczywistym do LVA modułu do analizy. Ten symulator pobiera wstępnie zarejestrowane pliki wideo z katalogu wejściowego. 
    
    Skrypt przygotowywania urządzenia użyty powyżej automatyzuje te zadania, aby można było uruchomić jedno polecenie i mieć wszystkie odpowiednie foldery wejściowe i konfiguracyjne, pliki wejściowe wideo i konta użytkowników z niezawodnie utworzonymi uprawnieniami. Po pomyślnym zakończeniu działania polecenia powinny zostać wyświetlone następujące foldery utworzone na urządzeniu brzegowym. 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    Zanotuj pliki wideo (*. mkv) w folderze/Home/lvaedgeuser/Samples/Input, które pełnią rolę plików wejściowych do przeanalizowania. 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="Analizy":::
1. Teraz, gdy urządzenie brzegowe zostało skonfigurowane, zarejestrowane w centrum i działa prawidłowo z prawidłowymi utworzonymi strukturami folderów, następnym krokiem jest skonfigurowanie następujących dodatkowych zasobów platformy Azure i wdrożenie modułu LVA. 

    * Konto magazynu
    * Konto Azure Media Services

    W tym celu zaleca się użycie [skryptu konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) w celu wdrożenia wymaganych zasobów w ramach subskrypcji platformy Azure. W tym celu wykonaj następujące czynności:

    1. Otwórz usługę [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/).

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Usługa Cloud Shell":::
    1. Jeśli używasz Cloud Shell po raz pierwszy, zostanie wyświetlony monit o wybranie subskrypcji w celu utworzenia konta magazynu i udziału plików Microsoft Azure. Wybierz pozycję **Utwórz magazyn** , aby utworzyć konto magazynu dla informacji sesji Cloud Shell. To konto magazynu jest niezależne od konta tworzonego przez skrypt do użycia z Twoim kontem Azure Media Services.
    1. W menu rozwijanym po lewej stronie okna Cloud Shell wybierz pozycję bash jako swoje środowisko.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Środowisko bash":::
    1. Uruchom następujące polecenie.

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        Upewnij się, że po wyświetleniu monitu o wybranie urządzenia brzegowego jako urządzenia IoT Edge wybierz pozycję **Y** , ponieważ wcześniej utworzono urządzenie i IoT Hub. Zostanie również wyświetlony monit o podanie nazwy IoT Hub i IoT Edge identyfikator urządzenia. Można to zrobić, logując się do Azure Portal i klikając IoT Hub a następnie wybierając opcję IoT Edge w bloku portalu IoT Hub.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="Zobacz IoT Edge urządzeń":::

    Po zakończeniu możesz zalogować się ponownie do powłoki poleceń IoT Edge urządzenia i uruchomić następujące polecenie.
    
    `sudo iotedge list`
    
    Powinny zostać wyświetlone następujące cztery moduły wdrożone i uruchomione na urządzeniu brzegowym. Należy pamiętać, że skrypt tworzenia zasobów wdraża moduł LVA wraz z modułami IoT Edge (edgeAgent i edgeHub) i modułem symulatora RTSP, aby zapewnić symulowany kanał wideo z użyciem protokołu RTSP.
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="Zobacz stan":::
1. Po wdrożeniu i skonfigurowaniu modułów można przystąpić do uruchamiania pierwszego grafu multimediów LVA na EFLOW. Można uruchomić prosty Graf wykrywania ruchu, jak pokazano poniżej, i wizualizować wyniki, wykonując następujące czynności:

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Wykres wykrywania ruchu":::

    1. [Skonfiguruj](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension) rozszerzenie narzędzi Azure IoT Tools.
    
        > [!Note]
        > Użyj następującej ścieżki: `~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json` .
    1. Ustawianie topologii, tworzenie wystąpienia grafu i aktywowanie go za pośrednictwem tych [wywołań metody bezpośredniej](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls).
    1. [Obserwuj wyniki](get-started-detect-motion-emit-events-quickstart.md#observe-results) w centrum.
    1. Wywołaj [metody oczyszczania](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate).
    1. Usuń zasoby, jeśli nie są potrzebne.

        > [!IMPORTANT]
        > Nieusunięte zasoby nadal mogą być aktywne i naliczane są koszty związane z platformą Azure.
    
## <a name="next-steps"></a>Następne kroki

* Wypróbuj wykrywanie ruchu wraz z nagrywaniem odpowiednich filmów wideo w chmurze. Wykonaj kroki opisane w sekcji [wykrywanie ruchu, Nagrywanie klipów wideo w celu Media Services](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video) przewodnika Szybki Start.
* Uruchom [AI na żywo wideo](use-your-model-quickstart#overview) (możesz pominąć konfigurację wymagań wstępnych, ponieważ została już wykonana powyżej)
* Użyj naszego [rozszerzenia vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge) , aby wyświetlić dodatkowe wykresy multimedialne.
* Użyj [kamery IP](https://en.wikipedia.org/wiki/IP_camera)  , która obsługuje protokół RTSP zamiast korzystania z symulatora RTSP. Kamery IP obsługujące protokół RTSP można znaleźć na stronie [ONVIF zgodne produkty](https://www.onvif.org/conformant-products/) . Wyszukaj urządzenia zgodne z profilami G, S lub T.

