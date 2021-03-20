---
title: Połącz Raspberry Pi z platformą Azure IoT Hub w chmurze (Node.js)
description: Dowiedz się, jak skonfigurować i połączyć Raspberry Pi z platformą IoT Hub Azure w celu wysyłania danych do platformy Azure w chmurze w tym samouczku.
author: wesmc7777
manager: eliotgra
keywords: usługa Azure IoT Raspberry Pi, Raspberry Pi IoT Hub, Raspberry Pi wysyła dane do chmury, Raspberry Pi do chmury
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
- devx-track-js
ms.openlocfilehash: c96f674b64401250d45542d0f59f13654cf37caa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97802528"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Połącz Raspberry Pi z platformą Azure IoT Hub (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku przedstawiono podstawowe informacje dotyczące pracy z Raspberry Pi, na którym uruchomiono raspbian. Następnie dowiesz się, jak bezproblemowo połączyć swoje urządzenia z chmurą przy użyciu [usługi Azure IoT Hub](about-iot-hub.md). W przypadku przykładów systemu Windows 10 IoT Core przejdź do [Centrum deweloperów systemu Windows](https://www.windowsondevices.com/).

Nie masz jeszcze zestawu? Wypróbuj [symulator online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Lub Kup [Nowy zestaw.](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>Co robisz

* Utwórz centrum IoT.

* Zarejestruj urządzenie dla platformy Pi w centrum IoT.

* Skonfiguruj Raspberry Pi.

* Uruchom przykładową aplikację w języku Pi, aby wysłać dane czujnika do centrum IoT Hub.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć usługę Azure IoT Hub i uzyskać nowe parametry połączenia z urządzeniem.

* Jak połączyć pi z czujnikiem BME280.

* Jak zbierać dane czujników przez uruchomienie przykładowej aplikacji w pi.

* Jak wysyłać dane czujnika do centrum IoT Hub.

## <a name="what-you-need"></a>Potrzebne elementy

![Potrzebne elementy](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Tablica Raspberry Pi 2 lub Raspberry Pi 3.

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Monitor, klawiatura USB i mysz, która łączy się z pi.

* Komputer Mac lub na komputerze z systemem Windows lub Linux.

* Połączenie internetowe.

* Karta microSD (16 GB lub nowsza).

* Karta USB-SD lub karta microSD do nagrania obrazu systemu operacyjnego na karcie microSD.

* Zasilacz z 5-wolt 2-amp z 6-nożnym kablem USB.

Następujące elementy są opcjonalne:

* Montowany Adafruit BME280, ciśnienie i czujnik wilgotności.

* Breadboard.

* 6 przewodów z zworką F/M.

* Rozproszone 10-mm dioda LED.

> [!NOTE]
> Jeśli nie masz elementów opcjonalnych, możesz użyć symulowanych danych czujników.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Konfigurowanie Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Zainstaluj system operacyjny raspbian dla liczby pi

Przygotuj kartę microSD na potrzeby instalacji obrazu raspbian.

1. Pobierz raspbian.

   a. [Raspbian Buster z pulpitem](https://www.raspberrypi.org/software/) (plik. zip).

   b. Wyodrębnij obraz raspbian do folderu na komputerze.

2. Zainstaluj raspbian na karcie microSD.

   a. [Pobierz i zainstaluj narzędzie do nagrywania kart SD](https://etcher.io/).

   b. Uruchom wytrawioner i wybierz obraz raspbian wyodrębniony w kroku 1.

   c. Wybierz dysk karty microSD. Program wytrawiony mógł już wybrać prawidłowy dysk.

   d. Kliknij przycisk Flash, aby zainstalować raspbian na karcie microSD.

   e. Po zakończeniu instalacji Usuń kartę microSD z komputera. Można bezpiecznie usunąć kartę microSD bezpośrednio, ponieważ program wytrawiony automatycznie wysunie lub odinstalował kartę microSD po zakończeniu.

   f. Wstaw kartę microSD do liczby pi.

### <a name="enable-ssh-and-i2c"></a>Włączanie protokołów SSH i I2C

1. Połącz pi z monitorem, klawiaturą i myszą.

2. Rozpocznij Pi, a następnie zaloguj się do raspbian przy użyciu `pi` nazwy użytkownika i `raspberry` hasła.

3. Kliknij ikonę Raspberry > **Preferencje**  >  **Raspberry Pi Configuration**.

   ![Menu preferencji raspbian](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. Na karcie **interfejsy** ustaw opcję **I2C** i **SSH** , aby **włączyć**, a następnie kliknij przycisk **OK**. Jeśli nie masz czujników fizycznych i chcesz użyć symulowanych danych czujników, ten krok jest opcjonalny.

   ![Włączanie I2C i SSH na Raspberry Pi](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> Aby włączyć protokół SSH i I2C, można znaleźć więcej dokumentów referencyjnych na [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) i [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Połącz czujnik z pi

Za pomocą przewodów breadboard i zworki Połącz diodę LED i BME280 z PI w następujący sposób. Jeśli nie masz czujnika, [Pomiń tę sekcję](#connect-pi-to-the-network).

![Połączenie Raspberry Pi i czujnika](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

Czujnik BME280 może zbierać dane dotyczące temperatury i wilgotności. Dioda LED miga, gdy urządzenie wysyła komunikat do chmury.

W przypadku numerów PIN czujnika Użyj następujących okablowania:

| Rozpocznij (dioda LED & czujnik)     | End (tablica)            | Kolor kabla   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Przypnij 5G)             | 3.3 v PWR (PIN 1)       | Biały kabel   |
| GND (Przypnij 7G)             | GND (Przypnij 6)            | Kabel brązowy   |
| SDI (PIN 10G)            | I2C1 SDA (numer PIN 3)       | Czerwony kabel     |
| SCK (Przypnij 8G)             | I2C1 SCL (numer PIN 5)       | Pomarańczowy kabel  |
| Dioda LED VDD (PIN 18F)        | GPIO 24 (numer PIN 18)       | Biały kabel   |
| Dioda LED GND (PIN 17F)        | GND (numer PIN 20)           | Czarny kabel   |

Kliknij, aby wyświetlić [mapowania Raspberry Pi 2 & 3 kod PIN](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) dla odwołania.

Po pomyślnym nawiązaniu połączenia BME280 z Raspberry Pi powinna wyglądać tak jak poniżej.

![Połączono pi i BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Połącz pi z siecią

Włącz opcję pi przy użyciu kabla micro USB i zasilacza. Podłącz PI do sieci przewodowej przy użyciu kabla Ethernet lub postępuj zgodnie z [instrukcjami z Raspberry Pi Foundation](https://www.raspberrypi.org/documentation/configuration/wireless/) , aby połączyć pi z siecią bezprzewodową. Po pomyślnym nawiązaniu połączenia pi z siecią należy zanotować [adres IP liczby pi](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Połączono z siecią przewodową](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Upewnij się, że Pi jest podłączony do tej samej sieci, co komputer. Na przykład, jeśli komputer jest połączony z siecią bezprzewodową, podczas gdy Pi jest podłączony do sieci przewodowej, adres IP może nie być widoczny w danych wyjściowych devdisco.

## <a name="run-a-sample-application-on-pi"></a>Uruchamianie przykładowej aplikacji na pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Klonowanie przykładowej aplikacji i instalowanie wstępnie wymaganych pakietów

1. Połącz się z Raspberry Pi z jednym z następujących klientów SSH z komputera hosta:

   **Użytkownicy systemu Windows**

   a. Pobierz [i zainstaluj system](https://www.putty.org/) Windows.

   b. Skopiuj adres IP liczby pi do sekcji Nazwa hosta (lub adres IP) i wybierz pozycję SSH jako typ połączenia.

   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Użytkownicy komputerów Mac i Ubuntu**

   Użyj wbudowanego klienta SSH w systemie Ubuntu lub macOS. Może być konieczne uruchomienie `ssh pi@<ip address of pi>` programu w celu nawiązania połączenia pi za pośrednictwem protokołu SSH.

   > [!NOTE]
   > Domyślna nazwa użytkownika to `pi` , a hasło to `raspberry` .

2. Zainstaluj Node.js i NPM do liczby pi.

   Najpierw sprawdź wersję Node.js.

   ```bash
   node -v
   ```

   Jeśli wersja jest starsza niż 10. x lub jeśli na pi nie ma Node.js, zainstaluj najnowszą wersję.

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Sklonuj przykładową aplikację.

   ```bash
   git clone https://github.com/Azure-Samples/azure-iot-samples-node.git
   ```

4. Zainstaluj wszystkie pakiety dla przykładu. Instalacja obejmuje zestaw SDK urządzeń Azure IoT, bibliotekę czujników BME280 oraz bibliotekę pi.

   ```bash
   cd azure-iot-samples-node/iot-hub/Tutorials/RaspberryPiApp
   npm install
   ```

   > [!NOTE]
   >Ukończenie tego procesu instalacji może potrwać kilka minut w zależności od połączenia sieciowego.

### <a name="configure-the-sample-application"></a>Konfigurowanie aplikacji przykładowej

1. Otwórz plik konfiguracyjny, uruchamiając następujące polecenia:

   ```bash
   nano config.json
   ```

   ![Plik konfiguracji](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   W tym pliku znajdują się dwa elementy, które można skonfigurować. Pierwszy z nich to `interval` , który definiuje przedział czasu (w milisekundach) między komunikatami wysyłanymi do chmury. Druga z nich to `simulatedData` , czyli wartość logiczna określająca, czy użyć symulowanych danych czujników, czy nie.

   Jeśli **nie masz czujnika**, ustaw wartość tak, `simulatedData` Aby `true` aplikacja Przykładowa utworzyła i używała symulowanych danych czujników.

   *Uwaga: adres I2C używany w tym samouczku jest domyślnie 0x77. W zależności od konfiguracji może być również 0x76: Jeśli wystąpi błąd I2C, spróbuj zmienić wartość na 118 i sprawdź, czy działa lepiej. Aby zobaczyć, jaki adres jest używany przez czujnik, uruchom polecenie `sudo i2cdetect -y 1` w powłoce na Raspberry Pi*

2. Zapisz i wyjdź, wpisując Control-O > Enter > Control-X.

### <a name="run-the-sample-application"></a>Uruchamianie przykładowej aplikacji

Uruchom przykładową aplikację, uruchamiając następujące polecenie:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Upewnij się, że kopiujesz, wklejając parametry połączenia urządzenia do pojedynczego cudzysłowu.

Powinny zostać wyświetlone następujące dane wyjściowe pokazujące dane czujnika i komunikaty, które są wysyłane do Twojego centrum IoT.

![Dane wyjściowe — dane czujnika wysyłane z urządzenia Raspberry Pi do centrum IoT](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Odczytywanie komunikatów odebranych przez centrum

Jednym ze sposobów monitorowania komunikatów odebranych przez Centrum IoT Hub z urządzenia jest użycie narzędzi Azure IoT Tools for Visual Studio Code. Aby dowiedzieć się więcej, zobacz [Korzystanie z narzędzi Azure IoT Tools for Visual Studio Code w celu wysyłania i odbierania komunikatów między urządzeniem i IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Aby uzyskać więcej sposobów przetwarzania danych wysyłanych przez urządzenie, przejdź do następnej sekcji.

## <a name="next-steps"></a>Następne kroki

Uruchomiono przykładową aplikację, która umożliwia zbieranie danych z czujnika i wysyłanie ich do centrum IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
