---
title: Szybki start — komunikacja z aplikacją urządzenia w języku C# za Azure IoT Hub strumieniami urządzeń
description: W tym przewodniku Szybki start uruchamiasz dwie przykładowe aplikacje języka C#, które komunikują się za pośrednictwem strumienia urządzenia ustanowionego za pośrednictwem IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: c4f6e06524412c76661623cb5ef2985a41f2d7fc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864094"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Szybki start: komunikacja z aplikacją urządzenia w języku C# za pośrednictwem IoT Hub strumieni urządzeń (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub obecnie obsługuje strumienie urządzeń jako funkcję w [wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Ten przewodnik Szybki start obejmuje dwie aplikacje języka C#, które mogą korzystać ze strumieni urządzeń do wysyłania danych tam i z powrotem (echo).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Wersja zapoznawcza strumieni urządzeń jest obecnie obsługiwana tylko w przypadku centrów IoT, które są tworzone w następujących regionach:
  * Central US
  * Środkowe stany USA — EUAP
  * Europa Północna
  * Azja Południowo-Wschodnia

* Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki start są napisane w języku C#. Potrzebujesz maszyny zestaw .NET Core SDK 2.1.0 lub nowszej na komputerze dewelopera.

    Pobierz plik [zestaw .NET Core SDK dla wielu platform z platformy .NET.](https://dotnet.microsoft.com/download)

    Sprawdź bieżącą wersję języka C# na komputerze dewelopera przy użyciu następującego polecenia:

    ```
    dotnet --version
    ```

* [Pobierz przykłady dla języka C# usługi Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) i wyodrębnij archiwum ZIP. Jest on potrzebny zarówno po stronie urządzenia, jak i po stronie usługi.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji użyjesz Azure Cloud Shell do zarejestrowania symulowanego urządzenia.

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

    Zwróć uwagę na zwrócone ciągi połączenia urządzenia do późniejszego użycia w tym przewodniku Szybki start. Wygląda to następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Potrzebne są też *parametry połączenia usługi* z centrum IoT, aby umożliwić aplikacji po stronie usługi nawiązanie połączenia z centrum IoT i utworzenie strumienia urządzeń. Następujące polecenie umożliwia pobranie tej wartości dla centrum IoT:

   > [!NOTE]
   > Zastąp symbol *zastępczy YourIoTHubName* nazwą wybraną dla centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Zanotuj zwrócone ciągi połączenia usługi do późniejszego użycia w tym przewodniku Szybki start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Komunikacja między urządzeniem i usługą za pośrednictwem strumieni urządzeń

W tej sekcji uruchamiasz zarówno aplikację po stronie urządzenia, jak i aplikację po stronie usługi i komunikujemy się między nimi.

### <a name="run-the-service-side-application"></a>Uruchamianie aplikacji po stronie usługi

W lokalnym oknie terminalu przejdź do `iot-hub/Quickstarts/device-streams-echo/service` katalogu w folderze rozpakowanego projektu. Zachowaj następujące informacje pod ręką:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Ciąg połączenia usługi centrum IoT. |
| `MyDevice` | Utworzony wcześniej identyfikator urządzenia. |

Skompiluj i uruchom kod za pomocą następujących poleceń:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
Aplikacja będzie czekać, aż aplikacja urządzenia stanie się dostępna.

> [!NOTE]
> Przekroczenie limitu czasu występuje, gdy aplikacja po stronie urządzenia przestaje odpowiadać na czas.

### <a name="run-the-device-side-application"></a>Uruchamianie aplikacji po stronie urządzenia

W innym lokalnym oknie terminalu przejdź `iot-hub/Quickstarts/device-streams-echo/device` do katalogu w folderze rozpakowanego projektu. Zachowaj następujące informacje pod ręką:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `DeviceConnectionString` | Ciąg połączenia urządzenia IoT Hub. |

Skompiluj i uruchom kod za pomocą następujących poleceń:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

Na końcu ostatniego kroku aplikacja po stronie usługi inicjuje strumień do urządzenia. Po nawiązyniu strumienia aplikacja wysyła bufor ciągu do usługi za pośrednictwem strumienia. W tym przykładzie aplikacja po stronie usługi po prostu powtarza te same dane do urządzenia, co demonstruje pomyślną dwukierunkową komunikację między dwiema aplikacjami.

Dane wyjściowe konsoli po stronie urządzenia:

![Dane wyjściowe konsoli po stronie urządzenia](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Dane wyjściowe konsoli po stronie usługi:

![Dane wyjściowe konsoli po stronie usługi](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Ruch przesyłany przez strumień jest tunelowany przez centrum IoT, a nie wysyłany bezpośrednio. Podane korzyści zostały szczegółowo opisane w [te tematze Korzyści ze strumieni urządzeń.](./iot-hub-device-streams-overview.md#benefits)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurujesz centrum IoT Hub, zarejestrujesz urządzenie, nawiązesz strumień urządzenia między aplikacjami języka C# po stronie urządzenia i usługi oraz za pomocą strumienia wyślesz dane między aplikacjami.

Aby dowiedzieć się więcej na temat strumieni urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
