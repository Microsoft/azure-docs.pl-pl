---
title: Szybki start — komunikacja z aplikacją urządzenia w języku C przy użyciu Azure IoT Hub strumieni urządzeń
description: W tym przewodniku Szybki start uruchamiasz aplikację języka C po stronie urządzenia, która komunikuje się z urządzeniem IoT za pośrednictwem strumienia urządzenia.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: ce0d0f9df4862d3c8ac8417e257fbf340e41961f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864126"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Szybki start: komunikacja z aplikacją urządzenia w języku C za pośrednictwem IoT Hub strumieni urządzeń (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub obecnie obsługuje strumienie urządzeń jako funkcję w [wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[Strumienie urządzeń usługi IoT Hub](iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. W publicznej wersji zapoznawczej zestaw SDK języka C obsługuje strumienie urządzeń tylko po stronie urządzenia. W związku z tym ten przewodnik Szybki start zawiera instrukcje uruchamiania tylko aplikacji po stronie urządzenia. Aby uruchomić odpowiednią aplikację po stronie usługi, zobacz następujące artykuły:

* [Komunikacja z aplikacjami urządzeń w języku C# za pośrednictwem IoT Hub strumieni urządzeń](./quickstart-device-streams-echo-csharp.md)

* [Komunikacja z aplikacjami urządzeń w Node.js za pośrednictwem IoT Hub strumieni urządzeń](./quickstart-device-streams-echo-nodejs.md)

Aplikacja C po stronie urządzenia w tym przewodniku Szybki start zawiera następujące funkcje:

* Tworzenie strumienia urządzenia do urządzenia IoT.

* Odbieranie danych wysyłanych z aplikacji po stronie usługi i ich powtarzanie.

Kod przedstawia proces inicjowania strumienia urządzenia, a także sposób używania go do wysyłania i odbierania danych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Wymagane są następujące wymagania wstępne:

* Zainstaluj [Visual Studio 2019 r. z](https://www.visualstudio.com/vs/) włączonym obciążeniem Tworzenie aplikacji klasycznych w języku **C++.**

* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Podgląd strumieni urządzeń jest obecnie obsługiwany tylko w przypadku centrów IoT, które są tworzone w następujących regionach:

  * Central US
  * Środkowe stany USA — EUAP
  * Europa Północna
  * Azja Południowo-Wschodnia

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki start użyjemy zestawu [SDK urządzenia usługi Azure IoT dla języka C.](iot-hub-device-sdk-c-intro.md) Przygotowujesz środowisko deweloperskie używane do klonowania i kompilowania zestawu [SDK języka C usługi Azure IoT z](https://github.com/Azure/azure-iot-sdk-c) usługi GitHub. Zestaw SDK w usłudze GitHub zawiera przykładowy kod używany w tym przewodniku Szybki start.

   > [!NOTE]
   > Przed rozpoczęciem tej procedury upewnij się, że program Visual Studio z obciążeniem Tworzenie aplikacji **klasycznych przy użyciu języka C++.**

1. Zainstaluj system [kompilacji CMake zgodnie](https://cmake.org/download/) z opisem na stronie pobierania.

1. Otwórz wiersz polecenia lub powłokę Git Bash. Uruchom następujące polecenia, aby sklonować repozytorium [GitHub zestawu SDK języka C usługi Azure IoT:](https://github.com/Azure/azure-iot-sdk-c)

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ta operacja powinna potrwać kilka minut.

1. Utwórz *podkatalog cmake* w katalogu głównym repozytorium git i przejdź do tego folderu. Uruchom następujące polecenia z katalogu *azure-iot-sdk-c:*

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia z katalogu *cmake,* aby utworzyć wersję zestawu SDK specyficzną dla platformy klienta dewelopera.

   * W systemie Linux:

      ```bash
      cmake ..
      make -j
      ```

   * W systemie Windows otwórz [wiersz polecenia dla deweloperów dla Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). Uruchom polecenie dla swojej wersji Visual Studio. W tym przewodniku Szybki start Visual Studio 2019 r. Te polecenia tworzą Visual Studio dla urządzenia symulowanego w katalogu *cmake.*

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

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Aby można było nawiązać połączenie, należy zarejestrować urządzenie w centrum IoT. W tej sekcji użyjesz Azure Cloud Shell z rozszerzeniem [IoT,](/cli/azure/iot) aby zarejestrować urządzenie symulowane.

1. Aby utworzyć tożsamość urządzenia, uruchom następujące polecenie w Cloud Shell:

   > [!NOTE]
   > * Zastąp symbol *zastępczy YourIoTHubName* nazwą wybraną dla centrum IoT.
   > * W przypadku nazwy rejestrowanego urządzenia zaleca się użycie urządzenia *MyDevice w* pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, użyj tej nazwy w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Aby uzyskać *parametrów połączenia urządzenia* dla urządzenia, które zostało właśnie zarejestrowane, uruchom następujące polecenie w Cloud Shell:

   > [!NOTE]
   > Zastąp symbol *zastępczy YourIoTHubName* nazwą wybraną dla centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Zanotuj zwrócone ciągi połączenia urządzenia do późniejszego użycia w tym przewodniku Szybki start. Wygląda to następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Komunikacja między urządzeniem i usługą za pośrednictwem strumieni urządzeń

W tej sekcji uruchamiasz zarówno aplikację po stronie urządzenia, jak i aplikację po stronie usługi i komunikujemy się między nimi.

### <a name="run-the-device-side-application"></a>Uruchamianie aplikacji po stronie urządzenia

Aby uruchomić aplikację po stronie urządzenia, wykonaj następujące kroki:

1. Podaj poświadczenia urządzenia, edytując plik **źródłowy iothub_client_c2d_streaming_sample.c** w `iothub_client/samples/iothub_client_c2d_streaming_sample` folderze i dodając ciąg połączenia urządzenia.

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

Jak wspomniano wcześniej, zestaw SDK IoT Hub C obsługuje strumienie urządzeń tylko po stronie urządzenia. Aby skompilować i uruchomić towarzyszącej aplikacji po stronie usługi, postępuj zgodnie z instrukcjami w jednym z następujących przewodników Szybki start:

* [Komunikacja z aplikacją urządzenia w języku C# za pośrednictwem IoT Hub strumieni urządzeń](./quickstart-device-streams-echo-csharp.md)

* [Komunikacja z aplikacją urządzenia w Node.js za pośrednictwem IoT Hub strumieni urządzeń](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurujesz centrum IoT Hub, zarejestrujesz urządzenie, nawiązesz strumień urządzenia między aplikacją języka C na urządzeniu a inną aplikacją po stronie usługi i za pomocą strumienia wyślesz dane między aplikacjami.

Aby dowiedzieć się więcej na temat strumieni urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
