---
title: Połącz kod programu IoT Plug and Play w wersji zapoznawczej do IoT Hub-Node.js | Microsoft Docs
description: Korzystając z Node.js, skompiluj i uruchom Podgląd usługi IoT Plug and Play przykładowego kodu urządzenia, który łączy się z Centrum IoT. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 68d8aca755f7b8df8eb7e65f4a3c21513feefede
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044183"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-nodejs"></a>Szybki Start: łączenie przykładowej aplikacji urządzenia IoT Plug and Play w wersji zapoznawczej do IoT Hub (Node.js)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

W tym przewodniku szybki start przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana dla Node.js i jest zawarta w zestawie SDK urządzeń IoT Hub platformy Azure dla Node.js. Deweloperzy rozwiązań mogą korzystać z narzędzia Azure IoT Explorer, aby zrozumieć możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, musisz Node.js na swoim komputerze deweloperskim. Najnowszą zalecaną wersję można pobrać dla wielu platform z [NodeJS.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

### <a name="install-the-azure-iot-explorer"></a>Instalowanie programu Azure IoT Explorer

Pobierz i zainstaluj najnowszą wersję programu **Azure IoT Explorer** ze strony [repozytorium](https://github.com/Azure/azure-iot-explorer/releases) narzędzi, wybierając plik msi w obszarze "zasoby" dla najnowszej aktualizacji.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _Parametry połączenia usługi IoT Hub_ dla Twojego centrum (Uwaga do użycia później):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku szybki start przygotowano środowisko programistyczne, którego można użyć do klonowania i kompilowania zestawu SDK urządzeń IoT Hub Azure dla Node.js.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować [Microsoft Azure IoT SDK dla Node.js](https://github.com/Azure/azure-iot-sdk-node) repozytorium GitHub do tej lokalizacji:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node --recursive -b digitaltwins-preview
```

Wykonanie tej operacji może potrwać kilka minut.

## <a name="install-required-libraries"></a>Instalowanie wymaganych bibliotek

Zestaw SDK urządzenia służy do tworzenia dołączonego przykładowego kodu. Utworzona Aplikacja symuluje urządzenie, które nawiązuje połączenie z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. W oknie terminalu lokalnego przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/Azure-IoT-SDK-Node/digitaltwins/Samples/Device/JavaScript** . Następnie uruchom następujące polecenie, aby zainstalować wymagane biblioteki:

    ```cmd/sh
    npm install
    ```
1. Skonfiguruj _Parametry połączenia urządzenia_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

Uruchom przykładową aplikację w zestawie SDK, aby symulować urządzenie Plug and Play IoT wysyłające dane telemetryczne do centrum IoT. Aby uruchomić przykładową aplikację, użyj następującego polecenia:

```cmd\sh
    node sample_device.js
```

Zobaczysz następujące dane wyjściowe, wskazujące, że urządzenie rozpoczęło wysyłanie danych telemetrycznych do centrum i jest teraz gotowe do odbierania poleceń i aktualizacji właściwości.

   ![Komunikaty z potwierdzeniem urządzenia](media/quickstart-connect-pnp-device/device-confirmation-node.png)

 Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. Aby upewnić się, że narzędzie może odczytywać definicje modeli interfejsów z urządzenia, wybierz pozycję **Ustawienia**. W menu ustawienia **na połączonym urządzeniu** mogą już występować konfiguracje Plug and Play; Jeśli tak nie jest, wybierz pozycję **+ Dodaj źródło definicji modułu** , a następnie **na podłączonym urządzeniu** , aby je dodać.

1. Na stronie Przegląd **urządzeń** Znajdź wcześniej utworzoną tożsamość urządzenia. Gdy aplikacja urządzenia jest nadal uruchomiona w wierszu polecenia, sprawdź, czy **stan połączenia** urządzenia w programie Azure IoT Explorer jest raportowany jako _połączony_ (jeśli nie, należy **odświeżyć** do momentu). Wybierz urządzenie, aby wyświetlić więcej szczegółów.

1. Rozwiń interfejs o IDENTYFIKATORze **urn: contoso: com: EnvironmentalSensor: 1** , aby odsłonić interfejs i elementy podstawowe Plug and Play IoT — właściwości, polecenia i dane telemetryczne.

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w usłudze IoT Hub. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Porada: łączenie z urządzeniem IoT Plug and Play Preview i korzystanie z niego](howto-develop-solution.md)
