---
ms.openlocfilehash: 6b1df49ed0f2c543706d7584a1ade5dc5554a564
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750031"
---
Ten samouczek wymaga następujących zasobów platformy Azure:

* Usługa IoT Hub
* Konto magazynu
* Konto Azure Media Services
* Maszyna wirtualna z systemem Linux na platformie Azure z zainstalowanym [IoT Edge środowiska uruchomieniowego](../../../../../iot-edge/how-to-install-iot-edge.md)

W tym przewodniku szybki start zalecamy używanie [skryptu konfiguracji zasobów analizy wideo na żywo](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) w celu wdrożenia wymaganych zasobów w ramach subskrypcji platformy Azure. W tym celu wykonaj następujące czynności:

1. Otwórz usługę [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/).
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/quickstarts/cloud-shell.png" alt-text="Cloud Shell":::
1. Jeśli używasz Cloud Shell po raz pierwszy, zostanie wyświetlony monit o wybranie subskrypcji w celu utworzenia konta magazynu i udziału plików Microsoft Azure. Wybierz pozycję **Utwórz magazyn** , aby utworzyć konto magazynu dla informacji sesji Cloud Shell. To konto magazynu jest niezależne od konta tworzonego przez skrypt do użycia z Twoim kontem Azure Media Services.
1. W menu rozwijanym po lewej stronie okna Cloud Shell wybierz pozycję **bash** jako swoje środowisko.

    ![Selektor środowiska](../../../media/quickstarts/env-selector.png)
1. Uruchom następujące polecenie.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    Po pomyślnym zakończeniu działania skryptu wszystkie wymagane zasoby powinny zostać wyświetlone w ramach subskrypcji. Skrypt zostanie skonfigurowany w sumie 12 zasobów:
    1. **Punkt końcowy przesyłania strumieniowego** — ułatwia to odtwarzanie zarejestrowanego zasobu AMS.
    1. **Maszyna wirtualna** — jest to maszyna wirtualna, która będzie pełnić funkcję urządzenia brzegowego.
    1. **Dysk** — dysk pamięci podręcznej dołączony do maszyny wirtualnej w celu przechowywania multimediów i artefaktów.
    1. **Grupa zabezpieczeń sieci** — służy do filtrowania ruchu sieciowego do i z zasobów platformy Azure w sieci wirtualnej platformy Azure.
    1. **Interfejs sieciowy** — umożliwia maszynie wirtualnej platformy Azure komunikowanie się z Internetem, platformą Azure i innymi zasobami.
    1. **Połączenie bastionu** — umożliwia nawiązanie połączenia z maszyną wirtualną przy użyciu przeglądarki i Azure Portal.
    1. **Publiczny adres IP** — dzięki temu zasoby platformy Azure mogą komunikować się z Internetem i publicznymi usługami platformy Azure
    1. **Sieć wirtualna** — umożliwia to wielu typom zasobów platformy Azure, takich jak maszyna wirtualna, bezpieczne komunikowanie się ze sobą, Internetem i sieciami lokalnymi. Dowiedz się więcej o [sieciach wirtualnych](../../../../../virtual-network/virtual-networks-overview.md)
    1. **IoT Hub** — działa jako centralny centrum komunikatów do komunikacji dwukierunkowej między aplikacją IoT, modułami IoT Edge i zarządzanymi urządzeniami.
    1. **Konto usługi multimediów** — ułatwia to zarządzanie zawartością multimediów i przesyłanie strumieniowe na platformie Azure.
    1. **Konto magazynu** — musisz mieć jedno konto magazynu podstawowego i można mieć dowolną liczbę kont magazynu pomocniczego skojarzonych z twoim kontem Media Services. Aby uzyskać więcej informacji, zobacz [konta usługi Azure Storage z kontami Azure Media Services](../../../../latest/storage-account-concept.md).
    1. **Rejestr kontenerów** — ułatwia przechowywanie prywatnych obrazów kontenerów platformy Docker i powiązanych artefaktów oraz zarządzanie nimi.

1. Po zakończeniu działania skryptu wybierz nawiasy klamrowe, aby uwidocznić strukturę folderów. Zobaczysz kilka plików w katalogu *~/CloudDrive/LVA-Sample* . Są one przydatne w tym przewodniku szybki start:

     * ***~/CloudDrive/LVA-Sample/Edge-Deployment/.env*** — ten plik zawiera właściwości, których Visual Studio Code używa do wdrożenia modułów na urządzeniu brzegowym.
     * ***~/clouddrive/lva-sample/appsetting.json*** -Visual Studio Code używa tego pliku do uruchomienia przykładowego kodu.
     
    Te pliki będą potrzebne podczas konfigurowania środowiska deweloperskiego w Visual Studio Code w następnej sekcji. Możesz chcieć skopiować je do pliku lokalnego.
    
    ![Ustawienia aplikacji](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> Jeśli wystąpią problemy z zasobami platformy Azure, które zostały utworzone, przejrzyj **[Przewodnik rozwiązywania problemów](../../../troubleshoot-how-to.md#common-error-resolutions)** , aby rozwiązać niektóre często występujące problemy.