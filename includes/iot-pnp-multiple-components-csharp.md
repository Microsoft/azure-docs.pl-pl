---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/20/2020
ms.openlocfilehash: cd87978f9ec34e103ede869360858c5633a8c6ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104611816"
---
W tym samouczku przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play ze składnikami, łączenia jej z usługą IoT Hub i używania narzędzia Azure IoT Explorer do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana w języku C# i jest zawarta w zestawie SDK urządzeń Azure IoT dla języka C#. Konstruktor rozwiązań może używać narzędzia Azure IoT Explorer do poznania możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzenia.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Pobierz przykładowy kod.
> * Kompiluj przykładowy kod.
> * Uruchom przykładową aplikację urządzenia i sprawdź, czy nawiązuje połączenie z Centrum IoT Hub.
> * Przejrzyj kod źródłowy.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-pnp-prerequisites](iot-pnp-prerequisites.md)]

Aby ukończyć ten samouczek w systemie Windows, Zainstaluj następujące oprogramowanie w lokalnym środowisku systemu Windows:

* [Visual Studio (Community, Professional lub Enterprise)](https://visualstudio.microsoft.com/downloads/).
* Usługi [git](https://git-scm.com/download/).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Klonowanie repozytorium zestawu SDK przy użyciu przykładowego kodu

Jeśli zakończysz [Przewodnik Szybki Start: Podłącz przykładową aplikację urządzenia IoT Plug and Play działającą w systemie Windows do IoT Hub (C#)](../articles/iot-pnp/quickstart-connect-device.md), masz już Sklonowane repozytorium.

Sklonuj przykłady z przykładów usługi Azure IoT dla repozytorium w serwisie GitHub w języku C#. Otwórz wiersz polecenia w wybranym folderze. Uruchom następujące polecenie, aby sklonować [Microsoft Azure IoT przykłady dla repozytorium programu .NET](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub:

```cmd
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

W tym przewodniku szybki start użyto przykładowego urządzenia kontrolera temperatury, które jest zapisywana w języku C# jako urządzenie Plug and Play IoT. Aby uruchomić przykładowe urządzenie:

1. Otwórz plik projektu *Azure-IoT-Samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController\TemperatureController.csproj* w programie Visual Studio 2019.

1. W programie Visual Studio przejdź do **projektu > właściwości TemperatureController > Debuguj**. Następnie Dodaj następujące zmienne środowiskowe do projektu:

    | Nazwa | Wartość |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DOKUMENTY |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | Wartość zanotowana po zakończeniu [konfigurowania środowiska](../articles/iot-pnp/set-up-environment.md) |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | My-PnP-Device |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | Wartość zanotowana po zakończeniu [konfigurowania środowiska](../articles/iot-pnp/set-up-environment.md) |


1. Teraz można skompilować przykład w programie Visual Studio i uruchomić go w trybie debugowania.

1. Zobaczysz komunikaty informujące, że urządzenie wysłało pewne informacje i zakończyło się w trybie online. Te komunikaty wskazują, że urządzenie rozpoczęło wysyłanie danych telemetrycznych do centrum i jest teraz gotowe do odbierania poleceń i aktualizacji właściwości. Nie zamykaj tego wystąpienia programu Visual Studio, musisz sprawdzić, czy przykład usługi działa.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą programu Azure IoT Explorer

Po rozpoczęciu próby klienta urządzenia Użyj narzędzia Azure IoT Explorer, aby sprawdzić, czy działa.

[!INCLUDE [iot-pnp-iot-explorer.md](iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Przeglądanie kodu

Ten przykład implementuje urządzenie kontrolera temperatury Plug and Play IoT. Model ten przykład implementuje użycie [wielu składników](../articles/iot-pnp/concepts-modeling-guide.md). [Plik modelu Digital bliźniaczych reprezentacji Definition Language (DTDL) dla urządzenia temperatury](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) definiuje dane telemetryczne, właściwości i polecenia implementowane przez urządzenie.

Kod urządzenia łączy się z Centrum IoT Hub przy użyciu standardowej `CreateFromConnectionString` metody. Urządzenie wysyła identyfikator modelu DTDL, który implementuje w żądaniu połączenia. Urządzenie, które wysyła identyfikator modelu, jest urządzeniem Plug and Play IoT:

```csharp
private static DeviceClient InitializeDeviceClient(string hostname, IAuthenticationMethod authenticationMethod)
{
    var options = new ClientOptions
    {
        ModelId = ModelId,
    };

    var deviceClient = DeviceClient.Create(hostname, authenticationMethod, TransportType.Mqtt, options);
    deviceClient.SetConnectionStatusChangesHandler((status, reason) =>
    {
        s_logger.LogDebug($"Connection status change registered - status={status}, reason={reason}.");
    });

    return deviceClient;
}
```

Identyfikator modelu jest przechowywany w kodzie, jak pokazano w poniższym fragmencie kodu:

```csharp
private const string ModelId = "dtmi:com:example:TemperatureController;1";
```

Po połączeniu urządzenia z usługą IoT Hub kod rejestruje programy obsługi poleceń. `reboot`Polecenie jest zdefiniowane w składniku domyślnym. `getMaxMinReport`Polecenie jest zdefiniowane w każdym z dwóch składników termostatu:

```csharp
await _deviceClient.SetMethodHandlerAsync("reboot", HandleRebootCommandAsync, _deviceClient, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat1*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat1, cancellationToken);
await _deviceClient.SetMethodHandlerAsync("thermostat2*getMaxMinReport", HandleMaxMinReportCommandAsync, Thermostat2, cancellationToken);

```

Istnieją osobne programy obsługi dla wymaganych aktualizacji właściwości na dwóch składnikach termostatu:

```csharp
_desiredPropertyUpdateCallbacks.Add(Thermostat1, TargetTemperatureUpdateCallbackAsync);
_desiredPropertyUpdateCallbacks.Add(Thermostat2, TargetTemperatureUpdateCallbackAsync);

```

Przykładowy kod wysyła dane telemetryczne z każdego składnika termostatu:

```csharp
await SendTemperatureAsync(Thermostat1, cancellationToken);
await SendTemperatureAsync(Thermostat2, cancellationToken);
```

`SendTemperatureTelemetryAsync`Metoda używa `PnpHhelper` klasy do tworzenia komunikatów dla każdego składnika:

```csharp
using Message msg = PnpHelper.CreateIothubMessageUtf8(telemetryName, JsonConvert.SerializeObject(currentTemperature), componentName);
```

`PnpHelper`Klasa zawiera inne przykładowe metody, których można użyć z modelem wielu składników.

Użyj narzędzia Azure IoT Explorer, aby wyświetlić dane telemetryczne i właściwości z dwóch składników termostatu:

:::image type="content" source="media/iot-pnp-multiple-components-csharp/multiple-component.png" alt-text="Wiele urządzeń składników w programie Azure IoT Explorer":::

Możesz również użyć narzędzia Azure IoT Explorer do wywołania poleceń z jednego z dwóch składników termostatu lub w składniku domyślnym.
