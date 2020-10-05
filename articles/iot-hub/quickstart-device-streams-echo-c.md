---
title: Komunikacja z aplikacją urządzenia w języku C za pomocą strumieni urządzeń usługi Azure IoT Hub
description: W tym przewodniku szybki start uruchomisz aplikację po stronie urządzenia, która komunikuje się z urządzeniem IoT za pośrednictwem strumienia urządzeń.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 7b5487233e2bd0e532efad4e108c41f188b51d91
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "83727180"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Szybki Start: komunikacja z aplikacją urządzenia w języku C za pośrednictwem IoT Hub strumieni urządzenia (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Usługa Azure IoT Hub obecnie obsługuje strumienie urządzeń jako [funkcję w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. W publicznej wersji zapoznawczej zestaw SDK języka C obsługuje strumienie urządzeń tylko po stronie urządzenia. W efekcie w tym przewodniku szybki start przedstawiono instrukcje dotyczące uruchamiania tylko aplikacji po stronie urządzenia. Aby uruchomić odpowiednią aplikację po stronie usługi, zobacz następujące artykuły:

* [Komunikacja z aplikacjami urządzeń w języku C# za pośrednictwem IoT Hub strumieni urządzeń](./quickstart-device-streams-echo-csharp.md)

* [Komunikacja z aplikacjami urządzeń w Node.js za pośrednictwem IoT Hub strumieni urządzeń](./quickstart-device-streams-echo-nodejs.md)

Aplikacja C po stronie urządzenia w tym przewodniku Szybki start zawiera następujące funkcje:

* Tworzenie strumienia urządzenia do urządzenia IoT.

* Odbieraj dane wysyłane z aplikacji po stronie usługi i wyłączaj je ponownie.

Kod demonstruje proces inicjowania strumienia urządzenia, a także sposób korzystania z niego do wysyłania i odbierania danych.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Wymagane są następujące wymagania wstępne:

* Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/vs/) z włączonym **programowaniem pulpitu przy użyciu języka C++** .

* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

* Uruchom następujące polecenie, aby dodać rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Podgląd strumieni urządzeń jest obecnie obsługiwany tylko w przypadku centrów IoT, które zostały utworzone w następujących regionach:

  * Środkowe stany USA
  * Środkowe stany USA — EUAP
  * Europa Północna
  * Azja Południowo-Wschodnia

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku szybki start użyjesz [zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Przygotuj środowisko programistyczne służące do klonowania i kompilowania [zestawu SDK usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) w witrynie GitHub. Zestaw SDK w witrynie GitHub zawiera przykładowy kod, który jest używany w tym przewodniku Szybki Start.

   > [!NOTE]
   > Przed rozpoczęciem tej procedury należy się upewnić, że program Visual Studio jest instalowany z **programowaniem dla komputerów stacjonarnych przy użyciu obciążenia języka C++** .

1. Zainstaluj [system kompilacji CMAKE](https://cmake.org/download/) zgodnie z opisem na stronie pobierania.

1. Otwórz wiersz polecenia lub powłokę Git Bash. Uruchom następujące polecenia, aby sklonować repozytorium GitHub [zestawu SDK usługi Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ta operacja może potrwać kilka minut.

1. Utwórz podkatalog *CMAKE* w katalogu głównym repozytorium git i przejdź do tego folderu. Uruchom następujące polecenia w katalogu *Azure-IoT-SDK-c* :

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia z katalogu *CMAKE* , aby skompilować wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego.

   * W systemie Linux:

      ```bash
      cmake ..
      make -j
      ```

   * W systemie Windows Otwórz [wiersz polecenia dla deweloperów dla programu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). Uruchom polecenie dla używanej wersji programu Visual Studio. Ten przewodnik Szybki Start używa programu Visual Studio 2019. Te polecenia tworzą rozwiązanie programu Visual Studio dla symulowanego urządzenia w katalogu *CMAKE* .

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Aby można było nawiązać połączenie, musisz zarejestrować urządzenie w usłudze IoT Hub. W tej sekcji użyjesz Azure Cloud Shell z [rozszerzeniem IoT](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) , aby zarejestrować symulowane urządzenie.

1. Aby utworzyć tożsamość urządzenia, uruchom następujące polecenie w Cloud Shell:

   > [!NOTE]
   > * Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla Centrum IoT Hub.
   > * W przypadku nazwy urządzenia, które rejestrujesz, zaleca się użycie *urządzenia* w sposób przedstawiony. Jeśli wybierzesz inną nazwę urządzenia, Użyj tej nazwy w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Aby uzyskać *Parametry połączenia urządzenia* dla zarejestrowanego urządzenia, uruchom następujące polecenie w Cloud Shell:

   > [!NOTE]
   > Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Zwróć uwagę na zwrócone parametry połączenia urządzenia do późniejszego użycia w tym przewodniku Szybki Start. Wygląda to następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Komunikacja między urządzeniem i usługą za pośrednictwem strumieni urządzeń

W tej sekcji uruchomisz aplikację po stronie urządzenia i aplikację po stronie usługi i komunikują się między nimi.

### <a name="run-the-device-side-application"></a>Uruchamianie aplikacji po stronie urządzenia

Aby uruchomić aplikację po stronie urządzenia, wykonaj następujące kroki:

1. Podaj poświadczenia urządzenia, edytując plik źródłowy **iothub_client_c2d_streaming_sample. c** w `iothub_client/samples/iothub_client_c2d_streaming_sample` folderze i dodając parametry połączenia urządzenia.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Skompiluj kod przy użyciu następujących poleceń:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Uruchom skompilowany program:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>Uruchamianie aplikacji po stronie usługi

Jak wspomniano wcześniej, zestaw SDK IoT Hub C obsługuje strumienie urządzeń tylko po stronie urządzenia. Aby skompilować i uruchomić towarzyszącą aplikację po stronie usług, postępuj zgodnie z instrukcjami w jednym z następujących przewodników szybki start:

* [Komunikacja z aplikacją urządzenia w języku C# za pośrednictwem IoT Hub strumieni urządzeń](./quickstart-device-streams-echo-csharp.md)

* [Komunikacja z aplikacją urządzenia w Node.js za pośrednictwem IoT Hub strumieni urządzeń](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz usługę IoT Hub, zarejestrowano urządzenie, nałożyłeś strumień urządzenia między aplikacją C na urządzeniu a inną aplikacją po stronie usługi, a następnie użyto strumienia do przesyłania danych z powrotem i między aplikacjami.

Aby dowiedzieć się więcej o strumieniach urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
