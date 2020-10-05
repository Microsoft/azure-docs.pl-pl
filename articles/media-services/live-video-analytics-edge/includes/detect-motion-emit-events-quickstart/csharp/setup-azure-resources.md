---
ms.openlocfilehash: 67d90836c382728f989ab2cb4fde4d81bac9eb25
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88691054"
---
Ten samouczek wymaga następujących zasobów platformy Azure:

* Usługa IoT Hub
* Konto magazynu
* Konto Azure Media Services
* Maszyna wirtualna z systemem Linux na platformie Azure z zainstalowanym [IoT Edge środowiska uruchomieniowego](../../../../../iot-edge/how-to-install-iot-edge-linux.md)

W tym przewodniku szybki start zalecamy używanie [skryptu konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) w celu wdrożenia wymaganych zasobów w ramach subskrypcji platformy Azure. Aby to zrobić, wykonaj następujące kroki:

1. Otwórz usługę [Azure Cloud Shell](https://shell.azure.com).
1. Jeśli używasz Cloud Shell po raz pierwszy, zostanie wyświetlony monit o wybranie subskrypcji w celu utworzenia konta magazynu i udziału plików Microsoft Azure. Wybierz pozycję **Utwórz magazyn** , aby utworzyć konto magazynu dla informacji sesji Cloud Shell. To konto magazynu jest niezależne od konta tworzonego przez skrypt do użycia z Twoim kontem Azure Media Services.
1. W menu rozwijanym po lewej stronie okna Cloud Shell wybierz pozycję **bash** jako swoje środowisko.

    ![Selektor środowiska](../../../media/quickstarts/env-selector.png)
1. Uruchom następujące polecenie.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Jeśli skrypt zakończy się pomyślnie, wszystkie wymagane zasoby powinny zostać wyświetlone w ramach subskrypcji.
1. Po zakończeniu działania skryptu wybierz nawiasy klamrowe, aby uwidocznić strukturę folderów. Zobaczysz kilka plików w katalogu *~/CloudDrive/LVA-Sample* . Są one przydatne w tym przewodniku szybki start:

     * ***~/CloudDrive/LVA-Sample/Edge-Deployment/.env*** — ten plik zawiera właściwości, których Visual Studio Code używa do wdrożenia modułów na urządzeniu brzegowym.
     * ***~/clouddrive/lva-sample/appsetting.json*** -Visual Studio Code używa tego pliku do uruchomienia przykładowego kodu.
     
    Te pliki będą potrzebne podczas konfigurowania środowiska deweloperskiego w Visual Studio Code w następnej sekcji. Możesz chcieć skopiować je do pliku lokalnego.
    
    ![Ustawienia aplikacji](../../../media/quickstarts/clouddrive.png)
    