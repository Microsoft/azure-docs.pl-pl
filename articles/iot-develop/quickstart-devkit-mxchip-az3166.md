---
title: Łączenie zestawu DEWELOPERSKIEGO AZ3166 z platformą Azure IoT Central Szybki Start
description: Aby połączyć urządzenie AZ3166 z usługą Azure IoT i wysłać dane telemetryczne, użyj wbudowanego oprogramowania RTO platformy Azure.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 03/17/2021
ms.openlocfilehash: 160797367e2daf0cb6fe708d626cbf217c9992c8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448608"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-central"></a>Szybki Start: Łączenie zestawu DEWELOPERSKIEGO AZ3166 Devkit z IoT Central

**Dotyczy**: [projektowanie urządzeń osadzonych](about-iot-develop.md#embedded-device-development)<br>
**Łączny czas ukończenia**: 30 minut

[![Przeglądaj kod](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

W tym samouczku za pomocą usługi Azure RTO nawiążesz połączenie z usługą Azure IoT zestawu DEWELOPERSKIEGO AZ3166 IoT DevKit (poniżej zestawu deweloperskiego DevKit). Artykuł jest częścią serii [wprowadzenie do projektowania urządzeń z usługą Azure IoT Embedded](quickstart-device-development.md). Seria wprowadza do usługi Azure RTO deweloperów urządzeń i pokazuje, jak połączyć kilka zestawów ewaluacyjnych urządzeń z usługą Azure IoT.

Wykonasz następujące zadania:

* Zainstaluj zestaw osadzonych narzędzi programistycznych na potrzeby programowania zestawu DEWELOPERSKIEGO DevKit w języku C
* Tworzenie obrazu i błyskowanie go na zestawu DEWELOPERSKIEGO DevKit
* Użyj usługi Azure IoT Central, aby tworzyć składniki w chmurze, wyświetlać właściwości, wyświetlać dane telemetryczne urządzeń i wywoływać bezpośrednie polecenia

## <a name="prerequisites"></a>Wymagania wstępne

* KOMPUTER z systemem Microsoft Windows 10
* Narzędzie [git](https://git-scm.com/downloads) do klonowania repozytorium
* Sprzęt

    > * [Zestawu DEWELOPERSKIEGO AZ3166 IoT DevKit](https://aka.ms/iot-devkit) (zestawu deweloperskiego DevKit)
    > * Wi-Fi 2,4 GHz
    > * USB 2,0 kabla męskiego do micro USB

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

Aby skonfigurować środowisko programistyczne, najpierw Sklonuj repozytorium GitHub zawierające wszystkie zasoby, które są potrzebne dla tego samouczka. Następnie instalujesz zestaw narzędzi programistycznych.

### <a name="clone-the-repo-for-the-tutorial"></a>Klonuj repozytorium dla samouczka

Sklonuj następujące repozytorium, aby pobrać wszystkie przykładowe kod urządzenia, skrypty instalacyjne i wersje w trybie offline dokumentacji. Jeśli wcześniej Sklonowano to repozytorium w innym samouczku, nie trzeba tego robić ponownie.

Aby sklonować repozytorium, uruchom następujące polecenie:

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>Instalowanie narzędzi

Sklonowane repozytorium zawiera skrypt instalacyjny, który instaluje i konfiguruje wymagane narzędzia. Jeśli zainstalowano te narzędzia w innym samouczku w przewodniku wprowadzenie, nie trzeba tego robić ponownie.

> [!NOTE]
> Skrypt Instalatora instaluje następujące narzędzia:
> * [CMAKE](https://cmake.org): Kompiluj
> * [ARM w zatoce](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): kompilacja
> * [Termite](https://www.compuphase.com/software_termite.htm): Monitoruj dane wyjściowe portu szeregowego dla podłączonych urządzeń

Aby zainstalować narzędzia:

1. W Eksploratorze plików przejdź do następującej ścieżki w repozytorium i uruchom skrypt instalacyjny o nazwie *get-toolchain.bat*:

    > *getting-started\tools\get-toolchain.bat*

1. Po zakończeniu instalacji Otwórz nowe okno konsoli, aby rozpoznać zmiany konfiguracji wprowadzone przez skrypt Instalatora. Za pomocą tej konsoli można wykonać pozostałe zadania programistyczne w samouczku. Możesz użyć polecenia Windows CMD, PowerShell lub git bash dla systemu Windows.
1. Uruchom następujący kod, aby upewnić się, że zainstalowano CMake w wersji 3,14 lub nowszej.

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>Tworzenie składników w chmurze

### <a name="create-the-iot-central-application"></a>Tworzenie aplikacji IoT Central

Istnieje kilka sposobów łączenia urządzeń z usługą Azure IoT. W tej sekcji dowiesz się, jak połączyć urządzenie przy użyciu usługi Azure IoT Central. IoT Central to platforma aplikacji IoT, która pozwala zmniejszyć koszty i złożoność tworzenia rozwiązań IoT oraz zarządzania nimi.

Aby utworzyć nową aplikację:
1. W [portalu usługi Azure IoT Central](https://apps.azureiotcentral.com/)wybierz pozycję **Moje aplikacje** w menu nawigacji po stronie.
1. Wybierz pozycję **+ Nowa aplikacja**.
1. Wybierz pozycję **Aplikacje niestandardowe**.
1. Dodaj nazwę aplikacji i adres URL.
1. Wybierz **bezpłatny** plan cenowy, aby aktywować 7-dniową wersję próbną.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-custom.png" alt-text="Tworzenie aplikacji niestandardowej na platformie Azure IoT Central":::

1. Wybierz przycisk **Utwórz**.

    Po IoT Central aplikacji program przekierowuje automatycznie do nowego pulpitu nawigacyjnego aplikacji.

    > [!NOTE]
    > Jeśli masz istniejącą aplikację IoT Central, możesz jej użyć do wykonania kroków opisanych w tym artykule zamiast tworzenia nowej aplikacji.

### <a name="create-a-new-device"></a>Tworzenie nowego urządzenia

W tej sekcji użyjesz pulpitu nawigacyjnego aplikacji IoT Central, aby utworzyć nowe urządzenie. Informacje o połączeniu dla nowo utworzonego urządzenia zostaną użyte do bezpiecznego połączenia urządzenia fizycznego w dalszej części.

Aby utworzyć urządzenie:
1. Na pulpicie nawigacyjnym aplikacji wybierz pozycję **urządzenia** w menu nawigacji po stronie.
1. Wybierz pozycję **+ Nowy** , aby otworzyć okno **Utwórz nowe urządzenie** .
1. Pozostaw szablon urządzenia jako **nieprzypisany**.
1. Wprowadź odpowiednią nazwę urządzenia i identyfikator urządzenia.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-create-device.png" alt-text="Tworzenie urządzenia w usłudze Azure IoT Central":::

1. Wybierz przycisk **Utwórz**.
1. Nowo utworzone urządzenie zostanie wyświetlone na liście **wszystkie urządzenia** .  Wybierz nazwę urządzenia, aby wyświetlić szczegóły.
1. Wybierz pozycję **Połącz** w prawym górnym pasku menu, aby wyświetlić informacje o połączeniu używane do konfigurowania urządzenia w następnej sekcji.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-connection-info.png" alt-text="Wyświetl szczegóły połączenia z urządzeniem":::

1. Zwróć uwagę na wartości połączenia dla następujących parametrów połączenia, które są wyświetlane w oknie dialogowym **Połącz** . Te wartości zostaną dodane do pliku konfiguracji w następnym kroku:

    > * `ID scope`
    > * `Device ID`
    > * `Primary key`

## <a name="prepare-the-device"></a>Przygotowywanie urządzenia

Aby połączyć zestawu DEWELOPERSKIEGO DevKit z platformą Azure, zmodyfikuj plik konfiguracji dla Wi-Fi i ustawień usługi Azure IoT, Odbuduj obraz i wypróbuje obraz na urządzeniu.

### <a name="add-configuration"></a>Dodawanie konfiguracji

1. Otwórz następujący plik w edytorze tekstu:

    > *getting-started\MXChip\AZ3166\app\ azure_config. h*

1. Ustaw stałe Wi-Fi na następujące wartości ze środowiska lokalnego.

    |Stała nazwa|Wartość|
    |-------------|-----|
    |`WIFI_SSID` |{*Twój Wi-Fi SSID*}|
    |`WIFI_PASSWORD` |{*Twoje hasło Wi-Fi*}|
    |`WIFI_MODE` |{*Jedna z wartości wyliczanych Wi-Fi w pliku*}|

1. Ustaw stałe informacji o urządzeniu Azure IoT na wartości zapisane po utworzeniu zasobów platformy Azure.

    |Stała nazwa|Wartość|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*Wartość zakresu identyfikatora*}|
    |`IOT_DPS_REGISTRATION_ID` |{*Wartość identyfikatora urządzenia*}|
    |`IOT_DEVICE_SAS_KEY` |{*Wartość klucza podstawowego*}|

1. Zapisz i zamknij plik.

### <a name="build-the-image"></a>Tworzenie obrazu

W konsoli programu lub w Eksploratorze plików uruchom skrypt *rebuild.bat* w następującej ścieżce, aby utworzyć obraz:

> *getting-started\MXChip\AZ3166\tools\rebuild.bat*

Po zakończeniu kompilacji upewnij się, że plik binarny został utworzony w następującej ścieżce:

> *getting-started\MXChip\AZ3166\build\app\ mxchip_azure_iot. bin*

### <a name="flash-the-image"></a>Obraz przedstawiający błysk

1. Na zestawu DEWELOPERSKIEGO DevKit Znajdź przycisk **Reset** i port Micro USB. Te składniki są używane w poniższych krokach. Obie są wyróżnione na poniższej ilustracji:

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/mxchip-iot-devkit.png" alt-text="Lokalizowanie kluczowych składników na tablicy zestawu deweloperskiego Devkit":::

1. Podłącz kabel micro USB do portu micro USB w zestawu DEWELOPERSKIEGO DevKit, a następnie połącz go z komputerem.
1. W Eksploratorze plików Znajdź plik binarny, który został utworzony w poprzedniej sekcji.
1. Skopiuj plik binarny *mxchip_azure_iot. bin*.
1. W Eksploratorze plików Znajdź urządzenie zestawu DEWELOPERSKIEGO DevKit podłączone do komputera. Urządzenie jest wyświetlane jako dysk w systemie z etykietą dysku **AZ3166**.
1. Wklej plik binarny do folderu głównego zestawu DEWELOPERSKIEGO Devkit. Migająca funkcja jest uruchamiana automatycznie i kończy się w ciągu kilku sekund.

    > [!NOTE]
    > W trakcie migania, zielone diody LED przełączają się na zestawu DEWELOPERSKIEGO DevKit.

### <a name="confirm-device-connection-details"></a>Potwierdź szczegóły połączenia z urządzeniem

Możesz użyć aplikacji **Termite** do monitorowania komunikacji i upewnienia się, że urządzenie zostało prawidłowo skonfigurowane.

1. Uruchom **Termite**.
    > [!TIP]
    > Jeśli nie możesz połączyć Termite z Devkit, zainstaluj [sterownik "St-link"](https://my.st.com/content/ccc/resource/technical/software/driver/files/stsw-link009.zip) i spróbuj ponownie. Dodatkowe kroki można znaleźć w temacie  [Rozwiązywanie problemów](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md) .
1. Wybierz pozycję **Ustawienia**.
1. W oknie dialogowym **Ustawienia portu szeregowego** sprawdź następujące ustawienia i zaktualizuj je w razie konieczności:
    * **Szybkość transmisji**: 115 200
    * **Port**: port, z którym jest połączony zestawu deweloperskiego DevKit. Jeśli na liście rozwijanej znajduje się wiele opcji portów, można znaleźć właściwy port do użycia. Otwórz **Device Manager** systemu Windows i Wyświetl **porty** , aby zidentyfikować port, który ma być używany.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/termite-settings.png" alt-text="Potwierdź ustawienia w aplikacji Termite":::

1. Wybierz przycisk OK.
1. Naciśnij przycisk **Reset** na urządzeniu. Przycisk jest oznaczony etykietą na urządzeniu i znajduje się blisko łącznika Micro USB.
1. W aplikacji **Termite** sprawdź następujące wartości punktów kontrolnych, aby upewnić się, że urządzenie zostało zainicjowane i połączone z usługą Azure IoT.

    ```output
    Starting Azure thread

    Initializing WiFi
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 10.0.0.123
        Mask: 255.255.255.0
        Gateway: 10.0.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 10.0.0.1
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 185.242.56.3
        SNTP time update: Nov 16, 2020 23:47:35.385 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: ***
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***
        Device id: ***
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoTHub
    SUCCESS: Azure IoT Hub client initialized

    Starting Main loop
    ```

Pozostaw Termite otwarte, aby monitorować dane wyjściowe urządzenia w poniższych krokach.

## <a name="verify-the-device-status"></a>Weryfikowanie stanu urządzenia

Aby wyświetlić stan urządzenia w portalu IoT Central:
1. Na pulpicie nawigacyjnym aplikacji wybierz pozycję **urządzenia** w menu nawigacji po stronie.
1. Upewnij się, że **stan urządzenia** został zaktualizowany do **aprowizacji**.
1. Upewnij się, że **szablon urządzenia** został zaktualizowany do **przewodnika Wprowadzenie**.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-view-status.png" alt-text="Wyświetlanie stanu urządzenia w IoT Central":::

## <a name="view-telemetry"></a>Wyświetlanie danych telemetrycznych

Za pomocą IoT Central można wyświetlić przepływ danych telemetrycznych z urządzenia do chmury.

Aby wyświetlić dane telemetryczne w portalu IoT Central:

1. Na pulpicie nawigacyjnym aplikacji wybierz pozycję **urządzenia** w menu nawigacji po stronie.
1. Wybierz urządzenie z listy urządzeń.
1. Wyświetl dane telemetryczne, gdy urządzenie wysyła komunikaty do chmury na karcie **Przegląd** .

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-telemetry.png" alt-text="Wyświetl dane telemetryczne urządzeń w IoT Central":::

    > [!NOTE]
    > Możesz również monitorować dane telemetryczne z urządzenia za pomocą aplikacji Termite.

## <a name="call-a-direct-method-on-the-device"></a>Wywoływanie metody bezpośredniej na urządzeniu

Można również użyć IoT Central, aby wywołać metodę bezpośrednią zaimplementowaną na urządzeniu. Metody bezpośrednie mają nazwę i opcjonalnie mogą mieć ładunek JSON, konfigurowalne połączenie i przekroczenie limitu czasu metody. W tej sekcji wywoływana jest metoda, która umożliwia włączenie lub wyłączenie diody LED.

Aby wywołać metodę w portalu IoT Central:

1. Wybierz kartę **polecenie** na stronie urządzenie.
1. Wybierz pozycję **stan** i wybierz polecenie **Uruchom**.  Należy włączyć oświetlenie LED.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-invoke-method.png" alt-text="Wywoływanie metody bezpośredniej na urządzeniu":::

1. Usuń zaznaczenie **stanu** i wybierz polecenie **Uruchom**. Lampa LED powinna wyłączyć.

## <a name="view-device-information"></a>Wyświetl informacje o urządzeniu

Informacje o urządzeniu można wyświetlić z IoT Central.

Wybierz kartę **informacje** na stronie urządzenie.

:::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-about.png" alt-text="Wyświetl informacje o urządzeniu w IoT Central":::

## <a name="debugging"></a>Debugowanie

Aby debugować aplikację, zobacz [debugowanie za pomocą Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby platformy Azure utworzone w tym samouczku nie są już potrzebne, można je usunąć z portalu IoT Central. Opcjonalnie, jeśli w tym przewodniku Wprowadzenie nadal znajduje się kolejny samouczek, możesz zachować te zasoby, które zostały już utworzone, i użyć ich ponownie.

Aby usunąć całą przykładową aplikację platformy Azure IoT Central i wszystkie jej urządzenia i zasoby:
1. Wybierz pozycję **Administracja**  >  **Twoja aplikacja**.
1. Wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono obraz niestandardowy, który zawiera przykładowy kod usługi Azure RTO, a następnie obraz został opracowany na urządzeniu zestawu DEWELOPERSKIEGO DevKit. Portal IoT Central jest również używany do tworzenia zasobów platformy Azure, bezpiecznego łączenia zestawu DEWELOPERSKIEGO DevKit z platformą Azure, wyświetlania danych telemetrycznych i wysyłania komunikatów.

* W przypadku deweloperów urządzeń sugerowany następny krok to zapoznaj się z innymi samouczkami z serii [rozpoczynających się od projektowania urządzeń z usługą Azure IoT Embedded](quickstart-device-development.md).
* Jeśli masz problemy z zainicjowaniem lub nawiązaniem połączenia z urządzeniem po wykonaniu kroków opisanych w tym przewodniku, zobacz [Rozwiązywanie problemów](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md).
* Aby dowiedzieć się więcej o tym, jak składniki usługi Azure RTO są używane w przykładowym kodzie dla tego samouczka, zobacz [Korzystanie z usługi Azure RTO w przewodniku wprowadzenie](https://github.com/azure-rtos/getting-started/blob/master/docs/using-azure-rtos.md).

    > [!IMPORTANT]
    > Usługa Azure RTO udostępnia producentom OEM składniki służące do zabezpieczania komunikacji i tworzenia izolacji kodu i danych przy użyciu podstawowych mechanizmów ochrony sprzętu MIKROKONTROLERY/MPU. Jednak każdy producent OEM jest ostatecznie odpowiedzialny za upewnienie się, że urządzenie spełnia rozwój wymagań w zakresie bezpieczeństwa.

