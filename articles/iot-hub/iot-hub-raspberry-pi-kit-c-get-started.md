---
title: Łączenie Raspberry Pi z usługą Azure IoT Hub przy użyciu języka C | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować i połączyć Raspberry Pi z usługą Azure IoT Hub dla Raspberry Pi, aby wysyłać dane do platformy chmurowej platformy Azure
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.openlocfilehash: 03f9d58cab725335b0f4090ac1a7289c32c0af7f
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640542"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Łączenie Raspberry Pi z usługą Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku możesz rozpocząć od nauki podstaw pracy z Raspberry Pi, który działa Raspbian. Następnie dowiesz się, jak bezproblemowo łączyć urządzenia z chmurą za pomocą [usługi Azure IoT Hub.](about-iot-hub.md) W przypadku przykładów rdzenia IoT systemu Windows 10 przejdź do [Centrum deweloperów systemu Windows](https://www.windowsondevices.com/).

Nie masz jeszcze zestawu? Wypróbuj [symulator online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Lub kupić nowy zestaw [tutaj](https://azure.microsoft.com/develop/iot/starter-kits).

## <a name="what-you-do"></a>Co robisz

* Utwórz centrum IoT.

* Zarejestruj urządzenie dla Pi w centrum IoT hub.

* Konfiguracja Raspberry Pi.

* Uruchom przykładową aplikację na Pi, aby wysłać dane z czujnika do centrum IoT hub.

Połącz Raspberry Pi z utworzonym centrum IoT Hub. Następnie uruchom przykładową aplikację na Pi, aby zebrać dane o temperaturze i wilgotności z czujnika BME280. Na koniec wysyłasz dane czujnika do centrum IoT Hub.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć centrum Usługi Azure IoT i uzyskać nowy ciąg połączenia urządzenia.

* Jak podłączyć Pi z czujnikiem BME280.

* Jak zbierać dane z czujników, uruchamiając przykładową aplikację na Pi.

* Jak wysyłać dane z czujników do centrum IoT Hub.

## <a name="what-you-need"></a>Co jest potrzebne

![Co jest potrzebne](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Płyta Raspberry Pi 2 lub Raspberry Pi 3.

* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [utwórz bezpłatne konto próbne platformy Azure](https://azure.microsoft.com/free/) w ciągu zaledwie kilku minut.

* Monitor, klawiatura USB i mysz łączące się z Pi.

* Komputer Mac lub komputer z systemem Windows lub Linux.

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
> Te elementy są opcjonalne, ponieważ przykładowy kod obsługuje symulowane dane z czujnika.
>

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Konfigurowanie Raspberry Pi

Teraz skonfiguruj Raspberry Pi.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Zainstaluj system operacyjny Raspbian dla Pi

Przygotuj kartę microSD do instalacji obrazu Raspbian.

1. Pobierz Raspbian.

   1. [Pobierz Raspbian Stretch z pulpitem](https://www.raspberrypi.org/downloads/raspbian/) (plik .zip).

   2. Wyodrębnij obraz Raspbian do folderu na komputerze.

2. Zainstaluj Raspbian na karcie microSD.

   1. [Pobierz i zainstaluj narzędzie palnika kart Etcher SD](https://etcher.io/).

   2. Uruchom Etcher i wybierz obraz Raspbian, który został wyodrębniony w kroku 1.

   3. Wybierz dysk karty microSD. Należy pamiętać, że Etcher mógł już wybrać właściwy dysk.

   4. Kliknij przycisk Flash, aby zainstalować Raspbian na karcie microSD.

   5. Wyjmij kartę microSD z komputera po zakończeniu instalacji. Bezpieczne jest wyjęcie karty microSD bezpośrednio, ponieważ Etcher automatycznie wysuwa lub odinstalowuje kartę microSD po zakończeniu.

   6. Włóż kartę microSD do Pi.

### <a name="enable-ssh-and-spi"></a>Włącz SSH i SPI

1. Podłącz Pi do monitora, klawiatury i myszy, uruchom Pi, `pi` a następnie `raspberry` zaloguj się do Raspbian, używając jako nazwy użytkownika i jako hasła.
 
2. Kliknij ikonę Raspberry > **preferencje** > **Raspberry Pi Konfiguracja**.

   ![Menu Preferencje Raspbian](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. Na karcie **Interfejsy** ustaw **spi** i **SSH,** aby **włączyć**, a następnie kliknij przycisk **OK**. Jeśli nie masz fizycznych czujników i chcesz używać symulowanych danych z czujników, ten krok jest opcjonalny.

   ![Włącz SPI i SSH na Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Aby włączyć SSH i SPI, można znaleźć więcej dokumentów referencyjnych na [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) i [RASPI-CONFIG](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Podłącz czujnik do Pi

Użyj deski do krojenia i przewodów zworkowych, aby podłączyć diodę LED i BME280 do Pi w następujący sposób. Jeśli nie masz czujnika, [pomiń tę sekcję](#connect-pi-to-the-network).

![Połączenie Raspberry Pi i czujnika](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

Czujnik BME280 może zbierać dane o temperaturze i wilgotności. A dioda LED będzie migać, jeśli istnieje komunikacja między urządzeniem a chmurą.

W przypadku pinów czujnika należy użyć następującego okablowania:

| Start (czujnik & dioda LED)     | Koniec (tablica)            | Kolor kabla   |
| -----------------------  | ---------------------- | ------------: |
| LED VDD (Pin 5G)         | GPIO 4 (Pin 7)         | Biały kabel   |
| LED GND (Pin 6G)         | GND (pin 6)            | Czarny kabel   |
| VDD (pin 18F)            | PWR 3,3 V (pin 17)      | Biały kabel   |
| GND (pin 20F)            | GND (pin 20)           | Czarny kabel   |
| SCK (Pin 21F)            | SPI0 SCLK (Pin 23)     | Pomarańczowy kabel  |
| SDO (Pin 22F)            | SPI0 MISO (Pin 21)     | Żółty kabel  |
| SDI (Pin 23F)            | SPI0 MOSI (Pin 19)     | Zielony kabel   |
| CS (Pin 24F)             | SPI0 CS (Pin 24)       | Niebieski kabel    |

Kliknij, aby wyświetlić [Raspberry Pi 2 & mapowania 3 Pin dla](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi) twojej pomocy.

Po pomyślnym podłączeniu BME280 do Raspberry Pi powinno to przypominać obrazek poniżej.

![Podłączone Pi i BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Podłącz Pi do sieci

Włącz Pi za pomocą kabla micro USB i zasilacza. Użyj kabla Ethernet, aby podłączyć Pi do sieci przewodowej lub postępuj [zgodnie z instrukcjami fundacji Raspberry Pi,](https://www.raspberrypi.org/documentation/configuration/wireless/) aby podłączyć Pi do sieci bezprzewodowej. Po pomyślnym podłączeniu Pi do sieci należy zanotować [adres IP urządzenia Pi.](https://www.raspberrypi.org/documentation/remote-access/ip-address.md)

![Połączenie z siecią przewodową](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Uruchamianie przykładowej aplikacji na pi

### <a name="sign-into-your-raspberry-pi"></a>Zaloguj się do Raspberry Pi

1. Użyj jednego z następujących klientów SSH z komputera-hosta, aby połączyć się z Raspberry Pi.
   
   **Użytkownicy systemu Windows**
   1. Pobierz i zainstaluj [PuTTY](https://www.putty.org/) dla Windows. 
   1. Skopiuj adres IP pi do sekcji Nazwa hosta (lub adres IP) i wybierz SSH jako typ połączenia.
   
   ![Putty](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Użytkownicy komputerów Mac i Ubuntu**

   Użyj wbudowanego klienta SSH na Ubuntu lub macOS. Może być konieczne `ssh pi@<ip address of pi>` uruchomienie, aby połączyć Pi przez SSH.
   > [!NOTE]
   > Domyślna nazwa `pi` użytkownika to `raspberry`, a hasło to .


### <a name="configure-the-sample-application"></a>Konfigurowanie aplikacji przykładowej

1. Sklonuj przykładową aplikację, uruchamiając następujące polecenie:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Uruchom skrypt konfiguracji:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Jeśli **nie masz fizycznego BME280,** możesz użyć parametru wiersza polecenia '--symulowane', aby symulować dane temperatury&wilgotności. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Tworzenie i uruchamianie przykładowej aplikacji

1. Skompiluj przykładową aplikację, uruchamiając następujące polecenie:

   ```bash
   cmake . && make
   ```
   
   ![Tworzenie danych wyjściowych](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Uruchom przykładową aplikację, uruchamiając następujące polecenie:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Upewnij się, że kopiujesz i wklejasz parametry połączenia urządzenia do pojedynczych cudzysłowów.
   >

Powinny zostać wyświetlone następujące dane wyjściowe pokazujące dane czujnika i komunikaty, które są wysyłane do Twojego centrum IoT.

![Dane wyjściowe — dane czujnika wysyłane z urządzenia Raspberry Pi do centrum IoT](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Czytanie wiadomości odebranych przez centrum

Jednym ze sposobów monitorowania wiadomości odebranych przez centrum IoT hub z urządzenia jest użycie narzędzi Azure IoT Tools for Visual Studio Code. Aby dowiedzieć się więcej, zobacz [Wysyłanie i odbieranie wiadomości między urządzeniem a centrum IoT Hub za pomocą narzędzi Azure IoT Tools for Visual Studio Code.](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)

Aby uzyskać więcej sposobów przetwarzania danych wysyłanych przez urządzenie, przejdź do następnej sekcji.

## <a name="next-steps"></a>Następne kroki

Uruchomisz przykładową aplikację, aby zebrać dane z czujników i wysłać je do centrum IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
