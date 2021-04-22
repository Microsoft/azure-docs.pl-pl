---
title: Rozwiązanie połączonej fabryki — często zadawane pytania — Azure | Microsoft Docs
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące akceleratora rozwiązań połączonej fabryki. Zawiera on linki do repozytorium GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 038403743caf13087655066f4acbec4dcee598c7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874211"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>Często zadawane pytania dotyczące akceleratora rozwiązań połączonej fabryki

Zobacz też ogólne często zadawane pytania [dotyczące](iot-accelerators-faq.md) akceleratorów rozwiązań IoT.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>Gdzie można znaleźć kod źródłowy akceleratora rozwiązań?

Kod źródłowy jest przechowywany w następującym repozytorium GitHub:

* [Akcelerator rozwiązania połączonej fabryki](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>Co to jest OPC UA?

OPC Unified Architecture (UA), wydany w 2008 r., to niezależny od platformy, zorientowany na usługę standard współdziałania. Serwer OPC UA jest używany przez różne systemy przemysłowe i urządzenia, takie jak komputery branżowe, komputery PLC i czujniki. OPC UA integruje funkcjonalność klasycznych specyfikacji OPC w jednej rozszerzanej platformie z wbudowanymi zabezpieczeniami. Jest to standard, który jest oparty na platformie OPC Foundation. OPC [Foundation](https://opcfoundation.org/) to organizacja nienachodowa z ponad 440 członkami. Celem organizacji jest użycie specyfikacji OPC w celu ułatwienia współdziałania wieloplatformowego, bezpiecznego i niezawodnego za pośrednictwem:

* Infrastruktura
* Specyfikacje
* Technologia
* Procesy

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Dlaczego firma Microsoft wybrała usługę OPC UA dla akceleratora rozwiązania połączonej fabryki?

Firma Microsoft wybrała OPC UA, ponieważ jest to otwarty, niezasądny, niezależny od platformy, uznany w branży i sprawdzony standard. Jest to wymaganie dla rozwiązań architektury referencyjnej Architecture 4.0 (RAMI4.0) zapewniających współdziałanie szerokiego zestawu procesów produkcyjnych i sprzętu. Firma Microsoft widzi zapotrzebowanie klientów na tworzenie rozwiązań w Witrynie 4.0. Obsługa OPC UA pomaga zmniejszyć barierę dla klientów w celu osiągnięcia ich celów i zapewnia im natychmiastową wartość biznesową.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>Jak mogę dodać publiczny adres IP do maszyny wirtualnej symulacji?

Dostępne są dwie opcje dodawania adresu IP:

* Użyj skryptu programu PowerShell `Simulation/Factory/Add-SimulationPublicIp.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory). Przekaż nazwę wdrożenia jako parametr. W przypadku wdrożenia lokalnego użyj programu `<your username>ConnFactoryLocal` . Skrypt drukuje adres IP maszyny wirtualnej.

* W Azure Portal znajdź grupę zasobów wdrożenia. Z wyjątkiem wdrożenia lokalnego grupa zasobów ma nazwę określoną jako nazwa rozwiązania lub wdrożenia. W przypadku wdrożenia lokalnego przy użyciu skryptu kompilacji nazwa grupy zasobów to `<your username>ConnFactoryLocal` . Teraz dodaj nowy **zasób publicznego adresu IP** do grupy zasobów.

> [!NOTE]
> W obu przypadkach upewnij się, że instalujesz najnowsze poprawki, zgodnie z instrukcjami w witrynie [internetowej systemu Ubuntu.](https://wiki.ubuntu.com/Security/Upgrades) Przechowuj instalację na bieżąco, tak długo, jak maszyna wirtualna jest dostępna za pośrednictwem publicznego adresu IP.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>Jak mogę usunąć publiczny adres IP z maszyny wirtualnej symulacji?

Dostępne są dwie opcje usuwania adresu IP:

* Użyj skryptu programu PowerShell Simulation/Factory/Remove-SimulationPublicIp.ps1 [repozytorium](https://github.com/Azure/azure-iot-connected-factory). Przekaż nazwę wdrożenia jako parametr. W przypadku wdrożenia lokalnego użyj programu `<your username>ConnFactoryLocal` . Skrypt drukuje adres IP maszyny wirtualnej.

* W Azure Portal znajdź grupę zasobów wdrożenia. Z wyjątkiem wdrożenia lokalnego grupa zasobów ma nazwę określoną jako nazwa rozwiązania lub wdrożenia. W przypadku wdrożenia lokalnego przy użyciu skryptu kompilacji nazwa grupy zasobów to `<your username>ConnFactoryLocal` . Teraz usuń **zasób Publiczny adres IP** z grupy zasobów.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>Jak mogę się do maszyny wirtualnej symulacji?

Logowanie do maszyny wirtualnej symulacji jest obsługiwane tylko wtedy, gdy rozwiązanie zostało wdrożone przy użyciu skryptu programu PowerShell `build.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory).

Jeśli rozwiązanie zostało wdrożone z www.azureiotsolutions.com, nie można zalogować się do maszyny wirtualnej. Nie można się zalogować, ponieważ hasło jest generowane losowo i nie można go zresetować.

1. Dodaj publiczny adres IP do maszyny wirtualnej. Zobacz [Jak mogę dodać publiczny adres IP do maszyny wirtualnej symulacji?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)
1. Utwórz sesję SSH z maszyną wirtualną przy użyciu adresu IP maszyny wirtualnej.
1. Nazwa użytkownika do użycia to: `docker` .
1. Hasło do użycia zależy od wersji użytej do wdrożenia:
    * W przypadku rozwiązań wdrożonych przy użyciu build.ps1 przed 1 czerwca 2017 r. hasło to `Passw0rd` .
    * W przypadku rozwiązań wdrożonych build.ps1 skryptu po 1 czerwca 2017 r. hasło można znaleźć w `<name of your deployment>.config.user` pliku . Hasło jest przechowywane w ustawieniu **VmAdminPassword.** Hasło jest generowane losowo w czasie wdrażania, chyba że zostanie określone za pomocą `build.ps1` parametru skryptu `-VmAdminPassword`

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>Jak mogę zatrzymać i uruchomić wszystkie procesy platformy Docker na maszynie wirtualnej symulacji?

1. Zaloguj się do maszyny wirtualnej symulacji. Widzisz [Jak mogę się do maszyny wirtualnej symulacji?](#how-do-i-sign-in-to-the-simulation-vm)
1. Aby sprawdzić, które kontenery są aktywne, uruchom: `docker ps` .
1. Aby zatrzymać wszystkie kontenery symulacji, uruchom: `./stopsimulation` .
1. Aby uruchomić wszystkie kontenery symulacji:
    * Wyeksportuj zmienną powłoki o **nazwie IOTHUB_CONNECTIONSTRING**. Użyj wartości ustawienia **IotHubOwnerConnectionString** w `<name of your deployment>.config.user` pliku . Na przykład:

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * Uruchom polecenie `./startsimulation`.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>Jak mogę zaktualizować symulację na maszynie wirtualnej?

Jeśli w symulacji zostały wprowadzone jakiekolwiek zmiany, możesz użyć skryptu programu PowerShell `build.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory) za pomocą `updatedimulation` polecenia . Ten skrypt tworzy wszystkie składniki symulacji, zatrzymuje symulację na maszynie wirtualnej, przesyła je, instaluje i uruchamia.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>Jak mogę się z parametrów połączenia centrum IoT używanego przez moje rozwiązanie?

Jeśli rozwiązanie zostało wdrożone za pomocą skryptu w repozytorium , parametrów połączenia jest wartość `build.ps1` **IotHubOwnerConnectionString** w [](https://github.com/Azure/azure-iot-connected-factory) `<name of your deployment>.config.user` pliku.

Możesz również znaleźć ciąg połączenia przy użyciu Azure Portal. W IoT Hub zasobów w grupie zasobów wdrożenia znajdź ustawienia parametrów połączenia.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>Z IoT Hub korzysta symulacja połączonej fabryki?

Symulacja samodzielnie rejestruje następujące urządzenia:

* proxy.contoso.corp.contoso
* proxy.przylądek.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.przech0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.contoso.corp.contoso
* publisher.kapsztad.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.materialch0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Za pomocą [narzędzia DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/) lub rozszerzenia [IoT](https://github.com/Azure/azure-iot-cli-extension) dla interfejsu wiersza polecenia platformy Azure można sprawdzić, które urządzenia są zarejestrowane w centrum IoT, którego używa rozwiązanie. Aby korzystać z eksploratora urządzeń, potrzebne są w twoim wdrożeniu ciągi połączenia dla centrum IoT. Aby użyć rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure, IoT Hub nazwę użytkownika.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>Jak mogę uzyskać dane dzienników ze składników symulacji?

Wszystkie składniki w dzienniku symulacji są logować się do plików dziennika. Te pliki można znaleźć na maszynie wirtualnej w folderze `home/docker/Logs` . Aby pobrać dzienniki, możesz użyć skryptu programu PowerShell `Simulation/Factory/Get-SimulationLogs.ps1` w [repozytorium](https://github.com/Azure/azure-iot-connected-factory).

Ten skrypt musi zalogować się do maszyny wirtualnej. Może być konieczne podanie poświadczeń logowania. Zobacz [Jak mogę się do maszyny wirtualnej symulacji?](#how-do-i-sign-in-to-the-simulation-vm) w celu znalezienia poświadczeń.

Skrypt dodaje/usuwa publiczny adres IP do maszyny wirtualnej, jeśli jeszcze jej nie ma i usuwa ją. Skrypt umieszcza wszystkie pliki dziennika w archiwum i pobiera je na dewelopera stacji roboczej.

Alternatywnie zaloguj się do maszyny wirtualnej za pośrednictwem SSH i sprawdź pliki dziennika w czasie wykonywania.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>Jak sprawdzić, czy symulacja wysyła dane do chmury?

Za pomocą [narzędzia Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) lub polecenia monitor-events rozszerzenia interfejsu wiersza polecenia usługi Azure [IoT](/cli/azure/iot/hub#az_iot_hub_monitor_events) możesz sprawdzić dane wysyłane do usługi IoT Hub z określonych urządzeń. Aby korzystać z tych narzędzi, musisz znać ciąg połączenia dla centrum IoT w swoim wdrożeniu. Zobacz [Jak mogę, jak znaleźć ciąg połączenia centrum IoT używanego przez moje rozwiązanie?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)

Sprawdź dane wysyłane przez jedno z urządzeń wydawcy:

* publisher.contoso.corp.contoso
* publisher.kapsztad.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.ichch0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

Jeśli nie widzisz żadnych danych wysłanych do IoT Hub, występuje problem z symulacją. Pierwszym krokiem analizy jest przeanalizowanie plików dziennika składników symulacji. Zobacz [Jak mogę pobrać dane dzienników ze składników symulacji?](#how-can-i-get-log-data-from-the-simulation-components) Następnie spróbuj zatrzymać i uruchomić symulację, a jeśli nadal nie są wysyłane żadne dane, zaktualizuj symulację całkowicie. Zobacz [Jak mogę zaktualizować symulację na maszynie wirtualnej?](#how-do-i-update-the-simulation-in-the-vm)

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>Jak mogę włączyć interaktywną mapę w rozwiązaniu połączonej fabryki?

Aby włączyć interaktywną mapę w rozwiązaniu połączonej fabryki, musisz mieć Azure Maps klienta.

Podczas wdrażania z [www.azureiotsolutions.com](https://www.azureiotsolutions.com)proces wdrażania dodaje konto Azure Maps do grupy zasobów zawierającej usługi akceleratora rozwiązań.

Podczas wdrażania przy użyciu skryptu w repozytorium GitHub połączonej fabryki ustaw zmienną środowiskową w oknie kompilacji na klucz `build.ps1` `$env:MapApiQueryKey` Azure Maps [usługi](../azure-maps/how-to-manage-account-keys.md). Interaktywna mapa jest następnie włączana automatycznie.

Możesz również dodać klucz Azure Maps do akceleratora rozwiązania po wdrożeniu. Przejdź do Azure Portal i uzyskaj dostęp do zasobu App Service we wdrożeniu połączonej fabryki. Przejdź do **sekcji Ustawienia aplikacji,** w której znajduje się **sekcja Ustawienia aplikacji.** Ustaw wartość **MapApiQueryKey** na [klucz Azure Maps konta.](../azure-maps/how-to-manage-account-keys.md) Zapisz ustawienia, a następnie przejdź do **strony Przegląd** i ponownie uruchom App Service.

### <a name="how-do-i-create-an-azure-maps-account"></a>Jak mogę utworzyć Azure Maps konta?

Zobacz Jak [zarządzać kontem Azure Maps konta i kluczami.](../azure-maps/how-to-manage-account-keys.md)

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Jak uzyskać klucz Azure Maps konta

Zobacz Jak [zarządzać kontem Azure Maps konta i kluczami.](../azure-maps/how-to-manage-account-keys.md)

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>Jak włączyć interaktywną mapę podczas lokalnego debugowania?

Aby włączyć interaktywną mapę podczas debugowania lokalnego, ustaw wartość ustawienia w plikach i w katalogu głównym wdrożenia na skopiowaną wcześniej wartość `MapApiQueryKey` `local.user.config` `<yourdeploymentname>.user.config` **QueryKey.**

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>Jak mogę użyć innego obrazu na stronie głównej pulpitu nawigacyjnego?

Aby zmienić statyczny obraz wyświetlany na stronie głównej pulpitu nawigacyjnego, zastąp obraz `WebApp\Content\img\world.jpg` . Następnie ponownie skompilować i ponownie wdązywać aplikacje internetowe.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>Jak mogę używać urządzeń innych niż OPC UA z połączenią fabryką?

Aby wysłać dane telemetryczne z urządzeń innych niż OPC UA do połączonej fabryki:

1. [Skonfiguruj nową stację w topologii połączonej fabryki](iot-accelerators-connected-factory-configure.md) w `ContosoTopologyDescription.json` pliku.

1. Pozyskaj dane telemetryczne w formacie JSON zgodnym z usługą Connected Factory:

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

1. Format pliku `<timestamp>` to: `2017-12-08T19:24:51.886753Z`

1. Uruchom ponownie usługę Connected Factory App Service.

### <a name="next-steps"></a>Następne kroki

Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Wdrażanie akceleratora rozwiązania połączonej fabryki](quickstart-connected-factory-deploy.md)
* [Zabezpieczenia IoT od podstaw](../iot-fundamentals/iot-security-ground-up.md)