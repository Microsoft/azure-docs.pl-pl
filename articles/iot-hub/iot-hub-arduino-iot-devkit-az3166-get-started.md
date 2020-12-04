---
title: Łączenie usługi IoT DevKit AZ3166 z usługą Azure IoT Hub
description: W tym samouczku dowiesz się, jak skonfigurować i połączyć usługę IoT DevKit AZ3166 z platformą Azure IoT Hub, aby mogła ona wysyłać dane do platformy chmury platformy Azure.
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.custom:
- mqtt
- 'Role: Cloud Development'
- devx-track-azurecli
ms.openlocfilehash: 6d5169c2424e89dd2fb77335dd2807e73e9b3a55
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572069"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Łączenie usługi IoT DevKit AZ3166 z platformą Azure IoT Hub

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Możesz użyć [zestawu deweloperskiego IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) do tworzenia i prototypowania rozwiązań Internet rzeczy (IoT), które wykorzystują usługi Microsoft Azure Services. Zawiera tablicę zgodną z Arduinoą z rozbudowanymi urządzeniami peryferyjnymi i czujników, pakietem tablicy typu open source i bogatyą [galerią przykładową](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/).

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć Centrum IoT i zarejestrować urządzenie na potrzeby usługi zestawu deweloperskiego IoT DevKit.
* Jak podłączyć DevKit IoT do Wi-Fi i skonfigurować IoT Hub parametry połączenia.
* Jak wysłać dane telemetryczne czujnika DevKit do centrum IoT Hub.
* Przygotowywanie środowiska deweloperskiego i opracowywanie aplikacji dla usługi IoT DevKit.

