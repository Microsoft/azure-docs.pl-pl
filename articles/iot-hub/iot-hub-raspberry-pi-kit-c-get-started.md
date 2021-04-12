---
title: Łączenie Raspberry Pi z platformą Azure IoT Hub przy użyciu języka C | Microsoft Docs
description: Dowiedz się, jak skonfigurować i połączyć Raspberry Pi z platformą IoT Hub Azure, aby Raspberry Pi w celu wysyłania danych do platformy w chmurze platformy Azure
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: wesmc
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: 52da3c28faa375f8b308e3fe78329fec4f996af9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97804058"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-c"></a>Łączenie Raspberry Pi z platformą Azure IoT Hub (C)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

W tym samouczku przedstawiono podstawowe informacje dotyczące pracy z Raspberry Pi, na którym uruchomiono raspbian. Następnie dowiesz się, jak bezproblemowo połączyć swoje urządzenia z chmurą przy użyciu [usługi Azure IoT Hub](about-iot-hub.md). W przypadku przykładów systemu Windows 10 IoT Core przejdź do [Centrum deweloperów systemu Windows](https://www.windowsondevices.com/).

Nie masz jeszcze zestawu? Wypróbuj [symulator online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md). Lub Kup [Nowy zestaw.](https://azure.microsoft.com/develop/iot/starter-kits)

## <a name="what-you-do"></a>Co robisz

* Utwórz centrum IoT.

* Zarejestruj urządzenie dla platformy Pi w centrum IoT.

* Konfiguracja Raspberry Pi.

* Uruchom przykładową aplikację w języku Pi, aby wysłać dane czujnika do centrum IoT Hub.

Połącz Raspberry Pi z tworzonym Centrum IoT. Następnie uruchom przykładową aplikację w języku Pi, aby zbierać dane dotyczące temperatury i wilgotności z czujnika BME280. Na koniec wysyłasz dane czujnika do centrum IoT Hub.

## <a name="what-you-learn"></a>Omawiane zagadnienia

* Jak utworzyć usługę Azure IoT Hub i uzyskać nowe parametry połączenia z urządzeniem.

* Jak połączyć pi z czujnikiem BME280.

* Jak zbierać dane czujników przez uruchomienie przykładowej aplikacji w pi.

* Jak wysyłać dane czujnika do centrum IoT Hub.

## <a name="what-you-need"></a>Potrzebne elementy

![Potrzebne elementy](./media/iot-hub-raspberry-pi-kit-c-get-started/0-starter-kit.png)

* Tablica Raspberry Pi 2 lub Raspberry Pi 3.

* Aktywna subskrypcja platformy Azure. Jeśli nie masz konta platformy Azure, [Utwórz bezpłatne konto wersji próbnej platformy Azure](https://azure.microsoft.com/free/) w ciągu zaledwie kilku minut.

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
> Te elementy są opcjonalne, ponieważ przykładowy kod obsługuje symulowane dane czujników.
>

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>Konfigurowanie Raspberry Pi

Teraz Skonfiguruj Raspberry Pi.

### <a name="install-the-raspbian-operating-system-for-pi"></a>Zainstaluj system operacyjny raspbian dla liczby pi

Przygotuj kartę microSD na potrzeby instalacji obrazu raspbian.

1. Pobierz raspbian.

   1. [Pobierz raspbian rozciąganie z pulpitem](https://www.raspberrypi.org/software/) (plik. zip).

   2. Wyodrębnij obraz raspbian do folderu na komputerze.

2. Zainstaluj raspbian na karcie microSD.

   1. [Pobierz i zainstaluj narzędzie do nagrywania kart SD](https://etcher.io/).

   2. Uruchom wytrawioner i wybierz obraz raspbian wyodrębniony w kroku 1.

   3. Wybierz dysk karty microSD. Należy zauważyć, że program do wytrawiania mógł już wybrać prawidłowy dysk.

   4. Kliknij przycisk Flash, aby zainstalować raspbian na karcie microSD.

   5. Po zakończeniu instalacji Usuń kartę microSD z komputera. Można bezpiecznie usunąć kartę microSD bezpośrednio, ponieważ program wytrawiony automatycznie wysunie lub odinstalował kartę microSD po zakończeniu.

   6. Wstaw kartę microSD do liczby pi.

### <a name="enable-ssh-and-spi"></a>Włączanie protokołów SSH i SPI

1. Połącz pi z monitorem, klawiaturą i myszą, Zacznij od Pi, a następnie zaloguj się do raspbian, używając `pi` jako nazwy użytkownika i `raspberry` hasła.
 
2. Kliknij ikonę Raspberry > **Preferencje**  >  **Raspberry Pi Configuration**.

   ![Menu preferencji raspbian](./media/iot-hub-raspberry-pi-kit-c-get-started/1-raspbian-preferences-menu.png)

3. Na karcie **interfejsy** ustaw wartość **WWH** i **SSH** na **enable**, a następnie kliknij przycisk **OK**. Jeśli nie masz czujników fizycznych i chcesz użyć symulowanych danych czujników, ten krok jest opcjonalny.

   ![Włącz indeks SPI i SSH na Raspberry Pi](./media/iot-hub-raspberry-pi-kit-c-get-started/2-enable-spi-ssh-on-raspberry-pi.png)

> [!NOTE]
> Aby włączyć SSH i SPI, można znaleźć więcej dokumentów referencyjnych w [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) i [RASPI-config](https://www.raspberrypi.org/documentation/configuration/raspi-config.md).
>

### <a name="connect-the-sensor-to-pi"></a>Połącz czujnik z pi

Za pomocą przewodów breadboard i zworki Połącz diodę LED i BME280 z PI w następujący sposób. Jeśli nie masz czujnika, [Pomiń tę sekcję](#connect-pi-to-the-network).

![Połączenie Raspberry Pi i czujnika](./media/iot-hub-raspberry-pi-kit-c-get-started/3-raspberry-pi-sensor-connection.png)

Czujnik BME280 może zbierać dane dotyczące temperatury i wilgotności. Dioda LED będzie migać w przypadku komunikacji między urządzeniem i chmurą.

W przypadku numerów PIN czujnika Użyj następujących okablowania:

| Rozpocznij (dioda LED & czujnik)     | End (tablica)            | Kolor kabla   |
| -----------------------  | ---------------------- | ------------: |
| Dioda LED VDD (PIN 5G)         | GPIO 4 (numer PIN 7)         | Biały kabel   |
| Dioda LED GND (PIN 6G)         | GND (Przypnij 6)            | Czarny kabel   |
| VDD (Przypnij 18F)            | 3.3 v PWR (numer PIN 17)      | Biały kabel   |
| GND (Przypnij 20F)            | GND (numer PIN 20)           | Czarny kabel   |
| SCK (Przypnij 21F)            | SPI0 SCLK (numer PIN 23)     | Pomarańczowy kabel  |
| SDO (Przypnij 22F)            | SPI0 MISO (numer PIN 21)     | Żółty kabel  |
| SDI (Przypnij 23F)            | SPI0 MOSI (Przypnij 19)     | Zielony kabel   |
| CS (Przypnij 24F)             | SPI0 CS (numer PIN 24)       | Niebieski kabel    |

Kliknij, aby wyświetlić [mapowania Raspberry Pi 2 & 3 kod PIN](/windows/iot-core/learn-about-hardware/pinmappings/pinmappingsrpi) dla odwołania.

Po pomyślnym nawiązaniu połączenia BME280 z Raspberry Pi powinna wyglądać tak jak poniżej.

![Połączono pi i BME280](./media/iot-hub-raspberry-pi-kit-c-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>Połącz pi z siecią

Włącz opcję pi przy użyciu kabla micro USB i zasilacza. Podłącz PI do sieci przewodowej przy użyciu kabla Ethernet lub postępuj zgodnie z [instrukcjami z Raspberry Pi Foundation](https://www.raspberrypi.org/documentation/configuration/wireless/) , aby połączyć pi z siecią bezprzewodową. Po pomyślnym nawiązaniu połączenia pi z siecią należy zanotować [adres IP liczby pi](https://www.raspberrypi.org/documentation/remote-access/ip-address.md).

![Połączono z siecią przewodową](./media/iot-hub-raspberry-pi-kit-c-get-started/5-power-on-pi.png)

## <a name="run-a-sample-application-on-pi"></a>Uruchamianie przykładowej aplikacji na pi

### <a name="sign-into-your-raspberry-pi"></a>Zaloguj się do Raspberry Pi

1. Aby nawiązać połączenie z Raspberry Pi, użyj jednego z następujących klientów SSH z komputera hosta.
   
   **Użytkownicy systemu Windows**
   1. Pobierz [i zainstaluj system](https://www.putty.org/) Windows. 
   1. Skopiuj adres IP liczby pi do sekcji Nazwa hosta (lub adres IP) i wybierz pozycję SSH jako typ połączenia.
   
   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Użytkownicy komputerów Mac i Ubuntu**

   Użyj wbudowanego klienta SSH w systemie Ubuntu lub macOS. Może być konieczne uruchomienie `ssh pi@<ip address of pi>` programu w celu nawiązania połączenia pi za pośrednictwem protokołu SSH.
   > [!NOTE]
   > Domyślna nazwa użytkownika to `pi` , a hasło to `raspberry` .


### <a name="configure-the-sample-application"></a>Konfigurowanie aplikacji przykładowej

1. Sklonuj przykładową aplikację, uruchamiając następujące polecenie:

   ```bash
   sudo apt-get install git-core
   git clone https://github.com/Azure-Samples/iot-hub-c-raspberrypi-client-app.git
   ```

2. Uruchom skrypt instalacyjny:

   ```bash
   cd ./iot-hub-c-raspberrypi-client-app
   sudo chmod u+x setup.sh
   sudo ./setup.sh
   ```

   > [!NOTE] 
   > Jeśli **nie masz fizycznego BME280**, możesz użyć "-------danych" jako parametru wiersza polecenia, aby symulować temperaturę&danych wilgotności. `sudo ./setup.sh --simulated-data`
   >

### <a name="build-and-run-the-sample-application"></a>Kompilowanie i uruchamianie aplikacji przykładowej

1. Utwórz przykładową aplikację, uruchamiając następujące polecenie:

   ```bash
   cmake . && make
   ```
   
   ![Dane wyjściowe kompilacji](./media/iot-hub-raspberry-pi-kit-c-get-started/7-build-output.png)

1. Uruchom przykładową aplikację, uruchamiając następujące polecenie:

   ```bash
   sudo ./app '<DEVICE CONNECTION STRING>'
   ```

   > [!NOTE] 
   > Upewnij się, że kopiujesz, wklejając parametry połączenia urządzenia do pojedynczego cudzysłowu.
   >

Powinny zostać wyświetlone następujące dane wyjściowe pokazujące dane czujnika i komunikaty, które są wysyłane do Twojego centrum IoT.

![Dane wyjściowe — dane czujnika wysyłane z urządzenia Raspberry Pi do centrum IoT](./media/iot-hub-raspberry-pi-kit-c-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>Odczytywanie komunikatów odebranych przez centrum

Jednym ze sposobów monitorowania komunikatów odebranych przez Centrum IoT Hub z urządzenia jest użycie narzędzi Azure IoT Tools for Visual Studio Code. Aby dowiedzieć się więcej, zobacz [Korzystanie z narzędzi Azure IoT Tools for Visual Studio Code w celu wysyłania i odbierania komunikatów między urządzeniem i IoT Hub](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md).

Aby uzyskać więcej sposobów przetwarzania danych wysyłanych przez urządzenie, przejdź do następnej sekcji.

## <a name="next-steps"></a>Następne kroki

Uruchomiono przykładową aplikację, która umożliwia zbieranie danych z czujnika i wysyłanie ich do centrum IoT.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]