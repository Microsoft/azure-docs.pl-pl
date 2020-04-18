---
title: Łączenie Raspberry Pi z usługą Azure IoT Hub w chmurze (node.js)
description: Dowiedz się, jak skonfigurować i połączyć Raspberry Pi z usługą Azure IoT Hub dla Raspberry Pi, aby wysyłać dane do platformy chmurowej platformy Azure w tym samouczku.
author: wesmc7777
manager: eliotgra
keywords: azure iot raspberry pi, raspberry pi iot hub, raspberry pi wysyłają dane do chmury, raspberry pi do chmury
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: wesmc
ms.openlocfilehash: 3175956e35603cc4ad3a938f3d316c0af8f2d227
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640529"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Łączenie Raspberry Pi z usługą Azure IoT Hub (node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku możesz rozpocząć od nauki podstaw pracy z Raspberry Pi, który działa Raspbian. Następnie dowiesz się, jak bezproblemowo łączyć urządzenia z chmurą za pomocą [usługi Azure IoT Hub.](about-iot-hub.md) W przypadku przykładów rdzenia IoT systemu Windows 10 przejdź do [Centrum deweloperów systemu Windows](https://www.windowsondevices.com/).

Nie masz jeszcze zestawu? Wypróbuj [symulator online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Lub kupić nowy zestaw [tutaj](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Co robisz

* Utwórz centrum IoT.

* Zarejestruj urządzenie dla Pi w centrum IoT hub.

* Skonfiguruj Raspberry Pi.

* Uruchom przykładową aplikację na Pi, aby wysłać dane z czujnika do centrum IoT hub.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć centrum Usługi Azure IoT i uzyskać nowy ciąg połączenia urządzenia.

* Jak podłączyć Pi z czujnikiem BME280.

* Jak zbierać dane z czujników, uruchamiając przykładową aplikację na Pi.

* Jak wysyłać dane z czujników do centrum IoT Hub.

## <a name="what-you-need"></a>Co jest potrzebne

![Co jest potrzebne](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* Płyta Raspberry Pi 2 lub Raspberry Pi 3.

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

* Monitor, klawiatura USB i mysz, która łączy się z Pi.

* Komputer Mac lub PC z systemem Windows lub Linux.

* Połączenie z Internetem.

* Karta microSD o masie 16 GB lub wyższej.

* Adapter USB-SD lub karta microSD do nagrywania obrazu systemu operacyjnego na karcie microSD.

* 5-woltowy zasilacz 2-A z 6-stopowym kablem micro USB.

Następujące elementy są opcjonalne:

* Zmontowany czujnik temperatury, ciśnienia i wilgotności Adafruit BME280.

* Deska do kroju.

* 6 przewodów zworki F/M.

* Rozproszona dioda LED 10 mm.

> [!NOTE]
> Jeśli nie masz elementów opcjonalnych, możesz użyć symulowanych danych z czujników.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Konfigurowanie Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>Zainstaluj system operacyjny Raspbian dla Pi

Przygotuj kartę microSD do instalacji obrazu Raspbian.

1. Pobierz Raspbian.

   a. [Kraina Raspbian z pulpitem](https://www.raspberrypi.org/downloads/raspbian/) (plik .zip).

   b. Wyodrębnij obraz Raspbian do folderu na komputerze.

2. Zainstaluj Raspbian na karcie microSD.

   a. [Pobierz i zainstaluj narzędzie palnika kart Etcher SD](https://etcher.io/).

   b. Uruchom Etcher i wybierz obraz Raspbian, który został wyodrębniony w kroku 1.

   d. Wybierz dysk karty microSD. Etcher mógł już wybrać właściwy dysk.

   d. Kliknij przycisk Flash, aby zainstalować Raspbian na karcie microSD.

   e. Wyjmij kartę microSD z komputera po zakończeniu instalacji. Bezpieczne jest wyjęcie karty microSD bezpośrednio, ponieważ Etcher automatycznie wysuwa lub odinstalowuje kartę microSD po zakończeniu.

   f. Włóż kartę microSD do Pi.

### <a name="enable-ssh-and-i2c"></a>Włączanie SSH i I2C

1. Podłącz pi do monitora, klawiatury i myszy.

2. Uruchom Pi, a następnie zaloguj `pi` się do Raspbian, używając jako nazwy użytkownika i `raspberry` jako hasła.

3. Kliknij ikonę Raspberry > **preferencje** > **Raspberry Pi Konfiguracja**.

   ![Menu Preferencje Raspbian](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. Na karcie **Interfejsy** ustaw **I2C** i **SSH** na **Włącz**, a następnie kliknij przycisk **OK**. Jeśli nie masz fizycznych czujników i chcesz używać symulowanych danych z czujników, ten krok jest opcjonalny.

   ![Włącz I2C i SSH na Raspberry Pi](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> Aby włączyć SSH i I2C, można znaleźć więcej dokumentów referencyjnych na [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) i [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c).

### <a name="connect-the-sensor-to-pi"></a>Podłącz czujnik do Pi

Użyj deski do krojenia i przewodów zworkowych, aby podłączyć diodę LED i BME280 do Pi w następujący sposób. Jeśli nie masz czujnika, [pomiń tę sekcję](#connect-pi-to-the-network).

![Połączenie Raspberry Pi i czujnika](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

Czujnik BME280 może zbierać dane o temperaturze i wilgotności. Dioda LED miga, gdy urządzenie wysyła wiadomość do chmury.

W przypadku pinów czujnika należy użyć następującego okablowania:

| Start (czujnik & dioda LED)     | Koniec (tablica)            | Kolor kabla   |
| -----------------------  | ---------------------- | ------------: |
| VDD (pin 5G)             | 3,3 V PWR (pin 1)       | Biały kabel   |
| GND (pin 7G)             | GND (pin 6)            | Brązowy kabel   |
| SDI (pin 10G)            | I2C1 SDA (Pin 3)       | Czerwony kabel     |
| SCK (Pin 8G)             | I2C1 SCL (pin 5)       | Pomarańczowy kabel  |
| LED VDD (Pin 18F)        | GPIO 24 (pin 18)       | Biały kabel   |
| LED GND (Pin 17F)        | GND (pin 20)           | Czarny kabel   |

Kliknij, aby wyświetlić [Raspberry Pi 2 & 3 pin mappings](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) dla twojego odniesienia.

Po pomyślnym podłączeniu BME280 do Raspberry Pi powinno to przypominać obrazek poniżej.

![Podłączone Pi i BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Podłącz Pi do sieci

Włącz Pi za pomocą kabla micro USB i zasilacza. Użyj kabla Ethernet, aby podłączyć Pi do sieci przewodowej lub postępuj [zgodnie z instrukcjami fundacji Raspberry Pi,](https://www.raspberrypi.org/documentation/configuration/wireless/) aby podłączyć Pi do sieci bezprzewodowej. Po pomyślnym podłączeniu Pi do sieci należy zanotować [adres IP urządzenia Pi.](https://www.raspberrypi.org/documentation/remote-access/ip-address.md)

![Połączenie z siecią przewodową](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> Upewnij się, że Pi jest podłączony do tej samej sieci co komputer. Jeśli na przykład komputer jest podłączony do sieci bezprzewodowej, gdy Pi jest podłączony do sieci przewodowej, adres IP może nie być widoczny w danych wyjściowych devdisco.

## <a name="run-a-sample-application-on-pi"></a>Uruchamianie przykładowej aplikacji na pi

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>Klonuj przykładową aplikację i instaluj pakiety wstępne

1. Połącz się z Raspberry Pi za pomocą jednego z następujących klientów SSH z komputera-hosta:

   **Użytkownicy systemu Windows**

   a. Pobierz i zainstaluj [PuTTY](https://www.putty.org/) dla Windows.

   b. Skopiuj adres IP pi do sekcji Nazwa hosta (lub adres IP) i wybierz SSH jako typ połączenia.

   ![Putty](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Użytkownicy komputerów Mac i Ubuntu**

   Użyj wbudowanego klienta SSH na Ubuntu lub macOS. Może być konieczne `ssh pi@<ip address of pi>` uruchomienie, aby połączyć Pi przez SSH.

   > [!NOTE]
   > Domyślna nazwa `pi` użytkownika jest `raspberry`i hasło jest .

2. Zainstaluj Node.js i NPM na pi.

   Najpierw sprawdź wersję node.js.

   ```bash
   node -v
   ```

   Jeśli wersja jest niższa niż 10.x lub jeśli nie ma node.js na Pi, zainstaluj najnowszą wersję.

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. Sklonuj przykładową aplikację.

   ```bash
   git clone https://github.com/Azure-Samples/azure-iot-samples-node.git
   ```

4. Zainstaluj wszystkie pakiety dla próbki. Instalacja obejmuje zestaw SDK urządzenia Azure IoT, bibliotekę czujników BME280 i bibliotekę Wiring Pi.

   ```bash
   cd azure-iot-samples-node/iot-hub/Tutorials/RaspberryPiApp
   npm install
   ```

   > [!NOTE]
   >Zakończenie tego procesu instalacji może potrwać kilka minut w zależności od połączenia sieciowego.

### <a name="configure-the-sample-application"></a>Konfigurowanie aplikacji przykładowej

1. Otwórz plik konfiguracyjny, uruchamiając następujące polecenia:

   ```bash
   nano config.json
   ```

   ![Plik konfiguracji](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   W tym pliku znajdują się dwa elementy, które można skonfigurować. Pierwszym z `interval`nich jest , który definiuje przedział czasu (w milisekundach) między wiadomościami wysyłanymi do chmury. Drugi to `simulatedData`, który jest wartością logiczną dla tego, czy używać symulowanych danych z czujnika, czy nie.

   Jeśli **nie masz czujnika,** ustaw `simulatedData` wartość, aby `true` przykładowa aplikacja utworzyła i użyła symulowanych danych z czujnika.

   *Uwaga: Adres i2c używany w tym samouczku jest domyślnie 0x77. W zależności od konfiguracji może to być również 0x76: jeśli wystąpi błąd i2c, spróbuj zmienić wartość na 118 i sprawdzić, czy to działa lepiej. Aby zobaczyć, jaki adres jest `sudo i2cdetect -y 1` używany przez czujnik, uruchom w powłoce na raspberry pi*

2. Zapisz i wyjdź, wpisując > Control-O Enter > Control-X.

### <a name="run-the-sample-application"></a>Uruchamianie przykładowej aplikacji

Uruchom przykładową aplikację, uruchamiając następujące polecenie:

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > Upewnij się, że kopiujesz i wklejasz parametry połączenia urządzenia do pojedynczych cudzysłowów.

Powinny zostać wyświetlone następujące dane wyjściowe pokazujące dane czujnika i komunikaty, które są wysyłane do Twojego centrum IoT.

![Dane wyjściowe — dane czujnika wysyłane z urządzenia Raspberry Pi do centrum IoT](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Czytanie wiadomości odebranych przez centrum

Jednym ze sposobów monitorowania wiadomości odebranych przez centrum IoT hub z urządzenia jest użycie narzędzi Azure IoT Tools for Visual Studio Code. Aby dowiedzieć się więcej, zobacz [Wysyłanie i odbieranie wiadomości między urządzeniem a centrum IoT Hub za pomocą narzędzi Azure IoT Tools for Visual Studio Code.](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)

Aby uzyskać więcej sposobów przetwarzania danych wysyłanych przez urządzenie, przejdź do następnej sekcji.

## <a name="next-steps"></a>Następne kroki

Uruchomisz przykładową aplikację, aby zebrać dane z czujników i wysłać je do centrum IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