Nie masz jeszcze DevKit? Wypróbuj [symulator DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) lub [Kup DevKit](https://aka.ms/iot-devkit-purchase).

Kod źródłowy wszystkich samouczków DevKit można znaleźć w [galerii przykładów kodu](/samples/browse/?term=mxchip).

## <a name="what-you-need"></a>Potrzebne elementy

- Zestawu deweloperskiego IoT DevKit z kablem mikroUSB. [Pobierz teraz](https://aka.ms/iot-devkit-purchase).
- Komputer z systemem Windows 10, macOS 10.10 + lub Ubuntu 18.04 +.
- Aktywna subskrypcja platformy Azure. [Aktywuj bezpłatne 30-dniowe konto wersji próbnej Microsoft Azure](https://azureinfo.microsoft.com/us-freetrial.html).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
  
## <a name="prepare-your-hardware"></a>Przygotuj sprzęt

Podłącz do komputera następujący sprzęt:

* Tablica DevKit
* Kabel USB Micro

![Wymagany sprzęt](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Aby połączyć DevKit z komputerem, wykonaj następujące kroki:

1. Podłącz złącze USB do komputera.

2. Podłącz złącze mikroUSB do zestawu deweloperskiego.

3. Zielona dioda LED dla zasilacza potwierdza połączenie.

   ![Połączenia sprzętowe](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>Szybki Start: wysyłanie danych telemetrycznych z DevKit do IoT Hub

Przewodnik Szybki Start używa wstępnie skompilowanego oprogramowania układowego DevKit do wysłania telemetrii do IoT Hub. Przed uruchomieniem programu należy utworzyć Centrum IoT Hub i zarejestrować urządzenie w centrum.

### <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyNodeDevice**: nazwa urządzenia, które jest rejestrowany. Użyj elementu **MyNodeDevice** w pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > Jeśli wystąpi błąd `device-identity` , zainstaluj [rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md).
   > Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell. Rozszerzenie IoT dodaje polecenia, które są specyficzne dla IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS) do interfejsu wiersza polecenia platformy Azure.
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

    Zanotuj parametry połączenia urządzenia, które są następujące:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

### <a name="send-devkit-telemetry"></a>Wyślij telemetrię DevKit

DevKit nawiązuje połączenie z punktem końcowym specyficznym dla urządzenia w centrum IoT Hub i wysyła dane telemetryczne dotyczące temperatury i wilgotności.

1. Pobierz najnowszą wersję [Getstartowego oprogramowania układowego](https://aka.ms/devkit/prod/getstarted/latest) dla usługi IoT DevKit.

1. Upewnij się, że IoT DevKit nawiązuje połączenie z komputerem za pośrednictwem portu USB. Otwórz Eksploratora plików istnieje urządzenie pamięci masowej USB o nazwie **AZ3166**.

    ![Otwórz Eksploratora Windows](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. Przeciągnij i upuść oprogramowanie układowe pobrane bezpośrednio do urządzenia pamięci masowej, które zostanie automatycznie wybłyskowe.

    ![Kopiuj oprogramowanie układowe](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. Na DevKit, przytrzymaj przycisk **B**, wypchnij i zwolnij przycisk **Reset** , a następnie zwolnij przycisk **b**. DevKit przechodzi do trybu AP. Aby potwierdzić, na ekranie zostanie wyświetlony Identyfikator zestawu usług (SSID) DevKit i adres IP portalu konfiguracji.

    ![Przycisk Resetuj, przycisk B i identyfikator SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![Ustaw tryb AP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. Korzystając z przeglądarki internetowej na innym urządzeniu z obsługą sieci Wi-Fi (komputerze lub telefonie komórkowym), połącz się z identyfikatorem SSID zestawu deweloperskiego IoT wyświetlonym w poprzednim kroku. Jeśli zostanie wyświetlony monit o podanie hasła, pozostaw to pole puste.

    ![Połącz identyfikator SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. Otwórz **192.168.0.1** w przeglądarce. Wybierz Wi-Fi, z którym chcesz nawiązać połączenie IoT DevKit, wpisz Wi-Fi hasło, a następnie wklej wcześniej zanotowane parametry połączenia urządzenia. Następnie kliknij przycisk Save (Zapisz).

    ![Interfejs użytkownika konfiguracji](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > DevKit IoT obsługuje tylko sieć 2,4 GHz. Sprawdź [często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) , aby uzyskać więcej szczegółów.

1. Informacje o urządzeniach Wi-Fi i połączenia z urządzeniem będą przechowywane w DevKit IoT, gdy zostanie wyświetlona strona wyników.

    ![Wynik konfiguracji](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > Po skonfigurowaniu Wi-Fi poświadczenia będą przechowywane na urządzeniu dla tego połączenia, nawet jeśli urządzenie jest odłączone.

1. Zestaw deweloperski IoT zostanie ponownie uruchomiony w ciągu kilku sekund. Na ekranie DevKit adres IP dla DevKit jest wyświetlany przez dane telemetryczne, w tym wartości temperatury i wilgotności z liczbą komunikatów wysyłanych do platformy Azure IoT Hub.

    ![Adres IP sieci Wi-Fi](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![Wysyłanie danych](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. Aby sprawdzić dane telemetryczne wysyłane do platformy Azure, uruchom następujące polecenie w Azure Cloud Shell:

    ```azurecli
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

Wykonaj następujące kroki, aby przygotować środowisko programistyczne dla DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>Zainstaluj Visual Studio Code z pakietem rozszerzenia narzędzi Azure IoT Tools

1. Zainstaluj [środowisko IDE Arduino](https://www.arduino.cc/en/Main/Software). Zapewnia łańcucha narzędzi niezbędne do kompilowania i przekazywania kodu Arduino.
    * **Windows**: Użyj Instalator Windows wersji. Nie instaluj ze sklepu App Store.
    * **macOS**: Przeciągnij i upuść wyodrębnioną **aplikację Arduino. app** do `/Applications` folderu.
    * **Ubuntu**: rozpakuj go do folderu, takiego jak `$HOME/Downloads/arduino-1.8.8`

2. Zainstaluj [Visual Studio Code](https://code.visualstudio.com/), Edytor kodu źródłowego dla wielu platform z zaawansowaną technologią IntelliSense, uzupełniania kodu i obsługi debugowania, a także bogatymi rozszerzeniami można instalować z witryny Marketplace.

3. Uruchom VS Code, Wyszukaj pozycję **Arduino** w witrynie rozszerzenia Marketplace i zainstaluj ją. To rozszerzenie zapewnia ulepszone środowisko programowania na platformie Arduino.

    ![Zainstaluj Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Poszukaj [narzędzi Azure IoT Tools](https://aka.ms/azure-iot-tools) w witrynie rozszerzenia Marketplace i zainstaluj ją.

    ![Zrzut ekranu przedstawiający narzędzia Azure IoT Tools w witrynie rozszerzenia Marketplace.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    Lub skopiuj i wklej ten adres URL do okna przeglądarki: `vscode:extension/vsciot-vscode.azure-iot-tools`

    > [!NOTE]
    > Pakiet rozszerzeń narzędzi Azure IoT Tools zawiera [Workbench urządzenia Azure IoT](https://aka.ms/iot-workbench) , który służy do opracowywania i debugowania na różnych urządzeniach IoT Devkit. [Rozszerzenie Azure IoT Hub](https://aka.ms/iot-toolkit), dołączone także do pakietu rozszerzenia narzędzi Azure IoT Tools, służy do zarządzania centrami Azure IoT Hub i interakcji z nimi.

5. Skonfiguruj VS Code przy użyciu ustawień Arduino.

    W Visual Studio Code kliknij kolejno pozycje **plik > preferencje > ustawienia** (w MacOS, **kod > preferencje > ustawienia**). Następnie kliknij ikonę **Otwórz ustawienia (JSON)** w prawym górnym rogu strony *ustawień* .

    ![Instalowanie narzędzi Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)

    Dodaj następujące wiersze, aby skonfigurować platformę Arduino w zależności od używanej platformy: 

    * **Windows**:

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

        Zastąp symbol zastępczy **{username}** poniżej nazwą użytkownika.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Kliknij `F1` , aby otworzyć paletę poleceń, wpisz i wybierz **Arduino: Board Manager**. Wyszukaj **AZ3166** i zainstaluj najnowszą wersję.

    ![Zainstaluj zestaw DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Zainstaluj ST-Link sterowniki

[St-link/v2](https://www.st.com/en/development-tools/st-link-v2.html) to interfejs USB używany przez program IoT DevKit do komunikacji z maszyną deweloperskią. Należy go zainstalować w systemie Windows, aby flashować skompilowany kod urządzenia w zestawie deweloperskim. Postępuj zgodnie z procedurami specyficznymi dla systemu operacyjnego, aby umożliwić komputerowi dostęp do urządzenia.

* **System Windows**: pobieranie i Instalowanie sterownika USB z [witryny sieci Web STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: żaden sterownik nie jest wymagany dla macOS.
* **Ubuntu**: uruchom polecenia w terminalu, a następnie wyloguj się i zaloguj, aby zmiany grupy zaczęły obowiązywać:

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Teraz wszystko jest gotowe do przygotowania i skonfigurowania środowiska deweloperskiego. Pozwól nam skompilować właśnie uruchomiony przykład getstarted.

## <a name="build-your-first-project"></a>Tworzenie pierwszego projektu

### <a name="open-sample-code-from-sample-gallery"></a>Otwieranie przykładowego kodu z galerii przykładów

Usługa IoT DevKit zawiera obszerną galerię przykładów, których można użyć do nawiązania połączenia DevKit z różnymi usługami platformy Azure.

1. Upewnij się, że Twój DevKit IoT **nie jest podłączony** do komputera. Najpierw Rozpocznij VS Code, a następnie połącz DevKit z komputerem.

1. Kliknij `F1` , aby otworzyć paletę poleceń, wpisz i wybierz pozycję **Azure IoT Device Workbench: Otwórz przykłady...**. Następnie wybierz pozycję **IoT DevKit** jako tablicę.

1. Na stronie Przykłady IoT Workbench Znajdź pozycję **Rozpocznij pracę** i kliknij pozycję **Otwórz przykład**. Następnie wybiera domyślną ścieżkę do pobrania przykładowego kodu.

    ![Otwórz przykład](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Udostępnianie usługi Azure IoT Hub i urządzenia

Zamiast inicjowania obsługi IoT Hub i urządzeń z platformą Azure za pomocą Azure Portal można wykonać ją w VS Code bez opuszczania środowiska deweloperskiego.

1. W oknie Nowy otwarty projekt kliknij, `F1` Aby otworzyć paletę poleceń, wpisz i wybierz pozycję **Azure IoT Device Workbench: zainicjuj usługi platformy Azure.**... Postępuj zgodnie z przewodnikiem krok po kroku, aby zakończyć Inicjowanie obsługi administracyjnej IoT Hub platformy Azure i utworzyć urządzenie IoT Hub.

    ![Polecenie aprowizacji](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Jeśli nie zalogowano się na platformie Azure. Wykonaj powiadomienie wyskakujące, aby się zalogować.

1. wybierz subskrypcję, której chcesz użyć.

    ![Wybierz element podrzędny](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Następnie wybierz lub Utwórz nową [grupę zasobów](../azure-resource-manager/management/overview.md#terminology).

    ![Wybieranie grupy zasobów](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. W podanej grupie zasobów postępuj zgodnie z przewodnikiem, aby wybrać lub utworzyć nowy IoT Hub platformy Azure.

    ![Wybierz IoT Hub kroki](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Wybierz IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Wybrany IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. W oknie dane wyjściowe zostanie wyświetlony IoT Hub aprowizacji.

    ![IoT Hub aprowizacji](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Wybierz lub Utwórz nowe urządzenie na platformie Azure IoT Hub aprowizacji.

    ![Wybierz kroki urządzenia IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Wybieranie urządzenia IoT z obsługą administracyjną](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Teraz masz aprowizowaną usługę Azure IoT Hub i utworzone w niej urządzenie. Ponadto parametry połączenia urządzenia zostaną zapisane w VS Code w celu późniejszego skonfigurowania usługi IoT DevKit.

    ![Inicjowanie obsługi administracyjnej](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Konfigurowanie i kompilowanie kodu urządzenia

1. Na prawym dolnym pasku stanu Sprawdź, czy **zestawu DEWELOPERSKIEGO AZ3166** jest pokazywany jako wybrana tablica i port szeregowy z **STMicroelectronics** .

    ![Wybierz tablicę i model COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Kliknij `F1` , aby otworzyć paletę poleceń, wpisz i wybierz pozycję **Azure IoT Device Workbench: Konfigurowanie ustawień urządzenia...**, a następnie wybierz pozycję **konfiguracja parametry połączenia urządzenia > wybierz pozycję IoT Hub parametry połączenia urządzenia**.

1. W DevKit, naciśnij **przycisk a**, wypchnij i zwolnij przycisk **Reset** , a następnie zwolnij **przycisk a**. DevKit wprowadza tryb konfiguracji i zapisuje parametry połączenia.

    ![Parametry połączenia](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Kliknij przycisk `F1` ponownie, wpisz i wybierz pozycję **Azure IoT Device Workbench: Przekaż kod urządzenia**. Rozpocznie Kompilowanie i przekazywanie kodu do DevKit.

    ![Arduino przekazywanie](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

Zestaw deweloperski wykonuje ponowny rozruch i uruchamia kod.

> [!NOTE]
> Jeśli występują błędy lub przerwy, można je zawsze odzyskać, uruchamiając polecenie ponownie.

## <a name="test-the-project"></a>Testowanie projektu

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Wyświetl dane telemetryczne wysyłane do usługi Azure IoT Hub

Kliknij ikonę zasilania na pasku stanu, aby otworzyć Monitor szeregowy:

![Monitor seryjny](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

Aplikacja Przykładowa zostanie uruchomiona pomyślnie, gdy zobaczysz następujące wyniki:

* Monitor seryjny wyświetla komunikat wysłany do IoT Hub.
* Dioda LED zestawu deweloperskiego IoT DevKit jest migana.

![Dane wyjściowe monitora szeregowego](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

> [!NOTE]
> Wystąpił błąd podczas testowania, w którym dioda LED nie miga, Azure Portal nie pokazuje danych przychodzących z urządzenia, ale ekran OLED urządzenia jest wyświetlany jako **uruchomiony...**. Aby rozwiązać ten problem, w Azure Portal przejdź do urządzenia w centrum IoT Hub i Wyślij komunikat do urządzenia. Jeśli w VS Code zostanie wyświetlona następująca odpowiedź w monitorze seryjnym, istnieje możliwość, że komunikacja bezpośrednia z urządzenia zostanie zablokowana na poziomie routera. Sprawdź reguły zapory i routera skonfigurowane dla połączonych urządzeń. Upewnij się również, że port wychodzący 1833 jest otwarty.
> 
> Błąd: mqtt_client. c (LN 454): błąd: nie można otworzyć połączenia z punktem końcowym  
> INFORMACJE:  >>>stanu połączenia: Rozłączono  
> Błąd: tlsio_mbedtls. c (LN 604): nie można otworzyć podstawowej operacji we/wy  
> Błąd: mqtt_client. c (LN 1042): błąd: io_open nie powiodło się  
> Błąd: iothubtransport_mqtt_common. c (LN 2283): Wystąpił błąd podczas nawiązywania połączenia z adresem atcsliothub.azure-devices.net.  
> INFORMACJE:  >>>ponownie nawiązać połączenia.  
> INFO: IoThub wersja: 1.3.6  

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Wyświetlanie danych telemetrycznych odebranych przez usługę Azure IoT Hub

Za pomocą [narzędzi Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) można monitorować komunikaty z urządzenia do chmury (D2C) w IoT Hub.

1. Zaloguj się [Azure Portal](https://portal.azure.com/), Znajdź IoT Hub utworzony.

    ![Azure Portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. W okienku **zasady dostępu współdzielonego** kliknij **zasady iothubowner** i Zapisz parametry połączenia Centrum IoT.

    ![Parametry połączenia usługi Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. W VS Code kliknij pozycję `F1` , wpisz i wybierz pozycję **Azure IoT Hub: Ustaw IoT Hub parametry połączenia**. Skopiuj do niego parametry połączenia.

    ![Ustaw parametry połączenia usługi Azure IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Rozwiń okienko **urządzenia usługi Azure IoT Hub** po lewej stronie, kliknij prawym przyciskiem myszy utworzoną nazwę urządzenia i wybierz pozycję **Rozpocznij monitorowanie wbudowanego punktu końcowego zdarzenia**.

    ![Monitorowanie komunikatu D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. W okienku **danych wyjściowych** przychodzące komunikaty D2C są wyświetlane w IoT Hub.

    ![Zrzut ekranu pokazujący przychodzące komunikaty D2C do IoT Hub.](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Przeglądanie kodu

`GetStarted.ino`To główny plik konspektu Arduino.

![D2C komunikat](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Aby zobaczyć, jak dane telemetryczne urządzeń są wysyłane do IoT Hub platformy Azure, Otwórz `utility.cpp` plik w tym samym folderze. Wyświetl [Informacje o interfejsie API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) , aby dowiedzieć się, jak używać czujników i urządzeń peryferyjnych w usłudze IoT DevKit.

`DevKitMQTTClient`Jest to otoka **iothub_client** z [Microsoft Azure usługi IoT SDK i bibliotek dla języka C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) w celu współdziałania z usługą Azure IoT Hub.

## <a name="problems-and-feedback"></a>Problemy i opinie

Jeśli wystąpią problemy, możesz wyszukać rozwiązanie w usłudze [IoT DevKit — często zadawane pytania](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) lub skontaktować się z nami z [warunkom](https://gitter.im/Microsoft/azure-iot-developer-kit). Możesz również przekazać nam swoją opinię, pozostawiając komentarz na tej stronie.

## <a name="next-steps"></a>Następne kroki

Pomyślnie nawiązano połączenie z usługą zestawu deweloperskiego IoT DevKit do centrum IoT Hub, a dane przechwyconego czujnika zostały przesłane do centrum IoT Hub.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
