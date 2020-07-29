---
title: Łączenie przykładowego kodu C w programie IoT Plug and Play w wersji zapoznawczej IoT Hub | Microsoft Docs
description: Kompiluj i uruchamiaj Podgląd Plug and Play IoT przykładowe kod urządzenia w systemie Linux lub Windows, który łączy się z Centrum IoT. Użyj narzędzia Azure IoT Explorer, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9011c56096d61e50ae3655a76a396ec3f2dd97c5
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352938"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-or-windows-to-iot-hub-c"></a>Szybki Start: łączenie przykładowej aplikacji urządzenia IoT Plug and Play Preview działającej w systemie Linux lub Windows do IoT Hub (C)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

Ten przewodnik Szybki Start przedstawia sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania danych telemetrycznych wysyłanych przez nią. Przykładowa aplikacja jest zapisywana w języku C i jest uwzględniona w zestawie SDK urządzeń Azure IoT dla języka C. Konstruktor rozwiązań może używać narzędzia Azure IoT Explorer do poznania możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start można uruchomić w systemie Linux lub Windows. Polecenia powłoki w tym przewodniku Szybki Start są zgodne z Konwencją systemu Linux dla separatorów ścieżek " `/` ", jeśli jesteś w systemie Windows, pamiętaj o zamianie tych separatorów dla " `\` ".

Wymagania wstępne różnią się w zależności od systemu operacyjnego:

### <a name="linux"></a>Linux

W tym przewodniku szybki start przyjęto założenie, że używasz Ubuntu Linux. Kroki opisane w tym przewodniku Szybki Start zostały przetestowane przy użyciu Ubuntu 18,04.

Aby ukończyć ten przewodnik Szybki Start w systemie Linux, Zainstaluj następujące oprogramowanie w lokalnym środowisku systemu Linux:

Zainstaluj oprogramowanie w **zatoce**, **git**, **CMAKE**i wszystkie wymagane zależności przy użyciu `apt-get` polecenia:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Sprawdź, czy wersja programu `cmake` jest powyżej **2.8.12** , a wersja **tej** wersji jest większa niż **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Aby ukończyć ten przewodnik Szybki Start w systemie Windows, Zainstaluj następujące oprogramowanie w lokalnym środowisku systemu Windows:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/) — upewnij się, że podczas [instalowania](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) programu Visual Studio dołączysz **programowanie klasyczne przy użyciu języka C++** .
* Usługi [git](https://git-scm.com/download/).
* [CMAKE](https://cmake.org/download/).

### <a name="azure-iot-explorer"></a>Eksplorator IoT Azure

Aby móc korzystać z przykładowego urządzenia w drugiej części tego przewodnika Szybki Start, użyj narzędzia **Azure IoT Explorer** . [Pobierz i zainstaluj najnowszą wersję programu Azure IoT Explorer](./howto-use-iot-explorer.md) dla danego systemu operacyjnego.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby pobrać _Parametry połączenia usługi IoT Hub_ dla centrum. Zanotuj te parametry połączenia w dalszej części tego przewodnika Szybki Start:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Możesz również użyć narzędzia Azure IoT Explorer, aby znaleźć parametry połączenia usługi IoT Hub.

Uruchom następujące polecenie, aby pobrać _Parametry połączenia urządzenia_ dla urządzenia dodanego do centrum. Zanotuj te parametry połączenia w dalszej części tego przewodnika Szybki Start:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Pobieranie kodu

W tym przewodniku szybki start przygotowano środowisko programistyczne, którego można użyć do klonowania i kompilowania zestawu SDK języka C dla systemu Azure IoT Hub.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować repozytorium [usługi Azure IoT C SDK i biblioteki](https://github.com/Azure/azure-iot-sdk-c) GitHub do tej lokalizacji:

```cmd\bash
git clone --depth 1 --recurse-submodules https://github.com/Azure/azure-iot-sdk-c.git
```

Oczekiwanie na ukończenie tej operacji.

## <a name="build-the-code"></a>Kompilowanie kod

Zestaw SDK urządzenia jest używany do kompilowania dołączonego przykładowego kodu:

1. Utwórz podkatalog _CMAKE_ w folderze głównym zestawu SDK urządzeń i przejdź do tego folderu:

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby skompilować zestaw SDK i przykłady:

    ```cmd\bash
    cmake ..
    cmake --build .
    ```

> [!TIP]
> W systemie Windows można otworzyć rozwiązanie wygenerowane przez `cmake` polecenie w programie Visual Studio 2019. Otwórz plik projektu *azure_iot_sdks. sln* w katalogu _CMAKE_ i ustaw projekt **pnp_simple_thermostat** jako projekt startowy w rozwiązaniu. Teraz można skompilować przykład w programie Visual Studio i uruchomić go w trybie debugowania.

## <a name="run-the-device-sample"></a>Uruchamianie przykładu urządzenia

Aby uruchomić przykładową aplikację w zestawie SDK, która symuluje wysyłanie danych telemetrycznych przez urządzenie Plug and Play IoT do centrum IoT:

Utwórz zmienną środowiskową o nazwie **IOTHUB_DEVICE_CONNECTION_STRING** do przechowywania parametrów połączenia urządzenia, które zostały wcześniej wykonane.

W folderze _CMAKE_ przejdź do folderu, który zawiera plik wykonywalny, i uruchom go:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_simple_thermostat/
./pnp_simple_thermostat
```

```cmd
REM Windows
cd  iothub_client\samples\pnp\pnp_simple_thermostat\Debug\pnp_simple_thermostat.exe
```

> [!TIP]
> Aby śledzić wykonywanie kodu w programie Visual Studio w systemie Windows, Dodaj punkt przerwania do `main` funkcji w pliku _pnp_simple_thermostat. c_ .

Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do centrum. Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

Po rozpoczęciu próby klienta urządzenia Użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Przeglądanie kodu

Ten przykład implementuje proste urządzenie z termostatem Plug and Play. Model tego przykładowego implementuje nie korzysta ze [składników](concepts-components.md)Plug and Play IoT. [Plik modelu DTDL dla urządzenia termostatu](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) definiuje dane telemetryczne, właściwości i polecenia implementowane przez urządzenie.

Kod urządzenia używa standardowej funkcji do nawiązywania połączenia z Centrum IoT Hub:

```c
deviceHandle = IoTHubDeviceClient_CreateFromConnectionString(connectionString, MQTT_Protocol)
```

Urządzenie wysyła identyfikator modelu DTDL, który implementuje w żądaniu połączenia. Urządzenie, które wysyła identyfikator modelu, jest urządzeniem Plug and Play IoT:

```c
static const char g_ModelId[] = "dtmi:com:example:Thermostat;1";

...

IoTHubDeviceClient_SetOption(deviceHandle, OPTION_MODEL_ID, modelId)
```

Kod, który aktualizuje właściwości, obsługuje polecenia i wysyła dane telemetryczne, jest identyczny z kodem dla urządzenia, które nie korzysta z Konwencji Plug and Play IoT.

Kod używa biblioteki Parson do analizowania obiektów JSON w ładunków wysyłanych z Centrum IoT:

```c
// JSON parser
#include "parson.h"
```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w usłudze IoT Hub. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: Nawiązywanie połączenia z urządzeniem i korzystanie z niego](howto-develop-solution.md)
