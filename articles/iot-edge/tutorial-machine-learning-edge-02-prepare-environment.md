---
title: 'Samouczek: Konfigurowanie środowiska Machine Learning na Azure IoT Edge'
description: 'Samouczek: Przygotowanie środowiska do opracowywania i wdrażania modułów na potrzeby uczenia maszynowego na brzegu.'
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/12/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 723d7b6ae5b96cf6dc622be1b1bb98065151787f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463157"
---
# <a name="tutorial-set-up-an-environment-for-machine-learning-on-iot-edge"></a>Samouczek: Konfigurowanie środowiska do uczenia maszynowego na IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Ten artykuł ułatwia przygotowanie środowiska do projektowania i wdrażania. Najpierw skonfiguruj komputer deweloperski przy użyciu wszystkich potrzebnych narzędzi. Następnie utwórz niezbędne zasoby w chmurze na platformie Azure.

W tej części samouczka dowiesz się, jak:

> [!div class="checklist"]
>
> * Skonfiguruj maszynę wirtualną na potrzeby programowania.
> * Skonfiguruj IoT Hub i magazyn w chmurze do użycia w środowisku deweloperskim.

## <a name="prerequisites"></a>Wymagania wstępne

Ten artykuł jest częścią serii samouczka dotyczącego używania Azure Machine Learning w IoT Edge. Każdy artykuł w serii jest oparty na pracy w poprzednim artykule. Jeśli ten artykuł został bezpośrednio osiągnięty, odwiedź [pierwszy artykuł](tutorial-machine-learning-edge-01-intro.md) z serii.

## <a name="set-up-the-development-vm"></a>Konfigurowanie maszyny wirtualnej tworzenia

Ten krok jest zwykle wykonywany przez dewelopera chmury. Niektóre oprogramowanie może być również przydatne w przypadku Analityka danych.

Utworzyliśmy skrypt programu PowerShell, który tworzy maszynę wirtualną platformy Azure z wieloma wcześniej skonfigurowanymi wymaganiami wstępnymi. Utworzona przez nas maszyna wirtualna musi być w stanie obsłużyć [wirtualizację zagnieżdżoną](../virtual-machines/windows/nested-virtualization.md), co oznacza, że wybrano rozmiar maszyny [Standard_D8s_v3](../virtual-machines/dv3-dsv3-series.md) .

Maszyna wirtualna programu Development zostanie skonfigurowana przy użyciu:

* Windows 10
* [Narzędzia Chocolatey](https://chocolatey.org/)
* [Pulpit Docker dla systemu Windows](https://www.docker.com/products/docker-desktop)
* [Git dla systemu Windows](https://gitforwindows.org/)
* [Menedżer poświadczeń git dla systemu Windows](https://github.com/Microsoft/Git-Credential-Manager-for-Windows)
* [Zestaw .NET Core SDK](https://dotnet.microsoft.com/)
* [Python 3](https://www.python.org/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure PowerShell](/powershell/azure/)
* [Rozszerzenia VS Code](https://marketplace.visualstudio.com/search?target=VSCode)
  * [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  * [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
  * [Program PowerShell](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)

Maszyna wirtualna dla deweloperów nie jest ściśle wymagana — wszystkie narzędzia programistyczne można uruchomić na komputerze lokalnym. Jednak zdecydowanie zalecamy użycie maszyny wirtualnej w celu zagwarantowania pola poziomu odtwarzania.

Tworzenie i Konfigurowanie maszyny wirtualnej trwa około 30 minut.

1. Sklonuj lub Pobierz [Machine Learning i IoT Edge](https://github.com/Azure-Samples/IoTEdgeAndMlSample) przykładowe repozytorium na komputer lokalny.

1. Otwórz program PowerShell jako administrator i przejdź do katalogu **\IoTEdgeAndMlSample\DevVM** znajdującego się w katalogu głównym, do którego pobrano kod. Odwołujemy się do katalogu głównego źródła jako `srcdir` .

    ```powershell
    cd c:\srcdir\IoTEdgeAndMlSample\DevVM
    ```

   Katalog DevVM zawiera pliki potrzebne do utworzenia maszyny wirtualnej platformy Azure odpowiednie do wykonania tego samouczka.

1. Uruchom następujące polecenie, aby zezwolić na wykonywanie skryptów. Po wyświetleniu monitu wybierz pozycję **tak, aby wszystkie** .

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Uruchom Create-AzureDevVM.ps1.

    ```powershell
    .\Create-AzureDevVm.ps1
    ```

    Po wyświetleniu monitu podaj następujące informacje:

    * **Identyfikator subskrypcji platformy Azure**: Identyfikator subskrypcji, który można znaleźć w [subskrypcjach platformy Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) w portalu.
    * **Nazwa grupy zasobów**: Nazwa nowej lub istniejącej grupy zasobów na platformie Azure.
    * **Lokalizacja**: Wybierz lokalizację platformy Azure, w której zostanie utworzona maszyna wirtualna. Na przykład "zachodnie stany USA 2" lub "Europa Północna". Aby uzyskać więcej informacji, zobacz [lokalizacje platformy Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **Nazwa użytkownika**: Podaj nazwę zapamiętaną dla konta administratora dla maszyny wirtualnej.
    * **Hasło**: Ustaw hasło dla konta administratora dla maszyny wirtualnej.

   Skrypt jest uruchamiany przez kilka minut, ponieważ wykonuje następujące czynności:

    1. Instaluje [Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).
    1. Zostanie wyświetlony komunikat z prośbą o zalogowanie się do platformy Azure.
    1. Potwierdza informacje dotyczące tworzenia maszyny wirtualnej. Naciśnij klawisz **t** lub **Enter** , aby kontynuować.
    1. Tworzy grupę zasobów, jeśli nie istnieje.
    1. Wdraża maszynę wirtualną.
    1. Włącza funkcję Hyper-V na maszynie wirtualnej.
    1. Instaluje oprogramowanie potrzebne do programowania i klonowania przykładowego repozytorium.
    1. Uruchamia ponownie maszynę wirtualną.
    1. Tworzy na pulpicie plik RDP służący do nawiązywania połączenia z maszyną wirtualną.

   Jeśli zostanie wyświetlony monit o podanie nazwy maszyny wirtualnej w celu ponownego uruchomienia, można skopiować jej nazwę z danych wyjściowych skryptu. W danych wyjściowych znajduje się również ścieżka do pliku RDP w celu nawiązania połączenia z maszyną wirtualną.

### <a name="set-auto-shutdown-schedule"></a>Ustawianie harmonogramu automatycznego zamykania

Aby pomóc obniżyć koszty, maszyna wirtualna tworzenia została utworzona z harmonogramem automatycznego zamykania, ustawionym na 1900 PST. Może być konieczne zaktualizowanie tego ustawienia w zależności od lokalizacji i harmonogramu. Aby zaktualizować harmonogram zamykania:

1. W Azure Portal przejdź do maszyny wirtualnej, która utworzyła skrypt.

1. W menu po lewej stronie w obszarze **operacje** wybierz pozycję **automatyczne zamykanie**.

1. Dostosuj **zaplanowane zamknięcie** i **strefę czasową** zgodnie z potrzebami i wybierz pozycję **Zapisz**.

## <a name="connect-to-the-development-vm"></a>Nawiązywanie połączenia z maszyną wirtualną tworzenia

Po utworzeniu maszyny wirtualnej musimy zainstalować oprogramowanie wymagane do ukończenia tego samouczka.

1. Kliknij dwukrotnie plik RDP utworzony przez skrypt na pulpicie.

1. Zostanie wyświetlone okno dialogowe z informacją, że Wydawca połączenia zdalnego jest nieznany. Jest to akceptowalne, więc wybierz pozycję **Połącz**.

1. Podaj podane hasło administratora, aby utworzyć maszynę wirtualną, a następnie kliknij przycisk **OK**.

1. Zostanie wyświetlony monit o zaakceptowanie certyfikatu dla maszyny wirtualnej. Wybierz pozycję **Tak**.

## <a name="install-visual-studio-code-extensions"></a>Instalowanie rozszerzeń programu Visual Studio Code

Teraz, gdy masz połączenie z maszyną deweloperskią, Dodaj przydatne rozszerzenia, aby Visual Studio Code ułatwić programowanie.

1. Połącz się z maszyną wirtualną tworzenia, Otwórz okno programu PowerShell i przejdź do katalogu **C:\source\IoTEdgeAndMlSample\DevVM** . Ten katalog został utworzony przez skrypt, który utworzył maszynę wirtualną.

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\DevVM
    ```

1. Uruchom następujące polecenie, aby zezwolić na wykonywanie skryptów. Po wyświetleniu monitu wybierz pozycję **tak, aby wszystkie** .

    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process
    ```

1. Uruchom skrypt rozszerzeń Visual Studio Code.

    ```powershell
    .\Enable-CodeExtensions.ps1
    ```

1. Skrypt będzie uruchamiany przez kilka minut instalacji rozszerzeń programu VS Code:

    * Narzędzia usługi Azure IoT
    * Python
    * C#
    * Docker
    * PowerShell

## <a name="set-up-iot-hub-and-storage"></a>Konfigurowanie IoT Hub i magazynu

Te kroki są zwykle wykonywane przez dewelopera chmury.

Platforma Azure IoT Hub to serce z dowolnej aplikacji IoT, ponieważ obsługuje ona bezpieczną komunikację między urządzeniami IoT i chmurą. Jest to główny punkt koordynacji dla operacji rozwiązania IoT Edge Machine Learning.

* IoT Hub używa tras do kierowania danych przychodzących z urządzeń IoT do innych usług podrzędnych. Będziemy korzystać z tras IoT Hub do wysyłania danych urządzenia do usługi Azure Storage. W usłudze Azure Storage dane urządzenia są używane przez Azure Machine Learning do uczenia pozostałego klasyfikatora okresu użytkowania (pozostałego czasu eksploatacji).

* W dalszej części tego samouczka będziemy używać IoT Hub do konfigurowania naszego urządzenia Azure IoT Edge i zarządzania nim.

W tej sekcji użyjesz skryptu do utworzenia usługi Azure IoT Hub i konta usługi Azure Storage. Następnie w Azure Portal konfigurujesz trasę, która przekazuje dane odebrane przez centrum do kontenera usługi Azure Storage. Wykonanie tych czynności zajmie około 10 minut.

1. Połącz się z maszyną wirtualną tworzenia, Otwórz okno programu PowerShell i przejdź do katalogu **IoTHub** .

    ```powershell
    cd C:\source\IoTEdgeAndMlSample\IoTHub
    ```

1. Uruchom skrypt tworzenia. Użyj tych samych wartości dla identyfikatora subskrypcji, lokalizacji i grupy zasobów, jak podczas tworzenia maszyny wirtualnej deweloperskiej.

    ```powershell
    .\New-HubAndStorage.ps1 -SubscriptionId <subscription id> -Location <location> -ResourceGroupName <resource group>
    ```

    * Zostanie wyświetlony monit o zalogowanie się do platformy Azure.
    * Skrypt potwierdza informacje dotyczące tworzenia centrum i konta magazynu. Naciśnij klawisz **t** lub **Enter** , aby kontynuować.

Wykonywanie skryptu trwa około dwóch minut. Po zakończeniu skrypt wyświetla nazwę Centrum IoT i konto magazynu.

## <a name="review-route-to-storage-in-iot-hub"></a>Przejrzyj trasy do magazynu w IoT Hub

W ramach tworzenia Centrum IoT, skrypt, który został uruchomiony w poprzedniej sekcji, również utworzył niestandardowy punkt końcowy i trasę. Trasy IoT Hub składają się z wyrażenia zapytania i punktu końcowego. Jeśli komunikat jest zgodny z wyrażeniem, dane są wysyłane wzdłuż trasy do skojarzonego punktu końcowego. Punkty końcowe mogą być Event Hubs, kolejki Service Bus i tematy. W takim przypadku punkt końcowy jest kontenerem obiektów BLOB na koncie magazynu. Użyjmy Azure Portal, aby przejrzeć trasę utworzoną przez nasz skrypt.

1. Otwórz [Azure Portal](https://portal.azure.com) i przejdź do grupy zasobów, która jest używana w tym samouczku.

1. Na liście zasobów wybierz IoT Hub utworzoną przez skrypt. Nazwa będzie kończyć się znakami losowymi, takimi jak `IotEdgeAndMlHub-jrujej6de6i7w` .

1. W menu po lewej stronie w obszarze **Obsługa wiadomości** wybierz pozycję **routing wiadomości**.

1. Na stronie **routing wiadomości** wybierz kartę **niestandardowe punkty końcowe** .

1. Rozwiń sekcję **Magazyn** :

   ![Sprawdź, czy turbofanDeviceStorage znajduje się na liście niestandardowych punktów końcowych](media/tutorial-machine-learning-edge-02-prepare-environment/custom-endpoints.png)

   Widzimy, że **turbofanDeviceStorage** znajduje się na liście niestandardowych punktów końcowych. Zwróć uwagę na następujące cechy tego punktu końcowego:

   * Wskazuje on kontener magazynu obiektów blob, który został utworzony, `devicedata` zgodnie z **nazwą kontenera**.
   * Jego **Format nazwy pliku** zawiera partycję jako ostatni element w nazwie. Ten format jest wygodniejszy dla operacji na plikach, które będziemy wykonywać, Azure Notebooks w dalszej części tego samouczka.
   * Jego **stan** powinien być w dobrej kondycji.

1. Wybierz kartę **Trasa**.

1. Wybierz trasę o nazwie **turbofanDeviceDataToStorage**.

1. Na stronie **szczegóły tras** Zwróć uwagę, że punkt końcowy trasy jest punktem końcowym **turbofanDeviceStorage** .

   ![Przejrzyj szczegóły dotyczące trasy turbofanDeviceDataToStorage](media/tutorial-machine-learning-edge-02-prepare-environment/route-details.png)

1. Zapoznaj się z **kwerendą routingu**, która jest ustawiona na **wartość true**. To ustawienie oznacza, że wszystkie komunikaty telemetryczne urządzenia będą zgodne z tą trasą; w związku z tym wszystkie komunikaty będą wysyłane do punktu końcowego **turbofanDeviceStorage** .

1. Ponieważ nie wprowadzono żadnych zmian, Zamknij Tę stronę.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Ten samouczek jest częścią zestawu, w którym każdy artykuł kompiluje się w pracy wykonanej w poprzednich. Zaczekaj na oczyszczenie wszystkich zasobów do momentu zakończenia ostatniego samouczka.

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzyliśmy IoT Hub i skonfigurowano trasę do konta usługi Azure Storage. Następnie wyślemy dane z zestawu symulowanych urządzeń za pośrednictwem IoT Hub na konto magazynu. W dalszej części tego samouczka po skonfigurowaniu naszych IoT Edge urządzeń i modułów, będziemy ponownie odwiedzić trasy i poszukać nieco więcej w zapytaniu routingu.

Przejdź do następnego artykułu, aby utworzyć symulowane urządzenie do monitorowania.

> [!div class="nextstepaction"]
> [Generowanie danych urządzenia](tutorial-machine-learning-edge-03-generate-data.md)
