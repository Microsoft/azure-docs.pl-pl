---
title: Rozwiązanie połączonej fabryki — często zadawane pytania — Azure | Microsoft Docs
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące akceleratora rozwiązania połączonej fabryki. Obejmuje ona linki do repozytorium GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: e6acea560c3a02420b15aff84475ab58e642116c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995968"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Często zadawane pytania dotyczące akceleratora rozwiązania połączonej fabryki

Zobacz również ogólne [często zadawane pytania](iot-accelerators-faq.md) dotyczące akceleratorów rozwiązań IoT.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Gdzie mogę znaleźć kod źródłowy akceleratora rozwiązania?

Kod źródłowy jest przechowywany w następującym repozytorium GitHub:

* [Akcelerator rozwiązania połączonej fabryki](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Co to jest OPC UA?

OPC Unified Architecture (UA), wydanej w 2008, to niezależna od platformy architektura współdziałania zorientowana na usługę. OPC UA jest używany przez różne systemy przemysłowe i urządzenia, takie jak komputery branżowe, PLCs i czujniki. OPC UA integruje funkcjonalność klasycznych specyfikacji OPC w jedną rozszerzalną strukturę z wbudowanymi zabezpieczeniami. Jest to standard, który jest oparty na OPC Foundation. [OPC Foundation](https://opcfoundation.org/) to niedochodowa organizacja z ponad 440 członkami. Celem organizacji jest użycie specyfikacji OPC w celu ułatwienia wielu dostawców, bezpiecznego i niezawodnego współdziałania w programie:

* Infrastruktura
* Specyfikacje
* Technologia
* Procesy

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Dlaczego firma Microsoft wybiera OPC UA dla akceleratora rozwiązania połączonej fabryki?

Firma Microsoft zdecydowała się na OPC UA, ponieważ jest otwartym, niezależnym od platformy, bez względu na platformę, znaną przez branżę i sprawdzoną Standard. Jest to wymagane w przypadku rozwiązań architektury referencyjnej Industrie 4,0 (RAMI 4.0) zapewniających współdziałanie między szeroką gamę procesów produkcyjnych i wyposażenia. Firma Microsoft zachęca klientów do tworzenia rozwiązań Industrie 4,0. Obsługa usługi OPC UA pomaga obniżyć poziom bariery dla klientów w celu osiągnięcia ich celów i zapewnia bezpośrednią dla nich wartość biznesową.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Jak mogę dodać publiczny adres IP do maszyny wirtualnej symulacji?

Dostępne są dwie opcje dodawania adresu IP:

* Użyj skryptu programu PowerShell `Simulation/Factory/Add-SimulationPublicIp.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory). Przekaż nazwę wdrożenia jako parametr. W przypadku lokalnego wdrożenia Użyj programu `<your username>ConnFactoryLocal` . Skrypt drukuje adres IP maszyny wirtualnej.

* W Azure Portal Znajdź grupę zasobów wdrożenia. Z wyjątkiem lokalnego wdrożenia Grupa zasobów ma nazwę, która została określona jako rozwiązanie lub nazwa wdrożenia. W przypadku lokalnego wdrożenia przy użyciu skryptu kompilacji nazwa grupy zasobów to `<your username>ConnFactoryLocal` . Teraz Dodaj nowy zasób **publicznego adresu IP** do grupy zasobów.

> [!NOTE]
> W obu przypadkach należy zadbać o zainstalowanie najnowszych poprawek, postępując zgodnie z instrukcjami w [witrynie sieci Web Ubuntu](https://wiki.ubuntu.com/Security/Upgrades). Utrzymuj aktualność instalacji, dopóki maszyna wirtualna jest dostępna za pomocą publicznego adresu IP.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Jak mogę usunąć publiczny adres IP do maszyny wirtualnej symulacji?

Dostępne są dwie opcje usunięcia adresu IP:

* Użyj symulacji skryptu programu PowerShell/fabryki/Remove-SimulationPublicIp.ps1 [repozytorium](https://github.com/Azure/azure-iot-connected-factory). Przekaż nazwę wdrożenia jako parametr. W przypadku lokalnego wdrożenia Użyj programu `<your username>ConnFactoryLocal` . Skrypt drukuje adres IP maszyny wirtualnej.

* W Azure Portal Znajdź grupę zasobów wdrożenia. Z wyjątkiem lokalnego wdrożenia Grupa zasobów ma nazwę, która została określona jako rozwiązanie lub nazwa wdrożenia. W przypadku lokalnego wdrożenia przy użyciu skryptu kompilacji nazwa grupy zasobów to `<your username>ConnFactoryLocal` . Teraz Usuń zasób **publicznego adresu IP** z grupy zasobów.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Jak mogę zalogować się do maszyny wirtualnej symulacji?

Logowanie się do maszyny wirtualnej symulacji jest obsługiwane tylko wtedy, gdy wdrożono rozwiązanie przy użyciu skryptu programu PowerShell `build.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory).

Jeśli wdrożono rozwiązanie z programu www.azureiotsolutions.com, nie można zalogować się do maszyny wirtualnej. Nie można się zalogować, ponieważ hasło jest generowane losowo i nie można go zresetować.

1. Dodaj publiczny adres IP do maszyny wirtualnej. Zobacz [Jak mogę dodać publiczny adres IP do maszyny wirtualnej symulacji?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Utwórz sesję SSH z maszyną wirtualną przy użyciu adresu IP maszyny wirtualnej.
1. Nazwa użytkownika do użycia to: `docker` .
1. Hasło, które ma być używane, zależy od wersji użytej do wdrożenia:
    * W przypadku rozwiązań wdrożonych przy użyciu skryptu build.ps1 przed 1 czerwca 2017 hasło to: `Passw0rd` .
    * W przypadku rozwiązań wdrożonych przy użyciu skryptu build.ps1 po 1 czerwca 2017 można znaleźć hasło w `<name of your deployment>.config.user` pliku. Hasło jest przechowywane w ustawieniu **VmAdminPassword** . Hasło jest generowane losowo w czasie wdrażania, chyba że zostanie ono określone przy użyciu `build.ps1` parametru skryptu `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Jak mogę zatrzymać i uruchomić wszystkie procesy platformy Docker na maszynie wirtualnej symulacji?

1. Zaloguj się do maszyny wirtualnej symulacji. Zobacz [Jak mogę zalogować się do maszyny wirtualnej symulacji?](#how-do-i-sign-in-to-the-simulation-vm)
1. Aby sprawdzić, które kontenery są aktywne, uruchom polecenie: `docker ps` .
1. Aby zatrzymać wszystkie kontenery symulacji, uruchom polecenie: `./stopsimulation` .
1. Aby rozpocząć wszystkie kontenery symulacji:
    * Wyeksportuj zmienną powłoki o nazwie **IOTHUB_CONNECTIONSTRING**. Użyj wartości ustawienia **IotHubOwnerConnectionString** w `<name of your deployment>.config.user` pliku. Na przykład:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Uruchom polecenie `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Jak mogę zaktualizować symulację w maszynie wirtualnej?

Jeśli wprowadzono jakiekolwiek zmiany symulacji, można użyć skryptu programu PowerShell `build.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory) za pomocą `updatedimulation` polecenia. Ten skrypt kompiluje wszystkie składniki symulacji, kończy symulację na maszynie wirtualnej, przekazuje je, instaluje i uruchamia.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Jak mogę dowiedzieć się, jakie parametry połączenia Centrum IoT są używane przez moje rozwiązanie?

Jeśli Twoje rozwiązanie zostało wdrożone przy użyciu `build.ps1` skryptu w [repozytorium](https://github.com/Azure/azure-iot-connected-factory), ciąg połączenia jest wartością **IotHubOwnerConnectionString** w `<name of your deployment>.config.user` pliku.

Parametry połączenia można również znaleźć za pomocą Azure Portal. W zasobie IoT Hub w grupie zasobów wdrożenia Znajdź ustawienia parametrów połączenia.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Które IoT Hub urządzenia używają symulacji połączonej fabryki?

Symulacja automatycznie rejestruje następujące urządzenia:

* proxy. Pekin. Corp. contoso
* proxy. Capetown. Corp. contoso
* serwer proxy. Mumbaj. Corp. contoso
* proxy. munich0. Corp. contoso
* proxy. Rio. Corp. contoso
* proxy. Seattle. Corp. contoso
* Wydawca. Pekin. Corp. contoso
* Publisher. Capetown. Corp. contoso
* Wydawca. Mumbaj. Corp. contoso
* Publisher. munich0. Corp. contoso
* Wydawca. Rio. Corp. contoso
* Wydawca. Seattle. Corp. contoso

Korzystając z [DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/) lub [rozszerzenia IoT dla narzędzia interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) , możesz sprawdzić, które urządzenia są zarejestrowane w usłudze IoT Hub używanej przez Twoje rozwiązanie. Aby można było korzystać z Eksploratora urządzeń, potrzebne są parametry połączenia dla Centrum IoT Hub w danym wdrożeniu. Aby użyć rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure, potrzebna jest nazwa IoT Hub.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Jak można uzyskać dane dziennika ze składników symulacji?

Wszystkie składniki w dzienniku symulacji informacje w plikach dziennika. Te pliki znajdują się na maszynie wirtualnej w folderze `home/docker/Logs` . Aby pobrać dzienniki, można użyć skryptu programu PowerShell `Simulation/Factory/Get-SimulationLogs.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory).

Ten skrypt musi zalogować się do maszyny wirtualnej. Może być konieczne podanie poświadczeń do logowania. Zobacz [Jak mogę zalogować się do maszyny wirtualnej symulacji?](#how-do-i-sign-in-to-the-simulation-vm) , aby znaleźć poświadczenia.

Skrypt dodaje/usuwa publiczny adres IP do maszyny wirtualnej, jeśli nie ma jeszcze jednego i usuwa go. Skrypt umieszcza wszystkie pliki dziennika w archiwum i pobiera archiwum do stacji roboczej deweloperskiej.

Alternatywnie Zaloguj się do maszyny wirtualnej za pośrednictwem protokołu SSH i sprawdź pliki dziennika w czasie wykonywania.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Jak sprawdzić, czy symulacja wysyła dane do chmury?

Za pomocą programu [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) lub polecenia [monitor rozszerzenia interfejsu CLI usługi Azure IoT](/cli/azure/ext/azure-iot/iot/hub?view=azure-cli-latest#ext-azure-iot-az-iot-hub-monitor-events) można sprawdzić dane wysyłane do IoT Hub z określonych urządzeń. Aby korzystać z tych narzędzi, należy znać parametry połączenia dla Centrum IoT Hub we wdrożeniu. Zobacz [Jak mogę wyszukać parametry połączenia usługi IoT Hub używanej przez moje rozwiązanie?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Sprawdź dane wysyłane przez jedno z urządzeń wydawcy:

* Wydawca. Pekin. Corp. contoso
* Publisher. Capetown. Corp. contoso
* Wydawca. Mumbaj. Corp. contoso
* Publisher. munich0. Corp. contoso
* Wydawca. Rio. Corp. contoso
* Wydawca. Seattle. Corp. contoso

Jeśli do IoT Hub nie są wysyłane żadne dane, wystąpi problem z symulacją. Jako pierwszy krok analizy należy analizować pliki dziennika składników symulacji. Zobacz [Jak mogę uzyskać dane dziennika ze składników symulacji?](#how-can-i-get-log-data-from-the-simulation-components) Następnie spróbuj zatrzymać i uruchomić symulację, a jeśli nadal nie są wysyłane żadne dane, zaktualizuj symulację całkowicie. Zobacz [Jak mogę zaktualizować symulację na maszynie wirtualnej?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Jak mogę włączyć mapy interaktywnej w rozwiązaniu połączonej fabryki?

Aby włączyć mapę interaktywną w rozwiązaniu połączonej fabryki, musisz mieć konto Azure Maps.

Podczas wdrażania z programu [www.azureiotsolutions.com](https://www.azureiotsolutions.com)proces wdrażania dodaje konto Azure Maps do grupy zasobów, która zawiera usługi Solution Accelerator.

Podczas wdrażania przy użyciu `build.ps1` skryptu w repozytorium usługi GitHub połączonej fabryki Ustaw zmienną środowiskową `$env:MapApiQueryKey` w oknie kompilacji na [klucz konta Azure Maps](../azure-maps/how-to-manage-account-keys.md). Mapa interaktywna jest następnie włączana automatycznie.

Po wdrożeniu można również dodać klucz konta Azure Maps do akceleratora rozwiązania. Przejdź do Azure Portal i uzyskaj dostęp do zasobu App Service w ramach wdrożenia połączonej fabryki. Przejdź do **ustawień aplikacji**, gdzie znajdziesz sekcję **Ustawienia aplikacji**. Ustaw wartość **MapApiQueryKey** na [klucz konta Azure Maps](../azure-maps/how-to-manage-account-keys.md). Zapisz ustawienia, a następnie przejdź do **omówienia** i ponownie uruchom App Service.

### <a name="how-do-i-create-an-azure-maps-account"></a>Jak mogę utworzyć konto Azure Maps?

Zobacz, [jak zarządzać kontem i kluczami Azure Maps](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Jak uzyskać klucz konta Azure Maps

Zobacz, [jak zarządzać kontem i kluczami Azure Maps](../azure-maps/how-to-manage-account-keys.md).

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Jak włączyć mapę interaktywną podczas debugowania lokalnego?

Aby włączyć mapę interaktywną podczas debugowania lokalnego, ustaw wartość ustawienia `MapApiQueryKey` w plikach `local.user.config` i `<yourdeploymentname>.user.config` w katalogu głównym wdrożenia na wartość **QueryKey** , która została wcześniej skopiowana.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Jak mogę użyć innego obrazu na stronie głównej mojego pulpitu nawigacyjnego?

Aby zmienić obraz statyczny pokazywany we/wy na stronie głównej pulpitu nawigacyjnego, Zastąp obraz `WebApp\Content\img\world.jpg` . Następnie Skompiluj i ponownie Wdróż WebApp.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Jak mogę używać urządzeń OPC UA z połączoną fabryką?

Aby wysyłać dane telemetryczne z urządzeń innych niż OPC UA do połączonej fabryki:

1. [Skonfiguruj nową stację w topologii połączonej fabryki](iot-accelerators-connected-factory-configure.md) w `ContosoTopologyDescription.json` pliku.

1. Pozyskaj dane telemetryczne w formacie JSON zgodnym z połączoną fabryką:

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. Format `<timestamp>` jest: `2017-12-08T19:24:51.886753Z`

1. Uruchom ponownie App Service połączonej fabryki.

### <a name="next-steps"></a>Następne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Omówienie akceleratora rozwiązania do konserwacji predykcyjnej](./iot-accelerators-predictive-walkthrough.md)
* [Wdróż Akcelerator rozwiązania połączonej fabryki](quickstart-connected-factory-deploy.md)
* [Zabezpieczenia IoT od podstaw](../iot-fundamentals/iot-security-ground-up.md)