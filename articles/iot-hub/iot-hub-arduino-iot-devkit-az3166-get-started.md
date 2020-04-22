---
title: Łączenie zestawu IoT DevKit AZ3166 z centrum Usługi Azure IoT Hub
description: W tym samouczku dowiesz się, jak skonfigurować i połączyć IoT DevKit AZ3166 z usługą Azure IoT Hub, aby mógł wysyłać dane do platformy Azure w chmurze.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.custom: mqtt
ms.openlocfilehash: 1c6af4f44cd16405d1ef12ae909085220b0d072d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733593"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Łączenie zestawu IoT DevKit AZ3166 z centrum Usługi Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Za pomocą [narzędzia MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) można tworzyć i prototypować rozwiązania Internetu rzeczy (IoT), które korzystają z usług platformy Microsoft Azure. Zawiera płytę kompatybilną z Arduino z bogatymi urządzeniami peryferyjnymi i czujnikami, pakiet płyt typu open source oraz bogatą [galerię próbek.](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć centrum IoT i zarejestrować urządzenie dla MXChip IoT DevKit.
* Jak podłączyć IoT DevKit do sieci Wi-Fi i skonfigurować parametry połączenia usługi IoT Hub.
* Jak wysłać dane telemetryczne czujnika DevKit do centrum IoT hub.
* Jak przygotować środowisko programistyczne i opracować aplikację dla IoT DevKit.

