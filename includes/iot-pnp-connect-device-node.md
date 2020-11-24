---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: 43ba9abc17217eea399ed4614002b001534da859
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95510623"
---
Ten przewodnik Szybki Start przedstawia sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania danych telemetrycznych wysyłanych przez nią. Przykładowa aplikacja jest zapisywana w Node.js i jest uwzględniona w zestawie SDK urządzeń Azure IoT dla Node.js. Konstruktor rozwiązań może używać narzędzia Azure IoT Explorer do poznania możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Aby ukończyć ten przewodnik Szybki Start, musisz Node.js na swoim komputerze deweloperskim. Najnowszą zalecaną wersję można pobrać dla wielu platform z [NodeJS.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

## <a name="download-the-code"></a>Pobieranie kodu

W tym przewodniku szybki start przygotowano środowisko programistyczne, którego można użyć do klonowania i kompilowania zestawu SDK urządzeń IoT Hub Azure dla Node.js.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować [Microsoft Azure IoT SDK dla Node.js](https://github.com/Azure/azure-iot-sdk-node) repozytorium GitHub do tej lokalizacji:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>Instalowanie wymaganych bibliotek

Zestaw SDK urządzenia służy do tworzenia dołączonego przykładowego kodu. Utworzona Aplikacja symuluje urządzenie, które nawiązuje połączenie z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. W oknie terminalu lokalnego przejdź do folderu sklonowanego repozytorium i przejdź do folderu */Azure-IoT-SDK-Node/Device/Samples/PnP* . Następnie uruchom następujące polecenie, aby zainstalować wymagane biblioteki:

    ```cmd/sh
    npm install
    ```

1. Skonfiguruj zmienną środowiskową przy użyciu parametrów połączenia urządzenia, które zostały wcześniej wykonane przez użytkownika:

    ```cmd/sh
    set IOTHUB_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

Ten przykład implementuje proste urządzenie z termostatem Plug and Play. Model tego przykładowego implementuje nie korzysta ze [składników](../articles/iot-pnp/concepts-components.md)Plug and Play IoT. [Plik modelu DTDL dla urządzenia termostatu](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definiuje dane telemetryczne, właściwości i polecenia implementowane przez urządzenie.

Otwórz plik _simple_thermostat.js_ . W tym pliku można zobaczyć, jak:

1. Zaimportuj wymagane interfejsy.
1. Napisz procedurę obsługi aktualizacji właściwości i procedurę obsługi poleceń.
1. Obsługa żądanych poprawek właściwości i wysyłanie danych telemetrycznych.
1. Opcjonalnie można zainicjować obsługę administracyjną urządzenia przy użyciu usługi Azure Device Provisioning (DPS).

W funkcji Main można zobaczyć, jak wszystko jest połączone:

1. Utwórz urządzenie na podstawie parametrów połączenia lub udostępnij je za pomocą usługi DPS.)
1. Użyj opcji **modelID** , aby określić model urządzenia IoT Plug and Play.
1. Włącz procedurę obsługi poleceń.
1. Wyślij dane telemetryczne z urządzenia do centrum.
1. Pobierz splot urządzeń i zaktualizuj raportowane właściwości.
1. Włącz obsługę aktualizacji żądanej właściwości.

[!INCLUDE [iot-pnp-environment](iot-pnp-environment.md)]

Aby dowiedzieć się więcej na temat konfiguracji przykładowej, zobacz [przykład pliku Readme](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md).

Uruchom przykładową aplikację, aby symulować urządzenie Plug and Play IoT, które wysyła telemetrię do centrum IoT. Aby uruchomić przykładową aplikację, użyj następującego polecenia:

```cmd\sh
node simple_thermostat.js
```

Zobaczysz następujące dane wyjściowe, wskazujące, że urządzenie rozpoczęło wysyłanie danych telemetrycznych do centrum i jest teraz gotowe do odbierania poleceń i aktualizacji właściwości.

![Komunikaty z potwierdzeniem urządzenia](media/iot-pnp-connect-device-node/device-confirmation-node.png)

Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

Po rozpoczęciu próby klienta urządzenia Użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]
