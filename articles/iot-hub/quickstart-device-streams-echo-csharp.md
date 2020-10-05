---
title: Komunikacja z aplikacją urządzenia w języku C# przy użyciu strumieni urządzeń usługi Azure IoT Hub
description: W tym przewodniku szybki start uruchomisz dwie przykładowe aplikacje C#, które komunikują się za pośrednictwem strumienia urządzeń ustanowionego za pośrednictwem IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 64af62cb6c2c56ca8c7e67e2f1467d4a7e8335a0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "78675524"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Szybki Start: komunikacja z aplikacją urządzenia w języku C# za pośrednictwem IoT Hub strumieni urządzenia (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Usługa Azure IoT Hub obecnie obsługuje strumienie urządzeń jako [funkcję w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Ten przewodnik Szybki Start obejmuje dwie aplikacje w języku C#, które wykorzystują strumienie urządzeń do wysyłania danych z powrotem i do przodu (echo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

* Podgląd strumieni urządzeń jest obecnie obsługiwany tylko w przypadku centrów IoT, które zostały utworzone w następujących regionach:
  * Środkowe stany USA
  * Środkowe stany USA — EUAP
  * Europa Północna
  * Azja Południowo-Wschodnia

* Dwie przykładowe aplikacje, które są uruchamiane w tym przewodniku Szybki Start, są zapisywane w języku C#. Potrzebujesz zestaw .NET Core SDK 2.1.0 na komputerze deweloperskim lub nowszym.
  * Pobierz [zestaw .NET Core SDK dla wielu platform z platformy .NET](https://www.microsoft.com/net/download/all).
  * Sprawdź bieżącą wersję języka C# na komputerze deweloperskim, używając następującego polecenia:

   ```
   dotnet --version
   ```

* Dodaj rozszerzenie Azure IoT dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell, uruchamiając następujące polecenie. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [Pobierz przykłady w języku C# usługi Azure IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) i Wyodrębnij archiwum zip. Będzie ona potrzebna zarówno po stronie urządzenia, jak i po stronie usługi.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji użyjesz Azure Cloud Shell, aby zarejestrować symulowane urządzenie.

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

3. Potrzebne są też *parametry połączenia usługi* z centrum IoT, aby umożliwić aplikacji po stronie usługi nawiązanie połączenia z centrum IoT i utworzenie strumienia urządzeń. Następujące polecenie umożliwia pobranie tej wartości dla centrum IoT:

   > [!NOTE]
   > Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Zwróć uwagę na zwrócone parametry połączenia usługi do późniejszego użycia w tym przewodniku Szybki Start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Komunikacja między urządzeniem i usługą za pośrednictwem strumieni urządzeń

W tej sekcji uruchomisz aplikację po stronie urządzenia i aplikację po stronie usługi i komunikują się między nimi.

### <a name="run-the-service-side-application"></a>Uruchamianie aplikacji po stronie usługi

W oknie terminalu lokalnego przejdź do `iot-hub/Quickstarts/device-streams-echo/service` katalogu w folderze niespakowanym projektu. Należy pamiętać o następujących kwestiach:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Parametry połączenia z usługą Centrum IoT. |
| `MyDevice` | Utworzony wcześniej identyfikator urządzenia. |

Kompiluj i uruchamiaj kod przy użyciu następujących poleceń:

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

W innym lokalnym oknie terminalu przejdź do `iot-hub/Quickstarts/device-streams-echo/device` katalogu w niespakowanym folderze projektu. Należy pamiętać o następujących kwestiach:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `DeviceConnectionString` | Parametry połączenia urządzenia IoT Hub. |

Kompiluj i uruchamiaj kod przy użyciu następujących poleceń:

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

Na końcu ostatniego kroku aplikacja po stronie usługi inicjuje strumień do urządzenia. Po ustanowieniu strumienia aplikacja wysyła bufor ciągu do usługi za pośrednictwem strumienia. W tym przykładzie aplikacja po stronie usługi po prostu zwraca te same dane do urządzenia, co pokazuje pomyślną komunikację dwukierunkową między dwiema aplikacjami.

Dane wyjściowe konsoli po stronie urządzenia:

![Dane wyjściowe konsoli po stronie urządzenia](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Dane wyjściowe konsoli po stronie usługi:

![Dane wyjściowe konsoli po stronie usługi](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Ruch przesyłany przez strumień jest tunelowany przez Centrum IoT, a nie bezpośrednio wysyłane. Podane korzyści są szczegółowe w zakresie [korzyści ze strumieni urządzeń](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz usługę IoT Hub, zarejestrowano urządzenie, nałożyłeś strumień urządzeń między aplikacjami języka C# na stronie urządzenia i usługi oraz użyto strumienia do przesyłania danych z powrotem i między aplikacjami.

Aby dowiedzieć się więcej o strumieniach urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