Nie masz jeszcze DevKit? Wypróbuj [symulator DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) lub [kup DevKit](https://aka.ms/iot-devkit-purchase).

Kod źródłowy wszystkich samouczków DevKit można znaleźć w [galerii przykładów kodu](https://docs.microsoft.com/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>Co jest potrzebne

* Płyta MXChip IoT DevKit z kablem Micro-USB. [Pobierz go teraz](https://aka.ms/iot-devkit-purchase).
* Komputer z systemem Windows 10, macOS 10.10+ lub Ubuntu 18.04+.
* Aktywna subskrypcja platformy Azure. [Aktywuj bezpłatne 30-dniowe konto testowe platformy Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
## <a name="prepare-your-hardware"></a>Przygotowanie sprzętu

Podłącz do komputera następujący sprzęt:

* Płytka DevKit
* Kabel micro-USB

![Wymagany sprzęt](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Aby połączyć devkit z komputerem, wykonaj następujące czynności:

1. Podłącz koniec USB do komputera.

2. Podłącz końcówka Micro-USB do devkitu.

3. Zielona dioda LED do zasilania potwierdza połączenie.

   ![Połączenia sprzętowe](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Szybki start: wysyłanie danych telemetrycznych z zestawu DevKit do centrum IoT Hub

Szybki start używa wstępnie skompilowanego oprogramowania układowego DevKit do wysyłania danych telemetrycznych do centrum IoT Hub. Przed uruchomieniem utworzysz centrum IoT i zarejestruj urządzenie w centrum.

### <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w usłudze Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyNodeDevice**: Nazwa zarejestrowanego urządzenia. Użyj elementu **MyNodeDevice** w pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Jeśli zostanie wyświetlony `device-identity`błąd podczas działania, zainstaluj [rozszerzenie IoT usługi Azure dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md).
   > Uruchom następujące polecenie, aby dodać rozszerzenie IoT usługi Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia usługi Cloud Shell. Rozszerzenie IoT dodaje polecenia, które są specyficzne dla Usługi IoT Hub, usługi IoT Edge i usługi inicjowania obsługi administracyjnej urządzeń IoT (DPS) do interfejsu wiersza polecenia platformy Azure.
   > 
   > ```azurecli-interactive
   > az extension add --name azure-iot
   >  ```
   >
  
1. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

### <a name="send-devkit-telemetry"></a>Wysyłanie danych telemetrycznych programu DevKit

DevKit łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT hub i wysyła dane telemetryczne temperatury i wilgotności.

1. Pobierz najnowszą wersję [oprogramowania układowego GetStarted](https://aka.ms/devkit/prod/getstarted/latest) dla IoT DevKit.

1. Upewnij się, że IoT DevKit łączy się z komputerem za pośrednictwem portu USB. Otwórz Eksplorator plików jest urządzenie pamięci masowej USB o nazwie **AZ3166**.

    ![Otwórz Eksploratora Windows](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Przeciągnij i upuść oprogramowanie układowe, które właśnie pobrano do urządzenia pamięci masowej i będzie migać automatycznie.

    ![Kopiowanie oprogramowania układowego](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. Na DevKit przytrzymaj przycisk **B**, naciśnij i zwolnij przycisk **Reset,** a następnie zwolnij przycisk **B**. DevKit wchodzi w tryb AP. Aby potwierdzić, na ekranie jest wyświetlany identyfikator zestawu usług (SSID) devkit i adres IP portalu konfiguracji.

    ![Przycisk Resetuj, przycisk B i SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Ustawianie trybu AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Użyj przeglądarki sieci Web na innym urządzeniu z obsługą sieci Wi-Fi (komputerze lub telefonie komórkowym), aby połączyć się z identyfikatorem SSID IoT DevKit wyświetlanym w poprzednim kroku. Jeśli poprosi o hasło, pozostaw go pustego.

    ![Łączenie SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Otwórz **192.168.0.1** w przeglądarce. Wybierz wi-fi, z którą chcesz się połączyć ioT DevKit, wpisz hasło Wi-Fi, a następnie wklej ostatnio zanotuj ciąg połączenia urządzenia. Następnie kliknij przycisk Save (Zapisz).

    ![Interfejs użytkownika konfiguracji](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit obsługuje tylko sieć 2,4 GHz. Sprawdź [często zadawane pytania, aby](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) uzyskać więcej informacji.

1. Informacje w sieci Wi-Fi i parametry połączenia urządzenia będą przechowywane w zestawie IoT DevKit po wyświetleniu strony wyników.

    ![Wynik konfiguracji](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Po skonfigurowaniu sieci Wi-Fi poświadczenia będą zachowywane na urządzeniu dla tego połączenia, nawet jeśli urządzenie jest odłączone.

1. IoT DevKit uruchamia się ponownie w ciągu kilku sekund. Na ekranie DevKit zobaczysz adres IP devkit następuje przez dane telemetryczne, w tym wartość temperatury i wilgotności z liczby wiadomości wysłać do usługi Azure IoT Hub.

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Wysyłanie danych](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Aby zweryfikować dane telemetryczne wysyłane do platformy Azure, uruchom następujące polecenie w usłudze Azure Cloud Shell:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

Wykonaj następujące kroki, aby przygotować środowisko programistyczne dla DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Instalowanie kodu programu Visual Studio za pomocą pakietu rozszerzeń narzędzi IoT usługi Azure

1. Zainstaluj [Arduino IDE](https://www.arduino.cc/en/Main/Software). Zapewnia niezbędny toolchain do kompilowania i przesyłania kodu Arduino.
    * **Windows**: Użyj wersji Instalatora Windows. Nie należy instalować ze sklepu App Store.
    * **macOS**: Przeciągnij i upuść wyodrębnione `/Applications` **Arduino.app do folderu.**
    * **Ubuntu**: Rozpażyć go do folderu, takich jak`$HOME/Downloads/arduino-1.8.8`

2. Zainstaluj [Visual Studio Code](https://code.visualstudio.com/), edytor kodu źródłowego między platformami z potężnym intellisense, uzupełnianie kodu i debugowania wsparcia, jak również rozbudowa rozszerzenia mogą być instalowane z marketplace.

3. Uruchom VS Code, **poszukaj Arduino** na rynku rozszerzeń i zainstaluj go. To rozszerzenie zapewnia ulepszone środowisko do tworzenia na platformie Arduino.

    ![Zainstaluj Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Poszukaj [narzędzi IoT platformy Azure](https://aka.ms/azure-iot-tools) w portalu windowseksei i zainstaluj je.

    ![Instalowanie narzędzi Usługi Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Możesz też skopiować i wkleić ten adres URL do okna przeglądarki:`vscode:extension/vsciot-vscode.azure-iot-tools`

    > [!NOTE]
    > Pakiet rozszerzeń narzędzia Azure IoT tools zawiera pakiet [roboczy urządzeń Usługi Azure IoT,](https://aka.ms/iot-workbench) który jest używany do tworzenia i debugowania na różnych urządzeniach devkit IoT. [Rozszerzenie usługi Azure IoT Hub](https://aka.ms/iot-toolkit), również dołączone do pakietu rozszerzeń narzędzi Azure IoT Tools, służy do zarządzania usługą Azure IoT Hubs i interakcji z nimi.

5. Skonfiguruj kod VS z ustawieniami Arduino.

    W programie Visual Studio Code kliknij pozycję **Preferencje > plików > ustawienia** (w systemie macOS, **preferencje > kodu > ustawienia ).** Następnie kliknij ikonę **Otwórz ustawienia (JSON)** w prawym górnym rogu strony *Ustawienia.*

    ![Instalowanie narzędzi Usługi Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Dodaj następujące wiersze, aby skonfigurować Arduino w zależności od platformy: 

    * **Okna**:

        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:

        Zastąp symbol zastępczy **{username}** poniżej na swoją nazwę użytkownika.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Kliknij, `F1` aby otworzyć paletę poleceń, wpisz i wybierz **Arduino: Board Manager**. Wyszukaj **AZ3166** i zainstaluj najnowszą wersję.

    ![Instalowanie zestawu SDK devkit](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Instalowanie sterowników ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) to interfejs USB używany przez IoT DevKit do komunikowania się z komputerem deweloperskim. Musisz zainstalować go w systemie Windows, aby flashować skompilowany kod urządzenia do DevKit. Wykonaj kroki specyficzne dla systemu operacyjnego, aby umożliwić urządzeniu dostęp do urządzenia.

* **Windows**: Pobierz i zainstaluj sterownik USB ze [strony STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: Dla systemu macOS nie jest wymagany żaden sterownik.
* **Ubuntu**: Uruchom polecenia w terminalu i wyloguj się i zaloguj, aby zmiana grupy została wprowadzona w życie:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Teraz wszystko jest ustawione z przygotowaniem i konfigurowaniem środowiska programistycznego. Pozwól nam zbudować próbkę GetStarted, którą właśnie uruchomiono.

## <a name="build-your-first-project"></a>Zbuduj swój pierwszy projekt

### <a name="open-sample-code-from-sample-gallery"></a>Otwórz przykładowy kod z przykładowej galerii

IoT DevKit zawiera bogatą galerię przykładów, których można użyć, aby dowiedzieć się połączyć DevKit z różnymi usługami platformy Azure.

1. Upewnij się, że zestaw IoT DevKit nie jest **podłączony** do komputera. Najpierw uruchom program VS Code, a następnie podłącz devkit do komputera.

1. Kliknij, `F1` aby otworzyć paletę poleceń, wpisz i wybierz pozycję Azure **IoT Device Workbench: Open Examples...**. Następnie wybierz **IoT DevKit** jako tablicę.

1. Na stronie Przykłady pracochłonneU IoT znajdź pozycję **Wprowadzenie** i kliknij pozycję **Otwórz przykład**. Następnie wybiera domyślną ścieżkę, aby pobrać przykładowy kod.

    ![Otwórz próbkę](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Aprowizuj usługę Azure IoT Hub i urządzenie

Zamiast inicjowania obsługi administracyjnej usługi Azure IoT Hub i urządzenia z witryny Azure portal, można to zrobić w programie VS Code bez opuszczania środowiska deweloperskiego.

1. W nowym otwartym oknie `F1` projektu kliknij, aby otworzyć paletę poleceń, wpisz i wybierz pozycję **Azure IoT Device Workbench: Provision Azure Services...**. Postępuj zgodnie z przewodnikiem krok po kroku, aby zakończyć inicjowanie obsługi administracyjnej usługi Azure IoT Hub i tworzenie urządzenia Usługi IoT Hub.

    ![Udostępnij polecenie](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Jeśli nie zalogowano się na platformie Azure. Postępuj zgodnie z wyskakującym powiadomieniem o zalogowaniu się.

1. wybierz subskrypcję, której chcesz użyć.

    ![Wybierz opcję podrzędną](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Następnie zaznacz lub utwórz nową [grupę zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).

    ![Wybieranie grupy zasobów](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. W określonej grupie zasobów postępuj zgodnie z przewodnikiem, aby wybrać lub utworzyć nową usługę Azure IoT Hub.

    ![Wybieranie kroków Centrum IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Wybierz Centrum IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Wybrane Centrum IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. W oknie danych wyjściowych zostanie wyświetlony usługi Azure IoT Hub aprowizowana.

    ![Aprowerowe centrum IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Wybierz lub utwórz nowe urządzenie w usłudze Azure IoT Hub, które zostały zainicjowane.

    ![Wybieranie kroków urządzenia IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Wybieranie aprowizowanego urządzenia IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Teraz masz usługi Azure IoT Hub aprowizowana i urządzenie utworzone w nim. Również parametry połączenia urządzenia zostaną zapisane w programie VS Code do późniejszego skonfigurowania zestawu IoT DevKit.

    ![Przepis wykonany](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Konfigurowanie i kompilowanie kodu urządzenia

1. Na pasku stanu w prawym dolnym rogu sprawdź, czy **wskaźnik MXCHIP AZ3166** jest wyświetlany jako wybrana płyta i używany jest port szeregowy z **stmicroelectronics.**

    ![Wybierz tablicę i COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Kliknij, `F1` aby otworzyć paletę poleceń, wpisz i wybierz pozycję **Azure IoT Device Workbench: Configure Device Settings...**, a następnie wybierz polecenie **Konfiguruj parametry połączenia urządzenia > Wybierz ciąg połączenia urządzenia usługi IoT Hub**.

1. Na DevKit przytrzymaj **przycisk A**, naciśnij i zwolnij przycisk **resetowania,** a następnie zwolnij **przycisk A**. DevKit wchodzi w tryb konfiguracji i zapisuje parametry połączenia.

    ![Parametry połączenia](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Kliknij `F1` ponownie, wpisz i wybierz pozycję **Azure IoT Device Workbench: Przekaż kod urządzenia**. Uruchamia kompilacji i przekazać kod do DevKit.

    ![Arduino przesłać](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit uruchamia się ponownie i uruchamia kod.

> [!NOTE]
> Jeśli wystąpią jakiekolwiek błędy lub przerwy, zawsze można odzyskać, uruchamiając polecenie ponownie.

## <a name="test-the-project"></a>Testowanie projektu

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Wyświetlanie danych telemetrycznych wysyłanych do usługi Azure IoT Hub

Kliknij ikonę wtyczki zasilania na pasku stanu, aby otworzyć monitor szeregowy:

![Monitor szeregowy](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Przykładowa aplikacja działa pomyślnie po wyświetleniu następujących wyników:

* Monitor szeregowy wyświetla komunikat wysłany do centrum IoT Hub.
* Dioda LED na MXChip IoT DevKit miga.

![Wyjście monitora szeregowego](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> Podczas testowania może wystąpić błąd, w którym dioda LED nie miga, portal Azure nie wyświetla danych przychodzących z urządzenia, ale ekran OLED urządzenia jest wyświetlany jako **uruchomiony...**. Aby rozwiązać ten problem, w witrynie Azure portal przejdź do urządzenia w centrum IoT i wyślij wiadomość do urządzenia. Jeśli widzisz następującą odpowiedź na monitorze szeregowym w programie VS Code, możliwe, że bezpośrednia komunikacja z urządzenia jest zablokowana na poziomie routera. Sprawdź reguły zapory i routera skonfigurowane dla urządzeń łączących. Upewnij się również, że port wychodzący 1833 jest otwarty.
> 
> BŁĄD: mqtt_client.c (ln 454): Błąd: błąd otwierania połączenia z punktem końcowym  
> INFO: >>>Stan połączenia: odłączony  
> BŁĄD: tlsio_mbedtls.c (ln 604): Podstawowe otwarcie we/wy nie powiodło się  
> BŁĄD: mqtt_client.c (ln 1042): Błąd: io_open nie powiodło się  
> BŁĄD: iothubtransport_mqtt_common.c (ln 2283): awaria połączenia z atcsliothub.azure-devices.net adres.  
> INFO: >>>Ponownie połączyć.  
> INFO: Wersja IoThub: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Wyświetlanie danych telemetrycznych odebranych przez usługę Azure IoT Hub

[Narzędzia IoT usługi Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) można używać do monitorowania komunikatów między urządzeniami do chmury (D2C) w centrum IoT Hub.

1. Zaloguj się w [witrynie Azure portal](https://portal.azure.com/), znajdź utworzone centrum IoT Hub.

    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. W okienku **Zasady dostępu udostępnionego** kliknij **zasady iothubowner**i zapisz ciąg połączenia centrum IoT Hub.

    ![Parametry połączenia usługi Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. W programie VS `F1`Code kliknij pozycję , wpisz i wybierz pozycję **Centrum Usługi Azure IoT: Ustaw ciąg połączenia usługi IoT Hub**. Skopiuj do niego ciąg połączenia.

    ![Ustawianie ciągu połączenia usługi Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Rozwiń okienko **URZĄDZENIA USŁUGI AZURE IOT HUB** po prawej stronie, kliknij prawym przyciskiem myszy nazwę utworzonego urządzenia i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

    ![Monitoruj komunikat D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. W okienku **WYJŚCIE** można wyświetlić przychodzące komunikaty D2C do centrum IoT Hub.

    ![Komunikat D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Przeglądanie kodu

Jest `GetStarted.ino` to główny plik szkicu Arduino.

![Komunikat D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Aby zobaczyć, jak dane telemetryczne urządzenia są wysyłane `utility.cpp` do usługi Azure IoT Hub, otwórz plik w tym samym folderze. Wyświetl [odwołanie do interfejsu API,](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) aby dowiedzieć się, jak używać czujników i urządzeń peryferyjnych w IoT DevKit.

Używany `DevKitMQTTClient` jest otoką **iothub_client** z [zestawów SDK IoT platformy Microsoft Azure i bibliotek dla języka C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) do interakcji z usługą Azure IoT Hub.

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli napotkasz problemy, możesz sprawdzić rozwiązanie w często zadawanych [pytaniach dotyczących IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktować się z nami z [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Możesz również przekazać nam swoją opinię, zostawiając komentarz na tej stronie.

## <a name="next-steps"></a>Następne kroki

Pomyślnie połączono zestaw MXChip IoT DevKit z centrum IoT hub i zostały wysłane przechwycone dane czujnika do centrum IoT hub.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
